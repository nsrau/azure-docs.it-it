---
title: Monitoraggio e registrazione-Azure
description: Questo articolo fornisce una panoramica di analisi video in tempo reale su IoT Edge monitoraggio e registrazione.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: e1f31c6bb3ea344286ad9af89417ca9f8fd59527
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934294"
---
# <a name="monitoring-and-logging"></a>Monitoraggio e registrazione

In questo articolo si apprenderà come ricevere gli eventi dal modulo di analisi video live sul modulo IoT Edge per il monitoraggio remoto. 

Si apprenderà anche come è possibile controllare i log generati dal modulo.

## <a name="taxonomy-of-events"></a>Tassonomia degli eventi

Analisi video in tempo reale su IoT Edge emette eventi o dati di telemetria in base alla tassonomia seguente.

![Analisi di video in tempo reale su IoT Edge schema di telemetria](./media/telemetry-schema/taxonomy.png)

* Operativo: eventi generati come parte di azioni eseguite da un utente o durante l'esecuzione di un [grafico multimediale](media-graph-concept.md).
   
   * Volume: dovrebbe essere bassa (alcune volte al minuto o persino una frequenza inferiore).
   * Esempi:

      Registrazione avviata (sotto), registrazione arrestata
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Diagnostica: eventi che consentono di diagnosticare problemi e/o problemi con le prestazioni.

   * Volume: può essere alto (più volte al minuto).
   * Esempi:
   
      Informazioni su RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) (sotto) o gap nel feed video in arrivo.

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* Analytics: eventi generati come parte dell'analisi dei video.

   * Volume: può essere alto (più volte al minuto o più spesso).
   * Esempi:
      
      Movimento rilevato (sotto), risultato di inferenza.
   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```
Gli eventi generati dal modulo vengono inviati all' [Hub IOT Edge](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)e da qui è possibile instradarli ad altre destinazioni. 

### <a name="timestamps-in-analytic-events"></a>Timestamp negli eventi analitici
Come indicato in precedenza, agli eventi generati come parte dell'analisi video è associato un timestamp. Se è stato [registrato il video live](video-recording-concept.md) come parte della topologia Graph, questo timestamp consente di individuare la posizione del video registrato in cui si è verificato l'evento specifico. Di seguito sono riportate le linee guida su come eseguire il mapping del timestamp in un evento analitico alla sequenza temporale del video registrato in un [asset di servizi multimediali di Azure](terminology.md#asset).

Estrarre innanzitutto il `eventTime` valore. Usare questo valore in un [filtro di intervallo di tempo](playback-recordings-how-to.md#time-range-filters) per recuperare una parte appropriata della registrazione. È ad esempio possibile che si desideri recuperare video che inizia 30 secondi prima `eventTime` e termina 30 secondi dopo. Con l'esempio precedente, dove `eventTime` è 2020-05-12T23:33:09.381 z, una richiesta di un manifesto HLS per la finestra +/-30s avrà un aspetto simile al seguente:
```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```
L'URL precedente restituisce una cosiddetta [playlist Master](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)che contiene gli URL per le playlist multimediali. La playlist multimediale conterrà voci simili alle seguenti:

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
Nella voce precedente, la voce indica che è disponibile un frammento video che inizia con un valore timestamp pari a `143039375031270` . Il `timestamp` valore nell'evento analitico usa la stessa scala cronologica della playlist media e può essere usato per identificare il frammento video pertinente e cercare il frame corretto.

Per altre informazioni, è possibile leggere uno dei numerosi [articoli](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) sulla ricerca di frame accurati in HLS.

## <a name="controlling-events"></a>Controllo degli eventi

È possibile usare le proprietà gemelle del modulo seguenti, come documentato nello [schema JSON del modulo gemello](module-twin-configuration-schema.md), per controllare gli eventi operativi e di diagnostica pubblicati dal modulo di analisi video in tempo reale su IOT Edge.

`diagnosticsEventsOutputName` – include e fornisce il valore (any) per questa proprietà, per ottenere gli eventi di diagnostica dal modulo. Ometterlo o lasciarlo vuoto per arrestare la pubblicazione di eventi di diagnostica da parte del modulo.
   
`operationalEventsOutputName` – include e fornisce il valore (any) per questa proprietà, per ottenere gli eventi operativi dal modulo. Ometterlo o lasciarlo vuoto per arrestare la pubblicazione di eventi operativi da parte del modulo.
   
Gli eventi di analisi vengono generati da nodi come il processore di rilevamento del movimento o il processore di estensione HTTP e il sink dell'hub Internet viene usato per inviarli all'hub IoT Edge. 

È possibile controllare il [routing di tutti gli eventi precedenti](../../iot-edge/module-composition.md#declare-routes) tramite una proprietà desiderata del modulo $edgeHub gemello (nel manifesto di distribuzione):

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

In precedenza, lvaEdge è il nome per l'analisi video in tempo reale sul modulo IoT Edge e la regola di routing segue lo schema definito in [Declare Routes](../../iot-edge/module-composition.md#declare-routes).

> [!NOTE]
> Per assicurarsi che gli eventi di analisi raggiungano l'hub di IoT Edge, è necessario che sia presente un nodo del sink dell'hub Internet a valle di qualsiasi nodo del processore di rilevamento del movimento e/o di qualsiasi nodo del processore di estensione HTTP.

## <a name="event-schema"></a>Schema di eventi

Gli eventi hanno origine nel dispositivo perimetrale e possono essere usati nel perimetro o nel cloud. Gli eventi generati da analisi video in tempo reale su IoT Edge sono conformi al [modello di messaggistica di streaming](../../iot-hub/iot-hub-devguide-messages-construct.md) stabilito dall'hub Azure Internet, con proprietà di sistema, proprietà dell'applicazione e corpo.

### <a name="summary"></a>Riepilogo

Ogni evento, se osservato tramite l'hub Internet delle cose, avrà un set di proprietà comuni, come descritto di seguito.

|Proprietà   |Tipo di proprietà| Tipo di dati   |Descrizione|
|---|---|---|---|
|message-id |sistema |guid|  ID evento univoco.|
|argomento| applicationProperty |string|    Azure Resource Manager percorso per l'account di servizi multimediali.|
|subject|   applicationProperty |string|    Percorso secondario dell'entità che emette l'evento.|
|eventTime| applicationProperty|    string| Ora di generazione dell'evento.|
|eventType| applicationProperty |string|    Identificatore del tipo di evento (vedere di seguito).|
|Corpo|Corpo  |object|    Particolari dati dell'evento.|
|dataVersion    |applicationProperty|   string  |{Major}. Secondaria|

### <a name="properties"></a>Proprietà

#### <a name="message-id"></a>message-id

Identificatore univoco globale (GUID) dell'evento

#### <a name="topic"></a>argomento

Rappresenta l'account di servizi multimediali di Azure associato al grafico.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

Entità che emette l'evento:

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

La proprietà Subject consente di eseguire il mapping degli eventi generici al modulo di generazione. Ad esempio, in caso di nome utente o password RTSP non valida, l'evento generato si trova nel `Microsoft.Media.Graph.Diagnostics.ProtocolError` `/graphInstances/myGraph/sources/myRtspSource` nodo.

#### <a name="event-types"></a>Tipi di eventi

I tipi di evento vengono assegnati a uno spazio dei nomi in base allo schema seguente:

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>Classi di eventi

|Nome della classe|Descrizione|
|---|---|
|Analytics  |Eventi generati come parte dell'analisi del contenuto.|
|Diagnostica    |Eventi che facilitano la diagnostica dei problemi e delle prestazioni.|
|Operativo    |Eventi generati come parte dell'operazione di risorsa.|

I tipi di evento sono specifici per ogni classe di evento.

Esempi:

* Microsoft. Media. Graph. Analytics. inferenza
* Microsoft. Media. Graph. Diagnostics. AuthorizationError
* Microsoft. Media. Graph. Operational. GraphInstanceStarted

### <a name="event-time"></a>Ora dell'evento

L'ora dell'evento è descritta in stringa ISO8601 e indica l'ora in cui si è verificato l'evento.

## <a name="logging"></a>Registrazione

Analogamente ad altri moduli IoT Edge, è anche possibile [esaminare i log dei contenitori](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) sul dispositivo perimetrale. Le informazioni scritte nei log possono essere controllate dalle proprietà [gemelle del modulo seguenti](module-twin-configuration-schema.md) :

* logLevel

   * I valori consentiti sono verbose, Information, Warning, Error e None.
   * Il valore predefinito è informazioni: i log contengono errori, avvisi e informazioni. messaggi.
   * Se si imposta il valore su avviso, i log conterranno messaggi di errore e di avviso.
   * Se si imposta il valore su Error, i log conterranno solo messaggi di errore.
   * Se si imposta il valore su None, non verrà generato alcun log. questa operazione non è consigliata.
   * È consigliabile usare solo verbose se è necessario condividere i log con il supporto di Azure per la diagnosi di un problema.
* logCategories

   * Elenco delimitato da virgole di uno o più degli elementi seguenti: Application, Events, MediaPipeline.
   * Impostazione predefinita: applicazione, eventi.
   * Applicazione: informazioni di alto livello del modulo, ad esempio messaggi di avvio del modulo, errori di ambiente e chiamate a metodi diretti.
   * Eventi: si tratta di tutti gli eventi descritti in precedenza in questo articolo.
   * MediaPipeline: si tratta di log di basso livello che possono offrire informazioni dettagliate per la risoluzione dei problemi, ad esempio le difficoltà di stabilire una connessione con una fotocamera che supporta RTSP.
   
### <a name="generating-debug-logs"></a>Generazione dei log di debug

In alcuni casi, potrebbe essere necessario generare log più dettagliati rispetto a quelli descritti in precedenza per consentire al supporto tecnico di Azure di risolvere un problema. Per eseguire questa operazione, è necessario eseguire due passaggi.

Per prima cosa, [collegare lo spazio di archiviazione del modulo alla risorsa di archiviazione del dispositivo](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) tramite CreateOptions. Se si esamina un [modello di manifesto di distribuzione](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) dall'avvio rapido, verrà visualizzato quanto segue:

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

Sopra consente al modulo perimetrale di scrivere i log nel percorso di archiviazione (dispositivo) "/var/local/MediaServices/". Se si aggiunge la proprietà desiderata seguente al modulo:

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

Il modulo scriverà quindi i log di debug in formato binario nel percorso di archiviazione (dispositivo)/var/local/MediaServices/debuglogs/, che è possibile condividere con il supporto di Azure.

## <a name="faq"></a>Domande frequenti

[Domande frequenti](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>Passaggi successivi

[Registrazione continua di video](continuous-video-recording-tutorial.md)
