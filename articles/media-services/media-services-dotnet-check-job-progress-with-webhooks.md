---
title: Usare i webhook di Azure per monitorare le notifiche dei processi di Servizi multimediali con .NET | Documentazione Microsoft
description: "Informazioni su come usare i webhook di Azure per monitorare le notifiche dei processi di Servizi multimediali. L'esempio di codice è scritto in C# e usa l'SDK di Servizi multimediali per .NET."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: a61fe157-81b1-45c1-89f2-224b7ef55869
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/06/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: c3a7c0196b3ff1a7bd939f4224cb109ce71872f2
ms.contentlocale: it-it
ms.lasthandoff: 03/22/2017

---
# <a name="use-azure-webhooks-to-monitor-media-services-job-notifications-with-net"></a>Usare i webhook di Azure per monitorare le notifiche dei processi di Servizi multimediali con .NET
Quando si esegue un processo, spesso è necessario monitorarne l'avanzamento. È possibile monitorare le notifiche dei processi di Servizi multimediali tramite webhook di Azure o [archiviazione code di Azure](media-services-dotnet-check-job-progress-with-queues.md). Questo argomento mostra come lavorare con i webhook.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione è necessario quanto segue:

* Un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* Account di Servizi multimediali. Per creare un account Servizi multimediali, vedere [Creare un account Servizi multimediali di Azure con il portale di Azure](media-services-portal-create-account.md).
* Comprensione della [modalità d'uso delle funzioni di Azure](../azure-functions/functions-overview.md). Rivedere anche [Associazioni HTTP e webhook in Funzioni di Azure](../azure-functions/functions-bindings-http-webhook.md).

Questo argomento illustra come

*  Definire una funzione di Azure personalizzata per rispondere ai webhook. 
    
    In questo caso, il webhook viene attivato da Servizi multimediali quando il processo di codifica cambia stato. La funzione è in attesa del callback del webhook dalle notifiche di Servizi multimediali e pubblica l'asset di output al termine del processo. 
    
    >[!NOTE]
    >Prima di continuare, assicurarsi di comprendere come funzionano le [associazioni HTTP e webhook in Funzioni di Azure](../azure-functions/functions-bindings-http-webhook.md).
    >
    
* Aggiungere un webhook all'attività di codifica e specificarne l'URL e la chiave privata a cui risponde. Nell'esempio riportato di seguito il codice che crea l'attività di codifica è un'applicazione console.

## <a name="setting-up-webhook-notification-azure-functions"></a>Configurazione delle funzioni di Azure per la notifica di webhook

Il codice in questa sezione mostra un'implementazione di una funzione di Azure corrispondente a un webhook. In questo esempio la funzione è in attesa del callback del webhook dalle notifiche di Servizi multimediali e pubblica l'asset di output al termine del processo.

Il webhook prevede una chiave di firma (credenziale) che corrisponda a quella trasmessa quando si configura l'endpoint di notifica. La chiave di firma è il valore codificato di 64 byte Base64 usato per proteggere i callback dei webhook da Servizi multimediali di Azure. 

Nel codice seguente il metodo **VerifyWebHookRequestSignature** esegue la verifica sul messaggio di notifica. Lo scopo di questa convalida è garantire che il messaggio sia stato inviato da Servizi multimediali di Azure e che non sia stato alterato. La firma è facoltativa per le funzioni di Azure dal momento che ha il valore **Code** come un parametro di query su Transport Layer Security (TLS). 

È possibile trovare la definizione delle varie funzioni di Azure .NET di Servizi multimediali (incluso quello illustrato in questo argomento) [qui](https://github.com/Azure-Samples/media-services-dotnet-functions-integration).

L'elenco codici seguente mostra le definizioni dei parametri della funzione di Azure e dei tre file associati a tale funzione: function.json, project.json e run.csx.

### <a name="application-settings"></a>Impostazioni dell'applicazione 

La tabella seguente illustra i parametri usati dalla funzione di Azure definita in questa sezione. 

|Nome|Definizione|Esempio| 
|---|---|---|
|AMSAccount|Nome dell'account AMS. |juliakomediaservices|
|AMSKey |Chiave dell'account AMS. | JUWJdDaOHQQqsZeiXZuE76eDt2SO+YMJk25Lghgy2nY=|
|MediaServicesStorageAccountName |Nome dell'account di archiviazione associato all'account AMS.| storagepkeewmg5c3peq|
|MediaServicesStorageAccountKey |Chiave dell'account di archiviazione associato all'account AMS.|
|SigningKey |Chiave di firma.| j0txf1f8msjytzvpe40nxbpxdcxtqcgxy0nt|
|WebHookEndpoint | Indirizzo di un endpoint di webhook. | https://juliakofuncapp.azurewebsites.net/api/Notification_Webhook_Function?code=iN2phdrTnCxmvaKExFWOTulfnm4C71mMLIy8tzLr7Zvf6Z22HHIK5g==.|

### <a name="functionjson"></a>function.json

Il file function.json definisce le associazioni di funzione e altre impostazioni di configurazione. Il runtime usa questo file per determinare gli eventi da monitorare e come passare i dati e restituirli dall'esecuzione di funzioni. 

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [
        "post",
        "get",
        "put",
        "update",
        "patch"
          ]
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
    
### <a name="projectjson"></a>project.json

Il file project.json contiene dipendenze. 

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

Il codice C# seguente mostra una definizione di una funzione di Azure corrispondente a un webhook. La funzione è in attesa del callback del webhook dalle notifiche di Servizi multimediali e pubblica l'asset di output al termine del processo. 


>[!NOTE]
>È previsto un limite di 1.000.000 di criteri per i diversi criteri AMS (ad esempio per i criteri Locator o ContentKeyAuthorizationPolicy). Usare lo stesso ID criterio se si usano sempre gli stessi giorni/autorizzazioni di accesso, come nel cado di criteri per i localizzatori che devono rimanere attivi per molto tempo (criteri di non caricamento). Per altre informazioni, vedere [questo](media-services-dotnet-manage-entities.md#limit-access-policies) argomento.

    ///////////////////////////////////////////////////
    #r "Newtonsoft.Json"

    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using System.Globalization;
    using Newtonsoft.Json;
    using Microsoft.Azure;
    using System.Net;
    using System.Security.Cryptography;

    internal const string SignatureHeaderKey = "sha256";
    internal const string SignatureHeaderValueTemplate = SignatureHeaderKey + "={0}";
    static string _webHookEndpoint = Environment.GetEnvironmentVariable("WebHookEndpoint");
    static string _signingKey = Environment.GetEnvironmentVariable("SigningKey");
    static string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    static string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");

    static CloudMediaContext _context = null;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

        Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
        byte[] requestBody = await taskForRequestBody;

        string jsonContent = await req.Content.ReadAsStringAsync();
        log.Info($"Request Body = {jsonContent}");

        IEnumerable<string> values = null;
        if (req.Headers.TryGetValues("ms-signature", out values))
        {
        byte[] signingKey = Convert.FromBase64String(_signingKey);
        string signatureFromHeader = values.FirstOrDefault();

        if (VerifyWebHookRequestSignature(requestBody, signatureFromHeader, signingKey))
        {
            string requestMessageContents = Encoding.UTF8.GetString(requestBody);

            NotificationMessage msg = JsonConvert.DeserializeObject<NotificationMessage>(requestMessageContents);

            if (VerifyHeaders(req, msg, log))
            { 
            string newJobStateStr = (string)msg.Properties.Where(j => j.Key == "NewState").FirstOrDefault().Value;
            if (newJobStateStr == "Finished")
            {
                _context = new CloudMediaContext(new MediaServicesCredentials(
                _mediaServicesAccountName,
                _mediaServicesAccountKey));

                if(_context!=null)   
                {                        
                string urlForClientStreaming = PublishAndBuildStreamingURLs(msg.Properties["JobId"]);
                log.Info($"URL to the manifest for client streaming using HLS protocol: {urlForClientStreaming}");
                }
            }

            return req.CreateResponse(HttpStatusCode.OK, string.Empty);
            }
            else
            {
            log.Info($"VerifyHeaders failed.");
            return req.CreateResponse(HttpStatusCode.BadRequest, "VerifyHeaders failed.");
            }
        }
        else
        {
            log.Info($"VerifyWebHookRequestSignature failed.");
            return req.CreateResponse(HttpStatusCode.BadRequest, "VerifyWebHookRequestSignature failed.");
        }
        }

        return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
    }

    private static string PublishAndBuildStreamingURLs(String jobID)
    {
        IJob job = _context.Jobs.Where(j => j.Id == jobID).FirstOrDefault();
        IAsset asset = job.OutputMediaAssets.FirstOrDefault();

        // Create a 30-day readonly access policy. 
        // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy,
        DateTime.UtcNow.AddMinutes(-5));


        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                    EndsWith(".ism")).
                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest" +  "(format=m3u8-aapl)";
        return urlForClientStreaming;

    }

    private static bool VerifyWebHookRequestSignature(byte[] data, string actualValue, byte[] verificationKey)
    {
        using (var hasher = new HMACSHA256(verificationKey))
        {
        byte[] sha256 = hasher.ComputeHash(data);
        string expectedValue = string.Format(CultureInfo.InvariantCulture, SignatureHeaderValueTemplate, ToHex(sha256));

        return (0 == String.Compare(actualValue, expectedValue, System.StringComparison.Ordinal));
        }
    }

    private static bool VerifyHeaders(HttpRequestMessage req, NotificationMessage msg, TraceWriter log)
    {
        bool headersVerified = false;

        try
        {
        IEnumerable<string> values = null;
        if (req.Headers.TryGetValues("ms-mediaservices-accountid", out values))
        {
            string accountIdHeader = values.FirstOrDefault();
            string accountIdFromMessage = msg.Properties["AccountId"];

            if (0 == string.Compare(accountIdHeader, accountIdFromMessage, StringComparison.OrdinalIgnoreCase))
            {
            headersVerified = true;
            }
            else
            {
            log.Info($"accountIdHeader={accountIdHeader} does not match accountIdFromMessage={accountIdFromMessage}");
            }
        }
        else
        {
            log.Info($"Header ms-mediaservices-accountid not found.");
        }
        }
        catch (Exception e)
        {
        log.Info($"VerifyHeaders hit exception {e}");
        headersVerified = false;
        }

        return headersVerified;
    }

    private static readonly char[] HexLookup = new char[] { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };

    /// <summary>
    /// Converts a <see cref="T:byte[]"/> to a hex-encoded string.
    /// </summary>
    private static string ToHex(byte[] data)
    {
        if (data == null)
        {
        return string.Empty;
        }

        char[] content = new char[data.Length * 2];
        int output = 0;
        byte d;
        for (int input = 0; input < data.Length; input++)
        {
        d = data[input];
        content[output++] = HexLookup[d / 0x10];
        content[output++] = HexLookup[d % 0x10];
        }
        return new string(content);
    }

    internal enum NotificationEventType
    {
        None = 0,
        JobStateChange = 1,
        NotificationEndPointRegistration = 2,
        NotificationEndPointUnregistration = 3,
        TaskStateChange = 4,
        TaskProgress = 5
    }
    
    internal sealed class NotificationMessage
    {
        public string MessageVersion { get; set; }
        public string ETag { get; set; }
        public NotificationEventType EventType { get; set; }
        public DateTime TimeStamp { get; set; }
        public IDictionary<string, string> Properties { get; set; }
    }

### <a name="function-output"></a>Output della funzione

L'esempio precedente ha prodotto il seguente output; i valori possono variare.

    C# HTTP trigger function processed a request. RequestUri=https://juliako001-functions.azurewebsites.net/api/Notification_Webhook_Function?code=9376d69kygoy49oft81nel8frty5cme8hb9xsjslxjhalwhfrqd79awz8ic4ieku74dvkdfgvi
    Request Body = {
      "MessageVersion": "1.1",
      "ETag": "b8977308f48858a8f224708bc963e1a09ff917ce730316b4e7ae9137f78f3b20",
      "EventType": 4,
      "TimeStamp": "2017-02-16T03:59:53.3041122Z",
      "Properties": {
        "JobId": "nb:jid:UUID:badd996c-8d7c-4ae0-9bc1-bd7f1902dbdd",
        "TaskId": "nb:tid:UUID:80e26fb9-ee04-4739-abd8-2555dc24639f",
        "NewState": "Finished",
        "OldState": "Processing",
        "AccountName": "mediapkeewmg5c3peq",
        "AccountId": "301912b0-659e-47e0-9bc4-6973f2be3424",
        "NotificationEndPointId": "nb:nepid:UUID:cb5d707b-4db8-45fe-a558-19f8d3306093"
      }
    }
    
    URL to the manifest for client streaming using HLS protocol: http://mediapkeewmg5c3peq.streaming.mediaservices.windows.net/0ac98077-2b58-4db7-a8da-789a13ac6167/BigBuckBunny.ism/manifest(format=m3u8-aapl)

## <a name="adding-webhook-to-your-encoding-task"></a>Aggiunta di un webhook all'attività di codifica

Questa sezione mostra il codice che aggiunge una notifica di webhook a un'attività. È inoltre possibile aggiungere una notifica di livello di processo, che dovrebbe essere più utile per un processo con attività concatenate.  

1. Creare una nuova applicazione console C# in Visual Studio. Immettere un valore nei campi Nome, Percorso e Nome soluzione, quindi fare clic su OK.
2. Usare [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) per installare Servizi multimediali di Azure.
3. Aggiornare il file App.config con valori appropriati: 
    
    * Nome e chiave di Servizi multimediali di Azure che invieranno notifiche, 
    * URL del webhook che prevede di ricevere le notifiche, 
    * la chiave di firma corrispondente alla chiave attesa dal webhook. La chiave di firma è il valore codificato di 64 byte Base64 usato per proteggere i callback dei webhook da Servizi multimediali di Azure. 

            <appSettings>
              <add key="MediaServicesAccountName" value="AMSAcctName" />
              <add key="MediaServicesAccountKey" value="AMSAcctKey" />
              <add key="WebhookURL" value="https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>" />
              <add key="WebhookSigningKey" value="j0txf1f8msjytzvpe40nxbpxdcxtqcgxy0nt" />
            </appSettings>
            
4. Aggiornare il file Program.cs con il codice seguente:

        using System;
        using System.Configuration;
        using System.Linq;
        using Microsoft.WindowsAzure.MediaServices.Client;

        namespace NotificationWebHook
        {
            class Program
            {
            // Read values from the App.config file.
            private static readonly string _mediaServicesAccountName =
                ConfigurationManager.AppSettings["MediaServicesAccountName"];
            private static readonly string _mediaServicesAccountKey =
                ConfigurationManager.AppSettings["MediaServicesAccountKey"];
            private static readonly string _webHookEndpoint =
                ConfigurationManager.AppSettings["WebhookURL"];
            private static readonly string _signingKey =
                 ConfigurationManager.AppSettings["WebhookSigningKey"];

            // Field for service context.
            private static CloudMediaContext _context = null;

            static void Main(string[] args)
            {

                // Used the cached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(new MediaServicesCredentials(
                        _mediaServicesAccountName,
                        _mediaServicesAccountKey));

                byte[] keyBytes = Convert.FromBase64String(_signingKey);

                IAsset newAsset = _context.Assets.FirstOrDefault();

                // Check for existing Notification Endpoint with the name "FunctionWebHook"

                var existingEndpoint = _context.NotificationEndPoints.Where(e => e.Name == "FunctionWebHook").FirstOrDefault();
                INotificationEndPoint endpoint = null;

                if (existingEndpoint != null)
                {
                Console.WriteLine("webhook endpoint already exists");
                endpoint = (INotificationEndPoint)existingEndpoint;
                }
                else
                {
                endpoint = _context.NotificationEndPoints.Create("FunctionWebHook",
                    NotificationEndPointType.WebHook, _webHookEndpoint, keyBytes);
                Console.WriteLine("Notification Endpoint Created with Key : {0}", keyBytes.ToString());
                }

                // Declare a new encoding job with the Standard encoder
                IJob job = _context.Jobs.Create("MES Job");

                // Get a media processor reference, and pass to it the name of the 
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
                TaskOptions.None);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(newAsset);

                // Add an output asset to contain the results of the job. 
                // This output is specified as AssetCreationOptions.None, which 
                // means the output asset is not encrypted. 
                task.OutputAssets.AddNew(newAsset.Name, AssetCreationOptions.None);

                // Add the WebHook notification to this Task and request all notification state changes.
                // Note that you can also add a job level notification
                // which would be more useful for a job with chained tasks.  
                if (endpoint != null)
                {
                task.TaskNotificationSubscriptions.AddNew(NotificationJobState.All, endpoint, true);
                Console.WriteLine("Created Notification Subscription for endpoint: {0}", _webHookEndpoint);
                }
                else
                {
                Console.WriteLine("No Notification Endpoint is being used");
                }

                job.Submit();

                Console.WriteLine("Expect WebHook to be triggered for the Job ID: {0}", job.Id);
                Console.WriteLine("Expect WebHook to be triggered for the Task ID: {0}", task.Id);

                Console.WriteLine("Job Submitted");

            }
            private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }

            }
        }

## <a name="next-step"></a>Passaggio successivo
Analizzare i percorsi di apprendimento dei Servizi multimediali

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

