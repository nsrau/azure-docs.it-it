---
title: Introduzione allo starter kit per il gateway IoT di Azure | Documentazione Microsoft
description: Iniziare a usare lo starter kit per il gateway IoT, creare l&quot;hub IoT di Azure e connettere il gateway all&quot;hub IoT
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: hub IoT di Azure, gateway IoT, introduzione a Internet delle cose, toolkit IoT
ms.assetid: 0c110b8b-bee4-4aec-a18a-dfc292aa17a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/21/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 65a25dd7a2f6a8d518217512f9e10fc9008ee728
ms.openlocfilehash: c62e0cc560af18945c371af000ec08c0ddc7cd5a


---

# <a name="get-started-with-iot-gateway-starter-kit-with-a-simulated-device"></a>Introduzione allo starter kit per il gateway IoT con un dispositivo simulato

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Dispositivo simulato](iot-hub-gateway-kit-c-sim-get-started.md)

In questa esercitazione vengono descritte le nozioni base per usare lo [starter kit per il gateway IoT](https://aka.ms/gateway-kit). Si userà Intel NUC che esegue Wind River Linux. Verrà illustrato come connettere i dispositivi al cloud usando l'hub IoT di Azure.

***
**Se non si ha ancora un kit**, fare clic [qui](https://aka.ms/gateway-kit).
***

## <a name="lesson-1-configure-your-nuc"></a>Lezione 1: Configurare NUC
![Diagramma di flusso della lezione 1](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson1.png)

In questa lezione si configura Intel NUC (Next Unit of Computing) nel kit come gateway IoT di Azure, si installa il pacchetto Azure IoT Gateway SDK in NUC e si esegue un'app di esempio per verificare la funzionalità del gateway.

*Tempo previsto per il completamento: 15 minuti*

Passare a [Configurare Intel NUC come gateway IoT](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md).

## <a name="lesson-2-create-your-iot-hub"></a>Lezione 2: Creare l'hub IoT
![Diagramma di flusso della lezione 2](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson2.png)

In questa lezione si installano gli strumenti e il software nel computer host, quindi si crea un account Azure gratuito, si effettua il provisioning dell'hub IoT di Azure e si crea il primo dispositivo nell'hub IoT.

Prima di iniziare questa lezione, completare la lezione 1.

### <a name="get-the-tools"></a>Get the tools
Installare gli strumenti e il software nel computer host.

*Tempo previsto per il completamento: 20 minuti*

Passare a [Ottenere gli strumenti](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md).

### <a name="create-an-iot-hub-and-register-your-device"></a>Creare un hub IoT e registrare il dispositivo
Creare il gruppo di risorse, effettuare il provisioning del primo hub IoT di Azure e aggiungere il primo dispositivo all'hub IoT usando l'interfaccia della riga di comando di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare un hub IoT e registrare il dispositivo](iot-hub-gateway-kit-c-sim-lesson2-register-device.md).

## <a name="lesson-3-receive-messages-from-the-simulated-device-and-read-messages-from-your-iot-hub"></a>Lezione 3: Ricevere messaggi dal dispositivo simulato e leggerli dall'hub IoT
In questa lezione si useranno script per automatizzare la configurazione e l'esecuzione di un'app per dispositivo simulato nel gateway. L'app per dispositivo simulato genera dati di esempio sulla temperatura e li invia a un modulo dell'hub IoT. Il modulo dell'hub IoT comprime i dati ricevuti e li invia all'hub IoT tramite il framework del gateway disponibile in Azure IoT Gateway SDK.

![Diagramma di flusso della lezione 3](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson3.png)

### <a name="configure-and-run-a-simulated-device"></a>Configurare ed eseguire un dispositivo simulato
Preparare i codici di esempio, quindi configurare ed eseguire l'applicazione di esempio per il dispositivo simulato.

*Tempo previsto per il completamento: 15 minuti*

Passare a [Configurare ed eseguire un dispositivo simulato](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md).

### <a name="read-messages-from-your-iot-hub"></a>Leggere messaggi dall'hub IoT
Eseguire un codice di esempio nel computer host per leggere i messaggi dall'hub IoT.

*Tempo previsto per il completamento: 15 minuti*

Passare a [Leggere messaggi dall'hub IoT](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md).

## <a name="lesson-4-save-messages-to-azure-table-storage"></a>Lezione 4: Salvare i messaggi nell'archivio tabelle di Azure
Creare un'app per le funzioni di Azure che preleva i messaggi in ingresso dall'hub IoT e li scrive nell'archiviazione tabelle di Azure.

![Diagramma di flusso della lezione 4](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson4.png)

### <a name="create-an-azure-function-app-and-azure-storage-account"></a>Creare un'app per le funzioni di Azure e un account di archiviazione di Azure
Usare un modello di Azure Resource Manager per creare un'app per le funzioni di Azure e un account di archiviazione di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare un'app per le funzioni di Azure e un account di archiviazione di Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md).

### <a name="read-messages-persisted-in-azure-table-storage"></a>Leggere i messaggi con salvataggio permanente nell'archivio tabelle di Azure
Monitorare i messaggi da gateway a cloud mentre vengono scritti nell'archivio tabelle di Azure.

*Tempo previsto per il completamento: 5 minuti*

Passare a [Leggere i messaggi con salvataggio permanente nell'archivio tabelle di Azure](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi
Per eventuali problemi durante le lezioni, vedere l'articolo [Risoluzione dei problemi](iot-hub-gateway-kit-c-sim-troubleshooting.md) per cercare le soluzioni.

## <a name="explore-more"></a>Altre informazioni
Per altre informazioni, vedere la [pagina per gli sviluppatori del kit Intel IoT Gateway](https://software.intel.com/en-us/iot/hardware/gateways/dev-kit).


<!--HONumber=Dec16_HO3-->


