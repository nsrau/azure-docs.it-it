---
title: Raccolte connessioni per il Database di Azure per MySQL
description: Questo articolo elenca le raccolte e i driver che è possibile usare nei programmi client quando ci si connette al database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 6ce0f2c761ede7d326f52f4d93d7f1b0bfa98cb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525538"
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Raccolte connessioni per il Database di Azure per MySQL
Questo articolo elenca le raccolte e i driver che è possibile usare nei programmi client quando ci si connette al database di Azure per MySQL.

## <a name="client-interfaces"></a>Interfacce client
MySQL offre la connettività driver di database standard per l'uso di MySQL con applicazioni e strumenti compatibili con gli standard di settore ODBC e JDBC. Qualsiasi sistema che funziona con ODBC o JDBC può usare MySQL.

| **Lingua** | **Piattaforma** | **Risorse aggiuntive** | **Scaricare** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Driver MySQL nativo per PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Scaricare](https://secure.php.net/downloads.php) |
| ODBC | Piattaforme Windows, Linux, Mac OS X e Unix | [Guida dello sviluppatore per il connettore MySQL/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Scaricare](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guida dello sviluppatore per il connettore MySQL/Net](https://dev.mysql.com/doc/connector-net/en/) | [Scaricare](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Indipendente dalla piattaforma | [Guida dello sviluppatore per il connettore MySQL/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Scaricare](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Scaricare](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Guida dello sviluppatore per il connettore MySQL/Python](https://dev.mysql.com/doc/connector-python/en/) | [Scaricare](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guida dello sviluppatore per il connettore MySQL/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Scaricare](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guida dello sviluppatore per il connettore MySQL/C](https://dev.mysql.com/doc/connector-c/en/) | [Scaricare](https://dev.mysql.com/downloads/connector/c/)
| Perl | Piattaforme Windows, Linux, Mac OS X e Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Scaricare](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Passaggi successivi
Leggere queste guide introduttive per informazioni su come connettersi ed eseguire query in Database di Azure per MySQL usando il linguaggio scelto:

[PHP](./connect-php.md) | [Java](./connect-java.md) |  [.NET (C#)](./connect-csharp.md) | [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Ruby](./connect-ruby.md) | [C++](connect-cpp.md) | [Go](./connect-go.md)

