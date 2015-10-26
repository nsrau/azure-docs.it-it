<properties 
	pageTitle="Come utilizzare SQL Azure (Java) | Microsoft Azure" 
	description="Informazioni su come usare il database SQL di Azure tramite codice Java." 
	services="sql-database" 
	documentationCenter="java" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor="jimbe"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/12/2015" 
	ms.author="genemi"/>

# Come usare il database SQL di Azure in Java

Nella procedura seguente viene illustrato come utilizzare il database SQL di Azure con Java. Per semplicità vengono illustrati solo esempi di riga di comando, tuttavia, per applicazioni Web ospitate in locale, all'interno di Azure o in altri ambienti, saranno applicabili passaggi molto simili a quelli illustrati. Questa guida illustra la creazione di un server e quella di un database dal [portale di gestione di Azure](https://windows.azure.com).

## Informazioni sul database SQL di Azure

Il database SQL di Azure offre un sistema di gestione di database relazionali per Azure e si basa sulla tecnologia SQL Server. Con un'istanza di database SQL, è possibile eseguire facilmente il provisioning e la distribuzione nel cloud di soluzioni di database relazionali e usufruire di un data center distribuito che offre disponibilità, scalabilità e sicurezza di livello aziendale, con i vantaggi delle funzionalità predefinite di riparazione automatica e protezione dati.



## Concetti
Poiché il database SQL di Azure è basato sulle tecnologie di SQL Server, l'accesso da Java è molto simile in entrambi i casi. È possibile sviluppare un'applicazione in locale, utilizzando SQL Server, e quindi connettersi al database SQL cambiando solo la stringa di connessione. È possibile utilizzare un driver JDBC per SQL Server nell'applicazione. Tuttavia, tra il database SQL e SQL Server esistono alcune differenze che potrebbero influire sull'applicazione. Per ulteriori informazioni, vedere [Linee guida e limitazioni (database SQL)](http://msdn.microsoft.com/library/windowsazure/ff394102.aspx).

Per risorse aggiuntive sul database SQL, vedere la sezione [Passaggi successivi][].

## Prerequisiti

Di seguito vengono riportati i prerequisiti per l'uso del database SQL con Java.

* Java Developer Kit (JDK) v 1.6 o versione successiva.
* Una sottoscrizione Azure, che può essere acquistata all'indirizzo <http://www.microsoft.com/windowsazure/offers/>.
* Se si usa Eclipse, è necessario IDE Eclipse per sviluppatori Java EE, Indigo o versione successiva. È possibile scaricare il pacchetto all'indirizzo <http://www.eclipse.org/downloads/>. Sarà anche necessario installare Azure Toolkit per Eclipse. Durante l'installazione del plug-in, assicurarsi che sia incluso Microsoft JDBC Driver 4.0 per SQL Server. Per ulteriori informazioni, vedere [Installare il Toolkit di Azure per Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Se non si usa Eclipse, è necessario Microsoft JDBC Driver 4.0 per SQL Server, che è possibile scaricare dall'indirizzo <http://www.microsoft.com/download/details.aspx?id=11774>.

## Creazione di un database SQL di Azure

Prima di usare il database SQL di Azure nel codice Java, è necessario creare un server di database SQL di Azure.

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **Nuovo**.

    ![Creazione del nuovo database SQL][create_new]

3. Fare clic su **Database SQL**, quindi su **Creazione personalizzata**.

    ![Creazione del database SQL personalizzato][create_new_sql_db]

4. Nella finestra di dialogo **Impostazioni database** specificare il nome per il database. Per le finalità di questa guida, utilizzare **gettingstarted** come nome per il database.
5. Nella casella **Server** selezionare **Nuovo server di database SQL**. Usare i valori predefiniti per gli altri campi.

    ![Impostazioni del database SQL][create_database_settings]

6. Fare clic sulla freccia avanti.
7. Nella finestra di dialogo **Impostazioni server** specificare un nome di accesso a SQL Server. Per le finalità di questa guida, è stato utilizzato **MySQLAdmin**. Specificare e confermare una password. Specificare un'area e assicurarsi che l'opzione **Consenti ai servizi di Azure di accedere al server** sia selezionata.

    ![Impostazioni di SQL Server][create_server_settings]

8. Fare clic sul pulsante di completamento.

## Determinazione della stringa di connessione del database SQL

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **SQL Databases**.
3. Fare clic sul database che si desidera utilizzare.
4. Fare clic su **Show connection strings**.
5. Evidenziare il contenuto della stringa di connessione **JDBC**.

    ![Determinazione della stringa di connessione JDBC][get_jdbc_connection_string]

6. Fare clic con il pulsante destro del mouse sul contenuto evidenziato della stringa di connessione **JDBC** e scegliere **Copia**.
7. È ora possibile incollare questo valore nel file di codice per creare una stringa di connessione con il formato seguente. Sostituire *your\_server* (due occorrenze) con il testo copiato nel passaggio precedente, quindi sostituire *your\_password* con il valore della password specificata al momento della creazione dell'account per il database SQL. Se sono stati utilizzati valori diversi da **gettingstarted** e **MySQLAdmin** sarà inoltre necessario modificare i valori assegnati rispettivamente a **database=** e **user=**. 

    String connectionString = "jdbc:sqlserver://*your_server*.database.windows.net:1433" + ";" + "database=gettingstarted" + ";" + "user=MySQLAdmin@*your\_server*" + ";" + "password=*your\_password*" + ";" + "encrypt=true" + ";" + "hostNameInCertificate=*.int.mscds.com" + ";" + "loginTimeout=30";

Questa stringa verrà usata in seguito in questa guida; per il momento sono stati illustrati i passaggi per determinare la stringa di connessione. A seconda delle esigenze dell'applicazione, potrebbe inoltre essere necessario utilizzare le impostazioni **encrypt** e **hostNameInCertificate**, nonché modificare l'impostazione **loginTimeout**.

## Per consentire l'accesso a un intervallo di indirizzi IP

1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **SQL Databases**.
3. Fare clic su **Servers**.
4. Fare clic sul server che si desidera utilizzare.
5. Fare clic su **Manage**.
6. Fare clic su **Configure**.
7. In **Allowed IP addresses** immettere il nome di una nuova regola IP. Specificare gli indirizzi IP di inizio e fine intervallo. Per praticità, viene mostrato l'indirizzo IP del client attuale. Il seguente esempio consente un singolo indirizzo IP (l'indirizzo IP in uso sarà diverso).

    ![Finestra di dialogo Indirizzi IP consentiti][allowed_ips_dialog]

8. Fare clic sul pulsante di completamento. Agli indirizzi IP specificati verrà ora consentito l'accesso al server di database.

## Per usare il database SQL di Azure in Java

1. Creare un progetto Java. Per le finalità di questa guida, denominarlo **HelloSQLAzure**.
2. Aggiungere un file di classe Java denominato **HelloSQLAzure.java** al progetto.
3. Aggiungere **Microsoft JDBC Driver per SQL Server** al percorso di compilazione.

   Se si usa Eclipse:

    1. Within Eclipse's Project Explorer, right-click the **HelloSQLAzure** project and click **Properties**.
    2. In the left-hand pane of the **Properties** dialog, click **Java Build Path**.
    3. Click the **Libraries** tab, and then click **Add Library**.
    4. In the **Add Library** dialog, select **Microsoft JDBC Driver 4.0 for SQL Server**, click **Next**, and then click **Finish**.
    5. Click **OK** to close the **Properties** dialog.

    If you are not using Eclipse, add the Microsoft JDBC Driver 4.0 for SQL Server JAR to your class path. For related information, see [Using the JDBC Driver](http://msdn.microsoft.com/library/ms378526.aspx).

4. Nel codice **HelloSQLAzure.java** aggiungere istruzioni `import` come illustrato di seguito:

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

5. Specificare la stringa di connessione. Di seguito è fornito un esempio. Come nei passaggi precedenti, sostituire *your\_server* (due occorrenze), *your\_user* e *your\_password* con i valori appropriati per il server di database SQL.

        String connectionString =
        	"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
        		"database=master" + ";" + 
        		"user=your_user@your_server" + ";" +  
        		"password=your_password";

È ora possibile aggiungere codice per comunicare con il server di database SQL.

## Comunicazione con il database SQL di Azure dal codice

Nella parte restante di questo argomento vengono riportati esempi per effettuare le seguenti operazioni:

1. Connessione al server di database SQL.
2. Definizione di un'istruzione SQL, ad esempio per creare o eliminare una tabella, inserire/selezionare/eliminare righe e così via.
3. Esecuzione dell'istruzione SQL, tramite chiamata a **executeUpdate** o a **executeQuery**.
4. Visualizzazione dei risultati della query, se appropriato.

Le sezioni seguenti sono progettate per essere lette (acquisite) nell'ordine dato. Il primo frammento è un esempio completo, gli altri si affidano a una parte del framework dell'esempio completo, ad esempio le istruzioni **import**, le dichiarazioni **class** e **main** la gestione degli errori e la chiusura di risorse.

## Per creare una tabella

Nel codice seguente viene illustrato come creare una tabella denominata **Person**.

	import java.sql.*;
	import com.microsoft.sqlserver.jdbc.*;
	
	public class HelloSQLAzure {
	
	    public static void main(String[] args) 
	    {
	
			// Connection string for your SQL Database server.
			// Change the values assigned to your_server, 
			// your_user@your_server,
			// and your_password.
			String connectionString = 
				"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
					"database=gettingstarted" + ";" + 
					"user=your_user@your_server" + ";" +  
					"password=your_password";
			
			// The types for the following variables are
			// defined in the java.sql library.
			Connection connection = null;  // For making the connection
			Statement statement = null;    // For the SQL statement
			ResultSet resultSet = null;    // For the result set, if applicable
			
			try
			{
			    // Ensure the SQL Server driver class is available.
			    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
			
			    // Establish the connection.
			    connection = DriverManager.getConnection(connectionString);
			
			    // Define the SQL string.
			    String sqlString = 
					"CREATE TABLE Person (" + 
			        	"[PersonID] [int] IDENTITY(1,1) NOT NULL," +
			            "[LastName] [nvarchar](50) NOT NULL," + 
			            "[FirstName] [nvarchar](50) NOT NULL)";
			
			    // Use the connection to create the SQL statement.
			    statement = connection.createStatement();
			
			    // Execute the statement.
			    statement.executeUpdate(sqlString);
			
			    // Provide a message when processing is complete.
			    System.out.println("Processing complete.");
			
			}
			// Exception handling
	        catch (ClassNotFoundException cnfe)  
	        {
	            
	            System.out.println("ClassNotFoundException " +
	                               cnfe.getMessage());
	        }
	        catch (Exception e)
	        {
	            System.out.println("Exception " + e.getMessage());
	            e.printStackTrace();
	        }
	        finally
	        {
	            try
	            {
	                // Close resources.
	                if (null != connection) connection.close();
	                if (null != statement) statement.close();
	                if (null != resultSet) resultSet.close();
	            }
	            catch (SQLException sqlException)
	            {
	                // No additional action if close() statements fail.
	            }
	        }
	        
	    }
	
	}
	

## Per creare un indice in una tabella

Nel codice seguente viene illustrato come creare un indice denominato **index1** sulla tabella **Person**, utilizzando la colonna **PersonID**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"CREATE CLUSTERED INDEX index1 " + "ON Person (PersonID)";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...



## Per inserire righe

Nel codice seguente viene illustrato come aggiungere righe alla tabella **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"SET IDENTITY_INSERT Person ON " + 
	        	"INSERT INTO Person " + 
	            "(PersonID, LastName, FirstName) " + 
	            "VALUES(1, 'Abercrombie', 'Kim')," + 
	            	  "(2, 'Goeschl', 'Gerhard')," + 
	                  "(3, 'Grachev', 'Nikolay')," + 
	                  "(4, 'Yee', 'Tai')," + 
	                  "(5, 'Wilson', 'Jim')";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
## Per recuperare righe

Nel codice seguente viene illustrato come recuperare righe dalla tabella **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "SELECT TOP 10 * FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Loop through the results
	    while (resultSet.next())
	    {
	        // Print out the row data
	        System.out.println(
	        	"Person with ID " + 
	        	resultSet.getString("PersonID") + 
	        	" has name " +
	        	resultSet.getString("FirstName") + " " +
	       		resultSet.getString("LastName"));
	        }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 Nel codice sopra riportato sono state selezionate le prime 10 righe dalla tabella **Person**. Se si desidera recuperare tutte le stringhe, modificare l'istruzione SQL come segue:

	String sqlString = "SELECT * FROM Person";

 
## Per recuperare righe inserendo una clausola WHERE

Per recuperare righe inserendo una clausola, usare il codice sopra riportato, modificando l'istruzione SQL per includere una clausola. Nella seguente istruzione SQL è inclusa una clausola per le righe il cui valore **FirstName** è uguale a **Jim**.

	// Define the SQL string.
	String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";
	
Le clausole WHERE possono inoltre essere usate per recuperare conteggi, aggiornare righe o eliminarle.

## Per recuperare un conteggio di righe

Nel codice seguente viene illustrato come recuperare un conteggio di righe dalla tabella **Person**.
 
	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	// Define the SQL string.
	    String sqlString = "SELECT COUNT (PersonID) FROM Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Print out the returned number of rows.
	    while (resultSet.next())
	    {
	        System.out.println("There were " + 
	                         resultSet.getInt(1) +
	                         " rows returned.");
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Per aggiornare righe

Il seguente codice illustra come aggiornare righe. In questo esempio il valore **LastName** viene modificato in **Kim** per tutte le righe in cui il valore **FirstName** corrisponde a **Jim**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"UPDATE Person " + "SET LastName = 'Kim' " + "WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	    
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}// Exception handling and resource closing not shown...

 

## Per eliminare righe

Il seguente codice illustra come eliminare righe. In questo esempio tutte le righe il cui valore **FirstName** corrisponde a **Jim** vengono eliminate.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DELETE from Person " + 
				"WHERE FirstName='Jim'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...
	
 
## Per verificare se una tabella esiste

Il seguente codice illustra come determinare se una tabella esiste.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"IF EXISTS (SELECT 1 " +
	        	"FROM sysobjects " + 
	            "WHERE xtype='u' AND name='Person') " +
	            "SELECT 'Person table exists.'" +
	            "ELSE  " +
	            "SELECT 'Person table does not exist.'";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    resultSet = statement.executeQuery(sqlString);
	
	    // Display the result.
	    while (resultSet.next())
	    {
	        System.out.println(resultSet.getString(1));
	    }
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Per eliminare un indice

Nel codice seguente viene illustrato come eliminare un indice denominato **index1** dalla tabella **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +
			"database=gettingstarted" + ";" +
			"user=your_user@your_server" + ";" +
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = 
			"DROP INDEX index1 " + 
	        	"ON Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

 
## Per eliminare una tabella

Nel codice seguente viene illustrato come eliminare una tabella denominata **Person**.

	// Connection string for your SQL Database server.
	// Change the values assigned to your_server, 
	// your_user@your_server,
	// and your_password.
	String connectionString = 
		"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
			"database=gettingstarted" + ";" + 
			"user=your_user@your_server" + ";" +  
			"password=your_password";
	
	// The types for the following variables are
	// defined in the java.sql library.
	Connection connection = null;  // For making the connection
	Statement statement = null;    // For the SQL statement
	ResultSet resultSet = null;    // For the result set, if applicable
	
	try
	{
	    // Ensure the SQL Server driver class is available.
	    Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
	
	    // Establish the connection.
	    connection = DriverManager.getConnection(connectionString);
	
	    // Define the SQL string.
	    String sqlString = "DROP TABLE Person";
	
	    // Use the connection to create the SQL statement.
	    statement = connection.createStatement();
	
	    // Execute the statement.
	    statement.executeUpdate(sqlString);
	
	    // Provide a message when processing is complete.
	    System.out.println("Processing complete.");
	
	}
	// Exception handling and resource closing not shown...

## Usare il database SQL in Java all'interno di una distribuzione di Azure

Per usare un database SQL in Java all'interno di una distribuzione di Azure, oltre a usare Microsoft JDBC Driver 4.0 per SQL Server come libreria nel percorso delle classi indicato sopra, sarà necessario includerlo nella creazione del pacchetto della distribuzione.


**Creazione del pacchetto con Microsoft JDBC Driver 4.0 per SQL Server quando si usa Eclipse**

1. In Project Explorer di Eclipse fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Properties**.
2. Nel riquadro sinistro della finestra di dialogo **Properties** fare clic su **Deployment Assembly**, quindi scegliere **Add**.
3. Nella finestra di dialogo **New Assembly Directive** fare clic su **Java Build Path Entries**, quindi scegliere **Next**.
4. Selezionare **Microsoft JDBC Driver 4.0 per SQL Server**, quindi fare clic su **Finish**.
5. Fare clic su **OK** per chiudere la finestra di dialogo **Properties**.
6. Esportare il file WAR del progetto nella cartella approot e ricompilare il progetto Azure secondo i passaggi forniti in [Creazione di un'applicazione Hello World utilizzando il Toolkit di Azure](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx). Nell'argomento viene anche descritto come eseguire l'applicazione nell'emulatore di calcolo e in Azure.

**Creazione del pacchetto con Microsoft JDBC Driver 4.0 per SQL Server quando non si usa Eclipse**

* Verificare che la libreria Microsoft JDBC Driver 4.0 per SQL Server si trovi nello stesso ruolo di Azure dell'applicazione Java e che sia stata aggiunta al percorso della classe dell'applicazione.

## Passaggi successivi

Per ulteriori informazioni su Microsoft JDBC Driver per SQL Server, vedere [Panoramica del driver JDBC](http://msdn.microsoft.com/library/ms378749.aspx). Per ulteriori informazioni sul database SQL, vedere [Panoramica dei database SQL](http://msdn.microsoft.com/library/windowsazure/ee336241.aspx).

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di Java](/develop/java/).

[Concepts]: #concepts
[Prerequisites]: #prerequisites
[Creating an Azure SQL Database]: #create_db
[Determining the SQL Database connection string]: #determine_connection_string
[To allow access to a range of IP addresses]: #specify_allowed_ips
[To use Azure SQL Database in Java]: #use_sql_azure_in_java
[Communicating with Azure SQL Database from your code]: #communicate_from_code
[To create a table]: #to_create_table
[To create an index on a table]: #to_create_index
[To insert rows]: #to_insert_rows
[To retrieve rows]: #to_retrieve_rows
[To retrieve rows using a WHERE clause]: #to_retrieve_rows_using_where
[To retrieve a count of rows]: #to_retrieve_row_count
[To update rows]: #to_update_rows
[To delete rows]: #to_delete_rows
[To check whether a table exists]: #to_check_table_existence
[To drop an index]: #to_drop_index
[To drop a table]: #to_drop_table
[Using SQL Database in Java within an Azure Deployment]: #using_in_azure
[Passaggi successivi]: #nextsteps
[create_new]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
[create_new_sql_db]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
[create_database_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
[create_server_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
[get_jdbc_connection_string]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
[allowed_ips_dialog]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png
 

<!---HONumber=Oct15_HO3-->