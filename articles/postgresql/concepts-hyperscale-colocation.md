---
title: Concetti relativi ai server in Database di Azure per PostgreSQL
description: Questo articolo presenta alcune considerazioni e linee guida per la configurazione e la gestione di server di Database di Azure per PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: d03cfd49887adf1f6a4650e374d3e13eeca735a4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "65077471"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Con più sedi nella tabella nel Database di Azure per PostgreSQL con Iperscalabilità (Citus) (anteprima)

Condivisione percorso significa che l'archiviazione delle informazioni correlate tra loro negli stessi nodi. Le query possono passare rapidamente quando i dati di tutte le necessarie sono disponibili senza alcun traffico di rete. Condivisione percorso per i dati correlati in nodi diversi consente alle query da eseguire in modo efficiente in parallelo in ogni nodo.

## <a name="data-colocation-for-hash-distributed-tables"></a>Condivisione percorso dati per tabelle con distribuzione hash

In con Iperscalabilità e una riga viene archiviata in una partizione, se l'hash del valore nella colonna di distribuzione è compreso nell'intervallo di hash della partizione. Le partizioni con lo stesso intervallo di hash vengono sempre inserite nello stesso nodo. Le righe con valori di colonna di distribuzione equa sono sempre nello stesso nodo tra tabelle.

![Partizioni](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Un esempio pratico di condivisione del percorso

Prendere in considerazione le tabelle seguenti che potrebbero far parte di un analitica web multi-tenant SaaS:

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

A questo punto si desidera rispondere alle query che può essere emesso da un dashboard per i clienti, ad esempio: "Restituisce il numero di visite nell'ultima settimana per tutte le pagine inizia con ' / blog' nel tenant six."

Se i dati in un'opzione di distribuzione a server singolo, è stato possibile express facilmente query tramite la vasta gamma di operazioni relazionali offerti da SQL:

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

Fino a quando la [working set](https://en.wikipedia.org/wiki/Working_set) per questa query si inserisce in memoria, una tabella a server singolo è la soluzione adatta. Tuttavia, si consideri l'opportunità di ridimensionamento del modello di dati con l'opzione di distribuzione con scalabilità elevatissima.

### <a name="distributing-tables-by-id"></a>Distribuzione di tabelle di base all'ID

Le query a server singolo avviare rallentano le prestazioni man mano che aumenta il numero di tenant e i dati archiviati per ogni tenant. Working set viene arrestato nella memoria e CPU diventa un collo di bottiglia.

In questo caso, è possibile partizioni di dati in molti nodi uso su scala molto vasta. La scelta di prima e più importante da prendere durante il partizionamento orizzontale è la colonna di distribuzione. Si inizierà con una scelta Naive dell'utilizzo `event_id` per la tabella di eventi e `page_id` per il `page` tabella:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Quando i dati sono suddivisi tra diversi ruoli di lavoro, non è possibile eseguire un join come si farebbe in un singolo nodo di PostgreSQL. Al contrario, si dovrà eseguire due query:

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

Successivamente, i risultati dai due passaggi devono essere combinati dall'applicazione.

Eseguire le query, è necessario consultare i dati in partizioni sparsi tra nodi.

![query non efficaci](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

In questo caso la distribuzione dei dati crea svantaggi sostanziali:

-   Overhead di esecuzione di query su ogni partizione, esecuzione di più query
-   Overhead di Q1 restituire il numero di righe al client
-   (Domanda 2) sta diventando di grandi dimensioni
-   La necessità di scrivere query in più passaggi richiede modifiche nell'applicazione

Poiché i dati sono suddivisi, le query possono essere eseguite in parallelo. Tuttavia, è solo vantaggioso se la quantità di lavoro che esegue la query è notevolmente maggiore il sovraccarico di una query su molte partizioni.

### <a name="distributing-tables-by-tenant"></a>Distribuzione di tabelle dal tenant

In con Iperscalabilità e le righe con lo stesso valore di colonna di distribuzione sono necessariamente sullo stesso nodo. Ricominciare, è possibile creare le tabelle con `tenant_id` come colonna di distribuzione.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

A questo punto con Iperscalabilità può rispondere a query a server singolo originale senza alcuna modifica (domanda 1):

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

A causa di filtro e join su tenant_id, su scala molto vasta sa che l'intera query è possibile rispondere con il set di partizioni con percorso condiviso che contengono i dati per il tenant specifico. Un singolo nodo di PostgreSQL è possibile rispondere alla query in un unico passaggio.

![query migliori](media/concepts-hyperscale-colocation/colocation-better-query.png)

In alcuni casi, query e gli schemi di tabella devono essere modificati per includere l'ID tenant in vincoli univoci e condizioni di join. Tuttavia, si tratta in genere una modifica semplice.

## <a name="next-steps"></a>Passaggi successivi

- Vedere come vengano con condivisione percorso dati del tenant nel [esercitazione multi-tenant](tutorial-design-database-hyperscale-multi-tenant.md)
