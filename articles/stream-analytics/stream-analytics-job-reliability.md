---
title: Evitare interruzioni del servizio nei processi di Analisi di flusso di Azure
description: Questo articolo offre indicazioni per ottenere la resilienza nell'aggiornamento dei processi di Analisi di flusso.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 889e298f64689748340713de6318f8ffcd181001
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006456"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantire l'affidabilità dei processi di Analisi di flusso durante gli aggiornamenti del servizio

Perché un servizio sia completamente gestito, deve includere anche la possibilità di introdurre rapidamente nuove funzionalità e miglioramenti. La distribuzione di aggiornamenti del servizio in Analisi di flusso può quindi essere eseguita con frequenza settimanale o superiore. Indipendentemente dalla quantità di test effettuati, esiste comunque il rischio che un processo esistente in esecuzione subisca un'interruzione a causa dell'introduzione di un bug. Se si eseguono processi mission-critical, è necessario evitare questi rischi. È possibile ridurre questo rischio seguendo il modello di **[area abbinata](../best-practices-availability-paired-regions.md)** di Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Vantaggi delle aree abbinate di Azure per la risoluzione del problema

Analisi di flusso garantisce che i processi nelle aree abbinate vengano aggiornati in batch separati. Di conseguenza, si verifica un lasso di tempo sufficiente tra gli aggiornamenti per identificare i potenziali problemi e risolverli.

_Con l'eccezione dell'India centrale_ (nella cui area abbinata, India meridionale, non è presente Analisi di flusso), la distribuzione di un aggiornamento in Analisi di flusso non viene eseguita contemporaneamente in un set di aree abbinate. Le distribuzioni in più aree **dello stesso gruppo** possono essere eseguite **contemporaneamente**.

L'articolo su **[disponibilità e aree abbinate](../best-practices-availability-paired-regions.md)** contiene le informazioni più aggiornate su quali aree sono abbinate.

È consigliabile distribuire processi identici a entrambe le aree abbinate. È quindi necessario [monitorare questi processi](./stream-analytics-set-up-alerts.md#scenarios-to-monitor) per ricevere una notifica quando si verifica un evento imprevisto. Se uno di questi processi termina con uno [stato di errore](./job-states.md) dopo l'aggiornamento di un servizio di analisi di flusso, è possibile contattare il supporto tecnico per identificare la causa principale. È inoltre consigliabile eseguire il failover di tutti i consumer downstream nell'output del processo integro.

## <a name="next-steps"></a>Passaggi successivi

* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi di flusso](stream-analytics-real-time-fraud-detection.md)
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sull'API REST di gestione di Analisi di flusso](/rest/api/streamanalytics/)