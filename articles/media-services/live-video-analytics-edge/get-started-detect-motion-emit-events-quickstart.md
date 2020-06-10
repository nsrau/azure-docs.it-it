---
title: Introduzione ad Analisi di video live in IoT Edge - Azure
description: Questo avvio rapido illustra come iniziare a usare Analisi di video live in IoT Edge e rilevare il movimento in un flusso video live.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 307a81938be3e25b8a6a07bb3696ca3b7647c0aa
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261566"
---
# <a name="quickstart-get-started---live-video-analytics-on-iot-edge"></a>Avvio rapido: Introduzione: analisi di video live in IoT Edge

Questo avvio rapido illustra la procedura per iniziare a usare Analisi di video live in IoT Edge. Usa una macchina virtuale di Azure come dispositivo IoT Edge e un flusso video live simulato. Dopo il completamento della procedura di configurazione, sarà possibile eseguire un flusso video live simulato tramite un grafo multimediale che rileva e segnala tutti i movimenti in tale flusso. Il diagramma seguente illustra una rappresentazione grafica del grafo multimediale.

![Analisi di video live basata su un rilevamento del movimento](./media/analyze-live-video/motion-detection.png)

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) nel computer di sviluppo con l'[estensione Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* La rete a cui è connesso il computer di sviluppo deve consentire il protocollo AMQP sulla porta 5671. In questo modo Azure IoT Tools può comunicare con l'hub IoT di Azure.

> [!TIP]
> È possibile che venga richiesta l'installazione di Docker durante l'installazione dell'estensione Azure IoT Tools. È possibile ignorare la richiesta.

## <a name="set-up-azure-resources"></a>Configurare le risorse di Azure

Per questa esercitazione sono necessarie le risorse di Azure seguenti.

* Hub IoT
* Account di archiviazione
* Account Servizi multimediali di Azure
* Macchina virtuale Linux in Azure, con [runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) installato

Per questo avvio rapido è consigliabile usare lo [script di configurazione delle risorse di Analisi di video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) per distribuire le risorse di Azure indicate in precedenza nella sottoscrizione di Azure. A tale scopo, seguire questa procedura:

1. Passare a https://shell.azure.com.
1. Se si usa Cloud Shell per la prima volta, verrà richiesto di selezionare una sottoscrizione, creare un account di archiviazione e una condivisione di File di Microsoft Azure. Selezionare "Create storage" (Crea risorsa di archiviazione) per creare un account di archiviazione per l'archiviazione delle informazioni sulla sessione Cloud Shell. Questo account di archiviazione è distinto da quello che verrà creato dallo script per l'uso con l'account di Servizi multimediali di Azure.
1. Dall'elenco a discesa sul lato sinistro della finestra della shell, selezionare "Bash" come ambiente.

    ![Selettore dell'ambiente](./media/quickstarts/env-selector.png)

1. Eseguire il comando seguente

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
Se lo script viene completato correttamente, saranno visualizzate tutte le risorse indicate in precedenza nella sottoscrizione. L'output dello script prevede anche la generazione di una tabella di risorse che elenca il nome dell'hub IoT. Individuare il tipo di risorsa **"Microsoft.Devices/IotHubs"** e prendere nota del nome. Servirà nel passaggio successivo. Lo script genererà anche alcuni file di configurazione nella directory ~/clouddrive/lva-sample/. Saranno necessari più avanti in questo avvio rapido.

## <a name="deploy-modules-on-your-edge-device"></a>Distribuire i moduli nel dispositivo perimetrale

Eseguire il comando seguente da Cloud Shell

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content ~/clouddrive/lva-sample/edge-deployment/deployment.amd64.json
```

Il comando precedente distribuirà i moduli seguenti nel dispositivo perimetrale (la macchina virtuale Linux):

* Analisi di video live in IoT Edge (nome del modulo "lvaEdge")
* Simulatore RTSP (nome del modulo "rtspsim")

Il modulo del simulatore RTSP simula un flusso video live usando un file video archiviato che è stato copiato nel dispositivo perimetrale durante l'esecuzione dello [script di configurazione delle risorse di Analisi di video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). A questo punto i moduli sono distribuiti ma non esistono grafi multimediali attivi.

## <a name="configure-azure-iot-tools-extension-in-visual-studio-code"></a>Configurare l'estensione Azure IoT Tools in Visual Studio Code

Avviare Visual Studio Code e seguire le istruzioni riportate di seguito per connettersi all'hub IoT di Azure usando l'estensione Azure IoT Tools.

1. Passare alla scheda Explorer (Esplora risorse) in Visual Studio Code selezionando **View** (Visualizza)  > **Explorer** (Esplora risorse) o semplicemente premendo CTRL+MAIUSC+E.
1. Nella scheda Explorer (Esplora risorse) fare clic sull'hub IoT di Azure nell'angolo inferiore sinistro.
1. Fare clic sull'icona Altre opzioni per visualizzare il menu di scelta rapida e selezionare l'opzione "Set IoT Hub Connection String" (Imposta la stringa di connessione dell'hub IoT).
1. Si aprirà una casella di input, quindi immettere la stringa di connessione dell'hub IoT. È possibile ottenere la stringa di connessione per l'hub IoT da ~/clouddrive/lva-sample/appsettings.json in Cloud Shell.
1. Se la connessione ha esito positivo, verrà visualizzato l'elenco dei dispositivi perimetrali, che conterrà almeno un dispositivo denominato "lva-sample-device".
1. È ora possibile gestire i dispositivi IoT Edge e interagire con l'hub IoT di Azure tramite il menu di scelta rapida.
1. È possibile visualizzare i moduli distribuiti nel dispositivo perimetrale espandendo il nodo Modules (Moduli) in "lva-sample-device".

    ![nodo lva-sample-device](./media/quickstarts/lva-sample-device-node.png)

## <a name="use-direct-methods"></a>Usare metodi diretti

È possibile usare il modulo per analizzare i flussi video live richiamando metodi diretti. Leggere [Metodi diretti per Analisi di video live in IoT Edge](direct-methods.md) per conoscere tutti i metodi diretti forniti dal modulo. 

### <a name="invoke-graphtopologylist"></a>Richiamare GraphTopologyList
Vengono enumerate tutte le [topologie del grafo](media-graph-concept.md#media-graph-topologies-and-instances) presenti nel modulo.

1. Fare clic con il pulsante destro del mouse sul modulo "lvaEdge" e selezionare "Invoke Module Direct Method" (Richiama metodo diretto del modulo) dal menu di scelta rapida.
1. Si aprirà una casella di modifica nella parte superiore centrale della finestra di Visual Studio Code. Immettere "GraphTopologyList" nella casella di modifica e premere INVIO.
1. Quindi copiare e incollare il payload JSON seguente nella casella di modifica e premere INVIO.

    ```
    {
        "@apiVersion" : "1.0"
    }
    ```

    In pochi secondi si aprirà la finestra OUTPUT nel popup di Visual Studio Code con la risposta seguente

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
    
    La risposta precedente è prevista perché non sono state create topologie del grafo.
    

### <a name="invoke-graphtopologyset"></a>Richiamare GraphTopologySet

Seguendo la stessa procedura illustrata per richiamare GraphTopologyList, è possibile richiamare GraphTopologySet per impostare una [topologia del grafo](media-graph-concept.md#media-graph-topologies-and-instances) usano il codice JSON seguente come payload.

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


Il payload JSON precedente genera una topologia del grafo che definisce tre parametri, due dei quali hanno valori predefiniti. La topologia ha un nodo di origine (origine RTSP), un nodo processore (processore di rilevamento movimento) e un nodo sink (sink dell'hub IoT).

In pochi secondi, nella finestra OUTPUT sarà visualizzata la risposta seguente:

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

Lo stato restituito è 201, a indicare che è stata creata una nuova topologia. Attenersi alla procedura seguente:

* Richiamare nuovamente GraphTopologySet. Si noti che il codice di stato restituito è 200. Il codice di stato 200 indica che è stata aggiornata una topologia esistente.
* Richiamare nuovamente GraphTopologySet, modificando però la stringa di descrizione. Si noti che il codice di stato nella risposta è 200 e la descrizione viene aggiornata con il nuovo valore.
* Richiamare GraphTopologyList come descritto nella sezione precedente. Si noti che ora la topologia "MotionDetection" viene visualizzata nel payload restituito.

### <a name="invoke-graphtopologyget"></a>Richiamare GraphTopologyGet

Richiamare ora GraphTopologyGet con il payload seguente

```

{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

In pochi secondi, nella finestra OUTPUT sarà visualizzata la risposta seguente:

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

Si noti quanto segue nel payload della risposta:

* Il codice di stato è 200, a indicare che l'operazione è stata eseguita correttamente.
* Il payload include i timestamp "created" e "lastModified".

### <a name="invoke-graphinstanceset"></a>Richiamare GraphInstanceSet

A questo punto creare un istanza del grafo che faccia riferimento alla topologia del grafo sopra specificata. Come descritto [in questo articolo](media-graph-concept.md#media-graph-topologies-and-instances), le istanze del grafo consentono di analizzare flussi video live da molte fotocamere con la stessa topologia del grafo.

Richiamare il metodo diretto GraphInstanceSet con il payload seguente.

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

Tenere presente quanto segue:

* Il payload precedente specifica il nome della topologia (MotionDetection) per cui è necessario creare l'istanza.
* Il payload contiene il valore del parametro per "rtspUrl", che non aveva un valore predefinito nel payload della topologia del grafo.

In pochi secondi, nella finestra OUTPUT sarà visualizzata la risposta seguente:

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

Si noti quanto segue nel payload della risposta:

* Il codice di stato è 201, a indicare che è stata creata una nuova istanza.
* Lo stato è "Inactive", a indicare che l'istanza del grafo è stata creata ma non è stata attivata. Per altre informazioni, vedere [Stati del grafo multimediale](media-graph-concept.md).

Attenersi alla procedura seguente:

* Richiamare nuovamente GraphInstanceSet con lo stesso payload. Si noti che il codice di stato restituito è ora 200.
* Richiamare nuovamente GraphInstanceSet con una descrizione diversa. Si noti che la descrizione aggiornata nel payload della risposta, a indicare che l'istanza del grafo è stata aggiornata correttamente.
* Richiamare GraphInstanceSet, modificando però il nome in "Sample-Graph-2" e osservare il payload della risposta. Si noti che viene creata una nuova istanza del grafo, quindi il codice di stato è 201.

### <a name="invoke-graphinstanceactivate"></a>Richiamare GraphInstanceActivate

A questo punto attivare l'istanza del grafo, che avvia il flusso di video live attraverso il modulo. Richiamare il metodo diretto GraphInstanceActivate con il payload seguente.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

In pochi secondi, nella finestra OUTPUT sarà visualizzata la risposta seguente:

```
[DirectMethod] Invoking Direct Method [GraphInstanceActivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Il codice di stato 200 nel payload della risposta indica che l'istanza del grafo è stata attivata.

### <a name="invoke-graphinstanceget"></a>Richiamare GraphInstanceGet

A questo punto richiamare il metodo diretto GraphInstanceGet con il payload seguente:

```
 {
     "@apiVersion" : "1.0",
     "name" : "Sample-Graph-1"
 }
 ```

In pochi secondi, nella finestra OUTPUT sarà visualizzata la risposta seguente:

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

Si noti quanto segue nel payload della risposta:

* Il codice di stato è 200, a indicare che l'operazione è stata eseguita correttamente.
* Lo stato è "Active", a indicare che l'istanza del grafo ha ora lo stato "attivo".

## <a name="observe-results"></a>Osservare i risultati

L'istanza del grafo creata e attivata in precedenza usa il nodo processore di rilevamento movimento per rilevare il movimento nel flusso video live in ingresso e invia gli eventi al nodo sink dell'hub IoT. Questi eventi vengono quindi inoltrati all'hub di IoT Edge, che ora è possibile osservare. A tale scopo, seguire questa procedura.

1. Aprire il riquadro Esplora risorse in Visual Studio Code e cercare l'hub IoT di Azure nell'angolo inferiore sinistro.
2. Espandere il nodo Devices (Dispositivi).
3. Fare clic con il pulsante destro del mouse su lva-sample-device e scegliere l'opzione "Avvia monitoraggio endpoint eventi predefinito".

![Avviare il monitoraggio degli eventi dell'hub IoT](./media/quickstarts/start-monitoring-iothub-events.png)

Nella finestra OUTPUT verranno visualizzati i messaggi seguenti:

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

Nel messaggio precedente si noti quanto segue

* Il messaggio contiene una sezione "body" e una sezione "applicationProperties". Per comprendere che cosa rappresentano queste sezioni, leggere l'articolo [Creare e leggere messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).
* "subject" in applicationProperties fa riferimento al nodo nel grafo multimediale da cui è stato generato il messaggio. In questo caso il messaggio è originato dal processore di rilevamento movimento.
* "eventType" in applicationProperties indica che si tratta di un evento di analisi.
* "eventTime" indica l'ora in cui si è verificato l'evento.
* "body" contiene dati sull'evento di analisi. In questo caso si tratta di un evento di inferenza, quindi il corpo contiene dati di tipo "timestamp" e "inferences".
* La sezione "inferences" indica che il valore "type" è "motion" e include dati aggiuntivi sull'evento "motion".

Se si lascia in esecuzione il grafo multimediale per un determinato periodo di tempo, nella finestra di output sarà visualizzato anche il messaggio seguente:

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

Nel messaggio precedente si noti quanto segue

* "subject" in applicationProperties indica che il messaggio è stato generato dal nodo di origine RTSP nel grafo multimediale.
* "eventType" in applicationProperties indica che si tratta di un evento di diagnostica.
* "body" contiene dati sull'evento di diagnostica. In questo caso l'evento è MediaSessionEstablished, quindi il corpo contiene dati su questo tipo di evento.

## <a name="invoke-additional-direct-methods-to-clean-up"></a>Richiamare metodi diretti aggiuntivi per pulire le risorse

A questo punto, richiamare i metodi diretti per disattivare ed eliminare l'istanza del grafo, seguendo questo ordine.

### <a name="invoke-graphinstancedeactivate"></a>Richiamare GraphInstanceDeactivate

Richiamare il metodo diretto GraphInstanceDeactivate con il payload seguente.

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

In pochi secondi, nella finestra OUTPUT sarà visualizzata la risposta seguente:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDeactivate] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Il codice di stato 200 indica che l'istanza del grafo è stata disattivata.

Attenersi alla procedura seguente.

* Richiamare GraphInstanceGet come indicato nelle sezioni precedenti e osservare il valore "state".

### <a name="invoke-graphinstancedelete"></a>Richiamare GraphInstanceDelete

Richiamare il metodo diretto GraphInstanceDelete con il payload seguente

```
{
    "@apiVersion" : "1.0",
    "name" : "Sample-Graph-1"
}
```

In pochi secondi, nella finestra OUTPUT sarà visualizzata la risposta seguente:

```
[DirectMethod] Invoking Direct Method [GraphInstanceDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Il codice di stato 200 nella risposta indica che l'istanza del grafo è stata eliminata.

### <a name="invoke-graphtopologydelete"></a>Richiamare GraphTopologyDelete

Richiamare il metodo diretto GraphTopologyDelete con il payload seguente:

```
{
    "@apiVersion" : "1.0",
    "name" : "MotionDetection"
}
```

In pochi secondi, nella finestra OUTPUT sarà visualizzata la risposta seguente:

```
[DirectMethod] Invoking Direct Method [GraphTopologyDelete] to [lva-sample-device/lvaEdge] ...
[DirectMethod] Response from [lva-sample-device/lvaEdge]:
{
  "status": 200,
  "payload": null
}
```

Il codice di stato 200 indica che la topologia del grafo è stata eliminata.

Attenersi alla procedura seguente.

* Richiamare GraphTopologyList e osservare che il modulo non include topologie del grafo.
* Richiamare GraphInstanceList con lo stesso payload di GraphTopologyList e osservare che non esistono istanze del grafo enumerate.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare le risorse create in questo avvio rapido.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come registrare i video usando Analisi di video live in IoT Edge
* Altre informazioni sui messaggi di diagnostica.
