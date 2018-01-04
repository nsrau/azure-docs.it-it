---
title: Output e messaggi dei runbook in Automazione di Azure | Documentazione Microsoft
description: Viene descritto come creare e recuperare l'output e i messaggi di errore da runbook in automazione di Azure.
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 13a414f5-0e2c-4be2-9558-a3e3ec84b6b2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2016
ms.author: magoedte;bwren
ms.openlocfilehash: 415eddaec9702a42ceee51858a39840fcd6a202b
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Output di runbook e messaggi in automazione di Azure
La maggior parte dei runbook di automazione di Azure è munita di output, ad esempio un messaggio di errore all'utente o un oggetto complesso deve essere utilizzato da un altro flusso di lavoro. Windows PowerShell fornisce [più flussi](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) per inviare l'output da uno script o flusso di lavoro. Automazione di Azure funziona con ciascuno di questi flussi in modo diverso ed è necessario seguire le procedure consigliate per utilizzarli quando si crea un runbook.

Nella tabella seguente fornisce una breve descrizione di ognuno dei flussi e del relativo comportamento nel portale di Azure durante l'esecuzione di un runbook pubblicato e quando [test di un runbook](automation-testing-runbook.md). Nelle sezioni successive vengono forniti ulteriori dettagli su ogni flusso.

| Flusso | DESCRIZIONE | Pubblicato | Test |
|:--- |:--- |:--- |:--- |
| Output |Gli oggetti devono essere utilizzati da altri runbook. |Scrivere la cronologia dei processi. |Visualizzata nel pannello Output del Test. |
| Avviso |Messaggio di avviso destinato all'utente. |Scrivere la cronologia dei processi. |Visualizzata nel pannello Output del Test. |
| Tipi di errore |Messaggio di errore previsto per l'utente. A differenza di un'eccezione, il runbook continua dopo un messaggio di errore per impostazione predefinita. |Scrivere la cronologia dei processi. |Visualizzata nel pannello Output del Test. |
| Dettagliato |Messaggi che forniscono informazioni generali o di debug. |Scritti nella cronologia del processo solo se è attivata la registrazione dettagliata per il runbook. |Viene visualizzata nel pannello Output del Test solo se $VerbosePreference è impostata su Continue nel runbook. |
| Avanzamento |Record generati automaticamente prima e dopo ogni attività del runbook. Il runbook non dovrebbe tentare di creare i propri record di stato di avanzamento poiché sono destinati ad un utente interattivo. |Scritti nella cronologia processo solo se lo stato di avanzamento della registrazione è attivata per il runbook. |Non sono visualizzati nel pannello Output del Test. |
| Debug |Messaggi destinati a un utente interattivo. Non devono essere utilizzati nei runbook. |Non scritti nella cronologia del processo. |Non scritti nel pannello Output del Test. |

## <a name="output-stream"></a>Flusso di output
Il flusso di Output è destinato agli output degli oggetti creati da uno script o da un flusso di lavoro quando vengono eseguiti correttamente. In automazione di Azure, il flusso viene utilizzato principalmente per gli oggetti che devono essere utilizzati da [runbook padre che chiamano il runbook corrente](automation-child-runbooks.md). In caso di [chiamata di un runbook inline](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) da un runbook padre, vengono restituiti i dati dal flusso di output all'elemento padre. Utilizzare solo il flusso di output per comunicare informazioni generali all'utente se si è a conosce del fatto che il runbook non verrà mai chiamato da un altro runbook. Come procedura consigliata, tuttavia, è consigliabile in genere utilizzare il [Flusso dettagliato](#Verbose) per comunicare informazioni generali all'utente.

È possibile scrivere i dati per il flusso di output tramite [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) o inserendo l'oggetto nella relativa riga nel runbook.

    #The following lines both write an object to the output stream.
    Write-Output –InputObject $object
    $object

### <a name="output-from-a-function"></a>Output da una funzione
Quando si scrive nel flusso di output in una funzione inclusa nel runbook, l'output viene passato nuovamente al runbook. Se il runbook assegna l'output a una variabile, allora non è scritto nel flusso di output. La scrittura in altri flussi della funzione scrive nel flusso corrispondente per il runbook.

Considerare il runbook di esempio seguente:

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


Il flusso di output per il processo di runbook sarebbe:

    Output inside of function
    Output outside of function

Il flusso dettagliato per il processo di runbook sarebbe:

    Verbose outside of function
    Verbose inside of function

Dopo aver pubblicato il runbook e prima di avviarlo, è anche necessario attivare la registrazione dettagliata nelle impostazioni del runbook per ottenere l'output di flusso dettagliato.

### <a name="declaring-output-data-type"></a>Dichiarazione del tipo di dati di output
Un flusso di lavoro può specificare il tipo di dati del relativo output utilizzando l’ [attributo OutputType](http://technet.microsoft.com/library/hh847785.aspx). Questo attributo non ha alcun effetto in fase di esecuzione, ma offre un'indicazione all'autore del runbook in fase di progettazione dell’output previsto del runbook. Il set di strumenti per i runbook continua evoluzione, aumenta l'importanza della dichiarazione di tipi di dati di output in fase di progettazione importanza. Di conseguenza, è consigliabile includere questa dichiarazione in qualsiasi runbook creato.

Di seguito è riportato un elenco di tipi di output di esempio:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Il runbook di esempio seguente restituisce un oggetto string e include una dichiarazione del tipo di output. Se il runbook restituisce una matrice di un determinato tipo, è comunque necessario specificare il tipo invece di una matrice del tipo.

    Workflow Test-Runbook
    {
       [OutputType([string])]

       $output = "This is some string output."
       Write-Output $output
    }

Per dichiarare un tipo di output in runbook grafico o grafica del flusso di lavoro di PowerShell, è possibile selezionare il **di Input e Output** opzione di menu e digitare il nome tipo di output. È consigliabile usare il nome della classe .NET completo affinché sia facilmente identificabile nel riferimento da un runbook padre. Questa operazione espone tutte le proprietà di tale classe nel bus di dati del runbook e offre una notevole flessibilità quando le proprietà vengono usate per la logica condizionale, la registrazione e il riferimento come valori per altre attività del runbook.<br> ![Opzione Input e output del runbook](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

Nell'esempio seguente sono disponibili due runbook grafici per illustrare questa funzionalità. Se si applica il modello di progettazione di runbook modulari, abbiamo un runbook, che serve come il *modello autenticazione Runbook* gestisce l'autenticazione con Azure utilizzando l'account RunAs. Il secondo runbook, che normalmente eseguirebbe la logica di base per automatizzare un determinato scenario, in questo caso eseguirà il *modello di runbook di autenticazione* e visualizzerà i risultati nel riquadro di output **Test** .  In circostanze normali, questo runbook eseguirebbe operazioni su una risorsa sfruttando l'output del runbook figlio.    

Di seguito è riportata la logica di base del runbook **AuthenticateTo-Azure**.<br> ![Esempio di modello di runbook di autenticazione](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Include il tipo di output *Microsoft.Azure.Commands.Profile.Models.PSAzureContext* che restituirà le proprietà del profilo di autenticazione.<br> ![Esempio di tipo di output del runbook](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Mentre il runbook è semplice, è un elemento di configurazione da sottolineare qui. L'ultima attività esegue il cmdlet **Write-Output** e scrive i dati di profilo in una variabile $_ usando un'espressione di PowerShell per il parametro **Inputobject**, necessario per tale cmdlet.  

Il secondo runbook di questo esempio, denominato *Test-ChildOutputType*, include semplicemente due attività.<br> ![Esempio di runbook di tipo di output figlio](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

La prima attività chiama il runbook **AuthenticateTo-Azure** e la seconda esegue il cmdlet **Write-Verbose** con **Output attività** come **Origine dati**. Il valore di **Percorso campo** è **Context.Subscription.SubscriptionName**, che specifica l'output di contesto del runbook **AuthenticateTo-Azure**.<br> ![Origine dati per il cmdlet Write-Verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

L'output risultante è il nome della sottoscrizione.<br> ![Risultati del runbook Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Una nota sul comportamento del controllo Tipo di output. Quando si digita un valore nel campo Tipo di output del pannello delle proprietà Input e output, affinché il valore immesso venga riconosciuto dal controllo è necessario fare clic all'esterno del controllo dopo aver digitato.  

## <a name="message-streams"></a>Flussi di messaggi
A differenza del flusso di output, i flussi di messaggi sono utili per comunicare informazioni all'utente. Sono presenti più flussi di messaggi per diversi tipi di informazioni e ciascuno viene gestito diversamente dall'automazione di Azure.

### <a name="warning-and-error-streams"></a>Flussi di avviso ed errore
I flussi di errore e di avviso sono utili per registrare i problemi che si verificano in un runbook. Vengono scritti nella cronologia processo quando viene eseguito un runbook e vengono inclusi nel riquadro di Output del Test nel portale di Azure quando viene testato un runbook. Per impostazione predefinita, il runbook continuerà l'esecuzione dopo un avviso o un errore. È possibile specificare che il runbook deve essere sospeso su un avviso o un errore impostando una [variabile di preferenza](#PreferenceVariables) nel runbook prima di creare il messaggio. Ad esempio, per far sì che un runbook venga sospeso in caso di errore come farebbe un'eccezione, impostare **$ErrorActionPreference** su Stop.

Creare un avviso o un messaggio di errore usando il cmdlet [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) o [Write-Error](http://technet.microsoft.com/library/hh849962.aspx). Anche le attività possono scrivere tali flussi.

    #The following lines create a warning message and then an error message that will suspend the runbook.

    $ErrorActionPreference = "Stop"
    Write-Warning –Message "This is a warning message."
    Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### <a name="verbose-stream"></a>Flusso dettagliato
Il flusso del messaggio dettagliato è destinato a informazioni generali sull'operazione del runbook. Poiché il [Flusso di Debug](#Debug) non è disponibile in un runbook, i messaggi dettagliati devono essere utilizzati per le informazioni di debug. Per impostazione predefinita, i messaggi dettagliati da runbook pubblicati non vengono archiviati nella cronologia del processo. Per archiviare i messaggi dettagliati, configurare i runbook pubblicati per Registra record dettagliati nella scheda Configura del runbook nel portale di Azure. Nella maggior parte dei casi, è consigliabile mantenere l'impostazione predefinita che non registra record dettagliati per un runbook per motivi di prestazioni. Attivare questa opzione solo per risolvere i problemi o eseguire il debug di un runbook.

Quando si esegue il [test di un runbook](automation-testing-runbook.md), i messaggi dettagliati non vengono visualizzati anche se il runbook è configurato per registrare i record dettagliati. Per visualizzare i messaggi dettagliati durante il [test di un runbook](automation-testing-runbook.md), è necessario impostare la variabile $VerbosePreference su Continue. Con l'impostazione di tale variabile, i messaggi dettagliati vengono visualizzati nel riquadro di Output del Test del portale di Azure.

Creare un messaggio dettagliato utilizzando il cmdlet [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx) .

    #The following line creates a verbose message.

    Write-Verbose –Message "This is a verbose message."

### <a name="debug-stream"></a>Flusso di Debug 
Il flusso di Debug deve essere utilizzato con un utente interattivo e non deve essere utilizzato nei runbook.

## <a name="progress-records"></a>Record di stato
Se si configura un runbook per registrare i record di stato di avanzamento (nella scheda Configura del runbook nel portale di Azure), verrà scritto un record per la cronologia dei processi prima e dopo l'esecuzione di ogni attività. Nella maggior parte dei casi, è consigliabile mantenere l'impostazione predefinita che non registra record dettagliati per un runbook per massimizzare le prestazioni. Attivare questa opzione solo per risolvere i problemi o eseguire il debug di un runbook. Quando si esegue il test di un runbook, i messaggi dettagliati non vengono visualizzati anche se il runbook è configurato per registrare i record dettagliati.

Il cmdlet [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) non è valido in un runbook, poiché questo deve essere utilizzato con un utente interattivo.

## <a name="preference-variables"></a>Variabili di preferenza
Windows PowerShell utilizza [le variabili di preferenza](http://technet.microsoft.com/library/hh847796.aspx) per stabilire come rispondere ai dati inviati ai diversi flussi di output. È possibile impostare queste variabili in un runbook per controllare la modalità di risposta ai dati inviati ai diversi flussi.

La tabella seguente elenca le variabili di preferenza che possono essere utilizzate nei runbook con i relativi valori validi e predefiniti. Si noti che questa tabella include solo i valori validi in un runbook. Gli altri valori sono validi per le variabili di preferenza, se utilizzate in Windows PowerShell al di fuori di automazione di Azure.

| Variabile | Default Value | Valori validi |
|:--- |:--- |:--- |
| WarningPreference |Continue |Arresto<br>Continue<br>SilentlyContinue |
| ErrorActionPreference |Continue |Arresto<br>Continue<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Arresto<br>Continue<br>SilentlyContinue |

Nella tabella seguente è elencato il comportamento dei valori delle variabili di preferenza valide nei runbook.

| Valore | Comportamento |
|:--- |:--- |
| Continue |Registra il messaggio e continua l'esecuzione del runbook. |
| SilentlyContinue |Continua l'esecuzione del runbook senza registrazione del messaggio. Questo ha l'effetto di ignorare il messaggio. |
| Stop |Registra il messaggio e sospende il runbook. |

## <a name="retrieving-runbook-output-and-messages"></a>Recupero di messaggi e output di runbook
### <a name="azure-portal"></a>Portale di Azure
È possibile visualizzare i dettagli di un processo di un runbook nel portale di Azure dalla scheda processi di un runbook. Il riepilogo del processo visualizzerà i parametri di input e [flusso di Output](#Output) oltre alle informazioni generali sul processo e tutte le eccezioni se si sono verificate. La cronologia include i messaggi dal [flusso di Output](#Output) e [Warning and Error Streams](#WarningError) oltre al [flusso dettagliato](#Verbose) e [recorddistato](#Progress) se il runbook è configurato per log dettagliati e record di stato.

### <a name="windows-powershell"></a>Windows PowerShell
In Windows PowerShell, è possibile recuperare i messaggi e gli output da un runbook tramite il cmdlet [Get-AzureAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) . Questo cmdlet richiede l'ID del processo e dispone di un parametro denominato flusso in cui è possibile specificare quale flusso restituire. È possibile specificare Any per restituire tutti i flussi per il processo.

Nell'esempio seguente viene avviato un runbook figlio e si attende il suo completamento. Una volta completato, il relativo output viene raccolto dal processo.

    $job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

    $doLoop = $true
    While ($doLoop) {
       $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
       –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
       $status = $job.Status
       $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
    }

    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output
    
    # For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
    Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
    

### <a name="graphical-authoring"></a>Creazione grafica
Per i runbook grafici, è disponibile una registrazione aggiuntiva sotto forma di traccia a livello di attività.  Sono disponibili due livelli di traccia: base e dettagliato.  Nella traccia di base sono indicate l'ora di inizio e di fine di ogni attività del runbook, oltre a informazioni relative a eventuali ulteriori tentativi di esecuzione dell'attività, ad esempio il numero di tentativi e l'ora di inizio dell'attività.  Nella traccia dettagliata sono indicate le informazioni della traccia di base e i dati di input e output di ogni attività.  Si noti che attualmente i record di traccia vengono scritti usando il flusso dettagliato. Per questa ragione è necessario abilitare la registrazione dettagliata quando si abilita la traccia.  Per i runbook grafici con la traccia abilitata non è necessario registrare record di stato perché la traccia di base svolge la stessa funzione e offre maggiori informazioni.

![Visualizzazione dei flussi dei processi di creazione grafica](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

È possibile osservare nella schermata precedente che quando si abilitano la registrazione e la traccia dettagliata per i runbook grafici sono disponibili molte più informazioni nella visualizzazione di flussi dei processi di produzione.  Queste informazioni aggiuntive possono essere essenziali per la risoluzione dei problemi di produzione di un runbook e pertanto è consigliabile attivarle solo a tale scopo e non come regola generale. I record di traccia possono essere particolarmente numerosi.  Con l'analisi grafica runbook, è possibile ottenere da due a quattro record per ogni attività a seconda che sia stato configurato l'analisi di base o dettagliato.  Se non sono necessarie queste informazioni per tenere traccia dell'avanzamento di un runbook per la risoluzione dei problemi, è consigliabile non abilitare la traccia.

**Per abilitare la traccia a livello di attività, seguire questa procedura.**

1. Nel portale di Azure aprire l'account di automazione.
2. Fare clic sul riquadro **Runbook** per aprire l'elenco dei runbook.
3. Nel pannello Runbook fare clic per selezionare un runbook grafico dall'elenco dei runbook.
4. Nel pannello Impostazioni del runbook selezionato, fare clic su **Registrazione e traccia**.
5. La registrazione e traccia pannello, Registra record dettagliati, fare clic su **su** per abilitare la registrazione dettagliata e in analisi a livello di attività, modificare il livello di traccia per **base** o **Detailed** in base al livello di traccia è necessario.<br>
   
   ![Pannello Registrazione e traccia della creazione grafica](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-operations-management-suite-oms-log-analytics"></a>Log Analytics di Microsoft Operations Management Suite (OMS)
Automazione può inviare lo stato e i flussi del processo del runbook all'area di lavoro Log Analytics di Microsoft Operations Management Suite (OMS).  Con Log Analytics è possibile:

* Ottenere informazioni dettagliate sui processi di Automazione. 
* Attivare un messaggio di posta elettronica o un avviso in base allo stato del processo del runbook, ad esempio non riuscito o sospeso. 
* Scrivere query avanzate nei flussi del processo. 
* Correlare i processi tra account di Automazione. 
* Visualizzare la cronologia dei processi nel tempo.    

Per altre informazioni sulla configurazione dell'integrazione con Log Analytics per raccogliere e correlare i dati del processo e agire su tali dati, vedere [Inoltrare lo stato e i flussi del processo da Automazione a Log Analytics (OMS)](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Passaggi successivi
* Per maggiori informazioni sull'esecuzione dei runbook, su come monitorare i processi dei runbook e su altri dettagli tecnici, vedere come tenere traccia del processo di un runbook in [Esecuzione di runbook in Automazione di Azure](automation-runbook-execution.md)
* Per informazioni come progettare e usare i runbook figlio, vedere [Runbook figlio in Automazione di Azure](automation-child-runbooks.md)

