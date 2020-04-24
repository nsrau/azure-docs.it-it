---
title: Gestire account RunAs di Automazione di Azure
description: Questo articolo descrive come gestire account RunAs con PowerShell o dal portale.
services: automation
ms.subservice: shared-capabilities
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 5cb3752e5a74f26936efcbb9dba5cdcda76e01f4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113307"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gestire account RunAs di Automazione di Azure

Gli account RunAs in automazione di Azure forniscono l'autenticazione per la gestione delle risorse in Azure usando i cmdlet di Azure. Quando si crea un account RunAs, viene creato un nuovo utente entità servizio in Azure Active Directory (AD) e viene assegnato a tale utente il ruolo Collaboratore a livello di sottoscrizione.

## <a name="types-of-run-as-accounts"></a>Tipi di account RunAs

Automazione di Azure usa due tipi di account RunAs:

* Account RunAs di Azure
* Account RunAs classico di Azure

>[!NOTE]
>Le sottoscrizioni Azure Cloud Solution Provider (CSP) supportano solo il modello di Azure Resource Manager. I servizi non Azure Resource Manager non sono disponibili nel programma. Quando si usa una sottoscrizione CSP, l'account RunAs classico di Azure non viene creato, ma viene creato l'account RunAs di Azure. Per altre informazioni sulle sottoscrizioni CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

L'entità servizio per un account RunAs non dispone delle autorizzazioni per leggere Azure AD per impostazione predefinita. Se si desidera aggiungere autorizzazioni per la lettura o la gestione di Azure AD, è necessario concedere le autorizzazioni per l'entità servizio in **autorizzazioni API**. Per altre informazioni, vedere [aggiungere autorizzazioni per accedere alle API Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>account RunAs

L'account RunAs gestisce [Gestione risorse](../azure-resource-manager/management/deployment-models.md) risorse del modello di distribuzione. Esegue le attività seguenti.

* Crea un'applicazione Azure AD da esportare con un certificato autofirmato, crea un account dell'entità servizio per l'applicazione in Azure AD e assegna il ruolo Collaboratore per l'account nella sottoscrizione corrente. È possibile modificare l'impostazione del certificato in proprietario o in qualsiasi altro ruolo. Per altre informazioni, vedere [controllo degli accessi in base al ruolo in automazione di Azure](automation-role-based-access-control.md).
  
* Crea un asset del certificato di `AzureRunAsCertificate` automazione denominato nell'account di automazione specificato. L'asset di certificato include la chiave privata del certificato usata dall'applicazione Azure AD.
  
* Crea un asset della connessione di `AzureRunAsConnection` automazione denominato nell'account di automazione specificato. L'asset di connessione include l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.

### <a name="azure-classic-run-as-account"></a>Account RunAs classico di Azure

L'account RunAs classico di Azure gestisce le risorse del [modello di distribuzione classica](../azure-resource-manager/management/deployment-models.md) . Per creare o rinnovare questo tipo di account, è necessario essere un coamministratore della sottoscrizione.

L'account RunAs classico di Azure esegue le attività seguenti.

  * Consente di creare un certificato di gestione nella sottoscrizione.

  * Crea un asset del certificato di `AzureClassicRunAsCertificate` automazione denominato nell'account di automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dal certificato di gestione.

  * Crea un asset della connessione di `AzureClassicRunAsConnection` automazione denominato nell'account di automazione specificato. L'asset di connessione include il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset del certificato.

>[!NOTE]
>L'account RunAs classico di Azure non viene creato per impostazione predefinita nello stesso momento in cui si crea un account di automazione. Questo account viene creato singolarmente seguendo i passaggi descritti più avanti in questo articolo.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Autorizzazioni account RunAs

Questa sezione definisce le autorizzazioni per gli account RunAs regolari e gli account RunAs classici.

### <a name="permissions-to-configure-run-as-accounts"></a>Autorizzazioni per la configurazione degli account RunAs

Per creare o aggiornare un account RunAs, è necessario avere autorizzazioni e privilegi specifici. Un amministratore dell'applicazione in Azure Active Directory e un proprietario in una sottoscrizione possono completare tutte le attività. In una situazione in cui si dispone della separazione dei compiti, nella tabella seguente viene illustrato un elenco delle attività, il cmdlet equivalente e le autorizzazioni necessarie:

|Attività|Cmdlet  |Autorizzazioni minime  |Dove impostare le autorizzazioni|
|---|---------|---------|---|
|Creare un'applicazione Azure AD|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Ruolo Sviluppatore applicazioni<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure AD registrazioni dell'app > |
|Aggiungere una credenziale all'applicazione.|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Amministratore dell'applicazione o amministratore globale<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure AD registrazioni dell'app >|
|Creare e ottenere un'entità servizio Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Amministratore dell'applicazione o amministratore globale<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure AD registrazioni dell'app >|
|Assegnare o ottenere il ruolo Controllo degli accessi in base al ruolo per l'entità specificata|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Amministratore o proprietario dell'accesso utente oppure disporre delle autorizzazioni seguenti:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abbonamento](../role-based-access-control/role-assignments-portal.md)</br>Home > Sottoscrizioni > \<nome della sottoscrizione\> - Controllo di accesso (IAM)|
|Creare o rimuovere un certificato di Automazione|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Collaboratore per il gruppo di risorse         |Gruppo di risorse dell'account di automazione|
|Creare o rimuovere una connessione di Automazione|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Collaboratore per il gruppo di risorse |Gruppo di risorse dell'account di automazione|

<sup>1</sup> gli utenti non amministratori nel tenant di Azure ad possono [registrare le applicazioni ad](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se l'opzione utenti del tenant del Azure ad **può registrare le applicazioni** nella pagina impostazioni utente è impostata su **Sì**. Se l'impostazione di registrazione dell'applicazione è **No**, l'utente che esegue questa azione deve essere definito in questa tabella.

Se non si è membri dell'istanza di Active Directory della sottoscrizione prima di essere aggiunti al ruolo di amministratore globale della sottoscrizione, si viene aggiunti come Guest. In questo caso si riceverà un avviso `You do not have permissions to create…` nella pagina Aggiungi account di Automazione. 

Se si è un membro dell'istanza Active Directory della sottoscrizione quando viene assegnato il ruolo di amministratore globale, è anche possibile ricevere un `You do not have permissions to create…` avviso nella pagina Aggiungi account di automazione. In questo caso, è possibile richiedere la rimozione dall'istanza Active Directory della sottoscrizione e quindi richiedere di nuovo l'aggiunta, in modo da diventare un utente completo in Active Directory.

Per verificare che la situazione che ha prodotto il messaggio di errore sia stata risolta:

1. Dal riquadro Azure Active Directory della portale di Azure selezionare **utenti e gruppi**. 
2. Selezionare **tutti gli utenti**.
3. Scegliere il nome e quindi selezionare **profilo**. 
4. Verificare che il valore dell'attributo **tipo utente** nel profilo utente non sia impostato su **Guest**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Autorizzazioni per configurare gli account RunAs classici

Per configurare o rinnovare gli account RunAs classici, è necessario avere il ruolo di co-amministratore a livello di sottoscrizione. Per altre informazioni sulle autorizzazioni di sottoscrizione classiche, vedere [amministratori della sottoscrizione di Azure classico](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Creazione di un account RunAs in portale di Azure

Eseguire la procedura seguente per aggiornare l'account di automazione di Azure nell'portale di Azure. Creare individualmente gli account RunAs e RunAs classico. Se non è necessario gestire le risorse classiche, è sufficiente creare l'account RunAs di Azure.

1. Accedere al portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
2. Cercare e selezionare gli **account di automazione**.
3. Nella pagina account di automazione selezionare l'account di automazione dall'elenco.
4. Nel riquadro sinistro selezionare **account RunAs** nella sezione Impostazioni account.
5. A seconda del tipo di account necessario, selezionare **Account RunAs di Azure** o **Account RunAs classico di Azure**. 
6. A seconda dell'account di interesse, usare il riquadro **Aggiungi** **account RunAs di Azure o Aggiungi account RunAs classico di Azure** . Dopo aver esaminato le informazioni generali, fare clic su **Crea**.
6. Mentre Azure crea l'account RunAs, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu. Viene inoltre visualizzato un banner che informa che l'account è in fase di creazione. Il completamento del processo può richiedere alcuni minuti.

## <a name="creating-a-run-as-account-using-powershell"></a>Creazione di un account RunAs con PowerShell

L'elenco seguente include i requisiti per creare un account RunAs in PowerShell. Questi requisiti si applicano a entrambi i tipi di account RunAs.

* Windows 10 o Windows Server 2016 con i moduli di Azure Resource Manager 3.4.1 e versioni successive. Lo script di PowerShell non supporta le versioni precedenti di Windows.
* Azure PowerShell 1.0 e versioni successive. Per informazioni su PowerShell 1.0, vedere [come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Un account di automazione, a cui viene fatto riferimento come valore per `AutomationAccountName` i `ApplicationDisplayName` parametri e.
* Autorizzazioni equivalenti a quelle elencate in [autorizzazioni necessarie per configurare gli account RunAs](#permissions).

Per ottenere i valori per `SubscriptionId`, `ResourceGroupName`e, che sono i parametri obbligatori per lo script di PowerShell, completare i passaggi successivi.

1. Nella portale di Azure selezionare account di **automazione**.
1. Nella pagina account di automazione selezionare l'account di automazione.
1. Nella sezione Impostazioni account selezionare **Proprietà**.
1. Prendere nota dei valori per **nome**, **ID sottoscrizione**e **gruppo di risorse** nella pagina delle proprietà. Questi valori corrispondono rispettivamente ai valori per i `AutomationAccountName`parametri `SubscriptionId`di script `ResourceGroupName` di PowerShell, e.

   ![Pagina delle proprietà dell'account di automazione](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script di PowerShell per creare un account RunAs

Questa sezione fornisce uno script di PowerShell per creare un account RunAs. Lo script include il supporto per diverse configurazioni.

* Creare un account RunAs usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato rilasciato dall'autorità di certificazione globale (enterprise).
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici.

Lo script usa più cmdlet di Azure Resource Manager per creare le risorse. Per i cmdlet e le autorizzazioni necessarie, vedere [autorizzazioni per configurare gli account RunAs](#permissions-to-configure-run-as-accounts).

Salvare lo script nel computer usando il nome file **New-RunAsAccount. ps1**.

```powershell
#Requires -RunAsAdministrator
Param (
    [Parameter(Mandatory = $true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory = $true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory = $true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory = $true)]
    [String] $SubscriptionId,

    [Parameter(Mandatory = $true)]
    [Boolean] $CreateClassicRunAsAccount,

    [Parameter(Mandatory = $true)]
    [String] $SelfSignedCertPlainPassword,

    [Parameter(Mandatory = $false)]
    [string] $EnterpriseCertPathForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPathForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [ValidateSet("AzureCloud", "AzureUSGovernment")]
    [string]$EnvironmentName = "AzureCloud",

    [Parameter(Mandatory = $false)]
    [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
)

function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
    [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
    $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
        -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
        -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

    $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
    Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
}

function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
    $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
    $keyId = (New-Guid).Guid

    # Create an Azure AD application, AD App Credential, AD ServicePrincipal

    # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
    $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
    # Requires Application administrator or GLOBAL ADMIN
    $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
    # Requires Application administrator or GLOBAL ADMIN
    $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
    $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

    # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
    Sleep -s 15
    # Requires User Access Administrator or Owner.
    $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 6) {
        Sleep -s 10
        New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
        $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries++;
    }
    return $Application.ApplicationId.ToString();
}

function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
    $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
    Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
    New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
}

function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
    Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
    New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
}

Import-Module AzureRm.Profile
Import-Module AzureRm.Resources

$AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
    Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
    return
}

# To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

# Import-Module Az.Automation
# Enable-AzureRmAlias


Connect-AzAccount -Environment $EnvironmentName
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

# Create a Run As account by using a service principal
$CertifcateAssetName = "AzureRunAsCertificate"
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionTypeName = "AzureServicePrincipal"

if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
    $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
    $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
}
else {
    $CertificateName = $AutomationAccountName + $CertifcateAssetName
    $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
    $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
    $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
    CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
}

# Create a service principal
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
$ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

# Create the Automation certificate asset
CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

# Populate the ConnectionFieldValues
$SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
$TenantID = $SubscriptionInfo | Select TenantId -First 1
$Thumbprint = $PfxCert.Thumbprint
$ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

# Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

if ($CreateClassicRunAsAccount) {
    # Create a Run As account by using a service principal
    $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
    $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
    $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
    $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
    "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
    "Then click Upload and upload the .cer format of #CERT#"

    if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
        $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
        $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
        $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
    }
    else {
        $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
        $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
        $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
        $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
        CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
    
    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

    # Populate the ConnectionFieldValues
    $SubscriptionName = $subscription.Subscription.Name
    $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

    Write-Host -ForegroundColor red       $UploadMessage
}
```

>[!NOTE]
>`Add-AzAccount`e `Add-AzureRMAccount` sono alias per [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](automation-update-azure-modules.md) nell'account di automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli anche se è stato appena creato un nuovo account di automazione.

### <a name="execute-the-powershell-script"></a>Eseguire lo script di PowerShell

1. Avviare **Windows PowerShell** con diritti utente elevati nel computer dalla schermata **Start**.
1. Dalla shell della riga di comando con privilegi elevati passare alla cartella che contiene lo script.
1. Eseguire lo script usando i valori dei parametri per la configurazione necessaria.
1. Se si crea un account RunAs classico, dopo l'esecuzione dello script, caricare il certificato pubblico (estensione**CER** filename) nell'archivio di gestione per la sottoscrizione in cui è stato creato l'account di automazione.

Dopo l'esecuzione dello script, viene richiesto di eseguire l'autenticazione con Azure. Accedere con un account membro del ruolo amministratori della sottoscrizione e coamministratore della sottoscrizione.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Creare un account RunAs usando un certificato autofirmato

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Creare un account RunAs e un account RunAs classico usando un certificato autofirmato

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Creare un account RunAs e un account RunAs classico usando un certificato enterprise

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Se è stato creato un account RunAs classico con un certificato pubblico aziendale (file con**estensione cer** ), usare questo certificato. Vedere [caricamento di un certificato dell'API di gestione nel portale di Azure](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Se è stato creato un account RunAs classico con un certificato pubblico autofirmato (file con**estensione cer** ), lo script lo crea e lo salva nella cartella dei file temporanei nel computer. Si trova nel profilo `%USERPROFILE%\AppData\Local\Temp`utente, che è stato usato per eseguire la sessione di PowerShell.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Eliminazione di un account RunAs o RunAs classico

Questa sezione descrive come eliminare un account RunAs o un account RunAs classico. Quando si esegue questa azione, l'account di Automazione viene mantenuto. Dopo aver eliminato l'account, è possibile ricrearlo nel portale di Azure.

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nel riquadro sinistro selezionare **account RunAs** nella sezione Impostazioni account.

3. Nella pagina delle proprietà Account RunAs selezionare l'account RunAs o l'account RunAs classico che si vuole eliminare. 

4. Nel riquadro proprietà per l'account selezionato fare clic su **Elimina**.

   ![Eliminare un account RunAs](media/manage-runas-account/automation-account-delete-runas.png)

5. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

6. Dopo aver eliminato l'account, è possibile crearlo di nuovo nella pagina delle proprietà Account RunAs selezionando l'opzione di creazione **Account RunAs di Azure**.

   ![Ricreare l'account RunAs di Automazione](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Rinnovo di un certificato autofirmato

Il certificato autofirmato creato per l'account RunAs scade un anno dalla data di creazione. A un certo punto, prima della scadenza dell'account RunAs, è necessario rinnovare il certificato. È possibile rinnovarlo in qualsiasi momento prima della scadenza. 

Quando si rinnova il certificato autofirmato, il certificato valido corrente viene mantenuto per garantire che tutti i manuali operativi accodati o in esecuzione e che eseguono l'autenticazione con l'account RunAs non siano compromessi. Il certificato rimane valido fino alla relativa data di scadenza.

>[!NOTE]
>Se si ritiene che l'account RunAs sia stato compromesso, è possibile eliminare e ricreare il certificato autofirmato.

>[!NOTE]
>Se l'account RunAs è stato configurato per l'uso di un certificato emesso dall'autorità di certificazione dell'organizzazione e si usa l'opzione per rinnovare un certificato autofirmato, il certificato Enterprise viene sostituito da un certificato autofirmato.

Usare la procedura seguente per rinnovare il certificato autofirmato.

1. Nel Portale di Azure aprire l'account di Automazione.

1. Selezionare **account RunAs** nella sezione Impostazioni account.

    ![Riquadro delle proprietà dell'account di Automazione](media/manage-runas-account/automation-account-properties-pane.png)

1. Nella pagina Proprietà account RunAs selezionare l'account RunAs o l'account RunAs classico per il quale rinnovare il certificato.

1. Nel riquadro proprietà per l'account selezionato fare clic su **Rinnova certificato**.

    ![Rinnovare il certificato per l'account RunAs](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Durante il rinnovamento del certificato, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Impostazione del rinnovo automatico del certificato con un Runbook di automazione

Per rinnovare automaticamente i certificati, è possibile usare un Runbook di automazione. Questo script in [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) Abilita questa funzionalità nell'account di automazione.

>[!NOTE]
>Per eseguire lo script, è necessario essere un amministratore globale o un amministratore della società in Azure AD.

Questo script crea una pianificazione settimanale per rinnovare i certificati dell'account RunAs. Aggiunge un Runbook **Update-AutomationRunAsCredential** all'account di automazione. È possibile visualizzare il codice Runbook su GitHub, nello script [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). È possibile usare il codice PowerShell nel file per rinnovare manualmente i certificati, in base alle esigenze.

Usare la procedura seguente per testare immediatamente il processo di rinnovo.

1. Modificare il Runbook **Update-AutomationRunAsCredential** e inserire un carattere di commento (#) alla riga 122, davanti al comando **Exit (1)** .

   ```powershell
   #Exit(1)
   ```

2. Pubblicare il Runbook.
3. Avviare il Runbook.
4. Verificare il rinnovo completato con il codice seguente:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Output:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Dopo il test, modificare il Runbook e rimuovere il carattere di commento aggiunto nel passaggio 1.
6. Pubblicare il Runbook.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Limitazione delle autorizzazioni dell'account RunAs

Per controllare la destinazione dell'automazione rispetto alle risorse in Azure, è possibile eseguire lo script [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) . Questo script modifica l'entità servizio dell'account RunAs esistente per creare e utilizzare una definizione di ruolo personalizzata. Il ruolo dispone di autorizzazioni per tutte le risorse eccetto [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Dopo aver eseguito lo script **Update-AutomationRunAsAccountRoleAssignments. ps1** , manuali operativi che accedono Key Vault tramite l'utilizzo degli account RunAs non funzionano più. Prima di eseguire lo script, è necessario rivedere manuali operativi nell'account per le chiamate a Azure Key Vault. Per consentire l'accesso ai Key Vault dal manuali operativi di automazione di Azure, è necessario [aggiungere l'account RunAs alle autorizzazioni di Key Vault](#add-permissions-to-key-vault).

Se è necessario limitare le operazioni che l'entità servizio RunAs può eseguire, è possibile aggiungere altri tipi di risorse all' `NotActions` elemento della definizione di ruolo personalizzata. Nell'esempio seguente viene limitato l'accesso a `Microsoft.Compute/*`. Se si aggiunge questo tipo di risorsa `NotActions` a per la definizione del ruolo, il ruolo non sarà in grado di accedere alle risorse di calcolo. Per altre informazioni sulle definizioni di ruolo, vedere informazioni sulle [definizioni di ruolo per le risorse di Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

È possibile determinare se l'entità servizio usata dall'account RunAs si trova nella definizione del ruolo Collaboratore o in una personalizzata. 

1. Passare all'account di automazione e selezionare **account RunAs** nella sezione Impostazioni account.
2. Selezionare **account RunAs di Azure**. 
3. Selezionare **Role (ruolo** ) per individuare la definizione di ruolo utilizzata.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

È inoltre possibile determinare la definizione di ruolo utilizzata dagli account RunAs per più sottoscrizioni o account di automazione. A tale scopo, usare lo script [Check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) nel PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Aggiungere autorizzazioni a Key Vault

È possibile consentire ad automazione di Azure di verificare se Key Vault e l'entità servizio dell'account RunAs usano una definizione di ruolo personalizzata. È necessario:

* Concedere le autorizzazioni per Key Vault.
* Impostare i criteri di accesso.

È possibile utilizzare lo script [extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) nella PowerShell Gallery per assegnare le autorizzazioni dell'account RunAs a Key Vault. Per informazioni dettagliate sull'impostazione delle autorizzazioni per Key Vault, vedere [concedere l'accesso alle applicazioni a un](../key-vault/general/group-permissions-for-apps.md) insieme di credenziali delle chiavi.

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Risoluzione dei problemi di configurazione errata per gli account RunAs

Alcuni elementi di configurazione necessari per un account RunAs o RunAs classico potrebbero essere stati eliminati o creati in modo errato durante l'installazione iniziale. Le istanze possibili di configurazione errata includono:

* Asset del certificato
* Asset di connessione
* Account RunAs rimosso dal ruolo Collaboratore
* Entità servizio o applicazione in Azure AD

Per tali istanze di configurazione errata, l'account di automazione rileva le modifiche e visualizza `Incomplete` lo stato nel riquadro delle proprietà account RunAs per l'account.

![Stato di configurazione Incompleto dell'account RunAs](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando si seleziona l'account RunAs, nel riquadro Proprietà account viene visualizzato il messaggio di errore seguente:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Per risolvere rapidamente questi problemi relativi all'account RunAs, è possibile eliminare e ricreare l'account.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulle entità servizio, vedere [oggetti applicazione e oggetti entità servizio](../active-directory/develop/app-objects-and-service-principals.md).
* Per altre informazioni sui certificati e i servizi di Azure, vedere [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).
