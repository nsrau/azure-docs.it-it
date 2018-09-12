---
title: Usare Ruby per eseguire query sul database SQL di Azure | Microsoft Docs
description: Questo argomento illustra come usare Ruby per creare un programma che si connette a un database SQL di Azure ed esegue query usando istruzioni Transact-SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: ruby
ms.topic: quickstart
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 4d9a918ad76e6aed603021abfd348f67d7208d3c
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052157"
---
# <a name="use-ruby-to-query-an-azure-sql-database"></a>Usare Ruby per eseguire query su un database SQL di Azure

Questa guida introduttiva illustra come usare [Ruby](https://www.ruby-lang.org) per creare un programma per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di soddisfare i requisiti seguenti:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Avere una [regola del firewall a livello di server](sql-database-get-started-portal-firewall.md) per l'indirizzo IP pubblico del computer usato per questa guida introduttiva.

- Avere installato Ruby e il software correlato adatti per il sistema operativo in uso:
    - **MacOS**: installare Homebrew, installare rbenv e ruby-build, installare Ruby e quindi installare FreeTDS. Vedere i [passaggi 1.2, 1.3, 1.4 e 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
    - **Ubuntu**: installare i prerequisiti per Ruby, installare rbenv e ruby-build, installare Ruby e quindi installare FreeTDS. Vedere i [passaggi 1.2, 1.3, 1.4 e 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).

## <a name="sql-server-connection-information"></a>Informazioni di connessione SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> È necessario avere una regola del firewall impostata per l'indirizzo IP pubblico del computer su cui si esegue questa esercitazione. Se si usa un computer o un indirizzo IP pubblico diverso, creare una [regola del firewall a livello di server con il portale di Azure](sql-database-get-started-portal-firewall.md). 

## <a name="insert-code-to-query-sql-database"></a>Inserire il codice per eseguire query sul database SQL

1. Nell'editor di testo preferito creare un nuovo file, **sqltest.rb**.

2. Sostituire il contenuto con il codice seguente e aggiungere i valori appropriati per il server, il database, l'utente e la password.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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

1. Al prompt dei comandi eseguire questi comandi:

   ```bash
   ruby sqltest.rb
   ```

2. Verificare che vengano restituite le prime 20 righe e quindi chiudere la finestra dell'applicazione.


## <a name="next-steps"></a>Passaggi successivi
- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md)
- [Repository GitHub per TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Segnalare problemi o porre domande su TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Driver Ruby per SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
