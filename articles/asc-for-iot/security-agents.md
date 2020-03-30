---
title: Inizia a usare il Centro sicurezza di Azure per gli agenti di sicurezza IoT Documenti Microsoft
description: Introduzione alla comprensione, alla configurazione, alla distribuzione e all'uso degli agenti del servizio di sicurezza Di Azure per gli agenti del servizio di sicurezza IoT nei dispositivi IoT.Get started with understanding, configuring, deploying and using Azure Security Center for IoT security service agents on your IoT devices.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 1ed890d9d3602de24e6f85f6f0ae7f59849f3df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74664188"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Introduzione agli agenti di sicurezza dei dispositivi di Azure Security Center per IoT

Il Centro sicurezza di Azure per gli agenti di sicurezza IoT offre funzionalità di sicurezza avanzate, ad esempio il monitoraggio di connessioni remote, applicazioni attive, eventi di accesso e procedure consigliate per la configurazione del sistema operativo. Prendi il controllo della protezione dalle minacce sul campo del tuo dispositivo e della sicurezza con un unico servizio. 

Vengono fornite l'architettura di riferimento per gli agenti di sicurezza Linux e Windows, sia in C e C.

Il Centro sicurezza di Azure per gli agenti di sicurezza IoT gestisce la raccolta di eventi non elaborati dal sistema operativo del dispositivo, l'aggregazione degli eventi per ridurre i costi e la configurazione tramite un modulo gemello del dispositivo. I messaggi di sicurezza vengono inviati tramite l'hub IoT, nel Centro sicurezza di Azure per i servizi di analisi IoT.Security messages are sent through your IoT Hub, into Azure Security Center for IoT analytics services.

Usare il flusso di lavoro seguente per distribuire e testare il Centro sicurezza di Azure per gli agenti di sicurezza IoT:Use the following workflow to deploy and test your Azure Security Center for IoT security agents: 

1. [Abilitare il Centro sicurezza di Azure per il servizio IoT nell'hub IoTEnable Azure Security Center for IoT service to your IoT Hub](quickstart-onboard-iot-hub.md)
1. Se l'hub IoT non dispone di dispositivi registrati, [registrare un nuovo dispositivo](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. Creare un modulo di [sicurezza azureiotsecurity](quickstart-create-security-twin.md) per i dispositivi.
1. Per installare l'agente in un dispositivo simulato di Azure anziché in un dispositivo effettivo, [creare una nuova macchina virtuale di Azure in](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) un'area disponibile. 
1. [Distribuire un security agent del Centro sicurezza di Azure per IoT](how-to-deploy-linux-cs.md) nel dispositivo IoT o in una nuova macchina virtuale.
1. Seguire le istruzioni per [trigger_events](https://aka.ms/iot-security-github-trigger-events) eseguire una simulazione innocua di un attacco.
1. Verificare il Centro sicurezza di Azure per gli avvisi IoT in risposta all'attacco simulato nel passaggio precedente. Iniziare la verifica cinque minuti dopo l'esecuzione dello script.
1. Esplora [gli avvisi,](concept-security-alerts.md) [i consigli](concept-recommendations.md)e le [immersioni approfondite usando Log Analytics](how-to-security-data-access.md) usando l'hub IoT. 


## <a name="next-steps"></a>Passaggi successivi

- Configurare la [soluzione](quickstart-configure-your-solution.md)
- [Creare moduli di sicurezza](quickstart-create-security-twin.md)
- Configurare [avvisi personalizzati](quickstart-create-custom-alerts.md)
- [Distribuire un agente di sicurezza](how-to-deploy-agent.md)
