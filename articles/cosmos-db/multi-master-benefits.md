---
title: Vantaggi di Azure Cosmos DB multimaster
description: Comprendere i vantaggi del multimaster in Azure Cosmos DB, il confronto dei requisiti di latenza e contratto di servizio in percorsi di scrittura singoli e multipli.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872010"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Informazioni sui vantaggi multimaster in Azure Cosmos DB

Cosmos DB gli operatori account devono scegliere la configurazione di distribuzione globale appropriata per garantire i requisiti di latenza, disponibilità e RTO per le applicazioni. Gli account Azure Cosmos configurati con più percorsi di scrittura offrono vantaggi significativi rispetto agli account con una singola posizione di scrittura, tra cui il contratto di servizi con disponibilità di scrittura 99,999%, < 10 ms di latenza Write al 99 ° percentile e RTO = 0 in un'emergenza locale.

## <a name="comparison-of-features"></a>Confronto delle funzionalità

|Requisiti dell'applicazione|Più percorsi di scrittura|Posizione di scrittura singola|Note|
|---|---|---|---|
|Contratto di contratto con latenza di scrittura di < 10 ms in P99|**Sì**|No|Gli account con percorso di scrittura singolo comportano una latenza di rete tra aree aggiuntive per ogni scrittura.|
|SLA di latenza di lettura di < 10 ms in P99|**Sì**|SÌ| |
|SLA di scrittura del 99,999%|**Sì**|No|Account con percorso di scrittura singolo garanzia del 99,99%|
|RTO = 0|**Sì**|No|Tempo di inattività pari a zero per le Scritture in caso di calamità locali. Gli account con percorso di scrittura singolo hanno RTO di 15 minuti.|

## <a name="next-steps"></a>Fasi successive

Se si vuole comunque disabilitare EnableMultipleWriteLocations nell'account Azure Cosmos, [aprire un ticket di supporto](https://azure.microsoft.com/support/create-ticket/).
