---
title: Analizzare il video live con Visione artificiale per Analisi spaziale - Azure
description: Questa esercitazione illustra come usare Analisi video live con la funzionalità di intelligenza artificiale Analisi spaziale di Visione artificiale in Servizi cognitivi di Azure per analizzare un feed di video live da una fotocamera IP simulata.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 0dc89eaddf5cabc3063744dfe2c9f0236c70438c
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015686"
---
# <a name="analyze-live-video-with-computer-vision-for-spatial-analysis-preview"></a>Analizzare il video live con Visione artificiale per Analisi spaziale (anteprima)

Questa esercitazione illustra come usare Analisi video live con il [servizio di intelligenza artificiale Analisi spaziale di Visione artificiale in Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/computer-vision/) per analizzare un feed di video live da una fotocamera IP simulata. Si vedrà come questo server di inferenza consente di analizzare il video in streaming per comprendere le relazioni spaziali tra persone e movimento nello spazio fisico.  Un sottoinsieme dei fotogrammi del feed di video viene inviato a questo server di inferenza e i risultati vengono inviati all'hub IoT Edge e, quando vengono soddisfatte alcune condizioni, i clip video vengono registrati e archiviati come asset di Servizi multimediali di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare le risorse.
> * Esaminare il codice.
> * Eseguire il codice di esempio.
> * Monitorare gli eventi.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="suggested-pre-reading"></a>Letture consigliate

Leggere questi articoli prima di iniziare:

* [Panoramica di Analisi di video live in IoT Edge](overview.md)
* [Terminologia di Analisi di video live in IoT Edge](terminology.md)
* [Concetti del grafo multimediale](media-graph-concept.md)
* [Registrazione di video basata su eventi](event-based-video-recording-concept.md)
* [Esercitazione: Sviluppo di un modulo IoT Edge](../../iot-edge/tutorial-develop-for-linux.md)
* [Distribuire Analisi video live in Azure Stack Edge](deploy-azure-stack-edge-how-to.md) 

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono elencati i prerequisiti per collegare il modulo di Analisi spaziale al modulo di Analisi video live.

* [Visual Studio Code](https://code.visualstudio.com/) nel computer di sviluppo. Assicurarsi che sia presente l'[estensione Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
    * Verificare che la rete a cui è connesso il computer di sviluppo accetti il protocollo AMQP (Advanced Message Queueing Protocol) sulla porta 5671. Questa configurazione consente la comunicazione tra Azure IoT Tools e l'hub IoT di Azure.
* [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) con accelerazione GPU.  
    È consigliabile usare Azure Stack Edge con accelerazione GPU, tuttavia il contenitore può essere eseguito in qualsiasi altro dispositivo con [GPU NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/). 
* [Contenitore di Visione artificiale di Servizi cognitivi di Azure](https://azure.microsoft.com/services/cognitive-services/computer-vision/) per l'analisi spaziale.  
    Per usare questo contenitore, è necessario avere una risorsa Visione artificiale per ottenere la **chiave API** associata e un **URI dell'endpoint**. La chiave API è disponibile nelle pagine Panoramica e Chiavi di Visione artificiale nel portale di Azure. La chiave e l'endpoint sono necessari per avviare il contenitore.

## <a name="overview"></a>Panoramica

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/overview.png" alt-text="Panoramica di Analisi spaziale":::
 
Il diagramma mostra il flusso dei segnali in questa esercitazione. Un [modulo Edge](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simula una videocamera IP che ospita un server RTSP (Real-Time Streaming Protocol). Un nodo di [origine RTSP](media-graph-concept.md#rtsp-source) estrae il feed video da questo server e invia i fotogrammi video al nodo del [processore di filtro della frequenza dei fotogrammi](media-graph-concept.md#frame-rate-filter-processor). Questo processore limita la frequenza fotogrammi del flusso video che raggiunge il nodo del processore MediaGraphCognitiveServicesVisionExtension.

Il nodo MediaGraphCognitiveServicesVisionExtension svolge il ruolo di proxy. Converte i fotogrammi video nel tipo di immagine specificato. Inoltra quindi l'immagine tramite **memoria condivisa** a un altro modulo Edge che esegue operazioni di intelligenza artificiale protetto da un endpoint gRPC. In questo esempio il modulo Edge è il modulo spatial-analysis. Il nodo del processore MediaGraphCognitiveServicesVisionExtension esegue due operazioni:

* Raccoglie i risultati e pubblica gli eventi nel nodo [sink dell'hub IoT](media-graph-concept.md#iot-hub-message-sink). Il nodo invia quindi gli eventi all'[hub di IoT Edge](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). 
* Acquisisce anche un clip video di 30 secondi dall'origine RTSP usando un [processore di controllo del segnale](media-graph-concept.md#signal-gate-processor) e lo archivia come asset di Servizi multimediali.

## <a name="create-the-computer-vision-resource"></a>Creare la risorsa di Visione artificiale

È necessario creare una risorsa di Azure di tipo Visione artificiale nel [portale di Azure](../../iot-edge/how-to-deploy-modules-portal.md) o mediante l'interfaccia della riga di comando di Azure. Sarà possibile crearla dopo che la richiesta di accesso al contenitore sarà stata approvata e l'ID sottoscrizione di Azure sarà stato registrato. Accedere a https://aka.ms/csgate per inviare il proprio caso d'uso e l'ID sottoscrizione di Azure.  Occorre creare la risorsa di Azure usando la stessa sottoscrizione di Azure specificata nel modulo di richiesta di accesso.

### <a name="gathering-required-parameters"></a>Raccolta dei parametri obbligatori

Per tutti i contenitori di Servizi cognitivi sono obbligatori tre parametri principali, incluso il contenitore spatial-analysis. Il contratto di licenza con l'utente finale deve essere presente con il valore accept. Sono inoltre necessari un URI dell'endpoint e una chiave API.

### <a name="keys-and-endpoint-uri"></a>Chiavi e URI dell'endpoint

La chiave viene usata per avviare il contenitore spatial-analysis ed è disponibile nella pagina `Keys and Endpoint` del portale di Azure per la risorsa di Servizi cognitivi corrispondente. Passare a tale pagina per trovare le chiavi e l'URI dell'endpoint.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Panoramica di Analisi spaziale":::

## <a name="set-up-azure-stack-edge"></a>Configurare Azure Stack Edge

Seguire [questa procedura](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) per configurare Azure Stack Edge e continuare a seguire la procedura descritta di seguito per distribuire i moduli di Analisi video live e di Analisi spaziale.

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

1. Clonare il repository da questo percorso: https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp.
1. In Visual Studio Code aprire la cartella in cui è stato scaricato il repository.
1. In Visual Studio Code passare alla cartella src/cloud-to-device-console-app. In questa cartella creare un file e assegnargli il nome appsettings.json. Questo file conterrà le impostazioni necessarie per eseguire il programma.
1. Seguire questa procedura per ottenere IotHubConnectionString da Azure Stack Edge:

    * passare all'hub IoT nel portale di Azure e fare clic su `Shared access policies` nel riquadro di spostamento a sinistra.
    * Fare clic su `iothubowner` per ottenere le chiavi di accesso condiviso.
    * Copiare la `Connection String – primary key` e incollarla nella casella di input in VSCode.
    
        La stringa di connessione sarà simile alla seguente: <br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
1. Copiare il contenuto riportato di seguito nel file. Assicurarsi di sostituire le variabili.
    
    ```json
    {
        "IoThubConnectionString" : "HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>",
        "deviceId" : "<your Azure Stack Edge name>",
        "moduleId" : "lvaEdge"
    } 
    ```
1. Passare alla cartella src/edge e creare un file denominato .env.
1. Copiare il contenuto del file /clouddrive/lva-sample/edge-deployment/.env. Il testo sarà simile al codice seguente.

    ```env
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"   
    ```
    
## <a name="set-up-deployment-template"></a>Configurare il modello di distribuzione  

Cercare il file di distribuzione in /src/edge/deployment.spatialAnalysis.template.json. Nel modello sono disponibili i moduli lvaEdge, rtspsim e spatial-analysis.

È necessario prestare attenzione ad alcuni aspetti del file del modello di distribuzione:

1. Impostare il binding porta.
    
    ```
    "PortBindings": {
        "50051/tcp": [
            {
                "HostPort": "50051"
            }
        ]
    },
    ```
1. `IpcMode` in lvaEdge e nel modulo di analisi spaziale createOptions devono essere uguali e impostati su host.
1. Per il corretto funzionamento del simulatore RTSP, assicurarsi di aver configurato i limiti del volume. Per altre informazioni, vedere [Configurare i montaggi del volume di Docker](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Connettersi alla condivisione SMB](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share) e copiare il [file video del bulldozer di esempio](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) nella condivisione locale.
    1. Verificare che il modulo rtspsim abbia la configurazione seguente:
        
        ```json
        "createOptions": {
                            "HostConfig": {
                              "Mounts": [
                                {
                                  "Target": "/live/mediaServer/media",
                                  "Source": "<your Local Docker Volume Mount name>",
                                  "Type": "volume"
                                }
                              ],
                              "PortBindings": {
                                "554/tcp": [
                                  {
                                    "HostPort": "554"
                                  }
                                ]
                              }
                            }
                          }
        ```
## <a name="generate-and-deploy-the-deployment-manifest"></a>Generare e distribuire il manifesto della distribuzione

Il manifesto della distribuzione definisce i moduli che vengono distribuiti in un dispositivo perimetrale e le impostazioni di configurazione dei moduli.

Seguire questa procedura per generare il manifesto dal file modello e quindi distribuirlo nel dispositivo perimetrale.

1. Aprire Visual Studio Code.
1. Accanto al riquadro HUB IOT DI AZURE selezionare l'icona Altre azioni per impostare la stringa di connessione dell'hub IoT. È possibile copiare la stringa dal file src/cloud-to-device-console-app/appsettings.json.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Panoramica di Analisi spaziale":::
1. Fare clic con il pulsante destro del mouse su `src/edge/deployment.spatialAnalysis.template.json` e scegliere Generate IoT Edge Deployment Manifest (Genera manifesto della distribuzione di IoT Edge).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Panoramica di Analisi spaziale":::
    
    Questa azione crea un file manifesto denominato deployment.amd64.json nella cartella src/edge/config.
1. Fare clic con il pulsante destro del mouse su `src/edge/config/deployment.spatialAnalysis.amd64.json`, scegliere Create Deployment for Single Device (Crea la distribuzione per un unico dispositivo) e quindi selezionare il nome del dispositivo perimetrale.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Panoramica di Analisi spaziale":::   
1. Quando viene chiesto di selezionare un dispositivo hub IoT, scegliere il nome di Azure Stack Edge dal menu a discesa.
1. Dopo circa 30 secondi, nell'angolo in basso a sinistra della finestra aggiornare l'hub IoT di Azure. Il dispositivo Edge mostra ora i moduli distribuiti seguenti:
    
    * Analisi video live in IoT Edge (nome del modulo lvaEdge).
    * Simulatore RTSP (Real-Time Streaming Protocol) (nome del modulo rtspsim).
    * Analisi spaziale (nome del modulo spatialAnalysis).
    
Se la distribuzione riesce, verrà visualizzato un messaggio simile al seguente nella finestra OUTPUT:

```
[Edge] Start deployment to device [<Azure Stack Edge name>]
[Edge] Deployment succeeded.
```

È quindi possibile trovare i moduli `lvaEdge`, `rtspsim`, `spatialAnalysis` e `rtspsim` in Dispositivi/Moduli e il relativo stato deve essere "Running".

## <a name="prepare-to-monitor-events"></a>Preparare il monitoraggio degli eventi

Per visualizzare gli eventi, seguire questa procedura:

1. In Visual Studio Code aprire la scheda **Estensioni** (oppure premere CTRL+MAIUSC+X) e cercare Hub IoT di Azure.
1. Fare clic con il pulsante destro del mouse e scegliere **Impostazioni estensione**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/extensions-tab.png" alt-text="Panoramica di Analisi spaziale" (Visualizza messaggio dettagliato).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/run-program/show-verbose-message.png" alt-text="Panoramica di Analisi spaziale":::
1. Aprire il riquadro Explorer e cercare Hub IoT di Azure nell'angolo in basso a sinistra.
1. Espandere il nodo Dispositivi.
1. Fare clic con il pulsante destro del mouse su Azure Stack Edge e scegliere Avvia il monitoraggio endpoint eventi predefinito.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Panoramica di Analisi spaziale":::
     
## <a name="run-the-program"></a>Eseguire il programma

È presente un file program.cs che richiama i metodi diretti in src/cloud-to-device-console-app/operations.json. È necessario configurare operations.json e fornire una topologia per l'uso del grafo multimediale.  

In operations.json:

* Impostare la topologia in modo simile al seguente (topologyFile per la topologia locale, topologyUrl per la topologia online):

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysisTopology.json"
    }
},
```

* Creare un'istanza del grafo come questa, impostare i parametri nella topologia qui:

```json
{
    "opName": "GraphInstanceSet",
    "opParams": {
        "name": "Sample-Graph-1",
        "properties": {
            "topologyName": "InferencingWithCVExtension",
            "description": "Sample graph description",
            "parameters": [
                {
                    "name": "rtspUrl",
                    "value": " rtsp://rtspsim:554/media/bulldozer.mkv"
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
},
```
* Cambiare il collegamento alla topologia del grafo:

`topologyUrl` : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/lva-spatial-analysis/topology.json"

In **GraphInstanceSet** modificare il nome della topologia del grafo in modo che corrisponda al valore nel collegamento precedente:

`topologyName`: InferencingWithCVExtension

In **GraphTopologyDelet**e modificare il nome:

`name`: InferencingWithCVExtension

>[!Note]
Usare MediaGraphRealTimeComputerVisionExtension per connettersi al modulo spatial-analysis. Impostare ${grpcUrl} su **tcp://spatialAnalysis:<PORT_NUMBER>** , ad esempio tcp://spatialAnalysis:50051

```json
{
    "@type": "#Microsoft.Media.MediaGraphCognitiveServicesVisionExtension",
    "name": "computerVisionExtension",
    "endpoint": {
    "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
    "url": "${grpcUrl}",
    "credentials": {
        "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
        "username": "${spatialanalysisusername}",
        "password": "${spatialanalysispassword}"
    }
    },
    "image": {
    "scale": {
        "mode": "pad",
        "width": "1408",
        "height": "786"
    },
    "format": {
        "@type": "#Microsoft.Media.MediaGraphImageFormatRaw",
        "pixelFormat": "bgr24"
    }
    },
    "inputs": [
    {
        "nodeName": "frameRateFilter"
    }
    ]
}
```

Eseguire una sessione di debug e seguire le istruzioni della finestra TERMINALE per impostare la topologia, impostare l'istanza del grafo, attivare l'istanza del grafo ed eliminare infine le risorse.

## <a name="interpret-results"></a>Interpretare i risultati

Quando viene creata un'istanza di un grafo multimediale, si dovrebbe vedere l'evento "MediaSessionEstablished". Ecco un [evento MediaSessionEstablished di esempio](detect-motion-emit-events-quickstart.md#mediasessionestablished-event).

Il modulo spatial-analysis invierà anche eventi di informazioni dettagliate sull'intelligenza artificiale ad Analisi video live e quindi all'hub IoT, visualizzandoli anche nella finestra OUTPUT. ENTITY rappresenta gli oggetti di rilevamento ed EVENT gli eventi di analisi spaziale. Questo output verrà passato in Analisi video live.

Output di esempio per personZoneEvent (dall'operazione cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics):

```
{
  "body": {
    "timestamp": 143810610210090,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "a895c86824db41a898f776da1876d230",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "person",
            "confidence": 0.66026187
          },
          "attributes": [],
          "box": {
            "l": 0.26559368,
            "t": 0.17887735,
            "w": 0.49247605,
            "h": 0.76629657
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "event",
        "subtype": "",
        "inferenceId": "995fe4206847467f8dfde83a15187d76",
        "relatedInferences": [
          "a895c86824db41a898f776da1876d230"
        ],
        "event": {
          "name": "personZoneEvent",
          "properties": {
            "status": "Enter",
            "metadataVersion": "1.0",
            "zone": "polygon0",
            "trackingId": "a895c86824db41a898f776da1876d230"
          }
        },
        "extensions": {},
        "valueCase": "event"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/81631a63-f0bd-4f35-8344-5bc541b36bfc/resourceGroups/lva-sample-resources/providers/microsoft.media/mediaservices/lvasamplea4bylcwoqqypi",
    "subject": "/graphInstances/Sample-Graph-1/processors/spatialanalysisExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-20T03:54:29.001Z",
    "dataVersion": "1.0"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

Provare le diverse operazioni offerte dal modulo `spatialAnalysis`, come **personCount** e **personDistance**, attivando il flag "enabled" nel nodo del grafo del file manifesto della distribuzione.
>[!Tip]
> Usare un [file video di esempio](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) il cui fotogramma contenga più di una persona.

> [!NOTE]
> È possibile eseguire una sola operazione per volta. Assicurarsi quindi che un solo flag sia impostato su **true** e che gli altri siano impostati su **false**.