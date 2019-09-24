---
title: Esportare e importare le registrazioni di hub di notifica di Azure in blocco | Microsoft Docs
description: Informazioni su come usare il supporto bulk di hub di notifica per eseguire un numero elevato di operazioni in un hub di notifica o per esportare tutte le registrazioni.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 8eb03a42f38c0cc7fe82eda6a81d1c8c1213ec74
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212388"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Esportare e importare in blocco le registrazioni di hub di notifica di Azure
Esistono scenari in cui è necessario creare o modificare un numero elevato di registrazioni in un hub di notifica. Alcuni di questi scenari sono gli aggiornamenti dei tag che seguono i calcoli batch o la migrazione di un'implementazione push esistente per l'uso di hub di notifica.

Questo articolo illustra come eseguire un numero elevato di operazioni in un hub di notifica o di esportare tutte le registrazioni in blocco.

## <a name="high-level-flow"></a>Flusso di alto livello
Il supporto per batch è progettato per supportare processi con esecuzione prolungata che coinvolgono milioni di registrazioni. Per ottenere questo livello di scalabilità, il supporto batch Usa archiviazione di Azure per archiviare i dettagli e l'output del processo. Per le operazioni di aggiornamento in blocco, è necessario che l'utente crei un file in un contenitore BLOB, il cui contenuto è l'elenco delle operazioni di aggiornamento della registrazione. Quando si avvia il processo, l'utente fornisce un URL al BLOB di input, insieme a un URL di una directory di output (anche in un contenitore BLOB). Dopo l'avvio del processo, l'utente può controllare lo stato eseguendo una query su un percorso URL fornito all'avvio del processo. Un processo specifico può eseguire solo operazioni di un tipo specifico (creazione, aggiornamento o eliminazione). Le operazioni di esportazione vengono eseguite in analogie.

## <a name="import"></a>Importa

### <a name="set-up"></a>Configura
In questa sezione si presuppone che siano presenti le entità seguenti:

- Un hub di notifica con provisioning.
- Un contenitore BLOB di archiviazione di Azure.
- Riferimenti al pacchetto NuGet di [archiviazione di Azure](https://www.nuget.org/packages/windowsazure.storage/) e al pacchetto NuGet di [Hub di notifica](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

### <a name="create-input-file-and-store-it-in-a-blob"></a>Creare un file di input e archiviarlo in un BLOB
Un file di input contiene un elenco di registrazioni serializzate in XML, una per riga. Con Azure SDK, l'esempio di codice seguente mostra come serializzare le registrazioni e caricarle nel contenitore BLOB.

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

Oltre agli URL di input e di output, in questo esempio viene `NotificationHubJob` creato un oggetto che `JobType` contiene un oggetto, che può essere uno dei tipi seguenti:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Una volta completata la chiamata, il processo viene continuato dall'hub di notifica ed è possibile controllarne lo stato con la chiamata a [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet).

Al termine del processo, è possibile esaminare i risultati esaminando i seguenti file nella directory di output:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Questi file contengono l'elenco delle operazioni riuscite e non riuscite dal batch. Il formato del file `.cvs`è, in cui ogni riga ha il numero di riga del file di input originale e l'output dell'operazione, in genere la descrizione della registrazione creata o aggiornata.

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

## <a name="export"></a>Esporta
L'esportazione della registrazione è simile all'importazione, con le differenze seguenti:

- È necessario solo l'URL di output.
- Si crea un NotificationHubJob di tipo ExportRegistrations.

### <a name="sample-code-snippet"></a>Frammento di codice di esempio
Ecco un frammento di codice di esempio per esportare le registrazioni in Java:

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
Per ulteriori informazioni sulle registrazioni, vedere gli articoli seguenti:

- [Gestione delle registrazioni](notification-hubs-push-notification-registration-management.md)
- [Tag per le registrazioni](notification-hubs-tags-segment-push-message.md)
- [Registrazioni modello](notification-hubs-templates-cross-platform-push-messages.md)
