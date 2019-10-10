---
title: Diramazione in una pipeline di Azure Data Factory | Microsoft Docs
description: Informazioni su come controllare il flusso dei dati in Azure Data Factory con la diramazione e il concatenamento delle attività.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 9/27/2019
ms.openlocfilehash: d8ea5a507cc110c92bb74491c3376f7b671638d9
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72175998"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Diramazione e concatenamento delle attività in una pipeline di Data factory

In questa esercitazione si crea una pipeline di Data Factory che illustra alcune funzionalità del flusso di controllo. La pipeline esegue la copia da un contenitore nell'archivio BLOB di Azure a un altro contenitore nello stesso account di archiviazione. Se l'attività di copia ha esito positivo, la pipeline invia i dettagli dell'operazione di copia completata in un messaggio di posta elettronica. Tali informazioni potrebbero includere la quantità di dati scritti. Se l'attività di copia ha esito negativo, vengono inviati i dettagli dell'errore di copia, ad esempio il messaggio di errore, in un messaggio di posta elettronica. Nel corso dell'esercitazione verrà illustrato come passare i parametri.

Questa immagine offre una panoramica dello scenario:

![Panoramica](media/tutorial-control-flow/overview.png)

L'esercitazione illustra come eseguire queste attività:

> [!div class="checklist"]
> * Creare una data factory
> * Creare un servizio collegato Archiviazione di Azure
> * Creare un set di dati del BLOB di Azure
> * Creare una pipeline contenente un'attività di copia e un'attività Web
> * Inviare gli output delle attività alle attività successive
> * Usare il passaggio di parametri e le variabili di sistema
> * Avviare un'esecuzione della pipeline
> * Monitorare le esecuzioni di pipeline e attività

In questa esercitazione viene usato .NET SDK. È possibile usare altri meccanismi per interagire con Azure Data Factory. Per informazioni di avvio rapido relative a Data Factory, vedere [Guide introduttive di 5 minuti](/azure/data-factory/quickstart-create-data-factory-portal).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Account di archiviazione di Azure. Come archivio dati di origine si usa un archivio BLOB. Se non si ha un account di archiviazione di Azure, vedere [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md).
* Azure Storage Explorer Per installare lo strumento, vedere [Azure Storage Explorer](https://storageexplorer.com/).
* Database SQL di Azure. Il database viene usato come archivio dati sink. Se non si ha un database SQL di Azure, vedere [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md).
* Visual Studio. Questo articolo usa Visual Studio 2019.
* Azure .NET SDK. Scaricare e installare [Azure .NET SDK](https://azure.microsoft.com/downloads/).

Per un elenco delle aree di Azure in cui Data Factory è attualmente disponibile, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Gli archivi dati e le risorse di calcolo possono trovarsi in altre aree. Gli archivi includono Archiviazione di Azure e Database SQL di Azure. Le risorse di calcolo includono HDInsight, usato da Data Factory.

Creare un'applicazione come descritto in [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Assegnare l'applicazione al ruolo **Collaboratore** seguendo le istruzioni disponibili nello stesso articolo. Per le parti successive di questa esercitazione saranno necessari diversi valori, ad esempio **ID applicazione (client)** e **ID della directory (tenant)** .

### <a name="create-a-blob-table"></a>Creare una tabella BLOB

1. Aprire un editor di testo. Copiare il testo seguente e salvarlo in locale come file *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Aprire Azure Storage Explorer. Espandere l'account di archiviazione. Fare clic con il pulsante destro del mouse su **Contenitori BLOB** e scegliere **Crea contenitore BLOB**.
1. Assegnare al nuovo contenitore il nome *adfv2branch* e selezionare **Carica** per aggiungere il file *input.txt* al contenitore.

## Creare un progetto di Visual Studio<a name="create-visual-studio-project"></a>

Creare un'applicazione console .NET in C#:

1. Avviare Visual Studio e selezionare **Crea un nuovo progetto**.
1. In **Crea un nuovo progetto** scegliere **App console (.NET Framework)** per C# e selezionare **Avanti**.
1. Assegnare al progetto il nome *ADFv2BranchTutorial*.
1. Selezionare **.NET versione 4.5.2** o successiva e quindi **Crea**.

### <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

1. Fare clic su **Strumenti** > **Gestione Pacchetti NuGet** > **Console di Gestione pacchetti**.
1. In **Console di Gestione pacchetti** eseguire questi comandi per installare i pacchetti. Per i dettagli, vedere il [pacchetto NuGet Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Creare un client di data factory

1. Aprire *Program.cs* e aggiungere le istruzioni seguenti:

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

1. Aggiungere queste variabili statiche alla classe `Program`. Sostituire i segnaposto con i valori personalizzati.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Aggiungere il codice seguente al metodo `Main` . Questo codice crea un'istanza della classe `DataFactoryManagementClient`. Usare quindi questo oggetto per creare una data factory, il servizio collegato, i set di dati e la pipeline. È possibile usare questo oggetto anche per monitorare i dettagli sull'esecuzione della pipeline.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Creare una data factory

1. Aggiungere un metodo `CreateOrUpdateDataFactory` al file *Program.cs*:

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Aggiungere la riga seguente al metodo `Main` per creare una data factory:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Creare un servizio collegato Archiviazione di Azure

1. Aggiungere un metodo `StorageLinkedServiceDefinition` al file *Program.cs*:

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Aggiungere la riga seguente al metodo `Main` per creare un servizio collegato Archiviazione di Azure:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Per altre informazioni sulle proprietà supportate e i dettagli, vedere [Proprietà del servizio collegato](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Creare set di dati

In questa sezione si creano due set di dati: uno per l'origine e uno per il sink.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Creare un set di dati per un BLOB di Azure di origine

Aggiungere un metodo per creare un *set di dati del BLOB di Azure*. Per altre informazioni sulle proprietà supportate e i dettagli, vedere [Proprietà del set di dati del BLOB di Azure](connector-azure-blob-storage.md#dataset-properties).

Aggiungere un metodo `SourceBlobDatasetDefinition` al file *Program.cs*:

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

Definire un set di dati che rappresenta i dati di origine nel BLOB di Azure. Questo set di dati del BLOB fa riferimento al servizio collegato Archiviazione di Azure supportato nel passaggio precedente. Il set di dati del BLOB descrive la posizione del BLOB da cui eseguire la copia: *FolderPath* e *FileName*.

Si noti l'uso dei parametri per *FolderPath*. `sourceBlobContainer` è il nome del parametro e l'espressione viene sostituita con i valori passati nell'esecuzione della pipeline. La sintassi per definire i parametri è `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Creare un set di dati per un BLOB di Azure sink

1. Aggiungere un metodo `SourceBlobDatasetDefinition` al file *Program.cs*:

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Aggiungere il codice seguente al metodo `Main` per creare i set di dati dei BLOB di Azure di origine e sink.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Creare una classe C#: EmailRequest

Nel progetto C# creare una classe denominata `EmailRequest`. Questa classe definisce le proprietà inviate dalla pipeline nel corpo della richiesta quando viene inviato un messaggio di posta elettronica. In questa esercitazione, dalla pipeline al messaggio di posta elettronica vengono inviate quattro proprietà.

* Message. Corpo del messaggio di posta elettronica. Per una copia corretta, questa proprietà contiene la quantità di dati scritti. Per una copia non riuscita, contiene i dettagli dell'errore.
* dataFactoryName. Nome della data factory.
* pipelineName. Nome della pipeline.
* receiver. Parametro passato. Questa proprietà specifica il destinatario del messaggio di posta elettronica.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>Creare gli endpoint del flusso di lavoro del messaggio di posta elettronica

Per attivare l'invio di un messaggio di posta elettronica, usare [App per la logica](../logic-apps/logic-apps-overview.md) per definire il flusso di lavoro. Per informazioni dettagliate sulla creazione di un flusso di lavoro di app per la logica, vedere [Come creare un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Flusso di lavoro del messaggio di posta elettronica di operazione riuscita

Nel [portale di Azure](https://portal.azure.com) creare un flusso di lavoro di app per la logica denominato *CopySuccessEmail*. Definire `When an HTTP request is received` come trigger del flusso di lavoro. Per il trigger di richiesta, compilare `Request Body JSON Schema` con il codice JSON seguente:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Il flusso di lavoro sarà simile all'esempio seguente:

![Flusso di lavoro del messaggio di posta elettronica di operazione riuscita](media/tutorial-control-flow/success-email-workflow-trigger.png)

Il contenuto JSON è in linea con la classe `EmailRequest` creata nella sezione precedente.

Aggiungere un'azione `Office 365 Outlook – Send an email`. Per l'azione **Invia un messaggio di posta elettronica** personalizzare la formattazione del messaggio nel modo desiderato, usando le proprietà passate nello schema JSON del **corpo** della richiesta. Ad esempio:

![Progettazione app per la logica: azione Invia un messaggio di posta elettronica](media/tutorial-control-flow/customize-send-email-action.png)

Dopo aver salvato il flusso di lavoro, copiare e salvare il valore di **URL POST HTTP** dal trigger.

## <a name="fail-email-workflow"></a>Flusso di lavoro del messaggio di posta elettronica di operazione non riuscita

Clonare **CopySuccessEmail** creando un altro flusso di lavoro di app per la logica denominato *CopyFailEmail*. Nel trigger di richiesta, `Request Body JSON schema` è uguale. Modificare la formattazione del messaggio di posta elettronica, ad esempio `Subject`, per adattarlo a un messaggio di posta elettronica di operazione non riuscita. Di seguito è fornito un esempio:

![Progettazione app per la logica: flusso di lavoro del messaggio di posta elettronica di operazione non riuscita](media/tutorial-control-flow/fail-email-workflow.png)

Dopo aver salvato il flusso di lavoro, copiare e salvare il valore di **URL POST HTTP** dal trigger.

Si avranno ora due URL di flusso di lavoro, come negli esempi seguenti:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Creare una pipeline

Tornare al progetto in Visual Studio. A questo punto si aggiungerà il codice per creare una pipeline con un'attività di copia e una proprietà `DependsOn`. In questa esercitazione, la pipeline contiene soltanto un'attività di copia che usa il set di dati del BLOB come origine e un altro set di dati del BLOB come sink. In caso di esito positivo o negativo dell'attività di copia vengono chiamate attività di posta elettronica diverse.

In questa pipeline si usano le funzionalità seguenti:

* Parametri
* Attività Web
* Dipendenza di attività
* Uso dell'output di un'attività come input per un'altra attività

1. Aggiungere questo metodo al progetto. Le sezioni seguenti offrono maggiori dettagli.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Aggiungere la riga seguente al metodo `Main` per creare la pipeline:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parametri

La prima sezione del codice della pipeline definisce i parametri.

* `sourceBlobContainer`. Il set di dati del BLOB di origine utilizza questo parametro nella pipeline.
* `sinkBlobContainer`. Il set di dati del BLOB sink utilizza questo parametro nella pipeline.
* `receiver`. Questo parametro viene usato dalle due attività Web della pipeline che inviano i messaggi di posta elettronica di operazione riuscita o non riuscita al destinatario.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Attività Web

L'attività Web consente una chiamata a qualsiasi endpoint REST. Per altre informazioni sull'attività, vedere [Attività Web in Azure Data Factory](control-flow-web-activity.md). Questa pipeline usa un'attività Web per chiamare il flusso di lavoro di app per la logica per il messaggio di posta elettronica. Si creano due attività Web: una chiama il flusso di lavoro `CopySuccessEmail` e l'altra chiama `CopyFailWorkFlow`.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

Nella proprietà `Url` incollare gli endpoint **URL POST HTTP** dal flusso di lavoro di app per la logica. Nella proprietà `Body` passare un'istanza della classe `EmailRequest`. La richiesta del messaggio di posta elettronica contiene le proprietà seguenti.

* Message. Passa il valore di `@{activity('CopyBlobtoBlob').output.dataWritten`. Accede a una proprietà della precedente attività di copia e passa il valore di `dataWritten`. In caso di esito negativo, passa invece l'output di errore di `@{activity('CopyBlobtoBlob').error.message`.
* Nome della data factory. Passa il valore di `@{pipeline().DataFactory}`. Questa variabile di sistema consente di accedere al nome di data factory corrispondente. Per un elenco delle variabili di sistema, vedere [Variabili di sistema](control-flow-system-variables.md).
* Nome della pipeline. Passa il valore di `@{pipeline().Pipeline}`. Questa variabile di sistema consente di accedere al nome di pipeline corrispondente.
* Destinatario. Passa il valore di `"@pipeline().parameters.receiver"`. Accede ai parametri della pipeline.

Questo codice crea una nuova dipendenza di attività in base all'attività di copia precedente.

## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline

Aggiungere il codice seguente al metodo `Main` per attivare un'esecuzione della pipeline.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};
 
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Classe principale

Il metodo `Main` finale avrà un aspetto simile al seguente.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Compilare ed eseguire il programma per attivare un'esecuzione della pipeline.

## <a name="monitor-a-pipeline-run"></a>Monitorare un'esecuzione della pipeline

1. Aggiungere il codice seguente al metodo `Main`:

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

    Questo codice controlla continuamente lo stato dell'esecuzione fino al termine della copia dei dati.

1. Aggiungere il codice seguente al metodo `Main` per recuperare i dettagli dell'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti/scritti:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Eseguire il codice

Compilare e avviare l'applicazione, quindi verificare l'esecuzione della pipeline.

L'applicazione visualizza lo stato di avanzamento della creazione della data factory, del servizio collegato, dei set di dati, della pipeline e dell'esecuzione della pipeline. Controlla quindi lo stato di esecuzione della pipeline. Attendere fino a quando non vengono visualizzati i dettagli sull'esecuzione dell'attività di copia con le dimensioni dei dati letti/scritti. Usare quindi strumenti come Azure Storage Explorer per verificare che il BLOB sia stato copiato da *inputBlobPath* a *outputBlobPath*, come specificato nelle variabili.

L'output sarà simile all'esempio seguente:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state completate le attività seguenti:

> [!div class="checklist"]
> * Creare una data factory
> * Creare un servizio collegato Archiviazione di Azure
> * Creare un set di dati del BLOB di Azure
> * Creare una pipeline contenente un'attività di copia e un'attività Web
> * Inviare gli output delle attività alle attività successive
> * Usare il passaggio di parametri e le variabili di sistema
> * Avviare un'esecuzione della pipeline
> * Monitorare le esecuzioni di pipeline e attività

È ora possibile passare alla sezione Concetti per altre informazioni su Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipeline e attività](concepts-pipelines-activities.md)