---
title: Gestire i criteri personalizzati con PowerShell
titleSuffix: Azure AD B2C
description: Usare il cmdlet di PowerShell Azure Active Directory (Azure AD) per la gestione a livello di codice dei criteri personalizzati di Azure AD B2C. Creare, leggere, aggiornare ed eliminare criteri personalizzati con PowerShell.
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e29bb245ed0fc79a6f72688dc6e4d044f2828c45
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77463128"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Gestire Azure AD B2C criteri personalizzati con Azure PowerShell

Azure PowerShell offre diversi cmdlet per la gestione dei criteri personalizzata basata su script e riga di comando nel tenant di Azure AD B2C. Informazioni su come usare il modulo Azure AD PowerShell per:

* Elencare i criteri personalizzati in un tenant di Azure AD B2C
* Scaricare un criterio da un tenant
* Aggiornare un criterio esistente sovrascrivendo il relativo contenuto
* Caricare un nuovo criterio nel tenant di Azure AD B2C
* Eliminare un criterio personalizzato da un tenant

## <a name="prerequisites"></a>Prerequisiti

* [Azure ad B2C tenant](tutorial-create-tenant.md)e credenziali per un utente nella directory con il ruolo di [amministratore dei criteri B2C Framework dell'esperienza](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Criteri personalizzati](custom-policy-get-started.md) caricati nel tenant
* [Azure AD PowerShell per il **modulo di anteprima** Graph](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Connettere la sessione di PowerShell al tenant B2C

Per usare i criteri personalizzati nel tenant di Azure AD B2C, è prima di tutto necessario connettere la sessione di PowerShell al tenant usando il comando [Connect-AzureAD][Connect-AzureAD] .

Eseguire il comando seguente, sostituendo `{b2c-tenant-name}` con il nome del tenant del Azure AD B2C. Accedere con un account a cui è assegnato il ruolo di [amministratore dei criteri B2C Framework dell'esperienza](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator) nella directory.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Esempio di output del comando che mostra un accesso riuscito:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Elencare tutti i criteri personalizzati nel tenant

L'individuazione di criteri personalizzati consente a un amministratore Azure AD B2C di esaminare, gestire e aggiungere la logica di business alle relative operazioni. Usare il comando [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] per restituire un elenco degli ID dei criteri personalizzati in un tenant di Azure ad B2C.

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

Dopo aver esaminato l'elenco degli ID dei criteri, è possibile indirizzare un criterio specifico con [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] per scaricarne il contenuto.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

In questo esempio vengono scaricati i criteri con ID *B2C_1A_signup_signin* :

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

Per modificare il contenuto del criterio localmente, inviare l'output del comando a un file con l'argomento `-OutputFilePath`, quindi aprire il file nell'editor preferito.

Esempio di comando che invia l'output a un file:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Aggiornare un criterio esistente

Dopo aver modificato un file di criteri creato o scaricato, è possibile pubblicare il criterio aggiornato in modo da Azure AD B2C usando il comando [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

Se si esegue il comando `Set-AzureADMSTrustFrameworkPolicy` con l'ID di un criterio già esistente nel tenant di Azure AD B2C, il contenuto del criterio verrà sovrascritto.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Comando di esempio:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Per altri esempi, vedere la Guida di riferimento al comando [set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] .

## <a name="upload-a-new-policy"></a>Carica un nuovo criterio

Quando si modifica un criterio personalizzato in esecuzione nell'ambiente di produzione, potrebbe essere necessario pubblicare più versioni del criterio per gli scenari di test di fallback o A/B. In alternativa, è possibile creare una copia di un criterio esistente, modificarla con alcune piccole modifiche, quindi caricarla come nuovo criterio per l'uso da un'altra applicazione.

Usare il comando [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] per caricare un nuovo criterio:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Comando di esempio:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Eliminare un criterio personalizzato

Per mantenere un ciclo di vita operativo pulito, è consigliabile rimuovere periodicamente i criteri personalizzati non usati. Ad esempio, potrebbe essere necessario rimuovere le versioni precedenti dei criteri dopo aver eseguito una migrazione a un nuovo set di criteri e aver verificato la funzionalità dei nuovi criteri. Inoltre, se si tenta di pubblicare un set di criteri personalizzati e si riceve un errore, potrebbe essere utile rimuovere i criteri creati come parte della versione non riuscita.

Usare il comando [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] per eliminare un criterio dal tenant.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Comando di esempio:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Risolvere i problemi di caricamento dei criteri

Quando si tenta di pubblicare un nuovo criterio personalizzato o di aggiornare un criterio esistente, la formattazione XML non corretta e gli errori nella catena di ereditarietà dei file di criteri possono causare errori di convalida.

Ad esempio, di seguito è riportato un tentativo di aggiornamento di un criterio con contenuto che contiene codice XML non valido (l'output viene troncato per brevità):

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

Per informazioni sulla risoluzione dei problemi relativi ai criteri personalizzati, vedere [risoluzione dei problemi Azure ad B2C criteri personalizzati e Framework dell'esperienza di identità](active-directory-b2c-guide-troubleshooting-custom.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'uso di PowerShell per distribuire criteri personalizzati come parte di una pipeline di integrazione continua/recapito continuo (CI/CD), vedere [distribuire criteri personalizzati da una pipeline di Azure DevOps](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: https://docs.microsoft.com/powershell/module/azuread/set-azureadmstrustframeworkpolicy
