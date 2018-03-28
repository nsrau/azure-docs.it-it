---
title: Creare account RunAs di Automazione di Azure
description: Questo articolo descrive come aggiornare l'account di Automazione e creare account RunAs con PowerShell o dal portale.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.devlang: na
ms.tgt_pltfrm: na
ms.openlocfilehash: e7a339ae443bc61e1d2364afd8400fc436593b9f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>Aggiornare l'autenticazione dell'account di Automazione con account RunAs 
È possibile aggiornare l'account di Automazione esistente dal portale di Azure o con PowerShell se:

* Si crea un account di Automazione ma si sceglie di non creare l'account RunAs.
* Si usa già un account di Automazione per gestire le risorse di Resource Manager e lo si vuole aggiornare per includere l'account RunAs per l'autenticazione dei runbook.
* Si usa già un account di Automazione per gestire le risorse classiche e lo si vuole aggiornare per usare l'account RunAs classico anziché creare un nuovo account ed eseguire la migrazione di runbook e asset nel nuovo account.   

Questo processo crea gli elementi seguenti nell'account di Automazione.

**Per gli account RunAs:**

* Crea un'applicazione Azure AD da esportare con un certificato autofirmato, crea un account dell'entità servizio per l'applicazione in Azure AD e assegna il ruolo Collaboratore per l'account nella sottoscrizione corrente. È possibile cambiare l'impostazione in Proprietario o in qualsiasi altro ruolo. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).
* Crea un asset di certificato di Automazione denominato *AzureRunAsCertificate* nell'account di Automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dall'applicazione Azure AD.
* Crea un asset di connessione di Automazione denominato *AzureRunAsConnection* nell'account di Automazione specificato. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.

**Per gli account RunAs classici:**

* Crea un asset di certificato di Automazione denominato *AzureClassicRunAsCertificate* nell'account di Automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dal certificato di gestione.
* Crea un asset di connessione di Automazione denominato *AzureClassicRunAsConnection* nell'account di Automazione specificato. L'asset di connessione contiene il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset di certificato.

## <a name="prerequisites"></a>prerequisiti
Se si sceglie di [usare PowerShell per creare gli account RunAs](#create-run-as-account-using-powershell), il processo richiede quanto segue:

* Windows 10 e Windows Server 2016 con i moduli di Azure Resource Manager 3.4.1 e versioni successive. Lo script di PowerShell non supporta versioni precedenti di Windows.
* Azure PowerShell 1.0 e versioni successive. Per informazioni su PowerShell 1.0, vedere [come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Un account di Automazione, a cui viene fatto riferimento come valore per i parametri *–AutomationAccountName* e *-ApplicationDisplayName*.

Per ottenere i valori per i parametri *SubscriptionID*, *ResourceGroup* e *AutomationAccountName*, obbligatori per gli script, seguire questa procedura:

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Automazione**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Account di automazione**.
2. Nella pagina Account di Automazione selezionare l'account di Automazione e quindi in **Impostazioni account** selezionare **Proprietà**.  
3. Prendere nota dei valori nella pagina **Proprietà**.<br><br> ![Pannello "Proprietà" dell'account di Automazione](media/automation-create-runas-account/automation-account-properties.png)  

### <a name="required-permissions-to-update-your-automation-account"></a>Autorizzazioni necessarie per l'aggiornamento dell'account di Automazione
Per aggiornare un account di Automazione, è necessario avere le autorizzazioni e i privilegi specifici seguenti, necessari per il completamento di questo argomento.   
 
* L'account utente di AD deve essere aggiunto a un ruolo con autorizzazioni equivalenti al ruolo Collaboratore per le risorse di Microsoft.Automation, come indicato nell'articolo [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md#contributor).  
* Gli utenti non amministratori nel tenant di Azure AD possono [registrare le applicazioni di AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions) se l'opzione **Gli utenti possono registrare applicazioni** nella pagina **Impostazioni utente** è impostata su **Sì**. Se le impostazioni delle registrazioni dell'app sono impostate su **No**, l'utente che esegue questa azione deve essere un amministratore globale in Azure AD.

Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima dell'aggiunta al ruolo di amministratore globale/coamministratore della sottoscrizione, viene aggiunto ad Active Directory come guest. In questa situazione si riceve un avviso di tipo "L'utente non è autorizzato a creare..." nel pannello **Aggiungi account di Automazione**. Gli utenti che prima sono stati aggiunti al ruolo di amministratore globale/coamministratore possono essere rimossi dall'istanza di Active Directory della sottoscrizione e aggiunti nuovamente per renderli utenti completi in Active Directory. Per verificare questa situazione dal riquadro **Azure Active Directory** nel portale di Azure selezionare **Utenti e gruppi**, **Tutti gli utenti** e, dopo avere selezionato l'utente specifico, selezionare **Profilo**. Il valore dell'attributo **Tipo utente** nel profilo utente non deve essere **Guest**.

## <a name="create-run-as-account-from-the-portal"></a>Creare un account RunAs dal portale
La procedura descritta in questa sezione consente di aggiornare un account di Automazione di Azure nel portale di Azure. Creare gli account RunAs e RunAs classico separatamente. Se non è necessario gestire le risorse classiche, è sufficiente creare l'account RunAs di Azure.  

1. Accedere al Portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Automazione**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Account di automazione**.
3. Nella pagina **Account di automazione** selezionare l'account di Automazione dall'elenco.
4. Nel riquadro a sinistra selezionare **Account RunAs** nella sezione **Impostazioni account**.  
5. A seconda del tipo di account necessario, selezionare **Account RunAs di Azure** o **Account RunAs classico di Azure**. Dopo aver selezionato **Account RunAs di Azure** o **Account RunAs classico di Azure**, viene visualizzato il riquadro corrispondente. Rivedere le informazioni generali e fare clic su **Crea** per procedere con la creazione dell'account RunAs.  
6. Mentre Azure crea l'account RunAs, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu. Viene anche visualizzato un banner che indica che l'account è in fase di creazione. Il processo potrebbe richiedere alcuni minuti.  

## <a name="create-run-as-account-using-powershell-script"></a>Creare un account RunAs usando lo script di PowerShell
Questo script di PowerShell include il supporto per le configurazioni seguenti:

* Creare un account RunAs usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato.
* Creare un account RunAs e un account RunAs classico usando un certificato rilasciato dall'autorità di certificazione globale (enterprise).
* Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici.

>[!NOTE]
> Se si seleziona una delle due opzioni per creare l'account RunAs classico, dopo l'esecuzione dello script è necessario caricare il certificato pubblico, con estensione cer, nell'archivio di gestione della sottoscrizione in cui è stato creato l'account di Automazione.
> 

1. Salvare lo script seguente nel computer. Per questo esempio, salvare il file con il nome *New-RunAsAccount.ps1*.

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
        [String] $EnterpriseCertPathForRunAsAccount,
        
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
        
        $startDate = Get-Date
        $endDate = (Get-Date $PfxCert.GetExpirationDateString()).AddDays(-1)
        
        #Create an Azure AD application, AD App Credential, AD ServicePrincipal
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $startDate -EndDate $endDate 
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
        
        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
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
        
    Login-AzureRmAccount -Environment $EnvironmentName 
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

2. Avviare **Windows PowerShell** con diritti utente elevati nel computer dalla schermata **Start**.
3. Nella shell della riga di comando con privilegi elevati passare alla cartella contenente lo script creato nel passaggio 1.  
4. Eseguire lo script usando i valori dei parametri per la configurazione richiesta.

    **Creare un account RunAs usando un certificato autofirmato**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Creare un account RunAs e un account RunAs classico usando un certificato autofirmato**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Creare un account RunAs e un account RunAs classico usando un certificato enterprise**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Dopo l'esecuzione dello script, viene richiesto di autenticarsi con Azure. Accedere con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.
    >
    >

Al termine dell'esecuzione dello script, tenere presente quanto segue:
* Se è stato creato un account RunAs classico con un certificato pubblico autofirmato, con estensione cer, lo script lo crea e lo salva nella cartella dei file temporanei del computer con il profilo utente *%USERPROFILE%\AppData\Local\Temp*, usato per eseguire la sessione di PowerShell.
* Se è stato creato un account RunAs classico con un certificato pubblico enterprise, con estensione cer, usare questo certificato. Seguire le istruzioni per [caricare un certificato dell'API di gestione nel portale di Azure](../azure-api-management-certs.md) e quindi usare il [codice di esempio per l'autenticazione con le risorse della distribuzione classica di Azure](automation-verify-runas-authentication.md#classic-run-as-authentication) per convalidare la configurazione delle credenziali con tali risorse. 
* Se *non* è stato creato un account RunAs classico, eseguire l'autenticazione con le risorse di Resource Manager e convalidare la configurazione delle credenziali usando il [codice di esempio per l'autenticazione con le risorse di Service Management](automation-verify-runas-authentication.md#automation-run-as-authentication).

## <a name="limiting-run-as-account-permissions"></a>Limitazione delle autorizzazioni dell'account RunAs

Per controllare la destinazione dell'automazione in base alle risorse in Automazione di Azure, all'account RunAs vengono concessi i diritti di collaboratore nella sottoscrizione per impostazione predefinita. Se è necessario limitare le operazioni che l'entità servizio RunAs può eseguire, è possibile rimuovere l'account dal ruolo di collaboratore per la sottoscrizione e aggiungerlo come collaboratore ai gruppi di risorse che si vuole specificare.

Nel portale di Azure selezionare **Sottoscrizioni** e scegliere la sottoscrizione dell'account di Automazione. Selezionare **Controllo di accesso (IAM)** e cercare l'entità servizio per l'account di Automazione, che è simile a \<NomeAccountDiAutomazione\>_identificatore univoco. Selezionare l'account e fare clic su **Rimuovi** per rimuoverlo dalla sottoscrizione.

![Collaboratori della sottoscrizione](media/automation-create-runas-account/automation-account-remove-subscription.png)

Per aggiungere l'entità servizio a un gruppo di risorse, selezionare il gruppo di risorse nel portale di Azure e selezionare **Controllo di accesso (IAM)**. Selezionare **Aggiungi** per aprire la pagina **Aggiungi autorizzazioni**. Per **Ruolo**, selezionare **Collaboratore**. Nella casella di testo **Seleziona** digitare il nome dell'entità servizio per l'account RunAs e selezionarlo dall'elenco. È consigliabile fare clic su **Salva** per salvare le modifiche. Eseguire questa operazione per i gruppi di risorse a cui l'entità servizio RunAs di Automazione di Azure deve poter accedere.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sulle entità servizio, vedere [Oggetti applicazione e oggetti entità servizio](../active-directory/active-directory-application-objects.md).
* Per altre informazioni sui certificati e i servizi di Azure, vedere [Panoramica sui certificati per i servizi cloud di Azure](../cloud-services/cloud-services-certs-create.md).
