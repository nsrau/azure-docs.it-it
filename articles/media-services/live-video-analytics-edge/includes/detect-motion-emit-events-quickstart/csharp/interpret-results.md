---
ms.openlocfilehash: eff73888a449de20b2b460d519b36c0f03c4ea77
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691100"
---
Quando si esegue il grafo multimediale, i risultati dal nodo del processore di rilevamento dei movimenti vengono inviati all'hub IoT passando attraverso il nodo sink dell'hub IoT. I messaggi visualizzati nella finestra **OUTPUT** di Visual Studio Code contengono una sezione `body` e una sezione `applicationProperties`. Per altre informazioni, vedere [Creare e leggere messaggi dell'hub IoT](../../../../../iot-hub/iot-hub-devguide-messages-construct.md).

Nei messaggi seguenti il modulo Analisi video live definisce le proprietà dell'applicazione e il contenuto del corpo.

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando viene creata un'istanza del grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore rtspsim-live555. Se la connessione riesce, viene stampato l'evento seguente.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:  
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-04-09T16:42:18.1280000Z"  
    }  
}
```

Nell'output precedente: 

* Il messaggio è un evento di diagnostica, `MediaSessionEstablished`. Indica che il nodo di origine RTSP (subject) ha stabilito una connessione con il simulatore RTSP e ha iniziato a ricevere un feed live simulato.
* In `applicationProperties` `subject` fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. In questo caso il messaggio viene originato dal nodo di origine RTSP.
* In `applicationProperties` `eventType` indica che questo evento è un evento di diagnostica.
* Il valore `eventTime` indica l'ora in cui si è verificato l'evento.
* La sezione `body` contiene dati sull'evento di diagnostica. In questo caso i dati sono costituiti dai dettagli di [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="motiondetection-event"></a>Evento MotionDetection

Quando viene rilevato movimento, il modulo Analisi video live in IoT Edge invia un evento di inferenza. `type` è impostato su `motion` per indicare che è un risultato del processore di rilevamento del movimento. Il valore `eventTime` indica quando si è verificato il movimento (in formato UTC). 

Ecco un esempio di questo messaggio:

```
  {  
  "body": {  
    "timestamp": 142843967343090,
    "inferences": [  
      {  
        "type": "motion",  
        "motion": {  
          "box": {  
            "l": 0.573222,  
            "t": 0.492537,  
            "w": 0.141667,  
            "h": 0.074074  
          }  
        }  
      }  
    ]  
  },  
  "applicationProperties": {  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/GRAPHINSTANCENAME/processors/md",  
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",  
    "eventTime": "2020-04-17T20:26:32.7010000Z",
    "dataVersion": "1.0"  
  }  
}  
```

Esempio: 

* In `applicationProperties` `subject` fa riferimento al nodo nel grafo multimediale da cui è stato generato il messaggio. In questo caso il messaggio è originato dal nodo del processore di rilevamento del movimento.
* In `applicationProperties` `eventType` indica che questo evento è un evento di analisi.
* Il valore di `eventTime` indica la data e l'ora in cui si è verificato l'evento.
* Il valore `body` rappresenta i dati sull'evento di analisi. In questo caso si tratta di un evento di inferenza e quindi il corpo contiene dati di tipo `timestamp` e `inferences`.
* I dati `inferences` indicano che `type` è `motion`. Il valore contiene dati aggiuntivi su tale evento `motion`.
* La sezione `box` contiene le coordinate per un rettangolo delimitatore intorno all'oggetto in movimento. I valori vengono normalizzati in base alla larghezza e all'altezza del video in pixel. Ad esempio, la larghezza è 1920 e l'altezza è 1080.

    ```
    l - distance from left of image
    t - distance from top of image
    w - width of bounding box
    h - height of bounding box
    ```
