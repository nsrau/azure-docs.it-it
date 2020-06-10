---
title: 'Esercitazione: Registrazione video continua nel cloud e riproduzione dal cloud - Azure'
description: In questa esercitazione si apprenderà come usare Analisi di video live in IoT Edge per registrare i video in modo continuo nel cloud ed eseguire lo streaming di qualsiasi parte del video usando i Servizi multimediali di Azure.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: a69d3f5db9dd8cbe25bbf79f44921f26258005cc
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259988"
---
# <a name="tutorial-continuous-video-recording-to-cloud-and-playback-from-cloud"></a>Esercitazione: Registrazione video continua nel cloud e riproduzione dal cloud  

In questa esercitazione si apprenderà come usare Analisi di video live in IoT Edge per [registrare i video in modo continuo](continuous-video-recording-concept.md) nel cloud ed eseguire lo streaming di qualsiasi parte del video usando i Servizi multimediali. Questa operazione è utile per gli scenari di sicurezza, conformità e altri scenari in cui è necessario conservare l'archivio dei filmati di una videocamera per più giorni o settimane.

> [!div class="checklist"]
> * Configurare le risorse pertinenti
> * Esaminare il codice che esegue la registrazione video continua (CVR)
> * Eseguire il codice di esempio
> * Esaminare i risultati e visualizzare il video

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Letture consigliate  

Si consiglia di leggere le pagine di documentazione seguenti

* [Panoramica di Analisi di video live in IoT Edge](overview.md)
* [Terminologia di Analisi di video live in IoT Edge](terminology.md)
* [Concetti del grafo multimediale](media-graph-concept.md) 
* [Scenari di registrazione video continua](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per l'esercitazione sono i seguenti

* [Visual Studio Code](https://code.visualstudio.com/) nel computer di sviluppo con l'estensione [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e l'estensione [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > È possibile che venga chiesto di installare Docker. È possibile ignorare questa richiesta.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) nel computer di sviluppo.
* Eseguire lo [script di installazione delle risorse di Analisi di video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)

Al termine dei passaggi precedenti, saranno disponibili determinate risorse di Azure distribuite nella sottoscrizione di Azure, tra cui:

* Hub IoT
* Account di archiviazione
* Account Servizi multimediali di Azure
* Macchina virtuale Linux in Azure, con [runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) installato

## <a name="concepts"></a>Concetti

Come descritto [qui](media-graph-concept.md), un grafo multimediale consente di definire la posizione da cui devono essere acquisiti i contenuti multimediali, la modalità di elaborazione e la posizione a cui inviare i risultati. Per eseguire la registrazione video continua (CVR), è necessario acquisire il video da una videocamera compatibile con RTSP e registrarlo in modo continuo in un [asset dei Servizi multimediali di Azure](terminology.md#asset). Il diagramma seguente mostra una rappresentazione grafica del grafo multimediale.

![Grafico multimediale](./media/continuous-video-recording-tutorial/continuous-video-recording-overview.png)

In questa esercitazione si userà un modulo Edge creato usando [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) per simulare una videocamera RTSP. All'interno del grafo multimediale, si userà un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) per ottenere il feed live e inviare il video al [nodo del sink dell'asset](media-graph-concept.md#asset-sink) che registrerà il video in un asset.

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Prima di iniziare, verificare di aver completato il terzo punto elencato in [Prerequisiti](#prerequisites). Al termine dello script di installazione delle risorse, fare clic sulle parentesi graffe per esporre la struttura di cartelle. Vengono visualizzati alcuni file creati nella directory ~/clouddrive/lva-sample.

![Impostazioni app](./media/quickstarts/clouddrive.png)

I file di interesse per questa esercitazione sono:

     * ~/clouddrive/lva-sample/edge-deployment/.env  - contains properties that Visual Studio Code uses to deploy modules to an edge device
     * ~/clouddrive/lva-sample/appsettings.json - used by Visual Studio Code for running the sample code

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

## <a name="examine-the-sample-files"></a>Esaminare i file di esempio

In Visual Studio Code aprire "src/edge/deployment.template.json". Questo modello definisce i moduli Edge che verranno distribuiti nel dispositivo Edge (la macchina virtuale Linux di Azure). Si noti che nella sezione "modules" sono presenti due voci con i nomi seguenti:

* lvaEdge: Analisi di video live nel modulo IoT Edge
* rtspsim: simulatore RTSP

Passare quindi alla cartella "src/cloud-to-device-console-app". Verrà visualizzato il file appsettings.json creato insieme ad altri file:

* c2d-console-app.csproj: il file di progetto per Visual Studio Code.
* operations.json: il file che elenca le diverse operazioni da eseguire
* Program.cs: il codice del programma di esempio che esegue le operazioni seguenti:
    * Carica le impostazioni dell'app
    * Richiama i metodi diretti esposti da Analisi di video live nel modulo IoT Edge. È possibile usare il modulo per analizzare i flussi video live richiamando i [metodi diretti](direct-methods.md)
    * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra del terminale e gli eventi generati dal modulo nella finestra di output
    * Richiama i metodi diretti per pulire le risorse

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione di IoT Edge 

Il manifesto della distribuzione definisce i moduli che vengono distribuiti in un dispositivo Edge e le impostazioni di configurazione dei moduli. Seguire questa procedura per generare il manifesto dal file modello e quindi distribuirlo nel dispositivo Edge.

1. Avviare Visual Studio Code
1. Impostare la stringa di connessione dell'hub IoT facendo clic sull'icona "Altre azioni" accanto al riquadro dell'hub IoT di Azure nell'angolo inferiore sinistro. È possibile copiare la stringa dal file src/cloud-to-device-console-app/appsettings.json. 

    ![Impostare la stringa di connessione IoT](./media/quickstarts/set-iotconnection-string.png)
1. Quindi fare clic con il pulsante destro del mouse sul file "src/edge/deployment.template.json" e fare clic su "Generate IoT Edge Deployment Manifest" (Genera manifesto della distribuzione di IoT Edge). Visual Studio Code usa i valori del file .env per sostituire le variabili individuate nel file modello di distribuzione. Questa operazione crea un file manifesto nella cartella src/edge/config denominato "deployment.amd64.json".

   ![Generate IoT Edge deployment manifest (Genera manifesto della distribuzione di IoT Edge)](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Fare clic con il pulsante destro del mouse su "src/edge/config/deployment.amd64.json" e fare clic su "Create Deployment for Single Device" (Crea la distribuzione per un unico dispositivo).

   ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/quickstarts/create-deployment-single-device.png)
1. Verrà quindi chiesto di selezionare un dispositivo dell'hub IoT. Selezionare lva-sample-device dall'elenco a discesa.
1. Entro circa 30 secondi, aggiornare l'hub IoT di Azure nella sezione inferiore sinistra. Verrà visualizzato il dispositivo Edge con i moduli seguenti distribuiti:
    * Analisi di video live in IoT Edge (nome del modulo "lvaEdge")
    * Simulatore RTSP (nome del modulo "rtspsim")
 
    ![Hub IoT](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Preparare il monitoraggio dei moduli 

Quando si usa Analisi di video live nel modulo IoT Edge per registrare il flusso video live, gli eventi vengono inviati all'hub IoT. Per visualizzare gli eventi, seguire questa procedura:

1. Aprire il riquadro Explorer in Visual Studio Code e cercare l'hub IoT di Azure nell'angolo inferiore sinistro.
1. Espandere il nodo Dispositivi.
1. Fare clic con il pulsante destro del mouse su lva-sample-device e scegliere l'opzione "Avvia monitoraggio endpoint eventi predefinito".

    ![Avvia monitoraggio endpoint eventi predefinito](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Eseguire il programma 

1. Visual Studio Code, passare a "src/cloud-to-device-console-app/operations.json"
1. Nel nodo GraphTopologySet, modificare quanto segue:

    `"topologyUrl" : "https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Quindi, nei nodi GraphInstanceSet e GraphTopologyDelete assicurarsi che il valore di topologyName corrisponda al valore della proprietà "name" nella topologia del grafo precedente:

    `"topologyName" : "CVRToAMSAsset"`  
1. Aprire la [topologia](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) in un browser e osservare assetNamePattern. Per assicurarsi di avere un asset con un nome univoco, potrebbe essere necessario modificare il nome dell'istanza del grafo nel file operations.json (il valore predefinito è "Sample-Graph-1").

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
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
1. Quando si preme il tasto "INVIO" nella finestra del terminale, viene eseguita la serie successiva di chiamate ai metodi diretti
     * Una chiamata a GraphTopologySet usando il valore topologyUrl precedente.
     * Una chiamata a GraphInstanceSet usando il corpo seguente.
     
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph-1",
       "properties": {
         "topologyName": "CVRToAMSAsset",
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
1. L'output nella finestra del terminale verrà sospeso quando viene visualizzato un prompt 'Premere INVIO per continuare'. Non premere "INVIO". È possibile scorrere verso l'alto per visualizzare i payload di risposta JSON per i metodi diretti richiamati
1. Se ora si passa alla finestra di output in Visual Studio Code, verranno visualizzati i messaggi inviati all'hub IoT da Analisi di video live nel modulo IoT Edge.

     * Questi messaggi sono descritti nella sezione seguente
1. L'istanza del grafo continuerà a essere eseguita e registrerà il video. Il simulatore RTSP continuerà il ciclo del video di origine. Per arrestare la registrazione, tornare alla finestra del terminale e premere "INVIO". Per pulire le risorse vengono eseguite le serie successive di chiamate:

     * Una chiamata a GraphInstanceDeactivate per disattivare l'istanza del grafo
     * Una chiamata a GraphInstanceDelete per eliminare l'istanza
     * Una chiamata a GraphTopologyDelete per eliminare la topologia
     * Una chiamata finale a GraphTopologyList per indicare che l'elenco è ora vuoto

## <a name="interpret-the-results"></a>Interpretare i risultati 

Quando si esegue il grafo multimediale, Analisi di video live nel modulo IoT Edge invia determinati eventi operativi e di diagnostica all'hub IoT Edge. Questi eventi sono i messaggi visualizzati nella finestra di output di Visual Studio Code che contengono una sezione "body" e una sezione "applicationProperties". Per comprendere cosa rappresentano queste sezioni, leggere [questo](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) articolo.

Nei messaggi seguenti le proprietà dell'applicazione e il contenuto del corpo sono definiti dal modulo Analisi di video live.

## <a name="diagnostic-events"></a>Eventi di diagnostica 

### <a name="mediasession-established-event"></a>Evento MediaSessionEstablished

Quando l'istanza del grafo è attivata, il nodo di origine RTSP tenta di connettersi al server RTSP in esecuzione nel modulo rtspsim. In caso di esito positivo, viene visualizzato questo evento:

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 XXX.XX.XX.XX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T09:42:18.1280000Z"
  }
}
```

* Il messaggio è l'evento di diagnostica MediaSessionEstablished che indica che il nodo di origine RTSP (oggetto) è stato in grado di stabilire una connessione con il simulatore RTSP e iniziare a ricevere un feed live (simulato).
* "subject" in applicationProperties fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. In questo caso il messaggio viene originato dal nodo di origine RTSP.
* "eventType" in applicationProperties indica che si tratta di un evento di diagnostica.
* "eventTime" indica l'ora in cui si è verificato l'evento.
* "body" contiene i dati sull'evento di diagnostica che in questo caso è rappresentato dai dettagli [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="operational-events"></a>Eventi operativi 

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quando il nodo del sink dell'asset inizia a registrare il video, genera questo evento di tipo Microsoft.Media.Graph.Operational.RecordingStarted

```
[IoTHubMonitor] [9:42:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-04-09T09:42:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

"subject" in applicationProperties fa riferimento al nodo del sink dell'asset nel grafo da cui è stato generato il messaggio.

Il corpo contiene informazioni sulla posizione dell'output, che in questo caso è il nome dell'asset dei Servizi multimediali di Azure in cui viene registrato il video. Prendere nota di questo valore.

### <a name="recordingavailable-event"></a>Evento RecordingAvailable

Come suggerisce il nome, l'evento RecordingStarted viene inviato all'avvio della registrazione, ma i dati video potrebbero non essere ancora stati caricati nell'asset. Quando il nodo del sink dell'asset ha caricato i dati video nell'asset, il nodo genera questo evento di tipo Microsoft.Media.Graph.Operational.RecordingAvailable

```
[IoTHubMonitor] [[9:43:38 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingAvailable",
    "eventTime": "2020-04-09T09:43:38.1280000Z",
    "dataVersion": "1.0"
  }
}
```

Questo evento indica che è stato scritto un numero sufficiente di dati nell'asset affinché i lettori o i client avviino la riproduzione del video.

"subject" in applicationProperties fa riferimento al nodo AssetSink nel grafo che ha generato il messaggio.

Il corpo contiene informazioni sulla posizione dell'output, che in questo caso è il nome dell'asset dei Servizi multimediali di Azure in cui viene registrato il video.

### <a name="recordingstopped-event"></a>Evento RecordingStopped

Quando si disattiva l'istanza del grafo, il nodo del sink dell'asset arresta la registrazione video nell'asset e genera questo evento di tipo Microsoft.Media.Graph.Operational.RecordingStopped.

```
[IoTHubMonitor] [11:33:31 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "assetName",
    "outputLocation": "sampleAsset-CVRToAMSAsset-Sample-Graph-1"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{resource-group-name}/providers/microsoft.media/mediaservices/{ams-account-name}",
    "subject": "/graphInstances/Sample-Graph-1/sinks/assetSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStopped",
    "eventTime": "2020-04-10T11:33:31.051Z",
    "dataVersion": "1.0"
  }
}
```

Questo evento indica che la registrazione è stata arrestata.

"subject" in applicationProperties fa riferimento al nodo AssetSink nel grafo che ha generato il messaggio.

Il corpo contiene informazioni sulla posizione dell'output, che in questo caso è il nome dell'asset dei Servizi multimediali di Azure in cui viene registrato il video.

## <a name="media-services-asset"></a>Asset Servizi multimediali  

È possibile esaminare l'asset Servizi multimediali creato dal grafo multimediale accedendo al portale di Azure e visualizzando il video.

1. Aprire il Web browser e quindi passare al [portale di Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.
1. Individuare l'account Servizi multimediali tra le risorse presenti nella sottoscrizione e aprire il pannello dell'account
1. Fare clic su Asset nell'elenco Servizi multimediali

    ![Asset](./media/continuous-video-recording-tutorial/assets.png)
1. Si troverà un asset elencato con il nome sampleAsset-CVRToAMSAsset-Sample-Graph-1 (questo è il modello di denominazione scelto nel file della topologia del grafo).
1. Fare clic sull'asset.
1. Nella pagina dei dettagli dell'asset fare clic su **Crea nuovo** sotto la casella di testo URL di streaming.

    ![Nuovo asset](./media/continuous-video-recording-tutorial/new-asset.png)

1. Nella procedura guidata visualizzata accettare le opzioni predefinite e premere "Aggiungi". Per altre informazioni, vedere [Riproduzione video](video-playback-concept.md).

    > [!TIP]
    > Assicurarsi che [l'endpoint di streaming sia in esecuzione](../latest/streaming-endpoint-concept.md).
1. Il lettore dovrebbe caricare il video e dovrebbe essere possibile premere **Play**> * * (Riproduci) per visualizzarlo.

> [!NOTE]
> Poiché l'origine del video è un contenitore che simula un feed della videocamera, i timestamp nel video sono correlati al momento in cui è stata attivata l'istanza del grafo e al momento in cui è stata disattivata. È possibile vedere [questa](playback-multi-day-recordings-tutorial.md) esercitazione per scoprire come visualizzare una registrazione di più giorni e visualizzare parti dell'archivio. In questa esercitazione sarà anche possibile visualizzare i timestamp nel video visualizzato sullo schermo.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di provare le altre esercitazioni, è necessario mantenere le risorse create. In caso contrario, passare al portale di Azure, passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stata eseguita l'esercitazione ed eliminare il gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

* Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP anziché il simulatore RTSP. È possibile cercare le videocamere IP con supporto RTSP nella [pagina dei prodotti conformi ONVIF](https://www.onvif.org/conformant-products/) cercando i dispositivi conformi ai profili G, S o T.
* Usare un dispositivo AMD64 o X64 Linux (anziché una macchina virtuale Linux di Azure). Il dispositivo deve trovarsi nella stessa rete della videocamera IP. È possibile seguire le istruzioni riportate in [Installare il runtime di Azure IoT Edge in Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux), quindi seguire le istruzioni nell'avvio rapido [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) per registrare il dispositivo con l'hub IoT di Azure.
