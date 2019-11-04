---
title: 'Dati distribuiti nel database di Azure per PostgreSQL: iperscalabilità (CITUS)'
description: Informazioni sulle tabelle distribuite, le tabelle di riferimento, le tabelle locali e le partizioni nel database di Azure per PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 53d656d8d39c71c813d7dd7a504ec45667bf18b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482437"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Dati distribuiti nel database di Azure per PostgreSQL: iperscalabilità (CITUS)

Questo articolo descrive i tre tipi di tabella in database di Azure per PostgreSQL – iperscalabilità (CITUS).
Mostra come vengono archiviate le tabelle distribuite come partizioni e come vengono inserite le partizioni nei nodi.

## <a name="table-types"></a>Tipi di tabella

Esistono tre tipi di tabelle in un gruppo di server con iperscalabilità (CITUS), ognuno usato per scopi diversi.

### <a name="type-1-distributed-tables"></a>Tipo 1: tabelle distribuite

Il primo tipo e più comune sono le tabelle distribuite. Sembrano essere tabelle normali per le istruzioni SQL, ma sono partizionate orizzontalmente tra i nodi del ruolo di lavoro. Ciò significa che le righe della tabella sono archiviate in nodi diversi, in tabelle di frammenti denominate partizioni.

Iperscale (CITUS) esegue non solo istruzioni SQL ma DDL in un cluster.
Modifica dello schema di una tabella distribuita a cascata per aggiornare tutte le partizioni della tabella tra i ruoli di lavoro.

#### <a name="distribution-column"></a>Colonna di distribuzione

Iperscale (CITUS) usa il partizionamento orizzontale algoritmico per assegnare righe alle partizioni. L'assegnazione viene eseguita in modo deterministico in base al valore di una colonna di tabella denominata colonna di distribuzione. Per la distribuzione di una tabella, è necessario che l'amministratore del cluster designi questa colonna.
La scelta corretta è importante per le prestazioni e le funzionalità.

### <a name="type-2-reference-tables"></a>Tipo 2: tabelle di riferimento

Una tabella di riferimento è un tipo di tabella distribuita il cui intero contenuto è concentrato in un'unica partizione. La partizione viene replicata in ogni ruolo di lavoro. Le query su qualsiasi ruolo di lavoro possono accedere alle informazioni di riferimento localmente, senza l'overhead di rete della richiesta di righe da un altro nodo. Le tabelle di riferimento non includono colonne di distribuzione perché non è necessario distinguere partizioni separate per riga.

Le tabelle di riferimento sono in genere di piccole dimensioni e vengono usate per archiviare i dati rilevanti per le query in esecuzione su qualsiasi nodo di lavoro. Un esempio è l'enumerazione di valori come gli Stati degli ordini o le categorie di prodotti.

### <a name="type-3-local-tables"></a>Tipo 3: tabelle locali

Quando si usa iperscale (CITUS), il nodo coordinatore a cui ci si connette è un normale database PostgreSQL. È possibile creare tabelle ordinarie nel coordinatore e scegliere di non partizionarle.

Un buon candidato per le tabelle locali è costituito da piccole tabelle amministrative che non fanno parte delle query di join. Un esempio è una tabella Users per l'accesso e l'autenticazione dell'applicazione.

## <a name="shards"></a>Partizioni

Nella sezione precedente è stata descritta la modalità di archiviazione delle tabelle distribuite come partizioni nei nodi di lavoro. In questa sezione vengono illustrati i dettagli più tecnici.

La tabella dei metadati `pg_dist_shard` nel coordinatore contiene una riga per ogni partizione di ogni tabella distribuita nel sistema. La riga corrisponde a un ID di partizione con un intervallo di numeri interi in uno spazio hash (shardminvalue, shardmaxvalue).

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

Se il nodo coordinatore desidera determinare quale partizione include una riga di `github_events`, viene eseguito l'hashing del valore della colonna Distribution nella riga. Il nodo verifica quindi l'intervallo della partizione\'s che contiene il valore con hash. Gli intervalli vengono definiti in modo che l'immagine della funzione hash sia la relativa unione non contigua.

### <a name="shard-placements"></a>Posizionamenti della partizione

Si supponga che la partizione 102027 sia associata alla riga in questione. La riga viene letta o scritta in una tabella denominata `github_events_102027` in uno dei ruoli di lavoro. Quale ruolo di lavoro? Questo è determinato interamente dalle tabelle di metadati. Il mapping della partizione al ruolo di lavoro è noto come posizionamento della partizione.

Il nodo coordinatore riscrive le query in frammenti che fanno riferimento a tabelle specifiche come `github_events_102027` ed esegue tali frammenti sui ruoli di lavoro appropriati. Di seguito è riportato un esempio di esecuzione di una query dietro le quinte per individuare il nodo che contiene l'ID di partizione 102027.

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
- Informazioni su come [scegliere una colonna di distribuzione](concepts-hyperscale-choose-distribution-column.md) per le tabelle distribuite.
