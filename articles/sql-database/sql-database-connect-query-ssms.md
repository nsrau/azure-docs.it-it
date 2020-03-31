---
title: 'SSMS: Connettersi ed eseguire query sui dati'
description: Informazioni su come connettersi a un database SQL in Azure tramite SQL Server Management Studio (SSMS). Eseguire quindi istruzioni Transact-SQL (T-SQL) per eseguire query sui dati e modificare questi ultimi.
keywords: connettersi al database sql,sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299295"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Guida introduttiva: Usare SQL Server Management Studio per connettersi a un database SQL di Azure ed eseguire query

In questo argomento di avvio rapido si userà SQL Server Management Studio (SSMS) per connettersi a un database SQL di Azure ed eseguire alcune query.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido, è necessario avere gli elementi seguenti:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- Il database di esempio AdventureWorksLT. Se è necessaria una copia di lavoro del database AdventureWorksLT, crearne una completando l'argomento di avvio rapido [Creare un database SQL di Azure](sql-database-single-database-get-started.md).
    - Gli script di questo articolo sono scritti per l'uso del database AdventureWorksLT. Se si usa un'istanza gestita, è necessario importare il database Adventure Works in un database dell'istanza oppure modificare gli script di questo articolo in modo da usare il database Wide World Importers.

Per eseguire semplicemente alcune query ad hoc senza installare SSMS, vedere [Avvio rapido: Usare l'editor di query del portale di Azure per eseguire query su un database SQL](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

Ottenere le informazioni necessarie per connettersi al database. Per completare questo argomento di avvio rapido, è necessario avere il nome completo del server o il nome host, il nome del database e le credenziali di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare al **database SQL** o all'**istanza gestita di SQL** su cui eseguire query.

3. Nella pagina **Panoramica** copiare il nome completo del server accanto a **Nome del server** per un database singolo oppure il nome completo accanto a **Host** per un'istanza gestita. Il nome completo è simile al seguente: *servername.database.windows.net*, con la differenza che include il nome del server effettivo.

## <a name="connect-to-your-database"></a>Connettersi al database

In SSMS connettersi al server di database SQL di Azure.

> [!IMPORTANT]
> Un server di Database SQL di Azure è in ascolto sulla porta 1433. Per connettersi a un server di database SQL protetto da un firewall aziendale, è necessario che nel firewall sia aperta tale porta.

1. Aprire SSMS.

2. Viene visualizzata la finestra di dialogo **Connetti al server** . Immettere le seguenti informazioni:

   | Impostazione      | Valore consigliato    | Descrizione |
   | ------------ | ------------------ | ----------- |
   | **Tipo di server** | Motore di database | Valore obbligatorio. |
   | **Nome server** | Nome completo del server | Ad esempio: **servername.database.windows.net**. |
   | **autenticazione** | Autenticazione di SQL Server | In questa esercitazione viene usata l'autenticazione SQL. |
   | **Accesso** | ID utente dell'account amministratore del server | ID utente dell'account amministratore del server usato per creare il server. |
   | **Password** | Password dell'account amministratore del server | Password dell'account amministratore del server usato per creare il server. |
   ||||

   ![Connetti al server](./media/sql-database-connect-query-ssms/connect.png)  

3. Selezionare **Opzioni** nella finestra di dialogo **Connetti al server**. Nel menu a discesa **Connetti al database** selezionare **mySampleDatabase**. Completando l'argomento di avvio rapido nella sezione [Prerequisiti](#prerequisites), viene creato un database AdventureWorksLT denominato mySampleDatabase. Se la copia di lavoro del database AdventureWorks ha un nome diverso da mySampleDatabase, selezionare invece tale nome.

   ![connettersi al database nel server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Selezionare **Connetti**. Viene visualizzata la finestra Esplora oggetti.

5. Per visualizzare gli oggetti del database, espandere **Database** e quindi il nodo del database.

   ![Oggetti mySampleDatabase](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Eseguire query sui dati

Eseguire questo codice Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) per eseguire una query e individuare i primi 20 prodotti per categoria.

1. In Esplora oggetti fare clic con il pulsante destro del mouse su **mySampleDatabase** e scegliere **Nuova query**. Viene visualizzata una nuova finestra di query connessa al database.

2. Nella finestra della query incollare la query SQL seguente:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Sulla barra degli strumenti selezionare **Esegui** per eseguire la query e recuperare i dati dalle tabelle `Product` e `ProductCategory`.

    ![query per recuperare dati dalla tabella Product e ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Inserire i dati

Eseguire questo codice Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) per creare un nuovo prodotto nella tabella `SalesLT.Product`.

1. Sostituire la query precedente con questa.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Selezionare **Esegui** per inserire una nuova riga nella tabella `Product`. Nel riquadro **Messaggi** verrà visualizzato **(1 riga interessata)** .

#### <a name="view-the-result"></a>Visualizzare il risultato

1. Sostituire la query precedente con questa.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Scegliere **Execute**(Esegui). Si ottiene il risultato seguente.

   ![risultato della query nella tabella Product](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Aggiornare i dati

Eseguire questo codice Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) per modificare il nuovo prodotto.

1. Sostituire la query precedente con quella seguente, che restituisce il nuovo record creato in precedenza:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selezionare **Esegui** per aggiornare la riga specificata nella tabella `Product`. Nel riquadro **Messaggi** verrà visualizzato **(1 riga interessata)** .

### <a name="delete-data"></a>Eliminare i dati

Eseguire questo codice Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) per rimuovere il nuovo prodotto.

1. Sostituire la query precedente con questa.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selezionare **Esegui** per eliminare la riga specificata nella tabella `Product`. Nel riquadro **Messaggi** verrà visualizzato **(1 riga interessata)** .

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su SSMS, vedere [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Per connettersi ed eseguire query tramite il portale di Azure, vedere [Connettersi ed eseguire query con l'editor di query SQL del portale di Azure](sql-database-connect-query-portal.md).
- Per connettersi ed eseguire query usando Visual Studio Code, vedere [Connettersi ed eseguire query con Visual Studio Code](sql-database-connect-query-vscode.md).
- Per connettersi ed eseguire una query usando .NET, vedere [Connettersi ed eseguire una query con .NET](sql-database-connect-query-dotnet.md).
- Per connettersi ed eseguire query usando PHP, vedere [Connettersi ed eseguire query con PHP](sql-database-connect-query-php.md).
- Per connettersi ed eseguire query usando Node.js, vedere [Connettersi ed eseguire query con Node.js](sql-database-connect-query-nodejs.md).
- Per connettersi ed eseguire query usando Java, vedere [Connettersi ed eseguire query con Java](sql-database-connect-query-java.md).
- Per connettersi ed eseguire query usando Python, vedere [Connettersi ed eseguire query con Python](sql-database-connect-query-python.md).
- Per connettersi ed eseguire query usando Ruby, vedere [Connettersi ed eseguire query con Ruby](sql-database-connect-query-ruby.md).
