---
title: includere file
description: includere file
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 7246375468b3419c3d52ee3d5a51a95aa20050a9
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876660"
---
L'applicazione di esempio include due dispositivi simulati e un gateway IoT Edge. Le esercitazioni seguenti mostrano due approcci per sperimentare e comprendere le funzionalità del gateway:

* Creare il gateway IoT Edge in una macchina virtuale di Azure e connettere una telecamera simulata.
* Creare il gateway IoT Edge in un dispositivo reale come un dispositivo Intel NUC e connettere una telecamera reale.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Usare il modello di applicazione di analisi video di Azure IoT Central per creare un'applicazione per punti vendita al dettaglio
> * Personalizzare le impostazioni dell'applicazione
> * Creare un modello di dispositivo per un dispositivo gateway IoT Edge
> * Aggiungere un dispositivo gateway all'applicazione IoT Central

## <a name="prerequisites"></a>Prerequisiti

Per completare questa serie di esercitazioni, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile crearne una nella [pagina di iscrizione ad Azure](https://aka.ms/createazuresubscription).
* Se si usa una telecamera reale, sono necessari la connettività tra il dispositivo IoT Edge e la telecamera e il canale **RTSP (Real Time Streaming Protocol)** .

## <a name="initial-setup"></a>Configurazione iniziale

In queste esercitazioni si aggiornano e si usano diversi file di configurazione. Le versioni iniziali di questi file sono disponibili nel repository GitHub [LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway). Il repository include anche un file di testo denominato scratchpad che è possibile scaricare e usare per registrare i valori di configurazione dei servizi distribuiti.

Creare una cartella denominata *lva-configuration* nel computer locale per salvare copie di questi file. Fare quindi clic con il pulsante destro del mouse su ognuno dei collegamenti seguenti e scegliere **Salva con nome** per salvare il file nella cartella *lva-configuration*:
