---
title: Eseguire runbook nel ruolo di lavoro ibrido per runbook di Automazione di Azure
description: Questo articolo fornisce informazioni sull'esecuzione di manuali operativi nei computer nel Data Center locale o nel provider di servizi cloud con Hybrid Runbook workers.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: 276ad52424fd0a835012a48411b3e9365f55b0fe
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82787635"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Eseguire runbook in un ruolo di lavoro ibrido per runbook

Manuali operativi destinati a un ruolo di lavoro ibrido per Runbook in genere gestiscono le risorse sul computer locale o sulle risorse nell'ambiente locale in cui viene distribuito il ruolo di lavoro. I runbook usati in Automazione di Azure gestiscono in genere le risorse nel cloud di Azure. Anche se vengono usati in modo diverso, i manuali operativi eseguiti in automazione di Azure e manuali operativi eseguiti in un ruolo di lavoro ibrido per Runbook sono identici nella struttura.

Quando si crea un Runbook per l'esecuzione in un ruolo di lavoro ibrido per Runbook, è necessario modificare e testare il Runbook nel computer che ospita il ruolo di lavoro. Il computer host dispone di tutti i moduli di PowerShell e di accesso alla rete necessari per gestire e accedere alle risorse locali. Una volta testato il Runbook nel computer di lavoro ibrido per Runbook, è possibile caricarlo nell'ambiente di automazione di Azure, dove può essere eseguito nel ruolo di lavoro. 

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="plan-runbook-job-behavior"></a>Pianificare il comportamento del processo Runbook

Automazione di Azure gestisce i processi nei ruoli di lavoro ibridi per Runbook in modo diverso rispetto ai processi eseguiti in sandbox di Azure. Se si dispone di un Runbook a esecuzione prolungata, assicurarsi che sia resiliente al riavvio possibile. Per informazioni dettagliate sul comportamento del processo, vedere [Hybrid Runbook Worker Jobs](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

Tenere presente che i processi per i ruoli di lavoro ibridi per Runbook vengono eseguiti con l'account di **sistema** locale in Windows o con l'account **nxautomation** in Linux. Per Linux, verificare che l'account **nxautomation** disponga dell'accesso al percorso in cui sono archiviati i moduli Runbook. Quando si usa il cmdlet [install-module](/powershell/module/powershellget/install-module) , assicurarsi di specificare ALLUSERS per il `Scope` parametro per assicurarsi che l'account **nxautomation** abbia accesso. Per altre informazioni su PowerShell in Linux, vedere [problemi noti per PowerShell in piattaforme non Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="set-up-runbook-permissions"></a>Configurare le autorizzazioni di Runbook

È possibile definire le autorizzazioni per l'esecuzione di Runbook in Hybrid Runbook Manager nei modi seguenti:

* Fare in modo che il Runbook fornisca la propria autenticazione alle risorse locali.
* Configurare l'autenticazione usando [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). 
* Specificare un account RunAs per fornire un contesto utente per tutti manuali operativi.

## <a name="use-runbook-authentication-to-local-resources"></a>Usare l'autenticazione Runbook per le risorse locali

Se si prepara una Runbook che fornisce la propria autenticazione alle risorse, usare le [credenziali](automation-credentials.md) e gli asset di [certificato](automation-certificates.md) in Runbook. Sono disponibili diversi cmdlet che consentono di specificare le credenziali in modo che i Runbook possano eseguire l'autenticazione a diverse risorse. L'esempio seguente illustra una parte di un runbook che riavvia un computer. Recupera le credenziali da un asset delle credenziali e il nome del computer da un asset di variabile, quindi usa questi valori con `Restart-Computer` il cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

È anche possibile usare un'attività [InlineScript](automation-powershell-workflow.md#inlinescript) . `InlineScript`consente di eseguire blocchi di codice in un altro computer con le credenziali specificate dal [parametro comune PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

## <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Usare l'autenticazione runbook con le identità gestite

I ruoli di lavoro ibridi per Runbook in macchine virtuali di Azure possono usare identità gestite per le risorse di Azure per l'autenticazione nelle risorse di Azure. L'uso delle identità gestite per le risorse di Azure anziché degli account RunAs offre vantaggi perché non è necessario:

* Esportare il certificato RunAs e quindi importarlo nel ruolo di lavoro ibrido per Runbook.
* Rinnovare il certificato utilizzato dall'account RunAs.
* Gestire l'oggetto connessione RunAs nel codice Runbook.

Seguire i passaggi successivi per usare un'identità gestita per le risorse di Azure in un ruolo di lavoro ibrido per Runbook.

1. Creare una macchina virtuale di Azure.
2. Configurare le identità gestite per le risorse di Azure nella macchina virtuale. Vedere [configurare le identità gestite per le risorse di Azure in una macchina virtuale usando il portale di Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Concedere alla macchina virtuale l'accesso a un gruppo di risorse in Gestione risorse. Vedere [usare un'identità gestita assegnata dal sistema VM Windows per accedere Gestione risorse](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installare il ruolo di lavoro ibrido per Runbook nella macchina virtuale. Vedere [distribuire un ruolo di lavoro ibrido per Runbook Windows](automation-windows-hrw-install.md).
5. Aggiornare Runbook per usare il cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) con il parametro `Identity` per l'autenticazione nelle risorse di Azure. Questa configurazione riduce la necessità di usare un account RunAs ed eseguire la gestione degli account associati.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`funziona per un ruolo di lavoro ibrido per Runbook usando un'identità assegnata dal sistema e una singola identità assegnata dall'utente. Se si usano più identità assegnate dall'utente nel ruolo di lavoro ibrido per Runbook, è necessario che `AccountId` il Runbook `Connect-AzAccount` specifichi il parametro per per selezionare un'identità specifica assegnata dall'utente.

## <a name="use-runbook-authentication-with-run-as-account"></a>Usare l'autenticazione runbook con l'account RunAs

Anziché fare in modo che i Runbook forniscano l'autenticazione per le risorse locali, è possibile specificare un account RunAs per un gruppo di lavoro ibrido per Runbook. A tale scopo, è necessario definire un [Asset credenziali](automation-credentials.md) con accesso alle risorse locali. Queste risorse includono gli archivi certificati e tutti i manuali operativi eseguiti con queste credenziali in un ruolo di lavoro ibrido per Runbook nel gruppo.

Il nome utente per le credenziali deve essere in uno dei formati seguenti:

* dominio\nome utente
* username@domain
* nome utente (per gli account locali nel computer locale)

Usare la procedura seguente per specificare un account RunAs per un gruppo di ruolo di lavoro ibrido per Runbook.

1. Creare un [asset credenziali](automation-credentials.md) con accesso alle risorse locali.
2. Nel portale di Azure aprire l'account di automazione.
3. Selezionare il riquadro **Gruppi di ruoli di lavoro ibridi** e quindi il gruppo.
4. Selezionare **tutte le impostazioni**, quindi **le impostazioni del gruppo di lavoro ibrido**.
5. Modificare il valore di **RunAs** da **predefinito** a **Custom**.
6. Selezionare le credenziali e fare clic su **Salva**.

### <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Installare il certificato dell'account RunAs

Come parte del processo di compilazione automatizzato per la distribuzione di risorse in Azure, potrebbe essere necessario l'accesso ai sistemi locali per supportare un'attività o un set di passaggi nella sequenza di distribuzione. Per fornire l'autenticazione in Azure usando l'account RunAs, è necessario installare il certificato dell'account RunAs.

Il Runbook di PowerShell seguente, denominato **Export-RunAsCertificateToHybridWorker**, esporta il certificato RunAs dall'account di automazione di Azure. Il Runbook Scarica e importa il certificato nell'archivio certificati del computer locale in un ruolo di lavoro ibrido per Runbook connesso allo stesso account. Al termine di questo passaggio, Runbook verifica che il ruolo di lavoro sia in grado di eseguire l'autenticazione in Azure usando l'account RunAs.

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
>Per i runbook PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` sono alias per `Connect-AzAccount`. Quando si eseguono `Connect-AzAccount`ricerche negli elementi della libreria, è possibile usare `Add-AzAccount`oppure è possibile aggiornare i moduli nell'account di automazione.

Per completare la preparazione dell'account RunAs:

1. Salvare il Runbook **Export-RunAsCertificateToHybridWorker** nel computer con estensione **ps1** .
2. Importarlo nell'account di automazione.
3. Modificare il Runbook, modificando il valore della `Password` variabile con la propria password. 
4. Pubblicare il Runbook.
5. Eseguire Runbook, destinando il gruppo di lavoro ibrido per Runbook che esegue e autentica manuali operativi usando l'account RunAs. 
6. Esaminare il flusso del processo per verificare che segnali il tentativo di importare il certificato nell'archivio del computer locale e che segua più righe. Questo comportamento dipende dal numero di account di automazione definiti nella sottoscrizione e dal grado di riuscita dell'autenticazione.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Avviare un Runbook in un ruolo di lavoro ibrido per Runbook

Per [avviare un Runbook in automazione di Azure](start-runbooks.md) vengono descritti i diversi metodi per l'avvio di un Runbook. L'avvio di un Runbook in un ruolo di lavoro ibrido per Runbook usa un'opzione **Run on** che consente di specificare il nome di un gruppo di ruolo di lavoro ibrido per Runbook. Quando viene specificato un gruppo, uno dei thread di lavoro del gruppo recupera ed esegue il Runbook. Se il Runbook non specifica questa opzione, automazione di Azure esegue il Runbook come di consueto.

Quando si avvia un Runbook nel portale di Azure, viene visualizzata l'opzione **Esegui su** per la quale è possibile selezionare **Azure** o ruolo di **lavoro ibrido**. Se si seleziona ruolo di **lavoro ibrido**, è possibile scegliere il gruppo di lavoro ibrido per Runbook da un elenco a discesa.

Usare il `RunOn` parametro con il cmdlet [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) . L'esempio seguente usa Windows PowerShell per avviare un Runbook denominato **test-Runbook** in un gruppo di ruolo di lavoro ibrido per Runbook denominato MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> È necessario [scaricare la versione più recente di PowerShell](https://azure.microsoft.com/downloads/) se è già installata una versione precedente. Installare questa versione solo nella workstation in cui si avvia il Runbook da PowerShell. Non è necessario installarlo nel computer di lavoro ibrido per Runbook, a meno che non si intenda avviare manuali operativi da questo computer.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Usare manuali operativi firmato in un ruolo di lavoro ibrido per Runbook Windows

È possibile configurare un ruolo di lavoro ibrido per Runbook Windows per eseguire solo manuali operativi con segno.

> [!IMPORTANT]
> Dopo aver configurato un ruolo di lavoro ibrido per runbook per eseguire solo i runbook firmati, i runbook che non stati firmati non verranno eseguiti nel ruolo di lavoro.

### <a name="create-signing-certificate"></a>Crea certificato di firma

L'esempio seguente crea un certificato autofirmato che può essere usato per la firma dei runbook. Questo codice crea il certificato e lo esporta in modo che il ruolo di lavoro ibrido per Runbook possa essere importato in un secondo momento. L'identificazione personale viene restituita anche per un uso successivo nel riferimento al certificato.

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

Copiare il certificato creato in ogni ruolo di lavoro ibrido per Runbook in un gruppo. Eseguire lo script seguente per importare il certificato e configurare i ruoli di lavoro per l'uso della convalida della firma in manuali operativi.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Firmare il manuali operativi usando il certificato

Con i ruoli di lavoro ibridi per Runbook configurati per usare solo manuali operativi firmati, è necessario firmare manuali operativi che devono essere usati in Hybrid Runbook Worker. Usare il codice PowerShell di esempio seguente per firmare questi manuali operativi.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando un Runbook è stato firmato, è necessario importarlo nell'account di automazione e pubblicarlo con il blocco della firma. Per informazioni su come importare i runbook,vedere [Importazione di un runbook da un file in Automazione di Azure](manage-runbooks.md#importing-a-runbook).

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Usare manuali operativi firmato in un ruolo di lavoro ibrido per Runbook di Linux

Per poter usare manuali operativi con segno, un ruolo di lavoro ibrido per Runbook di Linux deve avere il file eseguibile [GPG](https://gnupg.org/index.html) nel computer locale.

> [!IMPORTANT]
> Dopo aver configurato un ruolo di lavoro ibrido per runbook per eseguire solo i runbook firmati, i runbook che non stati firmati non verranno eseguiti nel ruolo di lavoro.

### <a name="create-a-gpg-keyring-and-keypair"></a>Creare un keyring e una coppia di chiavi di GPG

Per creare il portachiavi e la coppia di chiavi GPG, usare l'account **nxautomation** di lavoro ibrido per Runbook.

1. Usare l'applicazione sudo per accedere con l'account **nxautomation** .

    ```bash
    sudo su – nxautomation
    ```

2. Quando si usa **nxautomation**, generare la coppia di coppie di nomi di GPG. GPG guida l'utente nei passaggi necessari. È necessario specificare il nome, l'indirizzo di posta elettronica, l'ora di scadenza e la passphrase. Si attende quindi che la chiave da generare sia sufficiente per l'entropia del computer.

    ```bash
    sudo gpg --generate-key
    ```

3. Poiché la directory GPG è stata generata con sudo, è necessario modificarne il proprietario in **nxautomation** usando il comando seguente.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Rendere disponibile il portachiavi per il ruolo di lavoro ibrido per Runbook

Una volta creato il portachiavi, renderlo disponibile per il ruolo di lavoro ibrido per Runbook. Modificare il file di impostazioni **/var/opt/Microsoft/omsagent/state/automationworker/DIY/Worker.conf** in modo da includere il codice di esempio seguente `[worker-optional]`nella sezione del file.

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verificare che la convalida della firma sia attiva

Se la convalida della firma è stata disabilitata nel computer, è necessario attivarla eseguendo il comando sudo seguente. Sostituire `<LogAnalyticsworkspaceId>` con l'ID dell'area di lavoro.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Firmare un runbook

Dopo aver configurato la convalida della firma, usare il comando GPG seguente per firmare un Runbook.

```bash
gpg –-clear-sign <runbook name>
```

Il Runbook firmato viene chiamato ** <runbook name>. asc**.

È ora possibile caricare il Runbook firmato in automazione di Azure ed eseguirlo come un normale Runbook.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come usare l'editor di testo per lavorare con manuali operativi di PowerShell in automazione di Azure, vedere [modifica di un Runbook in automazione di Azure](automation-edit-textual-runbook.md).
* Se il manuali operativi non viene completato correttamente, vedere la guida alla risoluzione dei problemi relativi agli [errori di esecuzione di Runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Per altre informazioni su PowerShell, incluse le informazioni di riferimento sul linguaggio e i moduli di apprendimento, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
