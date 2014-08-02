<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="Web site with SQL Database" pageTitle="Node.js web site with SQL Database - Azure tutorial" metaKeywords="" description="Learn how to create a Node.js website that accesses a SQL Database and is deployed to Azure" metaCanonical="" services="web-sites,sql-database" documentationCenter="Node.js" title="Node.js Web Application using the Azure SQL Database" authors="" solutions="" manager="" editor="" />

Creazione dell'applicazione Web Node.js con il database SQL di Azure
====================================================================

In questa esercitazione viene illustrato come utilizzare il database SQL fornito da Gestione dati di Azure per archiviare e accedere ai dati da un'applicazione [node](http://nodejs.org) ospitata in Azure. In questa esercitazione si presuppone che l'utente abbia già utilizzato l'applicazione Node e [Git](http://git-scm.com).

Si apprenderà come:

-   Utilizzare il portale di gestione di Azure per creare un sito Web e il database SQL di Azure

-   Utilizzare npm (Node Package Manager) per installare i moduli di Node

-   Gestire un database SQL tramite il modulo node-sqlserver

-   Utilizzare app settings per specificare valori in fase di esecuzione per un'applicazione

In questa esercitazione verrà creata una semplice applicazione di gestione delle attività basata su Web che consente di creare, recuperare e completare le attività. Le attività sono archiviate nel database SQL.

I file di progetto per questa esercitazione verranno archiviati in una directory denominata **tasklist** e l'applicazione completata presenterà un aspetto simile al seguente:

![Pagina Web con un elenco di attività vuoto](./media/sql-database-nodejs-use-web-site/sql_todo_final.png)

**Nota**

Il driver Microsoft per Node.js per SQL Server utilizzato in questa esercitazione è attualmente disponibile come versione di anteprima e si basa su componenti di runtime disponibili solo nei sistemi operativi Microsoft Windows e Azure.

**Nota**

In questa esercitazione si fa riferimento alla cartella **tasklist**. Il percorso completo della cartella è omesso, in quanto la semantica dei percorsi varia da un sistema operativo all'altro. È consigliabile creare la cartella in un percorso di facile accesso nel file system locale, ad esempio **\~/node/tasklist** o **c:\\node\\tasklist**

**Nota**

Molte delle procedure illustrate di seguito prevedono l'utilizzo della riga di comando. Per tali procedure, utilizzare la riga di comando per il sistema operativo in uso, ad esempio **cmd.exe** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione Terminale.

Prerequisiti
------------

Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili i seguenti elementi:

-   [node](http://nodejs.org) 0.6.14 o versione successiva

-   [Git](http://git-scm.com)

-   Librerie di Microsoft SQL Server Native Client, disponibili nel [Feature Pack di Microsoft SQL Server 2012](http://www.microsoft.com/en-us/download/details.aspx?id=29065)

-   Un editor di testo

-   Un Web browser

Creazione di un sito Web con database
-------------------------------------

Per creare un sito Web di Azure e un database SQL, eseguire la procedura seguente:

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).
2.  Fare clic sull'icona **+ New** nella parte inferiore sinistra del portale.

    ![Creazione di un nuovo sito Web di Azure](./media/sql-database-nodejs-use-web-site/new_website.jpg)

3.  Fare clic su **WEB SITE** e quindi su **CUSTOM CREATE**.

    ![Creazione personalizzata di un nuovo sito Web](./media/sql-database-nodejs-use-web-site/custom_create.png)

    Immettere un valore per **URL**, scegliere **Create a New SQL Database** nell'elenco a discesa **DATABASE**, quindi selezionare il data center per il sito Web nell'elenco a discesa **REGION**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

    ![Inserimento di dettagli sul sito Web](./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg)

4.  Immettere un valore per il campo **NAME** relativo al database, selezionare l'opzione per **EDITION** [(WEB o BUSINESS)](http://msdn.microsoft.com/en-us/library/windowsazure/ee621788.aspx), selezionare il valore per **MAX SIZE** relativo al database, scegliere il valore per **COLLATION** e selezionare **NEW SQL Database Server**. Fare clic sulla freccia nella parte inferiore della finestra di dialogo.

    ![Compilazione delle impostazioni del database SQL](./media/sql-database-nodejs-use-web-site/database_settings.jpg)

5.  Immettere un nome amministratore e una password (e confermare la password), scegliere l'area in cui verrà creato il nuovo server di database SQL e selezionare la casella **Allow Azure Services to access the server**.

    ![Creazione del nuovo server di database SQL](./media/sql-database-nodejs-use-web-site/create_server.jpg)

    In seguito alla creazione del sito Web verrà visualizzato il messaggio **Creation of Web Site '[NOMESITO]' completed successfully**. A questo punto, è possibile abilitare la pubblicazione Git.

6.  Fare clic sul nome del sito Web visualizzato nell'elenco dei siti per aprire il dashboard Quick Start.

    ![Apertura del dashboard del sito Web](./media/sql-database-nodejs-use-web-site/go_to_dashboard.png)

7.  Nella parte inferiore della pagina Quick Start fare clic su **Set up Git publishing**.

    ![Configurazione della pubblicazione Git](./media/sql-database-nodejs-use-web-site/setup_git_publishing.png)

8.  Per abilitare la pubblicazione Git, è necessario specificare un nome utente e una password. Prendere nota del nome utente e della password creati. Se è stato configurato un archivio Git in precedenza, ignorare questo passaggio.

    ![Creazione di credenziali di pubblicazione](./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png)

    La configurazione dell'archivio richiederà alcuni secondi.

9.  Quando l'archivio è pronto, verranno visualizzate le istruzioni per effettuare il push dei file dell'applicazione nell'archivio. Prendere nota di queste istruzioni, che saranno necessarie in seguito.

    ![Istruzioni su Git](./media/sql-database-nodejs-use-web-site/git-instructions.png)

recuperare le informazioni sulla connessione al database SQL
------------------------------------------------------------

Per connettersi all'istanza del database SQL in esecuzione in Siti Web di Azure è necessario disporre delle apposite informazioni. Per recuperare le informazioni sulla connessione al database SQL, eseguire la procedura seguente:

1.  Nel portale di gestione di Azure fare clic su **LINKED RESOURCES** e quindi sul nome del database.

    ![Linked Resources](./media/sql-database-nodejs-use-web-site/linked_resources.jpg)

2.  Fare clic su **View connection strings**.

    ![Stringa di connessione](./media/sql-database-nodejs-use-web-site/connection_string.jpg)

3.  Nella sezione **ODBC** della finestra risultante, prendere nota della stringa di connessione in quanto verrà utilizzata più tardi.

Progettazione della tabella delle attività
------------------------------------------

Per creare la tabella di database utilizzata per archiviare gli elementi per l'applicazione tasklist, eseguire le operazioni seguenti:

1.  Dal portale di gestione di Azure selezionare il database SQL e fare clic su **MANAGE** nella parte inferiore della pagina. Se viene visualizzato un messaggio in cui si informa che l'IP corrente non è incluso nelle regole firewall, scegliere **OK** per aggiungerlo.

    ![Pulsante Manage](./media/sql-database-nodejs-use-web-site/sql-manage.png)

2.  Effettuare l'accesso con il nome e la password selezionati in precedenza durante la creazione del server database.

    ![Accesso per la gestione del database](./media/sql-database-nodejs-use-web-site/sqlazurelogin.png)

3.  Nella parte inferiore sinistra della pagina selezionare **Progettazione** e quindi **Nuova tabella**.

    ![Nuova tabella](./media/sql-database-nodejs-use-web-site/new-table.png)

4.  Immettere 'tasks' in **Nome tabella** e selezionare **Identità?** per la colonna **ID**.

    ![Nome di tabella impostato su tasks e opzione Identità? selezionata](./media/sql-database-nodejs-use-web-site/table-name-identity.png)

5.  Modificare **Colonna1** su **name** e **Colonna2** su **category**. Aggiungere due nuove colonne facendo clic sul pulsante **Aggiungi colonna**. Assegnare alla prima nuova colonna il nome **created** e il tipo **date**. Assegnare alla seconda nuova colonna il nome **completed** e il tipo **bit**. Entrambe le nuove colonne devono essere contrassegnate come **Richiesto?**.

    ![Progettazione della tabella completata](./media/sql-database-nodejs-use-web-site/table-columns.png)

6.  Fare clic sul pulsante **Salva** per salvare le modifiche apportate alla tabella. È ora possibile chiudere la pagina di gestione del database SQL.

Installazione dei moduli e generazione dello scaffolding
--------------------------------------------------------

In questa sezione verrà creata una nuova applicazione Node e verrà utilizzato npm per aggiungere pacchetti di modulo. Per l'applicazione tasklist verranno utilizzati i moduli [express](http://expressjs.com) e [node-sqlserver](https://github.com/WindowsAzure/node-sqlserver). Il modulo express fornisce un framework View Controller per node, mentre node-sqlserver fornisce la connettività al database SQL di Azure.

### Installare Express e generare lo scaffolding

1.  Dalla riga di comando passare alla directory **tasklist**. Se la directory **tasklist** non esiste, crearla.

2.  Immettere il comando seguente per installare Express.

         npm install express -g

    **Nota**

    Quando si utilizza il parametro '-g' in alcuni sistemi operativi, è possibile che venga restituito l'errore analogo a: **Error: EPERM, chmod '/usr/local/bin/express'** con la richiesta di provare ad eseguire l'account come amministratore. In questo caso, utilizzare il comando **sudo** per eseguire npm a un livello di privilegi più elevato.

    L'output di questo comando dovrebbe apparire simile al seguente:

         express@2.5.9 /usr/local/lib/node_modules/express
		├── mime@1.2.4
		├── mkdirp@0.3.0
		├── qs@0.4.2
		└── connect@1.8.7

    **Nota**

    Se si utilizza il parametro '-g' durante l'installazione del modulo Express, questo viene installato a livello globale, affinché sia possibile accedere al comando **express** per generare lo scaffolding del sito Web senza dover generare informazioni aggiuntive sul percorso.

3.  Per creare lo scaffolding che verrà utilizzato per questa applicazione, utilizzare il comando **express**:

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

    Verranno installati tutti i moduli predefiniti necessari per express.

2.  Utilizzare quindi il comando seguente per aggiungere il modulo nconf che verrà utilizzato dall'applicazione per leggere la stringa di connessione al database da un file di configurazione.

    npm install nconf -save

3.  Successivamente, scaricare la versione binaria del driver Microsoft per Node.JS per SQL Server dall'[Area download](http://www.microsoft.com/en-us/download/details.aspx?id=29995).

4.  Estrarre l'archivio nella directory **tasklist\\node\_modules**.

5.  Eseguire il file **msnodesql-install.cmd** nella directory **tasklist\\node\_modules**. Verrà creata una sottodirectory **msnodesql** sotto **node\_modules** e i file dei driver verranno spostati in questa nuova struttura di directory.

6.  Eliminare il file **msnodesql-install.cmd**, in quanto non più necessario.

Utilizzo del database SQL in un'applicazione node
-------------------------------------------------

In questa sezione si estenderà l'applicazione di base creata dal comando **express** modificando il file **app.js** e si creerà un nuovo file **index.js** per utilizzare il database creato in precedenza.

### Modificare il controller

1.  Nella directory **tasklist/routes** aprire il file **index.js** in un editor di testo.

2.  Sostituire il codice esistente nel file **index.js** con quello riportato di seguito. Verranno caricati i moduli msnodesql e nconf e quindi verrà utilizzato nconf per caricare la stringa di connessione da una variabile di ambiente denominata **SQL\_CONN** o da un valore **SQL\_CONN** nel file **config.json**.

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
                 var insert = "insert into tasks (name, category, created, completed) values (
        , 
        , GETDATE(), 0)";
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

Il file **layout.jade** della directory **views** viene utilizzato come modello globale per altri file **.jade**. In questo passaggio verrà modificato in modo da utilizzare [Twitter Bootstrap](https://github.com/twbs/bootstrap), un toolkit che semplifica la progettazione di un sito Web di aspetto gradevole.

1.  Scaricare ed estrarre i file per [Twitter Bootstrap](http://getbootstrap.com/). Copiare il file **bootstrap.min.css** dalla cartella **bootstrap\\css** alla directory **public\\stylesheets** dell'applicazione tasklist.

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
           "SQL_CONN" : "stringa_connessione"
         }

    Sostituire **stringa\_connessione** con il valore della stringa di connessione ODBC restituito in precedenza.

3.  Salvare il file.

Esecuzione dell'applicazione in locale
--------------------------------------

Per eseguire il test dell'applicazione nel computer locale, eseguire la procedura seguente:

1.  Dalla riga di comando passare alla directory **tasklist**.

2.  Utilizzare il comando seguente per avviare l'applicazione in locale:

         node app.js

3.  Aprire un Web browser e passare a http://127.0.0.1:3000. La pagina Web visualizzata dovrebbe essere simile alla seguente:

    ![Pagina Web con un elenco di attività vuoto](./media/sql-database-nodejs-use-web-site/sql_todo_empty.png)

4.  Utilizzare i campi **Item Name** e **Item Category** per l'immissione delle informazioni, quindi fare clic su **Add item**.

5.  La pagina verrà aggiornata e l'elemento verrà visualizzato nell'elenco attività.

    ![Immagine del nuovo elemento nell'elenco delle attività](./media/sql-database-nodejs-use-web-site/sql_todo_list.png)

6.  Per completare un'attività, è sufficiente selezionare la casella di controllo nella colonna Complete e quindi fare clic su **Update tasks**.

7.  Per interrompere il processo di Node, passare alla riga di comando e premere **CTRL**+**C**.

Distribuzione dell'applicazione in Azure
----------------------------------------

In questa sezione si utilizzerà la procedura di distribuzione ricevuta in seguito alla creazione del sito Web per pubblicare l'applicazione in Azure.

### Pubblicare l'applicazione

1.  Alla riga di comando passare alla directory **tasklist** se non è già stato fatto.

2.  Utilizzare i comandi seguenti per inizializzare un archivio git per l'applicazione, aggiungere i file dell'applicazione e infine inserire i file in Azure.

         git init
         git add .
         git commit -m "adding files"
         git remote add azure [URL dell'archivio remoto]
         git push azure master

    Al termine della distribuzione, verrà visualizzata un'istruzione simile alla seguente:

         To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
	     \* [new branch] 	master -\> master

3.  Al termine dell'operazione di push, passare a **http://[nome sito].azurewebsites.net/** per visualizzare l'applicazione.

### Passare a una variabile di ambiente

In precedenza è stato implementato codice che cerca una variabile di ambiente **SQL\_CONN** per la stringa di connessione o carica il valore dal file **config.json**. Nella procedura seguente verrà creata una coppia chiave-valore nella configurazione del sito Web a cui l'applicazione accede effettivamente tramite una variabile di ambiente.

1.  Nel portale di gestione di Azure fare clic su **Web Sites** e quindi selezionare il sito Web.

    ![Apertura del dashboard del sito Web](./media/sql-database-nodejs-use-web-site/go_to_dashboard.png)

2.  Fare clic su **CONFIGURE**, quindi individuare la sezione **app settings** della pagina.

    ![Collegamento Configure](./media/sql-database-nodejs-use-web-site/sql-task-configure.png)

3.  Nella sezione **app settings** immettere **SQL\_CONN** nel campo **KEY** e la stringa di connessione ODBC nel campo **VALUE**. Fare infine clic sul segno di spunta.

    ![app settings](./media/sql-database-nodejs-use-web-site/appsettings.png)

4.  Fare clic sull'icona **SAVE** nella parte inferiore della pagina per eseguire il commit della modifica nell'ambiente di runtime.

    ![Opzione Save in app settings](./media/sql-database-nodejs-use-web-site/savebutton.png)

5.  Dalla riga di comando passare alla directory **tasklist** e immettere il comando seguente per rimuovere il file **config.json**:

         git rm config.json
         git commit -m "Removing config file"

6.  Per distribuire le modifiche in azure, eseguire il comando seguente:

         git push azure master

Dopo la distribuzione delle modifiche in Azure, l'applicazione Web continuerà a funzionare dal momento che legge la stringa di connessione dalla voce **app settings**. Per verificarlo, modificare il valore della voce **SQL\_CONN** in **app settings** specificando un valore non valido. Dopo aver salvato questo valore, si verificherà un errore nel sito Web a causa della stringa di connessione non valida.

Passaggi successivi
-------------------

-   [Creazione di un'applicazione Web Node.js con MongoDB](../store-mongolab-web-sites-nodejs-store-data-mongodb/)

-   [Creazione di un'applicazione Web Node.js con Archiviazione tabelle](/en-us/develop/nodejs/tutorials/web-site-with-storage/)

Risorse aggiuntive
------------------

[Strumento da riga di comando di Azure per Mac e Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/)
