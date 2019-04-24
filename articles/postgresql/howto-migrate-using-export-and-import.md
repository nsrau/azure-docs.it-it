---
title: Eseguire la migrazione di un database tramite importazione ed esportazione nel Database di Azure per PostgreSQL
description: In questo articolo viene descritto come estrarre un database PostgreSQL in un file di script da cui importare i dati nel database di destinazione.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/01/2018
ms.openlocfilehash: ecd7dc225379fc9d3eda6fb2e80e3c47a73db49b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60422339"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migrare il database PostgreSQL usando le funzionalità di esportazione e importazione
È possibile usare [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) per estrarre un database PostgreSQL in un file di script e [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) importare i dati nel database di destinazione da quel file.

## <a name="prerequisites"></a>Prerequisiti
Per proseguire con questa guida, si richiedono:
- Un [Database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md) con le regole firewall per consentire l'accesso e il database sottostante.
- Utilità della riga di comando [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) installata
- Utilità della riga di comando [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) installata

Seguire questi passaggi per esportare e importare il database PostgreSQL.

## <a name="create-a-script-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Creare un file di script usando pg_dump che contiene i dati da caricare
Per esportare un database PostgreSQL disponibile in locale o in una macchina virtuale in un file di script SQL, eseguire il comando seguente nell'ambiente esistente:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Se ad esempio è presente un server locale che contiene un database denominato **testdb**:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importare i dati nel Database di Azure per PostgreSQL di destinazione
È possibile usare la riga di comando psql e il parametro- dbname, ovvero -d, per importare i dati nel server Database di Azure per PostgreSQL e caricare i dati dal file SQL.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
In questo esempio vengono usati l'utility PSQL e il file di script **testdb.sql** dal passaggio precedente per importare i dati nel database **mypgsqldb** disponibile nel server di destinazione **mydemoserver.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Passaggi successivi
- Per eseguire la migrazione di un database PostgreSQL tramite dump e ripristino, vedere [Eseguire la migrazione del database PostgreSQL usando dump e ripristino](howto-migrate-using-dump-and-restore.md).
- Per altre informazioni sulla migrazione dei database in Database di Azure per PostgreSQL, vedere [Database Migration Guide](https://aka.ms/datamigration) (Guida alla migrazione di database). 
