---
title: Convertire i protocolli Modbus con i gateway - Azure IoT Edge | Microsoft Docs
description: Consentire ai dispositivi che usano Modbus TCP di comunicare con l'hub IoT di Azure creando un dispositivo gateway IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 1c9855f982b888e8e1d68bfe5233983db8c826ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61248009"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Connettere dispositivi Modbus TCP tramite un dispositivo gateway IoT Edge

Se si vuole connettere dispositivi IoT che usano il protocollo Modbus TCP o RTU a un hub IoT di Azure, usare un dispositivo IoT Edge come gateway. Il dispositivo gateway legge i dati dai dispositivi Modbus, quindi li comunica al cloud usando un protocollo supportato.

![I dispositivi Modbus si connettono all'IoT Hub tramite il gateway IoT Edge](./media/deploy-modbus-gateway/diagram.png)

Questo articolo descrive come creare un'immagine del contenitore personalizzata per un modulo Modbus (è anche possibile usare un esempio predefinito) e quindi distribuirla nel dispositivo IoT Edge che fungerà da gateway.

Questo articolo presuppone l'uso del protocollo Modbus TCP. Per altre informazioni su come configurare il modulo per supportare il protocollo Modbus RTU, vedere il progetto [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) (Modulo Modbus per Azure IoT Edge) in GitHub.

## <a name="prerequisites"></a>Prerequisiti
* Un dispositivo Azure IoT Edge. Per una procedura dettagliata su come configurarne uno, vedere [distribuire Azure IoT Edge in Windows](quickstart.md) oppure [Linux](quickstart-linux.md).
* La chiave primaria della stringa di connessione del dispositivo IoT Edge.
* Un dispositivo Modbus fisico o simulato che supporta il protocollo Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Preparare un contenitore Modbus

Se si vuole testare la funzionalità del gateway Modbus, Microsoft offre un modulo di esempio che può essere usato a questo scopo. Da Azure Marketplace, si potrà accedere al modulo [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), o con l'URI, dell'immagine **mcr.microsoft.com/azureiotedge/modbus:1.0**.

Se si vuole creare un modulo e quindi personalizzarlo per l'ambiente, è disponibile un [modulo Modbus per Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) open source in GitHub. Seguire le indicazioni nel progetto per creare l'immagine del contenitore personalizzata. Se si crea la propria immagine di contenitore, fare riferimento a [sviluppare C# moduli in Visual Studio](how-to-visual-studio-develop-csharp-module.md) oppure [sviluppare moduli in Visual Studio Code](how-to-vs-code-develop-module.md). Questi articoli forniscono istruzioni sulla creazione di nuovi moduli e pubblicazione di immagini del contenitore in un registro di sistema.

## <a name="try-the-solution"></a>Provare la soluzione

In questa sezione illustra in modo dettagliato la distribuzione esempio Modbus modulo di Microsoft al dispositivo IoT Edge.

1. Nel [portale di Azure](https://portal.azure.com/) passare all'hub IoT.

2. Passare a **IoT Edge** e fare clic sul dispositivo IoT Edge.

3. Selezionare **Imposta moduli**.

4. Aggiungere il modulo Modbus:

   1. Fare clic su **Aggiungi** e selezionare **Modulo IoT Edge**.

   2. Nel campo **Name** (Nome) immettere "modbus".

   3. Nel campo **Image** (Immagine) immettere l'URI immagine del contenitore di esempio: `mcr.microsoft.com/azureiotedge/modbus:1.0`.

   4. Selezionare la casella **Enable** (Abilita) per aggiornare le proprietà desiderate del modulo gemello.

   5. Copiare il codice JSON seguente nella casella di testo. Modificare il valore di **SlaveConnection** in base all'indirizzo IPv4 del dispositivo Modbus.

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. Selezionare **Salva**.

5. Nel passaggio **Aggiungi moduli** selezionare **Avanti**.

7. Nel passaggio **Specify Routes** (Specifica route) copiare il codice JSON seguente nella casella di testo. Questa route invia tutti i messaggi raccolti dal modulo Modbus all'hub IoT. In questa route "modbusOutput" è l'endpoint che utilizzano il modulo Modbus per restituire i dati e "upstream" è una destinazione speciale che indica all'hub IoT Edge per inviare messaggi all'IoT Hub.
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Selezionare **Avanti**.

9. Nel passaggio **Review Deployment** (Verifica la distribuzione) fare clic su **Invia**.

10. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**. Dovrebbe essere visualizzato il nuovo modulo **modbus**, in esecuzione insieme al runtime IoT Edge.

## <a name="view-data"></a>Visualizzare i dati
Visualizzare i dati che passano attraverso il modulo modbus:
```cmd/sh
iotedge logs modbus
```

È anche possibile visualizzare i dati di telemetria inviati dal dispositivo usando l'[estensione Azure IoT Hub Toolkit per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (in precedenza estensione Azure IoT Toolkit).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul modo in cui i dispositivi IoT Edge possono operare come gateway, vedere [creare un dispositivo IoT Edge come gateway trasparente](./how-to-create-transparent-gateway.md).
- Per altre informazioni sul funzionano dei moduli di IoT Edge, vedere [moduli comprendere Azure IoT Edge](iot-edge-modules.md).
