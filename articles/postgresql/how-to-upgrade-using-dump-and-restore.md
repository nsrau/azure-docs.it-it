---
title: Eseguire l'aggiornamento tramite dump e Restore-database di Azure per PostgreSQL-server singolo
description: Descrive i metodi di aggiornamento offline usando i database dump e Restore per eseguire la migrazione a una versione successiva di database di Azure per PostgreSQL-server singolo.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 42bbe1c9f4056ae0dae0ccd59b452db90a7c63c5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493662"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Aggiornare il database PostgreSQL con dump e ripristino

È possibile aggiornare il server PostgreSQL distribuito nel database di Azure per PostgreSQL-server singolo migrando i database in un server di versione principale più elevato usando i metodi seguenti.
* Metodo **offline** con [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) PostgreSQL e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) che comporta tempi di inattività per la migrazione dei dati. Questo documento riguarda questo metodo di aggiornamento/migrazione.
* Metodo **online** con il [servizio migrazione del database](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS). Questo metodo consente una migrazione ridotta del tempo di inattività e mantiene il database di destinazione sincronizzato con con l'origine ed è possibile scegliere quando eseguire il troncamento. Tuttavia, esistono alcuni prerequisiti e restrizioni da affrontare per l'uso di DMS. Per informazioni dettagliate, vedere la [documentazione di DMS](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md). 

 Nella tabella seguente vengono forniti alcuni consigli basati su scenari e dimensioni del database.

| **Database/scenario** | **Dump/ripristino (offline)** | **DMS (online)** |
| ------ | :------: | :-----: |
| Si dispone di un database di piccole dimensioni che può permettersi tempi di inattività per l'aggiornamento  | X | |
| Database di piccole dimensioni (< 10 GB)  | X | X | 
| Database di piccole e medie dimensioni (10 GB-100 GB) | X | X |
| Database di grandi dimensioni (> 100 GB) |  | X |
| Può permettersi tempi di inattività per l'aggiornamento (indipendentemente dalle dimensioni del database) | X |  |
| È possibile risolvere i [prerequisiti](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites)DMS, incluso un riavvio? |  | X |
| È possibile evitare DDL e tabelle non registrate durante il processo di aggiornamento? | |  X |

Questa guida fornisce alcune metodologie ed esempi di migrazione offline per illustrare come è possibile eseguire la migrazione dal server di origine al server di destinazione che esegue una versione di PostgreSQL superiore.

> [!NOTE]
> Il dump e il ripristino di PostgreSQL possono essere eseguiti in molti modi. È possibile scegliere di eseguire la migrazione utilizzando uno dei metodi forniti in questa guida oppure scegliere eventuali modi alternativi per soddisfare le proprie esigenze. Per una sintassi dettagliata di dump e ripristino con parametri aggiuntivi, vedere gli articoli [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Prerequisiti per l'uso del dump e del ripristino con database di Azure per PostgreSQL
 
Per eseguire questa guida, è necessario:

- Un database PostgreSQL di **origine** che esegue 9,5, 9,6 o 10 che si desidera aggiornare
- Un server di database PostgreSQL di **destinazione** con la versione principale desiderata [database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md). 
- Sistema client PostgreSQL per eseguire i comandi dump e Restore.
  - Può trattarsi di un client Linux o Windows con PostgreSQL installato e con [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) utilità della riga di comando installate. 
  - In alternativa, è possibile usare [Azure cloud Shell](https://shell.azure.com) o facendo clic sul Azure cloud Shell sulla barra dei menu in alto a destra nella [portale di Azure](https://portal.azure.com). `az login`Prima di eseguire i comandi dump e Restore, sarà necessario eseguire l'accesso all'account.
- Il client PostgreSQL è preferibilmente in esecuzione nella stessa area dei server di origine e di destinazione. 


## <a name="additional-details-and-considerations"></a>Ulteriori dettagli e considerazioni
- È possibile trovare la stringa di connessione per i database di origine e di destinazione facendo clic sulle "stringhe di connessione" dal portale. 
- È possibile che nel server sia in esecuzione più di un database. È possibile trovare l'elenco dei database connettendosi al server di origine e eseguendo `\l` .
- Creare database corrispondenti nel server di database di destinazione.
- È possibile ignorare l'aggiornamento `azure_maintenance` o i database modello.
- Per determinare se il database è adatto a questa modalità di migrazione, fare riferimento alle tabelle precedenti.
- Se si desidera utilizzare Azure Cloud Shell, tenere presente che la sessione scade dopo 20 minuti. Se le dimensioni del database sono < 10 GB, potrebbe essere possibile completare l'aggiornamento senza timeout della sessione. In caso contrario, potrebbe essere necessario lasciare aperta la sessione in altro modo, ad esempio premendo il <Enter> tasto una volta in 10-15 minuti. 


## <a name="example-database-used-in-this-guide"></a>Database di esempio usato in questa guida

In questa guida vengono usati i nomi di database e i server di origine e di destinazione seguenti per illustrare gli esempi.

 | **Descrizione** | **Valore** |
 | ------- | ------- |
 | Server di origine (v 9.5) | pg-95.postgres.database.azure.com |
 | Database di origine | bench5gb |
 | Dimensioni database di origine | 5 GB |
 | Nome utente di origine | pg@pg-95 |
 | Server di destinazione (V11) | pg-11.postgres.database.azure.com |
 | Database di destinazione | bench5gb |
 | Nome utente di destinazione | pg@pg-11 |

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Aggiornare i database usando metodi di migrazione offline
È possibile scegliere di usare uno dei metodi descritti in questa sezione per gli aggiornamenti. Durante l'esecuzione delle attività, è possibile utilizzare i suggerimenti seguenti.

- Se si utilizza la stessa password per il database di origine e di destinazione, è possibile impostare la `PGPASSWORD=yourPassword` variabile di ambiente.  Non è quindi necessario specificare una password ogni volta che si eseguono comandi come PSQL, pg_dump e pg_restore.  Analogamente, è possibile configurare altre variabili `PGUSER` , ad esempio, e `PGSSLMODE` così via. vedere le [variabili di ambiente PostgreSQL](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Se il server PostgreSQL richiede connessioni TLS/SSL (per impostazione predefinita nel database di Azure per i server PostgreSQL), impostare una variabile di ambiente in `PGSSLMODE=require` modo che lo strumento di pg_restore si connetta con TLS. Senza TLS, l'errore può essere letto  `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- Nella riga di comando di Windows, eseguire il comando `SET PGSSLMODE=require` prima di eseguire il comando pg_restore. Nella riga di comando di Linux o Bash, eseguire il comando `export PGSSLMODE=require` prima di eseguire il comando pg_restore.

### <a name="method-1-migrate-using-dump-file"></a>Metodo 1: eseguire la migrazione utilizzando il file dump

Questo metodo prevede due passaggi. Per prima cosa è necessario creare un dump dal server di origine. Il secondo passaggio consiste nel ripristinare il file di dump nel server di destinazione. Per ulteriori informazioni, vedere la documentazione relativa alla [migrazione tramite dump e ripristino](howto-migrate-using-dump-and-restore.md) . Si tratta del metodo consigliato se si dispone di database di grandi dimensioni e il sistema client dispone di spazio di archiviazione sufficiente per archiviare il file di dump.

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>Metodo 2: eseguire la migrazione utilizzando il flusso dei dati di dump nel database di destinazione

Se non si ha un client PostgreSQL o si vuole usare Azure Cloud Shell, è possibile usare questo metodo. Il dump del database viene trasmesso direttamente al server di database di destinazione e non archivia il dump nel client. Questo può quindi essere usato con un client con archiviazione limitata ed è possibile eseguirlo anche dal Azure Cloud Shell. 

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

 Ad esempio, la tabella seguente illustra il tempo impiegato per eseguire la migrazione tramite il metodo di dump del flusso. I dati di esempio vengono popolati con [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Poiché il database può avere un numero diverso di oggetti con dimensioni diverse rispetto a quelle delle tabelle e degli indici generati da pgbench, è consigliabile eseguire il test del dump e del ripristino del database per comprendere il tempo effettivo necessario per aggiornare il database. 

| **Dimensioni database** | **Circa tempo impiegato** | 
| ----- | ------ |
| 1 GB  | 1-2 minuti |
| 5 GB | 8-10 minuti |
| 10 GB | 15-20 minuti |
| 50 GB | da 1 a 1,5 ore |
| 100 GB | 2,5-3 ore|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>Metodo 3: eseguire la migrazione con dump e ripristino paralleli 

È possibile prendere in considerazione questo metodo se nel database sono presenti poche tabelle di dimensioni maggiori e si desidera parallelizzare il processo di dump e ripristino per tale database. Per gestire i dump del backup, è inoltre necessario disporre di spazio di archiviazione sufficiente nel sistema client. Questo processo di dump e ripristino in parallelo riduce il consumo di tempo necessario per completare l'intera migrazione. Ad esempio, il database pgbench da 50 GB che ha richiesto da 1 a 1,5 ore di migrazione è stato completato con il metodo 1 e 2 ha richiesto meno di 30 minuti usando questo metodo.

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