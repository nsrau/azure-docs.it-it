---
title: Distribuire moduli su larga scala usando Visual Studio Code - Azure IoT EdgeDeploy modules at scale using Visual Studio Code - Azure IoT Edge
description: Usare l'estensione IoT per Visual Studio Code per creare distribuzioni automatiche per gruppi di dispositivi IoT Edge.Use the IoT extension for Visual Studio Code to create automatic deployments for groups of IoT Edge devices.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774133"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Distribuire moduli IoT Edge su larga scala utilizzando il codice di Visual StudioDeploy IoT Edge modules at scale using Visual Studio Code

È possibile creare una **distribuzione automatica di IoT Edge** usando visual Studio Code per gestire le distribuzioni in corso per molti dispositivi contemporaneamente. Le distribuzioni automatiche per IoT Edge fanno parte della funzionalità di gestione automatica dei dispositivi dell'hub IoT.Automatic deployments for IoT Edge are part of the [automatic device management](/azure/iot-hub/iot-hub-automatic-device-management) feature of IoT Hub. Le distribuzioni sono processi dinamici che consentono di distribuire più moduli in più dispositivi. È inoltre possibile tenere traccia dello stato e dell'integrità dei moduli e apportare modifiche quando necessario.

Per ulteriori informazioni, vedere [Informazioni sulle distribuzioni automatiche di IoT Edge per singoli dispositivi o su larga scala](module-deployment-monitoring.md).

In questo articolo vengono configurate visual Studio Code e l'estensione IoT.In this article, you set up Visual Studio Code and the IoT extension. Si apprenderà quindi come distribuire i moduli in un set di dispositivi IoT Edge.You then learn how to deploy modules to a set of IoT Edge devices.

## <a name="prerequisites"></a>Prerequisiti

* Un hub IoT nella sottoscrizione di Azure.An [IoT hub](../iot-hub/iot-hub-create-through-portal.md) in your Azure subscription.
* Un [dispositivo IoT Edge](how-to-register-device.md#register-with-visual-studio-code) con il runtime di IoT Edge installato.
* [Codice di Visual Studio](https://code.visualstudio.com/).
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) per Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

È possibile usare le estensioni IoT di Azure per Visual Studio Code per eseguire operazioni con l'hub. Affinché queste operazioni funzionino, è necessario accedere all'account Azure e selezionare l'hub IoT su cui si sta lavorando.

1. In Visual Studio Code aprire la **finestra di esplorazione**.

1. Nella parte inferiore di Esplora risorse espandere la sezione Hub IoT di Azure.At the bottom of the Explorer, expand the **Azure IoT Hub** section.

1. Fare clic su ... nell'intestazione della sezione Hub IoT di Azure.Click on **the ...** in the **Azure IoT Hub** section header. Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione.

1. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).

1. Se non è stato effettuato l'accesso all'account Azure, seguire le istruzioni visualizzate.

1. Selezionare la sottoscrizione di Azure.

1. Selezionare l'hub IoT.

## <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto di distribuzione è un documento JSON che descrive i moduli da distribuire. Viene inoltre descritto il flusso dei dati tra i moduli e le proprietà desiderate dei moduli gemelli. Per ulteriori informazioni, consultate [Informazioni su come distribuire moduli e stabilire percorsi in IoT Edge.](module-composition.md)

Per distribuire i moduli tramite Visual Studio Code, salvare il manifesto della distribuzione a livello locale come file con estensione JSON. È necessario specificare la posizione quando si esegue il comando per applicare la configurazione al dispositivo.

Di seguito è riportato un esempio di manifesto della distribuzione di base con un solo modulo:

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
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

Se è necessario determinare quali dispositivi IoT Edge è attualmente possibile configurare, eseguire il comando **IoT Edge: Get Device Info.**

## <a name="identify-devices-with-target-conditions"></a>Identificare i dispositivi con condizioni di destinazione

Per identificare i dispositivi IoT Edge che devono ricevere la distribuzione, è necessario specificare una condizione di destinazione. Una condizione di destinazione viene soddisfatta quando i criteri specificati corrispondono a un deviceId, un valore di tag o un valore di proprietà segnalato.

Configurare i tag nel dispositivo gemello. Di seguito è riportato un esempio di un dispositivo gemello con tag:Here is an example of a device twin that has tags:

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

Questo dispositivo riceverà una distribuzione se la condizione di destinazione per la distribuzione contiene `tag.location.building = '20'`un'espressione che corrisponde a uno dei valori del tag, ad esempio .

Se si desidera impostare come destinazione un dispositivo specifico indipendentemente dai relativi tag o altri valori, è sufficiente specificare l'oggetto `deviceId` per la condizione di destinazione.

Ecco alcuni altri esempi:

* deviceId ='linuxprod1'
* deviceId - 'linuxprod1' OR deviceId ' linuxprod2' OR deviceId 'linuxprod3'
* tags.environment ='prod'
* tags.environment - 'prod' AND tags.location '
* tags.environment - 'prod' OR tags.location '
* tags.operator - 'John' AND tags.environment ' prod' AND NOT deviceId ' linuxprod1'

Vedere [la condizione di destinazione](module-deployment-monitoring.md#target-condition) per i dettagli. Per altre informazioni sui dispositivi gemelli e i tag, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Modificare il dispositivo gemello

È possibile modificare il dispositivo gemello in Visual Studio Code per configurare i tag. Dal menu **Visualizza,** selezionare **Tavolozza comandi** ed eseguire il comando **IoT Edge: Edit Device Twin.** Selezionare il dispositivo IoT Edge e viene visualizzato il dispositivo gemello.

In questo esempio, non sono stati definiti tag. Sostituire la sezione `"tags": {}` vuota corrente con la propria definizione di tag.

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

Dopo aver salvato il file locale, eseguire il comando **IoT Edge: Update Device Twin.**

## <a name="create-deployment-at-scale"></a>Creare la distribuzione su larga scalaCreate deployment at scale

Dopo aver configurato il manifesto di distribuzione e configurato i tag nel dispositivo gemello, è possibile eseguire la distribuzione.

1. Scegliere **Tavolozza comandi dal** menu **Visualizza** e selezionare il comando **Azure IoT Edge: Crea distribuzione in base alla scala.**

1. Passare al file JSON del manifesto di distribuzione che si vuole usare e fare clic su **Select Edge deployment manifest** (Seleziona il manifesto della distribuzione di Edge).

1. Specificare i valori come richiesto, a partire **dall'ID di distribuzione.**

   ![Specificare un ID di distribuzioneSpecify a deployment id](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Specificare i valori per questi parametri:

  | Parametro | Descrizione |
  | --- | --- |
  | ID distribuzione | Nome della distribuzione che verrà creata nell'hub IoT. Assegnare alla distribuzione un nome univoco contenente al massimo 128 lettere minuscole. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`. |
  | Condizione di destinazione | Immettere una condizione di destinazione per determinare quali dispositivi verranno destinati a questa distribuzione.La condizione è basata sui tag o sulle proprietà segnalate dei dispositivi gemelli e deve corrispondere al formato di espressione.Ad esempio, `tags.environment='test' and properties.reported.devicemodel='4000x'`. |
  | Priorità |  Numero intero positivo. Se due o più distribuzioni sono destinate allo stesso dispositivo, verrà applicata la distribuzione con il valore numerico più alto per Priorità. |

  Dopo aver specificato la priorità, il terminale dovrebbe visualizzare un output simile al seguente:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Monitoraggio e modifica delle distribuzioni

Usare [l'interfaccia della riga di comando](how-to-deploy-monitor-cli.md#monitor-a-deployment) di Azure o il portale di [Azure](how-to-deploy-monitor.md#monitor-a-deployment) per monitorare, modificare ed eliminare le distribuzioni. Entrambi forniscono metriche nelle distribuzioni.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla [distribuzione di moduli nei dispositivi IoT Edge](module-deployment-monitoring.md).
