---
title: Parametri del server-database di Azure per MySQL-server flessibile
description: Questo argomento fornisce le linee guida per la configurazione dei parametri del server in database di Azure per MySQL-server flessibile.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 58978f120578afeca129b0d8928713835def8418
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94496745"
---
# <a name="server-parameters-in-azure-database-for-mysql---flexible-server"></a>Parametri del server nel database di Azure per MySQL-server flessibile

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo articolo fornisce considerazioni e linee guida per la configurazione dei parametri del server in database di Azure per il server flessibile MySQL.

## <a name="what-are-server-variables"></a>Che cosa sono le variabili server? 

Il motore MySQL fornisce diverse [variabili/parametri del server](https://dev.mysql.com/doc/refman/5.7/en/server-option-variable-reference.html) che possono essere usati per configurare e ottimizzare il comportamento del motore. Alcuni parametri possono essere impostati dinamicamente in fase di esecuzione mentre altri sono "statici", richiedendo un riavvio del server per poter essere applicati.

Il server flessibile database di Azure per MySQL espone la possibilità di modificare il valore dei vari parametri del server MySQL usando il [portale di Azure](./how-to-configure-server-parameters-portal.md) e l'interfaccia della riga di comando di [Azure](./how-to-configure-server-parameters-cli.md) in base alle esigenze del carico di lavoro.

## <a name="configurable-server-parameters"></a>Parametri del server configurabili

È possibile gestire la configurazione del server flessibile del database di Azure per MySQL usando i parametri del server. Quando si crea il server, i parametri del server sono configurati con il valore predefinito e consigliato. Il pannello dei parametri del server in portale di Azure Mostra sia i parametri del server modificabili che quelli non modificabili. I parametri del server non modificabili sono in grigio.

L'elenco di parametri del server supportati è in continua crescita. Utilizzare la scheda parametri server della portale di Azure per visualizzare l'elenco completo e configurare i valori dei parametri del server.

Vedere le sezioni seguenti per altre informazioni sui limiti dei diversi parametri del server aggiornati di frequente. I limiti sono determinati dal livello di calcolo e dalle dimensioni (VCore) del server.

> [!NOTE]
> Se si desidera modificare un parametro del server che non è modificabile ma si desidera visualizzarlo come modificabile per l'ambiente in uso, aprire un elemento [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) o votare se il feedback esiste già, che può contribuire alla priorità.

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

Nel database di Azure per il server flessibile MySQL i log binari sono sempre abilitati, ovvero `log_bin` è impostato su on. Se si vogliono usare i trigger, verrà generato un errore simile a quello in *cui non si dispone del privilegio Super e la registrazione binaria è abilitata (è possibile usare la variabile meno sicura `log_bin_trust_function_creators` )*. 

Il formato di registrazione binario è sempre **Row** e tutte le connessioni al server utilizzano **sempre** la registrazione binaria basata su righe. Con la registrazione binaria basata su righe, non esistono problemi di sicurezza e la registrazione binaria non può essere interrotta, quindi è possibile impostare in modo sicuro [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) su **true**.

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

Per altre informazioni su questo parametro, esaminare la [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size).

|**Piano tariffario**|**vCore**|**Dimensioni memoria (GiB)**|**Valore predefinito (byte)**|**Valore minimo (byte)**|**Valore massimo (byte)**|
|---|---|---|---|---|---|
|Con espansione in sequenza (B1S)|1|1|134217728|33554432|134217728|
|Con espansione in sequenza (B1ms)|1|2|536870912|134217728|536870912|
|Burst|2|4|2147483648|134217728|2147483648|
|Utilizzo generico|2|8|6442450944|134217728|6442450944|
|Utilizzo generico|4|16|12884901888|134217728|12884901888|
|Utilizzo generico|8|32|25769803776|134217728|25769803776|
|Utilizzo generico|16|64|51539607552|134217728|51539607552|
|Utilizzo generico|32|128|103079215104|134217728|103079215104|
|Utilizzo generico|48|192|154618822656|134217728|154618822656|
|Utilizzo generico|64|256|206158430208|134217728|206158430208|
|Con ottimizzazione per la memoria|2|16|12884901888|134217728|12884901888|
|Con ottimizzazione per la memoria|4|32|25769803776|134217728|25769803776|
|Con ottimizzazione per la memoria|8|64|51539607552|134217728|51539607552|
|Con ottimizzazione per la memoria|16|128|103079215104|134217728|103079215104|
|Con ottimizzazione per la memoria|32|256|206158430208|134217728|206158430208|
|Con ottimizzazione per la memoria|48|384|309237645312|134217728|309237645312|
|Con ottimizzazione per la memoria|64|504|405874409472|134217728|405874409472|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

MySQL archivia la tabella InnoDB in spazi di tabella diversi in base alla configurazione specificata durante la creazione della tabella. Lo [spazio di tabella del sistema](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) è l'area di archiviazione per il dizionario dei dati InnoDB. Uno [spazio di tabella di un file per tabella](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) contiene dati e indici per una sola tabella InnoDB e viene archiviato nel file system del file di dati in uso. Questo comportamento è controllato dal parametro del server `innodb_file_per_table`. Impostando `innodb_file_per_table` su `OFF` InnoDB crea tabelle nello spazio di tabella del sistema. Altrimenti, InnoDB crea tabelle in spazi di tabella di un file per tabella.

Il server flessibile database di Azure per MySQL supporta al massimo **4 TB** in un unico file di dati. Se le dimensioni del database sono maggiori di 4 TB, è necessario creare la tabella in [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) tabella. Se le dimensioni di una singola tabella sono maggiori di 4 TB, è necessario utilizzare la tabella di partizione.

### <a name="max_connections"></a>max_connections

Il valore di max_connection è determinato dalle dimensioni della memoria del server. 

|**Piano tariffario**|**vCore**|**Dimensioni memoria (GiB)**|**Valore predefinito**|**Valore minimo**|**Valore massimo**|
|---|---|---|---|---|---|
|Con espansione in sequenza (B1S)|1|1|85|10|171|
|Con espansione in sequenza (B1ms)|1|2|171|10|341|
|Burst|2|4|341|10|683|
|Utilizzo generico|2|8|683|10|1365|
|Utilizzo generico|4|16|1365|10|2731|
|Utilizzo generico|8|32|2731|10|5461|
|Utilizzo generico|16|64|5461|10|10923|
|Utilizzo generico|32|128|10923|10|21845|
|Utilizzo generico|48|192|16384|10|32768|
|Utilizzo generico|64|256|21845|10|43691|
|Con ottimizzazione per la memoria|2|16|1365|10|2731|
|Con ottimizzazione per la memoria|4|32|2731|10|5461|
|Con ottimizzazione per la memoria|8|64|5461|10|10923|
|Con ottimizzazione per la memoria|16|128|10923|10|21845|
|Con ottimizzazione per la memoria|32|256|21845|10|43691|
|Con ottimizzazione per la memoria|48|384|32768|10|65536|
|Con ottimizzazione per la memoria|64|504|43008|10|86016|

Quando le connessioni superano il limite, è possibile che venga visualizzato l'errore seguente:
> ERROR 1040 (08004): Troppe connessioni

> [!IMPORTANT]
> Per un'esperienza ottimale, è consigliabile usare un pool di connessioni come ProxySQL per gestire in modo efficiente le connessioni.

La creazione di nuove connessioni client a MySQL richiede tempo e una volta stabilite, queste connessioni occupano risorse del database, anche se inattive. La maggior parte delle applicazioni richiede molte connessioni di breve durata, che generano questa situazione. Di conseguenza sarà disponibile un minor numero di risorse per il carico di lavoro effettivo e le prestazioni saranno ridotte. Un pool di connessioni che riduce le connessioni inattive e riutilizza le connessioni esistenti consente di evitare il problema. Per informazioni sulla configurazione di ProxySQL, visitare il [post di blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) di Microsoft.

>[!Note]
>ProxySQL è uno strumento open source della community. È supportato da Microsoft in base al massimo sforzo. Per ottenere supporto di produzione con linee guida autorevoli, è possibile valutare e contattare il [supporto](https://proxysql.com/services/support/)tecnico di ProxySQL.

### <a name="innodb_strict_mode"></a>innodb_strict_mode

Se viene visualizzato un errore simile a "dimensioni di riga troppo grandi (> 8126)", potrebbe essere necessario disattivare il parametro **innodb_strict_mode**. Il parametro Server **innodb_strict_mode** non può essere modificato globalmente a livello di server, perché se le dimensioni dei dati delle righe sono maggiori di 8K, i dati verranno troncati senza errori e ciò può causare la perdita di dati. Si consiglia di modificare lo schema in modo da adattarlo al limite delle dimensioni della pagina. 

Questo parametro può essere impostato a livello di sessione usando `init_connect` . Per impostare **innodb_strict_mode** a livello di sessione, fare riferimento a [parametro impostazione non elencato](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters).

> [!NOTE]
> Se si dispone di un server di replica in lettura, impostando **innodb_strict_mode** su disattivato a livello di sessione in un server di origine si interrompe la replica. Se sono state lette repliche, è consigliabile mantenere il parametro impostato su disattivato.

### <a name="time_zone"></a>time_zone

Al momento della distribuzione iniziale, un server flessibile di Azure per MySQL include le tabelle di sistema per le informazioni sul fuso orario, ma queste tabelle non vengono popolate. Per popolare le tabelle di fuso orario, è possibile chiamare la stored procedure `mysql.az_load_timezone` da uno strumento come la riga di comando di MySQL o MySQL Workbench. Fare riferimento agli articoli sul [portale di Azure](./how-to-configure-server-parameters-portal.md#working-with-the-time-zone-parameter) o l'[interfaccia della riga di comando di Azure](./how-to-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) per le modalità in cui è possibile chiamare la stored procedure e impostare i fusi orari a livello globale o di sessione.

## <a name="non-modifiable-server-parameters"></a>Parametri del server non modificabili

Il pannello parametro Server nel portale di Azure Mostra sia i parametri del server modificabili che quelli non modificabili. I parametri del server non modificabili sono in grigio. Se si vuole configurare un parametro Server non modificabile a livello di sessione, vedere l'articolo [portale di Azure](./how-to-configure-server-parameters-portal.md#setting-non-modifiable-server-parameters) o l'interfaccia della riga di comando di [Azure](./how-to-configure-server-parameters-cli.md#setting-non-modifiable-server-parameters) per impostare il parametro a livello di connessione usando `init_connect` .

## <a name="next-steps"></a>Passaggi successivi

- Come configurare [i parametri del server nel portale di Azure](./how-to-configure-server-parameters-portal.md)
- Come configurare i [parametri del server nell'interfaccia della riga di comando di Azure](./how-to-configure-server-parameters-cli.md)
