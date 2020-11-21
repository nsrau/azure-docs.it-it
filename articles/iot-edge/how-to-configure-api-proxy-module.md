---
title: Configurare il modulo proxy API-Azure IoT Edge | Microsoft Docs
description: Informazioni su come personalizzare il modulo proxy API per le gerarchie di IoT Edge Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 894398d63e326db3c6ee9de9bebc426a6e621600
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024671"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>Configurare il modulo proxy API per lo scenario della gerarchia del gateway (anteprima)

Il modulo proxy API consente ai dispositivi IoT Edge di inviare richieste HTTP tramite gateway anziché stabilire connessioni dirette ai servizi cloud. Questo articolo illustra le opzioni di configurazione in modo da poter personalizzare il modulo per supportare i requisiti della gerarchia del gateway.

>[!NOTE]
>Per questa funzionalità è necessario IoT Edge versione 1.2, disponibile in anteprima pubblica, che esegue contenitori Linux.

In alcune architetture di rete IoT Edge i dispositivi dietro ai gateway non hanno accesso diretto al cloud. Tutti i moduli che tentano di connettersi ai servizi cloud avranno esito negativo. Il modulo proxy API supporta i dispositivi IoT Edge downstream in questa configurazione reindirizzando le connessioni al modulo per passare attraverso i livelli di una gerarchia del gateway. Consente ai client di comunicare in modo sicuro a più servizi tramite HTTPS senza tunneling, ma terminando le connessioni a ogni livello. Il modulo proxy API funge da modulo proxy tra i dispositivi IoT Edge in una gerarchia del gateway fino a raggiungere il dispositivo IoT Edge al livello superiore. A questo punto, i servizi in esecuzione sul livello superiore IoT Edge dispositivo gestiscono queste richieste e il modulo proxy API delega tutto il traffico HTTP dai servizi locali al cloud tramite una singola porta.

Il modulo proxy API può abilitare molti scenari per le gerarchie del gateway, tra cui consentire a dispositivi di livello inferiore di estrarre le immagini del contenitore o eseguire il push dei BLOB nella risorsa di archiviazione. La configurazione delle regole del proxy definisce il modo in cui i dati vengono instradati. Questo articolo illustra diverse opzioni di configurazione comuni.

## <a name="deploy-the-proxy-module"></a>Distribuire il modulo proxy

Il modulo proxy API è disponibile in Microsoft Container Registry (di Microsoft): `mcr.microsoft.com/azureiotedge-api-proxy:latest` .

È anche possibile distribuire il modulo proxy API direttamente da Azure Marketplace: [proxy api IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview).

## <a name="understand-the-proxy-module"></a>Informazioni sul modulo proxy

Il modulo proxy API utilizza un proxy inverso Nginx per indirizzare i dati attraverso i livelli di rete. Un proxy è incorporato nel modulo, il che significa che l'immagine del modulo deve supportare la configurazione del proxy. Ad esempio, se il proxy è in ascolto su una determinata porta, è necessario che la porta sia aperta nel modulo.

Il proxy inizia con un file di configurazione predefinito incorporato nel modulo. È possibile passare una nuova configurazione al modulo dal cloud usando il [modulo gemello](../iot-hub/iot-hub-devguide-module-twins.md). Inoltre, è possibile utilizzare le variabili di ambiente per attivare o disattivare le impostazioni di configurazione in fase di distribuzione.

Questo articolo è incentrato prima sul file di configurazione predefinito e su come usare le variabili di ambiente per abilitare le impostazioni. Viene quindi illustrata la personalizzazione del file di configurazione alla fine.

### <a name="default-configuration"></a>Configurazione predefinita

Il modulo proxy API è dotato di una configurazione predefinita che supporta scenari comuni e consente la personalizzazione. È possibile controllare la configurazione predefinita tramite le variabili di ambiente del modulo.

Attualmente, le variabili di ambiente predefinite includono:

| Variabile di ambiente | Descrizione |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | Elencare tutte le variabili che si intende aggiornare in un elenco delimitato da virgole. Questo passaggio impedisce di modificare accidentalmente le impostazioni di configurazione errate.
| `NGINX_DEFAULT_PORT` | Modifica la porta su cui il proxy nginx è in ascolto. Se si aggiorna questa variabile di ambiente, assicurarsi che la porta selezionata venga esposta anche nel modulo dockerfile e dichiarata come binding di porta nel manifesto di distribuzione.<br><br>Il valore predefinito è 443.<br><br>Quando viene distribuito da Azure Marketplace, la porta predefinita viene aggiornata a 8000 per evitare conflitti con il modulo edgeHub. Per altre informazioni, vedere [ridurre al minimo le porte aperte](#minimize-open-ports). |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | Indirizzo per indirizzare le richieste docker. Modificare questa variabile nel dispositivo di livello superiore per puntare al modulo del registro di sistema.<br><br>Il valore predefinito è il nome host padre. |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | Indirizzo per indirizzare le richieste del registro di sistema BLOB. Modificare questa variabile nel dispositivo di livello superiore per puntare al modulo di archiviazione BLOB.<br><br>Il valore predefinito è il nome host padre. |

## <a name="minimize-open-ports"></a>Ridurre al minimo le porte aperte

Per ridurre al minimo il numero di porte aperte, il modulo proxy API deve inoltrare tutto il traffico HTTPS (porta 443), incluso il traffico destinato al modulo edgeHub. Il modulo proxy API è configurato per impostazione predefinita per reindirizzare tutto il traffico edgeHub sulla porta 443.

Per configurare la distribuzione per ridurre al minimo le porte aperte, attenersi alla procedura seguente:

1. Aggiornare le impostazioni del modulo edgeHub in modo che non vengano associate sulla porta 443. in caso contrario, verranno rilevati conflitti di binding delle porte. Per impostazione predefinita, il modulo edgeHub è associato alle porte 443, 5671 e 8883. Eliminare il binding della porta 443 e lasciare gli altri due sul posto:

   ```json
   {
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
         ]
       }
     }
   }
   ```

1. Configurare il modulo proxy API per l'associazione sulla porta 443.

   1. Impostare il valore della variabile di ambiente **NGINX_DEFAULT_PORT** su `443` .
   1. Aggiornare le opzioni di creazione del contenitore da associare sulla porta 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

Se non è necessario ridurre al minimo le porte aperte, è possibile consentire al modulo edgeHub di usare la porta 443 e configurare il modulo proxy API per l'ascolto su un'altra porta. Il modulo proxy API, ad esempio, può restare in ascolto sulla porta 8000 impostando il valore della variabile di ambiente **NGINX_DEFAULT_PORT** su `8000` e creando un binding della porta per la porta 8000.

## <a name="enable-container-image-download"></a>Abilita download immagine contenitore

Un caso d'uso comune per il modulo proxy API è quello di abilitare i dispositivi IoT Edge nei livelli inferiori per eseguire il pull delle immagini del contenitore. Questo scenario usa il [modulo Docker Registry](https://hub.docker.com/_/registry) per recuperare le immagini del contenitore dal cloud e memorizzarle nella cache al livello superiore. Il proxy API inoltra tutte le richieste HTTPS per scaricare un'immagine del contenitore dai livelli inferiori da servire al modulo del registro di sistema nel livello superiore.

Questo scenario richiede che i dispositivi IoT Edge downstream puntino al nome di dominio `$upstream` seguito dal numero di porta del modulo proxy API invece che dal registro contenitori di un'immagine. Ad esempio: `$upstream:8000/azureiotedge-api-proxy:1.0`.

Questo caso d'uso è illustrato nell'esercitazione [creare una gerarchia di dispositivi IOT Edge usando i gateway](tutorial-nested-iot-edge.md).

Configurare i moduli seguenti al **livello superiore**:

* Un modulo Docker Registry
  * Configurare il modulo con un nome memorabile come *Registry* ed esporre una porta nel modulo per ricevere le richieste.
  * Configurare il modulo per eseguire il mapping al registro contenitori.
* Un modulo proxy API
  * Configurare le variabili di ambiente seguenti:

    | Nome | Valore |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | Il nome del modulo del registro di sistema e la porta aperta. Ad esempio: `registry:5000`. |
    | `NGINX_DEFAULT_PORT` | Porta su cui il proxy nginx è in ascolto per le richieste provenienti dai dispositivi downstream. Ad esempio: `8000`. |

  * Configurare le createOptions seguenti:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Configurare il modulo seguente in qualsiasi **livello inferiore** per questo scenario:

* Un modulo proxy API
  * Configurare le variabili di ambiente seguenti:

    | Nome | Valore |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Porta su cui il proxy nginx è in ascolto per le richieste provenienti dai dispositivi downstream. Ad esempio: `8000`. |

  * Configurare le createOptions seguenti:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>Abilita caricamento BLOB

Un altro caso d'uso per il modulo proxy API è quello di abilitare i dispositivi IoT Edge nei livelli inferiori per caricare i BLOB. Questo caso d'uso Abilita la funzionalità di risoluzione dei problemi nei dispositivi di livello inferiore, ad esempio il caricamento di log del modulo o il caricamento del bundle di supporto.

Questo scenario usa l' [Archivio BLOB di Azure sul modulo IOT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) al livello superiore per gestire la creazione e il caricamento di BLOB.

Configurare i moduli seguenti al **livello superiore**:

* Un archivio BLOB di Azure nel modulo IoT Edge.
* Un modulo proxy API
  * Configurare le variabili di ambiente seguenti:

    | Nome | Valore |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Nome del modulo di archiviazione BLOB e porta aperta. Ad esempio: `azureblobstorageoniotedge:1102`. |
    | `NGINX_DEFAULT_PORT` | Porta su cui il proxy nginx è in ascolto per le richieste provenienti dai dispositivi downstream. Ad esempio: `8000`. |

  * Configurare le createOptions seguenti:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Configurare il modulo seguente in qualsiasi **livello inferiore** per questo scenario:

* Un modulo proxy API
  * Configurare le variabili di ambiente seguenti:

    | Nome | Valore |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Porta su cui il proxy nginx è in ascolto per le richieste provenienti dai dispositivi downstream. Ad esempio: `8000`. |

  * Configurare le createOptions seguenti:

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

Usare la procedura seguente per caricare il pacchetto di supporto o il file di log nel modulo di archiviazione BLOB situato al livello superiore:

1. Creare un contenitore BLOB usando Azure Storage Explorer o le API REST. Per altre informazioni, vedere [archiviare dati sul perimetro con archiviazione BLOB di Azure in IOT Edge](how-to-store-data-blob.md).
1. Richiedere un log o il caricamento del bundle di supporto in base ai passaggi descritti in [recuperare i log dalle distribuzioni IOT Edge](how-to-retrieve-iot-edge-logs.md), ma usare il nome `$upstream` di dominio e la porta proxy aperta al posto dell'indirizzo del modulo di archiviazione BLOB. Ad esempio:

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>Modificare la configurazione del proxy

Un file di configurazione predefinito è incorporato nel modulo proxy API, ma è possibile passare una nuova configurazione al modulo tramite il cloud usando il modulo gemello.

Quando si scrive una configurazione personalizzata, è comunque possibile usare l'ambiente per modificare le impostazioni per ogni distribuzione. Usare la sintassi seguente:

* Utilizzare `${MY_ENVIRONMENT_VARIABLE}` per recuperare il valore di una variabile di ambiente.
* Usare le istruzioni condizionali per attivare o disattivare le impostazioni in base al valore di una variabile di ambiente:

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

Quando il modulo proxy API analizza una configurazione proxy, sostituisce innanzitutto tutte le variabili di ambiente elencate in `PROXY_CONFIG_ENV_VAR_LIST` con i valori specificati usando la sostituzione. Quindi, tutto ciò che intercorre tra una `#if_tag` `#endif_tag` coppia e viene sostituito. Il modulo fornisce quindi la configurazione analizzata al proxy inverso nginx.

Per aggiornare dinamicamente la configurazione del proxy, attenersi alla procedura seguente:

1. Scrivere il file di configurazione. È possibile usare questo modello predefinito come riferimento: [nginx_default_config. conf](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. Copiare il testo del file di configurazione e convertirlo in Base64.
1. Incollare il file di configurazione codificato come valore della `proxy_config` proprietà desiderata nel modulo gemello.

   ![Incollare il file di configurazione codificato come valore di proxy_config proprietà](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>Passaggi successivi

Usare il modulo proxy API per [connettere un dispositivo IOT Edge downstream a un gateway di Azure IOT Edge](how-to-connect-downstream-iot-edge-device.md).