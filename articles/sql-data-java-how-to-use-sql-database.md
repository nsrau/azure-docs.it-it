<properties linkid="develop-java-sql-azure" urlDisplayName="SQL Database" pageTitle="How to use SQL Azure (Java) - Azure feature guide" metaKeywords="" description="Learn how to use the Azure SQL Database from Java code. " metaCanonical="" services="sql-database" documentationCenter="Java" title="How to Use Azure SQL Database in Java" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Come usare il database SQL di Azure in Java

Nella procedura seguente viene illustrato come usare il database SQL di Azure con Java. Per semplicità vengono illustrati solo esempi di riga di comando, tuttavia, per applicazioni Web ospitate in locale, all'interno di Azure o in altri ambienti, saranno applicabili passaggi molto simili a quelli illustrati. Questa guida illustra la creazione di un server e quella di un database dal [portale di gestione di Azure][portale di gestione di Azure].

## Informazioni sul database SQL di Azure

Il database SQL di Azure offre un sistema di gestione di database relazionali per Azure e si basa sulla tecnologia SQL Server. Con un'istanza di database SQL, è possibile eseguire facilmente il provisioning e la distribuzione nel cloud di soluzioni di database relazionali e usufruire di un data center distribuito che offre disponibilità, scalabilità e sicurezza di livello aziendale, con i vantaggi delle funzionalità predefinite di riparazione automatica e protezione dati.

## Sommario

-   [Concetti][Concetti]
-   [Prerequisiti][Prerequisiti]
-   [Creazione di un database SQL di Azure][Creazione di un database SQL di Azure]
-   [Determinazione della stringa di connessione del database SQL][Determinazione della stringa di connessione del database SQL]
-   [Per consentire l'accesso a un intervallo di indirizzi IP][Per consentire l'accesso a un intervallo di indirizzi IP]
-   [Per usare il database SQL di Azure in Java][Per usare il database SQL di Azure in Java]
-   [Comunicazione con il database SQL di Azure dal codice][Comunicazione con il database SQL di Azure dal codice]
-   [Per creare una tabella][Per creare una tabella]
-   [Per creare un indice in una tabella][Per creare un indice in una tabella]
-   [Per inserire righe][Per inserire righe]
-   [Per recuperare righe][Per recuperare righe]
-   [Per recuperare righe inserendo una clausola WHERE][Per recuperare righe inserendo una clausola WHERE]
-   [Per recuperare un conteggio di righe][Per recuperare un conteggio di righe]
-   [Per aggiornare righe][Per aggiornare righe]
-   [Per eliminare righe][Per eliminare righe]
-   [Per verificare se una tabella esiste][Per verificare se una tabella esiste]
-   [Per eliminare un indice][Per eliminare un indice]
-   [Per eliminare una tabella][Per eliminare una tabella]
-   [Utilizzo di database SQL in Java all'interno di una distribuzione di Azure][Utilizzo di database SQL in Java all'interno di una distribuzione di Azure]
-   [Passaggi successivi][Passaggi successivi]

## <span id="concepts"></span></a>Concetti

Poiché il database SQL di Azure è basato sulle tecnologie di SQL Server, l'accesso da Java è molto simile in entrambi i casi. È possibile sviluppare un'applicazione in locale, usando SQL Server, e quindi connettersi al database SQL cambiando solo la stringa di connessione. È possibile usare un driver JDBC per SQL Server nell'applicazione. Tuttavia, tra il database SQL e SQL Server esistono alcune differenze che potrebbero influire sull'applicazione. Per altre informazioni, vedere [Linee guida e limitazioni (database SQL)][Linee guida e limitazioni (database SQL)].

Per risorse aggiuntive sul database SQL, vedere la sezione [Passaggi successivi][Passaggi successivi].

## <span id="prerequisites"></span></a>Prerequisiti

Di seguito vengono riportati i prerequisiti per l'uso del database SQL con Java.

-   Java Developer Kit (JDK) v 1.6 o versione successiva.
-   Una sottoscrizione di Azure, che può essere acquistata all'indirizzo <http://www.microsoft.com/windowsazure/offers/>.
-   Se si usa Eclipse, è necessario IDE Eclipse per sviluppatori Java EE, Indigo o versione successiva. È possibile scaricare il pacchetto all'indirizzo <http://www.eclipse.org/downloads/>. Il plug-in Azure per Eclipse con Java (da Microsoft Open Technologies). Durante l'installazione del plug-in, assicurarsi che sia incluso Microsoft JDBC Driver 4.0 per SQL Server. Per altre informazioni, vedere [Installazione del plug-in Azure per Eclipse con Java (da Microsoft Open Technologies)][Installazione del plug-in Azure per Eclipse con Java (da Microsoft Open Technologies)].
-   Se non si usa Eclipse, è necessario Microsoft JDBC Driver 4.0 per SQL Server, che è possibile scaricare dall'indirizzo <http://www.microsoft.com/it-it/download/details.aspx?id=11774>.

## <span id="create_db"></span></a>Creazione di un database SQL di Azure

Prima di usare il database SQL di Azure nel codice Java, è necessario creare un server di database SQL di Azure.

1.  Accedere al [portale di gestione di Azure][1].
2.  Fare clic su **New**.

    ![Creazione del nuovo database SQL][Creazione del nuovo database SQL]

3.  Fare clic su **SQL database**, quindi su **Custom create**.

    ![Creazione del database SQL personalizzato][Creazione del database SQL personalizzato]

4.  Nella finestra di dialogo **Database settings** specificare il nome per il database. Per le finalità di questa guida, usare **gettingstarted** come nome per il database.
5.  Nella casella **Server** selezionare **New SQL Database Server**. Usare i valori predefiniti per gli altri campi.

    ![Impostazioni del database SQL][Impostazioni del database SQL]

6.  Fare clic sulla freccia Avanti.
7.  Nella finestra di dialogo **Server settings** specificare un nome di accesso a SQL Server. Per le finalità di questa guida, è stato utilizzato **MySQLAdmin**. Specificare e confermare una password. Specificare un'area e assicurarsi che l'opzione **Allow Azure Services to access the server** sia selezionata.

    ![Impostazioni di SQL Server][Impostazioni di SQL Server]

8.  Fare clic sul pulsante di completamento.

## <span id="determine_connection_string"></span></a>Determinazione della stringa di connessione del database SQL

1.  Accedere al [portale di gestione di Azure][1].
2.  Fare clic su **SQL Databases**.
3.  Fare clic sul database che si desidera usare.
4.  Fare clic su **Show connection strings**.
5.  Evidenziare il contenuto della stringa di connessione **JDBC**.

    ![Determinazione della stringa di connessione JDBC][Determinazione della stringa di connessione JDBC]

6.  Fare clic con il pulsante destro del mouse sul contenuto evidenziato della stringa di connessione **JDBC** e scegliere **Copia**.
7.  È ora possibile incollare questo valore nel file di codice per creare una stringa di connessione con il formato seguente. Sostituire *your\_server* (due occorrenze) con il testo copiato nel passaggio precedente, quindi sostituire *your\_password* con il valore della password creata al momento della creazione dell'account per il database SQL. Se sono stati utilizzati valori diversi da **gettingstarted** e **MySQLAdmin** sarà inoltre necessario modificare i valori assegnati rispettivamente a **database=** e **user=**.

    String connectionString =
     "jdbc:sqlserver://*your\_server*.database.windows.net:1433" + ";" +

     "database=gettingstarted" + ";" +
     "user=MySQLAdmin@*your_server*" + ";" +

     "password=*your\_password*" + ";" +

     "encrypt=true" + ";" +
     "hostNameInCertificate=\*.int.mscds.com" + ";" +

     "loginTimeout=30";

Questa stringa verrà utilizzata in seguito in questa guida; per il momento sono stati illustrati i passaggi per determinare la stringa di connessione. A seconda delle esigenze dell'applicazione, potrebbe inoltre essere necessario usare le impostazioni **encrypt** e **hostNameInCertificate**, nonché modificare l'impostazione **loginTimeout**.

## <span id="specify_allowed_ips"></span></a>Per consentire l'accesso a un intervallo di indirizzi IP

1.  Accedere al [portale di gestione][1].
2.  Fare clic su **SQL Databases**.
3.  Fare clic su **Servers**.
4.  Fare clic sul server che si desidera usare.
5.  Fare clic su **Manage**.
6.  Fare clic su **Configure**.
7.  In **Allowed IP addresses** immettere il nome di una nuova regola IP. Specificare gli indirizzi IP di inizio e fine intervallo. Per praticità, viene mostrato l'indirizzo IP del client attuale. Nell'esempio seguente viene consentito un singolo indirizzo IP (l'indirizzo IP in uso sarà diverso).

    ![Finestra di dialogo Allowed IP addresses][Finestra di dialogo Allowed IP addresses]

8.  Fare clic sul pulsante di completamento. Agli indirizzi IP specificati verrà ora consentito l'accesso al server di database.

## <span id="use_sql_azure_in_java"></span></a>Per usare il database SQL di Azure in Java

1.  Creare un progetto Java. Per le finalità di questa guida, denominarlo **HelloSQLAzure**.
2.  Aggiungere un file di classe Java denominato **HelloSQLAzure.java** al progetto.
3.  Aggiungere **Microsoft JDBC Driver per SQL Server** al percorso di compilazione.

    Se si usa Eclipse:

    1. Within Eclipse's Project Explorer, right-click the **HelloSQLAzure** project and click **Properties**.
    2. In the left-hand pane of the **Properties** dialog, click **Java Build Path**.
    3. Click the **Libraries** tab, and then click **Add Library**.
    4. In the **Add Library** dialog, select **Microsoft JDBC Driver 4.0 for SQL Server**, click **Next**, and then click **Finish**.
    5. Click **OK** to close the **Properties** dialog.

    If you are not using Eclipse, add the Microsoft JDBC Driver 4.0 for SQL Server JAR to your class path. For related information, see [Using the JDBC Driver](http://msdn.microsoft.com/it-it/library/ms378526.aspx).

1.  Nel codice **HelloSQLAzure.java** aggiungere istruzioni `import` come illustrato di seguito:

        import java.sql.*;
        import com.microsoft.sqlserver.jdbc.*;

2.  Specificare la stringa di connessione. Di seguito è fornito un esempio. Come nei passaggi precedenti, sostituire *your\_server* (due occorrenze), *your\_user* e *your\_password* con i valori appropriati per il server di database SQL.

        String connectionString =
            "jdbc:sqlserver://your_server.database.windows.net:1433" + ";" +  
                "database=master" + ";" + 
                "user=your_user@your_server" + ";" +  
                "password=your_password";

È ora possibile aggiungere codice per comunicare con il server di database SQL.

## <span id="communicate_from_code"></span></a>Comunicazione con il database SQL di Azure dal codice

Nel resto di questo argomento vengono riportati esempi per eseguire le operazioni seguenti:

1.  Connessione al server di database SQL.
2.  Definizione di un'istruzione SQL, ad esempio per creare o eliminare una tabella, inserire/selezionare/eliminare righe e così via.
3.  Esecuzione dell'istruzione SQL, tramite chiamata a **executeUpdate** o a **executeQuery**.
4.  Visualizzazione dei risultati della query, se appropriato.

Le sezioni seguenti sono progettate per essere lette (acquisite) nell'ordine dato. Il primo frammento è un esempio completo; gli altri si affidano a una parte del framework nell'esempio completo, ad esempio le istruzioni **import**, le dichiarazioni **class** e **main** la gestione degli errori e la chiusura di risorse.

## <span id="to_create_table"></span></a>Per creare una tabella

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

## <span id="to_create_index"></span></a>Per creare un indice in una tabella

Nel codice seguente viene illustrato come creare un indice denominato **index1** sulla tabella **Person**, usando la colonna **PersonID**.

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

## <span id="to_insert_rows"></span></a>Per inserire righe

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

## <span id="to_retrieve_rows"></span></a>Per recuperare righe

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

## <span id="to_retrieve_rows_using_where"></span></a>Per recuperare righe inserendo una clausola WHERE

Per recuperare righe inserendo una clausola, usare il codice sopra riportato, modificando l'istruzione SQL per includere una clausola. Nella seguente istruzione SQL è inclusa una clausola per le righe il cui valore **FirstName** è uguale a **Jim**.

    // Define the SQL string.
    String sqlString = "SELECT * FROM Person WHERE FirstName='Jim'";

Le clausole WHERE possono inoltre essere utilizzate per recuperare conteggi, aggiornare righe o eliminarle.

## <span id="to_retrieve_row_count"></span></a>Per recuperare un conteggio di righe

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

## <span id="to_update_rows"></span></a>Per aggiornare righe

Nel codice seguente viene illustrato come aggiornare righe. In questo esempio il valore **LastName** viene modificato in **Kim** per tutte le righe in cui il valore **FirstName** corrisponde a **Jim**.

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

## <span id="to_delete_rows"></span></a>Per eliminare righe

Nel codice seguente viene illustrato come eliminare righe. In questo esempio tutte le righe il cui valore **FirstName** corrisponde a **Jim** vengono eliminate.

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

## <span id="to_check_table_existence"></span></a>Per verificare se una tabella esiste

Nel codice seguente viene illustrato come determinare se una tabella esiste.

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

## <span id="to_drop_index"></span></a>Per eliminare un indice

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

## <span id="to_drop_table"></span></a>Per eliminare una tabella

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

## <span id="using_in_azure"></span></a>Utilizzo di database SQL in Java all'interno di una distribuzione di Azure

Per usare un database SQL in Java all'interno di una distribuzione di Azure, oltre a usare Microsoft JDBC Driver 4.0 per SQL Server come libreria nel percorso delle classi indicato sopra, sarà necessario includerlo nella creazione del pacchetto della distribuzione.

**Creazione del pacchetto con Microsoft JDBC Driver 4.0 per SQL Server quando si utilizza Eclipse**

1.  In Project Explorer di Eclipse fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Properties**.
2.  Nel riquadro sinistro della finestra di dialogo **Properties** fare clic su **Deployment Assembly**, quindi scegliere **Add**.
3.  Nella finestra di dialogo **New Assembly Directive** fare clic su **Java Build Path Entries**, quindi scegliere **Next**.
4.  Selezionare **Microsoft JDBC Driver 4.0 per SQL Server**, quindi fare clic su **Finish**.
5.  Fare clic su **OK** per chiudere la finestra di dialogo **Properties**.
6.  Esportare il file WAR del progetto nella cartella approot e ricompilare il progetto Azure secondo i passaggi forniti in [Creazione di un'applicazione Hello World usando il plug-in di Azure per Eclipse con Java (da Microsoft Open Technologies)][Creazione di un'applicazione Hello World usando il plug-in di Azure per Eclipse con Java (da Microsoft Open Technologies)]. Nell'argomento viene anche descritto come eseguire l'applicazione nell'emulatore di calcolo e in Azure.

**Creazione del pacchetto con Microsoft JDBC Driver 4.0 per SQL Server quando non si utilizza Eclipse**

-   Verificare che la libreria Microsoft JDBC Driver 4.0 per SQL Server si trovi nello stesso ruolo di Azure dell'applicazione Java e che sia stata aggiunta al percorso della classe dell'applicazione.

## <span id="nextsteps"></span></a>Passaggi successivi

Per altre informazioni su Microsoft JDBC Driver per SQL Server, vedere [Panoramica del driver JDBC][Panoramica del driver JDBC]. Per altre informazioni sul database SQL, vedere [Panoramica dei database SQL][Panoramica dei database SQL].

  [portale di gestione di Azure]: https://windows.azure.com
  [Concetti]: #concepts
  [Prerequisiti]: #prerequisites
  [Creazione di un database SQL di Azure]: #create_db
  [Determinazione della stringa di connessione del database SQL]: #determine_connection_string
  [Per consentire l'accesso a un intervallo di indirizzi IP]: #specify_allowed_ips
  [Per usare il database SQL di Azure in Java]: #use_sql_azure_in_java
  [Comunicazione con il database SQL di Azure dal codice]: #communicate_from_code
  [Per creare una tabella]: #to_create_table
  [Per creare un indice in una tabella]: #to_create_index
  [Per inserire righe]: #to_insert_rows
  [Per recuperare righe]: #to_retrieve_rows
  [Per recuperare righe inserendo una clausola WHERE]: #to_retrieve_rows_using_where
  [Per recuperare un conteggio di righe]: #to_retrieve_row_count
  [Per aggiornare righe]: #to_update_rows
  [Per eliminare righe]: #to_delete_rows
  [Per verificare se una tabella esiste]: #to_check_table_existence
  [Per eliminare un indice]: #to_drop_index
  [Per eliminare una tabella]: #to_drop_table
  [Utilizzo di database SQL in Java all'interno di una distribuzione di Azure]: #using_in_azure
  [Passaggi successivi]: #nextsteps
  [1]: https://manage.windowsazure.com
  [Creazione del nuovo database SQL]: ./media/sql-data-java-how-to-use-sql-database/WA_New.png
  [Creazione del database SQL personalizzato]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_DB_Create.png
  [Impostazioni del database SQL]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_1.png
  [Impostazioni di SQL Server]: ./media/sql-data-java-how-to-use-sql-database/WA_CustomCreate_2.png
  [Determinazione della stringa di connessione JDBC]: ./media/sql-data-java-how-to-use-sql-database/WA_SQL_JDBC_ConnectionString.png
  [Finestra di dialogo Allowed IP addresses]: ./media/sql-data-java-how-to-use-sql-database/WA_Allowed_IPs.png
  [Panoramica del driver JDBC]: http://msdn.microsoft.com/it-it/library/ms378749.aspx
  [Panoramica dei database SQL]: http://msdn.microsoft.com/it-it/library/windowsazure/ee336241.aspx
