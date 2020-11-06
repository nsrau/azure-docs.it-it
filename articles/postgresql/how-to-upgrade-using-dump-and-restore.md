---
title: Eseguire l'aggiornamento tramite dump e Restore-database di Azure per PostgreSQL-server singolo
description: Descrive due metodi per eseguire il dump e il ripristino dei database per la migrazione a una versione successiva di database di Azure per PostgreSQL-server singolo.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.openlocfilehash: 6dfcf0b2ec1d46821007123908a8e7ba8df29744
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421765"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Aggiornare il database PostgreSQL con dump e ripristino

Nel database di Azure per PostgreSQL-server singolo è consigliabile aggiornare il motore di database PostgreSQL a una versione principale più elevata usando uno dei metodi seguenti:
* Metodo offline con [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html)PostgreSQL. In questo metodo è necessario innanzitutto eseguire il dump dal server di origine e quindi ripristinare il dump nel server di destinazione.
* Metodo online con il [**servizio migrazione del database**](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal) (DMS). Questo metodo mantiene il database di destinazione sincronizzato con con l'origine ed è possibile scegliere quando eseguire il troncamento. Tuttavia, esistono alcuni prerequisiti e restrizioni da risolvere. 

È possibile utilizzare la raccomandazione seguente per decidere tra i metodi online e offline per eseguire gli aggiornamenti della versione principale.

| **Database** | **Dump/ripristino (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Si dispone di un database di piccole dimensioni che può permettersi tempi di inattività per l'aggiornamento  | X | |
| Database di piccole dimensioni (< 10 GB)  | X | X | 
| Database di piccole e medie dimensioni (10 GB-100 GB) | X | X |
| Database di grandi dimensioni (> 100 GB) |  | X |
| Può permettersi tempi di inattività per l'aggiornamento (indipendentemente dalle dimensioni del database) | X |  |
| È possibile risolvere i [prerequisiti DMS,](https://docs.microsoft.com/azure/dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal#prerequisites) incluso il riavvio? |  | X |
| È possibile evitare DDL e tabelle non registrate durante il processo di aggiornamento? | |  X |

Questa guida dettagliata fornisce due metodi di esempio su come aggiornare i database usando i comandi di pg_dump e pg_restore di PostgreSQL. Il processo in questo documento viene definito **aggiornamento** anche se viene eseguita la  **migrazione** del database dal server di origine al server di destinazione. 

> [!NOTE]
> Il dump e il ripristino di PostgreSQL possono essere eseguiti in molti modi. È possibile scegliere di usare metodi diversi rispetto a quelli indicati in questo documento. Ad esempio, per eseguire un dump seguito da un ripristino da un client PostgreSQL, vedere la [documentazione](./howto-migrate-using-dump-and-restore.md) per istruzioni dettagliate e procedure consigliate. Per una sintassi dettagliata di dump e ripristino con parametri aggiuntivi, vedere gli articoli [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-postgresql"></a>Prerequisiti per l'uso di dump e ripristino con Azure PostgreSQL
 
Per eseguire questa guida, è necessario:
- Un database di origine che esegue 9,5, 9,6 o 10 (database di Azure per PostgreSQL – server singolo)
- Server di database di destinazione con la versione principale di PostgreSQL desiderata [database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md). 
- Un sistema client (Linux) con PostgreSQL installato e dotato di [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) utilità della riga di comando installate. 
- In alternativa, è possibile usare [Azure cloud Shell](https://shell.azure.com) o facendo clic sul Azure cloud Shell sulla barra dei menu in alto a destra nella [portale di Azure](https://portal.azure.com). `az login`Prima di eseguire i comandi dump e Restore, sarà necessario eseguire l'accesso all'account.
- La posizione del client PostgreSQL, ad esempio una macchina virtuale preferibilmente in esecuzione nella stessa area dei server di origine e di destinazione. 

## <a name="additional-details-and-considerations"></a>Ulteriori dettagli e considerazioni
- È possibile trovare la stringa di connessione per i database di origine e di destinazione facendo clic sulle "stringhe di connessione" dal portale. 
- È possibile che nel server sia in esecuzione più di un database. È possibile trovare l'elenco dei database connettendosi al server di origine e eseguendo `\l` .
- Creare database corrispondenti nel server di database di destinazione.
- È possibile ignorare l'aggiornamento `azure_maintenance` o i database modello.
- Per determinare se il database è adatto a questa modalità di migrazione, fare riferimento alle tabelle precedenti.
- Se si desidera utilizzare Azure Cloud Shell, la sessione scade dopo 20 minuti. Se le dimensioni del database sono < 10 GB, è possibile completare l'aggiornamento senza timeout. In caso contrario, potrebbe essere necessario lasciare aperta la sessione in altro modo, ad esempio premendo il <Enter> tasto una volta in 10-15 minuti. 

> [!TIP] 
> - Se si utilizza la stessa password per il database di origine e di destinazione, è possibile impostare la `PGPASSWORD=yourPassword` variabile di ambiente.  Non è quindi necessario specificare la password ogni volta che si eseguono comandi come PSQL, pg_dump e pg_restore.  Analogamente, è possibile configurare altre variabili `PGUSER` , ad esempio, e `PGSSLMODE` così via. vedere le [variabili di ambiente PostgreSQL](https://www.postgresql.org/docs/11/libpq-envars.html).
>  
> - Se il server PostgreSQL richiede connessioni TLS/SSL (per impostazione predefinita nel database di Azure per i server PostgreSQL), impostare una variabile di ambiente in `PGSSLMODE=require` modo che lo strumento di pg_restore si connetta con TLS. Senza TLS, l'errore può essere letto  `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> - Nella riga di comando di Windows, eseguire il comando `SET PGSSLMODE=require` prima di eseguire il comando pg_restore. Nella riga di comando di Linux o Bash, eseguire il comando `export PGSSLMODE=require` prima di eseguire il comando pg_restore.

## <a name="example-database-used-in-this-guide"></a>Database di esempio usato in questa guida

 | **Descrizione** | **Valore** |
 | ------- | ------- |
 | Server di origine (v 9.5) | pg-95.postgres.database.azure.com |
 | Database di origine | bench5gb |
 | Dimensioni database di origine | 5 GB |
 | Nome utente di origine | pg@pg-95 |
 | Server di destinazione (V11) | pg-11.postgres.database.azure.com |
 | Database di destinazione | bench5gb |
 | Nome utente di destinazione | pg@pg-11 |

## <a name="method-1-upgrade-with-streaming-backups-to-the-target"></a>Metodo 1: eseguire l'aggiornamento con backup di flusso alla destinazione 

In questo metodo, l'intero dump del database viene trasmesso direttamente al server di database di destinazione e non archivia il dump nel client. Questo può quindi essere usato con un client con archiviazione limitata ed è possibile eseguirlo anche dal Azure Cloud Shell. 

1. Verificare che il database esista nel server di destinazione tramite il `\l` comando. Se il database non esiste, creare il database.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Eseguire il dump e il ripristino come una singola riga di comando usando una pipe. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Ad esempio,

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Al termine del processo di aggiornamento (migrazione), è possibile testare l'applicazione con il server di destinazione. 
4. Ripetere questo processo per tutti i database all'interno del server.

 Nella tabella seguente viene illustrato il tempo impiegato per eseguire l'aggiornamento utilizzando questo metodo. I dati vengono popolati con [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Poiché il database può avere un numero diverso di oggetti con dimensioni diverse rispetto a quelle delle tabelle e degli indici generati da pgbench, è consigliabile eseguire il test del dump e del ripristino del database per comprendere il tempo effettivo necessario per aggiornare il database. 

| **Dimensioni database** | **Circa tempo impiegato** | 
| ----- | ------ |
| 1 GB  | 1-2 minuti |
| 5 GB | 8-10 minuti |
| 10 GB | 15-20 minuti |
| 50 GB | da 1 a 1,5 ore |
| 100 GB | 2,5-3 ore|
   
## <a name="method-2-upgrade-with-parallel-dump-and-restore"></a>Metodo 2: aggiornamento con dump e ripristino paralleli 

Questo metodo è utile se sono presenti più tabelle di dimensioni maggiori nel database e si desidera parallelizzare il dump e il processo di ripristino per tale database. È necessario disporre di spazio di archiviazione su disco locale sufficiente per gestire i dump del backup per i database. Questo processo di dump e ripristino in parallelo riduce il consumo di tempo necessario per completare l'intera migrazione/aggiornamento. Ad esempio, il database pgbench da 50 GB che ha richiesto da 1 a 1,5 ore di migrazione è stato completato in meno di 30 minuti.

1. Per ogni database nel server di origine, creare un database corrispondente nel server di destinazione.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Ad esempio,
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Eseguire il comando pg_dump in un formato di directory con numero di processi = 4 (numero di tabelle nel database). Con un livello di calcolo più ampio e con più tabelle, è possibile aumentarlo a un numero più elevato. Questo pg_dump creerà una directory per archiviare i file compressi per ogni processo.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Ad esempio,
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. Ripristinare quindi il backup nel server di destinazione.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Ad esempio,
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> Il processo descritto in questo documento può essere usato anche per aggiornare il database di Azure per PostgreSQL, un server flessibile, disponibile in anteprima. La differenza principale è che la stringa di connessione per la destinazione del server flessibile è senza `@dbName` .  Se, ad esempio, il nome utente è `pg` , il nome utente del singolo server nella stringa di connessione sarà `pg@pg-95` , mentre con server flessibile, sarà possibile utilizzare semplicemente `pg` .

## <a name="next-steps"></a>Passaggi successivi

- Una volta soddisfatta la funzione di database di destinazione, è possibile eliminare il server di database precedente. 
- Se si desidera utilizzare lo stesso endpoint di database del server di origine, dopo aver eliminato il vecchio server di database di origine, è possibile creare una replica di lettura con il nome del server di database precedente. Una volta stabilito lo stato stabile, è possibile arrestare la replica, in modo da innalzare di livello il server di replica come server indipendente. Per ulteriori informazioni, vedere [Replica](./concepts-read-replicas.md).
- Ricordarsi di testare e validare questi comandi in un ambiente di test prima di usarli in produzione.
