---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682338"
---
Quando si esegue il grafo multimediale, i risultati dal nodo del processore di rilevamento dei movimenti vengono inviati all'hub IoT passando attraverso il nodo sink dell'hub IoT. I messaggi visualizzati nella finestra **OUTPUT** di Visual Studio Code contengono una sezione `body` e una sezione `applicationProperties`. Per altre informazioni, vedere [Creare e leggere messaggi dell'hub IoT](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Nei messaggi seguenti il modulo Analisi video live definisce le proprietà dell'applicazione e il contenuto del corpo.

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando viene creata un'istanza del grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore rtspsim-live555. Se la connessione riesce, viene visualizzato l'evento seguente.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

Nell'output precedente: 

* Il messaggio è un evento di diagnostica, `MediaSessionEstablished`. Indica che il nodo di origine RTSP (subject) ha stabilito una connessione con il simulatore RTSP e ha iniziato a ricevere un feed live simulato.
* In `applicationProperties` `subject` fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. In questo caso il messaggio viene originato dal nodo di origine RTSP.
* In `applicationProperties` `eventType` indica che questo evento è un evento di diagnostica.
* Il valore di `eventTime` indica la data e l'ora in cui si è verificato l'evento.
* La sezione `body` contiene dati sull'evento di diagnostica. In questo caso i dati sono costituiti dai dettagli di [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quando viene rilevato un movimento, il nodo del processore di controllo del segnale viene attivato e il nodo sink di file nel grafo multimediale avvia la scrittura di un file MP4. Il nodo sink di file invia un evento operativo. `type` è impostato su `motion` per indicare che è un risultato del processore di rilevamento del movimento. Il valore `eventTime` è l'ora UTC in cui si è verificato il movimento. Per altre informazioni su questo processo, vedere la sezione [Panoramica](#overview) in questo argomento di avvio rapido.

Ecco un esempio di questo messaggio:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

Nel messaggio precedente: 

* In `applicationProperties` `subject` fa riferimento al nodo nel grafo multimediale da cui è stato generato il messaggio. In questo caso il messaggio viene originato dal nodo sink di file.
* In `applicationProperties` `eventType` indica che questo evento è un evento operativo.
* Il valore di `eventTime` indica la data e l'ora in cui si è verificato l'evento. Si verifica da 5 a 6 secondi dopo `MediaSessionEstablished` e dopo l'inizio del flusso del video. L'ora corrisponde all'indicazione dei 5-6 secondi dopo i quali l'[auto ha iniziato a muoversi](#review-the-sample-video) nel parcheggio.
* La sezione `body` contiene dati sull'evento operativo. In questo caso i dati comprendono `outputType` e `outputLocation`.
* La variabile `outputType` indica che queste informazioni riguardano il percorso del file.
* Il valore `outputLocation` è il percorso del file MP4 nel modulo Edge.

### <a name="recordingstopped-and-recordingavailable-events"></a>Eventi RecordingStopped e RecordingAvailable

Se si esaminano le proprietà del nodo del processore di controllo del segnale nella [topologia del grafo](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), si noterà che i tempi di attivazione sono impostati su 5 secondi. Quindi, circa 5 secondi dopo la ricezione dell'evento `RecordingStarted`, vengono generati:

* Un evento `RecordingStopped`, che indica che la registrazione è stata interrotta.
* Un evento `RecordingAvailable`, che indica che è ora possibile usare il file MP4 per la visualizzazione.

I due eventi di solito vengono generati a pochi secondi l'uno dall'altro.
