---
title: 'VS Code: connettersi ed eseguire query sui dati nel database SQL di Azure | Microsoft Docs'
description: Informazioni su come connettersi a un database SQL in Azure tramite Visual Studio Code. Eseguire quindi istruzioni Transact-SQL (T-SQL) per eseguire query e modificare i dati.
keywords: connettersi al database SQL
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 20d6ccca448d53da54835aad1d6dd85702c7390f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446928"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Guida introduttiva: Usare Visual Studio Code per connettersi a un database SQL di Azure ed eseguire query

[Visual Studio Code](https://code.visualstudio.com/docs) è un editor grafico di codice per Linux, macOS e Windows. Supporta estensioni, tra cui l'[estensione mssql](https://aka.ms/mssql-marketplace) per l'esecuzione di query su Microsoft SQL Server, database SQL di Azure e SQL Data Warehouse. In questa guida introduttiva si usa Visual Studio Code per connettersi a un database SQL di Azure e quindi eseguire query e inserire, aggiornare ed eliminare dati con istruzioni Transact-SQL.

## <a name="prerequisites"></a>Prerequisiti

- un database SQL di Azure. Per creare e quindi configurare un database in Database SQL di Azure è possibile usare una di queste guide introduttive:

  || Database singolo | Istanza gestita |
  |:--- |:--- |:---|
  | Create| [Portale](sql-database-single-database-get-started.md) | [Portale](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurare | [Regola del firewall per gli indirizzi IP a livello di server](sql-database-server-level-firewall-rule.md)| [Connettività da una VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connettività da locale](sql-database-managed-instance-configure-p2s.md)
  |Caricare i dati|Adventure Works caricato in base alla guida introduttiva|[Ripristinare Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Ripristinare o importare Adventure Works dal file [BACPAC](sql-database-import.md) ottenuto da [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Gli script in questo articolo sono scritti in modo da usare il database Adventure Works. Con un'istanza gestita, è necessario importare il database Adventure Works in un database dell'istanza oppure modificare gli script di questo articolo in modo da usare il database Wide World Importers.

## <a name="install-visual-studio-code"></a>Installare Visual Studio Code

Assicurarsi di aver installato l'ultima versione di [Visual Studio Code](https://code.visualstudio.com/Download) e di aver caricato l'[estensione mssql](https://aka.ms/mssql-marketplace). Per indicazioni sull'installazione dell'estensione mssql, vedere [Installare Visual Studio Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) e [mssql for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) (mssql per Visual Studio Code).

## <a name="configure-visual-studio-code"></a>Configurare Visual Studio Code 

### <a name="mac-os"></a>**Mac OS**

Per macOS è necessario installare OpenSSL, che è un prerequisito per .NET Core usato dall'estensione mssql. Aprire il terminale e immettere i comandi seguenti per installare **brew** e **OpenSSL**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Non è necessaria alcuna configurazione speciale.

### <a name="windows"></a>**Windows**

Non è necessaria alcuna configurazione speciale.

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla pagina **Database SQL** o **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per un database singolo o accanto a **Host** per un'istanza gestita. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**.

## <a name="set-language-mode-to-sql"></a>Impostare la modalità linguaggio SQL

In Visual Studio Code impostare la modalità di linguaggio su **SQL** per abilitare i comandi mssql e T-SQL IntelliSense.

1. Aprire una nuova finestra di Visual Studio Code. 

2. Premere **CTRL**+**N**. Verrà aperto un nuovo file di testo normale. 

3. Selezionare **Testo normale** nell'angolo inferiore destro della barra di stato.

4. Nel menu a discesa **Seleziona modalità linguaggio** visualizzato selezionare **SQL**. 

## <a name="connect-to-your-database"></a>Connettersi al database

Usare Visual Studio Code per stabilire una connessione al server del database SQL di Azure.

> [!IMPORTANT]
> Prima di continuare, assicurarsi di avere a disposizione le informazioni di accesso e del server. Se dopo aver iniziato a immettere le informazioni del profilo di connessione si sposta lo stato attivo da Visual Studio Code, sarà necessario iniziare di nuovo la creazione del profilo.
>

1. In Visual Studio Code premere **CTRL+MAIUSC+P** o **F1** per aprire il riquadro comandi.

2. Selezionare **MS SQL:Connect** (MS SQL: Connetti) e premere **INVIO**.

3. Selezionare **Create Connection Profile** (Crea profilo di connessione).

4. Seguire le istruzioni per specificare le proprietà di connessione del nuovo profilo. Dopo aver specificato ogni valore, premere **INVIO** per continuare. 

   | Proprietà       | Valore consigliato | Descrizione |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome server** | Nome completo del server | Ad esempio, **mynewserver20170313.database.windows.net**. |
   | **Nome database** | mySampleDatabase | Database a cui connettersi. |
   | **autenticazione** | Account di accesso SQL| Questa esercitazione usa l'autenticazione SQL. |
   | **Nome utente** | Nome utente | Nome utente dell'account amministratore del server usato per creare il server. |
   | **Password (SQL Login)** (Password - Account di accesso SQL) | Password | Password dell'account amministratore del server usato per creare il server. |
   | **Save Password?** (Salvare la password?) | Sì o No | Selezionare **Sì** se non si vuole immettere la password ogni volta. |
   | **Immettere un nome per questo profilo** | Nome di profilo, ad esempio **mySampleProfile** | Un profilo salvato velocizza la connessione agli accessi successivi. | 

   Se l'operazione ha esito positivo, viene visualizzata una notifica che informa che il profilo è stato creato e connesso.

## <a name="query-data"></a>Eseguire query sui dati

Eseguire l'istruzione [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL seguente per eseguire una query per individuare i primi 20 prodotti per categoria.

1. Nella finestra dell'editor incollare la query SQL seguente.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Premere **CTRL**+**MAIUSC**+**E** per eseguire la query e visualizzare i risultati ottenuti dalle tabelle `Product` e `ProductCategory`.

    ![Eseguire una query per recuperare dati da 2 tabelle](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Inserire dati

Eseguire l'istruzione [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL seguente per aggiungere un nuovo prodotto nella tabella `SalesLT.Product`.

1. Sostituire la query precedente con questa.

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

2. Premere **CTRL**+**MAIUSC**+**E** per inserire una nuova riga nella tabella `Product`.

## <a name="update-data"></a>Aggiornare i dati

Eseguire l'istruzione [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL seguente per aggiornare il prodotto aggiunto.

1. Sostituire la query precedente con questa:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Premere **CTRL**+**MAIUSC**+**E** per aggiornare la riga specificata nella tabella `Product`.

## <a name="delete-data"></a>Eliminare i dati

Eseguire l'istruzione [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL seguente per rimuovere il nuovo prodotto.

1. Sostituire la query precedente con questa:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Premere **CTRL**+**MAIUSC**+**E** per eliminare la riga specificata nella tabella `Product`.

## <a name="next-steps"></a>Passaggi successivi

- Per connettersi ed eseguire query con SQL Server Management Studio, vedere [Guida introduttiva: Usare SQL Server Management Studio per connettersi a un database SQL di Azure ed eseguire query sui dati](sql-database-connect-query-ssms.md).
- Per connettersi ed eseguire query con il portale di Azure, vedere [Guida introduttiva: Usare l'editor di query SQL del portale di Azure per connettersi ed eseguire query sui dati](sql-database-connect-query-portal.md).
- Per un articolo di MSDN Magazine sull'uso di Visual Studio Code, vedere il post di blog [Crea un IDE di database con estensione MSSQL](https://msdn.microsoft.com/magazine/mt809115).
