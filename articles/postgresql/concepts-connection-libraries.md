---
title: Raccolte connessioni per il database di Azure per PostgreSQL | Documentazione Microsoft
description: In questo articolo vengono descritte varie librerie e driver che gli sviluppatori possono usare per generare il codice delle applicazioni per la connessione e l'esecuzione di query nel database di Azure per PostgreSQL.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: f371d5cd4e20096d5101fadf9066e3a135218d0b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Raccolte connessioni per il database di Azure per PostgreSQL
Questo argomento elenca le librerie e i driver che gli sviluppatori possono usare per creare applicazioni per la connessione e l'esecuzione di query in Database di Azure per PostgreSQL.

## <a name="client-interfaces"></a>Interfacce client
La maggior parte delle librerie client di linguaggio usate per connettersi al server PostgreSQL è costituita da progetti esterni e viene distribuita in modo indipendente. Le librerie elencate sono supportate dalle piattaforme Windows, Linux e Mac per la connessione a Database di Azure per PostgreSQL. Nella sezione Passaggi successivi sono elencati alcuni esempi introduttivi.

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
Leggere queste guide introduttive per informazioni su come connettersi ed eseguire query in Database di Azure per PostgreSQL usando il linguaggio scelto:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
