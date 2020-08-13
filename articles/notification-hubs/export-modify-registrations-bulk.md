---
title: Esportare e importare le registrazioni di hub di notifica di Azure in blocco | Microsoft Docs
description: Informazioni su come usare il supporto bulk di hub di notifica per eseguire un numero elevato di operazioni in un hub di notifica o per esportare tutte le registrazioni.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: b9199c5ca70f0846f48c7b4ebaaa6cb38b969173
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142333"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Esportare e importare in blocco le registrazioni di hub di notifica di Azure

In alcuni scenari è necessario creare o modificare quantità elevate di registrazioni i in hub di notifica. Alcuni di questi scenari sono gli aggiornamenti dei tag che seguono i calcoli batch o la migrazione di un'implementazione push esistente per l'uso di hub di notifica di Azure.

Questo articolo illustra come eseguire un numero elevato di operazioni in un hub di notifica o di esportare tutte le registrazioni in blocco.

## <a name="high-level-flow"></a>Flusso di alto livello

Il supporto in batch è stato progettato per supportare processi a lunga esecuzione che coinvolgono milioni di registrazioni. Per ottenere questo livello di scalabilità, il supporto batch Usa archiviazione di Azure per archiviare i dettagli e l'output del processo. Per operazioni di aggiornamento in blocco, l'utente deve creare un file in un contenitore BLOB, che deve includere l'elenco di operazioni di aggiornamento delle registrazioni. Quando avvia il processo, l'utente fornisce un URL per il BLOB di input, oltre a un URL per una directory di output, che si trova in un contenitore BLOB. Dopo l'avvio del processo, l'utente può controllare lo stato eseguendo una query su un percorso URL fornito all'avvio del processo. Un processo specifico può eseguire solo operazioni di un tipo specifico (creazione, aggiornamento o eliminazione). Le operazioni di esportazione sono eseguite in modo analogo.

## <a name="import"></a>Importa

### <a name="set-up"></a>Configurazione

In questa sezione si presuppone che siano presenti le entità seguenti:

- Un hub di notifica sottoposto a provisioning.
- Un contenitore BLOB dell'archiviazione di Azure.
- Riferimenti al pacchetto NuGet di [archiviazione di Azure](https://www.nuget.org/packages/windowsazure.storage/) e al pacchetto NuGet di [Hub di notifica](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Creare il file di input e archiviarlo in un BLOB

Un file di input include un elenco di registrazioni serializzate in XML, una per riga. Con Azure SDK, l'esempio di codice seguente mostra come serializzare le registrazioni e caricarle nel contenitore BLOB:

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(registrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Il codice precedente serializza le registrazioni in memoria e quindi carica l'intero flusso in un BLOB. Se è stato caricato un file di oltre pochi megabyte, vedere le linee guida per i BLOB di Azure su come eseguire questi passaggi. ad esempio, [BLOB in blocchi](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>Creare token URL

Una volta caricato il file di input, generare gli URL da fornire all'hub di notifica sia per il file di input che per la directory di output. È possibile usare due contenitori BLOB diversi per l'input e l'output.

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

Con i due URL di input e output, è ora possibile avviare il processo batch.

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

Oltre agli URL di input e di output, in questo esempio viene creato un `NotificationHubJob` oggetto che contiene un `JobType` oggetto, che può essere uno dei tipi seguenti:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Una volta completata la chiamata, il processo viene continuato dall'hub di notifica ed è possibile controllarne lo stato con la chiamata a [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

Al termine del processo è possibile esaminare i risultati, verificando i file seguenti nella directory di output:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Questi file includono l'elenco delle operazioni riuscite e non riuscite del batch. Il formato del file è `.cvs` , in cui ogni riga ha il numero di riga del file di input originale e l'output dell'operazione, in genere la descrizione della registrazione creata o aggiornata.

### <a name="full-sample-code"></a>Codice di esempio completo

Il codice di esempio seguente importa le registrazioni in un hub di notifica.

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

            // Write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            // Import this file
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
                builder.AppendLine(registrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
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

L'esportazione della registrazione è analoga all'importazione, con le differenze seguenti:

- È necessario solo l'URL di output.
- Si crea un NotificationHubJob di tipo ExportRegistrations.

### <a name="sample-code-snippet"></a>Frammento di codice di esempio

Di seguito è riportato un frammento di codice di esempio per esportare le registrazioni in Java:

```java
// Submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// Wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle registrazioni, vedere gli articoli seguenti:

- [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- [Tag per le registrazioni](notification-hubs-tags-segment-push-message.md)
- [Registrazioni modello](notification-hubs-templates-cross-platform-push-messages.md)
