---
title: Hub IoT Azure - Introduzione alla connessione dei dispositivi IoT nel cloud | Documentazione Microsoft
description: Informazioni su come connettere le schede e gli starter kit all'Hub IoT. I dispositivi possono inviare dati di telemetria all'hub IoT e l'hub Iot può monitorare e gestire i dispositivi.
author: dominicbetts
manager: timlt
keywords: esercitazione sull'hub iot azure
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 76f427204f0ad31196ce8b995b9e4ed9676209ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634703"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Hub IoT di Azure - Introduzione ai dispositivi reali

È possibile usare Hub IoT Azure e gli SDK per dispositivi Azure IoT per creare soluzioni Internet delle cose (IoT):

* Hub IoT Azure è un servizio completamente gestito nel cloud che si connette, monitora e gestisce i dispositivi IoT in modo sicuro. Usare gli SDK per dispositivi Azure IoT per implementare i dispositivi IoT.
* Usare un gateway IoT in scenari IoT più complessi. Ad esempio, laddove è necessario prendere in considerazione fattori quali i dispositivi legacy, i costi della larghezza di banda, i criteri di protezione e privacy o l'elaborazione dei dati perimetrali. In questi scenari usare [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) per implementare un gateway che connetta i dispositivi all'hub IoT.

## <a name="what-the-how-to-articles-cover"></a>Di cosa trattano gli articoli delle procedure

Questi articoli presentano l'hub IoT Azure e gli SDK per dispositivi. Gli articoli presentano scenari IoT comuni per illustrare le funzionalità dell'hub IoT. Gli articoli spiegano anche come combinare l'hub IoT con altri servizi di Azure e strumenti per creare soluzioni IoT più potenti. Negli articoli si usano dispositivi IoT reali.

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
