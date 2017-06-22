---
title: Raccolte connessioni per il database di Azure per PostgreSQL | Documentazione Microsoft
description: "Elenca le raccolte e i driver che è possibile usare nei programmi client quando ci si connette al database di Azure per PostgreSQL."
keywords: azure cloud postgresql postgres
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql - database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6bb3f393a8e9533b86919e9a1ebe3490ab838714
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Raccolte connessioni per il database di Azure per PostgreSQL
Questo argomento elenca le raccolte e i driver che è possibile usare nei programmi client quando ci si connette al database di Azure per PostgreSQL.

## <a name="client-interfaces"></a>Interfacce client
La maggior parte delle raccolte del linguaggio per connettersi al server PostgreSQL sono progetti esterni e vengono distribuite in modo indipendente. Queste sono supportate sulle piattaforme Windows, Linux e Mac. Di seguito sono elencati alcuni dei driver client più diffusi.
| **Lingua** | **Interfaccia del client** | **Informazioni aggiuntive** | **Scaricare** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Compatibile con l'API DB 2.0 | [Scaricare](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://php.net/manual/en/book.pgsql.php) | Estensione del database | [Installare](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm package](https://www.npmjs.com/package/pg) | Client non bloccante JavaScript puro | [Installare](https://www.npmjs.com/package/pg) |
| Java | [JDBC](http://jdbc.postgresql.org/) | Driver JDBC tipo 4 | [Scaricare](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interfaccia Ruby | [Scaricare](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Driver postgres Go puro | [Installare](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](http://www.npgsql.org/) | Provider di dati ADO.NET | [Scaricare](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Driver ODBC | [Scaricare](http://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interfaccia primaria di linguaggio C | Incluso |
| C++ | [libpqxx](http://pqxx.org/) | Interfaccia nel nuovo stile C++ | [Scaricare](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Passaggi successivi
- Per una panoramica del servizio, vedere [Azure Database for PostgreSQL Overview](overview.md) (Database di Azure per una panoramica di PostgreSQL).
- Per altre informazioni sui server, vedere [Azure Database for PostgreSQL servers](concepts-servers.md) (Database di Azure per server PostgreSQL).
- Per creare il primo server PostgreSQL, vedere [Create an Azure Database for PostgreSQL in the Azure portal](quickstart-create-server-database-portal.md) (Creare un database di Azure per PostgreSQL nel portale di Azure).

