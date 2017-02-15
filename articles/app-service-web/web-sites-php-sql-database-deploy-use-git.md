---
title: Creare un&quot;app Web PHP-SQL e distribuire in Azure App Service tramite Git
description: Un&quot;esercitazione che illustra come creare un&quot;app Web PHP che archivia i dati nel database SQL di Azure e come usare la distribuzione Git nel servizio app di Azure.
services: app-service\web, sql-database
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 6b090bf6-31d8-4b74-81eb-050ef95929ca
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 58791f5362182596de8b792c408e392d8d6cbb95


---
# <a name="create-a-php-sql-web-app-and-deploy-to-azure-app-service-using-git"></a>Creare un'app Web PHP-SQL e distribuire in Azure App Service tramite Git
Questa esercitazione illustra come creare nel [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) un'app Web PHP che si connette al database SQL di Azure e come distribuirla tramite Git. Questa esercitazione presuppone che nel computer siano installati [PHP][install-php], [SQL Server Express][install-SQLExpress], i [driver Microsoft per SQL Server per PHP](http://www.microsoft.com/download/en/details.aspx?id=20098) e [Git][install-git]. Dopo aver completato questa guida, si disporrà di un'app Web PHP-MySQL in esecuzione in Azure.

> [!NOTE]
> Per installare e configurare PHP, SQL Server Express e i driver Microsoft per SQL Server per PHP, è possibile usare l' [Installazione guidata piattaforma Web Microsoft](http://www.microsoft.com/web/downloads/platform.aspx).
> 
> 

Si acquisiranno le nozioni seguenti:

* Creare un'app Web di Azure e un database SQL usando il [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Poiché PHP è abilitato nelle app Web di Servizio app di Azure per impostazione predefinita, non è necessario effettuare operazioni particolari per eseguire il codice PHP.
* Pubblicare e ripubblicare l'applicazione in Azure tramite Git.

Seguendo questa esercitazione, verrà creata una semplice applicazione Web di registrazione in PHP, ospitata in un sito Web di Azure. Di seguito è riportata una schermata dell'applicazione completata:

![Sito Web PHP di Azure](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

> [!NOTE]
> Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.
> 
> 

## <a name="create-an-azure-web-app-and-set-up-git-publishing"></a>Creare un'app Web di Azure e configurare la pubblicazione Git
Per creare un'app Web di Azure e un database SQL, seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Aprire Azure Marketplace facendo clic sull'icona **Nuovo** in alto a sinistra nel dashboard, fare clic su **Seleziona tutto** accanto a Marketplace e quindi selezionare **Web e dispositivi mobili**.
3. Nel Marketplace selezionare **Web e dispositivi mobili**.
4. Fare clic sull'icona **Web app + SQL** .
5. Dopo aver letto la descrizione dell'app Web e dell'SQL, fare clic su **Crea**.
6. Fare clic sulle singole parti, **Gruppo di risorse**, **App Web**, **Database** e **Sottoscrizione** e immettere o selezionare i valori per i campi obbligatori:
   
   * Immettere un nome di URL a scelta.    
   * Configurare le credenziali del server di database.
   * Selezionare l'area più vicina.
     
     ![configurazione dell'app](./media/web-sites-php-sql-database-deploy-use-git/configure-db-settings.png)
7. Al termine della definizione dell'app Web, fare clic su **Crea**.
   
    Quando l'app Web viene creata, nel pulsante **Notifiche** lampeggia in verde il testo **OPERAZIONE RIUSCITA** e il pannello del gruppo di risorse si apre per visualizzare sia l'app Web sia il database SQL presenti nel gruppo.
8. Fare clic sull'icona dell'app Web nel pannello del gruppo di risorse per aprire il pannello dell'app Web.
   
    ![Gruppo di risorse per app Web](./media/web-sites-php-sql-database-deploy-use-git/resource-group-blade.png)
9. In **Impostazioni** fare clic su **Distribuzione continua** > **Configurare le impostazioni necessarie**. Selezionare **Archivio Git locale** e fare clic su **OK**.
   
    ![Posizione del codice](./media/web-sites-php-sql-database-deploy-use-git/setup-local-git.png)
   
    Se in precedenza non è stato configurato un repository Git, è necessario fornire nome utente e password. A tale scopo, fare clic su **Impostazioni** > **Credenziali per la distribuzione** nel pannello dell'app Web.
   
    ![](./media/web-sites-php-sql-database-deploy-use-git/deployment-credentials.png)
10. In **Impostazioni** fare clic su **Proprietà** per visualizzare l'URL Git remoto da usare per distribuire l'app PHP in un secondo momento.

## <a name="get-sql-database-connection-information"></a>Recuperare le informazioni sulla connessione al database SQL
Per connettersi all'istanza del database SQL collegata all'app Web, saranno necessarie le informazioni di connessione specificate durante la creazione del database. Per ottenere le informazioni di connessione al database SQL, seguire questa procedura:

1. Tornando nel pannello del gruppo di risorse, fare clic sull'icona del database SQL.
2. Nel pannello del database SQL fare clic su **Impostazioni** > **Proprietà** e quindi su **Mostra stringhe di connessione del database**. 
   
    ![Visualizzazione delle proprietà database](./media/web-sites-php-sql-database-deploy-use-git/view-database-properties.png)
3. Nella sezione **PHP** della finestra di dialogo risultante prendere nota dei valori per `Server`, `SQL Database` e `User Name`. Si useranno questi valori successivamente, al momento di pubblicare l'app Web PHP nel servizio app di Azure.

## <a name="build-and-test-your-application-locally"></a>Creazione e test dell'applicazione in locale
L'applicazione di registrazione è una semplice applicazione PHP che consente di registrarsi per un evento specificando il proprio nome e l'indirizzo di posta elettronica. Le informazioni sui registranti precedenti vengono visualizzate in una tabella. Le informazioni sulle registrazioni vengono archiviate in un'istanza del database SQL. L'applicazione è costituita da due file (copiare e incollare il codice disponibile di seguito):

* **index.php**: consente di visualizzare un modulo per la registrazione e una tabella contenente informazioni sui registranti.
* **createtable.php**: consente di creare la tabella di database SQL per l'applicazione. Questo file verrà utilizzato una volta sola.

Per eseguire l'applicazione in locale, attenersi alla procedura seguente. Si noti che per questi passaggi si presuppone che nel computer locale siano già stati configurati PHP e SQL Server Express e che sia stata abilitata l'[estensione PDO per SQL Server][pdo-sqlsrv].

1. Creare un database di SQL Server denominato `registration`. A tale scopo, immettere nel prompt dei comandi `sqlcmd` i comandi seguenti:
   
        >sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
        1> create database registration
        2> GO    
2. Nella directory radice dell'applicazione creare due file: uno denominato `createtable.php` e l'altro denominato `index.php`.
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
   
    Si noti che sarà necessario aggiornare i valori per <code>$user</code> e <code>$pwd</code> con il nome utente e la password di SQL Server locali.
4. In un terminale nella directory radice dell'applicazione digitare il comando seguente:
   
        php -S localhost:8000
5. Aprire un Web browser e andare a **http://localhost:8000/createtable.php**. Verrà creata la tabella `registration_tbl` nel database.
6. Aprire il file **index.php** in un editor di testo o IDE e aggiungere il codice HTML e CSS di base per la pagina (il codice PHP verrà aggiunto nei passaggi successivi).
   
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
7. All'interno dei tag PHP, aggiungere il codice PHP per la connessione al database.
   
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
   
    Sarà necessario aggiornare i valori di <code>$user</code> e <code>$pwd</code> con il nome utente e la password di MySQL locali.
8. Dopo il codice di connessione al database, aggiungere il codice per l'inserimento delle informazioni di registrazione nel database.
   
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
9. Infine, dopo il codice sopra riportato, aggiungere il codice per recuperare i dati dal database.
   
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

A questo punto è possibile passare a **http://localhost:8000/index.php** per testare l'applicazione.

## <a name="publish-your-application"></a>Pubblicare l'applicazione
Dopo aver testato l'applicazione in locale, è possibile pubblicarla nelle app Web di Servizio app di Azure tramite Git. È tuttavia necessario aggiornare innanzitutto le informazioni di connessione al database nell'applicazione. Usando le informazioni di connessione al database ottenute in precedenza (nella sezione **Ottenere le informazioni di connessione al database SQL**), aggiornare le seguenti informazioni in **entrambi** i file `createdatabase.php` e `index.php` con i valori appropriati:

    // DB connection info
    $host = "tcp:<value of Server>";
    $user = "<value of User Name>";
    $pwd = "<your password>";
    $db = "<value of SQL Database>";

> [!NOTE]
> In <code>$host</code> prima del valore di server è necessario aggiungere <code>tcp:</code>.
> 
> 

A questo punto è possibile configurare la pubblicazione Git e pubblicare l'applicazione.

> [!NOTE]
> Questi passaggi sono uguali a quelli riportati alla fine della sezione precedente **Creare un'app Web di Azure e configurare la pubblicazione Git** .
> 
> 

1. Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione (la directory **registration** ) ed eseguire i comandi seguenti:
   
        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master
   
    Verrà richiesto di specificare la password creata in precedenza.
2. Passare a **http://[nome app Web].azurewebsites.net/createtable.php** per creare la tabella di database SQL per l'applicazione.
3. Passare a **http://[nome app Web].azurewebsites.net/index.php** per iniziare a usare l'applicazione.

Dopo aver pubblicato l'applicazione, è possibile iniziare ad apportarvi modifiche e ad usare Git per pubblicarle. 

## <a name="publish-changes-to-your-application"></a>Pubblicazione delle modifiche apportate all'applicazione
Per pubblicare le modifiche apportate all'applicazione, eseguire la procedura seguente:

1. Apportare le modifiche all'applicazione in locale.
2. Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:
   
        git add .
        git commit -m "comment describing changes"
        git push azure master
   
    Verrà richiesto di specificare la password creata in precedenza.
3. Passare a **http://[nome app Web].azurewebsites.net/index.php** per visualizzare le modifiche.

## <a name="whats-changed"></a>Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv




<!--HONumber=Nov16_HO3-->


