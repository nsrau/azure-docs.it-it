<properties
	pageTitle="Connettersi al database SQL tramite .NET (C#)"
	description="Usare il codice di esempio in questa guida introduttiva per creare con C# un'applicazione moderna, supportata da un database relazionale potente nel cloud con il database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="tobbox"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="tobiast"/>


# Uso del database SQL da .NET (C#)


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## Prerequisiti

### .NET Framework

.NET Framework deve essere preinstallato con Windows. Per Linux e Mac OS X è possibile scaricare .NET Framework dal [progetto Mono](http://www.mono-project.com/).

### Un database SQL

Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.

## Passaggio 1: Ottenere la stringa di connessione

[AZURE.INCLUDE [sql-database-include-connection-string-dotnet-20-portalshots](../../includes/sql-database-include-connection-string-dotnet-20-portalshots.md)]

## Passaggio 2: Connettersi

Per la connessione al database SQL viene usata la [classe System.Data.SqlClient.SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx).


```
using System.Data.SqlClient;

class Sample
{
  static void Main()
  {
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
	  {
		  conn.Open();
	  }
  }
}
```

## Passaggio 3: Eseguire una query

Per il recupero di un set di risultati di una query nel database SQL è possibile usare le classi [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) e [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx). Si noti che System.Data.SqlClient supporta anche il recupero di dati in una classe [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) offline.

```
using System;
using System.Data.SqlClient;

class Sample
{
	static void Main()
	{
	  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		{
			var cmd = conn.CreateCommand();
			cmd.CommandText = @"
					SELECT
						c.CustomerID
						,c.CompanyName
						,COUNT(soh.SalesOrderID) AS OrderCount
					FROM SalesLT.Customer AS c
					LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID
					GROUP BY c.CustomerID, c.CompanyName
					ORDER BY OrderCount DESC;";

			conn.Open();

			using(var reader = cmd.ExecuteReader())
			{
				while(reader.Read())
				{
					Console.WriteLine("ID: {0} Name: {1} Order Count: {2}", reader.GetInt32(0), reader.GetString(1), reader.GetInt32(2));
				}
			}					
		}
	}
}

```  

## Passaggio 4: Inserire una riga

Questo esempio illustra come eseguire un'istruzione [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) in modo sicuro, come passare i parametri che proteggono l'applicazione da attacchi [SQL injection](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e come recuperare il valore di [Chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx) generato automaticamente.

```
using System;
using System.Data.SqlClient;

class Sample
{
    static void Main()
    {
		using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={yourpassword};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
        {
            var cmd = conn.CreateCommand();
            cmd.CommandText = @"
                INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate)
                OUTPUT INSERTED.ProductID
                VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP)";

            cmd.Parameters.AddWithValue("@Name", "SQL Server Express");
            cmd.Parameters.AddWithValue("@Number", "SQLEXPRESS1");
            cmd.Parameters.AddWithValue("@Cost", 0);
            cmd.Parameters.AddWithValue("@Price", 0);

            conn.Open();

            int insertedProductId = (int)cmd.ExecuteScalar();

            Console.WriteLine("Product ID {0} inserted.", insertedProductId);
        }
    }
}
```

<!---HONumber=AcomDC_0323_2016-->