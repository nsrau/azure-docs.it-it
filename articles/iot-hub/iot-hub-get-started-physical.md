---
title: Introduzione alla connessione dei dispositivi fisici all&quot;hub IoT Azure | Microsoft Docs
description: "Informazioni su come creare i dispositivi IoT fisici e connetterli all&quot;hub IoT di Azure. I dispositivi possono inviare dati di telemetria all&quot;hub IoT e l&quot;hub Iot può monitorare e gestire i dispositivi."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
keywords: esercitazione sull&quot;hub iot azure
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 2e00affad9302145ac0c62e5994bd636cb2b72a9
ms.contentlocale: it-it
ms.lasthandoff: 06/03/2017


---
# <a name="azure-iot-hub-get-started-with-physical-devices-tutorials"></a>Hub IoT di Azure - Esercitazioni introduttive per i dispositivi fisici

Queste esercitazioni presentano l'hub IoT Azure e gli SDK per dispositivi. Le esercitazioni concernono scenari IoT comuni per illustrare le funzionalità dell'hub IoT. Le esercitazioni spiegano anche come combinare l'hub IoT con altri servizi di Azure e strumenti per creare soluzioni IoT più potenti. Le esercitazioni elencate nella tabella seguente illustrano come creare dispositivi IoT fisici.

| Dispositivo IoT                       | Linguaggio di programmazione |
|---------------------------------|----------------------|
| Raspberry Pi                    | [Node.js][Pi_Nd], [C][Pi_C]           |
| Intel Edison                    | [Node.js][Ed_Nd], [C][Ed_C]           |
| Adafruit Feather HUZZAH ESP8266 | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev      | [Arduino][Th_Ard]              |
| Adafruit Feather M0             | [Arduino][M0_Ard]              |

È anche possibile usare un gateway IoT Edge per consentire ai dispositivi di connettersi all'hub IoT.

| Dispositivo gateway               | Linguaggio di programmazione | Piattaforma         |
|------------------------------|----------------------|------------------|
| Intel NUC (modello DE3815TYKE) | C                    | [Wind River Linux][NUC_Lnx] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]


[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[NUC_Lnx]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

