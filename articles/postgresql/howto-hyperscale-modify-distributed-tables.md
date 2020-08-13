---
title: Modificare le tabelle distribuite-iperscalabilità (CITUS)-database di Azure per PostgreSQL
description: Comandi SQL per la creazione e la modifica di tabelle distribuite-iperscalabilità (CITUS) tramite il portale di Azure
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: 628944f9763dc79148e0b64c97158064208412bf
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136938"
---
# <a name="distribute-and-modify-tables"></a>Distribuire e modificare tabelle

## <a name="distributing-tables"></a>Distribuzione di tabelle

Per creare una tabella distribuita, è necessario innanzitutto definire lo schema della tabella. A tale scopo, è possibile definire una tabella usando l'istruzione [Create Table](http://www.postgresql.org/docs/current/static/sql-createtable.html) nello stesso modo in cui si farebbe con una normale tabella PostgreSQL.

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

Successivamente, è possibile usare la \_ funzione create Distributed \_ Table () per specificare la colonna di distribuzione della tabella e creare le partizioni del ruolo di lavoro.

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

La chiamata di funzione informa iperscale (CITUS) che la \_ tabella degli eventi di GitHub deve essere distribuita nella \_ colonna ID repository (eseguendo l'hashing del valore della colonna). La funzione crea anche partizioni nei nodi di lavoro usando i valori di configurazione del fattore di replica CITUS. partizionamento \_ e CITUS. partizionamento \_ \_ .

Viene creato un totale di CITUS. partizionamento \_ numero di partizioni, in cui ogni partizione è proprietaria di una parte di uno spazio hash e viene replicata in base al valore predefinito di configurazione del fattore di replica CITUS. partizione \_ \_ . Le repliche di partizione create nel ruolo di lavoro hanno lo stesso schema di tabella, indice e definizioni di vincolo della tabella nel coordinatore. Una volta create le repliche, la funzione Salva tutti i metadati distribuiti nel coordinatore.

A ogni partizione creata viene assegnato un ID di partizione univoco e tutte le relative repliche hanno lo stesso ID di partizione. Le partizioni sono rappresentate nel nodo del ruolo di lavoro come tabelle di PostgreSQL normali denominate \' TableName \_ shardid \' dove tablename è il nome della tabella distribuita e l'ID di partizione è l'ID univoco assegnato. È possibile connettersi alle istanze Postgres di lavoro per visualizzare o eseguire comandi su singole partizioni.

A questo punto si è pronti per inserire i dati nella tabella distribuita ed eseguire query su di essa. Per ulteriori informazioni sulla funzione definita dall'utente in questa sezione, vedere la [tabella e il riferimento DDL di partizionamento](reference-hyperscale-functions.md#table-and-shard-ddl) .

### <a name="reference-tables"></a>Tabelle di riferimento

Il metodo precedente distribuisce le tabelle in più partizioni orizzontali.  Un'altra possibilità è la distribuzione delle tabelle in una singola partizione e la replica della partizione in ogni nodo di lavoro. Le tabelle distribuite in questo modo sono denominate *tabelle di riferimento.* Vengono usati per archiviare i dati a cui è necessario accedere di frequente da più nodi in un cluster.

I candidati comuni per le tabelle di riferimento includono:

-   Tabelle più piccole che devono essere unite a tabelle distribuite di dimensioni maggiori.
-   Tabelle in app multi-tenant che non dispongono di una colonna ID tenant o che non sono \' associate a un tenant. O, durante la migrazione, anche per alcune tabelle associate a un tenant.
-   Le tabelle che richiedono vincoli univoci in più colonne e sono sufficientemente piccole.

Si supponga, ad esempio, che un sito di eCommerce multi-tenant debba calcolare l'imposta sulle vendite per le transazioni in uno qualsiasi dei relativi archivi. Le informazioni fiscali non sono \' specifiche di alcun tenant. È opportuno inserirlo in una tabella condivisa. Una tabella di riferimento incentrata sugli Stati Uniti potrebbe essere simile alla seguente:

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

A questo punto, le query come una tassa di calcolo per un carrello acquisti possono essere unite alla `states` tabella senza sovraccarico di rete e possono aggiungere una chiave esterna al codice di stato per una migliore convalida.

Oltre a distribuire una tabella come singola partizione replicata, la funzione definita dall' `create_reference_table` utente viene contrassegnata come tabella di riferimento nelle tabelle di metadati di iperscala (CITUS). Iperscale (CITUS) esegue automaticamente i commit in due fasi ([2pc](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) per apportare modifiche alle tabelle contrassegnate in questo modo, che fornisce garanzie di coerenza assoluta.

Se si dispone di una tabella distribuita con un numero di partizioni pari a uno, è possibile aggiornarla in modo che sia una tabella di riferimento riconosciuta come la seguente:

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

Per un altro esempio di utilizzo delle tabelle di riferimento, vedere l' [esercitazione sul database multi-tenant](tutorial-design-database-hyperscale-multi-tenant.md).

### <a name="distributing-coordinator-data"></a>Distribuzione dei dati del coordinatore

Se un database PostgreSQL esistente viene convertito nel nodo coordinatore per un cluster iperscalabile (CITUS), i dati nelle relative tabelle possono essere distribuiti in modo efficiente e con un'interruzione minima di un'applicazione.

La `create_distributed_table` funzione descritta in precedenza funziona in tabelle vuote e non vuote e per quest'ultimo distribuisce automaticamente le righe della tabella in tutto il cluster. Si saprà se i dati vengono copiati in base alla presenza del messaggio, \" Nota: copia dei dati dalla tabella locale \. . \" Per esempio:

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

Le Scritture nella tabella vengono bloccate durante la migrazione dei dati e le Scritture in sospeso vengono gestite come query distribuite una volta eseguito il commit della funzione. Se la funzione ha esito negativo, le query diventano nuovamente locali. Le letture possono continuare normalmente e diventeranno query distribuite una volta eseguito il commit della funzione.

Quando si distribuiscono le tabelle A e B, dove A dispone di una chiave esterna a B, distribuire prima la tabella di destinazione chiave B. L'operazione in ordine errato provocherà un errore:

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

Se non è possibile distribuire nell'ordine corretto, eliminare le chiavi esterne, distribuire le tabelle e ricreare le chiavi esterne.

Quando si esegue la migrazione di dati da un database esterno, ad esempio da Amazon RDS a iperscale (CITUS) cloud, creare innanzitutto le tabelle distribuite con iperscalabilità (CITUS) tramite `create_distributed_table` , quindi copiare i dati nella tabella.
La copia in tabelle distribuite evita di esaurire lo spazio disponibile nel nodo coordinatore.

## <a name="colocating-tables"></a>Spostamento di tabelle

La condivisione percorso indica l'inserimento di informazioni correlate sulle stesse macchine. Consente di eseguire query efficienti, sfruttando al contempo la scalabilità orizzontale per l'intero set di dati. Per altre informazioni, vedere condivisione [percorso](concepts-hyperscale-colocation.md).

Le tabelle si trovano in gruppi. Per controllare manualmente l'assegnazione del gruppo di condivisione del percorso di una tabella, usare il `colocate_with` parametro facoltativo di `create_distributed_table` . Se non si è interessati alla condivisione \' percorso di una tabella \' , omettere questo parametro. Per impostazione predefinita viene impostato il valore `'default'` , che raggruppa la tabella con qualsiasi altra tabella con percorso predefinito con lo stesso tipo di colonna di distribuzione, il numero di partizioni e il fattore di replica. Se si desidera interrompere o aggiornare la condivisione percorso implicita, è possibile utilizzare `update_distributed_table_colocation()` .

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

Quando una nuova tabella non è correlata ad altre nel gruppo di condivisione percorso implicito, specificare `colocated_with => 'none'` .

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

Suddividendo le tabelle non correlate nei rispettivi gruppi di condivisione percorso sarà possibile migliorare le prestazioni di [ribilanciamento](howto-hyperscale-scaling.md#rebalance-shards) delle partizioni, perché le partizioni nello stesso gruppo devono essere spostate insieme.

Quando le tabelle sono effettivamente correlate, ad esempio quando vengono unite in join, può essere utile eseguire una colocazione esplicita. I guadagni della condivisione percorso appropriata sono più importanti rispetto al sovraccarico di ribilanciamento.

Per colocare in modo esplicito più tabelle, distribuirne una e quindi inserirle nel proprio gruppo di condivisione percorso. Ad esempio:

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

Le informazioni sui gruppi di condivisione percorso sono archiviate nella tabella [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) , mentre [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) rivela quali tabelle sono assegnate a quali gruppi.

## <a name="dropping-tables"></a>Eliminazione di tabelle

È possibile usare il comando standard PostgreSQL DROP TABLE per rimuovere le tabelle distribuite. Come per le normali tabelle, DROP TABLE rimuove tutti gli indici, le regole, i trigger e i vincoli esistenti per la tabella di destinazione. Inoltre, rilascia anche le partizioni nei nodi di lavoro e pulisce i relativi metadati.

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>Modifica di tabelle

Iperscale (CITUS) propaga automaticamente molti tipi di istruzioni DDL.
Se si modifica una tabella distribuita nel nodo coordinatore, le partizioni vengono aggiornate anche nei ruoli di lavoro. Altre istruzioni DDL richiedono la propagazione manuale e alcune altre sono proibite, ad esempio quelle che modificano una colonna di distribuzione.
Il tentativo di eseguire DDL non idoneo per la propagazione automatica genererà un errore e lascerà le tabelle nel nodo coordinatore invariate.

Di seguito è riportato un riferimento alle categorie di istruzioni DDL che si propagano.
La propagazione automatica può essere abilitata o disabilitata con un [parametro di configurazione](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean)

### <a name="addingmodifying-columns"></a>Aggiunta/modifica di colonne

Iperscale (CITUS) propaga la maggior parte dei comandi [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html) automaticamente. L'aggiunta di colonne o la modifica dei valori predefiniti funzionano come in un database PostgreSQL a computer singolo:

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

Sono state apportate modifiche significative a una colonna esistente, ad esempio la ridenominazione o la modifica del tipo di dati. Tuttavia, non è possibile modificare il tipo di dati della [colonna di distribuzione](concepts-hyperscale-nodes.md#distribution-column) .
Questa colonna determina il modo in cui i dati della tabella vengono distribuiti tramite il cluster iperscalabile (CITUS) e la modifica del tipo di dati richiede lo scorrimento dei dati.

Il tentativo di eseguire questa operazione genera un errore:

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>Aggiunta/rimozione di vincoli

L'uso di iperscalabilità (CITUS) consente di continuare a sfruttare la sicurezza di un database relazionale, inclusi i vincoli del database. vedere la [documentazione](https://www.postgresql.org/docs/current/static/ddl-constraints.html)di PostgreSQL.
A causa della natura dei sistemi distribuiti, l'iperscalabilità (CITUS) non può fare riferimento ai vincoli di univocità o all'integrità referenziale tra i nodi del ruolo di lavoro.

Per configurare una chiave esterna tra le tabelle distribuite con percorso condiviso, includere sempre la colonna di distribuzione nella chiave. L'inclusione della colonna di distribuzione può comportare la creazione del composto di chiavi.

In queste situazioni è possibile creare chiavi esterne:

-   tra due tabelle locali (non distribuite),
-   tra due tabelle di riferimento,
-   tra due [tabelle](concepts-hyperscale-colocation.md) distribuite condivise quando la chiave include la colonna di distribuzione o
-   come tabella distribuita che fa riferimento a una [tabella di riferimento](concepts-hyperscale-nodes.md#type-2-reference-tables)

Le chiavi esterne dalle tabelle di riferimento alle tabelle distribuite non sono supportate.

> [!NOTE]
>
> Le chiavi primarie e i vincoli di univocità devono includere la colonna di distribuzione. Se vengono aggiunti a una colonna non di distribuzione, verrà generato un errore

Questo esempio illustra come creare chiavi primarie ed esterne nelle tabelle distribuite:

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

Analogamente, includere la colonna di distribuzione nei vincoli di univocità:

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

I vincoli NOT-NULL possono essere applicati a qualsiasi colonna (distribuzione o meno) perché non richiedono ricerche tra i ruoli di lavoro.

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>Uso di vincoli non validi

In alcune situazioni può essere utile applicare vincoli per le nuove righe, consentendo al contempo le righe non conformi esistenti di rimanere invariate. Iperscale (CITUS) supporta questa funzionalità per i vincoli CHECK e Foreign Key, usando \' la \" designazione di vincolo non valido di PostgreSQL \" .

Si consideri, ad esempio, un'applicazione che archivia i profili utente in una [tabella di riferimento](concepts-hyperscale-nodes.md#type-2-reference-tables).

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

Nel corso del tempo immaginiamo che alcuni indirizzi non vengano inseriti nella tabella.

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

Si vuole convalidare gli indirizzi, ma PostgreSQL non ci consente in genere di aggiungere un vincolo CHECK che non riesce per le righe esistenti. Tuttavia, *consente* un vincolo contrassegnato come non valido:

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

Le nuove righe sono ora protette.

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

In seguito, durante le ore non di punta, un amministratore di database può tentare di correggere le righe non valide e di riconvalidare il vincolo.

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

La documentazione di PostgreSQL contiene altre informazioni sul vincolo NOT VALID e VALIDATE nella sezione [ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html) .

### <a name="addingremoving-indices"></a>Aggiunta/rimozione di indici

Iperscale (CITUS) supporta l'aggiunta e la rimozione di [indici](https://www.postgresql.org/docs/current/static/sql-createindex.html):

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

L'aggiunta di un indice accetta un blocco di scrittura, che può essere indesiderato in un \" sistema di record multi-tenant. \" Per ridurre al minimo i tempi di inattività dell'applicazione, creare invece l'indice [simultaneamente](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) . Questo metodo richiede un lavoro più totale rispetto a una compilazione di indice standard e richiede più tempo per il completamento. Tuttavia, poiché consente di continuare le normali operazioni durante la compilazione dell'indice, questo metodo è utile per l'aggiunta di nuovi indici in un ambiente di produzione.

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
