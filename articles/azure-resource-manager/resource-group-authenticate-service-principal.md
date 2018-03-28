---
title: Creare un'identità per un'app Azure con PowerShell | Documentazione Microsoft
description: Descrive come usare Azure PowerShell per creare un'applicazione Azure Active Directory e un'entità servizio e concedere l'accesso alle risorse tramite il controllo degli accessi in base al ruolo. Illustra come autenticare l'applicazione con un certificato.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: d2caf121-9fbe-4f00-bf9d-8f3d1f00a6ff
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/12/2018
ms.author: tomfitz
ms.openlocfilehash: 175d95c16484b90b13936c3be39b67749f0c3238
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Usare Azure PowerShell per creare un'entità servizio con un certificato

Quando si ha un'app o uno script che deve accedere alle risorse, è possibile configurare un'identità per l'app ed eseguirne l'autenticazione con credenziali specifiche. Questa identità è nota come entità servizio. Questo approccio consente di:

* Assegnare all'identità dell'app autorizzazioni diverse rispetto a quelle dell'utente. Tali autorizzazioni sono in genere limitate alle specifiche operazioni che devono essere eseguite dall'app.
* Usare un certificato per l'autenticazione in caso di esecuzione di uno script automatico.

> [!IMPORTANT]
> Anziché creare un'entità servizio, considerare l'uso dell'identità del servizio gestito di Azure Active Directory per l'identità dell'applicazione. MSI di Azure Active Directory è una funzionalità di anteprima pubblica di Azure Active Directory che semplifica la creazione di un'identità per il codice. Se il codice viene eseguito in un servizio che supporta MSI di Azure Active Directory e accede alle risorse che supportano l'autenticazione di Azure Active Directory, MSI di Azure Active Directory è un'opzione migliore. Per altre informazioni su MSI di Azure Active Directory, inclusi i servizi che al momento lo supportano, vedere [Identità del servizio gestito per le risorse di Azure](../active-directory/managed-service-identity/overview.md).

Questo articolo illustra come creare un'entità servizio che esegue l'autenticazione con un certificato. Per configurare un'entità servizio con password, vedere come [creare un'entità servizio di Azure con Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per completare questo articolo, è necessario avere autorizzazioni sufficienti sia nell'istanza di Azure Active Directory che nella sottoscrizione di Azure. In particolare, è necessario poter creare un'app in Azure Active Directory e assegnare l'entità servizio a un ruolo.

Il modo più semplice per verificare se l'account dispone delle autorizzazioni appropriate è tramite il portale. Vedere [Controllare le autorizzazioni necessarie](resource-group-create-service-principal-portal.md#required-permissions).

## <a name="create-service-principal-with-self-signed-certificate"></a>Creare un'entità servizio con certificato autofirmato

L'esempio seguente illustra uno scenario semplice. Viene usato [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) per creare un'entità servizio con un certificato autofirmato viene usato [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) per assegnare il ruolo [Collaboratore](../active-directory/role-based-access-built-in-roles.md#contributor) all'entità servizio. L'ambito di assegnazione del ruolo corrisponde alla sottoscrizione di Azure selezionata. Per selezionare un'altra sottoscrizione, usare [Set-AzureRmContext](/powershell/module/azurerm.profile/set-azurermcontext).

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

L'esempio viene sospeso per 20 secondi per consentire la propagazione della nuova entità servizio in Azure Active Directory. Se la durata dell'attesa dello script non è sufficiente, verrà visualizzato un errore simile al seguente: "L'entità di sicurezza {ID} non esiste nella directory {DIR-ID}". Per correggere l'errore, attendere qualche istante quindi eseguire di nuovo il comando **New-AzureRmRoleAssignment**.

Il prossimo esempio è più complicato perché consente di impostare l'ambito per l'assegnazione del ruolo diverso da quello della sottoscrizione di Azure corrente. Specificare il parametro ResourceGroup solo quando si vuole limitare l'ambito dell'assegnazione di ruolo a un gruppo di risorse. Se si verifica un errore durante l'assegnazione del ruolo, l'assegnazione viene ritentata. È necessario che sia installato Azure PowerShell 2.0 su Windows 10 o Windows Server 2016.

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -Subscription $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
 $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId -Scope $Scope | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
```

Se **non si dispone di Windows 10 o Windows Server 2016**, è necessario scaricare il [generatore di certificati autofirmati](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) da Microsoft Script Center. Estrarre i contenuti e importare il cmdlet necessario.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

Nello script sostituire le due righe seguenti per generare il certificato.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -StoreName My `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornire il certificato tramite uno script di PowerShell automatizzato

Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertSubject,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -match $CertSubject }).Thumbprint
 Login-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Poiché l'ID applicazione e l'ID tenant non sono sensibili, è possibile incorporarli direttamente nello script. Se è necessario recuperare l'ID tenant, usare:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Se è necessario recuperare l'ID applicazione, usare:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Creare un'entità servizio con certificato dell'autorità di certificazione

L'esempio seguente usa un certificato emesso da un'autorità di certificazione per creare un'entità servizio. L'ambito dell'assegnazione corrisponde alla sottoscrizione di Azure specificata. Viene aggiunta l'entità servizio al ruolo [Collaboratore](../active-directory/role-based-access-built-in-roles.md#contributor). Se si verifica un errore durante l'assegnazione del ruolo, l'assegnazione viene ritentata.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Login-AzureRmAccount
 Import-Module AzureRM.Resources
 Set-AzureRmContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzureRmADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzureRMRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornire il certificato tramite uno script di PowerShell automatizzato
Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Azure Active Directory.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

Poiché l'ID applicazione e l'ID tenant non sono sensibili, è possibile incorporarli direttamente nello script. Se è necessario recuperare l'ID tenant, usare:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

Se è necessario recuperare l'ID applicazione, usare:

```powershell
(Get-AzureRmADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Modificare le credenziali

Per modificare le credenziali per un'app AD, a causa di una violazione della sicurezza o della scadenza delle credenziali, usare i cmdlet [Remove-AzureRmADAppCredential](/powershell/resourcemanager/azurerm.resources/v3.3.0/remove-azurermadappcredential) e [New-AzureRmADAppCredential](/powershell/module/azurerm.resources/new-azurermadappcredential).

Per rimuovere tutte le credenziali per un'applicazione, usare:

```powershell
Remove-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -All
```

Per aggiungere un valore del certificato, creare un certificato autofirmato come illustrato in questo articolo. Successivamente, usare:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Debug

Durante la creazione di un'entità servizio, è possibile riscontrare gli errori seguenti:

* **"Authentication_Unauthorized"** o **"Nessuna sottoscrizione trovata nel contesto".** - Questo errore viene visualizzato quando l'account non ha le [autorizzazioni necessarie](#required-permissions) in Azure Active Directory per registrare un'app. In genere, l'errore si verifica quando solo gli utenti amministratori di Azure Active Directory possono registrare le app e l'account in uso non è un account di amministratore. Chiedere all'amministratore di essere assegnati a un ruolo di amministratore oppure di consentire agli utenti di registrare le app.

* L'account **"non è autorizzato a eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' nell'ambito '/subscriptions/{guid}'."** - Questo errore viene visualizzato quando l'account non dispone di autorizzazioni sufficienti per assegnare un ruolo a un'identità. Chiedere all'amministratore della sottoscrizione di essere aggiunti al ruolo Amministratore accessi utente.

## <a name="next-steps"></a>Passaggi successivi
* Per configurare un'entità servizio con password, vedere come [creare un'entità servizio di Azure con Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).
* Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/active-directory-application-objects.md). 
* Per altre informazioni sull'autenticazione di Azure Active Directory, vedere [Scenari di autenticazione per Azure AD](../active-directory/active-directory-authentication-scenarios.md).
