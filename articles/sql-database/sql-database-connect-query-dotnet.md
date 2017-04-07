---
title: Connettersi al database SQL di Azure tramite .NET (C#) | Microsoft Docs
description: Usare il codice di esempio in questa guida introduttiva per creare con C# un&quot;applicazione moderna, supportata da un database relazionale potente nel cloud con il database SQL di Azure.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/24/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 61cc9cf7bdb552932a4659103a4d7ba479471948
ms.lasthandoff: 03/29/2017


---
# <a name="azure-sql-database-use-net-c-to-connect-and-query-data"></a>Database SQL di Azure: usare .NET (C#) per connettersi ai dati ed eseguire query

Usare [C# e ADO.NET](https://msdn.microsoft.com/library/kb9s9ks0.aspx) per connettersi ed eseguire query in un database SQL di Azure. Questa guida fornisce informazioni dettagliate sull'uso di C# per connettersi a un database SQL di Azure e quindi eseguire query e istruzioni di inserimento, aggiornamento ed eliminazione.

Questa guida introduttiva usa come punto di partenza le risorse create in una delle guide introduttive seguenti:

- [Creare un database: portale](sql-database-get-started-portal.md)
- [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)

Prima di iniziare, assicurarsi di avere configurato l'ambiente di sviluppo per C#. Vedere [Installare Visual Studio Community gratuitamente](https://www.visualstudio.com/) o installare il [driver ADO.NET per SQL Server](https://www.microsoft.com/net/download).

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere la stringa di connessione nel portale di Azure. Usare la stringa di connessione per connettersi al database SQL di Azure.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nel riquadro **Informazioni di base** del database esaminare il nome completo del server. 

    <img src="./media/sql-database-connect-query-dotnet/connection-strings.png" alt="connection strings" style="width: 780px;" />

4. Fare clic su **Mostra stringhe di connessione del database**.

5. Esaminare la stringa di connessione completa **ADO.NET**.

    <img src="./media/sql-database-connect-query-dotnet/adonet-connection-string.png" alt="ADO.NET connection string" style="width: 780px;" />

## <a name="select-data"></a>Selezionare i dati

1. Nell'ambiente di sviluppo aprire un file di codice vuoto.
2. Aggiungere ```using System.Data.SqlClient``` al file di codice ([spazio dei nomi System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)). 

3. Usare [SqlCommand.ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) con un'istruzione [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) di Transact-SQL per eseguire query sui dati nel database SQL di Azure. Aggiungere i valori appropriati per il server

    ```csharp
    string hostName = 'yourserver.database.windows.net';
    string dbName = 'yourdatabase';
    string user = 'yourusername';
    string password = 'yourpassword';

    string strConn = $"server=tcp:+hostName+,1433;Initial Catalog=+dbName+;Persist Security Info=False;User ID=+user+;Password=+password+;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;";
    
    using (var connection = new SqlConnection(strConn))
    {
       connection.Open();

       SqlCommand selectCommand = new SqlCommand("", connection);
       selectCommand.CommandType = CommandType.Text;

       selectCommand.CommandText = @"SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid";

       SqlDataReader reader = selectCommand.ExecuteReader();

       while (reader.Read())
       {
          // show data
          Console.WriteLine($"{reader.GetString(0)}\t{reader.GetString(1)}");
       }
       reader.Close();
    }
    ```

## <a name="insert-data"></a>Inserire dati

Usare [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) con un'istruzione [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) di Transact-SQL per inserire dati nel database SQL di Azure.

```csharp
    string hostName = 'yourserver.database.windows.net';
    string dbName = 'yourdatabase';
    string user = 'yourusername';
    string password = 'yourpassword';

    string strConn = $"server=tcp:+hostName+,1433;Initial Catalog=+dbName+;Persist Security Info=False;User ID=+user+;Password=+password+;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;";

    using (var connection = new SqlConnection(strConn))

    SqlCommand insertCommand = new SqlCommand("", connection);
    insertCommand.CommandType = CommandType.Text;
    insertCommand.CommandText = @"INSERT INTO[SalesLT].[Product]
            ( [Name]
            , [ProductNumber]
            , [Color]
            , [StandardCost]
            , [ListPrice]
            , [SellStartDate]
            )
VALUES
(
            @Name,
            @ProductNumber,
            @Color,
            @StandardCost,
            @ListPrice,
            @SellStartDate)";

            insertCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
            insertCommand.Parameters.AddWithValue("@ProductNumber", "200989");
            insertCommand.Parameters.AddWithValue("@Color", "Blue");
            insertCommand.Parameters.AddWithValue("@StandardCost", 75);
            insertCommand.Parameters.AddWithValue("@ListPrice", 80);
            insertCommand.Parameters.AddWithValue("@SellStartDate", "7/1/2016");

int newrows = insertCommand.ExecuteNonQuery();
Console.WriteLine($"Inserted {newrows.ToString()} row(s).");
```

## <a name="update-data"></a>Aggiornare i dati

Usare [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) con un'istruzione [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) di Transact-SQL per aggiornare i dati nel database SQL di Azure.

```csharp
    string hostName = 'yourserver.database.windows.net';
    string dbName = 'yourdatabase';
    string user = 'yourusername';
    string password = 'yourpassword';

    string strConn = $"server=tcp:+hostName+,1433;Initial Catalog=+dbName+;Persist Security Info=False;User ID=+user+;Password=+password+;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;";

    using (var connection = new SqlConnection(strConn))

    SqlCommand updateCommand = new SqlCommand("", connection);
    updateCommand.CommandType = CommandType.Text;
    updateCommand.CommandText = @"UPDATE SalesLT.Product SET ListPrice = @ListPrice WHERE Name = @Name";
    updateCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
    updateCommand.Parameters.AddWithValue("@ListPrice", 500);

    int updatedrows = updateCommand.ExecuteNonQuery();
    Console.WriteLine($"Updated {updatedrows.ToString()} row(s).");
```

## <a name="delete-data"></a>Eliminare i dati

Usare [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) con un'istruzione [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) di Transact-SQL per eliminare i dati nel database SQL di Azure.

```csharp
    string hostName = 'yourserver.database.windows.net';
    string dbName = 'yourdatabase';
    string user = 'yourusername';
    string password = 'yourpassword';

    string strConn = $"server=tcp:+hostName+,1433;Initial Catalog=+dbName+;Persist Security Info=False;User ID=+user+;Password=+password+;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;";

    using (var connection = new SqlConnection(strConn))

SqlCommand deleteCommand = new SqlCommand("", connection);
deleteCommand.CommandType = CommandType.Text;
deleteCommand.CommandText = @"DELETE FROM SalesLT.Product WHERE Name = @Name";
deleteCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");

int deletedrows = deleteCommand.ExecuteNonQuery();
Console.WriteLine($"Deleted {deletedrows.ToString()} row(s).");
```

## <a name="complete-script"></a>Script completo

Lo script seguente contiene tutti i passaggi precedenti in un singolo blocco di codice.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
             string hostName = 'yourserver.database.windows.net';
             string dbName = 'yourdatabase';
             string user = 'yourusername';
             string password = 'yourpassword';

             string strConn = $"server=tcp:+hostName+,1433;Initial Catalog=+dbName+;Persist Security Info=False;User ID=+user+;Password=+password+;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;";

             using (var connection = new SqlConnection(strConn))

            {
                connection.Open();

                Console.WriteLine("Query data example:");
                Console.WriteLine("\n=========================================\n");

                SqlCommand selectCommand = new SqlCommand("", connection);
                selectCommand.CommandType = CommandType.Text;

                selectCommand.CommandText = @"SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
                   FROM [SalesLT].[ProductCategory] pc
                   JOIN [SalesLT].[Product] p
                   ON pc.productcategoryid = p.productcategoryid";

                SqlDataReader reader = selectCommand.ExecuteReader();

                while (reader.Read())
                {
                    // show data columns
                    Console.WriteLine($"{reader.GetString(0)}\t{reader.GetString(1)}");
                }
                reader.Close();
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();

                Console.WriteLine("\nInsert data example:");
                Console.WriteLine("=========================================\n");
                SqlCommand insertCommand = new SqlCommand("", connection);
                insertCommand.CommandType = CommandType.Text;
                insertCommand.CommandText = @"INSERT INTO[SalesLT].[Product]
                          ( [Name]
                          , [ProductNumber]
                          , [Color]
                          , [StandardCost]
                          , [ListPrice]
                          , [SellStartDate]
                          )
                VALUES
                (
                            @Name,
                            @ProductNumber,
                            @Color,
                            @StandardCost,
                            @ListPrice,
                            @SellStartDate)";

                insertCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
                insertCommand.Parameters.AddWithValue("@ProductNumber", "200989");
                insertCommand.Parameters.AddWithValue("@Color", "Blue");
                insertCommand.Parameters.AddWithValue("@StandardCost", 75);
                insertCommand.Parameters.AddWithValue("@ListPrice", 80);
                insertCommand.Parameters.AddWithValue("@SellStartDate", "7/1/2016");

                int newrows = insertCommand.ExecuteNonQuery();
                Console.WriteLine($"Inserted {newrows.ToString()} row(s).");
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();

                Console.WriteLine("\nUpdate data example:");
                Console.WriteLine("======================\n");
                SqlCommand updateCommand = new SqlCommand("", connection);
                updateCommand.CommandType = CommandType.Text;
                updateCommand.CommandText = @"UPDATE SalesLT.Product SET ListPrice = @ListPrice WHERE Name = @Name";
                updateCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
                updateCommand.Parameters.AddWithValue("@ListPrice", 500);

                int updatedrows = updateCommand.ExecuteNonQuery();
                Console.WriteLine($"Updated {updatedrows.ToString()} row(s).");
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();

                Console.WriteLine("\nDelete data example:");
                Console.WriteLine("======================\n");
                SqlCommand deleteCommand = new SqlCommand("", connection);
                deleteCommand.CommandType = CommandType.Text;
                deleteCommand.CommandText = @"DELETE FROM SalesLT.Product WHERE Name = @Name";
                deleteCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");

                int deletedrows = deleteCommand.ExecuteNonQuery();
                Console.WriteLine($"Deleted {deletedrows.ToString()} row(s).");
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

- Per la documentazione di .NET, vedere la [pagina corrispondente](https://docs.microsoft.com/dotnet/).
- Per informazioni sulle query e sulla modifica dei dati tramite Visual Studio Code, vedere [Visual Studio Code](https://code.visualstudio.com/docs).

