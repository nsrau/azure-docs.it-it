<properties linkid="develop-php-website-with-mysql-and-ftp" urlDisplayName="Web w/ MySQL + FTP" pageTitle="PHP web site with MySQL and FTP - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP web site that stores data in MySQL and use FTP deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure Web Site and Deploy Using FTP" authors="" solutions="" manager="" editor="" />

Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP
=======================================================================

In questa esercitazione viene illustrato come creare un sito Web di Azure PHP-MySQL e distribuirlo tramite FTP. A tale scopo, si presuppone che nel computer siano installati [PHP](http://www.php.net/manual/en/install.php), [MySQL](http://dev.mysql.com/doc/refman/5.6/en/installing.html), un server Web e un client FTP. Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo, tra cui Windows, Mac e Linux. Dopo aver completato questa guida, si disporrà di un sito Web PHP/MySQL in esecuzione in Azure.

Si apprenderà come:

-   Creare un sito Web di Azure e un database MySQL utilizzando il portale di gestione di Azure. Poiché PHP è abilitato in Siti Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
-   Pubblicare l'applicazione in Azure tramite FTP.

Seguendo questa esercitazione, verrà creata una semplice applicazione Web di registrazione in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione completata:

![Sito Web PHP di Azure](./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Creazione di un sito Web di Azure e configurazione della pubblicazione FTP
--------------------------------------------------------------------------

Per creare un sito Web di Azure e un database MySQL, eseguire la procedura seguente:

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com).
2.  Fare clic sull'icona **+ New** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito Web di Azure](./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg)

3.  Fare clic su **WEB SITE** e quindi su **CUSTOM CREATE**.

    ![Creazione personalizzata di un nuovo sito Web](./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png)

    Immettere un valore per **URL**, scegliere **Create a New MySQL Database** nell'elenco a discesa **DATABASE**, quindi selezionare il data center per il sito Web nell'elenco a discesa **REGION**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

    ![Inserimento di dettagli sul sito Web](./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg)

4.  Immettere un valore per **NAME**, scegliere il data center per il database nell'elenco a discesa **REGION**, quindi selezionare la casella che indica che si accettano le note legali. Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo.

    ![Creazione di un nuovo database MySQL](./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg)

    In seguito alla creazione del sito Web verrà visualizzato il messaggio **Creation of Web Site ‘[NOMESITO]’ completed successfully**. A questo punto, è possibile abilitare la pubblicazione FTP.

5.  Fare clic sul nome del sito Web visualizzato nell'elenco dei siti per aprire il dashboard **QUICKSTART**.

    ![Apertura del dashboard del sito Web](./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png)

6.  Nella parte inferiore della pagina **QUICKSTART** fare clic su **Reset deployment credentials**.

    ![Reimpostare le credenziali di distribuzione](./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png)

7.  Per abilitare la pubblicazione FTP è necessario specificare un nome utente e una password. Prendere nota del nome utente e della password creati.

    ![Creazione di credenziali di pubblicazione](./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png)

Creazione e test dell'applicazione in locale
--------------------------------------------

L'applicazione di registrazione è una semplice applicazione PHP che consente di registrarsi per un evento specificando il proprio nome e l'indirizzo di posta elettronica. Le informazioni sui registranti precedenti vengono visualizzate in una tabella. Le informazioni sulle registrazioni vengono archiviate in un database MySQL. L'applicazione è costituita da due file:

-   **index.php**: consente di visualizzare un modulo per la registrazione e una tabella contenente informazioni sui registranti.
-   **createtable.php**: consente di creare la tabella MySQL per l'applicazione. Questo file verrà utilizzato una volta sola.

Per creare ed eseguire l'applicazione in locale, attenersi alla procedura seguente. Si noti che per questi passaggi si presuppone che nel computer locale siano già stati configurati PHP, MySQL e un server Web e che sia stata abilitata l'[estensione PDO per MySQL](http://www.php.net/manual/en/ref.pdo-mysql.php).

1.  Creare un database MySQL in denominato `registration`. A tale scopo, immettere al prompt dei comandi MySQL il comando seguente:

         mysql> create database registration;

2.  Nella directory radice del server Web creare una cartella denominata `registration` e al suo interno creare due file: uno denominato `createtable.php` e l'altro denominato `index.php`.

3.  Aprire il file `createtable.php` in un editor di testo o IDE e aggiungere il codice seguente. Questo codice verrà utilizzato per creare la tabella `registration_tbl` nel database `registration`.

         <
         php
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
          
         >

    > [WACOM.NOTE] Sarà necessario aggiornare i valori di `$user` e `$pwd` con il nome utente e la password MySQL locali.

4.  Aprire un browser Web e andare a [http://localhost/registration/createtable.php](http://localhost/tasklist/createtable.php). Verrà creata la tabella `registration_tbl` nel database.

5.  Aprire il file **index.php** in un editor di testo o IDE e aggiungere il codice HTML e CSS di base per la pagina (il codice PHP verrà aggiunto nei passaggi successivi).

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
         <
        php

         
        >
         </body>
         </html>

6.  All'interno dei tag PHP, aggiungere il codice PHP per la connessione al database.

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

    > [WACOM.NOTE] Sarà nuovamente necessario aggiornare i valori di `$user` e `$pwd` con il nome utente e la password MySQL locali.

7.  Dopo il codice di connessione al database, aggiungere il codice per l'inserimento delle informazioni di registrazione nel database.

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

8.  Infine, dopo il codice sopra riportato, aggiungere il codice per recuperare i dati dal database.

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

A questo punto è possibile passare a [http://localhost/registration/index.php](http://localhost/tasklist/index.php) per testare l'applicazione.

Recupero di informazioni sulla connessione a MySQL ed FTP
---------------------------------------------------------

Per connettersi al database MySQL in esecuzione in Siti Web di Azure, è necessario disporre delle apposite informazioni. Per recuperare le informazioni sulla connessione a MySQL, eseguire la procedura seguente:

1.  Nel dashboard del sito Web fare clic sul collegamento **View connection strings** sul lato destro della pagina:

    ![Recupero di informazioni sulla connessione al database](./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png)

2.  Prendere nota dei valori di `Database`, `Data Source`, `User Id` e `Password`.

3.  Dal dashboard del sito Web fare clic sul collegamento **Download publish profile** in basso a destra nella pagina:

    ![Scaricare un profilo di pubblicazione](./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png)

4.  Aprire il file `.publishsettings` in un editor XML.

5.  Trovare l'elemento `<publishProfile >` con with `publishMethod="FTP"` che abbia un aspetto simile al seguente:

         <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
             ...
         </publishProfile>

Prendere nota degli attributi `publishUrl`, `userName` e `userPWD`.

Pubblicazione dell'applicazione
-------------------------------

Dopo aver testato l'applicazione in locale è possibile pubblicarla nel sito Web di Azure tramite FTP. È tuttavia necessario aggiornare innanzitutto le informazioni di connessione al database nell'applicazione. Utilizzando le informazioni di connessione al database ottenute in precedenza (nella sezione **Recupero di informazioni sulla connessione a MySQL ed FTP**) aggiornare le informazioni seguenti in **entrambi** i file `createdatabase.php` e `index.php` con i valori appropriati:

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

A questo punto è possibile pubblicare l'applicazione tramite FTP.

1.  Aprire il client FTP preferito.

2.  Immettere nel client FTP la *parte di nome host* dell'attributo `publishUrl` precedentemente annotato.

3.  Immettere gli attributi `userName` e `userPWD` precedentemente annotati, senza modifiche, nel client FTP.

4.  Stabilire una connessione.

Dopo aver effettuato la connessione si sarà in grado di caricare e scaricare i file in base alle proprie esigenze. Assicurarsi di caricare i file nella directory radice, ossia `/site/wwwroot`.

Dopo aver caricato i file `index.php` e `createtable.php`, passare a **http://[site name].azurewebsites.net/createtable.php** per creare la tabella MySQL per l'applicazione, quindi passare a **http://[site name].azurewebsites.net/index.php** per iniziare a utilizzare l'applicazione.

