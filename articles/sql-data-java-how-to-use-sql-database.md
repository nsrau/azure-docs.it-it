<properties 
	pageTitle="Come usare SQL Azure (Java) - Guida alle funzionalità di Azure" 
	description="Informazioni su come usare il database SQL di Azure tramite codice Java." 
	services="sql-database" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="robmcm"/>

# Come usare il database SQL di Azure in Java

La seguente procedura illustra come usare il database SQL di Azure con Java. Per semplicità vengono illustrati solo esempi di riga di comando, tuttavia, per applicazioni Web ospitate in locale, all'interno di Azure o in altri ambienti, saranno applicabili passaggi molto simili a quelli illustrati. Questa guida illustra la creazione di un server e quella di un database dal [portale di gestione di Azure](https://windows.azure.com).

## Informazioni sul database SQL di Azure

Il database SQL di Azure offre un sistema di gestione di database relazionali per Azure e si basa sulla tecnologia SQL Server. Con un'istanza di database SQL, è possibile eseguire facilmente il provisioning e la distribuzione nel cloud di soluzioni di database relazionali e usufruire di un data center distribuito che offre disponibilità, scalabilità e sicurezza di livello aziendale, con i vantaggi delle funzionalità predefinite di riparazione automatica e protezione dati.



## Concetti
Poiché il database SQL di Azure è basato sulle tecnologie di SQL Server, l'accesso da Java è molto simile in entrambi i casi. È possibile sviluppare un'applicazione in locale, usando SQL Server, e quindi connettersi al database SQL cambiando solo la stringa di connessione. È possibile usare un driver JDBC per SQL Server nell'applicazione. Tuttavia, tra il database SQL e SQL Server esistono alcune differenze che potrebbero influire sull'applicazione. Per altre informazioni, vedere [Linee guida e limitazioni per il database SQL di Azure](http://msdn.microsoft.com/library/windowsazure/ff394102.aspx).

Per risorse aggiuntive sul database SQL, vedere la sezione [Passaggi successivi][].

## Prerequisiti

Di seguito vengono riportati i prerequisiti per l'uso del database SQL con Java.

* Java Developer Kit (JDK) v 1.6 o versione successiva.
* Una sottoscrizione Azure, che può essere acquistata all'indirizzo <http://www.microsoft.com/windowsazure/offers/>.
* Se si usa Eclipse, è necessario IDE Eclipse per sviluppatori Java EE, Indigo o versione successiva. È possibile scaricare il pacchetto all'indirizzo <http://www.eclipse.org/downloads/>. Il plug-in Azure per Eclipse con Java (da Microsoft Open Technologies). Durante l'installazione del plug-in, assicurarsi che sia incluso Microsoft JDBC Driver 4.0 per SQL Server. Per altre informazioni, vedere l'argomento relativo all'[installazione del plug-in Azure per Eclipse con Java (da Microsoft Open Technologies)](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Se non si usa Eclipse, è necessario Microsoft JDBC Driver 4.0 per SQL Server, che è possibile scaricare dall'indirizzo <http://www.microsoft.com/download/details.aspx?id=11774>.

## Creazione di un database SQL di Azure

Prima di usare il database SQL di Azure nel codice Java, è necessario creare un server di database SQL di Azure.

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **Nuovo**.

    ![Create new SQL database][create_new]

3. Fare clic su **Database SQL**, quindi su **Creazione personalizzata**.

    ![Create custom SQL database][create_new_sql_db]

4. Nella finestra di dialogo **Impostazioni database** specificare il nome per il database. Per le finalità di questa guida, usare **gettingstarted** come nome del database.
5. Per il campo **Server** selezionare **Nuovo server di database SQL**. Usare i valori predefiniti per gli altri campi.

    ![SQL database settings][create_database_settings]

6. Fare clic sulla freccia avanti.	
7. Nella finestra di dialogo **Impostazioni server** specificare un nome di accesso a SQL Server. Per le finalità di questa guida, è stato usato **MySQLAdmin**. Specificare e confermare una password. Specificare una regione e assicurarsi che l'opzione **Consentire ai servizi di Microsoft Azure di accedere al server** sia selezionata.

    ![SQL server settings][create_server_settings]

8. Fare clic sul pulsante di completamento.

## Determinazione della stringa di connessione del database SQL

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2. Fare clic su **Database SQL**.
3. Fare clic sul database che si desidera usare.
4. Fare clic su **Mostra stringhe di connessione**.
5. Evidenziare il contenuto della stringa di connessione **JDBC**.

    ![Determine JDBC connection string][get_jdbc_connection_string]

6. Fare clic con il pulsante destro del mouse sul contenuto evidenziato della stringa di connessione **JDBC** e scegliere **Copia**.
7. È ora possibile incollare questo valore nel file di codice per creare una stringa di connessione con il seguente formato. Sostituire *your_server* (due occorrenze) con il testo copiato nel passaggio precedente, quindi sostituire *your_password* con il valore della password specificato al momento della creazione dell'account per il database SQL. Se sono stati usati valori diversi da **gettingstarted** e **MySQLAdmin**, sarà anche necessario sostituire i valori assegnati rispettivamente a **database=** e **user=**. 

    String connectionString =
		"jdbc:sqlserver://*your_server*.database.windows.net:1433" + ";" +  
    	"database=gettingstarted" + ";" + 
    	"user=MySQLAdmin@*your_server*" + ";" +  
    	"password=*your_password*" + ";" +  
        "encrypt=true" + ";" +
        "hostNameInCertificate=*.int.mscds.com" + ";" +  
        "loginTimeout=30";

Questa stringa verrà usata in seguito in questa guida; per il momento sono stati illustrati i passaggi per determinare la stringa di connessione. A seconda delle esigenze dell'applicazione, potrebbe inoltre non essere necessario usare le impostazioni **encrypt** e **hostNameInCertificate**, mentre potrebbe essere necessario modificare l'impostazione **loginTimeout**.

## Per consentire l'accesso a un intervallo di indirizzi IP

1. Accedere al [portale di gestione](https://manage.windowsazure.com).
2. Fare clic su **Database SQL**.
3. Fare clic su **Server**.
4. Fare clic sul server che si desidera usare.
5. Fare clic su **Gestisci**.
6. Fare clic su **Configura**.
7. In **Indirizzi IP consentiti** immettere il nome di una nuova regola IP. Specificare gli indirizzi IP di inizio e fine intervallo. Per praticità, viene mostrato l'indirizzo IP del client attuale. Il seguente esempio consente un singolo indirizzo IP (l'indirizzo IP in uso sarà diverso).

    ![Allowed IP addresses dialog][allowed_ips_dialog]

8. Fare clic sul pulsante di completamento. Agli indirizzi IP specificati verrà ora consentito l'accesso al server di database.

## Per usare il database SQL di Azure in Java

1. Creare un progetto Java. Per le finalità di questa guida, denominarlo **HelloSQLAzure**.
2. Aggiungere un file di classe Java denominato **HelloSQLAzure.java** al progetto.
3. Aggiungere **Microsoft JDBC Driver per SQL Server** al percorso di compilazione.

   Se si usa Eclipse:

    1. In Project Explorer di Eclipse fare clic con il pulsante destro del mouse sul progetto **HelloSQLAzure**, quindi scegliere **Properties**.
    2. Nel riquadro sinistro della finestra di dialogo **Properties** fare clic su **Java Build Path**.
    3. Fare clic sulla scheda **Libraries**, quindi su **Add Library**.
    4. Nella finestra di dialogo **Add Library** selezionare **Microsoft JDBC Driver 4.0 for SQL Server**, fare clic su **Next**, quindi su **Finish**.
    5. Fare clic su **OK** per chiudere la finestra di dialogo **Properties**.

    Se non si usa Eclipse, aggiungere il file JAR di Microsoft JDBC Driver 4.0 per SQL Server al percorso della classe. Per informazioni correlate, vedere [Utilizzo del driver JDBC](http://msdn.microsoft.com/library/ms378526.aspx).

4. Nel codice **HelloSQLAzure.java** aggiungere istruzioni `import` come illustrato di seguito:

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

5. Specificare la stringa di connessione. Di seguito è fornito un esempio. Come nei passaggi precedenti, sostituire *your_server* (due occorrenze), *your_user* e *your_password* con i valori appropriati per il server di database SQL.

        String connectionString =
        	"jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
        		"database=master" + ";" + 
        		"user=your_user@your_server" + ";" +  
        		"password=your_password";

È ora possibile aggiungere codice per comunicare con il server di database SQL.

## Comunicazione con il database SQL di Azure dal codice

Nel resto di questo argomento vengono riportati esempi per effettuare le seguenti operazioni:

1. Connessione al server di database SQL.
2. Definizione di un'istruzione SQL, ad esempio per creare o eliminare una tabella, inserire/selezionare/eliminare righe e così via.
3. Esecuzione dell'istruzione SQL, tramite chiamata a **executeUpdate** o **executeQuery**.
4. Visualizzazione dei risultati della query, se appropriato.

Le seguenti sezioni sono progettate per essere lette (acquisite) nell'ordine dato. Il primo frammento è un esempio completo; gli altri si affidano a una parte del framework nell'esempio completo, ad esempio le istruzioni **import**, le dichiarazioni **class** e **main**, la gestione degli errori e la chiusura di risorse.

## Per creare una tabella

Il seguente codice illustra come creare una tabella denominata **Person**.

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

Il seguente codice illustra come creare un indice denominato **index1** nella tabella **Person**, usando la colonna **PersonID**.

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

Il seguente codice illustra come aggiungere righe alla tabella **Person**.

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

Il seguente codice illustra come recuperare righe dalla tabella **Person**.

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

 Il codice precedente ha selezionato le prime 10 righe dalla tabella **Person**. Se si desidera recuperare tutte le stringhe, modificare l'istruzione SQL come segue:

	String sqlString = "SELECT * FROM Person";

 
## Per recuperare righe inserendo una clausola WHERE

Per recuperare righe inserendo una clausola, usare il codice sopra riportato, modificando l'istruzione SQL per includere una clausola. La seguente istruzione SQL include una clausola per le righe il cui valore **FirstName** è uguale a **Jim**.

	// Define the SQL string.
	String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";
	
Le clausole WHERE possono inoltre essere usate per recuperare conteggi, aggiornare righe o eliminarle.

<h2><a id="to_retrieve_row_count"></a>Per recuperare un conteggio di righe</h2>

Il seguente codice illustra come recuperare un conteggio di righe dalla tabella **Person**.
 
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

Il seguente codice illustra come eliminare un indice denominato **index1** dalla tabella **Person**.

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

Il seguente codice illustra come eliminare una tabella denominata **Person**.

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
2. Nel riquadro sinistro della finestra di dialogo **Properties** fare clic su **Deployment Assembly**, quindi su **Add**.
3. Nella finestra di dialogo **New Assembly Directive** fare clic su **Java Build Path Entries**, quindi su **Next**.
4. Selezionare **Microsoft JDBC Driver 4.0 SQL Server**, quindi fare clic su **Finish**.
5. Fare clic su **OK** per chiudere la finestra di dialogo **Properties**.
6. Esportare il file WAR del progetto nella cartella approot e ricompilare il progetto Azure eseguendo i passaggi descritti nell'argomento relativo alla [creazione di un'applicazione Hello World usando il plug-in di Azure per Eclipse con Java (da Microsoft Open Technologies)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx). Nell'argomento viene anche descritto come eseguire l'applicazione nell'emulatore di calcolo e in Azure.

**Creazione del pacchetto con Microsoft JDBC Driver 4.0 per SQL Server quando non si usa Eclipse**

* Verificare che la libreria Microsoft JDBC Driver 4.0 per SQL Server si trovi nello stesso ruolo di Azure dell'applicazione Java e che sia stata aggiunta al percorso della classe dell'applicazione.

## Passaggi successivi

Per altre informazioni su Microsoft JDBC Driver per SQL Server, vedere [Panoramica del driver JDBC](http://msdn.microsoft.com/library/ms378749.aspx). Per altre informazioni sul database SQL, vedere le relative [informazioni generali](http://msdn.microsoft.com/library/windowsazure/ee336241.aspx).

[Concetti]:#concepts
[Prerequisiti]:#prerequisites
[Creazione di un database SQL di Azure]:#create_db
[Determinazione della stringa di connessione del database SQL]:#determine_connection_string
[Per consentire l'accesso a un intervallo di indirizzi IP]:#specify_allowed_ips
[Per usare il database SQL di Azure in Java]:#use_sql_azure_in_java
[Comunicazione con il database SQL di Azure dal codice]:#communicate_from_code
[Per creare una tabella]:#to_create_table
[Per creare un indice in una tabella]:#to_create_index
[Per inserire righe]:#to_insert_rows
[Per recuperare righe]:#to_retrieve_rows
[Per recuperare righe inserendo una clausola WHERE]:#to_retrieve_rows_using_where
[Per recuperare un conteggio di righe]:#to_retrieve_row_count
[Per aggiornare righe]:#to_update_rows
[Per eliminare righe]:#to_delete_rows
[Per verificare se una tabella esiste]:#to_check_table_existence
[Per eliminare un indice]:#to_drop_index
[Per eliminare una tabella]:#to_drop_table
[Usare il database SQL in Java all'interno di una distribuzione di Azure]:#using_in_azure
[Passaggi successivi]:#nextsteps
[create_new]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
[create_new_sql_db]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
[create_database_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
[create_server_settings]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
[get_jdbc_connection_string]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
[allowed_ips_dialog]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png

<!--HONumber=47-->
