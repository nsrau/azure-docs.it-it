<properties urlDisplayName="Web w/ MySQL + Git" pageTitle="Sito Web PHP con MySQL e Git - Esercitazione su Azure" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />

#Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git

Questa esercitazione illustra come creare un sito Web di Azure PHP-MySQL e distribuirlo tramite Git. Si useranno [PHP][install-php], lo strumento da riga di comando MySQL (che fa parte di [MySQL][install-mysql]), un server Web e [Git][install-git] installato nel computer. Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo, tra cui Windows, Mac e Linux. Dopo aver completato questa guida, si disporrà di un sito Web PHP-MySQL in esecuzione in Azure.
 
Si apprenderà come:

* Creare un sito Web di Azure e un database MySQL mediante il portale di gestione di Azure. Poiché PHP è abilitato in Siti Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
* Pubblicare e ripubblicare l'applicazione in Azure tramite Git.
 
Seguendo questa esercitazione, verrà creata una semplice applicazione Web di registrazione in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione completata:

![Azure PHP web site][running-app]

> [WACOM.NOTE]
> Per completare questa esercitazione, è necessario disporre di un account Azure in cui sia abilitata la funzionalità Siti Web di Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Versione di valutazione gratuita di Azure</a>.
> 
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Configurare l'ambiente di sviluppo

Questa esercitazione presuppone che nel computer siano installati [PHP][install-php], lo strumento da riga di comando MySQL (che fa parte di [MySQL][install-mysql]), un server Web e [Git][install-git].

> [WACOM.NOTE]
> Se l'esercitazione viene eseguita in Windows, è possibile configurare il computer per PHP e configurare automaticamente IIS (il server Web incorporato in Windows) installando <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">Azure SDK per PHP</a>.

##<a id="create-web-site-and-set-up-git"></a>Creare un sito Web di Azure e configurare la pubblicazione Git

Per creare un sito Web di Azure e un database MySQL, attenersi alla procedura seguente:

1. Accedere al [portale di gestione di Azure][management-portal].
2. Fare clic sull'icona **Nuovo** nella parte inferiore sinistra del portale.

	![Create New Azure web site][new-website]

3. Fare clic su **Sito Web** e quindi su **Creazione personalizzata**.

	![Custom Create a new web site][custom-create]
	
	Immettere un valore per **URL**, scegliere **Crea un nuovo database MySQL** nell'elenco a discesa **Database**, quindi selezionare il data center per il sito Web nell'elenco a discesa **Area**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

	![Fill in web site details][website-details]

4. In **Nome** immettere un valore per il database, selezionare il data center per il database nell'elenco a discesa **Area**, quindi selezionare la casella che indica che si accettano le note legali. Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo.

	![Create new MySQL database][new-mysql-db]

	In seguito alla creazione del sito Web verrà visualizzato il messaggio **La creazione del sito Web "[NOMESITO]" è stata completata**. A questo punto, è possibile abilitare la pubblicazione Git.

6. Fare clic sul nome del sito Web visualizzato nell'elenco dei siti Web per aprire il relativo dashboard **Avvio rapido**.

	![Open web site dashboard][go-to-dashboard]


7. Nella parte inferiore della pagina **Avvio rapido** fare clic su **Configura pubblicazione Git**. 

	![Set up Git publishing][setup-git-publishing]

8. Per abilitare la pubblicazione Git, è necessario specificare un nome utente e una password. Prendere nota del nome utente e della password creati. Se è stato configurato un repository Git in precedenza, ignorare questo passaggio.

	![Create publishing credentials][credentials]

	La configurazione del repository richiederà alcuni secondi.

9. Quando il repository è pronto, verranno visualizzate le istruzioni per effettuare il push dei file dell'applicazione nel repository. Prendere nota di queste istruzioni, che saranno necessarie in seguito.

	![Git instructions][git-instructions]

##Recupero di informazioni sulla connessione remota a MySQL

Per connettersi al database MySQL in esecuzione in Siti Web di Azure, è necessario disporre delle informazioni di connessione. Per recuperare le informazioni sulla connessione a MySQL, attenersi alla procedura seguente:

1. Nel dashboard del sito Web fare clic sul collegamento **Visualizza stringhe di connessione** sul lato destro della pagina:

	![Get database connection information][connection-string-info]
	
2. Prendere nota dei valori di `Database`, `Data Source`, `User Id` e `Password`.

##Creazione e test dell'applicazione in locale

Dopo aver creato un sito Web di Azure, è possibile sviluppare l'applicazione in locale e quindi distribuirla dopo i test. 

L'applicazione di registrazione è una semplice applicazione PHP che consente di registrarsi per un evento specificando il proprio nome e l'indirizzo di posta elettronica. Le informazioni sui registranti precedenti vengono visualizzate in una tabella. Le informazioni sulle registrazioni vengono archiviate in un database MySQL. L'applicazione è costituita da un unico file (copiare e incollare il codice disponibile di seguito):

* **index.php**: consente di visualizzare un modulo per la registrazione e una tabella contenente informazioni sui registranti.

Per creare ed eseguire l'applicazione in locale, attenersi alla procedura seguente. Si noti che per questi passaggi si presuppone che nel computer locale siano già stati configurati PHP, lo strumento da riga di comando MySQL (che fa parte di MySQL) e un server Web e che sia stata abilitata l'[estensione PDO per MySQL][pdo-mysql].

1. Connettersi al server MySQL remoto usando i valori di `Data Source`, `User Id`, `Password` e `Database` recuperati in precedenza:

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. Verrà visualizzato il prompt dei comandi MySQL:

		mysql>

3. Incollare il comando `CREATE TABLE` seguente per creare la tabella `registration_tbl` nel database:

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. Nella directory radice del server Web creare una cartella denominata `registration` e al suo interno creare un file denominato `index.php`.

5. Aprire il file **index.php** in un editor di testo o in un IDE e aggiungere il codice seguente, quindi completare le necessarie modifiche contrassegnate con commenti `//TODO:`.


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
		?>
		</body>
		</html>

A questo punto è possibile passare a **http://localhost/registration/index.php** per testare l'applicazione.


##Pubblicare l'applicazione

Dopo aver testato l'applicazione in locale, è possibile pubblicarla nel sito Web di Azure tramite Git. Inizializzare l'archivio Git locale e pubblicare l'applicazione.

> [WACOM.NOTE]
> Questi passaggi sono uguali a quelli illustrati nel portale alla fine della precedente sezione Creare un sito Web di Azure e configurare la pubblicazione Git.

1. (Facoltativo) Se l'URL del repisitory remoto Git è stato dimenticato o smarrito, passare alla scheda Distribuzione nel portale.
	
	![Get Git URL][git-instructions]

1. Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Verrà richiesto di specificare la password creata in precedenza.

	![Initial Push to Azure via Git][git-initial-push]

2. Passare a **http://[nome sito].azurewebsites.net/index.php** per iniziare a usare l'applicazione (queste informazioni verranno archiviate nel dashboard dell'account):

	![Azure PHP web site][running-app]

Dopo aver pubblicato l'applicazione, è possibile iniziare ad apportarvi modifiche e ad usare Git per pubblicarle. 

##Pubblicazione delle modifiche apportate all'applicazione

Per pubblicare le modifiche apportate all'applicazione, eseguire la procedura seguente:

1. Apportare le modifiche all'applicazione in locale.
2. Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

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

<!--HONumber=35_1-->
