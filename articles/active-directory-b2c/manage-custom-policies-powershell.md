---
title: Gestire i criteri personalizzati con PowerShellManage custom policies with PowerShell
titleSuffix: Azure AD B2C
description: Usare il cmdlet PowerShell di Azure Active Directory (Azure AD) per la gestione a livello di codice dei criteri personalizzati di Azure AD B2C.Use the Azure Active Directory (Azure AD) PowerShell cmdlet for programmatic management of your Azure AD B2C custom policies. Creare, leggere, aggiornare ed eliminare criteri personalizzati con PowerShell.Create, read, update, and delete custom policies with PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebf0cfffa410d8dfe2f0e0b42a0fee0c16106fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187407"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Gestire i criteri personalizzati di Azure AD B2C con Azure PowerShellManage Azure AD B2C custom policies with Azure PowerShell

Azure PowerShell offre diversi cmdlet per la gestione dei criteri personalizzati basata su script e riga di comando nel tenant B2C di Azure AD. Informazioni su come usare il modulo Azure AD PowerShell per:Learn how to use the Azure AD PowerShell module to:

* Elencare i criteri personalizzati in un tenant B2C di Azure ADList the custom policies in an Azure AD B2C tenant
* Scaricare un criterio da un tenantDownload a policy from a tenant
* Aggiornare un criterio esistente sovrascrivendone il contenuto
* Caricare un nuovo criterio nel tenant B2C di Azure ADUpload a new policy to your Azure AD B2C tenant
* Eliminare criteri personalizzati da un tenantDelete a custom policy from a tenant

## <a name="prerequisites"></a>Prerequisiti

* [Tenant B2C](tutorial-create-tenant.md)di Azure AD e credenziali per un utente nella directory con il ruolo [di amministratore dei criteri B2C IEFAzure](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) AD B2C tenant , and credentials for a user in the directory with the B2C IEF Policy Administrator role
* [Criteri personalizzati](custom-policy-get-started.md) caricati nel tenant
* [Modulo di anteprima di Azure AD PowerShell per GraphAzure AD PowerShell for Graph **preview module**](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Connettere la sessione di PowerShell al tenant B2CConnect PowerShell session to B2C tenant

Per usare i criteri personalizzati nel tenant di Azure AD B2C, è innanzitutto necessario connettere la sessione di PowerShell al tenant usando il comando [Connect-AzureAD.][Connect-AzureAD]

Eseguire il comando seguente, sostituendo `{b2c-tenant-name}` il nome del tenant B2C di Azure AD. Accedere con un account a cui è assegnato il ruolo [B2C IEF Policy Administrator](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) nella directory.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Output del comando di esempio che mostra un accesso riuscito:Example command output showing a successful sign-in:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Elencare tutti i criteri personalizzati nel tenantList all custom policies in the tenant

L'individuazione dei criteri personalizzati consente a un amministratore B2C di Azure AD di esaminare, gestire e aggiungere logica di business alle operazioni. Usare il comando [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] per restituire un elenco degli ID dei criteri personalizzati in un tenant B2C di Azure AD.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Esempio di output del comando:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Scaricare un criterio

Dopo aver esaminato l'elenco degli ID dei criteri, è possibile scegliere come destinazione un criterio specifico con [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] per scaricarne il contenuto.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

In questo esempio viene scaricato il criterio con ID *B2C_1A_signup_signin:*

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Per modificare localmente il contenuto dei criteri, `-OutputFilePath` reindirizzare l'output del comando in un file con l'argomento, quindi aprire il file nell'editor preferito.

Comando di esempio che invia l'output a un file:Example command sending output to a file:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Aggiornare un criterio esistente

Dopo aver modificato un file di criteri creato o scaricato, è possibile pubblicare i criteri aggiornati in Azure AD B2C usando il comando [Set-AzureADMSTrustFrameworkPolicy.After][Set-AzureADMSTrustFrameworkPolicy] editing a policy file you've created or downloaded, you can publish the updated policy to Azure AD B2C by using the Set-AzureADMSTrustFrameworkPolicy command.

Se si `Set-AzureADMSTrustFrameworkPolicy` esegue il comando con l'ID di un criterio già esistente nel tenant B2C di Azure AD, il contenuto di tale criterio viene sovrascritto.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Comando di esempio:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Per altri esempi, vedere la guida di riferimento sul comando [Set-AzureADMSTrustFrameworkPolicy.For][Set-AzureADMSTrustFrameworkPolicy] additional examples, see the Set-AzureADMSTrustFrameworkPolicy command reference.

## <a name="upload-a-new-policy"></a>Caricare un nuovo criterio

Quando si apporta una modifica a un criterio personalizzato in esecuzione nell'ambiente di produzione, è possibile pubblicare più versioni dei criteri per gli scenari di fallback o di test A/B. In alternativa, è possibile creare una copia di un criterio esistente, modificarlo con alcune piccole modifiche, quindi caricarlo come nuovo criterio per l'utilizzo da parte di un'applicazione diversa.

Usare il comando New-AzureADMSTrustFrameworkPolicy per caricare un nuovo criterio:Use the [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] command to upload a new policy:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Comando di esempio:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Eliminare un criterio personalizzatoDelete a custom policy

Per mantenere un ciclo di vita delle operazioni pulite, è consigliabile rimuovere periodicamente i criteri personalizzati inutilizzati. Ad esempio, è possibile rimuovere le versioni precedenti dei criteri dopo aver eseguito una migrazione a un nuovo set di criteri e aver verificato la funzionalità dei nuovi criteri. Inoltre, se si tenta di pubblicare un set di criteri personalizzati e ricevere un errore, potrebbe essere opportuno rimuovere i criteri creati come parte della versione non riuscita.

Usare il comando [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] per eliminare un criterio dal tenant.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Comando di esempio:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Risolvere i problemi di caricamento dei criteriTroubleshoot policy upload

Quando si tenta di pubblicare un nuovo criterio personalizzato o aggiornare un criterio esistente, la formattazione XML non corretta e gli errori nella catena di ereditarietà dei file dei criteri possono causare errori di convalida.

Ad esempio, ecco un tentativo di aggiornare un criterio con contenuto che contiene XML in formato non corretto (l'output viene troncato per brevità):For example, here's an attempt to updating a policy with content that contains malformed XML (output is truncated for brevity):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Per informazioni sulla risoluzione dei problemi relativi ai criteri personalizzati, vedere Risolvere i problemi relativi ai criteri personalizzati di [Azure AD B2C e Identity Experience Framework.](active-directory-b2c-guide-troubleshooting-custom.md)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'uso di PowerShell per distribuire criteri personalizzati come parte di una pipeline di integrazione continua/distribuzione continua (CI/CD), vedere Distribuire criteri personalizzati da una pipeline di Azure DevOps.For information about using PowerShell to deploy custom policies as part of a continuous integration/continuous delivery (CI/CD) pipeline, see [Deploy custom policies from an Azure DevOps pipeline.](deploy-custom-policies-devops.md)

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
