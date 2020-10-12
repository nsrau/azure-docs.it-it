---
title: Librerie di connessione-database di Azure per MySQL
description: Questo articolo elenca le raccolte e i driver che è possibile usare nei programmi client quando ci si connette al database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/3/2020
ms.openlocfilehash: 9d4d862389a607b18ee5f2440069fa2eb6cf7bc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87553037"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Raccolte connessioni per il Database di Azure per MySQL
Questo articolo elenca le raccolte e i driver che è possibile usare nei programmi client quando ci si connette al database di Azure per MySQL.

## <a name="client-interfaces"></a>Interfacce client
MySQL offre la connettività driver di database standard per l'uso di MySQL con applicazioni e strumenti compatibili con gli standard di settore ODBC e JDBC. Qualsiasi sistema che funziona con ODBC o JDBC può usare MySQL.

| **Lingua** | **Piattaforma** | **Risorse aggiuntive** | **Download** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Driver MySQL nativo per PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Download](https://secure.php.net/downloads.php) |
| ODBC | Piattaforme Windows, Linux, Mac OS X e Unix | [Guida dello sviluppatore per il connettore MySQL/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Download](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guida dello sviluppatore per il connettore MySQL/Net](https://dev.mysql.com/doc/connector-net/en/) | [Download](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Indipendente dalla piattaforma | [Guida dello sviluppatore per il connettore MySQL/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Download](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Download](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Guida dello sviluppatore per il connettore MySQL/Python](https://dev.mysql.com/doc/connector-python/en/) | [Download](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guida dello sviluppatore per il connettore MySQL/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Download](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guida per sviluppatori di MySQL Connector/C](https://dev.mysql.com/doc/c-api/8.0/en/) | [Download](https://dev.mysql.com/downloads/connector/c/)
| Perl | Piattaforme Windows, Linux, Mac OS X e Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Download](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Passaggi successivi
Leggere queste guide introduttive per informazioni su come connettersi ed eseguire query in Database di Azure per MySQL usando il linguaggio scelto:

- [PHP](./connect-php.md)
- [Java](./connect-java.md)
- [.NET (C#)](./connect-csharp.md)
- [Python](./connect-python.md)
- [Node.JS](./connect-nodejs.md)
- [Ruby](./connect-ruby.md)
- [C++](connect-cpp.md)
- [Go](./connect-go.md)
