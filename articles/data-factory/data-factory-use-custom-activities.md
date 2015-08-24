<properties 
	pageTitle="Usare attività personalizzate in una pipeline di Data factory di Azure" 
	description="Informazioni su come creare attività personalizzate e usarle in una pipeline di Data factory di Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="spelluru"/>

# Usare attività personalizzate in una pipeline di Data factory di Azure
Data factory di Azure supporta attività incorporate, ad esempio **attività di copia** e **attività HDInsight** da utilizzare nelle pipeline al fine di spostare ed elaborare i dati. È inoltre possibile creare un'attività .NET personalizzata con logica di elaborazione/trasformazione da utilizzare in una pipeline. È possibile configurare l'attività da eseguire utilizzando un cluster **Azure HDInsight** o un servizio **Azure Batch**.

Questo articolo descrive come creare un'attività personalizzata e usarla in una pipeline di Data factory di Azure. Fornisce anche una procedura dettagliata con istruzioni complete per creare e usare un'attività personalizzata. Nella procedura dettagliata viene usato il servizio HDInsight collegato. Per usare il servizio Azure Batch collegato, si crea un servizio collegato di tipo **AzureBatch** e lo si utilizza nella sezione relativa alle attività della pipeline JSON (**linkedServiceName**). Per altre informazioni sull'uso di Azure Batch con l'attività personalizzata, vedere la sezione [Servizi collegati ad Azure Batch](#AzureBatch).


## <a name="walkthrough" /> Procedura dettagliata
In questa procedura dettagliata vengono fornite istruzioni dettagliate per creare un'attività personalizzata da utilizzare in una pipeline di Data factory di Azure. Questa procedura dettagliata rappresenta un supplemento dell'esercitazione riportata in [Introduzione a Data factory di Azure][adfgetstarted]. Per vedere l'attività personalizzata in funzione, è necessario completare prima l'esercitazione di introduzione, quindi passare a questa procedura dettagliata.

**Prerequisiti:**


- Esercitazione tratta da [Introduzione a Data factory di Azure][adfgetstarted]. Prima di eseguire questa procedura dettagliata, è necessario completare l'esercitazione riportata in questo articolo.
- Visual Studio 2012 o 2013
- Scaricare e installare [.NET SDK di Azure][azure-developer-center]
- Scaricare la versione più recente del [pacchetto NuGet per Data factory di Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) e installarla. Le istruzioni sono disponibili nella procedura dettagliata.
- Scaricare e installare il pacchetto NuGet per l'archiviazione di Azure. Le istruzioni sono contenute nella procedura dettagliata, quindi è possibile ignorare questo passaggio.

## Passaggio 1: creare un'attività personalizzata

1.	Creare un progetto della libreria di classi .NET.
	<ol type="a">
	<li>Avviare <b>Visual Studio 2012</b> o <b>Visual Studio 2013</b>.</li>
	<li>Fare clic su <b>File</b>, scegliere <b>Nuovo</b> e quindi fare clic su <b>Progetto</b>.</li> 
	<li>Espandere <b>Modelli</b> e selezionare <b>Visual C#</b>. In questa procedura dettagliata viene usato C#, ma è possibile usare qualsiasi linguaggio .NET per sviluppare l'attività personalizzata.</li> 
	<li>Selezionare la <b>libreria di classi</b> dall'elenco relativo ai tipi di progetto visualizzato a destra.</li>
	<li>Immettere <b>MyDotNetActivity</b> nel campo <b>Nome</b>.</li> 
	<li>Selezionare <b>C:\ADFGetStarted</b> per <b>Percorso</b>.</li>
	<li>Fare clic su <b>OK</b> per creare il progetto.</li>
</ol>
2.  Fare clic su <b>Strumenti</b>, puntare a <b>Gestione pacchetti NuGet</b>, quindi fare clic sulla <b>console di gestione pacchetti</b>.
3.	Nella <b>console di gestione pacchetti</b>, eseguire il seguente comando per importare <b>Microsoft.Azure.Management.DataFactories</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories

4. Importare il pacchetto NuGet di archiviazione di Azure nel progetto.

		Install-Package Azure.Storage

5. Aggiungere le seguenti istruzioni **using** al file di origine nel progetto.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Modificare il nome dello **spazio dei nomi** in **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Modificare il nome della classe in **MyDotNetActivity** e derivarlo dall'interfaccia **IDotNetActivity**, come illustrato di seguito.

		public class MyDotNetActivity : IDotNetActivity

8. Implementare (aggiungere) il metodo **Execute** dell'interfaccia **IDotNetActivity** nella classe **MyDotNetActivity** e copiare il seguente codice di esempio nel metodo.


	Il seguente codice di esempio consente di contare il numero di righe nel BLOB di input e di generare il seguente contenuto nel BLOB di output: percorso al BLOB, numero di righe nel BLOB, computer in cui viene eseguita l'attività, data/ora corrente.

        public IDictionary<string, string> Execute(IEnumerable<LinkedService> linkedServices, IEnumerable<Table> tables, Activity activity, IActivityLogger logger)
        {
            IDictionary<string, string> extendedProperties = ((DotNetActivity)activity.TypeProperties).ExtendedProperties;

            AzureStorageLinkedService inputLinkedService, outputLinkedService;
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Table inputTable = tables.Single(table => table.Name == activity.Inputs.Single().Name);
            inputLocation = inputTable.Properties.TypeProperties as CustomDataset;

			// using First method instead of Single since we are using the same 
			// Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(linkedService => linkedService.Name == inputTable.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;


            string output = string.Empty;

            logger.Write("Before anything...");

            logger.Write("Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            string connectionString = GetConnectionString(inputLinkedService);
            string folderPath = GetFolderPath(inputTable);

            logger.Write("Reading blob from: {0}", folderPath);

            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            BlobContinuationToken continuationToken = null;

            do
            {
                BlobResultSegment result = inputClient.ListBlobsSegmented(folderPath,
                                            true,
                                            BlobListingDetails.Metadata,
                                            null,
                                            continuationToken,
                                            null,
                                            null);
                foreach (IListBlobItem listBlobItem in result.Results)
                {
                    CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                    int count = 0;
                    if (inputBlob != null)
                    {
                        using (StreamReader sr = new StreamReader(inputBlob.OpenRead()))
                        {
                            while (!sr.EndOfStream)
                            {
                                string line = sr.ReadLine();
                                if (count == 0)
                                {
                                    logger.Write("First line: [{0}]", line);
                                }
                                count++;
                            }

                        }

                    }
                    output += string.Format(CultureInfo.InvariantCulture,
                                    "{0},{1},{2},{3},{4}\n",
                                    folderPath,
                                    inputBlob.Name,
                                    count,
                                    Environment.MachineName,
                                    DateTime.UtcNow);

                }
                continuationToken = result.ContinuationToken;

            } while (continuationToken != null);

            Table outputTable = tables.Single(table => table.Name == activity.Outputs.Single().Name);
            outputLocation = outputTable.Properties.TypeProperties as AzureBlobDataset;
            outputLinkedService = linkedServices.First(linkedService => linkedService.Name == outputTable.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            connectionString = GetConnectionString(outputLinkedService);
            folderPath = GetFolderPath(outputTable);

            logger.Write("Writing blob to: {0}", folderPath);

            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            outputBlob.UploadText(output);

            return new Dictionary<string, string>();

        }

9. Aggiungere i seguenti metodi helper. Il metodo **Execute** richiama questi metodi helper. Il metodo **GetConnectionString** consente di recuperare la stringa di connessione dell'archiviazione di Azure, mentre il metodo **GetFolderPath** consente di recuperare il percorso BLOB.


        private static string GetConnectionString(AzureStorageLinkedService asset)
        {

            if (asset == null)
            {
                return null;
            }

            return asset.ConnectionString;
        }

        
        private static string GetFolderPath(Table dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }
   

10. Compilare il progetto. Fare clic su **Compila** dal menu e scegliere **Compila soluzione**.
11. Avviare **Esplora risorse** e passare alla cartella **bin\\debug** o **bin\\release**, a seconda del tipo di compilazione.
12. Creare un file ZIP **MyDotNetActivity.zip** contenente tutti i file binari nella cartella <project folder>\\bin\\Debug. È possibile includere il file MyDotNetActivity.pdb in modo da ottenere ulteriori dettagli, ad esempio il numero della riga nel codice sorgente che ha causato il problema in caso di errore. 
13. Caricare **MyDotNetActivity.zip** come BLOB nel contenitore BLOB: **customactivitycontainer** nell'archiviazione BLOB di Azure usata dal servizio collegato **StorageLinkedService** negli utilizzi **ADFTutorialDataFactory**. Se non è già presente, creare il contenitore BLOB **customactivitycontainer**. 


## Passaggio 2: usare l'attività personalizzata in una pipeline
Di seguito sono elencati i passaggi da eseguire in questa procedura:

1. Creare un servizio collegato per il cluster HDInsight nel quale l'attività personalizzata verrà eseguita come processo solo di mapping. 
2. Creare una tabella di output che verrà prodotta dalla pipeline in questo esempio.
3. Creare ed eseguire una pipeline che usi l'attività personalizzata creata al passaggio 1. 
 
### Creare un servizio collegato per il cluster HDInsight da usare per eseguire l'attività personalizzata
Il servizio Data factory di Azure supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Quando si usa il cluster HDInsight su richiesta, viene creato un cluster per ogni sezione. Invece, se si usa il proprio cluster HDInsight, il cluster è pronto per elaborare immediatamente la sezione. Quindi, quando si usa un cluster su richiesta, i dati di output potrebbero non essere visualizzati tanto rapidamente quanto i dati nel proprio cluster.

> [AZURE.NOTE]Al runtime, un'istanza di un'attività .NET viene eseguita solo in un nodo di lavoro nel cluster HDInsight, ma non può essere scalata al fine di essere eseguita in più nodi. È possibile eseguire più istanze di attività .NET contemporaneamente su diversi nodi del cluster HDInsight.

Se l'esercitazione in [Introduzione a Data factory di Azure][adfgetstarted] è stata estesa con la procedura dettagliata di [Usare Pig e Hive con Data factory di Azure][hivewalkthrough], è possibile ignorare la creazione di questo servizio collegato e usare il servizio collegato già disponibile in ADFTutorialDataFactory.


#### Per usare un cluster HDInsight su richiesta

1. Nel **portale di Azure**, fare clic su **Creare e distribuire** nella home page di Data factory.
2. Nell'Editor di Data factory, fare clic su **Nuovo calcolo** sulla barra dei comandi, quindi scegliere **Cluster HDInsight su richiesta** dal menu.
2. Nello script JSON procedere come segue: 
	1. Per la proprietà **clusterSize**, specificare le dimensioni del cluster HDInsight.
	2. Per la proprietà **jobsContainer**, specificare il nome del contenitore predefinito in cui verranno archiviati i log del cluster. Ai fini di questa esercitazione, specificare **adfjobscontainer**.
	3. Per la proprietà **timeToLive** specificare il tempo di inattività consentito per il cliente prima dell'eliminazione. 
	4. Per la proprietà **version**, specificare la versione di HDInsight da utilizzare. Se si esclude questa proprietà, verrà usata la versione più recente.  
	5. Per **linkedServiceName**, specificare l'oggetto **StorageLinkedService** creato nell'esercitazione introduttiva. 

		{ "name": "HDInsightOnDemandLinkedService", "properties": { "type": "HDInsightOnDemand", "typeProperties": { "clusterSize": "1", "timeToLive": "00:05:00", "version": "3.1", "linkedServiceName": "StorageLinkedService" } } }

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.
   
#### Per usare il proprio cluster HDInsight: 

1. Nel **portale di Azure**, fare clic su **Creare e distribuire** nella home page di Data factory.
2. Nell'**Editor di Data factory**, fare clic su **Nuovo calcolo** sulla barra dei comandi, quindi scegliere **Cluster HDInsight** dal menu.
2. Nello script JSON procedere come segue: 
	1. Per la proprietà **clusterUri**, immettere l'URL di HDInsight. Ad esempio: https://<clustername>.azurehdinsight.net/     
	2. Per la proprietà **UserName**, immettere il nome dell'utente che ha accesso al cluster HDInsight.
	3. Per la proprietà **Password**, immettere la password dell'utente. 
	4. Per la proprietà **LinkedServiceName**, immettere **StorageLinkedService**, ossia il servizio collegato creato nell'esercitazione introduttiva. 

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

### Creare una tabella di output

1. Nell'**Editor di Data factory**, fare clic su **Nuovo set di dati**, quindi fare clic su **Archivio BLOB di Azure** nella barra dei comandi.
2. Sostituire lo script JSON nel riquadro a destra con il seguente script JSON:

		{
		  "name": "OutputTableForCustom",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/customactivityoutput/{Slice}",
		      "partitionedBy": [
		        {
		          "name": "Slice",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMddHH"
		          }
		        }
		      ]
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

 	Il percorso di output è **adftutorial/customactivityoutput/AAAMMGGHH/** dove AAAMMGGHH corrisponde ad anno, mese, data e ora della sezione generata. Per informazioni dettagliate, vedere la [guida di riferimento per gli sviluppatori][adf-developer-reference].

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la tabella.


### Creare ed eseguire una pipeline che usi l'attività personalizzata
   
1. Nell'Editor di Data factory fare clic su **Nuova pipeline** sulla barra dei comandi. Se non viene visualizzato il comando, fare clic su **... (puntini di sospensione)** per visualizzarlo. 
2. Sostituire lo script JSON nel riquadro a destra con lo script JSON seguente. Per usare il proprio cluster dopo aver seguito i passaggi per creare il servizio collegato **HDInsightLinkedService**, sostituire **HDInsightOnDemandLinkedService** con **HDInsightLinkedService** nel codice JSON seguente. 
		
		{
		  "name": "ADFTutorialPipelineCustom",
		  "properties": {
		    "description": "Use custom activity",
		    "activities": [
		      {
		        "Name": "MyDotNetActivity",
		        "Type": "DotNetActivity",
		        "Inputs": [
		          {
		            "Name": "EmpTableFromBlob"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputTableForCustom"
		          }
		        ],
		        "LinkedServiceName": "HDInsightOnDemandLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "StorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
		          "Concurrency": 1,
		          "ExecutionPriorityOrder": "OldestFirst",
		          "Retry": 3,
		          "Timeout": "00:30:00",
		          "Delay": "00:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	Sostituire il valore **StartDateTime** con la data di tre giorni fa e il valore **EndDateTime** con il giorno corrente. StartDateTime ed EndDateTime devono essere in [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. La tabella di output viene pianificata per essere generata tutti i giorni, quindi saranno generate tre sezioni.

	Tenere presente quanto segue:

	- Nella sezione delle attività esiste una sola attività, di tipo **DotNetActivity**.
	- Usare la stessa tabella di input **EmpTableFromBlob** usata nell'esercitazione di introduzione.
	- Usare una nuova tabella di output **OutputTableForCustom** da creare nel passaggio successivo.
	- **AssemblyName** è impostato sul nome del DLL: **MyActivities.dll**.
	- **EntryPoint** è impostato su **MyDotNetActivityNS.MyDotNetActivity.**
	- **PackageLinkedService** è impostato su **MyBlobStore**, creato durante l'esercitazione in [Introduzione a Data factory di Azure][adfgetstarted]. Questo archivio BLOB contiene il file ZIP dell'attività personalizzata.
	- **PackageFile** è impostato su **customactivitycontainer/MyDotNetActivity.zip**.
     
4. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline.
8. Verificare che i file di output vengano generati nell'archiviazione BLOB nel contenitore **adftutorial**.

	![output dell'attività personalizzata][image-data-factory-ouput-from-custom-activity]

9. Se si apre il file di output, l'output visualizzato dovrebbe essere simile al seguente:
	
	adftutorial/,emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(percorso BLOB), (nome del BLOB), (numero di righe nel BLOB), (nodo in cui è stata eseguita l'attività), (indicatore data/ora)

10.	Usare il [portale di Azure][azure-preview-portal] o i cmdlet di Azure PowerShell per monitorare l'istanza di Data factory, le pipeline e i set di dati. I messaggi possono essere visualizzati da **ActivityLogger** nel codice per l'attività personalizzata nei log (specifically user-0.log) scaricabili dal portale o con i cmdlet.

	![scaricare i log dall'attività personalizzata][image-data-factory-download-logs-from-custom-activity]
	
   
Vedere [Monitoraggio e gestione delle pipeline](data-factory-monitor-manage-pipelines.md) per i passaggi dettagliati per il monitoraggio di set di dati e pipeline.

## Aggiornamento di un'attività personalizzata
Per aggiornare il codice dell'attività personalizzata, compilarlo e caricare il file ZIP contenente i nuovi file binari nell'archiviazione BLOB.
    
## <a name="AzureBatch"></a> Uso del servizio collegato Azure Batch 
> [AZURE.NOTE]Per una panoramica del servizio Azure Batch, vedere [Panoramica tecnica di Azure Batch][batch-technical-overview]; per iniziare subito a usare il servizio Azure Batch, vedere [Introduzione alla libreria di Azure Batch per .NET][batch-get-started].

È possibile eseguire le attività .NET personalizzate utilizzando Azure Batch come risorsa di calcolo. È necessario creare il proprio pool di Batch di Azure e specificare il numero di macchine virtuali e altre configurazioni. I pool di Batch Azure fornisce ai clienti le funzionalità seguenti:

1. Creare pool che contengano da una singola memoria centrale a migliaia di memorie centrali.
2. Numero di VM a scalabilità automatica basato su una formula
3. Macchine virtuali di supporto di qualsiasi dimensione
4. Numero configurabile di attività per ogni macchina virtuale
5. Numero illimitato della coda di attività


Di seguito sono riportati i passaggi generali per usare il servizio collegato Azure Batch nella procedura dettagliata descritta nella sezione precedente:

1. Creare un account di Azure Batch usando il portale di gestione di Azure. Per le istruzioni, consultare l'articolo [Panoramica tecnica di Azure Batch][batch-create-account]. Annotare il nome e la chiave dell'account Azure Batch. 

	Inoltre, è possibile usare il cmdlet [New-AzureBatchAccount][new-azure-batch-account] per creare un account di Azure Batch. Per istruzioni dettagliate sull'utilizzo del cmdlet, consultare [Utilizzo di Azure PowerShell per gestire l'account di Azure Batch][azure-batch-blog]. 
2. Creare un pool di Azure Batch. Per creare un pool di Azure Batch, è possibile scaricare il codice origine per lo [strumento di esplorazione di Azure Batch][batch-explorer], generarlo e utilizzarlo oppure usare la [libreria di Azure Batch per .NET][batch-net-library]. Per istruzioni dettagliate sull'uso dello strumento di esplorazione di Azure Batch, vedere la [procedura dettagliata di esempio relativa allo strumento di esplorazione di Azure Batch][batch-explorer-walkthrough].
	
	Inoltre, è possibile utilizzare [New-AzureBatchPool][new-azure-batch-pool] per creare un pool di Azure Batch.

2. Creare un servizio collegato di Azure Batch usando il seguente modello JSON. L'Editor di Data factory mostra un modello simile con cui iniziare. Specificare il nome dell'account Azure Batch, la chiave account e il nome del pool nel frammento JSON.

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.NOTE]Aggiungere "**.<region name**" al nome dell'account batch per la proprietà **accountName**. Esempio: "mybatchaccount.eastus". Un'altra opzione consiste nel fornire l’endpoint batchUri come illustrato di seguito.

		accountName: "adfteam",
		batchUri: "https://eastus.batch.azure.com",
 
	Per le descrizioni di queste proprietà, vedere l'[argomento MSDN sul servizio collegato di Azure Batch](https://msdn.microsoft.com/library/mt163609.aspx).

2.  Nell'Editor di Data factory, aprire la definizione JSON per la pipeline creata nella procedura dettagliata e sostituire **HDInsightLinkedService** con **AzureBatchLinkedService**.
3.  Può essere opportuno modificare l'ora di inizio e fine per la pipeline per poter testare lo scenario con il servizio di Azure Batch. 
4.  È possibile visualizzare le attività di Azure Batch associate all'elaborazione delle sezioni nello strumento di esplorazione di Azure Batch, come illustrato nel diagramma seguente.

	![Attività di Azure Batch][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE]Il servizio Data Factory non supporta un'opzione su richiesta per il Batch di Azure come accade per HDInsight. È possibile utilizzare solo il proprio pool di Batch di Azure in una data factory di Azure.

## Vedere anche

[Aggiornamenti della Data factory di Azure: eseguire le attività .NET personalizzate per ADF usando Azure Batch](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-pig-hive-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png
 

<!---HONumber=August15_HO7-->