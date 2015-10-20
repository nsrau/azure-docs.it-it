<properties
	pageTitle="Esercitazione - Introduzione alla libreria Azure Batch Apps per .NET"
	description="Informazioni sui concetti di base relativi a Azure Batch Apps e sullo sviluppo con un semplice scenario"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="07/21/2015"
	ms.author="yidingz"/>




# Introduzione alla libreria Azure Batch Apps per .NET
In questa esercitazione viene illustrato come eseguire carichi di lavoro di calcolo paralleli in Azure Batch tramite il servizio Batch Apps.

Batch Apps è una funzionalità di Azure Batch che offre una modalità incentrata sulle applicazioni di gestire ed eseguire carichi di lavoro di Batch. Usando Batch Apps SDK, è possibile creare pacchetti per abilitare per Batch un'applicazione e distribuirli nel proprio account o renderli disponibili ad altri utenti di Batch. Batch fornisce inoltre funzionalità di gestione dati, monitoraggio dei processi, diagnostica incorporata, registrazione e supporto per le dipendenze tra attività. È anche disponibile un portale di gestione in cui è possibile gestire processi, visualizzare log e scaricare output senza dover scrivere un proprio client.

Nello scenario di Batch Apps si scrive il codice usando Batch Apps Cloud SDK per partizionare i processi in attività parallele, descrivere le eventuali dipendenze tra le attività e specificare la modalità di esecuzione di ogni attività. Questo codice viene distribuito all'account Batch. I client possono quindi eseguire i processi semplicemente specificando il tipo di processo e i file di input per un'API REST.

>[AZURE.NOTE]Per completare l'esercitazione, è necessario un account Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/). È possibile usare NuGet per ottenere sia l'assembly <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">Batch Apps Cloud</a> sia l'assembly <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">Batch Apps Client</a>. Dopo aver creato il progetto in Visual Studio, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. È anche possibile scaricare l'estensione di Visual Studio per le app di Batch che include un modello di progetto per abilitare per il cloud le applicazioni e la possibilità di distribuire un'applicazione <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">qui</a> o effettuando la ricerca di **Batch Apps** in Visual Studio mediante la voce di menu Estensioni e aggiornamenti. È anche possibile trovare <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">esempi end-to-end su MSDN</a>.
>

###Dati fondamentali di Azure Batch Apps
Batch è progettato per funzionare con le applicazioni esistenti a elevato utilizzo di calcolo. Sfrutta il codice dell'applicazione esistente e viene eseguito in un ambiente generico dinamico e virtualizzato. Per abilitare il funzionamento di un'applicazione con Batch Apps è necessario effettuare alcune operazioni:

1.	Preparare un file zip contenente gli eseguibili dell'applicazione esistente, cioè gli stessi file che verrebbero eseguiti in una server farm o in un cluster tradizionale ed eventuali file di supporto necessari. Questo file zip viene quindi caricato nell'account di Batch tramite il portale di gestione o l'API REST.
2.	Creare un file zip degli "assembly cloud" che inviano i carichi di lavoro all'applicazione e caricarlo tramite il portale di gestione o l'API REST. Un assembly cloud contiene due componenti che sono basati sull'SDK cloud:
	1.	Componente di suddivisione dei processi: suddivide il processo in attività che possono essere elaborate in modo indipendente. Ad esempio, in uno scenario di animazione tale componente dividerebbe un processo relativo a un filmato in singoli fotogrammi.
	2.	Elaboratore delle attività: invoca l'eseguibile dell'applicazione per una determinata attività. Ad esempio, in uno scenario di animazione, l'elaboratore delle attività invocherebbe un programma di rendering per eseguire il rendering del singolo frame specificato dall'attività in questione.
3.	Specificare una modalità di invio dei processi per l'applicazione abilitata in Azure. Potrebbe trattarsi di un plug-in nell'interfaccia utente dell'applicazione, di un portale Web o perfino di un servizio automatico nell'ambito della pipeline di esecuzione. Vedere gli <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">esempi</a> su MSDN.



###Concetti chiave di Batch Apps
Il modello di programmazione e utilizzo delle app di Batch è incentrato sui seguenti concetti chiave:

####Processi
Un **processo** è un elemento di lavoro inviato dall'utente. Quando viene inviato un processo, l'utente specifica il tipo di processo, le relative impostazioni e i dati necessari per il processo. L'implementazione abilitata può fornire questi dettagli per conto dell'utente o, in alcuni casi, l'utente può fornire queste informazioni in modo esplicito tramite il client. Un processo restituisce risultati. Ogni processo dispone di un output primario e, facoltativamente, un output di anteprima. I processi possono inoltre restituire output aggiuntivi, se lo si desidera.

####Attività
Un'**attività** è un elemento di lavoro da eseguire come parte di un processo. Un processo inviato da un utente è suddiviso in attività più piccole. Il servizio elabora quindi le singole attività, quindi assembla i risultati dell'attività in un output del processo complessivo. La natura delle attività dipende dal tipo di processo. Il componente di suddivisione dei processi definisce la modalità di suddivisione di un processo in attività, sulla base della conoscenza dei blocchi di lavoro che l'applicazione è progettata per elaborare. È inoltre possibile scaricare i singoli output di attività e questa funzionalità potrebbe essere utile in alcuni casi, ad esempio quando un utente vuole scaricare singole attività da un processo di animazione.

####Attività di unione
Un'**attività di unione** è un tipo speciale di attività che assembla i risultati delle singole attività nei risultati finali del processo. Per un processo di rendering di un filmato, l'attività di unione potrebbe assemblare i fotogrammi sottoposti a rendering in un filmato o comprimere tutti i fotogrammi sottoposti a rendering in un unico file. Ogni processo presenta un'attività di unione anche nel caso in cui non sia necessaria alcuna unione effettiva.

####File
Un **file** è costituito da dati usati come input in un processo. Un processo può presentare uno o più file di input associati o esserne privo. Lo stesso file può anche essere usato in più processi, ad esempio per un processo di rendering di un filmato i file possono essere trame, modelli e così via. Per un processo di analisi dei dati, i file potrebbero essere costituiti da un insieme di osservazioni o misurazioni.

###Abilitazione dell'applicazione cloud
L'applicazione deve contenere un campo statico o una proprietà che contiene tutti i dettagli a essa relativi. Specifica il nome dell'applicazione e il tipo o i tipi di processo gestiti dall'applicazione. Queste informazioni vengono fornite quando si usa il modello nell'SDK che può essere scaricato tramite Visual Studio Gallery.

Di seguito è riportato un esempio di dichiarazione dell'applicazione cloud per un carico di lavoro parallelo:

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####Implementazione del componente di suddivisione dei processi e dell'elaboratore delle attività
Per i carichi di lavoro "imbarazzantemente paralleli", è necessario implementare un componente di suddivisione dei processi e un elaboratore delle attività.

####Implementazione di JobSplitter.SplitIntoTasks
L'implementazione di SplitIntoTasks deve restituire una sequenza di attività, ciascuna delle quali rappresenta un elemento di lavoro separato che deve essere accodato per l'elaborazione da parte di un nodo di calcolo. Ogni attività deve essere indipendente e configurata con tutte le informazioni necessarie per l'elaboratore delle attività.

Le attività specificate dal componente di suddivisione dei processi vengono rappresentate come oggetti TaskSpecifier. TaskSpecifier presenta numerose proprietà che possono essere impostate prima di restituire l'attività.


-	TaskIndex viene ignorato, ma è disponibile per gli elaboratori delle attività. È possibile usarlo per passare un indice all'elaboratore delle attività. Se non è necessario passare un indice, non occorre impostare questa proprietà.
-	Parameters è una raccolta vuota per impostazione predefinita. È possibile aggiungerla o sostituirla con una nuova raccolta. È possibile copiare le voci dalla raccolta di parametri di processo usando il metodo WithJobParameters o WithAllJobParameters.  


RequiredFiles è una raccolta vuota per impostazione predefinita. È possibile aggiungerla o sostituirla con una nuova raccolta. È possibile copiare le voci dalla raccolta di file di processo usando il metodo RequiringJobFiles o RequiringAllJobFiles.

È possibile specificare un'attività che dipende da un'altra attività specifica. A tale scopo, impostare la proprietà TaskSpecifier.DependsOn, passando l'ID dell'attività da cui dipende:

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

L'attività non verrà eseguita fino a quando non è disponibile l'output dell'attività da cui dipende.

È inoltre possibile specificare che un intero gruppo di attività non avvii l'elaborazione fino al completamento di un altro gruppo. In questo caso è possibile specificare la proprietà TaskSpecifier.Stage. Le attività con un determinato valore di fase non avvieranno l'elaborazione fino al completamento di tutte le attività con i valori di fase più bassi; ad esempio, le attività con fase 3 non avvieranno l'elaborazione fino al completamento di tutte le attività con fase 0, 1 o 2. Le fasi devono iniziare da 0, la sequenza di fasi non deve avere interruzioni e SplitIntoTasks deve restituire le attività in ordine di fase: ad esempio, un'attività con fase 0 non deve essere restituita dopo un'attività con fase 1.

Ogni processo parallelo termina con un'attività speciale denominata attività di unione. Il processo dell'attività di unione comporta l'assemblaggio dei risultati delle attività di elaborazione parallela in un risultato finale. Batch Apps crea automaticamente l'attività di unione.

In rari casi potrebbe essere necessario controllare in modo esplicito l'attività di unione, ad esempio per personalizzarne i parametri. In questo caso è possibile specificare l'attività di unione restituendo un TaskSpecifier con la proprietà IsMerge impostata su true. Questa impostazione eseguirà l'override dell'attività di unione automatica. Se si crea un'attività di unione esplicita:

-	È possibile creare solo un'attività di unione esplicita
-	Deve essere l'ultima attività nella sequenza
-	Deve avere IsMerge impostato su true e ogni altra attività deve avere IsMerge impostato su false  


Tenere presente che in genere non è necessario creare l'attività di unione in modo esplicito.

Il codice seguente illustra un'implementazione semplice di SplitIntoTasks.

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####Implementazione di ParallelTaskProcessor.RunExternalTaskProcess

RunExternalTaskProcess viene chiamato per ogni attività non di unione restituita dal componente di suddivisione dei processi. Dovrebbe richiamare l'applicazione con gli argomenti appropriati e restituire una raccolta di output da conservare per un uso successivo.

Il frammento seguente illustra come chiamare un programma denominato application.exe. Si noti che è possibile usare il metodo helper ExecutablePath per creare percorsi di file assoluti.

La classe ExternalProcess nell'SDK cloud fornisce la logia di helper per eseguire gli eseguibili dell'applicazione. ExternalProcess può gestire l'annullamento, la conversione dei codici di uscita in eccezioni, l'acquisizione di standard e la configurazione delle variabili di ambiente. Se lo si preferisce, è anche possibile usare direttamente la classe Process .NET per eseguire i programmi.

Il metodo RunExternalTaskProcess restituisce un TaskProcessResult, che include un elenco di file di output. È necessario includere almeno tutti i file necessari per l'unione; facoltativamente, è anche possibile restituire i file di log, i file di anteprima e i file intermedi (ad esempio, per scopi diagnostici in caso di errore dell'attività). Si noti che il metodo restituisce i percorsi, non il contenuto dei file.

Ciascun file deve essere identificato con il tipo di output in esso contenuto: output, ovvero parte dell'output del processo finale, preview, log o intermediate. Questi valori provengono dall'enumerazione TaskOutputFileKind. Il frammento seguente non restituisce anteprime o log, ma solo l'output di una singola attività. Il metodo TaskProcessResult.FromExternalProcessResult semplifica lo scenario comune di acquisizione del codice di uscita, l'output del processore e i file di output da un programma della riga di comando:

Il codice seguente fornisce una dimostrazione di un'implementazione semplice di ParallelTaskProcessor.RunExternalTaskProcess.

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####Implementazione di ParallelTaskProcessor.RunExternalMergeProcess

Viene chiamata per l'attività di unione. Deve richiamare l'applicazione per unire gli output delle precedenti attività, nel modo appropriato per l'applicazione, e restituire l'output combinato.

L'implementazione di RunExternalMergeProcess è molto simile a RunExternalTaskProcess, con la differenza che:

-	RunExternalMergeProcess utilizza gli output delle attività precedenti anziché i file di input dell'utente. È pertanto consigliabile gestire i nomi dei file che si desidera elaborare in base all'ID attività anziché dalla raccolta Task.RequiredFiles.
-	RunExternalMergeProcess restituisce un file JobOutput e, facoltativamente, un file JobPreview.


Il codice seguente fornisce una dimostrazione di un'implementazione semplice di ParallelTaskProcessor.RunExternalMergeProcess.

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###Invio dei processi a Batch Apps
Un processo descrive un carico di lavoro da eseguire e deve includere tutte le informazioni sul carico di lavoro ad eccezione del contenuto del file. Ad esempio, il processo contiene le impostazioni di configurazione inviate dal client alle attività tramite il componente di suddivisione dei processi. Gli esempi forniti su MSDN illustrano come inviare i processi e fornire più client, inclusi un portale Web e un client della riga di comando.

<!---HONumber=Oct15_HO3-->