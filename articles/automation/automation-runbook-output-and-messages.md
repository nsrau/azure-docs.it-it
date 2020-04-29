---
title: Output di runbook e messaggi in automazione di Azure
description: Viene descritto come creare e recuperare messaggi di output e di errore da manuali operativi in automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 92b6378b00e12f618d07798b5ce789cbd9971544
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535537"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Output di runbook e messaggi in automazione di Azure

La maggior parte dei runbook di Automazione Azure ha una forma di output. Questo output può essere un messaggio di errore all'utente o un oggetto complesso destinato a essere usato con un altro Runbook. Windows PowerShell fornisce [più flussi](/powershell/module/microsoft.powershell.core/about/about_redirection) per inviare l'output da uno script o flusso di lavoro. Automazione di Azure funziona con ogni flusso in modo diverso. È consigliabile seguire le procedure consigliate per l'uso dei flussi durante la creazione di un Runbook.

Nella tabella seguente viene brevemente descritto ogni flusso con il relativo comportamento nell'portale di Azure per manuali operativi pubblicati e durante il [test di un Runbook](automation-testing-runbook.md). Il flusso di output è il flusso principale usato per la comunicazione tra manuali operativi. Gli altri flussi sono classificati come flussi di messaggi, progettati per comunicare informazioni all'utente. 

| STREAM | Descrizione | Pubblicato | Test |
|:--- |:--- |:--- |:--- |
| Errore |Messaggio di errore previsto per l'utente. A differenza di un'eccezione, il Runbook continua dopo un messaggio di errore per impostazione predefinita. |Scritto nella cronologia processo |Visualizzato nel riquadro di output del test |
| Debug |Messaggi destinati a un utente interattivo. Non devono essere utilizzati nei runbook. |Non viene scritto nella cronologia processo |Non visualizzato nel riquadro di output del test |
| Output |Gli oggetti devono essere utilizzati da altri runbook. |Scritto nella cronologia processo |Visualizzato nel riquadro di output del test |
| Progress |Record generati automaticamente prima e dopo ogni attività del runbook. Il Runbook non deve provare a creare i propri record di stato, perché sono destinati a un utente interattivo. |Viene scritto nella cronologia processo solo se è attivata la registrazione dello stato per il Runbook |Non visualizzato nel riquadro di output del test |
| Dettagliato |Messaggi che includono informazioni generali o di debug. |Viene scritto nella cronologia processo solo se è attivata la registrazione dettagliata per il Runbook |Visualizzato nel riquadro di output del test `VerbosePreference` solo se la variabile è impostata per continuare in Runbook |
| Avviso |Messaggio di avviso destinato all'utente. |Scritto nella cronologia processo |Visualizzato nel riquadro di output del test |

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="output-stream"></a>Flusso di output

Il flusso di output viene usato per l'output degli oggetti creati da uno script o un flusso di lavoro quando viene eseguito correttamente. Automazione di Azure usa principalmente questo flusso per gli oggetti che devono essere usati dai manuali operativi padre che chiamano il [Runbook corrente](automation-child-runbooks.md). Quando un padre [chiama un Runbook inline](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution), l'elemento figlio restituisce i dati dal flusso di output all'elemento padre. 

Il Runbook usa il flusso di output per comunicare informazioni generali al client solo se non viene mai chiamato da un altro Runbook. Come procedura consigliata, tuttavia, è consigliabile usare il [flusso dettagliato](#verbose-stream) per comunicare le informazioni generali all'utente manuali operativi in genere.

Fare in modo che il Runbook scriva i dati nel flusso di output usando [Write-Output](https://technet.microsoft.com/library/hh849921.aspx). In alternativa, è possibile inserire l'oggetto nella propria riga dello script.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Gestione dell'output di una funzione

Quando una funzione Runbook scrive nel flusso di output, l'output viene passato nuovamente al Runbook. Se il Runbook assegna tale output a una variabile, l'output non viene scritto nel flusso di output. La scrittura in eventuali altri flussi da all'interno della funzione scriverà nel flusso corrispondente per il runbook. Si consideri il seguente esempio di flusso di lavoro PowerShell Runbook.

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

Il flusso di output per il processo del Runbook è:

```output
Output inside of function
Output outside of function
```

Il flusso dettagliato per il processo Runbook è il seguente:

```output
Verbose outside of function
Verbose inside of function
```

Dopo aver pubblicato il Runbook e prima di avviarlo, è necessario attivare anche la registrazione dettagliata nelle impostazioni di Runbook per ottenere l'output del flusso dettagliato.

### <a name="declaring-output-data-type"></a>Dichiarazione del tipo di dati di output

Di seguito sono riportati alcuni esempi di tipi di dati di output:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Dichiarare il tipo di dati di output in un flusso di lavoro

Un flusso di lavoro specifica il tipo di dati dell'output usando l' [attributo OutputType](https://technet.microsoft.com/library/hh847785.aspx). Questo attributo non ha alcun effetto durante il runtime, ma fornisce un'indicazione in fase di progettazione dell'output previsto del Runbook. Man mano che il set di strumenti per manuali operativi continua ad evolvere, l'importanza della dichiarazione dei tipi di dati di output in fase di progettazione aumenta. È quindi consigliabile includere questa dichiarazione in tutti manuali operativi creati.

Il runbook di esempio seguente restituisce un oggetto string e include una dichiarazione del tipo di output. Se il runbook restituisce una matrice di un determinato tipo, è comunque necessario specificare il tipo invece di una matrice del tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Dichiarare il tipo di dati di output in un Runbook grafico

Per dichiarare un tipo di output in un Runbook grafico o grafico di un flusso di lavoro PowerShell, è possibile selezionare l'opzione di menu **input e output** e immettere il tipo di output. È consigliabile usare il nome completo della classe .NET per rendere il tipo facilmente identificabile quando un Runbook padre vi fa riferimento. L'utilizzo del nome completo espone tutte le proprietà della classe a DataBus in Runbook e aumenta la flessibilità quando le proprietà vengono utilizzate per la logica condizionale, la registrazione e il riferimento come valori per altre attività Runbook.<br> ![Opzione Input e output del runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Dopo aver immesso un valore nel campo **tipo di output** nel riquadro proprietà input e output, assicurarsi di fare clic all'esterno del controllo in modo che riconosca la voce.

Nell'esempio seguente vengono illustrati due manuali operativi grafici per illustrare la funzionalità di input e output. Applicando il modello di progettazione modulare Runbook, è necessario un Runbook come modello di autenticazione Runbook per la gestione dell'autenticazione con Azure tramite l'account RunAs. Il secondo Runbook, che in genere esegue la logica di base per automatizzare uno scenario specifico, in questo caso esegue il modello di autenticazione Runbook. I risultati vengono visualizzati nel riquadro di output del test. In circostanze normali, questo runbook eseguirebbe operazioni su una risorsa sfruttando l'output del runbook figlio.

Di seguito è riportata la logica di base del runbook **AuthenticateTo-Azure**.<br> ![Esempio di modello di runbook di autenticazione](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

Runbook include il tipo `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`di output, che restituisce le proprietà del profilo di autenticazione.<br> ![Esempio di tipo di output del runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Sebbene questo runbook sia semplice, è presente un elemento di configurazione da richiamare qui. L'ultima attività esegue il `Write-Output` cmdlet per scrivere i dati di profilo in una variabile usando un'espressione di PowerShell `Inputobject` per il parametro. Questo parametro è obbligatorio per `Write-Output`.

Il secondo Runbook in questo esempio, denominato **test-ChildOutputType**, definisce semplicemente due attività.<br> ![Esempio di runbook di tipo di output figlio](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

La prima attività chiama **Runbook authenticateto-Azure** Runbook. La seconda attività esegue il `Write-Verbose` cmdlet con l' **origine dati** impostata sull' **output dell'attività**. Inoltre, il **campo percorso** è impostato su **context. Subscription. subscriptionname**, l'output del contesto di **Runbook authenticateto-Azure** Runbook.<br> ![Origine dati dei parametri del cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

L'output risultante è il nome della sottoscrizione.<br> ![Risultati del runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Flussi di messaggi

A differenza del flusso di output, i flussi di messaggi comunicano le informazioni all'utente. Sono disponibili più flussi di messaggi per diversi tipi di informazioni e automazione di Azure gestisce ogni flusso in modo diverso.

### <a name="warning-and-error-streams"></a>Flussi di avviso ed errore

I flussi di avviso ed errore registrano i problemi che si verificano in un Runbook. Automazione di Azure scrive questi flussi nella cronologia processo durante l'esecuzione di un Runbook. L'automazione include i flussi nel riquadro di output del test nel portale di Azure quando viene testato un Runbook. 

Per impostazione predefinita, un Runbook continua a essere eseguito dopo un avviso o un errore. È possibile specificare che il Runbook deve essere sospeso in caso di avviso o errore Runbook impostando una [variabile di preferenza](#preference-variables) prima di creare il messaggio. Ad esempio, per fare in modo che Runbook si sospenda in caso di errore in un'eccezione, impostare `ErrorActionPreference` la variabile su Stop.

Creare un avviso o un messaggio di errore usando il cmdlet [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) o [Write-Error](https://technet.microsoft.com/library/hh849962.aspx). Le attività possono anche scrivere nei flussi di errore e di avviso.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Flusso di Debug 

Automazione di Azure usa il flusso di messaggi di debug per gli utenti interattivi. Non deve essere usato in manuali operativi.

### <a name="verbose-stream"></a>Flusso dettagliato

Il flusso di messaggi dettagliati supporta le informazioni generali sull'operazione Runbook. Poiché il flusso di debug non è disponibile per un Runbook, il Runbook deve usare messaggi dettagliati per le informazioni di debug. 

Per impostazione predefinita, la cronologia processo non archivia i messaggi dettagliati da manuali operativi pubblicati, per motivi di prestazioni. Per archiviare i messaggi dettagliati, usare la scheda portale di Azure **Configura** con l'impostazione **Registra record dettagliati** per configurare il manuali operativi pubblicato per la registrazione dei messaggi dettagliati. Attivare questa opzione solo per risolvere i problemi o eseguire il debug di un runbook. Nella maggior parte dei casi, è consigliabile lasciare l'impostazione predefinita di non registrare i record dettagliati.

Quando si esegue il [test di un runbook](automation-testing-runbook.md) i messaggi dettagliati non vengono visualizzati, anche se il runbook è configurato per registrare i record dettagliati. Per visualizzare i messaggi dettagliati durante [il test di un Runbook](automation-testing-runbook.md), è `VerbosePreference` necessario impostare la variabile per continuare. Con tale set di variabili, i messaggi dettagliati vengono visualizzati nel riquadro di output del test del portale di Azure.

Il codice seguente crea un messaggio dettagliato con il cmdlet [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) .

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Record di stato

È possibile usare la scheda **Configura** del portale di Azure per configurare un Runbook per la registrazione dei record di stato. Per ottimizzare le prestazioni, l'impostazione predefinita consiste nel non registrare i record. Nella maggior parte dei casi, è consigliabile usare l'impostazione predefinita. Attivare questa opzione solo per risolvere i problemi o eseguire il debug di un runbook. 

Se si Abilita la registrazione dei record di stato, il Runbook scrive un record nella cronologia processo prima e dopo l'esecuzione di ogni attività. Il test di un Runbook non Visualizza i messaggi di stato anche se il Runbook è configurato per registrare i record di stato.

>[!NOTE]
>Il cmdlet [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) non è valido in un runbook, poiché questo cmdlet è destinato all'uso con un utente interattivo.

## <a name="preference-variables"></a>Variabili di preferenza

È possibile impostare determinate variabili di [preferenza](https://technet.microsoft.com/library/hh847796.aspx) di Windows PowerShell in manuali operativi per controllare la risposta ai dati inviati a flussi di output diversi. La tabella seguente elenca le variabili di preferenza che possono essere usate in manuali operativi, con i relativi valori predefiniti e validi. Sono disponibili valori aggiuntivi per le variabili di preferenza quando vengono usati in Windows PowerShell all'esterno di automazione di Azure.

| Variabile | Valore predefinito | Valori validi |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Arresto<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Arresto<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Arresto<br>Continue<br>SilentlyContinue |

La tabella seguente elenca il comportamento per i valori delle variabili di preferenza validi in manuali operativi.

| valore | Comportamento |
|:--- |:--- |
| Continue |Registra il messaggio e continua l'esecuzione del runbook. |
| SilentlyContinue |Continua l'esecuzione del runbook senza registrazione del messaggio. Questo valore fa in modo che il messaggio venga ignorato. |
| Arresto |Registra il messaggio e sospende il runbook. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Recupero di messaggi e output di runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Recuperare l'output e i messaggi di Runbook in portale di Azure

È possibile visualizzare i dettagli di un processo del Runbook nel portale di Azure usando la scheda **processi** per il Runbook. Il riepilogo del processo Visualizza i parametri di input e il [flusso di output](#output-stream), oltre alle informazioni generali sul processo e alle eventuali eccezioni che si sono verificate. La cronologia del processo include i messaggi dal flusso di output e dai [flussi di avviso ed errore](#warning-and-error-streams). Include anche i messaggi dal [flusso dettagliato](#verbose-stream) e i [record di avanzamento](#progress-records) se il Runbook è configurato per la registrazione dei record dettagliati e di avanzamento.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Recuperare l'output e i messaggi di Runbook in Windows PowerShell

In Windows PowerShell è possibile recuperare l'output e i messaggi da un Runbook usando il cmdlet [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) . Questo cmdlet richiede l'ID del processo e contiene un parametro denominato `Stream` in cui specificare il flusso da recuperare. È possibile specificare un valore qualsiasi per questo parametro per recuperare tutti i flussi per il processo.

Nell'esempio seguente viene avviato un runbook figlio e si attende il suo completamento. Al termine dell'esecuzione di Runbook, lo script raccoglie il flusso di output Runbook dal processo.

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Recuperare l'output e i messaggi di Runbook nei manuali operativi grafici

Per manuali operativi grafici, la registrazione aggiuntiva di output e messaggi è disponibile sotto forma di traccia a livello di attività. Sono disponibili due livelli di traccia: base e dettagliato. La traccia di base Visualizza l'ora di inizio e di fine per ogni attività nel Runbook, oltre a informazioni relative a eventuali tentativi di attività. Alcuni esempi sono il numero di tentativi e l'ora di inizio dell'attività. La traccia dettagliata include le funzionalità di traccia di base e la registrazione dei dati di input e di output per ogni attività. 

Attualmente la traccia a livello di attività scrive i record usando il flusso dettagliato. È pertanto necessario abilitare la registrazione dettagliata quando si Abilita la traccia. Per i runbook grafici con la traccia attivata non è necessario registrare record di avanzamento. La traccia di base svolge la stessa funzione e offre maggiori informazioni.

![Visualizzazione dei flussi dei processi di creazione grafica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Dall'immagine che Abilita la registrazione e la traccia dettagliata per manuali operativi grafici sono disponibili molte più informazioni nella visualizzazione **flussi dei processi** di produzione. Queste informazioni aggiuntive possono essere essenziali per la risoluzione dei problemi di produzione con un Runbook. 

Tuttavia, a meno che non siano necessarie queste informazioni per tenere traccia dello stato di avanzamento di un Runbook per la risoluzione dei problemi, potrebbe essere necessario disattivare la traccia come procedura generale. I record di traccia possono essere particolarmente numerosi. Grazie alla traccia Runbook grafica, è possibile ottenere da due a quattro record per ogni attività, a seconda della configurazione della traccia di base o dettagliata.

**Per abilitare la traccia a livello di attività:**

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.
3. Nella pagina manuali operativi selezionare un Runbook grafico dall'elenco di manuali operativi.
4. In **Impostazioni** fare clic su **Registrazione e traccia**.
5. Nella pagina registrazione e traccia, in **Registra record dettagliati**, fare clic **su** attiva per abilitare la registrazione dettagliata.
6. In **traccia a livello di attività**, impostare il livello di traccia su **Basic** o **dettagliato**, in base al livello di traccia necessario.<br>

   ![Pannello Registrazione e traccia per la creazione grafica](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Recuperare l'output e i messaggi di Runbook nei log del monitoraggio Microsoft Azure

Automazione di Azure può inviare lo stato e i flussi del processo del runbook all'area di lavoro Log Analytics. Monitoraggio di Azure supporta i log che consentono di:

* Ottenere informazioni dettagliate sui processi di Automazione.
* Attivare un messaggio di posta elettronica o un avviso in base allo stato del processo del Runbook, ad esempio non riuscito o sospeso.
* Scrivere query avanzate tra flussi di processi.
* Correlare i processi tra account di Automazione.
* Visualizza cronologia processi.

Per altre informazioni sulla configurazione dell'integrazione con i log di monitoraggio di Azure per raccogliere, correlare e agire sui dati del processo, vedere [trasmettere lo stato e i flussi del processo dall'automazione ai log di monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sull'esecuzione di Runbook, sul monitoraggio dei processi Runbook e su altri dettagli tecnici, vedere [tenere traccia di un processo Runbook](automation-runbook-execution.md).
* Per informazioni su come progettare e usare manuali operativi figlio, vedere [manuali operativi figlio in automazione di Azure](automation-child-runbooks.md).
* Per altre informazioni su PowerShell, incluse le informazioni di riferimento sul linguaggio e i moduli di apprendimento, vedere la [documentazione di PowerShell](/powershell/scripting/overview).
