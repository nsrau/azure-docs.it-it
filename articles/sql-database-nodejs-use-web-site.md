<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="Website with SQL Database" pageTitle="Node.js website with SQL Database - Azure tutorial" metaKeywords="" description="Learn how to create a Node.js website that accesses a SQL Database and is deployed to Azure" metaCanonical="" services="web-sites,sql-database" documentationCenter="nodejs" title="Node.js Web Application using the Azure SQL Database" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr" />

# Creazione dell'applicazione Web Node.js con il database SQL di Azure

In questa esercitazione viene illustrato come usare il database SQL fornito da Gestione dati di Azure per archiviare e accedere ai dati da un'applicazione [node][node] ospitata in Azure. In questa esercitazione si presuppone che l'utente abbia già utilizzato l'applicazione Node e [Git][Git].

Si apprenderà come:

-   Usare il portale di gestione di Azure per creare un sito Web e il database SQL di Azure

-   Usare npm (Node Package Manager) per installare i moduli di Node

-   Gestire un database SQL tramite il modulo node-sqlserver

-   Usare app settings per specificare valori in fase di esecuzione per un'applicazione

In questa esercitazione verrà creata una semplice applicazione di gestione delle attività basata su Web che consente di creare, recuperare e completare le attività. Le attività sono archiviate nel database SQL.

I file di progetto per questa esercitazione verranno archiviati in una directory denominata **tasklist** e l'applicazione completata presenterà un aspetto simile al seguente:

![Pagina Web con un elenco di attività vuoto][Pagina Web con un elenco di attività vuoto]

<div class="dev-callout">
<b>Nota</b>
<p>Il driver Microsoft per Node.js per SQL Server utilizzato in questa esercitazione &egrave; attualmente disponibile come versione di anteprima e si basa su componenti di runtime disponibili solo nei sistemi operativi Microsoft Windows e Azure.</p>
</div>

<div class="dev-callout">
<strong>Nota</strong>
<p>In questa esercitazione si fa riferimento alla cartella <strong>tasklist</strong>. Il percorso completo della cartella &egrave; omesso, in quanto la semantica dei percorsi varia da un sistema operativo all'altro. &Egrave; consigliabile creare la cartella in un percorso di facile accesso nel file system locale, ad esempio <strong>~/node/tasklist</strong> o <strong>c:\node\tasklist</strong></p>
</div>

<div class="dev-callout">
<strong>Nota</strong>
<p>Molte delle procedure illustrate di seguito prevedono l'uso della riga di comando. Per tali procedure, usare la riga di comando per il sistema operativo in uso, ad esempio <strong>cmd.exe</strong> (Windows) o <strong>Bash</strong> (shell Unix). Nei sistemi OS X &egrave; possibile accedere alla riga di comando tramite l'applicazione Terminale.</p>
</div>

## Prerequisiti

Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili i seguenti elementi:

-   [node][node] 0.6.14 o versione successiva

-   [Git][Git]

-   Librerie di Microsoft SQL Server Native Client, disponibili nel [Feature Pack di Microsoft SQL Server 2012][Feature Pack di Microsoft SQL Server 2012]

-   Un editor di testo

-   Un Web browser

<!--div chunk="../../Shared/Chunks/create-account-and-websites-note.md" /-->

## Creare un sito Web con database

Per creare un sito Web di Azure e un database SQL, attenersi alla procedura seguente:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Fare clic sull'icona **+ Nuovo** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito Web di Azure][Creazione di un nuovo sito Web di Azure]

3.  Fare clic su **SITO WEB** e quindi su **CREAZIONE PERSONALIZZATA**.

    ![Creazione personalizzata di un nuovo sito Web][Creazione personalizzata di un nuovo sito Web]

    Immettere un valore per **URL**, selezionare **Crea un nuovo database SQL** dall'elenco a discesa **DATABASE** e selezionare il data center per il sito Web dall'elenco a discesa **REGIONE**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

    ![Inserimento di dettagli sul sito Web][Inserimento di dettagli sul sito Web]

4.  Immettere un valore per il campo **NOME** relativo al database, selezionare l'opzione per **EDIZIONE** [(WEB o BUSINESS)][(WEB o BUSINESS)], selezionare il valore per **DIMENSIONI MASSIME** relativo al database, scegliere il valore per **REGOLE DI CONFRONTO** e selezionare **NUOVO SERVER DI DATABASE SQL**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

    ![Compilazione delle impostazioni del database SQL][Compilazione delle impostazioni del database SQL]

5.  Immettere un nome amministratore e una password (e confermare la password), scegliere l'area in cui verrà creato il nuovo server di database SQL e selezionare la casella **Allow Azure Services to access the server**.

    ![Creazione del nuovo server di database SQL][Creazione del nuovo server di database SQL]

    In seguito alla creazione del sito Web verrà visualizzato il messaggio **Creazione del sito Web "[NOMESITO]" completata**. A questo punto, è possibile abilitare la pubblicazione Git.

6.  Fare clic sul nome del sito Web visualizzato nell'elenco dei siti Web per aprire il dashboard Avvio rapido.

    ![Apertura del dashboard del sito Web][Apertura del dashboard del sito Web]

7.  Nella parte inferiore della pagina Avvio rapido fare clic su **Set up Git publishing**.

    ![Configurazione della pubblicazione Git][Configurazione della pubblicazione Git]

8.  Per abilitare la pubblicazione Git, è necessario specificare un nome utente e una password. Prendere nota del nome utente e della password creati. Se è stato configurato un repository Git in precedenza, ignorare questo passaggio.

    ![Creazione di credenziali di pubblicazione][Creazione di credenziali di pubblicazione]

    La configurazione del repository richiederà alcuni secondi.

9.  Quando il repository è pronto, verranno visualizzate le istruzioni per effettuare il push dei file dell'applicazione nel repository. Prendere nota di queste istruzioni, che saranno necessarie in seguito.

    ![Istruzioni su Git][Istruzioni su Git]

## Recuperare le informazioni sulla connessione al database SQL

Per connettersi all'istanza del database SQL in esecuzione in Siti Web di Azure è necessario disporre delle informazioni di connessione. Per recuperare le informazioni sulla connessione al database SQL, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **LINKED RESOURCES** e quindi sul nome del database.

    ![Linked Resources][Linked Resources]

2.  Fare clic su **View connection strings**.

    ![Stringa di connessione][Stringa di connessione]

3.  Nella sezione **ODBC** della finestra risultante, prendere nota della stringa di connessione in quanto verrà utilizzata più tardi.

## Progettazione della tabella delle attività

Per creare la tabella di database utilizzata per archiviare gli elementi per l'applicazione tasklist, eseguire le operazioni seguenti:

1.  Dal portale di gestione di Azure selezionare il database SQL e fare clic su **MANAGE** nella parte inferiore della pagina. Se viene visualizzato un messaggio in cui si informa che l'IP corrente non è incluso nelle regole firewall, scegliere **OK** per aggiungerlo.

    ![Pulsante Manage][Pulsante Manage]

2.  Effettuare l'accesso con il nome e la password selezionati in precedenza durante la creazione del server database.

    ![Accesso per la gestione del database][Accesso per la gestione del database]

3.  Nella parte inferiore sinistra della pagina selezionare **Progettazione** e quindi **Nuova tabella**.

    ![Nuova tabella][Nuova tabella]

4.  Immettere 'tasks' in **Nome tabella** e selezionare **Identità?** per la colonna **ID**.

    ![Nome di tabella impostato su tasks e opzione Identità? selezionata][Nome di tabella impostato su tasks e opzione Identità? selezionata]

5.  Modificare **Colonna1** su **name** e **Colonna2** su **category**. Aggiungere due nuove colonne facendo clic sul pulsante **Aggiungi colonna**. Assegnare alla prima nuova colonna il nome **created** e il tipo **date**. Assegnare alla seconda nuova colonna il nome **completed** e il tipo **bit**. Entrambe le nuove colonne devono essere contrassegnate come **Richiesto?**.

    ![Progettazione della tabella completata][Progettazione della tabella completata]

6.  Fare clic sul pulsante **Salva** per salvare le modifiche apportate alla tabella. È ora possibile chiudere la pagina di gestione del database SQL.

## Installazione dei moduli e generazione dello scaffolding

In questa sezione verrà creata una nuova applicazione Node e verrà usato npm per aggiungere pacchetti di modulo. Per l'applicazione tasklist verranno utilizzati i moduli [express][express] e [node-sqlserver][node-sqlserver]. Il modulo express fornisce un framework View Controller per node, mentre node-sqlserver fornisce la connettività al database SQL di Azure.

### Installare Express e generare lo scaffolding

1.  Dalla riga di comando passare alla directory **tasklist**. Se la directory **tasklist** non esiste, crearla.

2.  Immettere il comando seguente per installare Express.

        npm install express -g

    <div class="dev-callout">
<strong>Nota</strong>
<p>Quando si utilizza il parametro '-g' in alcuni sistemi operativi, &egrave; possibile che venga restituito l'errore analogo a: <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong> con la richiesta di provare ad eseguire l'account come amministratore. In questo caso, usare il comando <strong>sudo</strong> per eseguire npm a un livello di privilegi pi&ugrave; elevato.</p>
</div>

    L'output di questo comando dovrebbe apparire simile al seguente:

        express@2.5.9 /usr/local/lib/node_modules/express
        ├── mime@1.2.4
        ├── mkdirp@0.3.0
        ├── qs@0.4.2
        └── connect@1.8.7

    <div class="dev-callout">
<strong>Nota</strong>
<p>Se si usa il parametro '-g' durante l'installazione del modulo Express, questo viene installato a livello globale, affinch&eacute; sia possibile accedere al comando <strong>express</strong> per generare lo scaffolding del sito Web senza dover generare informazioni aggiuntive sul percorso.</p>
</div>

3.  Per creare lo scaffolding che verrà usato per questa applicazione, usare il comando **express**:

        express

    L'output di questo comando dovrebbe apparire simile al seguente:

        create : .
        create : ./package.json
        create : ./app.js
        create : ./public
        create : ./public/javascripts
        create : ./public/images
        create : ./public/stylesheets
        create : ./public/stylesheets/style.css
        create : ./routes
        create : ./routes/index.js
        create : ./views
        create : ./views/layout.jade
        create : ./views/index.jade

        dont forget to install dependencies:
        $ cd . && npm install

    Dopo il completamento di questo comando, nella directory **tasklist** saranno disponibili diverse nuove directory e file.

### Installare moduli aggiuntivi

1.  Dalla riga di comando passare alla cartella **tasklist** e immettere quanto segue per installare i moduli descritti nel file **package.json**:

        npm install

    L'output di questo comando dovrebbe apparire simile al seguente:

        express@2.5.8 ./node_modules/express
        ├── mime@1.2.4
        ├── qs@0.4.2
        ├── mkdirp@0.3.0
        └── connect@1.8.7
        jade@0.26.0 ./node_modules/jade
        ├── commander@0.5.2
        └── mkdirp@0.3.0

    Verranno installati tutti i moduli predefiniti necessari per Express.

2.  Usare quindi il comando seguente per aggiungere il modulo nconf che verrà usato dall'applicazione per leggere la stringa di connessione al database da un file di configurazione.

    npm install nconf -save

3.  Successivamente, scaricare la versione binaria del driver Microsoft per Node.JS per SQL Server dall'[Area download][Area download].

4.  Estrarre l'archivio nella directory **tasklist\\node\_modules**.

5.  Eseguire il file **msnodesql-install.cmd** nella directory **tasklist\\node\_modules**. Verrà creata una sottodirectory **msnodesql** sotto **node\_modules** e i file dei driver verranno spostati in questa nuova struttura di directory.

6.  Eliminare il file **msnodesql-install.cmd**, in quanto non più necessario.

## Utilizzo del database SQL in un'applicazione node

In questa sezione si estenderà l'applicazione di base creata dal comando **express** modificando il file **app.js** e si creerà un nuovo file **index.js** per usare il database creato in precedenza.

### Modificare il controller

1.  Nella directory **tasklist/routes** aprire il file **index.js** in un editor di testo.

2.  Sostituire il codice esistente nel file **index.js** con quello riportato di seguito. Verranno caricati i moduli msnodesql e nconf e quindi verrà usato nconf per caricare la stringa di connessione da una variabile di ambiente denominata **SQL\_CONN** o da un valore **SQL\_CONN** nel file **config.json**.

        var sql = require('msnodesql')
            , nconf = require('nconf');

        nconf.env()
             .file({ file: 'config.json' });
        var conn = nconf.get("SQL_CONN");

3.  Per continuare, aggiungere i metodi **index** e **updateItem** al file **index.js**. Il metodo **index** restituirà tutte le attività non completate dal database, mentre **updateItem** contrassegnerà le attività selezionate come completate.

        exports.index = function(req, res) {
            var select = "select * from tasks where completed = 0";
            sql.query(conn, select, function(err, items) {
                if(err)
                    throw err;
                res.render('index', { title: 'My ToDo List ', tasks: items });
            });
        };

        exports.updateItem = function(req, res) {
            var item = req.body.item;
            if(item) {
                var insert = "insert into tasks (name, category, created, completed) values (?, ?, GETDATE(), 0)";
                sql.query(conn, insert, [item.name, item.category], function(err) {
                    if(err)
                        throw err;
                    res.redirect('/');
                });
            } else {
                var completed = req.body.completed;
                if(!completed.forEach)
                    completed = [completed];
                var update = "update tasks set completed = 1 where id in (" + completed.join(",") + ")";
                sql.query(conn, update, function(err) {
                    if(err)
                        throw err;
                    res.redirect('/');
                });
            }
        }

4.  Salvare il file **index.js**.

### Modificare il file app.js

1.  Nella directory **tasklist** aprire il file **app.js** in un editor di testo. Questo file è stato creato in precedenza eseguendo il comando **express**.

2.  Nel file app.js scorrere verso il basso fino a individuare il codice seguente.

        app.configure('development', function(){
        app.use(express.errorHandler());
        });

3.  Inserire ora il codice seguente.

        app.get('/', routes.index);
        app.post('/', routes.updateItem);

   Verrà aggiunta una nuova route al metodo **updateItem** aggiunto in precedenza nel file **index.js**.

1.  Salvare il file **app.js**.

### Modificare la visualizzazione dell'indice

1.  Passare alla directory **views** e aprire il file **index.jade** in un editor di testo.

2.  Sostituire il contenuto del file **index.jade** con il codice seguente. Verrà definita la visualizzazione delle attività esistenti, nonché un formato per aggiungere nuove attività e contrassegnare quelle esistenti come completate.

        h1= title
        br

        form(action="/", method="post")
          table(class="table table-striped table-bordered")
            thead
              tr
                td Name
                td Category
                td Date
                td Complete
            tbody
            each task in tasks
              tr
                td #{task.name}
                td #{task.category}
                td #{task.created}
                td
                  input(type="checkbox", name="completed", value="#{task.ID}", checked=task.completed == 1)
          button(type="submit", class="btn") Update tasks
        hr

        form(action="/", method="post", class="well")
          label Item Name:
          input(name="item[name]", type="textbox")
          label Item Category:
          input(name="item[category]", type="textbox")
          br
          button(type="submit", class="btn") Add Item

3.  Salvare e chiudere il file **index.jade**.

### Modificare il layout globale

Il file **layout.jade** della directory **views** viene usato come modello globale per altri file **.jade**. In questo passaggio verrà modificato in modo da usare [Twitter Bootstrap][Twitter Bootstrap], un toolkit che semplifica la progettazione di un sito Web di aspetto gradevole.

1.  Scaricare ed estrarre i file per [Twitter Bootstrap][1]. Copiare il file **bootstrap.min.css** dalla cartella **bootstrap\\css** alla directory **public\\stylesheets** dell'applicazione tasklist.

2.  Dalla cartella **views** aprire **layout.jade** nell'editor di testo e sostituire il contenuto con quello seguente:

        !!!html
        html
          head
            title= title
            meta(http-equiv='X-UA-Compatible', content='IE=10')
            link(rel='stylesheet', href='/stylesheets/style.css')
            link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
          body(class='app')
            div(class='navbar navbar-fixed-top')
              .navbar-inner
                .container
                  a(class='brand', href='/') My Tasks
            .container!= body

3.  Salvare il file **layout.jade**.

### Creare il file di configurazione

Il file **config.json** contiene la stringa di connessione utilizzata per la connessione al database SQL e viene letto dal file **index.js** in fase di esecuzione. Per creare questo file, eseguire la procedura seguente:

1.  Nella directory **tasklist** creare un nuovo file denominato **config.json** e aprirlo in un editor di testo.

2.  Il contenuto del file **config.json** risulterà simile al seguente:

        {
          "SQL_CONN" : "connection_string"
        }

    Sostituire **stringa\_connessione** con il valore della stringa di connessione ODBC restituito in precedenza.

3.  Salvare il file.

## Esecuzione dell'applicazione in locale

Per eseguire il test dell'applicazione nel computer locale, eseguire la procedura seguente:

1.  Dalla riga di comando passare alla directory **tasklist**.

2.  Usare il comando seguente per avviare l'applicazione in locale:

        node app.js

3.  Aprire il Web browser e passare all'indirizzo http://127.0.0.1:3000. Verrà visualizzata una pagina Web simile alla seguente:

    ![Pagina Web con un elenco di attività vuoto][2]

4.  Usare i campi **Item Name** e **Item Category** per l'immissione delle informazioni, quindi fare clic su **Add item**.

5.  La pagina verrà aggiornata e l'elemento verrà visualizzato nell'elenco attività.

    ![Immagine del nuovo elemento nell'elenco delle attività][Immagine del nuovo elemento nell'elenco delle attività]

6.  Per completare un'attività, è sufficiente selezionare la casella di controllo nella colonna Complete e quindi fare clic su **Update tasks**.

7.  Per interrompere il processo di Node, passare alla riga di comando e premere **CTRL**+**C**.

## Distribuire l'applicazione in Azure

In questa sezione si userà la procedura di distribuzione ricevuta in seguito alla creazione del sito Web per pubblicare l'applicazione in Azure.

### Pubblicare l'applicazione

1.  Alla riga di comando passare alla directory **tasklist** se non è già stato fatto.

2.  Usare i comandi seguenti per inizializzare un repository Git per l'applicazione, aggiungere i file dell'applicazione e infine inserire i file in Azure.

        git init
        git add .
        git commit -m "adding files"
        git remote add azure [URL for remote repository]
        git push azure master

    Al termine della distribuzione, verrà visualizzata un'istruzione simile alla seguente:

        To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
         * [new branch]      master -> master

3.  Al termine dell'operazione di push, passare a **http://[site name].azurewebsites.net/** per visualizzare l'applicazione.

### Passare a una variabile di ambiente

In precedenza è stato implementato codice che cerca una variabile di ambiente **SQL\_CONN** per la stringa di connessione o carica il valore dal file **config.json**. Nella procedura seguente verrà creata una coppia chiave-valore nella configurazione del sito Web a cui l'applicazione accede effettivamente tramite una variabile di ambiente.

1.  Nel portale di gestione di Azure fare clic su **Siti Web**, quindi selezionare il sito Web.

    ![Apertura del dashboard del sito Web][Apertura del dashboard del sito Web]

2.  Fare clic su **CONFIGURA**, quindi individuare la sezione **impostazioni app** della pagina.

    ![Collegamento Configura][Collegamento Configura]

3.  Nella sezione **app settings** immettere **SQL\_CONN** nel campo **KEY** e la stringa di connessione ODBC nel campo **VALUE**. Fare infine clic sul segno di spunta.

    ![impostazioni app][impostazioni app]

4.  Fare clic sull'icona **SALVA** nella parte inferiore della pagina per eseguire il commit della modifica nell'ambiente di runtime.

    ![Salvataggio impostazioni app][Salvataggio impostazioni app]

5.  Dalla riga di comando passare alla directory **tasklist** e immettere il comando seguente per rimuovere il file **config.json**:

        git rm config.json
        git commit -m "Removing config file"

6.  Per distribuire le modifiche in Azure, eseguire il comando seguente:

        git push azure master

Dopo la distribuzione delle modifiche in Azure, l'applicazione Web continuerà a funzionare dal momento che legge la stringa di connessione dalla voce **impostazioni app** Per verificarlo, modificare il valore della voce **SQL\_CONN** in **app settings** specificando un valore non valido. Dopo aver salvato questo valore, si verificherà un errore nel sito Web a causa della stringa di connessione non valida.

## Passaggi successivi

-   [Creazione di un'applicazione Web Node.js con MongoDB][Creazione di un'applicazione Web Node.js con MongoDB]

-   [Creazione di un'applicazione Web Node.js con Archiviazione tabelle]

## Risorse aggiuntive

[Strumento da riga di comando di Azure per Mac e Linux]
[Creare e distribuire un'applicazione Node.js in Siti Web di Azure]: /it-it/develop/nodejs/tutorials/create-a-website-(mac)/
[Centro per sviluppatori di Azure]: /it-it/develop/nodejs/
[Creazione di un'applicazione Web Node.js con Archiviazione tabelle]: /it-it/develop/nodejs/tutorials/web-site-with-storage/

  [node]: http://nodejs.org
  [Git]: http://git-scm.com
  [Pagina Web con un elenco di attività vuoto]: ./media/sql-database-nodejs-use-web-site/sql_todo_final.png
  [Feature Pack di Microsoft SQL Server 2012]: http://www.microsoft.com/it-it/download/details.aspx?id=29065
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [Creazione di un nuovo sito Web di Azure]: ./media/sql-database-nodejs-use-web-site/new_website.jpg
  [Creazione personalizzata di un nuovo sito Web]: ./media/sql-database-nodejs-use-web-site/custom_create.png
  [Inserimento di dettagli sul sito Web]: ./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg
  [Compilazione delle impostazioni del database SQL]: ./media/sql-database-nodejs-use-web-site/database_settings.jpg
  [Creazione del nuovo server di database SQL]: ./media/sql-database-nodejs-use-web-site/create_server.jpg
  [Apertura del dashboard del sito Web]: ./media/sql-database-nodejs-use-web-site/go_to_dashboard.png
  [Configurazione della pubblicazione Git]: ./media/sql-database-nodejs-use-web-site/setup_git_publishing.png
  [Creazione di credenziali di pubblicazione]: ./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png
  [Istruzioni su Git]: ./media/sql-database-nodejs-use-web-site/git-instructions.png
  [Linked Resources]: ./media/sql-database-nodejs-use-web-site/linked_resources.jpg
  [Stringa di connessione]: ./media/sql-database-nodejs-use-web-site/connection_string.jpg
  [Pulsante Manage]: ./media/sql-database-nodejs-use-web-site/sql-manage.png
  [Accesso per la gestione del database]: ./media/sql-database-nodejs-use-web-site/sqlazurelogin.png
  [Nuova tabella]: ./media/sql-database-nodejs-use-web-site/new-table.png
  [Progettazione della tabella completata]: ./media/sql-database-nodejs-use-web-site/table-columns.png
  [express]: http://expressjs.com
  [node-sqlserver]: https://github.com/WindowsAzure/node-sqlserver
  [Area download]: http://www.microsoft.com/it-it/download/details.aspx?id=29995
  [Twitter Bootstrap]: https://github.com/twbs/bootstrap
  [1]: http://getbootstrap.com/
  [2]: ./media/sql-database-nodejs-use-web-site/sql_todo_empty.png
  [Immagine del nuovo elemento nell'elenco delle attività]: ./media/sql-database-nodejs-use-web-site/sql_todo_list.png
  [Collegamento Configura]: ./media/sql-database-nodejs-use-web-site/sql-task-configure.png
  [impostazioni app]: ./media/sql-database-nodejs-use-web-site/appsettings.png
  [Salvataggio impostazioni app]: ./media/sql-database-nodejs-use-web-site/savebutton.png
  [Creazione di un'applicazione Web Node.js con MongoDB]: ../store-mongolab-web-sites-nodejs-store-data-mongodb/
  [Strumento da riga di comando di Azure per Mac e Linux]: /it-it/develop/nodejs/how-to-guides/command-line-tools/
