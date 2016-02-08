<properties 
   pageTitle="Output di runbook e messaggi di automazione di Azure | Microsoft Azure"
   description="Viene descritto come creare e recuperare l'output e i messaggi di errore da runbook in automazione di Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/27/2016"
   ms.author="bwren" />

# Output di runbook e messaggi in automazione di Azure

La maggior parte dei runbook di automazione Azure avrà qualche forma di output, ad esempio un messaggio di errore all'utente o un oggetto complesso destinato a essere utilizzato da un altro flusso di lavoro. Windows PowerShell fornisce [più flussi](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) per inviare l'output da uno script o flusso di lavoro. Automazione di Azure funziona con ciascuno di questi flussi in modo diverso ed è necessario seguire le procedure consigliate per utilizzarli quando si crea un runbook.

La tabella seguente fornisce una breve descrizione di ciascuno dei flussi e il relativo comportamento nel portale di gestione di Azure durante l'esecuzione di un runbook pubblicato e quando si esegue il [test di un runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx). Nelle sezioni successive vengono forniti ulteriori dettagli su ogni flusso.

| Stream | Descrizione | Pubblicato | Test|
|:---|:---|:---|:---|
|Output|Gli oggetti devono essere utilizzati da altri runbook.|Scrivere la cronologia dei processi.|Visualizzata nel pannello Output del Test.|
|Avviso|Messaggio di avviso destinato all'utente.|Scrivere la cronologia dei processi.|Visualizzata nel pannello Output del Test.|
|Errore|Messaggio di errore previsto per l'utente. A differenza di un'eccezione, il runbook continua dopo un messaggio di errore per impostazione predefinita.|Scrivere la cronologia dei processi.|Visualizzata nel pannello Output del Test.|
|Dettagliato|Messaggi che forniscono informazioni generali o di debug.|Scritti nella cronologia del processo solo se è attivata la registrazione dettagliata per il runbook.|Viene visualizzata nel pannello Output del Test solo se $VerbosePreference è impostata su Continue nel runbook.|
|Avanzamento|Record generati automaticamente prima e dopo ogni attività del runbook. Il runbook non dovrebbe tentare di creare i propri record di stato di avanzamento poiché sono destinati ad un utente interattivo.|Scritti nella cronologia processo solo se lo stato di avanzamento della registrazione è attivata per il runbook.|Non sono visualizzati nel pannello Output del Test.|
|Debug|Messaggi destinati a un utente interattivo. Non devono essere utilizzati nei runbook.|Non scritti nella cronologia del processo.|Non scritti nel pannello Output del Test.|

## Flusso di output

Il flusso di Output è destinato agli output degli oggetti creati da uno script o da un flusso di lavoro quando vengono eseguiti correttamente. In automazione di Azure, il flusso viene utilizzato principalmente per gli oggetti che devono essere utilizzati da [runbook padre che chiamano il runbook corrente](automation-child-runbooks.md). Quando si [chiama l’inline di un runbook](automation-child-runbooks.md/#InlineExecution) da un runbook padre, vengono restituiti i dati dal flusso di output per l'elemento padre. Utilizzare solo il flusso di output per comunicare informazioni generali all'utente se si è a conosce del fatto che il runbook non verrà mai chiamato da un altro runbook. Come procedura consigliata, tuttavia, è consigliabile in genere utilizzare il [Flusso dettagliato](#Verbose) per comunicare informazioni generali all'utente.

È possibile scrivere i dati per il flusso di output tramite [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) o inserendo l'oggetto nella relativa riga nel runbook.

	#The following lines both write an object to the output stream.
	Write-Output –InputObject $object
	$object

### Output da una funzione

Quando si scrive nel flusso di output in una funzione inclusa nel runbook, l'output viene passato nuovamente al runbook. Se il runbook assegna l'output a una variabile, allora non è scritto nel flusso di output. La scrittura in eventuali altri flussi da all'interno della funzione scriverà nel flusso corrispondente per il runbook.

Si consideri il seguente esempio di runbook.

	Workflow Test-Runbook
	{
	   Write-Verbose "Verbose outside of function"
	   Write-Output "Output outside of function"
	   $functionOutput = Test-Function
	
	   Function Test-Function
	   {
	      Write-Verbose "Verbose inside of function"
	      Write-Output "Output inside of function"
	   }
	}

Il flusso di output per il processo di runbook sarebbe:

	Output outside of function

Il flusso dettagliato per il processo di runbook sarebbe:

	Verbose outside of function
	Verbose inside of function

### Dichiarazione del tipo di dati di output

Un flusso di lavoro può specificare il tipo di dati del relativo output utilizzando l’[attributo OutputType](http://technet.microsoft.com/library/hh847785.aspx). Questo attributo non ha alcun effetto in fase di esecuzione, ma offre un'indicazione all'autore del runbook in fase di progettazione dell’output previsto del runbook. Man mano che il set di strumenti per i runbook continuano ad evolvere, l'importanza della dichiarazione dei tipi di dati di output in fase di progettazione aumenterà. Di conseguenza, è consigliabile includere questa dichiarazione in qualsiasi runbook creato.

Il runbook di esempio seguente restituisce un oggetto string e include una dichiarazione del tipo di output. Se il runbook restituisce una matrice di un determinato tipo, è comunque necessario specificare il tipo invece di una matrice del tipo.

	Workflow Test-Runbook
	{
	   [OutputType([string])]
	
	   $output = "This is some string output."
	   Write-Output $output
	}

## Flussi di messaggi

A differenza del flusso di output, i flussi di messaggi sono utili per comunicare informazioni all'utente. Sono presenti più flussi di messaggi per diversi tipi di informazioni e ciascuno viene gestito diversamente dall'automazione di Azure.

### Flussi di avviso ed errore

I flussi di errore e di avviso sono utili per registrare i problemi che si verificano in un runbook. Quando un runbook viene eseguito essi vengono scritti nella cronologia del processo e vengono inclusi nel pannello Output del Test nel portale di gestione di Azure quando viene testato un runbook. Per impostazione predefinita, il runbook continuerà l'esecuzione dopo un avviso o un errore. È possibile specificare che il runbook deve essere sospeso su un avviso o un errore impostando una [variabile di preferenza](#PreferenceVariables) nel runbook prima di creare il messaggio. Ad esempio, per far sì che un runbook venga sospeso in caso di errore come farebbe un'eccezione, impostare **$ErrorActionPreference** su Stop.

Creare un avviso o un messaggio di errore utilizzando il cmdlet [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) o [Write-Error](http://technet.microsoft.com/library/hh849962.aspx). Anche le attività possono scrivere tali flussi.

	#The following lines create a warning message and then an error message that will suspend the runbook.
	
	$ErrorActionPreference = "Stop"
	Write-Warning –Message "This is a warning message."
	Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."

### Flusso dettagliato

Il flusso del messaggio dettagliato è destinato a informazioni generali sull'operazione del runbook. Poiché il [Flusso di Debug ](#Debug) non è disponibile in un runbook, i messaggi dettagliati devono essere utilizzati per le informazioni di debug. Per impostazione predefinita, i messaggi dettagliati da runbook pubblicati non vengono archiviati nella cronologia del processo. Per archiviare i messaggi dettagliati, configurare i runbook pubblicati su Log Verbose Records nella scheda Configura del runbook nel portale di gestione di Azure. Nella maggior parte dei casi, è consigliabile mantenere l'impostazione predefinita che non registra record dettagliati per un runbook per motivi di prestazioni. Attivare questa opzione solo per risolvere i problemi o eseguire il debug di un runbook.

Quando si esegue il[test di un runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx), i messaggi dettagliati non vengono visualizzati anche se il runbook è configurato per registrare i record dettagliati. Per visualizzare i messaggi dettagliati durante il [test di un runbook](http://msdn.microsoft.com/library/azure/dn879147.aspx), è necessario impostare la variabile $VerbosePreference su Continue. Con tale set di variabili, i messaggi dettagliati verranno visualizzati nel pannello Output del Test del portale di gestione di Azure.

Creare un messaggio dettagliato utilizzando il cmdlet [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx).

	#The following line creates a verbose message.
	
	Write-Verbose –Message "This is a verbose message."

### Flusso di debug

Il flusso di Debug deve essere utilizzato con un utente interattivo e non deve essere utilizzato nei runbook.

## Record di stato

Se si configura un runbook per registrare i record di stato di avanzamento (nella scheda Configura del runbook nel portale di gestione di Azure), verrà scritto un record per la cronologia dei processi prima e dopo l'esecuzione di ogni attività. Nella maggior parte dei casi, è consigliabile mantenere l'impostazione predefinita che non registra record dettagliati per un runbook per massimizzare le prestazioni. Attivare questa opzione solo per risolvere i problemi o eseguire il debug di un runbook. Quando si esegue il test di un runbook, i messaggi dettagliati non vengono visualizzati anche se il runbook è configurato per registrare i record dettagliati.

Il cmdlet [Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) non è valido in un runbook, poiché questo deve essere utilizzato con un utente interattivo.

## Variabili di preferenza

Windows PowerShell utilizza [le variabili di preferenza](http://technet.microsoft.com/library/hh847796.aspx) per stabilire come rispondere ai dati inviati ai diversi flussi di output. È possibile impostare queste variabili in un runbook per controllare la modalità di risposta ai dati inviati in diversi flussi.

La tabella seguente elenca le variabili di preferenza che possono essere utilizzate nei runbook con i relativi valori validi e predefiniti. Si noti che questa tabella include solo i valori validi in un runbook. Gli altri valori sono validi per le variabili di preferenza, se utilizzate in Windows PowerShell al di fuori di automazione di Azure.

| Variabile| Valore predefinito| Valori validi|
|:---|:---|:---|
|WarningPreference|Continue|Stop<br>Continue<br>SilentlyContinue|
|ErrorActionPreference|Continue|Stop<br>Continue<br>SilentlyContinue|
|VerbosePreference|SilentlyContinue|Stop<br>Continue<br>SilentlyContinue|

Nella tabella seguente è elencato il comportamento dei valori delle variabili di preferenza valide nei runbook.

| Valore| Comportamento|
|:---|:---|
|Continue|Registra il messaggio e continua l'esecuzione del runbook.|
|SilentlyContinue|Continua l'esecuzione del runbook senza registrazione del messaggio. Questo ha l'effetto di ignorare il messaggio.|
|Arresto|Registra il messaggio e sospende il runbook.|

## Recupero di messaggi e output di runbook

### Portale di gestione di Azure

È possibile visualizzare i dettagli di un processo di un runbook nel portale di gestione di Azure dalla scheda processi di un runbook. Il riepilogo del processo visualizzerà i parametri di input e il [flusso di Output](#Output) oltre alle informazioni generali sul processo e le eccezioni se si sono verificate. La cronologia includerà i messaggi dal [flusso di Output](#Output) e dai [flussi di errore e avviso](#WarningError) oltre al [Flusso dettagliato](#Verbose) e al [Record di stato](#Progress) se il runbook è configurato per i registri dettagliati e i record di stato.

### Windows PowerShell

In Windows PowerShell, è possibile recuperare i messaggi e gli output da un runbook tramite il cmdlet[Get-AzureAutomationJobOutput](http://msdn.microsoft.com/library/dn690268.aspx). Questo cmdlet richiede l'ID del processo e dispone di un parametro denominato flusso in cui è possibile specificare quale flusso restituire. È possibile specificare Any per restituire tutti i flussi per il processo.

Nell'esempio seguente viene avviato un runbook figlio e si attende il suo completamento. Una volta completato, il relativo output viene raccolto dal processo.

	$job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" 
	
	$doLoop = $true
	While ($doLoop) {
	   $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
	   $status = $job.Status
	   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
	}
	
	Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## Articoli correlati

- [Tenere traccia di un processo del runbook](automation-runbook-execution.md)
- [Runbook figlio](http://msdn.microsoft.com/library/azure/dn857355.aspx)

<!---HONumber=AcomDC_0128_2016-->