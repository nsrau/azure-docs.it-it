---
title: Introduzione all'uso del Centro sicurezza di Azure per gli agenti di sicurezza Internet Microsoft Docs
description: Inizia a usare il Centro sicurezza di Azure per gli agenti di sicurezza.
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
ms.openlocfilehash: 6e790c125c59aea85f1ac34240c5a1d1969544ae
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600592"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Introduzione al centro sicurezza di Azure per gli agenti di sicurezza dei dispositivi

Il Centro sicurezza di Azure per gli agenti di sicurezza di Azure offre funzionalità di sicurezza avanzate, ad esempio il monitoraggio di connessioni remote, applicazioni attive, eventi di accesso e procedure consigliate per la configurazione del sistema operativo. Assumere il controllo della protezione dalle minacce per il campo del dispositivo e del comportamento di sicurezza con un unico servizio. 

Viene fornita l'architettura di riferimento per gli agenti di sicurezza C# Linux e Windows, sia in che in C.

Il Centro sicurezza di Azure per gli agenti di protezione Internet gestisce la raccolta di eventi non elaborati dal sistema operativo del dispositivo, l'aggregazione di eventi per ridurre i costi e la configurazione tramite un dispositivo gemello. I messaggi di sicurezza vengono inviati tramite l'hub Internet, nel centro sicurezza di Azure per i servizi di analisi dei dati.

Usare il flusso di lavoro seguente per distribuire e testare il Centro sicurezza di Azure per gli agenti di sicurezza Internet: 

1. [Abilitare il Centro sicurezza di Azure per il servizio Internet delle cose nell'hub Internet delle cose](quickstart-onboard-iot-hub.md)
1. Se l'hub Internet delle cose non ha dispositivi registrati, [registrare un nuovo dispositivo](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Creare un modulo di sicurezza azureiotsecurity](quickstart-create-security-twin.md) per i dispositivi.
1. Per installare l'agente in un dispositivo simulato di Azure anziché installare in un dispositivo effettivo, [creare una nuova macchina virtuale (VM) di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) in una zona disponibile. 
1. [Distribuire un centro sicurezza di Azure per l'agente di sicurezza](how-to-deploy-linux-cs.md) di Internet delle cose nel dispositivo o nella nuova VM.
1. Seguire le istruzioni per [trigger_events](https://aka.ms/iot-security-github-trigger-events) per eseguire una simulazione innocua di un attacco.
1. Verificare il Centro sicurezza di Azure per gli avvisi relativi alle cose in risposta all'attacco simulato nel passaggio precedente. Inizia la verifica cinque minuti dopo l'esecuzione dello script.
1. Esplorare [avvisi](concept-security-alerts.md), [consigli](concept-recommendations.md)e [approfondimenti usando log Analytics](how-to-security-data-access.md) usando l'hub Internet. 


## <a name="next-steps"></a>Passaggi successivi

- Configurare la [soluzione](quickstart-configure-your-solution.md)
- [Creare moduli di sicurezza](quickstart-create-security-twin.md)
- Configurare gli [avvisi personalizzati](quickstart-create-custom-alerts.md)
- [Distribuire un agente di sicurezza](how-to-deploy-agent.md)
