---
title: Tabelle di sistema-iperscala (CITUS)-database di Azure per PostgreSQL
description: Metadati per l'esecuzione di query distribuite
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: c11fd7a9cb6fdd3eb976d0b9e6a91fdc69bf9fba
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136845"
---
# <a name="system-tables-and-views"></a>Tabelle e viste di sistema

Iperscale (CITUS) crea e gestisce tabelle speciali contenenti informazioni sui dati distribuiti nel gruppo di server. Il nodo coordinatore consulta queste tabelle quando si pianificano le modalità di esecuzione delle query nei nodi di lavoro.

## <a name="coordinator-metadata"></a>Metadati coordinatore

Iperscale (CITUS) divide ogni tabella distribuita in più partizioni logiche basate sulla colonna di distribuzione. Il coordinatore gestisce quindi le tabelle dei metadati per tenere traccia delle statistiche e delle informazioni sull'integrità e sulla posizione di queste partizioni.

In questa sezione vengono descritte tutte le tabelle di metadati e il relativo schema.
È possibile visualizzare ed eseguire query su queste tabelle utilizzando SQL dopo aver eseguito l'accesso al nodo coordinatore.

> [!NOTE]
>
> I gruppi di server iperscalare che eseguono versioni precedenti del motore CITUS potrebbero non offrire tutte le tabelle elencate di seguito.

### <a name="partition-table"></a>Tabella di partizione

La \_ tabella di partizione PG dist archivia i \_ metadati relativi alle tabelle del database distribuite. Per ogni tabella distribuita vengono inoltre archiviate informazioni sul metodo di distribuzione e informazioni dettagliate sulla colonna di distribuzione.

| Nome         | Tipo     | Descrizione                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | Tabella distribuita a cui corrisponde questa riga. Questo valore fa riferimento alla colonna relfilenode nella tabella pg_class catalogo di sistema.                                                                                                                   |
| partmethod   | char     | Metodo utilizzato per il partizionamento/distribuzione. I valori di questa colonna corrispondenti a metodi di distribuzione diversi sono Append:' a', hash:' h ', tabella di riferimento:' n'                                                                          |
| partkey      | testo     | Informazioni dettagliate sulla colonna di distribuzione, inclusi il numero di colonna, il tipo e altre informazioni rilevanti.                                                                                                                                      |
| colocationid | integer  | Gruppo di condivisione percorso a cui appartiene la tabella. Le tabelle nello stesso gruppo consentono i join con condivisione percorso e i rollup distribuiti tra le altre ottimizzazioni. Questo valore fa riferimento alla colonna colocationid nella tabella pg_dist_colocation.                      |
| repmodel     | char     | Metodo utilizzato per la replica dei dati. I valori di questa colonna corrispondenti a metodi di replica diversi sono: replica basata su istruzioni CITUS:' c', replica di streaming PostgreSQL:' s', commit a due fasi (per le tabelle di riferimento):' t' |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>Tabella partizione

La \_ \_ tabella di partizione PG dist archivia i metadati relativi alle singole partizioni di una tabella. Pg_dist_shard contiene informazioni sulle partizioni della tabella distribuita che appartengono a e statistiche sulla colonna di distribuzione per le partizioni.
Per le tabelle di Accodamento distribuite, queste statistiche corrispondono ai valori min/max della colonna di distribuzione. Per le tabelle con distribuzione hash, si tratta di intervalli di token hash assegnati a tale partizione. Queste statistiche vengono usate per eliminare le partizioni non correlate durante le query SELECT.

| Nome          | Tipo     | Descrizione                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | Tabella distribuita a cui corrisponde questa riga. Questo valore fa riferimento alla colonna relfilenode nella tabella pg_class catalogo di sistema.                                                          |
| shardid       | bigint   | Identificatore univoco globale assegnato a questa partizione.                                                                                                                                           |
| shardstorage  | char     | Tipo di archiviazione usato per questa partizione. Nella tabella seguente sono illustrati diversi tipi di archiviazione.                                                                                               |
| shardminvalue | testo     | Per accodare tabelle distribuite, valore minimo della colonna di distribuzione in questa partizione (inclusi). Per le tabelle con distribuzione hash, valore del token hash minimo assegnato a tale partizione (inclusivo). |
| shardmaxvalue | testo     | Per accodare tabelle distribuite, valore massimo della colonna di distribuzione in questa partizione (inclusi). Per le tabelle con distribuzione hash, il valore massimo del token hash assegnato a tale partizione (inclusi). |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>Tipi di archiviazione partizione

La colonna shardstorage nella \_ partizione PG dist \_ indica il tipo di archiviazione usato per la partizione. Di seguito è riportata una breve panoramica dei diversi tipi di archiviazione di partizioni e la relativa rappresentazione.

| Tipo di archiviazione | Valore Shardstorage | Descrizione                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABLE        | t                | Indica che la partizione archivia i dati appartenenti a una tabella distribuita regolare.         |
| COLONNARE     | c                | Indica che la partizione archivia i dati a colonne. (Utilizzato dalle tabelle cstore_fdw distribuite) |
| FOREIGN      | f                | Indica che la partizione archivia i dati esterni. (Utilizzato dalle tabelle file_fdw distribuite)    |

### <a name="shard-placement-table"></a>Tabella di posizionamento partizione

La \_ \_ tabella di selezione host PG rileva il percorso delle repliche di partizione nei nodi di lavoro. Ogni replica di una partizione assegnata a un nodo specifico è detta posizionamento della partizione. In questa tabella vengono archiviate informazioni sull'integrità e sulla posizione di ogni posizione di partizionamento.

| Nome        | Tipo   | Descrizione                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | Identificatore di partizione associato a questo posizionamento. Questo valore fa riferimento alla colonna shardid nella tabella pg_dist_shard catalogo.             |
| shardstate  | INT    | Descrive lo stato di questo posizionamento. Nella sezione seguente sono descritti diversi Stati di partizionamento.                                         |
| shardlength | bigint | Per accodare tabelle distribuite, la dimensione del posizionamento delle partizioni sul nodo del ruolo di lavoro in byte. Per le tabelle con distribuzione hash, zero.            |
| placementid | bigint | Identificatore univoco generato automaticamente per ogni singolo posizionamento.                                                                           |
| groupid     | INT    | Indica un gruppo di un server primario e zero o più server secondari quando viene utilizzato il modello di replica di flusso. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>Stati di posizionamento partizione

Iperscale (CITUS) gestisce l'integrità delle partizioni in base alla selezione host. Se un posizionamento inserisce il sistema in uno stato incoerente, CITUS lo contrassegna automaticamente come non disponibile. Lo stato del posizionamento viene registrato nella tabella pg_dist_shard_placement, all'interno della colonna shardstate. Ecco una breve panoramica dei diversi Stati di posizionamento della partizione:

| Nome dello stato | Valore Shardstate | Descrizione                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| FINALIZZATO  | 1                | Le nuove partizioni di stato vengono create in. I posizionamenti di partizionamento in questo stato sono considerati aggiornati e vengono utilizzati per la pianificazione e l'esecuzione delle query.                                                                                                                                                                                                                                                                                 |
| INACTIVE   | 3                | Le posizionamenti di partizionamento in questo stato sono considerate inattive perché non sono sincronizzate con altre repliche della stessa partizione. Lo stato può verificarsi quando un'operazione di Accodamento, modifica (inserimento, aggiornamento, eliminazione) o DDL non riesce per questo posizionamento. Query Planner ignorerà le inserimenti in questo stato durante la pianificazione e l'esecuzione. Gli utenti possono sincronizzare i dati in queste partizioni con una replica finalizzata come attività in background. |
| TO_DELETE  | 4                | Se CITUS tenta di eliminare un posizionamento della partizione in risposta a una chiamata master_apply_delete_command e non riesce, la selezione host viene spostata in questo stato. Gli utenti possono quindi eliminare queste partizioni come attività in background successiva.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>Tabella del nodo di lavoro

La \_ tabella del nodo Dist di PG \_ contiene informazioni sui nodi di lavoro nel cluster.

| Nome             | Tipo    | Descrizione                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NodeId           | INT     | Identificatore generato automaticamente per un singolo nodo.                                                                                                                                          |
| groupid          | INT     | Identificatore utilizzato per indicare un gruppo di un server primario e zero o più server secondari, quando viene utilizzato il modello di replica di flusso. Per impostazione predefinita, è uguale a NodeId.         |
| nodeName         | testo    | Nome host o indirizzo IP del nodo del ruolo di lavoro PostgreSQL.                                                                                                                                     |
| Deport         | INT     | Numero di porta su cui è in ascolto il nodo del ruolo di lavoro PostgreSQL.                                                                                                                              |
| noderack         | testo    | Opzionale Informazioni sulla posizione del rack per il nodo di lavoro.                                                                                                                                 |
| HasMetadata      | boolean | Riservato per utilizzo interno.                                                                                                                                                                 |
| IsActive         | boolean | Indica se il nodo è attivo accettando posizionamenti di partizionamento.                                                                                                                                     |
| noderole         | testo    | Indica se il nodo è primario o secondario                                                                                                                                                 |
| nodecluster      | testo    | Nome del cluster che contiene questo nodo                                                                                                                                               |
| shouldhaveshards | boolean | Se false, le partizioni verranno spostate al di fuori del nodo (svuotato) durante il ribilanciamento, né verranno posizionate partizioni dalle nuove tabelle distribuite nel nodo, a meno che non siano condivise con le partizioni già presenti |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>Tabella oggetti distribuiti

La \_ \_ tabella di oggetti dist CITUS.PG contiene un elenco di oggetti, ad esempio tipi e funzioni che sono stati creati nel nodo coordinatore e propagati ai nodi di lavoro. Quando un amministratore aggiunge nuovi nodi del ruolo di lavoro al cluster, iperscale (CITUS) crea automaticamente copie degli oggetti distribuiti nei nuovi nodi, nell'ordine corretto per soddisfare le dipendenze degli oggetti.

| Nome                        | Tipo    | Descrizione                                          |
|-----------------------------|---------|------------------------------------------------------|
| ClassID                     | oid     | Classe dell'oggetto distribuito                      |
| objid                       | oid     | ID oggetto dell'oggetto distribuito                  |
| objsubid                    | integer | ID secondario dell'oggetto distribuito, ad esempio attnum |
| tipo                        | testo    | Parte dell'indirizzo stabile usato durante gli aggiornamenti di PG   |
| object_names                | testo []  | Parte dell'indirizzo stabile usato durante gli aggiornamenti di PG   |
| object_args                 | testo []  | Parte dell'indirizzo stabile usato durante gli aggiornamenti di PG   |
| distribution_argument_index | integer | Valido solo per funzioni/procedure distribuite      |
| colocationid                | integer | Valido solo per funzioni/procedure distribuite      |

\"Gli indirizzi stabili \" identificano in modo univoco gli oggetti indipendentemente da un server specifico. Iperscale (CITUS) tiene traccia degli oggetti durante un aggiornamento di PostgreSQL usando indirizzi stabili creati con la funzione [PG \_ identifica \_ oggetto \_ As \_ Address ()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) .

\'Di seguito è riportato un esempio di come `create_distributed_function()` aggiunge voci alla `citus.pg_dist_object` tabella:

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Tabella del gruppo di condivisione percorso

La \_ tabella PG dist \_ Colocation contiene informazioni sulle tabelle che \' devono essere inserite insieme o condivise. [colocated](concepts-hyperscale-colocation.md)
Quando due tabelle si trovano nello stesso gruppo di condivisione percorso, l'iperscalabilità (CITUS) garantisce che le partizioni con gli stessi valori di partizione vengano inserite negli stessi nodi di lavoro.
La condivisione percorso consente le ottimizzazioni del join, alcuni rollup distribuiti e il supporto della chiave esterna. La condivisione del percorso di partizionamento viene dedotta quando i conteggi delle partizioni, i fattori di replica e i tipi di colonna di partizione corrispondono tutti tra due tabelle. è tuttavia possibile specificare un gruppo di condivisione percorso personalizzato quando si crea una tabella distribuita, se necessario.

| Nome                   | Tipo | Descrizione                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | INT  | Identificatore univoco per il gruppo di condivisione percorso a cui corrisponde la riga.          |
| shardcount             | INT  | Numero di partizioni per tutte le tabelle in questo gruppo di condivisione percorso                          |
| replicationfactor      | INT  | Fattore di replica per tutte le tabelle in questo gruppo di condivisione percorso.                  |
| distributioncolumntype | oid  | Tipo di colonna di distribuzione per tutte le tabelle in questo gruppo di condivisione percorso. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Tabella della strategia di ribilanciamento

Questa tabella definisce strategie che [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) possibile usare per determinare la posizione in cui spostare le partizioni.

| Nome                           | Tipo    | Descrizione                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | Indica se rebalance_table_shards deve scegliere questa strategia per impostazione predefinita. Usare citus_set_default_rebalance_strategy per aggiornare la colonna             |
| shard_cost_function            | regproc | Identificatore di una funzione di costo, che deve assumere un shardid come bigint e restituire la relativa nozione di un costo, come tipo Real                                |
| node_capacity_function         | regproc | Identificatore per una funzione di capacità, che deve assumere un NodeId come int e restituire la sua nozione di capacità del nodo come tipo Real                          |
| shard_allowed_on_node_function | regproc | Identificatore per una funzione che ha dato shardid bigint e nodeidarg int restituisce un valore booleano che indica se CITUS può archiviare la partizione nel nodo |
| default_threshold              | float4  | Soglia per il riscatto di un nodo troppo pieno o troppo vuoto, che determina quando il rebalance_table_shards deve tentare di spostare le partizioni                    |
| minimum_threshold              | float4  | Una salvaguardia per impedire che l'argomento soglia di rebalance_table_shards () venga impostato su un valore troppo basso                                                  |

Un'installazione iperscalabile (CITUS) viene fornita con queste strategie nella tabella:

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

La strategia predefinita, `by_shard_count` , assegna a ogni partizione lo stesso costo. L'effetto è quello di equalizzare il numero di partizioni tra i nodi. L'altra strategia predefinita, `by_disk_size` , assegna un costo a ogni partizione che corrisponde alle dimensioni del disco in byte, più quelle delle partizioni con il percorso. Le dimensioni del disco vengono calcolate usando `pg_total_relation_size` , quindi sono inclusi gli indici. Questa strategia tenta di ottenere lo stesso spazio su disco in ogni nodo. Si noti la soglia di 0,1, che impedisce lo spostamento di partizioni non necessarie causato da differenze non significative nello spazio su disco.

#### <a name="creating-custom-rebalancer-strategies"></a>Creazione di strategie di ribilanciamento personalizzate

Di seguito sono riportati alcuni esempi di funzioni che possono essere usate all'interno di nuove strategie di ribilanciamento delle partizioni e registrate nel [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) con la funzione di [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) .

-   Impostazione di un'eccezione di capacità del nodo in base al modello di nome host:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   Ribilanciamento in base al numero di query che passano a una partizione, misurata in base al [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table):

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   Isolamento di una partizione specifica (10000) in un nodo (Address \' 10.0.0.1 \' ):

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>Tabella Statistiche query

Iperscale (CITUS) fornisce `citus_stat_statements` statistiche sulle modalità di esecuzione delle query e su chi. È \' analoga a (e possono essere unite con) la visualizzazione delle [ \_ \_ istruzioni PG stat](https://www.postgresql.org/docs/current/static/pgstatstatements.html) in PostgreSQL, che tiene traccia delle statistiche sulla velocità della query.

Questa vista consente di tenere traccia delle query ai tenant di origine in un'applicazione multi-tenant, che consente di decidere quando eseguire l'isolamento dei tenant.

| Nome          | Tipo   | Descrizione                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| QueryId       | bigint | identificatore (valido per i join pg_stat_statements)                                   |
| userid        | oid    | utente che ha eseguito la query                                                           |
| dbid          | oid    | istanza di database di coordinatore                                                 |
| query         | testo   | stringa di query resi anonimi                                                          |
| Executor      | testo   | CITUS Executor usato: Adaptive, Real-Time, Task-Tracker, router o INSERT-SELECT |
| partition_key | testo   | valore della colonna di distribuzione nelle query eseguite dal router; else NULL               |
| calls         | bigint | numero di volte in cui è stata eseguita la query                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

Risultati:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

Avvertenze:

-   I dati delle statistiche non vengono replicati e gli \' arresti anomali o il failover del database sono stati superati
-   Tiene traccia di un numero limitato di query, impostate da `pg_stat_statements.max` GUC (valore predefinito 5000)
-   Per troncare la tabella, utilizzare la `citus_stat_statements_reset()` funzione

### <a name="distributed-query-activity"></a>Attività query distribuite

Iperscale (CITUS) fornisce visualizzazioni speciali per controllare le query e i blocchi in tutto il cluster, incluse le query specifiche della partizione utilizzate internamente per compilare i risultati per le query distribuite.

-   ** \_ \_ \_ attività CITUS dist stat**: Mostra le query distribuite in esecuzione su tutti i nodi. Superset di `pg_stat_activity` , utilizzabile laddove il secondo è.
-   **CITUS \_ Worker \_ Stat \_ Activity**: Mostra le query sui thread di lavoro, incluse le query di frammenti sulle singole partizioni.
-   ** \_ \_ attese di blocco CITUS**: query bloccate in tutto il cluster.

Nelle prime due visualizzazioni sono incluse tutte le colonne dell' [ \_ \_ attività PG stat](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) più l'host/porta host del ruolo di lavoro che ha avviato la query e l'host o la porta del nodo coordinatore del cluster.

Si consideri, ad esempio, il conteggio delle righe in una tabella distribuita:

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

È possibile vedere che la query viene visualizzata in `citus_dist_stat_activity` :

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

Questa query richiede informazioni da tutte le partizioni. Alcune informazioni sono contenute in una partizione `users_table_102038` , che viene archiviata in `localhost:9700` . È possibile visualizzare una query che accede alla partizione esaminando la `citus_worker_stat_activity` vista:

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

Il `query` campo Mostra i dati copiati fuori dalla partizione da contare.

> [!NOTE]
> Se una query del router (ad esempio, un singolo tenant in un'applicazione multi-tenant,' SELECT
> * La tabella in cui tenant_id = X ') viene eseguita senza un blocco di transazioni, \_ quindi \_ \_ le colonne nome host query master e \_ \_ porta host query master \_ saranno null \_ nell' \_ attività stat di CITUS Worker \_ .

Per verificarne il `citus_lock_waits` funzionamento, è possibile generare manualmente una situazione di blocco. Prima di tutto è necessario \' configurare una tabella di test dal coordinatore:

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

Quindi, utilizzando due sessioni sul coordinatore, è possibile eseguire questa sequenza di istruzioni:

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

La `citus_lock_waits` visualizzazione Mostra la situazione.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

In questo esempio le query sono state originate sul coordinatore, ma la vista può anche elencare i blocchi tra le query originate sui ruoli di lavoro (eseguiti con iperscale (CITUS) MX per l'istanza).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul modo in cui alcune [funzioni di scalabilità](reference-hyperscale-functions.md) modificano le tabelle di sistema
* Esaminare i concetti di [nodi e tabelle](concepts-hyperscale-nodes.md)
