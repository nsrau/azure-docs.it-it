---
title: Esportare e importare in blocco le registrazioni di hub di notifica di Azure | Microsoft Docs
description: Informazioni su come usare il supporto tecnico di blocco di hub di notifica per eseguire un numero elevato di operazioni in un hub di notifica, oppure per esportare tutte le registrazioni.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: jowargo
ms.openlocfilehash: d7e38e8eca58c06fc6896887522b320a797fc42e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575308"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Esportare e importare in blocco le registrazioni di hub di notifica di Azure
Esistono scenari in cui è necessario creare o modificare quantità elevate di registrazioni in un hub di notifica. Alcuni di questi scenari sono aggiornamenti di tag seguenti calcoli in batch o la migrazione di un'implementazione push esistente per usare hub di notifica.

Questo articolo illustra come eseguire un numero elevato di operazioni in un hub di notifica, oppure per esportare tutte le registrazioni in blocco.

## <a name="high-level-flow"></a>Flusso di alto livello
Supporto di batch è progettato per supportare processi con esecuzione prolungata che coinvolgono milioni di registrazioni. Per ottenere questa scalabilità, supporto di batch Usa archiviazione di Azure per archiviare i dettagli del processo e output. Per le operazioni di aggiornamento bulk, l'utente viene richiesto di creare un file in un contenitore blob, il cui contenuto è l'elenco di operazioni di aggiornamento della registrazione. Quando si avvia il processo, l'utente fornisce un URL per il blob di input, oltre a un URL a una directory di output (anche in un contenitore blob). Dopo aver avviato il processo, l'utente può controllare lo stato eseguendo una query di un percorso URL fornito all'avvio del processo. Un processo specifico può eseguire solo operazioni di un tipo specifico (Crea, aggiorna o Elimina). Operazioni di esportazione vengono eseguite in modo analogo.

## <a name="import"></a>Importa

### <a name="set-up"></a>Configurare
In questa sezione si presuppone le entità seguenti:

- Un hub di notifica sottoposto a provisioning.
- Un contenitore blob di archiviazione di Azure.
- Fa riferimento per la [pacchetto NuGet di archiviazione di Azure](https://www.nuget.org/packages/windowsazure.storage/) e [pacchetto NuGet degli hub di notifica](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Creare il file di input e archiviarlo in un blob
Un file di input contiene un elenco di registrazioni serializzate in XML, uno per ogni riga. Usa Azure SDK, il codice seguente viene illustrato come serializzare le registrazioni e caricarle nel contenitore blob.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Il codice precedente serializza le registrazioni in memoria e quindi carica l'intero flusso in un blob. Se è stato caricato un file di più di pochi megabyte, vedere le indicazioni di blob di Azure su come eseguire questi passaggi. ad esempio, [BLOB in blocchi](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Creare token URL
Una volta caricato il file di input, generare gli URL da fornire all'hub di notifica per il file di input e la directory di output. È possibile usare due contenitori blob diversi per input e output.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>Inviare il processo
Con i due URL di input e output, è ora possibile avviare il processo di batch.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Oltre agli URL di input e outpui, questo esempio viene creato un `NotificationHubJob` oggetto che contiene un `JobType` oggetto, che può essere uno dei tipi seguenti:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Dopo il completamento della chiamata, il processo è continuato dall'hub di notifica ed è possibile controllarne lo stato con la chiamata a [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

Al termine del processo, è possibile esaminare i risultati esaminando i seguenti file nella directory di output:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Questi file contengono l'elenco delle operazioni riuscite e non riuscite del batch. Il formato di file è `.cvs`, in cui ogni riga ha il numero di riga del file di input originale e l'output dell'operazione (in genere la descrizione di registrazione creata o aggiornata).

### <a name="full-sample-code"></a>Codice di esempio completo
Esempio di codice seguente importa le registrazioni in un hub di notifica.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Esportazione
L'esportazione della registrazione è simile all'importazione, con le differenze seguenti:

- È necessario solo l'URL di output.
- Si crea un NotificationHubJob typu ExportRegistrations.

### <a name="sample-code-snippet"></a>Frammento di codice di esempio
Ecco un frammento di codice di esempio per l'esportazione di registrazioni in Java:

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle registrazioni, vedere gli articoli seguenti:

- [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- [Tag per le registrazioni](notification-hubs-tags-segment-push-message.md)
- [Registrazioni dei modelli](notification-hubs-templates-cross-platform-push-messages.md)
