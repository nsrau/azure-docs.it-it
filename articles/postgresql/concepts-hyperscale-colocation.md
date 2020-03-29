---
title: Colocation delle tabelle - Hyperscale (Citus) - Database di Azure per PostgreSQL
description: Come archiviare insieme le informazioni correlate per query più veloci
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967338"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Colocation di tabelle nel database di Azure per PostgreSQL - Hyperscale (Citus)Table colocation in Azure Database for PostgreSQL – Hyperscale (Citus)

Colocation significa archiviare le informazioni correlate insieme sugli stessi nodi. Le query possono andare veloci quando tutti i dati necessari sono disponibili senza alcun traffico di rete. La colocazione di dati correlati in nodi diversi consente alle query di essere eseguite in modo efficiente in parallelo in ogni nodo.

## <a name="data-colocation-for-hash-distributed-tables"></a>Colocation dei dati per le tabelle con distribuzione hashData colocation for hash-distributed tables

Nel database di Azure per PostgreSQL – Hyperscale (Citus) una riga viene archiviata in una partizione se l'hash del valore nella colonna di distribuzione rientra nell'intervallo di hash della partizione. Le partizioni con lo stesso intervallo di hash vengono sempre posizionate sullo stesso nodo. Le righe con valori di colonna di distribuzione uguali si trovano sempre sullo stesso nodo tra le tabelle.

![Frammenti](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Un esempio pratico di colocation

Si considerino le tabelle seguenti che potrebbero far parte di un SaaS di analisi Web multi-tenant:Consider the following tables that might be part of a multi-tenant web analytics SaaS:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

A questo punto si desidera rispondere alle query che potrebbero essere emesse da un dashboard rivolto al cliente. Una query di esempio è "Restituire il numero di visite nell'ultima settimana per tutte le pagine che iniziano con '/blog' nel tenant sei".

Se i nostri dati erano nell'opzione di distribuzione Single-Server, potremmo facilmente esprimere la nostra query utilizzando il ricco set di operazioni relazionali offerte da SQL:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Finché il [working set](https://en.wikipedia.org/wiki/Working_set) per questa query si adatta a questa query, una tabella a server singolo è una soluzione appropriata. Consideriamo le opportunità di scalabilità del modello di dati con l'opzione di distribuzione Hyperscale (Citus).

### <a name="distribute-tables-by-id"></a>Distribuire le tabelle in base all'IDDistribute tables by ID

Le query a server singolo iniziano a rallentare man mano che aumenta il numero di tenant e i dati archiviati per ogni tenant. Il working set interrompe l'adattamento in memoria e CPU diventa un collo di bottiglia.

In questo caso, è possibile partizionare i dati in molti nodi utilizzando Hyperscale (Citus). La prima e più importante scelta che è necessario fare quando si decide di partizionare è la colonna di distribuzione. Iniziamo con una scelta ingenua di `event_id` utilizzare `page_id` per `page` la tabella degli eventi e per la tabella:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Quando i dati vengono distribuiti tra diversi lavoratori, non è possibile eseguire un join come in un singolo nodo PostgreSQL. Al contrario, è necessario eseguire due query:Instead, we need to issue two queries:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Successivamente, i risultati dei due passaggi devono essere combinati dall'applicazione.

L'esecuzione delle query deve consultare i dati in partizioni distribuite tra i nodi.

![Query inefficienti](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

In questo caso, la distribuzione dei dati crea notevoli svantaggi:

-   Sovraccarico dovuto all'esecuzione di query su ogni partizione e all'esecuzione di più query.
-   Sovraccarico del Q1 che restituisce molte righe al client.
-   Q2 diventa grande.
-   La necessità di scrivere query in più passaggi richiede modifiche nell'applicazione.

I dati vengono distribuiti, pertanto le query possono essere parallelizzate. È utile solo se la quantità di lavoro che la query esegue è sostanzialmente maggiore dell'overhead di query su molte partizioni.

### <a name="distribute-tables-by-tenant"></a>Distribuire tabelle per tenantDistribute tables by tenant

In Hyperscale (Citus), è garantito che le righe con lo stesso valore della colonna di distribuzione si trovino nello stesso nodo. Ricominciando, possiamo creare `tenant_id` le nostre tabelle con come colonna di distribuzione.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Ora Hyperscale (Citus) può rispondere alla query originale a server singolo senza modifiche (Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

A causa del filtro e del join in base a i tenant_id, Hyperscale (Citus) sa che è possibile rispondere all'intera query usando il set di partizioni conlocate che contengono i dati per quel particolare tenant. Un singolo nodo PostgreSQL può rispondere alla query in un unico passaggio.

![Query migliore](media/concepts-hyperscale-colocation/colocation-better-query.png)

In alcuni casi, le query e gli schemi di tabella devono essere modificati per includere l'ID tenant nei vincoli univoci e nelle condizioni di join. Questa modifica è in genere semplice.

## <a name="next-steps"></a>Passaggi successivi

- Vedere come i dati del tenant vengono collocati [nell'esercitazione multi-tenant.](tutorial-design-database-hyperscale-multi-tenant.md)
