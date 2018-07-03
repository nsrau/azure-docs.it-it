---
title: File di inclusione
description: File di inclusione
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/27/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 9c562f1ce938e5f5d9371cbccf032c0eb1d67125
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055031"
---
Una delle principali funzionalità di Azure IoT Edge è la possibilità di distribuire i moduli nei dispositivi IoT Edge dal cloud. Un modulo di IoT Edge è un pacchetto eseguibile implementato come contenitore. In questa sezione si distribuisce un modulo che genera dati di telemetria per il dispositivo simulato. 

1. Nel portale di Azure passare all'hub IoT.
1. Passare a **IoT Edge** e selezionare il dispositivo IoT Edge.
1. Selezionare **Set Modules** (Configura i moduli).
1. Nella sezione dei **moduli di distribuzione** della pagina fare clic su **Aggiungi** e quindi selezionare **Modulo IoT Edge**.
1. Nel campo **Nome** immettere `tempsensor`. 
1. Nel campo **URI immagine** immettere `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. 
1. Lasciare invariate le altre impostazioni e selezionare **Salva**.

   ![Salvare il modulo IoT Edge dopo l'immissione di nome e URI dell'immagine](./media/iot-edge-deploy-module/name-image.png)

1. Nel passaggio **Aggiungi moduli** fare clic su **Avanti**.
1. Nel passaggio **Specifica route** deve essere presente una route predefinita che invia tutti i messaggi da tutti i moduli per l'hub IoT. In caso contrario, aggiungere il codice seguente e quindi selezionare **Avanti**.

   ```json
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

1. Nel passaggio **Review Deployment** (Verifica la distribuzione) fare clic su **Invia**.
1. Tornare alla pagina dei dettagli del dispositivo e selezionare **Aggiorna**. Oltre al modulo edgeAgent, creato quando il servizio è stato avviato per la prima volta, vengono visualizzati un altro modulo di runtime denominato **edgeHub** e il modulo **tempSensor** elencato. 

   ![Visualizzazione di tempSensor nell'elenco di moduli distribuiti](./media/iot-edge-deploy-module/deployed-modules.png)
