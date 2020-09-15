---
ms.openlocfilehash: f2724a0ea0aa5f609be5847652973cfa03658c24
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421526"
---
### <a name="examine-and-edit-the-sample-files"></a>Esaminare e modificare i file di esempio

Come parte dei prerequisiti, il codice di esempio è stato scaricato in una cartella. Seguire questa procedura per esaminare e modificare i file di esempio.

1. In Visual Studio Code passare a *src/edge*. Si noteranno il file *.env* e alcuni file modello di distribuzione.

    Il file deployment.grpcyolov3icpu.template.json fa riferimento al manifesto della distribuzione per il dispositivo perimetrale. Include alcuni valori segnaposto. Il file .env include i valori per queste variabili.
1. Passare alla cartella *src/cloud-to-device-console-app*. Sono inclusi il file *appsettings.json* e alcuni altri file:
    
    * operations.json: un elenco di operazioni che il programma dovrà eseguire.
    * main.py: il codice del programma di esempio. Questo codice:

        * Carica le impostazioni dell'app.
        * Richiama i metodi diretti esposti dal modulo Analisi video live in IoT Edge. È possibile usare il modulo per analizzare i flussi video live richiamando i metodi diretti.
        * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra **TERMINAL** e gli eventi generati dal modulo nella finestra **OUTPUT**.
        * Richiama i metodi diretti per pulire le risorse.
1. Modificare il file *operations.json*:
 
    * Cambiare il collegamento alla topologia del grafo:
    * `"topologyUrl"` : `"https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/grpcExtension/topology.json"`
    * In GraphInstanceSet modificare il nome della topologia del grafo in modo che corrisponda al valore nel collegamento precedente:
    * `"topologyName"` : `"InferencingWithGrpcExtension"`
    * In GraphTopologyDelete modificare il nome:
    * `"name"` : `"InferencingWithGrpcExtension"`

> [!NOTE]
> <p>
> <details>
> <summary>Espandere questa opzione per verificare come viene implementato il nodo MediaGraphGrpcExtension nella topologia</summary>
> <pre><code>
> {
>   "@type": "#Microsoft.Media.MediaGraphGrpcExtension",
>   "name": "grpcExtension",
>   "endpoint": {
>       "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
>       "url": "${grpcExtensionAddress}",
>       "credentials": {
>           "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
>           "username": "${grpcExtensionUserName}",
>           "password": "${grpcExtensionPassword}"
>       }
>   },
>   "dataTransfer": {
>       "mode": "sharedMemory",
>       "SharedMemorySizeMiB": "5"
>   },
>   "image": {
>       "scale": {
>           "mode": "${imageScaleMode}",
>           "width": "${frameWidth}",
>           "height": "${frameHeight}"
>       },
>       "format": {
>           "@type": "#Microsoft.Media.MediaGraphImageFormatEncoded",
>           "encoding": "${imageEncoding}",
>           "quality": "${imageQuality}"
>       }
>   },
>   "inputs": [
>       {
>           "nodeName": "motionDetection"
>       }
>   ]
> }          
> </code></pre>
> </details>    
> </p>
    
### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione di IoT Edge

1. Fare clic con il pulsante destro del mouse sul file *src/edge/* *deployment.grpcyolov3icpu.template.json* e quindi selezionare **Generate IoT Edge Deployment Manifest** (Genera manifesto della distribuzione di Azure IoT Edge).

    ![Generate IoT Edge Deployment Manifest (Genera manifesto della distribuzione di IoT Edge)](../../../media/quickstarts/generate-iot-edge-deployment-manifest-grpc.png)

    Il file manifesto *deployment.grpcyolov3icpu.amd64.json* viene creato nella cartella *src/edge/config*.
1. Se è stato completato l'argomento di avvio rapido [Rilevare il movimento e generare eventi](../../../detect-motion-emit-events-quickstart.md), ignorare questo passaggio.

    In caso contrario, accanto al riquadro **HUB IOT DI AZURE** nell'angolo in basso a sinistra selezionare l'icona **Altre azioni** e quindi selezionare **Set IoT Hub Connection String** (Imposta la stringa di connessione dell'hub IoT). È possibile copiare la stringa dal file *appsettings.json*. In alternativa, per assicurarsi di aver configurato l'hub IoT appropriato in Visual Studio Code, usare il comando [Selezionare un hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).

    ![Stringa di connessione dell'hub IoT](../../../media/quickstarts/iot-hub-connection-string-grpc.png)
1. Fare clic con il pulsante destro del mouse sul file *src/edge/config/* *deployment.grpcyolov3icpu.amd64.json* e selezionare **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

    ![Create Deployment For Single Device (Crea la distribuzione per un unico dispositivo)](../../../media/quickstarts/create-deployment-single-device-grpc.png)
1. Quando viene chiesto di selezionare un dispositivo hub IoT, selezionare **lva-sample-device**.
1. Dopo circa 30 secondi, nell'angolo in basso a sinistra della finestra aggiornare l'hub IoT di Azure. Il dispositivo Edge mostra ora i moduli distribuiti seguenti:

    * Il modulo Analisi video live, denominato **lvaEdge**.
    * Il modulo **rtspsim**, che simula un server RTSP e funge da origine di un feed video live.

        > [!NOTE]
        > Se si usa un dispositivo perimetrale personalizzato invece del dispositivo sottoposto a provisioning dallo script di configurazione, passare al dispositivo perimetrale ed eseguire i comandi seguenti con **diritti di amministratore** per eseguire il pull e archiviare il file del video di esempio usato per questo avvio rapido:  

        ```
        mkdir /home/lvaadmin/samples
        mkdir /home/lvaadmin/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
        chown -R lvaadmin /home/lvaadmin/samples/  
        ```
    * Il modulo **lvaExtension**, ovvero il modello YOLOv3 di rilevamento oggetti che usa gRPC come metodo di comunicazione, applica la visione artificiale alle immagini e restituisce più classi di tipi di oggetto.
    
    ![lvaExtension](../../../media/quickstarts/lvaextension-grpc.png)

### <a name="prepare-to-monitor-events"></a>Preparare il monitoraggio degli eventi

Fare clic con il pulsante destro del mouse sul dispositivo di Analisi video live e scegliere **Avvia monitoraggio endpoint eventi predefinito**. Questo passaggio è necessario per monitorare gli eventi dell'hub IoT nella finestra **OUTPUT** di Visual Studio Code.

![Avviare il monitoraggio](../../../media/quickstarts/start-monitoring-built-event-endpoint-grpc.png)

### <a name="run-the-sample-program"></a>Eseguire il programma di esempio

1. Per avviare la sessione di debug, premere F5. Verranno visualizzati i messaggi stampati nella finestra TERMINALE.
1. Il codice *operations.json* verrà avviato con le chiamate ai metodi diretti `GraphTopologyList` e `GraphInstanceList`. Se dopo aver completato gli argomenti di avvio rapido precedenti sono state pulite le risorse, questa procedura restituirà elenchi vuoti e quindi verrà sospesa. Per continuare, premere INVIO.
    
    ```
    -------------------------------Executing operation GraphTopologyList-----------------------  
    Request: GraphTopologyList  --------------------------------------------------
    {
    "@apiVersion": "1.0"
    }
    ---------------  
    Response: GraphTopologyList - Status: 200  ---------------
    {
    "value": []
    }
    --------------------------------------------------------------------------
    Executing operation WaitForInput
    Press Enter to continue
    ```
    
    La finestra **TERMINALE** mostra il set successivo di chiamate ai metodi diretti:
    
    * Una chiamata a `GraphTopologySet` che usa l'elemento topologyUrl precedente
    * Una chiamata a `GraphInstanceSet` che usa il corpo seguente:
    
    ```
    {
      "@apiVersion": "1.0",
      "name": "Sample-Graph-1",
      "properties": {
        "topologyName": "InferencingWithGrpcExtension",
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
    
    * Una chiamata a `GraphInstanceActivate` che avvia l'istanza del grafo e il flusso del video.
    * Una seconda chiamata a `GraphInstanceList` che mostra che l'istanza del grafo è in esecuzione.
1. L'output nella finestra **TERMINALE** viene sospeso in corrispondenza della richiesta Premere INVIO per continuare. Non premere ancora INVIO. Scorrere in alto per visualizzare i payload della risposta JSON per i metodi diretti richiamati.
1. Passare alla finestra **OUTPUT** in Visual Studio Code. Vengono visualizzati i messaggi inviati dal modulo Analisi video live in IoT Edge all'hub IoT. La sezione seguente di questo argomento di avvio rapido descrive questi messaggi.
1. Il grafo multimediale continua a essere eseguito e a stampare i risultati. Il simulatore RTSP continua a eseguire il video di origine in un ciclo. Quindi, per arrestare il grafo multimediale, tornare nella finestra **TERMINALE** e premere INVIO.
1. La serie successiva di chiamate pulisce le risorse:
    
    * Una chiamata a `GraphInstanceDeactivate` disattiva l'istanza del grafo.
    * Una chiamata a `GraphInstanceDelete` elimina l'istanza.
    * Una chiamata a `GraphTopologyDelete` elimina la topologia.
    * Una chiamata finale a `GraphTopologyList` mostra che l'elenco è vuoto.

