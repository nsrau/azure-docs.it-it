<properties 
	pageTitle="Creazione, monitoraggio e gestione delle istanze di Data factory di Azure mediante Data Factory SDK" 
	description="Informazioni su come creare, monitorare e gestire a livello di codice le istanze di Data factory di Azure usando Data Factory SDK." 
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
	ms.date="07/22/2015" 
	ms.author="spelluru"/>

# Creazione, monitoraggio e gestione delle istanze di Data factory di Azure mediante Data Factory .NET SDK
## Panoramica
È possibile creare, monitorare e gestire le istanze di Data factory di Azure a livello di codice mediante Data Factory .NET SDK. Questo articolo contiene una procedura dettagliata per la creazione di un'applicazione console .NET di esempio che crea e monitora un'istanza di Data factory. Per informazioni dettagliate su Data Factory .NET SDK, vedere la sezione di [riferimento sulla libreria di classi di Data factory][adf-class-library-reference].



## Prerequisiti

- Visual Studio 2012 o 2013
- Scaricare e installare [Azure .NET SDK][azure-developer-center].
- Scaricare e installare i pacchetti NuGet per Data factory di Azure. Le istruzioni sono disponibili nella procedura dettagliata.

## Procedura dettagliata
1. Usando Visual Studio 2012 o 2013, creare un'applicazione console .NET in C#.
	<ol type="a">
	<li>Avviare <b>Visual Studio 2012</b> o <b>Visual Studio 2013</b>.</li>
	<li>Fare clic su <b>File</b>, scegliere <b>Nuovo</b> e quindi fare clic su <b>Progetto</b>.</li> 
	<li>Espandere <b>Modelli</b> e quindi selezionare <b>Visual C#</b>. In questa procedura dettagliata viene usato C#, ma è possibile usare qualsiasi linguaggio .NET.</li> 
	<li>Selezionare <b>Applicazione console</b> dall'elenco dei tipi di progetto a destra.</li>
	<li>Immettere <b>DataFactoryAPITestApp</b> come <b>Nome</b>.</li> 
	<li>Selezionare <b>C:\ADFGetStarted</b> per <b>Percorso</b>.</li>
	<li>Fare clic su <b>OK</b> per creare il progetto.</li>
</ol>
2. Fare clic su <b>Strumenti</b>, scegliere <b>Gestione pacchetti NuGet</b> e quindi fare clic su <b>Console di Gestione pacchetti</b>.
3.	In <b>Console di Gestione pacchetti</b> eseguire i comandi seguenti uno alla volta.</b> 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Aggiungere la sezione **appSettings** seguente al file **App.config**. Queste impostazioni sono usate dal metodo helper: **GetAuthorizationHeader**. 

	Sostituire i valori per **SubscriptionId** e **ActiveDirectoryTenantId** con gli ID della sottoscrizione Azure e del tenant. È possibile ottenere questi valori eseguendo **Get-AzureAccount** da Azure PowerShell. Potrebbe essere necessario eseguire prima l'accesso mediante Add-AzureAccount.
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="<subscription ID>" />
    		<add key="ActiveDirectoryTenantId" value="<tenant ID" />
		</appSettings>
6. Aggiungere le istruzioni **using** seguenti al file di origine (Program.cs) nel progetto.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
		
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Common.Models;
		
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure;
6. Aggiungere al metodo **Main** il codice seguente che crea un'istanza della classe **DataPipelineManagementClient**. Si userà questo oggetto per creare un'istanza di Data factory, un servizio collegato, tabelle di input e di output e una pipeline. Sarà anche possibile usare l'oggetto per monitorare le sezioni di una tabella in fase di esecuzione.    

        // create data factory management client
        string resourceGroupName = "resourcegroupname";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

	> [AZURE.NOTE]Sostituire **resourcegroupname** con il nome del gruppo di risorse di Azure. Per creare un gruppo di risorse, usare il cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx).

7. Aggiungere al metodo **Main** il codice seguente che crea una **data factory**.

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );

8. Aggiungere al metodo **Main** il codice seguente che crea un **servizio collegato**.
	> [AZURE.NOTE]Usare il **nome account** e la **chiave dell'account** dell'account di archiviazione di Azure per **ConnectionString**.

        // create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new LinkedServiceProperties
                    (
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=spestore;AccountKey=4VwviDOId32nYKABQy9NHsMG0vC/CXx9iuR02HJdGL+0kieqHqbT3ap+bM/c+aGnGoA7SqkwNFq90hqV1bmV0w==")
                    )
                }
            }
        );
9. Aggiungere al metodo **Main** il codice seguente che crea **tabelle di input e output**. 

	Tenere presente che **FolderPath** per il BLOB di input è impostato su **adftutorial/**, dove **adftutorial** è il nome del contenitore nell'archivio BLOB. Se questo contenitore non esiste nell'archivio BLOB di Azure, creare un contenitore con il nome **adftutorial** e caricare un file di testo nel contenitore.
	
	Si noti che FolderPath per il BLOB di output è impostato su **adftutorial/apifactoryoutput/{Slice}** dove il valore **Slice** è calcolato in modo dinamico in base al valore **SliceStart** (data-ora di inizio di ogni sezione).

 
        // create input and output tables
        Console.WriteLine("Creating input and output tables");
        string Table_Source = "TableBlobSource";
        string Table_Destination = "TableBlobDestination";

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Source,
                    Properties = new TableProperties()
                    {
                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Destination,
                    Properties = new TableProperties()
                    {

                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            }
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });

11. Aggiungere al metodo **Main** il codice seguente che **crea e attiva una pipeline**. Questa pipeline contiene una proprietà **CopyActivity** che accetta **BlobSource** come origine e **BlobSink** come sink.

            // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<Activity>()
                        {                                
                            new Activity()
                            {   
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput() {
                                        Name = Table_Source
                                    }
                                },
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination
                                    }
                                },
                                TypeProperties = new CopyActivity()
                                {
                                    Source = new BlobSource(),
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    }
                                }
                            }

                        },
                    }
                }
            });

	

12. Aggiungere alla classe **Program** il metodo helper seguente usato per il metodo **Main**. Tramite questo metodo viene visualizzata una finestra di dialogo che consente di specificare il **nome utente** e la **password** usati per accedere al portale di Azure.
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  
 
13. Aggiungere al metodo **Main** il codice seguente per ottenere lo stato di una sezione di dati della tabella di output. In questo esempio è prevista solo una sezione.
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Table_Destination,
                new DataSliceListParameters()
                {
                    DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                    DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                });

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.State);
                }
            }
        }

14. **(facoltativo)** Aggiungere al metodo **Main** il codice seguente per ottenere i dettagli dell'esecuzione di una sezione di dati.

        Console.WriteLine("Getting run details of a data slice");

		// give it a few minutes for the output slice to be ready
        Console.ReadKey();

        var datasliceRunListResponse = client.DataSliceRuns.List(
                resourceGroupName,
                dataFactoryName,
                Table_Destination,
                new DataSliceRunListParameters()
                {
                    DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                }
            );
        
        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();

15. Compilare l'applicazione console. Scegliere **Compila** dal menu e fare clic su **Compila soluzione**. Se viene visualizzato un errore relativo alla classe **ConfigurationManager**, aggiungere un riferimento all'assembly **System.Configuration** e provare a eseguire nuovamente la compilazione.
16. Verificare che esista almeno un file nel contenitore adftutorial nell'archiviazione BLOB di Azure. In caso contrario, creare il file Emp.txt nel Blocco note con il contenuto seguente e caricarlo nel contenitore adftutorial.

        John, Doe
		Jane, Doe
	 
17. Eseguire l'esempio scegliendo **Debug** -> **Avvia debug** dal menu. Quando viene visualizzato un messaggio simile ad **Acquisizione dettagli dell'esecuzione di una sezione di dati**, attendere qualche minuto e premere **INVIO**.
18. Usare il portale di anteprima di Azure per verificare che la data factory **APITutorialFactory** venga creata con gli elementi seguenti: 
	- Servizio collegato: **LinkedService\_AzureStorage**. 
	- Tabelle: **TableBlobSource** e **TableBlobDestination**.
	- Pipeline: **PipelineBlobSample**. 
18. Verificare che venga creato un file di output nella cartella **apifactoryoutput** nel contenitore **adftutorial**.


## Vedere anche

Articolo | Descrizione
------ | ---------------
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per la libreria di classi .NET, i cmdlet, lo script JSON, le funzioni e così via. 


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/
 

<!---HONumber=August15_HO6-->