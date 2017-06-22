---
title: Migrare un database tramite l&quot;importazione ed esportazione nel Database di Azure per PostgreSQL | Microsoft Docs
description: In questo articolo viene descritto come estrarre un database PostgreSQL in un file di script da cui importare i dati nel database di destinazione.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 275519089d682b6ef3c7858446ab2c4baface77f
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

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
Per esportare il database PostgreSQL esistente in locale o in una macchina virtuale in un file di script SQL, eseguire il comando seguente:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Ad esempio, se si dispone di un server locale che contiene un database denominato **testdb**
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postrgesql"></a>Importare i dati nel Database di Azure per PostrgeSQL di destinazione
È possibile usare la riga di comando PSQL e il parametro -d, - dbname per importare i dati nel Database di Azure per PostrgeSQL creato e caricare i dati dal file SQL.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
In questo esempio vengono usati il file PSQL e il file di script denominato **testdb.sql** dal passaggio precedente per importare i dati nel database **mypgsqldb** sul server di destinazione **mypgserver-20170401.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mypgserver-20170401.database.windows.net --port=5432 --username=mylogin@mypgserver-20170401 --dbname=mypgsqldb
```

## <a name="next-steps"></a>Passaggi successivi
- Per eseguire la migrazione di un database PostgreSQL tramite dump e ripristino, vedere [Migrare il database PostgreSQL tramite dump e restore](howto-migrate-using-dump-and-restore.md)
