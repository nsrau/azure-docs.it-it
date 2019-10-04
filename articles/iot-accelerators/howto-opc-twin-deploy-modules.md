---
title: Come distribuire il modulo OPC Twin per Azure da zero | Microsoft Docs
description: Come distribuire un dispositivo OPC gemello da zero.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: df1dd45d58baf82710b5e362afaf055aad140b98
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302633"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Distribuisci il modulo e le dipendenze di OPC Twin da zero

Il modulo OPC gemello viene eseguito su IoT Edge e fornisce diversi servizi perimetrali ai servizi OPC e del registro di sistema del dispositivo OPC. 

Sono disponibili diverse opzioni per distribuire i moduli nel gateway [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/) , tra cui

- [Distribuzione dal pannello IoT Edge di portale di Azure](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Distribuzione tramite AZ CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Per ulteriori informazioni sui dettagli e le istruzioni per la distribuzione, vedere il [repository](https://github.com/Azure/azure-iiot-components)github.

## <a name="deployment-manifest"></a>Manifesto della distribuzione

Tutti i moduli vengono distribuiti usando un manifesto della distribuzione.  Di seguito è riportato un manifesto di esempio per distribuire sia il [server di pubblicazione OPC](https://github.com/Azure/iot-edge-opc-publisher) che l' [OPC gemello](https://github.com/Azure/azure-iiot-opc-twin-module) .

```json
{
  "content": {
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
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>Distribuzione da portale di Azure

Il modo più semplice per distribuire i moduli in un dispositivo gateway Azure IoT Edge consiste nel portale di Azure.  

### <a name="prerequisites"></a>Prerequisiti

1. Distribuire le [dipendenze](howto-opc-twin-deploy-dependencies.md) del dispositivo OPC gemello e ottenere il file `.env` risultante. Si noti la `hub name` distribuzione `PCS_IOTHUBREACT_HUB_NAME` della variabile nel file risultante `.env` .

2. Registrare e avviare un gateway di IoT Edge [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) o [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) e prendere `device id`nota del relativo.

### <a name="deploy-to-an-edge-device"></a>Eseguire la distribuzione in un dispositivo perimetrale

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare all'hub IoT.

2. Selezionare **IOT Edge** dal menu a sinistra.

3. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi.

4. Selezionare **Set Modules** (Configura i moduli).

5. Nella sezione **moduli di distribuzione** della pagina selezionare **Aggiungi** e **IOT Edge modulo.**

6. Nella finestra di dialogo **IOT Edge modulo personalizzato** usare `opctwin` come nome per il modulo, quindi specificare l' *URI dell'immagine* del contenitore come

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Per le *Opzioni di creazione di contenitori*, usare il codice JSON seguente:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Specificare i campi facoltativi, se necessario. Per altre informazioni sulle opzioni di creazione dei contenitore, i criteri di riavvio e lo stato desiderato, vedere [Proprietà desiderate di EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Per altre informazioni sul modulo gemello, vedere [Definire o aggiornare le proprietà desiderate](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Selezionare **Save (Salva** ) e ripetere il passaggio **5**.  

8. Nella finestra di dialogo IOT Edge modulo personalizzato usare `opcpublisher` come nome per il modulo e l' *URI dell'immagine* del contenitore come 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Per le *Opzioni di creazione di contenitori*, usare il codice JSON seguente:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selezionare **Save (Salva** ) e quindi **Avanti** per passare alla sezione Routes (Route).

10. Nella scheda Route incollare il codice seguente: 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    e selezionare **Avanti**

11. Esaminare le informazioni sulla distribuzione e il manifesto.  Dovrebbe essere simile al manifesto di distribuzione precedente.  Selezionare **Submit** (Invia).

12. Dopo aver distribuito i moduli nel dispositivo, è possibile visualizzarli tutti nella pagina **Dettagli dispositivo** del portale. Questa pagina visualizza il nome di ogni modulo distribuito e informazioni utili come lo stato della distribuzione e il codice di uscita.

## <a name="deploying-using-azure-cli"></a>Distribuzione con interfaccia della riga di comando di Azure

### <a name="prerequisites"></a>Prerequisiti

1. Installare la versione più recente dell' [interfaccia della riga di comando di Azure (AZ)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) da [qui](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Guida introduttiva

1. Salvare il manifesto di distribuzione precedente in `deployment.json` un file.  

2. Per applicare la configurazione a un dispositivo IoT Edge usare il comando seguente:

   ```bash
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   Il `device id` parametro fa distinzione tra maiuscole e minuscole. Il parametro content punta al file del manifesto della distribuzione salvato. 
    ![AZ IoT Edge set-Modules output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Dopo aver distribuito i moduli nel dispositivo, è possibile visualizzarli tutti con il comando seguente:

   ```bash
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Il parametro ID dispositivo distingue tra maiuscole e minuscole. ![az iot hub module-identity list output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come distribuire OPC Twin da zero, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Distribuire un dispositivo OPC gemello a un progetto esistente](howto-opc-twin-deploy-existing.md)
