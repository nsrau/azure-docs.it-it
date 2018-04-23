---
title: Evitare interruzioni del servizio nei processi di Analisi di flusso di Azure
description: Questo articolo offre indicazioni per ottenere la resilienza nell'aggiornamento dei processi di Analisi di flusso.
services: stream-analytics
author: jseb225
manager: kfile
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 47ccfe99d2ee6576dbb70324eb383f52d2a1b2e7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantire l'affidabilità dei processi di Analisi di flusso durante gli aggiornamenti del servizio

Perché un servizio sia completamente gestito, deve includere anche la possibilità di introdurre rapidamente nuove funzionalità e miglioramenti. La distribuzione di aggiornamenti del servizio in Analisi di flusso può quindi essere eseguita con frequenza settimanale o superiore. Indipendentemente dalla quantità di test effettuati, esiste comunque il rischio che un processo esistente in esecuzione subisca un'interruzione a causa dell'introduzione di un bug. I clienti che eseguono processi di elaborazione in streaming critici devono evitare tali rischi. Come meccanismo per ridurli, possono usare il modello delle **[aree abbinate](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** di Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Vantaggi delle aree abbinate di Azure per la risoluzione del problema

Analisi di flusso garantisce che i processi nelle aree abbinate vengano aggiornati in batch separati. Di conseguenza, tra gli aggiornamenti intercorre un intervallo di tempo sufficiente a identificare e risolvere i bug che potrebbero causare interruzioni.

_Con l'eccezione dell'India centrale_ (nella cui area abbinata, India meridionale, non è presente Analisi di flusso), la distribuzione di un aggiornamento in Analisi di flusso non viene eseguita contemporaneamente in un set di aree abbinate. Le distribuzioni in più aree **dello stesso gruppo** possono essere eseguite **contemporaneamente**.

L'articolo su **[disponibilità e aree abbinate](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** contiene le informazioni più aggiornate su quali aree sono abbinate.

Per i clienti è consigliabile distribuire processi identici in entrambe le aree abbinate. In aggiunta alle funzionalità di monitoraggio interno di Analisi di flusso, è anche consigliabile monitorare i processi come se fossero **entrambi** processi di produzione. Se la causa identificata di un'interruzione è l'aggiornamento del servizio Analisi di flusso, eseguire l'escalation appropriata e il failover degli utenti downstream all'output del processo integro. L'escalation al supporto tecnico impedirà che l'area abbinata sia interessata dalla nuova distribuzione, in modo da mantenere l'integrità dei processi abbinati.
