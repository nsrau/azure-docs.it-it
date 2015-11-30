<properties 
	pageTitle="Connettersi al database SQL tramite Java con JDBC in Windows" 
	description="Presentazione di un esempio di codice Java che è possibile usare per connettersi al database SQL di Azure. L'esempio usa JDBC e viene eseguito su un computer client Windows."
	services="sql-database" 
	documentationCenter="" 
	authors="LuisBosquez" 
	manager="jeffreyg" 
	editor="genemi"/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="09/28/2015" 
	ms.author="lbosq"/>


# Connettersi al database SQL tramite Java con JDBC in Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento presenta un esempio di codice Java che è possibile usare per connettersi al database SQL di Azure. L'esempio Java si basa su Java Development Kit (JDK) versione 1.8 e si connette a un database SQL di Azure tramite il driver JDBC.


## Requisiti


- [Driver Microsoft JDBC per SQL Server - SQL JDBC 4](http://www.microsoft.com/download/details.aspx?displaylang=en&id=11774).
- Qualsiasi piattaforma di sistema operativo che esegue [Java Development Kit 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Un database esistente in SQL Azure. Vedere l'[argomento introduttivo](sql-database-get-started.md) per informazioni su come creare un database di esempio e recuperare la stringa di connessione.


## Ambiente di test


L'esempio di codice Java in questo argomento presuppone che la tabella di test seguente esista già nel database SQL di Azure.


<!--
Could this instead be a #tempPerson table, so that the Java code sample could be fully self-sufficient and be runnable (with automatic cleanup)?
-->


	CREATE TABLE Person
	(
		id         INT    PRIMARY KEY    IDENTITY(1,1),
		firstName  VARCHAR(32),
		lastName   VARCHAR(32),
		age        INT
	);


## Stringa di connessione per il database SQL


L'esempio di codice crea un oggetto `Connection` usando una stringa di connessione. È possibile trovare la stringa di connessione mediante il [portale di anteprima di Azure](http://portal.azure.com/). Per informazioni dettagliate su come trovare la stringa di connessione, vedere [Creare il primo database SQL di Azure](sql-database-get-started.md).


> [AZURE.NOTE]Driver JDBC JTDS se si utilizza il driver JDBC JTDS, sarà necessario aggiungere "ssl = require" all'URL della connessione stringa ed è necessario impostare l'opzione seguente per JVM "-Djsse.enableCBCProtection=false". Questa opzione JVM disattiva la correzione di una vulnerabilità di sicurezza,quindi assicurarsi di comprendere i rischi coinvolti prima di impostare questa opzione.


## Esempio di codice Java


Questa sezione contiene la parte principale dell'esempio di codice Java e include commenti che indicano dove copiare e incollare i segmenti Java più piccoli presentati nelle sezioni successive. L'esempio in questa sezione può essere compilato ed eseguito anche senza copiare e incollare i segmenti aggiuntivi in corrispondenza dei commenti, ma in tal caso può solo stabilire la connessione ed essere terminato. Sono inclusi i commenti seguenti:


1. `// INSERT two rows into the table.`
2. `// TRANSACTION and commit for an UPDATE.`
3. `// SELECT rows from the table.`


In questa sezione è illustrata la parte principale dell'esempio di codice Java. L'esempio include la funzione `main` della classe `SQLDatabaseTest`.


	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
	public class SQLDatabaseTest {
	
		public static void main(String[] args) {
			String connectionString =
				"jdbc:sqlserver://your_server.database.windows.net:1433;" 
				+ "database=your_database;"
				+ "user=your_user@your_server;"
				+ "password=your_password;"
				+ "encrypt=true;"
				+ "trustServerCertificate=false;"
				+ "hostNameInCertificate=*.database.windows.net;"
				+ "loginTimeout=30;"; 
	
			// Declare the JDBC objects.
			Connection connection = null;
			Statement statement = null;
			ResultSet resultSet = null;
			PreparedStatement prepsInsertPerson = null;
			PreparedStatement prepsUpdateAge = null;
	
			try {
				connection = DriverManager.getConnection(connectionString);
	
				// INSERT two rows into the table.
				// ...
	
				// TRANSACTION and commit for an UPDATE.
				// ...
	
				// SELECT rows from the table.
				// ...
			}
			catch (Exception e) {
				e.printStackTrace();
			}
			finally {
				// Close the connections after the data has been handled.
				if (prepsInsertPerson != null) try { prepsInsertPerson.close(); } catch(Exception e) {}
				if (prepsUpdateAge != null) try { prepsUpdateAge.close(); } catch(Exception e) {}
				if (resultSet != null) try { resultSet.close(); } catch(Exception e) {}
				if (statement != null) try { statement.close(); } catch(Exception e) {}
				if (connection != null) try { connection.close(); } catch(Exception e) {}
			}
		}
	}


Per eseguire effettivamente questo esempio, nella stringa di connessione è necessario inserire i valori reali in sostituzione dei segnaposto:


- your\_server
- your\_database
- your\_user
- your\_password


## Inserire due righe nella tabella


Questo segmento Java esegue un'istruzione Transact-SQL INSERT per inserire due righe nella tabella Person. La sequenza generale è la seguente:


1. Generare un oggetto `PreparedStatement` usando l'oggetto `Connection`.
 - Includere il parametro `Statement.RETURN_GENERATED_KEYS` in modo da poter ottenere in un secondo momento il valore generato automaticamente per il valore della chiave **id**.
2. Chiamare il metodo `execute` sull'oggetto `PreparedStatement`.
3. Ottenere il valore numerico che è stato generato automaticamente per la chiave primaria usando l'oggetto `PreparedStatement`.
 - Questa informazione è correlata alla specifica AUTO\_INCREMENT sulla colonna **id** della tabella Person.


Copiare questo breve segmento Java e incollarlo nell'esempio di codice principale in corrispondenza del commento `// INSERT two rows into the table.`.


	// Create and execute an INSERT SQL prepared statement.
	String insertSql = "INSERT INTO Person (firstName, lastName, age) VALUES "
		+ "('Bill', 'Gates', 59), "
		+ "('Steve', 'Ballmer', 59);";
	
	prepsInsertPerson = connection.prepareStatement(
		insertSql,
		Statement.RETURN_GENERATED_KEYS);
	prepsInsertPerson.execute();
	// Retrieve the generated key from the insert.
	resultSet = prepsInsertPerson.getGeneratedKeys();
	// Iterate through the set of generated keys.
	while (resultSet.next()) {
		System.out.println("Generated: " + resultSet.getString(1));
	}


## Eseguire la transazione e il commit per un aggiornamento


Questo segmento di codice Java esegue un'istruzione Transact-SQL UPDATE per aumentare il valore `age` per ogni riga della tabella Person. La sequenza generale è la seguente:


1. Il metodo `setAutoCommit` viene chiamato per impedire il commit automatico degli aggiornamenti nel database.
2. Il metodo `executeUpdate` viene chiamato per eseguire l'aggiornamento nel contesto della transazione.
3. Il metodo `commit` viene chiamato per eseguire in modo esplicito il commit della transazione.


Copiare questo breve segmento Java e incollarlo nell'esempio di codice principale in corrispondenza del commento `// TRANSACTION and commit for an UPDATE.`.


	// Set AutoCommit value to false to execute a single transaction at a time.
	connection.setAutoCommit(false);
	
	// Write the SQL Update instruction and get the PreparedStatement object.
	String transactionSql = "UPDATE Person SET Person.age = Person.age + 1;";
	prepsUpdateAge = connection.prepareStatement(transactionSql);
	
	// Execute the statement.
	prepsUpdateAge.executeUpdate();
	
	//Commit the transaction.
	connection.commit();
	
	// Return the AutoCommit value to true.
	connection.setAutoCommit(true);


## Selezionare le righe da una tabella


Questo segmento Java esegue un'istruzione Transact-SQL SELECT per visualizzare tutte le righe aggiornate dalla tabella Person. La sequenza generale è la seguente:


1. Generare un oggetto `Statement` usando l'oggetto `Connection`.
2. Generare un oggetto `ResultSet` usando l'oggetto `Statement`.
3. Eseguire in modo ciclico una chiamata a `resultSet.next` per scorrere tutte le righe restituite.


Copiare questo breve segmento Java e incollarlo nell'esempio di codice principale in corrispondenza del commento `// SELECT rows from a table.`.


	// Create and execute a SELECT SQL statement.
	String selectSql = "SELECT firstName, lastName, age FROM dbo.Person";
	statement = connection.createStatement();
	resultSet = statement.executeQuery(selectSql);
	
	// Iterate through the result set and print the attributes.
	while (resultSet.next()) {
		System.out.println(resultSet.getString(2) + " "
			+ resultSet.getString(3));
	}

## Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Java](/develop/java/).

<!---HONumber=Nov15_HO4-->