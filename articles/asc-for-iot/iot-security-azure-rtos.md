---
title: Supporto di Azure RTO
description: Informazioni sul supporto di Azure RTO nel centro sicurezza di Azure per il servizio Internet delle cose.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097757"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>Centro sicurezza di Azure per la soluzione di sicurezza Internet per Azure RTO 

Il modulo di sicurezza del Centro sicurezza di Azure per l'it offre una soluzione di sicurezza completa per i dispositivi Azure RTO. Azure RTO viene fornito con un modulo di protezione incorporato che illustra le minacce comuni sui dispositivi del sistema operativo in tempo reale. 

![Centro sicurezza di Azure per Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


Il Centro sicurezza di Azure per il modulo di sicurezza Internet con il supporto di Azure RTO offre le funzionalità seguenti: 
- Rilevamento di attività di rete dannose
- Basata su avvisi personalizzati, linea di base del comportamento del dispositivo
- Migliorare l'igiene della sicurezza dei dispositivi

### <a name="detection-of-malicious-network-activities"></a>Rilevamento di attività di rete dannosa

L'attività di rete in ingresso e in uscita di ogni dispositivo viene monitorata (protocolli supportati: TCP, UDP, ICMP su IPv4 e IPv6). Il Centro sicurezza di Azure per tutto controlla le singole attività di rete nel feed di intelligence per le minacce di Microsoft. Il feed viene aggiornato in tempo reale con milioni di indicatori di minaccia univoci raccolti in tutto il mondo. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Baseline del comportamento del dispositivo in base agli avvisi personalizzati

La linea di base consente il clustering di dispositivi in gruppi di sicurezza e la definizione del comportamento previsto di ogni gruppo. Poiché in genere i dispositivi sono progettati per funzionare in scenari ben definiti e limitati, è facile creare una baseline che definisce il comportamento previsto usando un set di parametri. Qualsiasi deviazione dalla linea di base, attiva un avviso. 

### <a name="improve-your-device-security-hygiene"></a>Migliorare l'igiene della sicurezza del dispositivo

Sfruttando l'infrastruttura consigliata, il Centro sicurezza di Azure per l'it fornisce informazioni e informazioni approfondite sui problemi nell'ambiente che incidono e danneggiano il comportamento di sicurezza dei dispositivi. Il comportamento della sicurezza dei dispositivi scarsi può consentire potenziali attacchi se rimangono invariati, perché la sicurezza viene sempre misurata in base al collegamento più debole all'interno di qualsiasi organizzazione. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Inizia a proteggere i dispositivi RTO di Azure

- Il Centro sicurezza di Azure per il modulo di sicurezza Internet per Azure RTO è disponibile come download gratuito per i dispositivi. Il Centro sicurezza di Azure per il servizio cloud è disponibile con una versione di valutazione di 30 giorni per ogni sottoscrizione di Azure. Per iniziare, scaricare il modulo di sicurezza del [Centro sicurezza di Azure per Azure RTO](https://github.com/azure-rtos/iot-security-module-preview) . 


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come il Centro sicurezza di Azure per il supporto di Azure RTO. Per informazioni su come iniziare e abilitare la soluzione di sicurezza nell'hub Internet, vedere gli articoli seguenti:

- [Prerequisiti del servizio](service-prerequisites.md)
- [Introduzione](getting-started.md)
- [Configurare la soluzione](quickstart-configure-your-solution.md)
- [Abilitare la sicurezza nell'hub Internet](quickstart-onboard-iot-hub.md)
- [Domande frequenti sul centro sicurezza di Azure](resources-frequently-asked-questions.md)
- [Avvisi di sicurezza del Centro sicurezza di Azure per IoT](concept-security-alerts.md)
