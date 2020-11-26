---
title: Connessione ed esecuzione di query
description: Collegamenti alle guide di avvio rapido sul database SQL di Azure che illustrano come connettersi a un database SQL di Azure e a un'istanza gestita di SQL di Azure ed eseguire query.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: ec40e209d522daf882b8c704c7de6a1fb6aa29f9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183941"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>Articoli su come connettersi al database SQL di Azure e all'istanza gestita di SQL di Azure ed eseguire query
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Il documento seguente include collegamenti ad esempi di Azure che mostrano come connettersi a un database SQL di Azure e a un'istanza gestita di SQL di Azure ed eseguire query. Per alcune raccomandazioni correlate per il protocollo Transport Layer Security, vedere [Considerazioni su TLS per la connettività del database](#tls-considerations-for-database-connectivity).

## <a name="quickstarts"></a>Avvi rapidi

| Guida introduttiva | Descrizione |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|Questa guida di avvio rapido illustra come usare SSMS per connettersi a un database e quindi usare istruzioni Transact-SQL per eseguire query e inserire, eliminare e aggiornare dati nel database.|
|[Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)|Questa guida di avvio rapido illustra come usare Azure Data Studio per connettersi a un database e quindi usare istruzioni Transact-SQL (T-SQL) per creare il database TutorialDB usato nelle esercitazioni di Azure Data Studio.|
|[Azure portal](connect-query-portal.md)|Questa guida di avvio rapido illustra come usare l'editor di query per connettersi a un database (solo database SQL di Azure) e quindi usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database.|
|[Visual Studio Code](connect-query-vscode.md)|Questa guida di avvio rapido illustra come usare Visual Studio Code per connettersi a un database e quindi come usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database.|
|[.NET con Visual Studio](connect-query-dotnet-visual-studio.md)|Questa guida di avvio rapido illustra come usare .NET Framework per creare un programma C# con Visual Studio per connettersi a un database e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[.NET Core](connect-query-dotnet-core.md)|Questa guida di avvio rapido illustra come usare .NET Core in Windows/Linux/macOS per creare un programma C# per connettersi a un database e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[Go](connect-query-go.md)|Questa guida di avvio rapido illustra come usare Go per connettersi a un database. Vengono illustrate anche istruzioni Transact-SQL per eseguire query e modificare i dati.|
|[Java](connect-query-java.md)|Questa guida di avvio rapido illustra come usare Java per connettersi a un database e quindi usare istruzioni Transact-SQL per eseguire query sui dati.|
|[Node.js](connect-query-nodejs.md)|Questa guida di avvio rapido illustra come usare Node.js per creare un programma per connettersi a un database e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[PHP](connect-query-php.md)|Questa guida di avvio rapido illustra come usare PHP per creare un programma per connettersi a un database e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[Python](connect-query-python.md)|Questa guida di avvio rapido illustra come usare Python per connettersi a un database e usare istruzioni Transact-SQL per eseguire query sui dati. |
|[Ruby](connect-query-ruby.md)|Questa guida di avvio rapido illustra come usare Ruby per creare un programma per connettersi a un database e usare istruzioni Transact-SQL per eseguire query sui dati.|
|[R](connect-query-r.md)|Questa guida di avvio rapido illustra come usare R con Machine Learning Services del database SQL di Azure per creare un programma per connettersi a un database nel database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.|
|||

## <a name="tls-considerations-for-database-connectivity"></a>Considerazioni su TLS per la connettività del database

Transport Layer Security, ovvero TLS, viene usato da tutti i driver offerti o supportati da Microsoft per la connessione ai database nel database SQL di Azure o nell'istanza gestita di SQL di Azure. Non è necessaria una configurazione speciale. Per tutte le connessioni a un'istanza di SQL Server, a un database nel database SQL di Azure o a un'istanza gestita di SQL di Azure, è consigliabile impostare le configurazioni seguenti, o configurazioni equivalenti, per tutte le applicazioni:

- **Encrypt = On**
- **TrustServerCertificate = Off**

Alcuni sistemi usano parole chiave diverse ma equivalenti per le parole chiave di configurazione. Queste configurazioni garantiscono che il driver del client verifichi l'identità del certificato TLS ricevuto dal server.

È anche consigliabile disabilitare TLS 1.1 e 1.0 nel client se è necessario ai fini della conformità con Payment Card Industry - Data Security Standard, ovvero PCI-DSS.

I driver che non appartengono a Microsoft potrebbero non usare TLS per impostazione predefinita. Potrebbe trattarsi di un fattore da prendere in considerazione quando si esegue la connessione al database SQL di Azure o all'istanza gestita di SQL di Azure. Le applicazioni con driver incorporati potrebbero non consentire all'utente idi controllare le impostazioni di connessione. Si consiglia di esaminare la sicurezza di tali applicazioni e driver prima di usarli nei sistemi che interagiscono con dati sensibili.

## <a name="libraries"></a>Librerie

È possibile usare varie librerie e diversi framework per connettersi al database SQL di Azure o all'istanza gestita di SQL di Azure. Per iniziare rapidamente a usare linguaggi di programmazione quali C#, Java, Node.js, PHP e Python, consultare le [esercitazioni introduttive](https://aka.ms/sqldev). Creare quindi un'app usando SQL Server in Linux o Windows oppure Docker in macOS.

La tabella seguente elenca le librerie di connettività o *driver* che le applicazioni client possono usare, partendo da una vasta gamma di linguaggi, per connettersi SQL Server in esecuzione in locale o nel cloud. È possibile usarle in Linux, Windows o Docker per connettersi a Database SQL di Azure, Istanza gestita di SQL di Azure e Azure Synapse Analytics (in precedenza SQL Data Warehouse).

| Linguaggio | Piattaforma | Risorse aggiuntive | Download | Introduzione |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET per SQL Server](/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Scaricare](https://www.microsoft.com/net/download/) | [Operazioni preliminari](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC Driver per SQL Server](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [Scaricare](https://go.microsoft.com/fwlink/?linkid=852460) |  [Operazioni preliminari](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Driver SQL PHP per SQL Server](/sql/connect/php/microsoft-php-driver-for-sql-server) | [Scaricare](/sql/connect/php/download-drivers-php-sql-server) | [Operazioni preliminari](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Driver Node.js per SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server/) | [Installazione](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [Operazioni preliminari](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Driver SQL Python](/sql/connect/python/python-driver-for-sql-server/) | Opzioni di installazione: <br/> \* [pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \* [pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [Operazioni preliminari](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Driver Ruby per SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/) | [Installazione](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [Operazioni preliminari](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC Driver for SQL Server](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [Scaricare](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

La tabella seguente elenca esempi di framework ORM (Object Relational Mapping) e framework Web che possono essere usati dalle applicazioni client con SQL Server, database SQL di Azure, istanza gestita di SQL di Azure o Azure Synapse Analytics. È possibile usare i framework in Linux, Windows o Docker.

| Linguaggio | Piattaforma | ORM |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](/ef)<br>[Entity Framework Core](/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'architettura di connettività, vedere [Architettura della connettività del database SQL di Azure](connectivity-architecture.md).
- Trovare i [driver di SQL Server](/sql/connect/sql-connection-libraries/) per la connessione da applicazioni client.
- Connettersi a un database SQL di Azure o a un'istanza gestita di database SQL di Azure:
  - [Connettersi ed eseguire query con .NET (C#)](connect-query-dotnet-core.md)
  - [Connettersi ed eseguire query con PHP](connect-query-php.md)
  - [Connettersi ed eseguire query con Node.js](connect-query-nodejs.md)
  - [Connettersi ed eseguire query con Java](connect-query-java.md)
  - [Connettersi ed eseguire query con Python](connect-query-python.md)
  - [Connettersi ed eseguire query con Ruby](connect-query-ruby.md)
- Esempi di codice di logica di ripetizione dei tentativi:
  - [Connettersi in modo resiliente con ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Connettersi in modo resiliente con PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php