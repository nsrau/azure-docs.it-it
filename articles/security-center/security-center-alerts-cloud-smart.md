---
title: Correlazione tra Smart Alert cloud nel centro sicurezza di Azure (eventi imprevisti) | Microsoft Docs
description: In questo argomento viene illustrato come Fusion utilizza la correlazione Smart Alert cloud per generare eventi imprevisti di sicurezza nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295851"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Correlazione tra Smart Alert cloud nel centro sicurezza di Azure (eventi imprevisti)

Il Centro sicurezza analizza continuamente i carichi di lavoro del cloud ibrido usando analisi avanzate e Intelligence per le minacce per segnalare attività dannose.

Con l'ampliamento della copertura delle minacce e la necessità di rilevare anche la minima indicazione degli aumenti compromessi, può essere difficile per gli analisti della sicurezza valutare i diversi avvisi e identificare un attacco effettivo. Il Centro sicurezza consente agli analisti di affrontare la fatica degli avvisi e di diagnosticare gli attacchi non appena si verificano, correlando gli avvisi diversi e i segnali di bassa fedeltà agli eventi imprevisti della sicurezza.

Fusion è la tecnologia e il back-end analitico che alimenta gli eventi imprevisti del Centro sicurezza, consentendo al reparto IT di correlare diversi avvisi e segnali contestuali insieme. Fusion funziona osservando i diversi segnali segnalati in una sottoscrizione tra le risorse e individuando i modelli più diffusi che mostrano la progressione degli attacchi o i segnali con informazioni contestuali condivise che indicano una procedura di risposta unificata sono state acquisite.

Fusion Analytics combina le informazioni sul dominio di sicurezza con intelligenza artificiale per analizzare gli avvisi, scoprendo nuovi modelli di attacco man mano che si verificano. 

Il Centro sicurezza usa la matrice di attacco MITRE per associare gli avvisi alla finalità percepita, contribuendo a formalizzare la conoscenza del dominio di sicurezza. Inoltre, usando le informazioni raccolte per ogni passaggio di un attacco, il Centro sicurezza può escludere l'attività che sembra essere una procedura di attacco, ma non lo è.  

Poiché gli attacchi si verificano spesso in tenant diversi, il Centro sicurezza può combinare gli algoritmi di intelligenza artificiale per analizzare le sequenze di attacco segnalate in ogni sottoscrizione per identificarle come modelli di avviso prevalenti anziché essere associati a ogni evento altri.

Durante un'indagine di un evento imprevisto, gli analisti spesso necessitano di un contesto aggiuntivo per raggiungere un verdetto sulla natura della minaccia e su come mitigarlo. Ad esempio, anche quando viene rilevata un'anomalia di rete, senza conoscere il resto della rete o per quanto riguarda la risorsa di destinazione, è difficile comprendere quali azioni eseguire successivamente. Per semplificare, un evento imprevisto di sicurezza può includere elementi, eventi correlati e informazioni. Le informazioni aggiuntive disponibili per gli eventi imprevisti di sicurezza variano a seconda del tipo di minaccia rilevato e della configurazione dell'ambiente. 

![Dettagli evento imprevisto della sicurezza](./media/security-center-alerts-cloud-smart/security-incident.png)

Per comprendere meglio gli eventi imprevisti della sicurezza, vedere [come gestire gli eventi imprevisti della sicurezza nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-incident).

