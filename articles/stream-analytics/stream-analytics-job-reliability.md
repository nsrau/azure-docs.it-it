---
title: Evitare interruzioni del servizio nei processi di Analisi di flusso di Azure
description: Questo articolo offre indicazioni per ottenere la resilienza nell'aggiornamento dei processi di Analisi di flusso.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7375fb2763ad83e049b1ef30a623f164e059a792
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479457"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantire l'affidabilità dei processi di Analisi di flusso durante gli aggiornamenti del servizio

Perché un servizio sia completamente gestito, deve includere anche la possibilità di introdurre rapidamente nuove funzionalità e miglioramenti. La distribuzione di aggiornamenti del servizio in Analisi di flusso può quindi essere eseguita con frequenza settimanale o superiore. Indipendentemente dalla quantità di test effettuati, esiste comunque il rischio che un processo esistente in esecuzione subisca un'interruzione a causa dell'introduzione di un bug. I clienti che eseguono processi di elaborazione in streaming critici devono evitare tali rischi. Come meccanismo per ridurli, possono usare il modello delle **[aree abbinate](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** di Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Vantaggi delle aree abbinate di Azure per la risoluzione del problema

Analisi di flusso garantisce che i processi nelle aree abbinate vengano aggiornati in batch separati. Di conseguenza, tra gli aggiornamenti intercorre un intervallo di tempo sufficiente a identificare e risolvere i bug che potrebbero causare interruzioni.

_Con l'eccezione dell'India centrale_ (nella cui area abbinata, India meridionale, non è presente Analisi di flusso), la distribuzione di un aggiornamento in Analisi di flusso non viene eseguita contemporaneamente in un set di aree abbinate. Le distribuzioni in più aree **dello stesso gruppo** possono essere eseguite **contemporaneamente**.

L'articolo su **[disponibilità e aree abbinate](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** contiene le informazioni più aggiornate su quali aree sono abbinate.

Per i clienti è consigliabile distribuire processi identici in entrambe le aree abbinate. In aggiunta alle funzionalità di monitoraggio interno di Analisi di flusso, è anche consigliabile monitorare i processi come se fossero **entrambi** processi di produzione. Se la causa identificata di un'interruzione è l'aggiornamento del servizio Analisi di flusso, eseguire l'escalation appropriata e il failover degli utenti downstream all'output del processo integro. L'escalation al supporto tecnico impedirà che l'area abbinata sia interessata dalla nuova distribuzione, in modo da mantenere l'integrità dei processi abbinati.

## <a name="next-steps"></a>Passaggi successivi

* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi di flusso](stream-analytics-real-time-fraud-detection.md)
* [Scalabilità dei processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sull'API REST di gestione di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx)
