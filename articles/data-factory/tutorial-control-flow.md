---
title: Diramazione in una pipeline di Azure Data Factory | Microsoft Docs
description: "Informazioni su come controllare il flusso dei dati in Azure Data Factory con la diramazione e il concatenamento delle attività."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/06/2017
ms.author: shlo
ms.openlocfilehash: 8a51ba22c6fea7a866815c33d164d72af08db8f0
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Diramazione e concatenamento delle attività in una pipeline di Data factory

[!INCLUDE [data-factory-what-is-include-md](../../includes/data-factory-what-is-include.md)]

#### <a name="this-tutorial"></a>Questa esercitazione

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

In questa esercitazione si crea una pipeline di Data Factory che illustra alcune funzionalità del flusso di controllo. La pipeline esegue una semplice copia da un contenitore nell'archivio BLOB di Azure a un altro contenitore nello stesso account di archiviazione. Se l'attività di copia ha esito positivo, si vogliono inviare i dettagli dell'operazione di copia completata (ad esempio, la quantità di dati scritti) in un messaggio di posta elettronica di operazione riuscita. Se l'attività di copia ha esito negativo, si vogliono inviare i dettagli dell'errore di copia (ad esempio, il messaggio di errore) in un messaggio di posta elettronica di operazione non riuscita. Nel corso dell'esercitazione verrà illustrato come passare i parametri.

Panoramica generale dello scenario: ![Panoramica](media/tutorial-control-flow/overview.png)

In questa esercitazione vengono completati i passaggi seguenti:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un servizio collegato Archiviazione di Azure
> * Creare un set di dati del BLOB di Azure
> * Creare una pipeline contenente un'attività di copia e un'attività Web
> * Inviare gli output delle attività alle attività successive
> * Utilizzare il passaggio di parametri e le variabili di sistema
> * Avviare un'esecuzione della pipeline
> * Monitorare le esecuzioni di pipeline e attività

In questa esercitazione viene usato .NET SDK. È possibile usare altri meccanismi per interagire con Azure Data Factory. Vedere "Guide introduttive" nel sommario.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* **Account di archiviazione di Azure**. Usare l'archivio BLOB come archivio dati di **origine**. Se non si ha un account di archiviazione di Azure, vedere l'articolo [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno.
* **Database SQL di Azure**. Usare il database come archivio dati **sink**. Se non si ha un database SQL di Azure, vedere la procedura per crearne uno nell'articolo [Creare un database SQL di Azure](../sql-database/sql-database-get-started-portal.md).
* **Visual Studio** 2013, 2015 o 2017. Nella procedura guidata illustrata in questo articolo viene usato Visual Studio 2017.
* **Scaricare e installare [Azure .NET SDK](http://azure.microsoft.com/downloads/)**.
* **Creare un'applicazione in Azure Active Directory** seguendo [queste istruzioni](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Annotare i valori seguenti, da usare nei passaggi successivi: **ID applicazione**, **chiave di autenticazione** e **ID tenant**. Assegnare l'applicazione al ruolo "**Collaboratore**" seguendo le istruzioni disponibili nello stesso articolo.

### <a name="create-blob-table"></a>Creare la tabella BLOB

1. Avviare il Blocco note. Copiare il testo seguente e salvarlo come file **input.txt** sul disco.

    ```
    John|Doe
    Jane|Doe
    ```
2. Usare strumenti come [Azure Storage Explorer](http://storageexplorer.com/) per creare il contenitore **adfv2branch** e per caricare il file **input.txt** nel contenitore.

## <a name="create-visual-studio-project"></a>Creare un progetto di Visual Studio

Creare un'applicazione console .NET in C# con Visual Studio 2015 o 2017.

1. Avviare **Visual Studio**.
2. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**. È necessario .NET versione 4.5.2 o successiva.
3. Selezionare **Visual C#** -> **App console (.NET Framework)** nell'elenco dei tipi di progetto a destra.
4. Immettere **ADFv2BranchTutorial** come nome.
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

2. Add these static variables to the **Program class**. Replace place-holders with your own values. Currently, Data Factory V2 allows you to create data factories only in the East US, East US2, and West Europe regions. The data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions.

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
Creare una funzione "CreateOrUpdateDataFactory" nel file Program.cs:

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



Aggiungere il codice seguente al metodo **Main** per creare una **data factory**. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Creare un servizio collegato Archiviazione di Azure
Creare una funzione "StorageLinkedServiceDefinition" nel file Program.cs:

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
Aggiungere il codice seguente al metodo **Main** per creare un **servizio collegato di Archiviazione di Azure**. Per informazioni sulle proprietà supportate e altri dettagli, vedere le [proprietà del servizio collegato BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties).

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Creare set di dati

In questa sezione vengono creati due set di dati: uno per l'origine e l'altro per il sink. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Creare un set di dati per il BLOB di Azure di origine
Aggiungere il codice seguente al metodo **Main** per creare un **set di dati del BLOB di Azure**. Per informazioni sulle proprietà supportate e altri dettagli, vedere le [proprietà dei set di dati dei BLOB di Azure](connector-azure-blob-storage.md#dataset-properties).

Definire un set di dati che rappresenta i dati di origine nel BLOB di Azure. Questo set di dati del BLOB fa riferimento al servizio collegato Archiviazione di Azure creato nel passaggio precedente e descrive quanto segue.

- Posizione del BLOB da cui eseguire la copia: **FolderPath** e **FileName**.
- Si noti l'uso dei parametri per FolderPath. "sourceBlobContainer" è il nome del parametro e l'espressione viene sostituita con i valori passati nell'esecuzione della pipeline. La sintassi per definire i parametri è `@pipeline().parameters.<parameterName>`

Creare una funzione "SourceBlobDatasetDefinition" nel file Program.cs:

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

### <a name="create-a-dataset-for-sink-azure-blob"></a>Creare un set di dati per il BLOB di Azure sink

Creare una funzione "SourceBlobDatasetDefinition" nel file Program.cs:

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

Aggiungere il codice seguente al metodo **Main** per creare i set di dati dei BLOB di Azure di origine e sink. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Creare una classe C#: EmailRequest
Nel progetto C# creare una classe denominata **EmailRequest**, che definisce le proprietà inviate dalla pipeline nel corpo della richiesta quando viene inviato un messaggio di posta elettronica. In questa esercitazione, dalla pipeline al messaggio di posta elettronica vengono inviate quattro proprietà.

- **Messaggio**: corpo del messaggio di posta elettronica. Se la copia ha esito positivo, questa proprietà contiene i dettagli dell'esecuzione (quantità di dati scritti). Se la copia ha esito negativo, contiene i dettagli dell'errore.
- **Nome data factory**: nome della data factory.
- **Nome pipeline**: nome della pipeline.
- **Destinatario**: parametro passato. Questa proprietà specifica il destinatario del messaggio di posta elettronica.

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
Per attivare l'invio di un messaggio di posta elettronica, usare [App per la logica](../logic-apps/logic-apps-what-are-logic-apps.md) per definire il flusso di lavoro. Per informazioni dettagliate sulla creazione di un flusso di lavoro di app per la logica, vedere l'articolo su [come creare un'app per la logica](../logic-apps/logic-apps-create-a-logic-app.md). 

### <a name="success-email-workflow"></a>Flusso di lavoro del messaggio di posta elettronica di operazione riuscita 
Creare un flusso di lavoro di app per la logica denominato `CopySuccessEmail`. Definire `When an HTTP request is received` come trigger del flusso di lavoro e aggiungere un'azione `Office 365 Outlook – Send an email`.

![Flusso di lavoro del messaggio di posta elettronica di operazione riuscita](media/tutorial-control-flow/success-email-workflow.png)

Per il trigger di richiesta, compilare `Request Body JSON Schema` con il codice JSON seguente:

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
Questo è in linea con la classe **EmailRequest** creata nella sezione precedente. 

La richiesta in Progettazione app per la logica avrà un aspetto simile al seguente:

![Progettazione app per la logica: richiesta](media/tutorial-control-flow/logic-app-designer-request.png)

Per l'azione **Invia un messaggio di posta elettronica** personalizzare la formattazione del messaggio nel modo desiderato, utilizzando le proprietà passate nello schema JSON del corpo della richiesta. Di seguito è fornito un esempio:

![Progettazione app per la logica: azione Invia un messaggio di posta elettronica](media/tutorial-control-flow/send-email-action.png)

Prendere nota dell'URL della richiesta HTTP Post per il flusso di lavoro del messaggio di posta elettronica di operazione riuscita:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Flusso di lavoro del messaggio di posta elettronica di operazione non riuscita 
Clonare **CopySuccessEmail** e creare un altro flusso di lavoro di app per la logica per **CopyFailEmail**. Nel trigger di richiesta, `Request Body JSON schema` è uguale. Modificare semplicemente la formattazione del messaggio di posta elettronica, ad esempio `Subject`, per adattarlo a un messaggio di posta elettronica di operazione non riuscita. Di seguito è fornito un esempio:

![Progettazione app per la logica: flusso di lavoro del messaggio di posta elettronica di operazione non riuscita](media/tutorial-control-flow/fail-email-workflow.png)

Prendere nota dell'URL della richiesta HTTP Post per il flusso di lavoro del messaggio di posta elettronica di operazione non riuscita:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Si avranno ora due URL di flusso di lavoro:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>Creare una pipeline
Aggiungere il codice seguente al metodo Main per creare una pipeline con un'attività di copia e una proprietà dependsOn. In questa esercitazione, la pipeline contiene soltanto un'attività di copia che usa il set di dati del BLOB come origine e un altro set di dati del BLOB come sink. Quando l'attività di copia viene completata o non riesce, vengono chiamate diverse attività di posta elettronica.

In questa pipeline si usano le funzionalità seguenti:

- Parametri
- Attività Web
- Dipendenza di attività
- Uso dell'output di un'attività come input per l'attività successiva

Verrà ora esaminata sezione per sezione la pipeline seguente:

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
Aggiungere il codice seguente al metodo **Main** per creare la pipeline:

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>Parametri
La prima sezione della pipeline definisce i parametri. 

- sourceBlobContainer: parametro nella pipeline utilizzato dal set di dati del BLOB di origine.
- sinkBlobContainer: parametro nella pipeline utilizzato dal set di dati del BLOB sink.
- receiver: parametro nella pipeline utilizzato dalle due attività Web, relativo all'indirizzo di posta elettronica che riceverà il messaggio.


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>Attività Web
L'attività Web consente una chiamata a qualsiasi endpoint REST. Per altre informazioni sull'attività, vedere l'articolo relativo all'[attività Web](control-flow-web-activity.md). Questa pipeline usa un'attività Web per chiamare il flusso di lavoro di app per la logica per il messaggio di posta elettronica. Si creano due attività Web: una chiama il flusso di lavoro **CopySuccessEmail** e l'altra chiama **CopyFailWorkFlow**.

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
Nella proprietà "Url" incollare di conseguenza gli endpoint degli URL della richiesta dal flusso di lavoro di app per la logica. Nella proprietà "Body" passare un'istanza della classe "EmailRequest". La richiesta del messaggio di posta elettronica contiene le proprietà seguenti.

- Messaggio: passa il valore di `@{activity('CopyBlobtoBlob').output.dataWritten`. Accede a una proprietà della precedente attività di copia e passa il valore di dataWritten. In caso di esito negativo, passa invece l'output di errore di `@{activity('CopyBlobtoBlob').error.message`.
- Nome data factory: passa il valore di `@{pipeline().DataFactory}`. È una variabile di sistema che consente di accedere al nome di data factory corrispondente. Per un elenco delle variabili di sistema, vedere l'articolo relativo alle [variabili di sistema](control-flow-system-variables.md).
- Nome pipeline: passa il valore di `@{pipeline().Pipeline}`. È anche questa una variabile di sistema, che consente di accedere al nome di pipeline corrispondente. 
- Destinatario: passa il valore di "@pipeline().parameters.receiver"), per l'accesso ai parametri della pipeline.
 
Questo codice crea una nuova dipendenza di attività, in base all'attività di copia da cui è preceduto.

## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline
Aggiungere il codice seguente al metodo **Main** per **attivare un'esecuzione della pipeline**.

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
Il metodo Main finale avrà un aspetto simile al seguente. Compilare ed eseguire il programma per attivare un'esecuzione della pipeline.

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

## <a name="monitor-a-pipeline-run"></a>Monitorare un'esecuzione della pipeline
1. Aggiungere il codice seguente al metodo **Main** per controllare continuamente lo stato dell'esecuzione della pipeline fino al termine della copia dei dati.

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
La console stampa lo stato di creazione della data factory, del servizio collegato, dei set di dati, della pipeline e dell'esecuzione della pipeline. Controlla quindi lo stato di esecuzione della pipeline. Attendere fino a quando non vengono visualizzati i dettagli sull'esecuzione dell'attività di copia con le dimensioni dei dati letti/scritti. Usare quindi strumenti come Azure Storage Explorer per verificare che i BLOB siano stati copiati da "inputBlobPath" a "outputBlobPath", come specificato nelle variabili.

**Output di esempio:**

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
In questa esercitazione sono stati eseguiti i passaggi seguenti: 

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un servizio collegato Archiviazione di Azure
> * Creare un set di dati del BLOB di Azure
> * Creare una pipeline contenente un'attività di copia e un'attività Web
> * Inviare gli output delle attività alle attività successive
> * Utilizzare il passaggio di parametri e le variabili di sistema
> * Avviare un'esecuzione della pipeline
> * Monitorare le esecuzioni di pipeline e attività

È ora possibile passare alla sezione Concetti per altre informazioni su Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipeline e attività](concepts-pipelines-activities.md)