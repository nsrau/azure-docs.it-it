---
title: Evitare interruzioni del servizio nei processi di Analisi di flusso di Azure
description: Questo articolo offre indicazioni per ottenere la resilienza nell'aggiornamento dei processi di Analisi di flusso.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425998"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantire l'affidabilità dei processi di Analisi di flusso durante gli aggiornamenti del servizio

Perché un servizio sia completamente gestito, deve includere anche la possibilità di introdurre rapidamente nuove funzionalità e miglioramenti. La distribuzione di aggiornamenti del servizio in Analisi di flusso può quindi essere eseguita con frequenza settimanale o superiore. Indipendentemente dalla quantità di test effettuati, esiste comunque il rischio che un processo esistente in esecuzione subisca un'interruzione a causa dell'introduzione di un bug. Se si eseguono processi mission-critical, è necessario evitare questi rischi. È possibile ridurre questo rischio seguendo il modello di **[area abbinata](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** di Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Vantaggi delle aree abbinate di Azure per la risoluzione del problema

Analisi di flusso garantisce che i processi nelle aree abbinate vengano aggiornati in batch separati. Di conseguenza, si verifica un lasso di tempo sufficiente tra gli aggiornamenti per identificare i potenziali problemi e risolverli.

_Con l'eccezione dell'India centrale_ (nella cui area abbinata, India meridionale, non è presente Analisi di flusso), la distribuzione di un aggiornamento in Analisi di flusso non viene eseguita contemporaneamente in un set di aree abbinate. Le distribuzioni in più aree **dello stesso gruppo** possono essere eseguite **contemporaneamente**.

L'articolo su **[disponibilità e aree abbinate](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** contiene le informazioni più aggiornate su quali aree sono abbinate.

È consigliabile distribuire processi identici a entrambe le aree abbinate. È quindi necessario [monitorare questi processi](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) per ricevere una notifica quando si verifica un evento imprevisto. Se uno di questi processi termina con uno [stato di errore](https://docs.microsoft.com/azure/stream-analytics/job-states) dopo l'aggiornamento di un servizio di analisi di flusso, è possibile contattare il supporto tecnico per identificare la causa principale. È inoltre consigliabile eseguire il failover di tutti i consumer downstream nell'output del processo integro.

## <a name="next-steps"></a>Passaggi successivi

* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi di flusso](stream-analytics-real-time-fraud-detection.md)
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sull'API REST di gestione di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx)
