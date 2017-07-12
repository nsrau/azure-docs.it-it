---
title: 'Dispositivo simulato e gateway Azure IoT: introduzione | Documentazione Microsoft'
description: Iniziare a usare lo starter kit per il gateway IoT, creare l'hub IoT di Azure e connettere il gateway all'hub IoT
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: hub IoT di Azure, gateway IoT, introduzione a Internet delle cose, toolkit IoT
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 0c110b8b-bee4-4aec-a18a-dfc292aa17a3
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 916fa40d9ac857dfa72197b40c232834593d3891
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---

<a id="get-started-with-iot-gateway-starter-kit-with-a-simulated-device" class="xliff"></a>

# Introduzione allo starter kit per il gateway IoT con un dispositivo simulato

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Dispositivo simulato](iot-hub-gateway-kit-c-sim-get-started.md)

In questa esercitazione vengono descritte le nozioni base per usare lo [starter kit per il gateway IoT](https://aka.ms/gateway-kit). Si userà Intel NUC che esegue Wind River Linux. Verrà illustrato come connettere i dispositivi al cloud usando l'hub IoT di Azure.

***
**Se non si ha ancora un kit**, fare clic [qui](https://aka.ms/gateway-kit).
***

<a id="lesson-1-configure-your-nuc" class="xliff"></a>

## Lezione 1: Configurare NUC
![Diagramma di flusso della lezione 1](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson1.png)

Questa lezione illustra come configurare il dispositivo Intel NUC (Next Unit of Computing) nel kit come un gateway IoT di Azure, installare il pacchetto di Azure IoT Edge sul dispositivo NUC ed eseguire un'app di esempio per verificare la funzionalità del gateway.

*Tempo previsto per il completamento: 15 minuti*

Passare a [Configurare il dispositivo Intel NUC come un gateway IoT](iot-hub-gateway-kit-c-sim-lesson1-set-up-nuc.md)

<a id="lesson-2-create-your-iot-hub" class="xliff"></a>

## Lezione 2. Creare l'hub IoT
![Diagramma di flusso della lezione 2](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson2.png)

Questa lezione illustra come installare gli strumenti e il software nel computer host. Verrà quindi indicato come creare l'account Azure gratuito, effettuare il provisioning dell'hub IoT di Azure e creare il primo dispositivo nell'hub IoT.

Prima di iniziare questa lezione, completare la lezione 1.

<a id="get-the-tools" class="xliff"></a>

### Get the tools
Installare strumenti e software nel computer host.

*Tempo previsto per il completamento: 20 minuti*

Passare a [Ottenere gli strumenti](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)

<a id="create-an-iot-hub-and-register-your-device" class="xliff"></a>

### Creare un hub IoT ed effettuare la registrazione del dispositivo
Creare il gruppo di risorse, effettuare il provisioning del primo hub IoT di Azure e aggiungere il primo dispositivo all'hub IoT usando l'interfaccia della riga di comando di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare un hub IoT e registrare il dispositivo](iot-hub-gateway-kit-c-sim-lesson2-register-device.md).

<a id="lesson-3-receive-messages-from-the-simulated-device-and-read-messages-from-your-iot-hub" class="xliff"></a>

## Lezione 3: Ricevere messaggi dal dispositivo simulato e leggerli dall'hub IoT
In questa lezione si useranno script per automatizzare la configurazione e l'esecuzione di un'app per dispositivo simulato nel gateway. L'app per dispositivo simulato genera dati di esempio sulla temperatura e li invia a un modulo dell'hub IoT. Il modulo dell'hub IoT inserisce in pacchetti i dati ricevuti e li invia all'hub IoT tramite il framework del gateway fornito in Azure IoT Edge.

![Diagramma di flusso della lezione 3](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson3.png)

<a id="configure-and-run-a-simulated-device" class="xliff"></a>

### Configurare ed eseguire un dispositivo simulato
Preparare i codici di esempio, quindi configurare ed eseguire l'applicazione di esempio per il dispositivo simulato.

*Tempo previsto per il completamento: 15 minuti*

Passare a [Configurare ed eseguire un dispositivo simulato](iot-hub-gateway-kit-c-sim-lesson3-configure-simulated-device-app.md).

<a id="read-messages-from-your-iot-hub" class="xliff"></a>

### Leggere messaggi dall'hub IoT
Eseguire un codice di esempio nel computer host per leggere i messaggi dall'hub IoT.

*Tempo previsto per il completamento: 15 minuti*

Passare a [Leggere i messaggi dall'hub IoT](iot-hub-gateway-kit-c-sim-lesson3-read-messages-from-hub.md)

<a id="lesson-4-save-messages-to-azure-table-storage" class="xliff"></a>

## Lezione 4. Salvare i messaggi nell'archiviazione tabelle di Azure
Creare un'app per le funzioni di Azure in grado di recuperare i messaggi in arrivo dall'hub IoT e scriverli nell'archiviazione tabelle di Azure.

![Diagramma di flusso della lezione 4](media/iot-hub-gateway-kit-lessons/e2e-sim-Lesson4.png)

<a id="create-an-azure-function-app-and-azure-storage-account" class="xliff"></a>

### Creare un'app per le funzioni di Azure e un account di archiviazione di Azure
Usare un modello di Azure Resource Manager per creare un'app per le funzioni di Azure e un account di archiviazione di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare un'app per le funzioni di Azure e un account di archiviazione di Azure](iot-hub-gateway-kit-c-sim-lesson4-deploy-resource-manager-template.md)

<a id="read-messages-persisted-in-azure-table-storage" class="xliff"></a>

### Leggere i messaggi persistenti nell'archiviazione tabelle di Azure
Monitorare i messaggi da gateway a cloud mentre vengono scritti nell'archiviazione tabelle di Azure.

*Tempo previsto per il completamento: 5 minuti*

Passare a [Leggere i messaggi persistenti nell'archiviazione tabelle di Azure](iot-hub-gateway-kit-c-sim-lesson4-read-table-storage.md).

<a id="troubleshooting" class="xliff"></a>

## Risoluzione dei problemi
In caso di problemi durante le lezioni, è possibile cercare soluzioni nell'articolo [Risoluzione dei problemi](iot-hub-gateway-kit-c-sim-troubleshooting.md).

<a id="explore-more" class="xliff"></a>

## Scopri di più
Per ulteriori informazioni, visitare la pagina relativa a [Intel IoT Gateway Kit in Developer Zone](https://software.intel.com/en-us/iot/hardware/gateways/dev-kit).
