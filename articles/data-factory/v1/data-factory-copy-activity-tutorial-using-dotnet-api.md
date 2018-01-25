---
title: "Esercitazione: Creare una pipeline con l'attività di copia usando l'API .NET | Documentazione Microsoft"
description: "In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando l'API .NET."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: d6ffaf132cf2407c3bd3a609b087508c94bb0e46
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Esercitazione: Creare una pipeline con l'attività di copia usando l'API .NET
> [!div class="op_single_selector"]
> * [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
> * [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modello di Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere l'[esercitazione sull'attività di copia nella documentazione sulla versione 2](../quickstart-create-data-factory-dot-net.md). 

Questo articolo illustra l'uso dell'[API .NET](https://portal.azure.com) per creare una data factory con una pipeline che copia i dati da un archivio BLOB di Azure a un database SQL di Azure. Se non si ha familiarità con Azure Data Factory, prima di eseguire questa esercitazione vedere l'articolo [Introduzione ad Azure Data Factory](data-factory-introduction.md).   

In questa esercitazione si crea una pipeline contenente una sola attività: un'attività di copia che copia i dati da un archivio dati supportato a un archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini e sink, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md).

Una pipeline può includere più attività ed è possibile concatenarne due, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input dell'altra. Per altre informazioni, vedere [Attività multiple in una pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> Per la documentazione completa sull'API .NET per Data Factory, vedere le [informazioni di riferimento sull'API NET di Data Factory](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1).
> 
> La pipeline di dati in questa esercitazione copia i dati da un archivio dati di origine a un archivio dati di destinazione. Per un'esercitazione su come trasformare i dati usando Azure Data Factory, vedere [Esercitazione: Creare una pipeline per trasformare i dati usando un cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>prerequisiti
* Per una panoramica dell'esercitazione e per eseguire i passaggi relativi ai [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) , vedere **Panoramica e prerequisiti** .
* Visual Studio 2012 o 2013 o 2015
* Scaricare e installare [.NET SDK di Azure](http://azure.microsoft.com/downloads/)
* Azure PowerShell. Seguire le istruzioni disponibili nell'articolo [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps) per installare la versione più recente di Azure PowerShell nel computer. Azure PowerShell verrà usato per creare un'applicazione Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Creare un'applicazione in Azure Active Directory
Creare l'applicazione Azure Active Directory, creare un'entità servizio per l'applicazione e assegnarla al ruolo **Collaboratore Data Factory** .

1. Avviare **PowerShell**.
2. Eseguire il comando seguente e immettere il nome utente e la password usati per accedere al portale di Azure.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Eseguire il comando seguente per visualizzare tutte le sottoscrizioni per l'account.

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. Eseguire il comando seguente per selezionare la sottoscrizione da usare. Sostituire **&lt;NameOfAzureSubscription**&gt; con il nome della sottoscrizione di Azure.

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```

   > [!IMPORTANT]
   > Dall'output del comando prendere nota dei valori di **SubscriptionId** e **TenantId**.

5. Creare un gruppo di risorse di Azure denominato **ADFTutorialResourceGroup** eseguendo il comando seguente in PowerShell.

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Se il gruppo di risorse esiste già, specificare se deve essere aggiornato (Y) o mantenuto invariato (N).

    Se si usa un gruppo di risorse diverso, è necessario usare il nome del gruppo di risorse invece di ADFTutorialResourceGroup in questa esercitazione.
6. Creare un'applicazione Azure Active Directory.

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    Se viene visualizzato l'errore seguente, specificare un URL diverso ed eseguire di nuovo il comando.
    
    ```PowerShell
    Another object with the same value for property identifierUris already exists.
    ```
7. Creare l'entità servizio di AD.

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Aggiungere l'entità servizio al ruolo **Collaboratore Data Factory** .

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Ottenere l'ID applicazione.

    ```PowerShell
    $azureAdApplication 
    ```
    Annotare l'ID applicazione (applicationID) dall'output.

Da questi passaggi si avranno i quattro valori seguenti:

* ID tenant
* ID sottoscrizione
* ID applicazione
* Password (specificata nel primo comando)

## <a name="walkthrough"></a>Procedura dettagliata
1. Creare un'applicazione console .NET in C# con Visual Studio 2012, 2013 o 2015.
   1. Avviare **Visual Studio** 2012, 2013 o 2015.
   2. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**.
   3. Espandere **Modelli** e quindi selezionare **Visual C#**. In questa procedura dettagliata viene usato C#, ma è possibile usare qualsiasi linguaggio .NET.
   4. Selezionare **Applicazione console** dall'elenco dei tipi di progetto a destra.
   5. Immettere **DataFactoryAPITestApp** per Nome.
   6. Selezionare **C:\ADFGetStarted** come percorso.
   7. Fare clic su **OK** per creare il progetto.
2. Fare clic su **Strumenti**, scegliere **Gestione pacchetti NuGet** e quindi fare clic su **Console di Gestione pacchetti**.
3. Nella finestra **Console di Gestione pacchetti** seguire questa procedura:
   1. Eseguire questo comando per installare il pacchetto di Data factory: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Eseguire questo comando per installare il pacchetto di Azure Active Directory. Nel codice viene usata l'API di Active Directory: `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Aggiungere la sezione **appSettings** seguente al file **App.config**. Queste impostazioni sono usate dal metodo helper **GetAuthorizationHeader**.

    Sostituire i valori di **&lt;Application ID&gt;**, **&lt;Password&gt;**, **&lt;Subscription ID&gt;** e **&lt;tenant ID&gt;** con i propri valori.

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. Aggiungere le istruzioni **using** seguenti al file di origine (Program.cs) nel progetto.

    ```csharp
    using System.Configuration;
    using System.Collections.ObjectModel;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure;
    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;

    ```

6. Aggiungere al metodo **Main** il codice seguente che crea un'istanza della classe **DataPipelineManagementClient**. Si userà questo oggetto per creare una data factory, un servizio collegato, i set di dati di input e output e una pipeline. Questo oggetto viene usato anche per monitorare le sezioni di un set di dati in fase di esecuzione.

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader().Result);

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Sostituire il valore di **resourceGroupName** con il nome del gruppo di risorse di Azure.
   >
   > Aggiornare il nome della data factory (dataFactoryName) in modo che sia univoco. Il nome della data factory deve essere globalmente univoco. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md) .

7. Aggiungere al metodo **Main** il codice seguente che crea una **data factory**.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties()
            }
        }
    );
    ```

    Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, un'attività di copia per copiare dati da un archivio dati di origine a uno di destinazione e un'attività Hive HDInsight per eseguire uno script Hive e trasformare i dati di input in dati di output di prodotto. In questo passaggio iniziale viene creata la data factory.
8. Aggiungere al metodo **Main** il codice seguente che crea un **servizio collegato di Archiviazione di Azure**.

   > [!IMPORTANT]
   > Sostituire **storageaccountname** e **accountkey** con il nome e la chiave dell'account di archiviazione di Azure.

    ```csharp
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
    ```

    Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa esercitazione non si usano servizi di calcolo come Azure HDInsight o Azure Data Lake Analytics, ma due archivi dati di tipo Archiviazione di Azure (origine) e database SQL di Azure (destinazione). 

    Si creano quindi due servizi collegati denominati AzureStorageLinkedService e AzureSqlLinkedService di tipo AzureStorage e AzureSqlDatabase.  

    AzureStorageLinkedService collega l'account di archiviazione di Azure alla data factory. L'account di archiviazione è quello in cui, come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), è stato creato un contenitore e sono stati caricati i dati.
9. Aggiungere al metodo **Main** il codice seguente che crea un **servizio collegato di Azure SQL**.

   > [!IMPORTANT]
   > Sostituire **servername**, **databasename**, **username** e **password** con i nomi del server, del database, dell'utente e della password di Azure SQL.

    ```csharp
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
    ```

    AzureSqlLinkedService collega il database SQL di Azure alla data factory. I dati copiati dall'archivio BLOB vengono archiviati in questo database. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stata creata la tabella emp in questo database.
10. Aggiungere al metodo **Main** il codice seguente che crea **set di dati di input e output**.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "InputDataset";
    string Dataset_Destination = "OutputDataset";

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
    ```
    
    Nel passaggio precedente sono stati creati servizi collegati per collegare l'account di archiviazione di Azure e un database SQL di Azure alla data factory. In questo passaggio vengono definiti due set di dati denominati InputDataset e OutputDataset, che rappresentano i dati di input e di output memorizzati negli archivi dati a cui fanno riferimento rispettivamente AzureStorageLinkedService e AzureSqlLinkedService.

    Il servizio collegato Archiviazione di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi all'account di archiviazione di Azure. Il set di dati del BLOB di input (InputDataset) specifica il contenitore e la cartella che contiene i dati di input.  

    Analogamente, il servizio collegato per il database SQL di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi al database SQL di Azure e il set di dati della tabella SQL di output (OututDataset) specifica la tabella del database in cui vengono copiati i dati dell'archivio BLOB.

    In questo passaggio viene creato un set di dati denominato InputDataset che punta a un file BLOB (emp.txt) nella cartella radice di un contenitore BLOB (adftutorial) nella risorsa di archiviazione di Azure rappresentata dal servizio collegato AzureStorageLinkedService. Se non si specifica un valore per fileName (o lo si ignora), i dati di tutti i BLOB della cartella di input vengono copiati nella destinazione. In questa esercitazione si specifica un valore per fileName.    

    In questo passaggio si crea un set di dati di output denominato **OutputDataset**. Questo set di dati punta a una tabella SQL nel database SQL di Azure rappresentato da **AzureSqlLinkedService**.
11. Aggiungere al metodo **Main** il codice seguente che **crea e attiva una pipeline**. In questo passaggio viene creata una pipeline con un'**attività di copia** che usa **InputDataset** come input e **OutputDataset** come output.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2017, 5, 11, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = new DateTime(2017, 5, 12, 0, 0, 0, 0, DateTimeKind.Utc);
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
                    }
                }
            }
        });
    ```

    Tenere presente quanto segue:
   
    - Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md). Nelle soluzioni Data Factory è anche possibile usare le [attività di trasformazione dei dati](data-factory-data-transformation-activities.md).
    - L'input per l'attività è impostato su **InputDataset** e l'output è impostato su **OutputDataset**. 
    - Nella sezione **typeProperties** vengono specificati **BlobSource** come tipo di origine e **SqlSink** come tipo di sink. Per un elenco completo degli archivi dati supportati dall'attività di copia come origini e sink, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Per informazioni su come usare uno specifico archivio dati supportato come origine/sink, fare clic sul collegamento nella tabella.  
   
    Attualmente, è il set di dati di output a determinare la pianificazione. In questa esercitazione il set di dati di output viene configurato per generare una sezione una volta ogni ora. La pipeline ha un'ora di inizio e un'ora di fine intervallate da un giorno, ovvero 24 ore. Vengono quindi generate dalla pipeline 24 sezioni di set di dati di output.
12. Aggiungere al metodo **Main** il codice seguente per ottenere lo stato di una sezione di dati del set di dati di output. In questo esempio è prevista solo una sezione.

    ```csharp
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
    ```

13. Aggiungere al metodo **Main** il codice seguente per ottenere i dettagli di esecuzione di una sezione dati.

    ```csharp
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
    ```

14. Aggiungere alla classe **Program** il metodo helper seguente usato per il metodo **Main**.

    > [!NOTE] 
    > Quando si copia e incolla il codice seguente, assicurarsi che il codice copiato si trovi allo stesso livello del metodo Main.

    ```csharp
    public static async Task<string> GetAuthorizationHeader()
    {
        AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        ClientCredential credential = new ClientCredential(
            ConfigurationManager.AppSettings["ApplicationId"],
            ConfigurationManager.AppSettings["Password"]);
        AuthenticationResult result = await context.AcquireTokenAsync(
            resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
            clientCredential: credential);

        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. In Esplora soluzioni espandere il progetto (DataFactoryAPITestApp), fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Aggiungi riferimento**. Selezionare la casella di controllo per l'assembly **System.Configuration** e fare clic su **OK**.
16. Compilare l'applicazione console. Scegliere **Compila** dal menu e fare clic su **Compila soluzione**.
17. Verificare che esista almeno un file nel contenitore **adftutorial** nell'archivio BLOB di Azure. In caso contrario, creare il file **Emp.txt** nel Blocco note con il contenuto seguente e caricarlo nel contenitore adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
18. Eseguire l'esempio scegliendo **Debug** -> **Avvia debug** dal menu. Quando viene visualizzato un messaggio simile ad **Acquisizione dettagli dell'esecuzione di una sezione di dati**, attendere qualche minuto e premere **INVIO**.
19. Usare il portale di Azure per verificare che la data factory **APITutorialFactory** venga creata con gli elementi seguenti:
   * Servizio collegato: **LinkedService_AzureStorage**.
   * Set di dati: **InputDataset** e **OutputDataset**.
   * Pipeline: **PipelineBlobSample**
20. Verificare che i due record dipendente vengano creati nella tabella **emp** del database SQL di Azure specificato.

## <a name="next-steps"></a>Passaggi successivi
Per la documentazione completa sull'API .NET per Data Factory, vedere le [informazioni di riferimento sull'API NET di Data Factory](/dotnet/api/index?view=azuremgmtdatafactories-4.12.1).

In questa esercitazione sono stati usati l'archivio BLOB di Azure come archivio dati di origine e un database SQL di Azure come archivio dati di destinazione in un'operazione di copia. La tabella seguente contiene un elenco degli archivi dati supportati come origini e come destinazioni dall'attività di copia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Per informazioni su come copiare dati da/in un archivio dati, fare clic sul collegamento relativo all'archivio dati nella tabella.

