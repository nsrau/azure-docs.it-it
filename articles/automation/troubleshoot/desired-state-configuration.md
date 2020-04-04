---
title: Risolvere i problemi relativi alla configurazione dello stato desiderato (DSC) di Automazione di AzureTroubleshoot Azure Automation Desired State Configuration (DSC)
description: Questo articolo contiene informazioni sulla risoluzione dei problemi di configurazione dello stato desiderato (DSC, Desired State Configuration)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9f33dc9528d5f7043dda2c6fad207a9a51347a2b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631484"
---
# <a name="troubleshoot-issues-with-azure-automation-desired-state-configuration-dsc"></a>Risolvere i problemi relativi alla configurazione dello stato desiderato (DSC) di Automazione di AzureTroubleshoot issues with Azure Automation Desired State Configuration (DSC)

Questo articolo contiene informazioni sulla risoluzione dei problemi della configurazione dello stato desiderato (DSC, Desired State Configuration).

## <a name="diagnosing-an-issue"></a>Diagnosi di un problema

Quando si verificano errori durante la compilazione o la distribuzione di configurazioni in Configurazione stato di Azure, ecco alcuni passaggi per diagnosticare il problema.

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. Assicurarsi che la configurazione venga compilata correttamente nel computer locale

La configurazione dello stato di Azure si basa su PowerShell DSC. È possibile trovare la documentazione per il linguaggio e la sintassi DSC in [Documenti di PowerShell DSC](https://docs.microsoft.com/powershell/scripting/overview).

Compilando la configurazione DSC nel computer locale, è possibile individuare e risolvere gli errori comuni, ad esempio:By compiling your DSC configuration on your local machine, you can discover and resolve common errors, such as:

   - Moduli mancanti
   - Errori di sintassi
   - Errori logici

### <a name="2-view-dsc-logs-on-your-node"></a>2. Visualizzare i registri DSC sul nodo

Se la configurazione viene compilata correttamente, ma non riesce quando viene applicata a un nodo, è possibile trovare informazioni dettagliate nei registri DSC. Per informazioni su dove trovare questi registri, vedere [Dove si trovano i registri eventi DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

Il modulo [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) può essere utili nell'analisi di informazioni dettagliate dai registri DSC. Se si contatta il supporto tecnico, questi registri richiedono per diagnosticare il problema.

È possibile installare il modulo xDscDiagnostics nel computer locale seguendo le istruzioni riportate in [Installare il modulo di versione stabile](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

Per installare il modulo xDscDiagnostics nel computer Azure, usare [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). È anche possibile usare l'opzione **Esegui comando** dal portale, seguendo i passaggi disponibili in Eseguire gli script di [PowerShell nella macchina virtuale Windows con Esegui comando](../../virtual-machines/windows/run-command.md).

Per informazioni sull'utilizzo di xDscDiagnostics, vedere [Utilizzo di xDscDiagnostics per analizzare i log DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs). Vedere anche [Cmdlet xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. Assicurarsi che i nodi e l'area di lavoro Automazione dispongano dei moduli necessari

DSC dipende dai moduli installati nel nodo. Quando si usa La configurazione dello stato di automazione di Azure, importare tutti i moduli necessari nell'account di automazione usando i passaggi elencati in [Import Modules](../shared-resources/modules.md#importing-modules). Le configurazioni possono anche avere una dipendenza da versioni specifiche dei moduli. Per ulteriori informazioni, vedere [Risoluzione dei problemi relativi ai moduli](shared-resources.md#modules).

## <a name="common-errors-when-working-with-dsc"></a>Errori comuni quando si lavora con DSCCommon errors when working with DSC

### <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>Scenario: una configurazione con caratteri speciali non può essere eliminata dal portale

#### <a name="issue"></a>Problema

Quando si tenta di eliminare una configurazione DSC dal portale, viene visualizzato il seguente errore:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Causa

Questo errore è un problema temporaneo che è stato pianificato per essere risolto.

#### <a name="resolution"></a>Risoluzione

* Utilizzare il cmdlet Az "Remove-AzAutomationDscConfiguration" per eliminare la configurazione.
* La documentazione di questo cmdlet non è stata ancora aggiornata.  Fino ad allora, fare riferimento alla documentazione per il modulo AzureRM.
  * [Rimuovere-AzureRmAutomationDSCConfigurationRemove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>Scenario: Failed to register Dsc Agent

#### <a name="issue"></a>Problema

Quando si tenta `Set-DscLocalConfigurationManager` di eseguire o un altro cmdlet DSC viene visualizzato l'errore:

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

#### <a name="cause"></a>Causa

Questo errore è normalmente causato da un firewall, il computer è dietro un server proxy o altri errori di rete.

#### <a name="resolution"></a>Risoluzione

Verificare che il computer abbia accesso agli endpoint appropriati per DSC di automazione di Azure e riprovare. Per un elenco delle porte e degli indirizzi necessari, [vedere](../automation-dsc-overview.md#network-planning)

### <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>Scenario: i rapporti di stato restituiscono il codice di risposta "Non autorizzato"

#### <a name="issue"></a>Problema

Quando si registra un nodo con configurazione di stato (DSC) viene visualizzato uno dei seguenti messaggi di errore:

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>Causa

Questo problema è causato da un certificato errato o scaduto.  Per ulteriori informazioni, vedere [Scadenza e nuova registrazione del certificato](../automation-dsc-onboarding.md#re-registering-a-node).

### <a name="resolution"></a>Risoluzione

Seguire i passaggi elencati di seguito per registrare nuovamente il nodo DSC in errore.

Innanzitutto, annullare la registrazione del nodo utilizzando la procedura seguente.

1. Dal portale di Azure, in**Account di automazione** **domestica** -> -> configurazione dello stato di > **(DSC)**
2. Fare clic su "Nodi" e fare clic sul nodo che ha problemi.
3. Fare clic su "Annulla registrazione" per annullare la registrazione del nodo.

In secondo luogo, disinstallare l'estensione DSC dal nodo.

1. Dal portale di Azure, in **Home** -> **Virtual Machine** -> ->-nodo "> **nodo",**
2. Fare clic su "Microsoft.Powershell.DSC".
3. Fare clic su "Disinstalla", per disinstallare l'estensione DiSC di PowerShell.

In terzo luogo, rimuovere tutti i certificati non valido o scaduti dal nodo.

Nel nodo in cui si è verificato l'errore da un prompt di Powershell con privilegi elevati eseguire le operazioni seguenti:On the failing node from an elevated Powershell Prompt, run the following:

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

Infine, registrare nuovamente il nodo in errore utilizzando la procedura seguente.

1. Dal portale di Azure, in**Account di automazione** **domestica** -> -> configurazione dello stato di > **(DSC)**
2. Fare clic su "Nodi".
3. Fare clic sul pulsante "Aggiungi".
4. Selezionare il nodo in errore.
5. Fare clic su "Connetti" e selezionare le opzioni desiderate.

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>Scenario: Lo stato di Node risulta non riuscito con un errore "Non trovato"

#### <a name="issue"></a>Problema

Un report del nodo indica lo stato **non riuscito** e contiene l'errore seguente:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione, ad esempio ABC, anziché un nome di configurazione nodo, ad esempio ABC.WebServer.

#### <a name="resolution"></a>Risoluzione

* Assicurarsi di assegnare il nodo con "nome configurazione nodo" e non il "nome configurazione".
* È possibile assegnare a un nodo una configurazione di nodo usando il portale di Azure o un cmdlet di PowerShell.

  * Per assegnare una configurazione di nodo a un nodo tramite il portale di Azure, aprire la pagina **Nodi DSC,** quindi selezionare un nodo e fare clic sul pulsante **Assegna configurazione nodo.**
  * Per assegnare una configurazione di nodo a un nodo utilizzando il cmdlet PowerShell, usare il cmdlet **Set-AzureRmAutomationDscNode**

### <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a><a name="no-mof-files"></a>Scenario: non sono state prodotte configurazioni di nodi (file MOF) quando viene compilata una configurazioneScenario: No node configurations (MOF files) were produced when a configuration is compiled

#### <a name="issue"></a>Problema

Il processo di compilazione DSC viene sospeso con l'errore seguente:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando l'espressione che segue la parola chiave **Node** nella configurazione DSC restituisce `$null`, non vengono prodotte configurazioni di nodo.

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Assicurarsi che l'espressione accanto alla parola chiave **Node** nella definizione di configurazione non stia valutando $null.
* Se durante la compilazione della configurazione si passano dei dati di configurazione, verificare di specificare i valori previsti necessari per la configurazione da [ConfigurationData](../automation-dsc-compile.md).

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-progress-state"></a><a name="dsc-in-progress"></a>Scenario: il report relativo al nodo DSC rimane bloccato sullo stato "in corso"

#### <a name="issue"></a>Problema

L'output dell'agente DSC è il seguente:

```error
No instance found with given property values
```

#### <a name="cause"></a>Causa

È stato eseguito l'aggiornamento della versione di WMF e WMI è stato danneggiato.

#### <a name="resolution"></a>Risoluzione

Per risolvere il problema, seguire le istruzioni nell'articolo [problemi noti e limitazioni di DSC.](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)

### <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>Scenario: Impossibile utilizzare una credenziale in una configurazione DSCScenario: Unable to use a credential in a DSC configuration

#### <a name="issue"></a>Problema

Il processo di compilazione DSC è stato sospeso con l'errore seguente:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

È stata utilizzata una credenziale in una configurazione ma non è stato fornito **ConfigurationData** appropriato per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione di nodo.

#### <a name="resolution"></a>Risoluzione

* Assicurarsi di passare il **valore ConfigurationData** appropriato per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione di nodo menzionata nella configurazione. Per altre informazioni, vedere [Compilazione di configurazioni DSC nella configurazione dello stato](../automation-dsc-compile.md)di automazione di Azure.For more information, see Compilazioning DSC configurations in Azure Automation State Configuration .

### <a name="scenario-onboarding-from-dsc-extension-failure-processing-extension-error"></a><a name="failure-processing-extension"></a>Scenario: onboarding dall'estensione dsc, errore "Estensione di elaborazione errori"Scenario: Onboarding from dsc extension, "Failure processing extension" error

#### <a name="issue"></a>Problema

Quando l'onboarding utilizzando l'estensione DSC, si verifica un errore contenente l'errore:When onboarding using DSC extension, a failure occurs containing the error:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione del nodo che non esiste nel servizio.

#### <a name="resolution"></a>Risoluzione

* Assicurarsi di assegnare il nodo con un nome di configurazione del nodo che corrisponda esattamente al nome nel servizio.
* È possibile scegliere di non includere il nome della configurazione del nodo, che comporterà l'onboarding del nodo ma non l'assegnazione di una configurazione di nodo

### <a name="scenario-registering-a-node-with-powershell-returns-the-error-one-or-more-errors-occurred"></a><a name="cross-subscription"></a>Scenario: Registrazione di un nodo con PowerShell restituisce l'errore "Si sono verificati uno o più errori"Scenario: Registering a node with PowerShell returns the error "One or more errors occurred"

#### <a name="issue"></a>Problema

Quando si registra `Register-AzAutomationDSCNode` un `Register-AzureRMAutomationDSCNode`nodo utilizzando o , viene visualizzato il seguente errore.

```error
One or more errors occurred.
```

#### <a name="cause"></a>Causa

Questo errore si verifica quando si tenta di registrare un nodo che si trova in una sottoscrizione separata rispetto all'account di automazione.

#### <a name="resolution"></a>Risoluzione

Considerare il nodo di sottoscrizione incrociata come se si trovasse in un cloud separato o in locale.

Attenersi alla seguente procedura per registrare il nodo.

* Windows - [Macchine Windows fisiche/virtuali in locale o in un cloud diverso da Azure/AWS](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances).
* Linux - [Macchine Linux fisiche/virtuali in locale o in un cloud diverso da Azure.](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure)

### <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>Scenario: Messaggio di errore - "Provisioning non riuscito"

#### <a name="issue"></a>Problema

Quando si registra un nodo, viene visualizzato l'errore:When registering a node, you see the error:

```error
Provisioning has failed
```

#### <a name="cause"></a>Causa

Questo messaggio si verifica quando si verifica un problema di connettività tra il nodo e Azure.This message occurs when there is a connectivity issue between the node and Azure.

#### <a name="resolution"></a>Risoluzione

Determinare se il nodo si trova in una rete virtuale privata o presenta altri problemi di connessione ad Azure.Determine whether your node is in a private virtual network or has other issues connecting to Azure.

Per ulteriori informazioni, vedere [Risolvere gli errori durante l'onboarding delle soluzioni](onboarding.md).

### <a name="scenario-applying-a-configuration-in-linux-a-failure-occurs-with-a-general-error"></a><a name="failure-linux-temp-noexec"></a>Scenario: applicazione di una configurazione in Linux, si verifica un errore con un errore generaleScenario: Applying a configuration in Linux, a failure occurs with a general error

#### <a name="issue"></a>Problema

Quando si applica una configurazione in Linux, si verifica un errore contenente l'errore:When applying a configuration in Linux, a failure occurs containing the error:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Causa

I clienti hanno `/tmp` identificato che `noexec`se la posizione è impostata su , la versione corrente di DSC non sarà in grado di applicare le configurazioni.

#### <a name="resolution"></a>Risoluzione

* Rimuovere `noexec` l'opzione `/tmp` dalla posizione.

### <a name="scenario-node-configuration-names-that-overlap-could-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>Scenario: i nomi di configurazione dei nodi che si sovrappongono potrebbero causare un rilascio non validoScenario: Node configuration names that overlap could result in bad release

#### <a name="issue"></a>Problema

Se un singolo script di configurazione viene utilizzato per generare più configurazioni di nodo e alcune configurazioni di nodo hanno un nome che è un sottoinsieme di altri, un problema nel servizio di compilazione potrebbe causare l'assegnazione di una configurazione errata.  Ciò si verifica solo quando si utilizza un singolo script per generare configurazioni con dati di configurazione per nodo e solo quando il nome si sovrappone si verifica all'inizio della stringa.

Ad esempio, se viene utilizzato un singolo script di configurazione per generare configurazioni basate sui dati del nodo passati come tabella hash utilizzando i cmdlet e i dati del nodo includono un server denominato "server" e "1server".

#### <a name="cause"></a>Causa

Problema noto con il servizio di compilazione.

#### <a name="resolution"></a>Risoluzione

La soluzione migliore consiste nel compilare localmente o in una pipeline CI/CD e caricare i file MOF direttamente nel servizio.  Se la compilazione nel servizio è un requisito, la soluzione migliore successiva consiste nel suddividere i processi di compilazione in modo che non vi siano sovrapposizioni nei nomi.

### <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>Scenario: errore di timeout del gateway durante il caricamento della configurazione DSCScenario: Gateway timeout error on DSC configuration upload

#### <a name="issue"></a>Problema

Viene visualizzato `GatewayTimeout` un errore durante il caricamento di una configurazione DSC. 

#### <a name="cause"></a>Causa

Le configurazioni DSC che richiedono molto tempo per la compilazione possono causare questo errore.

#### <a name="resolution"></a>Risoluzione

È possibile fare in modo che le `ModuleName` configurazioni `Import-DscResource` DSC vengano analizzate più rapidamente includendo in modo esplicito il parametro per tutte le chiamate. Per ulteriori informazioni, vedere [Utilizzo di Import-DSCResource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1).

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
