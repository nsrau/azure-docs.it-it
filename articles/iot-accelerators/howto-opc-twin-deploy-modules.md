---
title: Come distribuire il modulo OPC Twin per Azure da zero Documenti Microsoft
description: Questo articolo descrive come distribuire OPC Twin da zero usando il pannello IoT Edge del portale di Azure e anche l'uso dell'interfaccia della riga di comando.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241071"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>Distribuire il modulo OPC Twin e le dipendenze da zero

Il modulo OPC Twin viene eseguito su IoT Edge e fornisce diversi servizi edge per il dispositivo OPC gemello e servizi di registro. 

Sono disponibili diverse opzioni per distribuire i moduli nel gateway [Edge di Azure,](https://azure.microsoft.com/services/iot-edge/) tra cui

- [Distribuzione dal pannello IoT Edge del portale di AzureDeploying from Azure portal's IoT Edge blade](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [Distribuzione tramite l'interfaccia della riga di comando](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> Per ulteriori informazioni sui dettagli e le istruzioni di distribuzione, vedere il [repository](https://github.com/Azure/azure-iiot-components)GitHub .

## <a name="deployment-manifest"></a>Manifesto della distribuzione

Tutti i moduli vengono distribuiti tramite un manifesto di distribuzione.  Di seguito è riportato un manifesto di esempio per distribuire sia [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) che [OPC Twin.](https://github.com/Azure/azure-iiot-opc-twin-module)

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

## <a name="deploying-from-azure-portal"></a>Distribuzione dal portale di AzureDeploying from Azure portal

Il modo più semplice per distribuire i moduli in un dispositivo gateway Edge IoT di Azure è tramite il portale di Azure.The easiest way to deploy the modules to an Azure IoT Edge gateway device is through the Azure portal.  

### <a name="prerequisites"></a>Prerequisiti

1. Distribuire le [dipendenze](howto-opc-twin-deploy-dependencies.md) OPC Twin `.env` e ottenuto il file risultante. Si noti la distribuzione `hub name` della `PCS_IOTHUBREACT_HUB_NAME` variabile nel file risultante. `.env`

2. Registrare e avviare un gateway [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) o `device id` [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge e prendere nota del relativo .

### <a name="deploy-to-an-edge-device"></a>Eseguire la distribuzione in un dispositivo perimetraleDeploy to an edge device

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare all'hub IoT.

2. Selezionare **Bordo IoT** dal menu a sinistra.

3. Fare clic sull'ID del dispositivo di destinazione nell'elenco dei dispositivi.

4. Selezionare **Set Modules** (Configura i moduli).

5. Nella sezione Moduli di **distribuzione** della pagina selezionare **Aggiungi** e modulo **Edge IoT.**

6. Nella finestra **di dialogo Modulo personalizzato IoT Edge** utilizzare `opctwin` come nome per il modulo, quindi specificare l'URI *immagine* del contenitore come

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   Come *opzioni di creazione contenitore*, utilizzare il codice JSON seguente:As Container Create Options , use the following JSON:

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   Specificare i campi facoltativi, se necessario. Per altre informazioni sulle opzioni di creazione dei contenitore, i criteri di riavvio e lo stato desiderato, vedere [Proprietà desiderate di EdgeAgent](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties). Per altre informazioni sul modulo gemello, vedere [Definire o aggiornare le proprietà desiderate](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties).

7. Selezionare **Salva** e ripetere il passaggio **5**.  

8. Nella finestra di dialogo Modulo `opcpublisher` personalizzato IoT Edge, utilizzare come nome per il modulo e *l'URI dell'immagine* del contenitore come 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   Come *opzioni di creazione contenitore*, utilizzare il codice JSON seguente:As Container Create Options , use the following JSON:

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. Selezionare **Salva** e quindi **Avanti** per passare alla sezione dei percorsi.

10. Nella scheda percorsi, incollare le seguenti 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    e selezionare **Avanti**

11. Esaminare le informazioni sulla distribuzione e il manifesto.  Dovrebbe essere simile al manifesto di distribuzione precedente.  Selezionare **Submit**.

12. Dopo aver distribuito i moduli nel dispositivo, è possibile visualizzarli tutti nella pagina **Dettagli dispositivo** del portale. Questa pagina visualizza il nome di ogni modulo distribuito e informazioni utili come lo stato della distribuzione e il codice di uscita.

## <a name="deploying-using-azure-cli"></a>Distribuzione con l'interfaccia della riga di comando di AzureDeploying using Azure CLI

### <a name="prerequisites"></a>Prerequisiti

1. Installare la versione più recente [dell'interfaccia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) della riga di comando di Azure da [qui](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="quickstart"></a>Guida introduttiva

1. Salvare il manifesto di `deployment.json` distribuzione precedente in un file.  

2. Per applicare la configurazione a un dispositivo IoT Edge usare il comando seguente:

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   Il `device id` parametro fa distinzione tra maiuscole e minuscole. Il parametro content punta al file del manifesto della distribuzione salvato. 
    ![az IoT Edge set-modules di uscita](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. Dopo aver distribuito i moduli nel dispositivo, è possibile visualizzarli tutti con il comando seguente:

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   Il parametro ID dispositivo fa distinzione tra maiuscole e minuscole. ![az iot hub module-identity list output](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>Passaggi successivi

Ora che hai imparato a distribuire OPC Twin da zero, ecco il passaggio successivo suggerito:

> [!div class="nextstepaction"]
> [Distribuire OPC Twin in un progetto esistenteDeploy OPC Twin to an existing project](howto-opc-twin-deploy-existing.md)
