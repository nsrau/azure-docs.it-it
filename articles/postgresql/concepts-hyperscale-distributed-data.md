---
title: Dati distribuiti - Hyperscale (Citus) - Database di Azure per PostgreSQLDistributed data – Hyperscale (Citus) - Azure Database for PostgreSQL
description: Informazioni su tabelle distribuite, tabelle di riferimento, tabelle locali e partizioni nel database di Azure per PostgreSQL.Learn about distributed tables, reference tables, local tables, and shards in Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243653"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Dati distribuiti nel database di Azure per PostgreSQL – Hyperscale (Citus)

Questo articolo descrive i tre tipi di tabella in Database di Azure per PostgreSQL – Hyperscale (Citus).
Viene illustrato come le tabelle distribuite vengono archiviate come partizioni e il modo in cui le partizioni vengono posizionate nei nodi.

## <a name="table-types"></a>Tipi di tabella

Esistono tre tipi di tabelle in un gruppo di server Hyperscale (Citus), ognuna utilizzata per scopi diversi.

### <a name="type-1-distributed-tables"></a>Tipo 1: Tabelle distribuiteType 1: Distributed tables

Il primo tipo, e più comune, è tabelle distribuite. Sembrano essere tabelle normali per le istruzioni SQL, ma sono partizionate orizzontalmente tra i nodi di lavoro. Ciò significa che le righe della tabella vengono archiviate in nodi diversi, in tabelle di frammenti denominate partizioni.

Hyperscale (Citus) esegue non solo istruzioni SQL ma DDL in un cluster.
Modifica dello schema di una tabella distribuita a catena per aggiornare tutte le partizioni della tabella tra i worker.

#### <a name="distribution-column"></a>Colonna di distribuzione

L'iperscala (Citus) utilizza il partizionamento orizzontale algoritmico per assegnare le righe alle partizioni. L'assegnazione viene effettuata in modo deterministico in base al valore di una colonna di tabella denominata colonna di distribuzione. L'amministratore del cluster deve designare questa colonna durante la distribuzione di una tabella.
Fare la scelta giusta è importante per le prestazioni e la funzionalità.

### <a name="type-2-reference-tables"></a>Tipo 2: Tabelle di riferimento

Una tabella di riferimento è un tipo di tabella distribuita il cui intero contenuto è concentrato in un'unica partizione. La partizione viene replicata in ogni worker. Le query su qualsiasi worker possono accedere alle informazioni di riferimento in locale, senza l'overhead di rete dovuto alla richiesta di righe da un altro nodo. Le tabelle di riferimento non hanno una colonna di distribuzione perché non è necessario distinguere partizioni separate per riga.

Le tabelle di riferimento sono in genere di piccole dimensioni e vengono utilizzate per archiviare dati rilevanti per le query in esecuzione in qualsiasi nodo di lavoro. Un esempio è enumerato valori come gli stati degli ordini o le categorie di prodotti.

### <a name="type-3-local-tables"></a>Tipo 3: Tabelle locali

Quando si utilizza Hyperscale (Citus), il nodo coordinatore a cui ci si connette è un normale database PostgreSQL. È possibile creare tabelle ordinarie nel coordinatore e scegliere di non partizionarle.

Un buon candidato per le tabelle locali sarebbe piccole tabelle amministrative che non partecipano alle query di join. Un esempio è una tabella di utenti per l'accesso e l'autenticazione dell'applicazione.

## <a name="shards"></a>Frammenti

Nella sezione precedente è stato descritto il modo in cui le tabelle distribuite vengono archiviate come partizioni nei nodi di lavoro. In questa sezione vengono illustrati ulteriori dettagli tecnici.

La `pg_dist_shard` tabella dei metadati nel coordinatore contiene una riga per ogni partizione di ogni tabella distribuita nel sistema. La riga corrisponde a un ID partizione con un intervallo di numeri interi in uno spazio hash (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Se il nodo coordinatore desidera determinare quale `github_events`partizione contiene una riga di , esegue l'ansime del valore della colonna di distribuzione nella riga. Il nodo controlla quindi\'quale intervallo di partizioni contiene il valore hash. Gli intervalli vengono definiti in modo che l'immagine della funzione hash sia la loro unione disgiunta.

### <a name="shard-placements"></a>Posizionamenti frammenti

Si supponga che la partizione 102027 sia associata alla riga in questione. La riga viene letta o `github_events_102027` scritta in una tabella chiamata in uno dei worker. Quale lavoratore? Questo è determinato interamente dalle tabelle di metadati. Il mapping della partizione al worker è noto come posizionamento della partizione.

Il nodo coordinatore riscrive le query in `github_events_102027` frammenti che fanno riferimento alle tabelle specifiche come ed esegue tali frammenti sui worker appropriati. Di seguito è riportato un esempio di query eseguita dietro le quinte per trovare il nodo che contiene l'ID partizione 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [scegliere una colonna](concepts-hyperscale-choose-distribution-column.md) di distribuzione per le tabelle distribuite.
