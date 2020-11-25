---
title: Eseguire runbook di Automazione di Azure in un ruolo di lavoro ibrido per runbook
description: Questo articolo descrive come eseguire manuali operativi nei computer nel Data Center locale o in un altro provider di servizi cloud con Hybrid Runbook workers.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: 2f1c703f2bd2e90e15c566b7e04e8a878c16f6de
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001270"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Eseguire runbook in un ruolo di lavoro ibrido per runbook

I runbook eseguiti in un [ruolo di lavoro ibrido per runbook](automation-hybrid-runbook-worker.md) in genere gestiscono risorse nel computer locale o in base a risorse nell'ambiente locale in cui il ruolo di lavoro è stato distribuito. I runbook usati in Automazione di Azure gestiscono in genere le risorse nel cloud di Azure. Anche se vengono usati in modo diverso, i runbook eseguiti in Automazione di Azure e quelli eseguiti in un ruolo di lavoro ibrido per runbook hanno la stessa struttura.

Quando si crea un runbook da eseguire in un ruolo di lavoro ibrido per runbook, è consigliabile modificarlo e testarlo all'interno del computer che ospita il ruolo di lavoro. Il computer host ha tutti i moduli di PowerShell e i diritti di accesso di rete necessari per gestire le risorse locali. Dopo aver testato il runbook nel computer con il ruolo di lavoro ibrido per runbook, è possibile caricarlo nell'ambiente di Automazione di Azure, dove può essere eseguito nel ruolo di lavoro.

## <a name="plan-runbook-job-behavior"></a>Pianificare il comportamento dei processi dei runbook

Automazione di Azure gestisce i processi nei ruoli di lavoro ibridi per Runbook in modo diverso rispetto ai processi eseguiti in sandbox di Azure. Se si ha un runbook a esecuzione prolungata, assicurarsi che sia resiliente a un eventuale riavvio. Per informazioni dettagliate sul comportamento dei processi, vedere [Processi di ruoli di lavoro ibridi per runbook](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

I processi per i ruoli di lavoro ibridi per Runbook vengono eseguiti con l'account di **sistema** locale in Windows o con l'account **nxautomation** in Linux. Per Linux, verificare che l'account **nxautomation** disponga dell'accesso al percorso in cui sono archiviati i moduli Runbook. Quando si usa il cmdlet [Install-Module](/powershell/module/powershellget/install-module), assicurarsi di specificare AllUsers per il parametro `Scope`, per garantire che l'account **nxautomation** disponga dell'accesso. Per altre informazioni su PowerShell in Linux, vedere [Problemi noti di PowerShell in piattaforme non Windows](/powershell/scripting/whats-new/known-issues-ps6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="configure-runbook-permissions"></a>Configurare le autorizzazioni di Runbook

Definire le autorizzazioni per l'esecuzione dei runbook nel ruolo di lavoro ibrido per runbook nei modi seguenti:

* Fare in modo che il runbook fornisca i propri dati di autenticazione alle risorse locali.
* Configurare l'autenticazione usando [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
* Specificare un account RunAs per rendere disponibile un contesto utente per tutti i runbook.

### <a name="use-runbook-authentication-to-local-resources"></a>Usare l'autenticazione dei runbook alle risorse locali

Se si intende predisporre un runbook che fornisca alle risorse i propri dati di autenticazione, usare asset di tipo [credenziale](./shared-resources/credentials.md) e [certificato](./shared-resources/certificates.md) all'interno del runbook. Sono disponibili diversi cmdlet che consentono di specificare credenziali in modo che il runbook possa eseguire l'autenticazione a risorse diverse. L'esempio seguente illustra una parte di un runbook che riavvia un computer. Recupera le credenziali da un asset credenziali e il nome del computer da un asset variabile e quindi usa questi valori con il cmdlet `Restart-Computer`.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

È anche possibile usare un'attività [InlineScript](automation-powershell-workflow.md#use-inlinescript). `InlineScript` consente di eseguire blocchi di codice in un altro computer con credenziali.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Usare l'autenticazione dei runbook con identità gestite

I ruoli di lavoro ibridi per runbook all'interno di macchine virtuali di Azure possono usare identità gestite per l'autenticazione alle risorse di Azure. L'uso delle identità gestite anziché degli account RunAs per le risorse di Azure è vantaggioso perché non è necessario:

* Esportare il certificato RunAs e quindi importarlo nel ruolo di lavoro ibrido per runbook.
* Rinnovare il certificato usato dall'account RunAs.
* Gestire l'oggetto connessione RunAs nel codice del runbook.

Eseguire la procedura seguente per usare un'identità gestita per le risorse di Azure in un ruolo di lavoro ibrido per runbook:

1. Creare una macchina virtuale di Azure.
2. Configurare le identità gestite per le risorse di Azure nella macchina virtuale. Vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale tramite il portale di Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Resource Manager. Fare riferimento a [Usare un'identità gestita assegnata dal sistema per una macchina virtuale Windows per accedere a Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installare il ruolo di lavoro ibrido per runbook nella macchina virtuale. Vedere [Distribuire un ruolo di lavoro ibrido per runbook di Windows](automation-windows-hrw-install.md) o [Distribuire un ruolo di lavoro ibrido per runbook di Linux](automation-linux-hrw-install.md).
5. Aggiornare il runbook in modo che usi il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) con il parametro `Identity` per eseguire l'autenticazione alle risorse di Azure. Questa configurazione riduce la necessità di usare un account RunAs e di eseguire la gestione dell'account associata.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > Per i ruoli di lavoro ibridi per runbook, il cmdlet `Connect-AzAccount -Identity` funziona tramite un'identità assegnata dal sistema e una singola identità assegnata dall'utente. Se nel ruolo di lavoro ibrido per runbook si usano più identità assegnate dall'utente, il runbook deve specificare il parametro `AccountId` perché `Connect-AzAccount` selezioni un'identità specifica assegnata dall'utente.

### <a name="use-runbook-authentication-with-run-as-account"></a>Usare l'autenticazione dei runbook con un account RunAs

Anziché fare in modo che i runbook forniscano i propri dati di autenticazione alle risorse locali, è possibile specificare un account RunAs per un gruppo di ruoli di lavoro ibridi per runbook. Per specificare un account RunAs, è necessario definire un [Asset credenziali](./shared-resources/credentials.md) con accesso alle risorse locali. Queste risorse includono gli archivi certificati e tutti i runbook eseguiti con queste credenziali in un ruolo di lavoro ibrido per runbook nel gruppo.

Il nome utente per le credenziali deve essere in uno dei formati seguenti:

* dominio\nome utente
* username@domain
* nome utente (per gli account locali nel computer locale)

Usare la procedura seguente per specificare un account RunAs per un gruppo di ruoli di lavoro ibridi per runbook:

1. Creare un [asset credenziali](./shared-resources/credentials.md) con accesso alle risorse locali.
2. Nel portale di Azure aprire l'account di automazione.
3. Selezionare **Gruppi di ruoli di lavoro ibridi** e quindi selezionare il gruppo specifico.
4. Selezionare **Tutte le impostazioni** e quindi **Impostazioni del gruppo di lavoro ibrido**.
5. Modificare il valore di **Esegui come** da **Predefinito** a **Personalizzato**.
6. Selezionare le credenziali e fare clic su **Salva**.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Installare il certificato dell'account RunAs

Nell'ambito del processo di compilazione automatizzato per la distribuzione di risorse in Azure, può essere necessario accedere a sistemi locali per supportare un'attività o un set di passaggi nella sequenza di distribuzione. Per consentire l'autenticazione in Azure con l'account RunAs, è necessario installare il certificato dell'account RunAs.

Il runbook di PowerShell seguente, denominato **Export-RunAsCertificateToHybridWorker**, esporta il certificato RunAs dall'account di Automazione di Azure. Il runbook scarica e importa il certificato nell'archivio certificati del computer locale in un ruolo di lavoro ibrido per runbook connesso allo stesso account. Dopo aver completato questo passaggio, il runbook verifica che il ruolo di lavoro possa eseguire l'autenticazione ad Azure usando l'account RunAs.

>[!NOTE]
>Questo runbook di PowerShell non è progettato o deve essere eseguito all'esterno dell'account di automazione come uno script nel computer di destinazione.
>

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
>Per i runbook PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per `Connect-AzAccount`. Quando si esegue la ricerca tra gli elementi della libreria, se `Connect-AzAccount` non è visualizzato, è possibile usare `Add-AzAccount` oppure aggiornare i moduli nell'account di Automazione.

Per completare la preparazione dell'account RunAs:

1. Salvare il runbook **Export-RunAsCertificateToHybridWorker** nel computer con l'estensione **ps1**.
2. Importarlo nell'account di Automazione.
3. Modificare il runbook, sostituendo il valore della variabile `Password` con la propria password.
4. Pubblicare il runbook.
5. Eseguire il runbook scegliendo come destinazione il gruppo di ruoli di lavoro ibridi per runbook che esegue e autentica i runbook usando l'account RunAs. 
6. Esaminare il flusso del processo per vedere che segnala il tentativo di importare il certificato nell'archivio del computer locale, seguito da più righe. Questo comportamento dipende dal numero di account di Automazione definiti nella sottoscrizione e dal grado di riuscita dell'autenticazione.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Usare runbook firmati in un ruolo di lavoro ibrido per runbook di Windows

È possibile configurare un ruolo di lavoro ibrido per runbook di Windows per eseguire solo runbook firmato.

> [!IMPORTANT]
> Dopo aver configurato un ruolo di lavoro ibrido per runbook per l'esecuzione solo di runbook firmati, l'esecuzione di runbook non firmati nel ruolo di lavoro non riesce.

### <a name="create-signing-certificate"></a>Creare il certificato di firma

L'esempio seguente crea un certificato autofirmato che può essere usato per la firma dei runbook. Questo codice crea il certificato e lo esporta in modo che il ruolo di lavoro ibrido per runbook possa importarlo in seguito. Viene restituita anche l'identificazione personale perché possa essere usata in seguito quando si fa riferimento al certificato.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importare il certificato e configurare i ruoli di lavoro per la convalida della firma

Copiare il certificato creato in ogni ruolo di lavoro ibrido per runbook in un gruppo. Eseguire lo script seguente per importare il certificato e configurare i ruolo di lavoro per l'uso della convalida della firma per i runbook.

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

Con i ruoli di lavoro ibridi per runbook configurati per l'uso solo di runbook firmati, è necessario firmare i runbook da usare con il ruolo di lavoro ibrido per runbook. Usare l'esempio di PowerShell seguente per firmare i runbook.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Dopo che un runbook è stato firmato, deve essere importato nell'account di Automazione e pubblicato con il blocco della firma. Per informazioni su come importare runbook, vedere [Importare un runbook](manage-runbooks.md#import-a-runbook).

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Usare runbook firmati in un ruolo di lavoro ibrido per runbook di Linux

Per poter usare runbook firmati, un ruolo di lavoro ibrido per Runbook di Linux deve avere l'eseguibile [GPG](https://gnupg.org/index.html) nel computer locale.

> [!IMPORTANT]
> Dopo aver configurato un ruolo di lavoro ibrido per runbook per l'esecuzione solo di runbook firmati, l'esecuzione di runbook non firmati nel ruolo di lavoro non riesce.

Per completare questa configurazione, attenersi alla procedura seguente:

* Creare un keyring e una coppia di chiavi di GPG
* Rendere disponibile il keyring per il ruolo di lavoro ibrido per runbook
* Verificare che la convalida della firma sia abilitata
* Firmare un runbook

### <a name="create-a-gpg-keyring-and-keypair"></a>Creare un keyring e una coppia di chiavi di GPG

Per creare il keyring e la coppia di chiavi di GPG, usare l'[account nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) del ruolo di lavoro ibrido per runbook.

1. Usare l'applicazione sudo per accedere con l'account **nxautomation**.

    ```bash
    sudo su – nxautomation
    ```

2. Dopo aver iniziato a usare **nxautomation**, generare la coppia di chiavi di GPG. GPG consente di usare una procedura dettagliata. È necessario specificare il nome, l'indirizzo di posta elettronica, l'ora di scadenza e la passphrase. Attendere quindi che nel computer sia presente entropia sufficiente per la generazione della chiave.

    ```bash
    sudo gpg --generate-key
    ```

3. Poiché la directory GPG è stata generata con sudo, è necessario cambiare il proprietario in **nxautomation** tramite il comando seguente.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Rendere disponibile il keyring per il ruolo di lavoro ibrido per runbook

Dopo aver creato il keyring, renderlo disponibile per il ruolo di lavoro ibrido per runbook. Modificare il file di impostazioni **Home/nxautomation/state/Workgroup. conf** in modo da includere il codice di esempio seguente nella sezione del file `[worker-optional]` .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verificare che la convalida della firma sia abilitata

Se la convalida della firma è stata disabilitata nel computer, è necessario attivarla eseguendo il comando sudo seguente. Sostituire `<LogAnalyticsworkspaceId>` con l'ID della propria area di lavoro.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Firmare un runbook

Dopo aver configurato la convalida della firma, usare il comando GPG seguente per firmare il runbook.

```bash
gpg –-clear-sign <runbook name>
```

Il runbook firmato viene denominato **<runbook name>.asc**.

È ora possibile caricare il runbook firmato in Automazione di Azure ed eseguito come un runbook normale.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Avviare un runbook in un ruolo di lavoro ibrido per runbook

[Avviare un runbook in Automazione di Azure](start-runbooks.md) descrive metodi diversi per l'avvio di un runbook. L'avvio di un Runbook in un ruolo di lavoro ibrido per Runbook usa un'opzione **Run on** che consente di specificare il nome di un gruppo di ruolo di lavoro ibrido per Runbook. Quando si specifica un gruppo, uno dei ruoli di lavoro di questo recupera ed esegue il runbook. Se il runbook non specifica questa opzione, Automazione di Azure lo esegue come di consueto.

Quando si avvia un runbook nel portale di Azure, viene visualizzata l'opzione **Esegui in**, per la quale è possibile selezionare **Azure** o **Ruolo di lavoro ibrido**. Se si seleziona **Ruolo di lavoro ibrido**, è possibile scegliere il gruppo di ruoli di lavoro ibridi per runbook da un elenco a discesa.

Quando si avvia un runbook con PowerShell, usare il parametro `RunOn` con il cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). L'esempio seguente usa Windows PowerShell per avviare un runbook denominato **Test-Runbook** in un gruppo di ruoli di lavoro ibridi per runbook denominato MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Registrazione

Per semplificare la risoluzione dei problemi relativi a manuali operativi in esecuzione in un ruolo di lavoro ibrido per Runbook, i log vengono archiviati localmente nel percorso seguente:

* In Windows in `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` per la registrazione dettagliata del processo di runtime del processo. Gli eventi di stato del processo di Runbook di livello elevato vengono scritti nel registro eventi **Logs\Microsoft-Automation\Operations dell'applicazione e dei servizi** .

* In Linux i log del ruolo di lavoro ibrido dell'utente sono reperibili in e i log del ruolo di `/home/nxautomation/run/worker.log` lavoro di System Runbook sono reperibili in `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Passaggi successivi

* Se i runbook non vengono completati correttamente, vedere la guida per la risoluzione dei problemi relativi agli [errori di esecuzione dei runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Per altre informazioni su PowerShell, inclusi i riferimenti al linguaggio e i moduli di formazione, vedere la [documentazione di PowerShell](/powershell/scripting/overview).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](/powershell/module/az.automation#automation).
