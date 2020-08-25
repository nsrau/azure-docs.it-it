---
title: Analizzare video live con l'estensione per intelligenza artificiale OpenVINO™ Model Server di Intel
description: In questa esercitazione si userà un server di modelli di intelligenza artificiale offerto da Intel per analizzare il feed video live proveniente da una videocamera IP (simulata).
ms.topic: tutorial
ms.date: 07/24/2020
titleSuffix: Azure
ms.openlocfilehash: 2268300f711a939ed808d1f39bbde1653e8832c8
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212353"
---
# <a name="tutorial-analyze-live-video-by-using-openvino-model-server--ai-extension-from-intel"></a>Esercitazione: Analizzare video live con l'estensione per intelligenza artificiale OpenVINO™ Model Server di Intel 

Questa esercitazione illustra come usare l'estensione per intelligenza artificiale OpenVINO™ Model Server di Intel per analizzare un feed video live proveniente da una videocamera IP (simulata). Si osserverà come questo server di inferenza consente di accedere a modelli per il rilevamento di oggetti (una persona, un veicolo o una bicicletta) e a un modello per la classificazione dei veicoli. Un subset dei fotogrammi del feed video live viene inviato al server di inferenza e i risultati vengono inviati all'hub di IoT Edge. 

Questa esercitazione usa una VM di Azure come dispositivo IoT Edge e un flusso video live simulato. Si basa sul codice di esempio scritto in C# e si basa sull'argomento di avvio rapido [Rilevare il movimento e generare eventi](detect-motion-emit-events-quickstart.md). 

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non si ha un account, [crearne uno gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) con le estensioni seguenti:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Se non si è completata la guida di avvio rapido [Rilevare il movimento e generare eventi](detect-motion-emit-events-quickstart.md), assicurarsi di seguire la procedura per [configurare le risorse di Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources).

> [!TIP]
> Quando si installa Azure IoT Tools, è possibile che venga chiesto di installare Docker. È possibile ignorare questa richiesta.

## <a name="review-the-sample-video"></a>Esaminare il video di esempio
Quando si configurano le risorse di Azure, nella VM Linux di Azure usata come dispositivo IoT Edge viene copiato un breve video di un parcheggio. In questa guida il file video viene usato per simulare uno streaming live.

Aprire un'applicazione, ad esempio il [lettore multimediale VLC](https://www.videolan.org/vlc/). Premere CTRL+N, quindi incollare un collegamento al [video](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) per avviare la riproduzione. Verrà visualizzato il filmato dei veicoli in un parcheggio, con la maggior parte di essi parcheggiati e uno in movimento.

In questa guida di avvio rapido si userà Analisi video live in IoT Edge insieme all'estensione per intelligenza artificiale OpenVINO™ Model Server di Intel per rilevare gli oggetti, ad esempio i veicoli, o classificarli. Si pubblicheranno quindi gli eventi di inferenza risultanti nell'hub di IoT Edge.

## <a name="overview"></a>Panoramica

![Panoramica](./media/use-intel-openvino-tutorial/topology.png)

Il diagramma mostra il flusso dei segnali in questo argomento di avvio rapido. Un [modulo Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula una videocamera IP che ospita un server RTSP (Real-Time Streaming Protocol). Un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i fotogrammi video al nodo del [processore di filtro della frequenza dei fotogrammi](media-graph-concept.md#frame-rate-filter-processor). Questo processore limita la frequenza dei fotogrammi dello streaming video che raggiunge il nodo del [processore di estensioni HTTP](media-graph-concept.md#http-extension-processor). 

Il nodo di estensioni HTTP svolge il ruolo di proxy. Converte i fotogrammi video nel tipo di immagine specificato. Inoltra quindi l'immagine tramite REST a un altro modulo Edge che esegue modelli di intelligenza artificiale dietro un endpoint HTTP. In questo esempio, il modulo Edge è l'estensione per intelligenza artificiale OpenVINO™ Model Server di Intel. Il nodo del processore di estensioni HTTP raccoglie i risultati del rilevamento e pubblica gli eventi nel nodo [sink dell'hub IoT](media-graph-concept.md#iot-hub-message-sink). Il nodo invia quindi gli eventi all'[hub di IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

In questa esercitazione si apprenderà come:

1. Creare e distribuire il grafo multimediale, modificandolo. 
1. Interpretare i risultati.
1. Pulire le risorse.

## <a name="about-openvino-model-server--ai-extension-from-intel"></a>Informazioni sull'estensione per intelligenza artificiale OpenVINO™ Model Server di Intel
La distribuzione Intel® del [toolkit OpenVINO™](https://software.intel.com/content/www/us/en/develop/tools/openvino-toolkit.html) (Open Visual Inference and Neural Network Optimization) è un kit di software gratuito che consente a sviluppatori e data scientist di velocizzare i carichi di lavoro di visione artificiale, semplificare le distribuzioni e l'inferenza di Deep Learning e abilitare un'esecuzione facile ed eterogenea su piattaforme Intel® dalla rete perimetrale al cloud. Include Intel® Deep Learning Deployment Toolkit, con ottimizzatore dei modelli e motore di inferenza, e il repository [Open Model Zoo](https://github.com/openvinotoolkit/open_model_zoo), contenente oltre 40 modelli ottimizzati già sottoposti a training.

Per creare soluzioni di analisi video live complesse e a prestazioni elevate, il modulo Analisi video live in IoT Edge dovrà essere associato a un potente motore di inferenza che possa sfruttare la scalabilità a livello perimetrale. In questa esercitazione, le richieste di inferenza vengono inviate all'[estensione per intelligenza artificiale OpenVINO™ Model Server di Intel](https://aka.ms/lva-intel-ovms), un modulo Edge progettato per funzionare con Analisi video live in IoT Edge. Questo modulo server di inferenza contiene OpenVINO™ Model Server (OVMS), un server di inferenza basato sul toolkit OpenVINO™, altamente ottimizzato per i carichi di lavoro di visione artificiale e sviluppato per le architetture Intel. A OVMS è stata aggiunta un'estensione per facilitare lo scambio dei fotogrammi video e dei risultati dell'inferenza tra il server di inferenza e il modulo Analisi video live in IoT Edge e consentire così l'esecuzione di qualsiasi modello supportato da OpenVINO. È possibile personalizzare il modulo server di inferenza modificando il codice [qui](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_wrapper). È inoltre possibile scegliere tra un'ampia gamma di meccanismi di accelerazione offerti dall'hardware Intel, che includono CPU (Atom, Core e Xeon), FPGA e VPU.

Nella versione iniziale di questo server di inferenza è possibile accedere ai [modelli](https://github.com/openvinotoolkit/model_server/tree/master/extras/ams_models) seguenti:

- Rilevamento di veicoli (URL per inferenza: http://{nome-modulo}:4000/vehicleDetection)
- Rilevamento di persone/veicoli/biciclette (URL per inferenza: http://{nome-modulo}:4000/personVehicleBikeDetection)
- Classificazione di veicoli (URL per inferenza: http://{nome-modulo}: 4000/vehicleClassification)
- Rilevamento viso (URL per inferenza: http://{nome-modulo}:4000/faceDetection)

> [!NOTE]
> Scaricando e usando il modulo Edge, ossia l'estensione per intelligenza artificiale OpenVINO™ Model Server di Intel e il software incluso, si accettano le condizioni del [contratto di licenza](https://www.intel.com/content/www/us/en/legal/terms-of-use.html).
> Intel si impegna a rispettare i diritti umani ed evitare di essere complice nelle violazioni di tali diritti. Vedere i [principi dei diritti umani su scala globale di Intel](https://www.intel.com/content/www/us/en/policy/policy-human-rights.html). I prodotti e il software di Intel sono destinati esclusivamente all'uso in applicazioni che non causano violazioni dei diritti umani riconosciuti a livello internazionale né vi contribuiscono.

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

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json"`

    * In `GraphInstanceSet` modificare il nome della topologia del grafo in modo che corrisponda al valore nel collegamento precedente:

      `"topologyName" : "InferencingWithOpenVINO"`

    * In `GraphTopologyDelete` modificare il nome:

      `"name": "InferencingWithOpenVINO"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione di IoT Edge

1. Fare clic con il pulsante destro del mouse sul file *src/edge/deployment.openvino.template.json* e quindi scegliere **Generate IoT Edge Deployment Manifest** (Genera manifesto della distribuzione di IoT Edge).

    ![Generate IoT Edge Deployment Manifest (Genera manifesto della distribuzione di IoT Edge)](./media/use-intel-openvino-tutorial/generate-deployment-manifest.png)  

    Il file manifesto *deployment.yolov3.amd64.json* viene creato nella cartella *src/edge/config*.

1. Se è stato completato l'argomento di avvio rapido [Rilevare il movimento e generare eventi](detect-motion-emit-events-quickstart.md), ignorare questo passaggio. 

    In caso contrario, accanto al riquadro **HUB IOT DI AZURE** nell'angolo in basso a sinistra selezionare l'icona **Altre azioni** e quindi selezionare **Set IoT Hub Connection String** (Imposta la stringa di connessione dell'hub IoT). È possibile copiare la stringa dal file *appsettings.json*. In alternativa, per assicurarsi di aver configurato l'hub IoT appropriato in Visual Studio Code, usare il comando [Selezionare un hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![Impostare la stringa di connessione dell'hub IoT](./media/quickstarts/set-iotconnection-string.png)

1. Fare clic con il pulsante destro del mouse sul file *src/edge/config/deployment.openvino.amd64.json* e scegliere **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo). 

    ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/use-intel-openvino-tutorial/deploy-manifest.png)

1. Quando viene chiesto di selezionare un dispositivo hub IoT, selezionare **lva-sample-device**.
1. Dopo circa 30 secondi, nell'angolo in basso a sinistra della finestra aggiornare l'hub IoT di Azure. Il dispositivo Edge mostra ora i moduli distribuiti seguenti:

    * Il modulo Analisi video live, denominato **lvaEdge**
    * Il modulo **rtspsim**, che simula un server RTSP e funge da origine di un feed video live
    * Il modulo **openvino** dell'estensione per intelligenza artificiale OpenVINO™ Model Server di Intel 

### <a name="prepare-to-monitor-events"></a>Preparare il monitoraggio degli eventi

Fare clic con il pulsante destro del mouse sul dispositivo di Analisi video live e scegliere **Avvia monitoraggio endpoint eventi predefinito**. Questo passaggio è necessario per monitorare gli eventi dell'hub IoT nella finestra **OUTPUT** di Visual Studio Code. 

![Avviare il monitoraggio](./media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program-to-detect-vehicles"></a>Eseguire il programma di esempio per rilevare i veicoli
Se si apre la [topologia del grafo](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) per questa esercitazione in un browser, si noterà che il valore di `inferencingUrl` è stato impostato su `http://openvino:4000/vehicleDetection`, di conseguenza il server di inferenza restituirà i risultati dopo aver rilevato i veicoli eventualmente presenti nel video live.

1. Per avviare la sessione di debug, premere F5. Verranno visualizzati i messaggi stampati nella finestra **TERMINALE**.
1. Il codice *operations.json* verrà avviato con le chiamate ai metodi diretti `GraphTopologyList` e `GraphInstanceList`. Se dopo aver completato gli argomenti di avvio rapido precedenti sono state pulite le risorse, questa procedura restituirà elenchi vuoti e quindi verrà sospesa. Per continuare, premere INVIO.

    La finestra **TERMINALE** mostra il set successivo di chiamate ai metodi diretti:

     * Una chiamata a `GraphTopologySet` che usa `topologyUrl` precedente
     * Una chiamata a `GraphInstanceSet` che usa il corpo seguente:

         ```
         {
           "@apiVersion": "1.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithOpenVINO",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/lots_015.mkv"
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

Quando si esegue il grafo multimediale, i risultati del nodo del processore di estensioni HTTP vengono inviati all'hub IoT tramite il nodo sink dell'hub IoT. I messaggi visualizzati nella finestra **OUTPUT** contengono una sezione `body` e una sezione `applicationProperties`. Per altre informazioni, vedere [Creare e leggere messaggi dell'hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nei messaggi seguenti il modulo Analisi video live definisce le proprietà dell'applicazione e il contenuto del corpo. 

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando viene creata un'istanza del grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore rtspsim-live555. Se la connessione riesce, viene stampato l'evento seguente. Il tipo di evento è `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-07-24T16:42:18.1280000Z"
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

Il nodo del processore di estensioni HTTP riceve i risultati dell'inferenza dal modulo dell'estensione per intelligenza artificiale OpenVINO™ Model Server. Genera quindi i risultati tramite il nodo sink dell'hub IoT come eventi di inferenza. 

In questi eventi il tipo è impostato su `entity` per indicare che si tratta di un'entità, ad esempio un'automobile o un camion. Il valore di `eventTime` corrisponde all'ora UTC in cui l'oggetto è stato rilevato. 

Nell'esempio seguente sono stati rilevati due veicoli, con valori di attendibilità superiori a 0,9.

```
[IoTHubMonitor] [9:43:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.9951713681221008
          },
          "box": {
            "l": 0.042635321617126465,
            "t": 0.4004564881324768,
            "w": 0.10961548984050751,
            "h": 0.07942074537277222
          }
        }
      },
      {
        "type": "entity",
        "subtype": "vehicleDetection",
        "entity": {
          "tag": {
            "value": "vehicle",
            "confidence": 0.928486168384552
          },
          "box": {
            "l": 0.2506900727748871,
            "t": 0.07512682676315308,
            "w": 0.05470699071884155,
            "h": 0.07408371567726135
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:43:18.1280000Z"
  }
}
```

Nei messaggi si notino i dettagli seguenti:

* In `applicationProperties` `subject` fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. 
* In `applicationProperties` `eventType` indica che questo evento è un evento di analisi.
* Il valore di `eventTime` indica la data e l'ora in cui si è verificato l'evento.
* La sezione `body` contiene dati sull'evento di analisi. In questo caso si tratta di un evento di inferenza e quindi il corpo contiene dati di tipo `inferences`.
* La sezione `inferences` indica che `type` è `entity`. Questa sezione include dati aggiuntivi sull'entità.

## <a name="run-the-sample-program-to-detect-persons-or-vehicles-or-bikes"></a>Eseguire il programma di esempio per rilevare persone, veicoli o biciclette
Per usare un modello diverso, è necessario modificare la topologia del grafo, nonché il file `operations.json`.

Copiare la [topologia del grafo](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtensionOpenVINO/topology.json) in un file locale, ad esempio `C:\TEMP\topology.json`. Aprire la copia e modificare il valore di `inferencingUrl` in `http://openvino:4000/personVehicleBikeDetection`.

In Visual Studio Code passare quindi alla cartella *src/cloud-to-device-console-app* e aprire il file `operations.json`. Modificare la riga con `topologyUrl` come segue:

```
      "topologyFile" : "C:\\TEMP\\topology.json" 
```
È ora possibile ripetere i passaggi precedenti per eseguire di nuovo il programma di esempio, con la nuova topologia. I risultati dell'inferenza saranno simili (nello schema) a quelli del modello di rilevamento dei veicoli, ma con `subtype` impostato su `personVehicleBikeDetection`.

## <a name="run-the-sample-program-to-classify-vehicles"></a>Eseguire il programma di esempio per classificare i veicoli
In Visual Studio Code aprire la copia locale di `topology.json` del passaggio precedente e modificare il valore di `inferencingUrl` in `http://openvino:4000/vehicleClassification`. Se si è eseguito l'esempio precedente per rilevare persone, veicoli o biciclette, non è necessario modificare di nuovo il file `operations.json`.

È ora possibile ripetere i passaggi precedenti per eseguire di nuovo il programma di esempio, con la nuova topologia. Di seguito è riportato il risultato di una classificazione di esempio.

```
[IoTHubMonitor] [9:44:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "classification",
        "subtype": "color",
        "classification": {
          "tag": {
            "value": "black",
            "confidence": 0.9179772138595581
          }
        }
      },
      {
        "type": "classification",
        "subtype": "type",
        "classification": {
          "tag": {
            "value": "truck",
            "confidence": 1
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:44:18.1280000Z"
  }
}
```

## <a name="run-the-sample-program-to-detect-faces"></a>Eseguire il programma di esempio per rilevare i visi
In Visual Studio Code aprire la copia locale di `topology.json` del passaggio precedente e modificare il valore di `inferencingUrl` in `http://openvino:4000/faceDetection`. Se si è eseguito l'esempio precedente per rilevare persone, veicoli o biciclette, non è necessario modificare di nuovo il file `operations.json`.

È ora possibile ripetere i passaggi precedenti per eseguire di nuovo il programma di esempio, con la nuova topologia. Di seguito è riportato il risultato di un rilevamento di esempio. Si noti che il video del parcheggio usato in precedenza non contiene visi rilevabili, pertanto per provare questo modello sarà necessario un altro video.

```
[IoTHubMonitor] [9:54:18 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "inferences": [
      {
        "type": "entity",
        "subtype": "faceDetection",
        "entity": {
          "tag": {
            "value": "face",
            "confidence": 0.9997053742408752
          },
          "box": {
            "l": 0.2559490501880646,
            "t": 0.03403960168361664,
            "w": 0.17685115337371826,
            "h": 0.45835764706134796
          }
        }
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/inferenceClient",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-07-24T16:54:18.1280000Z"
  }
}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende provare altre guide di avvio rapido o esercitazioni, mantenere le risorse create. In caso contrario, passare al portale di Azure e quindi ai gruppi di risorse, selezionare il gruppo di risorse in cui è stata eseguita questa guida di avvio rapido ed eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

Esaminare altri problemi per utenti avanzati:

* Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP invece del simulatore RTSP. È possibile cercare videocamere IP che supportano RTSP nella pagina dei prodotti [conformi a ONVIF](https://www.onvif.org/conformant-products/). Cercare dispositivi conformi ai profili G, S o T.
* Usare un dispositivo Linux AMD64 o x64 invece di una VM Linux di Azure. Il dispositivo deve trovarsi nella stessa rete della videocamera IP. È possibile seguire le istruzioni riportate in [Installare il runtime Azure IoT Edge in Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Quindi seguire il dispositivo con l'hub IoT di Azure seguendo le istruzioni riportate in [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](../../iot-edge/quickstart-linux.md).
