---
title: Introduzione ad Analisi di video live in IoT Edge - Azure
description: Questo argomento di avvio rapido descrive come iniziare a usare Analisi video live in IoT Edge. Informazioni su come rilevare il movimento in un flusso video live.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: c7e5efa19c27c3f56f9653ed933c7ad290d18408
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568048"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Avvio rapido: Introduzione: analisi di video live in IoT Edge

Questo avvio rapido illustra la procedura per iniziare a usare Analisi di video live in IoT Edge. Come dispositivo IoT Edge viene usata una VM di Azure. Viene anche usato un flusso video live simulato. 

Dopo aver completato i passaggi di configurazione, sarà possibile eseguire un flusso video live simulato tramite un grafo multimediale che rileva e segnala qualsiasi movimento in tale flusso. Il diagramma seguente rappresenta graficamente il grafo multimediale.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/analyze-live-video/motion-detection.svg" alt-text="Analisi di video live basata su un rilevamento del movimento":::

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non si ha un account, [crearne uno gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) nel computer di sviluppo. Assicurarsi che sia presente l'[estensione Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Verificare che la rete a cui è connesso il computer di sviluppo consenta il protocollo AMQP (Advanced Message Queueing Protocol) sulla porta 5671. Questa configurazione consente la comunicazione tra Azure IoT Tools e l'hub IoT di Azure.

> [!TIP]
> È possibile che venga chiesto di installare Docker durante l'installazione dell'estensione Azure IoT Tools. Ignorare tranquillamente questa richiesta.

## <a name="set-up-azure-resources"></a>Configurare le risorse di Azure

Per questa esercitazione sono necessarie le risorse di Azure seguenti:

* Hub IoT
* Account di archiviazione
* Account Servizi multimediali di Azure
* Una VM Linux in Azure, con il [runtime IoT Edge](../../iot-edge/how-to-install-iot-edge-linux.md) installato

Per questo argomento di avvio rapido è consigliabile usare lo [script di configurazione delle risorse di Analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) per distribuire le risorse necessarie nella sottoscrizione di Azure. A questo scopo, attenersi alla procedura seguente:

1. Passare ad [Azure Cloud Shell](https://shell.azure.com).
1. Se si usa Cloud Shell per la prima volta, verrà chiesto di selezionare una sottoscrizione, oltre che di creare un account di archiviazione e una condivisione di File di Microsoft Azure. Selezionare **Crea risorsa di archiviazione** per creare un account di archiviazione per le informazioni della sessione Cloud Shell. Questo account di archiviazione è distinto da quello che verrà creato dallo script per l'uso con l'account di Servizi multimediali di Azure.
1. Nel menu a discesa sul lato sinistro della finestra di Cloud Shell selezionare **Bash** come ambiente.

    ![Selezione dell'ambiente](./media/quickstarts/env-selector.png)

1. Eseguire il comando seguente.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Se lo script viene completato correttamente, verranno visualizzate tutte le risorse necessarie nella sottoscrizione. Nell'output dello script una tabella di risorse include il nome dell'hub IoT. Cercare il tipo di risorsa `Microsoft.Devices/IotHubs` e prendere nota del nome. Servirà nel passaggio successivo. 

Lo script genera anche alcuni file di configurazione nella directory *~/clouddrive/lva-sample/* . Questi file saranno necessari più avanti in questo argomento.

## <a name="deploy-modules-on-your-edge-device"></a>Distribuire i moduli nel dispositivo perimetrale

Eseguire il comando seguente da Cloud Shell.

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Questo comando distribuisce i moduli seguenti al dispositivo Edge, che in questo caso è la VM Linux.

* Analisi video live in IoT Edge (nome del modulo `lvaEdge`)
* Simulatore RTSP (Real-Time Streaming Protocol) (nome del modulo `rtspsim`)

Il modulo del simulatore RTSP simula un flusso video live usando un file video copiato nel dispositivo Edge quando è stato eseguito lo [script di configurazione delle risorse di Analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

A questo punto i moduli sono distribuiti ma non sono disponibili grafi multimediali attivi.

## <a name="configure-the-azure-iot-tools-extension"></a>Configurare l'estensione Azure IoT Tools

Seguire queste istruzioni per connettersi all'hub IoT usando l'estensione Azure IoT Tools.

1. In Visual Studio Code selezionare **Visualizza** > **Explorer**. In alternativa, premere CTRL+MAIUSC+E.
1. Nell'angolo in basso a sinistra della scheda **Explorer** selezionare **Hub IoT di Azure**.
1. Selezionare l'icona **Altre opzioni** icona per visualizzare il menu di scelta rapida. Quindi scegliere **Set IoT Hub Connection String** (Imposta la stringa di connessione dell'hub IoT).
1. Quando viene visualizzata una casella di input, immettere la stringa di connessione dell'hub IoT. In Cloud Shell è possibile ottenere la stringa di connessione da *~/clouddrive/lva-sample/appsettings.json*.

Se la connessione riesce, viene visualizzato l'elenco di dispositivi Edge. Si dovrebbe vedere almeno un dispositivo denominato **lva-sample-device**. È ora possibile gestire i dispositivi IoT Edge e interagire con l'hub IoT di Azure tramite il menu di scelta rapida. Per visualizzare i moduli distribuiti nel dispositivo Edge, espandere il nodo **Moduli** sotto **lva-sample-device**.

![nodo lva-sample-device](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-method-calls"></a>Usare chiamate dei metodi diretti

È possibile usare il modulo per analizzare i flussi video live richiamando metodi diretti. Per altre informazioni, vedere [Metodi diretti per Analisi video live in IoT Edge](direct-methods.md). 

### <a name="invoke-graphtopologylist"></a>Richiamare GraphTopologyList

Per enumerare tutte le [topologie del grafo](media-graph-concept.md#media-graph-topologies-and-instances) presenti nel modulo:

1. In Visual Studio Code fare clic con il pulsante destro del mouse sul modulo **lvaEdge** e scegliere **Invoke Module Direct Method** (Richiama metodo diretto del modulo).
1. Nella casella che viene visualizzata immettere *GraphTopologyList*.
1. Incollare il payload JSON seguente e quindi incollarlo nella casella. Quindi premere INVIO.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    Entro alcuni secondi nella finestra **OUTPUT** verrà visualizzata la risposta seguente.

    ```
    [DirectMethod] Invoking Direct Method [GraphTopologyList] to [lva-sample-device/lvaEdge] ...
    [DirectMethod] Response from [lva-sample-device/lvaEdge]:
    {
      "status": 200,
      "payload": {
        "value": []
      }
    }
    ```
    
    Questa risposta è prevista, perché non sono state create topologie del grafo.
    

### <a name="invoke-graphtopologyset"></a>Richiamare GraphTopologySet

Usando i passaggi per richiamare `GraphTopologyList`, è possibile richiamare `GraphTopologySet` per impostare una [topologia del grafo](media-graph-concept.md#media-graph-topologies-and-instances). Usare il codice JSON seguente come payload.

```
{
    "@apiVersion": "1.0",
    "name": "MotionDetection",
    "properties": {
        "description": "Analyzing live video to detect motion and emit events",
        "parameters": [
            {
                "name": "rtspUserName",
                "type": "String",
                "description": "rtsp source user name.",
                "default": "dummyUserName"
            },
            {
                "name": "rtspPassword",
                "type": "String",
                "description": "rtsp source password.",
                "default": "dummyPassword"
            },
            {
                "name": "rtspUrl",
                "type": "String",
                "description": "rtsp Url"
            }
        ],
        "sources": [
            {
                "@type": "#Microsoft.Media.MediaGraphRtspSource",
                "name": "rtspSource",
                "endpoint": {
                    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
                    "url": "${rtspUrl}",
                    "credentials": {
                        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
                        "username": "${rtspUserName}",
                        "password": "${rtspPassword}"
                    }
                }
            }
        ],
        "processors": [
            {
                "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
                "name": "motionDetection",
                "sensitivity": "medium",
                "inputs": [
                    {
                        "nodeName": "rtspSource"
                    }
                ]
            }
        ],
        "sinks": [
            {
                "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
                "name": "hubSink",
                "hubOutputName": "inferenceOutput",
                "inputs": [
                    {
                        "nodeName": "motionDetection"
                    }
                ]
            }
        ]
    }
}

```

Questo payload JSON crea una topologia del grafo che definisce tre parametri. Due di questi parametri hanno valori predefiniti. La topologia ha un nodo di origine (origine RTSP), un nodo processore (processore di rilevamento movimento) e un nodo sink (sink dell'hub IoT).

Entro alcuni secondi nella finestra **OUTPUT** verrà visualizzata la risposta seguente.

```
[DirectMethod] Invoking Direct Method [GraphTopologySet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Lo stato restituito è 201. Questo stato indica che è stata creata una nuova topologia. 

Provare a seguire questa procedura:

1. Richiamare di nuovo `GraphTopologySet`. Il codice di stato restituito è 200. Questo codice di stato indica che è stata aggiornata una topologia esistente.
1. Richiamare di nuovo `GraphTopologySet`, ma cambiare la stringa della descrizione. Il codice di stato restituito è 200 e la descrizione è stata aggiornata al nuovo valore.
1. Richiamare `GraphTopologyList` come illustrato nella sezione precedente. Ora è possibile visualizzare la topologia `MotionDetection` nel payload restituito.

### <a name="invoke-graphtopologyget"></a>Richiamare GraphTopologyGet

Richiamare `GraphTopologyGet` usando il payload seguente.

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Entro alcuni secondi nella finestra **OUTPUT** verrà visualizzata la risposta seguente:

```
[DirectMethod] Invoking Direct Method [GraphTopologyGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "systemData": {
      "createdAt": "2020-05-19T07:41:34.507Z",
      "lastModifiedAt": "2020-05-19T07:41:34.507Z"
    },
    "name": "MotionDetection",
    "properties": {
      "description": "Analyzing live video to detect motion and emit events",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "Tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphMotionDetectionProcessor",
          "sensitivity": "medium",
          "name": "motionDetection",
          "inputs": [
            {
              "nodeName": "rtspSource",
              "outputSelectors": []
            }
          ]
        }
      ],
      "sinks": [
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "hubOutputName": "inferenceOutput",
          "name": "hubSink",
          "inputs": [
            {
              "nodeName": "motionDetection",
              "outputSelectors": []
            }
          ]
        }
      ]
    }
  }
}
```

Nel payload della risposta si notino questi dettagli:

* Il codice di stato è 200, a indicare che l'operazione è riuscita.
* Il payload include il timestamp `created` e il timestamp `lastModified`.

### <a name="invoke-graphinstanceset"></a>Richiamare GraphInstanceSet

Creare un'istanza del grafo che fa riferimento alla topologia del grafo precedente. Le istanze del grafo consentono di analizzare flussi video live provenienti da molte videocamere usando la stessa topologia del grafo. Per altre informazioni, vedere [Topologie e istanze del grafo multimediale](media-graph-concept.md#media-graph-topologies-and-instances).

Richiamare il metodo diretto `GraphInstanceSet` usando il payload seguente.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1",
    "properties" : {
        "topologyName" : "MotionDetection",
        "description" : "Sample graph description",
        "parameters" : [
            { "name" : "rtspUrl", "value" : "rtsp://rtspsim:554/media/camera-300s.mkv" }
        ]
    }
}
```

Si noti che questo payload:

* Specifica il nome della topologia `MotionDetection` per cui è necessario creare l'istanza.
* Contiene il valore del parametro per `rtspUrl`, che non aveva un valore predefinito nel payload della topologia del grafo.

Entro pochi secondi nella finestra **OUTPUT** verrà visualizzata la risposta seguente:

```
[DirectMethod] Invoking Direct Method [GraphInstanceSet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 201,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Inactive",
      "description": "Sample graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Nel payload della risposta si noti che:

* Il codice di stato è 201, a indicare che è stata creata una nuova istanza.
* Lo stato è `Inactive`, a indicare che l'istanza del grafo è stata creata ma non è stata attivata. Per altre informazioni, vedere [Stati del grafo multimediale](media-graph-concept.md).

Provare a seguire questa procedura:

1. Richiamare di nuovo `GraphInstanceSet` usando lo stesso payload. Si noti che il codice di stato restituito è 200.
1. Richiamare di nuovo `GraphInstanceSet`, ma usare una descrizione diversa. Si noti la descrizione aggiornata nel payload della risposta, a indicare che l'istanza del grafo è stata aggiornata.
1. Richiamare `GraphInstanceSet`, ma cambiare il nome in `Sample-Graph-2`. Nel payload della risposta osservare l'istanza del grafo appena creata (ovvero il codice di stato 201).

### <a name="invoke-graphinstanceactivate"></a>Richiamare GraphInstanceActivate

Ora attivare l'istanza del grafo per avviare il flusso di video live tramite il modulo. Richiamare il metodo diretto `GraphInstanceActivate` usando il payload seguente.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Entro alcuni secondi nella finestra **OUTPUT** verrà visualizzata la risposta seguente.

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Il codice di stato 200 indica che l'istanza del grafo è stata attivata.

### <a name="invoke-graphinstanceget"></a>Richiamare GraphInstanceGet

Ora richiamare il metodo diretto `GraphInstanceGet` usando il payload seguente.

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

Entro alcuni secondi nella finestra **OUTPUT** verrà visualizzata la risposta seguente.

```
[DirectMethod] Invoking Direct Method [GraphInstanceGet] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": {
    "name": "Sample-Graph-1",
    "properties": {
      "created": "2020-05-19T07:44:33.868Z",
      "lastModified": "2020-05-19T07:44:33.868Z",
      "state": "Active",
      "description": "graph description",
      "topologyName": "MotionDetection",
      "parameters": [
        {
          "name": "rtspUrl",
          "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
        }
      ]
    }
  }
}
```

Nel payload della risposta si notino questi dettagli:

* Il codice di stato è 200, a indicare che l'operazione è riuscita.
* Lo stato è `Active`, a indicare che l'istanza del grafo è ora attiva.

## <a name="observe-results"></a>Osservare i risultati

L'istanza del grafo creata e attivata in precedenza usa il nodo del processore di rilevamento del movimento per rilevare il movimento nel flusso video live in ingresso. Invia gli eventi al nodo sink dell'hub IoT. Questi eventi vengono inoltrati all'hub di IoT Edge. 

Per osservare i risultati, seguire questi passaggi.

1. In Visual Studio Code aprire il riquadro **Explorer**. Nell'angolo in basso a sinistra cercare **Hub IoT di Azure**.
2. Espandere il nodo **Dispositivi**.
3. Fare clic con il pulsante destro del mouse su **lva-sample-device** e scegliere **Avvia monitoraggio endpoint eventi predefinito**.

    ![Avviare il monitoraggio degli eventi dell'hub IoT](./media/quickstarts/start-monitoring-iothub-events.png)
    
Nella finestra **OUTPUT** viene visualizzato il messaggio seguente:

```
[IoTHubMonitor] [7:44:33 AM] Message received from [lva-sample-device/lvaEdge]:
{
    "body": {
    "timestamp": 143005362606360,
    "inferences": [
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.828452,
            "t": 0.455224,
            "w": 0.1,
            "h": 0.088889
            }
        }
        },
        {
        "type": "motion",
        "motion": {
            "box": {
            "l": 0.661088,
            "t": 0.597015,
            "w": 0.0625,
            "h": 0.051852
            }
        }
        }
    ]
    },
    "applicationProperties": {
    "topic": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{amsAccountName}",
    "subject": "/graphInstances/Sample-Graph-1/processors/motionDetection",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-19T07:45:34.404Z",
    "dataVersion": "1.0"
    }
}
```

Si notino questi dettagli:

* Il messaggio contiene una sezione `body` e una sezione `applicationProperties`. Per altre informazioni, vedere [Creare e leggere messaggi dell'hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).
* In `applicationProperties` `subject` fa riferimento al nodo in`MediaGraph` da cui è stato generato il messaggio. In questo caso il messaggio è originato dal processore di rilevamento del movimento.
* In `applicationProperties` `eventType` indica che questo evento è un evento di analisi.
* Il valore di `eventTime` indica la data e l'ora in cui si è verificato l'evento.
* La sezione `body` contiene dati sull'evento di analisi. In questo caso si tratta di un evento di inferenza e quindi il corpo contiene dati di tipo `timestamp` e `inferences`.
* La sezione `inferences` indica che `type` è `motion`. Fornisce dati aggiuntivi sull'evento `motion`.

Se si lascia in esecuzione il grafo multimediale per un periodo di tempo, nella finestra **OUTPUT** verrà visualizzato il messaggio seguente.

```
[IoTHubMonitor] [7:47:45 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1588948185746703 1 IN IP4 172.xx.xx.xx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-19T07:47:45.747Z"
  }
}
```

In questo messaggio si notino i dettagli seguenti:

* In `applicationProperties` `subject` indica che il messaggio è stato generato dal nodo di origine RTSP nel grafo multimediale.
* In `applicationProperties` `eventType` indica che questo evento è un evento di diagnostica.
* `body` contiene dati sull'evento di diagnostica. In questo caso, il messaggio contiene il corpo perché l'evento è `MediaSessionEstablished`.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Richiamare metodi diretti aggiuntivi per la pulizia

Richiamare i metodi diretti per disattivare l'istanza del grafo e poi eliminarla.

### <a name="invoke-graphinstancedeactivate"></a>Richiamare GraphInstanceDeactivate

Richiamare il metodo diretto `GraphInstanceDeactivate` usando il payload seguente.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Entro alcuni secondi nella finestra **OUTPUT** verrà visualizzata la risposta seguente:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Il codice di stato 200 indica che l'istanza del grafo è stata disattivata.

Successivamente, provare a richiamare `GraphInstanceGet` come indicato in precedenza in questo articolo. Osservare il valore di `state`.

### <a name="invoke-graphinstancedelete"></a>Richiamare GraphInstanceDelete

Richiamare il metodo diretto `GraphInstanceDelete` usando il payload seguente.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

Entro alcuni secondi nella finestra **OUTPUT** verrà visualizzata la risposta seguente:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Il codice di stato 200 indica che l'istanza del grafo è stata eliminata.

### <a name="invoke-graphtopologydelete"></a>Richiamare GraphTopologyDelete

Richiamare il metodo diretto `GraphTopologyDelete` usando il payload seguente.

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

Entro alcuni secondi nella finestra **OUTPUT** verrà visualizzata la risposta seguente.

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Il codice di stato 200 indica che la topologia del grafo è stata eliminata.

Provare a seguire questa procedura:

1. Richiamare `GraphTopologyList` e osservare che il modulo non contiene topologie del grafo.
1. Richiamare `GraphInstanceList` usando lo stesso payload di `GraphTopologyList`. Si noti che non vengono enumerate istanze del grafo.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare le risorse create in questo argomento di avvio rapido.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [registrare video usando Analisi video live in IoT Edge](continuous-video-recording-tutorial.md).
* Altre informazioni sui [messaggi di diagnostica](monitoring-logging.md).
