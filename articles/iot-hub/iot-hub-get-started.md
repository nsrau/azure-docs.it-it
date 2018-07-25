---
title: Hub IoT Azure - Introduzione alla connessione dei dispositivi IoT nel cloud | Microsoft Docs
description: Informazioni su come connettere le schede e gli starter kit all'Hub IoT. I dispositivi possono inviare dati di telemetria all'hub IoT e l'hub Iot può monitorare e gestire i dispositivi.
author: dominicbetts
manager: timlt
keywords: esercitazione sull'hub iot azure
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 77abe7e2187a3cb28b326ffa833a856625d6c33d
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125193"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Hub IoT di Azure - Introduzione ai dispositivi reali

Questi articoli illustrano le procedure relative all'hub IoT di Azure e all'esecuzione degli SDK su dispositivi reali.

## <a name="set-up-your-device"></a>Configurare il dispositivo

Connettere un dispositivo o un gateway IoT all'hub IoT di Azure:

| Dispositivo IoT                       | Linguaggio di programmazione |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| DevKit di IoT                       | [Arduino in VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Simulatore di dispositivi online         | [Raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
