---
title: Connettere una RuuviTag in Azure IoT Central | Microsoft Docs
description: Informazioni su come connettere un sensore di ambiente RuuviTag all'applicazione IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: conceptual
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 10/19/2019
ms.openlocfilehash: 0a93bedb697a0d5a514fddab55c79b969b2bd77b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954227"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Connettere un sensore RuuviTag all'applicazione IoT Central di Azure

Questo articolo descrive come un generatore di soluzioni può connettere un sensore RuuviTag all'applicazione Microsoft Azure IoT Central.

Che cos'è un tag Ruuvi?

RuuviTag è una piattaforma avanzata di Beacon per sensori open source progettata per soddisfare le esigenze dei clienti aziendali, degli sviluppatori, dei creatori, degli studenti e può anche essere usata nella propria abitazione e come parte degli sforzi personali. Il dispositivo è configurato per funzionare non appena lo si estrae ed è pronto per essere distribuito nel punto in cui è necessario. Si tratta di un beacon Bluetooth LE con un sensore di ambiente e un accelerometro incorporato. 

RuuviTag comunica tramite BLE (Bluetooth Low Energy) e pertanto richiede un dispositivo gateway per comunicare con IoT Central di Azure. Assicurarsi di avere un dispositivo gateway, ad esempio RIGADO Cascade 500, per poter connettere un RuuviTag a IoT Central. 

Per configurare un dispositivo gateway RIGADO Cascade 500, seguire le istruzioni riportate [qui](./howto-connect-rigado-cascade-500-pnp.md) .

## <a name="prerequisites"></a>Prerequisiti
Per connettere i sensori RuuviTag, sono necessarie le risorse seguenti:

* Un sensore RuuviTag. Per ulteriori informazioni, visitare [RuuviTag](https://ruuvi.com/). 
* Un dispositivo RIGADO Cascade 500 o un altro gateway BLE. Per ulteriori informazioni, visitare [RIGADO](https://www.rigado.com/).
* Un'applicazione Azure IoT Central creata da uno dei modelli di applicazione di anteprima. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di una nuova applicazione](https://docs.microsoft.com/azure/iot-central/quick-deploy-iot-central-pnp?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="add-a-ruuvitag-device-template"></a>Aggiungere un modello di dispositivo RuuviTag

Per caricare un sensore RuuviTag nell'istanza dell'applicazione IoT Central di Azure, sarà necessario configurare un modello di dispositivo corrispondente all'interno dell'applicazione.

Per aggiungere un modello di dispositivo RuuviTag: 

1. Passare alla scheda ***modelli di dispositivo*** nel riquadro sinistro, selezionare **+ nuovo**: ![Crea nuovo modello di dispositivo](./media/howto-connect-ruuvi/devicetemplate-new.png) la pagina offre un'opzione per ***creare un modello personalizzato*** o ***usare un modello di dispositivo preconfigurato***
1. Selezionare il modello di dispositivo RuuviTag dall'elenco dei modelli di dispositivo preconfigurati, come illustrato di seguito: ![selezionare il modello di dispositivo RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Selezionare ***Avanti: Personalizza*** per continuare con il passaggio successivo. 
1. Nella schermata successiva selezionare ***Crea*** per caricare il modello di dispositivo C500 nell'applicazione IoT Central.

## <a name="connect-a-ruuvitag-sensor"></a>Connettere un sensore RuuviTag

Come indicato in precedenza, per connettere il RuuviTag all'applicazione IoT Central, sarà necessario configurare un dispositivo gateway. Nei passaggi seguenti si presuppone che sia stato configurato un dispositivo gateway RIGADO Cascade 500.  

1. Accendere il dispositivo RIGADO Cascade 500 e connetterlo alla connessione di rete (tramite Ethernet o wireless)
1. Estrarre il coperchio del RuuviTag ed estrarre la scheda plastica per proteggere la connessione con la batteria. 
1. Posizionare il RuuviTag in prossimità del gateway RIGADO Cascade 500 precedentemente configurato con l'applicazione IoT Central. 
1. In pochi secondi, il RuuviTag dovrebbe essere visualizzato nell'elenco dei dispositivi all'interno IoT Central.  
![elenco di dispositivi RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png) ora è possibile usare questo RuuviTag all'interno dell'applicazione IoT Central.  

## <a name="create-a-simulated-ruuvitag"></a>Creare un RuuviTag simulato
Se non si dispone di un RuuviTag fisico pratico, è possibile creare un sensore RuuviTag simulato da usare per il test nell'applicazione IoT Central di Azure.

Per creare un RuuviTag simulato:

1. Selezionare **dispositivi > RuuviTag**. 
1. Selezionare **+ nuovo**. 
1. Specificare un **ID dispositivo** univoco e un nome descrittivo del **dispositivo**.  
1. Abilitare l'impostazione **simulata** .
1. Selezionare **Create** (Crea).  

## <a name="next-steps"></a>Fasi successive

Ora che si è appreso come connettere un RuuviTag all'applicazione IoT Central di Azure, il passaggio successivo suggerito consiste nell'apprendere come [personalizzare l'applicazione IoT Central](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) per creare una soluzione end-to-end. 