---
title: Come creare un file di dump ed eseguire il ripristino in Database di Azure per PostgreSQL
description: Descrive come estrarre un database PostgreSQL in un file di dump ed eseguire il ripristino da un file creato da pg_dump in Database di Azure per PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: d406132c4e359c78567ae47a3acba5b73aa39820
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420343"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Eseguire la migrazione del database PostgreSQL usando dump e ripristino
È possibile usare [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) per estrarre un database PostgreSQL in un file di dump e [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) per ripristinare il database PostgreSQL da un file di archivio creato da pg_dump.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un [server di Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md) con le regole del firewall per consentire l'accesso e il database sottostante.
- Utilità della riga di comando [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html) installate

Seguire questi passaggi per eseguire il dump e il ripristino del database PostgreSQL:

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Creare un file di dump usando pg_dump che contiene i dati da caricare
Per eseguire il backup di un database PostgreSQL in locale o in una macchina virtuale, eseguire questo comando:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Se ad esempio è presente un server locale che contiene un database denominato **testdb**, eseguire:
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```


## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Ripristinare i dati nel database di Azure per PostrgeSQL di destinazione usando pg_restore
Dopo avere creato il database di destinazione, è possibile usare il comando pg_restore e il parametro -d, --dbname per ripristinare i dati nel database di destinazione dal file di dump.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Se si include il parametro --no-owner, tutti gli oggetti creati durante il ripristino saranno di proprietà dell'utente specificato con --username. Per altre informazioni, vedere la documentazione ufficiale di PostgreSQL per [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

> [!NOTE]
> Se il server PostgreSQL richiede connessioni SSL (attive per impostazione predefinita nel Database di Azure per i server PostgreSQL), impostare una variabile di ambiente `PGSSLMODE=require` in modo che lo strumento pg_restore si connetta a SSL. Senza SSL, l'errore può riportare `FATAL:  SSL connection is required. Please specify SSL options and retry.`
>
> Nella riga di comando di Windows, eseguire il comando `SET PGSSLMODE=require` prima di eseguire il comando pg_restore. Nella riga di comando di Linux o Bash, eseguire il comando `export PGSSLMODE=require` prima di eseguire il comando pg_restore.
>

In questo esempio i dati vengono ripristinati dal file di dump **testdb.dump** nel database **mypgsqldb** disponibile sul server di destinazione **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="optimizing-the-migration-process"></a>Ottimizzazione del processo di migrazione

Un modo per eseguire la migrazione di un database PostgreSQL esistente al servizio Database di Azure per PostgreSQL è effettuare il backup del database nell'origine e ripristinarlo in Azure. Per ridurre al minimo il tempo necessario per completare la migrazione, è consigliabile usare i parametri seguenti con i comandi di backup e ripristino.

> [!NOTE]
> Per informazioni dettagliate sulla sintassi, vedere gli articoli [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) e [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).
>

### <a name="for-the-backup"></a>Per il backup
- Eseguire il backup con lo switch -Fc in modo che sia possibile eseguire il ripristino in parallelo per renderlo più rapido. Ad esempio: 

    ```
    pg_dump -h MySourceServerName -U MySourceUserName -Fc -d MySourceDatabaseName > Z:\Data\Backups\MyDatabaseBackup.dump
    ```

### <a name="for-the-restore"></a>Per il ripristino
- Si consiglia di spostare il file di backup in una macchina virtuale di Azure nella stessa area del server del Database di Azure per PostgreSQL in cui si sta eseguendo la migrazione e di eseguire il pg_restore da tale VM per ridurre la latenza di rete. Si consiglia inoltre di creare la macchina virtuale con [la funzionalità rete accelerata](../virtual-network/create-vm-accelerated-networking-powershell.md) abilitata.

- Deve essere già eseguito per impostazione predefinita, ma aprire il file di dump per verificare che le istruzioni CREATE INDEX siano effettuate dopo l'inserimento dei dati. In caso contrario, spostare le istruzioni CREATE INDEX dopo aver inserito i dati.

- Ripristinare con gli switch -Fc e -j *#* per parallelizzare il ripristino. *#* è il numero di core nel server di destinazione. È anche possibile provare con *#* impostato su due volte il numero di core del server di destinazione per verificare l'impatto. Ad esempio: 

    ```
    pg_restore -h MyTargetServer.postgres.database.azure.com -U MyAzurePostgreSQLUserName -Fc -j 4 -d MyTargetDatabase Z:\Data\Backups\MyDatabaseBackup.dump
    ```

- È anche possibile modificare il file di dump aggiungendo il comando *set synchronous_commit = off;* all'inizio e il comando *set synchronous_commit = on;* alla fine. Non accenderlo alla fine, prima che le applicazioni cambino i dati è possibile che si verifichi una successiva perdita di dati.

- Nel server di Database di Azure per PostgreSQL di destinazione è consigliabile eseguire le seguenti operazioni prima del ripristino:
    - Disattivare il rilevamento delle prestazioni delle query, poiché queste statistiche non sono necessarie durante la migrazione. È possibile eseguire questa operazione impostando pg_stat_statements.track, pg_qs.query_capture_mode e pgms_wait_sampling.query_capture_mode su NONE.

    - Usare uno SKU con prestazioni di calcolo e memoria elevate, ad esempio 32 vCore ottimizzati per la memoria, per completare la migrazione più velocemente. Sarà poi possibile tornare allo SKU preferito al termine della migrazione. Con uno SKU più potente, si può ottenere maggior parallelismo aumentando il parametro `-j` corrispondente nel comando pg_restore. 

    - Un numero maggiore di operazioni di I/O al secondo nel server di destinazione può migliorare le prestazioni di ripristino. È possibile effettuare il provisioning di più operazioni di I/O al secondo aumentando le dimensioni delle risorse di archiviazione del server. Questa impostazione non è reversibile, ma è opportuno valutare se un aumento delle operazioni di I/O al secondo può contribuire a migliorare il carico di lavoro effettivo.

Ricordarsi di testare e validare questi comandi in un ambiente di test prima di usarli in produzione.

## <a name="next-steps"></a>Passaggi successivi
- Per eseguire la migrazione di un database PostgreSQL tramite esportazione e importazione, vedere [Eseguire la migrazione del database PostgreSQL usando le funzionalità di esportazione e importazione](howto-migrate-using-export-and-import.md).
- Per altre informazioni sulla migrazione dei database in Database di Azure per PostgreSQL, vedere [Database Migration Guide](https://aka.ms/datamigration) (Guida alla migrazione di database).
