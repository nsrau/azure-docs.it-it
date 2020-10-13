---
title: Panoramica del modulo di sicurezza per Azure RTO
description: Scopri di più sul modulo di sicurezza per il supporto e l'implementazione di Azure RTO come parte del servizio Defender for Internet
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: 22bd12bbdcccef2fd0e9010f926cd18e95d42967
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91761856"
---
# <a name="overview-defender-for-iot-security-module-for-azure-rtos-preview"></a>Panoramica: Defender per il modulo di sicurezza per Azure RTO (anteprima)

Il modulo di sicurezza Defender for Internet per RTO offre una soluzione di sicurezza completa per i dispositivi RTO di Azure. Azure RTO è ora disponibile con il modulo Azure Internet per la sicurezza e fornisce la copertura per le minacce comuni e le potenziali attività dannose nei dispositivi del sistema operativo in tempo reale. 

![Defender per Azure RTO](./media/architecture/azure-rtos-security-monitoring.png)


Il modulo Security per Azure RTO offre le funzionalità seguenti: 
- Rilevamento di attività di rete dannose
- Basata su avvisi personalizzati, linea di base del comportamento del dispositivo
- Migliorare l'igiene della sicurezza dei dispositivi

## <a name="detection-of-malicious-network-activities"></a>Rilevamento di attività di rete dannosa

L'attività di rete in ingresso e in uscita di ogni dispositivo viene monitorata (protocolli supportati: TCP, UDP, ICMP su IPv4 e IPv6). Defender per le cose esamina tutte le attività di rete nel feed di intelligence per le minacce di Microsoft. Il feed viene aggiornato in tempo reale con milioni di indicatori di minaccia univoci raccolti in tutto il mondo. 

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>Baseline del comportamento del dispositivo in base agli avvisi personalizzati

La linea di base consente il clustering di dispositivi in gruppi di sicurezza e la definizione del comportamento previsto di ogni gruppo. Poiché in genere i dispositivi sono progettati per funzionare in scenari ben definiti e limitati, è facile creare una baseline che definisce il comportamento previsto usando un set di parametri. Qualsiasi deviazione dalla linea di base, attiva un avviso. 

## <a name="improve-your-device-security-hygiene"></a>Migliorare l'igiene della sicurezza del dispositivo

Sfruttando l'infrastruttura consigliata di Defender per l'it, è possibile ottenere informazioni e informazioni approfondite sui problemi dell'ambiente che incidono e danneggiano il comportamento di sicurezza dei dispositivi. Il comportamento della sicurezza dei dispositivi è debole può consentire la riuscita di potenziali attacchi se non vengono modificati, perché la sicurezza viene sempre misurata in base al collegamento più debole all'interno di qualsiasi organizzazione. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Inizia a proteggere i dispositivi RTO di Azure

Il modulo Security per Azure RTO è disponibile come download gratuito per i dispositivi. Il servizio cloud Defender for Internet è disponibile con una versione di valutazione di 30 giorni per ogni sottoscrizione di Azure. Per iniziare, scaricare il [modulo di sicurezza per Azure RTO](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md) . 


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato il modulo di sicurezza per il servizio Azure RTO. Per altre informazioni sul modulo di sicurezza e per iniziare, vedere gli articoli seguenti:

- [Concetti relativi ai moduli di sicurezza di Azure RTO](concept-rtos-security-module.md)
- [Guida introduttiva: modulo di sicurezza Azure RTO](quickstart-azure-rtos-security-module.md)
