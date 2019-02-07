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
ms.date: 01/02/2019
ms.openlocfilehash: 30174051c0d26595c65a79724cf3d08465a4cab7
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55566966"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Avvio rapido: Usare Ruby per eseguire query su un database SQL di Azure

Questa guida introduttiva illustra come usare [Ruby](https://www.ruby-lang.org) per connettersi a un database SQL di Azure ed eseguire query sui dati tramite istruzioni Transact-SQL.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva è necessario soddisfare i prerequisiti seguenti:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Ruby e software correlato per il sistema operativo in uso:
  
  - **MacOS**: Installare Homebrew, rbenv e ruby-build, Ruby, FreeTDS e TinyTDS. Vedere i passaggi 1.2, 1.3, 1.4, 1.5 e 2.1 in [Creare app Ruby con SQL Server in macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Installare i prerequisiti per Ruby, rbenv e ruby-build, Ruby, FreeTDS e TinyTDS. Vedere i passaggi 1.2, 1.3, 1.4, 1.5 e 2.1 in [Creare app Ruby con SQL Server in Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Installare Ruby, Ruby Devkit e TinyTDS. [Configurare l'ambiente di sviluppo Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

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
