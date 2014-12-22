<properties urlDisplayName="Web w/ SQL + Git" pageTitle="Sito Web PHP con database SQL e Git - Esercitazione su Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP website with a SQL Database and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

#Creare un sito Web PHP con un database SQL e distribuirlo tramite Git

In questa esercitazione viene illustrato come creare un sito Web PHP di Azure con un database SQL di Azure e come distribuirlo tramite Git. In questa esercitazione si presuppone che l'utente abbia installato [PHP][install-php], [SQL Server Express][install-SQLExpress], i [driver Microsoft per SQL Server per PHP][install-drivers], a server Web e [Git][install-git] sul proprio computer. Dopo avere completato questa guida, si disporrà di un database SQL-PHP in esecuzione in Azure.

> [WACOM.NOTE]
> Per installare e configurare PHP, SQL Server Express, i driver Microsoft per SQL Server per PHP e Internet Information Services (IIS), è possibile usare l' <a href="http://www.microsoft.com/web/downloads/platform.aspx">Microsoft Web Platform Installer</a>.

You will learn:

* How to create an Azure Website and a SQL Database using the Azure Management Portal. Because PHP is enabled in Azure Websites by default, nothing special is required to run your PHP code.
* How to publish and re-publish your application to Azure using Git.
 
By following this tutorial, you will build a simple registration web application in PHP. The application will be hosted in an Azure Website. A screenshot of the completed application is below:

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


##Create an Azure Website and set up Git publishing

Follow these steps to create an Azure Website and a SQL Database:

1. Login to the [Azure Management Portal][management-portal].
2. Click the **New** icon on the bottom left of the portal.
![Create New Azure Web Site][new-website]

3. Click **Website**, then **Custom Create**.

	![Custom Create a new Web Site][custom-create]

	Enter a value for **URL**, select **Create a New SQL Database** from the **Database** dropdown,  and select the data center for your website in the **Region** dropdown. Click the arrow at the bottom of the dialog.

	![Fill in web site details][website-details-sqlazure]

4. Enter a value for the **Name** of your database, select the **Edition** [(WEB or BUSINESS)][sql-database-editions], select the **Max Size** for your database, choose the **Collation**, and select **NEW SQL Database server**. Click the arrow at the bottom of the dialog.

	![Fill in SQL Database settings][database-settings]

5. Enter an administrator name and password (and confirm the password), choose the region in which your new SQL Database server will be created, and check the `Allow Azure Services to access the server` box.

	![Create new SQL Database server][create-server]

	When the website has been created you will see the text **Creation of Website "[SITENAME]" completed successfully**. Now, you can enable Git publishing.

6. Click the name of the website displayed in the list of websites to open the website's Quick Start dashboard.

	![Open web site dashboard][go-to-dashboard]


7. At the bottom of the Quick Start page, click **Set up deployment from source control**. 

	![Set up Git publishing][setup-git-publishing]

6. When asked "Where is your source code?" select **Local Git repository**, and then click the arrow.

	![where is your source code][where-is-code]

8. To enable Git publishing, you must provide a user name and password. Make a note of the user name and password you create. (If you have set up a Git repository before, this step will be skipped.)

	![Create publishing credentials][credentials]

	It will take a few seconds to set up your repository.

9. When your repository is ready, you will see instructions for pushing your application files to the repository. Make note of these instructions - they will be needed later.

	![Git instructions][git-instructions]

##Get SQL Database connection information

To connect to the SQL Database instance that is running in Azure Websites, your will need the connection information. To get SQL Database connection information, follow these steps:

1. From the Azure Management Portal, click **Linked Resources**, then click the database name.

	![Linked Resources][linked-resources]

2. Click **View connection strings**.

	![Connection string][connection-string]
	
3. From the **PHP** section of the resulting dialog, make note of the values for `SERVER`, `DATABASE`, and `USERNAME`.

##Build and test your application locally

The Registration application is a simple PHP application that allows you to register for an event by providing your name and email address. Information about previous registrants is displayed in a table. Registration information is stored in a SQL Database instance. The application consists of two files (copy/paste code available below):

* **index.php**: Displays a form for registration and a table containing registrant information.
* **createtable.php**: Creates the SQL Database table for the application. This file will only be used once.

To run the application locally, follow the steps below. Note that these steps assume you have PHP, SQL Server Express, and a web server set up on your local machine, and that you have enabled the [PDO extension for SQL Server][pdo-sqlsrv].

1. Create a SQL Server database called `registration`. You can do this from the `sqlcmd` command prompt with these commands:

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. In your web server's root directory, create a folder called `registration` and create two files in it - one called `createtable.php` and one called `index.php`.

3. Open the `createtable.php` file in a text editor or IDE and add the code below. This code will be used to create the `registration_tbl` table in the `registration` database.

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

	Note that you will need to update the values for <code>$user</code> and <code>$pwd</code> with your local SQL Server user name and password.

4. Open a web browser and browse to **http://localhost/registration/createtable.php**. This will create the `registration_tbl` table in the database.

5. Open the **index.php** file in a text editor or IDE and add the basic HTML and CSS code for the page (the PHP code will be added in later steps).

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

    Anche in questo caso sarà necessario aggiornare i valori per <code>$user</code> e <code>$pwd</code> con il nome utente e la password di MySQL locali.

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
			echo "<tr><th>Nome</th>";
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

Dopo aver testato l'applicazione in locale, è possibile pubblicarla nel sito Web di Azure tramite Git. È tuttavia necessario aggiornare innanzitutto le informazioni di connessione al database nell'applicazione. Usando le informazioni di connessione al database ottenute in precedenza (nella sezione **Recuperare le informazioni sulla connessione al database SQL**) aggiornare le informazioni seguenti in **entrambi** i file "createdatabase.php" e "index.php" con i valori appropriati:

	// DB connection info
	$host = "tcp:<value of SERVER>";
	$user = "<value of USERNAME>@<server ID>";
	$pwd = "<your password>";
	$db = "<value of DATABASE>";

> [WACOM.NOTE]
> In <code>$host</code>prima del valore di SERVER è necessario aggiungere <code>tcp:</code>, mentre il valore di <code>$user</code> è dato dalla concatenazione del valore di USERNAME, di "@" e dell'ID server. L'ID server è costituito dai primi 10 caratteri del valore di SERVER.


A questo punto è possibile configurare la pubblicazione Git e pubblicare l'applicazione.

> [WACOM.NOTE]
> Questi passaggi sono uguali a quelli riportati alla fine della precedente sezione Creare un sito Web di Azure e configurare la pubblicazione Git.


1. Aprire GitBash (o un terminale, se Git si trova in "PATH"), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

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
2. Aprire GitBash (o un terminale, se Git si trova in "PATH"), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

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
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/website_details_sqlazure.jpg
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/database_settings.jpg
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-sql-database-deploy-use-git/git-instructions.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/it-it/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/where_is_code.png
