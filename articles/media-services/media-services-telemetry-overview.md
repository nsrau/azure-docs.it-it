---
title: Telemetria di Servizi multimediali di Azure | Documentazione Microsoft
description: Questo articolo offre una panoramica sulla telemetria di Servizi multimediali di Azure.
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 95c20ec4-c782-4063-8042-b79f95741d28
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 99c43c63f75e01713600ef5ca46a8d11e8c5c7ce
ms.openlocfilehash: b6560fdd50c93a7e84f12047ec4401328b601deb
ms.contentlocale: it-it
ms.lasthandoff: 12/09/2016


---

# <a name="azure-media-services-telemetry"></a>Telemetria di Servizi multimediali di Azure

Tramite Servizi multimediali di Azure è possibile accedere ai dati di telemetria e delle metriche relativi ai servizi. La versione corrente di AMS consente di raccogliere i dati di telemetria relativi alle entità **Channel** live, **StreamingEndpoint** e **Archive** live. 

I dati di telemetria vengono scritti in una tabella di archiviazione all'interno di un account di Archiviazione di Azure specificato dall'utente (in genere quello associato all'account di AMS). 

Il sistema di telemetria non gestisce la conservazione dei dati. È possibile rimuovere i vecchi dati di telemetria eliminando le tabelle di archiviazione.

Questo argomento illustra come configurare e usare i dati di telemetria di AMS.

## <a name="configuring-telemetry"></a>Configurare la telemetria

È possibile configurare i dati di telemetria con granularità a livello di componente. Esistono due livelli di dettaglio: "Normal" e "Verbose". Attualmente, entrambi i livelli restituiscono le stesse informazioni. Si consiglia di usare "Normal". 

Gli argomenti seguenti illustrano come abilitare la telemetria:

[Abilitare la telemetria con .NET](media-services-dotnet-telemetry.md) 

[Abilitare la telemetria con REST](media-services-rest-telemetry.md)

## <a name="consuming-telemetry-information"></a>Uso delle informazioni di telemetria

I dati di telemetria vengono scritti in una tabella di archiviazione di Azure nell'account di archiviazione specificato dall'utente al momento di configurare la telemetria per l'account di Servizi multimediali. Questa sezione descrive le tabelle di archiviazione relative alle metriche.

I dati di telemetria possono essere usati in uno dei modi seguenti:

- Leggere i dati direttamente da Archiviazione tabelle di Azure tramite, ad esempio, Storage SDK. Per la descrizione delle tabelle di archiviazione di dati di telemetria, vedere **Uso delle informazioni di telemetria** in [questo](https://msdn.microsoft.com/library/mt742089.aspx) argomento.

Or

- Usare il supporto presente nel .NET SDK di Servizi multimediali per leggere i dati di archiviazione, come descritto in [questo](media-services-dotnet-telemetry.md) argomento. 


Lo schema telemetrico descritto di seguito è pensato per assicurare buone prestazioni entro i limiti dell'archiviazione tabelle di Azure:

- I dati vengono partizionati per ID account e ID di servizio, per consentire di eseguire query sulla telemetria di ogni servizio in modo indipendente.
- Le partizioni contengono la data per assegnare un ragionevole limite superiore alle dimensioni delle partizioni.
- Le chiavi di riga sono in ordine cronologico inverso per consentire di eseguire query sugli ultimi elementi di telemetria di un determinato servizio.

Ciò dovrebbe garantire l'efficienza di molte query comuni:

- Download parallelo e indipendente dei dati da destinare ad altri servizi.
- Recupero di tutti i dati relativi a un servizio in un certo intervallo di date.
- Recupero degli ultimi dati relativi a un servizio.

### <a name="telemetry-table-storage-output-schema"></a>Schema di output dell'archiviazione tabelle di telemetria

I dati di telemetria sono archiviati tutti insieme nella tabella "TelemetryMetrics20160321" dove "20160321" è la data di creazione della tabella. Il sistema di telemetria crea una nuova tabella per ogni giornata alle ore 00:00 UTC. In questa tabella vengono archiviati i valori ricorrenti, come la velocità in bit delle operazioni di inserimento in un determinato intervallo di tempo, i byte inviati e così via. 

Proprietà|Valore|Esempi/note
---|---|---
PartitionKey|{ID account}_{ID entità}|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66<br/<br/>L'ID dell'account viene incluso nella chiave di partizione per semplificare i flussi di lavoro in quei casi in cui nello stesso account di archiviazione scrivono più account di Servizi multimediali.
RowKey|{secondi a mezzanotte}_{valore casuale}|01688_00199<br/><br/>La chiave di riga inizia con il numero di secondi che mancano alla mezzanotte per consentire le principali query di stile all'interno di una partizione. Per altre informazioni, vedere [questo](../storage/storage-table-design-guide.md#log-tail-pattern) articolo. 
Timestamp|Data/ora|Timestamp automatico della tabella di Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo di entità che fornisce i dati di telemetria|Channel/StreamingEndpoint/Archive<br/><br/>Il tipo di evento è semplicemente un valore stringa.
Nome|Nome dell'evento di telemetria|ChannelHeartbeat/StreamingEndpointRequestLog
ObservedTime|L'ora (UTC) in cui si è verificato l'evento di telemetria|2016-09-09T22:42:36.924Z<br/><br/>L'ora osservata è fornita dall'entità che invia i dati di telemetria (ad esempio un canale). Poiché possono esserci problemi di sincronizzazione tra i componenti, questo valore è approssimativo
ServiceID|{ID servizio}|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
Proprietà specifiche di entità|Come definito dall'evento|NomeFlusso: flusso1, bitrate 10123, ...<br/><br/>Vengono definite le proprietà rimanenti per il tipo di evento specificato. Il contenuto della tabella di Azure corrisponde a coppie valore-chiave.  (ovvero a diverse righe della tabella corrispondono diversi set di proprietà).

### <a name="entity-specific-schema"></a>Schema specifico di entità

Esistono tre tipi di dati di telemetria specifici di entità inseriti con la seguente frequenza:

- Endpoint di streaming: ogni 30 secondi
- Canali live: ogni minuto
- Archivio live: ogni minuto

**Endpoint di streaming**

Proprietà|Valore|esempi
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Timestamp automatico della tabella di Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|StreamingEndpoint
Nome|Nome|StreamingEndpointRequestLog
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID del servizio|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
HostName|Nome host dell'endpoint|builddemoserver.Origin.mediaservices.Windows.NET
StatusCode|Stato dei record HTTP|200
ResultCode|Dettagli del codice risultato|S_OK
RequestCount|Totale delle richieste nell'aggregazione|3
BytesSent|Insieme dei byte inviati|2987358
ServerLatency|Latenza media del server (inclusa l'archiviazione)|129
E2ELatency|Latenza end-to-end media|250

**Canale live**

Proprietà|Valore|Esempi/note
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Timestamp automatico della tabella di Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|canale
Nome|Nome|ChannelHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID del servizio|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
TrackType|Tipo di testo traccia di testo/video/audio|video/audio
TrackName|Nome della traccia|video/audio_1
Bitrate|Velocità in bit della traccia|785000
CustomAttributes||   
IncomingBitrate|Velocità in bit in ingresso effettiva|784548
OverlapCount|Sovrapposizione nell'inserimento|0
DiscontinuityCount|Discontinuità di traccia|0
LastTimestamp|Timestamp degli ultimi dati inseriti|1800488800
NonincreasingCount|Numero di frammenti scartati a causa del timestamp invariato|2
UnalignedKeyFrames|Eventuali frammenti (con vari livelli di qualità) ricevuti con fotogrammi chiave non allineati |True
UnalignedPresentationTime|Eventuali frammenti (con vari livelli/tracce di qualità) ricevuti con l'ora di presentazione non allineata|True
UnexpectedBitrate|True, se la frequenza in bit audio/video calcolata/effettiva è > 40.000 bps e IncomingBitrate è = = 0 O i valori IncomingBitrate e actualBitrate sono diversi del 50% |True
Healthy|True, se <br/>overlapCount, <br/>DiscontinuityCount, <br/>NonIncreasingCount, <br/>UnalignedKeyFrames, <br/>UnalignedPresentationTime, <br/>UnexpectedBitrate<br/> sono tutti pari a 0|True<br/><br/>Healthy è una funzione composita che restituisce il valore false in presenza di una delle seguenti condizioni:<br/><br/>- OverlapCount > 0<br/>- DiscontinuityCount > 0<br/>- NonincreasingCount > 0<br/>- UnalignedKeyFrames == True<br/>- UnalignedPresentationTime == True<br/>- UnexpectedBitrate == True

**Archivio live**

Proprietà|Valore|Esempi/note
---|---|---
PartitionKey|PartitionKey|e49bef329c29495f9b9570989682069d_64435281c50a4dd8ab7011cb0f4cdf66
RowKey|RowKey|01688_00199
Timestamp|Timestamp|Timestamp automatico della tabella di Azure 2016-09-09T22:43:42.241Z
Tipo|Tipo|Archiviazione
Nome|Nome|ArchiveHeartbeat
ObservedTime|ObservedTime|2016-09-09T22:42:36.924Z
ServiceID|ID del servizio|f70bd731-691d-41c6-8f2d-671d0bdc9c7e
ManifestName|URL del programma|asset-eb149703-ed0a-483c-91c4-e4066e72cce3/a0a5cfbf-71ec-4bd2-8c01-a92a2b38c9ba.ism
TrackName|Nome della traccia|audio_1
TrackType|Tipo di traccia|Audio/video
CustomAttribute|Stringa esadecimale che opera la distinzione fra tracce diverse con lo stesso nome e la stessa velocità in bit (diverse angolazioni)|
Bitrate|Velocità in bit della traccia|785000
Healthy|True, se FragmentDiscardedCount == 0 e ArchiveAcquisitionError == False|True (questi due valori non sono presenti nella metrica, ma lo sono nell'evento di origine)<br/><br/>Healthy è una funzione composita che restituisce il valore false in presenza di una delle seguenti condizioni:<br/><br/>- FragmentDiscardedCount > 0<br/>- ArchiveAcquisitionError == True

## <a name="general-qa"></a>Domande generali

### <a name="how-to-consume-metrics-data"></a>Come si usano i dati di metrica?

I dati di metrica vengono archiviati come una serie di tabelle di Azure nell'account di archiviazione del cliente. Questi dati possono essere usati con gli strumenti seguenti:

- SDK DI AMS
- Microsoft Azure Storage Explorer (supporta l'esportazione in valori con formato delimitato da virgole ed elaborati in Excel)
- API REST

### <a name="how-to-find-average-bandwidth-consumption"></a>Come si fa a risalire al consumo medio della larghezza di banda?

Il consumo medio della larghezza di banda è la media del valore BytesSent in un determinato intervallo di tempo.

### <a name="how-to-define-streaming-unit-count"></a>Come si fa a definire il numero delle unità di streaming?

Il numero delle unità di streaming può essere definito come la velocità effettiva massima dagli endpoint di streaming del servizio, divisa per la velocità effettiva massima di un endpoint di streaming. La velocità effettiva massima utilizzabile di un endpoint di streaming è 160 Mbps.
Si supponga, ad esempio, che la velocità effettiva massima del servizio di un cliente sia 40 MBps (il valore BytesSent massimo in un determinato intervallo di tempo). In questo caso il numero delle unità di streaming è uguale a (40 MBps)*(8 bit/byte)/(160 Mbps) = 2 unità di streaming.

### <a name="how-to-find-average-requestssecond"></a>Come si fa a risalire alla media delle richieste al secondo?

Per trovare il numero medio di richieste al secondo, calcolare il numero medio di richieste (RequestCount) in un determinato intervallo di tempo.

### <a name="how-to-define-channel-health"></a>Come si fa a definire l'integrità del canale?

L'integrità del canale può essere definita come una funzione booleana composita, che risulta false in presenza di una delle seguenti condizioni:

- OverlapCount > 0
- DiscontinuityCount > 0
- NonincreasingCount > 0
- UnalignedKeyFrames == True 
- UnalignedPresentationTime == True 
- UnexpectedBitrate == True


### <a name="how-to-detect-discontinuities"></a>Come si fa a rilevare discontinuità?

Per individuare eventuali discontinuità, risalire a tutte le voci di dati del canale dove DiscontinuityCount > 0. Il timestamp ObservedTime corrispondente indica le ore in cui si sono verificate le discontinuità.

### <a name="how-to-detect-timestamp-overlaps"></a>Come si fa a rilevare le sovrapposizioni del timestamp?

Per rilevare le sovrapposizioni del timestamp, individuare tutte le voci di dati del canale dove OverlapCount > 0. Il timestamp ObservedTime corrispondente indica le ore in cui si è verificata la sovrapposizione del timestamp.

### <a name="how-to-find-streaming-request-failures-and-reasons"></a>Come si fa a risalire agli errori nelle richieste di streaming e alle relative cause?

Per trovare gli errori nelle richieste di streaming e le relative cause, cercare tutti i dati Streaming Endpoint in cui ResultCode è diverso da S_OK. Il campo StatusCode corrispondente indica il motivo dell'errore della richiesta.

### <a name="how-to-consume-data-with-external-tools"></a>Come faccio a usare i dati con strumenti esterni?

I dati di telemetria possono essere elaborati e visualizzati con gli strumenti seguenti:

- PowerBI
- Application Insights
- Monitoraggio di Azure (in precedenza Shoebox)
- Dashboard in tempo reale di AMS
- Portale di Azure (in attesa di rilascio)

### <a name="how-to-manage-data-retention"></a>Come si fa a gestire la conservazione dei dati?

Il sistema di telemetria non consente di gestire la conservazione dei dati o l'eliminazione automatica dei vecchi record. Pertanto, i record obsoleti devono essere gestiti ed eliminati manualmente dalla tabella di archiviazione. Consultare l'SDK dell'archiviazione per sapere come fare.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

