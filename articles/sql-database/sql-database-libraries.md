---
title: Raccolte di connessioni per database SQL | Documentazione Microsoft
description: Indica collegamenti per il download di moduli che consentono la connessione a SQL Server e a database SQL da una vasta gamma di linguaggi di programmazione client. I moduli vengono rilasciati dalla community o da Microsoft.
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: genemi
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 8c4d76e371a8f48806dd2229e406a2eb1f69504e
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="connectivity-libraries-and-frameworks-for-microsoft-sql-server"></a>Librerie di connettività e framework per Microsoft SQL Server

Vedere le [esercitazioni introduttive](http://aka.ms/sqldev) per iniziare a usare rapidamente linguaggi di programmazione come C#, Java, Node.js, PHP e Python, nonché creare un'app con SQL Server su Linux o Windows o con Docker su macOS.

La tabella seguente elenca le librerie di connettività o *driver* che le applicazioni client possono usare da svariati linguaggi per connettersi a Microsoft SQL Server e usarlo in esecuzione in locale o nel cloud, in Linux, Windows o in Docker, nonché per connettersi al database SQL di Azure e ad Azure SQL Data Warehouse. 

| Linguaggio | Piattaforma | Risorse aggiuntive | Scaricare | Attività iniziali |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET per SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Scaricare](https://www.microsoft.com/net/download/) | [Attività iniziali](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Driver Microsoft JDBC per SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Scaricare](http://go.microsoft.com/fwlink/?LinkId=245496) |  [Attività iniziali](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Driver SQL PHP per SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | Sistema operativo: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Attività iniziali](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Driver Node.js per SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Installare](https://msdn.microsoft.com/library/mt652094.aspx) |  [Attività iniziali](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Driver Python SQL](http://msdn.microsoft.com/library/mt652092.aspx) | Opzioni di installazione: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Attività iniziali](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Driver Ruby per SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Installare](https://msdn.microsoft.com/library/mt711041.aspx) | [Attività iniziali](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Driver Microsoft ODBC per SQL Server](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [Scaricare](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

La tabella seguente elenca alcuni esempi di framework ORM (Object Relational Mapping) e framework Web che le applicazioni client possono usare con Microsoft SQL Server in esecuzione in locale o nel cloud, in Linux, Windows o Docker e anche per il database SQL di Azure e Azure SQL Data Warehouse. 

| Linguaggio | Piattaforma | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/en-us/ef)<br>[Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |

## <a name="related-links"></a>Collegamenti correlati
- [Driver SQL Server](http://msdn.microsoft.com/library/mt654049.aspx) per la connessione da applicazioni client
- [Connettersi al database SQL tramite .NET (C#)](sql-database-connect-query-dotnet.md)
- [Connettersi al database SQL tramite PHP](sql-database-connect-query-php.md)
- [Connettersi al Database SQL tramite Node.js](sql-database-connect-query-nodejs.md)
- [Connettersi al database SQL tramite Java](sql-database-connect-query-java.md)
- [Connettersi al database SQL tramite Python](sql-database-connect-query-python.md)
- [Connettersi al database SQL tramite Ruby](sql-database-connect-query-ruby.md)

