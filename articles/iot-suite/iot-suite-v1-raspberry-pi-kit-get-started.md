---
title: Connettere un dispositivo Raspberry Pi ad Azure IoT Suite | Microsoft Docs
description: "Le esercitazioni che usano Node.js o C consentono di imparare a usare lo Starter Kit di Microsoft Azure IoT per Raspberry Pi 3 e la soluzione di monitoraggio remoto IoT Suite. È possibile scegliere un'esercitazione che simula i dati di telemetria, che usa sensori reali o che consente di eseguire aggiornamenti del firmware remoto."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: d9e737fcaaabd2088d2920b69fca96d6058f4b4a
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="connect-your-microsoft-azure-iot-raspberry-pi-3-starter-kit-to-the-remote-monitoring-solution"></a>Connettere lo Starter Kit di Microsoft Azure IoT per Raspberry Pi 3 alla soluzione di monitoraggio remoto

Le esercitazioni di questa sezione consentono di imparare a connettere un dispositivo Raspberry Pi 3 alla soluzione di monitoraggio remoto. Scegliere l'esercitazione appropriata al linguaggio di programmazione preferito e se si dispone o meno di hardware del sensore disponibile da usare con Raspberry Pi.

## <a name="the-tutorials"></a>Esercitazioni

| Esercitazione | Note | Lingue |
| -------- | ----- | --------- |
| Telemetria simulata (Base)| Consente di simulare i dati del sensore. Usa un computer autonomo Raspberry Pi. | [C][lnk-c-simulator], [Node.js][lnk-node-simulator] |
| Sensore reale (Intermedio) | Usa i dati di un sensore BME280 connesso a Raspberry Pi. | [C][lnk-c-basic], [Node.js][lnk-node-basic] |
| Implementazione dell'aggiornamento del firmware (Avanzato)| Usa i dati di un sensore BME280 connesso a Raspberry Pi. Consente di eseguire aggiornamenti del firmware remoto su Raspberry Pi. | [C][lnk-c-advanced], [Node.js][lnk-node-advanced] |

## <a name="next-steps"></a>Passaggi successivi

Visitare il [Centro per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot/) per altri esempi e documentazione su Azure IoT.

[lnk-node-simulator]: iot-suite-v1-raspberry-pi-kit-node-get-started-simulator.md
[lnk-node-basic]: iot-suite-v1-raspberry-pi-kit-node-get-started-basic.md
[lnk-node-advanced]: iot-suite-v1-raspberry-pi-kit-node-get-started-advanced.md
[lnk-c-simulator]: iot-suite-v1-raspberry-pi-kit-c-get-started-simulator.md
[lnk-c-basic]: iot-suite-v1-raspberry-pi-kit-c-get-started-basic.md
[lnk-c-advanced]: iot-suite-v1-raspberry-pi-kit-c-get-started-advanced.md