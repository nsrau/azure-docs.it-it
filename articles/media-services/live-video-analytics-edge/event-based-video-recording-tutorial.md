---
title: 'Esercitazione: Registrazione video basata su eventi nel cloud e riproduzione dal cloud - Azure'
description: In questa esercitazione si apprenderà come usare Analisi di video live in IoT Edge per eseguire una registrazione video basata su eventi nel cloud e la riproduzione dal cloud.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 7ff8502688e2b507b8a576c177948f29c2248be4
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456611"
---
# <a name="tutorial-event-based-video-recording-to-cloud-and-playback-from-cloud"></a>Esercitazione: Registrazione video basata su eventi nel cloud e riproduzione dal cloud

In questa esercitazione si apprenderà come usare Analisi di video live in IoT Edge per registrare selettivamente parti di un'origine video live in Servizi multimediali nel cloud. Questo caso d'uso viene definito [registrazione video basata su eventi](event-based-video-recording-concept.md) (EVR) in questa esercitazione. A tale scopo, si userà un modello di intelligenza artificiale per il rilevamento degli oggetti per cercare gli oggetti nel video e registrare i clip video solo quando viene rilevato un determinato tipo di oggetto. Si apprenderà anche come riprodurre i clip video registrati usando Servizi multimediali. Questa operazione è utile per diversi scenari in cui è necessario conservare un archivio di clip video di interesse.

> [!div class="checklist"]
> * Configurare le risorse pertinenti
> * Esaminare il codice che esegue la registrazione video basata su eventi (EVR)
> * Eseguire il codice di esempio
> * Esaminare i risultati e visualizzare il video

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Letture consigliate  

Si consiglia di leggere le pagine di documentazione seguenti

* [Panoramica di Analisi di video live in IoT Edge](overview.md)
* [Terminologia di Analisi di video live in IoT Edge](terminology.md)
* [Concetti del grafo multimediale](media-graph-concept.md) 
* [Registrazione video basata su eventi](event-based-video-recording-concept.md)
* [Esercitazione: Sviluppo di un modulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
* [Come modificare deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Sezione su [come dichiarare le route nel manifesto della distribuzione di IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes)

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per l'esercitazione sono i seguenti

* [Visual Studio Code](https://code.visualstudio.com/) nel computer di sviluppo con l'estensione [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e l'estensione [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > È possibile che venga chiesto di installare Docker. È possibile ignorare questa richiesta.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) nel computer di sviluppo.
* Eseguire lo [script di installazione delle risorse di Analisi di video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) e [configurare l'ambiente](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

Al termine dei passaggi precedenti, saranno disponibili determinate risorse di Azure distribuite nella sottoscrizione di Azure, tra cui:

* Hub IoT
* Account di archiviazione
* Account Servizi multimediali di Azure
* Macchina virtuale Linux in Azure, con [runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) installato

## <a name="concepts"></a>Concetti

Per registrazione video basata su eventi (EVR) si intende il processo di registrazione video attivato da un evento. Questo evento può essere generato dall'elaborazione del segnale video (ad esempio, quando viene rilevato un oggetto in movimento nel video) o da un'origine indipendente (ad esempio, l'apertura di una porta). In alternativa, è possibile attivare la registrazione solo quando un servizio di inferenza rileva che si è verificato un evento specifico.  In questa esercitazione verrà usato un video di veicoli in movimento su una superstrada e verranno registrati clip video ogni volta che viene rilevato un camion.

![Grafico multimediale](./media/event-based-video-recording-tutorial/overview.png)

Il diagramma precedente è la rappresentazione grafica di un [grafo multimediale](media-graph-concept.md) e dei moduli aggiuntivi che eseguono lo scenario desiderato. Vengono usati quattro moduli IoT Edge:

* Analisi di video live nel modulo IoT Edge.
* Un modulo Edge che esegue un modello di intelligenza artificiale basato su un endpoint HTTP. Questo modulo di intelligenza artificiale usa il modello [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) in grado di rilevare diversi tipi di oggetti.
* Un modulo personalizzato per conteggiare e filtrare gli oggetti (chiamato Contatore oggetti nel diagramma precedente) che verrà creato e distribuito in questa esercitazione.
* Un [modulo di simulatore RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) per simulare una videocamera RTSP.
    
    Come illustrato nel diagramma, verrà usata un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) nel grafo multimediale per acquisire il video live simulato (del traffico su una superstrada) e inviare il video a due percorsi.

* Il primo è il percorso del nodo di un [processore filtro della frequenza dei fotogrammi](media-graph-concept.md#frame-rate-filter-processor) che genera fotogrammi video con la frequenza specificata (ridotta). I fotogrammi video vengono inviati al nodo di un'estensione HTTP che quindi inoltra i fotogrammi (come immagini) al modulo di intelligenza artificiale (YOLO v3 che è un rilevatore di oggetti) e riceve i risultati, ovvero gli oggetti (veicoli nel traffico) rilevati dal modello. Il nodo dell'estensione HTTP pubblica quindi i risultati tramite il nodo del sink dei messaggi dell'hub IoT nell'hub IoT Edge.
* Il modulo contatore di oggetti è configurato per ricevere i messaggi dall'hub IoT Edge, che includono i risultati di rilevamento degli oggetti (veicoli nel traffico). Controlla i messaggi cercando gli oggetti di un determinato tipo (configurato tramite un'impostazione). Quando viene rilevato il tipo di oggetto specificato, il modulo invia un messaggio all'hub IoT Edge. I messaggi di "oggetto trovato" vengono quindi instradati al nodo di origine dell'hub IoT del grafo multimediale. Quando riceve un messaggio di questo tipo, il nodo di origine dell'hub IoT nel grafo multimediale attiva il nodo del [processore di controllo del segnale](media-graph-concept.md#signal-gate-processor) causandone l'apertura per l'intervallo di tempo impostato. Il flusso video passa attraverso il controllo verso il nodo del sink dell'asset per l'intervallo di tempo specificato. Questa parte dello streaming live viene quindi registrata tramite il nodo del [sink dell'asset](media-graph-concept.md#asset-sink) in un [asset](terminology.md#asset) nell'account Servizi multimediali di Azure.

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Prima di iniziare, verificare di aver completato il terzo punto elencato in [Prerequisiti](#prerequisites). Al termine dello script di installazione delle risorse, fare clic sulle parentesi graffe per esporre la struttura di cartelle. Vengono visualizzati alcuni file creati nella directory ~/clouddrive/lva-sample.

![Impostazioni app](./media/quickstarts/clouddrive.png)

I file di interesse per questa esercitazione sono:

* ~/clouddrive/lva-sample/edge-deployment/.env : contiene le proprietà usate da Visual Studio Code per distribuire i moduli in un dispositivo Edge.
* ~/clouddrive/lva-sample/appsetting.json: usato da Visual Studio Code per l'esecuzione del codice di esempio.

Questi file saranno necessari per i passaggi successivi.

1. Clonare il repository da https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Avviare Visual Studio Code e aprire la cartella in cui è stato scaricato il repository.
1. In Visual Studio Code passare alla cartella "src/cloud-to-device-console-app" e creare un file denominato "appsettings.json". Questo file conterrà le impostazioni necessarie per eseguire il programma.
1. Copiare il contenuto dal file ~/clouddrive/lva-sample/appsettings.json. Il testo sarà simile al seguente:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    La stringa di connessione dell'hub IoT consente di usare Visual Studio Code per inviare comandi ai moduli Edge tramite l'hub IoT di Azure.
    
1. Passare quindi alla cartella "src/edge" e creare un file denominato ".env".
1. Copiare il contenuto dal file ~/clouddrive/lva-sample/.env. Il testo sarà simile al seguente:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/output"  
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-template-file"></a>Esaminare il file modello 

Nel passaggio precedente è stato avviato Visual Studio Code e aperta la cartella contenente il codice di esempio.

In Visual Studio Code passare a "src/edge". Verranno visualizzati il file .env creato e alcuni file modello di distribuzione. Questo modello definisce i moduli Edge che verranno distribuiti nel dispositivo Edge (la macchina virtuale Linux di Azure). Il file .env contiene i valori delle variabili usate nei modelli, ad esempio le credenziali di Servizi multimediali.

Aprire "src/edge/deployment.objectCounter.template.json". Si noti che sono presenti quattro voci nella sezione "modules" che corrispondono agli elementi elencati precedentemente nella sezione Concetti:

* lvaEdge: Analisi di video live nel modulo IoT Edge
* yolov3: modulo di intelligenza artificiale creato usando il modello YOLO v3
* rtspsim: simulatore RTSP
* objectCounter: modulo che cerca oggetti specifici nei risultati di yolov3

Per il modulo objectCounter, vedere la stringa (${MODULES.objectCounter}) usata per il valore "image" (basata sull'[esercitazione](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux) per lo sviluppo di un modulo IoT Edge). Visual Studio Code riconoscerà automaticamente che il codice per il modulo contatore oggetti si trova in "src/edge/modules/objectCounter". 

Leggere [questa](https://docs.microsoft.com/azure/iot-edge/module-composition#declare-routes) sezione su come dichiarare le route nel manifesto della distribuzione di IoT Edge e quindi esaminare le route nel file JSON del modello. Si noti come:

* LVAToObjectCounter consente di inviare eventi specifici a un endpoint specifico nel modulo objectCounter.
* ObjectCounterToLVA consente di inviare un evento di attivazione a un endpoint specifico (che deve essere il nodo di origine dell'hub IoT) nel modulo lvaEdge.
* objectCounterToIoTHub viene usato come strumento di debug per visualizzare l'output di objectCounter quando si esegue l'esercitazione.

> [!NOTE]
> Controllare le proprietà desiderate per il modulo objectCounter, che sono configurate per cercare gli oggetti contrassegnati come "camion" con un livello di confidenza pari almeno al 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione di IoT Edge 

Il manifesto della distribuzione definisce i moduli che vengono distribuiti in un dispositivo Edge e le impostazioni di configurazione dei moduli. Seguire questa procedura per generare il manifesto dal file modello e quindi distribuirlo nel dispositivo Edge.

Usando Visual Studio Code, seguire [queste](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux#build-and-push-your-solution) istruzioni per accedere a Docker e scegliere "Build and Push IoT Edge solution" (Compila ed esegui il push della soluzione IoT Edge), ma usare src/edge/deployment.objectCounter.template.json per questo passaggio.

![Compilare ed eseguire il push della soluzione IoT Edge](./media/event-based-video-recording-tutorial/build-push.png)

Verrà compilato il modulo objectCounter per il conteggio degli oggetti e verrà eseguito il push dell'immagine nel Registro Azure Container.

* Verificare che le variabili di ambiente CONTAINER_REGISTRY_USERNAME_myacr e CONTAINER_REGISTRY_PASSWORD_myacr siano definite nel file .env.

Il passaggio precedente creerà il manifesto della distribuzione di IoT Edge in src/edge/config/deployment.objectCounter.amd64.json. Fare clic con il pulsante destro del mouse sul file e fare clic su "Create Deployment for Single Device" (Crea la distribuzione per un unico dispositivo).

![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/quickstarts/create-deployment-single-device.png)

Se si tratta della prima esercitazione con Analisi di video live in IoT Edge, Visual Studio Code richiederà di immettere la stringa di connessione IoTHub. È possibile copiarla dal file appsettings.json.

A questo punto, Visual Studio Code richiederà di selezionare un dispositivo hub IoT. Selezionare il dispositivo IoT Edge (deve essere "lva-sample-device").

In questa fase è stata avviata la distribuzione dei moduli Edge nel dispositivo IoT Edge.
Entro circa 30 secondi, aggiornare l'hub IoT di Azure nella sezione inferiore sinistra in Visual Studio Code. Verranno visualizzati quattro moduli distribuiti (prendere di nuovo nota dei nomi: lvaEdge, rtspsim, yolov3 e objectCounter).

![Quattro moduli distribuiti](./media/event-based-video-recording-tutorial/iot-hub.png)

## <a name="prepare-for-monitoring-events"></a>Operazioni preliminari al monitoraggio degli eventi

Per visualizzare gli eventi dal modulo contatore oggetti e da Analisi di video live nel modulo IoT Edge, seguire questa procedura:

1. Aprire il riquadro Explorer in Visual Studio Code e cercare l'hub IoT di Azure nell'angolo inferiore sinistro.
1. Espandere il nodo Dispositivi.
1. Fare clic con il pulsante destro del mouse su lva-sample-device e scegliere l'opzione **Avvia monitoraggio endpoint eventi predefinito**.

![Avvia monitoraggio endpoint eventi predefinito](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Eseguire il programma

1. Visual Studio Code, passare a "src/cloud-to-device-console-app/operations.json"

1. Nel nodo GraphTopologySet, modificare quanto segue:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Quindi, nei nodi GraphInstanceSet e GraphTopologyDelete modificare

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Avviare una sessione di debug (premere F5). Verranno visualizzati alcuni messaggi stampati nella finestra del terminale.

1. Il file operations.json inizia con le chiamate a GraphTopologyList e GraphInstanceList. Se sono state pulite le risorse dopo gli avvii rapidi o le esercitazioni precedenti, vengono restituiti elenchi vuoti e viene sospesa l'esecuzione per consentire di premere INVIO, come illustrato di seguito:

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
    1. Quando si preme il tasto "INVIO" nella finestra del terminale, viene eseguita la serie successiva di chiamate ai metodi diretti.
     * Una chiamata a GraphTopologySet usando il valore topologyUrl precedente.
     * Una chiamata a GraphInstanceSet usando il corpo seguente.
     
        ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "EVRtoAssetsOnObjDetect",
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
    
     * Una chiamata a GraphInstanceActivate per avviare l'istanza del grafo e avviare il flusso di video
     * Una seconda chiamata a GraphInstanceList per indicare che l'istanza del grafo è effettivamente nello stato in esecuzione
     
1. L'output nella finestra del terminale verrà sospeso quando viene visualizzato un prompt 'Premere INVIO per continuare'. Non premere "INVIO". È possibile scorrere verso l'alto per visualizzare i payload di risposta JSON per i metodi diretti richiamati.

1. Se ora si passa alla finestra di output in Visual Studio Code, verranno visualizzati i messaggi inviati all'hub IoT da Analisi di video live nel modulo IoT Edge.

     * Questi messaggi sono descritti nella sezione seguente.
     
1. L'istanza del grafo continuerà a essere eseguita e registrerà il video. Il simulatore RTSP continuerà il ciclo del video di origine. Esaminare i messaggi come illustrato nella sezione seguente e quindi, per arrestare l'istanza, tornare alla finestra del terminale e premere "INVIO". Per pulire le risorse vengono eseguite le serie successive di chiamate:

     * Una chiamata a GraphInstanceDeactivate per disattivare l'istanza del grafo
     * Una chiamata a GraphInstanceDelete per eliminare l'istanza
     * Una chiamata a GraphTopologyDelete per eliminare la topologia
     * Una chiamata finale a GraphTopologyList per indicare che l'elenco è ora vuoto

## <a name="interpret-the-results"></a>Interpretare i risultati 

Quando si esegue il grafo multimediale, Analisi di video live nel modulo IoT Edge invia determinati eventi operativi e di diagnostica all'hub IoT Edge. Questi eventi sono i messaggi visualizzati nella finestra di output di Visual Studio Code che contengono una sezione "body" e una sezione "applicationProperties". Per comprendere cosa rappresentano queste sezioni, vedere [Creare e leggere messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

Nei messaggi seguenti le proprietà dell'applicazione e il contenuto del corpo sono definiti dal modulo Analisi di video live.

## <a name="diagnostic-events"></a>Eventi di diagnostica

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished 

Quando viene creata un'istanza di un grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore del simulatore RTSP. In caso di esito positivo, viene visualizzato questo evento. Si noti che il tipo di evento è Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

```
[IoTHubMonitor] [5:53:17 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-05-17T17:53:16.981Z",
    "dataVersion": "1.0"
  }
}
```


* Il messaggio è l'evento di diagnostica MediaSessionEstablished che indica che il nodo di origine RTSP (oggetto) è stato in grado di stabilire una connessione con il simulatore RTSP e iniziare a ricevere un feed live (simulato).

* "subject" in applicationProperties fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. In questo caso il messaggio viene originato dal nodo di origine RTSP.

* "eventType" in applicationProperties indica che si tratta di un evento di diagnostica.

* "eventTime" indica l'ora in cui si è verificato l'evento, ovvero l'ora in cui il video del traffico (file MKV) ha iniziato ad arrivare nel modulo come streaming live.

* "body" contiene i dati sull'evento di diagnostica che in questo caso è rappresentato dai dettagli [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="operational-events"></a>Eventi operativi

Dopo un breve periodo di esecuzione del grafo multimediale, si otterrà un evento dal modulo contatore oggetti. 

```
[IoTHubMonitor] [5:53:44 PM] Message received from [lva-sample-device/objectCounter]:
{
  "body": {
    "count": 2
  },
  "applicationProperties": {
    "eventTime": "2020-05-17T17:53:44.062Z"
  }
}
```

applicationProperties contiene eventTime, ovvero l'ora in cui il modulo contatore oggetti ha rilevato che i risultati del modulo YOLO V3 contenevano oggetti di interesse (camion).

È possibile che vengano visualizzati più eventi di questo tipo quando vengono rilevati altri camion nel video.

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quasi immediatamente dopo l'invio dell'evento da parte del contatore oggetti, viene visualizzato un evento di tipo Microsoft.Media.Graph.Operational.RecordingStarted

```
[IoTHubMonitor] [5:53:46 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": " 2020-05-17T17:53:46.132Z",
    "dataVersion": "1.0"
  }
}
```

"subject" in applicationProperties fa riferimento al nodo del sink dell'asset nel grafo da cui è stato generato il messaggio. Il corpo contiene informazioni sulla posizione dell'output, che in questo caso è il nome dell'asset dei Servizi multimediali di Azure in cui viene registrato il video. Prendere nota di questo valore.

### <a name="recordingavailable-event"></a>Evento RecordingAvailable

Quando il nodo del sink dell'asset ha caricato il video nell'asset, il nodo genera questo evento di tipo Microsoft.Media.Graph.Operational.RecordingAvailable

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-05-17T17:54:15.808Z",
    "dataVersion": "1.0"
  }
}
```

Questo evento indica che è stato scritto un numero sufficiente di dati nell'asset affinché i lettori o i client avviino la riproduzione del video. "subject" in applicationProperties fa riferimento al nodo AssetSink nel grafo che ha generato il messaggio. Il corpo contiene informazioni sulla posizione dell'output, che in questo caso è il nome dell'asset dei Servizi multimediali di Azure in cui viene registrato il video.

### <a name="recordingstopped-event"></a>Evento RecordingStopped

Se si esaminano le impostazioni di attivazione (maximumActivationTime) per il nodo processore del controllo del segnale nella [topologia](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), si noterà che il controllo è configurato per essere chiuso dopo 30 secondi dall'invio del video. Approssimativamente 30 secondi dopo l'evento RecordingStarted, verrà visualizzato un evento di tipo Microsoft.Media.Graph.Operational.RecordingStopped che indica che il nodo del sink dell'asset ha interrotto la registrazione del video nell'asset.

```
[IoTHubMonitor] [5:54:15 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAssetFromEVR-LVAEdge-20200517T175346Z"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-05-17T17:54:15.040Z",
    "dataVersion": "1.0"
  }
}
```

Questo evento indica che la registrazione è stata arrestata. "subject" in applicationProperties fa riferimento al nodo AssetSink nel grafo che ha generato il messaggio. Il corpo contiene informazioni sulla posizione dell'output, che in questo caso è il nome dell'asset dei Servizi multimediali di Azure in cui viene registrato il video.

## <a name="media-services-asset"></a>Asset Servizi multimediali  

È possibile esaminare l'asset Servizi multimediali creato dal grafo accedendo al portale di Azure e visualizzando il video.

1. Aprire il Web browser e quindi passare al [portale di Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.
1. Individuare l'account Servizi multimediali tra le risorse presenti nella sottoscrizione e aprire il pannello dell'account
1. Fare clic su Asset nell'elenco Servizi multimediali.

    ![Asset](./media/continuous-video-recording-tutorial/assets.png)
1. Sarà disponibile un asset denominato sampleAssetFromEVR-LVAEdge-{DateTime}: si tratta del nome specificato nella proprietà outputLocation dell'evento RecordingStarted. assetNamePattern nella topologia determina il modo in cui è stato generato il nome.
1. Fare clic sull'asset.
1. Nella pagina dei dettagli dell'asset fare clic su **Crea nuovo** sotto la casella di testo URL di streaming.

    ![Nuovo asset](./media/continuous-video-recording-tutorial/new-asset.png)

1. Nella procedura guidata visualizzata accettare le opzioni predefinite e premere "Aggiungi". Per altre informazioni, vedere [Riproduzione video](video-playback-concept.md).

    > [!TIP]
    > Assicurarsi che [l'endpoint di streaming sia in esecuzione](../latest/streaming-endpoint-concept.md).
1. Il lettore dovrebbe caricare il video e dovrebbe essere possibile premere **Play** (Riproduci) per visualizzarlo.

> [!NOTE]
> Poiché l'origine del video è un contenitore che simula un feed della videocamera, i timestamp nel video sono correlati al momento in cui è stata attivata l'istanza del grafo e al momento in cui è stata disattivata. Se si usano i controlli di riproduzione incorporati nell'esercitazione [Riproduzione di registrazioni di più giorni](playback-multi-day-recordings-tutorial.md), è possibile visualizzare i timestamp nel video visualizzato sullo schermo.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di provare le altre esercitazioni, è necessario mantenere le risorse create. In caso contrario, passare al portale di Azure, passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stata eseguita l'esercitazione ed eliminare il gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

* Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP anziché il simulatore RTSP. È possibile cercare le videocamere IP con supporto RTSP nella [pagina dei prodotti conformi ONVIF](https://www.onvif.org/conformant-products/) cercando i dispositivi conformi ai profili G, S o T.
* Usare un dispositivo AMD64 o X64 Linux (anziché una macchina virtuale Linux di Azure). Il dispositivo deve trovarsi nella stessa rete della videocamera IP. È possibile seguire le istruzioni riportate in [Installare il runtime di Azure IoT Edge in Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux), quindi seguire le istruzioni nell'avvio rapido [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) per registrare il dispositivo con l'hub IoT di Azure.
