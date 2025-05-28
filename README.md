## Windows [Client management](https://learn.microsoft.com/en-us/windows/client-management/) notes

The Windows MDM APIs form the **foundation for how MDM tools interact with the underlying operating system**. MDM tools like InTune, ManageEngine, Ivanti, 
Workspace ONE, Jamf, Hexnode and similar are built on top of.these APIs. See [Endpoint Management Tools Reviews and Ratings](https://www.gartner.com/reviews/market/endpoint-management-tools) for a listing of different MDM solutions.

Overview of communication between Windows configuration service providers (CSPs), inbuilt MDM clients and MDM servers:  
![image](https://github.com/user-attachments/assets/58fe9dfc-ddc6-448b-9b8b-6a6c5a1731f1)

Comment: InTune is also deploying an additional [Management Extension (IME)](https://learn.microsoft.com/en-us/intune/intune-service/apps/intune-management-extension) (`Microsoft.Management.Services.IntuneWindowsAgent.exe`) agent that supplements the inbuilt Windows MDM APIs.


## [Mobile device enrollment](https://learn.microsoft.com/en-us/windows/client-management/mobile-device-enrollment)
Quotes: The enrollment process includes the following steps:
1. **Discovery of the enrollment endpoint**: This step provides the enrollment endpoint configuration settings.
2. **Certificate installation**: This step handles user authentication, certificate generation, and certificate installation. The installed certificates will be used in the future to manage client/server (TLS/SSL) mutual authentication.
3. **DM Client provisioning**: This step configures the Device Management (DM) client to connect to a Mobile Device Management (MDM) server after enrollment via DM SyncML over HTTPS (also known as Open Mobile Alliance Device Management (OMA DM) XML).

Quote: "_The DMClient that is configured via the enrollment process is granted access to enterprise related settings. During the enrollment process, the task scheduler is configured to invoke the DMClient to periodically poll the MDM server_."

Misc notes:
* Protocol used: Mobile Device Enrollment Protocol Version 2 (MS-MDE2)
* `SSLCLIENTCERTSEARCHCRITERIA` defines how SW can afterwards discover the client certificate for authentication purposes. Example: `Subject=CN=Tester,O=Microsoft&Stores=My\User`
* [ClientCertificateInstall CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/clientcertificateinstall-csp) (`SCEP`-based certificate enrollment)
* [2.2.9.3 DMClient Configuration Service Provider](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-mde2/f7553554-b6e1-4a0d-abd6-6a2534503af7)
* The client certificate store & thumbprint appear to be stored in `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Provisioning\OMADM\Accounts\{GUID}\SslClientCertReference` in the Windows registry.

Enrollment parameters:  
![image](https://github.com/user-attachments/assets/9287a593-686f-4531-931a-7b2267808f78)


## [Mobile Device Management overview](https://learn.microsoft.com/en-us/windows/client-management/mdm-overview)
Notes:  
* Quote: "_MDM servers don't need to create or download a client to manage Windows_".
* Protocol used: Mobile Device Management Protocol (MS-MDM)


### [Manage apps](https://learn.microsoft.com/en-us/windows/client-management/enterprise-app-management)
Notes:
* Inventory all MSIX apps for all users of a Windows device.
* Suports MSIX app installation ffrom an arbitrary `PackageUri` with certificate-based authentication.
* The associated [EnterpriseModernAppManagement CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/enterprisemodernappmanagement-csp) unfortunately seem to be limited to MSIX-packaged apps.
* [EnterpriseDesktopAppManagement CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/enterprisedesktopappmanagement-csp): Exposes a `Device/MSI/{ProductID}/DownloadInstall` node for MSI app installation from `ContentURL` 
* [Win32AppInventory CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/win32appinventory-csp): provide an inventory of installed applications on a device


### [Mobile device management (MDM) for device updates](https://learn.microsoft.com/en-us/windows/client-management/device-update-management)
Notes:  
* Quote_ "_keep devices up to date with the latest Microsoft updates_".
* Quote: "Get device compliance information (the list of updates that are needed but not yet installed)."
* [Policy CSP - Update](https://learn.microsoft.com/en-us/windows/client-management/mdm/policy-csp-update)

### [Collect MDM logs](https://learn.microsoft.com/en-us/windows/client-management/mdm-collect-logs)
Notes:  
* [DiagnosticLog CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/diagnosticlog-csp) (supports log file retrieval from arbitrary ETW channels)

