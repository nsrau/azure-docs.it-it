---
title: Esercitazione sulla progettazione di un database multi-tenant con Database di Azure per PostgreSQL - Hyperscale (Citus)
description: Questa esercitazione illustra come creare, popolare ed eseguire query su tabelle distribuite in Database di Azure per PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 130c3e9f5abb24ffcc4e0c4ad6b96af5fca62090
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496547"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Esercitazione: Progettare un database multi-tenant con Database di Azure per PostgreSQL - Hyperscale (Citus)

In questa esercitazione si usa Database di Azure per PostgreSQL - Hyperscale (Citus) per imparare a:

> [!div class="checklist"]
> * Creare un gruppo di server Hyperscale (Citus)
> * Usare l'utilità psql per creare uno schema
> * Ripartire le tabelle tra i nodi
> * Inserire dati di esempio
> * Eseguire query sui dati dei tenant
> * Condividere dati tra i tenant
> * Personalizzare lo schema in base al tenant

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Usare l'utilità psql per creare uno schema

Una volta connessi a Database di Azure per PostgreSQL - Hyperscale (Citus) tramite psql, è possibile completare alcune attività di base. Questa esercitazione illustra come creare un'app Web che consente agli inserzionisti di tenere traccia delle proprie campagne pubblicitarie.

Poiché l'app può essere usata da più aziende, si creeranno una tabella in cui includere le aziende e un'altra tabella per le campagne pubblicitarie. Nella console di psql eseguire questi comandi:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Per la gestione delle inserzioni di ogni campagna è previsto un addebito. Si aggiungerà quindi una tabella anche per le inserzioni eseguendo il codice seguente in psql dopo il codice mostrato sopra:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Infine si terrà traccia delle statistiche relative ai clic e alle impression per ogni inserzione:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

Le tabelle appena create possono ora essere visualizzate nell'elenco delle tabelle di psql eseguendo questo comando:

```postgres
\dt
```

Le applicazioni multi-tenant possono applicare l'univocità solo a livello di tenant e pertanto tutte le chiavi primarie ed esterne includono l'ID aziendale.

## <a name="shard-tables-across-nodes"></a>Ripartire le tabelle tra i nodi

Una distribuzione con iperscalabilità archivia le righe di tabella in nodi diversi in base al valore di una colonna designata dall'utente. Questa "colonna di distribuzione" indica i tenant proprietari delle diverse righe.

In questa esercitazione si imposterà la colonna di distribuzione come company\_id, l'identificatore del tenant. In psql eseguire queste funzioni:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Inserire dati di esempio

A questo punto, nella riga di comando normale all'esterno di psql, scaricare i set di dati di esempio:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Tornare quindi in psql ed eseguire il caricamento bulk dei dati. Assicurarsi di eseguire psql nella stessa directory in cui sono stati scaricati i file di dati.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

I dati verranno ora distribuiti tra i nodi di lavoro.

## <a name="query-tenant-data"></a>Eseguire query sui dati dei tenant

Quando l'applicazione richiede i dati per un singolo tenant, il database può eseguire la query su un singolo nodo di lavoro. Le query su singolo tenant filtrano i dati in base a un ID di tenant. Ad esempio, la query seguente filtra inserzioni e impression in base all'identificatore `company_id = 5`. Provare a eseguirla in psql ed esaminare i risultati.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Condividere dati tra i tenant

Finora tutte le tabelle sono state distribuite per `company_id`, ma alcuni dati non "appartengono" ad alcun tenant in particolare e possono essere condivisi. Ad esempio, tutte le aziende nella piattaforma per inserzioni di esempio possono avere l'esigenza di ottenere informazioni geografiche relative ai destinatari in base agli indirizzi IP.

È quindi opportuno creare una tabella per contenere le informazioni geografiche condivise. Eseguire i comandi seguenti in psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Impostare quindi `geo_ips` come "tabella di riferimento" per archiviare una copia della tabella in ogni nodo di lavoro.

```sql
SELECT create_reference_table('geo_ips');
```

Caricare i dati di esempio nella tabella. Ricordarsi di eseguire questo comando in psql all'interno della directory in cui è stato scaricato il set di dati.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Il join della tabella dei clic con geo\_ips è efficace in tutti i nodi.
Ecco un join per trovare la posizione di tutti gli utenti che hanno fatto clic su un'inserzione
290. Provare a eseguire la query in psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Personalizzare lo schema in base al tenant

Un singolo tenant potrebbe avere la necessità di archiviare informazioni particolari che non sono richieste da altri. Tutti i tenant condividono tuttavia un'infrastruttura comune con uno schema di database identico. Dove inserire i dati aggiuntivi richiesti?

Una soluzione può essere quella di usare un tipo di colonna aperta, ad esempio JSONB di PostgreSQL.  Nello schema dell'esercitazione è presente un campo JSONB in `clicks` denominato `user_data`.
Un'azienda, ad esempio quella con ID 5, può usare la colonna per rilevare se l'utente sta usando un dispositivo mobile.

Ecco una query per individuare gli utenti che fanno più clic sull'inserzione, ovvero i visitatori tradizionali o gli utenti di dispositivi mobili.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

È possibile ottimizzare la query per una singola azienda creando un [indice parziale](https://www.postgresql.org/docs/current/static/indexes-partial.html).

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Più in generale, è possibile creare [indici GIN](https://www.postgresql.org/docs/current/static/gin-intro.html) in ogni chiave e valore all'interno della colonna.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di server. Se non si prevede di avere bisogno di queste risorse in futuro, eliminare il gruppo di server. Fare clic sul pulsante *Elimina* nella pagina *Panoramica* per il gruppo di server. Quando viene visualizzata la richiesta in una pagina popup, verificare il nome del gruppo di server e fare clic sul pulsante *Elimina* in basso.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come effettuare il provisioning di un gruppo di server Hyperscale (Citus). È stata stabilita la connessione al gruppo con psql, è stato creato uno schema e sono stati distribuiti i dati. Si è inoltre appreso come eseguire query sui dati sia all'interno dei tenant sia tra più tenant e come personalizzare lo schema per ogni tenant.

Nell'argomento successivo verranno illustrati i concetti relativi all'iperscalabilità.
> [!div class="nextstepaction"]
> [Tipi di nodi Hyperscale](https://aka.ms/hyperscale-concepts)
