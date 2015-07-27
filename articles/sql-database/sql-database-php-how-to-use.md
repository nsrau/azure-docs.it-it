<properties 
	pageTitle="Come usare il database SQL (PHP) - Guide alle funzionalità di Azure" 
	description="Informazioni su come creare e connettersi a un database di SQL Azure tramite PHP." 
	services="sql-database" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="tomfitz"/>

#Come accedere al database SQL di Azure da PHP 

## Panoramica

Questa guida illustra le nozioni di base sull'uso del database SQL di Azure da PHP. Gli esempi sono scritti in PHP. Gli scenari presentati includono la **creazione di un database SQL** e la **connessione a un database SQL**. In questa guida viene illustrata la creazione di un database SQL dal [portale di gestione][management-portal]. Per informazioni su come eseguire queste attività dal portale di produzione, vedere la [guida introduttiva a PHP e al database SQL][prod-portal-instructions]. Per ulteriori informazioni, vedere la sezione [Passaggi successivi](#NextSteps).

##Informazioni sul database SQL

Il database SQL offre un sistema di gestione di database relazionali per Azure e si basa sulla tecnologia SQL Server. Con il database SQL, è possibile eseguire facilmente il provisioning e la distribuzione nel cloud di soluzioni di database relazionali e usufruire di un data center distribuito che offre disponibilità, scalabilità e sicurezza di livello aziendale, con i vantaggi delle funzionalità predefinite di riparazione automatica e protezione dati.

##<a id="Concepts"></a>Concetti
Poiché il database SQL è basato sulle tecnologie SQL Server, l'accesso da PHP è molto simile in entrambi i casi. È possibile sviluppare un'applicazione in locale, usando SQL Server, e quindi connettersi al database SQL cambiando solo la stringa di connessione. Tuttavia, tra il database SQL e SQL Server esistono alcune differenze che potrebbero influire sull'applicazione. Per ulteriori informazioni, vedere [Linee guida e limitazioni (database SQL)][limitations].

L'approccio consigliato per l'accesso al database SQL da PHP consiste nell'utilizzare i [driver Microsoft per PHP per SQL Server][download-drivers]. Negli esempi di questo articolo verranno utilizzati questi driver. I driver Microsoft per PHP per SQL Server sono compatibili solo con Windows.

##<a id="Setup"></a>Procedura: configurare l'ambiente

Per configurare l'ambiente, è consigliabile utilizzare l'[Installazione guidata piattaforma Web Microsoft][wpi-installer], tramite cui è possibile scegliere gli elementi della piattaforma di sviluppo Web che verranno quindi installati e configurati automaticamente. Scaricando l'Installazione guidata piattaforma Web e scegliendo di installare WebMatrix, PHP per WebMatrix e SQL Server Express, verrà configurato un ambiente di sviluppo completo.

In alternativa, è possibile configurare l'ambiente manualmente:

* Installare PHP e configurare IIS: [http://php.net/manual/it/install.windows.iis7.php][manual-config].
* Scaricare e installare SQL Server Express: [http://www.microsoft.com/download/details.aspx?id=29062][install-sql-express]
* Scaricare e installare i [driver Microsoft per PHP per SQL Server][download-drivers].

##<a id="CreateServer"></a>procedura: creare un database SQL

Per creare un database SQL di Azure, seguire questa procedura:

1. Accedere al [portale di gestione][management-portal].
2. Fare clic su **New** nella parte inferiore sinistra del portale.

	![Creazione di un nuovo sito Web di Azure][new-website]

3. Fare clic su **DATA SERVICES**, **DATABASE SQL**, quindi **CREAZIONE RAPIDA**. Specificare un nome per il database, indicando se usare un server di database esistente o uno nuovo, quindi specificare un'area e nome e password di un amministratore.

	![Creazione personalizzata di un nuovo database SQL][quick-create]


Per visualizzare le informazioni sul server e sul database, fare clic su **SQL Databases** nel portale di gestione. Sarà quindi possibile fare clic su **DATABASES** o su **SERVERS** per visualizzare le informazioni pertinenti.

![Visualizzazione delle informazioni sul server e sul database][sql-dbs-servers]

##<a id="ConnectionInfo"></a>Procedura: recuperare le informazioni sulla connessione al database SQL

Per ottenere informazioni sulla connessione al database SQL, fare clic su **SQL DATABASES** nel portale, quindi fare clic sul nome del database.

![Visualizzazione delle informazioni sul database][go-to-db-info]

Fare quindi clic su **View SQL Database connection strings for ADO.NET, ODBC, PHP, and JDBC**.

![Visualizzazione delle stringhe di connessione][show-connection-string]

Nella sezione PHP della finestra di dialogo risultante prendere nota dei valori per **SERVER**, **DATABASE** e **USERNAME**. La password sarà quella usata durante la creazione del database SQL.

##<a id="Connect"></a>Procedura: connettersi a un'istanza di database SQL

I seguenti esempi illustrano come usare le estensioni **SQLSRV** e **PDO_SQLSRV** per connettersi a un database SQL denominato `testdb`. Per informazioni sulle API **SQLSRV** e **PDO_SQLSRV**, vedere la [documentazione sui driver Microsoft per PHP per SQL Server][driver-docs]. Saranno necessarie le informazioni ottenute dalla sezione precedente. Sostituire `SERVER_ID` con l'ID server a 10 cifre, che corrispondono ai primi 10 caratteri del valore di SERVER ottenuto nella sezione precedente, e assegnare i valori corretti, ossia nome utente e password, alle variabili `$user` e `$pwd`.

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
Come accennato in precedenza, l'uso del database SQL è simile a quello di SQL Server. Dopo aver stabilito una connessione a un database SQL, come illustrato sopra, è possibile utilizzare le API **SQLSRV** o **PDO_SQLSRV** per inserire, recuperare, aggiornare ed eliminare dati. Tuttavia, tra il database SQL e SQL Server esistono alcune differenze che potrebbero influire sull'applicazione. Per ulteriori informazioni, vedere [Linee guida e limitazioni (database SQL)][limitations].

Un esempio in cui viene illustrato l'uso del database SQL con PHP su Azure è disponibile all'indirizzo <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-sqlazure>.

[download-drivers]: http://www.microsoft.com/download/en/details.aspx?id=20098
[limitations]: http://msdn.microsoft.com/library/windowsazure/ff394102.aspx
[odbc-php]: http://www.php.net/odbc
[manual-config]: http://php.net/manual/en/install.windows.iis7.php
[install-drivers]: http://php.net/manual/en/sqlsrv.requirements.php
[driver-docs]: http://msdn.microsoft.com/library/dd638075(SQL.10).aspx
[access-php-odbc]: http://social.technet.microsoft.com/wiki/contents/articles/accessing-sql-azure-from-php.aspx
[install-sql-express]: http://www.microsoft.com/download/details.aspx?id=29062
[management-portal]: https://manage.windowsazure.com
[prod-portal-instructions]: http://blogs.msdn.com/b/brian_swan/archive/2010/02/12/getting-started-with-php-and-sql-azure.aspx
[new-website]: ./media/sql-database-php-how-to-use/plus-new.png
[custom-create]: ./media/sql-database-php-how-to-use-sql-database/create_custom_sql_db-2.png
[database-settings]: ./media/sql-database-php-how-to-use-sql-database/new-sql-db.png
[create-server]: ./media/sql-database-php-how-to-use-sql-database/db-server-settings.png
[sql-dbs-servers]: ./media/sql-database-php-how-to-use/sql-dbs-portal.png
[wpi-installer]: http://go.microsoft.com/fwlink/?LinkId=253447
[go-to-db-info]: ./media/sql-database-php-how-to-use/go-to-db-info.png
[show-connection-string]: ./media/sql-database-php-how-to-use/show-connection-string-2.png
[quick-create]: ./media/sql-database-php-how-to-use/create-new-sql.png
 

<!---HONumber=July15_HO3-->