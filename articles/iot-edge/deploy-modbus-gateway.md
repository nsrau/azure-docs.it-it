---
title: Distribuire Modbus in Azure IoT Edge | Microsoft Docs
description: Consentire ai dispositivi che usano Modbus TCP di comunicare con l'hub IoT di Azure creando un dispositivo gateway IoT Edge
services: iot-Edge
documentationcenter: 
author: kgremban
manager: timlt
editor: chrisgmsft
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: kgremban
ms.custom: 
ms.openlocfilehash: e239bde48c3da0d899e3c78bdd39f520c4128b95
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway---preview"></a>Connettere dispositivi Modbus TCP tramite un dispositivo gateway IoT Edge - Anteprima

Se si vuole connettere dispositivi IoT che usano il protocollo Modbus TCP o RTU a un hub IoT di Azure, usare un dispositivo IoT Edge come gateway. Il dispositivo gateway legge i dati dai dispositivi Modbus, quindi li comunica al cloud usando un protocollo supportato. 

![I dispositivi Modbus si connettono all'hub IoT tramite il gateway Edge](./media/deploy-modbus-gateway/diagram.png)

Questo articolo descrive come creare un'immagine del contenitore personalizzata per un modulo Modbus (è anche possibile usare un esempio predefinito) e quindi distribuirla nel dispositivo IoT Edge che fungerà da gateway. 

Questo articolo presuppone l'uso del protocollo Modbus TCP. Per altre informazioni su come configurare il modulo per supportare il protocollo Modbus RTU, vedere il progetto [Azure IoT Edge Modbus module](https://github.com/Azure/iot-edge-modbus) (Modulo Modbus per Azure IoT Edge) in Github. 

## <a name="prerequisites"></a>prerequisiti
* Un dispositivo Azure IoT Edge. Per una procedura dettagliata su come configurarne uno, vedere [Distribuire Azure IoT Edge su un dispositivo simulato in Windows](tutorial-simulate-device-windows.md) o [Distribuire Azure IoT Edge su un dispositivo simulato in Linux](tutorial-simulate-device-linux.md). 
* La chiave primaria della stringa di connessione del dispositivo IoT Edge.
* Un dispositivo Modbus fisico o simulato che supporta il protocollo Modbus TCP.

## <a name="prepare-a-modbus-container"></a>Preparare un contenitore Modbus

Se si vuole testare la funzionalità del gateway Modbus, Microsoft offre un modulo di esempio che può essere usato a questo scopo. Per usare il modulo di esempio, passare alla sezione [Eseguire la soluzione](#run-the-solution) e immettere il valore seguente per l'URI immagine: 

```URL
microsoft/azureiotedge-modbus-tcp:1.0-preview
```

Se si vuole creare un modulo e quindi personalizzarlo per l'ambiente, è disponibile un [modulo Modbus per Azure IoT Edge](https://github.com/Azure/iot-edge-modbus) open source in Github. Seguire le indicazioni nel progetto per creare l'immagine del contenitore personalizzata. Se si crea un'immagine del contenitore personalizzata, vedere [Sviluppare e distribuire un modulo C# per IoT Edge in un dispositivo simulato](tutorial-csharp-module.md) per istruzioni sulla pubblicazione di immagini del contenitore in un registro e sulla distribuzione di un modulo personalizzato nel dispositivo. 


## <a name="run-the-solution"></a>Eseguire la soluzione
1. Nel [portale di Azure](https://portal.azure.com/) passare all'hub IoT.
2. Passare a **IoT Edge (preview)** (IoT Edge - anteprima) e selezionare il dispositivo IoT Edge.
3. Selezionare **Set modules** (Configura i moduli).
4. Aggiungere il modulo Modbus:
   1. Selezionare **Add IoT Edge module** (Aggiungi il modulo di IoT Edge).
   2. Nel campo **Name** (Nome) immettere "modbus".
   3. Nel campo **Image** (Immagine) immettere l'URI immagine del contenitore di esempio: `microsoft/azureiotedge-modbus-tcp:1.0-preview`.
   4. Selezionare la casella **Enable** (Abilita) per aggiornare le proprietà desiderate del modulo gemello.
   5. Copiare il codice JSON seguente nella casella di testo. Modificare il valore di **SlaveConnection** in base all'indirizzo IPv4 del dispositivo Modbus.

      ```JSON
      {  
        "properties.desired":{  
          "PublishInterval":"2000",
          "SlaveConfigs":{  
            "Slave01":{  
              "SlaveConnection":"<IPV4 address>",
              "HwId":"PowerMeter-0a:01:01:01:01:01",
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
5. Nel passaggio **Add Modules** (Aggiungi moduli) selezionare **Next** (Avanti).
7. Nel passaggio **Specify Routes** (Specifica route) copiare il codice JSON seguente nella casella di testo. Questa route invia tutti i messaggi raccolti dal modulo Modbus all'hub IoT. In questa route "modbusOutput" è l'endpoint usato dal modulo Modbus per restituire i dati, mentre "upstream" è una destinazione speciale che indica all'hub Edge di inviare i messaggi all'hub IoT. 
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. Selezionare **Avanti**. 
9. Nel passaggio **Review Template** (Verifica il modello) fare clic su **Invia**. 
10. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**. Dovrebbe essere visualizzato il nuovo modulo **modbus**, in esecuzione insieme al runtime IoT Edge.

## <a name="view-data"></a>Visualizzare i dati
Visualizzare i dati che passano attraverso il modulo modbus:
```cmd/sh
docker logs -f modbus
```

È anche possibile visualizzare i dati di telemetria inviati dal dispositivo tramite lo [strumento di esplorazione dell'hub IoT](https://github.com/azure/iothub-explorer). 

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul modo in cui i dispositivi IoT Edge possono operare come gateway, vedere [Creare un dispositivo IoT Edge come gateway trasparente](how-to-create-transparent-gateway.md)
- Per altre informazioni sul funzionamento dei moduli IoT Edge, vedere [Informazioni sui moduli Azure IoT Edge](iot-edge-modules.md)