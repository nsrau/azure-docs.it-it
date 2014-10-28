<properties linkid="develop-php-website-with-mysql-and-git" urlDisplayName="Web w/ MySQL + Git" pageTitle="PHP website with MySQL and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git

Questa esercitazione illustra come creare un sito Web di Azure PHP-MySQL e distribuirlo tramite Git. Verranno utilizzati [PHP][PHP], lo strumento da riga di comando MySQL (parte di [MySQL][install-mysql]), un server Web e [Git][Git] installato nel computer. Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo, tra cui Windows, Mac e Linux. Dopo aver completato questa guida, si disporrà di un sito Web PHP-MySQL in esecuzione in Azure.

Si apprenderà come:

-   Creare un sito Web di Azure e un database MySQL mediante il portale di gestione di Azure. Poiché PHP è abilitato in Siti Web di Azure per impostazione predefinita, non è necessario completare operazioni speciali per eseguire il codice PHP.
-   Pubblicare e ripubblicare l'applicazione in Azure tramite Git.

Seguendo questa esercitazione, verrà creata una semplice applicazione Web di registrazione in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione completata:

![Sito Web PHP di Azure][Sito Web PHP di Azure]

<div class="dev-callout"><strong>Nota</strong> <p>Per completare questa esercitazione &egrave; necessario disporre di un account Azure in cui sia abilitata la funzionalit&agrave; Siti Web di Azure. Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">versione di valutazione gratuita di Azure</a>.</p> </div>

## Configurare l'ambiente di sviluppo

In questa esercitazione si presuppone che siano presenti [PHP][PHP], lo strumento da riga di comando MySQL (parte di [MySQL][install-mysql]), un server Web e [Git][Git] installato nel computer.

> [WACOM.NOTE]
> Se l'esercitazione viene eseguita in Windows, è possibile configurare il computer per PHP e configurare automaticamente IIS (il server Web incorporato in Windows) installando [Azure SDK per PHP][Azure SDK per PHP].

## <span id="create-web-site-and-set-up-git"></span></a>Creazione di un sito Web di Azure e configurazione della pubblicazione Git

Per creare un sito Web di Azure e un database MySQL, eseguire questa procedura:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic sull'icona **New** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito Web di Azure][Creazione di un nuovo sito Web di Azure]

3.  Fare clic su **Sito WEB** e quindi su **Creazione personalizzata**.

    ![Creazione personalizzata di un nuovo sito Web][Creazione personalizzata di un nuovo sito Web]

    Immettere un valore per **URL**, selezionare **Crea un nuovo database MySQL** dall'elenco a discesa **Database**, quindi selezionare il data center per il sito Web nell'elenco a discesa **Area**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

    ![Inserimento di dettagli sul sito Web][Inserimento di dettagli sul sito Web]

4.  Immettere un valore per **Name**, scegliere il data center per il database nell'elenco a discesa **Region**, quindi selezionare la casella che indica che si accettano le note legali. Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo.

    ![Creazione di un nuovo database MySQL][Creazione di un nuovo database MySQL]

    In seguito alla creazione del sito Web verrà visualizzato il messaggio **Creazione del sito Web "[NOMESITO]" completata**. A questo punto, è possibile abilitare la pubblicazione Git.

5.  Fare clic sul nome del sito Web visualizzato nell'elenco dei siti per aprire il dashboard **Avvio rapido**.

    ![Apertura del dashboard del sito Web][Apertura del dashboard del sito Web]

6.  Nella parte inferiore della pagina **QuickStart** fare clic su **Set up Git publishing**.

    ![Configurazione della pubblicazione Git][Configurazione della pubblicazione Git]

7.  Per abilitare la pubblicazione Git, è necessario specificare un nome utente e una password. Prendere nota del nome utente e della password creati. Se è stato configurato un archivio Git in precedenza, ignorare questo passaggio.

    ![Creazione di credenziali di pubblicazione][Creazione di credenziali di pubblicazione]

    La configurazione dell'archivio richiederà alcuni secondi.

8.  Quando l'archivio è pronto, verranno visualizzate le istruzioni per effettuare il push dei file dell'applicazione nell'archivio. Prendere nota di queste istruzioni, che saranno necessarie in seguito.

    ![Istruzioni su Git][Istruzioni su Git]

## Recupero di informazioni sulla connessione remota a MySQL

Per connettersi al database MySQL in esecuzione in Siti Web di Azure, è necessario disporre delle apposite informazioni. Per recuperare le informazioni sulla connessione a MySQL, attenersi alla procedura seguente:

1.  Nel dashboard del sito Web fare clic sul collegamento **Visualizza stringhe di connessione** sul lato destro della pagina:

    ![Recupero di informazioni sulla connessione al database][Recupero di informazioni sulla connessione al database]

2.  Prendere nota dei valori di `Database`, `Data Source`, `User Id` e `Password`.

## Creazione e test dell'applicazione in locale

Dopo aver creato un sito Web di Azure, è possibile sviluppare l'applicazione in locale e quindi distribuirla dopo i test.

L'applicazione di registrazione è una semplice applicazione PHP che consente di registrarsi per un evento specificando il proprio nome e l'indirizzo di posta elettronica. Le informazioni sui registranti precedenti vengono visualizzate in una tabella. Le informazioni sulle registrazioni vengono archiviate in un database MySQL. L'applicazione è costituita da un unico file (copiare e incollare il codice disponibile di seguito):

-   **index.php**: consente di visualizzare un modulo per la registrazione e una tabella contenente informazioni sui registranti.

Per creare ed eseguire l'applicazione in locale, attenersi alla procedura seguente. Si noti che per questi passaggi si presuppone che siano presenti PHP, lo strumento da riga di comando MySQL (parte di MySQL) e un server Web configurato sul computer locale e che sia stata abilitata l'[estensione PDO per MySQL][estensione PDO per MySQL].

1.  Connettersi al server MySQL remoto usando i valori di `Data Source`, `User Id`, `Password` e `Database` recuperati in precedenza:

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2.  Verrà visualizzato il prompt dei comandi MySQL:

        mysql>

3.  Incollare il comando `CREATE TABLE` seguente per creare la tabella `registration_tbl` nel database:

        mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4.  Nella directory radice del server Web creare una cartella denominata `registration` e al suo interno creare un file denominato `index.php`.

5.  Aprire il file **index.php** in un editor di testo o in un IDE e aggiungere il codice seguente, quindi completare le necessarie modifiche contrassegnate con commenti `//TODO:`.

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

A questo punto è possibile passare a **<http://localhost/registration/index.php>** per testare l'applicazione.

## Pubblicare l'applicazione

Dopo aver testato l'applicazione in locale, è possibile pubblicarla nel sito Web di Azure tramite Git. Inizializzare l'archivio Git locale e pubblicare l'applicazione.

> [WACOM.NOTE]
> Questi passaggi sono uguali a quelli illustrati nel portale alla fine delle precedenti sezioni Creazione di un sito Web di Azure e Configurazione della pubblicazione Git.

1.  (Facoltativo) Se l'URL dell'archivio remoto Git è stato dimenticato o smarrito, passare alla scheda Development nel portale.

    ![Recupero dell'URL di Git][Istruzioni su Git]

2.  Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Verrà richiesto di specificare la password creata in precedenza.

    ![Push iniziale in Azure tramite Git][Push iniziale in Azure tramite Git]

3.  Passare a **[http://[nome sito].azurewebsites.net/index.php][]** per iniziare a usare l'applicazione. Queste informazioni verranno archiviate nel dashboard dell'account:

    ![Sito Web PHP di Azure][Sito Web PHP di Azure]

Dopo aver pubblicato l'applicazione, è possibile iniziare ad apportarvi modifiche e ad utilizzare Git per pubblicarle.

## Pubblicazione delle modifiche apportate all'applicazione

Per pubblicare le modifiche apportate all'applicazione, eseguire la procedura seguente:

1.  Apportare le modifiche all'applicazione in locale.
2.  Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Verrà richiesto di specificare la password creata in precedenza.

    ![Push delle modifiche del sito apportate in Azure tramite Git][Push delle modifiche del sito apportate in Azure tramite Git]

3.  Passare a **[http://[nome sito].azurewebsites.net/index.php][]** per visualizzare l'applicazione e le eventuali modifiche apportate:

    ![Sito Web PHP di Azure][Sito Web PHP di Azure]

4.  È inoltre possibile visualizzare la nuova distribuzione nella scheda 'Deployments' del portale di gestione di Azure:

    ![Elenco di distribuzioni di siti Web][Elenco di distribuzioni di siti Web]

  [PHP]: http://www.php.net/manual/en/install.php
  [Git]: http://git-scm.com/
  [Sito Web PHP di Azure]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A74E0F923
  [Azure SDK per PHP]: http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [Creazione di un nuovo sito Web di Azure]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
  [Creazione personalizzata di un nuovo sito Web]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
  [Inserimento di dettagli sul sito Web]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
  [Creazione di un nuovo database MySQL]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
  [Apertura del dashboard del sito Web]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
  [Configurazione della pubblicazione Git]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
  [Creazione di credenziali di pubblicazione]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png
  [Istruzioni su Git]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
  [Recupero di informazioni sulla connessione al database]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
  [estensione PDO per MySQL]: http://www.php.net/manual/en/ref.pdo-mysql.php
  [Push iniziale in Azure tramite Git]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
  [http://[nome sito].azurewebsites.net/index.php]: http://[site
  [Push delle modifiche del sito apportate in Azure tramite Git]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
  [Elenco di distribuzioni di siti Web]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
