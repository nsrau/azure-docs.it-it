---
title: Gestire account RunAs di Automazione di Azure
description: Questo articolo descrive come gestire account RunAs con PowerShell o dal portale.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 4a043bcc2f81214b68b166d443baead6cd230184
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457502"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gestire account RunAs di Automazione di Azure

Gli account RunAs in Automazione di Azure forniscono l'autenticazione per la gestione delle risorse in Azure usando i cmdlet di Azure.Run As accounts in Azure Automation provide authentication for managing resources in Azure using the Azure cmdlets. Quando si crea un account RunAs, viene creato un nuovo utente dell'entità servizio in Azure Active Directory (AD) e viene assegnato il ruolo Collaboratore all'utente a livello di sottoscrizione. Per i runbook che usano i ruoli di lavoro ibridi per runbook nelle macchine virtuali di Azure, è possibile usare [identità gestite per le risorse di Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) invece degli account RunAs per l'autenticazione con le risorse di Azure.

L'entità servizio per un account Runas non dispone delle autorizzazioni per leggere Azure AD per impostazione predefinita. Se si desidera aggiungere autorizzazioni per leggere o gestire Azure AD, è necessario concedere le autorizzazioni per l'entità servizio in **Autorizzazioni API.** Per altre informazioni, vedere [Aggiungere autorizzazioni per accedere alle API Web.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="types-of-run-as-accounts"></a>Tipi di account RunAs

Automazione di Azure usa due tipi di account RunAs:Azure Automation uses two RunAs accounts:

* Account RunAs di AzureAzure RunAs account
* Account RunAs classico di AzureAzure Classic RunAs account

>[!NOTE]
>Le sottoscrizioni di Azure Cloud Solution Provider (CSP) supportano solo il modello di Azure Resource Manager.Azure Cloud Solution Provider (CSP) subscriptions support only the Azure Resource Manager model. I servizi non di Azure Resource Manager non sono disponibili nel programma. Quando si usa una sottoscrizione CSP, l'account RunAs classico di Azure non viene creato, ma viene creato l'account RunAs di Azure.When you are using a CSP subscription, the Azure Classic RunAs account is not created, but the Azure RunAs account is created. Per altre informazioni sulle sottoscrizioni CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>account RunAs

L'account Esegui come gestisce le risorse del modello di distribuzione di [Resource Manager.The RunAs account](../azure-resource-manager/management/deployment-models.md) manages Resource Manager deployment model resources. Esegue le attività seguenti.

* Crea un'applicazione Azure AD da esportare con un certificato autofirmato, crea un account dell'entità servizio per l'applicazione in Azure AD e assegna il ruolo Collaboratore per l'account nella sottoscrizione corrente. È possibile modificare l'impostazione del certificato in Proprietario o qualsiasi altro ruolo. Per altre informazioni, vedere Controllo degli accessi in base al ruolo in Automazione di Azure.For more information, see [Role-based access control in Azure Automation.](automation-role-based-access-control.md)
  
* Crea un asset `AzureRunAsCertificate` di certificato di automazione denominato nell'account di automazione specificato. L'asset del certificato contiene la chiave privata del certificato usata dall'applicazione Azure AD.
  
* Crea un asset `AzureRunAsConnection` di connessione Di automazione denominato nell'account di automazione specificato. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.

### <a name="azure-classic-run-as-account"></a>Account RunAs classico di Azure

L'account RunAs classico di Azure gestisce le risorse del modello di [distribuzione classica.](../azure-resource-manager/management/deployment-models.md) Per creare o rinnovare questo tipo di account, è necessario essere un coamministratore della sottoscrizione.

L'account RunAs classico di Azure esegue le attività seguenti.

  * Crea un certificato di gestione nella sottoscrizione.

  * Crea un asset `AzureClassicRunAsCertificate` di certificato di automazione denominato nell'account di automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dal certificato di gestione.

  * Crea un asset `AzureClassicRunAsConnection` di connessione Di automazione denominato nell'account di automazione specificato. L'asset di connessione contiene il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset del certificato.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Autorizzazioni dell'account RunAs

In questa sezione vengono definite le autorizzazioni sia per gli account RunAs normali che per gli account RunAs classici.

### <a name="permissions-to-configure-run-as-accounts"></a>Autorizzazioni per la configurazione degli account RunAs

Per creare o aggiornare un account RunAs, è necessario avere autorizzazioni e privilegi specifici. Un amministratore dell'applicazione in Azure Active Directory e un proprietario in una sottoscrizione può completare tutte le attività. In una situazione in cui si dispone di separazione dei compiti, la tabella seguente mostra un elenco delle attività, il cmdlet equivalente e le autorizzazioni necessarie:

|Attività|Cmdlet  |Autorizzazioni minime  |Dove impostare le autorizzazioni|
|---|---------|---------|---|
|Creare un'applicazione Azure AD|[Nuovo-AzADApplicazione](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | Ruolo sviluppatore applicazioni<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > registrazioni dell'app > di Azure AD |
|Aggiungere una credenziale all'applicazione.|[Nuovo AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | Amministratore applicazioni o Amministratore globale<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > registrazioni dell'app > di Azure AD|
|Creare e ottenere un'entità servizio di Azure ADCreate and get an Azure AD service principal|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | Amministratore applicazioni o Amministratore globale<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > registrazioni dell'app > di Azure AD|
|Assegnare o ottenere il ruolo Controllo degli accessi in base al ruolo per l'entità specificata|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | Amministratore o proprietario dell'accesso utente oppure disporre delle autorizzazioni seguenti:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Sottoscrizione](../role-based-access-control/role-assignments-portal.md)</br>Home > Sottoscrizioni > \<nome della sottoscrizione\> - Controllo di accesso (IAM)|
|Creare o rimuovere un certificato di Automazione|[Nuovo-AzAutomationCertificateNew-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | Collaboratore per il gruppo di risorseContributor on resource group         |Gruppo di risorse dell'account di automazione|
|Creare o rimuovere una connessione di Automazione|[Nuova connessione azAutomationConnectionNew-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|Collaboratore per il gruppo di risorseContributor on resource group |Gruppo di risorse dell'account di automazione|

<sup>1</sup> Gli utenti non amministratori nel tenant di Azure AD possono [registrare le applicazioni AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se l'opzione Utenti del tenant di Azure AD **può registrare le applicazioni** nella pagina Impostazioni utente è impostata su **Sì**. Se l'impostazione di registrazione dell'applicazione è **No**, l'utente che esegue questa azione deve essere quello definito in questa tabella.

Se non si è membri dell'istanza di Active Directory della sottoscrizione prima di essere aggiunti al ruolo Amministratore globale della sottoscrizione, si viene aggiunti come guest. In questo caso si riceverà un avviso `You do not have permissions to create…` nella pagina Aggiungi account di Automazione. 

Se si è membri dell'istanza di Active Directory della sottoscrizione quando viene `You do not have permissions to create…` assegnato il ruolo amministratore globale, è anche possibile ricevere un avviso nella pagina Aggiungi account di automazione. In questo caso, è possibile richiedere la rimozione dall'istanza di Active Directory della sottoscrizione e quindi richiedere di essere nuovamente aggiunto, in modo da diventare un utente completo in Active Directory.

Per verificare che la situazione che genera il messaggio di errore sia stata smistata:

1. Nel riquadro Azure Active Directory del portale di Azure selezionare **Utenti e gruppi.** 
2. Selezionare **Tutti gli utenti**.
3. Scegli il tuo nome, quindi seleziona **Profilo**. 
4. Assicurarsi che il valore dell'attributo **Tipo utente** sotto il profilo dell'utente non sia impostato su **Guest**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Autorizzazioni per configurare gli account RunAs classici

Per configurare o rinnovare gli account RunAs classici, è necessario disporre del ruolo di coamministratore a livello di sottoscrizione. Per altre informazioni sulle autorizzazioni di sottoscrizione classica, vedere Amministratori di sottoscrizione classica di Azure.To learn more about classic subscription permissions, see [Azure classic subscription administrators.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

## <a name="creating-a-run-as-account-in-azure-portal"></a>Creazione di un account RunAs nel portale di AzureCreating a RunAs account in Azure portal

Eseguire la procedura seguente per aggiornare l'account di Automazione di Azure nel portale di Azure.Perform the following steps to update your Azure Automation account in the Azure portal. Creare singolarmente gli account RunAs e RunAs classico. Se non è necessario gestire le risorse classiche, è sufficiente creare l'account RunAs di Azure.

1. Accedere al portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
2. Cercare e selezionare **Account di automazione**.
3. Nella pagina Account di automazione selezionare l'account di automazione dall'elenco.
4. Nel riquadro sinistro selezionare **Account RunAs** nella sezione delle impostazioni dell'account.
5. A seconda del tipo di account necessario, selezionare **Account RunAs di Azure** o **Account RunAs classico di Azure**. 
6. A seconda dell'account di interesse, usare il riquadro Aggiungi RunAs di Azure o **Aggiungi account RunAs classico** di Azure.Depending on the account of interest, use the **Add Azure RunAs** pane or Add Azure Classic RunAs Account pane. Dopo aver esaminato le informazioni generali, fare clic su **Crea**.
6. Mentre Azure crea l'account RunAs, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu. Viene inoltre visualizzato un banner che indica che l'account è in fase di creazione. Il completamento del processo può richiedere alcuni minuti.

## <a name="creating-a-run-as-account-using-powershell"></a>Creazione di un account RunAs tramite PowerShellCreating a RunAs account using PowerShell

Nell'elenco seguente vengono forniti i requisiti per creare un account RunAs in PowerShell.The following list provides the requirements to create a RunAs account in PowerShell. Questi requisiti si applicano a entrambi i tipi di account RunAs.

* Windows 10 o Windows Server 2016 con i moduli di Azure Resource Manager 3.4.1 e versioni successive. Lo script di PowerShell non supporta le versioni precedenti di Windows.The PowerShell script doesn't support earlier versions of Windows.
* Azure PowerShell 1.0 e versioni successive. Per informazioni su PowerShell 1.0, vedere [come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Un account di automazione, a cui `AutomationAccountName` `ApplicationDisplayName` viene fatto riferimento come valore per i parametri e .
* Autorizzazioni equivalenti a quelle elencate in [Autorizzazioni necessarie per configurare gli account RunAs](#permissions).

Per ottenere i `SubscriptionId` `ResourceGroupName`valori per , e , che è obbligatorio per lo script di PowerShell, completare i passaggi successivi.

1. Nel portale di Azure selezionare **Account di automazione**.
1. Nella pagina Account di automazione selezionare l'account di automazione.
1. Nella sezione delle impostazioni dell'account selezionare **Proprietà**.
1. Notare i valori per **NAME**, **SUBSCRIPTION ID**e **RESOURCE GROUP** nella pagina Proprietà . Questi valori corrispondono ai valori per i `AutomationAccountName`parametri di script , `SubscriptionId`, e `ResourceGroupName` PowerShell, rispettivamente.

   ![Pagina delle proprietà dell'account di automazione](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script di PowerShell per creare un account RunAs

In questa sezione viene fornito uno script di PowerShell per creare un account RunAs.This section provides a PowerShell script to create a RunAs account. Lo script include il supporto per diverse configurazioni.

* Creare un account RunAs usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato rilasciato dall'autorità di certificazione globale (enterprise).
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici.

Lo script usa più cmdlet di Azure Resource Manager per creare le risorse. Per i cmdlet e le autorizzazioni necessarie, vedere [Autorizzazioni per configurare gli account RunAs](#permissions-to-configure-run-as-accounts).

Salvare lo script nel computer utilizzando il nome file **New-RunAsAccount.ps1**.

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
>`Add-AzAccount`e `Add-AzureRMAccount` sono alias per [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). È possibile utilizzare questi cmdlet [oppure aggiornare i moduli](automation-update-azure-modules.md) nell'account di automazione alle versioni più recenti. Potrebbe essere necessario aggiornare i moduli anche se è stato appena creato un nuovo account di automazione.

### <a name="execute-the-powershell-script"></a>Eseguire lo script di PowerShellExecute the PowerShell script

1. Avviare **Windows PowerShell** con diritti utente elevati nel computer dalla schermata **Start**.
1. Dalla shell della riga di comando con privilegi elevati passare alla cartella che contiene lo script.
1. Eseguire lo script utilizzando i valori dei parametri per la configurazione desiderata.
1. Se si crea un account RunAs classico, dopo l'esecuzione dello script, caricare il certificato pubblico **(estensione** cer) nell'archivio di gestione per la sottoscrizione in cui è stato creato l'account di automazione.

Dopo l'esecuzione dello script, viene richiesto di eseguire l'autenticazione con Azure.After the script has executed, you're prompted to authenticate with Azure. Accedere con un account membro del ruolo Amministratori sottoscrizione e coamministratore della sottoscrizione.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Creare un account RunAs utilizzando un certificato autofirmatoCreate a RunAs account by using a self-signed certificate

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

Se è stato creato un account RunAs classico con un certificato pubblico dell'organizzazione (file**con estensione cer),** utilizzare questo certificato. Vedere Caricamento di un certificato API di gestione nel portale di [Azure.See Uploading a management API certificate to the Azure portal.](../azure-api-management-certs.md)

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici

```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Se è stato creato un account RunAs classico con un certificato pubblico autofirmato (file**con estensione cer),** lo script lo crea e lo salva nella cartella dei file temporanei del computer. È reperibile nel `%USERPROFILE%\AppData\Local\Temp`profilo utente, usato per eseguire la sessione di PowerShell.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Eliminazione di un account RunAs o RunAs classico

In questa sezione viene descritto come eliminare un account RunAs o Classic RunAs. Quando si esegue questa azione, l'account di Automazione viene mantenuto. Dopo aver eliminato l'account, è possibile ricrearlo nel portale di Azure.After you delete the account, you can re-create it in the Azure portal.

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nel riquadro sinistro selezionare **Account RunAs** nella sezione delle impostazioni dell'account.

3. Nella pagina delle proprietà Account RunAs selezionare l'account RunAs o l'account RunAs classico che si vuole eliminare. 

4. Nel riquadro Proprietà per l'account selezionato fare clic su **Elimina**.

   ![Eliminare un account RunAs](media/manage-runas-account/automation-account-delete-runas.png)

5. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

6. Dopo aver eliminato l'account, è possibile crearlo di nuovo nella pagina delle proprietà Account RunAs selezionando l'opzione di creazione **Account RunAs di Azure**.

   ![Ricreare l'account RunAs di Automazione](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Rinnovo di un certificato autofirmato

Il certificato autofirmato creato per l'account RunAs scade un anno dalla data di creazione. A un certo punto prima della scadenza dell'account RunAs, è necessario rinnovare il certificato. Puoi rinnovarlo in qualsiasi momento prima della scadenza. 

Quando si rinnova il certificato autofirmato, il certificato valido corrente viene mantenuto per garantire che tutti i runbook accodati o in esecuzione attiva e che eseguono l'autenticazione con l'account RunAs non vengano influenzati negativamente. Il certificato rimane valido fino alla relativa data di scadenza.

>[!NOTE]
>Se si ritiene che l'account RunAs sia stato compromesso, è possibile eliminare e ricreare il certificato autofirmato.

>[!NOTE]
>Se l'account RunAs è stato configurato per l'utilizzo di un certificato emesso dall'autorità di certificazione aziendale e si utilizza l'opzione per rinnovare un certificato autofirmato, il certificato aziendale viene sostituito da un certificato autofirmato.

Utilizzare la procedura seguente per rinnovare il certificato autofirmato.

1. Nel Portale di Azure aprire l'account di Automazione.

1. Selezionare **Account RunAs** nella sezione delle impostazioni dell'account.

    ![Riquadro delle proprietà dell'account di Automazione](media/manage-runas-account/automation-account-properties-pane.png)

1. Nella pagina delle proprietà Account RunAs selezionare l'account RunAs o l'account RunAs classico per il quale rinnovare il certificato.

1. Nel riquadro delle proprietà dell'account selezionato fare clic su **Rinnova certificato**.

    ![Rinnovare il certificato per l'account RunAs](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Durante il rinnovamento del certificato, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Configurazione del rinnovo automatico dei certificati con un runbook di automazione

Per rinnovare automaticamente i certificati, è possibile usare un runbook di automazione. Questo script su [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) abilita questa funzionalità nell'account di automazione.

>[!NOTE]
>Per eseguire lo script, è necessario essere un amministratore globale o un amministratore della società in Azure AD.

Questo script crea una pianificazione settimanale per rinnovare i certificati dell'account RunAs.This script creates a weekly schedule to renew RunAs account certificates. Aggiunge un runbook **Update-AutomationRunAsCredential** all'account di automazione. È possibile visualizzare il codice del runbook in GitHub nello script [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). È possibile usare il codice PowerShell nel file per rinnovare manualmente i certificati, in base alle esigenze.

Utilizzare la procedura seguente per testare immediatamente il processo di rinnovo.

1. Modificare il runbook **Update-AutomationRunAsCredential** e inserire un carattere di commento (-) alla riga 122, davanti al comando **Exit(1).**

   ```powershell
   #Exit(1)
   ```

2. Pubblicare il runbook.
3. Avviare il runbook.
4. Verificare il rinnovo corretto con il codice seguente:Verify successful renewal with the following code:

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

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Limitazione delle autorizzazioni dell'account RunAs

Per controllare la destinazione dell'automazione sulle risorse in Azure, è possibile eseguire lo script Update-AutomationRunAsAccountRoleAssignments.ps1.To control the targeting of Automation against resources in Azure, you can run the [Update-AutomationRunAsAccountRoleAssignments.ps1 script.](https://aka.ms/AA5hug8) Questo script modifica l'entità servizio dell'account RunAs esistente per creare e usare una definizione di ruolo personalizzata. Il ruolo dispone delle autorizzazioni per tutte le risorse ad eccezione [di Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Dopo aver eseguito lo script **Update-AutomationRunAsAccountRoleAssignments.ps1,** i runbook che accedono all'insieme di credenziali delle chiavi tramite l'utilizzo di account RunAs non funzionano più. Prima di eseguire lo script, è consigliabile esaminare i runbook nell'account per le chiamate all'insieme di credenziali delle chiavi di Azure.Before running the script, you should review runbooks in your account for calls to Azure Key Vault. Per abilitare l'accesso all'insieme di credenziali delle chiavi dai runbook di Automazione di Azure, è necessario [aggiungere l'account RunAs alle autorizzazioni dell'insieme](#add-permissions-to-key-vault)di credenziali delle chiavi.

Se è necessario limitare, inoltre ciò che l'entità servizio RunAs può fare, è possibile aggiungere altri tipi di risorse all'elemento `NotActions` della definizione del ruolo personalizzato. Nell'esempio riportato `Microsoft.Compute/*`di seguito viene limitato l'accesso a . Se si aggiunge questo `NotActions` tipo di risorsa per la definizione del ruolo, il ruolo non sarà in grado di accedere ad alcuna risorsa di calcolo. Per altre informazioni sulle definizioni di ruolo, vedere Informazioni sulle definizioni di ruolo per le risorse di Azure.To learn more about role definitions, see [Understand role definitions for Azure resources.](../role-based-access-control/role-definitions.md)

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

È possibile determinare se l'entità servizio utilizzata dall'account RunAs si trova nella definizione del ruolo Collaboratore o personalizzata. 

1. Accedi al tuo account Di automazione e seleziona **Account RunAs** nella sezione delle impostazioni dell'account.
2. Selezionare **Account RunAs**di Azure . 
3. Selezionare **Ruolo** per individuare la definizione di ruolo in uso.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

È inoltre possibile determinare la definizione di ruolo utilizzata dagli account RunAs per più sottoscrizioni o account di automazione. Eseguire questa operazione utilizzando lo script [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) in PowerShell Gallery.

### <a name="add-permissions-to-key-vault"></a>Aggiungere autorizzazioni all'insieme di credenziali delle chiavi

È possibile consentire ad Automazione di Azure di verificare se l'insieme di credenziali delle chiavi e l'entità servizio dell'account RunAs usano una definizione di ruolo personalizzata. È necessario:

* Concedere autorizzazioni all'insieme di credenziali delle chiavi.
* Impostare i criteri di accesso.

È possibile utilizzare lo script [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) in PowerShell Gallery per assegnare all'account RunAs le autorizzazioni per L'insieme di credenziali delle chiavi. Per ulteriori informazioni sull'impostazione delle autorizzazioni nell'insieme di credenziali delle chiavi, vedere [Concedere alle applicazioni l'accesso a un insieme di credenziali](../key-vault/general/group-permissions-for-apps.md) delle chiavi.

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Risoluzione dei problemi di configurazione errata per gli account RunAs

Alcuni elementi di configurazione necessari per un account RunAs o Classic RunAs potrebbero essere stati eliminati o creati in modo non corretto durante l'installazione iniziale. Le possibili istanze di configurazione non corretto includono:Possible instances of misconfiguration include:

* Asset del certificato
* Asset di connessione
* Account RunAs rimosso dal ruolo Collaboratore
* Entità servizio o applicazione in Azure AD

Per tali istanze di configurazione errata, l'account di `Incomplete` automazione rileva le modifiche e visualizza lo stato nel riquadro delle proprietà Account RunAs per l'account.

![Stato di configurazione Incompleto dell'account RunAs](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando si seleziona l'account RunAs, nel riquadro delle proprietà dell'account viene visualizzato il seguente messaggio di errore:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Per risolvere rapidamente questi problemi relativi all'account RunAs, è possibile eliminare e ricreare l'account.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sulle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/develop/app-objects-and-service-principals.md).
* Per altre informazioni sui certificati e i servizi di Azure, vedere [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).
