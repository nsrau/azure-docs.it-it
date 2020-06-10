---
title: Rilevare il movimento, registrare video nei dispositivi perimetrali - Azure
description: Questo avvio rapido illustra come usare l'analisi di video live in IoT Edge per analizzare il feed video live da una videocamera IP (simulata), rilevare se è presente un movimento e, in tal caso, registrare un clip video MP4 nel file system locale nel dispositivo perimetrale.
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d824870ea95922bbbdbf01cf2c95692522936f85
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261687"
---
# <a name="quickstart-detect-motion-record-video-on-edge-devices"></a>Avvio rapido: Rilevare il movimento, registrare video nei dispositivi perimetrali
 
Questo avvio rapido illustra come usare l'analisi di video live in IoT Edge per analizzare il feed video live da una videocamera IP (simulata), rilevare se è presente un movimento e, in tal caso, registrare un clip video MP4 nel file system locale del dispositivo perimetrale. Usa una macchina virtuale di Azure come dispositivo IoT Edge e un flusso video live simulato. Questo articolo è basato su codice di esempio scritto in C#.

Questo articolo si basa su [questo](detect-motion-emit-events-quickstart.md) avvio rapido. 

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Visual Studio Code](https://code.visualstudio.com/) nel computer dell'utente con le estensioni seguenti:
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) installato nel sistema
* Se in precedenza non è stato completato [questo](detect-motion-emit-events-quickstart.md) avvio rapido, eseguire la procedura seguente:
     * [Configurare le risorse di Azure](detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     * [Configurare l'ambiente di sviluppo](detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     * [Generare e distribuire il manifesto della distribuzione di IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest)
     * [Operazioni preliminari al monitoraggio degli eventi](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

> [!TIP]
> Quando si installa Azure IoT Tools, può venire richiesto di installare Docker. Ignorare la richiesta.

## <a name="review-the-sample-video"></a>Esaminare il video di esempio
Nell'ambito della procedura precedente per la configurazione delle risorse di Azure, un breve video su un'area di parcheggio verrà copiato nella macchina virtuale Linux in Azure usata come dispositivo IoT Edge. Questo file video verrà usato per simulare un flusso di streaming live per questa esercitazione.

È possibile usare un'applicazione come [VLC Player](https://www.videolan.org/vlc/), avviarla, premere CTRL + N e incollare [questo](https://lvamedia.blob.core.windows.net/public/lots_015.mkv) collegamento al video sull'area di parcheggio per avviare la riproduzione. Quando l'indicazione del tempo è circa a 5 secondi una macchina bianca passa attraverso il parcheggio.

Nel corso della procedura descritta di seguito si userà l'analisi di video live in IoT Edge per rilevare il movimento dell'auto e registrare un clip video iniziando quando l'indicazione del tempo è 5 secondi.

## <a name="overview"></a>Panoramica

![cenni preliminari](./media/quickstarts/overview-qs4.png)

Il diagramma qui sopra illustra il flusso dei segnali in questo avvio rapido. Un modulo Edge (descritto in dettaglio [qui](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) simula una videocamera IP che ospita un server RTSP. Un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i fotogrammi video al nodo del [processore di rilevamento del movimento](media-graph-concept.md#motion-detection-processor). L'origine RTSP invia gli stessi fotogrammi video a un nodo del [processore di controllo del segnale](media-graph-concept.md#signal-gate-processor), che rimane chiuso finché non viene attivato da un evento.

Quando il processore di rilevamento del movimento determina che il movimento è presente nel video, invia un evento al nodo del processore di controllo del segnale attivandolo. Il controllo si apre per il periodo di tempo configurato, inviando i fotogrammi video al nodo [sink di file](media-graph-concept.md#file-sink). Il nodo sink registra il video come file MP4 nel file system locale del dispositivo perimetrale, nel percorso configurato.

Contenuto dell'avvio rapido:

1. Creare e distribuire il grafo multimediale
1. Interpretare i risultati
1. Pulire le risorse

## <a name="examine-and-edit-the-sample-files"></a>Esaminare e modificare i file di esempio
Nell'ambito dei prerequisiti, dovrebbe essere stato scaricato il codice di esempio in una cartella. Avviare Visual Studio Code e aprire la cartella.

1. In Visual Studio Code passare a "src/edge". Verranno visualizzati il file .env creato e alcuni file del modello di distribuzione
    * Il modello di distribuzione fa riferimento al manifesto della distribuzione per il dispositivo perimetrale con alcuni valori segnaposto. Il file .env include i valori per queste variabili.
1. Passare quindi alla cartella "src/cloud-to-device-console-app". Verrà visualizzato il file appsettings.json creato insieme ad altri file:
    * c2d-console-app.csproj: file di progetto per Visual Studio Code
    * operations.json: file che elenca le diverse operazioni che il programma deve eseguire
    * Program.cs: codice del programma di esempio, che esegue le operazioni seguenti:

        * Carica le impostazioni dell'app
        * Richiama i metodi diretti esposti da Live Video Analytics nel modulo IoT Edge. È possibile usare il modulo per analizzare i flussi video live richiamando i [metodi diretti](direct-methods.md) 
        * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra del terminale e gli eventi generati dal modulo nella finestra di output
        * Richiama i metodi diretti per pulire le risorse   

1. Eseguire le modifiche seguenti nel file operations.json
    * Modificare il collegamento alla topologia del grafo: `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/topology.json"`
    * In GraphInstanceSet modificare il nome della topologia del grafo in modo che corrisponda al valore nel collegamento precedente `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Modificare anche l'URL RTSP in modo che punti al file video `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * In GraphTopologyDelete modificare il nome `"name": "EVRToFilesOnMotionDetection"`

## <a name="review---check-status-of-the-modules"></a>Revisione: controllare lo stato dei moduli
Nella procedura di [generazione e distribuzione del manifesto della distribuzione di IoT Edge](detect-motion-emit-events-quickstart.md#generate-and-deploy-the-iot-edge-deployment-manifest) in Visual Studio Code, se si espande il nodo "lva-sample-device" nell'hub IoT di Azure (sezione in basso a sinistra) verranno distribuiti i moduli seguenti

    1. Il modulo di analisi di video live, denominato "lvaEdge"
    1. Un modulo denominato "rtspsim" che simula un server RTSP e funge da origine di un feed video live

        ![Moduli](./media/quickstarts/lva-sample-device-node.png)


## <a name="review---prepare-for-monitoring-events"></a>Revisione: operazioni preliminari al monitoraggio degli eventi
Verificare se sono stati completati i passaggi [preliminari al monitoraggio degli eventi](detect-motion-emit-events-quickstart.md#prepare-for-monitoring-events)

![Avvia monitoraggio endpoint eventi predefinito](./media/quickstarts/start-monitoring-iothub-events.png)

## <a name="run-the-sample-program"></a>Eseguire il programma di esempio

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
1. Quando si preme il tasto "INVIO" nella finestra del terminale, viene eseguita la serie successiva di chiamate ai metodi diretti
     * Una chiamata a GraphTopologySet usando il valore topologyUrl precedente
     * Una chiamata a GraphInstanceSet usando il corpo seguente
     ```
     {
       "@apiVersion": "1.0",
       "name": "Sample-Graph",
       "properties": {
         "topologyName": "EVRToFilesOnMotionDetection",
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
     * Una chiamata a GraphInstanceActivate per avviare l'istanza del grafo e avviare il flusso del video
     * Una seconda chiamata a GraphInstanceList per indicare che l'istanza del grafo è effettivamente nello stato in esecuzione
1. L'output nella finestra del terminale verrà sospeso quando viene visualizzato un prompt 'Premere INVIO per continuare'. Non premere "INVIO". È possibile scorrere verso l'alto per visualizzare i payload di risposta JSON per i metodi diretti richiamati
1. Se ora si passa alla finestra di output in Visual Studio Code, verranno visualizzati i messaggi inviati all'hub IoT dal modulo di Live Video Analytics in IoT Edge.
     * Questi messaggi sono descritti nella sezione seguente
1. L'esecuzione del grafo multimediale continuerà e verranno stampati i risultati: il simulatore RTSP continuerà a eseguire il loop del video di origine. Per arrestare il grafo multimediale, tornare alla finestra del terminale e premere "INVIO". Per pulire le risorse vengono eseguite le serie successive di chiamate:
     * Una chiamata a GraphInstanceDeactivate per disattivare l'istanza del grafo
     * Una chiamata a GraphInstanceDelete per eliminare l'istanza
     * Una chiamata a GraphTopologyDelete per eliminare la topologia
     * Una chiamata finale a GraphTopologyList per indicare che l'elenco è ora vuoto

## <a name="interpret-results"></a>Interpretare i risultati 
Quando si esegue il grafo multimediale, i risultati dal processo di rilevamento dei movimenti vengono inviati all'Hub IoT attraverso il nodo sink dell'Hub IoT. I messaggi visualizzati nella finestra di output di Visual Studio Code contengono una sezione "body" e una sezione "applicationProperties". Per comprendere cosa rappresentano queste sezioni, leggere [questo](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) articolo.

Nei messaggi seguenti le proprietà dell'applicazione e il contenuto del corpo sono definiti dal modulo di Live Video Analytics.

## <a name="mediasession-established-event"></a>Evento MediaSessionEstablished

Quando viene creata un'istanza del grafo multimediale, il nodo di origine RTSP prova a connettersi al server RTSP in esecuzione nel contenitore rtspsim-live555. In caso di esito positivo, viene visualizzato questo evento:

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

* Il messaggio è l'evento di diagnostica MediaSessionEstablished che indica che il nodo di origine RTSP (oggetto) è stato in grado di stabilire una connessione con il simulatore RTSP e iniziare a ricevere un feed live (simulato).
* "subject" in applicationProperties fa riferimento al nodo nella topologia del grafo da cui è stato generato il messaggio. In questo caso il messaggio viene originato dal nodo di origine RTSP.
* "eventType" in applicationProperties indica che si tratta di un evento di diagnostica.
* "eventTime" indica l'ora in cui si è verificato l'evento.
* "body" contiene i dati sull'evento di diagnostica che in questo caso è rappresentato dai dettagli [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol).


## <a name="recording-started-event"></a>Evento di registrazione avviata

Come illustrato [qui](#overview), quando viene rilevato un movimento, il nodo del processore di controllo del segnale viene attivato e il nodo sink di file nel grafo multimediale avvia la scrittura di un file MP4. Il nodo sink di file invia un evento operativo. Il tipo viene impostato su "motion" per indicare che si tratta di un risultato dal processore di rilevamento movimento ed eventTime indica l'ora (UTC) in cui si è verificato il movimento. Di seguito è riportato un esempio:

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

* "subject" in applicationProperties fa riferimento al nodo nel grafo multimediale da cui è stato generato il messaggio. In questo caso il messaggio viene originato dal nodo sink di file.
* "eventType" in applicationProperties indica che si tratta di un evento operativo.
* "eventTime" indica l'ora in cui si è verificato l'evento, 5-6 secondi dopo MediaSessionEstablished, quando viene avviato il flusso video. L'ora corrisponde all'indicazione dei 5-6 secondi dopo i quali l'[auto ha iniziato a muoversi](#review-the-sample-video) nel parcheggio
* "body" contiene i dati relativi all'evento operativo, che in questo caso sono "outputType" e "outputLocation".
* "outputType" indica che queste informazioni riguardano il percorso del file
* "outputLocation" specifica il percorso del file MP4, dall'interno del modulo perimetrale

## <a name="recording-stopped-and-available-events"></a>Eventi di registrazione interrotta e disponibile

Se si esaminano le proprietà del nodo del processore di controllo del segnale nella [topologia del grafo](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json), si noterà che i tempi di attivazione sono stati impostati su 5 secondi. Quindi, circa 5 secondi dopo la ricezione dell'evento RecordingStarted, si riceve un
* evento RecordingStopped, che indica che la registrazione è stata interrotta
* evento RecordingAvailable, che indica che è ora possibile usare il file MP4 per la visualizzazione

I due eventi di solito vengono generati a pochi secondi l'uno dall'altro.

### <a name="playing-back-the-mp4-clip"></a>Riproduzione della clip MP4

1. I file MP4 vengono scritti in una directory del dispositivo perimetrale configurato nel file ENV usando la chiave OUTPUT_VIDEO_FOLDER_ON_DEVICE. Se è stato lasciato il valore predefinito, i risultati devono trovarsi in /home/lvaadmin/samples/output/
1. Passare al gruppo di risorse, individuare la macchina virtuale e connettersi usando Bastion

    ![Resource group](./media/quickstarts/resource-group.png)
 
    ![VM](./media/quickstarts/virtual-machine.png)
1. Effettuato l'accesso (usando le credenziali generate durante [questo](detect-motion-emit-events-quickstart.md#set-up-azure-resources) passaggio), al prompt dei comandi passare alla directory pertinente (impostazione predefinita: /home/lvaadmin/samples/output), che dovrebbe contenere i file MP4. È possibile [spostare i file con SCP](https://docs.microsoft.com/azure/virtual-machines/linux/copy-files-to-linux-vm-using-scp) nel computer locale e riprodurli usando [VLC Player](https://www.videolan.org/vlc/) o qualsiasi altro lettore MP4.

    ![Output](./media/quickstarts/samples-output.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si prevede di provare gli altri avvii rapidi, è necessario mantenere le risorse create. In caso contrario, passare al portale di Azure, passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stato eseguito l'avvio rapido ed eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

* Eseguire l'avvio rapido [Eseguire l'analisi di video live con un modello personalizzato](use-your-model-quickstart.md), che illustra come applicare l'intelligenza artificiale ai feed video live.
* Esaminare altri problemi per utenti avanzati:

    * Usare una [videocamera IP](https://en.wikipedia.org/wiki/IP_camera) con supporto per RTSP anziché il simulatore RTSP. È possibile cercare le fotocamere IP con supporto RTSP nella pagina dei [prodotti conformi ONVIF](https://en.wikipedia.org/wiki/IP_camera) cercando i dispositivi conformi ai profili G, S o T.
    * Usare un dispositivo AMD64 o X64 Linux (anziché una macchina virtuale Linux di Azure). Il dispositivo deve trovarsi nella stessa rete della videocamera IP. È possibile seguire le istruzioni riportate in [Installare il runtime di Azure IoT Edge in Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux), quindi seguire le istruzioni nell'avvio rapido [Distribuire il primo modulo IoT Edge in un dispositivo Linux virtuale](https://docs.microsoft.com/azure/iot-edge/quickstart-linux) per registrare il dispositivo con l'hub IoT di Azure.
