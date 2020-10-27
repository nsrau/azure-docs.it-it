---
title: Monitoraggio del riferimento ai dati dell'hub Azure
description: Materiale di riferimento importante necessario per il monitoraggio dell'hub Azure
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.date: 10/22/2020
ms.openlocfilehash: b4d5b3682114d44ceaadc73252f355e4cdc9cc66
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548558"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Monitoraggio del riferimento ai dati dell'hub Azure

Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio per l'hub Azure Internet, vedere [monitorare l'hub](monitor-iot-hub.md) Internet.

## <a name="metrics"></a>Metriche

Questa sezione elenca tutte le metriche della piattaforma raccolte automaticamente per l'hub Azure. Lo spazio dei nomi del provider di risorse per le metriche dell'hub Internet è **Microsoft. Devices** e lo spazio dei nomi del tipo è **IoTHubs** .

Le sottosezioni seguenti suddividono le metriche della piattaforma dell'hub Internet in base alla categoria generale ed elencate in base al nome visualizzato che vengono visualizzate nel portale di Azure con. Vengono inoltre fornite informazioni rilevanti per le metriche visualizzate in ogni sottosezione.

È anche possibile trovare una singola tabella in cui sono elencate tutte le metriche della piattaforma dell'hub Internet per nome della metrica in [Microsoft. Devices/IotHubs](/azure/azure-monitor/platform/metrics-supported#microsoftdevicesiothubs) nella documentazione di monitoraggio di Azure. Tenere presente che questa tabella non fornisce alcune informazioni, ad esempio le [aggregazioni supportate](#supported-aggregations) per alcune metriche, disponibili in questo articolo.

Per informazioni sulle metriche supportate da altri servizi di Azure, vedere [metriche supportate con monitoraggio di Azure](/azure/azure-monitor/platform/metrics-supported).

**Argomenti in questa sezione**

- [Aggregazioni supportate](#supported-aggregations)
- [Metriche dei comandi da cloud a dispositivo](#cloud-to-device-command-metrics)
- [Metriche dei metodi diretti da cloud a dispositivo](#cloud-to-device-direct-methods-metrics)
- [Metriche delle operazioni da cloud a dispositivo gemello](#cloud-to-device-twin-operations-metrics)
- [Metriche di configurazione](#configurations-metrics)
- [Metriche della quota giornaliera](#daily-quota-metrics)
- [Metriche del dispositivo](#device-metrics)
- [Metriche di telemetria del dispositivo](#device-telemetry-metrics)
- [Metriche delle operazioni da dispositivo a cloud gemello](#device-to-cloud-twin-operations-metrics)
- [Metriche di griglia di eventi](#event-grid-metrics)
- [Metriche dei processi](#jobs-metrics)
- [Metriche di routing](#routing-metrics)
- [Metriche di query gemelle](#twin-query-metrics)

### <a name="supported-aggregations"></a>Aggregazioni supportate

La colonna **tipo di aggregazione** in ogni tabella corrisponde all'aggregazione predefinita utilizzata quando si seleziona la metrica per un grafico o un avviso.

   ![Screenshot che mostra l'aggregazione per le metriche](./media/monitor-iot-hub-reference/aggregation-type.png)

Per la maggior parte delle metriche, tutti i tipi di aggregazione sono validi. Tuttavia, per le metriche di conteggio, quelle con un valore di colonna di **unità** **conteggio** , sono valide solo alcune aggregazioni. Il conteggio delle metriche può essere uno dei due tipi seguenti:

* Per le metriche di conteggio a **punto singolo** , l'hub Internet registra un singolo punto dati, essenzialmente un 1, ogni volta che si verifica l'operazione misurata. Monitoraggio di Azure somma quindi questi punti dati in base alla granularità specificata. Esempi di metriche a **singolo punto** sono *i messaggi di telemetria inviati* e il *recapito dei messaggi C2D completati* . Per queste metriche, l'unico tipo di aggregazione pertinente è Total (Sum). Il portale consente di scegliere minimo, massimo e medio. Tuttavia, questi valori saranno sempre 1.

* Per la metrica del conteggio degli **snapshot** , l'hub Internet registra un conteggio totale quando si verifica l'operazione misurata. Attualmente sono disponibili tre metriche **snapshot** emesse dall'hub Internet: *numero totale di messaggi usati* , *totale dispositivi (anteprima)* e *dispositivi connessi (anteprima)* . Poiché queste metriche presentano una quantità "totale" ogni volta che vengono emesse, la somma della granularità specificata non è sensata. Monitoraggio di Azure limita la selezione di media, minimo e massimo per il tipo di aggregazione per queste metriche.

### <a name="cloud-to-device-command-metrics"></a>Metriche dei comandi da cloud a dispositivo

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|C2D Messages Expired (preview) (Messaggi C2D scaduti (anteprima))|C2DMessagesExpired|Conteggio|Totale|Numero di messaggi da cloud a dispositivo scaduti|nessuno|
|C2D message deliveries completed (Recapiti messaggi C2D completati)|C2D. Commands. in uscita.<br>operazione completata. operazione completata|Conteggio|Totale|Numero di recapiti di messaggi da cloud a dispositivo completati dal dispositivo|nessuno|
|C2D messages abandoned (Messaggi C2D abbandonati)|C2D. Commands. in uscita.<br>abbandono. esito positivo|Conteggio|Totale|Numero di messaggi da cloud a dispositivo abbandonati dal dispositivo|nessuno|
|C2D messages rejected (Messaggi C2D rifiutati)|C2D. Commands. in uscita.<br>rifiuto. operazione riuscita|Conteggio|Totale|Numero di messaggi da cloud a dispositivo rifiutati dal dispositivo|nessuno|

Per le metriche con un **Unit** valore di unità **conteggio** , è valida solo l'aggregazione totale (Sum). Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

### <a name="cloud-to-device-direct-methods-metrics"></a>Metriche dei metodi diretti da cloud a dispositivo

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Chiamate a metodi diretti non riuscite|c2d.methods.failure|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti non riuscite.|nessuno|
|Dimensioni delle richieste di chiamate a metodi diretti|c2d.methods.requestSize|Byte|Media|Conteggio di tutte le richieste di metodi diretti riuscite.|nessuno|
|Dimensioni delle risposte a chiamate a metodi diretti|c2d.methods.responseSize|Byte|Media|Numero di tutte le risposte al metodo diretto riuscite.|nessuno|
|Chiamate a metodi diretti riuscite|c2d.methods.success|Conteggio|Totale|Numero di tutte le chiamate a metodi diretti riuscite.|nessuno|

Per le metriche con un valore **unità** di aggregazione **conteggio** solo totale (Sum) è valido. Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

### <a name="cloud-to-device-twin-operations-metrics"></a>Metriche delle operazioni da cloud a dispositivo gemello

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Letture dei dispositivi gemelli non riuscite per il back-end|c2d.twin.read.failure|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end non riuscite.|nessuno|
|Aggiornamenti dei dispositivi gemelli non riusciti per il back-end|c2d.twin.update.failure|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end non riusciti.|nessuno|
|Dimensioni delle risposte di letture dei dispositivi gemelli dal back-end|c2d.twin.read.size|Byte|Media|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|nessuno|
|Dimensioni degli aggiornamenti dei dispositivi gemelli dal back-end|c2d.twin.update.size|Byte|Media|Dimensioni totali di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end riusciti.|nessuno|
|Letture dei dispositivi gemelli completate dal back-end|c2d.twin.read.success|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal back-end completate.|nessuno|
|Aggiornamenti dei dispositivi gemelli completati dal back-end|c2d.twin.update.success|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal back-end completati.|nessuno|

Per le metriche con un **Unit** valore di unità **conteggio** , è valida solo l'aggregazione totale (Sum). Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

### <a name="configurations-metrics"></a>Metriche di configurazione

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Metriche di configurazione|configurazioni|Conteggio|Totale|Numero di operazioni CRUD totali eseguite per la configurazione del dispositivo e la distribuzione di IoT Edge, in un set di dispositivi di destinazione. Questo include anche il numero di operazioni che modificano il dispositivo gemello o il modulo gemello a causa di queste configurazioni.|nessuno|

Per le metriche con un **Unit** valore di unità **conteggio** , è valida solo l'aggregazione totale (Sum). Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

### <a name="daily-quota-metrics"></a>Metriche della quota giornaliera

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Total device data usage (Utilizzo totale dati dispositivo)|deviceDataUsage|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|nessuno|
|Uso totale dei dati del dispositivo (anteprima)|deviceDataUsageV2|Byte|Totale|Byte trasferiti a e da qualsiasi dispositivo connesso all'hub IoT|nessuno|
|Total number of messages used (Numero totale di messaggi usati)|dailyMessageQuotaUsed|Conteggio|Media|Numero totale di messaggi usati nella data odierna. Si tratta di un valore cumulativo che viene reimpostato su zero alle 00:00 UTC ogni giorno.|nessuno|

Per il *numero totale di messaggi utilizzati* , sono supportate solo le aggregazioni minime, massime e medie. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

### <a name="device-metrics"></a>Metriche del dispositivo

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Dispositivi totali (deprecati)|devices.totalDevices|Conteggio|Totale|Il numero di dispositivi registrati nell'hub IoT|nessuno|
|Dispositivi connessi (deprecati) |Devices. connectedDevices.<br>allProtocol|Conteggio|Totale|Il numero di dispositivi connessi all'hub IoT|nessuno|
|Dispositivi totali (anteprima)|totalDeviceCount|Conteggio|Media|Il numero di dispositivi registrati nell'hub IoT|nessuno|
|Dispositivi connessi (anteprima)|connectedDeviceCount|Conteggio|Media|Il numero di dispositivi connessi all'hub IoT|nessuno|

Per i *dispositivi totali (deprecati)* e i *dispositivi connessi (deprecati)* , è valida solo l'aggregazione totale (Sum). Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

Per i *dispositivi totali (anteprima)* e i *dispositivi connessi (anteprima)* , sono valide solo le aggregazioni minime, massime e medie. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

I *dispositivi connessi (anteprima)* e i *dispositivi totali (anteprima)* non sono esportabili tramite le impostazioni di diagnostica.

### <a name="device-telemetry-metrics"></a>Metriche di telemetria del dispositivo

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Number of throttling errors (Numero di errori di limitazione)|D2C. telemetria. ingress.<br>sendThrottle|Conteggio|Totale|Numero di errori di limitazione dovuti alle limitazioni della velocità effettiva del dispositivo|nessuno|
|Tentativi di invio di messaggi di telemetria|D2C. telemetria. ingress.<br>allProtocol|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud che si è cercato di inviare all'hub IoT|nessuno|
|Messaggi di telemetria inviati|D2C. telemetria. ingress.<br>esito positivo|Conteggio|Totale|Il numero di messaggi di telemetria da dispositivo a cloud inviati all'hub IoT|nessuno|

Per le metriche con un **Unit** valore di unità **conteggio** , è valida solo l'aggregazione totale (Sum). Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

### <a name="device-to-cloud-twin-operations-metrics"></a>Metriche delle operazioni da dispositivo a cloud gemello

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Letture dei dispositivi gemelli non riuscite per i dispositivi|d2c.twin.read.failure|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo non riuscite.|nessuno|
|Aggiornamenti dei dispositivi gemelli non riusciti per i dispositivi|d2c.twin.update.failure|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo non riusciti.|nessuno|
|Dimensioni delle risposte di letture dei dispositivi gemelli dai dispositivi|d2c.twin.read.size|Byte|Media|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|nessuno|
|Dimensioni degli aggiornamenti dei dispositivi gemelli dai dispositivi|d2c.twin.update.size|Byte|Media|Dimensioni totali di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo riusciti.|nessuno|
|Letture dei dispositivi gemelli completate dai dispositivi|d2c.twin.read.success|Conteggio|Totale|Numero di tutte le letture dei dispositivi gemelli avviate dal dispositivo completate.|nessuno|
|Aggiornamenti dei dispositivi gemelli completati dai dispositivi|d2c.twin.update.success|Conteggio|Totale|Numero di tutti gli aggiornamenti dei dispositivi gemelli avviati dal dispositivo completati.|nessuno|

Per le metriche con un **Unit** valore di unità **conteggio** , è valida solo l'aggregazione totale (Sum). Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

### <a name="event-grid-metrics"></a>Metriche di griglia di eventi

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Event Grid deliveries(preview) (Recapiti Griglia di eventi (anteprima))|EventGridDeliveries|Conteggio|Totale|Il numero di eventi dell'hub Internet pubblico pubblicati in griglia di eventi. Utilizzare la dimensione risultato per il numero di richieste riuscite e non riuscite. EventType Dimension indica il tipo di evento ( https://aka.ms/ioteventgrid) .|Risultato:<br/>EventType<br>*Per altre informazioni, vedere [dimensioni della metrica](#metric-dimensions)* .|
|Event Grid latency (preview) (Latenza Griglia di eventi (anteprima))|EventGridLatency|Millisecondi|Media|Latenza media (in millisecondi) dal momento in cui è stato generato l'evento dell'hub Internet quando l'evento è stato pubblicato in griglia di eventi. Questo numero è una media tra tutti i tipi di evento. Utilizzare la dimensione EventType per visualizzare la latenza di un tipo specifico di evento.|EventType<br>*Per altre informazioni, vedere [dimensioni della metrica](#metric-dimensions)* .|

Per le metriche con un **Unit** valore di unità **conteggio** , è valida solo l'aggregazione totale (Sum). Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

### <a name="jobs-metrics"></a>Metriche dei processi

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Processi completati|jobs.completed|Conteggio|Totale|Numero di tutti i processi completati.|nessuno|
|Chiamate per elencare i processi non riuscite|jobs.listJobs.failure|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi non riuscite.|nessuno|
|Creazioni di processi di chiamata al metodo non riuscite|Jobs. createDirectMethodJob.<br>operazione non riuscita|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti non riuscite.|nessuno|
|Creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite|Jobs. createTwinUpdateJob.<br>operazione non riuscita|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli non riuscite.|nessuno|
|Annullamenti di processi non riusciti|jobs.cancelJob.failure|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi non riuscite.|nessuno|
|Query sui processi non riuscite|jobs.queryJobs.failure|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi non riuscite.|nessuno|
|Processi non riusciti|jobs.failed|Conteggio|Totale|Numero di tutti i processi non riusciti.|nessuno|
|Chiamate per elencare i processi riuscite|jobs.listJobs.success|Conteggio|Totale|Numero di tutte le chiamate per elencare i processi riuscite.|nessuno|
|Creazioni di processi di chiamata al metodo completate|Jobs. createDirectMethodJob.<br>esito positivo|Conteggio|Totale|Numero di tutte le creazioni di processi di chiamata a metodi diretti completate.|nessuno|
|Creazioni di processi di aggiornamento dei dispositivi gemelli completate|Jobs. createTwinUpdateJob.<br>esito positivo|Conteggio|Totale|Numero di tutte le creazioni di processi di aggiornamento dei dispositivi gemelli completate.|nessuno|
|Annullamenti di processi riusciti|jobs.cancelJob.success|Conteggio|Totale|Numero di tutte le chiamate per annullare i processi riuscite.|nessuno|
|Query sui processi riuscite|jobs.queryJobs.success|Conteggio|Totale|Numero di tutte le chiamate per eseguire query sui processi riuscite.|nessuno|

Per le metriche con un **Unit** valore di unità **conteggio** , è valida solo l'aggregazione totale (Sum). Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

### <a name="routing-metrics"></a>Metriche di routing

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
| Tentativi di recapito routing (anteprima) |RoutingDeliveries | Conteggio | Totale |Si tratta della metrica per il recapito del routing. Utilizzare le dimensioni per identificare lo stato di recapito di un endpoint specifico o per un'origine di routing specifica.| Risultato:<br>RoutingSource,<br>EndpointType<br>FailureReasonCategory,<br>EndpointName<br>*Per altre informazioni, vedere [dimensioni della metrica](#metric-dimensions)* . |
| Dimensioni dei dati di recapito del routing in byte (anteprima)|RoutingDataSizeInBytesDelivered| Byte | Totale |Il numero totale di byte indirizzati dall'hub degli indirizzi Internet all'endpoint personalizzato e all'endpoint predefinito. Utilizzare le dimensioni per identificare le dimensioni dei dati indirizzate a un endpoint specifico o per un'origine di routing specifica.| RoutingSource,<br>EndpointType<br>EndpointName<br>*Per altre informazioni, vedere [dimensioni della metrica](#metric-dimensions)* .|
| Latenza routing (anteprima) |RoutingDeliveryLatency| Millisecondi | Media |Si tratta della metrica della latenza di recapito del routing. Utilizzare le dimensioni per identificare la latenza per un endpoint specifico o per un'origine di routing specifica.| RoutingSource,<br>EndpointType<br>EndpointName<br>*Per altre informazioni, vedere [dimensioni della metrica](#metric-dimensions)* .|
|Routing: BLOB recapitati all'archiviazione|D2C. Endpoints. in uscita.<br>Storage. blob|Conteggio|Totale|Numero di volte in cui routing dell'hub IoT ha recapitato BLOB agli endpoint di archiviazione.|nessuno|
|Routing: dati recapitati all'archiviazione|D2C. Endpoints. in uscita.<br>Storage. bytes|Byte|Totale|Quantità di dati (byte) che il routing dell'hub IoT ha recapitato agli endpoint di archiviazione.|nessuno|
|Routing: latenza dei messaggi per l'hub eventi|D2C. Endpoints. latenza.<br>eventHubs|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub e l'ingresso del messaggio in endpoint personalizzati di tipo hub eventi. Non sono incluse le route dei messaggi all'endpoint predefinito (eventi).|nessuno|
|Routing: latenza dei messaggi per la coda del bus di servizio|D2C. Endpoints. latenza.<br>serviceBusQueues|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub e l'ingresso del messaggio in un endpoint della coda del bus di servizio.|nessuno|
|Routing: latenza dei messaggi per l'argomento del bus di servizio|D2C. Endpoints. latenza.<br>serviceBusTopics|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub e l'ingresso del messaggio in un endpoint dell'argomento del bus di servizio.|nessuno|
|Routing: latenza dei messaggi per messaggi/eventi|D2C. Endpoints. latenza.<br>Builtin. eventi|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub e l'ingresso del messaggio nell'endpoint predefinito (messaggi/eventi) e nella route di fallback.|nessuno|
|Routing: latenza dei messaggi per l'archiviazione|D2C. Endpoints. latenza.<br>storage|Millisecondi|Media|Latenza media (in millisecondi) tra l'ingresso del messaggio nell'hub e l'ingresso del messaggio in un endpoint di archiviazione.|nessuno|
|Routing: messaggi inviati all'hub eventi|D2C. Endpoints. in uscita.<br>eventHubs|Conteggio|Totale|Il numero di volte in cui il routing dell'hub Internet ha recapitato correttamente i messaggi agli endpoint personalizzati di tipo hub eventi. Non sono incluse le route dei messaggi all'endpoint predefinito (eventi).|nessuno|
|Routing: messaggi recapitati alla coda del bus di servizio|D2C. Endpoints. in uscita.<br>serviceBusQueues|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint della coda del bus di servizio.|nessuno|
|Routing: messaggi recapitati all'argomento del bus di servizio|D2C. Endpoints. in uscita.<br>serviceBusTopics|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint dell'argomento del bus di servizio.|nessuno|
|Routing: messaggi recapitati al fallback|D2C. telemetria. in uscita.<br>fallback|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi all'endpoint associato con la route di fallback.|nessuno|
|Routing: messaggi recapitati a messaggi/eventi|D2C. Endpoints. in uscita.<br>Builtin. eventi|Conteggio|Totale|Il numero di volte in cui il routing dell'hub Internet è riuscito a recapitare messaggi all'endpoint predefinito (messaggi/eventi) e alla route di fallback.|nessuno|
|Routing: messaggi recapitati all'archiviazione|D2C. Endpoints. in uscita.<br>storage|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT ha recapitato i messaggi agli endpoint di archiviazione.|nessuno|
|Routing: messaggi di telemetria recapitati|D2C. telemetria. in uscita.<br>esito positivo|Conteggio|Totale|Numero di volte in cui i messaggi sono stati recapitati correttamente a tutti gli endpoint tramite routing dell'hub IoT. Se un messaggio viene indirizzato a più endpoint, questo valore aumenta di uno per ogni recapito con esito positivo. Se un messaggio viene recapitato più volte allo stesso endpoint, questo valore aumenta di uno per ogni recapito con esito positivo.|nessuno|
|Routing: messaggi di telemetria eliminati |D2C. telemetria. in uscita.<br>eliminato|Conteggio|Totale|Numero di volte in cui i messaggi sono stati eliminati dal routing dell'hub IoT a causa degli endpoint inattivi. Questo valore non include messaggi recapitati alla route di fallback perché i messaggi eliminati non vengono recapitati in tale punto.|nessuno|
|Routing: messaggi di telemetria incompatibili|D2C. telemetria. in uscita.<br>non valido|Conteggio|Totale|Numero di volte in cui il routing dell'hub IoT non è riuscito a recapitare i messaggi a causa di un'incompatibilità con l'endpoint. Un messaggio non è compatibile con un endpoint quando l'hub degli errori tenta di recapitare il messaggio a un endpoint e non riesce con un errore non temporaneo. I messaggi non validi non vengono ripetuti. Questo valore non include i nuovi tentativi.|nessuno|
|Routing: messaggi di telemetria orfani |D2C. telemetria. in uscita.<br>isolati|Conteggio|Totale|Il numero di volte in cui i messaggi sono stati orfani dal routing dell'hub Internet in quanto non corrispondono ad alcuna query di routing, quando la route di fallback è disabilitata.|nessuno|

Per le metriche con un **Unit** valore di unità **conteggio** , è valida solo l'aggregazione totale (Sum). Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

### <a name="twin-query-metrics"></a>Metriche di query gemelle

|Nome visualizzato per la metrica|Metrica|Unità|Tipo di aggregazione|Descrizione|Dimensioni|
|---|---|---|---|---|---|
|Query dei dispositivi gemelli non riuscite|twinQueries.failure|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli non riuscite.|nessuno|
|Query dei dispositivi gemelli completate|twinQueries.success|Conteggio|Totale|Numero di tutte le query dei dispositivi gemelli completate.|nessuno|
|Dimensioni dei risultati delle query dei dispositivi gemelli|twinQueries.resultSize|Byte|Media|Totale delle dimensioni dei risultati di tutte le query gemelle riuscite.|nessuno|

Per le metriche con un **Unit** valore di unità **conteggio** , è valida solo l'aggregazione totale (Sum). Le aggregazioni minime, massime e medie restituiscono sempre 1. Per ulteriori informazioni, vedere le [aggregazioni supportate](#supported-aggregations).

## <a name="metric-dimensions"></a>Dimensioni metrica

L'hub Internet Azure è caratterizzato dalle dimensioni seguenti associate ad alcune delle metriche di routing e griglia di eventi.

|Nome della dimensione | Descrizione|
|---|---|
||
|**EndpointName**| Nome dell'endpoint.|
|**EndpointType**|Uno dei seguenti: **eventHubs** , **serviceBusQueues** , **cosmosDB** , **serviceBusTopics** . **Builtin** , o **blobStorage** .|
|**EventType**| Uno dei tipi di evento di griglia di eventi seguenti: **Microsoft. Devices. DeviceCreated** . **Microsoft. Devices. DeviceDeleted** , **Microsoft. Devices. DeviceConnected** , **Microsoft. Devices. DeviceDisconnected** o **Microsoft. Devices. DeviceTelemetry** . Per ulteriori informazioni, vedere [tipi di evento](iot-hub-event-grid.md#event-types).|
|**FailureReasonCategory**| Uno dei seguenti: **non valido** , **eliminato** , **orfano** o **null** .|
|**Risultato**| **Esito positivo** o **negativo** .|
|**RoutingSource**| Messaggi del dispositivo<br>Eventi di modifica del dispositivo gemello<br>Eventi del ciclo di vita del dispositivo|

Per altre informazioni sulle dimensioni delle metriche, vedere [metriche multidimensionali](/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics).

## <a name="resource-logs"></a>Log risorse

Questa sezione elenca tutti i tipi di categoria di log delle risorse e gli schemi raccolti per l'hub Azure. Il provider di risorse e il tipo per tutti i log dell'hub Internet è [Microsoft. Devices/IotHubs](/azure/azure-monitor/platform/resource-logs-categories#microsoftdevicesiothubs).

**Argomenti in questa sezione**

- [Connessioni](#connections)
- [Telemetria del dispositivo](#device-telemetry)
- [Comandi da cloud a dispositivo](#cloud-to-device-commands)
- [Operazioni relative alle identità dei dispositivi](#device-identity-operations)
- [Operazioni di caricamento file](#file-upload-operations)
- [Route](#routes)
- [Operazioni da dispositivo gemello a cloud](#device-to-cloud-twin-operations)
- [Operazioni da cloud a dispositivi gemelli](#cloud-to-device-twin-operations)
- [Query dei dispositivi gemelli](#twin-queries)
- [Operazioni dei processi](#jobs-operations)
- [Metodi diretti](#direct-methods)
- [Traccia distribuita (anteprima)](#distributed-tracing-preview)
  - [Log dell'hub IoT D2C (da dispositivo a cloud)](#iot-hub-d2c-device-to-cloud-logs)
  - [Log di ingresso dell'hub IoT](#iot-hub-ingress-logs)
  - [Log di uscita dell'hub IoT](#iot-hub-egress-logs)
- [Configurazioni](#configurations)
- [Flussi del dispositivo (anteprima)](#device-streams-preview)

### <a name="connections"></a>Connessioni

La categoria Connessioni tiene traccia degli eventi di connessione e disconnessione del dispositivo da un hub IoT, nonché degli errori. Questa categoria è utile per identificare i tentativi di connessione non autorizzati e per inviare avvisi in caso di interruzione della connessione ai dispositivi.

> [!NOTE]
> Per uno stato di connessione affidabile dei dispositivi, selezionare [Device heartbeat](iot-hub-devguide-identity-registry.md#device-heartbeat) (Heartbeat dispositivo).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Telemetria dei dispositivi

La categoria di telemetria dei dispositivi tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di telemetria. Questa categoria include gli errori che si verificano durante l'invio di eventi di telemetria, ad esempio la limitazione, e la ricezione di eventi di telemetria, ad esempio un lettore non autorizzato. Questa categoria non può intercettare gli errori causati da codice in esecuzione nel dispositivo stesso.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>Comandi da cloud a dispositivo

La categoria di comandi da cloud a dispositivo tiene traccia degli errori che si verificano nell'hub IoT e sono correlati alla pipeline di messaggi da cloud a dispositivo. Questa categoria include errori che risultano da:

* Invio di messaggi da cloud a dispositivo (ad esempio, errori di mittente non autorizzato)
* Ricezione di messaggi da cloud a dispositivo (ad esempio, errori di superamento del numero di recapiti)
* Ricezione di feedback sui messaggi da cloud a dispositivo (ad esempio, errori di feedback scaduto)

Questa categoria non intercetta gli errori quando il messaggio da cloud a dispositivo viene recapitato correttamente ma viene gestito in modo non corretto dal dispositivo.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Operazioni relative alle identità dei dispositivi

La categoria di operazioni di identità del dispositivo tiene traccia degli errori che si verificano quando si prova a creare, aggiornare o eliminare una voce nel registro delle identità dell'hub IoT. Il rilevamento di questa categoria è utile per gli scenari di provisioning.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Operazioni di caricamento file

La categoria di caricamenti dei file tiene traccia degli errori che si verificano nell'hub IoT e correlati alla funzionalità di caricamento dei file. Questa categoria include:

* Errori che si verificano con l'URI di firma di accesso condiviso, ad esempio quando l'URI scade prima che un dispositivo notifichi all'hub un caricamento completato.

* Caricamenti non riusciti segnalati dal dispositivo.

* Errori che si verificano quando un file non viene trovato nell'archivio durante la creazione del messaggio di notifica dell'hub IoT.

Questa categoria non può intercettare errori che si verificano direttamente mentre il dispositivo sta caricando un file in memoria.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Route

La categoria [routing messaggi](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) tiene traccia degli errori che si verificano durante la valutazione della route dei messaggi e l'integrità degli endpoint come percepiti dall'hub. Questa categoria include eventi di questo tipo:

* Una regola viene valutata come "non definita"
* Hub IoT contrassegna un endpoint come inattivo
* Qualsiasi errore ricevuto da un endpoint

Questa categoria non include errori specifici sui messaggi stessi, come gli errori di limitazione sui dispositivi, che vengono inclusi nella categoria "Telemetria dei dispositivi".

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Ecco altri dettagli sul routing dei log delle risorse:

* [Elenco dei codici di errore del log delle risorse di routing](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Elenco dei log delle risorse di routing operationNames](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Operazioni da dispositivo gemello a cloud

La categoria relativa alle operazioni da dispositivo gemello a cloud tiene traccia degli eventi avviati dal servizio su dispositivi gemelli. Queste operazioni possono includere il recupero del dispositivo gemello, l'aggiornamento delle proprietà segnalate e la sottoscrizione alle proprietà desiderate.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>Operazioni da cloud a dispositivi gemelli

La categoria relativa alle operazioni da cloud a dispositivo gemello tiene traccia degli eventi avviati dal servizio su dispositivi gemelli. Queste operazioni possono includere il recupero del dispositivo gemello, l'aggiornamento o la sostituzione dei tag e l'aggiornamento o la sostituzione delle proprietà desiderate.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>Query dei dispositivi gemelli

La categoria relativa alle query dei dispositivi gemelli segnala le richieste di query per i dispositivi gemelli che vengono avviati nel cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>Operazioni dei processi

La categoria relativa alle operazioni dei processi segnala le richieste di processo per aggiornare i dispositivi gemelli o richiamare i metodi diretti su più dispositivi. Queste richieste vengono avviate nel cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Metodi diretti

La categoria relativa ai metodi diretti tiene traccia delle interazioni di richiesta-risposta inviate ai singoli dispositivi. Queste richieste vengono avviate nel cloud.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Traccia distribuita (anteprima)

La categoria relativa alla traccia distribuita tiene traccia degli ID di correlazione per i messaggi che contengono l'intestazione del contesto di traccia. Per abilitare completamente questi log, è necessario aggiornare il codice sul lato client seguendo [le analisi e diagnosticare le applicazioni Internet end-to-end con l'analisi distribuita dell'hub Internet (anteprima)](iot-hub-distributed-tracing.md).

Si noti che `correlationId` è conforme alla proposta di [contesto di traccia W3C](https://github.com/w3c/trace-context) , in cui contiene un oggetto `trace-id` e un oggetto `span-id` .

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>Log dell'hub IoT D2C (da dispositivo a cloud)

L'hub IoT registra questo log all'arrivo di un messaggio contenente proprietà di traccia valide nell'hub IoT.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

In questo caso, il valore di `durationMs` non viene calcolato in quanto l'orologio dell'hub IoT potrebbe non essere sincronizzato con l'orologio del dispositivo e quindi il calcolo della durata potrebbe essere fuorviante. È consigliabile scrivere la logica usando i timestamp nella sezione `properties` per acquisire i picchi di latenza da dispositivo a cloud.

| Proprietà | Type | Descrizione |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Dimensione del messaggio da dispositivo a cloud in byte |
| **deviceId** | Stringa di caratteri alfanumerici ASCII a 7 bit | Identità del dispositivo |
| **callerLocalTimeUtc** | Timestamp UTC | Ora di creazione del messaggio in base a quanto indicato dall'orologio locale del dispositivo |
| **calleeLocalTimeUtc** | Timestamp UTC | Ora di arrivo del messaggio nel gateway dell'hub IoT in base a quanto indicato dall'orologio sul lato servizio dell'hub IoT |

#### <a name="iot-hub-ingress-logs"></a>Log di ingresso dell'hub IoT

L'hub IoT registra questo log quando un messaggio contenente proprietà di traccia valide scrive nell'hub eventi predefinito o interno.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

Nella `properties` sezione questo log contiene informazioni aggiuntive sull'ingresso dei messaggi.

| Proprietà | Type | Descrizione |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | string | True o false, indica se il routing dei messaggi è abilitato o meno nell'hub IoT |
| **parentSpanId** | string | Oggetto [span-id](https://w3c.github.io/trace-context/#parent-id) del messaggio padre, che in questo caso sarebbe la traccia del messaggio D2C |

#### <a name="iot-hub-egress-logs"></a>Log di uscita dell'hub IoT

L'hub IoT Hub registra questo log quando il [routing](iot-hub-devguide-messages-d2c.md) è abilitato e il messaggio viene scritto in un [endpoint](iot-hub-devguide-endpoints.md). Se il routing non è abilitato, l'hub IoT non registra questo log.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

Nella `properties` sezione questo log contiene informazioni aggiuntive sull'ingresso dei messaggi.

| Proprietà | Type | Descrizione |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | string | Nome dell'endpoint di routing |
| **endpointType** | string | Tipo dell'endpoint di routing |
| **parentSpanId** | string | Oggetto [span-id](https://w3c.github.io/trace-context/#parent-id) del messaggio padre, che in questo caso sarebbe la traccia del messaggio di ingresso nell'hub IoT |

### <a name="configurations"></a>Configurazioni

I log di configurazione dell'hub Internet rileva gli eventi e gli errori per il set di funzionalità di gestione dei dispositivi automatico.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Flussi del dispositivo (anteprima)

La categoria flussi del dispositivo tiene traccia delle interazioni richiesta-risposta inviate ai singoli dispositivi.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Tabelle di log di monitoraggio di Azure
<!-- REQUIRED. Please keep heading in this order -->

Questa sezione si riferisce a tutti i log di monitoraggio di Azure kusto le tabelle rilevanti per l'hub Azure e disponibili per le query da Log Analytics. Per un elenco di queste tabelle e per i collegamenti ad altre informazioni per il tipo di risorsa hub Internet, vedere l' [Hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) Internet nel riferimento alla tabella dei log di monitoraggio di Azure.

Per informazioni di riferimento su tutti i log di monitoraggio di Azure/tabelle Log Analytics, vedere il [riferimento alla tabella del log di monitoraggio di Azure](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="see-also"></a>Vedere anche

* Per una descrizione del monitoraggio dell' [Hub Azure.](monitor-iot-hub.md)
* Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](/azure/azure-monitor/insights/monitor-azure-resources).
