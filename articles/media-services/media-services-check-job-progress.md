<properties 
	pageTitle="Come controllare lo stato dei processi mediante .NET" 
	description="Informazioni su come usare il codice del gestore eventi per tenere traccia dello stato dei processi e inviare aggiornamenti di stato. Negli esempi di codice, scritti in C#, viene usato l'SDK di Servizi multimediali per .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/07/2015"   
	ms.author="juliako"/>

#Procedura: Controllare lo stato dei processi

> [AZURE.SELECTOR]
- [Portal](media-services-portal-check-job-progress.md)
- [.NET](media-services-check-job-progress.md)
- [REST](media-services-rest-check-job-progress.md)

Quando si esegue un processo, spesso è necessario monitorarne l'avanzamento. È possibile controllare l'avanzamento [definendo un gestore eventi StateChanged](#statechange_event_handler) o [usando l'archiviazione di accodamento di Azure per monitorare le notifiche dei processi di Servizi multimediali](#check_progress_with_queues). In questo argomento vengono illustrati entrambi i metodi.

##<a id="statechange_event_handler"></a>Definire un gestore eventi StateChanged per monitorare l'avanzamento dei processi

Nell'esempio di codice seguente viene definito il gestore eventi StateChanged, che monitora l'avanzamento del processo e ne indica lo stato aggiornato. Il codice consente inoltre di definire il metodo helper LogJobStop, che registra i dettagli degli errori.

	private static void StateChanged(object sender, JobStateChangedEventArgs e)
	{
	    Console.WriteLine("Job state changed event:");
	    Console.WriteLine("  Previous state: " + e.PreviousState);
	    Console.WriteLine("  Current state: " + e.CurrentState);
	
	    switch (e.CurrentState)
	    {
	        case JobState.Finished:
	            Console.WriteLine();
	            Console.WriteLine("********************");
	            Console.WriteLine("Job is finished.");
	            Console.WriteLine("Please wait while local tasks or downloads complete...");
	            Console.WriteLine("********************");
	            Console.WriteLine();
	            Console.WriteLine();
	            break;
	        case JobState.Canceling:
	        case JobState.Queued:
	        case JobState.Scheduled:
	        case JobState.Processing:
	            Console.WriteLine("Please wait...\n");
	            break;
	        case JobState.Canceled:
	        case JobState.Error:
	            // Cast sender as a job.
	            IJob job = (IJob)sender;
	            // Display or log error details as needed.
	            LogJobStop(job.Id);
	            break;
	        default:
	            break;
	    }
	}
	
	private static void LogJobStop(string jobId)
	{
	    StringBuilder builder = new StringBuilder();
	    IJob job = GetJob(jobId);
	
	    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	    builder.AppendLine("***************************");
	    builder.AppendLine("Job ID: " + job.Id);
	    builder.AppendLine("Job Name: " + job.Name);
	    builder.AppendLine("Job State: " + job.State.ToString());
	    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	    builder.AppendLine("Media Services account name: " + _accountName);
	    builder.AppendLine("Media Services account location: " + _accountLocation);
	    // Log job errors if they exist.  
	    if (job.State == JobState.Error)
	    {
	        builder.Append("Error Details: \n");
	        foreach (ITask task in job.Tasks)
	        {
	            foreach (ErrorDetail detail in task.ErrorDetails)
	            {
	                builder.AppendLine("  Task Id: " + task.Id);
	                builder.AppendLine("    Error Code: " + detail.Code);
	                builder.AppendLine("    Error Message: " + detail.Message + "\n");
	            }
	        }
	    }
	    builder.AppendLine("***************************\n");
	    // Write the output to a local file and to the console. The template 
	    // for an error output file is:  JobStop-{JobId}.txt
	    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
	    WriteToFile(outputFile, builder.ToString());
	    Console.Write(builder.ToString());
	}
	
	private static string JobIdAsFileName(string jobID)
	{
	    return jobID.Replace(":", "_");
	}



##<a id="check_progress_with_queues"></a>Usare l'archiviazione di accodamento di Azure per monitorare le notifiche dei processi di Servizi multimediali

Servizi multimediali di Microsoft Azure può inviare messaggi di notifica all'[archiviazione di accodamento di Azure](../storage-dotnet-how-to-use-queues.md#what-is) durante l'elaborazione di processi multimediali. Questo argomento illustra come ricevere questi messaggi di notifica dal servizio di archiviazione di accodamento.

È possibile accedere ai messaggi distribuiti al servizio di archiviazione di accodamento da ogni parte del mondo. L'architettura di messaggistica del servizio di accodamento di Azure, infatti, è affidabile e altamente scalabile. Tra i vari metodi disponibili, quindi, è preferibile usare il polling dell'archiviazione di accodamento.

Può essere necessario ascoltare le notifiche di Servizi multimediali quando, ad esempio, si sta sviluppando un sistema di gestione dei contenuti ed è necessario che il sistema effettui alcune attività aggiuntive dopo il completamento di un processo di codifica (ad esempio, deve attivare il passaggio successivo di un flusso di lavoro o pubblicare contenuti).

###Considerazioni

Quando si sviluppano applicazioni di Servizi multimediali che usano l'archiviazione di accodamento di Azure, tenere presente quanto segue.

- Il servizio di accodamento non garantisce un recapito ordinato dei messaggi di tipo FIFO (First-In-First-Out). Per altre informazioni, vedere [Analogie e differenze tra le code di Azure e le code del bus di servizio](https://msdn.microsoft.com/library/azure/hh767287.aspx).
- Le code di archiviazione di Azure non costituiscono un servizio di pushing ed è quindi necessario eseguire il polling della coda. 
- È possibile disporre di un qualsiasi numero di code. Per altre informazioni, vedere [API REST del servizio di accodamento](https://msdn.microsoft.com/library/azure/dd179363.aspx).
- Le code di archiviazione di Azure hanno delle limitazioni e le informazioni dettagliate sono descritte nel seguente articolo: [Analogie e differenze tra le code di Azure e le code del bus di servizio](https://msdn.microsoft.com/library/azure/hh767287.aspx).

###Esempio di codice

L'esempio di codice contenuto in questa sezione effettua quanto segue:

1. Definisce la classe **EncodingJobMessage** che esegue il mapping al formato del messaggio di notifica. Il codice deserializza i messaggi ricevuti dalla coda in oggetti del tipo **EncodingJobMessage**.
1. Carica informazioni sugli account di Servizi multimediali e di archiviazione dal file app.config Usa queste informazioni per creare gli oggetti **CloudMediaContext** e **CloudQueue**.
1. Crea la coda che riceverà i messaggi di notifica relativi al processo di codifica.
1. Crea l'endpoint di notifica di cui viene eseguito il mapping alla coda.
1. Collega l'endpoint di notifica al processo e invia il processo di codifica. A un processo possono essere collegati anche più endpoint di notifica.
1. Di questo esempio interessano solo gli stati finali dell'elaborazione dei processi, quindi verrà passato **NotificationJobState.FinalStatesOnly** al metodo **AddNew**. 
		
		job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
1. Se si passa NotificationJobState.All si ricevono tutte le notifiche di modifica dello stato: In coda -> Pianificato -> Elaborazione in corso -> Completato. Tuttavia, come indicato in precedenza, il servizio delle code di archiviazione di Azure non garantisce un recapito ordinato dei messaggi. Per ordinare i messaggi, è possibile usare la proprietà Timestamp (definita nel tipo EncodingJobMessage dell'esempio seguente). Per verificare la presenza di eventuali messaggi di notifica duplicati, è possibile invece usare la proprietà ETag (definita nel tipo EncodingJobMessage). È possibile inoltre che alcune notifiche di cambio di stato vengano ignorate. 
1. Attende che il processo abbia raggiunto lo stato Completato controllando la coda ogni 10 secondi. Elimina i messaggi man mano che vengono elaborati.
1. Elimina la coda e l'endpoint di notifica.

>[AZURE.NOTE]Il modo migliore per monitorare lo stato di un processo è quello di ascoltare i messaggi di notifica, come illustrato nel seguente esempio.
>
>In alternativa, è possibile controllare lo stato di un processo usando la proprietà **IJob.State**. Un messaggio di notifica relativo al completamento di un processo potrebbe essere ricevuto prima che lo stato in **IJob** sia impostato su **Completato**. La proprietà **IJob.State** riflette lo stato esatto con un leggero ritardo.

	
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
	 
	
	        // Upload a video file, and encode to Smooth Streaming format
	        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");
	
	            //Create an encrypted asset and upload the mp4. 
	            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
	                inputMediaFilePath);
	
	            // Get a media processor reference, and pass to it the name of the 
	            // processor to use for the specific task.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	
	            // Create a task with the conversion details, using a configuration file. 
	            ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
	                processor,
	                "H264 Smooth Streaming 720p",
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

Il precedente esempio ha prodotto il seguente output. I valori possono variare.
	
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
	

##Percorsi di apprendimento di Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=Nov15_HO3-->