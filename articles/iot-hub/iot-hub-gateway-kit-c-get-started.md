---
title: 'Dispositivo SensorTag e gateway Azure IoT: introduzione | Documentazione Microsoft'
description: Informazioni introduttive sullo starter kit del gateway IoT, su come creare l'hub IoT di Azure e connettere il dispositivo SensorTag e il gateway all'hub IoT
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: hub iot di azure, gateway iot, introduzione a Internet delle cose, toolkit iot
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-gateway-kit-c-lesson1-set-up-nuc
ms.assetid: 56d05f4e-f2c1-4b22-8701-f01e14deead6
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 624bdc7877d5048da08897f868272fd8e8f3f7b6
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---

<a id="get-started-with-iot-gateway-starter-kit-with-a-sensortag" class="xliff"></a>

# Introduzione allo starter kit del gateway IoT di Azure con un dispositivo SensorTag

> [!div class="op_single_selector"]
> * [SensorTag](iot-hub-gateway-kit-c-get-started.md)
> * [Dispositivo simulato](iot-hub-gateway-kit-c-sim-get-started.md)

Questa esercitazione illustra le nozioni di base sull'uso dello [starter kit del gateway IoT](https://aka.ms/gateway-kit). Si userà un dispositivo Intel NUC che esegue Wind River Linux e il [SensorTag di TI](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html#main) e si apprenderà come connettere con facilità i dispositivi al cloud usando l'hub IoT di Azure.

***
**Non si dispone ancora di un kit?**  Fare clic [qui](https://aka.ms/gateway-kit). **Non si dispone di un SensorTag?** [Iniziare con un dispositivo simulato](iot-hub-gateway-kit-c-sim-get-started.md) o [acquistare un SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/?INTC=SensorTag&HQS=sensortag)
***

<a id="lesson-1-configure-your-nuc" class="xliff"></a>

## Lezione 1. Configurare il dispositivo NUC
![Diagramma di flusso della lezione 1](media/iot-hub-gateway-kit-lessons/e2e-lesson1.png)

Questa lezione illustra come configurare il dispositivo Intel NUC (Next Unit of Computing) nel kit come un gateway IoT di Azure, installare il pacchetto di Azure IoT Edge sul dispositivo NUC ed eseguire un'app di esempio per verificare la funzionalità del gateway.

*Tempo previsto per il completamento: 15 minuti*

Passare a [Configurare il dispositivo Intel NUC come un gateway IoT](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)

<a id="lesson-2-create-your-iot-hub" class="xliff"></a>

## Lezione 2. Creare l'hub IoT
![Diagramma di flusso della lezione 2](media/iot-hub-gateway-kit-lessons/e2e-lesson2.png)

Questa lezione illustra come installare gli strumenti e il software nel computer host. Verrà quindi indicato come creare l'account Azure gratuito, effettuare il provisioning dell'hub IoT di Azure e creare il primo dispositivo nell'hub IoT.

Prima di iniziare questa lezione, completare la lezione 1.

<a id="get-the-tools" class="xliff"></a>

### Get the tools
Installare strumenti e software nel computer host.

*Tempo previsto per il completamento: 20 minuti*

Passare a [Ottenere gli strumenti](iot-hub-gateway-kit-c-lesson2-get-the-tools-win32.md)

<a id="create-an-iot-hub-and-register-your-device" class="xliff"></a>

### Creare un hub IoT ed effettuare la registrazione del dispositivo
Creare il gruppo di risorse, effettuare il provisioning del primo hub IoT di Azure e aggiungere il primo dispositivo all'hub IoT usando l'interfaccia della riga di comando di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare un hub IoT ed effettuare la registrazione del dispositivo](iot-hub-gateway-kit-c-lesson2-register-device.md)

<a id="lesson-3-receive-messages-from-sensortag-and-read-messages-from-your-iot-hub" class="xliff"></a>

## Lezione 3. Ricevere messaggi da SensorTag e leggere messaggi dall'hub IoT
Questa lezione illustra come usare gli script per automatizzare la configurazione e l'esecuzione di un'applicazione di esempio BLE nel gateway. Tali applicazioni utilizzano una raccolta di moduli per aggregare e trasformare dati, elaborare comandi o eseguire un numero qualsiasi di attività correlate. I moduli comunicano tra loro tramite un broker messaggi. L'applicazione di esempio è costituita da un modulo BLE e un modulo dell'hub IoT. Il modulo BLE riceve i dati dal dispositivo SensorTag BLE. Il modulo dell'hub IoT inserisce in pacchetti i dati ricevuti e li invia all'hub IoT tramite il framework del gateway fornito in Azure IoT Edge.

![Diagramma di flusso della lezione 3](media/iot-hub-gateway-kit-lessons/e2e-lesson3.png)

<a id="configure-and-run-the-ble-sample-app" class="xliff"></a>

### Configurare eseguire l'app di esempio BLE
Configurare la connettività tra SensorTag e il gateway. Completare quindi la configurazione ed eseguire l'applicazione di esempio BLE.

*Tempo previsto per il completamento: 15 minuti*

Passare a [Configurare ed eseguire un'app di esempio BLE](iot-hub-gateway-kit-c-lesson3-configure-ble-app.md)

<a id="read-messages-from-your-iot-hub" class="xliff"></a>

### Leggere i messaggi dall'hub IoT
Eseguire il codice di esempio sul computer host per leggere i messaggi dall'hub IoT.

*Tempo previsto per il completamento: 15 minuti*

Passare a [Leggere i messaggi dall'hub IoT](iot-hub-gateway-kit-c-lesson3-read-messages-from-hub.md)

<a id="lesson-4-save-messages-to-azure-table-storage" class="xliff"></a>

## Lezione 4. Salvare i messaggi nell'archiviazione tabelle di Azure
Creare un'app per le funzioni di Azure in grado di recuperare i messaggi in arrivo dall'hub IoT e scriverli nell'archiviazione tabelle di Azure.

![Diagramma di flusso della lezione 4](media/iot-hub-gateway-kit-lessons/e2e-lesson4.png)

<a id="create-an-azure-function-app-and-azure-storage-account" class="xliff"></a>

### Creare un'app per le funzioni di Azure e un account di archiviazione di Azure
Usare un modello di Azure Resource Manager per creare un'app per le funzioni di Azure e un account di archiviazione di Azure.

*Tempo previsto per il completamento: 10 minuti*

Passare a [Creare un'app per le funzioni di Azure e un account di archiviazione di Azure](iot-hub-gateway-kit-c-lesson4-deploy-resource-manager-template.md)

<a id="read-messages-persisted-in-azure-table-storage" class="xliff"></a>

### Leggere i messaggi persistenti nell'archiviazione tabelle di Azure
Monitorare i messaggi da gateway a cloud mentre vengono scritti nell'archiviazione tabelle di Azure.

*Tempo previsto per il completamento: 5 minuti*

Passare a [Leggere i messaggi persistenti nell'archiviazione tabelle di Azure](iot-hub-gateway-kit-c-lesson4-read-table-storage.md).

<a id="troubleshooting" class="xliff"></a>

## Risoluzione dei problemi
In caso di problemi durante le lezioni, è possibile cercare soluzioni nell'articolo [Risoluzione dei problemi](iot-hub-gateway-kit-c-troubleshooting.md).

<a id="explore-more" class="xliff"></a>

## Scopri di più
Per ulteriori informazioni, visitare la pagina relativa a [Intel IoT Gateway Kit in Developer Zone](http://software.intel.com/iot/microsoft-azure).
