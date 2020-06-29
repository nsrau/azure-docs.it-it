---
title: Analizzare un video live con un modello personalizzato - Azure
description: Questo argomento di avvio rapido illustra come applicare la visione artificiale all'analisi del feed di video live proveniente da una videocamera IP simulata.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 73b8f83b9e3c450612c742a831ac800343b3bbbc
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84816692"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-model"></a>Avvio rapido: Analizzare un video live con un modello personalizzato

Questo avvio rapido illustra come usare Analisi video live in IoT Edge per analizzare un feed video live proveniente da una videocamera IP simulata. Si vedrà come applicare un modello di visione artificiale per rilevare oggetti. Viene inviato un sottoinsieme di fotogrammi del feed video live a un servizio di inferenza. I risultati vengono inviati all'hub di IoT Edge. 

In questo argomento di avvio rapido si usano una VM di Azure come dispositivo IoT Edge e uno streaming di video live simulato. Si basa sul codice di esempio scritto in C# e si basa sull'argomento di avvio rapido [Rilevare il movimento e generare eventi](detect-motion-emit-events-quickstart.md). 

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non si ha un account, [crearne uno gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) con le estensioni seguenti:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Se l'argomento di avvio rapido [Rilevare il movimento e generare eventi](detect-motion-emit-events-quickstart.md) non è stato completato, assicurarsi di [configurare le risorse di Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Quando si installa Azure IoT Tools, è possibile che venga chiesto di installare Docker. È possibile ignorare questa richiesta.

## <a name="review-the-sample-video"></a>Esaminare il video di esempio
Quando si configurano le risorse di Azure, nella VM Linux di Azure usata come dispositivo IoT Edge viene copiato un breve video del traffico in autostrada. In questa guida il file video viene usato per simulare uno streaming live.

Aprire un'applicazione, ad esempio il [lettore multimediale VLC](https://www.videolan.org/vlc/). Premere CTRL+N, quindi incollare un collegamento al [video](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) per avviare la riproduzione. Viene visualizzato il filmato di molti veicoli in transito nel traffico di un'autostrada.

In questo argomento di avvio rapido si userà Analisi video live in IoT Edge per rilevare oggetti come veicoli e persone. Gli eventi di inferenza associati verranno pubblicati nell'hub di IoT Edge.

## <a name="overview"></a>Panoramica

![Panoramica](./media/quickstarts/overview-qs5.png)

Il diagramma mostra il flusso dei segnali in questo argomento di avvio rapido. Un [modulo Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula una videocamera IP che ospita un server RTSP (Real-Time Streaming Protocol). Un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i fotogrammi video al nodo del [processore di filtro della frequenza dei fotogrammi](media-graph-concept.md#frame-rate-filter-processor). Questo processore limita la frequenza dei fotogrammi dello streaming video che raggiunge il nodo del [processore di estensioni HTTP](media-graph-concept.md#http-extension-processor). 

Il nodo di estensioni HTTP svolge il ruolo di proxy. Converte i fotogrammi video nel tipo di immagine specificato. Quindi inoltra l'immagine su REST a un altro modulo Edge che esegue un modello di intelligenza artificiale dietro un endpoint HTTP. In questo esempio, il modulo Edge viene creato usando il modello [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), che è in grado di rilevare molti tipi di oggetti. Il nodo del processore di estensioni HTTP raccoglie i risultati del rilevamento e pubblica gli eventi nel nodo [sink dell'hub IoT](media-graph-concept.md#iot-hub-message-sink). Il nodo invia quindi gli eventi all'[hub di IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Contenuto dell'avvio rapido:

1. Creare e distribuire il grafico multimediale.
1. Interpretare i risultati.
1. Pulire le risorse.



## <a name="create-and-deploy-the-media-graph"></a>Creare e distribuire il grafico multimediale
    
### <a name="examine-and-edit-the-sample-files"></a>Esaminare e modificare i file di esempio

Come parte dei prerequisiti, il codice di esempio è stato scaricato in una cartella. Seguire questa procedura per esaminare e modificare i file di esempio.

1. In Visual Studio Code passare a *src/edge*. Si noteranno il file *.env* e alcuni file modello di distribuzione.

    Il modello di distribuzione fa riferimento al manifesto della distribuzione per il dispositivo Edge. Include alcuni valori segnaposto. Il file *.env* include i valori per queste variabili.

1. Passare alla cartella *src/cloud-to-device-console-app*. Sono inclusi il file *appsettings.json* e alcuni altri file:

    * ***c2d-console-app.csproj***: il file di progetto per Visual Studio Code.
    * ***operations.json***: un elenco di operazioni che il programma dovrà eseguire.
    * ***Program.cs***: il codice del programma di esempio. Questo codice:

        * Carica le impostazioni dell'app.
        * Richiama i metodi diretti esposti dal modulo Analisi video live in IoT Edge. È possibile usare il modulo per analizzare i flussi video live richiamando i [metodi diretti](direct-methods.md).
        * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra **TERMINAL** e gli eventi generati dal modulo nella finestra **OUTPUT**.
        * Richiama i metodi diretti per pulire le risorse.


1. Modificare il file *operations.json*:
    * Cambiare il collegamento alla topologia del grafo:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`

    * In `GraphInstanceSet` modificare il nome della topologia del grafo in modo che corrisponda al valore nel collegamento precedente:

      `"topologyName" : "InferencingWithHttpExtension"`

    * In `GraphTopologyDelete` modificare il nome:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione di IoT Edge

1. Fare clic con il pulsante destro del mouse sul file *src/edge/ deployment.yolov3.template.json* e scegliere **Generate IoT Edge Deployment Manifest** (Genera manifesto della distribuzione di IoT Edge).

    ![Generate IoT Edge Deployment Manifest (Genera manifesto della distribuzione di IoT Edge)](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    Il file manifesto *deployment.yolov3.amd64.json* viene creato nella cartella *src/edge/config*.

1. Se è stato completato l'argomento di avvio rapido [Rilevare il movimento e generare eventi](detect-motion-emit-events-quickstart.md), ignorare questo passaggio. 

    In caso contrario, accanto al riquadro **HUB IOT DI AZURE** nell'angolo in basso a sinistra selezionare l'icona **Altre azioni** e quindi selezionare **Set IoT Hub Connection String** (Imposta la stringa di connessione dell'hub IoT). È possibile copiare la stringa dal file *appsettings.json*. In alternativa, per assicurarsi di aver configurato l'hub IoT appropriato in Visual Studio Code, usare il comando [Selezionare un hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Impostare la stringa di connessione dell'hub IoT](./media/quickstarts/set-iotconnection-string.png)

1. Fare clic con il pulsante destro del mouse sul file *src/edge/config/ deployment.yolov3.amd64.json* e scegliere **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo). 

    ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/quickstarts/create-deployment-single-device.png)

1. Quando viene chiesto di selezionare un dispositivo hub IoT, selezionare **lva-sample-device**.
1. Dopo circa 30 secondi, nell'angolo in basso a sinistra della finestra aggiornare l'hub IoT di Azure. Il dispositivo Edge mostra ora i moduli distribuiti seguenti:

    * Il modulo Analisi video live, denominato **lvaEdge**
    * Il modulo **rtspsim**, che simula un server RTSP e funge da origine di un feed video live
    * Il modulo **yolov3**, ovvero il modello YOLOv3 di rilevamento oggetti che applica la visione artificiale alle immagini e restituisce più classi di tipi di oggetto
 
      ![Moduli distribuiti nel dispositivo Edge](./media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Preparare il monitoraggio degli eventi

Fare clic con il pulsante destro del mouse sul dispositivo di Analisi video live e scegliere **Avvia monitoraggio endpoint eventi predefinito**. Questo passaggio è necessario per monitorare gli eventi dell'hub IoT nella finestra **OUTPUT** di Visual Studio Code. 

![Avviare il monitoraggio](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Eseguire il programma di esempio

1. Per avviare la sessione di debug, premere F5. Verranno visualizzati i messaggi stampati nella finestra **TERMINALE**.
1. Il codice *operations.json* verrà avviato con le chiamate ai metodi diretti `GraphTopologyList` e `GraphInstanceList`. Se dopo aver completato gli argomenti di avvio rapido precedenti sono state pulite le risorse, questa procedura restituirà elenchi vuoti e quindi verrà sospesa. Per continuare, premere INVIO.

   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "1.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

    La finestra **TERMINALE** mostra il set successivo di chiamate ai metodi diretti:

     * Una chiamata a `GraphTopologySet` che usa `topologyUrl` precedente
     * Una chiamata a `GraphInstanceSet` che usa il corpo seguente:

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithHttpExtension",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               }
             ]
           }
         }
         ```

     * Una chiamata a `GraphInstanceActivate` che avvia l'istanza del grafo e il flusso di video
     * Una seconda chiamata a `GraphInstanceList` che mostra che l'istanza del grafo è in esecuzione
1. L'output della finestra **TERMINALE** viene sospeso in corrispondenza di una richiesta `Press Enter to continue`. Non premere ancora INVIO. Scorrere in alto per visualizzare i payload della risposta JSON per i metodi diretti richiamati.
1. Passare alla finestra **OUTPUT** in Visual Studio Code. Vengono visualizzati i messaggi inviati dal modulo Analisi video live in IoT Edge all'hub IoT. La sezione seguente di questo argomento di avvio rapido descrive questi messaggi.
1. Il grafo multimediale continua a essere eseguito e a stampare i risultati. Il simulatore RTSP continua a eseguire il video di origine in un ciclo. Quindi, per arrestare il grafo multimediale, tornare nella finestra **TERMINALE** e premere INVIO. 

    La serie successiva di chiamate pulisce le risorse:
      * Una chiamata a `GraphInstanceDeactivate` disattiva l'istanza del grafo.
      * Una chiamata a `GraphInstanceDelete` elimina l'istanza.
      * Una chiamata a `GraphTopologyDelete` elimina la topologia.
      * Una chiamata finale a `GraphTopologyList` mostra che l'elenco è vuoto.

## <a name="interpret-results"></a>Interpretare i risultati

Quando si esegue il grafo multimediale, i risultati del nodo del processore di estensioni HTTP vengono inviati all'hub IoT tramite il nodo sink dell'hub IoT. I messaggi visualizzati nella finestra **OUTPUT** contengono una sezione `body` e una sezione `applicationProperties`. Per altre informazioni, vedere [Creare e leggere messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

Nei messaggi seguenti il modulo Analisi video live definisce le proprietà dell'applicazione e il contenuto del corpo. 

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando viene creata un'istanza del grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore rtspsim-live55. Se la connessione riesce, viene stampato l'evento seguente. Il tipo di evento è `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
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

In questo messaggio si notino i dettagli seguenti:

* Il messaggio è un evento di diagnostica. `MediaSessionEstablished` indica che il nodo di origine RTSP (subject) ha stabilito una connessione con il simulatore RTSP e ha iniziato a ricevere un feed live simulato.
* In `applicationProperties` `subject` indica che il messaggio è stato generato dal nodo di origine RTSP nel grafo multimediale.
* In `applicationProperties` `eventType` indica che questo evento è un evento di diagnostica.
* `eventTime` indica la data e l'ora in cui si è verificato l'evento.
* `body` contiene dati sull'evento di diagnostica. In questo caso i dati sono costituiti dai dettagli di [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Evento di inferenza

Il nodo del processore di estensioni HTTP riceve i risultati di inferenza dal modulo yolov3. Genera quindi i risultati tramite il nodo sink dell'hub IoT come eventi di inferenza. 

In questi eventi il tipo è impostato su `entity` per indicare che si tratta di un'entità, ad esempio un'automobile o un camion. Il valore di `eventTime` corrisponde all'ora UTC in cui l'oggetto è stato rilevato. 

Nell'esempio seguente vengono rilevate due auto nello stesso fotogramma video, con livelli variabili di attendibilità.

```
[IoTHubMonitor] [11:37:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "entity": {
          "box": {
            "h": 0.0344108157687717,
            "l": 0.5756940841674805,
            "t": 0.5929375966389974,
            "w": 0.04484643936157227
          },
          "tag": {
            "confidence": 0.8714089393615723,
            "value": "car"
          }
        },
        "type": "entity"
      },
      {
        "entity": {
          "box": {
            "h": 0.03960910373263889,
            "l": 0.2750667095184326,
            "t": 0.6102327558729383,
            "w": 0.031027007102966308
          },
          "tag": {
            "confidence": 0.7042660713195801,
            "value": "car"
          }
        },
        "type": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-04-23T06:37:16.097Z"
  }
}
```

Nei messaggi si notino i dettagli seguenti:

* In `applicationProperties` `subject` fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. 
* In `applicationProperties` `eventType` indica che questo evento è un evento di analisi.
* Il valore di `eventTime` indica la data e l'ora in cui si è verificato l'evento.
* La sezione `body` contiene dati sull'evento di analisi. In questo caso si tratta di un evento di inferenza e quindi il corpo contiene dati di tipo `inferences`.
* La sezione `inferences` indica che `type` è `entity`. Questa sezione include dati aggiuntivi sull'entità.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende provare altri argomenti di avvio rapido, mantenere le risorse create. In caso contrario, passare al portale di Azure, passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stato eseguito l'argomento di avvio rapido ed eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

Esaminare altri problemi per utenti avanzati:

* Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP invece del simulatore RTSP. È possibile cercare videocamere IP che supportano RTSP nella pagina dei prodotti [conformi a ONVIF](https://www.onvif.org/conformant-products/). Cercare dispositivi conformi ai profili G, S o T.
* Usare un dispositivo Linux AMD64 o x64 invece di una VM Linux di Azure. Il dispositivo deve trovarsi nella stessa rete della videocamera IP. È possibile seguire le istruzioni riportate in [Installare il runtime Azure IoT Edge in Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux). Quindi seguire il dispositivo con l'hub IoT di Azure seguendo le istruzioni riportate in [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).

