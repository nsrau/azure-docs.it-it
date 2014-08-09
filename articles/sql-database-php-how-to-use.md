<properties linkid="develop-php-sql-database" urlDisplayName="SQL Database" pageTitle="How to use SQL Database (PHP) - Azure feature guides" metaKeywords="Azure SQL Database PHP, SQL Database PHP" description="Learn how to create and connect to an Azure SQL Database from PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" videoId="" scriptId="" />

Come accedere al database SQL di Azure da PHP
=============================================

In questa guida vengono illustrate le nozioni di base sull'utilizzo del database SQL di Azure da PHP. Gli esempi sono scritti in PHP. Gli scenari presentati includono la **creazione di un database SQL** e la **connessione a un database SQL**. In questa guida viene illustrata la creazione di un database SQL dal [portale di gestione](https://manage.windowsazure.com). Per informazioni su come eseguire queste attività dal portale di produzione, vedere la [guida introduttiva a PHP e al database SQL](http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx). Per ulteriori informazioni, vedere la sezione [Passaggi successivi](#NextSteps).

Informazioni sul database SQL di Azure
--------------------------------------

Il database SQL di Azure offre un sistema di gestione di database relazionali per Azure e si basa sulla tecnologia SQL Server. Con il database SQL, è possibile eseguire facilmente il provisioning e la distribuzione nel cloud di soluzioni di database relazionali e usufruire di un data center distribuito che offre disponibilità, scalabilità e sicurezza di livello aziendale, con i vantaggi delle funzionalità predefinite di riparazione automatica e protezione dati.

Sommario
--------

-   [Concetti](#Concepts)
-   [Procedura: Configurare l'ambiente](#Setup)
-   [Procedura: Creare un database SQL](#CreateServer)
-   [Procedura: Recuperare le informazioni sulla connessione al database SQL](#ConnectionInfo)
-   [Procedura: Connettersi a un'istanza di database SQL](#Connect)
-   [Passaggi successivi](#NextSteps)

Concetti
--------

Poiché il database SQL di Azure è basato sulle tecnologie di SQL Server, l'accesso da PHP è molto simile in entrambi i casi. È possibile sviluppare un'applicazione in locale, utilizzando SQL Server, e quindi connettersi al database SQL cambiando solo la stringa di connessione. Tuttavia, tra il database SQL e SQL Server esistono alcune differenze che potrebbero influire sull'applicazione. Per ulteriori informazioni, vedere [Linee guida e limitazioni (database SQL)](http://msdn.microsoft.com/it-it/library/windowsazure/ff394102.aspx).

L'approccio consigliato per l'accesso al database SQL da PHP consiste nell'utilizzare i [driver Microsoft per PHP per SQL Server](http://www.microsoft.com/download/en/details.aspx?id=20098). Negli esempi di questo articolo verranno utilizzati questi driver. I driver Microsoft per PHP per SQL Server sono compatibili solo con Windows.

Procedura: Configurare l'ambiente
---------------------------------

Per configurare l'ambiente, è consigliabile utilizzare l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/?LinkId=253447), tramite cui è possibile scegliere gli elementi della piattaforma di sviluppo Web che verranno quindi installati e configurati automaticamente. Scaricando l'Installazione guidata piattaforma Web e scegliendo di installare WebMatrix, PHP per WebMatrix e SQL Server Express, verrà configurato un ambiente di sviluppo completo.

In alternativa, è possibile configurare l'ambiente manualmente:

-   Installare PHP e configurare IIS: <http://php.net/manual/en/install.windows.iis7.php>.
-   Scaricare e installare SQL Server Express: <http://www.microsoft.com/en-us/download/details.aspx?id=29062>
-   Scaricare e installare i driver Microsoft per PHP per SQL Server: <http://php.net/manual/en/sqlsrv.requirements.php>.

Procedura: Creare un database SQL
---------------------------------

Per creare un database SQL di Azure, eseguire la procedura seguente:

1.  Accedere al [portale di gestione](https://manage.windowsazure.com).
2.  Fare clic su **New** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito Web di Azure](./media/sql-database-php-how-to-use-sql-database/plus-new.png)

3.  Fare clic su **DATA SERVICES**, su **SQL DATABASE**, quindi su **CUSTOM CREATE**.

    ![Creazione personalizzata di un nuovo database SQL](./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png)

4.  Immettere un valore per il campo **NAME** relativo al database, selezionare l'opzione per **EDITION** (WEB o BUSINESS), selezionare il valore per **MAX SIZE** relativo al database, scegliere il valore per **COLLATION** e selezionare **NEW SQL Database Server**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo. Si noti che, se in precedenza è stato creato un database SQL, è possibile scegliere un server esistente dall'elenco a discesa **Choose a server**.

    ![Compilazione delle impostazioni del database SQL](./media/sql-database-php-how-to-use-sql-database/new-sql-db.png)

5.  Immettere un nome amministratore e una password (e confermare la password), scegliere l'area in cui verrà creato il nuovo database SQL e selezionare la casella `Allow Azure Services to access the server`.

    ![Creazione del nuovo server di database SQL](./media/sql-database-php-how-to-use-sql-database/db-server-settings.png)

Per visualizzare le informazioni sul server e sul database, fare clic su **SQL Databases** nel portale di gestione. Sarà quindi possibile fare clic su **DATABASES** o su **SERVERS** per visualizzare le informazioni pertinenti.

![Visualizzazione delle informazioni sul server e sul database](./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png)

Procedura: Recuperare le informazioni sulla connessione al database SQL
-----------------------------------------------------------------------

Per ottenere informazioni sulla connessione al database SQL, fare clic su **SQL DATABASES** nel portale, quindi fare clic sul nome del database.

![Visualizzazione delle informazioni sul database](./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png)

Fare quindi clic su **View SQL Database connection strings for ADO.NET, ODBC, PHP, and JDBC**.

![Visualizzazione delle stringhe di connessione](./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png)

Nella sezione PHP della finestra di dialogo risultante prendere nota dei valori per **SERVER**, **DATABASE** e **USERNAME**. La password sarà quella utilizzata durante la creazione del database SQL.

Procedura: Connettersi a un'istanza di database SQL
---------------------------------------------------

Negli esempi seguenti viene illustrato come utilizzare le estensioni **SQLSRV** e **PDO\_SQLSRV** per connettersi a un database SQL denominato `testdb`. Saranno necessarie le informazioni ottenute dalla sezione precedente. Sostituire `SERVER_ID` con l'ID server a 10 cifre, che corrispondono ai primi 10 caratteri del valore di SERVER ottenuto nella sezione precedente, e assegnare i valori corretti, ossia nome utente e password, alle variabili `$user` e `$pwd`.

##### SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "password";
    $db = "testdb";

    $conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

    if($conn === false){
        die(print_r(sqlsrv_errors()));
        }

##### PDO\_SQLSRV

    $server = "tcp:<value of SERVER from section above>";
    $user = "<value of USERNAME from section above>"@SERVER_ID;
    $pwd = "password";
    $db = "testdb";

    try{
        $conn = new PDO( "sqlsrv:Server= $server ; Database = $db ", $user, $pwd);
        $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
        die(print_r($e));
        }

Passaggi successivi
-------------------

Come accennato in precedenza, l'utilizzo del database SQL è simile a quello di SQL Server. Dopo aver stabilito una connessione a un database SQL, come illustrato sopra, è possibile utilizzare le API **SQLSRV** o **PDO\_SQLSRV** per inserire, recuperare, aggiornare ed eliminare dati. Per informazioni sulle API **SQLSRV** e **PDO\_SQLSRV**, vedere la [documentazione sui driver Microsoft per PHP per SQL Server](http://msdn.microsoft.com/it-it/library/dd638075(SQL.10).aspx). Tuttavia, tra il database SQL e SQL Server esistono alcune differenze che potrebbero influire sull'applicazione. Per ulteriori informazioni, vedere [Linee guida e limitazioni (database SQL)](http://msdn.microsoft.com/it-it/library/windowsazure/ff394102.aspx).

Un esempio in cui viene illustrato l'utilizzo del database SQL con PHP in Azure è disponibile all'indirizzo &lt;https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure\>.

