---
title: Connettere una cascata di Rigado 500 in Azure IoT Central Documenti Microsoft
description: Informazioni su come connettere un dispositivo gateway Rigado Cascade 500 all'applicazione IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 3e6026e683d46ca9062c5c139fcd7febd7df9bd1
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758257"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo gateway Rigado Cascade 500 all'applicazione Azure IoT CentralConnect a Rigado Cascade 500 gateway device to your Azure IoT Central application

*Questo articolo si applica ai generatori di soluzioni e agli sviluppatori di dispositivi.*

In questo articolo viene descritto come, in qualità di generatore di soluzioni, è possibile connettere un dispositivo gateway Rigado Cascade 500 all'applicazione Microsoft Azure IoT Central.This article describes how, as a solution builder, you can connect a Rigado Cascade 500 gateway device to your Microsoft Azure IoT Central application. 

## <a name="what-is-cascade-500"></a>Che cos'è Cascade 500?

Il gateway IoT Cascade 500 è un'offerta hardware di Rigado inclusa nella soluzione Cascade Edge-as-a-Service. Fornisce ai team commerciali di progetti e prodotti IoT una potenza di elaborazione perimetrale flessibile, un robusto ambiente applicativo containerizzato e un'ampia gamma di opzioni di connettività dei dispositivi wireless, tra cui Bluetooth 5, LTE & Wi-Fi.

Cascade 500 è pre-certificato per Azure IoT Plug and Play (anteprima) che consente ai nostri generatori di soluzioni di integrare facilmente il dispositivo fino alle soluzioni end-to-end. Il gateway Cascade consente di connettersi in modalità wireless a una varietà di sensori di monitoraggio delle condizioni che si trovano in prossimità del dispositivo gateway. Questi sensori possono essere integrati in IoT Central tramite il dispositivo gateway.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa guida alle procedure, sono necessarie le risorse seguenti:To step through this how-to guide, you need the following resources:

* Un dispositivo Rigado Cascade 500. Per ulteriori informazioni, visitare [Rigado](https://www.rigado.com/).
* Un'applicazione Azure IoT Central. Per ulteriori informazioni, vedere [Creare una nuova applicazione](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Aggiungere un modello di dispositivo

Per eseguire l'onboarding di un dispositivo gateway Cascade 500 nell'istanza dell'applicazione Azure IoT Central, è necessario configurare un modello di dispositivo corrispondente all'interno dell'applicazione.

Per aggiungere un modello di dispositivo Cascade 500: 

1. Passare alla scheda ***Modelli di dispositivo*** nel riquadro sinistro, selezionare - **Nuovo**: ![Crea nuovo modello di dispositivo](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. La pagina offre un'opzione per ***creare un modello personalizzato*** o utilizzare un modello di dispositivo ***preconfigurato***
1. Selezionare il modello di dispositivo C500 dall'elenco ![dei modelli di dispositivo preconfigurati come illustrato di seguito: Selezionare il modello di dispositivo C500](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Selezionare ***Avanti: Personalizza*** per continuare con il passaggio successivo. 
1. Nella schermata successiva selezionare Crea per eseguire l'onboarding del modello di dispositivo C500 nell'applicazione IoT Central.On the next screen, select ***Create*** to onboard the C500 device template into your IoT Central application.

## <a name="retrieve-application-connection-details"></a>Recuperare i dettagli di connessione dell'applicazione

A questo punto sarà necessario recuperare **l'ID ambito** e la chiave primaria per l'applicazione Azure IoT Central per connettere il dispositivo Cascade 500.You will now to retrieve the Scope ID and Primary **key** for your Azure IoT Central application in order to connect the Cascade 500 device. 

1. Passare ad **Amministrazione** nel riquadro sinistro e fare clic su **Connessione dispositivo**. 
2. Prendere nota **dell'ID ambito** per l'applicazione IoT Central.
![ID ambito app](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Ora clicca su **Visualizza chiavi** e prendi nota della **chiave**
![primaria](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Contattare Rigado per connettere il gateway 

Per connettere il dispositivo Cascade 500 all'applicazione IoT Central, è necessario contattare Rigado e fornire i dettagli di connessione dell'applicazione dai passaggi precedenti. 

Una volta che il dispositivo è connesso a Internet, Rigado sarà in grado di spingere verso il basso un aggiornamento di configurazione verso il basso per il dispositivo gateway Cascade 500 attraverso un canale sicuro. 

Questo aggiornamento applicherà i dettagli di connessione di IoT Central sul dispositivo Cascade 500 e verrà visualizzato nell'elenco dei dispositivi. 

![Chiave primaria](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

Ora sei pronto per usare il tuo dispositivo C500 nell'applicazione IoT Central!

## <a name="next-steps"></a>Passaggi successivi

Se sei uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:If you're a device developer, some suggested next steps are to:

- Informazioni sulla connettività dei dispositivi [in Azure IoT CentralRead about Device connectivity in Azure IoT Central](./concepts-get-connected.md)
- Informazioni su come [monitorare la connettività dei dispositivi usando l'interfaccia della riga di comando di AzureLearn](./howto-monitor-devices-azure-cli.md) how to Monitor device connectivity using Azure CLI
