---
title: Parametri del server – iperscala (iperscala (CITUS)-database di Azure per PostgreSQL
description: Parametri nell'API SQL con iperscalabilità (CITUS)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136837"
---
# <a name="server-parameters"></a>Parametri del server

Sono disponibili vari parametri del server che influiscono sul comportamento di iperscala (CITUS), su entrambi i parametri di PostgreSQL standard e sui parametri specifici di iperscale (CITUS). Per altre informazioni sui parametri di configurazione di PostgreSQL, vedere la sezione relativa alla configurazione della fase di [esecuzione](http://www.postgresql.org/docs/current/static/runtime-config.html) della documentazione di PostgreSQL.

Il resto di questo riferimento mira a discutere i parametri di configurazione specifici di iperscala (CITUS). Questi parametri possono essere impostati nella portale di Azure in **parametri del nodo di lavoro** in **Impostazioni** per un gruppo di server di iperscala (CITUS).

> [!NOTE]
>
> I gruppi di server iperscalare che eseguono versioni precedenti del motore CITUS potrebbero non offrire tutti i parametri elencati di seguito.

## <a name="general-configuration"></a>Configurazione generale

### <a name="citususe_secondary_nodes-enum"></a>CITUS. USA \_ \_ nodi secondari (enum)

Imposta i criteri da usare quando si scelgono i nodi per le query di selezione. Se è impostato su' always ', il pianificatore eseguirà una query solo sui nodi contrassegnati come ' Secondary ' NODEROLE in [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

I valori supportati per questa enumerazione sono:

-   **Never:** (impostazione predefinita) tutte le letture avvengono sui nodi primari.
-   **sempre:** Le letture vengono eseguite sui nodi secondari e le istruzioni Insert/Update sono disabilitate.

### <a name="cituscluster_name-text"></a>nome CITUS. cluster \_ (testo)

Informa il coordinatore node Planner del cluster che coordina. Una volta \_ impostato il nome del cluster, il pianificatore eseguirà una query sui nodi di lavoro nel cluster.

### <a name="citusenable_version_checks-boolean"></a>controlli della versione di CITUS. enable \_ \_ (booleano)

Per l'aggiornamento della versione iperscalabile (CITUS) è necessario riavviare il server (per scegliere la nuova libreria condivisa), seguito dal comando ALTER EXTENSION UPDATE.  La mancata esecuzione di entrambi i passaggi potrebbe causare errori o arresti anomali.
Iperscale (CITUS) consente quindi di convalidare la versione del codice e quella della corrispondenza dell'estensione ed errori se non lo sono \' .

Per impostazione predefinita, questo valore è true ed è valido per il coordinatore. In rari casi, i processi di aggiornamento complessi possono richiedere l'impostazione di questo parametro su false, disabilitando in tal modo il controllo.

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>\_rilevamento deadlock distribuiti CITUS. log \_ \_ (booleano)

Indica se registrare l'elaborazione relativa al rilevamento di deadlock distribuiti nel log del server. Il valore predefinito è false.

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>CITUS. Distributed \_ deadlock \_ Detection \_ Factor (virgola mobile)

Imposta il tempo di attesa prima di verificare la presenza di deadlock distribuiti. In particolare, il tempo di attesa sarà questo valore moltiplicato per l' \' impostazione [del \_ timeout del deadlock](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) di PostgreSQL. Il valore predefinito è `2`. Il valore `-1` Disabilita il rilevamento del deadlock distribuito.

### <a name="citusnode_connection_timeout-integer"></a>timeout connessione CITUS. Node \_ \_ (Integer)

`citus.node_connection_timeout`GUC imposta la durata massima (in millisecondi) di attesa per lo stabilimento della connessione. Iperscale (CITUS) genera un errore se il timeout scade prima che venga stabilita almeno una connessione di lavoro. Questo GUC influiscono sulle connessioni tra il coordinatore e i ruoli di lavoro.

-   Impostazione predefinita: cinque secondi
-   Minimo: 10 millisecondi
-   Massimo: un'ora

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>Statistiche query

### <a name="citusstat_statements_purge_interval-integer"></a>CITUS. stat \_ - \_ Elimina \_ intervallo (Integer)

Imposta la frequenza con cui il daemon di manutenzione rimuove i record da [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) non corrispondenti in `pg_stat_statements` . Questo valore di configurazione imposta l'intervallo di tempo tra le eliminazioni in secondi, con un valore predefinito pari a 10. Il valore 0 Disabilita le eliminazioni.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Questo parametro è efficace sul coordinatore e può essere modificato in fase di esecuzione.

## <a name="data-loading"></a>Caricamento dati

### <a name="citusmulti_shard_commit_protocol-enum"></a>CITUS. \_ multipartizione \_ commit \_ Protocol (enum)

Imposta il protocollo di commit da usare quando si esegue la copia in una tabella con distribuzione hash. In ogni singola posizione di partizionamento, la copia viene eseguita in un blocco di transazioni per garantire che non vengano inseriti dati se si verifica un errore durante la copia. Tuttavia, esiste un particolare caso di errore in cui la copia ha esito positivo in tutti i posizionamenti, ma si verifica un errore (hardware) prima del commit di tutte le transazioni. Questo parametro può essere utilizzato per evitare la perdita di dati, in questo caso, scegliendo tra i protocolli di commit seguenti:

-   **2pc:** (impostazione predefinita) le transazioni in cui viene eseguita la copia sulle parti della partizione vengono preparate per la prima volta utilizzando il \' [commit a due fasi](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) di PostgreSQL e quindi ne viene eseguito il commit. I commit non riusciti possono essere ripristinati manualmente o interrotti rispettivamente tramite COMMIT preparato o ROLLBACK preparato.
    Quando si usa 2pc, è necessario aumentare le [ \_ \_ transazioni preparate massime](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) in tutti i ruoli di lavoro, in genere con lo stesso valore delle connessioni massime \_ .
-   **1pc:** Il commit delle transazioni in cui viene eseguita la copia sui posizionamenti della partizione viene eseguito in un singolo round. Se un commit ha esito negativo, i dati potrebbero andare persi in caso di esito positivo della copia in tutte le località (rare).

### <a name="citusshard_replication_factor-integer"></a>fattore di replica CITUS. partizione \_ \_ (Integer)

Imposta il fattore di replica per le partizioni, ovvero il numero di nodi in cui verranno inseriti i partizionamenti e il valore predefinito è 1. Questo parametro può essere impostato in fase di esecuzione e ha effetto sul coordinatore. Il valore ideale per questo parametro dipende dalle dimensioni del cluster e dalla frequenza di errore del nodo.  Ad esempio, è possibile aumentare questo fattore di replica se si eseguono cluster di grandi dimensioni e si osservano gli errori dei nodi su base più frequente.

### <a name="citusshard_count-integer"></a>conteggio CITUS. partizionamento \_ (Integer)

Imposta il numero di partizioni per le tabelle con partizionamento hash e le impostazioni predefinite su 32.  Questo valore viene utilizzato dal [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF quando si creano tabelle con partizionamento hash. Questo parametro può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

### <a name="citusshard_max_size-integer"></a>dimensioni massime di CITUS. partizionamento \_ \_ (Integer)

Imposta la dimensione massima consentita per la crescita di una partizione prima che venga divisa e il valore predefinito è 1 GB. Quando le dimensioni del file \' di origine (usato per la gestione temporanea) per una partizione superano questo valore di configurazione, il database garantisce che venga creata una nuova partizione. Questo parametro può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

## <a name="planner-configuration"></a>Configurazione di Planner

### <a name="cituslimit_clause_row_fetch_count-integer"></a>conteggio delle righe della clausola CITUS. Limit \_ \_ \_ \_ (Integer)

Imposta il numero di righe da recuperare per attività per l'ottimizzazione della clausola Limit.
In alcuni casi, può essere necessario recuperare tutte le righe di ogni attività per generare risultati in query SELECT con clausole Limit. In questi casi, e dove un'approssimazione produrrebbe risultati significativi, questo valore di configurazione imposta il numero di righe da recuperare da ogni partizione. Per impostazione predefinita, le approssimazioni di limite sono disabilitate e questo parametro è impostato su-1. Questo valore può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

### <a name="cituscount_distinct_error_rate-floating-point"></a>CITUS. Count \_ - \_ frequenza degli errori Distinct \_ (virgola mobile)

Iperscale (CITUS) consente di calcolare le approssimazioni di conteggio (DISTINCT) usando l'estensione PostgreSQL-HLL. Questa voce di configurazione imposta la frequenza di errore desiderata durante il calcolo del conteggio (DISTINCT). 0,0, che è l'impostazione predefinita, Disabilita le approssimazioni per Count (DISTINCT); e 1,0 non fornisce alcuna garanzia sull'accuratezza dei risultati. Per ottenere risultati ottimali, è consigliabile impostare questo parametro su 0,005. Questo valore può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

### <a name="citustask_assignment_policy-enum"></a>criteri di assegnazione CITUS. Task \_ \_ (enum)

> [!NOTE]
> Questo GUC è applicabile solo quando [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) è maggiore di uno oppure per le query eseguite su [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Imposta i criteri da utilizzare per l'assegnazione di attività ai ruoli di lavoro. Il coordinatore assegna le attività ai ruoli di lavoro in base alle posizioni della partizione. Questo valore di configurazione specifica i criteri da usare quando si effettuano queste assegnazioni.
Attualmente, è possibile utilizzare tre possibili criteri di assegnazione delle attività.

-   **greedy:** Il criterio greedy è l'impostazione predefinita e mira a distribuire uniformemente le attività tra i ruoli di lavoro.
-   **Round Robin:** Il criterio Round Robin assegna attività ai ruoli di lavoro in modo alternato tra repliche diverse. Questo criterio consente un migliore utilizzo del cluster quando il numero di partizioni per una tabella è ridotto rispetto al numero di ruoli di lavoro.
-   **prima replica:** Il criterio di prima replica Assegna attività sulla base dell'ordine di inserimento delle parti (repliche) per le partizioni. In altre parole, la query del frammento per una partizione viene assegnata al ruolo di lavoro che ha la prima replica di tale partizione.
    Questo metodo consente di avere garanzie sicure sulle partizioni che verranno usate per i nodi, ovvero garanzie di residenza di memoria più complesse.

Questo parametro può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

## <a name="intermediate-data-transfer"></a>Trasferimento dati intermedio

### <a name="citusbinary_worker_copy_format-boolean"></a>formato di copia di lavoro CITUS. Binary \_ \_ \_ (booleano)

Utilizzare il formato di copia binaria per trasferire i dati intermedi tra i ruoli di lavoro.
Durante i join di tabelle di grandi dimensioni, l'iperscalabilità (CITUS) potrebbe dover ripartizionare dinamicamente i dati tra ruoli di lavoro diversi. Per impostazione predefinita, questi dati vengono trasferiti in formato testo. L'abilitazione di questo parametro indica al database di usare il formato di serializzazione binaria di PostgreSQL per trasferire questi dati. Questo parametro è efficace nei ruoli di lavoro e deve essere modificato nel file PostgreSQL. conf. Dopo aver modificato il file di configurazione, gli utenti possono inviare un segnale SIGHUP o riavviare il server per rendere effettive le modifiche.

### <a name="citusbinary_master_copy_format-boolean"></a>formato di copia master CITUS. Binary \_ \_ \_ (booleano)

Utilizzare il formato di copia binaria per trasferire i dati tra coordinatore e i ruoli di lavoro. Quando si eseguono query distribuite, i thread di lavoro trasferiscono i risultati intermedi al coordinatore per l'aggregazione finale. Per impostazione predefinita, questi dati vengono trasferiti in formato testo. L'abilitazione di questo parametro indica al database di usare il formato di serializzazione binaria di PostgreSQL per trasferire questi dati.
Questo parametro può essere impostato in fase di esecuzione ed è efficace sul coordinatore.

### <a name="citusmax_intermediate_result_size-integer"></a>\_dimensioni risultato intermedio CITUS. Max \_ \_ (Integer)

Dimensioni massime in KB di risultati intermedi per CTE che non possono essere inoltrate ai nodi di lavoro per l'esecuzione e per sottoquery complesse. Il valore predefinito è 1 GB e il valore-1 indica nessun limite.
Le query che superano il limite verranno annullate e generano un messaggio di errore.

## <a name="ddl"></a>DDL

### <a name="citusenable_ddl_propagation-boolean"></a>CITUS. enable \_ DDL \_ Propagation (booleano)

Specifica se propagare automaticamente le modifiche DDL dal coordinatore a tutti i ruoli di lavoro. Il valore predefinito è true. Poiché alcune modifiche dello schema richiedono un blocco di accesso esclusivo sulle tabelle e, poiché la propagazione automatica viene applicata a tutti i ruoli di lavoro in sequenza, è possibile che un cluster iperscalabile (CITUS) risulti temporaneamente meno reattivo. È possibile scegliere di disabilitare questa impostazione e propagare manualmente le modifiche.

## <a name="executor-configuration"></a>Configurazione Executor

### <a name="general"></a>Generale

#### <a name="citusall_modifications_commutative"></a>CITUS. tutte le \_ modifiche \_ commutative

Iperscale (CITUS) impone regole commutatività e acquisisce i blocchi appropriati per le operazioni di modifica allo scopo di garantire la correttezza del comportamento. Si presuppone, ad esempio, che un'istruzione INSERT venga commutata con un'altra istruzione INSERT, ma non con un'istruzione UPDATE o DELETE. In modo analogo, si presuppone che un'istruzione UPDATE o DELETE non venga commutata con un'altra istruzione UPDATE o DELETE. Questa precauzione significa che gli aggiornamenti e le eliminazioni richiedono iperscalabilità (CITUS) per acquisire blocchi più avanzati.

Se si dispone di istruzioni UPDATE commutative con gli inserimenti o altri aggiornamenti, è possibile rilassare questi presupposti di commutatività impostando questo parametro su true. Quando questo parametro è impostato su true, tutti i comandi sono considerati commutativi e richiedono un blocco condiviso, che può migliorare la velocità effettiva complessiva. Questo parametro può essere impostato in fase di esecuzione ed è efficace sul coordinatore.

#### <a name="citusremote_task_check_interval-integer"></a>intervallo di controllo attività CITUS. Remote \_ \_ \_ (Integer)

Imposta la frequenza con cui iperscale (CITUS) verifica lo stato dei processi gestiti dall'esecutore dell'attività di rilevamento attività. Il valore predefinito è 10 ms. Il coordinatore assegna le attività ai ruoli di lavoro e quindi verifica periodicamente l'esecuzione di ogni attività \' . Questo valore di configurazione imposta l'intervallo di tempo tra due controlli successivi. Questo parametro è efficace sul coordinatore e può essere impostato in fase di esecuzione.

#### <a name="citustask_executor_type-enum"></a>tipo Executor CITUS. Task \_ \_ (enum)

Iperscale (CITUS) dispone di tre tipi Executor per l'esecuzione di query SELECT distribuite.  L'Executor desiderato può essere selezionato impostando questo parametro di configurazione. I valori accettati per questo parametro sono:

-   **adattivo:** Valore predefinito. Si tratta di una soluzione ottimale per risposte rapide alle query che coinvolgono aggregazioni e join con condivisione percorso tra più partizioni.
-   **Task-Tracker:** L'executor di Task-Tracker è particolarmente adatto per query complesse e a esecuzione prolungata che richiedono il shuffling dei dati tra i nodi di lavoro e una gestione efficiente delle risorse.
-   in **tempo reale:** (deprecato) ha uno scopo simile a quello dell'executor adattivo, ma è meno flessibile e può causare un maggior numero di richieste di connessione nei nodi del ruolo di lavoro.

Questo parametro può essere impostato in fase di esecuzione e ha effetto sul coordinatore.

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>livello di log delle query CITUS. \_ multitask \_ \_ \_ (enum) {#multi_task_logging}

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

#### <a name="citusenable_repartition_joins-boolean"></a>CITUS. enable \_ repartition \_ joins (booleano)

In genere, il tentativo di eseguire i join di ripartizionamento con l'executor adattivo avrà esito negativo con un messaggio di errore.  Tuttavia, `citus.enable_repartition_joins` l'impostazione di su true consente a iperscale (CITUS) di passare temporaneamente all'executor di Task-Tracker per eseguire il join.  Il valore predefinito è false.

### <a name="task-tracker-executor-configuration"></a>Configurazione executor di Task Tracker

#### <a name="citustask_tracker_delay-integer"></a>ritardo di rilevamento CITUS. Task \_ \_ (Integer)

Questo parametro imposta il tempo di sospensione della traccia attività tra i cicli di gestione delle attività e i valori predefiniti di 200 ms. Il processo di rilevamento attività viene riattivato regolarmente, analizza tutte le attività assegnate e pianifica ed esegue queste attività.  Quindi, lo strumento di rilevamento delle attività dorme per un periodo di tempo prima di ripassare le attività.
Questo valore di configurazione determina la lunghezza del periodo di sospensione. Questo parametro è efficace nei ruoli di lavoro e deve essere modificato nel file PostgreSQL. conf. Dopo aver modificato il file di configurazione, gli utenti possono inviare un segnale SIGHUP o riavviare il server per rendere effettive le modifiche.

Questo parametro può essere ridotto per ridurre il ritardo causato dall'esecutore del rilevamento attività riducendo il gap di tempo tra i cicli di gestione.
La riduzione del ritardo è utile nei casi in cui le query di partizionamento sono brevi e quindi aggiornano regolarmente il proprio stato.

#### <a name="citusmax_assign_task_batch_size-integer"></a>CITUS. Max \_ assegna \_ \_ dimensioni batch attività \_ (Integer)

L'esecutore di attività Tracker sul coordinatore assegna in modo sincrono le attività in batch al daemon nei ruoli di lavoro. Questo parametro imposta il numero massimo di attività da assegnare in un singolo batch. La scelta di una dimensione batch più ampia consente un'assegnazione di attività più rapida. Tuttavia, se il numero di ruoli di lavoro è elevato, potrebbe essere necessario più tempo per tutti i thread di lavoro.  Questo parametro può essere impostato in fase di esecuzione ed è efficace sul coordinatore.

#### <a name="citusmax_running_tasks_per_node-integer"></a>\_attività in esecuzione CITUS. Max \_ \_ per \_ nodo (Integer)

Il processo di rilevamento delle attività pianifica ed esegue le attività assegnate in base alle esigenze. Questo valore di configurazione consente di impostare il numero massimo di attività da eseguire contemporaneamente in un nodo in un determinato momento e il valore predefinito è 8.

Il limite garantisce che non vi \' siano molte attività che raggiungono il disco allo stesso tempo e contribuiscano a evitare i conflitti di I/O su disco. Se le query vengono gestite dalla memoria o dalle unità SSD, è possibile aumentare il numero massimo di \_ attività in esecuzione \_ \_ per \_ nodo senza molta preoccupazione.

#### <a name="cituspartition_buffer_size-integer"></a>dimensioni del buffer CITUS. partition \_ \_ (Integer)

Imposta la dimensione del buffer da utilizzare per le operazioni di partizione e il valore predefinito è 8 MB.
Iperscale (CITUS) consente di ripartizionare i dati della tabella in più file quando vengono unite in join due tabelle di grandi dimensioni. Quando il buffer di partizione si riempie, i dati ripartizionati vengono scaricati in file su disco.  Questa voce di configurazione può essere impostata in fase di esecuzione ed è valida per i ruoli di lavoro.

### <a name="explain-output"></a>Output spiegazione

#### <a name="citusexplain_all_tasks-boolean"></a>CITUS. explain \_ All \_ Tasks (valore booleano)

Per impostazione predefinita, iperscale (CITUS) Mostra l'output di una singola attività arbitraria quando si esegue [explain](http://www.postgresql.org/docs/current/static/sql-explain.html) su una query distribuita. Nella maggior parte dei casi, l'output di explain sarà simile tra le attività. In alcuni casi alcune attività verranno pianificate in modo diverso o avranno tempi di esecuzione molto più elevati. In questi casi, può essere utile abilitare questo parametro, dopo il quale l'output di EXPLAIN includerà tutte le attività. La spiegazione di tutte le attività potrebbe richiedere più tempo per la spiegazione.

## <a name="next-steps"></a>Passaggi successivi

* Un altro tipo di configurazione, oltre ai parametri del server, è costituito dalle [Opzioni di configurazione](concepts-hyperscale-configuration-options.md) delle risorse in un gruppo di server con iperscalabilità (CITUS).
* Il database PostgreSQL sottostante dispone anche di [parametri di configurazione](http://www.postgresql.org/docs/current/static/runtime-config.html).
