---
title: Analizzare il video live con Visione artificiale per Analisi spaziale - Azure
description: Questa esercitazione illustra come usare Analisi video live con la funzionalità di intelligenza artificiale Analisi spaziale di Visione artificiale in Servizi cognitivi di Azure per analizzare un feed di video live da una fotocamera IP simulata.
ms.topic: tutorial
ms.date: 09/08/2020
ms.openlocfilehash: 98ee57d4916ac0a8da8b48a9cdd881468b2d75d5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929651"
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
* [Esercitazione: Sviluppo di un modulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-develop-for-linux)
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

* Raccoglie i risultati e pubblica gli eventi nel nodo [sink dell'hub IoT](media-graph-concept.md#iot-hub-message-sink). Il nodo invia quindi gli eventi all'[hub di IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub). 
* Acquisisce anche un clip video di 30 secondi dall'origine RTSP usando un [processore di controllo del segnale](media-graph-concept.md#signal-gate-processor) e lo archivia come asset di Servizi multimediali.

## <a name="create-the-computer-vision-resource"></a>Creare la risorsa di Visione artificiale

È necessario creare una risorsa di Azure di tipo Visione artificiale nel [portale di Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal) o mediante l'interfaccia della riga di comando di Azure. Sarà possibile crearla dopo che la richiesta di accesso al contenitore sarà stata approvata e l'ID sottoscrizione di Azure sarà stato registrato. Accedere a https://aka.ms/csgate per inviare il proprio caso d'uso e l'ID sottoscrizione di Azure.  Occorre creare la risorsa di Azure usando la stessa sottoscrizione di Azure specificata nel modulo di richiesta di accesso.

### <a name="gathering-required-parameters"></a>Raccolta dei parametri obbligatori

Per tutti i contenitori di Servizi cognitivi sono obbligatori tre parametri principali, incluso il contenitore spatial-analysis. Il contratto di licenza con l'utente finale deve essere presente con il valore accept. Sono inoltre necessari un URL dell'endpoint e una chiave API.

### <a name="endpoint-uri-endpoint_uri"></a>URI endpoint {ENDPOINT_URI}

Il valore dell'URI dell'endpoin è disponibile nella pagina Panoramica del portale di Azure per la risorsa di Servizi cognitivi. Passare alla pagina Panoramica e trovare l'URI dell'endpoint. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/keys-endpoint.png" alt-text="Chiavi ed endpoint":::

### <a name="keys-api_key"></a>Chiavi {API_KEY}

Questa chiave viene usata per avviare il contenitore spatial-analysis ed è disponibile nella pagina Chiavi del portale di Azure per la risorsa di Servizi cognitivi corrispondente. Passare alla pagina Chiavi e trovare le chiavi.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/spatial-analysis-tutorial/endpoint-uri.png" alt-text="URI endpoint":::

## <a name="set-up-azure-stack-edge"></a>Configurare Azure Stack Edge

Seguire [questa procedura](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-prep) per configurare Azure Stack Edge e continuare a seguire la procedura descritta di seguito per distribuire i moduli di Analisi video live e di Analisi spaziale.

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
        "IoThubConnectionString" : " HostName=<IoTHubName>.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=<SharedAccessKey>”,
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

Aggiungere il modulo spatial-analysis in /src/edge/deployment.template.json. Nel modello sono disponibili i moduli lvaEdge, rtspsim e spatial-analysis.

<p>
<details>
<summary>Espandere quest'ultimo e visualizzare il modello di distribuzione di esempio.  
Copiare il contenuto e incollarlo in /src/edge/deployment.template.json.
</summary>
<pre><code>
{
  "$schema-template": "2.0.0",
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": {}
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "5671/tcp": [
                      {
                        "HostPort": "5671"
                      }
                    ],
                    "8883/tcp": [
                      {
                        "HostPort": "8883"
                      }
                    ],
                    "443/tcp": [
                      {
                        "HostPort": "443"
                      }
                    ]
                  }
                }
              }
            }
          }
        },
        "modules": {
          "lvaEdge": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/live-video-analytics:1",
              "createOptions": {
                "HostConfig": {
                  "LogConfig": {
                    "Type": "",
                    "Config": {
                      "max-size": "10m",
                      "max-file": "10"
                    }
                  },
                  "Binds": [
                    "$OUTPUT_VIDEO_FOLDER_ON_DEVICE:/var/media/",
                    "$APPDATA_FOLDER_ON_DEVICE:/var/lib/azuremediaservices"
                  ],
                  "IpcMode": "host",
                  "ShmSize": 1536870912
                }
              }
            },
            "env": {
              "IS_DEVELOPER_ENVIRONMENT": {
                "value": "true"
              }
            }
          },
          "rtspsim": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2",
                "createOptions": {
                  "HostConfig": {
                    "Mounts": [
                      {
                        "Target": "/live/mediaServer/media",
                        "Source": "lvaspatialanalysislocal",
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
              }
            },
          "spatialAnalysis": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azure-cognitive-services/spatial-analysis:1.0",
              "createOptions": {
                "HostConfig": {
                  "PortBindings": {
                    "50051/tcp": [
                      {
                        "HostPort": "50051"
                      }
                    ]
                  },
                  "IpcMode": "host",
                  "Binds": [
                      "/tmp/.X11-unix:/tmp/.X11-unix"
                  ],
                  "Runtime": "nvidia",
                  "ShmSize": 536870911,
                  "LogConfig": {
                      "Type": "json-file",
                      "Config": {
                          "max-size": "10m",
                          "max-file": "200"
                      }
                  }
                }
              }
            },
            "env": {
              "DISPLAY": {
                "value": ":0"
              },
              "ARCHON_SHARED_BUFFER_LIMIT": {
                "value": "377487360"
              },
              "ARCHON_PERF_MARKER": {
                "value": "false"
              },
              "QT_X11_NO_MITSHM": {
                "value": "1"
              },
              "OMP_WAIT_POLICY": {
                "value": "PASSIVE"
              },
              "EULA": {
                "value": "accept"
              },
              "BILLING_ENDPOINT": {
                "value": "<Use one key from Archon azure resource (keys page)>"
              },
              "API_KEY": {
                "value": "<Use endpoint from Archon azure resource (overview page)>"
              }
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "LVAToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "lvaEdge": {
      "properties.desired": {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/microsoft.media/mediaservices/$AMS_ACCOUNT",
        "aadTenantId": "$AAD_TENANT_ID",
        "aadServicePrincipalAppId": "$AAD_SERVICE_PRINCIPAL_ID",
        "aadServicePrincipalSecret": "$AAD_SERVICE_PRINCIPAL_SECRET",
        "aadEndpoint": "https://login.microsoftonline.com",
        "aadResourceId": "https://management.core.windows.net/",
        "armEndpoint": "https://management.azure.com/",
        "diagnosticsEventsOutputName": "AmsDiagnostics",
        "operationalEventsOutputName": "AmsOperational",        
        "logLevel": "Info",
        "logCategories": "Application,Events,MediaPipeline",
        "allowUnsecuredEndpoints": true,
        "telemetryOptOut": false
      }
    },
    "spatialAnalysis": {
      "properties.desired": {
        "globalSettings": {
          "PlatformTelemetryEnabled": true,
          "CustomerTelemetryEnabled": true
        },
        "graphs": {
            "polygonCross": {
              "version": 2,
              "enabled": true,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcrossingpolygon.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0,0],[0.6,0],[0.6,0.9],[0,0.9],[0,0]],\"threshold\":50,\"events\":[{\"type\":\"enter/exit\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personCount": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-personcount.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0 }",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"polygon0\",\"polygon\":[[0.8,0],[1,0],[1,1],[0.8,1],[0.8,0]],\"threshold\":50,\"events\":[{\"type\":\"count\",\"config\":{\"trigger\":\"event\"}}]}]}"
              },
              "nodesloglevel": "info"
            },
            "personDistance": {
              "version": 2,
              "enabled": false,
              "platformloglevel": "info",
              "operationId": "cognitiveservices.vision.spatialanalysis-persondistance.livevideoanalytics",
              "parameters": {
                  "BINDING_ADDRESS": "0.0.0.0:50051",
                  "DETECTOR_NODE_CONFIG": "{ \"show_debug_video\": false, \"gpu_index\": 0,\"gpu_index\": 0,\"do_calibration\": true}",
                  "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\": \"distance_zone\", \"polygon\": [[0,0],[0,1],[1,1],[1,0],[0,0]],\"threshold\": 35.00,\"events\":[{\"type\": \"people_distance\",\"config\":{\"trigger\": \"event\",\"output_frequency\":1,\"minimum_distance_threshold\":6.0,\"maximum_distance_threshold\":35.0}}]}]}"
              },
              "nodesloglevel": "info"
            }
        }
      }
    }
  }
}
</code>
</pre>
</details>
</p>

È necessario prestare attenzione ad alcuni aspetti:

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
1. IpcMode in lvaEdge e nel modulo di Analisi spaziale createOptions devono essere uguali e impostati su host.
1. Il file del modello di distribuzione deve contenere "deployment" nel nome file, altrimenti non può essere riconosciuto e generare il manifesto per la distribuzione.
1. Per il corretto funzionamento del simulatore RTSP, assicurarsi di aver configurato i limiti del volume. Per altre informazioni, vedere [Configurare i montaggi del volume di Docker](deploy-azure-stack-edge-how-to.md#optional-setup-docker-volume-mounts).

    1. [Connettersi alla condivisione SMB](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-deploy-add-shares#connect-to-an-smb-share) e copiare il [file video](https://lvamedia.blob.core.windows.net/public/bulldozer.mkv) nella condivisione locale.
    1. Verificare che il modulo rtspsim contenga:
        
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
    > :::image type="content" source="./media/spatial-analysis-tutorial/connection-string.png" alt-text="Analisi spaziale: stringa di connessione":::
1. Fare clic con il pulsante destro del mouse su src/edge/deployment.template.json e scegliere Generate IoT Edge Deployment Manifest (Genera manifesto della distribuzione di IoT Edge).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-amd64-json.png" alt-text="Analisi spaziale: JSON AMD64 di distribuzione":::
    
    Questa azione crea un file manifesto denominato deployment.amd64.json nella cartella src/edge/config.
1. Fare clic con il pulsante destro del mouse su src/edge/config/deployment.amd64.json, scegliere Create Deployment for Single Device (Crea la distribuzione per un unico dispositivo) e quindi selezionare il nome del dispositivo perimetrale.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/deployment-template-json.png" alt-text="Analisi spaziale: JSON modello di distribuzione":::   
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

1. Aprire il riquadro Explorer in Visual Studio Code e cercare Hub IoT di Azure nell'angolo in basso a sinistra.
1. Espandere il nodo Dispositivi.
1. Fare clic con il pulsante destro del mouse su Azure Stack Edge e scegliere Avvia il monitoraggio endpoint eventi predefinito.
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/spatial-analysis-tutorial/start-monitoring.png" alt-text="Analisi spaziale: avvio monitoraggio":::
     
## <a name="run-the-program"></a>Eseguire il programma

È presente un file program.cs che richiama i metodi diretti in src/cloud-to-device-console-app/operations.json. È necessario configurare operations.json e fornire una topologia per l'uso del grafo multimediale.
In operations.json:

Impostare la topologia in modo simile al seguente (topologyFile per la topologia locale, topologyUrl per la topologia online):

```json
{
    "opName": "GraphTopologySet",
    "opParams": {
        "topologyFile": "../edge/spatialAnalysistopology.json"
    }
},
```

Creare un'istanza del grafo come questa, impostare i parametri nella topologia qui:

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

<p>
<details>
<summary>Espandere per visualizzare il file della topologia di esempio per il modulo spatialAnalysis:
</summary>
<pre><code>
{
    "@apiVersion": "1.0",
    "name": "InferencingWithCVExtension",
    "properties": {
      "description": "Analyzing live video using spatialAnalysis Extension to send images to an external inference engine",
      "parameters": [
        {
          "name": "rtspUserName",
          "type": "String",
          "description": "rtsp source user name.",
          "default": "dummyUserName"
        },
        {
          "name": "rtspPassword",
          "type": "String",
          "description": "rtsp source password.",
          "default": "dummyPassword"
        },
        {
          "name": "rtspUrl",
          "type": "String",
          "description": "rtsp Url"
        },
        {
          "name": "grpcUrl",
          "type": "String",
          "description": "inferencing Url",
          "default": "tcp://spatialAnalysis:50051"
        },
        {
          "name": "frameRate",
          "type": "String",
          "description": "Rate of the frames per second to be received from LVA.",
          "default": "2"
        },
        {
          "name": "spatialanalysisusername",
          "type": "String",
          "description": "spatialanalysis endpoint username",
          "default": "not-in-use"
        },
        {
          "name": "spatialanalysispassword",
          "type": "String",
          "description": "spatialanalysis endpoint password",
          "default": "not-in-use"  
        }
      ],
      "sources": [
        {
          "@type": "#Microsoft.Media.MediaGraphRtspSource",
          "name": "rtspSource",
          "transport": "tcp",
          "endpoint": {
            "@type": "#Microsoft.Media.MediaGraphUnsecuredEndpoint",
            "url": "${rtspUrl}",
            "credentials": {
              "@type": "#Microsoft.Media.MediaGraphUsernamePasswordCredentials",
              "username": "${rtspUserName}",
              "password": "${rtspPassword}"
            }
          }
        }
      ],
      "processors": [
        {
          "@type": "#Microsoft.Media.MediaGraphFrameRateFilterProcessor",
          "name": "frameRateFilter",
          "inputs": [
            {
              "nodeName": "rtspSource"
            }
          ],
          "maximumFps": "${frameRate}"
        },
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
        },
        {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "computerVisionExtension"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "PT0S",
            "minimumActivationTime": "PT30S",
            "maximumActivationTime": "PT30S"
          }
      ],
      "sinks": [
        {
            "@type": "#Microsoft.Media.MediaGraphAssetSink",
            "name": "assetSink",
            "assetNamePattern": "sampleAssetFromEVR-CV-LVAEdge-${System.DateTime}",
            "segmentLength": "PT30S",
            "LocalMediaCacheMaximumSizeMiB": "200",
            "localMediaCachePath": "/var/lib/azuremediaservices/tmp/",
            "inputs": [
                {
                    "nodeName": "signalGateProcessor"
                }
            ]
        },
        {
          "@type": "#Microsoft.Media.MediaGraphIoTHubMessageSink",
          "name": "hubSink",
          "hubOutputName": "inferenceOutput",
          "inputs": [
            {
              "nodeName": "computerVisionExtension"
            }
          ]
        }
      ]
    }
  }
</code>
</pre>
</details>
</p>


Usare MediaGraphRealTimeComputerVisionExtension per connettersi al modulo spatial-analysis. Impostare ${grpcUrl} in tcp://spatialAnalysis:<NUMERO_PORTA>, ad esempio tcp://spatialAnalysis:50051

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
> Usare un [file video](https://lvamedia.blob.core.windows.net/public/2018-03-07.16-50-00.16-55-00.school.G421.mkv) il cui fotogramma mostri più di una persona.

> [!NOTE]
> È possibile eseguire una sola operazione per volta. Assicurarsi quindi che un solo flag sia impostato su **true** e che gli altri siano impostati su **false**.
