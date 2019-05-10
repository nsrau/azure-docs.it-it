---
title: Esercitazione sulla progettazione di un database multi-tenant con Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima)
description: Questa esercitazione illustra come creare, popolare ed eseguire query su tabelle distribuite in Database di Azure per PostgreSQL Hyperscale (Citus) (anteprima).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: b135baf73e21cd524b6e8fad35452362f36cf0c0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079546"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Esercitazione: Progettare un database multi-tenant con Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima)

In questa esercitazione si usa Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima) per imparare a:

> [!div class="checklist"]
> * Creare un gruppo di server Hyperscale (Citus)
> * Usare l'utilità psql per creare uno schema
> * Ripartire le tabelle tra i nodi
> * Inserire dati di esempio
> * Eseguire query sui dati dei tenant
> * Condividere dati tra i tenant
> * Personalizzare lo schema in base al tenant

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Creare un database di Azure per PostgreSQL

Seguire questa procedura per creare un database di Azure per il server PostgreSQL:
1. Fare clic su **Crea una risorsa** nell'angolo superiore sinistro del portale di Azure.
2. Selezionare **Database** nella pagina **Nuovo** e selezionare **Database di Azure per PostgreSQL** nella pagina **Database**.
3. Per l'opzione di distribuzione, fare clic sul pulsante **Crea** in **Gruppo di server Hyperscale (Citus) - ANTEPRIMA**.
4. Compilare il modulo dei dettagli del nuovo server con le informazioni seguenti:
   - Gruppo di risorse: fare clic sul collegamento **Crea nuovo** sotto la casella di testo di questo campo. Immettere un nome, ad esempio **myresourcegroup**.
   - Nome gruppo server: immettere un nome univoco per il nuovo gruppo di server, che verrà usato anche per un sottodominio di server.
   - Nome utente amministratore: immettere un nome utente univoco. Verrà usato in seguito per la connessione al database.
   - Password: deve essere composta da almeno otto caratteri e deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (0-9) e caratteri non alfanumerici (!, $, #, % e così via).
   - Località: usare la località più vicina agli utenti per consentire loro l'accesso più rapido ai dati.

   > [!IMPORTANT]
   > L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa esercitazione. Prendere nota di queste informazioni per usarle in seguito.

5. Fare clic su **Configura gruppo di server**. Lasciare invariate le impostazioni di tale sezione e fare clic su **Salva**.
6. Fare clic su **Rivedi e crea** e quindi su **Crea** per effettuare il provisioning del server. Il provisioning richiede alcuni minuti.
7. La pagina verrà reindirizzata per monitorare la distribuzione. Quando lo stato attivo passa da **La distribuzione è in corso** a **La distribuzione è stata completata**, fare clic sulla voce di menu **Output** nella parte sinistra della pagina.
8. La pagina degli output conterrà un nome host di coordinatore con accanto un pulsante per copiare il valore negli Appunti. Prendere nota di queste informazioni per un uso successivo.

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Il servizio Database di Azure per PostgreSQL usa un firewall a livello di server. Per impostazione predefinita, il firewall impedisce a tutte le applicazioni e a tutti gli strumenti esterni di connettersi al server e ai database sul server. È necessario aggiungere una regola per aprire il firewall per un intervallo specifico di indirizzi IP.

1. Dalla sezione **Output** dove prima si è copiato il nome host del nodo coordinatore, fare clic per tornare alla voce di menu **Panoramica**.

2. Trovare il gruppo di ridimensionamento per la distribuzione nell'elenco delle risorse e fare clic su di esso. Il nome sarà preceduto da "sg-".

3. Fare clic su **Firewall** in **Sicurezza** nel menu a sinistra.

4. Fare clic sul collegamento **+ Aggiungi regola del firewall per l'indirizzo IP del client corrente**. Fare infine clic sul pulsante **Salva**.

5. Fare clic su **Save**.

   > [!NOTE]
   > Il server PostgreSQL Azure comunica sulla porta 5432. Se si sta cercando di connettersi da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. In questo caso, non è possibile connettersi al server di database SQL di Azure, a meno che il reparto IT non apra la porta 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Connettersi al database usando psql in Cloud Shell

Si usi ora l'utilità della riga di comando [psql](https://www.postgresql.org/docs/current/app-psql.html) per connettersi al Database di Azure per il server PostgreSQL.
1. Avviare Azure Cloud Shell tramite l'icona del terminale nel riquadro di spostamento in alto.

   ![Database di Azure per PostgreSQL - Icona del terminale di Azure Cloud Shell](./media/tutorial-design-database-hyperscale-multi-tenant/psql-cloud-shell.png)

2. Azure Cloud Shell si apre nel browser, consentendo di digitare i comandi bash.

   ![Database di Azure per PostgreSQL - Prompt Bash di Azure Cloud Shell](./media/tutorial-design-database-hyperscale-multi-tenant/psql-bash.png)

3. Al prompt di Cloud Shell connettersi al database di Azure per il server PostgreSQL usando i comandi psql. Il formato seguente è usato per connettersi a un Database di Azure per il server PostgreSQL con l'utilità [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Il comando seguente, ad esempio, consente di connettersi al database predefinito denominato **citus** nel server PostgreSQL **mydemoserver.postgres.database.azure.com** usando le credenziali di accesso. Quando richiesto, immettere la password di amministratore del server.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>Usare l'utilità psql per creare uno schema

Una volta connessi a Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima) tramite psql, è possibile completare alcune attività di base. Questa esercitazione illustra come creare un'app Web che consente agli inserzionisti di tenere traccia delle proprie campagne pubblicitarie.

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

È quindi opportuno creare una tabella per contenere le informazioni geografiche condivise. A tale scopo, eseguire il codice seguente in psql:

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

Caricare i dati di esempio nella tabella. Ricordarsi di eseguire questa operazione in psql all'interno della directory in cui è stato scaricato il set di dati.

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
