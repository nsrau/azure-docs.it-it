---
title: Tabella Colocation-iperscala (CITUS)-database di Azure per PostgreSQL
description: Come archiviare insieme le informazioni correlate per query più veloci
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967338"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Condivisione della tabella nel database di Azure per PostgreSQL: iperscalabilità (CITUS)

La condivisione percorso consente di archiviare insieme le informazioni correlate negli stessi nodi. Le query possono essere eseguite rapidamente quando tutti i dati necessari sono disponibili senza il traffico di rete. La condivisione dei dati correlati in nodi diversi consente l'esecuzione efficiente delle query in parallelo in ogni nodo.

## <a name="data-colocation-for-hash-distributed-tables"></a>Condivisione percorso dati per le tabelle con distribuzione hash

In database di Azure per PostgreSQL – iperscalabilità (CITUS), una riga viene archiviata in una partizione se l'hash del valore nella colonna di distribuzione rientra nell'intervallo di hash della partizione. Le partizioni con lo stesso intervallo di hash vengono sempre posizionate nello stesso nodo. Le righe con valori di colonna di distribuzione uguali si trovano sempre nello stesso nodo tra le tabelle.

![Partizioni](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Esempio pratico di condivisione percorso

Si considerino le tabelle seguenti che potrebbero far parte di un'soluzione SaaS di analisi web multi-tenant:

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

A questo punto è necessario rispondere alle query che potrebbero essere emesse da un dashboard per i clienti. Una query di esempio è "restituisce il numero di visite nell'ultima settimana per tutte le pagine che iniziano con"/Blog "nel tenant sei".

Se i dati erano nell'opzione di distribuzione a server singolo, è possibile esprimere facilmente la query usando il set completo di operazioni relazionali offerte da SQL:

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

Fino a quando il [working set](https://en.wikipedia.org/wiki/Working_set) per questa query si adatta alla memoria, una tabella a server singolo è una soluzione appropriata. Si prendano in considerazione le opportunità di ridimensionamento del modello di dati con l'opzione di distribuzione iperscalabile (CITUS).

### <a name="distribute-tables-by-id"></a>Distribuisci tabelle in base all'ID

Le query a server singolo iniziano a rallentare man mano che il numero di tenant e i dati archiviati per ogni tenant aumentano. Il working set smette di adattarsi alla memoria e la CPU diventa un collo di bottiglia.

In questo caso, è possibile partizionare i dati in molti nodi usando iperscale (CITUS). La prima e più importante scelta da fare quando si decide di partizionare è la colonna di distribuzione. Iniziamo con una scelta ingenua di usare `event_id` per la tabella eventi e `page_id` per la tabella `page`:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Quando i dati vengono distribuiti tra ruoli di lavoro diversi, non è possibile eseguire un join come si farebbe per un singolo nodo PostgreSQL. Al contrario, è necessario eseguire due query:

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

L'esecuzione delle query deve consultare i dati nelle partizioni sparse tra i nodi.

![Query inefficienti](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

In questo caso, la distribuzione dei dati crea svantaggi sostanziali:

-   Overhead causato dall'esecuzione di query su ogni partizione e dall'esecuzione di più query.
-   Overhead del Q1 che restituisce molte righe al client.
-   Q2 diventa grande.
-   Per la necessità di scrivere query in più passaggi è necessario apportare modifiche all'applicazione.

I dati sono dispersi, quindi le query possono essere eseguite in parallelo. È vantaggioso solo se la quantità di lavoro eseguita dalla query è sostanzialmente maggiore del sovraccarico dovuto all'esecuzione di query su molte partizioni.

### <a name="distribute-tables-by-tenant"></a>Distribuire le tabelle in base al tenant

In iperscala (CITUS), le righe con lo stesso valore della colonna di distribuzione sono sempre presenti nello stesso nodo. A partire da, è possibile creare le tabelle con `tenant_id` come colonna di distribuzione.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Ora iperscalabile (CITUS) può rispondere alla query a server singolo originale senza modifiche (Q1):

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

A causa del filtro e del join in tenant_id, l'iperscalabilità (CITUS) sa che è possibile rispondere all'intera query usando il set di partizioni con percorso condiviso che contengono i dati per quel particolare tenant. Un singolo nodo PostgreSQL può rispondere alla query in un singolo passaggio.

![Query migliore](media/concepts-hyperscale-colocation/colocation-better-query.png)

In alcuni casi, è necessario modificare le query e gli schemi di tabella in modo da includere l'ID tenant in vincoli univoci e condizioni di join. Questa modifica è in genere semplice.

## <a name="next-steps"></a>Passaggi successivi

- Vedere in che modo i dati dei tenant sono collocati nell' [esercitazione multi-tenant](tutorial-design-database-hyperscale-multi-tenant.md).
