<properties linkid="develop-php-website-with-storage" urlDisplayName="Web w/ Storage" pageTitle="PHP website with table storage - Azure tutorial" metaKeywords="Azure table storage PHP, Azure PHP website, Azure PHP web site, Azure PHP tutorial, Azure PHP example" description="This tutorial shows you how to create a PHP website and use the Azure Tables storage service in the back-end." metaCanonical="" services="web-sites,storage" documentationCenter="PHP" title="Create a PHP Website using Azure Storage" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Creare un sito Web PHP mediante il portale di gestione di Azure

In questa esercitazione viene illustrato come creare un sito Web PHP e usare il servizio di archiviazione tabelle Azure nel back-end. A tale scopo, si presuppone che [PHP][PHP] e un server Web siano stati installati nel computer. Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo, tra cui Windows, Mac e Linux. Dopo aver completato questa guida, si disporrà di un sito Web PHP in esecuzione in Azure e in grado di accedere al servizio di archiviazione tabelle.

Si apprenderà come:

-   Installare le librerie client e includerle nell'applicazione.
-   Usare le librerie client per la creazione di tabelle e per la creazione, l'interrogazione e l'eliminazione di entità di tabella.
-   Creare un account di archiviazione di Azure e configurare l'applicazione in modo da utilizzarlo.
-   Come creare un sito Web di Azure e distribuirlo tramite Git

Verrà creata una semplice applicazione Web Tasklist in PHP. Di seguito è riportata una schermata dell'applicazione completata:

![Sito Web PHP di Azure][Sito Web PHP di Azure]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Installazione delle librerie client di Azure

Per installare le librerie client PHP per Azure tramite Composer, eseguire la procedura seguente:

1.  [Installazione di Git][Installazione di Git]

    > [WACOM.NOTE]
    > In Windows sarà inoltre necessario aggiungere l'eseguibile Git alla variabile di ambiente PATH.

2.  Creare un file denominato **composer.json** nella radice del progetto, quindi aggiungere nel file il codice seguente:

        {
            "require": {
                "microsoft/windowsazure": "*"
            },          
            "repositories": [
                {
                    "type": "pear",
                    "url": "http://pear.php.net"
                }
            ],
            "minimum-stability": "dev"
        }

3.  Scaricare **[composer.phar][composer.phar]** nella radice del progetto.

4.  Aprire un prompt dei comandi ed eseguire quanto segue nella radice del progetto

        php composer.phar install

## Guida introduttiva alle librerie client

Per effettuare una chiamata a un'API di Azure durante l'uso delle librerie è necessario eseguire quattro passaggi fondamentali. Si apprenderà a creare uno script di inizializzazione che consentirà di eseguire questi passaggi.

-   Creare un file denominato **init.php**.

-   Includere innanzitutto lo script autoloader:

        require_once 'vendor\autoload.php'; 

-   Includere gli spazi dei nomi che si intende usare.

    Per creare un client di servizio di Azure, è necessario usare la classe **ServicesBuilder**:

        use WindowsAzure\Common\ServicesBuilder;

    Per individuare le eccezioni prodotte da qualsiasi chiamata API è necessaria la classe **ServiceException**:

        use WindowsAzure\Common\ServiceException;

-   Per creare un'istanza di un client del servizio è necessario inoltre disporre di una stringa di connessione valida. Il formato della stringa di connessione del Servizio tabelle è:

    Per accedere a un servizio attivo:

        DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

    Per accedere alla memoria dell'emulatore:

        UseDevelopmentStorage=true

-   Usare il metodo `ServicesBuilder::createTableService` per creare un'istanza di un wrapper per le chiamate del servizio tabelle.

        $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    `$tableRestProxy` contiene un metodo per ogni chiamata REST disponibile nelle tabelle Azure.

## Creazione di una tabella

Prima di poter memorizzare i dati è necessario aver creato il relativo contenitore, cioè la tabella.

-   Creare un file denominato **createtable.php**.

-   Innanzitutto, includere lo script di inizializzazione appena creato, che verrà incluso anche in ogni file con accesso ad Azure:

        <?php
        require_once "init.php";

-   Quindi, eseguire una chiamata a *createTable* passando il nome della tabella. In maniera analoga ad altri archivi di tabelle NoSQL. non è richiesto alcuno schema per le tabelle di Azure.

        try {
            $tableRestProxy->createTable('tasks');
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }
        ?>

    I codici di errore e la scansione dei messaggi sono disponibili all'indirizzo: [http://msdn.microsoft.com/it-it/library/windowsazure/dd179438.aspx][http://msdn.microsoft.com/it-it/library/windowsazure/dd179438.aspx]

## Query di una tabella

Nella pagina iniziale dell'applicazione Tasklist dovrebbero essere elencate tutte le attività esistenti e dovrebbe essere possibile inserirne di nuove.

-   Creare un file denominato **index.php** e inserire il codice HTML e PHP seguente per formare l'intestazione della pagina:

        <html>
        <head>
            <title>Index</title>
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
        <h1>My ToDo List <font color="grey" size="5">(powered by PHP and Azure Tables) </font></h1>
        <?php       
        require_once "init.php";

-   Per eseguire la query delle tabelle di Azure per **tutte le entità** memorizzate nella tabella *tasks* chiamare il metodo *queryEntities* passando solo il nome della tabella. Nella sezione **Aggiornamento di un'entità** qui di seguito verrà inoltre illustrato come passare un filtro per la query di un'entità specifica.

        try {
            $result = $tableRestProxy->queryEntities('tasks');
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   Per scorrere le entità nel gruppo di risultati:

        $entities = $result->getEntities();

        for ($i = 0; $i < count($entities); $i++) {

-   Dopo aver ottenuto un'`Entity`, il modello per la lettura dei dati è `Entity->getPropertyValue('[name]')`:

            if ($i == 0) {
                echo "<table border='1'>
                <tr>
                    <td>Name</td>
                    <td>Category</td>
                    <td>Date</td>
                    <td>Mark Complete?</td>
                    <td>Delete?</td>
                </tr>";
            }
            echo "
                <tr>
                    <td>".$entities[$i]->getPropertyValue('name')."</td>
                    <td>".$entities[$i]->getPropertyValue('category')."</td>
                    <td>".$entities[$i]->getPropertyValue('date')."</td>";
                    if ($entities[$i]->getPropertyValue('complete') == false)
                        echo "<td><a href='markitem.php?complete=true&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Mark Complete</a></td>";
                    else
                        echo "<td><a href='markitem.php?complete=false&pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Unmark Complete</a></td>";
                    echo "
                    <td><a href='deleteitem.php?pk=".$entities[$i]->getPartitionKey()."&rk=".$entities[$i]->getRowKey()."'>Delete</a></td>
                </tr>";
        }

        if ($i > 0)
            echo "</table>";
        else
            echo "<h3>No items on list.</h3>";
        ?>

-   Infine, è necessario inserire il modulo che alimenta i dati nello script di inserimento dell'attività e completare il linguaggio HTML:

            <hr/>
            <form action="additem.php" method="post">
                <table border="1">
                    <tr>
                        <td>Item Name: </td>
                        <td><input name="itemname" type="textbox"/></td>
                    </tr>
                    <tr>
                        <td>Category: </td>
                        <td><input name="category" type="textbox"/></td>
                    </tr>
                    <tr>
                        <td>Date: </td>
                        <td><input name="date" type="textbox"/></td>
                    </tr>
                </table>
                <input type="submit" value="Add item"/>
            </form>
        </body>
        </html>

## Inserimento di entità in una tabella

L'applicazione è ora in grado di leggere tutti gli elementi memorizzati nella tabella. Poiché la tabella sarà inizialmente vuota, aggiungere una funzione che scriva i dati nel database.

-   Creare un file denominato **additem.php**.

-   Aggiungere il codice seguente al file:

        <?php       
        require_once "init.php";        
        use WindowsAzure\Table\Models\Entity;
        use WindowsAzure\Table\Models\EdmType;      

-   Il primo passaggio dell'inserimento di un'entità consiste nella creazione di un'istanza di un oggetto `Entity` e nell'impostazione delle relative proprietà:

        $entity = new Entity();
        $entity->setPartitionKey('p1');
        $entity->setRowKey((string) microtime(true));
        $entity->addProperty('name', EdmType::STRING, $_POST['itemname']);
        $entity->addProperty('category', EdmType::STRING, $_POST['category']);
        $entity->addProperty('date', EdmType::STRING, $_POST['date']);
        $entity->addProperty('complete', EdmType::BOOLEAN, false);

-   Quindi è possibile passare il valore `$entity` appena creato nel metodo `insertEntity`:

        try{
            $tableRestProxy->insertEntity('tasks', $entity);
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   Infine, per far sì che la pagina torni alla pagina iniziale dopo aver inserito l'entità:

        header('Location: index.php');      
        ?>

## Aggiornamento di un'entità

L'app di elenco attività è in grado di contrassegnare un elemento come completo, ma anche di rimuovere tale contrassegno. La pagina iniziale passa nei valori *RowKey* e *PartitionKey* di un'entità e dello stato di destinazione (contrassegnato==1, deselezionato==0).

-   Creare un file denominato **markitem.php** e aggiungere la parte dell'inizializzazione:

        <?php       
        require_once "init.php";

-   Il primo passaggio dell'aggiornamento di un'entità è il suo recupero dalla tabella:

        $result = $tableRestProxy->queryEntities('tasks', 'PartitionKey eq \''.$_GET['pk'].'\' and RowKey eq \''.$_GET['rk'].'\'');     
        $entities = $result->getEntities();     
        $entity = $entities[0];

    Come è possibile notare, il filtro query passato è sotto forma di `Key eq 'Value'`. Una descrizione completa della sintassi della query è disponibile [qui][qui].

-   Sarà quindi possibile modificare qualsiasi proprietà:

        $entity->setPropertyValue('complete', ($_GET['complete'] == 'true') ? true : false);

-   Mentre il metodo `updateEntity` esegue l'aggiornamento:

        try{
            $result = $tableRestProxy->updateEntity('tasks', $entity);
        }
        catch(ServiceException $e){
            $code = $e->getCode();
            $error_message = $e->getMessage();
            echo $code.": ".$error_message."<br />";
        }

-   Per far sì che la pagina torni alla pagina iniziale dopo aver inserito l'entità:

        header('Location: index.php');      
        ?>

## Eliminazione di un'entità

L'eliminazione di un elemento si ottiene con un'unica chiamata a `deleteItem`. I valori passati sono **PartitionKey** e **RowKey**, che assieme compongono la chiave primaria dell'entità. Creare un file denominato **deleteitem.php** e inserire il codice seguente:

        <?php
        
        require_once "init.php";        
        $tableRestProxy->deleteEntity('tasks', $_GET['pk'], $_GET['rk']);       
        header('Location: index.php');
        
        ?>

## Creare un account di archiviazione di Azure

Per trasferire i dati dell'account dell'archivio applicazione nel cloud è necessario prima creare un account di archiviazione in Azure e poi passare le corrette informazioni di autenticazione alla classe *Configuration*.

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic sull'icona **+ Nuovo** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito Web di Azure][Creazione di un nuovo sito Web di Azure]

3.  Fare clic su **Data Services**, **Storage** e quindi su **Quick Create**.

    ![Creazione personalizzata di un nuovo sito Web][Creazione personalizzata di un nuovo sito Web]

    Immettere un valore per **URL** e scegliere il data center per il sito Web nell'elenco a discesa **REGIONE**. Nella parte inferiore della finestra di dialogo fare clic sul collegamento **Create Storage Account**.

    ![Inserimento di dettagli sul sito Web][Inserimento di dettagli sul sito Web]

    In seguito alla creazione dell'account di archiviazione verrà visualizzato il messaggio **Creation of Storage Account '[NOME]' completed successfully**.

4.  Assicurarsi che la scheda **Storage** sia selezionata, quindi selezionare l'account di archiviazione appena creato dall'elenco.

5.  Fare clic su **Manage Keys** sulla barra dell'app in basso.

    ![Selezionare Manage Keys][Selezionare Manage Keys]

6.  Prendere nota del nome dell'account di archiviazione creato e quello della chiave primaria.

    ![Selezionare Manage Keys][1]

7.  Aprire **init.php** e sostituire `[YOUR_STORAGE_ACCOUNT_NAME]` e `[YOUR_STORAGE_ACCOUNT_KEY]` con il nome dell'account e la chiave annotati nel passaggio precedente. Salvare il file.

## Creare un sito Web di Azure e configurare la pubblicazione Git

Per creare un sito Web di Azure, attenersi alla procedura seguente:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic sull'icona **+ Nuovo** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito Web di Azure][Creazione di un nuovo sito Web di Azure]

3.  Fare clic su **Calcolo**, **Sito Web**, quindi su **Creazione rapida**.

    ![Creazione personalizzata di un nuovo sito Web][2]

    Immettere un valore per **URL** e scegliere il data center per il sito Web nell'elenco a discesa **REGIONE**. Nella parte inferiore della finestra di dialogo fare clic sul collegamento **Crea nuovo sito Web**.

    ![Inserimento di dettagli sul sito Web][3]

    In seguito alla creazione del sito Web verrà visualizzato il messaggio **Creazione del sito Web "[NOMESITO]" completata**. A questo punto, è possibile abilitare la pubblicazione Git.

4.  Fare clic sul nome del sito Web visualizzato nell'elenco dei siti Web per aprire il dashboard **AVVIO RAPIDO**.

    ![Apertura del dashboard del sito Web][Apertura del dashboard del sito Web]

5.  Nella parte inferiore destra della pagina Quickstart selezionare **Set up a deployment from source control**.

    ![Configurazione della pubblicazione Git][Configurazione della pubblicazione Git]

6.  Quando viene visualizzata la domanda "Where is your source code?", selezionare **Local Git repository**, quindi fare clic sulla freccia.

    ![Posizione del codice][Posizione del codice]

7.  Per abilitare la pubblicazione Git, è necessario specificare un nome utente e una password. Prendere nota del nome utente e della password creati. Se è stato configurato un repository Git in precedenza, ignorare questo passaggio.

    ![Creazione di credenziali di pubblicazione][Creazione di credenziali di pubblicazione]

    La configurazione del repository richiederà alcuni secondi.

8.  Quando il repository Git è pronto, verranno visualizzate le istruzioni sui comandi Git da usare per configurare un repository locale e quindi effettuare il push dei file in Azure.

    ![Istruzioni sulla distribuzione Git restituite dopo la creazione di un repository per il sito Web.][Istruzioni sulla distribuzione Git restituite dopo la creazione di un repository per il sito Web.]

    Prendere nota delle istruzioni, in quanto saranno utilizzate nella sezione successiva per pubblicare l'applicazione.

## Pubblicare l'applicazione

Per pubblicare l'applicazione con Git, attenersi alla procedura seguente.

1.  Aprire la cartella **vendor/microsoft/windowsazure** nella directory principale dell'applicazione ed eliminare i seguenti file e cartelle:

    -   git
    -   gitattributes
    -   gitignore

    Quando la gestione pacchetti Composer scarica le librerie client di Azure e le relative dipendenze effettua la clonazione del repository GitHub in cui risiede. Nel passaggio successivo, l'applicazione verrà distribuita tramite Git creando un repository dalla cartella radice dell'applicazione. Git ignorerà il repository secondario in cui risiedono le librerie del client, a meno che non vengano rimossi file specifici del repository.

2.  Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti (**nota:** questi passaggi sono uguali a quelli riportati alla fine della precedente sezione **Creare un sito Web di Azure e configurare la pubblicazione Git**):

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Verrà richiesto di specificare la password creata in precedenza.

3.  Passare a **http://dominio sito Web/createtable.php** per creare la tabella per l'applicazione.
4.  Passare a **http://dominio sito Web/index.php** per iniziare a usare l'applicazione.

Dopo aver pubblicato l'applicazione, è possibile iniziare ad apportarvi modifiche e ad usare Git per pubblicarle.

## Pubblicazione delle modifiche apportate all'applicazione

Per pubblicare le modifiche apportate all'applicazione, eseguire la procedura seguente:

1.  Apportare le modifiche all'applicazione in locale.
2.  Aprire GitBash (o un terminale, se Git si trova in `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Verrà richiesto di specificare la password creata in precedenza.

3.  Passare a **http://dominio sito Web/index.php** per visualizzare le modifiche.

  [PHP]: http://www.php.net/manual/en/install.php
  [Sito Web PHP di Azure]: ./media/web-sites-php-storage/ws-storage-app.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Installazione di Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [composer.phar]: http://getcomposer.org/composer.phar
  [qui]: http://msdn.microsoft.com/it-it/library/windowsazure/dd894031.aspx
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [Creazione di un nuovo sito Web di Azure]: ./media/web-sites-php-storage/new_website.jpg
  [Creazione personalizzata di un nuovo sito Web]: ./media/web-sites-php-storage/storage-quick-create.png
  [Inserimento di dettagli sul sito Web]: ./media/web-sites-php-storage/storage-quick-create-details.png
  [Selezionare Manage Keys]: ./media/web-sites-php-storage/storage-manage-keys.png
  [1]: ./media/web-sites-php-storage/storage-access-keys.png
  [2]: ./media/web-sites-php-storage/website-quick-create.png
  [3]: ./media/web-sites-php-storage/website-quick-create-details.png
  [Apertura del dashboard del sito Web]: ./media/web-sites-php-storage/go_to_dashboard.png
  [Configurazione della pubblicazione Git]: ./media/web-sites-php-storage/setup_git_publishing.png
  [Posizione del codice]: ./media/web-sites-php-storage/where_is_code.png
  [Creazione di credenziali di pubblicazione]: ./media/web-sites-php-storage/git-deployment-credentials.png
  [Istruzioni sulla distribuzione Git restituite dopo la creazione di un repository per il sito Web.]: ./media/web-sites-php-storage/git-instructions.png
