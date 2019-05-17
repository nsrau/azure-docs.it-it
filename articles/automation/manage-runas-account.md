---
title: Gestire account RunAs di Automazione di Azure
description: Questo articolo descrive come gestire account RunAs con PowerShell o dal portale.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/03/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2a104c9e41ef1843c377b0406b499ffae504db97
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595692"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gestire account RunAs di Automazione di Azure

Gli account RunAs in Automazione di Azure offrono l'autenticazione per la gestione delle risorse in Azure con i cmdlet di Azure.

Quando si crea un account RunAs, viene creato un nuovo utente dell'entità servizio in Azure Active Directory, a cui viene assegnato il ruolo Collaboratore a livello della sottoscrizione. Per i runbook che usano i ruoli di lavoro ibridi per runbook nelle macchine virtuali di Azure, è possibile usare [identità gestite per le risorse di Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) invece degli account RunAs per l'autenticazione con le risorse di Azure.

Esistono due tipi di account RunAs:

* **Account RunAs di Azure**: questo account viene usato per gestire le risorse del modello di distribuzione di Resource Manager.
  * Crea un'applicazione Azure AD da esportare con un certificato autofirmato, crea un account dell'entità servizio per l'applicazione in Azure AD e assegna il ruolo Collaboratore per l'account nella sottoscrizione corrente. È possibile cambiare l'impostazione in Proprietario o in qualsiasi altro ruolo. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
  * Crea un asset di certificato di Automazione denominato *AzureRunAsCertificate* nell'account di Automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dall'applicazione Azure AD.
  * Crea un asset di connessione di Automazione denominato *AzureRunAsConnection* nell'account di Automazione specificato. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.

* **Account RunAs classico di Azure**: questo account viene usato per gestire le risorse classiche del modello di distribuzione.
  * Crea un certificato di gestione nella sottoscrizione
  * Crea un asset di certificato di Automazione denominato *AzureClassicRunAsCertificate* nell'account di Automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dal certificato di gestione.
  * Crea un asset di connessione di Automazione denominato *AzureClassicRunAsConnection* nell'account di Automazione specificato. L'asset di connessione contiene il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset di certificato.
  * Deve essere un coamministratore della sottoscrizione per creare o rinnovo
  
  > [!NOTE]
  > Le sottoscrizioni Cloud Solution Provider di Azure (Azure CSP) supportano solo il modello Azure Resource Manager, i servizi non Azure Resource Manager non sono disponibili nel programma. Quando si usa una sottoscrizione CSP, l'Account RunAs di Azure non verrà creato. L'Account RunAs di Azure viene comunque creato. Per altre informazioni sulle sottoscrizioni CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

  > [!NOTE]
  > L'entità servizio per un Account runas non dispone di autorizzazioni per la lettura di Azure Active Directory per impostazione predefinita. Se si desidera aggiungere autorizzazioni di lettura o la gestione di Azure Active directory, è necessario concedere tale autorizzazione per l'entità nel servizio **le autorizzazioni API**. Per altre informazioni, vedere [aggiungere le autorizzazioni per accedere alle API web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a>Autorizzazioni per la configurazione degli account RunAs

Per creare o aggiornare un account RunAs, è necessario avere autorizzazioni e privilegi specifici. Un amministratore globale o un coamministratore può eseguire tutte le attività. Nel caso in cui i compiti siano separati, nella tabella seguente è disponibile un elenco delle attività, il relativo cmdlet e le autorizzazioni necessarie:

|Attività|Cmdlet  |Autorizzazioni minime  |Dove impostare le autorizzazioni|
|---|---------|---------|---|
|Creare un'applicazione Azure AD|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Ruolo Sviluppatore di applicazioni<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure Active Directory > Registrazioni per l'app |
|Aggiungere una credenziale all'applicazione.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Amministratore dell'applicazione o AMMINISTRATORE GLOBALE<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure Active Directory > Registrazioni per l'app|
|Creare e ottenere un'entità servizio di Azure AD|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Amministratore dell'applicazione o AMMINISTRATORE GLOBALE        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Home > Azure Active Directory > Registrazioni per l'app|
|Assegnare o ottenere il ruolo Controllo degli accessi in base al ruolo per l'entità specificata|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Proprietario o Amministratore Accesso utenti        | [Sottoscrizione](../role-based-access-control/role-assignments-portal.md)</br>Home > Sottoscrizioni > \<nome della sottoscrizione\> - Controllo di accesso (IAM)|
|Creare o rimuovere un certificato di Automazione|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Collaboratore nel gruppo di risorse         |Gruppo di risorse account di Automazione|
|Creare o rimuovere una connessione di Automazione|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Collaboratore nel gruppo di risorse |Gruppo di risorse account di Automazione|

<sup>1</sup> Gli utenti non amministratori nel tenant di Azure AD possono [registrare le applicazioni di AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se l'opzione **Gli utenti possono registrare applicazioni** del tenant di Azure AD nella pagina **Impostazioni utente** è impostata su **Sì**. Se le impostazioni delle registrazioni dell'app sono impostate su **No**, l'utente che esegue questa azione deve essere un amministratore globale in Azure AD.

Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima dell'aggiunta al ruolo di amministratore globale/coamministratore della sottoscrizione, viene aggiunto come guest. In questo caso si riceverà un avviso `You do not have permissions to create…` nella pagina **Aggiungi account di Automazione**. Gli utenti che prima sono stati aggiunti al ruolo di amministratore globale/coamministratore possono essere rimossi dall'istanza di Active Directory della sottoscrizione e aggiunti nuovamente per renderli utenti completi in Active Directory. Per verificare questa situazione dal riquadro **Azure Active Directory** nel portale di Azure selezionare **Utenti e gruppi**, **Tutti gli utenti** e, dopo avere selezionato l'utente specifico, selezionare **Profilo**. Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.

## <a name="permissions-classic"></a>Autorizzazioni per configurare gli account RunAs classico

Per configurare o rinnovare gli account RunAs classico, è necessario disporre di **CO-amministratore** ruolo a livello di sottoscrizione. Per altre informazioni sulle autorizzazioni del modello di distribuzione classica, vedere [gli amministratori di sottoscrizione di Azure classico](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Creare un account RunAs nel portale

La procedura descritta in questa sezione consente di aggiornare un account di Automazione di Azure nel portale di Azure. Creare gli account RunAs e RunAs classico separatamente. Se non è necessario gestire le risorse classiche, è sufficiente creare l'account RunAs di Azure.  

1. Accedere al Portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Automazione**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Account di automazione**.
3. Nella pagina **Account di automazione** selezionare l'account di Automazione dall'elenco.
4. Nel riquadro a sinistra selezionare **Account RunAs** nella sezione **Impostazioni account**.  
5. A seconda del tipo di account necessario, selezionare **Account RunAs di Azure** o **Account RunAs classico di Azure**. Dopo aver selezionato **Account RunAs di Azure** o **Account RunAs classico di Azure**, viene visualizzato il riquadro corrispondente. Rivedere le informazioni generali e fare clic su **Crea** per procedere con la creazione dell'account RunAs.  
6. Mentre Azure crea l'account RunAs, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu. Viene anche visualizzato un banner che indica che l'account è in fase di creazione. Il processo potrebbe richiedere alcuni minuti.  

## <a name="create-run-as-account-using-powershell"></a>Creare un account RunAs tramite PowerShell

## <a name="prerequisites"></a>Prerequisiti

L'elenco seguente include i requisiti per creare un account RunAs in PowerShell:

* Windows 10 o Windows Server 2016 con i moduli di Azure Resource Manager 3.4.1 e versioni successive. Lo script di PowerShell non supporta versioni precedenti di Windows.
* Azure PowerShell 1.0 e versioni successive. Per informazioni su PowerShell 1.0, vedere [come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Un account di Automazione, a cui viene fatto riferimento come valore per i parametri *–AutomationAccountName* e *-ApplicationDisplayName*.
* Autorizzazioni equivalenti a quanto indicato in [Autorizzazioni per la configurazione degli account RunAs](#permissions)

Per ottenere i valori per i parametri *SubscriptionID*, *ResourceGroup* e *AutomationAccountName*, obbligatori per gli script, seguire questa procedura:

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Automazione**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Account di automazione**.
1. Nella pagina Account di Automazione selezionare l'account di Automazione e quindi in **Impostazioni account** selezionare **Proprietà**.  
1. Prendere nota dei valori di **ID sottoscrizione**, **Nome** e **Gruppo di risorse** nella pagina **Proprietà**.

   ![Pagina "Proprietà" dell'account di Automazione](media/manage-runas-account/automation-account-properties.png)

Questo script di PowerShell include il supporto per le configurazioni seguenti:

* Creare un account RunAs usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato rilasciato dall'autorità di certificazione globale (enterprise).
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici.

>[!NOTE]
> Se si seleziona una delle due opzioni per creare l'account RunAs classico, dopo l'esecuzione dello script è necessario caricare il certificato pubblico, con estensione cer, nell'archivio di gestione della sottoscrizione in cui è stato creato l'account di Automazione.

1. Salvare lo script seguente nel computer. Per questo esempio, salvare il file con il nome *New-RunAsAccount.ps1*.

   Lo script usa più cmdlet di Azure Resource Manager per creare le risorse. La tabella seguente illustra i cmdlet e le relative autorizzazioni necessarie.

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
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

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
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
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

    > [!IMPORTANT]
    > **Add-AzureRmAccount** è ora un alias per **Connect-AzureRMAccount**. Quando si esegue la ricerca tra gli elementi della libreria, se **Connect-AzureRMAccount** non viene visualizzato, è possibile usare **Add-AzureRmAccount** oppure [aggiornare i moduli](automation-update-azure-modules.md) nell'account di Automazione.

1. Avviare **Windows PowerShell** con diritti utente elevati nel computer dalla schermata **Start**.
1. Nella shell della riga di comando con privilegi elevati passare alla cartella contenente lo script creato nel passaggio 1.  
1. Eseguire lo script usando i valori dei parametri per la configurazione richiesta.

    **Creare un account RunAs usando un certificato autofirmato**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Creare un account RunAs e un account RunAs classico usando un certificato autofirmato**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Creare un account RunAs e un account RunAs classico usando un certificato enterprise**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici**
  
    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > Dopo l'esecuzione dello script, viene richiesto di autenticarsi con Azure. Accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

Al termine dell'esecuzione dello script, tenere presente quanto segue:

* Se è stato creato un account RunAs classico con un certificato pubblico autofirmato, con estensione cer, lo script lo crea e lo salva nella cartella dei file temporanei del computer con il profilo utente *%USERPROFILE%\AppData\Local\Temp*, usato per eseguire la sessione di PowerShell.

* Se è stato creato un account RunAs classico con un certificato pubblico enterprise, con estensione cer, usare questo certificato. Seguire le istruzioni per il [caricamento di un certificato di gestione API nel portale di Azure](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Eliminare un account RunAs o un account RunAs classico

Questa sezione illustra come eliminare e creare nuovamente un account RunAs o un account RunAs classico. Quando si esegue questa azione, l'account di Automazione viene mantenuto. Dopo aver eliminato l'account RunAs o l'account RunAs classico, è possibile ricrearlo nel portale di Azure.

1. Nel Portale di Azure aprire l'account di Automazione.

2. Nella pagina **Account di Automazione** selezionare **Account RunAs**.

3. Nella pagina delle proprietà **Account RunAs** selezionare l'account RunAs o l'account RunAs classico che si vuole eliminare. Nel riquadro **Proprietà** per l'account selezionato fare quindi clic su **Elimina**.

   ![Eliminare un account RunAs](media/manage-runas-account/automation-account-delete-runas.png)

1. Durante l'eliminazione dell'account, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

1. Dopo aver eliminato l'account, è possibile crearlo di nuovo nella pagina delle proprietà **Account RunAs** selezionando l'opzione di creazione **Account RunAs di Azure**.

   ![Ricreare l'account RunAs di Automazione](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Rinnovo del certificato autofirmato

Prima della scadenza dell'account RunAs, è necessario rinnovare il certificato. Se si ritiene che l'account RunAs sia stato compromesso, è possibile eliminarlo e crearlo nuovamente. Questa sezione illustra come eseguire tali operazioni.

Il certificato autofirmato creato per l'account RunAs scade un anno dopo la data di creazione. È possibile rinnovarlo in qualsiasi momento prima della scadenza. Quando si procede al rinnovo, il certificato valido corrente viene mantenuto per evitare di influire negativamente su eventuali runbook messi in coda o in esecuzione, che eseguono l'autenticazione con l'account RunAs. Il certificato rimane valido fino alla relativa data di scadenza.

> [!NOTE]
> Se l'account RunAs di Automazione è stato configurato per l'uso di un certificato rilasciato dall'autorità di certificazione aziendale e si usa questa opzione, il certificato viene sostituito da un certificato autofirmato.

Per rinnovare il certificato, seguire questa procedura:

1. Nel Portale di Azure aprire l'account di Automazione.

1. In **Impostazioni account** selezionare **Account RunAs**.

    ![Riquadro delle proprietà dell'account di Automazione](media/manage-runas-account/automation-account-properties-pane.png)

1. Nella pagina delle proprietà **Account RunAs** selezionare l'account RunAs o l'account RunAs classico per cui si vuole rinnovare il certificato.

1. Nel riquadro **Proprietà** per l'account selezionato fare clic su **Rinnova certificato**.

    ![Rinnovare il certificato per l'account RunAs](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Durante il rinnovamento del certificato, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

## <a name="limiting-run-as-account-permissions"></a>Limitazione delle autorizzazioni dell'account RunAs

Per controllare la destinazione dell'automazione in base alle risorse in Automazione di Azure, all'account RunAs vengono concessi i diritti di collaboratore nella sottoscrizione per impostazione predefinita. Se è necessario limitare le operazioni che l'entità servizio RunAs può eseguire, è possibile rimuovere l'account dal ruolo di collaboratore per la sottoscrizione e aggiungerlo come collaboratore ai gruppi di risorse che si vuole specificare.

Nel portale di Azure selezionare **Sottoscrizioni** e scegliere la sottoscrizione dell'account di Automazione. Selezionare **Controllo di accesso (IAM)** e quindi la scheda **Assegnazioni di ruolo**. Cercare l'entità servizio per l'account di Automazione, che è simile a \<NomeAccountDiAutomazione\>_identificatore univoco. Selezionare l'account e fare clic su **Rimuovi** per rimuoverlo dalla sottoscrizione.

![Collaboratori della sottoscrizione](media/manage-runas-account/automation-account-remove-subscription.png)

Per aggiungere l'entità servizio a un gruppo di risorse, selezionare il gruppo di risorse nel portale di Azure e selezionare **Controllo di accesso (IAM)**. Selezionare **Aggiungi assegnazione di ruolo** per aprire la pagina **Aggiungi assegnazione di ruolo**. Per **Ruolo**, selezionare **Collaboratore**. Nella casella di testo **Seleziona** digitare il nome dell'entità servizio per l'account RunAs e selezionarlo dall'elenco. È consigliabile fare clic su **Salva** per salvare le modifiche. Eseguire questa procedura per i gruppi di risorse a cui l'entità servizio RunAs di Automazione di Azure deve poter accedere.

## <a name="misconfiguration"></a>Errore di configurazione

Alcuni elementi di configurazione necessari per il corretto funzionamento dell'account RunAs o dell'account RunAs classico potrebbero essere stati eliminati o non essere stati creati correttamente durante la configurazione iniziale. Tali elementi includono:

* Asset del certificato
* Asset di connessione
* Account RunAs rimosso dal ruolo di collaboratore
* Entità servizio o applicazione in Azure AD

In caso di errore di configurazione, l'account di Automazione rileva le modifiche e visualizza lo stato *Incompleto* nel riquadro delle proprietà **Account RunAs** per l'account.

![Stato di configurazione Incompleto dell'account RunAs](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando si seleziona l'account RunAs, nel riquadro **Proprietà** dell'account viene visualizzato il messaggio di errore seguente:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Per risolvere rapidamente questi problemi relativi all'account RunAs, è possibile eliminare e ricreare l'account.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/develop/app-objects-and-service-principals.md).
* Per altre informazioni sui certificati e i servizi di Azure, vedere [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).