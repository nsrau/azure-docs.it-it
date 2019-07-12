---
title: Vantaggi multimaster di Azure Cosmos DB
description: Comprendere i vantaggi del multi-master in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: mjbrown
ms.openlocfilehash: c78e5e4f8d396d777738bddfd6baf086c0b2ecf4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789293"
---
# <a name="understand-multi-master-benefits-in-azure-cosmos-db"></a>Comprendere i vantaggi più master in Azure Cosmos DB

Gli operatori dell'account COSMOS DB devono scegliere Configurazione distribuzione globale appropriata per garantire la latenza, disponibilità e requisiti di RTO per le proprie applicazioni. Gli account Cosmos Azure configurati con più località di scrittura offrono vantaggi significativi rispetto gli account con singola operazione di scrittura inclusi località, al 99,999% scrivere disponibilità contratto di servizio, < 10 ms di latenza contratto di servizio al 99 ° percentile e RTO di scrittura = 0 in caso di emergenza a livello di area.

## <a name="comparison-of-features"></a>Confronto delle funzionalità

|Requisiti dell'applicazione|Più località di scrittura|Località singola operazione di scrittura|Note|
|---|---|---|---|
|Contratto di servizio che la latenza di scrittura < 10 ms a P99|**Sì**|No|Gli account con l'unica posizione di scrittura soggetta a latenza di rete tra più aree aggiuntive per ogni scrittura.|
|Leggere la latenza dal contratto di servizio < 10 ms a P99|**Sì**|Sì| |
|Contratto di servizio del 99,999% di scrittura|**Sì**|No|Gli account con l'unica posizione di scrittura garantiscono contratto di servizio del 99,99%|
|RTO = 0|**Sì**|No|Zero tempi per le scritture in caso di emergenze locali di inattività. Gli account con singola operazione di scrittura posizione hanno l'obiettivo del tempo di 15 minuti.|

## <a name="next-steps"></a>Fasi successive

Se si desidera comunque disabilitare EnableMultipleWriteLocations nell'account Azure Cosmos, please [aprire un ticket di supporto](https://azure.microsoft.com/support/create-ticket/).
