<properties title="Use custom activities in an Azure Data Factory pipeline" pageTitle="Usare attività personalizzate in una pipeline di Data factory di Azure" description="Learn how to create custom activities and use them in an Azure Data Factory pipeline." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Usare attività personalizzate in una pipeline di Data factory di Azure
Data factory di Azure supporta attività predefinite come **l'attività di copia** e **l'attività HDInsight** da usare nelle pipeline per spostare ed elaborare i dati. È anche possibile creare un'attività personalizzata con la propria logica di trasformazione/elaborazione e usare l'attività in una pipeline. L'attività personalizzata viene eseguita come processo solo di mapping in un cluster HDInsight, quindi è necessario collegare un cluster HDInsight per l'attività personalizzata nella pipeline.
 
Questo articolo descrive come creare un'attività personalizzata e usarla in una pipeline di Data factory di Azure. Fornisce anche una procedura dettagliata con istruzioni complete per creare e usare un'attività personalizzata.

## Creazione di un'attività personalizzata

Per creare un'attività personalizzata:
 
1.	Creare un progetto **Libreria di classi** in Visual Studio 2013.
2.	Scaricare il [pacchetto NuGet per Data factory di Azure][nuget-package] e installarlo. Le istruzioni per l'installazione si trovano nella procedura dettagliata.
3. Aggiungere quanto segue usando le istruzioni nella parte superiore del file di origine nella libreria di classi.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Aggiornare la classe per implementare l'interfaccia **ICustomActivity**.
	<ol type='a'>
		<li>
			Derivare la classe da <b>ICustomActivity</b>.
			<br/>
			Esempio: <br/>
			classe pubblica <b>MyCustomActivity : ICustomActivity</b>
		</li>

		<li>
			Implementare il metodo  <b>Execute</b> dell'interfaccia <b>ICustomActivity</b> 
		</li>

	</ol>
5. Compilare il progetto.


## Usare l'attività personalizzata in una pipeline
Per usare l'attività personalizzata in una pipeline:

1.	**Comprimere** tutti i file binari dalle cartelle di output **bin\debug** o **bin\release** per il progetto. 
2.	**Caricare il file ZIP** come BLOB nell'**archiviazione BLOB di Azure**. 
3.	Aggiornare il file **JSON della pipeline** affinché faccia riferimento al file ZIP, il DLL dell'attività personalizzata, la classe dell'attività e il BLOB che contiene il file ZIP nel JSON della pipeline. Nel file JSON:
	<ol type ="a">
Il 		<li><b>tipo di attività</b> deve essere impostare su <b>CustomActivity</b>.</li>
		<li><b>AssemblyName</b> è il nome del DLL di output dal progetto di Visual Studio.</li>
		<li><b>EntryPoint</b> specifica lo <b>spazio dei nomi</b> e il <b>nome</b> per la <b>classe</b> che implementa l'interfaccia <b>ICustomActivity</b>.</li>
		<li><b>PackageLinkedService</b> è il servizio collegato che fa riferimento al BLOB che contiene il file ZIP. </li>
		<li><b>PackageFile</b> specifica il percorso e il nome del file ZIP caricato nell'archiviazione BLOB di Azure.</li>
		<li><b>LinkedServiceName</b> è il nome del servizio collegato che collega un cluster HDInsight (su richiesta o proprio) a un'istanza di Data factory. L'attività personalizzata viene eseguita come processo solo di mapping nel cluster HDInsight specificato.</li>
	</ol>

	

	**Esempio JSON parziale**

		"Name": "MyCustomActivity",
    	"Type": "CustomActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyCustomActivity.dll",
    	    "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyCustomActivity.zip",

## Per aggiornare l'attività personalizzata
Se si aggiorna il codice per l'attività personalizzata, si compila e si carica il file ZIP che contiene i nuovi file binari per l'archiviazione BLOB, è necessario sostituire la pipeline esistente che usa l'attività personalizzata eseguendo **New-AzureDataFactoryPipeline**. Quando si crea una pipeline che usa l'attività personalizzata, il file ZIP viene copiato dall'archiviazione BLOB specificata in un contenitore di Data factory di Azure nell'archiviazione BLOB collegata al cluster HDInsight. Questa operazione viene eseguita solo alla creazione della pipeline. Quindi, è necessario ricreare la pipeline quando si aggiorna l'attività personalizzata. 

La seguente procedura dettagliata fornisce le istruzioni complete per creare un'attività personalizzata e usare l'attività in una pipeline di Data factory di Azure. Questa procedura dettagliata estende l'esercitazione in [Introduzione a Data factory di Azure][adfgetstarted]. Per vedere l'attività personalizzata in funzione, è necessario completare prima l'esercitazione di introduzione, quindi passare a questa procedura dettagliata. 

## Procedura dettagliata
**Prerequisiti:**


- Esercitazione in [Introduzione a Data factory di Azure][adfgetstarted]. È necessario completare l'esercitazione in questo articolo prima di continuare con questa procedura dettagliata.
- Visual Studio 2012 o 2013
- Scaricare e installare [Microsoft Azure .NET SDK][azure-developer-center]
- Scaricare i [pacchetti NuGet per Data factory di Azure][nuget-package].
- Scaricare e installare il pacchetto NuGet per l'archiviazione di Azure. Le istruzioni sono contenute nella procedura dettagliata, quindi è possibile ignorare questo passaggio.

### Passaggio 1: Creare un'attività personalizzata

1.	Creare un progetto della libreria di classi .NET.
	<ol type="a">
		<li>Avviare <b>Visual Studio 2012</b> o <b>Visual Studio 2013</b>.</li>
		<li>Fare clic su <b>File</b>, <b>Nuovo</b>, quindi fare clic su <b>Progetto</b>.</li> 
		<li>Espandere <b>Modelli</b>, quindi selezionare <b>Visual C#</b>. In questa procedura dettagliata viene usato C#, ma è possibile usare qualsiasi linguaggio .NET per sviluppare l'attività personalizzata.</li> 
		<li>Selezionare <b>Libreria di classi</b> dall'elenco dei tipi di progetto sulla destra.</li>
		<li>Immettere <b>MyCustomActivity</b> per il <b>nome</b>.</li> 
		<li>Selezionare <b>C:\ADFGetStarted</b> per il <b>percorso</b>.</li>
		<li>Fare clic su <b>OK</b> per creare il progetto.</li>
	</ol>
2.  Fare clic su <b>Strumenti</b>, <b>Gestione pacchetti NuGet</b>, quindi fare clic su <b>Console di Gestione pacchetti</b>.
3.	Nella <b>Console di Gestione pacchetti</b>, eseguire il seguente comando per importare <b>Microsoft.Azure.Management.DataFactories</b> scaricato in precedenza. Sostituire la cartella con il percorso contenente il pacchetto NuGet di Data factory scaricato.

		Install-Package Microsoft.Azure.Management.DataFactories -Source d:\packages -Pre

3.	Nella <b>Console di Gestione pacchetti</b>, eseguire il seguente comando per importare <b>Microsoft.DataFactories.Runtime</b>. Sostituire la cartella con il percorso contenente il pacchetto NuGet di Data factory scaricato.

		Install-Package Microsoft.DataFactories.Runtime -Source d:\packages -Pre

4. Importare il pacchetto NuGet di Archiviazione di Microsoft Azure nel progetto.

		Install-Package WindowsAzure.Storage

5. Aggiungere le seguenti istruzioni **using** al file di origine nel progetto.

		using System.IO;
		using System.Globalization;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Modificare il nome dello **spazio dei nomi** in **MyCustomActivityNS**.

		namespace MyCustomActivityNS

7. Modificare il nome della classe in **MyCustomActivity** e derivarlo dall'interfaccia **ICustomActivity** come mostrato di seguito.

		public class MyCustomActivity : ICustomActivity

8. Implementare (aggiungere) il metodo **Execute** dell'interfaccia **ICustomActivity** nella classe **MyCustomActivity** e copiare il seguente codice di esempio nel metodo. 

	I parametri **inputTables** e **outputTables** rappresentano le tabelle di input e di output per l'attività, come suggeriscono i nomi. È possibile visualizzare i messaggi registrati usando l'oggetto **logger** nel file di log che è possibile scaricare dal portale di Azure o usando i cmdlet. Il dizionario **extendedProperties** contiene l'elenco delle proprietà estese specificate nel file JSON per l'attività e i relativi valori. 

	Il seguente codice di esempio conta il numero di righe nel BLOB di input e genera il seguente contenuto nel BLOB di output: percorso al BLOB, numero di righe nel BLOB, computer in cui viene eseguita l'attività, data/ora corrente.

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
										"{0},{1},{2},{3}\n", 
										folderPath, 
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

9. Aggiungere i seguenti metodi helper. Il metodo **Execute** richiama questi metodi helper. Il metodo **GetConnectionString** recupera la stringa di connessione dell'archiviazione di Azure, mentre il metodo **GetFolderPath** recupera il percorso BLOB. 


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
   


10. Compilare il progetto. Fare clic su **Compila** dal menu e scegliere **Compila soluzione**.
11. Avviare **Esplora risorse** e passare alla cartella **bin\debug** o **bin\release** a seconda del tipo di compilazione.
12. Creare un file ZIP **MyCustomActivity.zip** che contiene tutti i file binari nella cartella <cartella del progetto>\bin\Debug.
	![zip output binaries][image-data-factory-zip-output-binaries]
13. Caricare **MyCustomActivity.zip** come BLOB nel contenitore BLOB: **customactvitycontainer** nell'archiviazione BLOB di Azure usato dal servizio collegato **MyBlobStore** in **ADFTutorialDataFactory**.  Creare il contenitore BLOB **blobcustomactivitycontainer** se non è già presente. 
    ![upload zip to blob][image-data-factory-upload-zip-to-blob]

### Creare un servizio collegato per il   cluster HDInsight da usare per eseguire l'attività personalizzata
Il servizio Data factory di Azure supporta la creazione di un cluster su richiesta e lo usa per elaborare l'input per generare i dati di output. È anche possibile usare il proprio cluster per eseguire la stessa operazione. Quando si usa il cluster HDInsight su richiesta, viene creato un cluster per ogni sezione. Invece, se si usa il proprio cluster HDInsight, il cluster è pronto per elaborare immediatamente la sezione. Quindi, quando si usa un cluster su richiesta, i dati di output potrebbero non essere visualizzati tanto rapidamente quanto i dati nel proprio cluster. Ai fini dell'esempio, viene usato un cluster su richiesta. 

> [WACOM.NOTE] Se l'esercitazione in [Introduzione a Data factory di Azure][adfgetstarted] è stata estesa con la procedura dettagliata di [Usare Pig e Hive con Data factory di Azure][hivewalkthrough], è possibile ignorare la creazione di questo servizio collegato e usare il servizio collegato già disponibile in ADFTutorialDataFactory. 

#### Per usare un cluster HDInsight su richiesta

1. Creare un file JSON denominato **HDInsightOnDemandCluster.json** con il seguente contenuto e salvarlo nella cartella **C:\ADFGetStarted\Custom**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": 4,
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Avviare **Azure PowerShell** ed eseguire il seguente comando per passare in modalità **AzureResourceManager**. I cmdlet di Data factory di Azure sono disponibili in modalità **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Passare alla cartella **C:\ADFGetstarted\Custom**.
4. Eseguire il seguente comando per creare il servizio collegato per il cluster HDInsight su richiesta.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
   
#### Per usare il proprio cluster HDInsight: 

1. Creare un file JSON denominato **MyHDInsightCluster.json** con il seguente contenuto e salvarlo nella cartella **C:\ADFGetStarted\Custom**. Sostituire clustername, username e password con i valori appropriati prima di salvare il file JSON.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Avviare **Azure PowerShell** ed eseguire il seguente comando per passare in modalità **AzureResourceManager**. I cmdlet di Data factory di Azure sono disponibili in modalità **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Passare alla cartella **C:\ADFGetstarted\Custom**.
4. Eseguire il seguente comando per creare il servizio collegato per il cluster HDInsight su richiesta.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json



### Passaggio 2: Usare l'attività personalizzata in una pipeline
Estendere l'esercitazione in [Introduzione a Data factory di Azure][adfgetstarted] per creare un'altra pipeline per testare questa attività personalizzata.

#### Creare un servizio collegato per il cluster HDInsight da usare per eseguire l'attività personalizzata


1.	Creare un JSON per la pipeline come mostrato nell'esempio seguente e salvarlo come **ADFTutorialPipelineCustom.json** nella cartella **C:\ADFGetStarted\Custom**. Modificare il nome di **LinkedServiceName** nel nome del cluster HDInsight (**HDInsightOnDemandCluster** o **MyHDInsightCluster**)


		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyCustomActivity",
                     	"Type": "CustomActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "MyHDInsightCluster",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyCustomActivity.dll",
                            "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyCustomActivity.zip",
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
				]
			}
		}

	Tenere presente quanto segue: 

	- Esiste una sola attività nella sezione delle attività ed è di tipo: **CustomActivity**.
	- Usare la stessa tabella di input **EmpTableFromBlob** usata nell'esercitazione di introduzione.
	- Usare una nuova tabella di output **OutputTableForCustom** da creare nel passaggio successivo.
	- **AssemblyName** è impostato sul nome del DLL: **MyActivities.dll**.
	- **EntryPoint** è impostato su **MyCustomActivityNS.MyCustomActivity**.
	- **PackageLinkedService** è impostato su **MyBlobStore**, creato durante l'esercitazione in [Introduzione a Data factory di Azure][adfgetstarted]. Questo archivio BLOB contiene il file ZIP dell'attività personalizzata.
	- **PackageFile** è impostato su **customactivitycontainer/MyCustomActivity.zip**.
     

4. Creare un file JSON per la tabella di output (**OutputTableForCustom** a cui fa riferimento il JSON della pipeline) e salvarlo come C:\ADFGetStarted\Custom\OutputTableForCustom.json.

		

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

 	Il percorso di output è **adftutorial/customactivityoutput/YYYYMMDDHH/** dove YYYYMMDDHH corrisponde ad anno, mese, data e ora della sezione generata. Vedere la [guida di riferimento per gli sviluppatori][adf-developer-reference] per i dettagli. 

5. Eseguire il seguente comando per **creare la tabella di output** in **ADFTutorialDataFactory**.
		
		

		New-AzureDataFactoryTable  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\OutputTableForCustom.json -ResourceGroupName ADFTutorialResourceGroup


6. A questo punto, eseguire il seguente comando per **creare la pipeline**. Il file JSON della pipeline è stato creato in un passaggio precedente.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\ADFTutorialPipelineCustom.json -ResourceGroupName ADFTutorialResourceGroup



7. Eseguire il seguente comando PowerShell per **impostare il periodo attivo** nella pipeline creata.

		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineCustom

	> [WACOM.NOTE] Sostituire il valore di **StartDateTime** con il giorno corrente e il valore di **EndDateTime** con il giorno seguente. StartDateTime ed EndDateTime sono orari UTC e devono essere in formato [ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. **EndDateTime** è facoltativo, ma in questa esercitazione verrà usato. 
	> Se non si specifica **EndDateTime**, viene calcolato come "**StartDateTime + 48 ore**". Per eseguire la pipeline illimitatamente, specificare **9/9/9999** come **EndDateTime**.



8. Verificare che i file di output vengano generati nell'archiviazione BLOB nel contenitore **adftutorial**.

	![output from custom activity][image-data-factory-ouput-from-custom-activity]

9. Se si apre il file di output, l'output visualizzato dovrebbe essere simile al seguente:
	
	adftutorial/input,2,WORKERNODE0,10/07/2014 18:34:33 

	(percorso BLOB), (numero di righe nel BLOB), (nodo in cui è stata eseguita l'attività), (indicatore data/ora)

10.	Usare il [portale di Azure][azure-preview-portal] o il cmdlet di Azure PowerShell per monitorare l'istanza di Data factory, le pipeline e i set di dati. I messaggi possono essere visualizzati da **ActivityLogger** nel codice per l'attività personalizzata nei log scaricabili dal portale o tramite cmdlet.

	![download logs from custom activity][image-data-factory-download-logs-from-custom-activity]
   
Vedere [Introduzione a Data factory di Azure][adfgetstarted] per i passaggi dettagliati per il monitoraggio dei set di dati e delle pipeline.      
    
## Vedere anche

Articolo | Descrizione
------ | ---------------
[Consentire alle pipeline di usare dati locali][use-onpremises-datasources] | Questo articolo contiene una procedura dettagliata che mostra come copiare dati da un database SQL Server locale a un BLOB di Azure.
[Usare Pig e Hive con Data factory][use-pig-and-hive-with-data-factory] | Questo articolo contiene una procedura dettagliata che mostra come usare l'attività HDInsight per eseguire uno script hive/pig per elaborare i dati di input in modo da generare i dati di output. 
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | Questo articolo fornisce una procedura dettagliata end-to-end che mostra come implementare uno scenario quasi reale usando Data factory di Azure per trasformare i dati da file di log a informazioni accurate.
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'attività personalizzata e usarla in una pipeline. 
[Monitorare e gestire Data factory di Azure con Azure PowerShell][monitor-manage-using-powershell] | Questo articolo descrive come monitorare Data factory di Azure con i cmdlet di Azure PowerShell. È possibile provare gli esempi dell'articolo in ADFTutorialDataFactory.
[Risolvere i problemi di Data factory][troubleshoot] | Questo articolo descrive come risolvere i problemi di Data factory di Azure. È possibile provare la procedura dettagliata di questo articolo in ADFTutorialDataFactory introducendo un errore (eliminando la tabella nel database SQL di Azure). 
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via. 


[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456




[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/it-it/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: ../data-factory-get-started
[hivewalkthrough]: ../data-factory-pig-hive-activities

[image-data-factory-zip-output-binaries]: ./media/data-factory-use-custom-activities/ZipOuputBinaries.png

[image-data-factory-upload-zip-to-blob]: ./media/data-factory-use-custom-activities/UploadZipToBlob.png

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
