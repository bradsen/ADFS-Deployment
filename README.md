# ADFS-Deployment

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
