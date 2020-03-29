---
title: Vantaggi multimaster di Azure Cosmos DBAzure Cosmos DB multi-master benefits
description: Comprendere i vantaggi di più master in Azure Cosmos DB, il confronto dei requisiti di latenza e sLA in posizioni di scrittura singole e multiple.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: b21b6ba82ba1ada0103501b8beeca270df86abd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872010"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Comprendere i vantaggi multi-master in Azure Cosmos DBUnderstand multi-master benefits in Azure Cosmos DB

Gli operatori di account Cosmos DB devono scegliere una configurazione di distribuzione globale appropriata per garantire la latenza, la disponibilità e i requisiti RTO per le loro applicazioni. Gli account di Azure Cosmos configurati con più posizioni di scrittura offrono vantaggi significativi rispetto agli account con una singola posizione di scrittura, tra cui il 99,999% del livello di verifica della scrittura, <sLA di latenza di scrittura di 10 ms al 99o percentile e RTO : 0 in caso di emergenza regionale.

## <a name="comparison-of-features"></a>Confronto delle funzionalità

|Requisiti dell'applicazione|Più posizioni di scritturaMultiple Write Locations|Posizione di scrittura singola|Note|
|---|---|---|---|
|Latenza di scrittura SLA di <10ms a P99|**Sì**|No|Gli account con posizione di scrittura singola comportano una latenza di rete tra aree aggiuntiva per ogni scrittura.|
|Latenza di lettura SLA di <10 ms su P99Read latency SLA of <10ms at P99|**Sì**|Sì| |
|Scrivi SLA del 99,999%|**Sì**|No|Garanzia di account con posizione di scrittura singola del 99,99%|
|RTO - 0|**Sì**|No|Nessun tempo di inattività per le scritture in caso di disastri regionali. Gli account con posizione di scrittura singola hanno RTO di 15 min.|

## <a name="next-steps"></a>Passaggi successivi

Se si desidera comunque disabilitare EnableMultipleWriteLocations nell'account Azure Cosmos, aprire un ticket di [supporto.](https://azure.microsoft.com/support/create-ticket/)
