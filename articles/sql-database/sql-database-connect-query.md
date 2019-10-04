---
title: Guide introduttive sulla connessione e l'esecuzione di query sui database SQL di Azure | Microsoft Docs
description: Guide introduttive sui database SQL di Azure che illustrano come connettersi a un database SQL di Azure ed eseguire query su di esso.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 63ed2c5c334aef8f6281ee34ec4ed6e47ca8521a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569098"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Argomenti di avvio rapido: Connessione ed esecuzione di query sui database SQL di Azure

Il documento seguente include collegamenti ad esempi di Azure che mostrano come connettersi a un database SQL di Azure ed eseguire query su di esso. Contiene anche alcune indicazioni per la sicurezza a livello di trasporto.

## <a name="quickstarts"></a>Guide introduttive

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Questa guida introduttiva illustra come usare SSMS per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL per eseguire query e inserire, eliminare e aggiornare dati nel database.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Questa guida introduttiva illustra come usare Azure Data Studio per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL (T-SQL) per creare il database TutorialDB usato nelle esercitazioni di Azure Data Studio.|
|[Portale di Azure](sql-database-connect-query-portal.md)|Questa guida introduttiva illustra come usare l'editor di query per connettersi a un database SQL e quindi usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Questa guida introduttiva illustra come usare Visual Studio Code per connettersi a un database SQL di Azure e quindi come usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database.|
|[.NET con Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Questa guida introduttiva illustra come usare .NET Framework per creare un programma C# con Visual Studio per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|Questa guida introduttiva illustra come usare .NET Core in Windows/Linux/macOS per creare un programma C# per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[Go](sql-database-connect-query-go.md)|Questa guida introduttiva illustra come usare Go per connettersi a un database SQL di Azure. Vengono illustrate anche istruzioni Transact-SQL per eseguire query e modificare i dati.|
|[Java](sql-database-connect-query-java.md)|Questa guida introduttiva illustra come usare Java per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL per eseguire query sui dati.|
|[Node.js](sql-database-connect-query-nodejs.md)|Questa guida introduttiva illustra come usare Node.js per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[PHP](sql-database-connect-query-php.md)|Questa guida introduttiva illustra come usare PHP per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[Python](sql-database-connect-query-python.md)|Questa guida introduttiva illustra come usare Python per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati. |
|[Ruby](sql-database-connect-query-ruby.md)|Questa guida introduttiva illustra come usare Ruby per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[R](sql-database-connect-query-r.md)|Questa guida di avvio rapido illustra come usare R con Machine Learning Services del database SQL di Azure per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Considerazioni su TLS per la connettività del database SQL
Transport Layer Security, ovvero TLS, viene usato da tutti i driver offerti o supportati da Microsoft per la connessione al database SQL di Azure. Non è necessaria una configurazione speciale. Per tutte le connessioni a SQL Server o al database SQL di Azure, è consigliabile impostare le configurazioni seguenti, o configurazioni equivalenti, per tutte le applicazioni:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Alcuni sistemi usano parole chiave diverse ma equivalenti per le parole chiave di configurazione. Queste configurazioni garantiscono che il driver del client verifichi l'identità del certificato TLS ricevuto dal server.

È anche consigliabile disabilitare TLS 1.1 e 1.0 nel client se è necessario ai fini della conformità con Payment Card Industry - Data Security Standard, ovvero PCI-DSS.

I driver che non appartengono a Microsoft potrebbero non usare TLS per impostazione predefinita. Potrebbe trattarsi di un fattore da prendere in considerazione quando si esegue la connessione al database SQL di Azure. Le applicazioni con driver incorporati potrebbero non consentire all'utente idi controllare le impostazioni di connessione. Si consiglia di esaminare la sicurezza di tali applicazioni e driver prima di usarli nei sistemi che interagiscono con dati sensibili.

## <a name="libraries"></a>Librerie

È possibile usare varie librerie e diversi framework per connettersi al database SQL di Azure. Per iniziare rapidamente a usare linguaggi di programmazione quali C#, Java, Node.js, PHP e Python, consultare le [esercitazioni introduttive](https://aka.ms/sqldev). Creare quindi un'app usando SQL Server in Linux o Windows oppure Docker in macOS.

La tabella seguente elenca le librerie di connettività o *driver* che le applicazioni client possono usare, partendo da una vasta gamma di linguaggi, per connettersi SQL Server in esecuzione in locale o nel cloud. È possibile usare le librerie in Linux, Windows o Docker per connettersi al database SQL di Azure e ad Azure SQL Data Warehouse. 

| Linguaggio | Piattaforma | Risorse aggiuntive | Download | Attività iniziali |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET per SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Scaricare](https://www.microsoft.com/net/download/) | [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC Driver per SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Scaricare](https://go.microsoft.com/fwlink/?linkid=852460) |  [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Driver SQL PHP per SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Scaricare](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Driver Node.js per SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Installa](https://msdn.microsoft.com/library/mt652094.aspx) |  [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Driver SQL Python](https://msdn.microsoft.com/library/mt652092.aspx) | Opzioni di installazione: <br/> \*[pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Driver Ruby per SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Installa](https://msdn.microsoft.com/library/mt711041.aspx) | [Introduzione](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC Driver for SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Scaricare](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

La tabella seguente elenca esempi di framework ORM (Object Relational Mapping) e framework Web che possono essere usati dalle applicazioni client con SQL Server in esecuzione in locale o nel cloud. È possibile usare i framework in Linux, Windows o Docker per connettersi al database SQL e a SQL Data Warehouse. 

| Linguaggio | Piattaforma | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'architettura di connettività, vedere [Architettura della connettività del database SQL di Azure](sql-database-connectivity-architecture.md).
- Trovare i [driver di SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) per la connessione da applicazioni client
- Connessione al database SQL:
  - [Connettersi al database SQL tramite .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Connettersi al database SQL tramite PHP](sql-database-connect-query-php.md) 
  - [Connettersi al Database SQL tramite Node.js](sql-database-connect-query-nodejs.md) 
  - [Connettersi al database SQL tramite Java](sql-database-connect-query-java.md) 
  - [Connettersi al database SQL tramite Python](sql-database-connect-query-python.md)
  - [Connettersi al database SQL tramite Ruby](sql-database-connect-query-ruby.md)
- Esempi di codice di logica di ripetizione dei tentativi:
  - [Connettersi in modo resiliente a SQL tramite ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Connettersi in modo resiliente a SQL tramite PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
