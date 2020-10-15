---
title: Nodi-iperscala (CITUS)-database di Azure per PostgreSQL
description: Informazioni sui tipi di nodi e tabelle in un gruppo di server in database di Azure per PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2019
ms.openlocfilehash: b3eda2c8de8319552f32938f20ff98af0e0a49fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314828"
---
# <a name="nodes-and-tables-in-azure-database-for-postgresql--hyperscale-citus"></a>Nodi e tabelle nel database di Azure per PostgreSQL: iperscalabilità (CITUS)

## <a name="nodes"></a>Nodi

Il tipo di hosting iperscalabile (CITUS) consente ai server di database di Azure per PostgreSQL (detti nodi) di coordinarsi tra loro in un'architettura "Shared Nothing". I nodi in un gruppo di server contengono collettivamente più dati e utilizzano più core CPU di quanti ne sarebbero possibili in un singolo server. L'architettura consente inoltre la scalabilità del database aggiungendo più nodi al gruppo di server.

### <a name="coordinator-and-workers"></a>Coordinatore e ruoli di lavoro

Ogni gruppo di server dispone di un nodo coordinatore e di più ruoli di lavoro. Le applicazioni inviano le query al nodo coordinatore, che lo inoltra ai dipendenti pertinenti e accumula i risultati. Le applicazioni non sono in grado di connettersi direttamente ai thread di lavoro.

Iperscale (CITUS) consente all'amministratore del database di *distribuire* tabelle, archiviando righe diverse in nodi di lavoro diversi. Le tabelle distribuite rappresentano le prestazioni principali per l'iperscalabilità (CITUS). Se non si riesce a distribuire le tabelle, queste rimangono interamente nel nodo coordinatore e non possono sfruttare il parallelismo tra computer.

Per ogni query sulle tabelle distribuite, il coordinatore lo instrada a un singolo nodo di lavoro o parallelizzazione tra diversi a seconda che i dati necessari si trovino in un singolo nodo o in più. Il coordinatore decide cosa fare consultando le tabelle di metadati. In queste tabelle vengono rilevati i nomi DNS e l'integrità dei nodi del ruolo di lavoro e la distribuzione dei dati tra i nodi.

## <a name="table-types"></a>Tipi table

Sono disponibili tre tipi di tabelle in un gruppo di server con iperscalabilità (CITUS), ognuno archiviato in modo diverso nei nodi e utilizzato per scopi diversi.

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

La `pg_dist_shard` tabella dei metadati nel coordinatore contiene una riga per ogni partizione di ogni tabella distribuita nel sistema. La riga corrisponde a un ID di partizione con un intervallo di numeri interi in uno spazio hash (shardminvalue, shardmaxvalue).

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

Se il nodo coordinatore desidera determinare quale partizione include una riga di `github_events` , viene eseguito l'hashing del valore della colonna Distribution nella riga. Il nodo verifica quindi l' \' intervallo della partizione che contiene il valore con hash. Gli intervalli vengono definiti in modo che l'immagine della funzione hash sia la relativa unione non contigua.

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

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>Passaggi successivi

- [Determinare il tipo di applicazione](concepts-hyperscale-app-type.md) da preparare per la modellazione dei dati
