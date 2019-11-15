---
title: Creare un'app di monitoraggio pannelli solari con IoT Central | Microsoft Docs
description: Informazioni su come creare un'applicazione per pannelli solari usando i modelli di applicazione di Azure IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: c560c7e298dabbb5a3c31d90e0d48d0d63cfac5f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581528"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Esercitazione: Creare ed esaminare il modello di app per il monitoraggio dei pannelli solari 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Questa esercitazione assiste l'utente durante il processo di creazione dell'applicazione di monitoraggio dei pannelli solari, che include un modello di dispositivo di esempio con dati simulati. In questa esercitazione si apprenderà come:


> [!div class="checklist"]
> * Creare gratuitamente l'app per pannelli solari
> * Procedura dettagliata per l'applicazione
> * Pulire le risorse


Se non si dispone di una sottoscrizione, [creare un account di valutazione gratuito](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Prerequisiti
- Nessuna
- La sottoscrizione di Azure è consigliata, ma non è necessaria


## <a name="create-a-solar-panel-monitoring-app"></a>Creare un'app per il monitoraggio pannelli solari 

È possibile creare questa applicazione in tre semplici passaggi:

1. Aprire la [home page di Azure IoT Central](https://apps.azureiotcentral.com) e fare clic su **Compila** per creare una nuova applicazione. 

2. Selezionare la scheda **Energia** e fare clic su **Crea app** nel riquadro dell'applicazione **Monitoraggio pannelli solari**. 

    > [!div class="mx-imgBorder"]
    > ![Compila app](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. Dopo la selezione di **Crea app** verrà aperto il modulo **Nuova applicazione**. Specificare i dettagli richiesti, come illustrato nella figura seguente:
    * **Nome applicazione**: Scegliere un nome per l'applicazione IoT Central. 
    * **URL**: Selezionare un URL di IoT Central. La piattaforma verificherà se è univoco o meno.
    * **Versione di valutazione gratuita valida 7 giorni**: Se si dispone già di una sottoscrizione di Azure, è consigliabile usare l'impostazione predefinita. Se non si dispone di una sottoscrizione di Azure, iniziare con una versione di valutazione gratuita.
    * **Info di fatturazione**: L'applicazione stessa è gratuita. Per effettuare il provisioning delle risorse per l'app sono necessari i dettagli relativi alla directory, alla sottoscrizione di Azure e all'area.
    * Fare clic sul pulsante **Crea** nella parte inferiore della pagina. L'app verrà creata in un minuto circa.
        > [!div class="mx-imgBorder"]
        > ![Modulo Nuova applicazione](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Verificare l'applicazione e i dati simulati

L'app per pannelli solari appena creata è l'app dell'utente ed è possibile modificarla in qualsiasi momento. Assicurarsi che l'app sia distribuita e che funzioni come previsto prima di modificarla.

Per verificare la creazione dell'app e la simulazione dei dati, passare al **Dashboard**. Se i riquadri presentano alcuni dati, la distribuzione dell'app ha avuto esito positivo. La simulazione dei dati potrebbe richiedere alcuni minuti per la generazione dei dati. Attendere almeno 1 o 2 minuti. 

## <a name="application-walk-through"></a>Procedura dettagliata per l'applicazione
Dopo aver distribuito il modello di app, saranno disponibili un dispositivo contatore intelligente di esempio, un modello di dispositivo e un dashboard.

Adatum è una società fornitrice di energia fittizia che monitora e gestisce i pannelli solari. Nel dashboard di monitoraggio del pannello solare sono presenti le proprietà del pannello solare, i dati e i comandi di esempio. Consente agli operatori e ai team di supporto tecnico di eseguire proattivamente le attività seguenti prima che si trasformino in richieste di assistenza:
* Esaminare le informazioni sul pannello più recenti e la posizione di installazione sulla mappa
* Controllare in modo proattivo lo stato del pannello e lo stato della connessione
* Esaminare le tendenze relative alla generazione di energia e alla temperatura per rilevare modelli anomali
* Tenere traccia della generazione di energia totale ai fini della pianificazione e della fatturazione
* Eseguire comandi e operazioni di controllo, ad esempio attivare il pannello e aggiornare la versione del firmware. Nel modello i pulsanti di comando mostrano le funzionalità possibili senza inviare comandi reali.

> [!div class="mx-imgBorder"]
> ![Dashboard di monitoraggio pannelli solari](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Dispositivi
L'app include un dispositivo pannello solare di esempio. È possibile visualizzare i dettagli del dispositivo facendo clic sulla scheda **Dispositivi**.

> [!div class="mx-imgBorder"]
> ![Dispositivi pannello solare](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Fare clic sul collegamento del dispositivo di esempio **SP0123456789** per visualizzare i dettagli del dispositivo. Nella pagina **Aggiorna proprietà** è possibile aggiornare le proprietà scrivibili del dispositivo e visualizzare i valori aggiornati nel dashboard. 

> [!div class="mx-imgBorder"]
> ![Proprietà del pannello solare](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Modello di dispositivo
Fare clic sulla scheda **Modelli di dispositivo** per visualizzare il modello di dispositivo pannello solare. Il modello offre un'interfaccia predefinita per dati, proprietà, comandi e visualizzazioni.

> [!div class="mx-imgBorder"]
> ![Modello di dispositivo pannello solare](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Se si decide di non continuare a usare questa applicazione, seguire questa procedura per eliminare l'applicazione:

1. Aprire la scheda Amministrazione nel riquadro sinistro.
2. Selezionare Impostazioni applicazione e fare clic sul pulsante Elimina nella parte inferiore della pagina. 

    > [!div class="mx-imgBorder"]
    > ![Eliminare l'applicazione](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sull'architettura dell'app per pannelli solari, vedere l'[articolo relativo ai concetti](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Creare modelli di applicazione per pannelli solari gratuitamente: [app per pannelli solari](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Per altre informazioni su IoT Central, vedere [Panoramica di IoT Central](https://docs.microsoft.com/azure/iot-central/)

