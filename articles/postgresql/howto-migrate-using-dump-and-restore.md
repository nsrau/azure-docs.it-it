---
title: Come eseguire dump e ripristino in Database di Azure per PostgreSQL | Microsoft Docs
description: Descrive come estrarre un database PostgreSQL in un file di dump e come ripristinare il database PostgreSQL da un file di archivio creato da pg_dump in Database di Azure per PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.topic: article
ms.date: 06/14/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 19cf0a68a8cd05d94badee2efc6c8b023fee7461
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017

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
pg_restore -v –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
In questo esempio i dati vengono ripristinati dal file dump **testdb.dump** nel database **mypgsqldb** disponibile sul server di destinazione **mypgserver-20170401.postgres.database.azure.com**.
```bash
pg_restore -v --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>Passaggi successivi
- Per eseguire la migrazione di un database PostgreSQL usando esportazione e importazione, vedere [Migrate your PostgreSQL database using export and import](howto-migrate-using-export-and-import.md) (Eseguire la migrazione del database PostgreSQL usando esportazione e importazione)
