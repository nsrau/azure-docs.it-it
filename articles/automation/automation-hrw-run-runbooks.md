---
title: Eseguire runbook nel ruolo di lavoro ibrido per runbook di Automazione di Azure
description: Questo articolo fornisce informazioni sull'esecuzione di runbook su computer presenti nel data center locale o in un provider di servizi cloud con il ruolo di lavoro ibrido per runbook.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c10262e50fff2903d7caf242304145a2ab93dbcd
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970609"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Esecuzione di runbook in un ruolo di lavoro ibrido per runbook

Non esiste alcuna differenza nella struttura dei runbook che vengono eseguiti in Automazione di Azure e di quelli eseguiti in un ruolo di lavoro ibrido per runbook. I runbook usati nei due casi sono invece molto diversi. Questa differenza è dovuta al fatto che i runbook destinati a un ruolo di lavoro ibrido per runbook, in genere, gestiscono le risorse nel computer locale stesso o su risorse nell'ambiente locale in cui viene distribuito. I runbook usati in Automazione di Azure gestiscono in genere le risorse nel cloud di Azure.

Quando si creano runbook da eseguire in un ruolo di lavoro ibrido per runbook, è opportuno modificare e testare i runbook all'interno del computer che ospita il ruolo di lavoro ibrido. Il computer host include tutti i moduli di PowerShell e i diritti di accesso di rete necessari per gestire e accedere alle risorse locali. Dopo che un runbook è stato testato nel computer con il ruolo di lavoro ibrido, è possibile caricarlo nell'ambiente di Automazione di Azure in cui risulterà disponibile per essere eseguito nel ruolo di lavoro ibrido. È importante sapere quali processi vengono eseguiti con l'account di sistema locale per Windows o con un account utente speciale `nxautomation` in Linux. In Linux questo significa che è necessario assicurarsi che l' `nxautomation` account abbia accesso al percorso in cui vengono archiviati i moduli. Quando si usa il cmdlet [install-module]() , specificare **ALLUSERS** al `-Scope` parametro per verificare che l'account `naxautomation` disponga dell'accesso.

Per altre informazioni su PowerShell in Linux, vedere [problemi noti per PowerShell in piattaforme non Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Avvio di un runbook in un ruolo di lavoro ibrido per runbook

[avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md) illustra diversi modi in cui è possibile eseguire l'avvio dei runbook. Hybrid Runbook Workers aggiunge un'opzione **RunOn** in cui è possibile specificare il nome di un gruppo di computer di lavoro runbook ibridi. Quando si specifica un gruppo, il runbook verrà recuperato ed eseguito da uno dei ruoli di lavoro inclusi in tale gruppo. Se non si specifica l'opzione, il runbook verrà eseguito come di consueto in Automazione di Azure.

Quando si avvia un runbook nel portale di Azure, viene visualizzata l'opzione **Esegui in**, che consente di scegliere tra **Azure** o **Ruolo di lavoro ibrido**. Se si seleziona **Computer di lavoro ibrido**, sarà quindi possibile selezionare il gruppo da un elenco a discesa.

Usare il parametro **RunOn**. È possibile usare il comando seguente per avviare un runbook denominato Test-Runbook in un gruppo di ruoli di lavoro ibridi per runbook denominato MyHybridGroup usando Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Il parametro **RunOn** è stato aggiunto al cmdlet **Start-AzureAutomationRunbook** nella versione 0.9.1 di Microsoft Azure PowerShell. È consigliabile [scaricare la versione più recente](https://azure.microsoft.com/downloads/) se la versione installata è precedente. È sufficiente installare questa versione nella workstation in cui si avvierà il runbook da PowerShell. Non è necessario installarla nel computer di lavoro, a meno che non si intenda avviare i runbook da tale computer.

## <a name="runbook-permissions"></a>Autorizzazioni per i runbook

I runbook eseguiti in un ruolo di lavoro ibrido per runbook non possono usare lo stesso metodo in genere usato per l'autenticazione dei runbook per le risorse di Azure, perché accedono a risorse esterne ad Azure. Il runbook può fornire la propria autenticazione alle risorse locali o configurare l'autenticazione usando le [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
). È anche possibile specificare un account RunAs per fornire un contesto utente per tutti i runbook.

### <a name="runbook-authentication"></a>Autenticazione dei runbook

Per impostazione predefinita, i runbook vengono eseguiti nel contesto dell'account di sistema locale per Windows e di un account utente speciale `nxautomation` per Linux nel computer locale e devono quindi autenticarsi per le risorse a cui accedono.

Nel proprio runbook è possibile usare asset di tipo [Credenziali](automation-credentials.md) e [Certificato](automation-certificates.md) con cmdlet che consentono di specificare le credenziali per poter eseguire l'autenticazione per risorse diverse. L'esempio seguente illustra una parte di un runbook che riavvia un computer. Recupera le credenziali da un asset di tipo credenziale e il nome del computer da un asset di tipo variabile e quindi usa questi valori con il cmdlet Restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

È anche possibile usare [InlineScript](automation-powershell-workflow.md#inlinescript), che consente di eseguire blocchi di codice in un altro computer con le credenziali specificate dal [parametro comune PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Account RunAs

Per impostazione predefinita il ruolo di lavoro ibrido per runbook usa Sistema locale per Windows e un account utente speciale `nxautomation` per Linux per eseguire i runbook. Per evitare che i runbook debbano autenticarsi per le risorse locali, è possibile specificare un account **RunAs** per un gruppo di ruoli di lavoro ibridi. Specificare un [asset credenziali](automation-credentials.md) con accesso alle risorse locali. Tutti i runbook useranno queste credenziali durante l'esecuzione in un ruolo di lavoro ibrido per runbook nel gruppo.

Il nome utente per le credenziali deve essere in uno dei formati seguenti:

* dominio\nome utente
* username@domain
* nome utente (per gli account locali nel computer locale)

Usare la procedura seguente per specificare un account RunAs per un gruppo di lavoro ibrido:

1. Creare un [asset credenziali](automation-credentials.md) con accesso alle risorse locali.
2. Nel portale di Azure aprire l'account di automazione.
3. Selezionare il riquadro **Gruppi di ruoli di lavoro ibridi** e quindi il gruppo.
4. Selezionare **Tutte le impostazioni** e quindi **Impostazioni del gruppo di lavoro ibrido**.
5. Modificare **Esegui come** da **Predefinito** a **Personalizzato**.
6. Selezionare le credenziali e fare clic su **Salva**.

### <a name="managed-identities-for-azure-resources"></a>Identità gestite per le risorse di Azure

I ruoli di lavoro ibridi per runbook in esecuzione in macchine virtuali di Azure possono usare le identità gestite per le risorse di Azure per l'autenticazione alle risorse di Azure. Esistono molti vantaggi nell'uso delle identità gestite per le risorse di Azure su account RunAs.

* Non è necessario esportare il certificato RunAs e quindi importarlo nel ruolo di lavoro ibrido per runbook
* Non è necessario rinnovare il certificato usato dall'account RunAs
* Non è necessario gestire l'oggetto connessione RunAs nel codice del runbook

Per usare un'identità gestita per le risorse di Azure in un ruolo di lavoro ibrido per runbook, è necessario completare la procedura seguente:

1. Creare una macchina virtuale di Azure
2. [Configurare le identità gestite per le risorse di Azure nella macchina virtuale](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Gestione risorse](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [Ottenere un token di accesso usando l'identità gestita assegnata dal sistema della macchina virtuale](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [Installare il ruolo di lavoro ibrido per runbook](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) nella macchina virtuale.

Dopo aver completato i passaggi precedenti, è possibile usare `Connect-AzureRmAccount -Identity` nel runbook per l'autenticazione alle risorse di Azure. Questa configurazione riduce la necessità di usare un account RunAs e di gestire il certificato per l'account RunAs.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Account RunAs di Automazione

Nell'ambito del processo di compilazione automatizzato per la distribuzione di risorse in Azure, è possibile che sia necessario accedere a sistemi locali per supportare un'attività o un set di passaggi nella sequenza di distribuzione. Per supportare l'autenticazione in Azure con l'account RunAs, è necessario installare il certificato dell'account RunAs.

Il runbook di PowerShell seguente, **Export-RunAsCertificateToHybridWorker**, esporta il certificato RunAs dall'account di Automazione di Azure, lo scarica e lo importa nell'archivio certificati del computer locale in un ruolo di lavoro ibrido connesso allo stesso account. Una volta completato questo passaggio, viene verificato che il ruolo di lavoro possa eseguire l'autenticazione in Azure usando l'account RunAs.

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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
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

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Add-AzureRmAccount** è ora un alias per **Connect-AzureRMAccount**. Quando si esegue la ricerca tra gli elementi della libreria, se **Connect-AzureRMAccount** non viene visualizzato, è possibile usare **Add-AzureRmAccount** oppure aggiornare i moduli nell'account di Automazione.

Salvare il runbook *Export-RunAsCertificateToHybridWorker* nel computer con un'estensione `.ps1`. Importarlo nell'account di Automazione e modificare il runbook, cambiando il valore della variabile `$Password` con quello della propria password. Pubblicare e quindi eseguire il runbook. Scegliere come destinazione il gruppo di ruoli di lavoro ibridi che esegue e autentica i runbook usando l'account RunAs. Il flusso del processo segnala il tentativo di importare il certificato nell'archivio del computer locale e visualizza più righe. Questo comportamento dipende dal numero di account di Automazione definiti nella sottoscrizione e dall'esito positivo dell'autenticazione.

## <a name="job-behavior"></a>Comportamento dei processi

Nei ruoli di lavoro ibridi per runbook i processi vengono gestiti in modo leggermente diverso rispetto a come vengono eseguiti nelle sandbox di Azure. Nei ruoli di lavoro ibridi per runbook, ad esempio, non è previsto alcun limite per la durata del processo. Esiste una limitazione di tre ore per i runbook eseguiti nelle sandbox Azure a causa dell'errore di [condivisione equa](automation-runbook-execution.md#fair-share). Per un runbook a esecuzione prolungata è necessario assicurarsi che sia resiliente a un eventuale riavvio. Ad esempio, in caso di riavvio del computer che ospita il ruolo di lavoro ibrido. In caso di riavvio del computer host con il ruolo di lavoro ibrido, qualsiasi processo di runbook in esecuzione viene riavviato dall'inizio o dall'ultimo checkpoint per i runbook del flusso di lavoro di PowerShell. Se riavviato più di tre volte, un processo di runbook viene sospeso.

## <a name="run-only-signed-runbooks"></a>Eseguire solo i runbook firmati

I ruoli di lavoro ibridi per un runbook possono essere configurati per eseguire solo i runbook firmati con alcune attività di configurazione. La sezione seguente descrive come configurare i ruoli di lavoro ibridi per eseguire il [ruolo di lavoro ibrido per runbook di Windows](#windows-hybrid-runbook-worker) e il [ruolo di lavoro ibrido per runbook di Linux](#linux-hybrid-runbook-worker) firmati

> [!NOTE]
> Dopo aver configurato un ruolo di lavoro ibrido per runbook per eseguire solo i runbook firmati, i runbook che **non** stati firmati non verranno eseguiti nel ruolo di lavoro.

### <a name="windows-hybrid-runbook-worker"></a>Ruolo di lavoro ibrido per runbook di Windows

#### <a name="create-signing-certificate"></a>Creare il certificato di firma

L'esempio seguente crea un certificato autofirmato che può essere usato per la firma dei runbook. L'esempio crea il certificato e lo esporta. Il certificato viene importato nel ruolo di lavoro ibrido per runbook in un secondo momento. Anche l'identificazione personale viene restituita e usata in un secondo momento come riferimento al certificato.

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

#### <a name="configure-the-hybrid-runbook-workers"></a>Configurare i ruoli di lavoro ibridi per runbook

Copiare il certificato creato per ogni ruolo di lavoro ibrido per runbook in un gruppo. Eseguire lo script seguente per importare il certificato e configurare il ruolo di lavoro ibrido per l'uso della convalida della firma sui runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Firmare i runbook usando il certificato

Con i ruoli di lavoro ibrido per runbook configurati per usare solo runbook firmati, è necessario firmare i runbook da usare sul ruolo di lavoro ibrido per runbook. Usare l'esempio seguente di PowerShell per firmare i runbook.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Dopo che il runbook è stato firmato, deve essere importato nell'account di automazione e pubblicato con il blocco firma. Per informazioni su come importare i runbook,vedere [Importazione di un runbook da un file in Automazione di Azure](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Ruolo di lavoro ibrido per runbook di Linux

Per firmare i runbook in un ruolo di lavoro ibrido per runbook di Linux, è necessario che nel computer del ruolo sia presente l'eseguibile [GPG](https://gnupg.org/index.html).

#### <a name="create-a-gpg-keyring-and-keypair"></a>Creare un keyring e una coppia di chiavi di GPG

Per creare il keyring e la coppia di chiavi è necessario usare l'account del ruolo di lavoro ibrido per runbook `nxautomation`.

Usare `sudo` per accedere con l'account `nxautomation`.

```bash
sudo su – nxautomation
```

Dopo aver usato l'account `nxautomation`, generare la coppia di chiavi di GPG.

```bash
sudo gpg --generate-key
```

GPG indicherà i passaggi da seguire per creare la coppia di chiavi. È necessario specificare un nome, un indirizzo di posta elettronica, la scadenza, la passphrase e attendere che il computer abbia accumulato entropia sufficiente per generare la chiave.

Poiché la directory GPG è stata generata con sudo, è necessario sostituire il nome del proprietario con `nxautomation`.

Eseguire il comando seguente per cambiare il proprietario.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Rendere disponibile il keyring al ruolo di lavoro ibrido per runbook

Dopo aver creato il keyring, è necessario renderlo disponibile al ruolo di lavoro ibrido per runbook. Modificare il file di impostazioni `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` per includere l'esempio seguente nella sezione `[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Verificare che la convalida della firma sia abilitata

Se la convalida della firma è stata disabilitata nel computer, è necessario abilitarla. Per abilitare la convalida della firma, eseguire il comando seguente. Sostituire `<LogAnalyticsworkspaceId>` con l'ID della propria area di lavoro.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Firmare un runbook

Dopo aver configurato la convalida della firma, è possibile usare il comando seguente per firmare un runbook:

```bash
gpg –-clear-sign <runbook name>
```

Il runbook firmato avrà il nome `<runbook name>.asc`.

Il runbook firmato può ora essere caricato in Automazione di Azure ed eseguito come un runbook normale.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui vari modi per avviare un runbook, vedere [Avvio di un runbook in Automazione di Azure](automation-starting-a-runbook.md).
* Per comprendere le diverse modalità di utilizzo dei runbook di PowerShell in Automazione di Azure con l'editor di testo, vedere [Modifica di runbook testuali in Automazione di Azure](automation-edit-textual-runbook.md).
* Se i runbook non vengono completati correttamente, vedere l'articolo sulla risoluzione dei problemi relativi agli [errori di esecuzione dei runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la [documentazione di PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview).
