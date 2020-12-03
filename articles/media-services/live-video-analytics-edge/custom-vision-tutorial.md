---
title: Analizzare video live con Analisi video live in IoT Edge e Visione personalizzata di Azure
description: Informazioni su come usare Visione personalizzata di Azure per creare un modello in contenitori in grado di rilevare un camion giocattolo e usare la funzionalità di estendibilità dell'intelligenza artificiale di Analisi video live di Azure in Azure IoT Edge per distribuire il modello nel dispositivo Edge per rilevare i veicoli giocattolo da un flusso video live.
ms.topic: tutorial
ms.date: 09/08/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: b4d9f82d99542bde216f0eaa1459d0f6c1a52659
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498337"
---
# <a name="tutorial-analyze-live-video-with-live-video-analytics-on-iot-edge-and-azure-custom-vision"></a>Esercitazione: Analizzare video live con Analisi video live in IoT Edge e Visione personalizzata di Azure

In questa esercitazione viene illustrato come usare [Visione personalizzata](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) di Azure per creare un modello in contenitori in grado di rilevare un camion giocattolo e usare la [funzionalità di estendibilità dell'intelligenza artificiale](analyze-live-video-concept.md#analyzing-video-using-a-custom-vision-model) di Analisi video live di Azure in Azure IoT Edge per distribuire il modello nel dispositivo Edge per rilevare i veicoli giocattolo da un flusso video live.

Verrà spiegato come sfruttare le potenzialità di Visione personalizzata per creare ed eseguire il training di un modello di visione artificiale caricando ed etichettando alcune immagini, senza che sia necessaria alcuna conoscenza di data science, Machine Learning o intelligenza artificiale. Verranno illustrate anche le funzionalità di Analisi video live per distribuire facilmente un modello personalizzato come contenitore nel dispositivo Edge e analizzare un feed video live simulato.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/custom-vision-tutorial/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/custom-vision-tutorial/python/header.md)]
::: zone-end

L'esercitazione illustra come:

> [!div class="checklist"]
> * Configurare le risorse pertinenti.
> * Creare un modello di Visione personalizzata nel cloud per rilevare i camion giocattolo e distribuirlo nel dispositivo Edge.
> * Creare e distribuire un grafo multimediale con un'estensione HTTP in un modello di Visione personalizzata.
> * Eseguire il codice di esempio.
> * Esaminare e interpretare i risultati.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Letture consigliate  

Prima di iniziare, leggere gli articoli seguenti:

* [Panoramica di Analisi di video live in IoT Edge](overview.md)
* [Panoramica di Visione personalizzata di Azure](../../cognitive-services/custom-vision-service/overview.md)
* [Terminologia di Analisi di video live in IoT Edge](terminology.md)
* [Concetti del grafo multimediale](media-graph-concept.md)
* [Live Video Analytics senza registrazione video](analyze-live-video-concept.md)
* [Eseguire l'analisi di video live con un modello personalizzato](use-your-model-quickstart.md)
* [Esercitazione: Sviluppo di un modulo IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Come modificare deployment.*.template.json](https://github.com/microsoft/vscode-azure-iot-edge/wiki/How-to-edit-deployment.*.template.json)

## <a name="prerequisites"></a>Prerequisiti


::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/custom-vision-tutorial/python/prerequisites.md)]
::: zone-end
## <a name="review-the-sample-video"></a>Esaminare il video di esempio


Questa esercitazione usa un file [video di inferenza di una vettura giocattolo](https://lvamedia.blob.core.windows.net/public/t2.mkv) per simulare un flusso live. È possibile esaminare il video tramite un'applicazione come il [lettore multimediale VLC](https://www.videolan.org/vlc/). Premere **CTRL+N**, quindi incollare un collegamento al [video di inferenza di una vettura giocattolo](https://lvamedia.blob.core.windows.net/public/t2.mkv) per avviare la riproduzione. Si noti che, in corrispondenza del trentaseiesimo secondo della riproduzione, nel video compare un camion giocattolo. Il modello personalizzato è stato sottoposto a training per rilevare questo camion giocattolo specifico. In questa esercitazione si userà Analisi video live in IoT Edge per rilevare il camion giocattolo e pubblicare gli eventi di inferenza associati nell'hub IoT Edge.

## <a name="overview"></a>Panoramica

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/topology-custom-vision.svg" alt-text="Diagramma di panoramica di Visione personalizzata.":::

Il diagramma mostra il flusso dei segnali in questa esercitazione. Un [modulo Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula una videocamera IP che ospita un server RTSP (Real-Time Streaming Protocol). Un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i fotogrammi video al nodo del [processore di filtro della frequenza dei fotogrammi](media-graph-concept.md#frame-rate-filter-processor). Questo processore limita la frequenza dei fotogrammi dello streaming video che raggiunge il nodo del [processore di estensioni HTTP](media-graph-concept.md#http-extension-processor).

Il nodo di estensioni HTTP svolge il ruolo di proxy. Converte i fotogrammi video nel tipo di immagine specificato. Quindi inoltra l'immagine su REST a un altro modulo Edge che esegue un modello di intelligenza artificiale dietro un endpoint HTTP. In questo esempio, il modulo Edge è il modello di rilevamento del camion giocattolo creato tramite Visione personalizzata. Il nodo del processore di estensioni HTTP raccoglie i risultati del rilevamento e pubblica gli eventi nel nodo [sink dell'hub IoT di Azure](media-graph-concept.md#iot-hub-message-sink). Il nodo invia quindi gli eventi all'[hub di IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub).

## <a name="build-and-deploy-a-custom-vision-toy-detection-model"></a>Creare e distribuire un modello di rilevamento del giocattolo di Visione personalizzata 

Come suggerisce il nome stesso, è possibile usare Visione personalizzata per creare un rilevatore o un classificatore di oggetti personalizzato nel cloud. La sua interfaccia semplice e intuitiva consente di creare facilmente modelli di Visione personalizzata che possono essere distribuiti nel cloud o nel dispositivo Edge tramite contenitori.

Per creare un rilevatore del camion giocattolo, seguire la procedura descritta in [Avvio rapido: Creare un rilevatore di oggetti con il sito Web di Visione personalizzata](../../cognitive-services/custom-vision-service/get-started-build-detector.md).

Note aggiuntive:
 
* Per questa esercitazione non usare le immagini di esempio fornite nella [sezione Prerequisiti](../../cognitive-services/custom-vision-service/get-started-build-detector.md#prerequisites) dell'argomento di avvio rapido. Per creare un modello di rilevamento del giocattolo di Visione personalizzata è stato usato un set di immagini specifico. Usare [queste immagini](https://lvamedia.blob.core.windows.net/public/ToyCarTrainingImages.zip) quando viene chiesto di [scegliere le immagini di training](../../cognitive-services/custom-vision-service/get-started-build-detector.md#choose-training-images) nell'avvio rapido.
* Nella sezione dell'avvio rapido relativa all'aggiunta di tag alle immagini assicurarsi di contrassegnare il camion giocattolo visualizzato nell'immagine con il tag "delivery truck".

Al termine, è possibile esportare il modello in un contenitore Docker usando il pulsante **Esporta** nella scheda **Prestazioni**. Scegliere Linux come tipo di piattaforma del contenitore. È la piattaforma in cui verrà eseguito il contenitore. Il computer in cui si scarica il contenitore può essere Windows o Linux. Le istruzioni seguenti sono basate sul file contenitore scaricato in un computer Windows.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/docker-file.png" alt-text="Schermata che mostra il Dockerfile selezionato.":::
 
1. Dovrebbe essere presente un file ZIP denominato `<projectname>.DockerFile.Linux.zip` scaricato nel computer locale. 
1. Verificare che Docker sia installato. Se non lo è, installare [Docker](https://docs.docker.com/get-docker/) per il desktop di Windows.
1. Decomprimere il file scaricato in un percorso a scelta. Usare la riga di comando per passare alla directory della cartella decompressa.
    
    Eseguire i comandi seguenti:
    
    1. `docker build -t cvtruck` 
    
        Questo comando scarica diversi pacchetti, crea l'immagine Docker e vi aggiunge il tag `cvtruck:latest`.
    
        > [!NOTE]
        > Se il comando riesce, si dovrebbero vedere i messaggi seguenti: `Successfully built <docker image id>` e `Successfully tagged cvtruck:latest`. Se il comando di compilazione non riesce, riprovare. Può infatti capitare che i pacchetti di dipendenze non vengano scaricati la prima volta.
    1. `docker  image ls`

        Questo comando verifica se la nuova immagine si trova nel Registro di sistema locale.
    1. `docker run -p 127.0.0.1:80:80 -d cvtruck`
    
        Questo comando dovrebbe pubblicare la porta esposta di Docker (80) sulla porta del computer locale (80).
    1. `docker container ls`
    
        Questo comando controlla i mapping delle porte e verifica la corretta esecuzione del contenitore Docker nel computer. L'output dovrebbe essere simile al seguente:

        ```
        CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                      NAMES
        8b7505398367        cvtruck             "/bin/sh -c 'python …"   13 hours ago        Up 25 seconds       127.0.0.1:80->80/tcp   practical_cohen
        ```
      1. `curl -X POST http://127.0.0.1:80/image -F imageData=@<path to any image file that has the toy delivery truck in it>`
            
            Questo comando testa il contenitore nel computer locale. Se l'immagine contiene lo stesso camion per le consegne su cui è stato eseguito il training del modello, l'output dovrebbe essere simile all'esempio seguente. Suggerisce che il camion è stato rilevato con una probabilità del 90,12%.
    
            ```
            {"created":"2020-03-20T07:10:47.827673","id":"","iteration":"","predictions":[{"boundingBox":{"height":0.66167289,"left":-0.03923762,"top":0.12781593,"width":0.70003178},"probability":0.90128148,"tagId":0,"tagName":"delivery truck"},{"boundingBox":{"height":0.63733053,"left":0.25220079,"top":0.0876643,"width":0.53331227},"probability":0.59745145,"tagId":0,"tagName":"delivery truck"}],"project":""}
            ```

<!--## Create and deploy a media graph with http extension to custom vision model-->

## <a name="examine-the-sample-files"></a>Esaminare i file di esempio


::: zone pivot="programming-language-csharp"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/csharp/examine-sample-files.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [examine-sample-files](includes/custom-vision-tutorial/python/examine-sample-files.md)]
::: zone-end

## <a name="generate-and-deploy-the-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione

1. In Visual Studio Code passare a src/cloud-to-device-console-app/operations.json.

1. In `GraphTopologySet` verificare quanto segue:<br/>`"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/topology.json"`
1. In `GraphInstanceSet` verificare quanto segue:
    1. `"topologyName" : "InferencingWithHttpExtension"`
    1. Aggiungere il codice seguente all'inizio della matrice di parametri: `{"name": "inferencingUrl","value": "http://cv:80/image"},`
    1. Cambiare il valore del parametro `rtspUrl` in `"rtsp://rtspsim:554/media/t2.mkv"`.
1. In `GraphTopologyDelete` verificare che `"name": "InferencingWithHttpExtension"`.
1. Fare clic con il pulsante destro del mouse sul file src/edge/ deployment.customvision.template.json e scegliere **Generate IoT Edge Deployment Manifest** (Genera manifesto della distribuzione di IoT Edge).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-template-json.png" alt-text="Screenshot del comando Generate IoT Edge Deployment Manifest (Genera manifesto della distribuzione di IoT Edge).":::
  
    Viene creato un file manifesto denominato deployment.customvision.amd64.json nella cartella src/edge/config.
1. Aprire il file src/edge/ deployment.customvision.template.json e trovare il blocco di codice JSON `registryCredentials`. Questo blocco contiene l'indirizzo del registro contenitori di Azure, insieme al nome utente e alla password.
1. Usare la riga di comando per eseguire il push del contenitore di Visione personalizzata locale nell'istanza di Registro Azure Container seguendo questa procedura:

    1. Accedere al registro eseguendo il comando seguente:
    
        `docker login <address>`
    
        Quando richiesti per l'autenticazione, immettere il nome utente e la password.
        
        > [!NOTE]
        > La password non è visibile nella riga di comando.
    1. Aggiungere tag all'immagine usando questo comando: <br/>`docker tag cvtruck   <address>/cvtruck`.
    1. Eseguire il push dell'immagine con questo comando: <br/>`docker push <address>/cvtruck`.

        Se l'operazione riesce, nella riga di comando dovrebbe essere visualizzato `Pushed` insieme al SHA dell'immagine.
    1. È possibile verificarlo anche controllando l'istanza di Registro Azure Container nel portale di Azure. Qui si vedranno il nome del repository e il tag.
1. Impostare la stringa di connessione dell'hub IoT facendo clic sull'icona **Altre azioni** accanto al riquadro **HUB IOT DI AZURE** nell'angolo in basso a sinistra. È possibile copiarle la stringa dal file appsettings.json. Ecco un altro approccio consigliato per assicurarsi di avere configurato l'hub IoT appropriato in Visual Studio Code tramite il [comando Selezionare un hub IoT](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/connection-string.png" alt-text="Screenshot con il comando Set IoT Hub Connection String (Imposta la stringa di connessione dell'hub IoT) evidenziato.":::
1. Quindi, fare clic con il pulsante destro del mouse su src/edge/config/ deployment.customvision.amd64.json e scegliere **Create Deployment for Single Device** (Crea la distribuzione per un unico dispositivo).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-vision-tutorial/deployment-amd64-json.png" alt-text="Screenshot con il comando Create Deployment for Single Device (Crea la distribuzione per un unico dispositivo) evidenziato.":::
1. Verrà chiesto di selezionare un dispositivo hub IoT. Selezionare **lva-sample-device** nell'elenco a discesa.
1. Dopo circa 30 secondi aggiornare l'hub IoT di Azure nella sezione in basso a sinistra. Dovrebbe essere presente il dispositivo Edge con i moduli seguenti distribuiti:

    * Il modulo di Analisi video live in IoT Edge con nome `lvaEdge`.
    * Un modulo denominato `rtspsim` che simula un server RTSP che funge da origine di un feed di video live.
    * Un modulo denominato `cv` che, come suggerisce il nome, è il modello di rilevamento del camion giocattolo di Visione personalizzata che applica Visione personalizzata alle immagini e restituisce più tipi di tag. Per il modello di questa esercitazione è stato eseguito il training in base a un solo tag, delivery truck.

## <a name="prepare-for-monitoring-events"></a>Operazioni preliminari al monitoraggio degli eventi

Fare clic con il pulsante destro del mouse sul dispositivo di Analisi video live e scegliere **Avvia monitoraggio endpoint eventi predefinito**. Questo passaggio è necessario per monitorare gli eventi dell'hub IoT nella finestra **OUTPUT** di Visual Studio Code.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-vision-tutorial/start-monitoring.png" alt-text="Screenshot con il comando Avvia monitoraggio endpoint eventi predefinito evidenziato.":::

## <a name="run-the-sample-program"></a>Eseguire il programma di esempio

Se si apre la topologia del grafo per questa esercitazione in un browser, si vedrà che il valore di `inferencingUrl` è stato impostato su `http://cv:80/image`. Questa impostazione indica che il server di inferenza restituirà i risultati dopo aver rilevato i camion giocattolo, se presenti, nel video live.

1. In Visual Studio Code aprire la scheda **Estensioni** (oppure premere **CTRL+MAIUSC+X**) e cercare Hub IoT di Azure.
1. Fare clic con il pulsante destro del mouse e scegliere **Impostazioni estensione**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Screenshot con il comando Impostazioni estensione evidenziato.":::
1. Cercare e abilitare **Show Verbose Message** (Visualizza messaggio dettagliato).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Screenshot che mostra il comando Show Verbose Message (Visualizza messaggio dettagliato).":::
1. Per avviare una sessione di debug, premere **F5**. Verranno visualizzati i messaggi stampati nella finestra **TERMINALE**.
1. Il codice operations.json verrà avviato con le chiamate ai metodi diretti `GraphTopologyList` e `GraphInstanceList`. Se dopo aver completato gli argomenti di avvio rapido precedenti sono state pulite le risorse, questa procedura restituirà elenchi vuoti e quindi verrà sospesa. Per continuare, premere **INVIO**.
    
   La finestra **TERMINALE** mostra il set successivo di chiamate ai metodi diretti:
    
   * Una chiamata a `GraphTopologySet` che usa il `topologyUrl` precedente.
   * Una chiamata a `GraphInstanceSet` che usa il corpo seguente:
        
   ```
        {
          "@apiVersion": "1.0",
          "name": "Sample-Graph-1",
          "properties": {
            "topologyName": "CustomVisionWithHttpExtension",
            "description": "Sample graph description",
            "parameters": [
              { 
                "name": "inferencingUrl",
                "value": "http://cv:80/image"
              },
              {
                "name": "rtspUrl",
                "value": "rtsp://rtspsim:554/media/t2.mkv"
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
    
1. L'output nella finestra **TERMINALE** viene sospeso alla richiesta **Premere INVIO per continuare**. Non premere ancora **INVIO**. Scorrere in alto per visualizzare i payload della risposta JSON per i metodi diretti richiamati.
1. Passare alla finestra **OUTPUT** in Visual Studio Code. Vengono visualizzati i messaggi inviati dal modulo Analisi video live in IoT Edge all'hub IoT. La sezione seguente di questa esercitazione descrive questi messaggi.
1. Il grafo multimediale continua a essere eseguito e a stampare i risultati. Il simulatore RTSP continua a eseguire il video di origine in un ciclo. Per arrestare il grafo multimediale, tornare nella finestra **TERMINALE** e premere **INVIO**.
La serie successiva di chiamate pulisce le risorse:
    
   * Una chiamata a `GraphInstanceDeactivate` disattiva l'istanza del grafo.
   * Una chiamata a `GraphInstanceDelete` elimina l'istanza.
   * Una chiamata a `GraphTopologyDelete` elimina la topologia.
   * Una chiamata finale a `GraphTopologyList` mostra che l'elenco è vuoto.
    
## <a name="interpret-the-results"></a>Interpretare i risultati

Quando si esegue il grafo multimediale, i risultati del nodo del processore di estensioni HTTP vengono inviati all'hub IoT tramite il nodo sink dell'hub IoT. I messaggi visualizzati nella finestra **OUTPUT** contengono una sezione body e una sezione `applicationProperties`. Per altre informazioni, vedere [Creare e leggere messaggi dell'hub IoT](../../iot-hub/iot-hub-devguide-messages-construct.md).

Nei messaggi seguenti il modulo Analisi video live definisce le proprietà dell'applicazione e il contenuto del corpo.

### <a name="mediasessionestablished-event"></a>Evento MediaSessionEstablished

Quando viene creata un'istanza del grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore rtspsim-live555. Se la connessione riesce, viene visualizzato l'evento seguente. Il tipo di evento è `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`.

```
{
  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1599412849822466 1 IN IP4 172.18.0.3\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/t2.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-78.357\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/t2.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=42C01F;sprop-parameter-sets=Z0LAH9kAUAW6EAAAAwAQAAADAwDxgySA,aMuBcsg=\r\na=control:track1\r\nm=audio 0 RTP/AVP 97\r\nc=IN IP4 0.0.0.0\r\nb=AS:96\r\na=rtpmap:97 MPEG4-GENERIC/44100/2\r\na=fmtp:97 streamtype=5;profile-level-id=1;mode=AAC-hbr;sizelength=13;indexlength=3;indexdeltalength=3;config=121056E500\r\na=control:track2\r\n"
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lvaavi_0827/providers/microsoft.media/mediaservices/lvasampleulf2rv2x5msy2",
    "subject": "/graphInstances/ GRAPHINSTANCENAMEHERE /sources/rtspSource",
    "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-09-06T17:20:49.823Z",
    "dataVersion": "1.0"
  }
```

In questo messaggio si notino i dettagli seguenti:

* Il messaggio è un evento di diagnostica. `MediaSessionEstablished` indica che il nodo di origine RTSP (subject) ha stabilito una connessione con il simulatore RTSP e ha iniziato a ricevere un feed live simulato.
* In `applicationProperties` `subject` indica che il messaggio è stato generato dal nodo di origine RTSP nel grafo multimediale.
* In `applicationProperties`, eventType indica che questo evento è un evento di diagnostica.
* eventTime indica l'ora in cui si è verificato l'evento.
* body contiene dati sull'evento di diagnostica. In questo caso i dati sono costituiti dai dettagli di [SDP (Session Description Protocol)](https://en.wikipedia.org/wiki/Session_Description_Protocol).

### <a name="inference-event"></a>Evento di inferenza

Il nodo del processore dell'estensione HTTP riceve i risultati dell'inferenza dal contenitore di Visione personalizzata e li emette tramite il nodo sink dell'hub IoT come eventi di inferenza.

```
{
  "body": {
    "created": "2020-05-18T01:08:34.483Z",
    "id": "",
    "iteration": "",
    "predictions": [
      {
        "boundingBox": {
          "height": 0.06219418,
          "left": 0.14977954,
          "top": 0.65847444,
          "width": 0.01879117
        },
        "probability": 0.69806677,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.1148454,
          "left": 0.77430711,
          "top": 0.54488315,
          "width": 0.11315252
        },
        "probability": 0.29394844,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.03187029,
          "left": 0.62644471,
          "top": 0.59640052,
          "width": 0.01582896
        },
        "probability": 0.14435098,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.11421723,
          "left": 0.72737214,
          "top": 0.55907888,
          "width": 0.12627538
        },
        "probability": 0.1141128,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.04717135,
          "left": 0.66516746,
          "top": 0.34617995,
          "width": 0.03802613
        },
        "probability": 0.10461298,
        "tagId": 0,
        "tagName": "delivery truck"
      },
      {
        "boundingBox": {
          "height": 0.20650843,
          "left": 0.56349564,
          "top": 0.51482571,
          "width": 0.35045707
        },
        "probability": 0.10056595,
        "tagId": 0,
        "tagName": "delivery truck"
      }
    ],
    "project": ""
  },
  "applicationProperties": {
    "topic": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/lsravi/providers/microsoft.media/mediaservices/lvasamplerc3he6a7uhire",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/httpExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-05-18T01:08:34.038Z"
  }
}
```

Tenere presenti le informazioni seguenti nei messaggi precedenti:

* In `applicationProperties` subject fa riferimento al nodo nel grafo multimediale da cui è stato generato il messaggio. In questo caso il messaggio viene originato dal processore di estensione HTTP.
* eventType in `applicationProperties` indica che si tratta di un evento di inferenza di Analytics.
* eventTime indica l'ora in cui si è verificato l'evento.
* body contiene dati sull'evento di analisi. In questo caso si tratta di un evento di inferenza, quindi il corpo contiene una matrice di inferenze dette stime.
* La sezione predictions contiene un elenco di stime relative al punto di rilevamento di un camion giocattolo (il tag è delivery truck) nel fotogramma. Come si ricorderà, "delivery truck" è il tag personalizzato fornito al modello di training personalizzato per il camion giocattolo. Il modello esegue l'inferenza e identifica il camion giocattolo nel video di input con punteggi di attendibilità diversi per la probabilità.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di provare le altre esercitazioni o guide di avvio rapido, conservare le risorse create. In caso contrario, accedere al portale di Azure, passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stata eseguita l'esercitazione ed eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

Esaminare altri problemi per utenti avanzati:

* Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP invece del simulatore RTSP. È possibile cercare videocamere IP che supportano RTSP nella pagina dei prodotti [conformi a ONVIF](https://www.onvif.org/conformant-products/). Cercare dispositivi conformi ai profili G, S o T.
* Usare un dispositivo Linux AMD64 o x64 invece di una VM Linux di Azure. Il dispositivo deve trovarsi nella stessa rete della videocamera IP. È possibile seguire le istruzioni riportate in [Installare il runtime Azure IoT Edge in Linux](../../iot-edge/how-to-install-iot-edge.md).

Quindi seguire il dispositivo con l'hub IoT di Azure seguendo le istruzioni riportate in [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](../../iot-edge/quickstart-linux.md).