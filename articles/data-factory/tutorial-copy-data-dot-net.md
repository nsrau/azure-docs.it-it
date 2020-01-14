---
title: Copiare i dati dall'archiviazione BLOB di Azure al database SQL di Azure
description: Questa esercitazione offre istruzioni dettagliate per copiare dati da un archivio BLOB di Azure al database SQL di Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: 7f3fdf1b723158db873bc2635de34d878c464201
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439427"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copiare dati da un BLOB di Azure al database SQL di Azure con Azure Data Factory

In questa esercitazione si crea una pipeline di Data Factory che copia i dati da un archivio BLOB di Azure al database SQL di Azure. Il modello di configurazione di questa esercitazione si applica alla copia da un archivio dati basato su file a un archivio dati relazionale. Per un elenco degli archivi dati supportati come origini e sink, vedere i [formati e gli archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare servizi collegati Archiviazione di Azure e Database SQL di Azure.
> * Creare i set di dati del BLOB di Azure e del database SQL di Azure.
> * Creare una pipeline contenente un'attività di copia.
> * Avviare un'esecuzione della pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

In questa esercitazione viene usato .NET SDK. È possibile usare altri meccanismi per interagire con Azure Data Factory. Vedere gli esempi in **Avvii rapidi**.

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

* *Account di archiviazione di Azure*. Usare l'archivio BLOB come archivio dati di *origine*. Se non si ha un account di archiviazione di Azure, vedere [Creare un account di archiviazione per utilizzo generico](../storage/common/storage-quickstart-create-account.md).
* *Database SQL di Azure*. Usare il database come archivio dati *sink*. Se non si ha un database SQL di Azure, vedere [Creare un database SQL di Azure](../sql-database/sql-database-single-database-get-started.md).
* *Visual Studio*. Nella procedura guidata illustrata in questo articolo viene usato Visual Studio 2019.
* *[Azure SDK per .NET](/dotnet/azure/dotnet-tools)* .
* *Applicazione Azure Active Directory*. Se non si dispone di un'applicazione Azure Active Directory, vedere la sezione [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) dell'articolo [Procedura: Usare il portale per creare un'applicazione Azure AD](../active-directory/develop/howto-create-service-principal-portal.md). Copiare i valori seguenti per l'uso nei passaggi successivi: **ID applicazione (client)** , **chiave di autenticazione** e **ID della directory (tenant)** . Assegnare l'applicazione al ruolo **Collaboratore** seguendo le istruzioni disponibili nello stesso articolo.

### <a name="create-a-blob-and-a-sql-table"></a>Creare un BLOB e una tabella SQL

Preparare ora il BLOB di Azure e il database SQL di Azure per l'esercitazione creando un BLOB di origine e una tabella SQL sink.

#### <a name="create-a-source-blob"></a>Creare un BLOB di origine

Prima di tutto, creare un BLOB di origine creando un contenitore e caricando al suo interno un file di testo di input:

1. Aprire il Blocco note. Copiare il testo seguente e salvarlo in locale in un file denominato *inputEmp.txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Usare uno strumento come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per creare il contenitore *adfv2tutorial* e per caricare il file *inputEmp.txt* nel contenitore.

#### <a name="create-a-sink-sql-table"></a>Creare una tabella SQL sink

Creare quindi una tabella SQL sink:

1. Usare lo script SQL seguente per creare la tabella *dbo.emp* nel database SQL di Azure.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Consentire ai servizi di Azure di accedere a SQL Server. Assicurarsi di aver consentito l'accesso ai servizi di Azure nel server SQL Azure in modo che il servizio Data Factory possa scrivere dati nel server. Per verificare e attivare l'impostazione, seguire questa procedura:

    1. Passare al [portale di Azure](https://portal.azure.com) per gestire il server SQL. Cercare e selezionare **Server SQL**.

    2. Selezionare il server.
    
    3. Nell'intestazione **Sicurezza** menu del server SQL selezionare **Firewall e reti virtuali**.

    4. Nella pagina **Firewall e reti virtuali** selezionare **SÌ** per **Consenti alle risorse e ai servizi di Azure di accedere a questo server**.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

Creare un'applicazione console .NET in C# con Visual Studio.

1. Aprire Visual Studio.
2. Nella finestra **iniziale** selezionare **Crea un nuovo progetto**.
3. Nella finestra **Crea un nuovo progetto** scegliere la versione C# di **App console (.NET Framework)** dall'elenco dei tipi di progetto. Fare quindi clic su **Avanti**.
4. Nella finestra **Configura il nuovo progetto** in **Nome progetto** immettere *ADFv2Tutorial*. Per **Percorso**, individuare e/o creare la directory in cui salvare il progetto. Selezionare quindi **Crea**. Il nuovo progetto verrà visualizzato nell'IDE di Visual Studio.

## <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

Installare quindi i pacchetti di libreria necessari usando Gestione pacchetti NuGet.

1. Nella barra dei menu scegliere **Strumenti** > **Gestione pacchetti NuGet** > **Console di Gestione pacchetti**.
2. Nel riquadro **Console di Gestione pacchetti** eseguire questi comandi per installare i pacchetti. Per altre informazioni sul pacchetto NuGet per Azure Data Factory, vedere il [Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Creare un client di data factory

Per creare un client di data factory, seguire questa procedura.

1. Aprire il file *Program.cs*, sovrascrivere le istruzioni `using` esistenti con il codice seguente per aggiungere i riferimenti agli spazi dei nomi.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Aggiungere il codice seguente al metodo `Main` per impostare le variabili. Sostituire i 14 segnaposto con i valori personalizzati.

    Per visualizzare l'elenco delle aree di Azure in cui Data Factory è attualmente disponibile, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Nell'elenco a discesa **Prodotti** scegliere **Esplora** > **Analisi** > **Data Factory**. Quindi nell'elenco a discesa **Aree** selezionare le aree a cui si è interessati. Verrà visualizzata una griglia con lo stato di disponibilità dei prodotti Data Factory per le aree selezionate.

    > [!NOTE]
    > Gli archivi dati, ad esempio, Archiviazione di Azure e il database SQL di Azure, e le risorse di calcolo, ad esempio HDInsight, usati da Data Factory possono risiedere in aree diverse da quelle scelte dall'utente per Data Factory.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = 
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Aggiungere il codice seguente al metodo `Main` per creare un'istanza della classe `DataFactoryManagementClient`. Usare questo oggetto per creare una data factory, il servizio collegato, i set di dati e la pipeline. È possibile usare questo oggetto anche per monitorare i dettagli sull'esecuzione della pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Creare una data factory

Aggiungere il codice seguente al metodo `Main` per creare una *data factory*.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Creare servizi collegati

In questa esercitazione verranno creati due servizi collegati, rispettivamente per l'origine e il sink.

### <a name="create-an-azure-storage-linked-service"></a>Creare un servizio collegato Archiviazione di Azure

Aggiungere il codice seguente al metodo `Main` per creare un *servizio collegato di Archiviazione di Azure*. Per informazioni sulle proprietà supportate e i dettagli, vedere [Proprietà del servizio collegato BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Creare un servizio collegato Database SQL di Azure

Aggiungere il codice seguente al metodo `Main` per creare un *servizio collegato Database SQL di Azure*. Per informazioni sulle proprietà supportate e i dettagli, vedere [Proprietà del servizio collegato Database SQL di Azure](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Creare set di dati

In questa sezione vengono creati due set di dati: uno per l'origine e l'altro per il sink. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Creare un set di dati per il BLOB di Azure di origine

Aggiungere il codice seguente al metodo `Main` per creare un *set di dati del BLOB di Azure*. Per informazioni sulle proprietà supportate e i dettagli, vedere [Proprietà del set di dati del BLOB di Azure](connector-azure-blob-storage.md#dataset-properties).

Definire un set di dati che rappresenta i dati di origine nel BLOB di Azure. Questo set di dati del BLOB fa riferimento al servizio collegato Archiviazione di Azure creato nel passaggio precedente e descrive quanto segue:

- Posizione del BLOB da cui eseguire la copia: `FolderPath` e `FileName`
- Formato del BLOB che indica come analizzare il contenuto: `TextFormat` e relative impostazioni, ad esempio il delimitatore di colonna
- Struttura dei dati, inclusi i nomi di colonna e i tipi di dati mappati in questo esempio alla tabella SQL sink

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference { 
            ReferenceName = storageLinkedServiceName 
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Creare un set di dati per il database SQL di Azure sink

Aggiungere il codice seguente al metodo `Main` per creare un *set di dati del database SQL di Azure*. Per informazioni sulle proprietà supportate e i dettagli, vedere [Proprietà del set di dati del database SQL di Azure](connector-azure-sql-database.md#dataset-properties).

Definire un set di dati che rappresenta i dati sink nel database SQL di Azure. Questo set di dati fa riferimento al servizio collegato Database SQL di Azure creato nel passaggio precedente e specifica anche la tabella SQL contenente i dati copiati. 

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Creare una pipeline

Aggiungere il codice seguente al metodo `Main` per creare una *pipeline con un'attività di copia*. In questa esercitazione la pipeline contiene solo un'attività, `CopyActivity`, che usa il set di dati del BLOB come origine e il set di dati SQL come sink. Per informazioni sui dettagli dell'attività di copia, vedere [Attività di copia in Azure Data Factory](copy-activity-overview.md).

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline

Aggiungere il codice seguente al metodo `Main` per *attivare un'esecuzione della pipeline*.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorare un'esecuzione della pipeline

A questo punto, inserire il codice per controllare gli stati di esecuzione della pipeline e per ottenere i dettagli sull'esecuzione dell'attività di copia.

1. Aggiungere il codice seguente al metodo `Main` per controllare continuamente gli stati dell'esecuzione della pipeline fino al termine della copia dei dati.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Aggiungere il codice seguente al metodo `Main` per recuperare i dettagli dell'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti o scritti.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Eseguire il codice

Per compilare l'applicazione scegliere **Compila** > **Compila soluzione**. Avviare quindi l'applicazione e scegliere **Debug** > **Avvia debug** e verificare l'esecuzione della pipeline.

La console stampa lo stato di avanzamento della creazione di una data factory, del servizio collegato, dei set di dati, della pipeline e della relativa esecuzione. Controlla quindi lo stato di esecuzione della pipeline. Attendere fino a quando non vengono visualizzati i dettagli sull'esecuzione dell'attività di copia con le dimensioni dei dati letti/scritti. Usare quindi strumenti come SSMS (SQL Server Management Studio) o Visual Studio per connettersi al database SQL di Azure di destinazione e verificare se la tabella di destinazione specificata contiene i dati copiati.

### <a name="sample-output"></a>Output di esempio

```json
Creating a data factory AdfV2Tutorial...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```

## <a name="next-steps"></a>Passaggi successivi

La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Si è appreso come: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare servizi collegati Archiviazione di Azure e Database SQL di Azure.
> * Creare i set di dati del BLOB di Azure e del database SQL di Azure.
> * Creare una pipeline contenente un'attività di copia.
> * Avviare un'esecuzione della pipeline.
> * Monitorare le esecuzioni di pipeline e attività.

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati dall'ambiente locale al cloud: 

> [!div class="nextstepaction"]
>[Copiare dati dall'ambiente locale al cloud](tutorial-hybrid-copy-powershell.md)
