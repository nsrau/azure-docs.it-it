---
title: Dati distribuiti in Database di Azure per PostgreSQL-con Iperscalabilità (Citus) (anteprima)
description: Le tabelle e partizioni distribuite nel gruppo di server.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 9020ee690d93a1b477471fac4a482a909fca5935
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077336"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Dati distribuiti in Database di Azure per PostgreSQL-con Iperscalabilità (Citus) (anteprima)

Questo articolo illustra i tipi di tre tabella in con Iperscalabilità (Citus).
Mostra le tabelle distribuite come vengono archiviati come partizioni e il modo in cui le partizioni vengono inserite nei nodi.

## <a name="table-types"></a>Tipi di tabella

Esistono tre tipi di tabelle in un gruppo di server con Iperscalabilità, ognuno utilizzato per scopi diversi.

### <a name="type-1-distributed-tables"></a>Tipo 1: le tabelle distribuite

Il primo tipo e più comuni, viene *distribuite* tabelle. Vengono visualizzate come normali tabelle in istruzioni SQL, ma sono orizzontalmente *partizionata* tra i nodi di lavoro. Ciò significa che le righe della tabella vengono archiviate in nodi diversi, nel frammento tabelle denominate *partizioni*.

Non solo SQL in esecuzione su scala molto vasta ma le istruzioni DDL in un cluster, pertanto la modifica dello schema di una tabella distribuita viene propagata per aggiornare le partizioni della tabella nei ruoli di lavoro.

#### <a name="distribution-column"></a>Colonna di distribuzione

Su scala molto vasta Usa il partizionamento orizzontale algoritmico per assegnare righe alle partizioni. L'assegnazione viene definito in modo deterministico in base il valore di una colonna di tabella denominato il *colonna di distribuzione.* L'amministratore del cluster è necessario definire questa colonna durante la distribuzione di una tabella.
La scelta giusta è importante per le prestazioni e funzionalità.

### <a name="type-2-reference-tables"></a>Tipo 2: le tabelle di riferimento

Una tabella di riferimento è un tipo di tabella con distribuzione il cui tutto il contenuto è concentrato in una singola partizione. La partizione viene replicata in ogni ruolo di lavoro, in modo che le query in qualsiasi ruolo di lavoro possono accedere in locale, le informazioni di riferimento senza l'overhead di rete di richiesta di righe da un altro nodo. Le tabelle di riferimento non dispone di alcuna colonna di distribuzione poiché non è necessario distinguere partizioni separate per ogni riga.

Le tabelle di riferimento vengono in genere piccole e vengono usate per archiviare i dati relativi alle query in esecuzione su qualsiasi nodo di lavoro. Enumerata, ad esempio, i valori, ad esempio gli stati degli ordini o le categorie di prodotti.

### <a name="type-3-local-tables"></a>Tipo 3: tabelle locali

Quando si usa con Iperscalabilità, il nodo coordinatore a che si connette è un normale database PostgreSQL. È possibile creare tabelle normali per il coordinatore e sceglierli non alla partizione.

Un buon candidato per le tabelle locali saranno le piccole tabelle amministrative che non fanno parte di query join. Ad esempio, una tabella di utenti per l'autenticazione e account di accesso dell'applicazione.

## <a name="shards"></a>Partizioni

La sezione precedente descritte le tabelle distribuite come vengono archiviate come partizioni in nodi di lavoro. In questa sezione Ottiene ulteriori direttamente ai dettagli tecnici.

Il `pg_dist_shard` tabella di metadati per il coordinatore contiene una riga per ogni partizione di ogni tabella con distribuzione del sistema. La riga corrisponde a un ID di partizione con un intervallo di integer in uno spazio di hash (shardminvalue, shardmaxvalue):

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

Se il nodo coordinatore desidera determinare quale partizione contiene una riga della `github_events`, genera un hash per il valore della colonna di distribuzione nella riga e controlla quale partizione\'intervallo s contiene il valore con hash. (Gli intervalli sono definiti in modo che l'immagine della funzione hash è l'unione non contiguo).

### <a name="shard-placements"></a>Posizioni delle partizioni

Si supponga che tale partizionamento 102027 associata alla riga in questione. La riga verrà letti o scritta in una tabella denominata `github_events_102027` in uno dei ruoli di lavoro. Computer di lavoro? Che sono determinate interamente dalle tabelle di metadati e il mapping di partizione per ruolo di lavoro è noto come il partizionamento *posizionamento*.

Il nodo coordinatore riscrive le query in frammenti che fanno riferimento a tabelle specifiche, ad esempio `github_events_102027`, ed esegue tali frammenti nei ruoli di lavoro appropriati. Ecco un esempio di una query eseguita in background per trovare il nodo che contiene partizioni 102027 ID.

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
- Informazioni su come [sceglie una colonna di distribuzione](concepts-hyperscale-choose-distribution-column.md) per le tabelle distribuite
