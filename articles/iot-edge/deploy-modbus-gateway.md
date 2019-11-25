---
title: Convertire i protocolli Modbus con i gateway - Azure IoT Edge | Microsoft Docs
description: Consentire ai dispositivi che usano Modbus TCP di comunicare con l'hub IoT di Azure creando un dispositivo gateway IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.openlocfilehash: 649c7f620b83464d1bb56cf4b8191b0747105f01
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457203"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Connettere dispositivi Modbus TCP tramite un dispositivo gateway IoT Edge

Se si vuole connettere dispositivi IoT che usano il protocollo Modbus TCP o RTU a un hub IoT di Azure, usare un dispositivo IoT Edge come gateway. Il dispositivo gateway legge i dati dai dispositivi Modbus, quindi li comunica al cloud usando un protocollo supportato.

![Modbus devices connect to IoT Hub through IoT Edge gateway](./media/deploy-modbus-gateway/diagram.png)

Questo articolo descrive come creare un'immagine del contenitore personalizzata per un modulo Modbus (è anche possibile usare un esempio predefinito) e quindi distribuirla nel dispositivo IoT Edge che fungerà da gateway.

Questo articolo presuppone l'uso del protocollo Modbus TCP. Per altre informazioni su come configurare il modulo per supportare il protocollo Modbus RTU, vedere il progetto [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) (Modulo Modbus per Azure IoT Edge) in GitHub.

## <a name="prerequisites"></a>Prerequisiti
* Un dispositivo Azure IoT Edge. For a walkthrough on how to set up one, see [Deploy Azure IoT Edge on Windows](quickstart.md) or [Linux](quickstart-linux.md).
* La chiave primaria della stringa di connessione del dispositivo IoT Edge.
* Un dispositivo Modbus fisico o simulato che supporta il protocollo Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Preparare un contenitore Modbus

Se si vuole testare la funzionalità del gateway Modbus, Microsoft offre un modulo di esempio che può essere usato a questo scopo. You can access the module from the Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), or with the image URI, **mcr.microsoft.com/azureiotedge/modbus:1.0**.

Se si vuole creare un modulo e quindi personalizzarlo per l'ambiente, è disponibile un [modulo Modbus per Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) open source in GitHub. Seguire le indicazioni nel progetto per creare l'immagine del contenitore personalizzata. To create a container image, refer to [Develop C# modules in Visual Studio](how-to-visual-studio-develop-csharp-module.md) or [Develop modules in Visual Studio Code](how-to-vs-code-develop-module.md). Those articles provide instructions on creating new modules and publishing container images to a registry.

## <a name="try-the-solution"></a>Try the solution

This section walks through deploying Microsoft's sample Modbus module to your IoT Edge device.

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
                  "StartAddress":"40001",
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

7. Nel passaggio **Specify Routes** (Specifica route) copiare il codice JSON seguente nella casella di testo. Questa route invia tutti i messaggi raccolti dal modulo Modbus all'hub IoT. In this route, **modbusOutput** is the endpoint that Modbus module uses to output data and **$upstream** is a special destination that tells IoT Edge hub to send messages to IoT Hub.

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

- To learn more about how IoT Edge devices can act as gateways, see [Create an IoT Edge device that acts as a transparent gateway](./how-to-create-transparent-gateway.md).
- For more information about how IoT Edge modules work, see [Understand Azure IoT Edge modules](iot-edge-modules.md).
