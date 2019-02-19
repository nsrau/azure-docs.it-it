---
title: Usare Ruby per eseguire query sul database SQL di Azure | Microsoft Docs
description: Questo argomento illustra come usare Ruby per creare un programma che si connette a un database SQL di Azure ed esegue query usando istruzioni Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: bea32acf4028a4ab43fcc8cd967a31ea397babf3
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234221"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Guida introduttiva: Usare Ruby per eseguire query su un database SQL di Azure

Questa guida introduttiva illustra come usare [Ruby](https://www.ruby-lang.org) per connettersi a un database SQL di Azure ed eseguire query sui dati tramite istruzioni Transact-SQL.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

- un database SQL di Azure. Per creare e quindi configurare un database in Database SQL di Azure è possibile usare una di queste guide introduttive:

  || Database singolo | Istanza gestita |
  |:--- |:--- |:---|
  | Create| [Portale](sql-database-single-database-get-started.md) | [Portale](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/) |
  | Configurare | [Regola del firewall per gli indirizzi IP a livello di server](sql-database-server-level-firewall-rule.md)| [Connettività da una VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connettività da locale](sql-database-managed-instance-configure-p2s.md)
  |Caricare i dati|Adventure Works caricato in base alla guida introduttiva|[Ripristinare Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Ripristinare o importare Adventure Works dal file [BACPAC](sql-database-import.md) ottenuto da [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Gli script in questo articolo sono scritti in modo da usare il database Adventure Works. Con un'istanza gestita, è necessario importare il database Adventure Works in un database dell'istanza oppure modificare gli script di questo articolo in modo da usare il database Wide World Importers.
  
- Ruby e software correlato per il sistema operativo in uso:
  
  - **MacOS**: Installare Homebrew, rbenv e ruby-build, Ruby, FreeTDS e TinyTDS. Vedere i passaggi 1.2, 1.3, 1.4, 1.5 e 2.1 in [Creare app Ruby con SQL Server in macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Installare i prerequisiti per Ruby, rbenv e ruby-build, Ruby, FreeTDS e TinyTDS. Vedere i passaggi 1.2, 1.3, 1.4, 1.5 e 2.1 in [Creare app Ruby con SQL Server in Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Installare Ruby, Ruby Devkit e TinyTDS. [Configurare l'ambiente di sviluppo Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla pagina **Database SQL** o **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per un database singolo o accanto a **Host** per un'istanza gestita. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**. 

## <a name="create-code-to-query-your-sql-database"></a>Creare codice per eseguire query sul database SQL

1. In un editor di testo o di codice creare un nuovo file denominato *sqltest.rb*.
   
1. Aggiungere il codice seguente. Sostituire `<server>`, `<database>`, `<username>` e `<password>` con i valori corrispondenti del database SQL di Azure.
   
   >[!IMPORTANT]
   >Il codice in questo esempio usa i dati di esempio di AdventureWorksLT, che è possibile scegliere come origine durante la creazione del database. Se il database contiene dati diversi, utilizzare le tabelle del database personale nella query SELECT. 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Eseguire il codice

1. Al prompt dei comandi, eseguire il seguente comando:

   ```bash
   ruby sqltest.rb
   ```
   
1. Verificare che vengano restituite le prime 20 righe di categoria/prodotto del database. 

## <a name="next-steps"></a>Passaggi successivi
- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md).
- [Repository GitHub per TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Segnalare problemi o porre domande su TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Driver Ruby per SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
