<properties title="Create, monitor, and manage Azure data factories using Data Factory SDK" pageTitle="Creazione, monitoraggio e gestione delle istanze di Data factory di Azure mediante SDK per Data factory." description="Informazioni su come creare, monitorare e gestire a livello di codice le data factory di Azure usando Data Factory SDK." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="spelluru" />

# Creazione, monitoraggio e gestione delle istanze di Data factory di Azure mediante .NET SDK per Data factory.
È possibile creare, monitorare e gestire le istanze di Data factory di Azure a livello di codice mediante .NET SDK per Data factory. Questo articolo contiene una procedura dettagliata per la creazione di un'applicazione console .NET di esempio che crea e monitora un'istanza di Data factory. Per informazioni dettagliate su .NET SDK per Data factory, vedere la pagina relativa al [riferimento alla libreria di classi di Data factory][adf-class-library-reference]. 

## Procedura dettagliata: Creazione di un'istanza di Data factory di Azure mediante .NET SDK per Data factory

**Prerequisiti:**

- Visual Studio 2012 o 2013
- Scaricare e installare [Microsoft Azure .NET SDK][azure-developer-center]
- Scaricare e installare i pacchetti NuGet per Data factory di Azure. Le istruzioni sono disponibili nella procedura dettagliata.

### Passaggio 1: Creazione di un'istanza di Data factory di Azure mediante .NET SDK per Data factory
1. Usando Visual Studio 2012 o 2013, creare un'applicazione console .NET in C#.
	<ol type="a">
		<li>Avviare <b>Visual Studio 2012</b> oppure <b>Visual Studio 2013</b>.</li>
		<li>Fare clic su <b>File</b>, scegliere <b>Nuovo</b>e selezionare <b>Progetto</b>.</li> 
		<li>Espandere <b>Modelli</b>e selezionare <b>Visual C#</b>. In questa procedura dettagliata viene usato C#, ma è possibile usare qualsiasi linguaggio .NET.</li> 
		<li>Selezionare <b>Applicazione console</b> dall'elenco dei tipi di progetto a destra.</li>
		<li>Immettere <b>DataFactoryAPITestApp</b> in <b>Nome</b>.</li> 
		<li>Selezionare <b>C:\ADFGetStarted</b> in <b>Percorso</b>.</li>
		<li>Fare clic su <b>OK</b> per creare il progetto.</li>
	</ol>
2. Fare clic su <b>Strumenti</b>, scegliere <b>Gestione pacchetti NuGet</b>e selezionare <b>Console di gestione pacchetti</b>.
3.	Nella <b>Console di gestione pacchetti</b>eseguire i comandi seguenti uno alla volta.</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories -Pre
		Install-Package Microsoft.DataFactories.Runtime -Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Aggiungere la sezione **appSetttings** seguente al file **App.config**. Queste impostazioni sono usate dal metodo helper: **GetAuthorizationHeader**. 

	Sostituire i valori di **SubscriptionId** e **ActiveDirectoryTenantId** con l'ID sottoscrizione e l'ID tenant di Azure. È possibile ottenere questi valori eseguendo **Get-AzureAccount** da Azure PowerShell. Potrebbe essere necessario eseguire prima l'accesso mediante Add-AzureAccount.
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="49fb6e5f-3098-4fb2-ba2f-6d6eed843a65" />
    		<add key="ActiveDirectoryTenantId" value="37330244-7828-4a28-99b7-c8c3a437c7ac" />
		</appSettings>
6. Aggiungere le istruzioni **using** seguenti al file di origine (Program.cs) nel progetto.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.WindowsAzure; 
6. Aggiungere al metodo **Main** il codice seguente che crea un'istanza della classe **DataPipelineManagementClient**. Si userà questo oggetto per creare un'istanza di Data factory, un servizio collegato, tabelle di input e di output e una pipeline. Sarà anche possibile usare l'oggetto per monitorare le sezioni di una tabella in fase di esecuzione.    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. Add the following code that creates a **data factory** to the **Main** method.

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
	> [WACOM.NOTE] Usare il **nome account** e la **chiave** dell'account di archiviazione di Azure per la stringa **ConnectionString**. 

		// create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new AzureStorageLinkedService()
                    {
                        ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=account key",
                    }
                }
            }
        );
9. Aggiungere al metodo **Main** il codice seguente che crea **tabelle di input e output**. 

	Tenere presente che **FolderPath** per il BLOB di input è impostato su **adftutorial/** dove **adftutorial** è il nome del contenitore nell'archiviazione BLOB. Se questo contenitore non esiste nell'archiviazione BLOB di Azure, creare un contenitore con il nome **adftutorial** e caricare un file di testo nel contenitore.
	
	Tenere presente che FolderPath per il BLOB di output è impostato su: **adftutorial/apifactoryoutput/{Slice}** dove il valore **Slice** è calcolato in modo dinamico in base al valore **SliceStart** (data-ora di inizio di ogni sezione).  

 
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
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/",
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                            WaitOnExternal = new WaitOnExternal()
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
                        Location = new AzureBlobLocation()
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
                            },
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });
10. Aggiungere al metodo **Main** il codice seguente che **crea e attiva una pipeline**. Questa pipeline contiene una proprietà **CopyActivity** che accetta **BlobSource** come origine e **BlobSink** come sink. 

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

                        Activities = new List<BaseActivity>()
                        {
                            new CopyActivity()
                            {
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput()
                                    {
                                        Name = Table_Source,
                                    }
                                },
                        
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination, 
                                    }
                                },
                     
                                Transformation = new CopyActivityProperties()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                            
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    },

                                },

                                Policy = new ActivityPolicy()
                                {
                                    ExecutionPriorityOrder = ExecutionPriorityOrder.NewestFirst,
                                    Concurrency = 1,
                                    Retry = 2,
                                    Timeout = TimeSpan.FromMinutes(10),
                                }
                            }

                        },
                    }
                }
            });

11. Aggiungere alla classe **Program** il metodo helper seguente usato per il metodo **Main**. Tramite questo metodo viene visualizzata una finestra di dialogo che consente di fornire il **nome utente** e la **password** usati per accedere al portale di Azure. 
 
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
 
13. Aggiungere il codice seguente al metodo **Main** per ottenere lo stato di una sezione di dati della tabella di output. In questo esempio è prevista solo una sezione.   
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString());

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.Status == DataSliceStatus.Failed || slice.Status == DataSliceStatus.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.Status);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.Status);
                }
            }
        }

14. Aggiungere al metodo **Main** il codice seguente per ottenere i dettagli dell'esecuzione di una sezione di dati.

        Console.WriteLine("Getting run details of a data slice");

        var datasliceRunListResponse = client.DataSliceRuns.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString());

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
    }

15. Compilare l'applicazione console. Fare clic su **Compila** dal menu e scegliere **Compila soluzione**.
16. Verificare che esista almeno un file nel contenitore adftutorial nell'archiviazione BLOB di Azure. In caso contrario, creare il file Emp.txt nel Blocco note con il contenuto seguente e caricarlo nel contenitore adftutorial.

        John, Doe
		Jane, Doe
	 
17. Eseguire l'esempio facendo clic su **Debug** -> **Avvia debug** nel menu.
18. Usare il portale di anteprima di Azure per verificare che l'istanza di Data factory **APITutorialFactory** venga creata con gli elementi seguenti: 
	- Servizio collegato: **LinkedService_AzureStorage** 
	- Tabelle: **TableBlobSource** e **TableBlobDestination**.
	- Pipeline: **PipelineBlobSample** 
18. Verificare che venga creato un file di output nella cartella **apifactoryoutput** nel contenitore **adftutorial**.


## Vedere anche

Articolo | Descrizione
------ | ---------------
[Introduzione a Data factory di Azure][data-factory-introduction] | Questo articolo fornisce un'introduzione al servizio Data factory di Azure, ai concetti, al valore che offre e agli scenari che supporta.
[Introduzione a Data factory di Azure][adf-getstarted] | Questo articolo fornisce un'esercitazione end-to-end che mostra come creare un'istanza di Data factory di Azure di esempio che copia i dati da un BLOB di Azure a un database SQL di Azure.
[Consentire alle pipeline di usare dati locali][use-onpremises-datasources] | Questo articolo contiene una procedura dettagliata che mostra come copiare dati da un database di SQL Server locale a un BLOB di Azure.
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | Questo articolo fornisce una procedura dettagliata end-to-end che mostra come implementare uno scenario quasi reale usando Data factory di Azure per trasformare i dati da file di log a informazioni accurate.
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'attività personalizzata e usarla in una pipeline. 
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via. 


[data-factory-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/it-it/downloads/

<!--HONumber=35.2-->
