---
title: Raccolte connessioni per Database di Azure per PostgreSQL - Server singolo
description: Questo articolo vengono descritte varie librerie e i driver che gli sviluppatori possono utilizzare quando codifica di applicazioni per connettersi ed eseguire query su Database di Azure per PostgreSQL - singolo Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 85110126f9bdec225b1644860814cd89832132a1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073601"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Raccolte connessioni per Database di Azure per PostgreSQL - Server singolo
Questo argomento elenca le librerie e i driver che gli sviluppatori possono usare per creare applicazioni per la connessione e l'esecuzione di query in Database di Azure per PostgreSQL.

## <a name="client-interfaces"></a>Interfacce client
La maggior parte delle librerie client di linguaggio usate per connettersi al server PostgreSQL è costituita da progetti esterni e viene distribuita in modo indipendente. Le librerie elencate sono supportate dalle piattaforme Windows, Linux e Mac per la connessione a Database di Azure per PostgreSQL. Nella sezione Passaggi successivi sono elencati alcuni esempi introduttivi.

| **Lingua** | **Interfaccia del client** | **Informazioni aggiuntive** | **Scaricare** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Compatibile con l'API DB 2.0 | [Scaricare](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Estensione del database | [Installa](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm package](https://www.npmjs.com/package/pg) | Client non bloccante JavaScript puro | [Installa](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Driver JDBC tipo 4 | [Download](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interfaccia Ruby | [Scaricare](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Driver postgres Go puro | [Installa](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Provider di dati ADO.NET | [Scaricare](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Driver ODBC | [Scaricare](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interfaccia primaria di linguaggio C | Incluso |
| C++ | [libpqxx](http://pqxx.org/) | Interfaccia nel nuovo stile C++ | [Scaricare](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Passaggi successivi
Leggere queste guide introduttive per informazioni su come connettersi ed eseguire query in Database di Azure per PostgreSQL usando il linguaggio scelto:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md)  |  [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
