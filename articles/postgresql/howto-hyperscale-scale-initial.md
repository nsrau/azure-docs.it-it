---
title: Dimensioni iniziali del gruppo di server-iperscala (CITUS)-database di Azure per PostgreSQL
description: Selezionare le dimensioni iniziali corrette per il caso d'uso
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026404"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Selezionare le dimensioni iniziali per il gruppo di server CITUS (iperscale)

Le dimensioni di un gruppo di server, sia il numero di nodi che la capacità hardware, sono [facili da modificare](howto-hyperscale-scale-grow.md). Tuttavia, è comunque necessario scegliere una dimensione iniziale per un nuovo gruppo di server. Ecco alcuni suggerimenti per una scelta ragionevole.

## <a name="multi-tenant-saas-use-case"></a>Casi d'uso SaaS multi-tenant

Quando si esegue la migrazione a iperscale (CITUS) da un'istanza di database PostgreSQL a nodo singolo esistente, scegliere un cluster in cui il numero di Vcore e RAM del ruolo di lavoro in totale sia uguale a quello dell'istanza originale. In questi scenari sono stati rilevati miglioramenti delle prestazioni da 2 a 3 volte perché il partizionamento orizzontale migliora l'utilizzo delle risorse, consentendo indici più piccoli e così via.

Il numero di vCore è in realtà l'unica decisione. L'allocazione di RAM è attualmente determinata in base al numero di vCore, come descritto nella pagina [Opzioni di configurazione di iperscala (CITUS)](concepts-hyperscale-configuration-options.md) .
Il nodo coordinatore non richiede la stessa quantità di RAM dei thread di lavoro, ma non è possibile scegliere RAM e Vcore in modo indipendente.

## <a name="real-time-analytics-use-case"></a>Casi d'uso di analisi in tempo reale

Totale VCore: quando i dati di lavoro si adattano alla RAM, è possibile prevedere un miglioramento delle prestazioni lineare su iperscala (CITUS) proporzionale al numero di core del ruolo di lavoro. Per determinare il numero corretto di Vcore per le proprie esigenze, prendere in considerazione la latenza corrente per le query nel database a nodo singolo e la latenza necessaria in iperscala (CITUS). Dividere la latenza corrente per la latenza desiderata e arrotondare il risultato.

RAM del ruolo di lavoro: il caso migliore è rendere disponibile memoria sufficiente per fare in modo che la maggior parte del working set venga salvato nella memoria. Il tipo di query che l'applicazione utilizza influisce sui requisiti di memoria. È possibile eseguire EXPLAIN ANALYZe su una query per determinare la quantità di memoria richiesta. Tenere presente che Vcore e RAM vengono ridimensionati insieme come descritto nell'articolo [Opzioni di configurazione dell'iperscalabilità (CITUS)](concepts-hyperscale-configuration-options.md) .

## <a name="next-steps"></a>Passaggi successivi

- [Dimensionare un gruppo di server](howto-hyperscale-scale-grow.md)
- Ulteriori informazioni sulle [Opzioni relative alle prestazioni](concepts-hyperscale-configuration-options.md)del gruppo di server.
