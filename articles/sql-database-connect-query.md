
<properties 
	pageTitle="Connettersi ed eseguire una query nel database SQL con C#" 
	description="Esempio di codice per un client C# che usa ADO.NET per connettersi e interagire con il database AdventureWorks nel servizio cloud del database SQL di Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="ckarst" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="cakarst"/>


# Connettersi ed eseguire una query nel database SQL con C## 

Questo argomento fornisce un esempio di codice C# che mostra come connettersi a un database SQL AdventureWorks esistente usando ADO.NET. L'esempio viene compilato in un'applicazione console che esegue una query nel database e visualizza i risultati.


## Prerequisiti


- Un database AdventureWorks esistente nel database SQL di Azure. È possibile [crearne uno in pochi minuti](sql-database-get-started.md).
- [Visual Studio con .NET Framework](https://www.visualstudio.com/it-it/visual-studio-homepage-vs.aspx)


## Passaggio 1: Applicazione console


1. Creare un'applicazione console C# usando Visual Studio.


![Connettersi ed eseguire query](./media/sql-database-connect-query/ConnectandQuery_VisualStudio.png)


## Passaggio 2: Esempio di codice SQL


1. Copiare l'esempio di codice seguente e incollarlo nell'applicazione console.


> [AZURE.WARNING]L'esempio di codice è il più breve possibile in modo da risultare di più facile comprensione. Non è destinato all'uso in produzione.


Questo codice non è stato progettato per ambienti di produzione. Se si desidera implementare codice pronto per la produzione, tenere presenti le seguenti procedure consigliate nel settore:


- Gestione delle eccezioni
- Logica di retry per errori temporanei
- Archiviazione sicura delle password in un file di configurazione



### Codice sorgente per l'esempio C#


Incollare il codice sorgente nel file **Program.cs**.


	using System;  // C#
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Data.SqlClient;
	
	namespace ConnectandQuery_Example
	{
		class Program
		{
			static void Main()
			{
				string SQLConnectionString = "[Your_Connection_String]";
				// Create a SqlConnection from the provided connection string.
				using (SqlConnection connection = new SqlConnection(SQLConnectionString))
				{
					// Begin to formulate the command.
					SqlCommand command = new SqlCommand();
					command.Connection = connection;

					// Specify the query to be executed.
					command.CommandType = System.Data.CommandType.Text;
						command.CommandText =
						@"SELECT TOP 10
						CustomerID, NameStyle, Title, FirstName, LastName
						FROM SalesLT.Customer";

					// Open connection to database.
					connection.Open();

					// Read data from the query.
					SqlDataReader reader = command.ExecuteReader();
					while (reader.Read())
					{
						// Formatting will depend on the contents of the query.
						Console.WriteLine("Value: {0}, {1}, {2}, {3}, {4}",
							reader[0], reader[1], reader[2], reader[3], reader[4]);
					}
				}
				Console.WriteLine("Press any key to continue...");
				Console.ReadKey();
			}
		}
	}


## Passaggio 3: Trovare la stringa di connessione per il database


1. Aprire il [portale di Azure](http://portal.azure.com/).
2. Fare clic su **Sfoglia** > **Database SQL** > **Database "Adventure Works"** > **Proprietà** > **Mostra stringhe di connessione database**.


![Portale](.\media\sql-database-connect-query\ConnectandQuery_portal.png)


Nel pannello delle stringhe di connessione per il database, è possibile visualizzare le stringhe di connessione appropriate per ADO.NET, ODBC, PHP e JDBC.


## Passaggio 4: Sostituire con informazioni di connessione reali


Nel codice sorgente incollato sostituire il segnaposto *[Your_Connection_String]* con la stringa di connessione e assicurarsi di sostituire *your_password_here* in tale stringa con la password effettiva.


## Passaggio 5: Eseguire l'applicazione


1. Per compilare ed eseguire l'applicazione, fare clic su **DEBUG** > **Avvia debug**.
2. I risultati della query vengono visualizzati nella finestra della console.

<!---HONumber=58-->