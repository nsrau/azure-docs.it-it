---
title: Risolvere problemi relativi a State Configuration in Automazione di Azure
description: Questo articolo mostra come risolvere i problemi relativi a State Configuration in Automazione di Azure.
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6e057f5c9525f3b4ca373897c865990eb29835c0
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681384"
---
# <a name="troubleshoot-azure-automation-state-configuration-issues"></a>Risolvere problemi relativi a State Configuration in Automazione di Azure

Questo articolo mostra come risolvere i problemi che si verificano durante la compilazione o la distribuzione di configurazioni in State Configuration di Automazione di Azure. Per informazioni generali sulla funzionalità State Configuration, vedere [Panoramica di State Configuration in Automazione di Azure](../automation-dsc-overview.md).

## <a name="diagnose-an-issue"></a>Diagnosticare un problema

Quando si riceve un errore di compilazione o distribuzione per la configurazione, attenersi alla procedura seguente per diagnosticare il problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Assicurarsi che la configurazione venga compilata correttamente nel computer locale

State Configuration in Automazione di Azure si basa su PowerShell DSC (Desired State Configuration). La documentazione relativa alla sintassi e al linguaggio DSC è disponibile all'interno della [Documentazione di PowerShell DSC](https://docs.microsoft.com/powershell/scripting/overview).

Mediante la compilazione di una configurazione DSC nel computer locale, è possibile individuare e risolvere errori comuni, come ad esempio:

   - Moduli mancanti.
   - Errori di sintassi.
   - Errori di logica.

### <a name="2-view-dsc-logs-on-your-node"></a>2. Visualizzare log DSC nel nodo

Se la configurazione viene compilata correttamente, ma ha esito negativo quando viene applicata a un nodo, è possibile trovare informazioni dettagliate nei log DSC. Per informazioni su dove trovare questi log, vedere [Where are the DSC Event Logs](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs) (Ubicazione dei log eventi DSC).

Il modulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) può risultare utile per l'analisi dettagliata delle informazioni contenute nei log DSC. Se si contatta il supporto tecnico, questi log sono necessari per diagnosticare il problema.

È possibile installare il modulo `xDscDiagnostics` nel computer locale seguendo le istruzioni riportate nell'articolo [Installare la versione stabile del modulo](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Per installare il modulo `xDscDiagnostics` nel computer Azure, usare il comando [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). È anche possibile usare l'opzione **Esegui comando** nel portale di Azure seguendo la procedura descritta in [Eseguire gli script di PowerShell nella macchina virtuale Linux con Esegui comando](../../virtual-machines/windows/run-command.md).

Per informazioni sull'uso di **xDscDiagnostics**, vedere [Uso di xDscDiagnostics per analizzare i registri DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Vedere anche [Cmdlet di xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Verificare che i nodi e l'area di lavoro di automazione dispongano dei moduli necessari

DSC dipende dai moduli installati nel nodo. Quando si usa State Configuration di Automazione di Azure, importare tutti i moduli richiesti nell'account di automazione attenendosi alla procedura descritta in [Importare moduli](../shared-resources/modules.md#import-modules). Le configurazioni possono anche presentare una dipendenza da versioni specifiche dei moduli. Per altre informazioni, vedere [Risoluzione dei problemi relativi ai moduli](shared-resources.md#modules).

## <a name="scenario-a-configuration-with-special-characters-cant-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: Le configurazioni con caratteri speciali non possono essere eliminate dal portale

### <a name="issue"></a>Problema

Quando si tenta di eliminare una configurazione DSC dal portale, viene visualizzato l'errore seguente:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Causa

Si tratta di un problema temporaneo per cui è prevista a breve una soluzione.

### <a name="resolution"></a>Risoluzione

Usare il cmdlet [Remove-AzAutomationDscConfiguration] (cmdlet https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 per eliminare la configurazione).

## <a name="scenario-failed-to-register-the-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: Non è stato possibile registrare l'agente DSC

### <a name="issue"></a>Problema

Quando il cmdlet [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) o un altro cmdlet DSC viene visualizzato l'errore:

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

Questo errore è in genere causato da un firewall, dal fatto che il computer risiede dietro un server proxy o da altri errori di rete.

### <a name="resolution"></a>Risoluzione

Verificare che il computer abbia accesso agli endpoint corretti per DSC e riprovare. Per un elenco di porte e indirizzi, vedere [Pianificazione della rete](../automation-dsc-overview.md#network-planning).

## <a name="a-nameunauthorizedscenario-status-reports-return-the-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: I report di stato restituiscono il codice di risposta Non autorizzato

### <a name="issue"></a>Problema

Quando si registra un nodo con State Configuration di Automazione di Azure, viene visualizzato uno dei messaggi di errore seguenti:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Questo problema è causato da un certificato non valido o scaduto. Vedere [Ripetere la registrazione di un nodo](../automation-dsc-onboarding.md#re-register-a-node).

Questo problema può essere causato anche da una configurazione del proxy che non consente l'accesso ad * **.azure-automation.net**. Per altre informazioni, vedere [Configurazione di reti private](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Risoluzione

Per registrare nuovamente il nodo DSC che ha avuto esito negativo, attenersi alla procedura seguente.

#### <a name="step-1-unregister-the-node"></a>Passaggio 1: Annullare la registrazione del nodo

1. Nel portale di Azure, passare a **Home** > **Account di automazione** > (account di automazione pertinente) > **State Configuration (DSC)** .
1. Selezionare **Nodi**, quindi selezionare il nodo in cui si sono verificati problemi.
1. Selezionare **Annulla registrazione** per annullare la registrazione del nodo.

#### <a name="step-2-uninstall-the-dsc-extension-from-the-node"></a>Passaggio 2: Disinstallare l'estensione DSC dal nodo

1. Nel portale di Azure, passare a **Home** > **Macchina virtuale** > (nodo con esito negativo) > **Estensioni**.
1. Selezionare **Microsoft.PowerShell.DSC**, l'estensione di PowerShell DSC.
1. Selezionare **Disinstalla** per disinstallare l'estensione.

#### <a name="step-3-remove-all-bad-or-expired-certificates-from-the-node"></a>Passaggio 3: Rimuovere dal nodo tutti i certificati non validi o scaduti

Nel nodo con esito negativo, eseguire questi comandi da un prompt di PowerShell con privilegi elevati:

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

#### <a name="step-4-reregister-the-failing-node"></a>Passaggio 4: Registrare nuovamente il nodo con esito negativo

1. Nel portale di Azure, passare a **Home** > **Account di automazione** > (account di automazione pertinente) > **State Configuration (DSC)** .
1. Selezionare **Nodi**.
1. Selezionare **Aggiungi**.
1. Selezionare il nodo con esito negativo.
1. Selezionare **Connetti**, quindi selezionare le opzioni desiderate.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: lo stato del nodo indica che non è riuscito con errore "Non trovato"

### <a name="issue"></a>Problema

Un report del nodo indica lo stato Non riuscito e contiene l'errore seguente:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione, ad esempio **ABC**, anziché un nome di configurazione nodo (file MOF), ad esempio **ABC.WebServer**.

### <a name="resolution"></a>Risoluzione

* Assicurarsi di assegnare al nodo un nome di configurazione nodo e non il nome di configurazione.
* È possibile assegnare a un nodo una configurazione di nodo usando il portale di Azure o un cmdlet di PowerShell.

  * Nel portale di Azure, passare a **Home** > **Account di automazione** > (account di automazione pertinente) > **State Configuration (DSC)** . Selezionare un nodo, quindi selezionare **Assegna configurazione nodo**.
  * Usare il cmdlet [Set-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0).

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenario: non sono state generate configurazioni di nodo (file con estensione mof) durante la compilazione di una configurazione

### <a name="issue"></a>Problema

Il processo di compilazione DSC viene sospeso con l'errore seguente:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Causa

Quando l'espressione che segue la parola chiave `Node` nella configurazione DSC restituisce `$null`, non vengono prodotte configurazioni di nodo.

### <a name="resolution"></a>Risoluzione

L'uso di una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Verificare che l'espressione accanto alla parola chiave `Node` nella definizione di configurazione non restituisca Null.
* Se, durante la compilazione della configurazione, si passa il comando [ConfigurationData](../automation-dsc-compile.md), verificare di specificare i valori previsti necessari per la configurazione dai dati di configurazione.

## <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: il report relativo al nodo DSC rimane bloccato nello stato In Progress

### <a name="issue"></a>Problema

L'output dell'agente DSC è il seguente:

```error
No instance found with given property values
```

### <a name="cause"></a>Causa

La versione di Windows Management Framework (WMF) è stata aggiornata, danneggiando Strumentazione gestione Windows (WMI).

### <a name="resolution"></a>Risoluzione

Seguire le istruzioni riportate nell'articolo [Limitazioni e problemi noti di DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: non è possibile usare le credenziali in una configurazione DSC

### <a name="issue"></a>Problema

Il processo di compilazione DSC è stato sospeso con l'errore seguente:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Causa

In una configurazione sono state usate credenziali, ma non è stato passato l'oggetto `ConfigurationData` corretto per impostare `PSDscAllowPlainTextPassword` su True per ogni configurazione nodo.

### <a name="resolution"></a>Risoluzione

Verificare di passare l'oggetto `ConfigurationData` corretto per impostare `PSDscAllowPlainTextPassword` su True per ogni configurazione nodo indicata nella configurazione. Vedere [Compilazione di configurazioni DSC in State Configuration di Automazione di Azure](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-enabling-a-machine-from-a-dsc-extension"></a><a name="failure-processing-extension"></a>Scenario: "Errore durante l'attivazione dell'estensione" per l'abilitazione di un computer da un'estensione DSC

### <a name="issue"></a>Problema

Quando si abilita un computer usando un'estensione DSC, si verifica l'errore:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione nodo che non esiste nel servizio.

### <a name="resolution"></a>Risoluzione

* Assicurarsi di assegnare al nodo un nome che corrisponda esattamente al nome del servizio.
* È possibile scegliere di non includere il nome della configurazione del nodo, il che comporta l'abilitazione del nodo, ma non l'assegnazione di una configurazione del nodo.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-by-using-powershell"></a><a name="cross-subscription"></a>Scenario: Durante la registrazione di un nodo tramite PowerShell viene visualizzato il messaggio "Si sono verificati uno o più errori"

### <a name="issue"></a>Problema

Quando si registra un nodo tramite [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) o [Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0), viene visualizzato l'errore seguente:

```error
One or more errors occurred.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando si tenta di registrare un nodo in una sottoscrizione separata da quella usata dall'account di automazione.

### <a name="resolution"></a>Risoluzione

Considerare il nodo tra sottoscrizioni come se fosse definito per un cloud separato o in locale. Registrare il nodo usando una di queste opzioni per l'abilitazione dei computer:

* Windows: [Computer fisici/macchine virtuali Windows locali o in un cloud diverso da Azure/AWS](../automation-dsc-onboarding.md#enable-physicalvirtual-windows-machines).
* Linux: [Computer fisici/macchine virtuali Linux locali o in un cloud diverso da Azure](../automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines).

## <a name="scenario-provisioning-has-failed-error-message"></a><a name="agent-has-a-problem"></a>Scenario: Messaggio di errore "Provisioning non riuscito"

### <a name="issue"></a>Problema

Quando si registra un nodo, viene visualizzato l'errore:

```error
Provisioning has failed
```

### <a name="cause"></a>Causa

Questo messaggio viene visualizzato quando si verifica un problema di connettività tra il nodo e Azure.

### <a name="resolution"></a>Risoluzione

Stabilire se il nodo si trova in una rete privata virtuale (VPN) o presenta altri problemi di connessione ad Azure. Vedere [Risolvere i problemi relativi alla distribuzione di funzionalità](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenario: Esito negativo con errore generico quando si applica una configurazione in Linux

### <a name="issue"></a>Problema

Quando si applica una configurazione in Linux, si verifica l'errore:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Causa

Se il percorso **/tmp** è impostato su `noexec`, l'applicazione delle configurazioni mediante la versione corrente di DSC ha esito negativo.

### <a name="resolution"></a>Risoluzione

Rimuovere l'opzione `noexec` dal percorso **/tmp**.

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-a-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: I nomi di configurazione dei nodi che si sovrappongono possono causare problemi di release

### <a name="issue"></a>Problema

Quando si usa un singolo script di configurazione per generare più configurazioni di nodi e alcuni nomi di configurazione nodo sono sottoinsiemi di altri, il servizio di compilazione può finire per assegnare la configurazione sbagliata. Questo problema si verifica solo quando si usa un singolo script per generare configurazioni con i dati di configurazione per singolo nodo e solo quando si verifica una sovrapposizione del nome all'inizio della stringa. Un esempio è costituito da un singolo script di configurazione usato per generare configurazioni basate sui dati del nodo passati come Hashtable mediante cmdlet e i dati del nodo includono server denominati **server** e **1server**.

### <a name="cause"></a>Causa

Si tratta di un problema noto relativo al servizio di compilazione.

### <a name="resolution"></a>Risoluzione

La soluzione migliore consiste nell'eseguire la compilazione localmente o in una pipeline CI/CD e caricare i file MOF di configurazione del nodo direttamente nel servizio. Se la compilazione nel servizio è un requisito, la soluzione migliore successiva consiste nel suddividere i processi di compilazione in modo che non vi sia alcuna sovrapposizione nei nomi.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: Errore di timeout del gateway durante il caricamento della configurazione DSC

#### <a name="issue"></a>Problema

Quando si carica una configurazione DSC, viene visualizzato l'errore `GatewayTimeout`. 

### <a name="cause"></a>Causa

Questo errore può essere causato da una configurazione DSC la cui compilazione richieda molto tempo.

### <a name="resolution"></a>Risoluzione

È possibile eseguire l'analisi delle configurazioni DSC più velocemente includendo in modo esplicito il parametro `ModuleName` per qualsiasi chiamata [Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottenere risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto di Azure mette in contatto la community di Azure con le risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Supporto tecnico**.
