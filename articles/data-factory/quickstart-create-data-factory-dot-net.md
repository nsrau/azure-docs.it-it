---
title: Creare una data factory di Azure usando .NET | Microsoft Docs
description: Creare una data factory di Azure per copiare dati da un percorso a un altro in Archiviazione BLOB di Azure.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: b430f5932bcd54d5e2ab787fb6c4491b48dbc416
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Creare una data factory e una pipeline con .NET SDK
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione 2 - Anteprima](quickstart-create-data-factory-dot-net.md)

Questa guida introduttiva descrive come usare .NET SDK per creare una data factory di Azure. La pipeline creata in questa data factory **copia** dati da una cartella a un'altra in un archivio BLOB di Azure. Per un'esercitazione su come **trasformare** i dati usando Azure Data Factory, vedere [Esercitazione: Trasformare dati usando Spark](transform-data-using-spark.md). 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere l'[introduzione a Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Questo articolo non offre una presentazione dettagliata del servizio Data Factory. Per un'introduzione al servizio Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>prerequisiti

### <a name="azure-subscription"></a>Sottoscrizione di Azure
Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="azure-roles"></a>Ruoli di Azure
Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure. Nel portale di Azure fare clic sul **nome utente** nell'angolo in alto a destra e selezionare **Autorizzazioni** per visualizzare le autorizzazioni disponibili nella sottoscrizione. Se si accede a più sottoscrizioni, selezionare quella appropriata. Per istruzioni di esempio sull'aggiunta di un utente a un ruolo, vedere l'articolo [Aggiungere i ruoli](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Account di archiviazione di Azure
In questa guida introduttiva si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB) come archivio dati sia di **origine** che di **destinazione**. Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno. 

#### <a name="get-storage-account-name-and-account-key"></a>Recuperare il nome e la chiave dell'account di archiviazione
In questa guida introduttiva si usano il nome e la chiave dell'account di archiviazione di Azure. La procedura seguente illustra i passaggi per recuperare il nome e la chiave dell'account di archiviazione. 

1. Avviare un Web browser e passare al [portale di Azure](https://portal.azure.com). Eseguire l'accesso con il nome utente e la password di Azure. 
2. Fare clic su **Altri servizi** nel menu a sinistra, usare la parola chiave **Archiviazione** come filtro e selezionare **Account di archiviazione**.

    ![Cercare l'account di archiviazione](media/quickstart-create-data-factory-dot-net/search-storage-account.png)
3. Nell'elenco degli account di archiviazione filtrare, se necessario, e quindi selezionare il **proprio account di archiviazione**. 
4. Nella pagina **Account di archiviazione** selezionare **Chiavi di accesso** nel menu.

    ![Recuperare il nome e la chiave dell'account di archiviazione](media/quickstart-create-data-factory-dot-net/storage-account-name-key.png)
5. Copiare i valori dei campi **Nome account di archiviazione** e **key1** negli Appunti. Incollarli in un blocco note o in qualsiasi altro editor e salvare.  

#### <a name="create-input-folder-and-files"></a>Creare la cartella e i file di input
In questa sezione si crea un contenitore BLOB denominato **adftutorial** nell'archivio BLOB di Azure. Si crea quindi una cartella denominata **input** nel contenitore e si carica un file di esempio in tale cartella. 

1. Nella pagina **Account di archiviazione** passare a **Panoramica** e quindi fare clic su **BLOB**. 

    ![Selezionare l'opzione BLOB](media/quickstart-create-data-factory-dot-net/select-blobs.png)
2. Nella pagina **Servizio BLOB** fare clic su **+ Contenitore** sulla barra degli strumenti. 

    ![Pulsante Aggiungi contenitore](media/quickstart-create-data-factory-dot-net/add-container-button.png)    
3. Nella finestra di dialogo **Nuovo contenitore** immettere **adftutorial** come nome e fare clic su **OK**. 

    ![Immettere il nome del contenitore](media/quickstart-create-data-factory-dot-net/new-container-dialog.png)
4. Fare clic su **adftutorial** nell'elenco di contenitori. 

    ![Selezionare il contenitore](media/quickstart-create-data-factory-dot-net/select-adftutorial-container.png)
1. Nella pagina **Contenitore** fare clic su **Carica** sulla barra degli strumenti.  

    ![Pulsante Carica](media/quickstart-create-data-factory-dot-net/upload-toolbar-button.png)
6. Nella pagina **Carica BLOB** fare clic su **Avanzate**.

    ![Fare clic sul collegamento Avanzate](media/quickstart-create-data-factory-dot-net/upload-blob-advanced.png)
7. Avviare il **Blocco note** e creare un file denominato **emp.txt** con il contenuto seguente. Salvarlo nella cartella **c:\ADFv2QuickStartPSH**. Creare la cartella **ADFv2QuickStartPSH** se non esiste già.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. Nella pagina **Carica BLOB** del portale di Azure cercare e selezionare il file **emp.txt** nel campo **File**. 
9. Immettere il valore **input** nel campo **Carica nella cartella**. 

    ![Caricare le impostazioni BLOB](media/quickstart-create-data-factory-dot-net/upload-blob-settings.png)    
10. Verificare che la cartella sia **input** e il file sia **emp.txt** e fare clic su **Carica**.
11. Verranno visualizzati il file **emp.txt** e lo stato del caricamento nell'elenco. 
12. Chiudere la pagina **Carica BLOB** facendo clic su **X** nell'angolo. 

    ![Chiudere la pagina Carica BLOB](media/quickstart-create-data-factory-dot-net/close-upload-blob.png)
1. Tenere aperta la pagina **Contenitore** perché verrà usata per verificare l'output alla fine di questa guida introduttiva.

### <a name="visual-studio"></a>Visual Studio
Nella procedura guidata illustrata in questo articolo viene usato Visual Studio 2017. È anche possibile usare Visual Studio 2013 o 2015.

### <a name="azure-net-sdk"></a>Azure .NET SDK
Scaricare e installare [Azure .NET SDK](http://azure.microsoft.com/downloads/) nel computer.

### <a name="create-an-application-in-azure-active-directory"></a>Creare un'applicazione in Azure Active Directory
Seguendo le istruzioni in [questo articolo](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) eseguire queste attività: 

1. **Creare un'applicazione di Azure Active Directory**. In Azure Active Directory creare un'applicazione che rappresenti l'applicazione .NET che si crea in questa esercitazione. Come URL di accesso è possibile specificare un URL fittizio, come illustrato nell'articolo (`https://contoso.org/exampleapp`).
2. Ottenere l'**ID applicazione** e la **chiave di autenticazione**** usando le istruzioni della sezione **Ottenere l'ID applicazione e la chiave di autenticazione** dell'articolo. Prendere nota di questi valori che verranno usati più avanti in questa esercitazione. 
3. Ottenere l'**ID tenant** usando le istruzioni della sezione **Ottenere l'ID tenant** dell'articolo. Prendere nota di questo valore. 
4. Assegnare l'applicazione al ruolo **Collaboratore** a livello di sottoscrizione in modo che l'applicazione possa creare le data factory nella sottoscrizione. Seguire le istruzioni della sezione **Assegnare l'applicazione al ruolo** dell'articolo. 

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

Usando Visual Studio 2013/2015/2017, creare un'applicazione console .NET per C#.

1. Avviare **Visual Studio**.
2. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**.
3. Selezionare **Visual C#** -> **App console (.NET Framework)** nell'elenco dei tipi di progetto a destra. È necessario .NET versione 4.5.2 o successiva.
4. Immettere **ADFv2QuickStart** per il nome.
5. Fare clic su **OK** per creare il progetto.

## <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

1. Fare clic su **Strumenti** -> **Gestione pacchetti NuGet** -> **Console di Gestione pacchetti**.
2. In **Console di Gestione pacchetti** eseguire questi comandi per installare i pacchetti:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

    ```

## <a name="create-a-data-factory-client"></a>Creare un client di data factory

1. Aprire **Program.cs** e includere le istruzioni seguenti per aggiungere riferimenti agli spazi dei nomi.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Aggiungere il codice seguente al metodo **Main** per impostare le variabili. Sostituire i segnaposto con i propri valori. Data Factory V2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
    ```

3. Aggiungere il codice seguente al metodo **Main** per creare un'istanza della classe **DataFactoryManagementClient**. Usare questo oggetto per creare una data factory, il servizio collegato, i set di dati e la pipeline. È possibile usare questo oggetto anche per monitorare i dettagli sull'esecuzione della pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

Aggiungere il codice seguente al metodo **Main** per creare una **data factory**. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Creare un servizio collegato

Aggiungere il codice seguente al metodo **Main** per creare un **servizio collegato di Archiviazione di Azure**.

Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa guida introduttiva è necessario creare solo un servizio collegato Archiviazione di Azure come archivio sink e di origine della copia, denominato "AzureStorageLinkedService" nell'esempio.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Creare un set di dati

Aggiungere il codice seguente al metodo **Main** per creare un **set di dati del BLOB di Azure**.

È necessario definire un set di dati che rappresenta i dati da copiare da un'origine a un sink. In questo esempio il set di dati del BLOB fa riferimento al servizio collegato Archiviazione di Azure creato nel passaggio precedente. Il set di dati accetta un parametro il cui valore è impostato in un'attività che utilizza il set di dati. Il parametro viene usato per costruire la proprietà "folderPath" che punta al percorso in cui si trovano e sono archiviati i dati.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Creare una pipeline

Aggiungere il codice seguente al metodo **Main** per creare una **pipeline con un'attività di copia**.

In questo esempio la pipeline contiene un'attività e accetta due parametri, il percorso del BLOB di input e il percorso del BLOB di output. I valori per questi parametri vengono impostati all'esecuzione/attivazione della pipeline. L'attività di copia fa riferimento allo stesso set di dati del BLOB creato nel passaggio precedente come input e output. Quando il set di dati viene usato come set di dati di input, viene specificato il percorso di input. Quando il set di dati viene usato come set di dati di output, viene specificato il percorso di output. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline

Aggiungere il codice seguente al metodo **Main** per **attivare un'esecuzione della pipeline**.

Questo codice imposta anche i valori dei parametri **inputPath** e **outputPath** specificati nella pipeline con gli effettivi valori dei percorsi dei BLOB di origine e sink.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorare un'esecuzione della pipeline

1. Aggiungere il codice seguente al metodo **Main** per controllare continuamente lo stato fino al termine della copia dei dati.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Aggiungere il codice seguente al metodo **Main** per recuperare i dettagli dell'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Eseguire il codice

Compilare e avviare l'applicazione, quindi verificare l'esecuzione della pipeline.

La console stampa lo stato di creazione della data factory, del servizio collegato, dei set di dati, della pipeline e dell'esecuzione della pipeline. Controlla quindi lo stato di esecuzione della pipeline. Attendere fino a quando non vengono visualizzati i dettagli sull'esecuzione dell'attività di copia con le dimensioni dei dati letti/scritti. Usare quindi strumenti come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per verificare che i BLOB siano stati copiati da "inputBlobPath" a "outputBlobPath", come specificato nelle variabili.

### <a name="sample-output"></a>Output di esempio: 
```json
Creating data factory SPv2Factory0907...
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
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verificare l'output
La pipeline crea automaticamente la cartella di output nel contenitore BLOB adftutorial, quindi copia il file emp.txt dalla cartella di input a quella di output. 

1. Nella pagina del contenitore **adftutorial** del portale di Azure fare clic su **Aggiorna** per visualizzare la cartella di output. 
    
    ![Aggiorna](media/quickstart-create-data-factory-dot-net/output-refresh.png)
2. Fare clic su **output** nell'elenco di cartelle. 
2. Verificare che **emp.txt** venga copiato nella cartella di output. 

    ![Aggiorna](media/quickstart-create-data-factory-dot-net/output-file.png)

## <a name="clean-up-resources"></a>Pulire le risorse
Per eliminare una data factory a livello di codice, aggiungere al programma le righe di codice seguenti: 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, fare riferimento alle [esercitazioni](tutorial-copy-data-dot-net.md). 
