---
title: Ribilanciare le partizioni-iperscala (CITUS)-database di Azure per PostgreSQL
description: Distribuisci uniformemente le partizioni tra i server per ottenere prestazioni migliori
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026387"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Ribilanciare le partizioni nel gruppo di server CITUS (iperscale)

Per sfruttare i vantaggi dei nuovi nodi aggiunti è necessario ribilanciare le partizioni [delle tabelle](concepts-hyperscale-distributed-data.md#shards)distribuite, ovvero trasferire alcune partizioni dai nodi esistenti a quelle nuove. Verificare prima di tutto che i nuovi thread di lavoro abbiano completato il provisioning. Avviare quindi il ribilanciamento delle partizioni connettendosi al nodo coordinatore cluster con PSQL ed eseguendo:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

La funzione [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) ribilancia tutte le tabelle nel gruppo della condivisione [percorso](concepts-hyperscale-colocation.md) della tabella denominata nell'argomento. Pertanto non è necessario chiamare la funzione per ogni tabella distribuita, ma è sufficiente chiamarla in una tabella rappresentativa di ogni gruppo di condivisione percorso.

**Passaggi successivi**


- Ulteriori informazioni sulle [Opzioni relative alle prestazioni](concepts-hyperscale-configuration-options.md)del gruppo di server.
- [Ridimensionare un gruppo di server](howto-hyperscale-scale-grow.md)
- Vedere il materiale di riferimento [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards)
