---
title: Risoluzione dei problemi relativi alla configurazione dello stato di automazione di Azure (DSC)
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi di Azure Automation state Configuration (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679318"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Risolvere i problemi relativi alla configurazione dello stato di automazione di Azure (DSC)

Questo articolo fornisce informazioni sulla risoluzione dei problemi che si verificano durante la compilazione o la distribuzione di configurazioni in Azure Automation state Configuration (DSC).

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](../automation-update-azure-modules.md).

## <a name="diagnosing-an-issue"></a>Diagnosi di un problema

Quando si riceve un errore di compilazione o distribuzione per la configurazione, di seguito sono riportati alcuni passaggi che consentono di diagnosticare il problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Assicurarsi che la configurazione venga compilata correttamente nel computer locale

La configurazione dello stato di automazione di Azure (DSC) si basa su PowerShell DSC (Desired state Configuration). È possibile trovare la documentazione relativa alla sintassi e al linguaggio DSC in [PowerShell DSC docs](https://docs.microsoft.com/powershell/scripting/overview).

Con la compilazione di una configurazione DSC nel computer locale, è possibile individuare e risolvere gli errori comuni, ad esempio:

   - Moduli mancanti
   - Errori di sintassi
   - Errori di logica

### <a name="2-view-dsc-logs-on-your-node"></a>2. visualizzare i log DSC nel nodo

Se la configurazione viene compilata correttamente, ma ha esito negativo quando viene applicata a un nodo, è possibile trovare informazioni dettagliate nei log DSC. Per informazioni su dove trovare questi log, vedere [dove sono i registri eventi DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Il modulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) può risultare utile per l'analisi di informazioni dettagliate dai log DSC. Se si contatta il supporto tecnico, questi registri sono necessari per diagnosticare il problema.

È possibile installare il `xDscDiagnostics` modulo nel computer locale usando le istruzioni disponibili in [installare il modulo versione stabile](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Per installare il `xDscDiagnostics` modulo nel computer Azure, usare [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). È anche possibile usare l'opzione **Esegui comando** nella portale di Azure attenendosi alla procedura descritta in [eseguire script di PowerShell nella macchina virtuale Windows con il comando Esegui](../../virtual-machines/windows/run-command.md).

Per informazioni sull'uso di **xDscDiagnostics**, vedere [uso di xDscDiagnostics per analizzare i log DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Vedere anche [cmdlet xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Assicurarsi che i nodi e l'area di lavoro di automazione dispongano dei moduli necessari

DSC dipende dai moduli installati nel nodo. Quando si usa la configurazione dello stato di automazione di Azure, importare tutti i moduli richiesti nell'account di automazione usando la procedura descritta in [importare i moduli](../shared-resources/modules.md#importing-modules). Le configurazioni possono anche avere una dipendenza da versioni specifiche dei moduli. Per altre informazioni, vedere [risolvere i problemi relativi ai moduli](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: una configurazione con caratteri speciali non può essere eliminata dal portale

### <a name="issue"></a>Problema

Quando si tenta di eliminare una configurazione DSC dal portale, viene visualizzato l'errore seguente:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Causa

Questo errore è un problema temporaneo che è pianificato per essere risolto.

### <a name="resolution"></a>Soluzione

Usare il cmdlet [Remove-AzAutomationDscConfiguration]https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 (per eliminare la configurazione.

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: non è stato possibile registrare l'agente DSC

### <a name="issue"></a>Problema

Quando si [imposta set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) o un altro cmdlet DSC, viene visualizzato l'errore seguente:

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>Causa

Questo errore è in genere causato da un firewall, dal computer che si trova dietro un server proxy o da altri errori di rete.

### <a name="resolution"></a>Soluzione

Verificare che il computer abbia accesso agli endpoint appropriati per DSC e riprovare. Per un elenco di porte e indirizzi necessari, vedere [pianificazione della rete](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: i rapporti di stato restituiscono il codice di risposta non autorizzato

### <a name="issue"></a>Problema

Quando si registra un nodo con la configurazione dello stato di automazione di Azure, viene visualizzato uno dei messaggi di errore seguenti:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Questo problema è causato da un certificato non valido o scaduto. Vedere [scadenza e registrazione del certificato](../automation-dsc-onboarding.md#re-registering-a-node).

Questo problema potrebbe essere causato anche da una configurazione proxy che non consente l'accesso a ***. Azure-Automation.NET**. Per ulteriori informazioni, vedere [configurazione di reti private](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Soluzione

Attenersi alla procedura riportata di seguito per registrare nuovamente il nodo DSC che ha avuto esito negativo.

Passaggio 1: annullare la registrazione del nodo.

1. In portale di Azure passare a **Home** -> **Automation accounts** -> (account di automazione)-> **state Configuration (DSC)**.
2. Selezionare **Nodes (nodi**) e fare clic sul nodo con problemi.
3. Fare clic su **Annulla registrazione** per annullare la registrazione del nodo.

Passaggio 2: disinstallare l'estensione DSC dal nodo.

1. In portale di Azure passare a **Home** -> **macchina virtuale** -> (nodo in errore)-> **estensioni**.
2. Selezionare **Microsoft. PowerShell. DSC**, l'estensione DSC PowerShell.
3. Fare clic su **Disinstalla** per disinstallare l'estensione.

Passaggio 3: rimuovere tutti i certificati non validi o scaduti dal nodo.

Nel nodo in errore da un prompt di PowerShell con privilegi elevati eseguire questi comandi:

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

Passaggio 4: registrare nuovamente il nodo in errore.

1. Nel portale di Azure passare a **Home** -> **Automation accounts** -> (account di automazione)-> **state Configuration (DSC)**
2. Selezionare i **nodi**.
3. Fare clic su **Aggiungi**.
4. Selezionare il nodo in errore.
5. Fare clic su **Connetti** e selezionare le opzioni desiderate.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: Lo stato di Node risulta non riuscito con un errore "Non trovato"

### <a name="issue"></a>Problema

Un report del nodo indica lo stato non riuscito e contiene l'errore seguente:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Causa

Questo errore si verifica in genere quando il nodo viene assegnato a un nome di configurazione, ad esempio **ABC**, anziché un nome di configurazione nodo (file MOF), ad esempio **ABC. Server Web**.

### <a name="resolution"></a>Soluzione

* Assicurarsi di assegnare il nodo con il nome della configurazione del nodo e non con il nome della configurazione.
* È possibile assegnare a un nodo una configurazione di nodo usando il portale di Azure o un cmdlet di PowerShell.

  * Nel portale di Azure passare a **Home** -> **Automation accounts** -> (account di automazione)-> **state Configuration (DSC)**, quindi selezionare un nodo e fare clic su **Assegna configurazione nodo**.
  * Usare il cmdlet [set-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) .

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenario: non sono state prodotte configurazioni di nodo (file MOF) durante la compilazione di una configurazione

### <a name="issue"></a>Problema

Il processo di compilazione DSC viene sospeso con l'errore seguente:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Causa

Quando l'espressione che `Node` `$null`segue la parola chiave nella configurazione DSC restituisce, non viene prodotta alcuna configurazione del nodo.

### <a name="resolution"></a>Soluzione

Per risolvere il problema, usare una delle soluzioni seguenti:

* Verificare che l'espressione accanto alla `Node` parola chiave nella definizione di configurazione non venga valutata come null.
* Se si passa [configurationData](../automation-dsc-compile.md) durante la compilazione della configurazione, assicurarsi di passare i valori previsti dalla configurazione dai dati di configurazione.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: il rapporto del nodo DSC diventa bloccato nello stato in corso

### <a name="issue"></a>Problema

L'output dell'agente DSC è il seguente:

```error
No instance found with given property values
```

### <a name="cause"></a>Causa

È stata aggiornata la versione di Windows Management Framework (WMF) ed è stato danneggiato Strumentazione gestione Windows (WMI).

### <a name="resolution"></a>Soluzione

Seguire le istruzioni riportate in [limitazioni e problemi noti di DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: non è possibile usare le credenziali in una configurazione DSC

### <a name="issue"></a>Problema

Il processo di compilazione DSC è stato sospeso con l'errore:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Causa

È stata usata una credenziale in una configurazione ma non è `ConfigurationData` stato fornito `PSDscAllowPlainTextPassword` il valore appropriato per impostare su true per ogni configurazione del nodo.

### <a name="resolution"></a>Soluzione

Assicurarsi di passare l'oggetto appropriato `ConfigurationData` per impostare `PSDscAllowPlainTextPassword` su true per ogni configurazione del nodo indicata nella configurazione. Vedere [compilazione di configurazioni DSC in configurazione dello stato di automazione di Azure](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Scenario: errore dell'estensione per l'elaborazione degli errori durante l'onboarding dall'estensione DSC

### <a name="issue"></a>Problema

Quando si esegue l'onboarding usando un'estensione DSC, si verifica un errore che contiene l'errore:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione di nodo che non esiste nel servizio.

### <a name="resolution"></a>Soluzione

* Assicurarsi di assegnare il nodo con un nome che corrisponda esattamente al nome del servizio.
* È possibile scegliere di non includere il nome della configurazione del nodo, il che comporta l'onboarding del nodo, ma non l'assegnazione di una configurazione del nodo.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Scenario: errore "si è verificato uno o più errori" durante la registrazione di un nodo con PowerShell

### <a name="issue"></a>Problema

Quando si registra un nodo con [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) o [Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0), viene visualizzato l'errore seguente:

```error
One or more errors occurred.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando si tenta di registrare un nodo in una sottoscrizione separata da quella usata dall'account di automazione.

### <a name="resolution"></a>Soluzione

Considerare il nodo tra sottoscrizioni come se fosse definito per un cloud separato o in locale. Registrare il nodo usando una di queste opzioni di onboarding:

* Windows: [computer fisici/virtuali Windows locali o in un cloud diverso da Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux: [macchine virtuali Linux in locale o in un cloud diverso da Azure](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines).

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenario: messaggio di errore-"provisioning non riuscito"

### <a name="issue"></a>Problema

Quando si registra un nodo, viene visualizzato l'errore:

```error
Provisioning has failed
```

### <a name="cause"></a>Causa

Questo messaggio viene visualizzato quando si verifica un problema di connettività tra il nodo e Azure.

### <a name="resolution"></a>Soluzione

Determinare se il nodo si trova in una rete privata virtuale (VPN) o presenta altri problemi di connessione ad Azure. Vedere [risolvere gli errori durante l'onboarding delle soluzioni](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenario: errore con errore generale durante l'applicazione di una configurazione in Linux

### <a name="issue"></a>Problema

Quando si applica una configurazione in Linux, si verifica un errore che contiene l'errore:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Causa

Se il **percorso del** /o è `noexec`impostato su, la versione corrente di DSC non è in grado di applicare le configurazioni.

### <a name="resolution"></a>Soluzione

Rimuovere l' `noexec` opzione dal percorso/ **tmp** .

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: i nomi di configurazione del nodo che si sovrappongono possono causare una versione non valida

### <a name="issue"></a>Problema

Quando si usa un singolo script di configurazione per generare più configurazioni di nodo e alcuni nomi di configurazione dei nodi sono subset di altri nomi, il servizio di compilazione può terminare l'assegnazione della configurazione errata. Questo problema si verifica solo quando si usa un singolo script per generare configurazioni con i dati di configurazione per nodo e solo quando si verifica una sovrapposizione del nome all'inizio della stringa. Un esempio è costituito da un singolo script di configurazione usato per generare configurazioni basate sui dati del nodo passati come Hashtable usando i cmdlet e i dati del nodo includono server denominati **Server** e **1Server**.

### <a name="cause"></a>Causa

Si tratta di un problema noto con il servizio di compilazione.

### <a name="resolution"></a>Soluzione

La soluzione migliore consiste nel compilare localmente o in una pipeline CI/CD e caricare i file MOF di configurazione del nodo direttamente nel servizio. Se la compilazione nel servizio è un requisito, la soluzione migliore successiva consiste nel suddividere i processi di compilazione in modo che non vi siano sovrapposizioni nei nomi.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: errore di timeout del gateway durante il caricamento della configurazione DSC

#### <a name="issue"></a>Problema

Quando si carica `GatewayTimeout` una configurazione DSC, viene visualizzato un errore. 

### <a name="cause"></a>Causa

Questo errore può essere causato da una configurazione DSC che richiede molto tempo per la compilazione.

### <a name="resolution"></a>Soluzione

È possibile eseguire l'analisi delle configurazioni DSC più velocemente includendo esplicitamente il `ModuleName` parametro per qualsiasi chiamata a [Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) .

## <a name="next-steps"></a>Passaggi successivi

Se il problema non viene visualizzato in precedenza o non è possibile risolvere il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi a [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottieni supporto**.