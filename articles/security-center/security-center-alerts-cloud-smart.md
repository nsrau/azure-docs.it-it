---
title: Incidenti del Centro sicurezza di Azure- correlazioni intelligenti degli avvisiAzure Security Center incidents - smart correlations of alerts
description: Questo argomento illustra come la fusione usa la correlazione degli avvisi intelligenti cloud per generare eventi imprevisti di sicurezza nel Centro sicurezza di Azure.This topic explains how fusion uses cloud smart alert correlation to generate security incidents in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73686484"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlazione degli avvisi intelligenti cloud nel Centro sicurezza di Azure (incidenti)Cloud smart alert correlation in Azure Security Center (incidents)

Il Centro sicurezza di Azure analizza continuamente i carichi di lavoro del cloud ibrido usando analisi avanzate e informazioni sulle minacce per avvisare l'utente di attività dannose.

L'ampiezza della copertura delle minacce sta crescendo. La necessità di rilevare anche il minimo compromesso è importante e può essere difficile per gli analisti della sicurezza valutare i diversi avvisi e identificare un attacco effettivo. Il Centro sicurezza aiuta gli analisti a far fronte a questa fatica di allerta. Aiuta a diagnosticare gli attacchi non appena si verificano, correlando diversi avvisi e segnali di bassa fedeltà in incidenti di sicurezza.

L'analisi della fusione è la tecnologia e il back-end analitico che alimenta gli incidenti del Centro sicurezza, consentendogli di correlare diversi avvisi e segnali contestuali insieme. Fusion esamina i diversi segnali riportati su un abbonamento attraverso le risorse. Fusion trova modelli che rivelano la progressione dell'attacco o segnali con informazioni contestuali condivise, indicando che è necessario utilizzare una procedura di risposta unificata per loro.

L'analisi fusione combina la conoscenza del dominio di sicurezza con l'iaa per analizzare gli avvisi, scoprendo nuovi modelli di attacco man mano che si verificano. 

Il Centro sicurezza sfrutta MITRE Attack Matrix per associare gli avvisi alle loro intenzioni percepite, aiutando a formalizzare la conoscenza del dominio di sicurezza. Inoltre, utilizzando le informazioni raccolte per ogni passaggio di un attacco, Centro sicurezza PC può escludere l'attività che sembra essere passaggi di un attacco, ma in realtà non lo è.

Poiché gli attacchi si verificano spesso tra tenant diversi, il Centro sicurezza può combinare algoritmi di controllo di sicurezza per analizzare le sequenze di attacco segnalate in ogni sottoscrizione. Questa tecnica identifica le sequenze di attacco come modelli di avviso prevalenti, anziché essere associati solo accidentalmente tra loro.

Durante un'indagine su un incidente, gli analisti spesso hanno bisogno di un contesto aggiuntivo per raggiungere un verdetto sulla natura della minaccia e su come mitigarla. Ad esempio, anche quando viene rilevata un'anomalia di rete, senza comprendere cos'altro sta accadendo sulla rete o per quanto riguarda la risorsa mirata, è difficile capire quali azioni intraprendere successivamente. Per aiutare, un incidente di sicurezza può includere elementi, eventi correlati e informazioni. Le informazioni aggiuntive disponibili per gli incidenti di sicurezza variano a seconda del tipo di minaccia rilevata e della configurazione dell'ambiente. 

![Screenshot del rapporto Operazioni non consentite di sicurezza rilevate](./media/security-center-alerts-cloud-smart/security-incident.png)

Per comprendere meglio gli incidenti di sicurezza, vedere Come gestire gli incidenti di sicurezza nel Centro sicurezza di Azure.To better understand security [incidents, see How to handle security incidents in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

