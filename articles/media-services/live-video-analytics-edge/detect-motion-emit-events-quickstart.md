---
title: Rilevare il movimento e generare eventi - Azure
description: Questo avvio rapido illustra come usare Live Video Analytics in IoT Edge per rilevare il movimento e generare eventi mediante la chiamata diretta dei metodi a livello di codice.
ms.topic: quickstart
ms.date: 05/29/2020
ms.openlocfilehash: 69486515125c624b3ef5d44aba6e6d8f7694a3cc
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/16/2020
ms.locfileid: "84816699"
---
# <a name="quickstart-detect-motion-and-emit-events"></a>Avvio rapido: Rilevare il movimento e generare eventi

Questo avvio rapido illustra la procedura per iniziare a usare Live Video Analytics in IoT Edge. Usa una macchina virtuale di Azure come dispositivo IoT Edge e un flusso video live simulato. Dopo aver completato i passaggi di configurazione, sarà possibile eseguire un flusso video live simulato tramite un grafo multimediale che rileva e segnala qualsiasi movimento in tale flusso. Il diagramma seguente mostra una rappresentazione grafica del grafo multimediale.

![Analisi di video live basata su un rilevamento del movimento](./media/analyze-live-video/motion-detection.png) 

Questo articolo è basato su [codice di esempio](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp) scritto in C#.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non si ha un account, [crearne uno gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) con le estensioni seguenti:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). 

> [!TIP]
> È possibile che venga chiesto di installare Docker durante l'installazione dell'estensione Azure IoT Tools. È possibile ignorare questa richiesta.

## <a name="set-up-azure-resources"></a>Configurare le risorse di Azure

Per questa esercitazione sono necessarie le risorse di Azure seguenti:

* Hub IoT
* Account di archiviazione
* Account Servizi multimediali di Azure
* Macchina virtuale Linux in Azure, con [runtime IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) installato

Per questo argomento di avvio rapido è consigliabile usare lo [script di configurazione delle risorse di Analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup) per distribuire le risorse necessarie nella sottoscrizione di Azure. A questo scopo, attenersi alla procedura seguente:

1. Aprire [Azure Cloud Shell](https://shell.azure.com).
1. Se si usa Cloud Shell per la prima volta, verrà chiesto di selezionare una sottoscrizione, oltre che di creare un account di archiviazione e una condivisione di File di Microsoft Azure. Selezionare **Crea risorsa di archiviazione** per creare un account di archiviazione per le informazioni della sessione Cloud Shell. Questo account di archiviazione è distinto da quello che verrà creato dallo script per l'uso con l'account di Servizi multimediali di Azure.
1. Nel menu a discesa sul lato sinistro della finestra di Cloud Shell selezionare **Bash** come ambiente.

    ![Selezione dell'ambiente](./media/quickstarts/env-selector.png)

1. Eseguire il comando seguente.

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```

    Se lo script viene completato correttamente, verranno visualizzate tutte le risorse necessarie nella sottoscrizione.

1. Al termine dello script, selezionare le parentesi graffe per esporre la struttura della cartella. Vengono visualizzati alcuni file nella directory *~/clouddrive/lva-sample*. I file rilevanti per questo avvio rapido sono:

     * ***~/clouddrive/lva-sample/edge-deployment/.env*** - Questo file contiene le proprietà usate da Visual Studio Code per distribuire i moduli in un dispositivo perimetrale.
     * ***~/clouddrive/lva-sample/appsetting.json*** - Visual Studio Code usa questo file per l'esecuzione del codice di esempio.
     
Questi file saranno necessari per configurare l'ambiente di sviluppo in Visual Studio Code nella sezione successiva. È consigliabile copiarli in un file locale per il momento.

 ![Impostazioni app](./media/quickstarts/clouddrive.png)

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

1. Clonare il repository da questo percorso: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. In Visual Studio Code aprire la cartella in cui è stato scaricato il repository.
1. In Visual Studio Code passare alla cartella *src/cloud-to-device-console-app*. In questa cartella creare un file e assegnargli il nome *appsettings.json*. Questo file conterrà le impostazioni necessarie per eseguire il programma.
1. Copiare il contenuto dal file *~/clouddrive/lva-sample/appsettings.json* generato in precedenza in questo argomento di avvio rapido.

    Il testo sarà simile all'output seguente.

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Passare alla cartella *src/edge* e creare un file denominato *.env*.
1. Copiare il contenuto del file */clouddrive/lva-sample/edge-deployment/.env*. Il testo sarà simile al codice seguente.

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry username>"      
    ```

## <a name="examine-the-sample-files"></a>Esaminare i file di esempio

1. In Visual Studio Code passare a *src/edge*. Verranno visualizzati il file *.env* e alcuni file modello di distribuzione.

    Il modello di distribuzione fa riferimento al manifesto della distribuzione per il dispositivo perimetrale, in cui per alcune proprietà vengono usate delle variabili. Il file *.env* contiene i valori per queste variabili.
1. Passare alla cartella *src/cloud-to-device-console-app*. Sono inclusi il file *appsettings.json* e alcuni altri file:

    * ***c2d-console-app.csproj***: il file di progetto per Visual Studio Code.
    * ***operations.json***: un elenco di operazioni che il programma dovrà eseguire.
    * ***Program.cs***: il codice del programma di esempio. Questo codice:
    
      * Carica le impostazioni dell'app.
      * Richiama i metodi diretti esposti dal modulo Analisi video live in IoT Edge. È possibile usare il modulo per analizzare i flussi video live richiamando i [metodi diretti](direct-methods.md).
      * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra **TERMINALE** e gli eventi generati dal modulo nella finestra **OUTPUT**.
      * Richiama i metodi diretti per pulire le risorse.   

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione

Il manifesto della distribuzione definisce i moduli che vengono distribuiti in un dispositivo perimetrale e le impostazioni di configurazione dei moduli. 

Seguire questa procedura per generare il manifesto dal file modello e quindi distribuirlo nel dispositivo perimetrale.

1. Aprire Visual Studio Code.
1. Accanto al riquadro **HUB IOT DI AZURE** selezionare l'icona **Altre azioni** per impostare la stringa di connessione dell'hub IoT. È possibile copiare la stringa dal file *src/cloud-to-device-console-app/appsettings.json*. 

    ![Impostare la stringa di connessione IoT](./media/quickstarts/set-iotconnection-string.png)

1. Fare clic con il pulsante destro del mouse su **src/edge/deployment.template.json** e scegliere **Generate IoT Edge Deployment Manifest** (Genera manifesto della distribuzione di IoT Edge).

    ![Generate IoT Edge Deployment Manifest (Genera manifesto della distribuzione di IoT Edge)](./media/quickstarts/generate-iot-edge-deployment-manifest.png)

    Questa azione crea un file manifesto denominato *deployment.amd64.json* nella cartella *src/edge/config*.
1. Fare clic con il pulsante destro del mouse su **src/edge/config/deployment.amd64.json**, scegliere **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo) e quindi selezionare il nome del dispositivo perimetrale.

    ![Creare una distribuzione per un singolo dispositivo](./media/quickstarts/create-deployment-single-device.png)

1. Quando viene chiesto di selezionare un dispositivo hub IoT, scegliere **lva-sample-device** dal menu a discesa.
1. Dopo circa 30 secondi, nell'angolo in basso a sinistra della finestra aggiornare l'hub IoT di Azure. Il dispositivo perimetrale mostra ora i moduli distribuiti seguenti:

    * Analisi video live in IoT Edge (nome del modulo `lvaEdge`)
    * Simulatore RTSP (Real-Time Streaming Protocol) (nome del modulo `rtspsim`)

Il modulo del simulatore RTSP simula un flusso video live usando un file video copiato nel dispositivo perimetrale quando è stato eseguito lo [script di configurazione delle risorse di Analisi video live](https://github.com/Azure/live-video-analytics/tree/master/edge/setup). 

A questo punto i moduli sono distribuiti ma non sono disponibili grafi multimediali attivi.

## <a name="prepare-to-monitor-events"></a>Preparare il monitoraggio degli eventi

Si userà il modulo di Analisi video live in IoT Edge per rilevare il movimento nel flusso video live in ingresso e inviare eventi all'hub IoT. Per visualizzare gli eventi, seguire questa procedura:

1. Aprire il riquadro Explorer in Visual Studio Code e cercare Hub IoT di Azure nell'angolo in basso a sinistra.
1. Espandere il nodo **Dispositivi**.
1. Fare clic con il pulsante destro del mouse sul file **lva-sample-device** e scegliere **Avvia il monitoraggio endpoint eventi predefinito**.

    ![Avvia monitoraggio endpoint eventi predefinito](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Eseguire il programma di esempio

Per eseguire il codice di esempio, seguire questa procedura:

1. In Visual Studio Code passare a *src/cloud-to-device-console-app/operations.json*.
1. Nel nodo **GraphTopologySet** assicurarsi che sia impostato il valore seguente:

    `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/motion-detection/topology.json"`
1. Nei nodi **GraphInstanceSet** e **GraphTopologyDelete** assicurarsi che il valore di `topologyName` corrisponda al valore della proprietà `name` nella topologia del grafo:

    `"topologyName" : "MotionDetection"`
    
1. Avviare una sessione di debug premendo F5. Nella finestra **TERMINALE** verranno visualizzati alcuni messaggi.
1. Il file *operations.json* verrà avviato con le chiamate a `GraphTopologyList` e `GraphInstanceList`. Se dopo aver completato gli argomenti di avvio rapido precedenti sono state pulite le risorse, questa procedura restituirà elenchi vuoti e quindi verrà sospesa. Per continuare, premere INVIO.

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
           "name": "Sample-Graph",
           "properties": {
             "topologyName": "MotionDetection",
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
1. L'output della finestra **TERMINALE** viene sospeso in corrispondenza di `Press Enter to continue`. Non premere ancora INVIO. Scorrere in alto per visualizzare i payload della risposta JSON per i metodi diretti richiamati.
1. Passare alla finestra **OUTPUT** in Visual Studio Code. Vengono visualizzati i messaggi inviati dal modulo Analisi video live in IoT Edge all'hub IoT. La sezione seguente di questo argomento di avvio rapido descrive questi messaggi.
1. Il grafo multimediale continua a essere eseguito e a visualizzare i risultati. Il simulatore RTSP continua a eseguire il video di origine in un ciclo. Quindi, per arrestare il grafo multimediale, tornare nella finestra **TERMINALE** e premere INVIO. 

    La serie successiva di chiamate pulisce le risorse:
     * Una chiamata a `GraphInstanceDeactivate` disattiva l'istanza del grafo.
     * Una chiamata a `GraphInstanceDelete` elimina l'istanza.
     * Una chiamata a `GraphTopologyDelete` elimina la topologia.
     * Una chiamata finale a `GraphTopologyList` mostra che l'elenco è vuoto.

## <a name="interpret-results"></a>Interpretare i risultati

Quando si esegue il grafo multimediale, i risultati dal nodo del processore di rilevamento dei movimenti vengono inviati all'hub IoT passando attraverso il nodo sink dell'hub IoT. I messaggi visualizzati nella finestra **OUTPUT** di Visual Studio Code contengono una sezione `body` e una sezione `applicationProperties`. Per altre informazioni, vedere [Creare e leggere messaggi dell'hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct).

Nei messaggi seguenti il modulo Analisi video live definisce le proprietà dell'applicazione e il contenuto del corpo.

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando viene creata un'istanza del grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore rtspsim-live555. Se la connessione riesce, viene visualizzato l'evento seguente.

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
* La sezione `body` contiene dati sull'evento di diagnostica. In questo caso, i dati sono costituiti dai dettagli di [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).


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
    
## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende provare gli altri argomenti di avvio rapido, non eliminare le risorse create. In caso contrario, nel portale di Azure passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stato eseguito questo argomento di avvio rapido e quindi eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

Eseguire gli altri argomenti di avvio rapido, ad esempio quello relativo al rilevamento di un oggetto in un feed video live.        
