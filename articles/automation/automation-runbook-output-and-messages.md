---
title: Output di runbook e messaggi in automazione di Azure
description: Descrive come creare e recuperare messaggi di output e di errore dai runbook in Automazione di Azure.Describes how to create and retrieve output and error messages from runbooks in Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 457b2d2211ea1ba5fa36cec4b7e9a214f5bcad77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367092"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Output di runbook e messaggi in automazione di Azure

La maggior parte dei runbook di Automazione Azure ha una forma di output. Questo output può essere un messaggio di errore per l'utente o un oggetto complesso destinato a essere utilizzato con un altro runbook. Windows PowerShell fornisce [più flussi](/powershell/module/microsoft.powershell.core/about/about_redirection) per inviare l'output da uno script o flusso di lavoro. Automazione di Azure funziona con ogni flusso in modo diverso. Dovresti seguire le procedure consigliate per usare i flussi quando crei un runbook.

Nella tabella seguente viene descritto brevemente ogni flusso con il relativo comportamento nel portale di Azure per i runbook pubblicati e durante il [test di un runbook.](automation-testing-runbook.md) Il flusso di output è il flusso principale utilizzato per la comunicazione tra runbook. Gli altri flussi sono classificati come flussi di messaggi, destinati a comunicare informazioni all'utente. 

| STREAM | Descrizione | Pubblicato | Test |
|:--- |:--- |:--- |:--- |
| Errore |Messaggio di errore previsto per l'utente. A differenza di un'eccezione, il runbook continua dopo un messaggio di errore per impostazione predefinita. |Scritto nella cronologia dei processi |Visualizzato nel riquadro Output test |
| Debug |Messaggi destinati a un utente interattivo. Non devono essere utilizzati nei runbook. |Non scritto nella cronologia dei processi |Non visualizzato nel riquadro Output test |
| Output |Gli oggetti devono essere utilizzati da altri runbook. |Scritto nella cronologia dei processi |Visualizzato nel riquadro Output test |
| Progress |Record generati automaticamente prima e dopo ogni attività del runbook. Il runbook non deve tentare di creare i propri record di stato, poiché sono destinati a un utente interattivo. |Scritto nella cronologia dei processi solo se la registrazione dello stato di avanzamento è attivata per il runbook |Non visualizzato nel riquadro Output test |
| Dettagliato |Messaggi che includono informazioni generali o di debug. |Scritto nella cronologia dei processi solo se la registrazione dettagliata è attivata per il runbook |Visualizzato nel riquadro Output `VerbosePreference` test solo se la variabile è impostata su Continua nel runbookDisplayed in Test output pane only if variable is set to Continue in runbook |
| Avviso |Messaggio di avviso destinato all'utente. |Scritto nella cronologia dei processi |Visualizzato nel riquadro Output test |

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](automation-update-azure-modules.md)

## <a name="output-stream"></a>Flusso di output

Il flusso di output viene utilizzato per l'output di oggetti creati da uno script o da un flusso di lavoro quando viene eseguito correttamente. Automazione di Azure usa principalmente questo flusso per gli oggetti che devono essere utilizzati dai runbook padre che chiamano il [runbook corrente.](automation-child-runbooks.md) Quando un elemento padre [chiama un runbook inline](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution), l'elemento figlio restituisce i dati dal flusso di output all'elemento padre. 

Il runbook usa il flusso di output per comunicare informazioni generali al client solo se non viene mai chiamato da un altro runbook. Come procedura consigliata, tuttavia, si esegueiii i libri in genere devono usare il [flusso Dettagliato](#verbose-stream) per comunicare informazioni generali all'utente.

Fare in modo che il runbook scriva i dati nel flusso output utilizzando [Write-Output](https://technet.microsoft.com/library/hh849921.aspx). In alternativa, è possibile inserire l'oggetto sulla propria riga nello script.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Gestione dell'output da una funzioneHandling output from a function

Quando una funzione runbook scrive nel flusso di output, l'output viene passato nuovamente al runbook. Se il runbook assegna tale output a una variabile, l'output non viene scritto nel flusso di output. La scrittura in eventuali altri flussi da all'interno della funzione scriverà nel flusso corrispondente per il runbook. Si consideri il runbook del flusso di lavoro di PowerShell di esempio seguente.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Il flusso di output per il processo del runbook è:The Output stream for the runbook job is:

```output
Output inside of function
Output outside of function
```

Il flusso dettagliato per il processo del runbook è:The Verbose stream for the runbook job is:

```output
Verbose outside of function
Verbose inside of function
```

Dopo aver pubblicato il runbook e prima di avviarlo, è necessario attivare anche la registrazione dettagliata nelle impostazioni del runbook per ottenere l'output di flusso dettagliato.

### <a name="declaring-output-data-type"></a>Dichiarazione del tipo di dati di output

Di seguito sono riportati alcuni esempi di tipi di dati di output:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Dichiarare il tipo di dati di output in un flusso di lavoroDeclare output data type in a workflow

Un flusso di lavoro specifica il tipo di dati del relativo output utilizzando [l'attributo OutputType](https://technet.microsoft.com/library/hh847785.aspx). Questo attributo non ha alcun effetto durante il runtime, ma fornisce un'indicazione in fase di progettazione dell'output previsto del runbook. Man mano che il set di strumenti per i runbook continua a evolversi, aumenta l'importanza di dichiarare i tipi di dati di output in fase di progettazione. È pertanto consigliabile includere questa dichiarazione in tutti i runbook creati.

Il runbook di esempio seguente restituisce un oggetto string e include una dichiarazione del tipo di output. Se il runbook restituisce una matrice di un determinato tipo, è comunque necessario specificare il tipo invece di una matrice del tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Dichiarare il tipo di dati di output in un runbook graficoDeclare output data type in a graphical runbook

Per dichiarare un tipo di output in un runbook del flusso di lavoro PowerShell grafico o grafico, è possibile selezionare l'opzione di menu **Input e output** e immettere il tipo di output. Si consiglia di usare il nome completo della classe .NET per rendere il tipo facilmente identificabile quando un runbook padre vi fa riferimento. L'utilizzo del nome completo espone tutte le proprietà della classe al databus nel runbook e aumenta la flessibilità quando le proprietà vengono utilizzate per la logica condizionale, la registrazione e il riferimento come valori per altre attività del runbook.<br> ![Opzione Input e output del runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Dopo aver immesso un valore nel campo **Tipo di output** nel riquadro Proprietà input e output, assicurarsi di fare clic all'esterno del controllo in modo che riconosca la voce.

Nell'esempio seguente vengono illustrati due runbook grafici per illustrare la funzionalità Input e Output.The following example shows two graphical runbooks to demonstrate the Input and Output feature. Applicando il modello di progettazione modulare del runbook, si dispone di un runbook come modello Autentica runbook che gestisce l'autenticazione con Azure usando l'account RunAs.Applying the modular runbook design model, you have one runbook as the Authenticate Runbook template managing authentication with Azure using the RunAs account. Il secondo runbook, che normalmente esegue la logica di base per automatizzare un determinato scenario, in questo caso esegue il modello Autentica Runbook.The second runbook, which normally performs core logic to automate a given scenario, in this case executes the Authenticate Runbook template. I risultati vengono visualizzati nel riquadro di output del test. In circostanze normali, questo runbook eseguirebbe operazioni su una risorsa sfruttando l'output del runbook figlio.

Di seguito è riportata la logica di base del runbook **AuthenticateTo-Azure**.<br> ![Esempio di modello di runbook di autenticazione](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

Il runbook include `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`il tipo di output , che restituisce le proprietà del profilo di autenticazione.<br> ![Esempio di tipo di output del runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Anche se questo runbook è semplice, c'è un elemento di configurazione da chiamare qui. L'ultima attività `Write-Output` esegue il cmdlet per scrivere i dati del `Inputobject` profilo in una variabile utilizzando un'espressione di PowerShell per il parametro. Questo parametro `Write-Output`è obbligatorio per .

Il secondo runbook di questo esempio, denominato **Test-ChildOutputType**, definisce semplicemente due attività.<br> ![Esempio di runbook di tipo di output figlio](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

La prima attività chiama il runbook **AuthenticateTo-Azure.The** first activity calls the AuthenticateTo-Azure runbook. La seconda attività `Write-Verbose` esegue il cmdlet con **Origine dati** impostata su **Output attività**. Inoltre, **field path** è impostato su **Context.Subscription.SubscriptionName**, l'output di contesto del runbook **AuthenticateTo-Azure.**<br> ![Write-Verbose Cmdlet Parameter Data Source](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

L'output risultante è il nome della sottoscrizione.<br> ![Risultati del runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Flussi di messaggi

A differenza del flusso di output, i flussi di messaggi comunicano informazioni all'utente. Esistono più flussi di messaggi per diversi tipi di informazioni e Automazione di Azure gestisce ogni flusso in modo diverso.

### <a name="warning-and-error-streams"></a>Flussi di avviso ed errore

Gli avvisi e gli errori trasmettono i problemi di log che si verificano in un runbook. Automazione di Azure scrive questi flussi nella cronologia dei processi durante l'esecuzione di un runbook. L'automazione include i flussi nel riquadro Output test nel portale di Azure quando viene testato un runbook. 

Per impostazione predefinita, un runbook continua a essere eseguito dopo un avviso o un errore. È possibile specificare che il runbook deve essere sospeso in caso di avviso o errore facendo impostare una variabile di [preferenza](#preference-variables) nel runbook prima di creare il messaggio. Ad esempio, per fare in modo che il runbook venga `ErrorActionPreference` sospeso in caso di errore come in un'eccezione, impostare la variabile su Stop.For example, to cause the runbook to suspend on an error as it does on an exception, set the variable to Stop.

Creare un avviso o un messaggio di errore usando il cmdlet [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) o [Write-Error](https://technet.microsoft.com/library/hh849962.aspx). Le attività possono anche scrivere nei flussi avviso ed errore.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Flusso di Debug 

Automazione di Azure usa il flusso di messaggi Debug per gli utenti interattivi. Non deve essere usato nei runbook.

### <a name="verbose-stream"></a>Flusso dettagliato

Il flusso di messaggi dettagliato supporta informazioni generali sul funzionamento del runbook. Poiché il flusso di debug non è disponibile per un runbook, il runbook deve usare messaggi dettagliati per le informazioni di debug. 

Per impostazione predefinita, la cronologia dei processi non archivia i messaggi dettagliati dei runbook pubblicati, per motivi di prestazioni. Per archiviare messaggi dettagliati, usare la scheda **Configura** portale di Azure con l'impostazione **Registra record dettagliati** per configurare i runbook pubblicati per registrare messaggi dettagliati. Attivare questa opzione solo per risolvere i problemi o eseguire il debug di un runbook. Nella maggior parte dei casi, è consigliabile mantenere l'impostazione predefinita di non registrare i record dettagliati.

Quando si esegue il [test di un runbook](automation-testing-runbook.md) i messaggi dettagliati non vengono visualizzati, anche se il runbook è configurato per registrare i record dettagliati. Per visualizzare messaggi dettagliati durante il test di `VerbosePreference` [un runbook](automation-testing-runbook.md), è necessario impostare la variabile su Continua. Con tale variabile impostata, i messaggi dettagliati vengono visualizzati nel riquadro Output test del portale di Azure.With that variable set, verbose messages are displayed in the Test output pane of the Azure portal.

Il codice seguente crea un messaggio dettagliato utilizzando il cmdlet [Write-Verbose.](https://technet.microsoft.com/library/hh849951.aspx)

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Record di stato

È possibile usare la scheda **Configura** del portale di Azure per configurare un runbook per registrare i record di stato. L'impostazione predefinita consiste nel non registrare i record, per ottimizzare le prestazioni. Nella maggior parte dei casi, è consigliabile mantenere l'impostazione predefinita. Attivare questa opzione solo per risolvere i problemi o eseguire il debug di un runbook. 

Se si abilita la registrazione dei record di stato, il runbook scrive un record nella cronologia dei processi prima e dopo l'esecuzione di ogni attività. Il test di un runbook non visualizza i messaggi di stato anche se il runbook è configurato per registrare i record di stato.

>[!NOTE]
>Il cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) non è valido in un runbook, poiché questo cmdlet è destinato all'uso con un utente interattivo.

## <a name="preference-variables"></a>Variabili di preferenza

È possibile impostare determinate variabili di [preferenza](https://technet.microsoft.com/library/hh847796.aspx) di Windows PowerShell nei runbook per controllare la risposta ai dati inviati a diversi flussi di output. Nella tabella seguente sono elencate le variabili di preferenza che possono essere usate nei runbook, con i relativi valori predefiniti e validi. Sono disponibili valori aggiuntivi per le variabili di preferenza quando vengono usate in Windows PowerShell al di fuori di Automazione di Azure.Additional values are available for the preference variables when used in Windows PowerShell outside of Azure Automation.

| Variabile | Default Value | Valori validi |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Arresto<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Arresto<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Arresto<br>Continue<br>SilentlyContinue |

Nella tabella seguente viene elencato il comportamento per i valori delle variabili di preferenza validi nei runbook.

| valore | Comportamento |
|:--- |:--- |
| Continue |Registra il messaggio e continua l'esecuzione del runbook. |
| SilentlyContinue |Continua l'esecuzione del runbook senza registrazione del messaggio. Questo valore fa in modo che il messaggio venga ignorato. |
| Arresto |Registra il messaggio e sospende il runbook. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Recupero di messaggi e output di runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Recuperare l'output e i messaggi del runbook nel portale di AzureRetrieve runbook output and messages in Azure portal

È possibile visualizzare i dettagli di un processo del runbook nel portale di Azure usando la scheda **Processi** per il runbook. Nel riepilogo del processo vengono visualizzati i parametri di input e il [flusso](#output-stream)di output , oltre alle informazioni generali sul processo e sulle eventuali eccezioni che si sono verificate. La cronologia dei processi include i messaggi provenienti dai flussi Flusso di output e [Avvertenza ed Errore](#warning-and-error-streams). Include inoltre i messaggi dal [flusso dettagliato](#verbose-stream) e i record di [stato](#progress-records) se il runbook è configurato per registrare i record dettagliati e di stato.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Recuperare l'output e i messaggi del runbook in Windows PowerShellRetrieve runbook output and messages in Windows PowerShell

In Windows PowerShell è possibile recuperare output e messaggi da un runbook utilizzando il cmdlet [Get-AzAutomationJobOutput.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) Questo cmdlet richiede l'ID del processo `Stream` e dispone di un parametro chiamato in cui specificare il flusso da recuperare. È possibile specificare un valore Any per questo parametro per recuperare tutti i flussi per il processo.

Nell'esempio seguente viene avviato un runbook figlio e si attende il suo completamento. Al termine dell'esecuzione del runbook, lo script raccoglie il flusso di output del runbook dal processo.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Recuperare l'output e i messaggi del runbook nei runbook graficiRetrieve runbook output and messages in graphical runbooks

Per i runbook grafici, la registrazione aggiuntiva dell'output e dei messaggi è disponibile sotto forma di traccia a livello di attività. Sono disponibili due livelli di traccia: base e dettagliato. L'analisi di base visualizza l'ora di inizio e di fine per ogni attività nel runbook, oltre a informazioni correlate a qualsiasi tentativo di attività. Alcuni esempi sono il numero di tentativi e l'ora di inizio dell'attività. La traccia dettagliata include funzionalità di traccia di base oltre alla registrazione dei dati di input e output per ogni attività. 

Attualmente l'analisi a livello di attività scrive i record utilizzando il flusso Verbose. Pertanto è necessario abilitare la registrazione dettagliata quando si abilita la traccia. Per i runbook grafici con la traccia attivata non è necessario registrare record di avanzamento. La traccia di base svolge la stessa funzione e offre maggiori informazioni.

![Visualizzazione dei flussi dei processi di creazione grafica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

È possibile vedere dall'immagine che l'abilitazione della registrazione e traccia dettagliata per i runbook grafici rende disponibili molte più informazioni nella visualizzazione **dei flussi** di lavoro di produzione. Queste informazioni aggiuntive possono essere essenziali per la risoluzione dei problemi di produzione con un runbook. 

Tuttavia, a meno che non siano necessarie queste informazioni per tenere traccia dello stato di avanzamento di un runbook per la risoluzione dei problemi, è consigliabile mantenere la traccia disattivata come procedura generale. I record di traccia possono essere particolarmente numerosi. Con l'analisi grafica del runbook, è possibile ottenere da due a quattro record per attività, a seconda della configurazione dell'analisi di base o dettagliata.

**Per abilitare l'analisi a livello di attività:To enable activity-level tracing:**

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Runbooks** nella sezione **Process Automation** per aprire l'elenco dei runbook.
3. Nella pagina Runbooks selezionare un runbook grafico dall'elenco dei runbook.
4. In **Impostazioni** fare clic su **Registrazione e traccia**.
5. Nella pagina Registrazione e traccia, in **Registra record dettagliati,** fare clic su **Attivato** per abilitare la registrazione dettagliata.
6. In **Traccia a livello di attività**modificare il livello di traccia in Di **base** o **Dettagliato**, in base al livello di traccia richiesto.<br>

   ![Pannello Registrazione e traccia per la creazione grafica](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Recuperare l'output e i messaggi del runbook nei log di Monitoraggio di Microsoft AzureRetrieve runbook output and messages in Microsoft Azure Monitor logs

Automazione di Azure può inviare lo stato del processo del runbook e i flussi di lavoro all'area di lavoro di Log Analytics.Azure Automation can send runbook job status and job streams to your Log Analytics workspace. Monitoraggio di Azure supporta i log che consentono di:Azure Monitor supports logs that allow you to:

* Ottenere informazioni dettagliate sui processi di Automazione.
* Attivare un messaggio di posta elettronica o un avviso in base allo stato del processo del runbook, ad esempio non riuscito o sospeso.
* Scrivere query avanzate tra flussi di lavoro.
* Correlare i processi tra account di Automazione.
* Visualizzare la cronologia dei processi.

Per altre informazioni sulla configurazione dell'integrazione con i log di Monitoraggio di Azure per raccogliere, correlare e agire sui dati dei processi, vedere Inoltrare lo stato dei processi e i flussi di [processo dall'automazione ai log](automation-manage-send-joblogs-log-analytics.md)di Monitoraggio di Azure.For more information on configuring integration with Azure Monitor logs to collect, correlate, and act on job data, see Forward job status and job streams from Automation to Azure Monitor logs.

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sull'esecuzione del runbook, sul monitoraggio dei processi del runbook e su altri dettagli tecnici, vedere [Tenere traccia di un processo del runbook.](automation-runbook-execution.md)
* Per informazioni su come progettare e usare i runbook figlio, vedere [Runbook figlio in Automazione di Azure.](automation-child-runbooks.md)
* Per altre informazioni su PowerShell, inclusi i moduli di riferimento e apprendimento del linguaggio, vedere la documentazione di [PowerShell](/powershell/scripting/overview).
