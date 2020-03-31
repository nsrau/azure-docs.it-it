---
title: Eseguire runbook nel ruolo di lavoro ibrido per runbook di Automazione di Azure
description: Questo articolo fornisce informazioni sull'esecuzione di runbook su computer presenti nel data center locale o in un provider di servizi cloud con il ruolo di lavoro ibrido per runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 9b9196cde45686e42d1baf7faedf94bdb73acccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367059"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Esecuzione di runbook in un ruolo di lavoro ibrido per runbook

I Runbook destinati a un ruolo di lavoro ibrido per runbook in genere gestiscono le risorse nel computer locale o in base alle risorse nell'ambiente locale in cui viene distribuito il ruolo di lavoro. I runbook usati in Automazione di Azure gestiscono in genere le risorse nel cloud di Azure. Anche se vengono usati in modo diverso, i runbook eseguiti in Automazione di Azure e i runbook eseguiti in un ruolo di lavoro ibrido per runbook sono identici nella struttura.

Quando si crea un runbook per l'esecuzione in un ruolo di lavoro ibrido per runbook, è necessario modificare e testare il runbook nel computer che ospita il worker. Il computer host dispone di tutti i moduli di PowerShell e l'accesso alla rete necessari per gestire e accedere alle risorse locali. Dopo aver testato il runbook nel computer Hybrid Runbook Worker, è possibile caricarlo nell'ambiente di automazione di Azure, dove può essere eseguito nel computer di lavoro. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorizzazioni del runbook per un ruolo di lavoro ibrido per runbookRunbook

Poiché accedono a risorse non Azure, i runbook in esecuzione in un ruolo di lavoro ibrido per runbook non possono usare il meccanismo di autenticazione usato in genere dai runbook che eseguono l'autenticazione alle risorse di Azure.As they are accessing non-Azure resources, runbooks running on a Hybrid Runbook Worker can't use the authentication mechanism typically used by runbooks authenticating to Azure resources. Un runbook fornisce la propria autenticazione alle risorse locali oppure configura l'autenticazione usando le identità gestite per le risorse di Azure.A runbook either provides its own authentication to local resources, or configures authentication using [managed identities for Azure resources.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) È inoltre possibile specificare un account RunAs per fornire un contesto utente per tutti i runbook.

### <a name="runbook-authentication"></a>Autenticazione dei runbook

Per impostazione predefinita, i runbook vengono eseguiti nel computer locale. Per Windows, vengono eseguiti nel contesto dell'account di sistema locale. Per Linux, vengono eseguiti nel contesto dell'account utente speciale **nxautomation**. In entrambi gli scenari, i runbook devono fornire la propria autenticazione alle risorse a cui accedono.

È possibile usare le risorse [Credenziali](automation-credentials.md) e [Certificato](automation-certificates.md) nel runbook con cmdlet che consentono di specificare le credenziali in modo che il runbook possa eseguire l'autenticazione in risorse diverse. L'esempio seguente illustra una parte di un runbook che riavvia un computer. Recupera le credenziali da un asset delle credenziali e il nome del computer `Restart-Computer` da un asset variabile e quindi utilizza questi valori con il cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

È inoltre possibile utilizzare un'attività [InlineScript.You](automation-powershell-workflow.md#inlinescript) can also use an InlineScript activity. `InlineScript`consente di eseguire blocchi di codice in un altro computer con le credenziali specificate dal [parametro comune PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>account RunAs

Anziché fare in modo che il runbook fornisca la propria autenticazione alle risorse locali, è possibile specificare un account RunAs per un gruppo di lavoro ibrido per runbook. A tale scopo, è necessario definire un [asset delle credenziali](automation-credentials.md) con accesso alle risorse locali. Queste risorse includono archivi certificati e tutti i runbook vengono eseguiti con queste credenziali in un ruolo di lavoro ibrido per runbook nel gruppo.

Il nome utente per le credenziali deve essere in uno dei formati seguenti:

* dominio\nome utente
* username@domain
* nome utente (per gli account locali nel computer locale)

Utilizzare la procedura seguente per specificare un account RunAs per un gruppo di lavoro Runbook ibrido.

1. Creare un [asset credenziali](automation-credentials.md) con accesso alle risorse locali.
2. Nel portale di Azure aprire l'account di automazione.
3. Selezionare il riquadro **Gruppi di ruoli di lavoro ibridi** e quindi il gruppo.
4. Selezionare **Tutte le impostazioni**, seguito dalle impostazioni del gruppo di lavoro **ibrido**.
5. Modificare il valore di **Esegui come** da **Predefinito** a **Personalizzato**.
6. Selezionare le credenziali e fare clic su **Salva**.

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Identità gestite per le risorse di AzureManaged Identities for Azure Resources

I ruoli client ibridi I ruoli di lavoro nelle macchine virtuali di Azure possono usare le identità gestite per le risorse di Azure per l'autenticazione nelle risorse di Azure.Hybrid Runbook Workers on Azure virtual machines can use managed identities for Azure resources to authenticate to Azure resources. L'uso di identità gestite per le risorse di Azure anziché per gli account RunAs offre vantaggi perché non è necessario:Using managed identities for Azure resources of Run As accounts provides benefits because you don't need to:

* Esportare il certificato RunAs e quindi importarlo nel ruolo di lavoro ibrido per runbook.
* Rinnovare il certificato utilizzato dall'account RunAs.
* Gestire l'oggetto connessione RunAs nel codice del runbook.

Seguire i passaggi successivi per usare un'identità gestita per le risorse di Azure in un ruolo di lavoro ibrido per runbook.

1. Creare una macchina virtuale di Azure.Create an Azure VM.
2. Configurare le identità gestite per le risorse di Azure nella macchina virtuale. Vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale usando il portale di Azure.See Configure managed identities for Azure resources on a VM using the Azure portal.](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Resource Manager.Give the VM access to a resource group in Resource Manager. Fare riferimento a [Usare un'identità gestita assegnata dal sistema di macchine virtuali Windows per accedere a Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installare il ruolo di lavoro ibrido per runbook nella macchina virtuale. Vedere Distribuire un ruolo di lavoro ibrido per [runbook di Windows.](automation-windows-hrw-install.md)
5. Aggiornare il runbook per usare il `Identity` cmdlet Connect-AzAccount con il parametro per l'autenticazione alle risorse di Azure.Update the runbook to use the [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) cmdlet with the parameter to authenticate to Azure resources. Questa configurazione riduce la necessità di utilizzare un account RunAs ed eseguire la gestione degli account associata.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`funziona per un ruolo di lavoro ibrido per runbook utilizzando un'identità assegnata dal sistema e una singola identità assegnata dall'utente. Se si usano più identità assegnate dall'utente nel ruolo di lavoro ibrido, il runbook deve specificare il parametro *AccountId* per `Connect-AzAccount` selezionare un'identità assegnata dall'utente specifica.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Account RunAs di Automazione

Come parte del processo di compilazione automatizzato per la distribuzione delle risorse in Azure, potrebbe essere necessario l'accesso ai sistemi locali per supportare un'attività o un set di passaggi nella sequenza di distribuzione. Per fornire l'autenticazione in Azure usando l'account RunAs, è necessario installare il certificato dell'account RunAs.To provide authentication against Azure using the RunAs account, you must install the RunAs account certificate.

Il runbook di PowerShell seguente, denominato Export-RunAsCertificateToHybridWorker , esporta il certificato RunAs dall'account di Automazione di Azure.The following PowerShell runbook, called **Export-RunAsCertificateToHybridWorker**, exports the RunAs certificate from your Azure Automation account. Il runbook scarica e importa il certificato nell'archivio certificati del computer locale in un ruolo di lavoro ibrido per runbook connesso allo stesso account. Al termine di questo passaggio, il runbook verifica che il ruolo di lavoro possa eseguire correttamente l'autenticazione in Azure usando l'account RunAs.Once it completes that step, the runbook verifies that the worker can successfully authenticate to Azure using the RunAs account.

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Per i runbook `Add-AzAccount` `Add-AzureRMAccount` di PowerShell `Connect-AzAccount`e sono alias per . Quando si cercano gli elementi `Connect-AzAccount`della libreria, `Add-AzAccount`se non si visualizza , è possibile utilizzare , oppure aggiornare i moduli nell'account di automazione.

Per completare la preparazione dell'account RunAs:

1. Salvare il runbook **Export-RunAsCertificateToHybridWorker** nel computer con estensione **ps1.**
2. Importalo nel tuo account Automation.
3. Modificare il runbook, cambiando `Password` il valore della variabile o la propria password. 
4. Pubblicare il runbook.
5. Eseguire il runbook, destinato al gruppo Hybrid Runbook Worker che esegue e autentica i runbook utilizzando l'account RunAs. 
6. Esaminare il flusso di lavoro per verificare che venga segnalato il tentativo di importazione del certificato nell'archivio del computer locale e che segue con più righe. Questo comportamento dipende dal numero di account di automazione definiti nella sottoscrizione e dal grado di esito positivo dell'autenticazione.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Comportamento del processo nei ruoli di lavoro per runbook ibridi

Automazione di Azure gestisce i processi nei ruoli di lavoro dei runbook ibridi in modo leggermente diverso rispetto ai processi eseguiti nelle sandbox di Azure.Azure Automation handles jobs on Hybrid Runbook Workers somely differently from jobs run in Azure sandboxes. Una differenza fondamentale è che non c'è limite alla durata del lavoro per i lavoratori runbook. I runbook eseguiti nelle sandbox di Azure sono limitati a tre ore a causa della [condivisione equa.](automation-runbook-execution.md#fair-share)

Per un runbook a esecuzione prolungata, si desidera assicurarsi che sia resiliente al possibile riavvio, ad esempio, se il computer che ospita il worker viene riavviato. Se il computer host Hybrid Runbook Worker viene riavviato, qualsiasi processo runbook in esecuzione viene riavviato dall'inizio o dall'ultimo checkpoint per i runbook del flusso di lavoro di PowerShell.If the Hybrid Runbook Host host machine reboots, any running runbook job restarts from the beginning, or from the last checkpoint for PowerShell Workflow runbooks. Dopo che un processo runbook viene riavviato più di tre volte, viene sospeso.

Tenere presente che i processi per i ruoli di lavoro ibridi Runbook vengono eseguiti con l'account di sistema locale in Windows o l'account **nxautomation** su Linux. Per Linux, è necessario assicurarsi che l'account **nxautomation** abbia accesso al percorso in cui sono archiviati i moduli del runbook. Quando si utilizza il cmdlet [Install-Module,](/powershell/module/powershellget/install-module) assicurarsi `Scope` di specificare AllUsers per il parametro per assicurarsi che l'account **nxautomation** disponga dell'accesso. Per altre informazioni su PowerShell in Linux, vedere [Problemi noti di PowerShell su piattaforme non Windows.](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms)

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Avvio di un runbook in un ruolo di lavoro ibrido per runbookStarting a runbook on a Hybrid Runbook Worker

[L'avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md) descrive diversi metodi per l'avvio di un runbook. Avvio per un runbook in un ruolo di lavoro ibrido per runbook usa un'opzione **Esegui su** che consente di specificare il nome di un gruppo di lavoro ibrido per runbook. Quando viene specificato un gruppo, uno dei lavoratori di tale gruppo recupera ed esegue il runbook. Se il runbook non specifica questa opzione, Automazione di Azure esegue il runbook come di consueto.

Quando si avvia un runbook nel portale di Azure, viene visualizzata l'opzione **Esegui su** per cui è possibile selezionare **Azure** o **Hybrid Worker**. Se si seleziona **Lavoratore ibrido**, è possibile scegliere il gruppo Hybrid Runbook Worker da un elenco a discesa.

Utilizzare `RunOn` il parametro con il `Start-AzureAutomationRunbook` cmdlet. Nell'esempio seguente viene utilizzato Windows PowerShell per avviare un runbook denominato Test-Runbook in un gruppo di lavoro ibrido per runbook denominato MyHybridGroup.The following example uses Windows PowerShell to start a runbook named **Test-Runbook** on a Hybrid Runbook Worker group named MyHybridGroup.

```azurepowershell-interactive
Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Il `RunOn` parametro `Start-AzureAutomationRunbook` è stato aggiunto alla versione 0.9.1 di Microsoft Azure PowerShell.The parameter was added to in version 0.9.1 of Microsoft Azure PowerShell. È consigliabile [scaricare la versione più recente](https://azure.microsoft.com/downloads/) se la versione installata è precedente. Installare questa versione solo nella workstation in cui si sta avviando il runbook da PowerShell. Non è necessario installarlo nel computer di lavoro ibrido per runbook a meno che non si intenda avviare l'esecuzione di libri da questo computer.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Utilizzo di runbook firmati in un runbook di Windows Hybrid Runbook Worker

È possibile configurare un ruolo di lavoro ibrido ibrido di Windows per eseguire solo runbook firmati.

> [!IMPORTANT]
> Dopo aver configurato un ruolo di lavoro ibrido per runbook per eseguire solo i runbook firmati, i runbook che non stati firmati non verranno eseguiti nel ruolo di lavoro.

### <a name="create-signing-certificate"></a>Creare il certificato di firma

L'esempio seguente crea un certificato autofirmato che può essere usato per la firma dei runbook. Questo codice crea il certificato e lo esporta in modo che il ruolo di lavoro ibrido per runbook possa importarlo in un secondo momento. L'identificazione personale viene restituita anche per un utilizzo successivo nel riferimento al certificato.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
                                        -Subject "CN=contoso.com" `
                                        -KeyAlgorithm RSA `
                                        -KeyLength 2048 `
                                        -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
                                        -KeyExportPolicy Exportable `
                                        -KeyUsage DigitalSignature `
                                        -Type CodeSigningCert


# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importare il certificato e configurare i ruoli di lavoro per la convalida della firmaImport certificate and configure workers for signature validation

Copiare il certificato creato in ogni ruolo di lavoro ibrido per runbook in un gruppo. Eseguire lo script seguente per importare il certificato e configurare i worker per l'utilizzo della convalida della firma nei runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Firmare i runbook usando il certificato

Con i ruoli di lavoro per runbook ibridi configurati per l'utilizzo solo di runbook firmati, è necessario firmare i runbook che devono essere usati nel ruolo di lavoro ibrido per runbook. Usare il codice PowerShell di esempio seguente per firmare questi runbook.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando un runbook è stato firmato, è necessario importarlo nell'account Di automazione e pubblicarlo con il blocco della firma. Per informazioni su come importare i runbook,vedere [Importazione di un runbook da un file in Automazione di Azure](manage-runbooks.md#import-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Utilizzo di runbook firmati in un runbook ibrido LinuxWorking with signed runbooks on a Linux Hybrid Runbook Worker

Per poter utilizzare i runbook firmati, un runbook worker ibrido Linux deve avere l'eseguibile [GPG](https://gnupg.org/index.html) nel computer locale.

> [!IMPORTANT]
> Dopo aver configurato un ruolo di lavoro ibrido per runbook per eseguire solo i runbook firmati, i runbook che non stati firmati non verranno eseguiti nel ruolo di lavoro.

### <a name="create-a-gpg-keyring-and-keypair"></a>Creare un keyring e una coppia di chiavi di GPG

Per creare il keyring GPG e la coppia di chiavi, utilizzare l'account Hybrid Runbook Worker **nxautomation.**

1. Utilizzare l'applicazione sudo per accedere come account **nxautomation.**

    ```bash
    sudo su – nxautomation
    ```

2. Una volta che si utilizza **nxautomation**, generare la coppia di chiavi GPG. GPG guida l'utente attraverso i passaggi. È necessario fornire nome, indirizzo di posta elettronica, data di scadenza e passphrase. Quindi si attende fino a quando non vi è sufficiente entropia sulla macchina per la chiave da generare.

    ```bash
    sudo gpg --generate-key
    ```

3. Poiché la directory GPG è stata generata con sudo, è necessario modificarne il proprietario in **nxautomation** utilizzando il comando seguente.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Rendere il portachiavi disponibile per il ruolo di lavoro ibrido per runbook

Dopo aver creato il keyring, renderlo disponibile per il ruolo di lavoro ibrido per runbook. Modificare il file di impostazioni **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf** in modo `[worker-optional]`da includere il codice di esempio seguente nella sezione file .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verificare che la convalida della firma sia attivata

Se la convalida della firma è stata disabilitata nel computer, è necessario attivarla eseguendo il comando sudo seguente. Sostituire `<LogAnalyticsworkspaceId>` con l'ID dell'area di lavoro.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Firmare un runbook

Dopo aver configurato la convalida della firma, utilizzare il comando GPG seguente per firmare un runbook.

```bash
gpg –-clear-sign <runbook name>
```

Il runbook firmato `<runbook name>.asc`è chiamato .

È ora possibile caricare il runbook firmato in Automazione di Azure ed eseguirlo come un normale runbook.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui metodi per avviare un runbook, vedere [Avvio di un runbook in Automazione di Azure.To](automation-starting-a-runbook.md)learn more about the methods for starting a runbook, see Starting a Runbook in Azure Automation.
* Per informazioni su come usare l'editor testuale per usare i runbook di PowerShell in Automazione di Azure, vedere [Modifica di un runbook in Automazione di Azure.To](automation-edit-textual-runbook.md)understand how to use the textual editor to work with PowerShell runbooks in Azure Automation, see Editing a Runbook in Azure Automation.
* Se i runbook non vengono completati correttamente, consultare la guida alla risoluzione dei problemi relativa agli errori di esecuzione dei [runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere Documenti di [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
