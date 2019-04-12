---
title: Come distribuire il modulo gemello OPC per Azure da zero | Microsoft Docs
description: Come distribuire gemello OPC da zero.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f470beb79e69b5a4a3febeb6a433c48490b96cf7
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491357"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Distribuire il modulo gemello OPC e le dipendenze da zero

Il modulo gemello OPC eseguito in IoT Edge e fornisce diversi servizi di edge per il dispositivo gemello OPC e servizi del Registro di sistema. 

Sono disponibili diverse opzioni per distribuire i moduli per il [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) Gateway, tra cui

- [Distribuzione da un pannello di IoT Edge del portale di Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Eseguire la distribuzione con AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Per altre informazioni sui dettagli di distribuzione e istruzioni, vedere GitHub [repository](https://github.com/Azure/azure-iiot-components).

## <a name="deployment-manifest"></a>Manifesto della distribuzione

Tutti i moduli vengono distribuiti tramite un manifesto della distribuzione.  Un esempio di manifesto per distribuire entrambi [server di pubblicazione OPC](https://github.com/Azure/iot-edge-opc-publisher) e [OPC gemello](https://github.com/Azure/azure-iiot-opc-twin-module) è illustrato di seguito.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
            }
          }
        },
        "modules": {
          "opctwin": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
              "createOptions": "{\"HostConfig\": { \"NetworkMode\": \"host\", \"CapAdd\": [\"NET_ADMIN\"] } }"
            }
          },
          "opcpublisher": {
            "version": "2.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "never",
            "settings": {
              "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
              "createOptions": "{\"Hostname\": \"publisher\", \"Cmd\": [ \"publisher\", \"--pf=./pn.json\", \"--di=60\", \"--to\", \"--aa\", \"--si=0\", \"--ms=0\" ], \"ExposedPorts\": { \"62222/tcp\": {} }, \"HostConfig\": { \"PortBindings\": { \"62222/tcp\": [{ \"HostPort\": \"62222\" }] } } }"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
          "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Distribuzione dal portale di Azure

Il modo più semplice per distribuire i moduli in un dispositivo gateway Azure IoT Edge è tramite il portale di Azure.  

### <a name="prerequisites"></a>Prerequisiti

1. Distribuire il dispositivo gemello OPC [dipendenze](howto-opc-twin-deploy-dependencies.md) e ottenuto l'oggetto risultante `.env` file. Si noti il distribuito `hub name` del `PCS_IOTHUBREACT_HUB_NAME` variabili nella finestra di `.env` file.

2. Registrare e avviare un [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) oppure [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) gateway IoT Edge e nota relativa `device id`.

### <a name="deploy-to-an-edge-device"></a>Distribuire in un dispositivo perimetrale

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare all'hub IoT.

2. Selezionare **IoT Edge** nel menu a sinistra.

3. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi.

4. Selezionare **Set Modules** (Configura i moduli).

5. Nel **moduli di distribuzione** sezione della pagina, seleziona **Add** e **modulo di IoT Edge.**

6. Nel **IoT Edge Custom Module** finestra di dialogo utilizzare `opctwin` come nome per il modulo, quindi specificare il contenitore *immagine URI* come

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Come *opzioni di creazione* usare il codice JSON seguente:

   ```json
   {"HostConfig":{"NetworkMode":"host","CapAdd":["NET_ADMIN"]}}
   ```

   Specificare i campi facoltativi, se necessario. Per altre informazioni sulle opzioni di creazione dei contenitore, i criteri di riavvio e lo stato desiderato, vedere [Proprietà desiderate di EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Per altre informazioni sul modulo gemello, vedere [Definire o aggiornare le proprietà desiderate](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Selezionare **salvare** e ripetere il passaggio **5**.  

8. Nella finestra di dialogo di IoT Edge Custom Module, usare `opcpublisher` come nome per il modulo e il contenitore *immagine URI* come 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Come *opzioni di creazione* usare il codice JSON seguente:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selezionare **salvare** e quindi **successivo** per continuare con la sezione di route.

10. Nella scheda route incollare quanto segue 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/outputs/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/outputs/* INTO $upstream"
      }
    }
    ```

    e selezionare **successivo**

11. Esaminare le informazioni sulla distribuzione e il manifesto.  Apparirà il manifesto della distribuzione precedente.  Selezionare **Submit** (Invia).

12. Dopo aver distribuito i moduli nel dispositivo, è possibile visualizzarli tutti nella pagina **Dettagli dispositivo** del portale. Questa pagina visualizza il nome di ogni modulo distribuito e informazioni utili come lo stato della distribuzione e il codice di uscita.

## <a name="deploying-using-azure-cli"></a>Eseguire la distribuzione con CLI di Azure

### <a name="prerequisites"></a>Prerequisiti

1. Installare la versione più recente del [interfaccia della riga di comando Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) dalla [qui](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Guida introduttiva

1. Salvare il manifesto della distribuzione precedente in un `deployment.json` file.  

2. Per applicare la configurazione a un dispositivo IoT Edge usare il comando seguente:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   Il `device id` parametro è tra maiuscole e minuscole. Il parametro content punta al file del manifesto della distribuzione salvato. 
    ![output di set di moduli di IoT Edge AZ](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Dopo aver distribuito i moduli nel dispositivo, è possibile visualizzarli tutti con il comando seguente:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Il parametro di ID dispositivo è tra maiuscole e minuscole. ![az iot hub module-identity list output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="run-and-debug-locally"></a>Esecuzione e il debug in locale

Per problemi relativi ai ripresa ed eseguirne il debug è utile per eseguire i moduli di Edge in locale usando il [IoT Edge sviluppo simulatore](https://github.com/Azure/iotedgehubdev).  Fornisce un'esperienza di sviluppo locale con un simulatore per la creazione, sviluppo, test, in esecuzione e debug di moduli Azure IoT Edge e soluzioni usando il codice o i bit stesso che vengono usate nell'ambiente di produzione.

### <a name="prerequisites"></a>Prerequisiti

1. Distribuire il dispositivo gemello OPC [dipendenze](howto-opc-twin-deploy-dependencies.md).

2. Installare [Docker CE (18.02.0+)](https://www.docker.com/community-edition) sul [Windows](https://docs.docker.com/docker-for-windows/install/), [macOS](https://docs.docker.com/docker-for-mac/install/) oppure [Linux](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce).

3. Installare [Docker Compose (1.20.0+)](https://docs.docker.com/compose/install/#install-compose) (richiesta solo per **Linux**. Compose è già stato incluso nell'installazione di Windows o macOS Docker CE)

4. Installare [Python (2.7 / 3.5+) e Pip](https://www.python.org/)

5. Installare iotedgehubdev eseguendo seguente comando nel terminale

   ```bash
   pip install --upgrade iotedgehubdev
   ```

> [!NOTE]
> Installare `iotedgehubdev` al **radice** su Linux/macOS (*non usare '-utente ' opzione nel comando "pip install"*).
> Assicurarsi che non è presente alcun runtime di Azure IoT Edge in esecuzione nello stesso computer con iotedgehubdev poiché richiedono le stesse porte.

### <a name="quickstart"></a>Guida introduttiva

1. Per seguire le istruzioni [creare un dispositivo Edge nel portale di Azure](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal).  Copiare la stringa di connessione del dispositivo edge.

2. Consente di impostare il simulatore con la stringa di connessione di edge.

    ```bash
    iotedgehubdev setup -c <edge-device-connection-string>
    ```

3. Copia di sopra di manifesto in un `deployment.json` file nella stessa cartella.  Avviare la distribuzione nel simulatore usando

    ```bash
    iotedgehubdev start -d deployment.json
    ```

4. Arrestare il simulatore usando

   ```bash
   iotedgehubdev stop
   ```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come distribuire gemello OPC da zero, ecco il passaggio successivo consigliato:

> [!div class="nextstepaction"]
> [Distribuire OPC dei dispositivi gemelli in un progetto esistente](howto-opc-twin-deploy-existing.md)