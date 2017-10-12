---
title: "Creare un'identità per un'app Azure con PowerShell | Documentazione Microsoft"
description: "Descrive come usare Azure PowerShell per creare un'applicazione Azure Active Directory e un'entità servizio e concedere l'accesso alle risorse tramite il controllo degli accessi in base al ruolo. Illustra come autenticare l'applicazione con una password o un certificato."
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
ms.date: 08/28/2017
ms.author: tomfitz
ms.openlocfilehash: df5d705a4451950110c11b7d468bf7d59e5474d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Usare Azure PowerShell per creare un'entità servizio per accedere alle risorse

Quando si ha un'app o uno script che deve accedere alle risorse, è possibile configurare un'identità per l'app ed eseguirne l'autenticazione con credenziali specifiche. Questa identità è nota come entità servizio. Questo approccio consente di:

* Assegnare all'identità dell'app autorizzazioni diverse rispetto a quelle dell'utente. Tali autorizzazioni sono in genere limitate alle specifiche operazioni che devono essere eseguite dall'app.
* Usare un certificato per l'autenticazione in caso di esecuzione di uno script automatico.

Questo argomento illustra come usare [Azure PowerShell](/powershell/azure/overview) per impostare tutte le informazioni necessarie a un'applicazione per l'esecuzione con credenziali e identità proprie.

## <a name="required-permissions"></a>Autorizzazioni necessarie
Per completare questo argomento è necessario avere autorizzazioni sufficienti sia nell'istanza di Azure Active Directory che nella sottoscrizione di Azure. In particolare, è necessario poter creare un'app in Azure Active Directory e assegnare l'entità servizio a un ruolo. 

Il modo più semplice per verificare se l'account dispone delle autorizzazioni appropriate è tramite il portale. Vedere [Controllare le autorizzazioni necessarie](resource-group-create-service-principal-portal.md#required-permissions).

A questo punto, procedere a una sezione per l'autenticazione con:

* [password](#create-service-principal-with-password)
* [Certificato autofirmato](#create-service-principal-with-self-signed-certificate)
* [Certificato di autorità di certificazione](#create-service-principal-with-certificate-from-certificate-authority)

## <a name="powershell-commands"></a>Comandi di PowerShell

Per configurare un'entità servizio, usare:

| Comando | Descrizione |
| ------- | ----------- | 
| [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) | Crea un'entità servizio di Azure Active Directory |
| [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) | Assegna il ruolo specificato del controllo degli accessi in base al ruolo all'entità specificata nell'ambito specificato. |


## <a name="create-service-principal-with-password"></a>Creare un'entità servizio con password

Per creare un'entità servizio con il ruolo Collaboratore per la sottoscrizione, usare: 

```powershell
Login-AzureRmAccount
$sp = New-AzureRmADServicePrincipal -DisplayName exampleapp -Password "{provide-password}"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

L'esempio viene sospeso per 20 secondi per consentire la propagazione della nuova entità servizio in Azure Active Directory. Se la durata dell'attesa dello script non è sufficiente, viene visualizzato un errore simile al seguente: "PrincipalNotFound: L'entità {ID} non esiste nella directory".

Lo script seguente consente di specificare un ambito diverso dalla sottoscrizione predefinita e ritenta l'assegnazione del ruolo, se si verifica un errore:

```powershell
Param (

 # Use to set scope to resource group. If no value is provided, scope is set to subscription.
 [Parameter(Mandatory=$false)]
 [String] $ResourceGroup,

 # Use to set subscription. If no value is provided, default subscription is used. 
 [Parameter(Mandatory=$false)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $Password
)

 Login-AzureRmAccount
 Import-Module AzureRM.Resources

 if ($SubscriptionId -eq "") 
 {
    $SubscriptionId = (Get-AzureRmContext).Subscription.Id
 }
 else
 {
    Set-AzureRmContext -SubscriptionId $SubscriptionId
 }

 if ($ResourceGroup -eq "")
 {
    $Scope = "/subscriptions/" + $SubscriptionId
 }
 else
 {
    $Scope = (Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop).ResourceId
 }

 
 # Create Service Principal for the AD app
 $ServicePrincipal = New-AzureRMADServicePrincipal -DisplayName $ApplicationDisplayName -Password $Password
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

Alcuni elementi da considerare per lo script:

* Per concedere l'accesso all'identità alla sottoscrizione predefinita, non è necessario specificare i parametri ResourceGroup o SubscriptionId.
* Specificare il parametro ResourceGroup solo quando si vuole limitare l'ambito dell'assegnazione di ruolo a un gruppo di risorse.
*  In questo esempio viene aggiunta l'entità servizio al ruolo Collaboratore. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md).
* Lo script viene sospeso per 15 secondi per consentire la propagazione della nuova entità servizio in Azure Active Directory. Se la durata dell'attesa dello script non è sufficiente, viene visualizzato un errore simile al seguente: "PrincipalNotFound: L'entità {ID} non esiste nella directory".
* Se è necessario concedere l'accesso all'entità servizio a più sottoscrizioni o gruppi di risorse, eseguire il cmdlet `New-AzureRMRoleAssignment` con ambiti diversi.


### <a name="provide-credentials-through-powershell"></a>Fornire le credenziali tramite PowerShell
A questo punto è necessario accedere come applicazione per eseguire operazioni. Per il nome utente, usare il valore `ApplicationId` creato per l'applicazione. Per la password, usare quella specificata durante la creazione dell'account. 

```powershell   
$creds = Get-Credential
Login-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId {tenant-ID}
```

Poiché l'ID tenant non è sensibile, è possibile incorporarlo direttamente nello script. Se è necessario recuperare l'ID tenant, usare:

```powershell
(Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId
```

## <a name="create-service-principal-with-self-signed-certificate"></a>Creare un'entità servizio con certificato autofirmato

Per creare un'entità servizio con un certificato autofirmato e il ruolo Collaboratore per la sottoscrizione, usare: 

```powershell
Login-AzureRmAccount
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleappScriptCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzureRMADServicePrincipal -DisplayName exampleapp -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

L'esempio viene sospeso per 20 secondi per consentire la propagazione della nuova entità servizio in Azure Active Directory. Se la durata dell'attesa dello script non è sufficiente, viene visualizzato un errore simile al seguente: "PrincipalNotFound: L'entità {ID} non esiste nella directory".

Lo script seguente consente di specificare un ambito diverso dalla sottoscrizione predefinita e ritenta l'assegnazione di ruolo, se si verifica un errore. È necessario che sia installato Azure PowerShell 2.0 su Windows 10 o Windows Server 2016.

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
    Set-AzureRmContext -SubscriptionId $SubscriptionId
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

Alcuni elementi da considerare per lo script:

* Per concedere l'accesso all'identità alla sottoscrizione predefinita, non è necessario specificare i parametri ResourceGroup o SubscriptionId.
* Specificare il parametro ResourceGroup solo quando si vuole limitare l'ambito dell'assegnazione di ruolo a un gruppo di risorse.
* In questo esempio viene aggiunta l'entità servizio al ruolo Collaboratore. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md).
* Lo script viene sospeso per 15 secondi per consentire la propagazione della nuova entità servizio in Azure Active Directory. Se la durata dell'attesa dello script non è sufficiente, viene visualizzato un errore simile al seguente: "PrincipalNotFound: L'entità {ID} non esiste nella directory".
* Se è necessario concedere l'accesso all'entità servizio a più sottoscrizioni o gruppi di risorse, eseguire il cmdlet `New-AzureRMRoleAssignment` con ambiti diversi.

Se **non si dispone di Windows 10 o Windows Server 2016 Technical Preview**, è necessario scaricare il [generatore di certificato autofirmato](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) da Microsoft Script Center. Estrarre i contenuti e importare il cmdlet necessario.

```powershell  
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```
  
Nello script sostituire le due righe seguenti per generare il certificato.
  
```powershell
New-SelfSignedCertificateEx  -StoreLocation CurrentUser -StoreName My -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornire il certificato tramite uno script di PowerShell automatizzato
Ogni volta che si accede come un'entità servizio, è necessario fornire l'ID tenant della directory per l'app AD. Un tenant è un'istanza di Azure Active Directory. Se è disponibile solo una sottoscrizione, è possibile usare:

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
 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
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
Per usare un certificato emesso da un'autorità di certificazione per creare un'entità servizio, usare lo script seguente:

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
 Set-AzureRmContext -SubscriptionId $SubscriptionId
 
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

Alcuni elementi da considerare per lo script:

* L'ambito di accesso è limitato alla sottoscrizione.
* In questo esempio viene aggiunta l'entità servizio al ruolo Collaboratore. Per gli altri ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md).
* Lo script viene sospeso per 15 secondi per consentire la propagazione della nuova entità servizio in Azure Active Directory. Se la durata dell'attesa dello script non è sufficiente, viene visualizzato un errore simile al seguente: "PrincipalNotFound: L'entità {ID} non esiste nella directory".
* Se è necessario concedere l'accesso all'entità servizio a più sottoscrizioni o gruppi di risorse, eseguire il cmdlet `New-AzureRMRoleAssignment` con ambiti diversi.

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
 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Login-AzureRmAccount -ServicePrincipal -CertificateThumbprint $Thumbprint -ApplicationId $ApplicationId -TenantId $TenantId
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

Per aggiungere una password, usare:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -Password p@ssword!
```

Per aggiungere un valore del certificato, creare un certificato autofirmato come illustrato in questo argomento. Successivamente, usare:

```powershell
New-AzureRmADAppCredential -ApplicationId 8bc80782-a916-47c8-a47e-4d76ed755275 -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
```

## <a name="save-access-token-to-simplify-log-in"></a>Salvare il token di accesso per semplificare l'accesso
Il token di accesso può essere slavato al fine di evitare di dover fornire le credenziali dell'entità servizio ogni volta che viene richiesto di effettuare l'accesso.

Salvare il profilo per usare il token di accesso corrente in una sessione successiva.
   
```powershell
Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```
   
Aprire il profilo ed esaminare il relativo contenuto. Si noti che contiene un token di accesso. Invece di effettuare di nuovo l'accesso manuale, caricare il profilo.
   
```powershell
Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
```

> [!NOTE]
> Il token di accesso scade, quindi l'uso di un profilo salvato funziona solo fino al termine del periodo di validità del token.
>  

In alternativa, è possibile richiamare operazioni REST da PowerShell per eseguire l'accesso. Dalla risposta di autenticazione è possibile recuperare il token di accesso da usare con altre operazioni. Per un esempio di come recuperare il token di accesso richiamando operazioni REST, vedere [Generazione di un token di accesso](resource-manager-rest-api.md#generating-an-access-token).

## <a name="debug"></a>Debug

Durante la creazione di un'entità servizio, è possibile riscontrare gli errori seguenti:

* **"Authentication_Unauthorized"** o **"Nessuna sottoscrizione trovata nel contesto".** - Questo errore viene visualizzato quando l'account non ha le [autorizzazioni necessarie](#required-permissions) in Azure Active Directory per registrare un'app. In genere, l'errore si verifica quando solo gli utenti amministratori di Azure Active Directory possono registrare le app e l'account in uso non è un account di amministratore. Chiedere all'amministratore di essere assegnati a un ruolo di amministratore oppure di consentire agli utenti di registrare le app.

* L'account **"non è autorizzato a eseguire l'azione 'Microsoft.Authorization/roleAssignments/write' nell'ambito '/subscriptions/{guid}'."** - Questo errore viene visualizzato quando l'account non dispone di autorizzazioni sufficienti per assegnare un ruolo a un'identità. Chiedere all'amministratore della sottoscrizione di essere aggiunti al ruolo Amministratore accessi utente.

## <a name="sample-applications"></a>Applicazioni di esempio
Per informazioni su come effettuare l'accesso all'applicazione su diverse piattaforme, vedere:

* [.NET](/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)
* [Java](/java/azure/java-sdk-azure-authenticate)
* [Node.js](/nodejs/azure/node-sdk-azure-get-started?view=azure-node-2.0.0)
* [Python](/python/azure/python-sdk-azure-authenticate?view=azure-python)
* [Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [Guida per gli sviluppatori all'autorizzazione con l'API di Azure Resource Manager](resource-manager-api-authentication.md).
* Per una spiegazione più dettagliata delle applicazioni e delle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/active-directory-application-objects.md). 
* Per altre informazioni sull'autenticazione di Azure Active Directory, vedere [Scenari di autenticazione per Azure AD](../active-directory/active-directory-authentication-scenarios.md).
* Per un elenco di azioni disponibili che è possibile concedere o negare agli utenti, vedere [Operazioni di provider di risorse con Azure Resource Manager](../active-directory/role-based-access-control-resource-provider-operations.md).

