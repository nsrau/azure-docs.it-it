<properties
	pageTitle="Esercitazione - Introduzione alla libreria Azure Batch .NET | Microsoft Azure"
	description="Informazioni sui concetti di base di Azure Batch e su come sviluppare per il servizio Batch con uno scenario semplice."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="09/23/2015"
	ms.author="yidingz"/>

# Introduzione alla libreria di Azure Batch per .NET  

Iniziare a lavorare con la libreria .NET di Azure Batch tramite la creazione di un'applicazione console che consente di impostare i file di supporto e un programma che viene eseguito su più nodi di calcolo in un pool di Azure Batch. Le attività create in questa esercitazione prevedono la valutazione del testo dei file presenti in Archiviazione di Azure e quindi la restituzione delle parole usate con maggiore frequenza. Gli esempi sono in codice C# e usano la [Libreria Azure Batch .NET](https://msdn.microsoft.com/library/azure/mt348682.aspx).

## Prerequisiti

- Gli account:

	- **Account Azure**: è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).

	- **Account Batch**: vedere la sezione **Account Batch** in [Panoramica tecnica di Azure Batch](batch-technical-overview.md).

	- **Account di archiviazione**: vedere la sezione **Creare un account di archiviazione** in [Informazioni sugli account di archiviazione di Azure](../storage-create-storage-account.md). In questa esercitazione è possibile creare un contenitore nell'account denominato **testcon1**.

- Un progetto applicazione console di Visual Studio:

	1.  Aprire Visual Studio, nel menu **File** scegliere **Nuovo** e quindi fare clic su **Progetto**.

	2.	Da **Windows**, in **Visual C#**, fare clic su **Applicazione console**, assegnare al progetto il nome **GettingStarted**, assegnare alla soluzione il nome **AzureBatch**, quindi fare clic su **OK**.

- Gli assembly NuGet:

	1. Dopo aver creato il progetto in Visual Studio, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. Cercare online **Azure.Batch** e fare clic su **Installa** per installare il pacchetto Microsoft Azure Batch e le relative dipendenze.

	2. Cercare online **WindowsAzure.Storage** e fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le dipendenze.

> [AZURE.TIP]In questa esercitazione vengono utilizzati alcuni concetti fondamentali di Batch illustrati in [Nozioni fondamentali di API per Azure Batch](batch-api-basics.md), la cui lettura è consigliata per i nuovi utenti.

## Passaggio 1: Creare e caricare i file di supporto

Per supportare l'applicazione viene creato un contenitore in Archiviazione di Azure, vengono creati i file di testo e quindi i file di testo e i file di supporto vengono caricati nel contenitore.

### Configurare la stringa di connessione di archiviazione

1. Aprire il file App.config per il progetto GettingStarted e quindi aggiungere l'elemento *&lt;appSettings&gt;* in *&lt;configuration&gt;*.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
			<appSettings>
				<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[account-name];AccountKey=[account-key]"/>
			</appSettings>
		</configuration>

	Sostituire questi valori:

	- **[account-name]**: nome dell'account di archiviazione creato in precedenza.

	- **[account key]**: chiave primaria dell'account di archiviazione. È possibile trovare la chiave primaria nella pagina Archiviazione nel portale di Azure.

2. Salvare il file App.config.

Per ulteriori informazioni sulle stringhe di connessione di archiviazione di Azure, vedere [configurare stringhe di connessione di archiviazione di Azure](../storage/storage-configure-connection-string.md).

### Creare il contenitore di archiviazione

1. Aggiungere queste dichiarazioni all'inizio del file Program.cs nel progetto GettingStarted:

		using System.Configuration;
		using System.IO;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

2. Aggiungere *System.Configuration* a **Riferimenti** in **Esplora soluzioni** per il progetto GettingStarted

3. Aggiungere questo metodo alla classe Program che recupera la stringa di connessione di archiviazione, crea il contenitore e imposta le autorizzazioni:

		static void CreateStorage()
		{
			// Get the storage connection string
			CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
				ConfigurationManager.AppSettings["StorageConnectionString"]);

			// Create the container
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
			CloudBlobContainer container = blobClient.GetContainerReference("testcon1");
			container.CreateIfNotExists();

			// Set permissions on the container
			BlobContainerPermissions containerPermissions = new BlobContainerPermissions();
			containerPermissions.PublicAccess = BlobContainerPublicAccessType.Blob;
			container.SetPermissions(containerPermissions);
			Console.WriteLine("Created the container. Press Enter to continue.");
			Console.ReadLine();
		}

4. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

		CreateStorage();

5. Salvare il file Program.cs.

	> [AZURE.NOTE]In un ambiente di produzione, si consiglia di usare una [firma di accesso condiviso](https://msdn.microsoft.com/library/azure/ee395415.aspx).

Per altre informazioni sull’archiviazione BLOB, vedere [Come utilizzare l’archiviazione BLOB da .NET](../storage/storage-dotnet-how-to-use-blobs.md)

### Creare il programma di elaborazione

1. In **Esplora soluzioni** creare un nuovo progetto di applicazione console denominato **ProcessTaskData**.

2. Dopo aver creato il progetto in Visual Studio, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**. Cercare online **WindowsAzure.Storage** e poi fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le dipendenze.

3. Aggiungere la seguente direttiva using all’inizio del file Program.cs:

		using Microsoft.WindowsAzure.Storage.Blob;

4. Aggiungere questo codice a Main per elaborare il testo dai file:

		string blobName = args[0];
		Uri blobUri = new Uri(blobName);
		int numTopN = int.Parse(args[1]);

		CloudBlockBlob blob = new CloudBlockBlob(blobUri);
		string content = blob.DownloadText();
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

5. Salvare e compilare il progetto ProcessTaskData.

### Creare i file di dati

1. Nel progetto GettingStarted creare un nuovo file di testo denominato **taskdata1.txt**, copiare il testo seguente nel file e quindi salvarlo.

	Quando sono necessarie risorse flessibili per soddisfare le esigenze aziendali, è possibile usare Macchine virtuali di Azure per eseguire il provisioning dell'infrastruttura di calcolo su richiesta e scalabile. Dalla raccolta è possibile creare macchine virtuali che eseguono Windows, Linux e applicazioni aziendali come SharePoint e SQL Server. In alternativa, è possibile acquisire e usare le proprie immagini per creare macchine virtuali personalizzate.

2. Creare un nuovo file di testo denominato **taskdata2.txt**, copiare il testo seguente nel file e quindi salvarlo.

	Con Servizi cloud di Azure è possibile distribuire rapidamente e gestire potenti applicazioni e servizi. È sufficiente caricare l'applicazione e Azure gestirà i dettagli della distribuzione, dal provisioning al bilanciamento del carico, al monitoraggio dell'integrità per la disponibilità continua. L'applicazione è supportata da un contratto di servizio mensile del 99,95%, leader nel settore. L'utente può pertanto concentrarsi sull'applicazione anziché sull'infrastruttura.

3. Creare un nuovo file di testo denominato **taskdata3.txt**, copiare il testo seguente nel file e quindi salvarlo.

	Siti Web di Azure fornisce un ambiente scalabile, affidabile e facile da usare per l'hosting delle applicazioni Web. È possibile scegliere tra una vasta gamma di framework e modelli per creare un sito Web in pochi secondi e anche usare qualsiasi strumento o sistema operativo per sviluppare un sito con .NET, PHP, Node.js o Python. È inoltre possibile scegliere tra numerose opzioni di controllo del codice sorgente, tra cui TFS, GitHub e BitBucket, per configurare l'integrazione continua ed eseguire attività di sviluppo in team. Le funzionalità del sito possono essere ampliate nel corso del tempo grazie ai servizi gestiti aggiuntivi di Azure come Archiviazione, CDN e Database SQL.

### Caricare i file nel contenitore di archiviazione

1. Aprire il file Program.cs del progetto **GettingStarted** e quindi aggiungere questo metodo che carica i file:

		static void CreateFiles()
		{
			CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
				ConfigurationManager.AppSettings["StorageConnectionString"]);
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
			CloudBlobContainer container = blobClient.GetContainerReference("testcon1");

			CloudBlockBlob taskData1 = container.GetBlockBlobReference("taskdata1");
			CloudBlockBlob taskData2 = container.GetBlockBlobReference("taskdata2");
			CloudBlockBlob taskData3 = container.GetBlockBlobReference("taskdata3");
			taskData1.UploadFromFile("..\\..\\taskdata1.txt", FileMode.Open);
			taskData2.UploadFromFile("..\\..\\taskdata2.txt", FileMode.Open);
			taskData3.UploadFromFile("..\\..\\taskdata3.txt", FileMode.Open);

			CloudBlockBlob storageassembly = container.GetBlockBlobReference("Microsoft.WindowsAzure.Storage.dll");
			storageassembly.UploadFromFile("Microsoft.WindowsAzure.Storage.dll", FileMode.Open);

			CloudBlockBlob dataprocessor = container.GetBlockBlobReference("ProcessTaskData.exe");
			dataprocessor.UploadFromFile("..\\..\\..\\ProcessTaskData\\bin\\debug\\ProcessTaskData.exe", FileMode.Open);

			Console.WriteLine("Uploaded the files. Press Enter to continue.");
			Console.ReadLine();
		}

2. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

		CreateFiles();

3. Salvare il file Program.cs.

## Passaggio 2. Aggiungere un pool al proprio account Batch

Un pool di nodi di calcolo è il primo set di risorse che è necessario creare per eseguire attività.

1.	Aggiungere queste dichiarazioni all'inizio del file Program.cs nel progetto GettingStarted:

			using Microsoft.Azure.Batch;
			using Microsoft.Azure.Batch.Auth;
			using Microsoft.Azure.Batch.Common;

2. Aggiungere questo codice a Main per impostare le credenziali per le chiamate al servizio Azure Batch:

			BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials("[account-url]", "[account-name]", "[account-key]");
			BatchClient client = BatchClient.Open(cred);

	Sostituire i valori tra parentesi quadre con quelli associati all'account di Batch, ognuno dei quali è disponibile nel [portale di anteprima di Azure](https://portal.azure.com). Per individuare questi valori, accedere al [portale di anteprima di Azure](https://portal.azure.com) e:

	- **[nome-account]** - Fare clic su **Account Batch**, selezionare l'account Batch creato in precedenza
	- **[chiave-account]** - Nel pannello dell’account Batch, fare clic su **Proprietà** > **URL**
	- **[chiave-account]** - nel pannello dell’account Batch, fare clic su **Proprietà** > **Chiavi** > **Chiave di accesso primaria**

3.	Aggiungere questo metodo alla classe Program che crea il pool:

			static void CreatePool(BatchClient client)
			{
			  CloudPool newPool = client.PoolOperations.CreatePool(
			    "testpool1",
			    "3",
			    "small",
			    3);
			  newPool.Commit();
			  Console.WriteLine("Created the pool. Press Enter to continue.");
			  Console.ReadLine();
		  	}

4. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

		CreatePool(client);

5. Aggiungere questo metodo alla classe Program per elencare i pool nell'account. Ciò consente di verificare che sia stato creato il pool:

			static void ListPools(BatchClient client)
			{
				IPagedEnumerable<CloudPool> pools = client.PoolOperations.ListPools();
				foreach (CloudPool pool in pools)
				{
					Console.WriteLine("Pool name: " + pool.Id);
					Console.WriteLine("   Pool status: " + pool.State);
				}
				Console.WriteLine("Press enter to continue.");
				Console.ReadLine();
			}

6. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

		ListPools(client);

7. Salvare il file Program.cs.

## Passaggio 3: Aggiungere un processo a un account

Creare un processo che consente di gestire le attività eseguite nel pool. Tutte le attività devono essere associate a un processo.

1. Aggiungere questo metodo alla classe Program per creare il processo:

		static CloudJob CreateJob (BatchClient client)
		{
			CloudJob newJob = client.JobOperations.CreateJob();
			newJob.Id = "testjob1";
			newJob.PoolInformation = new PoolInformation() { PoolId = "testpool1" };
			newJob.Commit();
			Console.WriteLine("Created the job. Press Enter to continue.");
			Console.ReadLine();

			return newJob;
		}

2. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

		CreateJob(client);

3. Aggiungere questo metodo alla classe Program per elencare i processi nell'account. Ciò consente di verificare che il processo sia stato creato:

		static void ListJobs (BatchClient client)
		{
			IPagedEnumerable<CloudJob> jobs = client.JobOperations.ListJobs();
			foreach (CloudJob job in jobs)
			{
				Console.WriteLine("Job id: " + job.Id);
				Console.WriteLine("   Job status: " + job.State);
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}

4. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

		ListJobs(client);

5. Salvare il file Program.cs.

## Passaggio 4: Aggiungere attività al processo

Dopo aver creato il processo, è possibile aggiungervi attività. Ogni attività viene eseguita su un nodo di calcolo ed elabora un file di testo. Per questa esercitazione, è necessario aggiungere tre attività al processo.

1. Aggiungere questo metodo alla classe Program per aggiungere le tre attività al processo:

		static void AddTasks(BatchClient client)
		{
			CloudJob job = client.JobOperations.GetJob("testjob1");
			ResourceFile programFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/ProcessTaskData.exe",
				"ProcessTaskData.exe");
      	  ResourceFile assemblyFile = new ResourceFile(
				"https://[account-name].blob.core.windows.net/testcon1/Microsoft.WindowsAzure.Storage.dll",
				"Microsoft.WindowsAzure.Storage.dll");
			for (int i = 1; i < 4; ++i)
			{
				string blobName = "taskdata" + i;
				string taskName = "mytask" + i;
				ResourceFile taskData = new ResourceFile("https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName, blobName);
				CloudTask task = new CloudTask(taskName, "ProcessTaskData.exe https://[account-name].blob.core.windows.net/testcon1/" +
				  blobName + " 3");
				List<ResourceFile> taskFiles = new List<ResourceFile>();
				taskFiles.Add(taskData);
				taskFiles.Add(programFile);
				taskFiles.Add(assemblyFile);
				task.ResourceFiles = taskFiles;
				job.AddTask(task);
				job.Commit();
				job.Refresh();
			}

			client.Utilities.CreateTaskStateMonitor().WaitAll(job.ListTasks(),
        TaskState.Completed, new TimeSpan(0, 30, 0));
			Console.WriteLine("The tasks completed successfully.");
			foreach (CloudTask task in job.ListTasks())
			{
				Console.WriteLine("Task " + task.Id + " says:\n" + task.GetNodeFile(Constants.StandardOutFileName).ReadAsString());
			}
			Console.WriteLine("Press Enter to continue.");
			Console.ReadLine();
		}


	**[nome-account]** deve essere sostituito con il nome dell'account di archiviazione creato in precedenza. Nell'esempio precedente, aggiornare tutte le quattro istanze di **[nome-account]**.


2. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

			AddTasks(client);

3. Aggiungere questo metodo alla classe Program per elencare le attività associate al processo:

		static void ListTasks(BatchClient client)
		{
			IPagedEnumerable<CloudTask> tasks = client.JobOperations.ListTasks("testjob1");
			foreach (CloudTask task in tasks)
			{
				Console.WriteLine("Task id: " + task.Id);
				Console.WriteLine("   Task status: " + task.State);
			  Console.WriteLine("   Task start: " + task.ExecutionInformation.StartTime);
			}
			Console.ReadLine();
		}

4. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

		ListTasks(client);

5. Salvare il file Program.cs.

## Passaggio 5: Eliminare le risorse

Poiché sono previsti addebiti per le risorse in Azure, è sempre consigliabile eliminare le risorse che non sono più necessarie.

### Eliminare le attività

1.	Aggiungere questo metodo alla classe Program per eliminare le attività:

			static void DeleteTasks(BatchClient client)
			{
				CloudJob job = client.JobOperations.GetJob("testjob1");
				foreach (CloudTask task in job.ListTasks())
				{
					task.Delete();
				}
				Console.WriteLine("All tasks deleted.");
				Console.ReadLine();
			}

2. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

		DeleteTasks(client);

3. Salvare il file Program.cs.

### Eliminare il processo

1.	Aggiungere questo metodo alla classe Program per eliminare il processo:

			static void DeleteJob(BatchClient client)
			{
				client.JobOperations.DeleteJob("testjob1");
				Console.WriteLine("Job was deleted.");
				Console.ReadLine();
			}

2. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

		DeleteJob(client);

3. Salvare il file Program.cs.

### Eliminare il pool

1. Aggiungere questo metodo alla classe Program per eliminare il pool:

		static void DeletePool (BatchClient client)
		{
			client.PoolOperations.DeletePool("testpool1");
			Console.WriteLine("Pool was deleted.");
			Console.ReadLine();
		}

2. Aggiungere questo codice a Main per chiamare il metodo appena aggiunto:

		DeletePool(client);

3. Salvare il file Program.cs.

## Passaggio 6: Eseguire l'applicazione

1. Avviare il progetto GettingStarted. Nella finestra della console dovrebbe essere visualizzato il messaggio seguente dopo la creazione del contenitore:

		Created the container. Press Enter to continue.

2. Premere INVIO e i file vengono creati e caricati. A questo punto nella finestra dovrebbe essere visibile una nuova riga:

		Uploaded the files. Press Enter to continue.

3. Premere INVIO e il pool viene creato:

		Created the pool. Press Enter to continue.

4. Premere INVIO. Dovrebbero essere visualizzate le informazioni seguenti per il nuovo pool:

		Pool name: testpool1
			Pool status: Active
		Press Enter to continue.

5. Premere INVIO e il processo viene creato:

		Created the job. Press Enter to continue.

6. Premere INVIO. Dovrebbero essere visualizzate le informazioni seguenti per il nuovo processo:

		Job id: testjob1
			Job status: Active
		Press Enter to continue.

7. Premere INVIO e le attività vengono aggiunte al processo. Dopo l'aggiunta, le attività vengono eseguite automaticamente:

		The tasks completed successfully.
		Task mytask1 says:
		can 3
		you 3
		and 3

		Task mytask2 says:
		and 5
		application 3
		the 3

		Task mytask3 says:
		a 5
		and 5
		to 3

		Press Enter to continue.

7. Premere INVIO. Dovrebbe essere visualizzato l'elenco delle attività e del relativo stato:

		Task id: mytask1
			Task status: Completed
			Task start: 7/17/2015 8:31:58 PM
		Task id: mytask2
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM
		Task id: mytask3
			Task status: Completed
			Task start: 7/17/2015 8:31:57 PM

8. A questo punto è possibile passare al portale di Azure per esaminare le risorse create. Per eliminare le risorse, premere INVIO fino al completamento del programma.

## Passaggi successivi

1. Dopo aver appreso le nozioni di base dell'esecuzione di attività, è possibile scoprire come gestire la scalabilità automatica dei nodi di calcolo quando cambia la richiesta per l'applicazione. A tale scopo, vedere [Scalabilità automatica dei nodi di calcolo in un pool di Azure Batch](batch-automatic-scaling.md)

2. Alcune applicazioni producono grandi quantità di dati che possono essere difficili da elaborare. L'esecuzione di [query di elenco efficienti](batch-efficient-list-queries.md) è uno dei modi per risolvere questa difficoltà.

<!---HONumber=Sept15_HO4-->