---
title: Connettere un RuuviTag in Azure IoT Central Documenti Microsoft
description: Informazioni su come collegare un sensore di ambiente RuuviTag all'applicazione IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: e8d1c4a605e8db2e9753bb80c9712dd6c2be7b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158242"
---
# <a name="connect-a-ruuvitag-sensor-to-your-azure-iot-central-application"></a>Connettere un sensore RuuviTag all'applicazione Azure IoT Central

Questo articolo descrive come, in qualità di generatore di soluzioni, è possibile connettere un sensore RuuviTag all'applicazione Microsoft Azure IoT Central.This article describes how, as a solution builder, you can connect a RuuviTag sensor to your Microsoft Azure IoT Central application.

Cos'è un tag Ruuvi?

RuuviTag è un'avanzata piattaforma di sensori open source progettata per soddisfare le esigenze di clienti aziendali, sviluppatori, maker, studenti e hobbisti. Il dispositivo è configurato per funzionare non appena lo si estrae dalla sua scatola ed è pronto per la distribuzione dove serve. È un faro Bluetooth LE con sensore di ambiente e accelerometro integrato.

RuuviTag comunica tramite BLE (Bluetooth Low Energy) e richiede un dispositivo gateway per comunicare con Azure IoT Central. Assicurarsi di disporre di un dispositivo gateway, ad esempio Rigado Cascade 500, configurato per consentire a un RuuviTag di connettersi a IoT Central.

Seguire le [istruzioni qui](./howto-connect-rigado-cascade-500.md) se si desidera configurare un dispositivo gateway Rigado Cascade 500.

## <a name="prerequisites"></a>Prerequisiti

Per collegare i sensori RuuviTag, sono necessarie le seguenti risorse:

* Un sensore RuuviTag. Per ulteriori informazioni, visitare [RuuviTag](https://ruuvi.com/).
* Un dispositivo Rigado Cascade 500 o un altro gateway BLE. Per ulteriori informazioni, visitare [Rigado](https://www.rigado.com/).
* Un'applicazione Azure IoT Central. Per ulteriori informazioni, vedere [Creare una nuova applicazione](./quick-deploy-iot-central.md).

## <a name="add-a-ruuvitag-device-template"></a>Aggiungere un modello di dispositivo RuuviTagAdd a RuuviTag device template

Per eseguire l'onboarding di un sensore RuuviTag nell'istanza dell'applicazione Azure IoT Central, è necessario configurare un modello di dispositivo corrispondente all'interno dell'applicazione.

Per aggiungere un modello di dispositivo RuuviTag:

1. Passare alla scheda ***Modelli*** di dispositivo nel riquadro sinistro,](./media/howto-connect-ruuvi/devicetemplate-new.png) selezionare - **Nuovo**: ![Crea nuovo modello di dispositivo La pagina offre un'opzione per creare un modello di dispositivo ***personalizzato*** o Usa un modello di ***dispositivo preconfigurato***
1. Selezionare il modello di dispositivo RuuviTag dall'elenco ![dei modelli di dispositivo preconfigurati come illustrato di seguito: Selezionare il modello di dispositivo RuuviTag](./media/howto-connect-ruuvi/devicetemplate-preconfigured.png)
1. Selezionare ***Avanti: Personalizza*** per continuare con il passaggio successivo.
1. Nella schermata successiva selezionare Crea per eseguire l'onboarding del modello di dispositivo C500 nell'applicazione IoT Central.On the next screen, select ***Create*** to onboard the C500 device template into your IoT Central application.

## <a name="connect-a-ruuvitag-sensor"></a>Collegare un sensore RuuviTag

Come accennato in precedenza, per connettere RuuviTag con l'applicazione IoT Central, è necessario configurare un dispositivo gateway. I passaggi seguenti presuppongono che sia stato configurato un dispositivo gateway Rigado Cascade 500.  

1. Accendere il dispositivo Rigado Cascade 500 e collegarlo alla connessione di rete (tramite Ethernet o wireless)
1. Estrarre il coperchio dal RuuviTag e tirare la linguetta di plastica per fissare la connessione con la batteria.
1. Posizionare RuuviTag vicino a un gateway Rigado Cascade 500 già configurato nell'applicazione IoT Central.
1. In pochi secondi, il RuuviTag dovrebbe essere visualizzato nell'elenco dei dispositivi all'interno di IoT Central.  
    ![Elenco dispositivi RuuviTag](./media/howto-connect-ruuvi/ruuvi-devicelist.png)

È ora possibile utilizzare questo RuuviTag all'interno dell'applicazione IoT Central.You can now use this RuuviTag within your IoT Central application.  

## <a name="create-a-simulated-ruuvitag"></a>Creare un RuuviTag simulato

Se non si dispone di un dispositivo RuuviTag fisico, è possibile creare un sensore RuuviTag simulato da usare per il test all'interno dell'applicazione Azure IoT Central.If you don't have a physical RuuviTag device, you can create a simulated RuuviTag sensor to use for testing within your Azure IoT Central application.

Per creare un RuuviTag simulato:

1. Selezionare **Dispositivi > RuuviTag**.
1. Selezionare **+ Nuovo**.
1. Specificare un **ID dispositivo** univoco e un **nome descrittivo del dispositivo.**  
1. Attivare l'impostazione **Simulato.**
1. Selezionare **Crea**.  

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come connettere un RuuviTag all'applicazione Azure IoT Central, il passaggio successivo consigliato consiste nell'imparare a [personalizzare l'applicazione IoT Central](../retail/tutorial-in-store-analytics-customize-dashboard-pnp.md) per creare una soluzione end-to-end.
