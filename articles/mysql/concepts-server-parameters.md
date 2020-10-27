---
title: Parametri del server-database di Azure per MySQL
description: Questo argomento fornisce le linee guida per la configurazione dei parametri del server in database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: b6a914df9ed277625d3706465fe335e128aeced1
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545158"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>Parametri del server nel database di Azure per MySQL

Questo articolo fornisce considerazioni e linee guida per la configurazione dei parametri del server in database di Azure per MySQL.

## <a name="what-are-server-parameters"></a>Che cosa sono i parametri del server? 

Il motore MySQL fornisce diverse variabili/parametri del server che possono essere usati per configurare e ottimizzare il comportamento del motore. Alcuni parametri possono essere impostati dinamicamente in fase di esecuzione mentre altri sono "statici", richiedendo un riavvio del server per poter essere applicati.

Database di Azure per MySQL offre la possibilità di modificare il valore dei vari parametri del server MySQL usando il [portale di Azure](./howto-server-parameters.md), l'interfaccia della riga di comando di [Azure](./howto-configure-server-parameters-using-cli.md)e [PowerShell](./howto-configure-server-parameters-using-powershell.md) per soddisfare le esigenze del carico di lavoro.

## <a name="configurable-server-parameters"></a>Parametri del server configurabili

L'elenco di parametri del server supportati è in continua crescita. Utilizzare la scheda parametri server della portale di Azure per visualizzare l'elenco completo e configurare i valori dei parametri del server.

Vedere le sezioni seguenti per altre informazioni sui limiti dei diversi parametri del server aggiornati di frequente. I limiti sono determinati dal piano tariffario e dal Vcore del server.

### <a name="thread-pools"></a>Pool di thread

MySQL assegna tradizionalmente un thread per ogni connessione client. Con l'aumentare del numero di utenti simultanei, si verifica un calo delle prestazioni corrispondente. Molti thread attivi possono influire significativamente sulle prestazioni a causa di un aumento del cambio del contesto, della contesa dei thread e della località non valida per le cache della CPU.

I pool di thread, una funzionalità lato server e diversi dal pool di connessioni, ottimizzano le prestazioni introducendo un pool dinamico di thread di lavoro che possono essere usati per limitare il numero di thread attivi in esecuzione sul server e ridurre al minimo la varianza dei thread. Ciò consente di garantire che un aumento delle connessioni non provochi l'esaurimento delle risorse del server o l'arresto anomalo del sistema con un errore di memoria insufficiente. I pool di thread sono più efficienti per query brevi e carichi di lavoro con utilizzo intensivo della CPU, ad esempio carichi di lavoro OLTP.

Per altre informazioni sui pool di thread, vedere [Introduzione ai pool di thread nel database di Azure per MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)

> [!NOTE]
> La funzionalità del pool di thread non è supportata per la versione MySQL 5,6. 

### <a name="configuring-the-thread-pool"></a>Configurazione del pool di thread
Per abilitare il pool di thread, aggiornare il `thread_handling` parametro Server a "pool-of-threads". Per impostazione predefinita, questo parametro è impostato su `one-thread-per-connection` , il che significa che MySQL crea un nuovo thread per ogni nuova connessione. Si noti che si tratta di un parametro statico e che richiede l'applicazione di un riavvio del server.

È anche possibile configurare il numero massimo e minimo di thread nel pool impostando i parametri del server seguenti: 
- `thread_pool_max_threads`: Questo valore garantisce che non vi sarà più di questo numero di thread nel pool.
- `thread_pool_min_threads`: Questo valore imposta il numero di thread che verranno riservati anche dopo la chiusura delle connessioni.

Per migliorare le prestazioni delle query brevi nel pool di thread, database di Azure per MySQL consente di abilitare l'esecuzione del batch in cui, anziché tornare al pool di thread subito dopo l'esecuzione di una query, i thread restano attivi per un breve periodo di attesa per la query successiva tramite questa connessione. Il thread esegue quindi la query rapidamente e, una volta completata, attende il successivo, fino a quando il consumo complessivo del processo supera una soglia. Il comportamento di esecuzione del batch viene determinato usando i parametri del server seguenti:  

-  `thread_pool_batch_wait_timeout`: Questo valore specifica il tempo di attesa di un thread per l'elaborazione di un'altra query.
- `thread_pool_batch_max_time`: Questo valore determina il tempo massimo durante il quale un thread ripeterà il ciclo di esecuzione della query e aspetterà la query successiva.

> [!IMPORTANT]
> Testare il pool di thread prima di attivarlo nell'ambiente di produzione. 

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

In database di Azure per MySQL i log binari sono sempre abilitati, ovvero `log_bin` è impostato su on. Se si vogliono usare i trigger, verrà generato un errore simile a quello in *cui non si dispone del privilegio Super e la registrazione binaria è abilitata (è possibile usare la variabile meno sicura `log_bin_trust_function_creators` )* . 

Il formato di registrazione binario è sempre **Row** e tutte le connessioni al server utilizzano **sempre** la registrazione binaria basata su righe. Con la registrazione binaria basata su righe, non esistono problemi di sicurezza e la registrazione binaria non può essere interrotta, quindi è possibile impostare in modo sicuro [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) su **true** .

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Per altre informazioni su questo parametro, esaminare la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size).

#### <a name="servers-supporting-up-to-4-tb-storage"></a>Server che supportano fino a 4 TB di archiviazione

|**Piano tariffario**|**vCore**|**Valore predefinito (byte)**|**Valore minimo (byte)**|**Valore massimo (byte)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Utilizzo generico|2|3758096384|134217728|3758096384|
|Utilizzo generico|4|8053063680|134217728|8053063680|
|Utilizzo generico|8|16106127360|134217728|16106127360|
|Utilizzo generico|16|32749125632|134217728|32749125632|
|Utilizzo generico|32|66035122176|134217728|66035122176|
|Utilizzo generico|64|132070244352|134217728|132070244352|
|Con ottimizzazione per la memoria|2|7516192768|134217728|7516192768|
|Con ottimizzazione per la memoria|4|16106127360|134217728|16106127360|
|Con ottimizzazione per la memoria|8|32212254720|134217728|32212254720|
|Con ottimizzazione per la memoria|16|65498251264|134217728|65498251264|
|Con ottimizzazione per la memoria|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>I server supportano fino a 16 TB di archiviazione

|**Piano tariffario**|**vCore**|**Valore predefinito (byte)**|**Valore minimo (byte)**|**Valore massimo (byte)**|
|---|---|---|---|---|
|Basic|1|872415232|134217728|872415232|
|Basic|2|2684354560|134217728|2684354560|
|Utilizzo generico|2|7516192768|134217728|7516192768|
|Utilizzo generico|4|16106127360|134217728|16106127360|
|Utilizzo generico|8|32212254720|134217728|32212254720|
|Utilizzo generico|16|65498251264|134217728|65498251264|
|Utilizzo generico|32|132070244352|134217728|132070244352|
|Utilizzo generico|64|264140488704|134217728|264140488704|
|Con ottimizzazione per la memoria|2|15032385536|134217728|15032385536|
|Con ottimizzazione per la memoria|4|32212254720|134217728|32212254720|
|Con ottimizzazione per la memoria|8|64424509440|134217728|64424509440|
|Con ottimizzazione per la memoria|16|130996502528|134217728|130996502528|
|Con ottimizzazione per la memoria|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` può essere aggiornato solo nei piani tariffari per utilizzo generico e con ottimizzazione per la memoria.

MySQL archivia la tabella InnoDB in spazi di tabella diversi in base alla configurazione specificata durante la creazione della tabella. Lo [spazio di tabella del sistema](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) è l'area di archiviazione per il dizionario dei dati InnoDB. Uno [spazio di tabella di un file per tabella](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) contiene dati e indici per una sola tabella InnoDB e viene archiviato nel file system del file di dati in uso. Questo comportamento è controllato dal parametro del server `innodb_file_per_table`. Impostando `innodb_file_per_table` su `OFF` InnoDB crea tabelle nello spazio di tabella del sistema. Altrimenti, InnoDB crea tabelle in spazi di tabella di un file per tabella.

Database di Azure per MySQL supporta al massimo **1 TB** in un unico file di dati. Se le dimensioni del database sono maggiori di 1 TB, è necessario creare la tabella nello spazio di tabella [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table). Se si dispone di una singola tabella di dimensioni superiori a 1 TB, è necessario usare la tabella di partizione.

### <a name="join_buffer_size"></a>join_buffer_size

Per altre informazioni su questo parametro, esaminare la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size).

|**Piano tariffario**|**vCore**|**Valore predefinito (byte)**|**Valore minimo (byte)**|**Valore massimo (byte)**|
|---|---|---|---|---|
|Basic|1|Non configurabile nel livello Basic|N/D|N/D|
|Basic|2|Non configurabile nel livello Basic|N/D|N/D|
|Utilizzo generico|2|262144|128|268435455|
|Utilizzo generico|4|262144|128|536870912|
|Utilizzo generico|8|262144|128|1073741824|
|Utilizzo generico|16|262144|128|2147483648|
|Utilizzo generico|32|262144|128|4294967295|
|Utilizzo generico|64|262144|128|4294967295|
|Con ottimizzazione per la memoria|2|262144|128|536870912|
|Con ottimizzazione per la memoria|4|262144|128|1073741824|
|Con ottimizzazione per la memoria|8|262144|128|2147483648|
|Con ottimizzazione per la memoria|16|262144|128|4294967295|
|Con ottimizzazione per la memoria|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|**Piano tariffario**|**vCore**|**Valore predefinito**|**Valore minimo**|**Valore massimo**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Utilizzo generico|2|300|10|600|
|Utilizzo generico|4|625|10|1250|
|Utilizzo generico|8|1250|10|2500|
|Utilizzo generico|16|2500|10|5000|
|Utilizzo generico|32|5000|10|10000|
|Utilizzo generico|64|10000|10|20000|
|Con ottimizzazione per la memoria|2|625|10|1250|
|Con ottimizzazione per la memoria|4|1250|10|2500|
|Con ottimizzazione per la memoria|8|2500|10|5000|
|Con ottimizzazione per la memoria|16|5000|10|10000|
|Con ottimizzazione per la memoria|32|10000|10|20000|

Quando le connessioni superano il limite, è possibile che venga visualizzato l'errore seguente:
> ERROR 1040 (08004): Troppe connessioni

> [!IMPORTANT]
> Per un'esperienza ottimale, è consigliabile usare un pool di connessioni come ProxySQL per gestire in modo efficiente le connessioni.

La creazione di nuove connessioni client a MySQL richiede tempo e una volta stabilite, queste connessioni occupano risorse del database, anche se inattive. La maggior parte delle applicazioni richiede molte connessioni di breve durata, che generano questa situazione. Di conseguenza sarà disponibile un minor numero di risorse per il carico di lavoro effettivo e le prestazioni saranno ridotte. Un pool di connessioni che riduce le connessioni inattive e riutilizza le connessioni esistenti consente di evitare il problema. Per informazioni sulla configurazione di ProxySQL, visitare il [post di blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) di Microsoft.

>[!Note]
>ProxySQL è uno strumento open source della community. È supportato da Microsoft in base al massimo sforzo. Per ottenere supporto di produzione con linee guida autorevoli, è possibile valutare e contattare il [supporto](https://proxysql.com/services/support/)tecnico di ProxySQL.

### <a name="max_heap_table_size"></a>max_heap_table_size

Per altre informazioni su questo parametro, esaminare la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size).

|**Piano tariffario**|**vCore**|**Valore predefinito (byte)**|**Valore minimo (byte)**|**Valore massimo (byte)**|
|---|---|---|---|---|
|Basic|1|Non configurabile nel livello Basic|N/D|N/D|
|Basic|2|Non configurabile nel livello Basic|N/D|N/D|
|Utilizzo generico|2|16777216|16384|268435455|
|Utilizzo generico|4|16777216|16384|536870912|
|Utilizzo generico|8|16777216|16384|1073741824|
|Utilizzo generico|16|16777216|16384|2147483648|
|Utilizzo generico|32|16777216|16384|4294967295|
|Utilizzo generico|64|16777216|16384|4294967295|
|Con ottimizzazione per la memoria|2|16777216|16384|536870912|
|Con ottimizzazione per la memoria|4|16777216|16384|1073741824|
|Con ottimizzazione per la memoria|8|16777216|16384|2147483648|
|Con ottimizzazione per la memoria|16|16777216|16384|4294967295|
|Con ottimizzazione per la memoria|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

La cache della query è disattivata per impostazione predefinita. Per abilitare la cache delle query, configurare il parametro `query_cache_type`. 

Per altre informazioni su questo parametro, esaminare la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size).

> [!NOTE]
> La cache delle query è deprecata a partire da MySQL 5.7.20 ed è stata rimossa in MySQL 8.0

|**Piano tariffario**|**vCore**|**Valore predefinito (byte)**|**Valore minimo (byte)**|* * Valore massimo * *|
|---|---|---|---|---|
|Basic|1|Non configurabile nel livello Basic|N/D|N/D|
|Basic|2|Non configurabile nel livello Basic|N/D|N/D|
|Utilizzo generico|2|0|0|16777216|
|Utilizzo generico|4|0|0|33554432|
|Utilizzo generico|8|0|0|67108864|
|Utilizzo generico|16|0|0|134217728|
|Utilizzo generico|32|0|0|134217728|
|Utilizzo generico|64|0|0|134217728|
|Con ottimizzazione per la memoria|2|0|0|33554432|
|Con ottimizzazione per la memoria|4|0|0|67108864|
|Con ottimizzazione per la memoria|8|0|0|134217728|
|Con ottimizzazione per la memoria|16|0|0|134217728|
|Con ottimizzazione per la memoria|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

Per impostazione predefinita, il lower_case_table_name è impostato su 1 ed è possibile aggiornare questo parametro in MySQL 5,6 e MySQL 5,7

Per altre informazioni su questo parametro, esaminare la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names).

> [!NOTE]
> In MySQL 8,0 il lower_case_table_name è impostato su 1 per impostazione predefinita e non è possibile modificarlo.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Se viene visualizzato un errore simile a "dimensioni di riga troppo grandi (> 8126)", potrebbe essere necessario disattivare il parametro **innodb_strict_mode** . Il parametro Server **innodb_strict_mode** non può essere modificato globalmente a livello di server perché le dimensioni dei dati delle righe sono maggiori di 8K, i dati verranno troncati senza errori che comportano una potenziale perdita di dati. Si consiglia di modificare lo schema in modo che corrisponda al limite delle dimensioni della pagina. 

Questo parametro può essere impostato a livello di sessione usando `init_connect` . Per impostare **innodb_strict_mode** a livello di sessione, fare riferimento a [parametro impostazione non elencato](./howto-server-parameters.md#setting-parameters-not-listed).

> [!NOTE]
> Se si dispone di un server di replica in lettura, impostando **innodb_strict_mode** su disattivato a livello di sessione in un server di origine si interrompe la replica. Se sono state lette repliche, è consigliabile mantenere il parametro impostato su disattivato.

### <a name="sort_buffer_size"></a>sort_buffer_size

Per altre informazioni su questo parametro, esaminare la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size).

|**Piano tariffario**|**vCore**|**Valore predefinito (byte)**|**Valore minimo (byte)**|**Valore massimo (byte)**|
|---|---|---|---|---|
|Basic|1|Non configurabile nel livello Basic|N/D|N/D|
|Basic|2|Non configurabile nel livello Basic|N/D|N/D|
|Utilizzo generico|2|524288|32768|4194304|
|Utilizzo generico|4|524288|32768|8388608|
|Utilizzo generico|8|524288|32768|16777216|
|Utilizzo generico|16|524288|32768|33554432|
|Utilizzo generico|32|524288|32768|33554432|
|Utilizzo generico|64|524288|32768|33554432|
|Con ottimizzazione per la memoria|2|524288|32768|8388608|
|Con ottimizzazione per la memoria|4|524288|32768|16777216|
|Con ottimizzazione per la memoria|8|524288|32768|33554432|
|Con ottimizzazione per la memoria|16|524288|32768|33554432|
|Con ottimizzazione per la memoria|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

Per altre informazioni su questo parametro, esaminare la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size).

|**Piano tariffario**|**vCore**|**Valore predefinito (byte)**|**Valore minimo (byte)**|**Valore massimo (byte)**|
|---|---|---|---|---|
|Basic|1|Non configurabile nel livello Basic|N/D|N/D|
|Basic|2|Non configurabile nel livello Basic|N/D|N/D|
|Utilizzo generico|2|16777216|1024|67108864|
|Utilizzo generico|4|16777216|1024|134217728|
|Utilizzo generico|8|16777216|1024|268435456|
|Utilizzo generico|16|16777216|1024|536870912|
|Utilizzo generico|32|16777216|1024|1073741824|
|Utilizzo generico|64|16777216|1024|1073741824|
|Con ottimizzazione per la memoria|2|16777216|1024|134217728|
|Con ottimizzazione per la memoria|4|16777216|1024|268435456|
|Con ottimizzazione per la memoria|8|16777216|1024|536870912|
|Con ottimizzazione per la memoria|16|16777216|1024|1073741824|
|Con ottimizzazione per la memoria|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

Al momento della distribuzione iniziale, un server Azure per MySQL include le tabelle di sistema per le informazioni sul fuso orario, ma queste tabelle non vengono popolate. Per popolare le tabelle di fuso orario, è possibile chiamare la stored procedure `mysql.az_load_timezone` da uno strumento come la riga di comando di MySQL o MySQL Workbench. Fare riferimento agli articoli sul [portale di Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) o l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) per le modalità in cui è possibile chiamare la stored procedure e impostare i fusi orari a livello globale o di sessione.

## <a name="non-configurable-server-parameters"></a>Elenco di parametri del server non configurabili

I parametri del server seguenti non sono configurabili nel servizio:

|**Parametro**|**Valore fisso**|
| :------------------------ | :-------- |
|innodb_file_per_table nel livello Basic|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Altre variabili non elencate qui sono impostate sui valori predefiniti di MySQL. Per i valori predefiniti, vedere la documentazione di MySQL per le versioni [8,0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html), [5,7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)e [5,6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) . 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [configurare i parametri del server con il portale di Azure](./howto-server-parameters.md)
- Informazioni su come [configurare i parametri del server con l'interfaccia della](./howto-configure-server-parameters-using-cli.md) riga di comando di Azure
- Informazioni su come [configurare i parametri del server con PowerShell](./howto-configure-server-parameters-using-powershell.md)