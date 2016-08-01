<properties pageTitle="Telemetria di Servizi multimediali di Azure con .NET | Microsoft Azure" 
	description="Questo articolo illustra come usare i dati di telemetria di Servizi multimediali di Azure." 
	services="" 
	documentationCenter=""
	authors="juliako" />

# Telemetria di Servizi multimediali di Azure con .NET

## Panoramica

La telemetria e il monitoraggio di Servizi multimediali consentono ai clienti di accedere ai dati di metrica per i servizi. La versione corrente supporta i dati di telemetria per le entità "Channel" e "StreamingEndpoint". È possibile configurare i dati di telemetria con granularità a livello di componente. Esistono due livelli di dettaglio: "Normal" e "Verbose". La versione corrente supporta solo "Normal".

I dati di telemetria vengono scritti in un account di archiviazione di Azure messo a disposizione dal cliente. È infatti necessario collegare un account di archiviazione all'account di Servizi multimediali. I dati di telemetria vengono scritti in una tabella di Archiviazione di Azure nell'account di archiviazione specificato. Il sistema di telemetria creerà una tabella separata per ogni nuovo giorno in base alle 00:00 UTC. Ad esempio, "TelemetryMetrics20160321" dove "20160321" è la data di creazione della tabella. Per ogni giorno sarà presente una tabella separata.

Il sistema di telemetria non offre la conservazione dei dati né l'eliminazione automatica dei record obsoleti. Per questo motivo è necessario gestire ed eliminare i record obsoleti. La presenza di tabelle separate per ogni giorno semplifica l'eliminazione dei record obsoleti. È possibile semplicemente eliminare le tabelle obsolete.

Questo argomento illustra come abilitare la telemetria per i servizi di Servizi multimediali di Azure specificati e come eseguire query sulle metriche con .NET.

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


## Log StreamingEndpoint

###Metriche disponibili

È possibile eseguire query per le seguenti metriche StreamingEndPoint.

- **PartitionKey** ottiene la chiave di partizione del record.
- **RowKey** ottiene la chiave di riga del record.
- **AccountId** ottiene l'ID dell'account di Servizi multimediali.
- **AccountId** ottiene l'ID dell'endpoint di streaming di Servizi multimediali.
- **ObservedTime** ottiene l'ora di osservazione della metrica.
- **HostName** ottiene il nome host dell'endpoint di streaming.
- **StatusCode** ottiene il codice di stato.
- **ResultCode** ottiene il codice del risultato.
- **RequestCount** ottiene il numero di richieste.
- **BytesSent** ottiene i byte inviati.
- **ServerLatency** ottiene la latenza del server.
- **EndToEndLatency** ottiene il tempo di richiesta end-to-end.

###Esempio di risultati della query per l'endpoint di streaming

![Query endpoint di streaming](media/media-services-telemetry/media-services-telemetry01.png)


## Heartbeat canale live

###Metriche disponibili

È possibile eseguire query per le seguenti metriche del canale live.

- **PartitionKey** ottiene la chiave di partizione del record.
- **RowKey** ottiene la chiave di riga del record.
- **AccountId** ottiene l'ID dell'account di Servizi multimediali.
- **ChannelId** ottiene l'ID del canale di Servizi multimediali.
- **ObservedTime** ottiene l'ora di osservazione della metrica.
- **CustomAttributes** ottiene gli attributi personalizzati.
- **TrackType** ottiene il tipo di traccia.
- **TrackName** ottiene il nome della traccia.
- **Bitrate** ottiene il bitrate.
- **IncomingBitrate** ottiene il bitrate in ingresso.
- **OverlapCount** ottiene il conteggio delle sovrapposizioni.
- **DiscontinuityCount** ottiene il conteggio delle discontinuità.
- **LastTimestamp** ottiene l'ultimo timestamp.
 
###Esempio di risultati della query per il canale live

![Query endpoint di streaming](media/media-services-telemetry/media-services-telemetry01.png)

## Esempio di metriche StreamingEndpoint
		
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
	
	            INotificationEndPoint notificationEndPoint = 
	                          _context.NotificationEndPoints.Create("monitoring", NotificationEndPointType.AzureTable, GetTableEndPoint());
	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
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

<!---HONumber=AcomDC_0720_2016-->