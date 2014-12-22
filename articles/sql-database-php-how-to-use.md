<properties urlDisplayName="SQL Database" pageTitle="Come usare Database SQL (PHP) - Guide alle funzionalità di Azure" metaKeywords="Azure SQL Database PHP, SQL Database PHP" description="Learn how to create and connect to an Azure SQL Database from PHP." metaCanonical="" services="sql-database" documentationCenter="PHP" title="How to Access Azure SQL Database from PHP" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/29/2014" ms.author="tomfitz" />

#Come accedere al database SQL di Azure da PHP 

In questa guida vengono illustrate le nozioni di base sull'utilizzo del database SQL di Azure da PHP. Gli esempi sono scritti in PHP. Gli scenari presentati includono la **creazione di un database SQL** e la **connessione a un database SQL**. In questa guida viene illustrata la creazione di un database SQL dal [portale di gestione][management-portal]. Per informazioni su come eseguire queste attività dal portale di produzione, vedere la [guida introduttiva a PHP e al database SQL][prod-portal-instructions]. Per altre informazioni, vedere la sezione [Passaggi successivi](#NextSteps) .

##Informazioni sul database SQL di Azure

Il database SQL di Azure offre un sistema di gestione di database relazionali per Azure e si basa sulla tecnologia SQL Server. Con il database SQL, è possibile eseguire facilmente il provisioning e la distribuzione nel cloud di soluzioni di database relazionali e usufruire di un data center distribuito che offre disponibilità, scalabilità e sicurezza di livello aziendale, con i vantaggi delle funzionalità predefinite di riparazione automatica e protezione dati.

##Sommario

* [Concetti](#Concepts)
* [Procedura: Configurare l'ambiente](#Setup)
* [Procedura: Creare un database SQL](#CreateServer)
* [Procedura: Recuperare le informazioni sulla connessione al database SQL](#ConnectionInfo)
* [Procedura: Connettersi a un'istanza di database SQL](#Connect)
* [Passaggi successivi](#NextSteps)

##<a id="Concepts"></a>Concetti
Poiché il database SQL di Azure è basato sulle tecnologie di SQL Server, l'accesso da PHP è molto simile in entrambi i casi. È possibile sviluppare un'applicazione in locale, usando SQL Server, e quindi connettersi al database SQL cambiando solo la stringa di connessione. Tuttavia, tra il database SQL e SQL Server esistono alcune differenze che potrebbero influire sull'applicazione. Per altre informazioni, vedere [Linee guida e limitazioni (database SQL)][limitations].

L'approccio consigliato per l'accesso al database SQL da PHP consiste nell'usare i [driver Microsoft per PHP per SQL Server][download-drivers]. Negli esempi di questo articolo verranno usati questi driver. I driver Microsoft per PHP per SQL Server sono compatibili solo con Windows.

##<a id="Setup"></a>Procedura: Configurare l'ambiente

Per configurare l'ambiente, è consigliabile usare l'[Installazione guidata piattaforma Web Microsoft][wpi-installer], tramite cui è possibile scegliere gli elementi della piattaforma di sviluppo Web che verranno quindi installati e configurati automaticamente. Scaricando l'Installazione guidata piattaforma Web e scegliendo di installare WebMatrix, PHP per WebMatrix e SQL Server Express, verrà configurato un ambiente di sviluppo completo.

In alternativa, è possibile configurare l'ambiente manualmente:

* Installare PHP e configurare IIS: [http://php.net/manual/en/install.windows.iis7.php][manual-config].
* Scaricare e installare SQL Server Express: [http://www.microsoft.com/it-it/download/details.aspx?id=29062][install-sql-express]
* Scaricare e installare i driver Microsoft per PHP per SQL Server: [http://php.net/manual/en/sqlsrv.requirements.php][install-drivers].

##<a id="CreateServer"></a>Procedura: Creare un database SQL

Per creare un database SQL di Azure, eseguire la procedura seguente:

1. Accedere al [portale di gestione][management-portal].
2. Fare clic su **Nuovo** nella parte inferiore sinistra del portale.

	![Create New Azure Web Site][new-website]

3. Fare clic su **SERVIZI DATI**, su **DATABASE SQL**, quindi su **CREAZIONE PERSONALIZZATA**. Specificare un nome per il database, indicando se usare un server di database esistente o uno nuovo, quindi specificare un'area e nome e password di un amministratore.

	![Custom Create a new SQL Database][quick-create]


Per visualizzare le informazioni sul server e sul database, fare clic su **Database SQL** nel portale di gestione. Sarà quindi possibile fare clic su **DATABASE** o su **SERVER** per visualizzare le informazioni pertinenti.

![View server and database information][sql-dbs-servers]

##<a id="ConnectionInfo"></a>Procedura: Recuperare le informazioni sulla connessione al database SQL

Per ottenere informazioni sulla connessione al database SQL, fare clic su **DATABASE SQL** nel portale, quindi fare clic sul nome del database.

![View database information][go-to-db-info]

Fare quindi clic su **Visualizzare le stringhe di connessione del database SQL per ADO .Net, ODBC, PHP e JDBC**.

![Show connection strings][show-connection-string]

Nella sezione PHP della finestra di dialogo risultante prendere nota dei valori per **SERVER**, **DATABASE** e **NOME UTENTE**. La password sarà quella usata durante la creazione del database SQL.

##<a id="Connect"></a>Procedura: Connettersi a un'istanza di database SQL

Negli esempi seguenti viene illustrato come usare le estensioni **SQLSRV** e **PDO_SQLSRV** per connettersi a un database SQL denominato `testdb`. Saranno necessarie le informazioni ottenute dalla sezione precedente. Sostituire `SERVER_ID` con l'ID server a 10 cifre, che corrispondono ai primi 10 caratteri del valore di SERVER ottenuto nella sezione precedente, e assegnare i valori corretti, ossia nome utente e password, alle variabili `$user` e `$pwd`.

#####SQLSRV

	$server = "tcp:<value of SERVER from section above>";
	$user = "<value of USERNAME from section above>"@SERVER_ID;
	$pwd = "password";
	$db = "testdb";

	$conn = sqlsrv_connect($server, array("UID"=>$user, "PWD"=>$pwd, "Database"=>$db));

	if($conn === false){
		die(print_r(sqlsrv_errors()));
	}

#####PDO_SQLSRV

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


##<a id="NextSteps"></a>Passaggi successivi
Come accennato in precedenza, l'uso del database SQL è simile a quello di SQL Server. Dopo aver stabilito una connessione a un database SQL, come illustrato sopra, è possibile usare le API **SQLSRV** o **PDO\_SQLSRV** per inserire, recuperare, aggiornare ed eliminare dati. Per informazioni sulle API **SQLSRV** e **PDO\_SQLSRV**, vedere la [documentazione sui driver Microsoft per PHP per SQL Server][driver-docs]. Tuttavia, tra il database SQL e SQL Server esistono alcune differenze che potrebbero influire sull'applicazione. Per altre informazioni, vedere [Linee guida e limitazioni (database SQL)][limitations].

Un esempio in cui viene illustrato l'uso del database SQL con PHP su Azure è disponibile all'indirizzo <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/it-it/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/it-it/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/it-it/download/details.aspx?id=29062
[management-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use-sql-database/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use-sql-database/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use-sql-database/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use-sql-database/show-connection-string-2.png
[quick-create]: ./media/sql-database-php-how-to-use-sql-database/create-new-sql.png
