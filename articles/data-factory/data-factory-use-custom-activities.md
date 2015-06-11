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
	ms.date="06/02/2015" 
	ms.author="spelluru"/>

# Usare attività personalizzate in una pipeline di Data factory di Azure
Factory dati Azure supporta attività incorporate, ad esempio **attività Copia** e **attività HDInsight** da utilizzare nelle pipeline per spostare ed elaborare i dati. È inoltre possibile creare un'attività personalizzata di .NET con la logica di elaborazione e di trasformazione e utilizzare l'attività in una pipeline. È possibile configurare l'attività eseguita utilizzando un **Azure HDInsight** cluster o un **Azure Batch** servizio.

Questo articolo descrive come creare un'attività personalizzata e usarla in una pipeline di Data factory di Azure. Fornisce anche una procedura dettagliata con istruzioni complete per creare e usare un'attività personalizzata. La procedura dettagliata viene utilizzato il servizio HDInsight collegato. Per utilizzare il Batch di Azure collegati service, invece, si crea un servizio di tipo collegato **AzureBatchLinkedService** e utilizzarlo nella sezione attività della pipeline JSON (* * linkedServiceName * *). Vedere il [servizi collegati Batch Azure](#AzureBatch) per ulteriori informazioni sull'utilizzo di Batch di Azure con l'attività personalizzata.

## Prerequisiti
Scaricare la versione più recente [pacchetto NuGet per Azure dati Factory][nuget-package] e installarlo. Istruzioni il [procedura dettagliata](#SupportedSourcesAndSinks) in questo articolo.

## Creazione di un'attività personalizzata

Per creare un'attività personalizzata:
 
1.	Creare un **libreria di classi** progetto in Visual Studio 2013.
3. Aggiungere quanto segue usando le istruzioni nella parte superiore del file di origine nella libreria di classi.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Aggiornare la classe per implementare il **IDotNetActivity** interfaccia.
	<ol type='a'>
	<li>
		Derivare la classe da <b>IDotNetActivity</b>.
		<br/>
		Esempio: <br/>
		classe pubblica <b>MyDotNetActivity: IDotNetActivity</b>
	</li>

	<li>
		Implementare il <b>Execute</b> metodo <b>IDotNetActivity</b> interfaccia
	</li>

</ol>
5. Compilare il progetto.


## Utilizzo dell'attività personalizzata in una pipeline
Per usare l'attività personalizzata in una pipeline:

1.	**Comprimere** tutti i file binari dal **bin\debug** o **bin\release** cartelle per il progetto di output. 
2.	**Caricare il file zip** file come blob per il **archiviazione blob di Azure**. 
3.	Aggiorna il **pipeline JSON** file per fare riferimento al file zip, DLL di attività personalizzata, la classe di attività e il blob che contiene il file zip nella pipeline di JSON. Nel file JSON:
	<ol type ="a">
	<li><b>Tipo di attività</b> deve essere impostato su <b>DotNetActivity</b>.</li>
	<li><b>AssemblyName</b> è il nome dell'output DLL dal progetto di Visual Studio.</li>
	<li><b>EntryPoint</b> specifica il <b>dello spazio dei nomi</b> e <b>nome</b> del <b>classe</b> che implementa il <b>IDotNetActivity</b> interfaccia.</li>
	<li><b>PackageLinkedService</b> è il servizio collegato che fa riferimento all'oggetto blob che contiene il file zip. </li>
	<li><b>PackageFile</b> specifica il percorso e nome del file zip che è stato caricato nell'archiviazione blob di Azure.</li>
	<li><b>LinkedServiceName</b> è il nome del servizio collegato che si collega un cluster HDInsight (su richiesta o personalizzato) a una factory di dati. L'attività personalizzata viene eseguita come processo solo di mapping nel cluster HDInsight specificato.</li>
</ol>**Esempio JSON parziale**

		"Name": "MyDotNetActivity",
    	"Type": "DotNetActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyDotNetActivity.dll",
    	    "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",

## Aggiornamento di un'attività personalizzata
Se si aggiorna il codice per l'attività personalizzata, compilarlo e caricare il file zip che contiene i file binari nuovi nell'archiviazione blob.

## <a name="walkthrough" /> Procedura dettagliata
Questa procedura dettagliata fornisce istruzioni dettagliate per la creazione di un'attività personalizzata e utilizzo dell'attività in una pipeline di Factory di dati di Azure. Questa procedura dettagliata estende l'esercitazione dalla [iniziare a utilizzare Azure dati Factory][adfgetstarted]. Per vedere l'attività personalizzata in funzione, è necessario completare prima l'esercitazione di introduzione, quindi passare a questa procedura dettagliata.

**Prerequisiti:**


- Esercitazione da [iniziare a utilizzare Azure dati Factory][adfgetstarted]. Prima di eseguire questa procedura dettagliata, è necessario completare l'esercitazione in questo articolo.
- Visual Studio 2012 o 2013
- Scaricare e installare [Azure .NET SDK][azure-developer-center]
- Scaricare la versione più recente [pacchetto NuGet per Azure dati Factory][nuget-package] e installarlo. Le istruzioni sono disponibili nella procedura dettagliata.
- Scaricare e installare il pacchetto NuGet per l'archiviazione di Azure. Le istruzioni sono contenute nella procedura dettagliata, quindi è possibile ignorare questo passaggio.

## Passaggio 1: Creare un'attività personalizzata

1.	Creare un progetto della libreria di classi .NET.
	<ol type="a">
	<li>Avviare <b>Visual Studio 2012</b> o <b>Visual Studio 2013</b>.</li>
	<li>Fare clic su <b>File</b>, scegliere <b>nuovo</b>, e fare clic su <b>progetto</b>.</li> 
	<li>Espandere <b>modelli</b>, e selezionare <b>Visual c#</b>. In questa procedura dettagliata viene usato C#, ma è possibile usare qualsiasi linguaggio .NET per sviluppare l'attività personalizzata.</li> 
	<li>Selezionare <b>libreria di classi</b> dall'elenco dei tipi di progetto a destra.</li>
	<li>Immettere <b>MyDotNetActivity</b> per il <b>nome</b>.</li> 
	<li>Selezionare <b>C:\ADFGetStarted</b> per il <b>indirizzo</b>.</li>
	<li>Fare clic su <b>OK</b> per creare il progetto.</li>
</ol>
2.  Fare clic su <b>strumenti</b>, scegliere <b>Gestione pacchetti NuGet</b>, e fare clic su <b>Console di gestione pacchetti</b>.
3.	Nel <b>Console di gestione pacchetti</b>, eseguire il comando seguente per importare <b>Microsoft.Azure.Management.DataFactories</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre

3.	Nel <b>Console di gestione pacchetti</b>, eseguire il comando seguente per importare <b>Microsoft.DataFactories.Runtime</b>. Sostituire la cartella con il percorso contenente il pacchetto NuGet di Data factory scaricato.

		Install-Package Microsoft.DataFactories.Runtime –Pre

4. Importare il pacchetto NuGet di archiviazione di Azure nel progetto.

		Install-Package Azure.Storage

5. Aggiungere il seguente **utilizzando** istruzioni al file di origine nel progetto.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Modificare il nome del **dello spazio dei nomi** a **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Modificare il nome della classe per **MyDotNetActivity** e derivarlo dal **IDotNetActivity** interfaccia come illustrato di seguito.

		public class MyDotNetActivity : IDotNetActivity

8. Implementare (Aggiungi) il **Execute** metodo il **IDotNetActivity** interfaccia per il **MyDotNetActivity** classe e copiare l'esempio di codice seguente al metodo.

	Il **inputTables** e **outputTables** parametri rappresentano le tabelle di input e output per l'attività come indicato dal nome. È possibile visualizzare messaggi di log utilizzando il **logger** oggetto nel file di log che è possibile scaricare dal portale di Azure oppure utilizzando i cmdlet. Il **extendedProperties** dizionario contiene l'elenco di proprietà estese, è possibile specificare nel file di JSON per l'attività e i relativi valori.

	Esempio di codice seguente conta il numero di righe nel blob di input e produce il seguente contenuto nel blob di output: percorso del blob, numero di righe nell'oggetto blob, il computer in cui l'attività è stato eseguito, data / ora corrente.

        public IDictionary<string, string> Execute(
                    IEnumerable<ResolvedTable> inputTables, 
                    IEnumerable<ResolvedTable> outputTables, 
                    IDictionary<string, string> extendedProperties, 
                    IActivityLogger logger)
        {
            string output = string.Empty;

            logger.Write(TraceEventType.Information, "Before anything...");

            logger.Write(TraceEventType.Information, "Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write(TraceEventType.Information, "<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            foreach (ResolvedTable inputTable in inputTables)
            {
                string connectionString = GetConnectionString(inputTable.LinkedService);
                string folderPath = GetFolderPath(inputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Reading blob from: {0}", folderPath);

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
                                        logger.Write(TraceEventType.Information, "First line: [{0}]", line);
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
            }

            foreach (ResolvedTable outputTable in outputTables)
            {
                string connectionString = GetConnectionString(outputTable.LinkedService);
                string folderPath = GetFolderPath(outputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Writing blob to: {0}", folderPath);

                CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
                Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

                CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
                outputBlob.UploadText(output);

            }
            return new Dictionary<string, string>();

        }

9. Aggiungere i seguenti metodi helper. Il **Execute** metodo richiama questi metodi di supporto. Il **GetConnectionString** metodo recupera la stringa di connessione di archiviazione di Azure e **GetFolderPath** metodo recupera la posizione del blob.


        private static string GetConnectionString(LinkedService asset)
        {
            AzureStorageLinkedService storageAsset;
            if (asset == null)
            {
                return null;
            }

            storageAsset = asset.Properties as AzureStorageLinkedService;
            if (storageAsset == null)
            {
                return null;
            }

            return storageAsset.ConnectionString;
        }

        private static string GetFolderPath(Table dataArtifact)
        {
            AzureBlobLocation blobLocation;
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            blobLocation = dataArtifact.Properties.Location as AzureBlobLocation;
            if (blobLocation == null)
            {
                return null;
            }

            return blobLocation.FolderPath;
        }
   


10. Compilare il progetto. Fare clic su **Build** dal menu, quindi fare clic su **Compila soluzione**.
11. Avviare **Esplora**, e passare a **bin\debug** o **bin\release** tipo a seconda della cartella di compilazione.
12. Creare un file zip **MyDotNetActivity.zip** che contengono tutti i file binari nel <project folder>nella cartella \bin\Debug.
13. Caricare **MyDotNetActivity.zip** come blob nel contenitore blob: **customactvitycontainer** di Azure nell'archiviazione blob che il **MyBlobStore** collegati servizio nel **ADFTutorialDataFactory** utilizza. Creare il contenitore di blob **blobcustomactivitycontainer** se non esiste già. 


## Passaggio 2: Utilizzare l'attività personalizzata in una pipeline
Ecco i passaggi che verranno eseguiti in questo passaggio:

1. Creare un servizio per il cluster HDInsight in cui l'attività personalizzata verrà eseguito come processo un sola mappa collegato. 
2. Creare una tabella di output della pipeline in questo esempio verrà generato.
3. Creare ed eseguire una pipeline che utilizza l'attività personalizzata creata nel passaggio 1. 
 
### Creare un servizio collegato per il cluster HDInsight che verrà utilizzato per eseguire l'attività personalizzata
Il servizio Data factory di Azure supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Quando si usa il cluster HDInsight su richiesta, viene creato un cluster per ogni sezione. Invece, se si usa il proprio cluster HDInsight, il cluster è pronto per elaborare immediatamente la sezione. Quindi, quando si usa un cluster su richiesta, i dati di output potrebbero non essere visualizzati tanto rapidamente quanto i dati nel proprio cluster.

> [AZURE.NOTE]In fase di esecuzione un'istanza di un'attività .NET viene eseguita solo su un nodo di lavoro nel cluster HDInsight. essa non può essere scalata per l'esecuzione in più nodi. Più istanze di attività .NET possono essere eseguiti in parallelo su diversi nodi del cluster HDInsight.

Se è stato esteso il [iniziare a utilizzare Azure dati Factory][adfgetstarted] esercitazione con la procedura dettagliata da [utilizzare Pig e Hive con Azure dati Factory][hivewalkthrough], è possibile ignorare la creazione di questo servizio collegato e utilizzare il servizio collegato già il ADFTutorialDataFactory.


#### Per usare un cluster HDInsight su richiesta

1. Nel **portale Azure**, fare clic su **autore e la distribuzione** nella home page di Factory di dati.
2. Fare clic nell'Editor di dati Factory **nuovo calcolo** dalla barra dei comandi e selezionare **cluster HDInsight su richiesta** dal menu.
2. Eseguire le operazioni seguenti nello script JSON: 
	1. Per il **clusterSize** proprietà, specificare le dimensioni del cluster HDInsight.
	2. Per il **jobsContainer** proprietà, specificare il nome del contenitore predefinito in cui verranno archiviati i registri cluster. Ai fini di questa esercitazione, specificare **adfjobscontainer**.
	3. Per il **timeToLive** proprietà, specificare quanto tempo il cliente può rimanere inattivo prima che venga eliminato. 
	4. Per il **versione** proprietà, specificare la versione di HDInsight si desidera utilizzare. Se si esclude questa proprietà, viene utilizzata la versione più recente.  
	5. Per il **linkedServiceName**, specificare **StorageLinkedService** era stata creata in Get esercitazione introduttiva. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.
   
#### Per usare il proprio cluster HDInsight: 

1. Nel **portale Azure**, fare clic su **autore e la distribuzione** nella home page di Factory di dati.
2. Nel **dati Factory Editor**, fare clic su **nuovo calcolo** dalla barra dei comandi e selezionare **cluster HDInsight** dal menu.
2. Eseguire le operazioni seguenti nello script JSON: 
	1. Per il **clusterUri** proprietà, immettere l'URL per il HDInsight. Ad esempio: https://<clustername>.azurehdinsight.net/     
	2. Per il **nome utente** proprietà, immettere il nome utente che ha accesso al cluster HDInsight.
	3. Per il **Password** proprietà, immettere la password dell'utente. 
	4. Per il **LinkedServiceName** proprietà, immettere **StorageLinkedService**. Si tratta del servizio collegato che era stata creata nell'esercitazione introduttiva Get. 

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il servizio collegato.

### Creare una tabella di output

1. Nel **editor dati Factory**, fare clic su **nuovo set di dati**, quindi fare clic su **archiviazione Blob di Azure** dalla barra dei comandi.
2. Sostituire lo script JSON nel riquadro di destra con il seguente script JSON:

		{
    		"name": "OutputTableForCustom",
    		"properties":
    		{
        		"location": 
        		{
					"type": "AzureBlobLocation",
					"folderPath": "adftutorial/customactivityoutput/{Slice}",
					"partitionedBy": [ { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }],

					"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1
        		}   
    		}
		}


 	Percorso di output è **customactivityoutput/adftutorial/AAAAMMGGHH/** dove AAAAMMGGHH è l'anno, mese, data e ora della sezione in fase di produzione. Vedere [di riferimento per sviluppatori][adf-developer-reference] per informazioni dettagliate.

2. Fare clic su **Distribuisci** sulla barra dei comandi per la distribuzione della tabella.


### Creare ed eseguire una pipeline che utilizza l'attività personalizzata
   
1. Fare clic nell'Editor di dati Factory **nuova pipeline** sulla barra dei comandi. Se non viene visualizzato il comando, fare clic su **... (Puntini di sospensione)** Per visualizzarla. 
2. Sostituire il JSON nel riquadro di destra con il seguente script JSON. Se si desidera utilizzare il proprio cluster e seguiti i passaggi per creare il **HDInsightLinkedService** collegato del servizio, sostituire **HDInsightOnDemandLinkedService** con **HDInsightLinkedService** in JSON seguente. 

		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyDotNetActivity",
                     	"Type": "DotNetActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "HDInsightLinkedService",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyDotNetActivity.dll",
                            "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
                            "ExtendedProperties":
							{
								"SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
							}
                      	},
                        "Policy":
                        {
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

	> [AZURE.NOTE]Sostituire **valore StartDateTime** valore con tre giorni prima della data odierna e **EndDateTime** valore con il giorno corrente. Il valore StartDateTime ed EndDateTime devono trovarsi in [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. La tabella di output viene pianificata per essere generata tutti i giorni, quindi saranno generate tre sezioni.

	Tenere presente quanto segue:

	- Esiste un'attività nella sezione attività ed è di tipo: **DotNetActivity**.
	- Utilizzare la stessa tabella di input **EmpTableFromBlob** utilizzato Get esercitazione introduttiva.
	- Utilizzare una nuova tabella di output **OutputTableForCustom** che verrà creato nel passaggio successivo.
	- **AssemblyName** è impostato sul nome della DLL: **MyActivities.dll**.
	- **EntryPoint** è impostato su **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** è impostato su **MyBlobStore** che è stato creato come parte dell'esercitazione da [iniziare a utilizzare Azure dati Factory][adfgetstarted]. Questo archivio BLOB contiene il file ZIP dell'attività personalizzata.
	- **PackageFile** è impostato su **customactivitycontainer/MyDotNetActivity.zip**.
     
4. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline.
8. Verificare che i file di output vengono generati nell'archiviazione blob nel **adftutorial** contenitore.

	![l'output di attività personalizzata][image-data-factory-ouput-from-custom-activity]

9. Se si apre il file di output, l'output visualizzato dovrebbe essere simile al seguente:
	
	adftutorial/,Emp.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(posizione del blob), (nome del blob) (numero di righe nell'oggetto blob), (nodo su cui è stato eseguito l'attività), (timestamp Data)

10.	Utilizzare il [portale Azure][azure-preview-portal] o i cmdlet PowerShell di Azure per monitorare la factory di dati, la pipeline e il set di dati. È possibile visualizzare i messaggi provenienti dal **ActivityLogger** nel codice per l'attività personalizzata nei registri è possibile scaricare dal portale oppure utilizzando i cmdlet.

	![scaricare i log dall'attività personalizzata][image-data-factory-download-logs-from-custom-activity]
   
Vedere [iniziare a utilizzare Azure dati Factory][adfgetstarted] per i passaggi dettagliati per il monitoraggio di set di dati e pipeline.
    
## <a name="AzureBatch"></a> Utilizzo di Batch di Azure collegati servizio 
> [AZURE.NOTE]Vedere [Panoramica tecnica di Azure Batch][batch-technical-overview] per una panoramica del Batch di Azure service e vedere [Introduzione alla libreria di Batch di Azure per .NET][batch-get-started] per iniziare rapidamente con il servizio di Azure Batch.

Ecco i passaggi generali per l'utilizzo del servizio collegato del Batch di Azure nella procedura dettagliata descritta nella sezione precedente:

1. Creare un account Azure Batch tramite il portale di gestione di Azure. Vedere [Panoramica tecnica di Azure Batch][batch-create-account] articolo per istruzioni. Annotare la chiave account e il nome dell'account Azure Batch. 

	È inoltre possibile utilizzare [Nuovo AzureBatchAccount][new-azure-batch-account] cmdlet per creare un account Azure Batch. Vedere [utilizzando Azure PowerShell per gestire Account Batch Azure][azure-batch-blog] per istruzioni dettagliate sull'utilizzo di questo cmdlet. 
2. Creare un pool di Batch di Azure. È possibile scaricare e utilizzare il [strumento Explorer Batch Azure][batch-explorer] (o) utilizzare [libreria di Batch di Azure per .NET][batch-net-library] per creare un pool di Batch di Azure. Vedere [scenario di esempio Azure Batch Explorer][batch-explorer-walkthrough] per istruzioni dettagliate per l'utilizzo di Esplora risorse di Batch di Azure.
	
	È inoltre possibile utilizzare [Nuovo AzureBatchPool][new-azure-batch-pool] cmdlet per creare un pool di Batch di Azure.

2. Creare un servizio collegato Batch Azure utilizzando il seguente modello JSON. Factory Editor dati vengono visualizzati per iniziare con un modello simile. Specificare il nome dell'account Azure Batch, il nome di chiave e il pool di account nel frammento JSON.

		{
		    "name": "AzureBatchLinkedService",
		    "properties": {
		        "type": "AzureBatchLinkedService",
		        "accountName": "<Azure Batch account name>",
		        "accessKey": "<Azure Batch account key>",
		        "poolName": "<Azure Batch pool name>",
		        "linkedServiceName": "<Specify associated storage linked service reference here>"
		  }
		}

	Vedere [argomento Azure Batch collegato servizio MSDN](https://msdn.microsoft.com/library/mt163609.aspx) per le descrizioni di queste proprietà.

2.  Nell'Editor di Data Factory, aprire la definizione di JSON per la pipeline creato nella procedura dettagliata e sostituire **HDInsightLinkedService** con **AzureBatchLinkedService**.
3.  È possibile modificare l'ora di inizio e fine per la pipeline in modo da poter testare lo scenario con il servizio di Azure Batch. 
4.  È possibile visualizzare le attività di Azure Batch associate all'elaborazione le sezioni in Azure Batch Explorer come illustrato nel diagramma seguente.

	![Attività Batch Azure][image-data-factory-azure-batch-tasks]

## Vedere anche

[Factory gli aggiornamenti dei dati di azure: attività di esecuzione personalizzate per ADF .NET utilizzando Azure Batch](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

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

<!---HONumber=GIT-SubDir--> 