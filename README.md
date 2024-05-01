# Hybrid Identity with Federated Authentication (Part 1)
Resources:<br>
[Choose the right authentication method for your Microsoft Entra hybrid identity solution](https://learn.microsoft.com/en-us/entra/identity/hybrid/connect/choose-ad-authn)<br>
[Add your custom domain - Microsoft Entra](https://learn.microsoft.com/en-us/entra/fundamentals/add-custom-domain)<br>
[Free DNS hosting](https://www.cloudns.net/)<br>

# ADFS-Deployment (Part 5)

Commands that I used in the deployment

## ADFS Configuration
Powershell

Enables the IdP initiated sign-on page in AD FS, allowing users to initiate the authentication process directly from the identity provider.
```
Set-AdfsProperties -EnableIdPInitiatedSignonPage $true
```
Login page 
```
https://(ADFS domain)/adfs/ls/idpinitiatedsignon
```
Metadata page
```
https://(ADFS domain)/federationmetadata/2007-06/federationmetadata.xml
```
### Add relying party trust (Microsoft 365 identity platform)
Import ADFS module
```
Import-Module ADFS
```
```
Add-AdfsRelyingPartyTrust -Name "Microsoft 365 Identity Platform" –MetadataUrl https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml
```
With the above steps, many of the settings are configured perfectly for the Relying Party Trust. However, we need to set three more settings to make it perfect.
The first setting defines the additional WS-Fed Endpoints for the RPT. The other two settings enable monitoring of the RPT and automatic updating.
Enter the following lines of PowerShell, below the earlier ones to configure the settings:
```
$AdditionalWSFedEndpoint = @(
  "https://ccs.login.microsoftonline.com/ccs/login.srf"
  "https://ccs-sdf.login.microsoftonline.com/ccs/login.srf"
  "https://stamp2.login.microsoftonline.com/login.srf"
  )
```
```
Set-AdfsRelyingPartyTrust -TargetName "Microsoft 365 Identity Platform" -AdditionalWSFedEndpoint $AdditionalWSFedEndpoint -AutoUpdateEnabled $true -MonitoringEnabled $true
```
One of the other features of the Microsoft Office 365 Identity Platform RPT, is the default claims issuance authorization rule.
Let’s add it to the RPT by entering the following lines of PowerShell, below the earlier ones:
```
Set-AdfsRelyingPartyTrust -Targetname "Microsoft 365 Identity Platform" -IssuanceAuthorizationRules ' => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");'
```
Resources: <br>
[Configure a Federation server](https://learn.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/configure-a-federation-server)<br>
[Install ADFS role service](https://learn.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/install-the-ad-fs-role-service)<br>
[Install the Certificate Authority](https://learn.microsoft.com/en-us/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority)<br>
[Obtain and Configure an SSL Certificate for AD FS](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn781428(v=ws.11))<br>
[Creating Microsoft Office 365 identity platform relying party trust manually](https://dirteam.com/sander/2019/06/04/creating-the-microsoft-office-365-identity-platform-relying-party-trust-manually/)<br>
[Enable the Idp-initiated sign on page](https://learn.microsoft.com/en-us/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-initiatedsignon)

# Web Application Proxy configuration (Part 6)

Enable WinRM

Powershell

```
Enable-PSRemoting -force 
```
```
Set-item WSMan:\localhost\Client\TrustedHosts -Value (hostname of entra connect server)
```
```
Restart-service -name winrm
```
Resources:
[Install and Configure the Web Application Proxy Server](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383662(v=ws.11))
[Install federation proxy server](https://www.youtube.com/watch?v=N5idLsbI3W0)

# Microsoft Entra Connect Sync (Part 7)
Initiates an interactive authentication process

Go to *C:\Program Files\Microsoft Azure Active Directory Connect*
```
AzureADConnect.exe /InteractiveAuth
```
```
Set-item WSMan:\localhost\Client\TrustedHosts -Value '(hostname of WAP server)' -Force -Concatenate
```
Resources:<br>
[Download Microsoft Entra Connect (Method 01)](https://www.microsoft.com/en-us/download/details.aspx?id=47594)<br>
[Free SSL certificate](https://zerossl.com/)

 
