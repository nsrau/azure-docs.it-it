<properties
	pageTitle="Connettersi al database SQL tramite Java con JDBC in Windows"
	description="Presentazione di un esempio di codice Java che è possibile usare per connettersi al database SQL di Azure. L'esempio usa JDBC e viene eseguito su un computer client Windows."
	services="sql-database"
	documentationCenter=""
	authors="ajlam"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="andrela"/>


# Connettersi al database SQL tramite Java con JDBC in Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento presenta un esempio di codice Java che è possibile usare per connettersi al database SQL di Azure. L'esempio Java si basa su Java Development Kit (JDK) versione 1.8 e si connette a un database SQL di Azure tramite il driver JDBC.

## Passaggio 1: Configurare l'ambiente di sviluppo

Installare i driver e le librerie:

- [Microsoft JDBC Driver 4.2 per SQL Server](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774).
- Qualsiasi piattaforma di sistema operativo che esegue [Java Development Kit 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

## Passaggio 2: Creare un database SQL

Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.

## Passaggio 3: Ottenere la stringa di connessione

[AZURE.INCLUDE [sql-database-include-connection-string-jdbc-20-portalshots](../../includes/sql-database-include-connection-string-jdbc-20-portalshots.md)]

> [AZURE.NOTE] Se si usa il driver JDBC JTDS, sarà necessario aggiungere "ssl = require" all'URL della stringa di connessione ed è necessario impostare l'opzione seguente per JVM "-Djsse.enableCBCProtection=false". Questa opzione JVM disattiva la correzione di una vulnerabilità di sicurezza,quindi assicurarsi di comprendere i rischi coinvolti prima di impostare questa opzione.

## Passaggio 4: Effettuare la connessione

Per connettersi al database SQL, usare la classe di connessione.

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
							
				try {
					connection = DriverManager.getConnection(connectionString);
	
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}

## Passaggio 5: Eseguire una query
In questo esempio, connettersi al database SQL di Azure, eseguire un'istruzione SELECT e restituire le righe selezionate.

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
				Statement statement = null; 
				ResultSet resultSet = null;
							
				try {
					connection = DriverManager.getConnection(connectionString);
	
					// Create and execute a SELECT SQL statement.
					String selectSql = "SELECT TOP 10 Title, FirstName, LastName from SalesLT.Customer";
					statement = connection.createStatement();
					resultSet = statement.executeQuery(selectSql);
	
					// Print results from select statement
					while (resultSet.next()) 
					{
						System.out.println(resultSet.getString(2) + " "
							+ resultSet.getString(3));
					}
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					// Close the connections after the data has been handled.
					if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
					if (statement != null) try { statement.close(); } catch(Exception e) {}
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}

## Passaggio 6: Inserire una riga
In questo esempio, eseguire un'istruzione INSERT, passare i parametri e recuperare il valore della chiave primaria generato automaticamente.

	// Use the JDBC driver
	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
		public class SQLDatabaseConnection {
	
			// Connect to your database.
			// Replace server name, username, and password with your credentials
			public static void main(String[] args) {
				String connectionString =
					"jdbc:sqlserver://yourserver.database.windows.net:1433;"
					+ "database=AdventureWorks;"
					+ "user=yourusername@yourserver;"
					+ "password=yourpassword;"
					+ "encrypt=true;"
					+ "trustServerCertificate=false;"
					+ "hostNameInCertificate=*.database.windows.net;"
					+ "loginTimeout=30;";
			
				// Declare the JDBC objects.
				Connection connection = null;
				Statement statement = null; 
				ResultSet resultSet = null;
				PreparedStatement prepsInsertProduct = null;
				
				try {
					connection = DriverManager.getConnection(connectionString);
	
					// Create and execute an INSERT SQL prepared statement.
					String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES "
						+ "('NewBike', 'BikeNew', 'Blue', 50, 120, '2016-01-01');";
	
					prepsInsertProduct = connection.prepareStatement(
						insertSql,
						Statement.RETURN_GENERATED_KEYS);
					prepsInsertProduct.execute();
					
					// Retrieve the generated key from the insert.
					resultSet = prepsInsertProduct.getGeneratedKeys();
					
					// Print the ID of the inserted row.
					while (resultSet.next()) {
						System.out.println("Generated: " + resultSet.getString(1));
					}
				}
				catch (Exception e) {
					e.printStackTrace();
				}
				finally {
					// Close the connections after the data has been handled.
					if (prepsInsertProduct != null) try { prepsInsertProduct.close(); } catch(Exception e) {}
					if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
					if (statement != null) try { statement.close(); } catch(Exception e) {}
					if (connection != null) try { connection.close(); } catch(Exception e) {}
				}
			}
		}


## Passaggi successivi

Per altre informazioni, vedere il [Centro per sviluppatori di Java](/develop/java/).

<!---HONumber=AcomDC_0413_2016-->