---
title: Librerie di connessione-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive diverse librerie e driver che è possibile usare quando si codificano le applicazioni per connettersi ed eseguire query su database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 2305a02e5b094ec9e98f39363ddbd0c39221ab0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74768895"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Librerie di connessione per database di Azure per PostgreSQL-server singolo
Questo argomento elenca le librerie e i driver che gli sviluppatori possono usare per creare applicazioni per la connessione e l'esecuzione di query in Database di Azure per PostgreSQL.

## <a name="client-interfaces"></a>Interfacce client
La maggior parte delle librerie client di linguaggio usate per connettersi al server PostgreSQL è costituita da progetti esterni e viene distribuita in modo indipendente. Le librerie elencate sono supportate dalle piattaforme Windows, Linux e Mac per la connessione a Database di Azure per PostgreSQL. Nella sezione Passaggi successivi sono elencati alcuni esempi introduttivi.

| **Lingua** | **Interfaccia del client** | **Informazioni aggiuntive** | **Scarica** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Compatibile con l'API DB 2.0 | [Scarica](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Estensione del database | [Installazione](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm package](https://www.npmjs.com/package/pg) | Client non bloccante JavaScript puro | [Installazione](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Driver JDBC tipo 4 | [Scaricare](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interfaccia Ruby | [Scarica](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Driver postgres Go puro | [Installazione](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Provider di dati ADO.NET | [Scarica](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Driver ODBC | [Scarica](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interfaccia primaria di linguaggio C | Incluso |
| C++ | [libpqxx](http://pqxx.org/) | Interfaccia nel nuovo stile C++ | [Scarica](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Passaggi successivi
Leggere queste guide introduttive per informazioni su come connettersi ed eseguire query in Database di Azure per PostgreSQL usando il linguaggio scelto:

[Python](./connect-python.md) | [node. js](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) |  | [Go](./connect-go.md) [PHP](./connect-php.md)php .NET[(C#) go](./connect-csharp.md) | 
