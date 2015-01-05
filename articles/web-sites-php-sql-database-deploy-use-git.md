<properties urlDisplayName="Web w/ SQL + Git" pageTitle="Sito Web PHP con database SQL e Git - Esercitazione su Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP website with a SQL Database and deploy using Git" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/18/2014" ms.author="tomfitz" />

#Creare un sito Web PHP con un database SQL e distribuirlo tramite Git

Questa esercitazione illustra come creare un sito Web PHP di Azure con un database SQL di Azure e come distribuirlo tramite Git. Questa esercitazione presuppone che nel computer siano installati [PHP][install-php], [SQL Server Express][install-SQLExpress], i [driver Microsoft per SQL Server per PHP][install-drivers], un server Web e [Git][install-git]. Dopo avere completato questa guida, si disporrà di un database SQL-PHP in esecuzione in Azure.

> [WACOM.NOTE]
> Per installare e configurare PHP, SQL Server Express, i driver Microsoft per SQL Server per PHP e Internet Information Services (IIS), è possibile usare l'<a href="http://www.microsoft.com/web/downloads/platform.aspx">Installazione guidata piattaforma Web Microsoft</a>.

Si apprenderà come:

* Creare un sito Web di Azure e un database SQL usando il portale di gestione di Azure. Poiché PHP è abilitato in Siti Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
* Pubblicare e ripubblicare l'applicazione in Azure tramite Git.
 
Seguendo questa esercitazione, verrà creata una semplice applicazione Web di registrazione in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione completata:

![Azure PHP Web Site][running-app]

> [WACOM.NOTE]
> Per completare l'esercitazione, è necessario un account Azure. È possibile <a href="http://azure.microsoft.com/it-it/pricing/member-offers/msdn-benefits-details/">attivare i benefici della sottoscrizione MSDN</a> o <a href="http://azure.microsoft.com/it-it/pricing/free-trial/">iscriversi per una versione di valutazione gratuita</a>.
> 
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a> in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Creare un sito Web di Azure e configurare la pubblicazione Git

Per creare un sito Web di Azure e un database SQL, seguire questa procedura:

1. Accedere al [portale di gestione di Azure][management-portal].
2. Fare clic sull'icona **Nuovo** nella parte inferiore sinistra del portale.
![Create New Azure Web Site][new-website]

3. Fare clic su **Sito Web** e quindi su **Creazione personalizzata**.

	![Custom Create a new Web Site][custom-create]

	Immettere un valore per **URL**, selezionare **Crea un nuovo database SQL** nell'elenco a discesa **Database** e quindi selezionare **Pubblica da controllo codice sorgente**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

	![Fill in web site details][website-details-sqlazure]

4. In **Nome** immettere un valore per il database, selezionare **Nuovo server di database SQL**, specificare le credenziali di accesso e selezionare un'area. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

	![Fill in SQL Database settings][database-settings]

5. Selezionare **Archivio Git locale** per il codice sorgente.

	![where is your source code][where-is-code]

	Se in precedenza non è stato configurato un repository Git, è necessario fornire nome utente e password.

6. Dopo la creazione del sito Web, aprire il relativo dashboard e selezionare **Visualizza distribuzioni**.

	![Web site dashboard][go-to-dashboard]

9. Verranno visualizzate le istruzioni per effettuare il push dei file dell'applicazione nel repository. Prendere nota di queste istruzioni, che saranno necessarie in seguito.

	![Git instructions][git-instructions]

##Ottenere le informazioni di connessione al database SQL

Per connettersi all'istanza di database SQL in esecuzione in Siti Web di Azure, sono necessarie le informazioni di connessione. Per ottenere le informazioni di connessione al database SQL, attenersi alla procedura seguente:

1. Nel portale di gestione di Azure fare clic su **Risorse collegate** e quindi sul nome del database.

	![Linked Resources][linked-resources]

2. Fare clic su **Visualizza stringhe di connessione**.

	![Connection string][connection-string]
	
3. Nella sezione **PHP** della finestra di dialogo risultante prendere nota dei valori per `SERVER`, `DATABASE` e `USERNAME`.

##Creazione e test dell'applicazione in locale

L'applicazione di registrazione è una semplice applicazione PHP che consente di registrarsi per un evento specificando il proprio nome e l'indirizzo di posta elettronica. Le informazioni sui registranti precedenti vengono visualizzate in una tabella. Le informazioni di registrazione vengono archiviate in un'istanza di database SQL. L'applicazione è costituita da due file (copiare e incollare il codice disponibile di seguito):

* **index.php**: consente di visualizzare un modulo per la registrazione e una tabella contenente informazioni sui registranti.
* **createtable.php**: consente di creare la tabella di database SQL per l'applicazione. Questo file verrà usato una volta sola.

Per eseguire l'applicazione in locale, attenersi alla procedura seguente. Si noti che per questi passaggi si presuppone che nel computer locale siano già stati configurati PHP, SQL Server Express e un server Web e che sia stata abilitata l'[estensione PDO per SQL Server][pdo-sqlsrv].

1. Creare un database di SQL Server denominato `registration`. A tale scopo, al prompt dei comandi `sqlcmd` immettere i comandi seguenti:

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. Nella directory radice del server Web creare una cartella denominata `registration` e al suo interno creare due file: uno denominato `createtable.php` e l'altro denominato `index.php`.

3. Aprire il file `createtable.php` in un editor di testo o IDE e aggiungere il codice seguente. Questo codice verrà usato per creare la tabella `registration_tbl` nel database `registration`.

		<?php
		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
			id INT NOT NULL IDENTITY(1,1) 
			PRIMARY KEY(id),
			name VARCHAR(30),
			email VARCHAR(30),
			date DATE)";
			$conn->query($sql);
		}
		catch(Exception $e){
			die(print_r($e));
		}
		echo "<h3>Table created.</h3>";
		?>

	Sarà necessario aggiornare i valori per <code>$user</code> e <code>$pwd</code> con il nome utente e la password di SQL Server locali.

4. Aprire un Web browser e passare a **http://localhost/registration/createtable.php**. In questo modo, verrà creata la tabella `registration_tbl` nel database.

5. Aprire il file **index.php** in un editor di testo o IDE e aggiungere il codice HTML e CSS di base per la pagina (il codice PHP verrà aggiunto nei passaggi successivi).

		<html>
		<head>
		<Title>Registration Form</Title>
		<style type="text/css">
			body { background-color: #fff; border-top: solid 10px #000;
			    color: #333; font-size: .85em; margin: 20; padding: 20;
			    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			}
			h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			h1 { font-size: 2em; }
			h2 { font-size: 1.75em; }
			h3 { font-size: 1.2em; }
			table { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Register here!</h1>
		<p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Name  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php

		?>
		</body>
		</html>

6. All'interno dei tag PHP, aggiungere il codice PHP per la connessione al database.

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

    Sarà di nuovo necessario aggiornare i valori di <code>$user</code> e <code>$pwd</code> con il nome utente e la password MySQL locali.

7. Dopo il codice di connessione al database, aggiungere il codice per l'inserimento delle informazioni di registrazione nel database.

		if(!empty($_POST)) {
		try {
			$name = $_POST['name'];
			$email = $_POST['email'];
			$date = date("Y-m-d");
			// Insert data
			$sql_insert = "INSERT INTO registration_tbl (name, email, date) 
						   VALUES (?,?,?)";
			$stmt = $conn->prepare($sql_insert);
			$stmt->bindValue(1, $name);
			$stmt->bindValue(2, $email);
			$stmt->bindValue(3, $date);
			$stmt->execute();
		}
		catch(Exception $e) {
			die(var_dump($e));
		}
		echo "<h3>Your're registered!</h3>";
		}

8. Infine, dopo il codice sopra riportato, aggiungere il codice per recuperare i dati dal database.

		$sql_select = "SELECT * FROM registration_tbl";
		$stmt = $conn->query($sql_select);
		$registrants = $stmt->fetchAll(); 
		if(count($registrants) > 0) {
			echo "<h2>People who are registered:</h2>";
			echo "<table>";
			echo "<tr><th>Name</th>";
			echo "<th>Email</th>";
			echo "<th>Date</th></tr>";
			foreach($registrants as $registrant) {
				echo "<tr><td>".$registrant['name']."</td>";
				echo "<td>".$registrant['email']."</td>";
				echo "<td>".$registrant['date']."</td></tr>";
		    }
		 	echo "</table>";
		} else {
			echo "<h3>No one is currently registered.</h3>";
		}

A questo punto è possibile passare a **http://localhost/registration/index.php** per testare l'applicazione.

##Pubblicare l'applicazione

Dopo aver testato l'applicazione in locale, è possibile pubblicarla nel sito Web di Azure tramite Git. È tuttavia necessario aggiornare innanzitutto le informazioni di connessione al database nell'applicazione. Usando le informazioni di connessione al database ottenute in precedenza (nella sezione **Ottenere le informazioni di connessione al database SQL**) aggiornare le informazioni seguenti in **entrambi** i file `createdatabase.php` e `index.php` con i valori appropriati:

	// DB connection info
	$host = "tcp:<value of SERVER>";
	$user = "<value of USERNAME>@<server ID>";
	$pwd = "<your password>";
	$db = "<value of DATABASE>";

> [WACOM.NOTE]
> In <code>$host</code>, il valore di SERVER deve essere preceduto da <code>tcp:</code> e il valore di <code>$user</code> è dato dalla concatenazione del valore di USERNAME, '@' e ID server. L'ID server è costituito dai primi 10 caratteri del valore di SERVER.


A questo punto è possibile configurare la pubblicazione Git e pubblicare l'applicazione.

> [WACOM.NOTE]
> Questi passaggi sono uguali a quelli riportati alla fine della precedente sezione Creare un sito Web di Azure e configurare la pubblicazione Git.


1. Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Verrà richiesto di specificare la password creata in precedenza.

2. Passare a **http://[nome sito].azurewebsites.net/createtable.php** per creare la tabella MySQL per l'applicazione.
3. Passare a **http://[nome sito].azurewebsites.net/index.php** per iniziare a usare l'applicazione.

Dopo aver pubblicato l'applicazione, è possibile iniziare ad apportarvi modifiche e ad usare Git per pubblicarle. 

##Pubblicazione delle modifiche apportate all'applicazione

Per pubblicare le modifiche apportate all'applicazione, eseguire la procedura seguente:

1. Apportare le modifiche all'applicazione in locale.
2. Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Verrà richiesto di specificare la password creata in precedenza.

3. Passare a **http://[nome sito].azurewebsites.net/index.php** per visualizzare le modifiche.

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/it-it/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/it-it/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
[running-app]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
[new-website]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/createphpgitsite.png
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/setupdb.png
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/viewdeploy.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-sql-database-deploy-use-git/gitsettings.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/it-it/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/setupgit.png

<!--HONumber=35.1-->
