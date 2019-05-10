---
title: Avvio rapido per Database di Azure per PostgreSQL – Hyperscale (Citus) (anteprima)
description: Avvio rapido per creare ed eseguire query su tabelle distribuite in Database di Azure per PostgreSQL Hyperscale (Citus) (anteprima).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/06/2019
ms.openlocfilehash: 4271d94f07125a870cc4aa859b01db819d583f40
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406439"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Guida introduttiva: Creare un database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima) nel portale di Azure

Il database di Azure per PostgreSQL è un servizio gestito usato per eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. Questo argomento di avvio rapido illustra come creare un gruppo di server Database di Azure per PostgreSQL - Hyperscale (Citus) (anteprima) con il portale di Azure. Verranno esplorati i dati distribuiti: le tabelle di partizionamento orizzontale tra i nodi, l'inserimento di dati di esempio e l'esecuzione di query su più nodi.

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
   > L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida introduttiva. Prendere nota di queste informazioni per usarle in seguito.

5. Fare clic su **Configura gruppo di server**. Lasciare invariate le impostazioni di tale sezione e fare clic su **Salva**.
6. Fare clic su **Rivedi e crea** e quindi su **Crea** per effettuare il provisioning del server. Il provisioning richiede alcuni minuti.
7. La pagina verrà reindirizzata per monitorare la distribuzione. Quando lo stato attivo passa da **La distribuzione è in corso** a **La distribuzione è stata completata**, fare clic sulla voce di menu **Output** nella parte sinistra della pagina.
8. La pagina degli output conterrà un nome host di coordinatore con accanto un pulsante per copiare il valore negli Appunti. Prendere nota di queste informazioni per un uso successivo.

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Il servizio Database di Azure per PostgreSQL – Hyperscale (Citus) usa un firewall a livello di server. Per impostazione predefinita, il firewall impedisce a tutte le applicazioni e a tutti gli strumenti esterni di connettersi al nodo coordinatore e ai database al suo interno. È necessario aggiungere una regola per aprire il firewall per un intervallo specifico di indirizzi IP.

1. Dalla sezione **Output** dove prima si è copiato il nome host del nodo coordinatore, fare clic per tornare alla voce di menu **Panoramica**.

2. Il nome del gruppo di ridimensionamento della distribuzione verrà preceduto da "sg-". Individuarlo nell'elenco delle risorse e fare clic su di esso.

3. Fare clic su **Firewall** in **Sicurezza** nel menu a sinistra.

4. Fare clic sul collegamento **+ Aggiungi regola del firewall per l'indirizzo IP del client corrente**. Fare infine clic sul pulsante **Salva**.

5. Fare clic su **Save**.

   > [!NOTE]
   > Il server PostgreSQL Azure comunica sulla porta 5432. Se si sta cercando di connettersi da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. In questo caso, non è possibile connettersi al server di database SQL di Azure, a meno che il reparto IT non apra la porta 5432.
   >

## <a name="connect-to-the-database-using-psql-in-cloud-shell"></a>Connettersi al database usando psql in Cloud Shell

Si usi ora l'utilità della riga di comando [psql](https://www.postgresql.org/docs/current/app-psql.html) per connettersi al Database di Azure per il server PostgreSQL.
1. Avviare Azure Cloud Shell tramite l'icona del terminale nel riquadro di spostamento in alto.

   ![Database di Azure per PostgreSQL - Icona del terminale di Azure Cloud Shell](./media/quickstart-create-hyperscale-portal/psql-cloud-shell.png)

2. Azure Cloud Shell si apre nel browser, consentendo di digitare i comandi bash.

   ![Database di Azure per PostgreSQL - Prompt Bash di Azure Cloud Shell](./media/quickstart-create-hyperscale-portal/psql-bash.png)

3. Al prompt di Cloud Shell connettersi al database di Azure per il server PostgreSQL usando i comandi psql. Il formato seguente è usato per connettersi a un Database di Azure per il server PostgreSQL con l'utilità [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
   ```bash
   psql --host=<myserver> --username=myadmin --dbname=citus
   ```

   Il comando seguente, ad esempio, consente di connettersi al database predefinito denominato **citus** nel server PostgreSQL **mydemoserver.postgres.database.azure.com** usando le credenziali di accesso. Quando richiesto, immettere la password di amministratore del server.

   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --username=myadmin --dbname=citus
   ```

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

Il campo `payload` di `github_events` presenta un tipo di dati JSONB. JSONB è il tipo di dati JSON in formato binario in Postgres. Questo semplifica l'archiviazione di uno schema più flessibile in una singola colonna.

Postgres può creare un indice `GIN` su questo tipo, che indicizzerà tutte le chiavi e i valori in esso contenuti. Grazie all'indice, l'esecuzione di query del payload in varie condizioni diventa un'operazione semplice e veloce. È possibile procedere alla creazione di un paio di indici prima di caricare i dati. In psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Successivamente queste tabelle Postgres verranno spostate nel nodo coordinatore e verrà indicato a Hyperscale di partizionarle tra i ruoli di lavoro. A tale scopo, verrà eseguita una query per ogni tabella specificando la chiave in cui verrà eseguita la partizione. Nell'esempio corrente verrà eseguita la partizione sia della tabella degli eventi che della tabella degli utenti in `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

A questo punto, è possibile caricare i dati. Scaricare i due file di esempio [users.csv](https://examples.citusdata.com/users.csv) ed [events.csv](https://examples.citusdata.com/events.csv). Dopo aver scaricato i file, connettersi al database tramite psql, prestando attenzione a eseguire psql dalla directory contenente i file scaricati. Caricare i dati con il comando `\copy`:

```sql
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
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
      payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create risorse di Azure in un gruppo di server. Se non si prevede di avere bisogno di queste risorse in futuro, eliminare il gruppo di server. Fare clic sul pulsante **Elimina** nella pagina **Panoramica** per il gruppo di server. Quando viene visualizzata la richiesta in una pagina popup, verificare il nome del gruppo di server e fare clic sul pulsante **Elimina** in basso.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato illustrato come effettuare il provisioning di un gruppo di server Hyperscale (Citus). È stata stabilita la connessione al gruppo con psql, è stato creato uno schema e sono stati distribuiti i dati.

Come passaggio successivo, seguire un'esercitazione su come compilare applicazioni multi-tenant scalabili.
> [!div class="nextstepaction"]
> [Progettare un database multitenant](https://aka.ms/hyperscale-tutorial-multi-tenant)
