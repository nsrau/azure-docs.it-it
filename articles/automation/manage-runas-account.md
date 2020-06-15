---
title: Gestire un account RunAs di Automazione di Azure
description: Questo articolo descrive come gestire l'account RunAs con PowerShell o nel portale di Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: d85eac7af71f03be384b8fa9d9603525c0807dac
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83770789"
---
# <a name="manage-an-azure-automation-run-as-account"></a>Gestire un account RunAs di Automazione di Azure

Gli account RunAs in Automazione di Azure offrono l'autenticazione per la gestione delle risorse in Azure con i cmdlet di Azure. Quando si crea un account RunAs, viene creato un nuovo utente dell'entità servizio in Azure Active Directory (AD), a cui viene assegnato il ruolo Collaboratore a livello della sottoscrizione.

## <a name="types-of-run-as-accounts"></a>Tipi di account RunAs

Automazione di Azure usa due tipi di account RunAs:

* Account RunAs di Azure
* Account RunAs classico di Azure

>[!NOTE]
>Le sottoscrizioni Azure Cloud Solution Provider (CSP) supportano solo il modello di Azure Resource Manager. I servi diversi da Azure Resource Manager non sono disponibili nel programma. Quando si usa una sottoscrizione CSP, non viene creato l'account RunAs classico di Azure, ma l'account RunAs di Azure. Per altre informazioni sulle sottoscrizioni CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

Per impostazione predefinita l'entità servizio per un account RunAs non dispone delle autorizzazioni per leggere Azure AD. Se si desidera aggiungere autorizzazioni per la lettura o la gestione di Azure AD, è necessario concedere le autorizzazioni all'entità servizio in **Autorizzazioni API**. Per altre informazioni, vedere [Aggiungere autorizzazioni per accedere ad API Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>account RunAs

L'account RunAs gestisce le risorse del [modello di distribuzione di Resource Manager](../azure-resource-manager/management/deployment-models.md) ed effettua le attività seguenti.

* Crea un'applicazione Azure AD da esportare con un certificato autofirmato, crea un account dell'entità servizio per l'applicazione in Azure AD e assegna il ruolo Collaboratore per l'account nella sottoscrizione corrente. È possibile cambiare l'impostazione del certificato in Proprietario o in qualsiasi altro ruolo. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
  
* Crea un asset del certificato di Automazione denominato `AzureRunAsCertificate` nell'account di Automazione specificato. L'asset del certificato contiene la chiave privata del certificato usata dall'applicazione Azure AD.
  
* Crea un asset della connessione di Automazione denominato `AzureRunAsConnection` nell'account di Automazione specificato. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.

### <a name="azure-classic-run-as-account"></a>Account RunAs classico di Azure

L'account RunAs classico di Azure gestisce le risorse del [modello di distribuzione classica](../azure-resource-manager/management/deployment-models.md). Per creare o rinnovare questo tipo di account, è necessario essere un coamministratore della sottoscrizione.

L'account RunAs classico di Azure esegue le attività seguenti.

  * Crea un certificato di gestione nella sottoscrizione.

  * Crea un asset del certificato di Automazione denominato `AzureClassicRunAsCertificate` nell'account di Automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dal certificato di gestione.

  * Crea un asset della connessione di Automazione denominato `AzureClassicRunAsConnection` nell'account di Automazione specificato. L'asset della connessione contiene il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset del certificato.

>[!NOTE]
>Gli account RunAs classici di Azure non vengono creati per impostazione predefinita mentre si crea un account di Automazione. Questo account viene creato separatamente seguendo i passaggi descritti più avanti in questo articolo.

## <a name="obtain-run-as-account-permissions"></a><a name="permissions"></a>Ottenere le autorizzazioni dell'account RunAs

Questa sezione definisce le autorizzazioni sia per i normali account RunAs che per gli account RunAs classici.

### <a name="get-permissions-to-configure-run-as-accounts"></a>Ottenere le autorizzazioni per configurare gli account RunAs

Per creare o aggiornare un account RunAs, è necessario avere autorizzazioni e privilegi specifici. Un Amministratore di applicazioni in Azure Active Directory e un Proprietario in una sottoscrizione possono completare tutte le attività. Nel caso in cui i compiti siano separati, nella tabella seguente è disponibile un elenco delle attività, dei relativi cmdlet e delle autorizzazioni necessarie:

|Attività|Cmdlet  |Autorizzazioni minime  |Dove impostare le autorizzazioni|
|---|---------|---------|---|
|Creare un'applicazione Azure AD|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Ruolo di Sviluppatore di applicazioni<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure AD > Registrazioni app |
|Aggiungere una credenziale all'applicazione.|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Amministratore di applicazioni o Amministratore globale<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure AD > Registrazioni app|
|Creare e ottenere un'entità servizio di Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Amministratore di applicazioni o Amministratore globale<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure AD > Registrazioni app|
|Assegnare o ottenere il ruolo Controllo degli accessi in base al ruolo per l'entità specificata|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Proprietario o Amministratore Accesso utenti oppure disporre delle autorizzazioni seguenti:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Sottoscrizione](../role-based-access-control/role-assignments-portal.md)</br>Home > Sottoscrizioni > \<nome della sottoscrizione\> - Controllo di accesso (IAM)|
|Creare o rimuovere un certificato di Automazione|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Collaboratore nel gruppo di risorse         |Gruppo di risorse dell'account di Automazione|
|Creare o rimuovere una connessione di Automazione|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Collaboratore nel gruppo di risorse |Gruppo di risorse dell'account di Automazione|

<sup>1</sup> Gli utenti non amministratori nel tenant di Azure AD possono [registrare le applicazioni di AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se l'opzione **Gli utenti possono registrare applicazioni** del tenant di Azure AD nella pagina Impostazioni utente è impostata su **Sì**. Se l'impostazione di registrazione dell'applicazione è impostata su **No**, l'utente che esegue questa azione deve essere quello definito in questa tabella.

Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima di essere aggiunto al ruolo di amministratore globale della sottoscrizione, viene aggiunto come guest. In questo caso si riceverà un avviso `You do not have permissions to create…` nella pagina Aggiungi account di Automazione. 

Se l'utente è membro dell'istanza di Active Directory della sottoscrizione quando viene assegnato il ruolo di Amministratore globale, riceve anche un avviso `You do not have permissions to create…` nella pagina Aggiungi account di Automazione. In questo caso l'utente può richiedere di essere rimosso dall'istanza di Active Directory della sottoscrizione e poi di essere aggiunto di nuovo, in modo da diventare un utente completo in Active Directory.

Per verificare che la situazione che ha prodotto il messaggio di errore è stata risolta:

1. Nel riquadro di Azure Active Directory del portale di Azure selezionare **Utenti e gruppi**. 
2. Selezionare **Tutti gli utenti**.
3. Scegliere il nome, quindi selezionare **Profilo**. 
4. Verificare che il valore dell'attributo **Tipo utente** nel profilo dell'utente non sia impostato su **Guest**.

### <a name="get-permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Ottenere le autorizzazioni per configurare gli account RunAs classici

Per configurare o rinnovare gli account RunAs classici, è necessario avere il ruolo di coamministratore a livello di sottoscrizione. Per altre informazioni sulle autorizzazioni della sottoscrizione classica, vedere [Amministratori della sottoscrizione classica di Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-azure-portal"></a>Creare un account RunAs nel portale di Azure

Eseguire la procedura seguente per aggiornare l'account di Automazione di Azure nel portale di Azure. Creare gli account RunAs e RunAs classico separatamente. Se non è necessario gestire le risorse classiche, è sufficiente creare l'account RunAs di Azure.

1. Accedere al portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
2. Cercare e selezionare **Account di Automazione**.
3. Nella pagina Account di automazione selezionare l'account di Automazione dall'elenco.
4. Nel riquadro a sinistra selezionare **Account RunAs** nella sezione delle impostazioni dell'account.
5. A seconda del tipo di account necessario, selezionare **Account RunAs di Azure** o **Account RunAs classico di Azure**. 
6. A seconda del tipo di account interessato, usare il riquadro **Aggiungi account RunAs di Azure** o **Aggiungi account RunAs classico di Azure**. Dopo aver esaminato le informazioni generali, fare clic su **Crea**.
6. Mentre Azure crea l'account RunAs, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu. Viene anche visualizzato un banner che indica che l'account è in fase di creazione. Il completamento del processo potrebbe richiedere alcuni minuti.

## <a name="create-a-run-as-account-using-powershell"></a>Creare un account RunAs tramite PowerShell

L'elenco seguente include i requisiti per creare un account RunAs in PowerShell. Questi requisiti si applicano a entrambi i tipi di account RunAs.

* Windows 10 o Windows Server 2016 con i moduli di Azure Resource Manager 3.4.1 e versioni successive. Lo script di PowerShell non supporta versioni precedenti di Windows.
* Azure PowerShell 1.0 e versioni successive. Per informazioni su PowerShell 1.0, vedere [come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Un account di Automazione, a cui si fa riferimento con il valore per i parametri `AutomationAccountName` e `ApplicationDisplayName`.
* Autorizzazioni equivalenti a quanto indicato in [Autorizzazioni necessarie per la configurazione degli account RunAs](#permissions).

Per ottenere i valori di `SubscriptionId` e `ResourceGroupName`, che sono i parametri obbligatori per lo script di PowerShell, completare i passaggi successivi.

1. Nel portale di Azure selezionare **Account di Automazione**.
1. Nella pagina Account di Automazione selezionare l'account di Automazione.
1. Nella sezione delle impostazioni per l'account selezionare **Proprietà**.
1. Prendere nota dei valori di **NOME**, **ID SOTTOSCRIZIONE** e **GRUPPO DI RISORSE** nella pagina Proprietà. Questi valori corrispondono rispettivamente ai valori per i parametri dello script di PowerShell `AutomationAccountName`, `SubscriptionId` e `ResourceGroupName`.

   ![Pagina delle proprietà dell'account di Automazione](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script di PowerShell per creare un account RunAs

Questa sezione descrive lo script di PowerShell per creare l'account RunAs. Lo script include il supporto per diverse configurazioni.

* Creare un account RunAs usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato rilasciato dall'autorità di certificazione globale (enterprise).
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici.

Lo script usa più cmdlet di Azure Resource Manager per creare le risorse. Per i cmdlet e le autorizzazioni necessarie, vedere [Ottenere le autorizzazioni per configurare gli account RunAs](#get-permissions-to-configure-run-as-accounts).

Salvare lo script nel computer usando il nome di file **New-RunAsAccount.ps1**.

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
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId | Set-AzContext

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
    $SubscriptionName = $subscription.Name
    $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

    Write-Host -ForegroundColor red       $UploadMessage
}
```

>[!NOTE]
>`Add-AzAccount` e `Add-AzureRMAccount` sono gli alias per [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile usare questi cmdlet oppure è possibile [aggiornare i moduli](automation-update-azure-modules.md) nell'account di Automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli, anche se è stato appena creato un nuovo account di Automazione.

### <a name="execute-the-powershell-script"></a>Eseguire lo script di PowerShell

1. Avviare **Windows PowerShell** con diritti utente elevati nel computer dalla schermata **Start**.
1. Nella shell della riga di comando con privilegi elevati passare alla cartella contenente lo script.
1. Eseguire lo script usando i valori dei parametri per la configurazione richiesta.
1. Se si crea un account RunAs classico, dopo l'esecuzione dello script è necessario caricare il certificato pubblico, con estensione **CER**, nell'archivio di gestione della sottoscrizione in cui è stato creato l'account di Automazione.

Dopo l'esecuzione dello script, all'utente viene richiesto di autenticarsi con Azure. Accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Creare un account RunAs usando un certificato autofirmato

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Creare un account RunAs e un account RunAs classico usando un certificato autofirmato

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Creare un account RunAs e un account RunAs classico usando un certificato globale (enterprise)

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Se è stato creato un account RunAs classico con un certificato pubblico globale (enterprise), ovvero un file con estensione **CER**, usare questo certificato. Vedere [Caricamento di un certificato dell'API di gestione nel portale di Azure](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Se è stato creato un account RunAs classico con un certificato pubblico autofirmato, ovvero un file con estensione **CER**, lo script lo crea e lo salva nella cartella dei file temporanei nel computer. Si trova nel profilo utente `%USERPROFILE%\AppData\Local\Temp`, che viene stato usato per eseguire la sessione di PowerShell.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminare un account RunAs o un account RunAs classico

Questa sezione illustra come eliminare un account RunAs o un account RunAs classico. Quando si esegue questa azione, l'account di Automazione viene mantenuto. Dopo aver eliminato l'account, è possibile ricrearlo nel portale di Azure.

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nel riquadro a sinistra selezionare **Account RunAs** nella sezione delle impostazioni dell'account.

3. Nella pagina delle proprietà dell'account RunAs selezionare l'account RunAs o l'account RunAs classico che si desidera eliminare. 

4. Nel riquadro Proprietà per l'account selezionato fare quindi clic su **Elimina**.

   ![Eliminare un account RunAs](media/manage-runas-account/automation-account-delete-runas.png)

5. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

6. Dopo aver eliminato l'account, è possibile crearlo di nuovo nella pagina delle proprietà dell'account RunAs selezionando l'opzione di creazione **Account RunAs di Azure**.

   ![Ricreare l'account RunAs di Automazione](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renew-a-self-signed-certificate"></a><a name="cert-renewal"></a>Rinnovare un certificato autofirmato

Il certificato autofirmato creato per l'account RunAs scade un anno dopo la data di creazione. Prima della scadenza dell'account RunAs, è necessario rinnovare il certificato. È possibile rinnovarlo in qualsiasi momento prima della scadenza. 

Quando si rinnova il certificato autofirmato, il certificato valido corrente viene mantenuto per evitare di influire negativamente su eventuali runbook messi in coda o in esecuzione, che eseguono l'autenticazione con l'account RunAs. Il certificato rimane valido fino alla relativa data di scadenza.

>[!NOTE]
>Se si ritiene che l'account RunAs sia stato compromesso, è possibile eliminare e creare nuovamente il certificato autofirmato.

>[!NOTE]
>Se l'account RunAs è stato configurato per l'uso di un certificato rilasciato dall'autorità di certificazione globale (enterprise) e si usa questa opzione per rinnovare il certificato autofirmato, il certificato globale (enterprise) viene sostituito da un certificato autofirmato.

Usare la procedura seguente per rinnovare il certificato autofirmato.

1. Nel Portale di Azure aprire l'account di Automazione.

1. Selezionare **Account RunAs** nella sezione delle impostazioni dell'account.

    ![Riquadro delle proprietà dell'account di Automazione](media/manage-runas-account/automation-account-properties-pane.png)

1. Nella pagina delle proprietà dell'Account RunAs selezionare l'account RunAs o l'account RunAs classico per cui si vuole rinnovare il certificato.

1. Nel riquadro delle Proprietà per l'account selezionato fare clic su **Rinnova certificato**.

    ![Rinnovare il certificato per l'account RunAs](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Durante il rinnovamento del certificato, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

## <a name="set-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Configurare il rinnovo automatico del certificato con un runbook di automazione

Per rinnovare i certificati in modo automatico, è possibile usare un Runbook di automazione. Questo script in [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) abilita la funzionalità nell'account di Automazione.

>[!NOTE]
>Per eseguire lo script, è necessario essere un Amministratore globale o un Amministratore aziendale in Azure AD.

Lo script crea una pianificazione settimanale per rinnovare i certificati dell'account RunAs. Aggiunge un runbook **Update-AutomationRunAsCredential** all'account di Automazione. È possibile visualizzare il codice del runbook su GitHub, nello script [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). In base alle esigenze, è possibile usare il codice di PowerShell nel file per rinnovare manualmente i certificati.

Usare la procedura seguente per testare subito il processo di rinnovo.

1. Modificare il runbook **Update-AutomationRunAsCredential** e inserire un carattere di commento (#) alla riga 122, davanti al comando **Exit(1)** .

   ```powershell
   #Exit(1)
   ```

2. Pubblicare il runbook.
3. Avviare il runbook.
4. Verificare che il rinnovo sia stato completato correttamente con il codice seguente:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Output:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Dopo il test, modificare il runbook e rimuovere il carattere di commento aggiunto nel passaggio 1.
6. Pubblicare il runbook.

## <a name="limit-run-as-account-permissions"></a>Limitare le autorizzazioni dell'account RunAs

Per controllare la destinazione di Automazione rispetto alle risorse in Azure, è possibile eseguire lo script [Update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8). Questo script modifica l'entità servizio dell'account RunAs esistente per creare e usare una definizione del ruolo personalizzata. Il ruolo dispone delle autorizzazioni per tutte le risorse eccetto [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Dopo aver eseguito lo script **Update-AutomationRunAsAccountRoleAssignments.ps1**, i runbook che accedono a Key Vault con gli account RunAs non funzionano più. Prima di eseguire lo script, è necessario rivedere i runbook nell'account per le chiamate ad Azure Key Vault. Per consentire l'accesso a Key Vault dai runbook di Automazione di Azure, è necessario [aggiungere l'account RunAs alle autorizzazioni di Key Vault](#add-permissions-to-key-vault).

Se è necessario limitare ancora le operazioni che l'entità servizio RunAs può eseguire, aggiungere altri tipi di risorse all'elemento `NotActions` della definizione di ruolo personalizzata. L'esempio seguente limita l'accesso a `Microsoft.Compute/*`. Se si aggiunge questo tipo di risorsa a `NotActions` per la definizione del ruolo, il ruolo non potrà accedere alle risorse di calcolo. Per altre informazioni sulle definizioni dei ruoli, vedere [Informazioni sulle definizioni dei ruoli per le risorse di Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

È possibile determinare se l'entità servizio usata dall'account RunAs si trova nella definizione del ruolo Collaboratore o in una definizione personalizzata. 

1. Passare all'account di Automazione selezionare **Account RunAs** nella sezione di impostazioni dell'account.
2. Selezionare **Account RunAs di Azure**. 
3. Selezionare **Ruolo** per individuare la definizione del ruolo usata.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

È anche possibile determinare la definizione del ruolo usata dagli account RunAs per più sottoscrizioni o account di Automazione. A tale scopo, usare lo script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) nella PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Aggiungere autorizzazioni a Key Vault

È possibile consentire ad Automazione di Azure di verificare se Key Vault e l'entità servizio dell'account RunAs usano una definizione del ruolo personalizzata. È necessario:

* Concedere le autorizzazioni a Key Vault.
* Impostare i criteri di accesso.

È possibile usare lo script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) nella PowerShell Gallery per concedere le autorizzazioni dell'account RunAs a Key Vault. Per informazioni dettagliate sull'impostazione delle autorizzazioni in Key Vault, vedere [Concedere l'accesso delle applicazioni a un insieme di credenziali delle chiavi](../key-vault/general/group-permissions-for-apps.md).

## <a name="resolve-misconfiguration-issues-for-run-as-accounts"></a>Risolvere i problemi di configurazione errata per gli account RunAs

Alcuni elementi di configurazione necessari per l'account RunAs o l'account RunAs classico potrebbero essere stati eliminati o non essere stati creati correttamente durante la configurazione iniziale. Tra le istanze di configurazione errata possibili ci sono:

* Asset del certificato
* Asset di connessione
* Account RunAs rimosso dal ruolo di Collaboratore
* Entità servizio o applicazione in Azure AD

Per tali istanze di configurazione errata, l'account di Automazione rileva le modifiche e visualizza lo stato *Incompleto* nel riquadro delle proprietà dell'Account RunAs.

![Stato di configurazione Incompleto dell'account RunAs](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando si seleziona l'account RunAs, il riquadro delle proprietà dell'account mastra il messaggio di errore seguente:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Per risolvere rapidamente questi problemi relativi all'account RunAs, è possibile eliminare e ricreare l'account.

## <a name="next-steps"></a>Passaggi successivi

* [Oggetti applicazione e oggetti entità servizio](../active-directory/develop/app-objects-and-service-principals.md).
* [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).
