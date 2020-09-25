---
title: Parametri del server – iperscala (CITUS)-database di Azure per PostgreSQL
description: Parametri nell'API SQL con iperscalabilità (CITUS)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336242"
---
# <a name="server-parameters"></a>Parametri del server

Sono disponibili vari parametri del server che influiscono sul comportamento di iperscalabilità (CITUS), sia dal PostgreSQL standard che da specifico a iperscalabile (CITUS).
Questi parametri possono essere impostati nella portale di Azure per un gruppo di server CITUS (iperscalare). Nella categoria **Impostazioni** scegliere parametri del **nodo di lavoro** o **parametri del nodo coordinatore**. Queste pagine consentono di impostare i parametri per tutti i nodi del ruolo di lavoro o solo per il nodo coordinatore.

## <a name="hyperscale-citus-parameters"></a>Parametri iperscale (CITUS)

> [!NOTE]
>
> I gruppi di server iperscale (CITUS) che eseguono versioni precedenti del motore CITUS potrebbero non offrire tutti i parametri elencati di seguito.

### <a name="general-configuration"></a>Configurazione generale

#### <a name="citususe_secondary_nodes-enum"></a>CITUS. USA \_ \_ nodi secondari (enum)

Imposta i criteri da usare quando si scelgono i nodi per le query di selezione. Se è impostato su' always ', il pianificatore eseguirà una query solo sui nodi contrassegnati come ' Secondary ' NODEROLE in [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

I valori supportati per questa enumerazione sono:

-   **Never:** (impostazione predefinita) tutte le letture avvengono sui nodi primari.
-   **sempre:** Le letture vengono eseguite sui nodi secondari e le istruzioni Insert/Update sono disabilitate.

#### <a name="cituscluster_name-text"></a>nome CITUS. cluster \_ (testo)

Informa il coordinatore node Planner del cluster che coordina. Una volta \_ impostato il nome del cluster, il pianificatore eseguirà una query sui nodi di lavoro nel cluster.

#### <a name="citusenable_version_checks-boolean"></a>controlli della versione di CITUS. enable \_ \_ (booleano)

Per l'aggiornamento della versione iperscalabile (CITUS) è necessario riavviare il server (per scegliere la nuova libreria condivisa), seguito dal comando ALTER EXTENSION UPDATE.  La mancata esecuzione di entrambi i passaggi potrebbe causare errori o arresti anomali.
Iperscale (CITUS) consente quindi di convalidare la versione del codice e quella della corrispondenza dell'estensione ed errori se non lo sono \' .

Per impostazione predefinita, questo valore è true ed è valido per il coordinatore. In rari casi, i processi di aggiornamento complessi possono richiedere l'impostazione di questo parametro su false, disabilitando in tal modo il controllo.

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>\_rilevamento deadlock distribuiti CITUS. log \_ \_ (booleano)

Indica se registrare l'elaborazione relativa al rilevamento di deadlock distribuiti nel log del server. Il valore predefinito è false.

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>CITUS. Distributed \_ deadlock \_ Detection \_ Factor (virgola mobile)

Imposta il tempo di attesa prima di verificare la presenza di deadlock distribuiti. In particolare, il tempo di attesa sarà questo valore moltiplicato per l' \' impostazione [del \_ timeout del deadlock](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) di PostgreSQL. Il valore predefinito è `2`. Il valore `-1` Disabilita il rilevamento del deadlock distribuito.

#### <a name="citusnode_connection_timeout-integer"></a>timeout connessione CITUS. Node \_ \_ (Integer)

`citus.node_connection_timeout`GUC imposta la durata massima (in millisecondi) di attesa per lo stabilimento della connessione. Iperscale (CITUS) genera un errore se il timeout scade prima che venga stabilita almeno una connessione di lavoro. Questo GUC influiscono sulle connessioni tra il coordinatore e i ruoli di lavoro.

-   Impostazione predefinita: cinque secondi
-   Minimo: 10 millisecondi
-   Massimo: un'ora

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>Statistiche query

#### <a name="citusstat_statements_purge_interval-integer"></a>CITUS. stat \_ - \_ Elimina \_ intervallo (Integer)

Imposta la frequenza con cui il daemon di manutenzione rimuove i record da [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) non corrispondenti in `pg_stat_statements` . Questo valore di configurazione imposta l'intervallo di tempo tra le eliminazioni in secondi, con un valore predefinito pari a 10. Il valore 0 Disabilita le eliminazioni.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Questo parametro è efficace sul coordinatore e può essere modificato in fase di esecuzione.

### <a name="data-loading"></a>Caricamento dati

#### <a name="citusmulti_shard_commit_protocol-enum"></a>CITUS. \_ multipartizione \_ commit \_ Protocol (enum)

Imposta il protocollo di commit da usare quando si esegue la copia in una tabella con distribuzione hash. In ogni singola posizione di partizionamento, la copia viene eseguita in un blocco di transazioni per garantire che non vengano inseriti dati se si verifica un errore durante la copia. Tuttavia, esiste un particolare caso di errore in cui la copia ha esito positivo in tutti i posizionamenti, ma si verifica un errore (hardware) prima del commit di tutte le transazioni. Questo parametro può essere utilizzato per evitare la perdita di dati, in questo caso, scegliendo tra i protocolli di commit seguenti:

-   **2pc:** (impostazione predefinita) le transazioni in cui viene eseguita la copia sulle parti della partizione vengono preparate per la prima volta utilizzando il \' [commit a due fasi](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) di PostgreSQL e quindi ne viene eseguito il commit. I commit non riusciti possono essere ripristinati manualmente o interrotti rispettivamente tramite COMMIT preparato o ROLLBACK preparato.
    Quando si usa 2pc, è necessario aumentare le [ \_ \_ transazioni preparate massime](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) in tutti i ruoli di lavoro, in genere con lo stesso valore delle connessioni massime \_ .
-   **1pc:** Il commit delle transazioni in cui viene eseguita la copia sui posizionamenti della partizione viene eseguito in un singolo round. Se un commit ha esito negativo, i dati potrebbero andare persi in caso di esito positivo della copia in tutte le località (rare).

#### <a name="citusshard_replication_factor-integer"></a>fattore di replica CITUS. partizione \_ \_ (Integer)

Imposta il fattore di replica per le partizioni, ovvero il numero di nodi in cui verranno inseriti i partizionamenti e il valore predefinito è 1. Questo parametro può essere impostato in fase di esecuzione e ha effetto sul coordinatore. Il valore ideale per questo parametro dipende dalle dimensioni del cluster e dalla frequenza di errore del nodo.  Ad esempio, è possibile aumentare questo fattore di replica se si eseguono cluster di grandi dimensioni e si osservano gli errori dei nodi su base più frequente.

#### <a name="citusshard_count-integer"></a>conteggio CITUS. partizionamento \_ (Integer)

Imposta il numero di partizioni per le tabelle con partizionamento hash e le impostazioni predefinite su 32.  Questo valore viene utilizzato dal [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF quando si creano tabelle con partizionamento hash. Questo parametro può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

#### <a name="citusshard_max_size-integer"></a>dimensioni massime di CITUS. partizionamento \_ \_ (Integer)

Imposta la dimensione massima consentita per la crescita di una partizione prima che venga divisa e il valore predefinito è 1 GB. Quando le dimensioni del file \' di origine (usato per la gestione temporanea) per una partizione superano questo valore di configurazione, il database garantisce che venga creata una nuova partizione. Questo parametro può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

### <a name="planner-configuration"></a>Configurazione di Planner

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>conteggio delle righe della clausola CITUS. Limit \_ \_ \_ \_ (Integer)

Imposta il numero di righe da recuperare per attività per l'ottimizzazione della clausola Limit.
In alcuni casi, può essere necessario recuperare tutte le righe di ogni attività per generare risultati in query SELECT con clausole Limit. In questi casi, e dove un'approssimazione produrrebbe risultati significativi, questo valore di configurazione imposta il numero di righe da recuperare da ogni partizione. Per impostazione predefinita, le approssimazioni di limite sono disabilitate e questo parametro è impostato su-1. Questo valore può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

#### <a name="cituscount_distinct_error_rate-floating-point"></a>CITUS. Count \_ - \_ frequenza degli errori Distinct \_ (virgola mobile)

Iperscale (CITUS) consente di calcolare le approssimazioni di conteggio (DISTINCT) usando l'estensione PostgreSQL-HLL. Questa voce di configurazione imposta la frequenza di errore desiderata durante il calcolo del conteggio (DISTINCT). 0,0, che è l'impostazione predefinita, Disabilita le approssimazioni per Count (DISTINCT); e 1,0 non fornisce alcuna garanzia sull'accuratezza dei risultati. Per ottenere risultati ottimali, è consigliabile impostare questo parametro su 0,005. Questo valore può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

#### <a name="citustask_assignment_policy-enum"></a>criteri di assegnazione CITUS. Task \_ \_ (enum)

> [!NOTE]
> Questo GUC è applicabile solo quando [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) è maggiore di uno oppure per le query eseguite su [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Imposta i criteri da utilizzare per l'assegnazione di attività ai ruoli di lavoro. Il coordinatore assegna le attività ai ruoli di lavoro in base alle posizioni della partizione. Questo valore di configurazione specifica i criteri da usare quando si effettuano queste assegnazioni.
Attualmente, è possibile utilizzare tre possibili criteri di assegnazione delle attività.

-   **greedy:** Il criterio greedy è l'impostazione predefinita e mira a distribuire uniformemente le attività tra i ruoli di lavoro.
-   **Round Robin:** Il criterio Round Robin assegna attività ai ruoli di lavoro in modo alternato tra repliche diverse. Questo criterio consente un migliore utilizzo del cluster quando il numero di partizioni per una tabella è ridotto rispetto al numero di ruoli di lavoro.
-   **prima replica:** Il criterio di prima replica Assegna attività sulla base dell'ordine di inserimento delle parti (repliche) per le partizioni. In altre parole, la query del frammento per una partizione viene assegnata al ruolo di lavoro che ha la prima replica di tale partizione.
    Questo metodo consente di avere garanzie sicure sulle partizioni che verranno usate per i nodi, ovvero garanzie di residenza di memoria più complesse.

Questo parametro può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

### <a name="intermediate-data-transfer"></a>Trasferimento dati intermedio

#### <a name="citusbinary_worker_copy_format-boolean"></a>formato di copia di lavoro CITUS. Binary \_ \_ \_ (booleano)

Utilizzare il formato di copia binaria per trasferire i dati intermedi tra i ruoli di lavoro.
Durante i join di tabelle di grandi dimensioni, l'iperscalabilità (CITUS) potrebbe dover ripartizionare dinamicamente i dati tra ruoli di lavoro diversi. Per impostazione predefinita, questi dati vengono trasferiti in formato testo. L'abilitazione di questo parametro indica al database di usare il formato di serializzazione binaria di PostgreSQL per trasferire questi dati. Questo parametro è efficace nei ruoli di lavoro e deve essere modificato nel file PostgreSQL. conf. Dopo aver modificato il file di configurazione, gli utenti possono inviare un segnale SIGHUP o riavviare il server per rendere effettive le modifiche.

#### <a name="citusbinary_master_copy_format-boolean"></a>formato di copia master CITUS. Binary \_ \_ \_ (booleano)

Utilizzare il formato di copia binaria per trasferire i dati tra coordinatore e i ruoli di lavoro. Quando si eseguono query distribuite, i thread di lavoro trasferiscono i risultati intermedi al coordinatore per l'aggregazione finale. Per impostazione predefinita, questi dati vengono trasferiti in formato testo. L'abilitazione di questo parametro indica al database di usare il formato di serializzazione binaria di PostgreSQL per trasferire questi dati.
Questo parametro può essere impostato in fase di esecuzione ed è efficace sul coordinatore.

#### <a name="citusmax_intermediate_result_size-integer"></a>\_dimensioni risultato intermedio CITUS. Max \_ \_ (Integer)

Dimensioni massime in KB di risultati intermedi per CTE che non possono essere inoltrate ai nodi di lavoro per l'esecuzione e per sottoquery complesse. Il valore predefinito è 1 GB e il valore-1 indica nessun limite.
Le query che superano il limite verranno annullate e generano un messaggio di errore.

### <a name="ddl"></a>DDL

#### <a name="citusenable_ddl_propagation-boolean"></a>CITUS. enable \_ DDL \_ Propagation (booleano)

Specifica se propagare automaticamente le modifiche DDL dal coordinatore a tutti i ruoli di lavoro. Il valore predefinito è true. Poiché alcune modifiche dello schema richiedono un blocco di accesso esclusivo sulle tabelle e, poiché la propagazione automatica viene applicata a tutti i ruoli di lavoro in sequenza, è possibile che un cluster iperscalabile (CITUS) risulti temporaneamente meno reattivo. È possibile scegliere di disabilitare questa impostazione e propagare manualmente le modifiche.

### <a name="executor-configuration"></a>Configurazione Executor

#### <a name="general"></a>Generale

##### <a name="citusall_modifications_commutative"></a>CITUS. tutte le \_ modifiche \_ commutative

Iperscale (CITUS) impone regole commutatività e acquisisce i blocchi appropriati per le operazioni di modifica allo scopo di garantire la correttezza del comportamento. Si presuppone, ad esempio, che un'istruzione INSERT venga commutata con un'altra istruzione INSERT, ma non con un'istruzione UPDATE o DELETE. In modo analogo, si presuppone che un'istruzione UPDATE o DELETE non venga commutata con un'altra istruzione UPDATE o DELETE. Questa precauzione significa che gli aggiornamenti e le eliminazioni richiedono iperscalabilità (CITUS) per acquisire blocchi più avanzati.

Se si dispone di istruzioni UPDATE commutative con gli inserimenti o altri aggiornamenti, è possibile rilassare questi presupposti di commutatività impostando questo parametro su true. Quando questo parametro è impostato su true, tutti i comandi sono considerati commutativi e richiedono un blocco condiviso, che può migliorare la velocità effettiva complessiva. Questo parametro può essere impostato in fase di esecuzione ed è efficace sul coordinatore.

##### <a name="citusremote_task_check_interval-integer"></a>intervallo di controllo attività CITUS. Remote \_ \_ \_ (Integer)

Imposta la frequenza con cui iperscale (CITUS) verifica lo stato dei processi gestiti dall'esecutore dell'attività di rilevamento attività. Il valore predefinito è 10 ms. Il coordinatore assegna le attività ai ruoli di lavoro e quindi verifica periodicamente l'esecuzione di ogni attività \' . Questo valore di configurazione imposta l'intervallo di tempo tra due controlli successivi. Questo parametro è efficace sul coordinatore e può essere impostato in fase di esecuzione.

##### <a name="citustask_executor_type-enum"></a>tipo Executor CITUS. Task \_ \_ (enum)

Iperscale (CITUS) dispone di tre tipi Executor per l'esecuzione di query SELECT distribuite.  L'Executor desiderato può essere selezionato impostando questo parametro di configurazione. I valori accettati per questo parametro sono:

-   **adattivo:** Valore predefinito. Si tratta di una soluzione ottimale per risposte rapide alle query che coinvolgono aggregazioni e join con condivisione percorso tra più partizioni.
-   **Task-Tracker:** L'executor di Task-Tracker è particolarmente adatto per query complesse e a esecuzione prolungata che richiedono il shuffling dei dati tra i nodi di lavoro e una gestione efficiente delle risorse.
-   in **tempo reale:** (deprecato) ha uno scopo simile a quello dell'executor adattivo, ma è meno flessibile e può causare un maggior numero di richieste di connessione nei nodi del ruolo di lavoro.

Questo parametro può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>livello di log delle query CITUS. \_ multitask \_ \_ \_ (enum) {#multi_task_logging}

Imposta un livello di log per qualsiasi query che genera più di un'attività, ovvero che raggiunge più di una partizione. La registrazione è utile durante la migrazione di un'applicazione multi-tenant, in quanto è possibile scegliere di generare un errore o di avvisare tali query per trovarle e aggiungervi un \_ filtro ID tenant. Questo parametro può essere impostato in fase di esecuzione ed è efficace sul coordinatore. Il valore predefinito per questo parametro è \' disattivato \' .

I valori supportati per questa enumerazione sono:

-   **disattivato:** Disattivare la registrazione di tutte le query che generano più attività, ovvero si estendono su più partizioni.
-   **debug:** Registra un'istruzione a livello di gravità del DEBUG.
-   **log:** Registra un'istruzione a livello di gravità del LOG. La riga di log includerà la query SQL eseguita.
-   **Avviso:** Registra l'istruzione a livello di gravità dell'avviso.
-   **Avviso:** Registra un'istruzione a livello di gravità dell'avviso.
-   **errore:** Registra un'istruzione a livello di gravità dell'errore.

Può essere utile usare durante i `error` test di sviluppo e un livello di log inferiore, ad esempio `log` durante la distribuzione effettiva di produzione.
`log`Se si sceglie, le query con più attività verranno visualizzate nei log del database con la query stessa visualizzata dopo l' \" istruzione.\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>CITUS. enable \_ repartition \_ joins (booleano)

In genere, il tentativo di eseguire i join di ripartizionamento con l'executor adattivo avrà esito negativo con un messaggio di errore.  Tuttavia, `citus.enable_repartition_joins` l'impostazione di su true consente a iperscale (CITUS) di passare temporaneamente all'executor di Task-Tracker per eseguire il join.  Il valore predefinito è false.

#### <a name="task-tracker-executor-configuration"></a>Configurazione executor di Task Tracker

##### <a name="citustask_tracker_delay-integer"></a>ritardo di rilevamento CITUS. Task \_ \_ (Integer)

Questo parametro imposta il tempo di sospensione della traccia attività tra i cicli di gestione delle attività e i valori predefiniti di 200 ms. Il processo di rilevamento attività viene riattivato regolarmente, analizza tutte le attività assegnate e pianifica ed esegue queste attività.  Quindi, lo strumento di rilevamento delle attività dorme per un periodo di tempo prima di ripassare le attività.
Questo valore di configurazione determina la lunghezza del periodo di sospensione. Questo parametro è efficace nei ruoli di lavoro e deve essere modificato nel file PostgreSQL. conf. Dopo aver modificato il file di configurazione, gli utenti possono inviare un segnale SIGHUP o riavviare il server per rendere effettive le modifiche.

Questo parametro può essere ridotto per ridurre il ritardo causato dall'esecutore del rilevamento attività riducendo il gap di tempo tra i cicli di gestione.
La riduzione del ritardo è utile nei casi in cui le query di partizionamento sono brevi e quindi aggiornano regolarmente il proprio stato.

##### <a name="citusmax_assign_task_batch_size-integer"></a>CITUS. Max \_ assegna \_ \_ dimensioni batch attività \_ (Integer)

L'esecutore di attività Tracker sul coordinatore assegna in modo sincrono le attività in batch al daemon nei ruoli di lavoro. Questo parametro imposta il numero massimo di attività da assegnare in un singolo batch. La scelta di una dimensione batch più ampia consente un'assegnazione di attività più rapida. Tuttavia, se il numero di ruoli di lavoro è elevato, potrebbe essere necessario più tempo per tutti i thread di lavoro.  Questo parametro può essere impostato in fase di esecuzione ed è efficace sul coordinatore.

##### <a name="citusmax_running_tasks_per_node-integer"></a>\_attività in esecuzione CITUS. Max \_ \_ per \_ nodo (Integer)

Il processo di rilevamento delle attività pianifica ed esegue le attività assegnate in base alle esigenze. Questo valore di configurazione consente di impostare il numero massimo di attività da eseguire contemporaneamente in un nodo in un determinato momento e il valore predefinito è 8.

Il limite garantisce che non vi \' siano molte attività che raggiungono il disco allo stesso tempo e contribuiscano a evitare i conflitti di I/O su disco. Se le query vengono gestite dalla memoria o dalle unità SSD, è possibile aumentare il numero massimo di \_ attività in esecuzione \_ \_ per \_ nodo senza molta preoccupazione.

##### <a name="cituspartition_buffer_size-integer"></a>dimensioni del buffer CITUS. partition \_ \_ (Integer)

Imposta la dimensione del buffer da utilizzare per le operazioni di partizione e il valore predefinito è 8 MB.
Iperscale (CITUS) consente di ripartizionare i dati della tabella in più file quando vengono unite in join due tabelle di grandi dimensioni. Quando il buffer di partizione si riempie, i dati ripartizionati vengono scaricati in file su disco.  Questa voce di configurazione può essere impostata in fase di esecuzione ed è valida per i ruoli di lavoro.

#### <a name="explain-output"></a>Output spiegazione

##### <a name="citusexplain_all_tasks-boolean"></a>CITUS. explain \_ All \_ Tasks (valore booleano)

Per impostazione predefinita, iperscale (CITUS) Mostra l'output di una singola attività arbitraria quando si esegue [explain](http://www.postgresql.org/docs/current/static/sql-explain.html) su una query distribuita. Nella maggior parte dei casi, l'output di explain sarà simile tra le attività. In alcuni casi alcune attività verranno pianificate in modo diverso o avranno tempi di esecuzione molto più elevati. In questi casi, può essere utile abilitare questo parametro, dopo il quale l'output di EXPLAIN includerà tutte le attività. La spiegazione di tutte le attività potrebbe richiedere più tempo per la spiegazione.

## <a name="postgresql-parameters"></a>Parametri di PostgreSQL

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) -imposta il formato di visualizzazione per i valori di data e ora
* [IntervalStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) -imposta il formato di visualizzazione per i valori di intervallo
* [TimeZone](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) -imposta il fuso orario per la visualizzazione e l'interpretazione dei timestamp
* [application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) : imposta il nome dell'applicazione da segnalare in statistiche e log
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) -Abilita l'input di elementi null nelle matrici
* [autovacuum](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) : avvia il sottoprocesso autovacuum
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) : numero di inserimenti, aggiornamenti o eliminazioni di Tuple prima dell'analisi come frazione di reltuples
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) : numero minimo di inserimenti, aggiornamenti o eliminazioni di Tuple prima dell'analisi
* tempo di [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) per la sospensione tra le esecuzioni di autovacuum
* ritardo dei costi [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) -Vacuum, in millisecondi, per il sottovuoto
* quantità di costi [autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) -Vacuum disponibile prima del napping, per autovacuum
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) : numero di aggiornamenti o eliminazioni di Tuple precedenti a Vacuum come frazione di reltuples
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) : numero minimo di aggiornamenti o eliminazioni di Tuple prima del vuoto
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) : imposta la quantità massima di memoria che deve essere usata da ogni processo di lavoro di autovacuum
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) : numero di pagine dopo le quali le scritture eseguite in precedenza vengono scaricate su disco
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) -imposta se " \' " è consentito nei valori letterali stringa
* tempo di sospensione del writer in background [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) tra i cicli
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) : numero di pagine dopo le quali le scritture eseguite in precedenza vengono scaricate su disco
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) -numero massimo di pagine LRU da scaricare in background writer per round
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) -multiplo dell'utilizzo medio del buffer per l'utilizzo gratuito per round
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) -imposta il formato di output per bytea
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) : controlla i corpi della funzione durante la creazione della funzione
* tempo di [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) dedicato allo scaricamento di buffer dirty durante il checkpoint, come frazione dell'intervallo del checkpoint
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) : imposta il tempo massimo tra i checkpoint di Wal automatici
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) : Abilita gli avvisi se i segmenti di checkpoint vengono riempiti più di frequente rispetto a questo
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) -imposta la codifica del set di caratteri del client
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) -imposta i livelli dei messaggi inviati al client
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) : imposta il ritardo in microsecondi tra il commit della transazione e lo scaricamento di Wal sul disco
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) -imposta le transazioni di apertura simultanee minime prima di eseguire commit_delay
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) : consente al pianificatore di usare vincoli per ottimizzare le query
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) -imposta la stima del pianificatore del costo di elaborazione di ogni voce di indice durante un'analisi dell'indice
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) -imposta la stima del pianificatore del costo di elaborazione di ogni operatore o chiamata di funzione
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) -imposta la stima del pianificatore del costo di elaborazione di ogni tupla (riga)
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) -imposta la stima del pianificatore della frazione di righe di un cursore che verrà recuperato
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) : imposta la quantità di tempo, in millisecondi, di attesa di un blocco prima di verificare il deadlock
* visualizzazione dell'albero del piano e dell'analisi con rientri [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3)
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -registra l'albero di analisi di ogni query
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) : registra il piano di esecuzione di ogni query
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) : registra l'albero di analisi riscritto di ogni query
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) -imposta la destinazione statistica predefinita
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) -imposta lo spazio di tabella predefinito per creare tabelle e indici in
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) -imposta la configurazione della ricerca di testo predefinita
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) -imposta lo stato rinviabile predefinito delle nuove transazioni
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) -imposta il livello di isolamento delle transazioni di ogni nuova transazione
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) -imposta lo stato di sola lettura predefinito per le nuove transazioni
* default_with_oids-crea nuove tabelle con OID per impostazione predefinita
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) : imposta il presupposto di Planner sulle dimensioni della cache del disco
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) -Abilita l'uso dei piani di analisi bitmap da parte di Planner
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) -Abilita l'uso del pianificatore per la raccolta dei piani di merge
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) -Abilita l'uso dei piani di aggregazione con hash da parte di Planner
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) -Abilita l'uso dei piani hash join da parte di Planner
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) -Abilita l'uso da parte di planner dei piani di analisi solo degli indici
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) -Abilita l'uso dei piani di analisi degli indici da parte di Planner
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) -Abilita l'uso della materializzazione da parte di Planner
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) -Abilita l'uso dei piani di merge join da parte di Planner
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) -Abilita l'uso dei piani di join a cicli annidati da parte di Planner
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) -Abilita l'uso dei piani di analisi sequenziale da parte di Planner
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) -Abilita l'uso dei passaggi di ordinamento espliciti da parte di Planner
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) -Abilita l'uso dei piani di analisi TID da parte di Planner
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) : avvisa in merito a caratteri di escape barra rovesciata nei valori letterali stringa ordinari
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) -termina la sessione in caso di errore
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) -imposta il numero di cifre visualizzate per i valori a virgola mobile
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) forza l'uso di funzionalità di query parallele
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) -imposta le dimensioni da elenco oltre le sottoquery non compresse
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) -Abilita l'ottimizzazione delle query genetiche
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) -geqo: effort viene usato per impostare il valore predefinito per altri parametri di geqo
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) -geqo: numero di iterazioni dell'algoritmo
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) -geqo: numero di utenti nella popolazione
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) -geqo: valore di inizializzazione per la selezione di un percorso casuale
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) -geqo: pressione selettiva nella popolazione
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) : imposta la soglia di da elementi oltre i quali viene usato geqo
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) : imposta il risultato massimo consentito per la ricerca esatta per Gin
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) -imposta la dimensione massima dell'elenco in sospeso per l'indice Gin
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) -imposta la durata massima consentita di tutte le transazioni al minimo
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) : imposta la dimensione dall'elenco oltre la quale i costrutti di join non sono bidimensionali
* [LC_MONETARY](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) -imposta le impostazioni locali per la formattazione degli importi monetari
* [LC_NUMERIC](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) -imposta le impostazioni locali per la formattazione dei numeri
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) -Abilita la modalità di compatibilità con le versioni precedenti per i controlli dei privilegi sugli oggetti grandi
* [LOCK_TIMEOUT](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) : imposta la durata massima consentita (in millisecondi) di attesa di un blocco. 0 disattiva questa opzione
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) -imposta il tempo di esecuzione minimo oltre il quale verranno registrate le azioni di autovacuum
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) : registra ogni checkpoint
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) : registra ogni connessione riuscita
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) -imposta la destinazione per l'output del log del server
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) -registra la fine di una sessione, inclusa la durata
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) : registra la durata di ogni istruzione SQL completata
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) -imposta il livello di dettaglio dei messaggi registrati
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) log attese di blocco lungo
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) : consente di impostare il tempo di esecuzione minimo (in millisecondi) oltre il quale le istruzioni verranno registrate. -1 Disabilita le durate dell'istruzione di registrazione
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) -causa la registrazione di tutte le istruzioni che generano un errore al livello superiore o superiore a questo livello
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) -imposta i livelli dei messaggi registrati
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) : registra ogni comando di replica
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) -imposta il tipo di istruzioni registrate
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) : per ogni query, scrive le statistiche sulle prestazioni cumulative nel log del server
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) : registra l'uso di file temporanei di dimensioni superiori a questo numero di kilobyte
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) -imposta la quantità massima di memoria da usare per le operazioni di manutenzione
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) : imposta il numero massimo di thread di lavoro paralleli che possono essere attivi alla volta
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) : imposta il numero massimo di processi paralleli per ogni nodo Executor
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) : imposta il numero massimo di Tuple bloccate in base al predicato per pagina
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) : imposta il numero massimo di pagine e Tuple bloccate dal predicato per relazione
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) : imposta il ritardo massimo prima di annullare le query quando un server di hot standby elabora dati Wal archiviati
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) : imposta il ritardo massimo prima di annullare le query quando un server di hot standby elabora i dati di Wal in flusso
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) : numero massimo di thread di lavoro per la sincronizzazione delle tabelle per sottoscrizione
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) -imposta le dimensioni di Wal che attivano un checkpoint
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) -imposta la quantità minima di dati dell'indice per un'analisi parallela
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) -imposta le dimensioni minime per COMPATTAre Wal
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) : genera un avviso per i costrutti che hanno modificato il significato rispetto a PostgreSQL 9,4
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) : imposta la stima del costo di avvio dei processi di lavoro per la query parallela
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) : imposta la stima del piano di pianificazione del costo del passaggio di ogni tupla (riga) dal back-end del ruolo di lavoro a quello master
* [pg_stat_statements. Save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) : Salva pg_stat_statements statistiche tra le chiusure del server
* [pg_stat_statements. Track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) : consente di selezionare le istruzioni che vengono rilevate da pg_stat_statements
* [pg_stat_statements. track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) : consente di selezionare se i comandi dell'utilità vengono rilevati da pg_stat_statements
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) : durante la generazione di frammenti SQL, vengono citati tutti gli identificatori
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) : imposta la stima del costo di una pagina del disco recuperata in modo non sequenziale
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) -Abilita la sicurezza delle righe
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) -imposta l'ordine di ricerca dello schema per i nomi non qualificati per lo schema
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) : imposta la stima del costo di una pagina del disco recuperata in sequenza
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) -imposta il comportamento della sessione per i trigger e le regole di riscrittura
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) causa '.. .' stringhe per trattare letteralmente le barre rovesciate
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) : imposta la durata massima consentita (in millisecondi) di qualsiasi istruzione. 0 disattiva questa opzione
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) -Abilita le analisi sequenziali sincronizzate
* [SYNCHRONOUS_COMMIT](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) -imposta il livello di sincronizzazione della transazione corrente
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) -numero massimo di ritrasmissioni TCP keepalive
* tempo di [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) tra l'emissione di keep-alive TCP
* [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) -Time tra le ritrasmissioni TCP keepalive
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) : imposta il numero massimo di buffer temporanei utilizzati da ogni sessione del database
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) -imposta gli spazi di tabella da utilizzare per le tabelle temporanee e i file di ordinamento
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) : raccoglie informazioni sull'esecuzione di comandi
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) -raccoglie statistiche sull'attività del database
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) -raccoglie le statistiche a livello di funzione sull'attività del database
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) -raccoglie le statistiche temporali per l'attività di I/O del database
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) -considera "expr = null" come "expr is null"
* ritardo dei costi [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) -Vacuum in millisecondi
* quantità di costi [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) -Vacuum disponibile prima del napping
* costo [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) -vacuum per una pagina sporcata da Vacuum
* costo [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) -vacuum per una pagina trovata nella cache buffer
* [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) -il costo vuoto per una pagina non è stato trovato nella cache buffer
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) : numero di transazioni per cui è necessario rinviare il vuoto e la pulizia a caldo, se presente
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) -durata minima in cui il vuoto deve bloccare una riga di tabella
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) -Age in cui Vacuum deve eseguire l'analisi dell'intera tabella per bloccare le tuple
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) : età minima a cui Vacuum deve bloccare un MultiXactId in una riga di tabella
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) -multixact età a cui Vacuum deve eseguire l'analisi dell'intera tabella per bloccare le tuple
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) : imposta l'intervallo massimo tra i rapporti di stato del ricevitore Wal e il database primario
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) tempo tra gli scaricamenti di Wal eseguiti nel writer Wal
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) -quantità di Wal scritta dal writer Wal che attiva uno scaricamento
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) : imposta la quantità di memoria che deve essere utilizzata dalle operazioni di ordinamento interno e dalle tabelle hash prima della scrittura nei file su disco temporanei
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) -imposta il modo in cui i valori binari devono essere codificati in XML
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) : specifica se i dati XML nelle operazioni di analisi e serializzazione implicita devono essere considerati come documenti o frammenti di contenuto

## <a name="next-steps"></a>Passaggi successivi

* Un altro tipo di configurazione, oltre ai parametri del server, è costituito dalle [Opzioni di configurazione](concepts-hyperscale-configuration-options.md) delle risorse in un gruppo di server con iperscalabilità (CITUS).
* Il database PostgreSQL sottostante dispone anche di [parametri di configurazione](http://www.postgresql.org/docs/current/static/runtime-config.html).
