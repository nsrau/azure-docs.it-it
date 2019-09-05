---
title: Avvio rapido per Database di Azure per PostgreSQL – Hyperscale (Citus) (anteprima)
description: Avvio rapido per creare ed eseguire query su tabelle distribuite in Database di Azure per PostgreSQL Hyperscale (Citus) (anteprima).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: fe981167249e24a43a8cb14c51c9b7c1eb081225
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164025"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Guida introduttiva: Creare un database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima) nel portale di Azure

Il database di Azure per PostgreSQL è un servizio gestito usato per eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. Questo argomento di avvio rapido illustra come creare un gruppo di server Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima) con il portale di Azure. Verranno esplorati i dati distribuiti: le tabelle di partizionamento orizzontale tra i nodi, l'inserimento di dati di esempio e l'esecuzione di query su più nodi.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Creare e distribuire le tabelle

Dopo la connessione al nodo coordinatore Hyperscale tramite psql, è possibile completare alcune attività di base.

All'interno dei server Hyperscale esistono tre tipi di tabelle:

- Tabelle distribuite o partizionate (ripartite per agevolare il ridimensionamento a favore delle prestazioni e della parallelizzazione)
- Tabelle di riferimento (vengono mantenute più copie)
- Tabelle locali (spesso usate per le tabelle di amministrazione interna)

Questo argomento di avvio rapido si concentra in particolar modo sulle tabelle distribuite e su come imparare a usarle.

Il modello di dati che verrà usato è semplice: dati utente e dati sugli eventi provenienti da GitHub. Gli eventi includono la creazione di fork, commit GIT correlati a un'organizzazione e molto altro.

Dopo la connessione tramite psql è possibile creare le tabelle. Nella console di psql eseguire:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

Il campo `payload` di `github_events` presenta un tipo di dati JSONB. JSONB è il tipo di dati JSON in formato binario in Postgres. Il tipo di dati semplifica l'archiviazione di uno schema flessibile in una singola colonna.

Postgres può creare un indice `GIN` su questo tipo, che indicizzerà tutte le chiavi e i valori in esse contenuti. Grazie all'indice, l'esecuzione di query del payload in varie condizioni diventa un'operazione semplice e veloce. È possibile procedere alla creazione di un paio di indici prima di caricare i dati. In psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Successivamente queste tabelle Postgres verranno spostate nel nodo coordinatore e verrà indicato a Hyperscale di partizionarle tra i ruoli di lavoro. A tale scopo, verrà eseguita una query per ogni tabella specificando la chiave in cui verrà eseguita la partizione. Nell'esempio corrente verrà eseguita la partizione sia della tabella degli eventi che della tabella degli utenti in `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

A questo punto, è possibile caricare i dati. Ancora in psql passare alla shell per scaricare i file:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Caricare quindi i dati dai file nelle tabelle distribuite:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Eseguire le query

È arrivato il momento di eseguire qualche query. Iniziamo semplicemente con `count (*)` per sapere quanti dati sono stati caricati:

```sql
SELECT count(*) from github_events;
```

Ha funzionato. A breve torneremo su questo tipo di aggregazione, ma per adesso esaminiamo qualche altra query. Nella colonna `payload` JSONB sono contenuti numerosi dati, che però variano in base al tipo di evento. Gli eventi `PushEvent` contengono una dimensione che include il numero dei diversi commit per il push. È possibile usarlo per individuare il totale di commit orari:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Finora le query hanno riguardato esclusivamente github\_events, ma è possibile combinare queste informazioni con github\_users. Dal momento che sia gli utenti che gli eventi sono stati partizionati con lo stesso identificatore (`user_id`), le righe di entrambe le tabelle con ID utente corrispondenti avranno un [percorso condiviso](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) negli stessi nodi del database e potranno facilmente essere sottoposte a join.

Se si crea un join su `user_id`, Hyperscale può eseguire il push dell'esecuzione del join nelle partizioni in modo che l'esecuzione avvenga in parallelo nei nodi di lavoro. È ad esempio possibile trovare gli utenti che hanno creato il maggior numero di repository:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di server. Se non si prevede di avere bisogno di queste risorse in futuro, eliminare il gruppo di server. Fare clic sul pulsante **Elimina** nella pagina **Panoramica** per il gruppo di server. Quando viene visualizzata la richiesta in una pagina popup, verificare il nome del gruppo di server e fare clic sul pulsante **Elimina** in basso.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato illustrato come effettuare il provisioning di un gruppo di server Hyperscale (Citus). È stata stabilita la connessione al gruppo con psql, è stato creato uno schema e sono stati distribuiti i dati.

Come passaggio successivo, seguire un'esercitazione su come compilare applicazioni multi-tenant scalabili.
> [!div class="nextstepaction"]
> [Progettare un database multitenant](https://aka.ms/hyperscale-tutorial-multi-tenant)
