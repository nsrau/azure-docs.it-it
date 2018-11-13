---
title: Creare una nuova simulazione IoT - Azure | Microsoft Docs
description: In questa esercitazione si usa Simulazione dispositivi per creare ed eseguire una nuova simulazione.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 268efa35f63ac8b9e0c6ea3f22a71242a2984726
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50756783"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Esercitazione: Creare ed eseguire una simulazione di dispositivi IoT

In questa esercitazione si usa Simulazione dispositivi per creare ed eseguire una nuova simulazione IoT usando uno o più dispositivi simulati.

In questa esercitazione:

>[!div class="checklist"]
> * Visualizzare tutte le simulazioni attive e cronologiche
> * Creare ed eseguire una nuova simulazione
> * Visualizzare le metriche per una simulazione
> * Arrestare una simulazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per seguire questa esercitazione, è necessaria un'istanza distribuita di Simulazione dispositivi nella sottoscrizione di Azure.

Se la soluzione Simulazione dispositivi non è stata ancora distribuita, è necessario completare la guida introduttiva [Distribuire ed eseguire una soluzione di simulazione dei dispositivi in Azure](quickstart-device-simulation-deploy.md).

## <a name="open-device-simulation"></a>Aprire Simulazione dispositivi

Per eseguire Simulazione dispositivi nel browser, passare innanzitutto al sito [Acceleratori di soluzione Microsoft Azure IoT](https://www.azureiotsolutions.com). 

Potrebbe essere necessario accedere con le credenziali della sottoscrizione di Azure.

Fare quindi clic su **Avvio** nel riquadro per la soluzione Simulazione dispositivi distribuita nella [guida introduttiva](quickstart-device-simulation-deploy.md).

## <a name="view-simulations"></a>Visualizzare le simulazioni

Selezionare **Simulations** (Simulazioni) nella barra dei menu. Nella pagina **Simulations** (Simulazioni) vengono visualizzate informazioni su tutte le simulazioni disponibili. In questa pagina è possibile visualizzare le simulazioni attualmente in esecuzione e quelle eseguite in precedenza. Per rieseguire una simulazione precedente, fare clic sul riquadro della simulazione per visualizzarne i dettagli:

![Simulazioni](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Creare una simulazione

Per creare una simulazione, fare clic su **+ New Simulation** (+ Nuova simulazione) nell'angolo in alto a destra.

Una simulazione è costituita da uno o più modelli di dispositivo. Il modello di dispositivo definisce il comportamento, la telemetria e il formato del messaggio del dispositivo da simulare.

Per aggiungere un modello di dispositivo, fare clic su **+ Add a device type** (+ Aggiungi un tipo di dispositivo) e selezionare il modello di dispositivo dall'elenco. La simulazione può avere più di un modello di dispositivo. Ogni modello di dispositivo può avere un numero di dispositivi e una frequenza di messaggi diversi.

Una volta completato il modulo della nuova simulazione, fare clic su **Start Simulation** (Avvia simulazione) per iniziare la simulazione.

A seconda del numero di dispositivi simulati, potrebbero essere necessari alcuni minuti per la configurazione e l'avvio della simulazione:

![Nuova simulazione](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Arrestare una simulazione

Quando si fa clic su **Start Simulation** (Avvia simulazione), viene visualizzata la pagina dei dettagli della simulazione. Questa pagina dei dettagli mostra le metriche dell'hub IoT e le statistiche della simulazione in tempo reale. È anche possibile passare a questa pagina dei dettagli facendo clic sul riquadro della simulazione nella pagina **Simulations** (Simulazioni).

Per arrestare una simulazione, fare clic su **Stop Simulation** (Arresta simulazione) nella barra delle azioni in alto a destra.

![Stop Simulation (Arresta simulazione)](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come creare, eseguire e arrestare una simulazione. Si è inoltre appreso come visualizzare i dettagli della simulazione. Per altre informazioni su come eseguire le simulazioni, continuare con l'esercitazione successiva:

> [!div class="nextstepaction"]
> [Creare un dispositivo simulato personalizzato](iot-accelerators-device-simulation-create-custom-device.md)
