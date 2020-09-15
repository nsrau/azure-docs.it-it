---
title: 'Esercitazione: Registrazione di video basata su eventi nel cloud e riproduzione dal cloud - Azure'
description: Questa esercitazione illustra come usare Analisi video live di Azure in Azure IoT Edge per eseguire una registrazione di video basata su eventi nel cloud e riprodurla dal cloud.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: 05ee34770cacdcda270afced13373a61ba83e13a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568566"
---
# <a name="tutorial-event-based-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Esercitazione: Registrazione di video basata su eventi nel cloud e riproduzione dal cloud

Questa esercitazione illustra come usare Analisi video live di Azure in Azure IoT Edge per eseguire una registrazione selettiva di parti di un'origine video live in Servizi multimediali di Azure nel cloud. Questo caso d'uso viene definito [registrazione di video basata su eventi](event-based-video-recording-concept.md) in questa esercitazione. Per registrare parti di un video live, si userà un modello di intelligenza artificiale per il rilevamento degli oggetti per cercare oggetti nel video e registrare video clip solo quando ne viene rilevato un determinato tipo. Verrà inoltre descritto come riprodurre i video clip registrati usando Servizi multimediali. Questa funzionalità è utile per diversi scenari in cui è necessario conservare un archivio di video clip di interesse. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare le risorse pertinenti.
> * Esaminare il codice che esegue la registrazione di video basata su eventi.
> * Eseguire il codice di esempio.
> * Esaminare i risultati e visualizzare il video.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Letture consigliate  

Leggere questi articoli prima di iniziare:

* [Panoramica di Analisi di video live in IoT Edge](overview.md)
* [Terminologia di Analisi di video live in IoT Edge](terminology.md)
* [Concetti del grafo multimediale](media-graph-concept.md) 
* [Registrazione di video basata su eventi](event-based-video-recording-concept.md)
* [Esercitazione: Sviluppo di un modulo IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Come modificare deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)
* Sezione su [come dichiarare le route nel manifesto della distribuzione di IoT Edge](../../iot-edge/module-composition.md#declare-routes)

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per questa esercitazione sono:

* [Visual Studio Code](https://code.visualstudio.com/) nel computer di sviluppo con l'estensione [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e le estensioni per [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > È possibile che venga chiesto di installare Docker. Ignorare questa richiesta.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) nel computer di sviluppo.
* Eseguire lo [script di configurazione delle risorse di Analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) e [configurare l'ambiente](https://review.docs.microsoft.com/en-us/azure/media-services/live-video-analytics-edge/detect-motion-emit-events-quickstart?branch=release-preview-media-services-lva#set-up-the-environment)

Al termine di questa procedura, le risorse di Azure pertinenti verranno distribuite nella sottoscrizione di Azure:

* Hub IoT Azure
* Account di archiviazione di Azure
* Account Servizi multimediali di Azure
* VM Linux in Azure, con il [runtime IoT Edge](../../iot-edge/how-to-install-iot-edge-linux.md) installato

## <a name="concepts"></a>Concetti

Per registrazione di video basata su eventi si intende il processo di registrazione di video attivato da un evento. Questo evento può essere generato da:
- L'elaborazione del segnale video stesso, ad esempio al rilevamento di un oggetto in movimento nel video.
- Un'origine indipendente, ad esempio l'apertura di una porta. 

In alternativa, è possibile attivare la registrazione solo quando un servizio di inferenza rileva che si è verificato un evento specifico. In questa esercitazione verrà usato un video di veicoli in movimento in un'autostrada e verranno registrati video clip ogni volta che viene rilevato un camion.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/overview.svg" alt-text="Grafo multimediale":::

Il diagramma precedente è una rappresentazione grafica di un [grafo multimediale](media-graph-concept.md) e dei moduli aggiuntivi che eseguono lo scenario previsto. Vengono usati quattro moduli IoT Edge:

* Il modulo Analisi video live in IoT Edge.
* Un modulo Edge che esegue un modello di intelligenza artificiale dietro un endpoint HTTP. Questo modulo di intelligenza artificiale usa il modello [YOLO v3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx) in grado di rilevare diversi tipi di oggetti.
* Un modulo personalizzato per contare e filtrare gli oggetti, a cui viene fatto riferimento come contatore di oggetti nel diagramma. In questa esercitazione verrà creato e distribuito un contatore di oggetti.
* Un [modulo di simulatore RTSP](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) per simulare una videocamera RTSP.
    
Come illustrato nel diagramma, verrà usato un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) nel grafo multimediale per acquisire il video live simulato del traffico in un'autostrada e inviarlo a due percorsi:

* Il primo è il percorso del nodo di un [processore di filtro della frequenza dei fotogrammi](media-graph-concept.md#frame-rate-filter-processor) che genera fotogrammi video con la frequenza specificata (ridotta). Questi fotogrammi video vengono inviati a un nodo di estensioni HTTP. Il nodo inoltra quindi i fotogrammi, come immagini, al modulo di intelligenza artificiale YOLO v3, che è un rilevatore di oggetti. Il nodo riceve i risultati, ovvero gli oggetti (veicoli nel traffico) rilevati dal modello. Il nodo di estensioni HTTP pubblica quindi i risultati tramite il nodo sink di messaggi dell'hub IoT nell'hub di IoT Edge.
* Il modulo ObjectCounter è configurato per ricevere i messaggi dall'hub di IoT Edge, che includono i risultati di rilevamento degli oggetti (veicoli nel traffico). Il modulo controlla questi messaggi e cerca oggetti di un determinato tipo, configurati tramite un'impostazione. Quando questo tipo di oggetto viene individuato, il modulo invia un messaggio all'hub di IoT Edge. I messaggi di "oggetto trovato" vengono quindi instradati al nodo di origine dell'hub IoT del grafo multimediale. Alla ricezione di tale messaggio, il nodo di origine dell'hub IoT nel grafo multimediale attiva il nodo del [processore del gate di segnale](media-graph-concept.md#signal-gate-processor). Il nodo del processore del gate di segnale viene quindi aperto per un intervallo di tempo configurato. Il flusso video passa attraverso il controllo verso il nodo del sink dell'asset per l'intervallo di tempo specificato. Questa parte dello streaming live viene quindi registrata tramite il nodo [sink di asset](media-graph-concept.md#asset-sink) in un [asset](terminology.md#asset) nell'account Servizi multimediali di Azure.

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Prima di iniziare, verificare di aver completato il terzo punto elencato in [Prerequisiti](#prerequisites). Al termine dello script di configurazione delle risorse, fare clic sulle parentesi graffe per esporre la struttura della cartella. Vengono visualizzati alcuni file creati nella directory ~/clouddrive/lva-sample.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/clouddrive.png" alt-text="Impostazioni app":::

I file di interesse per questa esercitazione sono:

* **~/clouddrive/lva-sample/edge-deployment/.env**: contiene le proprietà usate da Visual Studio Code per distribuire i moduli in un dispositivo Edge.
* **~/clouddrive/lva-sample/appsetting.json**: viene usato da Visual Studio Code per l'esecuzione del codice di esempio.

I file sono necessari per questi passaggi.

1. Clonare il repository dal collegamento di GitHub https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. Avviare Visual Studio Code e aprire la cartella in cui è stato scaricato il repository.
1. In Visual Studio Code passare alla cartella src/cloud-to-device-console-app e creare un file denominato **appsettings.json**. Questo file contiene le impostazioni necessarie per eseguire il programma.
1. Copiare il contenuto dal file ~/clouddrive/lva-sample/appsettings.json. Il testo sarà simile al seguente:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```

    La stringa di connessione dell'hub IoT consente di usare Visual Studio Code per inviare comandi ai moduli Edge tramite l'hub IoT di Azure.
    
1. Passare quindi alla cartella src/edge e creare un file denominato **.env**.
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

Nel passaggio precedente è stato avviato Visual Studio Code ed è stata aperta la cartella contenente il codice di esempio.

In Visual Studio Code passare a src/edge. Verranno visualizzati il file .env creato e alcuni file modello di distribuzione. Questo modello definisce i moduli Edge che verranno distribuiti nel dispositivo Edge (la macchina virtuale Linux di Azure). Il file .env contiene i valori delle variabili usate nei modelli, ad esempio le credenziali di Servizi multimediali.

Aprire src/edge/deployment.objectCounter.template.json. Nella sezione **modules** sono presenti quattro voci che corrispondono agli elementi elencati nella sezione "Concetti" precedente:

* **lvaEdge**: il modulo Analisi video live in IoT Edge.
* **yolov3**: il modulo di intelligenza artificiale creato con il modello YOLO v3.
* **rtspsim**: il simulatore RTSP.
* **objectCounter**: il modulo che cerca oggetti specifici nei risultati di yolov3.

Per il modulo objectCounter, vedere la stringa (${MODULES.objectCounter}) usata per il valore di "image". Si basa sull'[esercitazione](../../iot-edge/tutorial-develop-for-linux.md) relativa allo sviluppo di un modulo IoT Edge. Visual Studio Code riconosce automaticamente che il codice per il modulo objectCounter si trova in src/edge/modules/objectCounter. 

Per informazioni su come dichiarare route nel manifesto della distribuzione di IoT Edge, vedere [questa sezione](../../iot-edge/module-composition.md#declare-routes). Esaminare quindi le route nel file modello JSON. Si noti come:

* LVAToObjectCounter consente di inviare eventi specifici a un endpoint specifico nel modulo objectCounter.
* ObjectCounterToLVA viene usato per inviare un evento trigger a un endpoint specifico (che dovrà essere il nodo di origine dell'hub IoT) nel modulo lvaEdge.
* objectCounterToIoTHub viene usato come strumento di debug per visualizzare l'output di objectCounter quando si esegue l'esercitazione.

> [!NOTE]
> Controllare le proprietà desiderate per il modulo objectCounter, che sono configurate per cercare gli oggetti contrassegnati come "camion" con un livello di attendibilità di almeno il 50%.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione di IoT Edge 

Il manifesto della distribuzione definisce i moduli che vengono distribuiti in un dispositivo Edge e le relative impostazioni di configurazione. Seguire questa procedura per generare il manifesto dal file modello e quindi distribuirlo nel dispositivo Edge.

In Visual Studio Code seguire [queste istruzioni](../../iot-edge/tutorial-develop-for-linux.md#build-and-push-your-solution) per accedere a Docker. Quindi selezionare **Build and Push IoT Edge Solution** (Crea ed esegui il push della soluzione IoT Edge). Usare src/edge/deployment.objectCounter.template.json per questo passaggio.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/build-push.png" alt-text="Build and Push IoT Edge Solution":::

Questa azione crea il modulo objectCounter per il conteggio degli oggetti ed esegue il push dell'immagine nel Registro Azure Container.

* Verificare che le variabili di ambiente CONTAINER_REGISTRY_USERNAME_myacr e CONTAINER_REGISTRY_PASSWORD_myacr siano definite nel file .env.

Questo passaggio crea il manifesto della distribuzione di IoT Edge in src/edge/config/deployment.objectCounter.amd64.json. Fare clic con il pulsante destro del mouse sul file e scegliere **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstarts/create-deployment-single-device.png" alt-text="Create Deployment for Single Device":::

Se si tratta della prima esercitazione con Analisi video live in IoT Edge, Visual Studio Code richiederà di immettere la stringa di connessione dell'hub IoT. È possibile copiarla dal file appsettings.json.

A questo punto, Visual Studio Code chiede di selezionare un dispositivo hub IoT. Selezionare il dispositivo IoT Edge, che sarà lva-sample-device.

In questa fase è stata avviata la distribuzione dei moduli Edge nel dispositivo IoT Edge.
Dopo circa 30 secondi aggiornare l'hub IoT di Azure nella sezione in basso a sinistra in Visual Studio Code. Si noterà che sono stati distribuiti quattro moduli denominati lvaEdge, rtspsim, yolov3 e objectCounter.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/event-based-video-recording-tutorial/iot-hub.png" alt-text="Quattro moduli distribuiti":::

## <a name="prepare-for-monitoring-events"></a>Operazioni preliminari al monitoraggio degli eventi

Per visualizzare gli eventi del modulo ObjectCounter e del modulo Analisi video live in IoT Edge, seguire questa procedura:

1. Aprire il riquadro Explorer in Visual Studio Code e cercare **Hub IoT di Azure** nell'angolo in basso a sinistra.
1. Espandere il nodo **Dispositivi**.
1. Fare clic con il pulsante destro del mouse sul file lva-sample-device e scegliere **Avvia il monitoraggio endpoint eventi predefinito**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstarts/start-monitoring-iothub-events.png" alt-text="Avvia monitoraggio endpoint eventi predefinito":::
    
    ## <a name="run-the-program"></a>Eseguire il programma

1. In Visual Studio Code passare a src/cloud-to-device-console-app/operations.json.

1. Nel nodo **GraphTopologySet** modificare quanto segue:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json"`
    
1. Quindi, nei nodi **GraphInstanceSet** e **GraphTopologyDelete** modificare:

    `"topologyName" : "EVRtoAssetsOnObjDetect"`
1. Avviare una sessione di debug premendo F5. Verranno visualizzati alcuni messaggi stampati nella finestra **TERMINALE**.

1. Il file operations.json verrà avviato con le chiamate a GraphTopologyList e GraphInstanceList. Se sono state pulite le risorse dopo gli argomenti di avvio rapido o le esercitazioni precedenti, questa azione restituisce elenchi vuoti e quindi viene sospesa per consentire di premere **INVIO**, come illustrato di seguito:

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

1. Quando si preme **INVIO** nella finestra **TERMINALE**, viene eseguita la serie successiva di chiamate ai metodi diretti:
   * Una chiamata a GraphTopologySet usando il valore di topologyUrl precedente
   * Una chiamata a GraphInstanceSet usando il corpo seguente
     
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
    
   * Una chiamata a GraphInstanceActivate per avviare l'istanza del grafo e il flusso del video
   * Una seconda chiamata a GraphInstanceList per indicare che l'istanza del grafo è in esecuzione
     
1. L'output nella finestra **TERMINALE** viene sospeso in corrispondenza della richiesta **Premere INVIO per continuare**. Non premere **INVIO** questa volta. Scorrere in alto per visualizzare i payload della risposta JSON per i metodi diretti richiamati.

1. Se ora si passa alla finestra **OUTPUT** in Visual Studio Code, verranno visualizzati i messaggi inviati all'hub IoT dal modulo Analisi video Live in IoT Edge.

   Questi messaggi sono descritti nella sezione seguente.
     
1. L'istanza del grafo continua a essere eseguita e a registrare il video. Il simulatore RTSP continua a eseguire il video di origine in un ciclo. Esaminare questi messaggi come descritto nella sezione seguente. Quindi, per arrestare la registrazione, tornare nella finestra **TERMINALE** e premere **INVIO**. La serie successiva di chiamate serve a pulire le risorse usando:

   * Una chiamata a GraphInstanceDeactivate per disattivare l'istanza del grafo.
   * Una chiamata a GraphInstanceDelete per eliminare l'istanza.
   * Una chiamata a GraphTopologyDelete per eliminare la topologia.
   * Una chiamata finale a GraphTopologyList per indicare che l'elenco è ora vuoto.

## <a name="interpret-the-results"></a>Interpretare i risultati 

Quando si esegue il grafo multimediale, il modulo Analisi video live in IoT Edge invia determinati eventi operativi e di diagnostica all'hub di IoT Edge. Questi eventi sono i messaggi visualizzati nella finestra **OUTPUT** di Visual Studio Code. Contengono una sezione body e una sezione applicationProperties. Per comprendere cosa rappresentano queste sezioni, vedere [Creare e leggere messaggi dell'hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nei messaggi seguenti le proprietà dell'applicazione e il contenuto del corpo sono definiti dal modulo di Analisi video live.

## <a name="diagnostics-events"></a>Eventi di diagnostica

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished 

Quando viene creata un'istanza di un grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore del simulatore RTSP. In caso di esito positivo, viene stampato questo evento. Il tipo di evento è Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished.

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


* Il messaggio è un evento di diagnostica (MediaSessionEstablished). Indica che il nodo di origine RTSP (subject) ha stabilito una connessione con il simulatore RTSP e ha iniziato a ricevere un feed live simulato.
* La sezione subject in applicationProperties fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. In questo caso il messaggio viene originato dal nodo di origine RTSP.
* La sezione eventType in applicationProperties indica che si tratta di un evento di diagnostica.
* La sezione eventTime indica la data e l'ora in cui si è verificato l'evento. Si tratta della data/ora in cui il video del traffico (file MKV) ha iniziato ad arrivare nel modulo come streaming live.
* La sezione body contiene i dati sull'evento di diagnostica, che in questo caso è rappresentato dai dettagli [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="operational-events"></a>Eventi operativi

Dopo un breve periodo di esecuzione del grafo multimediale, si otterrà un evento dal modulo objectCounter. 

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

La sezione applicationProperties contiene la data e l'ora dell'evento. Si tratta della data/ora in cui il modulo objectCounter ha rilevato che i risultati del modulo yolov3 contengono oggetti di interesse (camion).

È possibile che vengano visualizzati più eventi di questo tipo quando vengono rilevati altri camion nel video.

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quasi immediatamente dopo l'invio dell'evento da parte di objectCounter, viene visualizzato un evento di tipo Microsoft.Media.Graph.Operational.RecordingStarted:

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

La sezione subject in applicationProperties fa riferimento al nodo sink di asset nel grafo da cui è stato generato questo messaggio. La sezione body contiene informazioni sulla posizione di output. In questo caso, è il nome dell'asset di Servizi multimediali di Azure in cui viene registrato il video. Prendere nota di questo valore.

### <a name="recordingavailable-event"></a>Evento RecordingAvailable

Dopo aver caricato il video nell'asset, il nodo sink di asset genera questo evento di tipo Microsoft.Media.Graph.Operational.RecordingAvailable:

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

Questo evento indica che la quantità di dati scritti nell'asset è sufficiente per consentire a lettori o client di avviare la riproduzione del video. La sezione subject in applicationProperties fa riferimento al nodo AssetSink nel grafo da cui è stato generato questo messaggio. La sezione body contiene informazioni sulla posizione di output. In questo caso, è il nome dell'asset di Servizi multimediali di Azure in cui viene registrato il video.

### <a name="recordingstopped-event"></a>Evento RecordingStopped

Se si esaminano le impostazioni di attivazione (maximumActivationTime) per il nodo del processore del gate di segnale nella [topologia](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json), si noterà che il gate è configurato per essere chiuso dopo 30 secondi dall'invio del video. Approssimativamente 30 secondi dopo l'evento RecordingStarted, verrà visualizzato un evento di tipo Microsoft.Media.Graph.Operational.RecordingStopped. Questo evento indica che il nodo sink di asset ha interrotto la registrazione del video nell'asset.

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

Questo evento indica che la registrazione è stata arrestata. La sezione subject in applicationProperties fa riferimento al nodo AssetSink nel grafo da cui è stato generato questo messaggio. La sezione body contiene informazioni sulla posizione di output. In questo caso, è il nome dell'asset di Servizi multimediali di Azure in cui viene registrato il video.

## <a name="media-services-asset"></a>Asset Servizi multimediali  

È possibile esaminare l'asset di Servizi multimediali creato dal grafo accedendo al portale di Azure e visualizzando il video.

1. Aprire il Web browser e quindi passare al [portale di Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.
1. Individuare l'account Servizi multimediali tra le risorse presenti nella sottoscrizione. Aprire il riquadro dell'account.
1. Selezionare **Asset** nell'elenco **Servizi multimediali**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/assets.png" alt-text="Registrazione continua di video":::
1. L'elenco conterrà un asset denominato sampleAssetFromEVR-LVAEdge-{DateTime}. Si tratta del nome specificato nella proprietà outputLocation dell'evento RecordingStarted. assetNamePattern nella topologia determina il modo in cui è stato generato il nome.
1. Selezionare l'asset.
1. Nella pagina dei dettagli dell'asset fare clic su **Crea nuovo** sotto la casella di testo **URL di streaming**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/continuous-video-recording-tutorial/new-asset.png" alt-text="Nuovo asset":::
1. Nella procedura guidata accettare le opzioni predefinite e selezionare **Aggiungi**. Per altre informazioni, vedere [Riproduzione di video](video-playback-concept.md).

    > [!TIP]
    > Assicurarsi che [l'endpoint di streaming sia in esecuzione](../latest/streaming-endpoint-concept.md).
1. Il lettore dovrebbe caricare il video. Selezionare **Riproduci** per visualizzarlo.

> [!NOTE]
> Poiché l'origine del video è un contenitore che simula un feed della videocamera, i timestamp nel video sono correlati al momento in cui è stata attivata l'istanza del grafo e al momento in cui è stata disattivata. Se si usano i controlli di riproduzione incorporati nell'esercitazione [Riproduzione di registrazioni di più giorni](playback-multi-day-recordings-tutorial.md), è possibile visualizzare i timestamp nel video visualizzato sullo schermo.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende provare le altre esercitazioni, mantenere le risorse create. In caso contrario, passare al portale di Azure, passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stata eseguita l'esercitazione ed eliminare il gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

* Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP anziché il simulatore RTSP. È possibile cercare le videocamere IP con supporto RTSP nella [pagina dei prodotti conformi ONVIF](https://www.onvif.org/conformant-products/) cercando i dispositivi conformi ai profili G, S o T.
* Usare un dispositivo AMD64 o X64 Linux (anziché una macchina virtuale Linux di Azure). Il dispositivo deve trovarsi nella stessa rete della videocamera IP. Seguire le istruzioni riportate in [Installare il runtime Azure IoT Edge in Linux](../../iot-edge/how-to-install-iot-edge-linux.md). Quindi seguire le istruzioni dell'argomento di avvio rapido [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](../../iot-edge/quickstart-linux.md) per registrare il dispositivo con l'hub IoT di Azure.
