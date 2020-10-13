---
title: Risolvere i problemi di latenza di replica-database di Azure per MySQL
description: Informazioni su come risolvere i problemi relativi alla latenza di replica con database di Azure per MySQL Read repliche
keywords: MySQL, risoluzione dei problemi, latenza di replica in secondi
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877108"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Risolvere i problemi di latenza di replica nel database di Azure per MySQL

La funzionalità di [lettura della replica](concepts-read-replicas.md) consente di replicare i dati da un database di Azure per il server MySQL a un server di replica di sola lettura. Le repliche di lettura vengono usate per aumentare il carico di lavoro eseguendo il routing delle query di lettura/Reporting dall'applicazione ai server di replica. In questo modo si riduce la pressione sul server primario e si migliorano le prestazioni complessive e la latenza dell'applicazione mentre si ridimensiona. Le repliche vengono aggiornate in modo asincrono tramite la tecnologia di replica basata su posizione del file di registro binario nativo, o binlog, del motore MySQL. Per altre informazioni su questo tipo di replica, vedere [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Panoramica della replica basata su binlog di MySQL). 

L'intervallo di replica sulle repliche di lettura secondarie dipende dal numero di fattori, tra cui 

- Latenza di rete
- Volume delle transazioni nel server di origine
- Livello di calcolo dell'origine e del server di replica di lettura secondaria
- Query in esecuzione nel server primario e secondario. 

In questo documento si apprenderà come risolvere i problemi relativi alla latenza di replica nel database di Azure per MySQL. Inoltre, si comprenderanno anche alcune delle cause più comuni di una maggiore latenza di replica nei server di replica.

## <a name="replication-concepts"></a>Concetti relativi alla replica

Quando il registro binario è abilitato, il server di origine scrive la transazione di cui è stato eseguito il commit nel log binario, che viene utilizzato per la replica. Per impostazione predefinita, il log binario è attivato per tutti i server di cui è stato effettuato il provisioning che supporta fino a 16 TB di archiviazione. Nei server di replica sono in esecuzione due thread per ogni server di replica, uno denominato thread IO e l'altro denominato thread SQL.

- Il **thread io** si connette al server di origine e richiede log binari aggiornati. Quando questo thread riceve gli aggiornamenti dei log binari, viene salvato in un server di replica, in un log locale denominato log di inoltro.
- Il **thread SQL** legge il log di inoltro e applica le modifiche dei dati nei server di replica.

## <a name="monitoring-replication-latency"></a>Monitoraggio della latenza di replica

Database di Azure per MySQL offre la metrica di ritardo della replica in secondi in [monitoraggio di Azure](concepts-monitoring.md). Questa metrica è disponibile solo nei server di replica di lettura. Questa metrica viene calcolata usando la metrica seconds_behind_master disponibile in MySQL. Per comprendere la causa principale dell'aumento della latenza di replica, connettersi al server di replica usando [MySQL Workbench](connect-workbench.md) o [Azure cloud Shell](https://shell.azure.com) ed eseguire il comando seguente:

 Sostituire i valori con il nome del server di replica effettivo e il nome di accesso dell'utente amministratore. Il nome utente amministratore richiede ' @ \<servername> ' per database di Azure per MySQL:

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Ecco come si presenta l'esperienza nel terminale Cloud Shell
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  Nello stesso Azure Cloud Shell terminale eseguire il comando seguente

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  Un output tipico sarà simile al seguente:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Monitoraggio della latenza di replica&quot;:::


L'output contiene una grande quantità di informazioni, ma in genere è importante concentrarsi solo sulle colonne seguenti:

|Metrica|Descrizione|
|---|---|
|Slave_IO_State| Stato corrente del thread di IO. In genere, lo stato è &quot;in attesa dell'evento di invio del Master&quot;, se è in corso la sincronizzazione. Tuttavia, se viene visualizzato uno stato come &quot;connessione al Master&quot;, la replica ha perso la connessione al server master. Controllare se il database master è in esecuzione o se un firewall sta bloccando la connessione.|
|Master_Log_File| File di log binario in cui viene scritto il database master.|
|Read_Master_Log_Pos| Rappresenta la posizione nel file di log binario precedente in cui viene scritto il database master.|
|Relay_Master_Log_File| Indicato rappresenta il file di log binario che il server di replica sta leggendo dal master.|
|Slave_IO_Running| Indica se il thread IO è in esecuzione. Deve essere &quot;Yes&quot;. Se &quot;NO&quot;, probabilmente la replica è interruppe.|
|Slave_SQL_Running| Indica se il thread SQL è in esecuzione. Deve essere &quot;Yes&quot;. Se &quot;NO&quot;, probabilmente la replica è interruppe.|
|Exec_Master_Log_Pos| Visualizza la posizione dell'Relay_Master_Log_File la replica sta applicando. Se è presente una latenza, questa sequenza di posizione deve essere inferiore a Read_Master_Log_Pos.|
|Relay_Log_Space|Visualizza il limite superiore delle dimensioni del log di inoltro. È possibile controllare le dimensioni eseguendo una query su Mostra variabili globali come &quot;relay_log_space_limit&quot;.|
|Seconds_Behind_Master| Visualizza la latenza di replica in secondi.|
|Last_IO_Errno|Visualizza il codice di errore del thread IO, se presente. Per ulteriori informazioni su questi codici, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_IO_Error| Visualizza il messaggio di errore thread IO, se presente.|
|Last_SQL_Errno|Visualizza il codice di errore del thread SQL, se presente. Per ulteriori informazioni su questi codici, vedere la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Visualizza il messaggio di errore thread SQL, se presente.|
|Slave_SQL_Running_State| Indica lo stato del thread SQL corrente. Si noti che &quot;blocco di sistema" visualizzato in questo stato è un comportamento normale. È normale visualizzare lo stato "in attesa del commit di una transazione dipendente". Indica che la replica è in attesa del master per aggiornare le transazioni di cui è stato eseguito il commit.|

Se Slave_IO_Running è sì e Slave_SQL_Running è sì, la replica è in esecuzione correttamente. 

Successivamente, è necessario controllare Last_IO_Errno, Last_IO_Error, Last_SQL_Errno e Last_SQL_Error.  Questi campi contengono il numero di errore e il messaggio di errore dell'errore più recente che ha causato l'arresto del thread SQL. Un numero di errore 0 e un messaggio vuoto significa che non è presente alcun errore. È necessario esaminare ulteriormente un valore diverso da zero nell'errore cercando il codice di errore nella [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Scenari comuni per la latenza di replica elevata

### <a name="network-latency-or-high-cpu-on-source-server"></a>Latenza di rete o CPU elevata nel server di origine

Se si osservano i valori seguenti, la causa più comune della latenza di replica è una latenza di rete elevata o un utilizzo elevato della CPU nel server di origine. In questo caso, il thread IO è in esecuzione e in attesa del master. Il Master (server di origine) ha già scritto nel file di log binario #20, mentre la replica è stata ricevuta solo fino al #10 file. I principali fattori che contribuiscono alla latenza di replica elevata in questo scenario sono la velocità di rete o un utilizzo elevato della CPU nel server di origine.  In Azure, la latenza di rete all'interno di un'area è in genere compresa tra i millisecondi e l'intera area può raggiungere i secondi. Nella maggior parte dei casi, il ritardo nel thread IO per la connessione al server di origine è causato da un utilizzo elevato della CPU nel server di origine, causando un rallentamento dell'elaborazione del thread IO. Questa operazione può essere rilevata monitorando l'utilizzo della CPU e osservando il numero di connessioni simultanee nel server di origine con monitoraggio di Azure.

Se non viene visualizzato un utilizzo elevato della CPU nel server di origine, le possibili cause possono essere la latenza di rete. Se si verifica una latenza di rete elevata in modo anomalo, è consigliabile controllare la [pagina stato di Azure](https://status.azure.com/status) per verificare che non siano presenti problemi noti o interruzioni. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>Elevato numero di transazioni nel server di origine

Se si osservano i valori seguenti, la causa più comune della latenza di replica è il numero elevato di transazioni nel server di origine. Nell'output seguente, anche se la replica può recuperare il log binario dietro il master, il thread di i/o di replica indica che lo spazio del log di inoltro è già pieno. Quindi, la velocità della rete non causa il ritardo, perché la replica è già stata tentata di aggiornarla in modo più rapido possibile. Al contrario, la dimensione del log binario aggiornata supera il limite superiore dello spazio del log di inoltro. Per risolvere ulteriormente questo problema, è necessario abilitare il [log di query lente](concepts-server-logs.md) sul server master. I log di query lente consentono di identificare le transazioni con esecuzione prolungata nel server di origine. Le query identificate devono essere ottimizzate per ridurre la latenza nel server. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

Di seguito sono riportate le cause più comuni della latenza in questa categoria:

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>Latenza di replica a causa del caricamento dei dati nel server di origine
In alcuni casi, il caricamento dei dati settimanali o mensili nei server di origine. Sfortunatamente, in questo caso la latenza di replica è inevitabile. In questo scenario, i server di replica vengono rilevati dopo il completamento del caricamento dei dati nel server di origine.


### <a name="slowness-on-the-replica-server"></a>Lentezza sul server di replica

Se si osservano i valori seguenti, la causa più comune può essere un problema nel server di replica che richiede un'ulteriore analisi. In questo scenario, come illustrato nell'output, sia i thread IO che SQL sono in esecuzione correttamente e la replica sta leggendo lo stesso file di log binario delle Scritture master. Tuttavia, si verifica una latenza nel server di replica per riflettere la stessa transazione del server di origine. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

Di seguito sono riportate le cause più comuni della latenza in questa categoria:

#### <a name="no-primary-or-unique-key-on-a-table"></a>Nessuna chiave primaria o univoca in una tabella

Database di Azure per MySQL usa la replica basata su righe. Con la replica basata su righe, il server master scrive gli eventi nel log binario sulla modifica delle singole righe della tabella. Il thread SQL esegue a sua volta le modifiche apportate alle righe della tabella corrispondenti nel server di replica. Nessuna chiave primaria o univoca in una tabella è una delle cause comuni della latenza di replica. La mancanza di chiavi primarie o univoche comporta l'analisi di tutte le righe nella tabella di destinazione da parte del thread SQL per applicare le modifiche.

In MySQL la chiave primaria è un indice associato che garantisce prestazioni di esecuzione delle query veloci perché non può includere valori NULL. Con il motore di archiviazione InnoDB, i dati della tabella sono organizzati fisicamente per eseguire ricerche e ordinamenti estremamente veloci in base alla chiave primaria. È pertanto consigliabile aggiungere una chiave primaria nelle tabelle nel server di origine prima di creare il server di replica. In questo scenario è necessario aggiungere chiavi primarie nel server di origine e ricreare le repliche di lettura per migliorare la latenza di replica.

È possibile utilizzare la query seguente per determinare le tabelle con la chiave primaria mancante nel server di origine:

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>Latenza di replica a causa di query con esecuzione prolungata nel server di replica

È possibile che il carico di lavoro nel server di replica possa impedire al thread SQL di rimanere al passo con il thread di IO. Questa è una delle cause comuni della latenza di replica elevata se è presente una query con esecuzione prolungata nel server di replica. In questo caso, è necessario abilitare il [log di query lente](concepts-server-logs.md) sul server di replica per facilitare la risoluzione del problema. Le query lente possono aumentare il consumo di risorse o rallentare il server, di conseguenza la replica non sarà in grado di recuperare il database master. In questo scenario è necessario ottimizzare le query lente. Una query più veloce impedisce il blocco del thread SQL e migliora significativamente la latenza di replica.


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>Latenza di replica a causa di query DDL nel server di origine
Se è presente un comando DDL con esecuzione prolungata, ad esempio [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) eseguito nel server di origine e l'esecuzione è stata impiegata per 1 ora. Durante questo periodo, è possibile che siano in esecuzione migliaia di altre query in parallelo nel server di origine. Quando il linguaggio DDL viene replicato nella replica, per garantire la coerenza del database, il motore MySQL deve eseguire il linguaggio DDL in un unico thread di replica. Tutte le altre query replicate verranno bloccate e sarà necessario attendere un'ora o più fino a quando l'operazione DDL non viene completata nel server di replica. Questo vale indipendentemente dall'operazione DDL online. Con le operazioni DDL, è prevista una latenza di replica più elevata per la replica.

Se il [log di query lento](concepts-server-logs.md) è abilitato nel server di origine, è possibile rilevare questo scenario esaminando i log di query lente per verificare se è stato eseguito un comando DDL nel server di origine. Sebbene l'eliminazione di indici, la ridenominazione e la creazione usino l'algoritmo inplace per ALTER TABLE, può comportare la copia dei dati della tabella e la ricompilazione della tabella. In genere, per l'algoritmo di inserimento DML simultaneo è supportato, ma un blocco di metadati esclusivo per la tabella può essere ricavato brevemente durante le fasi di preparazione ed esecuzione dell'operazione. Di conseguenza, per l'istruzione CREATE INDEX è possibile utilizzare l'algoritmo e il blocco delle clausole per influenzare il metodo di copia della tabella e il livello di concorrenza per la lettura e la scrittura, tuttavia l'aggiunta di un indice full-text o spaziale impedisce comunque le operazioni DML. Vedere di seguito un esempio di creazione di un indice con clausole ALGORITHM e LOCK:

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Sfortunatamente, per l'istruzione DDL che richiede un blocco, non è possibile evitare la latenza di replica, ma questi tipi di operazioni DDL devono essere eseguiti durante gli orari di minore attività, ad esempio durante la notte, per ridurre l'impatto potenziale.

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>Latenza di replica dovuta allo SKU inferiore del server di replica

Le repliche di lettura del database di Azure per MySQL vengono create con la stessa configurazione del server del server master. La configurazione del server di replica può essere modificata dopo la creazione. Tuttavia, se verrà eseguito il downgrade del server di replica, il carico di lavoro può causare un consumo di risorse maggiore che a sua volta può causare una latenza di replica. Questo problema può essere osservato monitorando l'utilizzo della CPU e della memoria del server di replica da monitoraggio di Azure. In questo scenario, è consigliabile mantenere la configurazione del server di replica con valori uguali o superiori a quelli dell'origine per assicurarsi che la replica sia in grado di rimanere al passo con il database master.

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>Miglioramento della latenza di replica mediante l'ottimizzazione del parametro Server nel server di origine

In database di Azure per MySQL la replica è ottimizzata per l'esecuzione con thread paralleli sulle repliche per impostazione predefinita. Per carichi di lavoro di concorrenza elevati nel server di origine in cui il server di replica non riesce a recuperare, la latenza di replica può essere migliorata configurando i parametri binlog_group_commit_sync_delay nel server di origine. Questo parametro controlla il numero di microsecondi di attesa del commit del log binario prima di sincronizzare il file di log binario. Il vantaggio è che anziché applicare immediatamente ogni transazione di cui è stato eseguito il commit, il database master invia gli aggiornamenti dei log binari in blocco. In questo modo si riduce la i/o sulla replica e si contribuisce a migliorare le prestazioni. In questo scenario può essere utile impostare binlog_group_commit_sync_delay su 1000, quindi monitorare la latenza di replica. Questo parametro deve essere impostato con cautela e sfruttato solo per carichi di lavoro simultanei elevati. Per uno scenario di concorrenza ridotto con molte transazioni singleton, l'impostazione binlog_group_commit_sync_delay può aggiungere alla latenza perché il thread di i/o è in attesa di aggiornamenti dei log binari bulk mentre è possibile eseguire il commit solo di poche transazioni. 

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [MySQL binlog Replication Overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
