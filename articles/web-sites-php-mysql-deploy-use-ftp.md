<properties urlDisplayName="Web w/ MySQL + FTP" pageTitle="Sito Web PHP con MySQL e FTP - Esercitazione su Azure" metaKeywords="" description="Un'esercitazione che illustra come creare un sito Web PHP che archivia i dati in MySQL e come usare la distribuzione FTP in Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure Website and Deploy Using FTP" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />


#Creare un sito Web di Azure PHP-MySQL e distribuirlo tramite FTP

Questa esercitazione illustra come creare un sito Web di Azure PHP-MySQL e distribuirlo tramite FTP. A tale scopo, si presuppone che nel computer siano installati [PHP][install-php], [MySQL][install-mysql], un server Web e un client FTP. Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo, tra cui Windows, Mac e Linux. Dopo aver completato questa guida, si disporrà di un sito Web PHP-MySQL in esecuzione in Azure.
 
Si apprenderà come:

* Creare un sito Web di Azure e un database MySQL mediante il portale di gestione di Azure. Poiché PHP è abilitato in Siti Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
* Pubblicare l'applicazione in Azure tramite FTP.
 
Seguendo questa esercitazione, verrà creata una semplice applicazione Web di registrazione in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione completata:

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Creare un sito Web di Azure e configurare la pubblicazione FTP

Per creare un sito Web di Azure e un database MySQL, attenersi alla procedura seguente:

1. Accedere al [portale di gestione di Azure][management-portal].
2. Fare clic sull'icona **+ Nuovo** nella parte inferiore sinistra del portale.

	![Create New Azure Web Site][new-website]

3. Fare clic su **SITO WEB** e quindi su **CREAZIONE PERSONALIZZATA**.

	![Custom Create a new Web Site][custom-create]
	
	Immettere un valore per **URL**, scegliere **Crea un nuovo database MySQL** nell'elenco a discesa **DATABASE**, quindi selezionare il data center per il sito Web nell'elenco a discesa **AREA**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

	![Fill in Web Site details][website-details]

4. In **NOME** immettere un valore per il database, selezionare il data center per il database nell'elenco a discesa **AREA**, quindi selezionare la casella che indica che si accettano le note legali. Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo.

	![Create new MySQL database][new-mysql-db]

	In seguito alla creazione del sito Web verrà visualizzato il messaggio **La creazione del sito Web '[NOMESITO]' è stata completata**. A questo punto, è possibile abilitare la pubblicazione FTP.

5. Fare clic sul nome del sito Web visualizzato nell'elenco dei siti Web per aprire il relativo dashboard **AVVIO RAPIDO**.

	![Open web site dashboard][go-to-dashboard]


6. Nella parte inferiore della pagina **AVVIO RAPIDO** fare clic su **Reimposta credenziali di distribuzione**. 

	![Reset deployment credentials][reset-deployment-credentials]

7. Per abilitare la pubblicazione FTP è necessario specificare un nome utente e una password. Prendere nota del nome utente e della password creati.

	![Create publishing credentials][portal-git-username-password]

##Creazione e test dell'applicazione in locale

L'applicazione di registrazione è una semplice applicazione PHP che consente di registrarsi per un evento specificando il proprio nome e l'indirizzo di posta elettronica. Le informazioni sui registranti precedenti vengono visualizzate in una tabella. Le informazioni sulle registrazioni vengono archiviate in un database MySQL. L'applicazione è costituita da due file:

* **index.php**: consente di visualizzare un modulo per la registrazione e una tabella contenente informazioni sui registranti.
* **createtable.php**: consente di creare la tabella MySQL per l'applicazione. Questo file verrà usato una volta sola.

Per creare ed eseguire l'applicazione in locale, attenersi alla procedura seguente. Si noti che per questi passaggi si presuppone che nel computer locale siano già stati configurati PHP, MySQL e un server Web e che sia stata abilitata l'[estensione PDO per MySQL][pdo-mysql].

1. Creare un database MySQL denominato `registration`. A tale scopo, immettere al prompt dei comandi MySQL il comando seguente:

		mysql> create database registration;

2. Nella directory radice del server Web creare una cartella denominata `registration` e al suo interno creare due file: uno denominato `createtable.php` e l'altro denominato `index.php`.

3. Aprire il file `createtable.php` in un editor di testo o IDE e aggiungere il codice seguente. Questo codice verrà usato per creare la tabella `registration_tbl` nel database `registration`.

		<?php
		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
						id INT NOT NULL AUTO_INCREMENT, 
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

	> [WACOM.NOTE] 
	> Sarà necessario aggiornare i valori di <code>$user</code> e <code>$pwd</code> con il nome utente e la password MySQL locali.

4. Aprire un Web browser e passare a [http://localhost/registration/createtable.php][localhost-createtable]. In questo modo, verrà creata la tabella `registration_tbl` nel database.

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

6. Nei tag PHP add PHP code for connecting to the database.

		// DB connection info
		$host = "localhost";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

	> [WACOM.NOTE]
	> Sarà di nuovo necessario aggiornare i valori di <code>$user</code> e <code>$pwd</code> con il nome utente e la password MySQL locali.

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

A questo punto è possibile passare a [http://localhost/registration/index.php][localhost-index] per testare l'applicazione.

##Recuperare le informazioni di connessione a MySQL ed FTP

Per connettersi al database MySQL in esecuzione in Siti Web di Azure, è necessario disporre delle informazioni di connessione. Per recuperare le informazioni sulla connessione a MySQL, attenersi alla procedura seguente:

1. Nel dashboard del sito Web fare clic sul collegamento **Visualizza stringhe di connessione** sul lato destro della pagina:

	![Get database connection information][connection-string-info]
	
2. Prendere nota dei valori di `Database`, `Data Source`, `User Id` e `Password`.

3. Dal dashboard del sito Web fare clic sul collegamento **Scarica profilo di pubblicazione** in basso a destra nella pagina:

	![Download publish profile][download-publish-profile]

4. Aprire il file `.publishsettings` in un editor XML. 

3. Trovare l'elemento `<publishProfile >` con `publishMethod="FTP"` che abbia un aspetto simile al seguente:

		<publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
			...
		</publishProfile>
	
Prendere nota degli attributi `publishUrl`, `userName` e `userPWD`.

##Pubblicare l'applicazione

Dopo aver testato l'applicazione in locale è possibile pubblicarla nel sito Web di Azure tramite FTP. È tuttavia necessario aggiornare innanzitutto le informazioni di connessione al database nell'applicazione. Usando le informazioni di connessione al database ottenute in precedenza (nella sezione **Recuperare le informazioni di connessione a MySQL ed FTP**) aggiornare le informazioni seguenti in **entrambi** i file `createdatabase.php` e `index.php` con i valori appropriati:

	// DB connection info
	$host = "value of Data Source";
	$user = "value of User Id";
	$pwd = "value of Password";
	$db = "value of Database";

A questo punto è possibile pubblicare l'applicazione tramite FTP.

1. Aprire il client FTP preferito.

2. Immettere nel client FTP la *parte di nome host* dell'attributo `publishUrl` precedentemente annotato.

3. Immettere gli attributi `userName` e `userPWD` precedentemente annotati, senza modifiche, nel client FTP.

4. Stabilire una connessione.

Dopo aver effettuato la connessione si sarà in grado di caricare e scaricare i file in base alle proprie esigenze. Assicurarsi di caricare i file nella directory radice, ovvero `/site/wwwroot`.

Dopo aver caricato i file `index.php` e `createtable.php`, passare a **http://[nome sito].azurewebsites.net/createtable.php** per creare la tabella MySQL per l'applicazione, quindi passare a **http://[nome sito].azurewebsites.net/index.php** per iniziare a usare l'applicazione.
 

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg
[custom-create]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png
[reset-deployment-credentials]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png
[portal-git-username-password]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png


[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[download-publish-profile]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png

<!--HONumber=35.1-->
