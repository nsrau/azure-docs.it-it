---
title: Modulo di sicurezza e dispositivi gemelli
description: Informazioni sul concetto di gemelli dei moduli di sicurezza e sul loro uso nel centro sicurezza di Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311452"
---
# <a name="security-module"></a>Modulo di sicurezza

Questo articolo illustra come il Centro sicurezza di Azure usa i dispositivi gemelli e i moduli.

## <a name="device-twins"></a>Dispositivi gemelli

Per le soluzioni IoT integrate in Azure, i dispositivi gemelli svolgono un ruolo fondamentale sia nella gestione dei dispositivi che nell'automazione dei processi.

Il Centro sicurezza di Azure per IoT offre l'integrazione completa con la piattaforma di gestione dispositivi IoT esistente, consentendo di gestire lo stato di sicurezza dei dispositivi, nonché di usufruire delle funzionalità di controllo dispositivi esistenti. Per ottenere l'integrazione, è possibile usare il meccanismo gemello dell'hub Internet.

Altre informazioni sul concetto di [dispositivi gemelli](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) nell'hub Azure.

## <a name="security-module-twins"></a>Moduli di sicurezza gemelli

Il Centro sicurezza di Azure per le cose gestisce un modulo di sicurezza gemello per ogni dispositivo nel servizio.
Il gemello del modulo di sicurezza include tutte le informazioni rilevanti per la sicurezza dei dispositivi per ogni dispositivo specifico nella soluzione.
Le proprietà di sicurezza del dispositivo vengono mantenute in un modulo di protezione dedicato gemello per una comunicazione più sicura e per abilitare gli aggiornamenti e la manutenzione che richiedono meno risorse.

Per informazioni su come creare, personalizzare e configurare il dispositivo gemello, vedere [creare un modulo di sicurezza gemello](quickstart-create-security-twin.md) e [configurare gli agenti di sicurezza](how-to-agent-configuration.md) . Vedere [informazioni sui moduli gemelli](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) per altre informazioni sul concetto di moduli gemelli nell'hub Internet.

## <a name="see-also"></a>Vedere anche

- [Panoramica del Centro sicurezza di Azure](overview.md)
- [Distribuire gli agenti di sicurezza](how-to-deploy-agent.md)
- [Metodi di autenticazione dell'agente di sicurezza](concept-security-agent-authentication-methods.md)
