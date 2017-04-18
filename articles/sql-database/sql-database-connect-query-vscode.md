---
title: 'Visual Studio Code: Connettersi ed eseguire query sui dati nel database SQL di Azure | Microsoft Docs'
description: Informazioni su come connettersi a un database SQL in Azure tramite Visual Studio Code. Eseguire quindi istruzioni Transact-SQL (T-SQL) per eseguire query e modificare i dati.
metacanonical: 
keywords: connettersi al database SQL
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: ff5d156ab2b701233c4cdbf08e3d6e517c01b9fb
ms.lasthandoff: 04/12/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Database SQL di Azure: Usare Visual Studio Code per connettersi ai dati ed eseguire query

[Visual Studio Code](https://code.visualstudio.com/docs) è un editor grafico di codice per Linux, macOS e Windows che supporta le estensioni, tra cui l'[estensione mssql](https://aka.ms/mssql-marketplace), per le query di Microsoft SQL Server, database SQL di Azure e SQL Data Warehouse. Questa guida introduttiva illustra come usare Visual Studio Code per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database.

Questa guida introduttiva usa come punto di partenza le risorse create in una delle guide introduttive seguenti:

- [Creare un database: portale](sql-database-get-started-portal.md)
- [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)

Prima di iniziare, assicurarsi di avere installato la versione più recente di [Visual Studio Code](https://code.visualstudio.com/Download) e di aver caricato l'[estensione mssql](https://aka.ms/mssql-marketplace). Per istruzioni sull'installazione dell'estensione mssql, vedere [Install VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) (Installare Visual Studio Code) e [mssql for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) (mssql per Visual Studio Code). 

## <a name="configure-vs-code-mac-os-only"></a>Configurare Visual Studio Code (solo Mac OS)

### <a name="mac-os"></a>**Mac OS**
Per macOS è necessario installare OpenSSL. Si tratta di un prerequisito per DotNet Core, che viene usato dall'estensione mssql. Aprire il terminale e immettere i comandi seguenti per installare **brew** e **OpenSSL***. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere il nome completo del server per il server del database SQL di Azure nel portale di Azure. Usare il nome completo del server per connettersi al server tramite Visual Studio Code.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nel riquadro **Informazioni di base** della pagina del portale di Azure per il database trovare e quindi copiare il **nome server** da usare più avanti in questa guida introduttiva.

    <img src="./media/sql-database-connect-query-vscode/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="set-language-mode-to-sql"></a>Impostare la modalità linguaggio SQL

Impostare la modalità linguaggio su **SQL** in Visual Studio Code per abilitare i comandi mssql e T-SQL IntelliSense.

1. Aprire una nuova finestra di Visual Studio Code. 

2. Premere **⌘+K,M** o **CTRL+K,M** (opzioni per Mac e Windows rispettivamente), digitare **SQL** e premere **INVIO** per impostare la modalità linguaggio su SQL. 

<img src="./media/sql-database-connect-query-vscode/vscode-language-mode.png" alt="SQL language mode" style="width: 780px;" />

## <a name="connect-to-the-server"></a>Connettersi al server

Usare Visual Studio Code per stabilire una connessione al server del database SQL di Azure.

1. In Visual Studio Code premere **CTRL+MAIUSC+P** o **F1** per aprire il riquadro comandi.

2. Digitare **sqlcon**, premere **INVIO** e impostare il linguaggio su **SQL**.

3. Premere **INVIO** per selezionare **Create Connection Profile** (Creare profilo di connessione). Verrà creato un profilo di connessione per l'istanza di SQL Server.

4. Seguire le istruzioni per specificare le proprietà di connessione per il nuovo profilo di connessione. Dopo aver specificato ogni valore, premere **INVIO** per continuare. 

   La tabella seguente descrive le proprietà del profilo di connessione.

   | Impostazione | Descrizione |
   |-----|-----|
   | **Server name** (Nome server) | Immettere il nome completo del server, ad esempio **mynewserver20170313.database.windows.net** |
   | **Database name** (Nome database) | Immettere il nome del database, ad esempio **mySampleDatabase** |
   | **Autenticazione** | Selezionare SQL Login (Account di accesso SQL) |
   | **Nome utente** | Immettere l'account dell'amministratore del server |
   | **Password (SQL Login)** (Password - Account di accesso SQL) | Immettere la password per l'account dell'amministratore del server | 
   | **Save Password?** (Salvare la password?) | Selezionare **Yes** o **No** |
   | **[Optional] Enter a name for this profile** (Facoltativo - Immettere un nome per il profilo) | Immettere il nome del profilo di connessione, ad esempio **mySampleDatabase**. 

5. Premere il tasto **ESC** per chiudere il messaggio che informa che il profilo è stato creato e connesso.

6. Verificare la connessione nella barra di stato.

   <img src="./media/sql-database-connect-query-vscode/vscode-connection-status.png" alt="Connection status" style="width: 780px;" />

## <a name="query-data"></a>Eseguire query sui dati

Usare l'istruzione [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) di Transact-SQL per eseguire query sui dati nel database SQL di Azure.

1. Nella finestra dell'**editor** immettere la query seguente nella finestra di query vuota:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Premere **CTRL+MAIUSC+E** per recuperare dati dalle tabelle Product e ProductCategory.

    <img src="./media/sql-database-connect-query-vscode/query.png" alt="Query" style="width: 780px;" />

## <a name="insert-data"></a>Inserire dati

Usare l'istruzione [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) di Transact-SQL per inserire dati nel database SQL di Azure.

1. Nella finestra dell'**editor** eliminare la query precedente e immettere la query seguente:

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Premere **CTRL+MAIUSC+E** per inserire una nuova riga nella tabella Product.

## <a name="update-data"></a>Aggiornare i dati

Usare l'istruzione [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) di Transact-SQL per aggiornare i dati nel database SQL di Azure.

1.  Nella finestra dell'**editor** eliminare la query precedente e immettere la query seguente:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Premere **CTRL+MAIUSC+E** per aggiornare la riga specificata nella tabella Product.

## <a name="delete-data"></a>Eliminare i dati

Usare l'istruzione [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) di Transact-SQL per eliminare i dati nel database SQL di Azure.

1. Nella finestra dell'**editor** eliminare la query precedente e immettere la query seguente:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Premere **CTRL+MAIUSC+E** per eliminare la riga specificata nella tabella Product.

## <a name="next-steps"></a>Passaggi successivi

- Per connettersi ed effettuare una query usando SQL Server Management Studio, vedere [Connettersi ed eseguire una query con SSMS](sql-database-connect-query-ssms.md)
- Per connettersi ed eseguire una query usando .NET, vedere [Connettersi ed eseguire una query con .NET](sql-database-connect-query-dotnet.md).
- Per connettersi ed eseguire una query usando PHP, vedere [Connettersi ed eseguire una query con PHP](sql-database-connect-query-php.md).
- Per connettersi ed eseguire una query usando Node.js, vedere [Connettersi ed eseguire una query con Node.js](sql-database-connect-query-nodejs.md).
- Per connettersi ed eseguire una query usando Java, vedere [Connettersi ed eseguire una query con Java](sql-database-connect-query-java.md).
- Per connettersi ed eseguire una query usando Python, vedere [Connettersi ed eseguire una query con Python](sql-database-connect-query-python.md).
- Per connettersi ed eseguire una query usando Ruby, vedere [Connettersi ed eseguire una query con Ruby](sql-database-connect-query-ruby.md).

