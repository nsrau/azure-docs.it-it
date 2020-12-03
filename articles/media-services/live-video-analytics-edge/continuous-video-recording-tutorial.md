---
title: 'Esercitazione: Registrazione continua di video nel cloud e riproduzione dal cloud - Azure'
description: Questa esercitazione illustra come usare Analisi video live di Azure in Azure IoT Edge per registrare i video in modo continuo nel cloud ed eseguirne lo streaming di qualsiasi parte usando Servizi multimediali di Azure.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: c38ab1f32d1ef4e54cd8568ff17d325fabdefc31
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498371"
---
# <a name="tutorial-continuous-video-recording-to-the-cloud-and-playback-from-the-cloud"></a>Esercitazione: Registrazione continua di video nel cloud e riproduzione dal cloud

Questa esercitazione illustra come usare Analisi video live di Azure in Azure IoT Edge per eseguire la [registrazione continua di video](continuous-video-recording-concept.md) nel cloud ed eseguirne lo streaming di qualsiasi parte usando Servizi multimediali di Azure. Questa funzionalità è utile per scenari come la sicurezza e la conformità in cui è necessario mantenere un archivio dei filmati eseguiti con una videocamera per più giorni o settimane. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare le risorse pertinenti.
> * Esaminare il codice che esegue la registrazione continua di video.
> * Eseguire il codice di esempio.
> * Esaminare i risultati e visualizzare il video.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Letture consigliate  

Leggere questi articoli prima di iniziare:

* [Panoramica di Analisi di video live in IoT Edge](overview.md)
* [Terminologia di Analisi di video live in IoT Edge](terminology.md)
* [Concetti del grafo multimediale](media-graph-concept.md) 
* [Scenari di registrazione video continua](continuous-video-recording-concept.md)

## <a name="prerequisites"></a>Prerequisiti

I prerequisiti per questa esercitazione sono:

* [Visual Studio Code](https://code.visualstudio.com/) nel computer di sviluppo con l'estensione [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) e le estensioni per [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

    > [!TIP]
    > È possibile che venga chiesto di installare Docker. Ignorare questa richiesta.
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/thank-you/sdk-3.1.201-windows-x64-installer) nel computer di sviluppo.
* Completare lo [script di configurazione delle risorse di Analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup).

Al termine di questa procedura, le risorse di Azure pertinenti verranno distribuite nella sottoscrizione di Azure:

* Hub IoT Azure
* Account di archiviazione di Azure
* Account Servizi multimediali di Azure
* VM Linux in Azure, con il [runtime IoT Edge](../../iot-edge/how-to-install-iot-edge.md) installato

## <a name="concepts"></a>Concetti

Come descritto nell'articolo [Concetti relativi al grafo multimediale](media-graph-concept.md), un grafo multimediale consente di definire:

- Da dove devono essere acquisiti i contenuti multimediali.
- Come devono essere elaborati.
- Dove devono essere distribuiti i risultati. 
 
 Per eseguire la registrazione continua di video, è necessario acquisire il video da una videocamera compatibile con il protocollo RTSP e registrarlo in modo continuo in un [asset di Servizi multimediali di Azure](terminology.md#asset). Questo diagramma mostra una rappresentazione grafica del grafo multimediale.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/continuous-video-recording-tutorial/continuous-video-recording-overview.svg" alt-text="Grafo multimediale":::

In questa esercitazione si userà un modulo Edge creato usando [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) per simulare una videocamera RTSP. All'interno del grafo multimediale, si userà un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) per ottenere il feed live e inviare il video al [nodo sink di asset](media-graph-concept.md#asset-sink) che registrerà il video in un asset.

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Prima di iniziare, verificare di aver completato il terzo punto elencato in [Prerequisiti](#prerequisites). Al termine dello script di configurazione delle risorse, fare clic sulle parentesi graffe per esporre la struttura della cartella. Vengono visualizzati alcuni file creati nella directory ~/clouddrive/lva-sample.

![Impostazioni app](./media/quickstarts/clouddrive.png)

I file di interesse per questa esercitazione sono:

* **~/clouddrive/lva-sample/edge-deployment/.env**: contiene le proprietà usate da Visual Studio Code per distribuire i moduli in un dispositivo Edge.
* **~/clouddrive/lva-sample/appsettings.json**: viene usato da Visual Studio Code per l'esecuzione del codice di esempio.

I file sono necessari per questi passaggi:

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
1. Copiare il contenuto dal file ~/clouddrive/lva-sample/edge-deployment/.env. Il testo sarà simile al seguente:

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

In Visual Studio Code aprire src/edge/deployment.template.json. Questo modello definisce i moduli Edge che verranno distribuiti nel dispositivo Edge (la macchina virtuale Linux di Azure). Si noti che nella sezione **modules** sono presenti due voci con i nomi seguenti:

* **lvaEdge**: il modulo Analisi video live in IoT Edge.
* **rtspsim**: il simulatore RTSP.

Passare quindi alla cartella src/cloud-to-device-console-app. Verrà visualizzato il file appsettings.json creato insieme ad altri file:

* **c2d-console-app.csproj**: il file di progetto per Visual Studio Code.
* **operations.json**: il file che elenca le diverse operazioni da eseguire.
* **Program.cs**: il codice del programma di esempio, che:
    * Carica le impostazioni dell'app.
    * Richiama i metodi diretti esposti da Live Video Analytics nel modulo IoT Edge. È possibile usare il modulo per analizzare i flussi video live richiamando i [metodi diretti](direct-methods.md).
    * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra **TERMINALE** e gli eventi generati dal modulo nella finestra **OUTPUT**.
    * Richiama i metodi diretti per pulire le risorse.

## <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione di IoT Edge 

Il manifesto della distribuzione definisce i moduli che vengono distribuiti in un dispositivo Edge e le relative impostazioni di configurazione. Seguire questa procedura per generare il manifesto dal file modello e quindi distribuirlo nel dispositivo Edge.

1. Avviare Visual Studio Code.
1. Impostare la stringa di connessione dell'hub IoT facendo clic sull'icona **Altre azioni** accanto al riquadro **HUB IOT DI AZURE** nell'angolo in basso a sinistra. Copiare la stringa dal file src/cloud-to-device-console-app/appsettings.json. 

    ![Impostare la stringa di connessione dell'hub IoT](./media/quickstarts/set-iotconnection-string.png)
1. Fare clic con il pulsante destro del mouse sul file src/edge/deployment.template.json e scegliere **Generate IoT Edge Deployment Manifest** (Genera manifesto della distribuzione di IoT Edge). Visual Studio Code usa i valori del file .env per sostituire le variabili individuate nel file modello di distribuzione. Questa azione crea un file manifesto nella cartella src/edge/config denominato **deployment.amd64.json**.

   ![Generate IoT Edge deployment manifest (Genera manifesto della distribuzione di IoT Edge)](./media/quickstarts/generate-iot-edge-deployment-manifest.png)
1. Fare clic con il pulsante destro del mouse sul file src/edge/config/deployment.amd64.json e scegliere **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

   ![Create deployment for single device (Crea la distribuzione per un unico dispositivo)](./media/quickstarts/create-deployment-single-device.png)
1. Verrà quindi visualizzata la richiesta **Selezionare un hub IoT**. Selezionare lva-sample-device nell'elenco a discesa.
1. Dopo circa 30 secondi aggiornare l'hub IoT di Azure nella sezione in basso a sinistra. Si noterà che nel dispositivo Edge sono distribuiti i moduli seguenti:
    * Analisi video live in IoT Edge (nome del modulo **lvaEdge**)
    * Simulatore RTSP (nome del modulo **rtspsim**)
 
    ![Hub IoT](./media/continuous-video-recording-tutorial/iot-hub.png)

## <a name="prepare-to-monitor-the-modules"></a>Preparare il monitoraggio dei moduli 

Quando si usa il modulo Analisi video live in IoT Edge per registrare il flusso video live, gli eventi vengono inviati all'hub IoT. Per visualizzare gli eventi, seguire questa procedura:

1. Aprire il riquadro Explorer in Visual Studio Code e cercare **Hub IoT di Azure** nell'angolo in basso a sinistra.
1. Espandere il nodo **Dispositivi**.
1. Fare clic con il pulsante destro del mouse sul file lva-sample-device e scegliere **Avvia il monitoraggio endpoint eventi predefinito**.

    ![Avvia monitoraggio endpoint eventi predefinito](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-program"></a>Eseguire il programma 

1. In Visual Studio Code aprire la scheda **Estensioni** (oppure premere CTRL+MAIUSC+X) e cercare Hub IoT di Azure.
1. Fare clic con il pulsante destro del mouse e scegliere **Impostazioni estensione**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Impostazioni estensione":::
1. Cercare e abilitare "Show Verbose Message" (Visualizza messaggio dettagliato).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Show Verbose Message":::
1. <!--In Visual Studio Code, go-->Passare a src/cloud-to-device-console-app/operations.json.
1. Nel nodo **GraphTopologySet** modificare quanto segue:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json" `
1. Quindi, nei nodi **GraphInstanceSet** e **GraphTopologyDelete** assicurarsi che il valore di **topologyName** corrisponda al valore della proprietà **name** nella topologia del grafo precedente:

    `"topologyName" : "CVRToAMSAsset"`  
1. Aprire la [topologia](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/cvr-asset/topology.json) in un browser e osservare assetNamePattern. Per assicurarsi di avere un asset con un nome univoco, potrebbe essere necessario cambiare il nome dell'istanza del grafo nel file operations.json (il valore predefinito è Sample-Graph-1).

    `"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}"`    
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
   * Una chiamata a GraphInstanceActivate per avviare l'istanza del grafo e il flusso del video
   * Una seconda chiamata a GraphInstanceList per indicare che l'istanza del grafo è in esecuzione 
1. L'output nella finestra **TERMINALE** viene sospeso in corrispondenza della richiesta **Premere INVIO per continuare**. Non premere **INVIO** questa volta. Scorrere in alto per visualizzare i payload della risposta JSON per i metodi diretti richiamati.
1. Se ora si passa alla finestra **OUTPUT** in Visual Studio Code, verranno visualizzati i messaggi inviati all'hub IoT dal modulo Analisi video Live in IoT Edge.

   Questi messaggi sono descritti nella sezione seguente.
1. L'istanza del grafo continua a essere eseguita e a registrare il video. Il simulatore RTSP continua a eseguire il video di origine in un ciclo. Per arrestare la registrazione, tornare nella finestra **TERMINALE** e premere **INVIO**. La serie successiva di chiamate serve a pulire le risorse usando:

   * Una chiamata a GraphInstanceDeactivate per disattivare l'istanza del grafo.
   * Una chiamata a GraphInstanceDelete per eliminare l'istanza.
   * Una chiamata a GraphTopologyDelete per eliminare la topologia.
   * Una chiamata finale a GraphTopologyList per indicare che l'elenco è ora vuoto.

## <a name="interpret-the-results"></a>Interpretare i risultati 

Quando si esegue il grafo multimediale, il modulo Analisi video live in IoT Edge invia determinati eventi operativi e di diagnostica all'hub di IoT Edge. Questi eventi sono i messaggi visualizzati nella finestra **OUTPUT** di Visual Studio Code. Contengono una sezione body e una sezione applicationProperties. Per comprendere cosa rappresentano queste sezioni, vedere [Creare e leggere messaggi dell'hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nei messaggi seguenti le proprietà dell'applicazione e il contenuto del corpo sono definiti dal modulo di Analisi video live.

## <a name="diagnostics-events"></a>Eventi di diagnostica 

### <a name="mediasession-established-event"></a>Evento MediaSessionEstablished

Quando l'istanza del grafo è attivata, il nodo di origine RTSP tenta di connettersi al server RTSP in esecuzione nel modulo rtspsim. In caso di esito positivo, viene stampato questo evento:

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

* Il messaggio è un evento di diagnostica (MediaSessionEstablished). Indica che il nodo di origine RTSP (subject) ha stabilito una connessione con il simulatore RTSP e ha iniziato a ricevere un feed live simulato.
* La sezione subject in applicationProperties fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. In questo caso il messaggio viene originato dal nodo di origine RTSP.
* La sezione eventType in applicationProperties indica che si tratta di un evento di diagnostica.
* La sezione eventTime indica la data e l'ora in cui si è verificato l'evento.
* La sezione body contiene i dati sull'evento di diagnostica, che in questo caso è rappresentato dai dettagli [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).

## <a name="operational-events"></a>Eventi operativi 

### <a name="recordingstarted-event"></a>Evento RecordingStarted

Quando il nodo sink di asset inizia a registrare il video, genera questo evento di tipo Microsoft.Media.Graph.Operational.RecordingStarted:

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

La sezione subject in applicationProperties fa riferimento al nodo sink di asset nel grafo da cui è stato generato questo messaggio.

La sezione body contiene informazioni sulla posizione di output. In questo caso, è il nome dell'asset di Servizi multimediali di Azure in cui viene registrato il video. Prendere nota di questo valore.

### <a name="recordingavailable-event"></a>Evento RecordingAvailable

Come suggerisce il nome, l'evento RecordingStarted viene inviato all'avvio della registrazione, ma i dati video potrebbero non essere ancora stati caricati nell'asset. Dopo aver caricato i dati video nell'asset, il nodo sink di asset genera questo evento di tipo Microsoft.Media.Graph.Operational.RecordingAvailable:

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

Questo evento indica che la quantità di dati scritti nell'asset è sufficiente per consentire a lettori o client di avviare la riproduzione del video.

La sezione subject in applicationProperties fa riferimento al nodo AssetSink nel grafo da cui è stato generato questo messaggio.

La sezione body contiene informazioni sulla posizione di output. In questo caso, è il nome dell'asset di Servizi multimediali di Azure in cui viene registrato il video.

### <a name="recordingstopped-event"></a>Evento RecordingStopped

Quando si disattiva l'istanza del grafo, il nodo sink di asset interrompe la registrazione del video nell'asset. Genera questo evento di tipo Microsoft.Media.Graph.Operational.RecordingStopped:

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

La sezione subject in applicationProperties fa riferimento al nodo AssetSink nel grafo da cui è stato generato questo messaggio.

La sezione body contiene informazioni sulla posizione dell'output, che in questo caso è il nome dell'asset di Servizi multimediali di Azure in cui viene registrato il video.

## <a name="media-services-asset"></a>Asset Servizi multimediali  

È possibile esaminare l'asset di Servizi multimediali creato dal grafo multimediale accedendo al portale di Azure e visualizzando il video.

1. Aprire il Web browser e quindi passare al [portale di Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.
1. Individuare l'account Servizi multimediali tra le risorse presenti nella sottoscrizione e aprire il relativo pannello.
1. Selezionare **Asset** nell'elenco **Servizi multimediali**.

    ![Asset di Servizi multimediali](./media/continuous-video-recording-tutorial/assets.png)
1. L'elenco conterrà un asset denominato sampleAsset-CVRToAMSAsset-Sample-Graph-1. Si tratta del modello di denominazione scelto nel file della topologia del grafo.
1. Selezionare l'asset.
1. Nella pagina dei dettagli dell'asset fare clic su **Crea nuovo** sotto la casella di testo **URL di streaming**.

    ![Nuovo asset](./media/continuous-video-recording-tutorial/new-asset.png)

1. Nella procedura guidata accettare le opzioni predefinite e selezionare **Aggiungi**. Per altre informazioni, vedere [Riproduzione di video](video-playback-concept.md).

    > [!TIP]
    > Assicurarsi che [l'endpoint di streaming sia in esecuzione](../latest/streaming-endpoint-concept.md).
1. Il lettore dovrebbe caricare il video. Selezionare **Riproduci** per visualizzarlo.

> [!NOTE]
> Poiché l'origine del video è un contenitore che simula un feed della videocamera, i timestamp nel video sono correlati al momento in cui è stata attivata l'istanza del grafo e al momento in cui è stata disattivata. Per informazioni su come esplorare una registrazione di più giorni e visualizzare parti di tale archivio, vedere l'esercitazione [Riproduzione di registrazioni di più giorni](playback-multi-day-recordings-tutorial.md). In questa esercitazione sarà anche possibile vedere i timestamp nel video visualizzato sullo schermo.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende provare le altre esercitazioni, mantenere le risorse create. In caso contrario, passare al portale di Azure, passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stata eseguita l'esercitazione ed eliminare il gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi

* Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP anziché il simulatore RTSP. È possibile cercare le videocamere IP con supporto RTSP nella [pagina dei prodotti conformi ONVIF](https://www.onvif.org/conformant-products/) cercando i dispositivi conformi ai profili G, S o T.
* Usare un dispositivo AMD64 o X64 Linux (anziché una macchina virtuale Linux di Azure). Il dispositivo deve trovarsi nella stessa rete della videocamera IP. Seguire le istruzioni riportate in [Installare il runtime Azure IoT Edge in Linux](../../iot-edge/how-to-install-iot-edge.md). Quindi seguire le istruzioni dell'argomento di avvio rapido [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](../../iot-edge/quickstart-linux.md) per registrare il dispositivo con l'hub IoT di Azure.