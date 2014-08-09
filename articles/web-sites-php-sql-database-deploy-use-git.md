<properties linkid="develop-php-website-with-sql-database-and-git" urlDisplayName="Web w/ SQL + Git" pageTitle="PHP web site with SQL Database and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP web site that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP web site with a SQL Database and deploy using Git" authors="waltpo" solutions="" manager="" editor="mollybos" />

Creazione di un sito Web PHP con un database SQL e distribuzione tramite Git
============================================================================

In questa esercitazione viene illustrato come creare un sito Web PHP di Azure con un database SQL di Azure e come distribuirlo tramite Git. In questa esercitazione si presuppone che nel computer siano installati [PHP](http://www.php.net/manual/en/install.php), [SQL Server Express](http://www.microsoft.com/en-us/download/details.aspx?id=29062), i [driver Microsoft per SQL Server per PHP](http://www.microsoft.com/en-us/download/details.aspx?id=20098), un server Web e [Git](http://git-scm.com/). Dopo avere completato questa guida, si disporrà di un database SQL-PHP in esecuzione in Azure.

> [WACOM.NOTE] Per installare e configurare PHP, SQL Server Express, i driver Microsoft Drivers per SQL Server per PHP e Internet Information Services (IIS), è possibile utilizzare l'[Installazione guidata piattaforma Web Microsoft](http://www.microsoft.com/web/downloads/platform.aspx).

Si apprenderà come:

-   Creare un sito Web di Azure e un database SQL utilizzando il portale di gestione di Azure. Poiché PHP è abilitato in Siti Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
-   Pubblicare e ripubblicare l'applicazione in Azure tramite Git.

Seguendo questa esercitazione, verrà creata una semplice applicazione Web di registrazione in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione completata:

![Sito Web PHP di Azure](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Creazione di un sito Web di Azure e configurazione della pubblicazione Git
--------------------------------------------------------------------------

Per creare un sito Web di Azure e un database SQL, eseguire la procedura seguente:

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).
2.  Fare clic sull'icona **New** nella parte inferiore sinistra del portale.

	![Creazione di un nuovo sito Web di Azure](./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg)

3.  Fare clic su **Web Site** e quindi su **Custom Create**.

    ![Creazione personalizzata di un nuovo sito Web](./media/web-sites-php-sql-database-deploy-use-git/custom_create.png)

    Immettere un valore per **URL**, scegliere **Create a New SQL Database** nell'elenco a discesa **Database**, quindi selezionare il data center per il sito Web nell'elenco a discesa **Region**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

    ![Inserimento di dettagli sul sito Web](./media/web-sites-php-sql-database-deploy-use-git/website_details_sqlazure.jpg)

4.  Immettere un valore per il campo **Name** relativo al database, selezionare l'opzione per **Edition** [(WEB o BUSINESS)](http://msdn.microsoft.com/it-it/library/windowsazure/ee621788.aspx), selezionare il valore per **Max Size** relativo al database, scegliere il valore per **Collation** e selezionare **NEW SQL Database Server**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

    ![Compilazione delle impostazioni del database SQL](./media/web-sites-php-sql-database-deploy-use-git/database_settings.jpg)

5.  Immettere un nome amministratore e una password (e confermare la password), scegliere l'area in cui verrà creato il nuovo server di database SQL e selezionare la casella `Allow Azure Services to access the server`.

    ![Creazione del nuovo server di database SQL](./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg)

    In seguito alla creazione del sito Web verrà visualizzato il messaggio **Creation of Web Site "[NOMESITO]" completed successfully**. A questo punto, è possibile abilitare la pubblicazione Git.

6.  Fare clic sul nome del sito Web visualizzato nell'elenco dei siti per aprire il dashboard Quick Start.

    ![Apertura del dashboard del sito Web](./media/web-sites-php-sql-database-deploy-use-git/go_to_dashboard.png)

7.  Nella parte inferiore della pagina Quick Start fare clic su **Set up deployment from source control**.

    ![Configurazione della pubblicazione Git](./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png)

8.  Quando viene visualizzata la domanda "Where is your source code?", selezionare **Local Git repository**, quindi fare clic sulla freccia.

    ![Posizione del codice](./media/web-sites-php-sql-database-deploy-use-git/where_is_code.png)

9.  Per abilitare la pubblicazione Git, è necessario specificare un nome utente e una password. Prendere nota del nome utente e della password creati. Se è stato configurato un archivio Git in precedenza, ignorare questo passaggio.

    ![Creazione di credenziali di pubblicazione](./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png)

    La configurazione dell'archivio richiederà alcuni secondi.

10. Quando l'archivio è pronto, verranno visualizzate le istruzioni per effettuare il push dei file dell'applicazione nell'archivio. Prendere nota di queste istruzioni, che saranno necessarie in seguito.

    ![Istruzioni su Git](./media/web-sites-php-sql-database-deploy-use-git/git-instructions.png)

Recuperare le informazioni sulla connessione al database SQL
------------------------------------------------------------

Per connettersi all'istanza del database SQL in esecuzione in Siti Web di Azure è necessario disporre delle apposite informazioni. Per recuperare le informazioni sulla connessione al database SQL, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **Linked Resources** e quindi sul nome del database.

    ![Linked Resources](./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg)

2.  Fare clic su **View connection strings**.

    ![Stringa di connessione](./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg)

3.  Nella sezione **PHP** della finestra di dialogo risultante prendere nota dei valori per `SERVER`, `DATABASE` e `USERNAME`.

Creazione e test dell'applicazione in locale
--------------------------------------------

L'applicazione di registrazione è una semplice applicazione PHP che consente di registrarsi per un evento specificando il proprio nome e l'indirizzo di posta elettronica. Le informazioni sui registranti precedenti vengono visualizzate in una tabella. Le informazioni sulle registrazioni vengono archiviate in un'istanza del database SQL. L'applicazione è costituita da due file (copiare e incollare il codice disponibile di seguito):

-   **index.php**: consente di visualizzare un modulo per la registrazione e una tabella contenente informazioni sui registranti.
-   **createtable.php**: consente di creare la tabella di database SQL per l'applicazione. Questo file verrà utilizzato una volta sola.

Per eseguire l'applicazione in locale, attenersi alla procedura seguente. Si noti che per questi passaggi si presuppone che nel computer locale siano già stati configurati PHP, SQL Server Express e un server Web e che sia stata abilitata l'[estensione PDO per MySQL](http://php.net/pdo_sqlsrv).

1.  Creare un database di SQL Server denominato `registration`. A tale scopo, immettere al prompt dei comandi `sqlcmd` i comandi seguenti:

         >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
         1> create database registration
         2> GO   

2.  Nella directory radice del server Web creare una cartella denominata `registration` e al suo interno creare due file: uno denominato `createtable.php` e l'altro denominato `index.php`.

3.  Aprire il file `createtable.php` in un editor di testo o IDE e aggiungere il codice seguente. Questo codice verrà utilizzato per creare la tabella `registration_tbl` nel database `registration`.

         <
         php
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
          
         >

    Si noti che sarà necessario aggiornare i valori per `$user` and `$pwd` con il nome utente la password di SQL Server locali.

4.  Aprire un browser Web e andare a **http://localhost/registration/createtable.php**. Verrà creata la tabella `registration_tbl` nel database.

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

    Anche in questo caso sarà necessario aggiornare i valori di `$user` e `$pwd` con il nome utente e la password MySQL locali.

7.  Dopo il codice di connessione al database, aggiungere il codice per l'inserimento delle informazioni di registrazione nel database.

         if(!empty($_POST)) {
         try {
             $name = $_POST['name'];
             $email = $_POST['email'];
             $date = date("Y-m-d");
             // Insert data
             $sql_insert = "INSERT INTO registration_tbl (name, email, date) 
                            VALUES (
         ,
         ,
         )";
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

A questo punto è possibile passare a **http://localhost/registration/index.php** per testare l'applicazione.

Pubblicazione dell'applicazione
-------------------------------

Dopo aver testato l'applicazione in locale, è possibile pubblicarla nel sito Web di Azure tramite Git. È tuttavia necessario aggiornare innanzitutto le informazioni di connessione al database nell'applicazione. Utilizzando le informazioni di connessione al database ottenute in precedenza (nella sezione **Recupero di informazioni sulla connessione al database SQL**) aggiornare le informazioni seguenti in **entrambi** i file `createdatabase.php` e `index.php` con i valori appropriati:

    // DB connection info
    $host = "tcp:<value of SERVER>";
    $user = "<value of USERNAME>@<server ID>";
    $pwd = "<your password>";
    $db = "<value of DATABASE>";

> [WACOM.NOTE] In `$host` prima del valore di SERVER è necessario aggiungere `tcp:`, mentre il valore di `$user` è dato dalla concatenazione del valore di USERNAME, di '@' e dell'ID server. L'ID server è costituito dai primi 10 caratteri del valore di SERVER.

A questo punto è possibile configurare la pubblicazione Git e pubblicare l'applicazione.

> [WACOM.NOTE] Questi passaggi sono uguali a quelli riportati alla fine delle precedenti sezioni Creazione di un sito Web di Azure e Configurazione della pubblicazione Git.

1.  Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [URL dell'archivio remoto]
         git push azure master

    Verrà richiesto di specificare la password creata in precedenza.

2.  Passare a **http://[nome sito].azurewebsites.net/createtable.php** per creare la tabella MySQL per l'applicazione.
3.  Passare a **http://[nome sito].azurewebsites.net/index.php** per iniziare a utilizzare l'applicazione.

Dopo aver pubblicato l'applicazione, è possibile iniziare ad apportarvi modifiche e ad utilizzare Git per pubblicarle.

Pubblicazione delle modifiche apportate all'applicazione
--------------------------------------------------------

Per pubblicare le modifiche apportate all'applicazione, eseguire la procedura seguente:

1.  Apportare le modifiche all'applicazione in locale.
2.  Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

         git add .
         git commit -m "comment describing changes"
         git push azure master

    Verrà richiesto di specificare la password creata in precedenza.

3.  Passare a **http://[nome sito].azurewebsites.net/index.php** per visualizzare le modifiche.

