<properties 
	pageTitle="Uso del database SQL da .NET (C#)" 
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
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="tobiast"/>


# Uso del database SQL da .NET (C#) 


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


## Requisiti

### .NET Framework

.NET Framework deve essere preinstallato con Windows. Per Linux e Mac OS X è possibile scaricare .NET Framework dal [progetto Mono](http://www.mono-project.com/).

### Un database SQL

Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio e ottenere la stringa di connessione.

## Connettersi al database SQL

Per la connessione al database SQL viene usata la [classe System.Data.SqlClient.SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.aspx).
	
	```
	using System.Data.SqlClient;
	
	class Sample
	{
	  static void Main()
	  {
		  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
		  {
			  conn.Open();	
		  }
	  }
	}	
	```

## Eseguire una query e recuperare il set di risultati 

Per il recupero di un set di risultati di una query nel database SQL è possibile usare le classi [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx) e [SqlDataReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqldatareader.aspx). Si noti che System.Data.SqlClient supporta anche il recupero di dati in una classe [System.Data.DataSet](https://msdn.microsoft.com/library/system.data.dataset.aspx) offline.
	
	```
	using System;
	using System.Data.SqlClient;
	
	class Sample
	{
		static void Main()
		{
		  using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
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

## Inserimento di una riga, passaggio di parametri e recupero del valore di chiave primaria generato 

Nel database SQL, per generare automaticamente i valori di [chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx), è possibile usare la proprietà [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx). Questo esempio illustra come eseguire un'[istruzione insert](https://msdn.microsoft.com/library/ms174335.aspx), come passare i parametri in modo sicuro per evitare attacchi [SQL injection](https://msdn.microsoft.com/magazine/cc163917.aspx) e come recuperare il valore di chiave primaria generato automaticamente.

Per eseguire un'istruzione e recuperare la prima colonna e le prima riga restituite da tale istruzione, è possibile usare il metodo [ExecuteScalar](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executescalar.aspx) della classe [System.Data.SqlClient.SqlCommand](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.aspx). Per restituire i valori inseriti come set di risultati all'applicazione chiamante, è possibile usare la clausola [OUTPUT](https://msdn.microsoft.com/library/ms177564.aspx) dell'istruzione INSERT. Si noti che la clausola OUTPUT è supportata anche dalle istruzioni [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx), [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) e [MERGE](https://msdn.microsoft.com/library/bb510625.aspx). Se vengono inserite più righe, è consigliabile usare il metodo [ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) per recuperare i valori inseriti per tutte le righe.
	
	```
	class Sample
	{
	    static void Main()
	    {
			using(var conn = new SqlConnection("Server=tcp:yourserver.database.windows.net,1433;Database=yourdatabase;User ID=yourlogin@yourserver;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"))
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

<!---HONumber=58--> 