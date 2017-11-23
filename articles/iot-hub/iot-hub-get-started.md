---
title: Hub IoT Azure - Introduzione alla connessione dei dispositivi IoT nel cloud | Documentazione Microsoft
description: "Informazioni su come connettere le schede e gli starter kit all'Hub IoT. I dispositivi possono inviare dati di telemetria all'hub IoT e l'hub Iot può monitorare e gestire i dispositivi."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: esercitazione sull'hub iot azure
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: dobett
ms.openlocfilehash: 764c6bd3c2466ceb93e33d03bece1c9036c1b1d9
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="azure-iot-hub-get-started-tutorials"></a>Esercitazioni introduttive sull'Hub IoT Azure

È possibile usare Hub IoT Azure e gli SDK per dispositivi Azure IoT per creare soluzioni Internet delle cose (IoT):

* Hub IoT Azure è un servizio completamente gestito nel cloud che si connette, monitora e gestisce i dispositivi IoT in modo sicuro. Usare gli SDK per dispositivi Azure IoT per implementare i dispositivi IoT.
* Usare un gateway IoT in scenari IoT più complessi. Ad esempio, laddove è necessario prendere in considerazione fattori quali i dispositivi legacy, i costi della larghezza di banda, i criteri di protezione e privacy o l'elaborazione dei dati perimetrali. In questi scenari si usa Azure IoT Edge per implementare un gateway che connette i dispositivi all'hub IoT.

## <a name="what-the-tutorials-cover"></a>Argomenti trattati nelle esercitazioni

Queste esercitazioni presentano l'hub IoT Azure e gli SDK per dispositivi. Le esercitazioni concernono scenari IoT comuni per illustrare le funzionalità dell'hub IoT. Le esercitazioni spiegano anche come combinare l'hub IoT con altri servizi di Azure e strumenti per creare soluzioni IoT più potenti. Nelle esercitazioni è possibile scegliere di usare dispositivi IoT simulati o reali. È anche possibile imparare a usare un gateway per consentire ai dispositivi di connettersi all'hub IoT.

## <a name="set-up-your-device"></a>Configurare il dispositivo

Connettere un dispositivo o un gateway IoT all'hub IoT di Azure. È possibile scegliere un dispositivo fisico o simulato per iniziare:

| Dispositivo IoT                       | Linguaggio di programmazione |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| DevKit di IoT                       | [Arduino in VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Dispositivo simulato nel PC           | [.NET][Sim_NET], [Java][Sim_Jav], [Node.js][Sim_Nd], [Python][Sim_Pyth] |
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
[Sim_NET]: iot-hub-csharp-csharp-getstarted.md
[Sim_Jav]: iot-hub-java-java-getstarted.md
[Sim_Nd]: iot-hub-node-node-getstarted.md
[Sim_Pyth]: iot-hub-python-getstarted.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md
[Sim_Lnx]: iot-hub-linux-iot-edge-get-started.md
[Sim_Win]: iot-hub-windows-iot-edge-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
