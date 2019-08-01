---
title: Informazioni sul centro sicurezza di Azure per i dispositivi gemelli per i moduli di sicurezza | Microsoft Docs
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
ms.openlocfilehash: ab3b6e740e644a1ed1495eb776045888be448047
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596508"
---
# <a name="security-module"></a>Modulo di sicurezza


Questo articolo illustra come il Centro sicurezza di Azure usa i dispositivi gemelli e i moduli. 

## <a name="device-twins"></a>Dispositivi gemelli

Per le soluzioni IoT integrate in Azure, i dispositivi gemelli svolgono un ruolo fondamentale sia nella gestione dei dispositivi che nell'automazione dei processi.  

Il Centro sicurezza di Azure per gli Internet offre un'integrazione completa con la piattaforma di gestione dei dispositivi per Internet delle cose, consentendo di gestire lo stato di sicurezza del dispositivo e di usare le funzionalità di controllo dei dispositivi esistenti. Per ottenere l'integrazione, è possibile usare il meccanismo gemello dell'hub Internet.  

Altre informazioni sul concetto di [dispositivi gemelli](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) nell'hub Azure. 

## <a name="security-module-twins"></a>Moduli di sicurezza gemelli

Il Centro sicurezza di Azure per le cose gestisce un modulo di sicurezza gemello per ogni dispositivo nel servizio.
Il gemello del modulo di sicurezza include tutte le informazioni rilevanti per la sicurezza dei dispositivi per ogni dispositivo specifico nella soluzione.
Le proprietà di sicurezza del dispositivo vengono mantenute in un modulo di protezione dedicato gemello per una comunicazione più sicura e per abilitare gli aggiornamenti e la manutenzione che richiedono meno risorse.  

Per informazioni su come creare, personalizzare e configurare il dispositivo gemello, vedere [creare un modulo di sicurezza gemello](quickstart-create-security-twin.md) e configurare gli agenti di [sicurezza](how-to-agent-configuration.md) . Vedere [informazioni sui moduli gemelli](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-module-twins) per altre informazioni sul concetto di moduli gemelli nell'hub Internet. 
 

## <a name="see-also"></a>Vedere anche
- [Panoramica del Centro sicurezza di Azure](overview.md)
- [Distribuisci agenti di sicurezza](how-to-deploy-agent.md)
- [Metodi di autenticazione dell'agente di sicurezza](concept-security-agent-authentication-methods.md)