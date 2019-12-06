---
title: Risoluzione dei problemi della configurazione dello stato desiderato (DSC) di Automazione di Azure
description: Questo articolo contiene informazioni sulla risoluzione dei problemi di configurazione dello stato desiderato (DSC, Desired State Configuration)
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1a45ed90b2b2c4a3a4f8eb11c4618c11e6d66761
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849361"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Risolvere i problemi della configurazione dello stato desiderato (DSC)

Questo articolo contiene informazioni sulla risoluzione dei problemi della configurazione dello stato desiderato (DSC, Desired State Configuration).

## <a name="steps-to-troubleshoot-desired-state-configuration-dsc"></a>Passaggi per la risoluzione dei problemi di desired state Configuration (DSC)

Quando si verificano errori durante la compilazione o la distribuzione di configurazioni in Azure state Configuration, di seguito sono riportati alcuni passaggi che consentono di diagnosticare il problema.

1. **Assicurarsi che la configurazione venga compilata correttamente nel computer locale:**  La configurazione dello stato di Azure si basa su PowerShell DSC. È possibile trovare la documentazione relativa alla sintassi e al linguaggio DSC in [PowerShell DSC docs](https://docs.microsoft.com/powershell/scripting/overview).

   Compilando la configurazione DSC nel computer locale è possibile individuare e risolvere gli errori comuni, ad esempio:

   - **Moduli mancanti**
   - **Errori di sintassi**
   - **Errori di logica**

2. **Visualizzare i log DSC nel nodo:** Se la configurazione viene compilata correttamente, ma ha esito negativo quando viene applicata a un nodo, è possibile trovare informazioni dettagliate nei log. Per informazioni su dove trovare i log DSC, vedere [dove sono i registri eventi DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs).

   Inoltre, [xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics) può essere utile per l'analisi di informazioni dettagliate dai log DSC. Se si contatta il supporto tecnico, questi registri saranno necessari per diagnosticare il problema.

   È possibile installare **xDscDiagnostics** nel computer locale usando le istruzioni disponibili in [installare il modulo versione stabile](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module).

   Per installare **xDscDiagnostics** nel computer Azure, è possibile usare [AZ VM Run-Command](/cli/azure/vm/run-command) o [Invoke-AzVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand). È anche possibile usare l'opzione **Esegui comando** dal portale, seguendo i passaggi descritti in [eseguire script di PowerShell nella macchina virtuale Windows con il comando Esegui](../../virtual-machines/windows/run-command.md).

   Per informazioni sull'uso di **xDscDiagnostics**, vedere [uso di xDscDiagnostics per analizzare i log DSC](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs), nonché i [cmdlet di xDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics#cmdlets).
3. **Verificare che i nodi e l'area di lavoro di automazione dispongano dei moduli necessari:** La configurazione dello stato desiderato dipende dai moduli installati nel nodo.  Quando si usa la configurazione dello stato di automazione di Azure, importare tutti i moduli richiesti nell'account di automazione usando i passaggi elencati in [importare i moduli](../shared-resources/modules.md#import-modules). Le configurazioni possono anche avere una dipendenza da versioni specifiche dei moduli.  Per ulteriori informazioni, vedere [risoluzione dei problemi relativi ai moduli](shared-resources.md#modules).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Errori comuni durante l'utilizzo di Desired State Configuration (DSC)

### <a name="unsupported-characters"></a>Scenario: una configurazione con caratteri speciali non può essere eliminata dal portale

#### <a name="issue"></a>Problema

Quando si tenta di eliminare una configurazione DSC dal portale, viene visualizzato l'errore seguente:

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

#### <a name="cause"></a>Causa

Questo errore è un problema temporaneo che è pianificato per essere risolto.

#### <a name="resolution"></a>Risoluzione

* Usare il comando AZ cmdlet "Remove-AzAutomationDscConfiguration" per eliminare la configurazione.
* La documentazione per questo cmdlet non è stata ancora aggiornata.  Fino ad allora, fare riferimento alla documentazione per il modulo AzureRM.
  * [Remove-AzureRmAutomationDSCConfiguration](/powershell/module/azurerm.automation/Remove-AzureRmAutomationDscConfiguration)

### <a name="failed-to-register-agent"></a>Scenario: non è stato possibile registrare l'agente DSC

#### <a name="issue"></a>Problema

Quando si tenta di eseguire `Set-DscLocalConfigurationManager` o un altro cmdlet DSC, viene visualizzato l'errore:

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

Questo errore è in genere causato da un firewall, dal computer che si trova dietro un server proxy o da altri errori di rete.

#### <a name="resolution"></a>Risoluzione

Verificare che il computer abbia accesso agli endpoint appropriati per Automation DSC di Azure e riprovare. Per un elenco di porte e indirizzi necessari, vedere [pianificazione della rete](../automation-dsc-overview.md#network-planning)

### <a name="failed-not-found"></a>Scenario: Lo stato del nodo indica che non è riuscito con errore "Non trovato"

#### <a name="issue"></a>Problema

Un report del nodo indica lo stato **non riuscito** e contiene l'errore seguente:

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione, ad esempio ABC, anziché un nome di configurazione nodo, ad esempio ABC.WebServer.

#### <a name="resolution"></a>Risoluzione

* Assicurarsi di assegnare il nodo "nome configurazione nodo" e non il "nome configurazione".
* È possibile assegnare a un nodo una configurazione di nodo usando il portale di Azure o un cmdlet di PowerShell.

  * Per assegnare una configurazione del nodo a un nodo usando portale di Azure, aprire la pagina **nodi DSC** , selezionare un nodo e fare clic sul pulsante **Assegna configurazione nodo** .
  * Per assegnare una configurazione del nodo a un nodo usando il cmdlet di PowerShell, usare il cmdlet **set-AzureRmAutomationDscNode**

### <a name="no-mof-files"></a>Scenario: Non sono state prodotte configurazioni di nodo (file MOF) durante la compilazione di una configurazione

#### <a name="issue"></a>Problema

Il processo di compilazione DSC viene sospeso con l'errore seguente:

```error
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Causa

Quando l'espressione che segue la parola chiave **Node** nella configurazione DSC restituisce `$null`, non vengono prodotte configurazioni di nodo.

#### <a name="resolution"></a>Risoluzione

una qualsiasi delle soluzioni seguenti consente di correggere il problema:

* Verificare che l'espressione accanto alla parola chiave **node** nella definizione di configurazione non stia valutando $null.
* Se durante la compilazione della configurazione si passano dei dati di configurazione, verificare di specificare i valori previsti necessari per la configurazione da [ConfigurationData](../automation-dsc-compile.md).

### <a name="dsc-in-progress"></a>Scenario: Il report relativo al nodo DSC rimane bloccato nello stato "in corso"

#### <a name="issue"></a>Problema

L'output dell'agente DSC è il seguente:

```error
No instance found with given property values
```

#### <a name="cause"></a>Causa

È stato eseguito l'aggiornamento della versione di WMF e WMI è stato danneggiato.

#### <a name="resolution"></a>Risoluzione

Per risolvere il problema, seguire le istruzioni riportate nell'articolo [limitazioni e problemi noti di DSC](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc) .

### <a name="issue-using-credential"></a>Scenario: Non è possibile usare le credenziali in una configurazione DSC

#### <a name="issue"></a>Problema

Il processo di compilazione DSC è stato sospeso con l'errore seguente:

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Causa

Sono state usate le credenziali in una configurazione ma non è stato fornito un **configurationData** appropriato per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione del nodo.

#### <a name="resolution"></a>Risoluzione

* Assicurarsi di passare il **configurationData** appropriato per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione di nodo citata nella configurazione. Per altre informazioni, vedere la sezione relativa agli [asset in Automation DSC per Azure](../automation-dsc-compile.md#working-with-assets-in-azure-automation-during-compilation).

### <a name="failure-processing-extension"></a>Scenario: onboarding dall'estensione DSC, errore dell'estensione per l'elaborazione degli errori

#### <a name="issue"></a>Problema

Quando si esegue l'onboarding con l'estensione DSC, si verifica un errore che contiene l'errore:

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

#### <a name="cause"></a>Causa

Questo errore si verifica in genere quando al nodo viene assegnato un nome di configurazione di nodo che non esiste nel servizio.

#### <a name="resolution"></a>Risoluzione

* Assicurarsi di assegnare il nodo con un nome di configurazione nodo che corrisponda esattamente al nome del servizio.
* È possibile scegliere di non includere il nome della configurazione del nodo, che comporterà l'onboarding del nodo, ma non l'assegnazione di una configurazione del nodo

### <a name="failure-linux-temp-noexec"></a>Scenario: applicazione di una configurazione in Linux. si verifica un errore con un errore generale

#### <a name="issue"></a>Problema

Quando si applica una configurazione in Linux, si verifica un errore che contiene l'errore:

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

#### <a name="cause"></a>Causa

I clienti hanno identificato che se il percorso del `/tmp` è impostato su `noexec`, la versione corrente di DSC non sarà in grado di applicare le configurazioni.

#### <a name="resolution"></a>Risoluzione

* Rimuovere l'opzione `noexec` dal percorso `/tmp`.

### <a name="compilation-node-name-overlap"></a>Scenario: i nomi di configurazione del nodo che si sovrappongono possono causare una versione non valida

#### <a name="issue"></a>Problema

Se viene utilizzato un singolo script di configurazione per generare più configurazioni di nodo e alcune delle configurazioni del nodo hanno un nome che è un subset di altri, un problema nel servizio di compilazione può comportare l'assegnazione della configurazione errata.  Questo si verifica solo quando si usa un singolo script per generare configurazioni con i dati di configurazione per nodo e solo quando si verifica una sovrapposizione del nome all'inizio della stringa.

Esempio, se viene utilizzato un singolo script di configurazione per generare configurazioni basate sui dati del nodo passati come Hashtable mediante i cmdlet e i dati del nodo includono un server denominato "Server" e "1Server".

#### <a name="cause"></a>Causa

Problema noto con il servizio di compilazione.

#### <a name="resolution"></a>Risoluzione

La soluzione migliore consiste nel compilare localmente o in una pipeline CI/CD e caricare i file MOF direttamente nel servizio.  Se la compilazione nel servizio è un requisito, la soluzione migliore successiva consiste nel suddividere i processi di compilazione in modo che non vi siano sovrapposizioni nei nomi.

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/)
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al sito del [supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Richiedi supporto**.
