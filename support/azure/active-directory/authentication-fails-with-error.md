---
title: Authentication fails with an error stating "The requested federation realm object 'xxxxxx' does not exist"
description: Describes and provides a resolution for an error that occurs when Authentication fails with an error stating "The requested federation realm object 'xxxxxx' does not exist".
ms.date: 07/30/2021
ms.prod-support-area-path: 
ms.reviewer: fszita
---

# Authentication fails with an error stating "The requested federation realm object 'xxxxxx' does not exist"

Authentication fails with the error "The requested federation realm object 'xxxxxx' does not exist" for users who are part of domain which is federated with a 3rd party identity provider in either [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) or [Microsoft 365](https://www.microsoft.com/microsoft-365).

:::image type="content" source="media/authentication-fails-with-error/authentication-fails-trouble-sign-in.png" alt-text="Screenshot of an error when signing into a federated domain." border="true":::

:::image type="content" source="media/authentication-fails-with-error/authentication-fails-troubleshooting-details.png" alt-text="Screenshot of the details of an error when signing into a federated domain." border="true":::

This failure happens when the 3rd Party identity provider returns the wrong *IssuerURI* within the **Issuer** field in the Security Assertion Markup Language (SAML) response.

## Resolution 1

Contact the support team for the 3rd party identity provider and have them correct the IssuerURI, returned as *Issuer*, in the SAML the response returned to either Azure AD or Microsoft 365, through the client.

## Resolution 2

Use the command `Set-MsolDomainFederationSettings` to modify the IssuerURI of the federated domain to match the realm object listed in the error.

1. Connect to Azure AD using the *MSONLINE* module. To check that the module is installed, open PowerShell and execute the `get-module MSONLINE -ListAvailable` command.

2. Follow the steps outlined in [Install the Azure AD Module](/powershell/azure/active-directory/install-msonlinev1#install-the-azure-ad-module) to install the module.

3. Run the following commands to verify the preferred authentication protocol of the federated domain.

   ```powershell
   $federationSettings=Get-MsolDomainFederationSettings -DomainName domain.com

   $federationSettings.PreferredAuthenticationProtocol
   ```

4. If the `PreferredAuthenticationProtocol` value listed in step 3 is shown as **WSFED**, run the following command to update the **IssuerURI**.

   ```powershell
   Set-MsolDomainFederationSettings -DomainName domain.com -IssuerUri "value of federated realm object listed in the authentication failure message"
   ```

   The necessary IssuerURI value is listed by Azure AD in the authentication failure message.

5. If the `PreferredAuthenticationProtocol` value listed in step 3 is SAMLP (SAML Protocol), run the following command to update the IssuerURI.

   ```powershell
   Set-MsolDomainFederationSettings -DomainName domain.com -IssuerUri "value of federated realm object listed in the authentication failure message" -PreferredAuthenticationProtocol samlp
   ```

   The necessary IssuerURI value is listed by Azure AD in the authentication failure message.

## Additional information

Still need help? Go to [Microsoft Community](https://answers.microsoft.com/) or the [Azure Active Directory Forums](https://social.msdn.microsoft.com/Forums) website.