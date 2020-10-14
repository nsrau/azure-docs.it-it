---
title: Distribuisci moduli su larga scala con Visual Studio Code Azure IoT Edge
description: Usare l'estensione Internet per Visual Studio Code per creare distribuzioni automatiche per gruppi di dispositivi IoT Edge.
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7f6e90edc0503326dc9dbb06abfcf59fa2d51e1e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043817"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>Distribuisci moduli IoT Edge su larga scala usando Visual Studio Code

È possibile creare una **IOT Edge distribuzione automatica** usando Visual Studio Code per gestire contemporaneamente le distribuzioni in corso per molti dispositivi. Le distribuzioni automatiche per IoT Edge rientrano nella funzionalità di [gestione automatica dei dispositivi](../iot-hub/iot-hub-automatic-device-management.md) dell'hub IoT. Le distribuzioni sono processi dinamici che consentono di distribuire più moduli a più dispositivi. È anche possibile tenere traccia dello stato e dell'integrità dei moduli e apportare modifiche quando necessario.

Per altre informazioni, vedere [Informazioni sulle distribuzioni automatiche di IoT Edge per singoli dispositivi o su vasta scala](module-deployment-monitoring.md).

In questo articolo si configurano Visual Studio Code e l'estensione Internet. Si apprenderà quindi come distribuire i moduli in un set di dispositivi IoT Edge.

## <a name="prerequisites"></a>Prerequisiti

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) nella sottoscrizione di Azure.
* Uno o più dispositivi IoT Edge.

  Se non si dispone di un dispositivo IoT Edge configurato, è possibile crearne uno in una macchina virtuale di Azure. Per [creare un dispositivo Linux virtuale](quickstart-linux.md) o [creare un dispositivo Windows virtuale](quickstart.md), seguire la procedura descritta in uno degli articoli introduttivi.

* [Visual Studio Code](https://code.visualstudio.com/).
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview) per Visual Studio Code.

## <a name="sign-in-to-access-your-iot-hub"></a>Eseguire l'accesso all'hub IoT

È possibile usare le estensioni Azure per le attività Visual Studio Code per eseguire operazioni con l'hub. Per il corretto funzionamento di queste operazioni, è necessario accedere al proprio account Azure e selezionare l'hub Internet delle cose su cui si sta lavorando.

1. In Visual Studio Code aprire la **finestra di esplorazione**.

1. Nella parte inferiore di Explorer, espandere la sezione **Hub IoT di Azure**.

1. Fare clic sui puntini di sospensione ( **...** ) nell'intestazione della sezione **Hub IoT di Azure**. Se i puntini di sospensione non sono visibili, passare il puntatore sull'intestazione.

1. Scegliere **Select IoT Hub** (Seleziona l'hub IoT).

1. Se non è stato effettuato l'accesso all'account Azure, seguire le istruzioni per eseguire questa operazione.

1. Selezionare la sottoscrizione di Azure.

1. Selezionare l'hub IoT.

## <a name="configure-a-deployment-manifest"></a>Configurare un manifesto della distribuzione

Un manifesto di distribuzione è un documento JSON che descrive i moduli da distribuire. Viene inoltre descritto il flusso dei dati tra i moduli e le proprietà desiderate dei moduli gemelli. Per altre informazioni, vedere [Informazioni su come distribuire moduli e definire route in IoT Edge](module-composition.md).

Per distribuire i moduli tramite Visual Studio Code, salvare il manifesto della distribuzione a livello locale come file con estensione JSON. Quando si esegue il comando per applicare la configurazione al dispositivo, sarà necessario specificarne il percorso.

Di seguito è riportato un esempio di manifesto della distribuzione di base con un solo modulo:

>[!NOTE]
>Questo manifesto di distribuzione di esempio usa la versione dello schema 1,1 per l'agente IoT Edge e l'hub. La versione dello schema 1,1 è stata rilasciata insieme a IoT Edge versione 1.0.10 e Abilita funzionalità come l'ordine di avvio del modulo e la priorità delle route.

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
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
              "version": "1.1",
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

Se è necessario determinare quali dispositivi IoT Edge è attualmente possibile configurare, eseguire il comando **IOT Edge: Ottieni informazioni sul dispositivo** .

## <a name="identify-devices-with-target-conditions"></a>Identificare i dispositivi con le condizioni di destinazione

Per identificare i dispositivi IoT Edge che devono ricevere la distribuzione, è necessario specificare una condizione di destinazione. Una condizione di destinazione viene soddisfatta quando per i criteri specificati viene stabilita una corrispondenza tra un deviceId, un valore di tag o un valore della proprietà segnalato.

È possibile configurare i tag nel dispositivo gemello. Di seguito è riportato un esempio di un dispositivo gemello con Tag:

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

Il dispositivo riceverà una distribuzione se la condizione di destinazione per la distribuzione contiene un'espressione che corrisponde a uno dei valori del tag, ad esempio `tag.location.building = '20'` .

Se si vuole scegliere come destinazione un dispositivo specifico indipendentemente dai tag o da altri valori, è sufficiente specificare `deviceId` per la condizione di destinazione.

Ecco alcuni altri esempi:

* deviceId ='linuxprod1'
* deviceId =' linuxprod1' o deviceId =' linuxprod2' o deviceId =' linuxprod3'
* tags.environment ='prod'
* Tags. Environment =' prod ' e Tags. location =' westus2'
* Tags. Environment =' prod ' o Tags. location =' westus2'
* Tags. Operator =' John ' e Tags. Environment =' prod ' e NOT deviceId =' linuxprod1'

Per informazioni dettagliate, vedere [condizione di destinazione](module-deployment-monitoring.md#target-condition) . Per altre informazioni sui dispositivi gemelli e i tag, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).

### <a name="edit-the-device-twin"></a>Modificare il dispositivo gemello

È possibile modificare il dispositivo gemello in Visual Studio Code per configurare i tag. Dal menu **Visualizza** selezionare **tavolozza comandi** ed eseguire il comando **IOT Edge: modifica dispositivo gemello** . Selezionare il dispositivo IoT Edge e viene visualizzato il dispositivo gemello.

In questo esempio non è stato definito alcun tag. Sostituire la sezione vuota corrente `"tags": {}` con la definizione di tag personalizzata.

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

Dopo aver salvato il file locale, eseguire il comando **IOT Edge: aggiorna il dispositivo gemello** .

## <a name="create-deployment-at-scale"></a>Crea distribuzione su larga scala

Dopo aver configurato il manifesto di distribuzione e i tag configurati nel dispositivo gemello, si è pronti per la distribuzione.

1. Dal menu **Visualizza** selezionare **riquadro comandi** e selezionare il comando **Azure IOT Edge: Crea distribuzione su scala** .

1. Passare al file JSON del manifesto di distribuzione che si vuole usare e fare clic su **Select Edge deployment manifest** (Seleziona il manifesto della distribuzione di Edge).

1. Specificare i valori come richiesto, a partire dall' **ID distribuzione**.

   ![Specificare un ID distribuzione](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   Specificare i valori per i parametri seguenti:

  | Parametro | Descrizione |
  | --- | --- |
  | ID distribuzione | Nome della distribuzione che verrà creata nell'hub Internet. Assegnare alla distribuzione un nome univoco contenente al massimo 128 lettere minuscole. Evitare gli spazi e i seguenti caratteri non validi: `& ^ [ ] { } \ | " < > /`. |
  | Condizione di destinazione | Immettere una condizione di destinazione per determinare quali dispositivi saranno assegnati a questa distribuzione. La condizione è basata sui tag o sulle proprietà segnalate dei dispositivi gemelli e deve corrispondere al formato di espressione.Ad esempio, `tags.environment='test' and properties.reported.devicemodel='4000x'` . |
  | Priorità |  Numero intero positivo. Se due o più distribuzioni sono destinate allo stesso dispositivo, viene applicata la distribuzione con il valore numerico più elevato per Priority. |

  Dopo aver specificato la priorità, nel terminale dovrebbe essere visualizzato un output simile a quello illustrato di seguito:

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>Monitoraggio e modifica delle distribuzioni

Usare il [portale di Azure](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal) o l' [interfaccia](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) della riga di comando di Azure per monitorare, modificare ed eliminare le distribuzioni. Entrambi forniscono metriche sulle distribuzioni.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [distribuzione di moduli nei dispositivi IoT Edge](module-deployment-monitoring.md).