---
title: Usare l&quot;archiviazione code di Azure per monitorare le notifiche dei processi di Servizi multimediali con .NET | Microsoft Docs
description: "Informazioni su come usare l&quot;archiviazione code di Azure per monitorare le notifiche dei processi di Servizi multimediali. L&quot;esempio di codice è scritto in C# e usa l&quot;SDK di Servizi multimediali per .NET."
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: a9bb00c6f0a691ac3a67d19ada4341d8d94876c1
ms.lasthandoff: 04/25/2017


---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Usare l'archiviazione code di Azure per monitorare le notifiche dei processi di Servizi multimediali con .NET
Quando si esegue un processo, spesso è necessario monitorarne l'avanzamento. È possibile controllare l'avanzamento usando l'archiviazione code di Azure per monitorare le notifiche dei processi di Servizi multimediali di Azure, come descritto in questo articolo. È inoltre possibile definire un gestore eventi **StateChanged**, come descritto in [Monitorare lo stato dei processi mediante .NET](media-services-check-job-progress.md).  

## <a name="use-queue-storage-to-monitor-media-services-job-notifications"></a>Usare il servizio archiviazione code per monitorare le notifiche dei processi di Servizi multimediali
Durante l'elaborazione dei processi multimediali, Servizi multimediali può recapitare notifiche ad [archiviazione code](../storage/storage-dotnet-how-to-use-queues.md). Questo argomento illustra come ricevere questi messaggi di notifica dal servizio di archiviazione code.

È possibile accedere ai messaggi distribuiti al servizio di archiviazione di accodamento da ogni parte del mondo. L'architettura di messaggistica di archiviazione code è affidabile e altamente scalabile. Tra i vari metodi disponibili è preferibile usare il polling dell'archiviazione code per i messaggi.

Può essere necessario ascoltare le notifiche di Servizi multimediali quando, ad esempio, si sta sviluppando un sistema di gestione dei contenuti ed è necessario che il sistema effettui alcune attività aggiuntive dopo il completamento di un processo di codifica (ad esempio, deve attivare il passaggio successivo di un flusso di lavoro o pubblicare contenuti).

### <a name="considerations"></a>Considerazioni
Quando si sviluppano applicazioni di Servizi multimediali che usano l'archiviazione code, tenere presente quanto segue:

* Il servizio di archiviazione code non garantisce un recapito ordinato dei messaggi di tipo FIFO (First-In-First-Out). Per altre informazioni, vedere [Analogie e differenze tra le code di Azure e le code del bus di servizio](https://msdn.microsoft.com/library/azure/hh767287.aspx).
* Archiviazione code non è un servizio di push. È necessario eseguire il polling della coda.
* È possibile disporre di un qualsiasi numero di code. Per altre informazioni, vedere [API REST del servizio di accodamento](https://docs.microsoft.com/rest/api/storageservices/Queue-Service-REST-API).
* Archiviazione code presenta alcune limitazioni e specifiche da tenere presenti. Queste sono descritte in [Analogie e differenze tra le code di Azure e le code del bus di servizio di Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted).

### <a name="code-example"></a>Esempio di codice
L'esempio di codice contenuto in questa sezione effettua quanto segue:

1. Definisce la classe **EncodingJobMessage** che esegue il mapping al formato del messaggio di notifica. Il codice deserializza i messaggi ricevuti dalla coda in oggetti del tipo **EncodingJobMessage** .
2. Carica informazioni sugli account di Servizi multimediali e di archiviazione dal file app.config La coda di esempio usa queste informazioni per creare gli oggetti **CloudMediaContext** e **CloudQueue**.
3. Crea la coda che riceve i messaggi di notifica relativi al processo di codifica.
4. Crea l'endpoint di notifica di cui viene eseguito il mapping alla coda.
5. Collega l'endpoint di notifica al processo e invia il processo di codifica. A un processo possono essere collegati anche più endpoint di notifica.
6. Passa **NotificationJobState.FinalStatesOnly** al metodo **AddNew**. (In questo esempio vanno notati solo gli stati finali dell'elaborazione dei processi.)

        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
7. Se si passa **NotificationJobState.All**, si ricevono tutte le notifiche di modifica dello stato: in coda, pianificate, in elaborazione e completate. Tuttavia, come indicato in precedenza, il servizio di archiviazione code non garantisce un recapito ordinato. Per ordinare i messaggi, usare la proprietà **Timestamp** (definita nel tipo **EncodingJobMessage** nell'esempio seguente). Sono possibili i messaggi duplicati. Per verificare la presenza di duplicati, usare la **proprietà ETag** (definita nel tipo **EncodingJobMessage**). È possibile inoltre che alcune notifiche di modifica dello stato vengano ignorate.
8. Attende che il processo abbia raggiunto lo stato Completato controllando la coda ogni 10 secondi. Elimina i messaggi man mano che vengono elaborati.
9. Elimina la coda e l'endpoint di notifica.

> [!NOTE]
> Il modo migliore per monitorare lo stato di un processo è quello di ascoltare i messaggi di notifica, come illustrato nel seguente esempio.
>
> In alternativa, è possibile controllare lo stato di un processo usando la proprietà **IJob.State** .  Un messaggio di notifica relativo al completamento del processo potrebbe essere ricevuto prima che lo stato in **IJob** sia impostato su **Operazione completata**. La proprietà **IJob.State** riflette lo stato esatto con un leggero ritardo.
>
>

    using System;
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Web;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Runtime.Serialization.Json;

    namespace JobNotification
    {
        public class EncodingJobMessage
        {
            // MessageVersion is used for version control.
            public String MessageVersion { get; set; }

            // Type of the event. Valid values are
            // JobStateChange and NotificationEndpointRegistration.
            public String EventType { get; set; }

            // ETag is used to help the customer detect if
            // the message is a duplicate of another message previously sent.
            public String ETag { get; set; }

            // Time of occurrence of the event.
            public String TimeStamp { get; set; }

            // Collection of values specific to the event.

            // For the JobStateChange event the values are:
            //     JobId - Id of the Job that triggered the notification.
            //     NewState- The new state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
            //     OldState- The old state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

            // For the NotificationEndpointRegistration event the values are:
            //     NotificationEndpointId- Id of the NotificationEndpoint
            //          that triggered the notification.
            //     State- The state of the Endpoint.
            //          Valid values are: Registered and Unregistered.

            public IDictionary<string, object> Properties { get; set; }
        }

        class Program
        {
            private static CloudMediaContext _context = null;
            private static CloudQueue _queue = null;
            private static INotificationEndPoint _notificationEndPoint = null;

            private static readonly string _singleInputMp4Path =
                Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");

            static void Main(string[] args)
            {
                // Get the values from app.config file.
                string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
                string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
                string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];


                string endPointAddress = Guid.NewGuid().ToString();

                // Create the context.
                _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);

                // Create the queue that will be receiving the notification messages.
                _queue = CreateQueue(storageConnectionString, endPointAddress);

                // Create the notification point that is mapped to the queue.
                _notificationEndPoint =
                        _context.NotificationEndPoints.Create(
                        Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


                if (_notificationEndPoint != null)
                {
                    IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                    WaitForJobToReachedFinishedState(job.Id);
                }

                // Clean up.
                _queue.Delete();      
                _notificationEndPoint.Delete();
           }


            static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
            {
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

                // Create the queue client
                CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

                // Retrieve a reference to a queue
                CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

                // Create the queue if it doesn't already exist
                queue.CreateIfNotExists();

                return queue;
            }


            public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

                //Create an encrypted asset and upload the mp4.
                IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                    inputMediaFilePath);

                // Get a media processor reference, and pass to it the name of the
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                // Create a task with the conversion details, using a configuration file.
                ITask task = job.Tasks.AddNew("My encoding Task",
                    processor,
                    "Adaptive Streaming",
                    Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);

                // Add a notification point to the job. You can add multiple notification points.  
                job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                    _notificationEndPoint);

                job.Submit();

                return job;
            }

            public static void WaitForJobToReachedFinishedState(string jobId)
            {
                int expectedState = (int)JobState.Finished;
                int timeOutInSeconds = 600;

                bool jobReachedExpectedState = false;
                DateTime startTime = DateTime.Now;
                int jobState = -1;

                while (!jobReachedExpectedState)
                {
                    // Specify how often you want to get messages from the queue.
                    Thread.Sleep(TimeSpan.FromSeconds(10));

                    foreach (var message in _queue.GetMessages(10))
                    {
                        using (Stream stream = new MemoryStream(message.AsBytes))
                        {
                            DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                            settings.UseSimpleDictionaryFormat = true;
                            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                            EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                            Console.WriteLine();

                            // Display the message information.
                            Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                            Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                            Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                            Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                            foreach (var property in encodingJobMsg.Properties)
                            {
                                Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                            }

                            // We are only interested in messages
                            // where EventType is "JobStateChange".
                            if (encodingJobMsg.EventType == "JobStateChange")
                            {
                                string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                                if (JobId == jobId)
                                {
                                    string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                    string newJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                    JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                    JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                    if (newJobState == (JobState)expectedState)
                                    {
                                        Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                            jobId, newJobState);
                                        jobReachedExpectedState = true;
                                        break;
                                    }
                                }
                            }
                        }
                        // Delete the message after we've read it.
                        _queue.DeleteMessage(message);
                    }

                    // Wait until timeout
                    TimeSpan timeDiff = DateTime.Now - startTime;
                    bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                    if (timedOut)
                    {
                        Console.WriteLine(@"Timeout for checking job notification messages,
                                            latest found state ='{0}', wait time = {1} secs",
                            jobState,
                            timeDiff.TotalSeconds);

                        throw new TimeoutException();
                    }
                }
            }

            static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
            {
                var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                    assetCreationOptions);

                var fileName = Path.GetFileName(singleFilePath);

                var assetFile = asset.AssetFiles.Create(fileName);

                Console.WriteLine("Created assetFile {0}", assetFile.Name);
                Console.WriteLine("Upload {0}", assetFile.Name);

                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading of {0}", assetFile.Name);

                return asset;
            }

            static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }
        }
    }

Il precedente esempio ha prodotto l'output seguente. I valori possono variare.

    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4

    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35

    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
    State: Finished


## <a name="next-step"></a>Passaggio successivo
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

