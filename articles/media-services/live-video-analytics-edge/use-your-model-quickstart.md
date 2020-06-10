---
title: Analizzare video live con un modello personalizzato - Azure
description: In questo avvio rapido si applicherà la visione artificiale all'analisi del feed video live da una fotocamera IP (simulata).
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 0b502fb4bcfa3a11890167c5ef297463a3c51cdc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261489"
---
# <a name="quickstart-analyze-live-video-with-your-own-model"></a>Avvio rapido: Analizzare video live con un modello personalizzato

Questo avvio rapido illustra come usare l'analisi di video live in IoT Edge per analizzare il feed video live da una fotocamera IP (simulata) applicando un modello di visione artificiale per rilevare gli oggetti. Un subset dei frame nel feed video live viene inviato a un servizio di inferenza, i cui risultati vengono inviati all'hub di IoT Edge. Usa una macchina virtuale di Azure come dispositivo IoT Edge e un flusso video live simulato. Questo articolo è basato su codice di esempio scritto in C#.

Questo articolo si basa su [questo](detect-motion-emit-events-quickstart.md) avvio rapido. 

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) nel computer in uso con le estensioni seguenti:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) installato nel sistema
* Se in precedenza non è stato completato [questo](detect-motion-emit-events-quickstart.md) avvio rapido, eseguire la procedura seguente:
     * [Configurare le risorse di Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)

> [!TIP]
> Quando si installa Azure IoT Tools, può venire richiesto di installare Docker. È possibile ignorare la richiesta.

## <a name="review-the-sample-video"></a>Esaminare il video di esempio
Nell'ambito della procedura precedente per la configurazione delle risorse di Azure, un breve video del traffico su un'autostrada verrà copiato nella macchina virtuale Linux in Azure usata come dispositivo IoT Edge. Questo file video verrà usato per simulare un flusso di streaming live per questa esercitazione.

È possibile usare un'applicazione come [VLC Player](https://www.videolan.org/vlc/), avviarla, premere CTRL + N e incollare [questo](https://lvamedia.blob.core.windows.net/public/camera-300s.mkv) collegamento al video per avviare la riproduzione. Si noterà che il filmato riguarda il traffico su un'autostrada, con molti veicoli in movimento.

Al termine della procedura descritta di seguito, si sarà usata l'analisi di video live in IoT Edge per rilevare oggetti, ad esempio veicoli, persone e così via e pubblicare eventi di inferenza associati nell'hub di IoT Edge.

## <a name="overview"></a>Panoramica

![Panoramica](./media/quickstarts/overview-qs5.png)

Il diagramma qui sopra illustra il flusso dei segnali in questo avvio rapido. Un modulo Edge (descritto in dettaglio [qui](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simula una fotocamera IP che ospita un server RTSP. Un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i fotogrammi video al nodo del [processore filtro della frequenza dei fotogrammi](media-graph-concept.md#frame-rate-filter-processor). Questo processore limita la frequenza dei fotogrammi del flusso video che raggiunge il nodo del [processore dell'estensione HTTP](media-graph-concept.md#http-extension-processor). 

Il nodo dell'estensione HTTP svolge il ruolo di proxy, convertendo i fotogrammi video nel tipo di immagine specificato e inoltrando l'immagine tramite REST a un altro modulo Edge che esegue un modello di intelligenza artificiale dietro un endpoint HTTP. In questo esempio, il modulo Edge viene compilato tramite il modello [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx), che è in grado di rilevare molti tipi di oggetti. Il nodo del processore dell'estensione HTTP raccoglie i risultati del rilevamento e pubblica gli eventi nel nodo del [sink dell'hub IoT](media-graph-concept.md#iot-hub-message-sink ), che quindi invia tali eventi all'[hub di IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

Contenuto dell'avvio rapido:

1. Creare e distribuire il grafico multimediale.
1. Interpretare i risultati.
1. Pulire le risorse.



## <a name="create-and-deploy-the-media-graph"></a>Creare e distribuire il grafico multimediale
    
### <a name="examine-and-edit-the-sample-files"></a>Esaminare e modificare i file di esempio

Nell'ambito dei prerequisiti, dovrebbe essere stato scaricato il codice di esempio in una cartella. Avviare Visual Studio Code e aprire la cartella.

1. In Visual Studio Code passare a "src/edge". Verranno visualizzati il file con estensione env creato e alcuni file modello di distribuzione.

    * Il modello di distribuzione fa riferimento al manifesto della distribuzione per il dispositivo perimetrale con alcuni valori segnaposto. Il file con estensione env include i valori per queste variabili.
1. Passare quindi alla cartella "src/cloud-to-device-console-app". Verranno visualizzati il file appsettings.json creato e altri file:

    * c2d-console-app.csproj: file di progetto per Visual Studio Code.
    * operations.json: file che elenca le diverse operazioni che il programma deve eseguire.
    * Program.cs: codice del programma di esempio, che esegue le operazioni seguenti:

        * Carica le impostazioni dell'app.
        *  Chiama i metodi diretti esposti dall'analisi di video live nel modulo IoT Edge. È possibile usare il modulo per analizzare i flussi video live chiamando i [metodi diretti](direct-methods.md) 
        * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra del terminale e gli eventi generati dal modulo nella finestra di output
        * Chiama metodi diretti per pulire le risorse   


1. Eseguire le modifiche seguenti nel file operations.json
    * Modificare il collegamento alla topologia del grafo: `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
    * In GraphInstanceSet modificare il nome della topologia del grafo in modo che corrisponda al valore nel collegamento precedente `"topologyName" : "InferencingWithHttpExtension"`
    * In GraphTopologyDelete modificare il nome `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione di IoT Edge

1. Fare clic con il pulsante destro del mouse sul file "src/edge/ deployment.yolov3.template.json" e fare clic su "Generate IoT Edge Deployment Manifest" (Genera manifesto della distribuzione di IoT Edge).

    ![Generate IoT Edge Deployment Manifest (Genera manifesto della distribuzione di IoT Edge)](./media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  
1. Questa operazione crea un file manifesto nella cartella src/edge/config denominato "deployment.yolov3.amd64.json".
1. Se in precedenza è stato completato l'[avvio rapido](detect-motion-emit-events-quickstart.md), ignorare questo passaggio. In caso contrario, impostare la stringa di connessione dell'hub IoT facendo clic sull'icona "Altre azioni" accanto al riquadro dell'hub IoT di Azure nell'angolo inferiore sinistro. È possibile copiarle la stringa dal file appsettings.json. Ecco un altro approccio consigliato per assicurarsi di avere configurato l'hub IoT appropriato in Visual Studio Code tramite il [comando Selezionare un hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Stringa di connessione dell'hub IoT](./media/quickstarts/set-iotconnection-string.png)
1. Quindi fare clic con il pulsante destro del mouse su "src/edge/config/ deployment.yolov3.amd64.json" e fare clic su "Create Deployment for Single Device" (Crea la distribuzione per un unico dispositivo). 

    ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/quickstarts/create-deployment-single-device.png)
1. Verrà quindi chiesto di selezionare un dispositivo hub IoT. Selezionare lva-sample-device dall'elenco a discesa.
1. Tra circa 30 secondi, aggiornare l'hub IoT di Azure nella sezione inferiore sinistra. Verrà visualizzato il dispositivo perimetrale con i moduli seguenti distribuiti:

    1. Il modulo di analisi di video live, denominato "lvaEdge".
    1. Un modulo denominato "rtspsim" che simula un server RTSP e funge da origine di un feed video live.
    1. Un modulo denominato "yolov3" che, come suggerisce il nome, è il modello di rilevamento degli oggetti YOLOv3 che applica la visione artificiale alle immagini e restituisce più classi di tipi di oggetto.
 
        ![Modello di rilevamento degli oggetti YOLOv3](./media/quickstarts/yolov3.png)

### <a name="prepare-for-monitoring-events"></a>Operazioni preliminari al monitoraggio degli eventi

Fare clic con il pulsante destro del mouse sul dispositivo di analisi di video live e fare clic su "Avvia monitoraggio endpoint eventi predefinito". Questo passaggio è necessario per monitorare gli eventi dell'hub IoT e visualizzarli nella finestra di output di Visual Studio Code. 

![Avviare il monitoraggio](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Eseguire il programma di esempio

1. Avviare una sessione di debug (premere F5). Verranno visualizzati alcuni messaggi stampati nella finestra del terminale.
1. Il file operations.json inizia con le chiamate ai metodi diretti GraphTopologyList e GraphInstanceList. Se sono state pulite le risorse dopo gli avvii rapidi precedenti, verranno restituiti elenchi vuoti e verrà sospesa l'esecuzione per consentire di premere Invio
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

1. Quando si preme "Invio" nella finestra del terminale, viene eseguito il set successivo di chiamate ai metodi diretti
     * Una chiamata a GraphTopologySet con il valore topologyUrl precedente
     * Una chiamata a GraphInstanceSet con il corpo seguente
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
     * Una chiamata a GraphInstanceActivate per avviare l'istanza del grafo e avviare il flusso del video
     * Una seconda chiamata a GraphInstanceList per indicare che l'istanza del grafo è effettivamente in stato di esecuzione
1. L'output nella finestra del terminale viene ora sospeso con la visualizzazione del prompt 'Premere Invio per continuare'. Non premere "Invio". È possibile scorrere verso l'alto per visualizzare i payload di risposta JSON per i metodi diretti chiamati
1. Se ora si passa alla finestra di output in Visual Studio Code, verranno visualizzati i messaggi inviati all'hub IoT dal modulo di analisi di video live nel modulo IoT Edge.
     * Questi messaggi sono trattati nella sezione seguente
1. L'esecuzione del grafo multimediale continuerà e verranno stampati i risultati. Il simulatore RTSP continuerà a eseguire il ciclo del video di origine. Per arrestare il grafo multimediale, tornare alla finestra del terminale e premere "Invio". La prossima serie di chiamate ha lo scopo di pulire le risorse:
     * Una chiamata a GraphInstanceDeactivate per disattivare l'istanza del grafo
     * Una chiamata a GraphInstanceDelete per eliminare l'istanza
     * Una chiamata a GraphTopologyDelete per eliminare la topologia
     * Una chiamata finale a GraphTopologyList per indicare che l'elenco è ora vuoto

## <a name="interpret-results"></a>Interpretare i risultati

Quando si esegue il grafo multimediale, i risultati del nodo del processore dell'estensione HTTP vengono inviati all'hub IoT tramite il nodo del sink dell'hub IoT. I messaggi visualizzati nella finestra di output di Visual Studio Code contengono una sezione "body" e una sezione "applicationProperties". Per comprendere cosa rappresentano queste sezioni, leggere [questo](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) articolo.

Nei messaggi seguenti le proprietà dell'applicazione e il contenuto del corpo sono definiti dal modulo di analisi di video live. 



### <a name="mediasession-established-event"></a>Evento MediaSessionEstablished

Quando viene creata un'istanza del grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore rtspsim-live55. In caso di esito positivo, viene stampato questo evento. Il tipo di evento è Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

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

Nel messaggio precedente si noti quanto segue:
* Il messaggio è l'evento di diagnostica MediaSessionEstablished, che indica che il nodo di origine RTSP (oggetto) è riuscito a stabilire una connessione con il simulatore RTSP e a iniziare a ricevere un feed live (simulato).
* "subject" in applicationProperties indica che il messaggio è stato generato dal nodo di origine RTSP nel grafo multimediale.
* "eventType" in applicationProperties indica che si tratta di un evento di diagnostica.
* "eventTime" indica l'ora in cui si è verificato l'evento.
* "body" contiene i dati sull'evento di diagnostica, che in questo caso sono costituiti dai dettagli [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Evento di inferenza

Il nodo del processore dell'estensione HTTP riceve i risultati dell'inferenza dal modulo yolov3 e li genera tramite il nodo del sink dell'hub IoT come eventi di inferenza. In questi eventi il tipo è impostato su "entity" per indicare che si tratta di un'entità, ad esempio un'auto o un camion, mentre eventTime indica a quale ora (UTC) l'oggetto è stato rilevato. Di seguito è riportato un esempio in cui sono state rilevate due auto con livelli di attendibilità variabili nello stesso fotogramma video.

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

Nei messaggi precedenti si noti quanto segue:

* "subject" in applicationProperties fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. 
* "eventType" in applicationProperties indica che si tratta di un evento di analisi.
* "eventTime" indica l'ora in cui si è verificato l'evento.
* "body" contiene dati sull'evento di analisi. In questo caso si tratta di un evento di inferenza e quindi il corpo contiene dati di tipo "inferences".
* La sezione "inferences" indica che il valore "type" è "entity" e include dati aggiuntivi su "entity".

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di provare gli altri avvii rapidi, è necessario mantenere le risorse create. In caso contrario, passare al portale di Azure, passare ai gruppi di risorse personali, selezionare il gruppo di risorse in cui è stato eseguito l'avvio rapido ed eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

Esaminare altri problemi per utenti avanzati:

* Usare una [fotocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP anziché il simulatore RTSP. È possibile cercare fotocamere IP con supporto RTSP nella pagina dei prodotti [conformi ONVIF](https://www.onvif.org/conformant-products/) cercando i dispositivi conformi ai profili G, S o T.
* Usare un dispositivo Linux AMD64 o X64 (anziché una macchina virtuale Linux di Azure). Il dispositivo deve trovarsi nella stessa rete della fotocamera IP. È possibile seguire le istruzioni riportate in [Installare il runtime di Azure IoT Edge in Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) e quindi seguire le istruzioni nell'avvio rapido [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) per registrare il dispositivo con l'hub IoT di Azure.

