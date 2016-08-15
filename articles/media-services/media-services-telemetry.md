<properties pageTitle="Telemetria di Servizi multimediali di Azure con .NET | Microsoft Azure" 
	description="Questo articolo illustra come usare i dati di telemetria di Servizi multimediali di Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/27/2016"   
	ms.author="juliako"/>

# Telemetria di Servizi multimediali di Azure con .NET
 
## Panoramica

La telemetria e il monitoraggio di Servizi multimediali consentono ai clienti di accedere ai dati di metrica per i servizi. La versione corrente supporta i dati di telemetria per le entità "Channel" e "StreamingEndpoint". È possibile configurare i dati di telemetria con granularità a livello di componente. Esistono due livelli di dettaglio: "Normal" e "Verbose". La versione corrente supporta solo "Normal".

I dati di telemetria vengono scritti in una tabella di archiviazione di un account di archiviazione di Azure messo a disposizione dal cliente. È infatti necessario collegare un account di archiviazione all'account di Servizi multimediali. Il sistema di telemetria creerà una tabella separata per ogni nuovo giorno in base alle 00:00 UTC. Ad esempio, "TelemetryMetrics20160321" dove "20160321" è la data di creazione della tabella. Per ogni giorno sarà presente una tabella separata.

Si noti che il sistema di telemetria non gestisce la conservazione dei dati. È possibile rimuovere i vecchi dati di telemetria eliminando le tabelle di archiviazione.

I dati di telemetria possono essere usati in uno dei modi seguenti:

- Leggere i dati direttamente da Archiviazione tabelle di Azure tramite, ad esempio, Storage SDK. Per la descrizione delle tabelle di archiviazione di dati di telemetria, vedere **Uso delle informazioni di telemetria** in [questo](https://msdn.microsoft.com/library/mt742089.aspx) argomento.

Oppure

- Usare il supporto presente nell'SDK di Servizi multimediali per .NET per la lettura dei dati di archiviazione. Questo argomento illustra come abilitare la telemetria per gli account di Servizi multimediali di Azure specificati e come eseguire query sulle metriche con l'SDK di Servizi multimediali per .NET.

## Configurazione della telemetria per un account di Servizi multimediali

Per abilitare la telemetria sono necessari i passaggi seguenti:

- Ottenere le credenziali dell'account di archiviazione collegato all'account di Servizi multimediali.
- Creare un endpoint di notifica con **EndPointType** impostato su **AzureTable** ed endPontAddress che punta alla tabella di archiviazione.

	    INotificationEndPoint notificationEndPoint = 
	                  _context.NotificationEndPoints.Create("monitoring", 
	                  NotificationEndPointType.AzureTable,
	                  "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- Creare un'impostazione di configurazione del monitoraggio per i servizi da monitorare. È consentita una sola impostazione di configurazione del monitoraggio.
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });

## Uso delle informazioni di telemetria

I dati di telemetria vengono scritti in una tabella di archiviazione di Azure nell'account di archiviazione specificato durante la configurazione della telemetria per l'account di Servizi multimediali. Il sistema di telemetria creerà una tabella separata per ogni nuovo giorno in base alle 00:00 UTC. Ad esempio, "TelemetryMetrics20160321" dove "20160321" è la data di creazione della tabella. Per ogni giorno sarà presente una tabella separata.

È possibile eseguire una query sulle tabelle di telemetria per informazioni sulle metriche seguenti. L'esempio illustrato più avanti in questo argomento mostra come usare l'SDK di Servizi multimediali per .NET per eseguire query sulle metriche.

### Log StreamingEndpoint

È possibile eseguire query per le seguenti metriche StreamingEndPoint.

Proprietà|Descrizione|Valore di esempio
---|---|---
**PartitionKey**|Ottiene la chiave di partizione del record.|60b71b0f6a0e4d869eb0645c16d708e1\_6efed125eef44fb5b61916edc80e6e23
**RowKey**|Ottiene la chiave di riga del record.|00959\_00000
**AccountId**|Ottiene l'ID dell'account di Servizi multimediali.|6efed125-eef4-4fb5-b619-16edc80e6e23
**StreamingEndpointId**|Ottiene l'ID dell'endpoint di streaming di Servizi multimediali.|d17ec9e4-a5d4-033d-0c36-def70229f06f
**ObservedTime**|Ottiene l'ora di osservazione della metrica.|1/20/16 23:44:01
**HostName**|Ottiene il nome host dell'endpoint di streaming.|builddemoserver.Origin.mediaservices.Windows.NET
**StatusCode**|Ottiene il codice di stato.|200
**ResultCode**|Ottiene il codice di risultato.|S\_OK
**RequestCount**|Ottiene il numero di richieste.|3
**BytesSent**|Ottiene i byte inviati.|2987358
**ServerLatency**|Ottiene la latenza del server, inclusa la risorsa di archiviazione.|129
**EndToEndLatency**|Ottiene il tempo di richiesta end-to-end.|250


### Heartbeat canale live

È possibile eseguire query per le seguenti metriche del canale live.

Proprietà|Descrizione|Valore di esempio
---|---|---
**PartitionKey**|Ottiene la chiave di partizione del record.|60b71b0f6a0e4d869eb0645c16d708e1\_0625cc45918e4f98acfc9a33e8066628
**RowKey**|Ottiene la chiave di riga del record.|13872\_00005
**AccountId**|Ottiene l'ID dell'account di Servizi multimediali.|6efed125-eef4-4fb5-b619-16edc80e6e23
**ChannelId**|Ottiene l'ID del canale di Servizi multimediali.|
**ObservedTime**|Ottiene l'ora di osservazione della metrica.|1/21/2016 20:08:49
**CustomAttributes**|Ottiene gli attributi personalizzati.|
**TrackType**|Ottiene il tipo di traccia.|video
**TrackName**|Ottiene il nome della traccia.|video
**Bitrate**|Ottiene il bitrate.|785000
**IncomingBitrate**|Ottiene il bitrate in ingresso.|784548
**OverlapCount**|Ottiene il conteggio delle sovrapposizioni.|0
**DiscontinuityCount**|Ottiene il conteggio delle discontinuità.|0
**LastTimestamp**|Ottiene l'ultimo timestamp.|1800488800
 
## Esempio  
	
L'esempio seguente illustra come abilitare la telemetria per l'account di Servizi multimediali di Azure specificato e come eseguire query sulle metriche con l'SDK di Servizi multimediali per .NET.

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace AMSMetrics
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	        private static readonly string _mediaServicesAccountID =
	            ConfigurationManager.AppSettings["MediaServicesAccountID"];
	        private static readonly string _streamingEndpointID =
	            ConfigurationManager.AppSettings["StreamingEndpointID"];
	        private static readonly string _mediaServicesStorageAccountName =
	            ConfigurationManager.AppSettings["StorageAccountName"];
	        private static readonly string _mediaServicesStorageAccountKey =
	            ConfigurationManager.AppSettings["StorageAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	

	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
		            INotificationEndPoint notificationEndPoint = 
		                          _context.NotificationEndPoints.Create("monitoring", 
								  NotificationEndPointType.AzureTable, GetTableEndPoint());

	                monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
	                    new List<ComponentMonitoringSetting>()
	                    {
	                        new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
	                    });
	            }
	
	            //Print metrics for a Streaming Endpoint.
	            PrintStreamingEndpointMetrics();
	
	            Console.ReadLine();
	        }
	
	        private static string GetTableEndPoint()
	        {
	            return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
	        }
	
	        private static void PrintStreamingEndpointMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some streaming endpoint metrics.
	            var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
	                    GetTableEndPoint(),
	                    _mediaServicesStorageAccountKey,
	                    _mediaServicesAccountID,
	                    _streamingEndpointID,
	                    start,
	                    end);
	
	
	            Console.Title = "Streaming endpoint metrics:";
	
	            foreach (var log in res)
	            {
	                Console.WriteLine("AccountId: {0}", log.AccountId);
	                Console.WriteLine("BytesSent: {0}", log.BytesSent);
	                Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
	                Console.WriteLine("HostName: {0}", log.HostName);
	                Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
	                Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
	                Console.WriteLine("RequestCount: {0}", log.RequestCount);
	                Console.WriteLine("ResultCode: {0}", log.ResultCode);
	                Console.WriteLine("RowKey: {0}", log.RowKey);
	                Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
	                Console.WriteLine("StatusCode: {0}", log.StatusCode);
	                Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
	                Console.WriteLine();
	            }
	
	            Console.WriteLine();
	        }

	        private static void PrintChannelMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some channel metrics.
	            var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
	                GetTableEndPoint(),
	                _mediaServicesStorageAccountKey,
	                _mediaServicesAccountName,
	                _context.Channels.FirstOrDefault().Id,
	                start,
	                end);
	
	            // Print the channel metrics.
	            Console.WriteLine("Channel metrics:");
	
	            foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
	            {
	                Console.WriteLine(
	                    "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
	                    channelHeartbeat.ObservedTime,
	                    channelHeartbeat.LastTimestamp,
	                    channelHeartbeat.IncomingBitrate);
	            }
	
	            Console.WriteLine();
	        }
	    }
	}


##Fornire commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Passaggio successivo
 
Vedere i percorsi di apprendimento di Servizi multimediali di Azure per informazioni sulle potenti funzionalità offerte da Servizi multimediali di Azure.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

<!---HONumber=AcomDC_0803_2016-->