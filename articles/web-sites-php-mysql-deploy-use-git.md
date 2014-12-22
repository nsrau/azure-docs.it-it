<properties urlDisplayName="Web w/ MySQL + Git" pageTitle="Sito Web PHP con MySQL e Git - Esercitazione su Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

#Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git

Questa esercitazione illustra come creare un sito Web di Azure PHP-MySQL e distribuirlo tramite Git. Verranno usati [PHP][install-php], lo strumento da riga di comando MySQL (parte di [MySQL][install-mysql]), un server Web e [Git][install-git] installato sul computer. Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo, tra cui Windows, Mac e Linux. Dopo aver completato questa guida, si disporrà di un sito Web PHP-MySQL in esecuzione in Azure.
 
Si apprenderà come:

* Creare un sito Web di Azure e un database MySQL mediante il portale di gestione di Azure. Poiché PHP è abilitato in Siti Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
* Pubblicare e ripubblicare l'applicazione in Azure tramite Git.
 
Seguendo questa esercitazione, verrà creata una semplice applicazione Web di registrazione in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione completata:

![Azure PHP web site][running-app]

<div class="dev-callout"><strong>Nota</strong> <p>Per completare questa esercitazione, è necessario disporre di un account Azure in cui sia abilitata la funzionalità Siti Web di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Azure Free Trial</a>.</p> </div>

##Set up the development environment

This tutorial assumes you have [PHP][install-php], the MySQL Command-Line Tool (part of [MySQL][install-mysql]), a web server, and [Git][install-git] installed on your computer.

> [WACOM.NOTE]
> If you are performing this tutorial on Windows, you can set up your machine for PHP and automatically configure IIS (the built-in web server in Windows) by installing the <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">Azure SDK for PHP</a>.

##<a id="create-web-site-and-set-up-git"></a>Create an Azure website and set up Git publishing

Follow these steps to create an Azure website and a MySQL database:

1. Login to the [Azure Management Portal][management-portal].
2. Click the **New** icon on the bottom left of the portal.

	![Create New Azure web site][new-website]

3. Click **WebSite**, then **Custom Create**.

	![Custom Create a new web site][custom-create]
	
	Enter a value for **URL**, select **Create a New MySQL Database** from the **Database** dropdown,  and select the data center for your website in the **Region** dropdown. Click the arrow at the bottom of the dialog.

	![Fill in web site details][website-details]

4. Enter a value for the **Name** of your database, select the data center for your database in the **Region** dropdown, and check the box that indicates you agree with the legal terms. Click the checkmark at the bottom of the dialog.

	![Create new MySQL database][new-mysql-db]

	When the website has been created you will see the text **Creation of Website "[SITENAME]" completed successfully**. Now, you can enable Git publishing.

6. Click the name of the website displayed in the list of websites to open the website's **QuickStart** dashboard.

	![Open web site dashboard][go-to-dashboard]


7. At the bottom of the **QuickStart** page, click **Set up Git publishing**. 

	![Set up Git publishing][setup-git-publishing]

8. To enable Git publishing, you must provide a user name and password. Make a note of the user name and password you create. (If you have set up a Git repository before, this step will be skipped.)

	![Create publishing credentials][credentials]

	It will take a few seconds to set up your repository.

9. When your repository is ready, you will see instructions for pushing your application files to the repository. Make note of these instructions - they will be needed later.

	![Git instructions][git-instructions]

##Get remote MySQL connection information

To connect to the MySQL database that is running in Azure Websites, your will need the connection information. To get MySQL connection information, follow these steps:

1. From your website's dashboard, click the **View connection strings** link on the right side of the page:

	![Get database connection information][connection-string-info]
	
2. Make note of the values for `Database`, `Data Source`, `User Id`, and `Password`.

##Build and test your application locally

Now that you have created an Azure Website, you can develop your application locally, then deploy it after testing. 

The Registration application is a simple PHP application that allows you to register for an event by providing your name and email address. Information about previous registrants is displayed in a table. Registration information is stored in a MySQL database. The application consists of one file (copy/paste code available below):

* **index.php**: Displays a form for registration and a table containing registrant information.

To build and run the application locally, follow the steps below. Note that these steps assume you have PHP, the MySQL Command-Line Tool (part of MySQL), and a web server set up on your local machine, and that you have enabled the [PDO extension for MySQL][pdo-mysql].

1. Connect to the remote MySQL server, using the value for `Data Source`, `User Id`, `Password`, and `Database` that you retrieved earlier:

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. The MySQL command prompt will appear:

		mysql>

3. Paste in the following `CREATE TABLE` command to create the `registration_tbl` table in your database:

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. In your web server's root directory, create a folder called `registration` and create a file in it called `index.php`.

5. Open the **index.php** file in a text editor or IDE and add the following code, and complete the necessary changes marked with `//TODO:` comments.


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
			// DB connection info
			//TODO: Update the values for $host, $user, $pwd, and $db
			//using the values you retrieved earlier from the portal.
			$host = "value of Data Source";
			$user = "value of User Id";
			$pwd = "value of Password";
			$db = "value of Database";
			// Connect to database.
			try {
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			// Insert registration info
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
			// Retrieve data
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
		?>
		</body>
		</html>

A questo punto è possibile passare a **http://localhost/registration/index.php** per testare l'applicazione.


##Pubblicare l'applicazione

Dopo aver testato l'applicazione in locale, è possibile pubblicarla nel sito Web di Azure tramite Git. Inizializzare l'archivio Git locale e pubblicare l'applicazione.

> [WACOM.NOTE]
> Questi passaggi sono uguali a quelli illustrati nel portale alla fine della precedente sezione Creare un sito Web di Azure e configurare la pubblicazione Git.

1. (Facoltativo) Se l'URL dell'archivio remoto Git è stato dimenticato o smarrito, passare alla scheda Development nel portale.
	
	![Get Git URL][git-instructions]

1. Aprire GitBash (o un terminale, se Git si trova in "PATH"), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Verrà richiesto di specificare la password creata in precedenza.

	![Initial Push to Azure via Git][git-initial-push]

2. Passare a **http://[nome sito].azurewebsites.net/index.php** per iniziare a usare l'applicazione. Queste informazioni verranno archiviate nel dashboard dell'account:

	![Azure PHP web site][running-app]

Dopo aver pubblicato l'applicazione, è possibile iniziare ad apportarvi modifiche e ad usare Git per pubblicarle. 

##Pubblicazione delle modifiche apportate all'applicazione

Per pubblicare le modifiche apportate all'applicazione, eseguire la procedura seguente:

1. Apportare le modifiche all'applicazione in locale.
2. Aprire GitBash (o un terminale, se Git si trova in "PATH"), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Verrà richiesto di specificare la password creata in precedenza.

	![Pushing site changes to Azure via Git][git-change-push]

3. Passare a **http://[nome sito].azurewebsites.net/index.php** per visualizzare l'applicazione e le eventuali modifiche apportate:

	![Azure PHP web site][running-app]

4. È inoltre possibile visualizzare la nuova distribuzione nella scheda 'Deployments' del portale di gestione di Azure:

	![List of web site deployments][deployments-list]

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/it-it/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/it-it/download/details.aspx?id=20098
[install-git]: http://git-scm.com/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png


[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[sql-database-editions]: http://msdn.microsoft.com/it-it/library/windowsazure/ee621788.aspx
