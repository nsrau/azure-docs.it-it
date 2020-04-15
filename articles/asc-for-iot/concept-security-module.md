---
title: Modulo di sicurezza e dispositivi gemelli
description: Informazioni sul concetto di moduli gemelli di sicurezza e su come vengono usati nel Centro sicurezza di Azure per l'IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: a5c25cba-59a4-488b-abbe-c37ff9b151f9
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: d598d291612c6e4f58caf77e1b213b2bc3f42820
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311452"
---
# <a name="security-module"></a>Modulo di sicurezza

Questo articolo illustra come il Centro sicurezza di Azure per IoT usa i dispositivi gemelli e i moduli.

## <a name="device-twins"></a>Dispositivi gemelli

Per le soluzioni IoT integrate in Azure, i dispositivi gemelli svolgono un ruolo fondamentale sia nella gestione dei dispositivi che nell'automazione dei processi.

Il Centro sicurezza di Azure per IoT offre l'integrazione completa con la piattaforma di gestione dispositivi IoT esistente, consentendo di gestire lo stato di sicurezza dei dispositivi, nonché di usufruire delle funzionalità di controllo dispositivi esistenti. L'integrazione si ottiene utilizzando il meccanismo IoT Hub twin.

Altre informazioni sul concetto di dispositivi gemelli nell'hub IoT di Azure.Learn more about the concept of [device twins](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) in Azure IoT Hub.

## <a name="security-module-twins"></a>Modulo di sicurezza gemelli

Il Centro sicurezza di Azure per IoT gestisce un modulo di sicurezza gemello per ogni dispositivo nel servizio.
Il modulo di sicurezza twin contiene tutte le informazioni rilevanti per la sicurezza del dispositivo per ogni dispositivo specifico nella soluzione.
Le proprietà di sicurezza dei dispositivi vengono mantenute in un modulo gemello dedicato per comunicazioni più sicure e per abilitare gli aggiornamenti e la manutenzione che richiedono meno risorse.

Vedere [Creare il modulo di sicurezza gemello](quickstart-create-security-twin.md) e [Configurare gli agenti](how-to-agent-configuration.md) di sicurezza per informazioni su come creare, personalizzare e configurare il gemello. Vedere Informazioni sui gemelli del modulo per ulteriori informazioni sul concetto di gemelli modulo nell'hub IoT.See [Understanding module twins](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) to learn more about the concept of module twins in IoT Hub.

## <a name="see-also"></a>Vedere anche

- [Panoramica del Centro sicurezza di Azure per IoTAzure Security Center for IoT overview](overview.md)
- [Distribuire gli agenti di sicurezza](how-to-deploy-agent.md)
- [Metodi di autenticazione del Security Agent](concept-security-agent-authentication-methods.md)
