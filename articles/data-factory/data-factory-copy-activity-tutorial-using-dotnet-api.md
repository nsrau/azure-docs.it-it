<properties 
	pageTitle="Esercitazione: Creare una pipeline con l'attività di copia usando l'API .NET | Microsoft Azure" 
	description="In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando l'API .NET." 
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
	ms.topic="get-started-article" 
	ms.date="09/16/2016" 
	ms.author="spelluru"/>

# Esercitazione: Creare una pipeline con l'attività di copia usando l'API .NET
> [AZURE.SELECTOR]
- [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Copia guidata](data-factory-copy-data-wizard-tutorial.md)

Questa esercitazione mostra come creare e monitorare una data factory di Azure con l'API .NET. La pipeline nella data factory usa un'attività di copia per copiare i dati dall'archivio BLOB di Azure al database SQL di Azure.

L'attività di copia esegue lo spostamento dei dati in Azure Data Factory e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per informazioni dettagliate sull'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md).

> [AZURE.NOTE] 
Questo articolo non descrive tutte le API .NET di Data Factory. Per informazioni dettagliate su Data Factory .NET SDK, vedere le [informazioni di riferimento sull'API .NET di Data Factory](https://msdn.microsoft.com/library/mt415893.aspx).

## Prerequisiti
- Per una panoramica dell'esercitazione e per eseguire i passaggi relativi ai **prerequisiti**, vedere [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Visual Studio 2012 o 2013 o 2015
- Scaricare e installare [.NET SDK di Azure](http://azure.microsoft.com/downloads/)
- Azure PowerShell. Seguire le istruzioni disponibili nell'articolo [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per installare la versione più recente di Azure PowerShell nel computer. Azure PowerShell verrà usato per creare un'applicazione Azure Active Directory.

### Creare un'applicazione in Azure Active Directory
Creare l'applicazione Azure Active Directory, creare un'entità servizio per l'applicazione e assegnarla al ruolo **Collaboratore Data Factory**.

1. Avviare **PowerShell**.
1. Eseguire il comando seguente e immettere il nome utente e la password usati per accedere al portale di Azure.
	
		Login-AzureRmAccount   
2. Eseguire il comando seguente per visualizzare tutte le sottoscrizioni per l'account.

		Get-AzureRmSubscription 
3. Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **&lt;NameOfAzureSubscription**&gt; con il nome della sottoscrizione di Azure.

		Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext

	> [AZURE.IMPORTANT] Dall'output del comando prendere nota dei valori di **SubscriptionId** e **TenantId**.
4. Creare un gruppo di risorse di Azure denominato **ADFTutorialResourceGroup** eseguendo il comando seguente in PowerShell.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Se il gruppo di risorse esiste già, specificare se deve essere aggiornato (Y) o mantenuto invariato (N).

	Se si usa un gruppo di risorse diverso, è necessario usare il nome del gruppo di risorse invece di ADFTutorialResourceGroup in questa esercitazione.
5. Creare un'applicazione Azure Active Directory.

		$azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"

	Se viene visualizzato l'errore seguente, specificare un URL diverso ed eseguire di nuovo il comando.

		Another object with the same value for property identifierUris already exists.

6. Creare l'entità servizio di AD.

		New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

7. Aggiungere l'entità servizio al ruolo **Collaboratore Data Factory**.

		New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

8. Ottenere l'ID applicazione.

		$azureAdApplication

	Annotare l'ID applicazione (**applicationID**) dall'output.

Da questi passaggi si avranno i quattro valori seguenti:

- ID tenant
- ID sottoscrizione
- ID applicazione
- Password (specificata nel primo comando)

## Procedura dettagliata
1. Creare un'applicazione console .NET in C# con Visual Studio 2012, 2013 o 2015.
	1. Avviare **Visual Studio** 2012, 2013 o 2015.
	2. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**.
	3. Espandere **Modelli** e quindi selezionare **Visual C#**. In questa procedura dettagliata viene usato C#, ma è possibile usare qualsiasi linguaggio .NET.
	4. Selezionare **Applicazione console** dall'elenco dei tipi di progetto a destra.
	5. Immettere **DataFactoryAPITestApp** per Nome.
	6. Selezionare **C:\\ADFGetStarted** per Percorso.
	7. Fare clic su **OK** per creare il progetto.
2. Fare clic su **Strumenti**, scegliere **Gestione pacchetti NuGet** e fare clic su **Console di Gestione pacchetti**.
3.	In **Console di Gestione pacchetti** eseguire i comandi seguenti uno alla volta.

		Install-Package Microsoft.Azure.Management.DataFactories
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
6. Aggiungere la sezione **appSettings** seguente al file **App.config**. Queste impostazioni sono usate dal metodo helper **GetAuthorizationHeader**.

	Sostituire i valori per **&lt;ID applicazione&gt;**, **&lt;Password&gt;**, **&lt;ID sottoscrizione&gt;** e **&lt;ID tenant**&gt; con i valori personalizzati.

		<appSettings>
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

		    <!-- Replace the following values with your own -->
    		<add key="ApplicationId" value="<Application ID>" />
    		<add key="Password" value="<Password>" />    
		    <add key="SubscriptionId" value= "Subscription ID" />
    		<add key="ActiveDirectoryTenantId" value="tenant ID" />
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
6. Aggiungere al metodo **Main** il codice seguente che crea un'istanza della classe **DataPipelineManagementClient**. Si userà questo oggetto per creare una data factory, un servizio collegato, i set di dati di input e output e una pipeline. Questo oggetto viene usato anche per monitorare le sezioni di un set di dati in fase di esecuzione.

			// create data factory management client
        	string resourceGroupName = "ADFTutorialResourceGroup";
        	string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials =
                new TokenCloudCredentials(
                    ConfigurationManager.AppSettings["SubscriptionId"],
                    GetAuthorizationHeader());

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

	> [AZURE.IMPORTANT] 
	Sostituire il valore di **resourceGroupName** con il nome del gruppo di risorse di Azure.
	> 
	> Aggiornare il nome della data factory (**dataFactoryName**) in modo che sia univoco. Il nome della data factory deve essere globalmente univoco. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'argomento [Azure Data Factory - Regole di denominazione](data-factory-naming-rules.md).

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

8. Aggiungere al metodo **Main** il codice seguente che crea un **servizio collegato di Archiviazione di Azure**.

	> [AZURE.IMPORTANT] Sostituire **accountname** e **accountkey** con il nome e la chiave dell'account di archiviazione di Azure.

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );
9. Aggiungere al metodo **Main** il codice seguente che crea un **servizio collegato di Azure SQL**.
 
	> [AZURE.IMPORTANT] Sostituire **servername**, **databasename**, **username** e **password** con i nomi del server, del database, dell'utente e della password di Azure SQL.

			// create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure SQL Database linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureSqlLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                        )
                    }
                }
            );
10. Aggiungere al metodo **Main** il codice seguente che crea **set di dati di input e output**.

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "DatasetBlobSource";
            string Dataset_Destination = "DatasetAzureSqlDestination";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,


            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        Structure = new List<DataElement>()
                        {
                            new DataElement() { Name = "FirstName", Type = "String" },
                            new DataElement() { Name = "LastName", Type = "String" }
                        },
                        LinkedServiceName = "AzureStorageLinkedService",
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


            Console.WriteLine("Creating output dataset of type: Azure SQL");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            Structure = new List<DataElement>()
                            {
                                new DataElement() { Name = "FirstName", Type = "String" },
                                new DataElement() { Name = "LastName", Type = "String" }
                            },
                            LinkedServiceName = "AzureSqlLinkedService",
                            TypeProperties = new AzureSqlTableDataset()
                            {
                                TableName = "emp"
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
            DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipeline";

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
                                    Name = "BlobToAzureSql",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
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

12. Aggiungere al metodo **Main** il codice seguente per ottenere lo stato di una sezione di dati del set di dati di output. In questo esempio è prevista solo una sezione.
 
            // Pulling status within a timeout threshold
            DateTime start = DateTime.Now;
            bool done = false;

            while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
            {
                Console.WriteLine("Pulling the slice status");
                // wait before the next status check
                Thread.Sleep(1000 * 12);

                var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
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

14. Aggiungere al metodo **Main** il codice seguente per ottenere i dettagli di esecuzione di una sezione dati.

            Console.WriteLine("Getting run details of a data slice");

            // give it a few minutes for the output slice to be ready
            Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
            Console.ReadKey();

            var datasliceRunListResponse = client.DataSliceRuns.List(
                    resourceGroupName,
                    dataFactoryName,
                    Dataset_Destination,
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

13. Aggiungere alla classe **Program** il metodo helper seguente usato per il metodo **Main**.
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                    result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
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


15. In Esplora soluzioni, espandere il progetto (**DataFactoryAPITestApp**), fare doppio clic su **riferimenti**, fare clic su **Aggiungi riferimento**. Selezionare" la casella di controllo per l'assembly **System.Configuration**" e fare clic su **OK**.
16. Compilare l'applicazione console. Scegliere **Compila** dal menu e fare clic su **Compila soluzione**.
16. Verificare che esista almeno un file nel contenitore **adftutorial** nell'archivio BLOB di Azure. In caso contrario, creare il file **Emp.txt** nel Blocco note con il contenuto seguente e caricarlo nel contenitore adftutorial.

        John, Doe
		Jane, Doe
	 
17. Eseguire l'esempio scegliendo **Debug** -> **Avvia debug** dal menu. Quando viene visualizzato un messaggio simile ad **Acquisizione dettagli dell'esecuzione di una sezione di dati**, attendere qualche minuto e premere **INVIO**.
18. Usare il portale di Azure per verificare che la data factory **APITutorialFactory** venga creata con gli elementi seguenti:
	- Servizio collegato: **LinkedService\_AzureStorage**.
	- Set di dati: **DatasetBlobSource** e **DatasetBlobDestination**.
	- Pipeline: **PipelineBlobSample**.
18. Verificare che venga creato un file di output nella cartella "**apifactoryoutput**" nel contenitore **adftutorial**.

## Passaggi successivi

- Leggere l'articolo [Spostare dati con l'attività di copia](data-factory-data-movement-activities.md) che fornisce informazioni dettagliate sull'attività di copia usata nell'esercitazione.
- Per informazioni dettagliate su Data Factory .NET SDK, vedere le [informazioni di riferimento sull'API .NET di Data Factory](https://msdn.microsoft.com/library/mt415893.aspx). Questo articolo non descrive tutte le API .NET di Data Factory.

 

<!---HONumber=AcomDC_0921_2016-->