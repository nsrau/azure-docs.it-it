---
title: Configurare l'output del Runbook e i flussi di messaggi
description: Questo articolo illustra come implementare la logica di gestione degli errori e descrive i flussi di output e messaggi nei manuali operativi di automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.openlocfilehash: beed3ec50d0c7990168ee75976c732796cdbe246
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324421"
---
# <a name="configure-runbook-output-and-message-streams"></a>Configurare l'output del Runbook e i flussi di messaggi

La maggior parte dei runbook di Automazione Azure ha una forma di output. L'output può essere un messaggio di errore destinato all'utente o un oggetto complesso il cui uso era destinato a un altro runbook. Windows PowerShell fornisce [più flussi](/powershell/module/microsoft.powershell.core/about/about_redirection) per inviare l'output da uno script o flusso di lavoro. Automazione di Azure funziona con ogni flusso in modo diverso. Seguire le procedure consigliate per usare i flussi quando si crea un runbook.

La tabella seguente descrive brevemente ogni flusso con il relativo comportamento nel portale di Azure per i runbook pubblicati e durante il [test di un runbook](./manage-runbooks.md). Il flusso di output è il principale flusso usato per la comunicazione tra i runbook. Gli altri flussi sono classificati come flussi di messaggi, destinati a comunicare informazioni all'utente.

| STREAM | Descrizione | Pubblicato | Test |
|:--- |:--- |:--- |:--- |
| Errore |Messaggio di errore previsto per l'utente. A differenza di un'eccezione, per impostazione predefinita il runbook continua dopo un messaggio di errore. |Scritto nella cronologia del processo |Visualizzato nel riquadro di output del test |
| Debug |Messaggi destinati a un utente interattivo. Non devono essere utilizzati nei runbook. |Non scritti nella cronologia del processo |Non visualizzati nel riquadro di output del test |
| Output |Gli oggetti devono essere utilizzati da altri runbook. |Scritti nella cronologia del processo |Visualizzati nel riquadro di output del test |
| Progress |Record generati automaticamente prima e dopo ogni attività del runbook. Il runbook non dovrebbe tentare di creare i propri record di stato, dato che sono destinati a un utente interattivo. |Scritti nella cronologia processo solo se la registrazione dello stato è attivata per il runbook |Non visualizzati nel riquadro di output del test |
| Dettagliato |Messaggi che includono informazioni generali o di debug. |Scritti nella cronologia processo solo se è attivata la registrazione dettagliata per il runbook |Visualizzati nel riquadro di output del test solo se la variabile `VerbosePreference` è impostata su Continua nel runbook |
| Avviso |Messaggio di avviso destinato all'utente. |Scritto nella cronologia del processo |Visualizzato nel riquadro di output del test |

## <a name="use-the-output-stream"></a>Usare il flusso di output

Il flusso di output viene usato per l'output degli oggetti creati da uno script o da un flusso di lavoro se eseguito correttamente. Automazione di Azure usa principalmente questo flusso per gli oggetti che devono essere usati dai runbook padre che chiamano il [runbook corrente](automation-child-runbooks.md). Quando un runbook padre [chiama un runbook inline](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution), il runbook figlio restituisce i dati del flusso di output al runbook padre.

Il runbook usa il flusso di output per comunicare informazioni generali al client solo se non viene mai chiamato da un altro runbook. Come procedura consigliata, tuttavia, i runbook devono usare in generale il [flusso dettagliato](#write-output-to-verbose-stream) per comunicare informazioni generali all'utente.

Fare in modo che il runbook scriva i dati nel flusso di output usando [Write-Output](/powershell/module/microsoft.powershell.utility/write-output). In alternativa, è possibile inserire l'oggetto nella relativa riga dello script.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Gestire l'output da una funzione

Quando una funzione del runbook scrive nel flusso di output, l'output ritorna al runbook. Se il runbook assegna l'output a una variabile, non è scritto nel flusso di output. La scrittura in eventuali altri flussi da all'interno della funzione scriverà nel flusso corrispondente per il runbook. Si consideri l'esempio di runbook del flusso di lavoro di PowerShell seguente.

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

Il flusso di output per il processo di runbook è:

```output
Output inside of function
Output outside of function
```

Il flusso dettagliato per il processo di runbook è:

```output
Verbose outside of function
Verbose inside of function
```

Dopo aver pubblicato il runbook e prima di avviarlo, è anche necessario attivare la registrazione dettagliata nelle impostazioni del runbook, per ottenere l'output del flusso dettagliato.

### <a name="declare-output-data-type"></a>Dichiarare il tipo di dati di output

Di seguito sono riportati esempi di tipi di dati di output:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Dichiarare il tipo di dati di output in un flusso di lavoro

Un flusso di lavoro specifica il tipo di dati del relativo output usando l'[attributo OutputType](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Questo attributo non ha alcun effetto durante il runtime, ma offre un'indicazione in fase di progettazione dell'output previsto del runbook. Man mano che il set di strumenti per i runbook continua a evolversi, l'importanza della dichiarazione dei tipi di dati di output in fase di progettazione aumenterà. Quindi, è consigliabile includere questa dichiarazione in qualsiasi runbook creato.

Il runbook di esempio seguente restituisce un oggetto string e include una dichiarazione del tipo di output. Se il runbook restituisce una matrice di un determinato tipo, è comunque necessario specificare il tipo invece di una matrice del tipo.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Dichiarare il tipo di dati di output in un runbook grafico

Per dichiarare un tipo di output in un runbook grafico o del flusso di lavoro di PowerShell grafico, è possibile selezionare l'opzione di menu **Input e output** e digitare il tipo di output. È consigliabile usare il nome della classe .NET completo, in modo che il tipo sia facilmente identificabile nel riferimento da un runbook padre. L'uso del nome completo espone tutte le proprietà della classe nel bus di dati del runbook e migliora la flessibilità quando le proprietà vengono usate per la logica condizionale, la registrazione e il riferimento come valori per altre attività del runbook.<br> ![Opzione Input e output del runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Dopo aver immesso un valore nel campo **Tipo di output** nel riquadro delle proprietà Input e output, assicurarsi di fare clic all'esterno del controllo in modo che la voce venga riconosciuta.

L'esempio seguente mostra due runbook grafici per illustrare la funzionalità Input e output. Con l'applicazione del modello di progettazione modulare del runbook, si avrà un runbook come modello di runbook di autenticazione che gestisce l'autenticazione con Azure tramite l'account RunAs. Il secondo runbook, che in genere esegue la logica di base per automatizzare uno scenario specifico, in questo caso esegue il modello di runbook di autenticazione. I risultati vengono visualizzati nel riquadro di output del test. In circostanze normali, questo runbook eseguirebbe operazioni su una risorsa sfruttando l'output del runbook figlio.

Di seguito è riportata la logica di base del runbook **AuthenticateTo-Azure**.<br> ![Esempio di modello di runbook di autenticazione](media/automation-runbook-output-and-messages/runbook-authentication-template.png).

Il runbook include il tipo di output `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`, che restituisce le proprietà del profilo di autenticazione.<br> ![Esempio di tipo di output del runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

Anche se questo runbook è semplice, è necessario chiamare un elemento di configurazione. L'ultima attività esegue il cmdlet `Write-Output` per scrivere i dati del profilo in una variabile usando un'espressione di PowerShell per il parametro `Inputobject`. Questo parametro è obbligatorio per `Write-Output`.

Il secondo runbook di questo esempio, denominato **Test-ChildOutputType** , definisce semplicemente due attività.<br> ![Esempio di runbook di tipo di output figlio](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

La prima attività chiama il runbook **AuthenticateTo-Azure**. La seconda attività esegue il cmdlet `Write-Verbose` con **Origine dati** impostato su **Output attività**. Anche **Percorso campo** è impostato su **Context.Subscription.SubscriptionName** , l'output del contesto del runbook **AuthenticateTo-Azure**.<br> ![Origine dati del parametro per il cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

L'output risultante è il nome della sottoscrizione.<br> ![Risultati del runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Utilizzo dei flussi di messaggi

A differenza del flusso di output, i flussi di messaggi comunicano le informazioni all'utente. Esistono più flussi di messaggi per diversi tipi di informazioni e ciascuno viene gestito diversamente da Automazione di Azure.

### <a name="write-output-to-warning-and-error-streams"></a>Scrivi output in flussi di avviso ed errore

I flussi di errore e di avviso registrano i problemi che si verificano in un runbook. Automazione di Azure scrive questi flussi nella cronologia processo durante l'esecuzione di un runbook. Automazione include i flussi nel riquadro di output del test del portale di Azure quando viene testato un runbook.

Per impostazione predefinita, il runbook continua a essere eseguito dopo un avviso o un errore. È possibile specificare che il runbook deve essere sospeso in caso di avviso o errore impostando una [variabile di preferenza](#work-with-preference-variables) del runbook prima di creare il messaggio. Ad esempio, per far sì che un runbook venga sospeso in caso di errore come succede per un'eccezione, impostare la variabile `ErrorActionPreference` su Arresta.

Creare un avviso o un messaggio di errore usando il cmdlet [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) o [Write-Error](/powershell/module/microsoft.powershell.utility/write-error). Le attività possono scrivere anche nei flussi di errore e di avviso.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Scrivi output nel flusso di debug

Automazione di Azure usa il flusso dei messaggi di debug per gli utenti interattivi. Per impostazione predefinita, automazione di Azure non acquisisce i dati del flusso di debug, vengono acquisiti solo i dati di output, di errore e di avviso, oltre ai dati dettagliati se il Runbook è configurato per l'acquisizione.

Per acquisire i dati del flusso di debug, è necessario eseguire due azioni in manuali operativi:

1. Impostare la variabile `$GLOBAL:DebugPreference="Continue"` , che indica a PowerShell di continuare ogni volta che viene rilevato un messaggio di debug.  La parte **$Global:** indica a PowerShell di eseguire questa operazione nell'ambito globale anziché nell'ambito locale in cui si trova lo script al momento dell'esecuzione dell'istruzione.

1. Reindirizzare il flusso di debug che non viene acquisito in un flusso che viene acquisito come *output*. Questa operazione viene eseguita impostando il reindirizzamento di PowerShell sull'istruzione da eseguire. Per ulteriori informazioni sul reindirizzamento PowerShell, vedere informazioni sul [Reindirizzamento](/powershell/module/microsoft.powershell.core/about/about_redirection).

#### <a name="examples"></a>Esempi

In questo esempio, Runbook viene configurato usando i `Write-Output` cmdlet e `Write-Debug` con l'intenzione di output di due flussi diversi.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Se il Runbook deve essere eseguito così com'è, il riquadro di output per il processo Runbook eseguirà il flusso dell'output seguente:

```output
This is an output message.
```

In questo esempio, Runbook è configurato in modo simile all'esempio precedente, ad eccezione del fatto che l'istruzione `$GLOBAL:DebugPreference="Continue"` è inclusa con l'aggiunta di `5>&1` alla fine dell' `Write-Debug` istruzione.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Se questo runbook venisse eseguito, il riquadro di output per il processo Runbook avrebbe trasmesso l'output seguente:

```output
This is an output message.
This is a debug message.
```

Questo problema si verifica perché l' `$GLOBAL:DebugPreference="Continue"` istruzione indica a PowerShell di visualizzare i messaggi di debug e l'aggiunta di `5>&1` alla fine dell' `Write-Debug` istruzione indica a PowerShell di reindirizzare il flusso 5 (debug) al flusso 1 (output).

### <a name="write-output-to-verbose-stream"></a>Scrivi output nel flusso dettagliato

Il flusso del messaggio dettagliato supporta informazioni generali sul funzionamento del runbook. Poiché il flusso di debug non è disponibile per il runbook, questo deve usare i messaggi dettagliati per le informazioni di debug.

Per impostazione predefinita, la cronologia processo non archivia i messaggi dettagliati dei runbook pubblicati, per motivi di prestazioni. Per archiviare i messaggi dettagliati, usare la scheda **Configura** nel portale di Azure con l'impostazione **Registra record dettagliati** per configurare i runbook pubblicati affinché registrino i messaggi dettagliati. Attivare questa opzione solo per risolvere i problemi o eseguire il debug di un runbook. Nella maggior parte dei casi, è consigliabile mantenere l'impostazione predefinita che non registra i record dettagliati.

Quando si esegue il [test di un runbook](./manage-runbooks.md) i messaggi dettagliati non vengono visualizzati, anche se il runbook è configurato per registrare i record dettagliati. Per visualizzare i messaggi dettagliati durante il [test di un runbook](./manage-runbooks.md), è necessario impostare la variabile `VerbosePreference` su Continua. Con tale set di variabili, i messaggi dettagliati vengono visualizzati nel riquadro di output del test del portale di Azure.

Il codice seguente crea un messaggio dettagliato con il cmdlet [Write-Verbose](/powershell/module/microsoft.powershell.utility/write-verbose).

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Gestire i record di stato

È possibile usare la scheda **Configura** del portale di Azure per configurare un runbook affinché registri i record di stato. Per ottimizzare le prestazioni, l'impostazione predefinita prevede di non registrare i record. Nella maggior parte dei casi, è consigliabile mantenere l'impostazione predefinita. Attivare questa opzione solo per risolvere i problemi o eseguire il debug di un runbook.

Se si abilita la registrazione dei record di stato, il runbook scrive il record nella cronologia processo prima e dopo l'esecuzione di ogni attività. Il test di un runbook non mostra i messaggi di stato dettagliati anche se il runbook è configurato per registrare i record di stato.

>[!NOTE]
>Il cmdlet [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) non è valido in un runbook, poiché questo cmdlet è destinato all'uso con un utente interattivo.

## <a name="work-with-preference-variables"></a>Usare le variabili di preferenza

È possibile impostare alcune [variabili di preferenza](/powershell/module/microsoft.powershell.core/about/about_preference_variables) di Windows PowerShell nei runbook per controllare la risposta ai dati inviati a flussi di output diversi. La tabella seguente elenca le variabili di preferenza che possono essere usate nei runbook con i relativi valori validi e predefiniti. Gli altri valori sono disponibili per le variabili di preferenza se usate in Windows PowerShell al di fuori di Automazione di Azure.

| Variabile | Default Value | Valori validi |
|:--- |:--- |:--- |
| `WarningPreference` |Continue |Arresto<br>Continue<br>SilentlyContinue |
| `ErrorActionPreference` |Continue |Arresto<br>Continue<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Arresto<br>Continue<br>SilentlyContinue |

La tabella seguente elenca il comportamento dei valori delle variabili di preferenza valide nei runbook.

| valore | Comportamento |
|:--- |:--- |
| Continue |Registra il messaggio e continua l'esecuzione del runbook. |
| SilentlyContinue |Continua l'esecuzione del runbook senza registrazione del messaggio. Questo valore fa in modo che il messaggio venga ignorato. |
| Arresto |Registra il messaggio e sospende il runbook. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Recuperare i messaggi e l'output del runbook

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Recuperare i messaggi e l'output del runbook nel portale di Azure

È possibile visualizzare i dettagli del processo di un runbook nel portale di Azure usando la scheda **Processi** per il runbook. Il riepilogo del processo mostra i parametri di input e il [flusso di output](#use-the-output-stream), oltre alle informazioni generali sul processo e a eventuali eccezioni che si sono verificate. La cronologia processo include i messaggi dei flussi di output e i [flussi di avviso e di errore](#write-output-to-warning-and-error-streams). Include anche i messaggi del [flusso dettagliato](#write-output-to-verbose-stream) e dei [record di stato](#handle-progress-records) se il runbook è configurato per registrare i record dettagliati e di stato.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Recuperare i messaggi e l'output del runbook in Windows PowerShell

In Windows PowerShell, è possibile recuperare i messaggi e l'output di un runbook tramite il cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput). Questo cmdlet richiede l'ID del processo e dispone di un parametro denominato `Stream` in cui è possibile specificare il flusso da recuperare. È possibile specificare il valore Qualsiasi per questo parametro per recuperare tutti i flussi del processo.

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Recuperare i messaggi e l'output del runbook nei runbook grafici

Per i runbook grafici, la registrazione aggiuntiva dell'output e dei messaggi è disponibile sotto forma di traccia a livello di attività. Sono disponibili due livelli di traccia: di base e dettagliata. La traccia di base mostra l'ora di inizio e di fine di ogni attività nel runbook, oltre a informazioni relative a eventuali nuovi tentativi di esecuzione dell'attività. Alcuni esempi sono il numero di tentativi e l'ora di inizio dell'attività. La traccia dettagliata include le funzionalità della traccia di base oltre alla registrazione dei dati di input e output per ogni attività. 

Attualmente la traccia a livello di attività scrive i record usando il flusso dettagliato. È pertanto necessario abilitare la registrazione dettagliata quando si abilita la traccia. Per i runbook grafici con la traccia attivata non è necessario registrare record di avanzamento. La traccia di base svolge la stessa funzione e offre maggiori informazioni.

![Visualizzazione dei flussi dei processi di creazione grafica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

È possibile vedere nell'immagine che quando si abilitano la registrazione e la traccia dettagliata per i runbook grafici sono disponibili molte più informazioni nella visualizzazione **Flussi di processo** di produzione. Queste informazioni aggiuntive possono essere essenziali per la risoluzione dei problemi di produzione con un runbook. 

Tuttavia, a meno che non siano necessarie per tenere traccia dello stato di un runbook per la risoluzione dei problemi, in generale, è consigliabile non abilitare la traccia. I record di traccia possono essere particolarmente numerosi. Con la traccia dei runbook grafici è possibile ottenere da due a quattro record per ogni attività, a seconda della configurazione della traccia di base o dettagliata.

**Per abilitare la traccia a livello di attività:**

1. Nel portale di Azure aprire l'account di automazione.
2. Selezionare **Runbook** in **Automazione processi** per aprire l'elenco dei runbook.
3. Nella pagina Runbook selezionare un runbook grafico dall'elenco dei runbook.
4. In **Impostazioni** fare clic su **Registrazione e traccia**.
5. Nella pagina Registrazione e traccia fare clic su **Attiva** per abilitare la registrazione dettagliata in **Registra record dettagliati**.
6. In **Traccia a livello di attività** impostare il livello di traccia su **Di base** o **Dettagliato** , in base al livello di traccia necessario.<br>

   ![Pannello Registrazione e traccia per la creazione grafica](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Recuperare l'output e messaggi dei runbook nei log di Monitoraggio di Microsoft Azure

Automazione di Azure può inviare lo stato e i flussi del processo del runbook all'area di lavoro Log Analytics. Monitoraggio di Azure supporta i log che consentono di:

* Ottenere informazioni dettagliate sui processi di Automazione.
* Attivare un messaggio di posta elettronica o un avviso in base allo stato del processo del runbook, ad esempio Non riuscito o Sospeso.
* Scrivere query avanzate nei flussi del processo.
* Correlare i processi tra account di Automazione.
* Visualizzare la cronologia processo.

Per altre informazioni sulla configurazione dell'integrazione con i log di monitoraggio di Azure per raccogliere, correlare e agire sui dati del processo, vedere [trasmettere lo stato e i flussi del processo dall'automazione ai log di monitoraggio di Azure](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Passaggi successivi

* Per usare i runbook, vedere [Gestire runbook in Automazione di Azure](manage-runbooks.md).
* Se non si ha familiarità con gli script di PowerShell, vedere la documentazione di [PowerShell](/powershell/scripting/overview) .
* Per informazioni di riferimento sui cmdlet di PowerShell per automazione di Azure, vedere [AZ. Automation](/powershell/module/az.automation).
