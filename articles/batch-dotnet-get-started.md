<properties 
	pageTitle="Esercitazione - Introduzione alla libreria di Azure Batch per .NET" 
	description="Informazioni sui concetti di base di Azure Batch e su come usare il servizio Batch con uno scenario semplice." 
	services="batch" 
	documentationCenter=".net" 
	authors="yidingzhou" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="batch" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-compute" 
	ms.date="12/03/2014" 
	ms.author="yidingz, kabatta"/>

#Introduzione alla libreria di Azure Batch per .NET  

Questo articolo contiene le due esercitazioni seguenti che consentono di iniziare a sviluppare con la libreria .NET per il servizio Azure Batch.  

-	[Esercitazione 1: Libreria di Azure Batch per .NET](#tutorial1)
-	[Esercitazione 2: Libreria di Azure Batch Apps per .NET](#tutorial2)  


Per informazioni generali e scenari per Azure Batch, vedere [Panoramica tecnica di Azure Batch](http://azure.microsoft.com/documentation/articles/batch-technical-overview/).

##<a name="tutorial1"></a>Esercitazione 1: Libreria di Azure Batch per .NET
  	
In questa esercitazione verrà illustrato come creare un'applicazione console che consente di impostare il calcolo distribuito in un pool di macchine virtuali usando il servizio Azure Batch. Le attività create in questa esercitazione prevedono la valutazione del testo dei file presenti in Archiviazione di Azure e quindi la restituzione delle parole usate con maggiore frequente. Gli esempi sono in codice C# e usano la libreria di Azure Batch per .NET.


>[WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/). 
>
>È necessario usare NuGet per ottenere l'assembly **Microsoft.Azure.Batch.dll**. Dopo aver creato il progetto in Visual Studio, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. Cercare online **Azure.Batch** e fare clic su Installa per installare il pacchetto Azure Batch e le relative dipendenze.
>
>Assicurarsi che la versione di Gestione pacchetti NuGet sia 2.8 o successiva. È possibile trovare il numero di versione in Visual Studio -> "?" -> finestra di dialogo "Informazioni su Microsoft Visual Studio". Se si dispone di una versione precedente di Gestione pacchetti NuGet, è necessario aggiornare Visual Studio, altrimenti potrebbero verificarsi problemi con il download della versione corretta delle dipendenze di NuGet.
> 
>Inoltre, è possibile fare riferimento all'[esempio Hello World di Azure Batch](https://code.msdn.microsoft.com/Azure-Batch-Sample-Hello-6573967c) su MSDN per un esempio simile al codice esaminato in questo articolo.



###Concetti
Il servizio Batch consente di pianificare attività di calcolo scalabili e distribuite. In questo modo è possibile eseguire scalare carichi di lavoro distribuiti su più macchine virtuali. Questi carichi di lavoro supportano in modo efficiente attività di calcolo con utilizzo intensivo di risorse. Quando si usa il servizio Batch, è possibile sfruttare le risorse seguenti:  

-	**Account**: un'entità identificata in modo univoco all'interno del servizio. Tutte le operazioni di elaborazione avvengono tramite un account di Batch.
-	**Pool**: una raccolta di macchine virtuali delle attività sulle quali vengono eseguite le applicazioni.
-	**Macchina virtuale delle attività**: un computer assegnato a un pool e usato dalle attività assegnate ai processi in esecuzione nel pool.
-	**Utente di macchina virtuale delle attività**: un account utente su una macchina virtuale delle attività.
-	**Elemento di lavoro**: specifica la modalità di esecuzione di calcoli nelle macchine virtuali delle attività in un pool.
-	**Processo**: un'istanza in esecuzione di un elemento di lavoro costituito da una raccolta di attività.
-	**Attività**: un'applicazione associata a un processo ed eseguita su una macchina virtuale delle attività.
-	**File**: contiene le informazioni elaborate da un'attività.  

Verranno ora esaminati i casi di utilizzo più comuni.

###Creare un account Azure Batch
Per creare un account Batch è possibile usare il portale di gestione di Azure. Dopo la creazione dell'account, all'utente viene fornita una chiave. Per altre informazioni, vedere [Panoramica tecnica di Azure Batch](http://azure.microsoft.com/documentation/articles/batch-technical-overview/).  

###Procedura: Aggiungere un pool a un account
Un pool di macchine virtuali di attività è il primo set di risorse che è necessario creare per eseguire attività.  

1.	Aprire Microsoft Visual Studio 2013, scegliere **Nuovo** dal menu **File**, quindi fare clic su **Progetto**.

2.	Da **Windows**, in **Visual C#**, fare clic su **Applicazione console**, assegnare al progetto il nome **GettingStarted**, assegnare alla soluzione il nome **AzureBatch**, quindi fare clic su **OK**.

3.	Aggiungere le seguenti dichiarazioni di spazi dei nomi all'inizio del file Program.cs:

		using Microsoft.Azure.Batch;
		using Microsoft.Azure.Batch.Auth;
		using Microsoft.Azure.Batch.Common;

	Assicurarsi di fare riferimento all'assembly Microsoft.WindowsAzure.Batch.dll.

4.	Aggiungere le seguenti variabili alla classe Program:

		private const string PoolName = "[name-of-pool]";
		private const int NumOfMachines = 3;
		private const string AccountName = "[name-of-batch-account]";
		private const string AccountKey = "[key-of-batch-account]";
		private const string Uri = "https://batch.core.windows.net";
	Sostituire i seguenti valori:
	-	**[name-of-pool]**: il nome da usare per il pool.
	-	**[name-of-batch-account]**: il nome dell'account Batch.
	-	**[key-of-batch-account]**: la chiave fornita per l'account Batch.
5.	Aggiungere al Main il codice seguente che definisce le credenziali da usare:

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
6.	Aggiungere al Main il codice seguente per creare il client usato per eseguire operazioni:

		IBatchClient client = BatchClient.Connect(Uri, cred);
7.	Aggiungere al Main il codice seguente che crea un pool nel caso in cui non esista:

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   IEnumerable<ICloudPool> pools = pm.ListPools();
		   if (!pools.Select(pool => pool.Name).Contains(PoolName))
		   {
		      Console.WriteLine("The pool does not exist, creating now...");
		      ICloudPool newPool = pm.CreatePool(
		         PoolName, 
		         osFamily: "3", 
		         vmSize: "small", 
		         targetDedicated: NumOfMachines);
		       newPool.Commit();                  
		    }
		}
		Console.WriteLine("Created pool {0}", PoolName);
		Console.ReadLine();
8.	Salvare ed eseguire il programma. Se un pool è stato aggiunto correttamente, il relativo stato è **Attivo**.  

###Procedura: Elencare i pool in un account
Se non si conosce il nome di un pool esistente, è possibile ottenere un elenco dei pool in un account.  

1.	Aggiungere al Main il codice seguente che definisce le credenziali da usare:  

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
2.	Aggiungere al Main il codice seguente per creare il client usato per eseguire operazioni:

		IBatchClient client = BatchClient.Connect(Uri, cred);

3.	Aggiornare la procedura Main in base al codice seguente che scrive i nomi e gli stati di tutti i pool dell'account e per creare il pool nel caso in cui non esista:

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
		IBatchClient client = BatchClient.Connect(Uri, cred);
		
		using (IPoolManager pm = client.OpenPoolManager())
		{
		    IEnumerable<ICloudPool> pools = pm.ListPools();
		
		    Console.WriteLine("Listing Pools\n=================");
		    foreach (var p in pools)
		    {
		        Console.WriteLine("Pool: " + p.Name + " State:" + p.State);
		    }  
		
		    if (!pools.Select(pool => pool.Name).Contains(PoolName))
		    {
		        Console.WriteLine("The pool does not exist, creating now...");
		        ICloudPool newPool = pm.CreatePool(
		           PoolName,
		           osFamily: "3",
		           vmSize: "small",
		           targetDedicated: NumOfMachines);
		        newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}. Press <Enter> to continue.", PoolName);
		Console.ReadLine();

4.	Salvare ed eseguire il programma. Viene visualizzato l'output seguente.

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

###Procedura: Elencare tutti gli elementi di lavoro in un account
Se non si conosce il nome di un pool elemento di lavoro esistente, è possibile ottenere un elenco dei pool in un account.  

1.	Aggiungere il codice seguente alla fine del Main che scrive i nomi e gli stati di tutti gli elementi di lavoro nell'account:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   Console.WriteLine("Listing Workitems\n=================");
		   IEnumerable<ICloudWorkItem> workitems = wm.ListWorkItems();
		   foreach (var w in workitems)
		   {
		      Console.WriteLine("Workitem: " + w.Name + " State:" + w.State);
		   }   
		}
		Console.WriteLine("Press <Enter> to continue."); 
		Console.ReadLine();
7.	Salvare ed eseguire il programma. Probabilmente non verrà visualizzato nulla perché non è stato ancora inviato alcun elemento di lavoro. Nella sezione successiva verranno fornite informazioni sull'aggiunta di elementi di lavoro.  

##Procedura: Aggiungere un elemento di lavoro a un account
È necessario creare un elemento di lavoro per definire la modalità di esecuzione delle attività nel pool.  

1.	Aggiungere le seguenti variabili alla classe Program:

		private static readonly string WorkItemName = Environment.GetEnvironmentVariable("USERNAME") + DateTime.Now.ToString("yyyyMMdd-HHmmss");

2.	Quando si crea un elemento di lavoro, viene creato anche un processo. È possibile assegnare un nome all'elemento di lavoro, ma al processo viene sempre assegnato il nome **job-0000000001**. Aggiungere a Main il codice seguente (prima del codice degli elementi di lavoro dell'elenco) che aggiunge l'elemento di lavoro:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   ICloudWorkItem cloudWorkItem = wm.CreateWorkItem(WorkItemName);
           cloudWorkItem.JobExecutionEnvironment = new JobExecutionEnvironment() {PoolName = PoolName};
		   cloudWorkItem.Commit();
		}
		Console.WriteLine("Workitem successfully added. Press <Enter> to continue.");
		Console.ReadLine();
3.	Salvare ed eseguire il programma. Se un elemento di lavoro è stato aggiunto correttamente, il relativo stato è **Attivo**. Viene visualizzato l'output seguente.

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.
		
		Workitem successfully added. Press <Enter> to continue.
		
		Listing Workitems
		=================
		Workitem: yidingz20141106-111211 State:Active
		Press <Enter> to continue.

###Procedura: Aggiungere attività a un processo
Un elemento di lavoro senza attività non esegue alcuna operazione. Dopo aver creato l'elemento di lavoro e il processo, è possibile aggiungere attività al processo. Ora verrà aggiunta un'attività semplice al processo.  

1.	Aggiungere le seguenti variabili alla classe Program:

		private const int Count = 4; // number of tasks that will run
		private const string JobName = "job-0000000001";

2.	Aggiungere a Main il codice seguente che aggiunge attività a un processo, attende che vengano eseguite e genera i risultati:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		    string taskName;
		    ICloudJob job = wm.GetJob(WorkItemName, JobName);
		    for (int i = 1; i <= Count; ++i)
		    {
		        taskName = "taskdata" + i;
		        string commandLine = String.Format("cmd /c echo I am {0}", taskName);
		        ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		        job.AddTask(taskToAdd);
		        job.Commit();
		        job.Refresh();
		    }
		
		    ICloudJob listjob = wm.GetJob(WorkItemName, JobName);
		    client.OpenToolbox().CreateTaskStateMonitor().WaitAll(listjob.ListTasks(),
		       TaskState.Completed, new TimeSpan(0, 30, 0));
		    Console.WriteLine("The tasks completed successfully. Terminating the workitem...");
		    wm.GetWorkItem(WorkItemName).Terminate();
		    foreach (ICloudTask task in listjob.ListTasks())
		    {
		        Console.WriteLine("Task " + task.Name + " says:\n" + task.GetTaskFile(Constants.StandardOutFileName).ReadAsString());
		    }
		    Console.ReadLine();
		}

3.	Salvare ed eseguire il programma. Il risultato dovrebbe avere l'aspetto seguente:

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		I am taskdata1
		
		Task taskdata2 says:
		I am taskdata2
		
		Task taskdata3 says:
		I am taskdata3

###Creare un programma di elaborazione di attività
Ora che è possibile eseguire Hello World nella VM, si passerà ad alcuni esempi concreti. In questa sezione verrà creato un programma di elaborazione delle attività e tale programma verrà caricato nella macchina virtuale che esegue le attività.  

1.	In Esplora soluzioni creare un nuovo progetto di applicazione console denominato **ProcessTaskData** nella soluzione **AzureBatch**.

2.	Aggiungere le seguenti dichiarazioni di spazi dei nomi all'inizio del file Program.cs:

		using System.Net;

3.	Aggiungere al Main il codice riportato di seguito che verrà usato per elaborare i dati:

		string blobName = args[0];
		int numTopN = int.Parse(args[1]);
		
		WebClient myWebClient = new WebClient();
		string content = myWebClient.DownloadString(blobName);
		
		string[] words = content.Split(' ');
		var topNWords =
		   words.
		   Where(word => word.Length > 0).
		   GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
		   OrderByDescending(x => x.Value).
		   Take(numTopN).
		   ToList();
		
		foreach (var pair in topNWords)
		{
		    Console.WriteLine("{0} {1}", pair.Key, pair.Value);
		}

4.	Salvare e compilare il progetto.  

###Preparare le risorse per l'esecuzione di attività

Quando si crea un scenario di calcolo distribuito con il servizio Batch, usare il seguente flusso di lavoro di base:  

1.	Caricare i file da usare nello scenario di calcolo distribuito in un account di archiviazione di Azure. I file devono trovarsi nell'account di archiviazione in modo che il servizio Batch possa accedervi. I file vengono caricati in una macchina virtuale delle attività quando viene eseguita l'attività.
2.	Caricare i file binari dipendenti nell'account di archiviazione. I file binari includono il programma che viene eseguito dall'attività e gli assembly dipendenti. È necessario poter accedere a questi file dal servizio di archiviazione. È inoltre necessario che tali file siano caricati sulla macchina virtuale delle attività.
3.	Creare un pool di macchine virtuali delle attività. È possibile assegnare la dimensione della macchina virtuale delle attività da usare durante la creazione del pool. Quando si esegue un'attività, a essa viene assegnata una macchina virtuale del pool.
4.	Creare un elemento di lavoro. Un elemento di lavoro consente di gestire un processo di attività. Un processo viene automaticamente creato quando si crea un elemento di lavoro.
5.	Aggiungere attività al processo. Ogni attività usa il programma caricato per elaborare le informazioni da un file caricato.
6.	Monitorare i risultati dell'output.  

Sono stati illustrati i passaggi da 3 a 6. Ora verrà descritto come preparare Archiviazione di Azure per l'esecuzione dell'attività.

####Ottenimento di un account di archiviazione
Per completare il resto di questa esercitazione è necessario un account di archiviazione. Per conoscere la procedura di creazione di un account, vedere [Creare un account di archiviazione di Azure](#tutorial1_storage).

####Caricamento di dati

1. Creare un contenitore denominato "gettingstarted" nell'account di archiviazione di Azure. Questa operazione può essere eseguita nel portale di Azure. Assicurarsi di impostare il campo "ACCESSO" su "Contenitore pubblico".

2. Caricare "ProcessTaskData.exe" nel contenitore.

3. Creare tre file di testo (taskdata1, taskdata2, taskdata3.txt) ciascuno dei quali contenente uno dei paragrafi seguenti e caricarli nel contenitore:

		Quando sono necessarie risorse flessibili per soddisfare le esigenze aziendali, è possibile usare Macchine virtuali di Azure per eseguire il provisioning dell'infrastruttura di calcolo su richiesta e scalabile. Dalla raccolta è possibile creare macchine virtuali che eseguono Windows, Linux e applicazioni aziendali come SharePoint e SQL Server. In alternativa, è possibile acquisire e usare le proprie immagini per creare macchine virtuali personalizzate.
		
		Con Servizi cloud di Azure è possibile distribuire rapidamente e gestire potenti applicazioni e servizi. È sufficiente caricare l'applicazione e Azure gestirà i dettagli della distribuzione, dal provisioning al bilanciamento del carico, al monitoraggio dell'integrità per la disponibilità continua. L'applicazione è supportata da un contratto di servizio mensile del 99,95%, leader nel settore. L'utente può pertanto concentrarsi sull'applicazione anziché sull'infrastruttura.
		
		Siti Web di Azure fornisce un ambiente scalabile, affidabile e facile da usare per l'hosting delle applicazioni Web. È possibile scegliere tra una vasta gamma di framework e modelli per creare un sito Web in pochi secondi e anche usare qualsiasi strumento o sistema operativo per sviluppare un sito con .NET, PHP, Node.js o Python. È inoltre possibile scegliere tra numerose opzioni di controllo del codice sorgente, tra cui TFS, GitHub e BitBucket, per configurare l'integrazione continua ed eseguire attività di sviluppo in team. Le funzionalità del sito possono essere ampliate nel corso del tempo grazie ai servizi gestiti aggiuntivi di Azure come Archiviazione, CDN e Database SQL.


>[WACOM.NOTE] In un ambiente di produzione, si consiglia di usare una firma di accesso condiviso.


>[WACOM.NOTE] Il team dei servizi di archiviazione di Azure ha un [post di blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) in cui sono indicati gli strumenti di esplorazione dei servizi di archiviazione di Azure che facilitano il caricamento di file.



###Aggiornare il codice di invio di attività

1.	Aggiungere le seguenti variabili alla classe Program:

		private const string BlobPath = "[storage-path]"; 
	Sostituire i seguenti valori:
	-	**[storage-path]**: il percorso per accedere al BLOB nella risorsa di archiviazione. Ad esempio: http://yiding.blob.core.windows.net/gettingstarted/

2. Aggiornare il codice di invio di attività come indicato di seguito.

		string taskName;
		ICloudJob job = wm.GetJob(WorkItemName, JobName);
		for (int i = 1; i <= Count; ++i)
		{
		    taskName = "taskdata" + i;
		    IResourceFile programFile = new ResourceFile(BlobPath + "ProcessTaskData.exe", "ProcessTaskData.exe");
		    IResourceFile supportFile = new ResourceFile(BlobPath + taskName + ".txt", taskName);
		    string commandLine = String.Format("ProcessTaskData.exe {0} {1}", BlobPath + taskName + ".txt", 3);
		    ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		    taskToAdd.ResourceFiles = new List<IResourceFile>();
		    taskToAdd.ResourceFiles.Add(programFile);
		    taskToAdd.ResourceFiles.Add(supportFile);
		    job.AddTask(taskToAdd);
		}
		job.Commit(); 

3. Salvare ed eseguire il programma. Dovrebbe essere visualizzato:

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.
		
		Workitem successfully added. Press <Enter> to continue.
		
		Listing Workitems
		=================
		Workitem: yidingz20141106-132140 State:Active
		Press <Enter> to continue.
		
		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		can 3
		you 3
		and 3
		
		Task taskdata2 says:
		and 5
		application 3
		the 3
		
		Task taskdata3 says:
		a 5
		and 5
		to 3

###Procedura: Eliminare il pool e l'elemento di lavoro
Una volta conclusa l'elaborazione dell'elemento di lavoro, è possibile liberare le risorse eliminando il pool e l'elemento di lavoro.

####Eliminare il pool
1.	Aggiungere alla fine del Main il codice seguente per eliminare il pool:

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   pm.DeletePool(PoolName);
		}
		Console.WriteLine("Pool successfully deleted.");
		Console.ReadLine();
2.	Salvare ed eseguire il programma.


####Eliminare l'elemento di lavoro
1.	Aggiungere al Main il codice seguente per eliminare l'elemento di lavoro:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   wm.DeleteWorkItem(WorkItemName);
		}
		Console.WriteLine("Workitem successfully deleted.");
		Console.ReadLine();
2.	Salvare ed eseguire il programma.

###<a name="tutorial1_storage"></a>APPENDICE: Creare un account di archiviazione di Azure
Prima di eseguire il codice di questa esercitazione, è necessario eseguire l'accesso a un account di archiviazione di Azure.  

1.	Accedere al [portale di gestione di Azure](http://manage.windowsazure.com/).
2.	Nella parte inferiore del pannello di navigazione fare clic su **NUOVO**.  
![][1]
3.	Fare clic su **SERVIZI DATI**, quindi su **ARCHIVIAZIONE** e infine su **CREAZIONE RAPIDA**.
![][2]

4.	Nel campo **URL** digitare un nome di sottodominio da usare nell'URI per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo valore diventa il nome host all'interno dell'URI utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.
5.	Scegliere un'opzione dall'elenco **POSIZIONE/GRUPPO DI AFFINITÀ** per definire la posizione di archiviazione.
6.	Facoltativamente, è possibile abilitare la replica geografica.
7.	Fare clic su **CREA ACCOUNT DI ARCHIVIAZIONE**.  

Per altre informazioni sul servizio di archiviazione di Azure, vedere [Come usare il servizio di archiviazione BLOB di Azure in .NET](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/).  


##<a name="tutorial2"></a>Esercitazione 2: Libreria di Azure Batch Apps per .NET
In questa esercitazione viene illustrato come eseguire carichi di lavoro di calcolo paralleli in Azure Batch tramite il servizio Batch Apps.

Batch Apps è una funzionalità di Azure Batch che offre una modalità incentrata sulle applicazioni di gestire ed eseguire carichi di lavoro di Batch.  Usando Batch Apps SDK, è possibile creare pacchetti per abilitare per Batch un'applicazione e distribuirli nel proprio account o renderli disponibili ad altri utenti di Batch.  Batch fornisce inoltre funzionalità di gestione dati, monitoraggio dei processi, diagnostica incorporata, registrazione e supporto per le dipendenze tra attività.  È anche disponibile un portale di gestione in cui è possibile gestire processi, visualizzare log e scaricare output senza dover scrivere un proprio client.

Nello scenario di Batch Apps si scrive il codice usando Batch Apps Cloud SDK per partizionare i processi in attività parallele, descrivere le eventuali dipendenze tra le attività e specificare la modalità di esecuzione di ogni attività.  Questo codice viene distribuito all'account Batch.  I client possono quindi eseguire i processi semplicemente specificando il tipo di processo e i file di input per un'API REST.

>[WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/). Per ottenere gli assembly <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">Batch Apps Cloud</a> e <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">Batch Apps Client</a> è possibile usare NuGet. Dopo aver creato il progetto in Visual Studio, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. È inoltre possibile scaricare l'estensione di Visual Studio per Batch Apps, in cui è incluso un modello di progetto per abilitare le applicazioni per il cloud e per distribuire un'applicazione. Per effettuare questa operazione, accedere <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">qui</a> o eseguire la ricerca di **Batch Apps** in Visual Studio tramite la voce di menu Estensioni e aggiornamenti.  Sono inoltre disponibili <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">esempi end-to-end su MSDN.</a>
>

###Dati fondamentali di Azure Batch Apps 
Batch è progettato per funzionare con le applicazioni esistenti a elevato utilizzo di calcolo. Sfrutta il codice dell'applicazione esistente e viene eseguito in un ambiente generico dinamico e virtualizzato. Per abilitare il funzionamento di un'applicazione con Batch Apps è necessario effettuare alcune operazioni:

1.	Preparare un file zip contenente gli eseguibili dell'applicazione esistente, cioè gli stessi file che verrebbero eseguiti in una server farm o in un cluster tradizionale ed eventuali file di supporto necessari. Questo file zip viene quindi caricato nell'account di Batch tramite il portale di gestione o l'API REST.
2.	Creare un file zip degli "assembly cloud" che inviano i carichi di lavoro all'applicazione e caricarlo tramite il portale di gestione o l'API REST. Un assembly cloud contiene due componenti che sono basati sull'SDK cloud:
	1.	Componente di suddivisione dei processi: suddivide il processo in attività che possono essere elaborate in modo indipendente. Ad esempio, in uno scenario di animazione tale componente dividerebbe un processo relativo a un filmato in singoli fotogrammi. 
	2.	Elaboratore delle attività: invoca l'eseguibile dell'applicazione per una determinata attività. Ad esempio, in uno scenario di animazione, l'elaboratore delle attività invocherebbe un programma di rendering per eseguire il rendering del singolo frame specificato dall'attività in questione. 
3.	Specificare una modalità di invio dei processi per l'applicazione abilitata in Azure. Potrebbe trattarsi di un plug-in nell'interfaccia utente dell'applicazione, di un portale Web o perfino di un servizio automatico nell'ambito della pipeline di esecuzione. Vedere gli <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">esempi</a> nelle apposite sezioni su MSDN.



###Concetti chiave di Batch Apps
Il modello di programmazione e utilizzo di Batch Apps è incentrato sui seguenti concetti chiave:

####Processi
Un **processo** è un elemento di lavoro inviato dall'utente. Quando viene inviato un processo, l'utente specifica il tipo di processo, le relative impostazioni e i dati necessari per il processo. L'implementazione abilitata può fornire questi dettagli per conto dell'utente o, in alcuni casi, l'utente può fornire queste informazioni in modo esplicito tramite il client. Un processo restituisce risultati. Ogni processo dispone di un output primario e, facoltativamente, un output di anteprima. I processi possono inoltre restituire output aggiuntivi, se lo si desidera.

####Attività
Un'**attività** è un elemento di lavoro da eseguire come parte di un processo. Un processo inviato da un utente è suddiviso in attività più piccole. Il servizio elabora quindi le singole attività, quindi assembla i risultati dell'attività in un output del processo complessivo. La natura delle attività dipende dal tipo di processo. Il componente di suddivisione dei processi definisce la modalità di suddivisione di un processo in attività, sulla base della conoscenza dei blocchi di lavoro che l'applicazione è progettata per elaborare. È inoltre possibile scaricare i singoli output di attività e questa funzionalità potrebbe essere utile in alcuni casi, ad esempio quando un utente vuole scaricare singole attività da un processo di animazione.

####Attività di unione
Un'**attività di unione** è un tipo speciale di attività che assembla i risultati delle singole attività nei risultati finali del processo. Per un processo di rendering di un filmato, l'attività di unione potrebbe assemblare i fotogrammi sottoposti a rendering in un filmato o comprimere tutti i fotogrammi sottoposti a rendering in un unico file. Ogni processo presenta un'attività di unione anche nel caso in cui non sia necessaria alcuna unione effettiva.

####File
Un **file** è costituito da dati usati come input in un processo. Un processo può presentare uno o più file di input associati o esserne privo. Lo stesso file può essere usato in più processi, ad esempio, per un processo di rendering di un filmato i file potrebbero essere trame, modelli e così via. Per un processo di analisi dei dati, i file potrebbero essere un insieme di osservazioni o misure.

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

Il metodo RunExternalTaskProcess restituisce un TaskProcessResult, che include un elenco di file di output. È necessario includere almeno tutti i file necessari per l'unione; facoltativamente, è anche possibile restituire i file di log, i file di anteprima e i file intermedi (ad esempio, per scopi diagnostici in caso di errore dell'attività).  Si noti che il metodo restituisce i percorsi, non il contenuto dei file.

Ciascun file deve essere identificato con il tipo di output in esso contenuto: output, ovvero parte dell'output del processo finale, preview, log o intermediate.  Questi valori provengono dall'enumerazione TaskOutputFileKind. Il frammento seguente non restituisce anteprime o log, ma solo l'output di una singola attività. Il metodo TaskProcessResult.FromExternalProcessResult semplifica lo scenario comune di acquisizione del codice di uscita, l'output del processore e i file di output da un programma della riga di comando:

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
 

<!--Anchors-->


<!--Image references-->
[1]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-01.jpg
[2]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-02.jpg
[3]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-03.jpg
[4]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-04.jpg



<!--HONumber=35.2-->

<!--HONumber=46--> 

<!--HONumber=46--> 
