---
title: Parametri del server-database di Azure per MariaDB
description: Questo argomento fornisce le linee guida per la configurazione dei parametri del server nel database di Azure per MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: b5064e3cef7def1aca5aa0c97d031d519fd610cf
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91626395"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>Parametri del server nel database di Azure per MariaDB

Questo articolo fornisce considerazioni e linee guida per la configurazione dei parametri del server nel database di Azure per MariaDB.

## <a name="what-are-server-parameters"></a>Che cosa sono i parametri del server? 

Il motore MariaDB fornisce molte variabili server/parametri diversi che possono essere usati per configurare e ottimizzare il comportamento del motore. Alcuni parametri possono essere impostati dinamicamente in fase di esecuzione mentre altri sono "statici", richiedendo un riavvio del server per poter essere applicati.

Il database di Azure per MariaDB espone la possibilità di modificare il valore dei vari parametri del server MariaDB usando il [portale di Azure](./howto-server-parameters.md), l'interfaccia della riga di comando di [Azure](./howto-configure-server-parameters-cli.md)e [PowerShell](./howto-configure-server-parameters-using-powershell.md) per soddisfare le esigenze del carico di lavoro.

## <a name="configurable-server-parameters"></a>Parametri del server configurabili

L'elenco di parametri del server supportati è in continua crescita. Utilizzare la scheda parametri server della portale di Azure per visualizzare l'elenco completo e configurare i valori dei parametri del server.

Vedere le sezioni seguenti per altre informazioni sui limiti dei diversi parametri del server aggiornati di frequente. I limiti sono determinati dal piano tariffario e dal Vcore del server.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Nel database di Azure per MariaDB i log binari sono sempre abilitati, ovvero `log_bin` è impostato su on. Se si vogliono usare i trigger, verrà generato un errore simile a quello in *cui non si dispone del privilegio Super e la registrazione binaria è abilitata (è possibile usare la variabile meno sicura `log_bin_trust_function_creators` )*.

Il formato di registrazione binario è sempre **Row** e tutte le connessioni al server utilizzano **sempre** la registrazione binaria basata su righe. Con la registrazione binaria basata su righe, non esistono problemi di sicurezza e la registrazione binaria non può essere interrotta, quindi è possibile impostare in modo sicuro [`log_bin_trust_function_creators`](https://mariadb.com/docs/reference/mdb/system-variables/log_bin_trust_function_creators/) su **true**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Per altre informazioni su questo parametro, esaminare la [documentazione di MariaDB](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size).

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

MariaDB archivia la tabella InnoDB in spazi di tabella diversi in base alla configurazione specificata durante la creazione della tabella. Lo [spazio di tabella del sistema](https://mariadb.com/kb/en/innodb-system-tablespaces/) è l'area di archiviazione per il dizionario dei dati InnoDB. Uno [spazio di tabella di un file per tabella](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/) contiene dati e indici per una sola tabella InnoDB e viene archiviato nel file system del file di dati in uso. Questo comportamento è controllato dal parametro del server `innodb_file_per_table`. Impostando `innodb_file_per_table` su `OFF` InnoDB crea tabelle nello spazio di tabella del sistema. Altrimenti, InnoDB crea tabelle in spazi di tabella di un file per tabella.

Database di Azure per MariaDB supporta al massimo **1 TB** in un unico file di dati. Se le dimensioni del database sono maggiori di 1 TB, è necessario creare la tabella nello spazio di tabella [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table). Se si dispone di una singola tabella di dimensioni superiori a 1 TB, è necessario usare la tabella di partizione.

### <a name="join_buffer_size"></a>join_buffer_size

Per altre informazioni su questo parametro, esaminare la [documentazione di MariaDB](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size).

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

La creazione di nuove connessioni client a MariaDB richiede tempo e una volta stabilite, queste connessioni occupano risorse del database, anche se inattive. La maggior parte delle applicazioni richiede molte connessioni di breve durata, che generano questa situazione. Di conseguenza sarà disponibile un minor numero di risorse per il carico di lavoro effettivo e le prestazioni saranno ridotte. Un pool di connessioni che riduce le connessioni inattive e riutilizza le connessioni esistenti consente di evitare il problema. Per informazioni sulla configurazione di ProxySQL, visitare il [post di blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) di Microsoft.

>[!Note]
>ProxySQL è uno strumento open source della community. È supportato da Microsoft in base al massimo sforzo. Per ottenere supporto di produzione con linee guida autorevoli, è possibile valutare e contattare il [supporto](https://proxysql.com/services/support/)tecnico di ProxySQL.

### <a name="max_heap_table_size"></a>max_heap_table_size

Per altre informazioni su questo parametro, esaminare la [documentazione di MariaDB](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size).

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

La cache di query è abilitata per impostazione predefinita in MariaDB con il `have_query_cache` parametro. 

Per altre informazioni su questo parametro, esaminare la [documentazione di MariaDB](https://mariadb.com/kb/en/server-system-variables/#query_cache_size).

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

### <a name="sort_buffer_size"></a>sort_buffer_size

Per altre informazioni su questo parametro, esaminare la [documentazione di MariaDB](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size).

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

Per altre informazioni su questo parametro, esaminare la [documentazione di MariaDB](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size).

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

Al momento della distribuzione iniziale, un server Azure per MariaDB include le tabelle di sistema per le informazioni sul fuso orario, ma queste tabelle non vengono popolate. Per popolare le tabelle di fuso orario, è possibile chiamare la stored procedure `mysql.az_load_timezone` da uno strumento come la riga di comando di MySQL o MySQL Workbench. Fare riferimento agli articoli sul [portale di Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) o l'[interfaccia della riga di comando di Azure](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) per le modalità in cui è possibile chiamare la stored procedure e impostare i fusi orari a livello globale o di sessione.

## <a name="non-configurable-server-parameters"></a>Elenco di parametri del server non configurabili

I parametri del server seguenti non sono configurabili nel servizio:

|**Parametro**|**Valore fisso**|
| :------------------------ | :-------- |
|innodb_file_per_table nel livello Basic|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Altri parametri non elencati qui sono impostati sui propri valori predefiniti per [MariaDB](https://mariadb.com/kb/en/server-system-variables/).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [configurare i parametri del server con il portale di Azure](./howto-server-parameters.md)
- Informazioni su come [configurare i parametri del server con l'interfaccia della](./howto-configure-server-parameters-cli.md) riga di comando di Azure
- Informazioni su come [configurare i parametri del server con PowerShell](./howto-configure-server-parameters-using-powershell.md)