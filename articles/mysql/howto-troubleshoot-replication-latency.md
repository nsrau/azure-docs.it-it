---
title: Risolvere i problemi di latenza di replica-database di Azure per MySQL
description: Informazioni su come risolvere i problemi relativi alla latenza di replica tramite database di Azure per MySQL Read repliche.
keywords: MySQL, risoluzione dei problemi, latenza di replica in secondi
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/25/2020
ms.openlocfilehash: a6ada3557350cd3f2f67dad54152eafded6639ec
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087027"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>Risolvere i problemi di latenza di replica in Database di Azure per MySQL

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

La funzionalità di [lettura della replica](concepts-read-replicas.md) consente di replicare i dati da un database di Azure per il server MySQL a un server di replica di sola lettura. È possibile scalare in orizzontale i carichi di lavoro eseguendo il routing delle query di lettura e Reporting dall'applicazione ai server di replica. Questa configurazione riduce la pressione del server di origine. Migliora inoltre le prestazioni complessive e la latenza dell'applicazione in modo scalabile. 

Le repliche vengono aggiornate in modo asincrono usando la tecnologia di replica basata sul file di log binario nativo (binlog) del motore MySQL. Per altre informazioni, vedere la [Panoramica della configurazione della replica basata sulla posizione del file MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

L'intervallo di replica sulle repliche di lettura secondarie dipende da diversi fattori. Questi fattori includono ma non sono limitati a: 

- Latenza di rete.
- Volume delle transazioni nel server di origine.
- Livello di calcolo del server di origine e del server di replica secondaria di lettura.
- Query in esecuzione nel server di origine e nel server secondario. 

Questo articolo illustra come risolvere i problemi relativi alla latenza di replica nel database di Azure per MySQL. Si comprenderanno anche alcune cause più comuni di una maggiore latenza di replica nei server di replica.

## <a name="replication-concepts"></a>Concetti relativi alla replica

Quando un log binario è abilitato, il server di origine scrive le transazioni di cui è stato eseguito il commit nel log binario. Il log binario viene utilizzato per la replica. È attivato per impostazione predefinita per tutti i server di cui è stato effettuato il provisioning fino a 16 TB di spazio di archiviazione. Nei server di replica vengono eseguiti due thread in ogni server di replica. Un thread è il thread di *io* e l'altro è il *thread SQL* :

- Il thread IO si connette al server di origine e richiede log binari aggiornati. Questo thread riceve gli aggiornamenti del log binario. Questi aggiornamenti vengono salvati in un server di replica, in un log locale denominato *relay log* .
- Il thread SQL legge il log di inoltro e quindi applica le modifiche ai dati nei server di replica.

## <a name="monitoring-replication-latency"></a>Monitoraggio della latenza di replica

Database di Azure per MySQL fornisce la metrica per l'intervallo di replica in secondi in [monitoraggio di Azure](concepts-monitoring.md). Questa metrica è disponibile solo nei server di replica di lettura. Viene calcolato dalla metrica seconds_behind_master disponibile in MySQL. 

Per comprendere la provocazione di una maggiore latenza di replica, connettersi al server di replica usando [MySQL Workbench](connect-workbench.md) o [Azure cloud Shell](https://shell.azure.com). Eseguire quindi il comando seguente.

>[!NOTE] 
> Nel codice sostituire i valori di esempio con il nome del server di replica e il nome utente amministratore. Il nome utente amministratore richiede `@\<servername>` per database di Azure per MySQL.

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

Ecco come si presenta l'esperienza nel terminale di Cloud Shell:

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

Nello stesso Cloud Shell terminale eseguire il comando seguente:

```
mysql> SHOW SLAVE STATUS;
```

Di seguito è riportato un output tipico:
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="Monitoraggio della latenza di replica&quot;:::


L'output contiene una grande quantità di informazioni. In genere, è necessario concentrarsi solo sulle righe descritte nella tabella seguente.

|Metrica|Descrizione|
|---|---|
|Slave_IO_State| Rappresenta lo stato corrente del thread di IO. In genere, lo stato è &quot;in attesa dell'evento master per inviare l'evento&quot; Se il server di origine (Master) esegue la sincronizzazione. Uno stato come &quot;connessione al Master" indica che la replica ha perso la connessione al server di origine. Verificare che il server di origine sia in esecuzione o controllare se un firewall sta bloccando la connessione.|
|Master_Log_File| Rappresenta il file di log binario in cui il server di origine sta scrivendo.|
|Read_Master_Log_Pos| Indica la posizione in cui il server di origine scrive nel file di log binario.|
|Relay_Master_Log_File| Rappresenta il file di log binario che il server di replica sta leggendo dal server di origine.|
|Slave_IO_Running| Indica se il thread IO è in esecuzione. Il valore deve essere `Yes` . Se il valore è `NO` , la replica probabilmente è interruppe.|
|Slave_SQL_Running| Indica se il thread SQL è in esecuzione. Il valore deve essere `Yes` . Se il valore è `NO` , la replica probabilmente è interruppe.|
|Exec_Master_Log_Pos| Indica la posizione del Relay_Master_Log_File che la replica sta applicando. Se è presente una latenza, questa sequenza di posizione deve essere inferiore a Read_Master_Log_Pos.|
|Relay_Log_Space|Indica il limite superiore delle dimensioni del log di inoltro. È possibile controllare le dimensioni eseguendo una query `SHOW GLOBAL VARIABLES` come `relay_log_space_limit` .|
|Seconds_Behind_Master| Visualizza la latenza di replica in secondi.|
|Last_IO_Errno|Visualizza il codice di errore del thread IO, se presente. Per ulteriori informazioni su questi codici, vedere la Guida di [riferimento ai messaggi di errore del server MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_IO_Error| Visualizza il messaggio di errore thread IO, se presente.|
|Last_SQL_Errno|Visualizza il codice di errore del thread SQL, se presente. Per ulteriori informazioni su questi codici, vedere la Guida di [riferimento ai messaggi di errore del server MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).|
|Last_SQL_Error|Visualizza il messaggio di errore thread SQL, se presente.|
|Slave_SQL_Running_State| Indica lo stato del thread SQL corrente. In questo stato, `System lock` è normale. È anche normale visualizzare lo stato `Waiting for dependent transaction to commit` . Questo stato indica che la replica è in attesa che il server di origine aggiorni le transazioni di cui è stato eseguito il commit.|

Se Slave_IO_Running è `Yes` e Slave_SQL_Running è `Yes` , la replica viene eseguita correttamente. 

Selezionare quindi Last_IO_Errno, Last_IO_Error, Last_SQL_Errno e Last_SQL_Error.  In questi campi vengono visualizzati il numero di errore e il messaggio di errore dell'errore più recente che ha causato l'arresto del thread SQL. Un numero di errore `0` e un messaggio vuoto significa che non è presente alcun errore. Esaminare qualsiasi valore di errore diverso da zero controllando il codice di errore nel [riferimento al messaggio di errore del server MySQL](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html).

## <a name="common-scenarios-for-high-replication-latency"></a>Scenari comuni per la latenza di replica elevata

Le sezioni seguenti riportano scenari in cui la latenza di replica elevata è comune.

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>Latenza di rete o utilizzo elevato della CPU nel server di origine

Se vengono visualizzati i valori seguenti, la latenza di replica è probabilmente causata da un'elevata latenza di rete o da un elevato utilizzo della CPU nel server di origine. 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

In questo caso, il thread IO è in esecuzione ed è in attesa nel server di origine. Il server di origine è già stato scritto nel file di log binario numero 20. La replica ha ricevuto solo un numero massimo di file 10. I fattori principali per la latenza di replica elevata in questo scenario sono la velocità di rete o un utilizzo elevato della CPU nel server di origine.  

In Azure, la latenza di rete all'interno di un'area può essere in genere misurata in millisecondi. Tra le aree, la latenza varia da millisecondi a secondi. 

Nella maggior parte dei casi, il ritardo di connessione tra i thread di IO e il server di origine è causato da un utilizzo elevato della CPU nel server di origine. I thread IO vengono elaborati lentamente. È possibile rilevare questo problema usando monitoraggio di Azure per controllare l'utilizzo della CPU e il numero di connessioni simultanee nel server di origine.

Se non viene visualizzato un utilizzo elevato della CPU nel server di origine, il problema potrebbe essere la latenza di rete. Se la latenza di rete è improvvisamente eccessivamente elevata, verificare la presenza di problemi noti o interruzioni nella [pagina stato di Azure](https://status.azure.com/status) . 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>Picchi intensivi delle transazioni nel server di origine

Se vengono visualizzati i valori seguenti, è probabile che un elevato numero di transazioni nel server di origine causi una latenza di replica. 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

L'output mostra che la replica può recuperare il log binario dietro il server di origine. Tuttavia, il thread di i/o di replica indica che lo spazio del log di inoltro è già pieno. 

La velocità della rete non causa il ritardo. La replica sta provando a aggiornarsi. Tuttavia, la dimensione del log binario aggiornata supera il limite superiore dello spazio del log di inoltro. 

Per risolvere il problema, abilitare il [log di query lente](concepts-server-logs.md) nel server di origine. Utilizzare i log di query lente per identificare le transazioni con esecuzione prolungata nel server di origine. Quindi ottimizzare le query identificate per ridurre la latenza nel server. 

La latenza di replica di questo ordinamento è generalmente causata dal caricamento dei dati nel server di origine. Quando i server di origine hanno carichi di dati settimanali o mensili, la latenza di replica è purtroppo inevitabile. Al termine del caricamento dei dati nel server di origine, i server di replica si aggiornano.


### <a name="slowness-on-the-replica-server"></a>Lentezza sul server di replica

Se si osservano i valori seguenti, il problema potrebbe essere il server di replica. 

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

In questo scenario, l'output mostra che sia il thread IO che il thread SQL sono in esecuzione correttamente. La replica legge lo stesso file di log binario scritto dal server di origine. Tuttavia, una certa latenza nel server di replica riflette la stessa transazione del server di origine. 

Le sezioni seguenti descrivono le cause comuni di questo tipo di latenza.

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>Nessuna chiave primaria o chiave univoca in una tabella

Database di Azure per MySQL usa la replica basata su righe. Il server di origine scrive gli eventi nel log binario, registrando le modifiche nelle singole righe della tabella. Il thread SQL replica quindi le modifiche apportate alle righe della tabella corrispondenti nel server di replica. Quando una tabella non dispone di una chiave primaria o di una chiave univoca, il thread SQL analizza tutte le righe nella tabella di destinazione per applicare le modifiche. Questa analisi può causare la latenza di replica.

In MySQL la chiave primaria è un indice associato che garantisce prestazioni rapide delle query perché non può includere valori NULL. Se si usa il motore di archiviazione InnoDB, i dati della tabella sono organizzati fisicamente per eseguire ricerche e ordinamenti estremamente veloci in base alla chiave primaria. 

È consigliabile aggiungere una chiave primaria nelle tabelle nel server di origine prima di creare il server di replica. Aggiungere chiavi primarie nel server di origine e quindi ricreare le repliche di lettura per migliorare la latenza di replica.

Utilizzare la query seguente per individuare le tabelle in cui manca una chiave primaria nel server di origine:

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

#### <a name="long-running-queries-on-the-replica-server"></a>Query con esecuzione prolungata sul server di replica

Il carico di lavoro nel server di replica può far ritardare il thread SQL dietro il thread di IO. Le query con esecuzione prolungata sul server di replica sono una delle cause più comuni della latenza di replica elevata. Per risolvere il problema, abilitare il [log di query lente](concepts-server-logs.md) sul server di replica. 

Le query lente possono aumentare l'utilizzo delle risorse o rallentare il server in modo che la replica non riesca a aggiornarsi con il server di origine. In questo scenario, ottimizzare le query lente. Le query più veloci impediscono il blocco del thread SQL e migliorano significativamente la latenza di replica.


#### <a name="ddl-queries-on-the-source-server"></a>Query DDL nel server di origine
Nel server di origine, un comando Data Definition Language (DDL) come [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) può richiedere molto tempo. Durante l'esecuzione del comando DDL, migliaia di altre query potrebbero essere eseguite in parallelo nel server di origine. 

Quando il linguaggio DDL viene replicato, per garantire la coerenza del database, il motore MySQL esegue il DDL in un singolo thread di replica. Durante questa attività, tutte le altre query replicate vengono bloccate e devono attendere il completamento dell'operazione DDL nel server di replica. Anche le operazioni DDL online provocano questo ritardo. Le operazioni DDL aumentano la latenza di replica.

Se è stato abilitato il [log di query lente](concepts-server-logs.md) nel server di origine, è possibile rilevare questo problema di latenza controllando la presenza di un comando DDL eseguito nel server di origine. Tramite l'eliminazione, la ridenominazione e la creazione di indici, è possibile utilizzare l'algoritmo inplace per ALTER TABLE. Potrebbe essere necessario copiare i dati della tabella e ricompilare la tabella. 

In genere, il DML simultaneo è supportato per l'algoritmo inplace. È tuttavia possibile adottare brevemente un blocco di metadati esclusivo sulla tabella quando si prepara ed esegue l'operazione. Per l'istruzione CREATE INDEX è quindi possibile utilizzare l'algoritmo e il blocco delle clausole per influenzare il metodo per la copia della tabella e il livello di concorrenza per la lettura e la scrittura. È comunque possibile impedire operazioni DML aggiungendo un indice full-text o un indice spaziale. 

Nell'esempio seguente viene creato un indice utilizzando le clausole ALGORITHM e LOCK.

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

Sfortunatamente, per un'istruzione DDL che richiede un blocco, non è possibile evitare la latenza di replica. Per ridurre i potenziali effetti, eseguire questi tipi di operazioni DDL durante le fasce orarie di minore attività, ad esempio durante la notte.

#### <a name="downgraded-replica-server"></a>Server di replica con downgrade

In database di Azure per MySQL leggere le repliche usare la stessa configurazione server del server di origine. È possibile modificare la configurazione del server di replica dopo che è stata creata. 

Se il server di replica viene sottoclassato al downgrade, il carico di lavoro può utilizzare più risorse, che a loro volta possono causare una latenza di replica. Per rilevare questo problema, usare monitoraggio di Azure per verificare l'utilizzo della CPU e della memoria del server di replica. 

In questo scenario si consiglia di lasciare la configurazione del server di replica con valori uguali o maggiori dei valori del server di origine. Questa configurazione consente alla replica di rimanere al passo con il server di origine.

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>Miglioramento della latenza di replica mediante l'ottimizzazione dei parametri del server di origine

Per impostazione predefinita, in database di Azure per MySQL la replica è ottimizzata per l'esecuzione con thread paralleli sulle repliche. Quando i carichi di lavoro con concorrenza elevata nel server di origine causano il mancato rispetto del server di replica, è possibile migliorare la latenza di replica configurando il parametro binlog_group_commit_sync_delay nel server di origine. 

Il parametro binlog_group_commit_sync_delay controlla il numero di microsecondi di attesa del commit del log binario prima di sincronizzare il file di log binario. Il vantaggio di questo parametro è che anziché applicare immediatamente ogni transazione di cui è stato eseguito il commit, il server di origine invia gli aggiornamenti dei log binari in blocco. Questo ritardo riduce le operazioni di i/o sulla replica e contribuisce a migliorare le prestazioni. 

Potrebbe essere utile impostare il parametro binlog_group_commit_sync_delay su 1000. Monitorare quindi la latenza di replica. Impostare questo parametro con cautela e usarlo solo per carichi di lavoro con concorrenza elevata. 

> [!IMPORTANT] 
> Nel server di replica, è consigliabile binlog_group_commit_sync_delay parametro come 0. Questa operazione è consigliata perché, a differenza del server di origine, il server di replica non avrà una concorrenza elevata e l'aumento del valore per binlog_group_commit_sync_delay nel server di replica potrebbe causare un aumento involontario del ritardo di replica.

Per i carichi di lavoro con concorrenza ridotta che includono molte transazioni singleton, l'impostazione binlog_group_commit_sync_delay può aumentare la latenza. La latenza può aumentare perché il thread di IO attende gli aggiornamenti bulk dei log binari anche se viene eseguito il commit solo di alcune transazioni. 

## <a name="next-steps"></a>Passaggi successivi
Vedere la [Panoramica della replica di MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).
