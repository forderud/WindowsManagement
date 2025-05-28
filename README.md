Windows [Client management](https://learn.microsoft.com/en-us/windows/client-management/).

Overview of communication between Windows configuration service providers (CSPs) and MDM servers:  
![image](https://github.com/user-attachments/assets/58fe9dfc-ddc6-448b-9b8b-6a6c5a1731f1)


# Device Enrollment
Quote from [Mobile device enrollment](https://learn.microsoft.com/en-us/windows/client-management/mobile-device-enrollment):


The enrollment process includes the following steps:
1. Discovery of the enrollment endpoint: This step provides the enrollment endpoint configuration settings.
2. Certificate installation: This step handles user authentication, certificate generation, and certificate installation. The installed certificates will be used in the future to manage client/server (TLS/SSL) mutual authentication.
3. DM Client provisioning: This step configures the Device Management (DM) client to connect to a Mobile Device Management (MDM) server after enrollment via DM SyncML over HTTPS (also known as Open Mobile Alliance Device Management (OMA DM) XML).

Other notes:
* Protocol: Mobile Device Enrollment Protocol Version 2 (MS-MDE2)
* `SSLCLIENTCERTSEARCHCRITERIA` defines how SW can afterwards discover the client certificate for authentication purposes. Example: `Subject=CN=Tester,O=Microsoft&Stores=My\User`
* [ClientCertificateInstall CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/clientcertificateinstall-csp) (`SCEP`-based certificate enrollment)
* [2.2.9.3 DMClient Configuration Service Provider](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-mde2/f7553554-b6e1-4a0d-abd6-6a2534503af7)

Enrollment parameters configured:  
![image](https://github.com/user-attachments/assets/9287a593-686f-4531-931a-7b2267808f78)


# Device Management
Notes from [Mobile Device Management overview](https://learn.microsoft.com/en-us/windows/client-management/mdm-overview):  
* Quote: "_MDM servers don't need to create or download a client to manage Windows_".


## SW installation management
[Manage apps](https://learn.microsoft.com/en-us/windows/client-management/enterprise-app-management)


## Device Update management
[Mobile device management (MDM) for device updates](https://learn.microsoft.com/en-us/windows/client-management/device-update-management)
