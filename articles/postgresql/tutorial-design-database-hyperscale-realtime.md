---
title: Esercitazione sulla progettazione di un dashboard in tempo reale con Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima)
description: Questa esercitazione illustra come creare, popolare ed eseguire query su tabelle distribuite in Database di Azure per PostgreSQL Hyperscale (Citus) (anteprima).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/06/2019
ms.openlocfilehash: 7324ab1d7aa6e42100c9c6760c17b0ea6445f21d
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079456"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus-preview"></a>Esercitazione: Progettare un dashboard di analisi in tempo reale usando Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima)

In questa esercitazione si usa Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima) per imparare a:

> [!div class="checklist"]
> * Creare un gruppo di server Hyperscale (Citus)
> * Usare l'utilità psql per creare uno schema
> * Condividere le tabelle tra i nodi
> * Generare i dati di esempio
> * Eseguire i rollup
> * Eseguire query sui dati non elaborati e aggregati
> * Impostare la scadenza dei dati

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
   - Nome gruppo server: **mydemoserver** (nome di un server, che esegue il mapping al nome DNS e deve essere univoco a livello globale).
   - Nome utente amministratore: **myadmin** (verrà usato in seguito per la connessione al database).
   - Password: deve essere composta da almeno otto caratteri e deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (0-9) e caratteri non alfanumerici (!, $, #, % e così via).
   - Località: usare la località più vicina agli utenti per consentire loro l'accesso più rapido ai dati.

   > [!IMPORTANT]
   > L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa esercitazione. Prendere nota di queste informazioni per usarle in seguito.

5. Fare clic su **Configura gruppo di server**. Lasciare invariate le impostazioni di tale sezione e fare clic su **Salva**.
6. Fare clic su **Rivedi e crea** e quindi su **Crea** per effettuare il provisioning del server. Il provisioning richiede alcuni minuti.
7. La pagina verrà reindirizzata per monitorare la distribuzione. Quando lo stato attivo passa da **La distribuzione è in corso** a **La distribuzione è stata completata**, fare clic sulla voce di menu **Output** nella parte sinistra della pagina.
8. La pagina degli output conterrà un nome host di coordinatore con accanto un pulsante per copiare il valore negli Appunti. Prendere nota di queste informazioni per un uso successivo.

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Il servizio Database di Azure per PostgreSQL usa un firewall a livello di server. Per impostazione predefinita, il firewall impedisce a tutte le applicazioni e strumenti esterni di connettersi al server e ai database sul server. È necessario aggiungere una regola per aprire il firewall per un intervallo specifico di indirizzi IP.

1. Dalla sezione **Output** dove prima si è copiato il nome host del nodo coordinatore, fare clic per tornare alla voce di menu **Panoramica**.

2. Trovare il gruppo di ridimensionamento per la distribuzione nell'elenco delle risorse e fare clic su di esso. Il nome sarà preceduto da "sg-".

3. Fare clic su **Firewall** in **Sicurezza** nel menu a sinistra.

4. Fare client sul collegamento **+ Aggiungi regola del firewall per l'indirizzo IP del client corrente**. Fare infine clic sul pulsante **Salva**.

5. Fare clic su **Save**.

   > [!NOTE]
   > Il server PostgreSQL Azure comunica sulla porta 5432. Se si sta cercando di connettersi da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. In questo caso, non è possibile connettersi al server di database SQL di Azure, a meno che il reparto IT non apra la porta 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Connettersi al database usando psql in Cloud Shell

Si usi ora l'utilità della riga di comando [psql](https://www.postgresql.org/docs/current/app-psql.html) per connettersi al Database di Azure per il server PostgreSQL.
1. Avviare Azure Cloud Shell tramite l'icona del terminale nel riquadro di spostamento in alto.

   ![Database di Azure per PostgreSQL - Icona del terminale di Azure Cloud Shell](./media/tutorial-design-database-hyperscale-realtime/psql-cloud-shell.png)

2. Azure Cloud Shell si apre nel browser, consentendo di digitare i comandi bash.

   ![Database di Azure per PostgreSQL - Prompt Bash di Azure Cloud Shell](./media/tutorial-design-database-hyperscale-realtime/psql-bash.png)

3. Al prompt di Cloud Shell connettersi al database di Azure per il server PostgreSQL usando i comandi psql. Il formato seguente è usato per connettersi a un Database di Azure per il server PostgreSQL con l'utilità [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Il comando seguente, ad esempio, connette al database predefinito denominato **citus** nel server PostgreSQL **mydemoserver.postgres.database.azure.com** usando le credenziali di accesso. Quando richiesto, immettere la password di amministratore del server.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

## <a name="use-psql-utility-to-create-a-schema"></a>Usare l'utilità psql per creare uno schema

Una volta connessi a Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima) tramite psql, è possibile completare alcune attività di base. Questa esercitazione illustra come inserire dati sul traffico dall'analisi Web e come eseguire il rollup dei dati per fornire dashboard in tempo reale basati su tali dati.

Verrà ora creata una tabella che utilizzerà tutti i dati sul traffico Web non elaborati. Eseguire i comandi seguenti nel terminale psql:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

Verrà anche creata una tabella che conterrà le aggregazioni al minuto e una tabella che gestisce la posizione dell'ultimo rollup. Eseguire anche il codice seguente in psql:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

È ora possibile visualizzare le tabelle appena create nell'elenco delle tabelle con questo comando psql:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Condividere le tabelle tra i nodi

Una distribuzione con iperscalabilità archivia le righe di tabella in nodi diversi in base al valore di una colonna designata dall'utente. Questa "colonna di distribuzione" determina come i dati vengono condivisi tra i nodi.

La colonna di distribuzione verrà impostata come site\_id, ovvero la chiave di partizione. In psql eseguire queste funzioni:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Generare i dati di esempio

A questo punto il gruppo di server sarà pronto per l'inserimento di alcuni dati. È possibile eseguire il codice seguente in locale dalla connessione `psql` per inserire in modo continuativo i dati.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

La query aggiunge una riga ogni 0,25 secondi circa. Le righe vengono archiviate in nodi di lavoro diversi determinati dalla colonna di distribuzione `site_id`.

   > [!NOTE]
   > Lasciare la query di generazione dei dati in esecuzione e aprire una seconda connessione psql per i comandi rimanenti in questa esercitazione.
   >

## <a name="query"></a>Query

L'opzione di hosting con iperscalabilità consente a più nodi di elaborare le query in parallelo per aumentare la velocità. Il database consente, ad esempio, di calcolare aggregazioni come SUM e COUNT nei nodi di lavoro e combina i risultati in una risposta finale.

Ecco una query per il conteggio delle richieste Web al minuto insieme ad alcune statistiche.
Provare a eseguirla in psql e osservare i risultati.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="performing-rollups"></a>Esecuzione di rollup

La query precedente funziona bene nelle fasi iniziali, ma le prestazioni diminuiranno con l'aumentare dei dati. Anche con l'elaborazione distribuita, è più veloce precalcolare questi dati che ricalcolarli ripetutamente.

Per fare in modo che il dashboard non subisca rallentamenti, eseguire regolarmente il rollup dei dati non elaborati in una tabella aggregata. In questo caso si eseguirà il rollup nella tabella di aggregazione di un minuto, ma è anche possibile avere aggregazioni di 5 minuti, 15 minuti, un'ora e così via.

Poiché questo rollup verrà eseguito continuamente, si creerà una funzione per eseguirlo. Eseguire questi comandi in psql per creare la funzione `rollup_http_request`.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Dopo aver creato la funzione, usarla per eseguire il rollup dei dati:

```sql
SELECT rollup_http_request();
```

Con i dati in formato preaggregato è possibile eseguire una query sulla tabella di rollup per ottenere lo stesso report di prima. Eseguire questo comando:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Impostare come scaduti i dati meno recenti

I rollup velocizzano le query, ma è comunque necessario impostare come scaduti i dati meno recenti per evitare costi di archiviazione eccessivi. È sufficiente decidere per quanto tempo conservare i dati per ogni granularità e usare le query standard per eliminare i dati scaduti. Nell'esempio seguente è stato deciso di conservare i dati non elaborati per un giorno e le aggregazioni al minuto per un mese:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

In fase di produzione è possibile eseguire il wrapping di queste query in una funzione e chiamarla ogni minuto in un processo cron.

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di server. Se non si prevede di avere bisogno di queste risorse in futuro, eliminare il gruppo di server. Fare clic sul pulsante *Elimina* nella pagina *Panoramica* per il gruppo di server. Quando viene visualizzata la richiesta in una pagina popup, verificare il nome del gruppo di server e fare clic sul pulsante *Elimina* in basso.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come effettuare il provisioning di un gruppo di server Hyperscale (Citus). È stata stabilita la connessione al gruppo con psql, è stato creato uno schema e sono stati distribuiti i dati. È stato illustrato come eseguire query sui dati in formato non elaborato, come aggregare regolarmente i dati, eseguire query sulle tabelle aggregate e impostare come scaduti i dati meno recenti.

L'argomento successivo sono i concetti relativi all'iperscalabilità.
> [!div class="nextstepaction"]
> [Tipi di nodi Hyperscale](https://aka.ms/hyperscale-concepts)