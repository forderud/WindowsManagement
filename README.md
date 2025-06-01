**DISCLAIMER**: This document is a work-in-progress collection of quotes, links and notes related to management of Windows computers. Please contact the author if something is incorrect or missing, and the document will be updated accordingly.

## Windows [Client management](https://learn.microsoft.com/en-us/windows/client-management/) notes

The Windows MDM APIs form the **foundation for how MDM tools interact with the underlying operating system**. MDM tools like InTune, ManageEngine, Ivanti, 
Workspace ONE, Jamf, Hexnode and similar are built on top of.these APIs. See [Endpoint Management Tools Reviews and Ratings](https://www.gartner.com/reviews/market/endpoint-management-tools) for a listing of different MDM solutions.

#### Overview of communication between Windows configuration service providers (CSPs), inbuilt MDM clients and MDM servers (figure from [Enterprise settings and policy management](https://learn.microsoft.com/en-us/windows/client-management/windows-mdm-enterprise-settings)):
![image](https://github.com/user-attachments/assets/58fe9dfc-ddc6-448b-9b8b-6a6c5a1731f1)  


Note: InTune is also deploying an additional [Management Extension (IME)](https://learn.microsoft.com/en-us/intune/intune-service/apps/intune-management-extension) (Microsoft.Management.Services.IntuneWindowsAgent.exe) agent that supplements the inbuilt Windows MDM APIs.


## [Mobile device enrollment](https://learn.microsoft.com/en-us/windows/client-management/mobile-device-enrollment)
Quotes: The enrollment process includes the following steps:
1. **Discovery of the enrollment endpoint**: This step provides the enrollment endpoint configuration settings.
2. **Certificate installation**: This step handles user authentication, certificate generation, and certificate installation. The installed certificates will be used in the future to manage client/server (TLS/SSL) mutual authentication.
3. **DM Client provisioning**: This step configures the Device Management (DM) client to connect to a Mobile Device Management (MDM) server after enrollment via DM SyncML over HTTPS (also known as Open Mobile Alliance Device Management (OMA DM) XML).

Quote: "_The DMClient that is configured via the enrollment process is granted access to enterprise related settings. During the enrollment process, the task scheduler is configured to invoke the DMClient to periodically poll the MDM server_."

Misc notes:
* Protocol used: **Mobile Device Enrollment Protocol Version 2** [MS-MDE2]
* [ClientCertificateInstall CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/clientcertificateinstall-csp) (`SCEP`-based certificate enrollment)
* [2.2.9.3 DMClient Configuration Service Provider](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-mde2/f7553554-b6e1-4a0d-abd6-6a2534503af7)
* [Certificate Renewal](https://learn.microsoft.com/en-us/windows/client-management/certificate-renewal-windows-mdm)

![image](https://github.com/user-attachments/assets/5147a0a5-6aa6-4b68-b0cf-781bc767e09e)  


Enrollment parameters:  
![image](https://github.com/user-attachments/assets/9287a593-686f-4531-931a-7b2267808f78)

Client certificate query string:  
* MS-MDE2 `SSLCLIENTCERTSEARCHCRITERIA` defines how SW can discover the MDM client certificate required for network authentication. This certificate is used for communication with the MDM server, to download SW packages and more. Example: `Subject=CN=Tester,O=Microsoft&Stores=My\User`.
* The certificate query seem to be stored in `HKLM\SOFTWARE\Microsoft\Provisioning\OMADM\Accounts\{GUID}\Protected\SslClientCertSearchCriteria` in the Windows registry.

Client certificate lookup:  
* The client certificate store & thumbprint appear to be stored in `HKLM\SOFTWARE\Microsoft\Provisioning\OMADM\Accounts\{GUID}\SslClientCertReference` in the Windows registry.
* The client certificate thumbprint is also exposed in `HKLM\SOFTWARE\Microsoft\Enrollments\{GUID}\DMPCertThumbPrint` and `HKLM\SOFTWARE\Microsoft\Enrollments\{GUID}\DMClient\MS DM Server\EntDMID`.

TODO: Ask Microsoft on the reocmmended way for discovering the correct client certificate.

## [Mobile Device Management overview](https://learn.microsoft.com/en-us/windows/client-management/mdm-overview)
Notes:  
* Quote: "_MDM servers don't need to create or download a client to manage Windows_".
* Protocol used: **Mobile Device Management Protocol** [MS-MDM]

![image](https://github.com/user-attachments/assets/4065756f-30a1-494b-ae44-f3985f0bc6d4)  


### [Manage apps](https://learn.microsoft.com/en-us/windows/client-management/enterprise-app-management)
Notes:
* Inventory all apps for all users of a Windows device.
* Suports app installation ffrom an arbitrary `PackageUri` with certificate-based authentication.
* [EnterpriseModernAppManagement CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/enterprisemodernappmanagement-csp) (limited to MSIX-packaged apps)
* [EnterpriseDesktopAppManagement CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/enterprisedesktopappmanagement-csp): Exposes a `Device/MSI/{ProductID}/DownloadInstall` node for MSI app installation from `ContentURL` . Also see `MsiInstallJob` example below.
* [Win32AppInventory CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/win32appinventory-csp): provide an inventory of installed applications on a device

#### `EnterpriseDesktopAppManagement` CSP MSI installation example:
```
<MsiInstallJob id="{f5645004-3214-46ea-92c2-48835689da06}">
  <Download>
    <ContentURL>https://myorg.com/app-installers/myapp.msi</ContentURL>
  </Download>
  <Validation>
    <FileHash>7D127BA8F8CC5937DB3052E2632D672120217D910E271A58565BBA780ED8F05C</FileHash>
  </Validation>
  <Enforcement>
    <CommandLine>/quiet</CommandLine>
    <TimeOut>10</TimeOut>
    <RetryCount>1</RetryCount>
  </Enforcement>
</MsiInstallJob>
```
... from [Abusing_the_MDM_Client_Stack_Typhooncon_2024-2.pdf](https://typhooncon.com/wp-content/uploads/2024/08/Abusing_the_MDM_Client_Stack_Typhooncon_2024-2.pdf).

### [Mobile device management (MDM) for device updates](https://learn.microsoft.com/en-us/windows/client-management/device-update-management)
Notes:  
* Quote_ "_keep devices up to date with the latest Microsoft updates_".
* Quote: "Get device compliance information (the list of updates that are needed but not yet installed)."
* [Policy CSP - Update](https://learn.microsoft.com/en-us/windows/client-management/mdm/policy-csp-update)

### [Collect MDM logs](https://learn.microsoft.com/en-us/windows/client-management/mdm-collect-logs)
Notes:  
* [DiagnosticLog CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/diagnosticlog-csp) supports log file retrieval from arbitrary ETW channels


## [Configuration Service Providers (CSPs)](https://learn.microsoft.com/en-us/windows/configuration/provisioning-packages/how-it-pros-can-use-configuration-service-providers)
The [MDM Bridge WMI Provider](https://learn.microsoft.com/en-us/windows/win32/dmwmibridgeprov/mdm-bridge-wmi-provider-portal) appear to expose CSPs over WMI, so that they can also be accessed locally on the device. Also, see [WMI providers supported in Windows](https://learn.microsoft.com/en-us/windows/client-management/wmi-providers-supported-in-windows) for additional WMI MDM providers.

TODO: Test this out [Using PowerShell scripting with the WMI Bridge Provider](https://learn.microsoft.com/en-us/windows/client-management/using-powershell-scripting-with-the-wmi-bridge-provider).

## External resources
* [Windows MDM Overview](https://youtu.be/AT2Y8APvQVw?si=kcyn-IhanQ95QDC_&t=534), from Windows Agentless C2: (Ab)using the MDM Client Stack, Black Hat 2023.

