---
title: File di inclusione
description: File di inclusione
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/14/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 06e2be14cf7592edf373b6f679ac5d9ff3c87269
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161457"
---
Una delle principali funzionalità di Azure IoT Edge è la possibilità di distribuire i moduli nei dispositivi IoT Edge dal cloud. Un modulo di IoT Edge è un pacchetto eseguibile implementato come contenitore. In questa sezione si distribuisce un modulo che genera dati di telemetria per il dispositivo simulato. 

1. Nel portale di Azure passare all'hub IoT.
1. Passare a **IoT Edge** e selezionare il dispositivo IoT Edge.
1. Selezionare **Set Modules** (Configura i moduli).
1. Nella sezione dei **moduli di distribuzione** della pagina fare clic su **Aggiungi** e quindi selezionare **Modulo IoT Edge**.
1. Nel campo **Nome** immettere `tempSensor`. 
1. Nel campo **URI immagine** immettere `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. 
1. Lasciare invariate le altre impostazioni e selezionare **Salva**.

   ![Salvare il modulo IoT Edge dopo l'immissione di nome e URI dell'immagine](./media/iot-edge-deploy-module/name-image.png)

1. Nel passaggio **Aggiungi moduli** fare clic su **Avanti**.
1. Nel passaggio **Specifica route** deve essere presente una route predefinita che invia tutti i messaggi di tutti i moduli all'hub IoT. In caso contrario, aggiungere il codice seguente e quindi selezionare **Avanti**.

   ```json
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

1. Nel passaggio **Review Deployment** (Verifica la distribuzione) fare clic su **Invia**.
1. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**. Oltre al modulo edgeAgent, creato quando il servizio è stato avviato per la prima volta, vengono visualizzati un altro modulo di runtime denominato **edgeHub** e il modulo **tempSensor** elencato. 

   ![Visualizzazione di tempSensor nell'elenco di moduli distribuiti](./media/iot-edge-deploy-module/deployed-modules.png)
