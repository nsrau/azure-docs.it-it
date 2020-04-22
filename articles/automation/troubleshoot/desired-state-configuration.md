---
title: Risoluzione dei problemi di configurazione dello stato di automazione di AzureTroubleshooting Azure Automation State Configuration (DSC)
description: Questo articolo fornisce informazioni sulla risoluzione dei problemi relativi alla configurazione dello stato di automazione di Azure.This article provides information on troubleshooting Azure Automation State Configuration (DSC).
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679318"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>Risolvere i problemi relativi alla configurazione dello stato di automazione di AzureTroubleshoot issues with Azure Automation State Configuration (DSC)

Questo articolo fornisce informazioni sulla risoluzione dei problemi che si verificano durante la compilazione o la distribuzione di configurazioni in Configurazione dello stato di automazione di Azure.This article provides information on troubleshooting issues that soffre while compiling or deploying configurations in Azure Automation State Configuration (DSC).

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="diagnosing-an-issue"></a>Diagnosi di un problema

Quando si riceve un errore di compilazione o distribuzione per la configurazione, di seguito sono riportati alcuni passaggi per diagnosticare il problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Assicurarsi che la configurazione venga compilata correttamente nel computer locale

La configurazione dello stato di automazione di Azure è basata su DSC (Desired State Configuration) di PowerShell. È possibile trovare la documentazione per il linguaggio e la sintassi DSC in [Documenti di PowerShell DSC](https://docs.microsoft.com/powershell/scripting/overview).

Compilando una configurazione DSC nel computer locale, è possibile individuare e risolvere gli errori comuni, ad esempio:By compiling a DSC configuration on your local machine, you can discover and resolve common errors, such as:

   - Moduli mancanti
   - Errori di sintassi
   - Errori logici

### <a name="2-view-dsc-logs-on-your-node"></a>2. Visualizzare i registri DSC sul nodo

Se la configurazione viene compilata correttamente, ma non riesce quando viene applicata a un nodo, è possibile trovare informazioni dettagliate nei registri DSC. Per informazioni su dove trovare questi registri, vedere [Dove si trovano i registri eventi DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Il modulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) può essere utili nell'analisi di informazioni dettagliate dai registri DSC. Se si contatta il supporto tecnico, questi registri richiedono per diagnosticare il problema.

È possibile `xDscDiagnostics` installare il modulo sul computer locale utilizzando le istruzioni disponibili in [Installare il modulo di versione stabile](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Per installare `xDscDiagnostics` il modulo nel computer Azure, usare [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0). È anche possibile usare l'opzione **Esegui comando** nel portale di Azure seguendo i passaggi disponibili in Eseguire gli script di [PowerShell in Windows VM con Esegui comando](../../virtual-machines/windows/run-command.md).

Per informazioni sull'utilizzo di **xDscDiagnostics**, vedere [Utilizzo di xDscDiagnostics per analizzare i log DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Vedere anche [Cmdlet xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Assicurarsi che i nodi e l'area di lavoro Automazione dispongano dei moduli necessari

DSC dipende dai moduli installati nel nodo. Quando si usa Configurazione stato di automazione di Azure, importare tutti i moduli necessari nell'account di automazione usando la procedura descritta in [Importare moduli](../shared-resources/modules.md#importing-modules). Le configurazioni possono anche avere una dipendenza da versioni specifiche dei moduli. Per ulteriori informazioni, consultate [Risoluzione dei problemi relativi ai moduli.](shared-resources.md#modules)

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: una configurazione con caratteri speciali non può essere eliminata dal portale

### <a name="issue"></a>Problema

Quando si tenta di eliminare una configurazione DSC dal portale, viene visualizzato il seguente errore:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>Causa

Questo errore è un problema temporaneo che è stato pianificato per essere risolto.

### <a name="resolution"></a>Risoluzione

Utilizzare il cmdlet [Remove-AzAutomationDscConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 per eliminare la configurazione.

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: Failed to register Dsc Agent

### <a name="issue"></a>Problema

Quando [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) o un altro cmdlet DSC, viene visualizzato l'errore:

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

Questo errore è normalmente causato da un firewall, il computer è dietro un server proxy o altri errori di rete.

### <a name="resolution"></a>Risoluzione

Verificare che il computer abbia accesso agli endpoint appropriati per DSC e riprovare. Per un elenco delle porte e degli indirizzi necessari, [vedere](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: Status reports return response code Unauthorized

### <a name="issue"></a>Problema

Quando si registra un nodo con la configurazione dello stato di automazione di Azure, viene visualizzato uno dei seguenti messaggi di errore:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Questo problema è causato da un certificato errato o scaduto. Vedere [Scadenza e nuova registrazione dei certificati](../automation-dsc-onboarding.md#re-registering-a-node).

Questo problema potrebbe anche essere causato da una configurazione proxy che non consente l'accesso a**azure-automation.net**. Per ulteriori informazioni, vedere [Configurazione di reti private](../automation-dsc-overview.md#network-planning). 

### <a name="resolution"></a>Risoluzione

Utilizzare la procedura seguente per registrare nuovamente il nodo DSC in errore.

Passaggio 1 - Annullare la registrazione del nodo.

1. Nel portale di Azure passare a **Home** -> **Automation Accounts** -> (account di automazione) -> State configuration **(DSC)**.
2. Selezionare **Nodi**e fare clic sul nodo con problemi.
3. Fare clic su **Annulla registrazione** per annullare la registrazione del nodo.

Passaggio 2 - Disinstallare l'estensione DSC dal nodo.

1. Nel portale di Azure passare a **Home** -> **Virtual Machine** -> (nodo con errori) -> **Extensions**.
2. Selezionare **Microsoft.Powershell.DSC**, l'estensione DSC di PowerShell.
3. Fare clic su **Disinstalla** per disinstallare l'estensione.

Passaggio 3 - Rimuovere tutti i certificati non valido o scaduti dal nodo.

Nel nodo in errore da un prompt di PowerShell con privilegi elevati eseguire questi comandi:On the failing node from an elevated PowerShell prompt, run these commands:

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

Passaggio 4 - Registrare nuovamente il nodo in errore.

1. Nel portale di Azure passare a Account**di automazione** **domestica** -> -> (account di automazione) **-> Configurazione stato (DSC)In** the Azure portal, navigate to Home Automation Accounts -> (your Automation account) -> State configuration (DSC)
2. Selezionare **Nodi**.
3. Scegliere **Aggiungi**.
4. Selezionare il nodo in errore.
5. Fare clic su **Connetti** e selezionare le opzioni desiderate.

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: Lo stato di Node risulta non riuscito con un errore "Non trovato"

### <a name="issue"></a>Problema

Un report del nodo indica lo stato non riuscito e contiene l'errore seguente:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>Causa

Questo errore si verifica in genere quando il nodo viene assegnato a un nome di configurazione, ad esempio **ABC**, anziché un nome di configurazione del nodo (file MOF), ad esempio **ABC. WebServer**.

### <a name="resolution"></a>Risoluzione

* Assicurarsi di assegnare il nodo con il nome di configurazione del nodo e non il nome della configurazione.
* È possibile assegnare a un nodo una configurazione di nodo usando il portale di Azure o un cmdlet di PowerShell.

  * Nel portale di Azure passare a **Home** -> **Automation Accounts** -> (account di automazione) **-> State configuration (DSC),** quindi selezionare un nodo e fare clic su **Assegna configurazione nodo**.
  * Utilizzare il cmdlet [Set-AzAutomationDscNode.](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0)

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>Scenario: non sono state prodotte configurazioni di nodi (file MOF) quando è stata compilata una configurazioneScenario: No node configurations (MOF files) were produced when a configuration was compiled

### <a name="issue"></a>Problema

Il processo di compilazione DSC viene sospeso con l'errore seguente:

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>Causa

Quando l'espressione `Node` che segue la parola chiave `$null`nella configurazione DSC restituisce , non viene prodotta alcuna configurazione di nodo.

### <a name="resolution"></a>Risoluzione

Utilizzare una delle seguenti soluzioni per risolvere il problema:

* Assicurarsi che l'espressione `Node` accanto alla parola chiave nella definizione di configurazione non sia la valutazione su Null.
* Se si passa [ConfigurationData](../automation-dsc-compile.md) durante la compilazione della configurazione, assicurarsi di passare i valori previsti dalla configurazione dai dati di configurazione.

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: il report del nodo DSC rimane bloccato nello stato In corso

### <a name="issue"></a>Problema

L'output dell'agente DSC è il seguente:

```error
No instance found with given property values
```

### <a name="cause"></a>Causa

La versione di Windows Management Framework (WMF) è stata aggiornata e si è danneggiata Strumentazione gestione Windows (WMI).

### <a name="resolution"></a>Risoluzione

Seguire le istruzioni in [Problemi noti e limitazioni di DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc).

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: Impossibile utilizzare una credenziale in una configurazione DSCScenario: Unable to use a credential in a DSC configuration

### <a name="issue"></a>Problema

Il processo di compilazione DSC è stato sospeso con l'errore:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>Causa

È stata usata una credenziale `ConfigurationData` in `PSDscAllowPlainTextPassword` una configurazione ma non è stata fornita la corretteta per impostare su true per ogni configurazione di nodo.

### <a name="resolution"></a>Risoluzione

Assicurarsi di passare `ConfigurationData` il `PSDscAllowPlainTextPassword` valore appropriato per impostare su true per ogni configurazione di nodo menzionato nella configurazione. Vedere [Compilazione di configurazioni DSC in Azure Automation State Configuration](../automation-dsc-compile.md).

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>Scenario: errore "Estensione di elaborazione errore" durante l'onboarding dall'estensione DSCScenario: "Failure processing extension" error when onboarding from DSC extension

### <a name="issue"></a>Problema

Quando si esegue l'onboarding utilizzando un'estensione DSC, si verifica un errore contenente l'errore:When onboarding using a DSC extension, a failure occurs containing the error:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione del nodo che non esiste nel servizio.

### <a name="resolution"></a>Risoluzione

* Assicurarsi di assegnare il nodo con un nome che corrisponda esattamente al nome nel servizio.
* È possibile scegliere di non includere il nome della configurazione del nodo, che comporta l'onboarding del nodo ma non l'assegnazione di una configurazione di nodo.

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>Scenario: "One or more errors occurred" error when registering a node using PowerShell

### <a name="issue"></a>Problema

Quando si registra un nodo utilizzando [Register-AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0) o [Register-AzureRMAutomationDSCNode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0), viene visualizzato il seguente errore:

```error
One or more errors occurred.
```

### <a name="cause"></a>Causa

Questo errore si verifica quando si tenta di registrare un nodo in una sottoscrizione separata da quella utilizzata dall'account di automazione.

### <a name="resolution"></a>Risoluzione

Considerare il nodo di sottoscrizione incrociata come se fosse definito per un cloud separato o in locale. Registrare il nodo utilizzando una di queste opzioni di onboarding:Register the node using one of these onboarding options:

* Windows - [Macchine Windows fisiche/virtuali in locale o in un cloud diverso da Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines).
* Linux - [Macchine Linux fisiche/virtuali in locale o in un cloud diverso da Azure.](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines)

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenario: Messaggio di errore - "Provisioning non riuscito"

### <a name="issue"></a>Problema

Quando si registra un nodo, viene visualizzato l'errore:When registering a node, you see the error:

```error
Provisioning has failed
```

### <a name="cause"></a>Causa

Questo messaggio si verifica quando si verifica un problema di connettività tra il nodo e Azure.This message occurs when there's an issue with connectivity between the node and Azure.

### <a name="resolution"></a>Risoluzione

Determinare se il nodo si trova in una rete privata virtuale (VPN) o presenta altri problemi di connessione ad Azure.Determine if your node is in a virtual private network (VPN) or has other issues connecting to Azure. Vedere [Risolvere gli errori durante l'onboarding delle soluzioni](onboarding.md).

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>Scenario: Failure with a general error when applying a configuration in Linux

### <a name="issue"></a>Problema

Quando si applica una configurazione in Linux, si verifica un errore contenente l'errore:When applying a configuration in Linux, a failure occurs containing the error:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>Causa

Se il percorso **/tmp** è impostato su `noexec`, la versione corrente di DSC non riesce ad applicare le configurazioni.

### <a name="resolution"></a>Risoluzione

Rimuovere `noexec` l'opzione dal percorso **/tmp.**

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: i nomi di configurazione dei nodi che si sovrappongono possono causare un rilascio non validoScenario: Node configuration names that overlap can result in bad release

### <a name="issue"></a>Problema

Quando si utilizza un singolo script di configurazione per generare configurazioni di più nodi e alcuni nomi di configurazione dei nodi sono sottoinsiemi di altri nomi, il servizio di compilazione può finire per assegnare la configurazione errata. Questo problema si verifica solo quando si utilizza un singolo script per generare configurazioni con dati di configurazione per nodo e solo quando il nome si sovrappone all'inizio della stringa. Un esempio è un singolo script di configurazione utilizzato per generare configurazioni basate sui dati del nodo passati come tabella hash utilizzando i cmdlet e i dati del nodo includono server denominati **server** e **1server**.

### <a name="cause"></a>Causa

Si tratta di un problema noto con il servizio di compilazione.

### <a name="resolution"></a>Risoluzione

La soluzione migliore consiste nel compilare localmente o in una pipeline CI/CD e caricare i file MOF di configurazione del nodo direttamente nel servizio. Se la compilazione nel servizio è un requisito, la soluzione migliore successiva consiste nel suddividere i processi di compilazione in modo che non vi siano sovrapposizioni nei nomi.

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: errore di timeout del gateway durante il caricamento della configurazione DSCScenario: Gateway timeout error on DSC configuration upload

#### <a name="issue"></a>Problema

Viene visualizzato `GatewayTimeout` un errore durante il caricamento di una configurazione DSC. 

### <a name="cause"></a>Causa

Le configurazioni DSC che richiedono molto tempo per la compilazione possono causare questo errore.

### <a name="resolution"></a>Risoluzione

È possibile fare in modo che le `ModuleName` configurazioni DSC vengano analizzate più rapidamente includendo in modo esplicito il parametro per tutte le chiamate [Import-DSCResource.You](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1) can make your DSC configurations parse faster by explicitly including the parameter for any Import-DSCResource calls.

## <a name="next-steps"></a>Passaggi successivi

Se non vedi il problema sopra o non riesci a risolvere il problema, prova uno dei seguenti canali per ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport)con , l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.