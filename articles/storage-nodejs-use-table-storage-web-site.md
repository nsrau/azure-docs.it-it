<properties linkid="dev-nodejs-tutorials-web-site-with-storage" urlDisplayName="Web Site with Storage" pageTitle="Node.js web site with table storage | Microsoft Azure" metaKeywords="Azure table storage Node.js, Azure Node.js application, Azure Node.js tutorial, Azure Node.js example" description="A tutorial that teaches you how to use the Azure Table service to store data from a Node application hosted on an Azure web site." metaCanonical="" services="web-sites,storage" documentationCenter="Node.js" title="Node.js Web Application using the Azure Table Service" authors="" solutions="" manager="" editor="" />

Applicazione Web Node.js con il servizio tabelle di Azure
=========================================================

In questa esercitazione viene illustrato come utilizzare il servizio tabelle fornito da Gestione dati di Azure per archiviare e accedere ai dati da un'applicazione [Node](http://nodejs.org) ospitata in Azure. In questa esercitazione si presuppone che l'utente abbia già utilizzato l'applicazione Node e [Git](http://git-scm.com).

Si apprenderà come:

-   Utilizzare npm (Node Package Manager) per installare i moduli di Node

-   Utilizzare il servizio tabelle di Azure

-   Utilizzare lo strumento da riga di comando di Azure per Mac e Linux per creare un sito Web di Azure

In questa esercitazione verrà creata una semplice applicazione di gestione delle attività basata su Web che consente di creare, recuperare e completare le attività. Le attività vengono archiviate nel servizio tabelle.

I file di progetto per questa esercitazione verranno archiviati in una directory denominata **tasklist** e l'applicazione completata presenterà un aspetto simile al seguente:

![Pagina Web con un elenco di attività vuoto](./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png)

**Nota**

In questa esercitazione si fa riferimento alla cartella **tasklist**. Il percorso completo della cartella è omesso, in quanto la semantica dei percorsi varia da un sistema operativo all'altro. È consigliabile creare la cartella in un percorso di facile accesso nel file system locale, ad esempio **\~/node/tasklist** o **c:\\node\\tasklist**

**Nota**

Molte delle procedure illustrate di seguito prevedono l'utilizzo della riga di comando. Per tali procedure, utilizzare la riga di comando per il sistema operativo in uso, ad esempio **cmd.exe** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione Terminale.

Prerequisiti
------------

Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili i seguenti elementi:

-   [node](http://nodejs.org) 0.6.14 o versione successiva

-   [Git](http://git-scm.com)

-   Un editor di testo

-   Un Web browser

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Creazione di un account di archiviazione
----------------------------------------

Procedere come descritto di seguito per creare un account di archiviazione. Tale account verrà utilizzato in istruzioni successive nella presente esercitazione.

1.  Aprire il Web browser e passare al [portale di Azure](http://windowsazure.com). Se richiesto, accedere con le informazioni della sottoscrizione di Azure.

2.  Nella parte inferiore della pagina del portale, fare clic su **+ NEW**, quindi selezionare **Storage Account**.

    ![+new](./media/storage-nodejs-use-table-storage-web-site/plus-new.png)

    ![storage account](./media/storage-nodejs-use-table-storage-web-site/new-storage.png)

3.  Selezionare **Quick Create**, quindi immettere l'URL e la regione/gruppo di affinità per l'account di archiviazione. Poiché si sta eseguendo un'esercitazione e non è necessaria la replica globale, deselezionare l'opzione **Enable Geo-Replication**. Fare clic su "Create Storage Account".

    ![quick create](./media/storage-nodejs-use-table-storage-web-site/quick-storage.png)

    Prendere nota dell'URL immesso, poiché vi verrà fatto riferimento come nome dell'account nei passaggi successivi.

4.  Dopo la creazione dell'account di archiviazione, fare clic su **Manage Keys** nella parte inferiore della pagina. Vengono visualizzate le chiavi di accesso primaria e secondaria per l'account di archiviazione. Copiare e salvare la chiave di accesso primaria, quindi fare clic sul segno di spunta.

    ![chiavi di accesso](./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png)

Installazione dei moduli e generazione dello scaffolding
--------------------------------------------------------

In questa sezione verrà creata una nuova applicazione Node e verrà utilizzato npm per aggiungere pacchetti di modulo. Per l'applicazione tasklist verranno utilizzati i moduli [Express](http://expressjs.com) e [Azure](https://github.com/WindowsAzure/azure-sdk-for-node). Il modulo Express fornisce un modello di framework View Controller per Node, mentre i moduli Azure forniscono la connettività al servizio tabelle.

### Installare Express e generare lo scaffolding

1.  Dalla riga di comando passare alla directory **tasklist**. Se la directory **tasklist** non esiste, crearla.

2.  Immettere il comando seguente per installare Express.

         npm install express -g

    **Nota**

    Quando si utilizza il parametro '-g' in alcuni sistemi operativi, è possibile che venga restituito l'errore analogo a: **Error: EPERM, chmod '/usr/local/bin/express'** con la richiesta di provare ad eseguire l'account come amministratore. In questo caso, utilizzare il comando **sudo** per eseguire npm a un livello di privilegi più elevato.

    L'output di questo comando dovrebbe apparire simile al seguente:

		express@3.4.0 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
		├── methods@0.0.1
		├── fresh@0.2.0
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.9.0 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, multiparty@2.1.8)

    **Nota**

    Se si utilizza il parametro '-g' durante l'installazione del modulo Express, questo viene installato a livello globale, affinché sia possibile accedere al comando **express** per generare lo scaffolding del sito Web senza dover generare informazioni aggiuntive sul percorso.

3.  Per creare lo scaffolding che verrà utilizzato per questa applicazione, utilizzare il comando **express**:

         express

    L'output di questo comando dovrebbe apparire simile al seguente:

         create : .
         create : ./package.json
         create : ./app.js
         create : ./public/javascripts
         create : ./public
         create : ./public/stylesheets
         create : ./public/stylesheets/style.css
         create : ./views
         create : ./views/layout.jade
         create : ./views/index.jade
         create : ./routes
         create : ./routes/index.js
         create : ./routes/user.js
         create : ./public/images

         install dependencies:
           $ cd . && npm install

         run the app:
           $ node app

Dopo il completamento di questo comando, nella directory **tasklist** saranno disponibili diverse nuove directory e file.

### Installare moduli aggiuntivi

Il file **package.json** è uno di quelli creati dal comando **express**. Questo file contiene un elenco di moduli aggiuntivi necessari per un'applicazione Express. In seguito, quando si distribuirà questa applicazione in un sito Web di Azure, il file verrà utilizzato per determinare quali moduli devono essere installati in Azure per supportare l'applicazione.

1.  Dalla riga di comando passare alla cartella **tasklist** e immettere quanto segue per installare i moduli descritti nel file **package.json**:

         npm install

    L'output di questo comando dovrebbe apparire simile al seguente:

		express@3.4.0 node_modules\express
		├── methods@0.0.1
		├── range-parser@0.0.4
		├── cookie-signature@1.0.1
		├── fresh@0.2.0
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.4 (mime@1.2.11)
		└── connect@2.9.0 (uid2@0.0.2, pause@0.0.1, bytes@0.2.0, qs@0.6.5, multiparty@2.1.8)

		jade@0.35.0 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.0.0
		├── mkdirp@0.3.5
		├── monocle@1.1.50 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── with@1.1.1 (uglify-js@2.4.0)
		└── constantinople@1.0.2 (uglify-js@2.4.0)

	
    Verranno installati tutti i moduli predefiniti necessari per Express.

2.  Immettere quindi il comando seguente per installare i moduli [node-uuid], [nconf] e [async] di [azure](https://github.com/WindowsAzure/azure-sdk-for-node) e per salvare una voce per tali moduli nel file **package.json**:

         npm install azure node-uuid async nconf --save

    L'output di questo comando dovrebbe apparire simile al seguente:

		async@0.2.9 node_modules\async

		node-uuid@1.4.1 node_modules\node-uuid

		nconf@0.6.7 node_modules\nconf
		├── ini@1.1.0
		├── async@0.1.22
		├── pkginfo@0.2.3
		└── optimist@0.3.7 (wordwrap@0.0.2)

		azure@0.7.15 node_modules\azure
		├── dateformat@1.0.2-1.2.3
		├── xmlbuilder@0.4.2
		├── envconf@0.0.4
		├── node-uuid@1.2.0
		├── mpns@2.0.1
		├── underscore@1.5.2
		├── mime@1.2.11
		├── validator@1.5.1
		├── tunnel@0.0.2
		├── wns@0.5.3
		├── xml2js@0.2.8 (sax@0.5.5)
		└── request@2.25.0 (json-stringify-safe@5.0.0, aws-sign@0.3.0, forever-agent@0.5.0, tunnel-agent@0.3.0, qs@0.6.5, oauth-sign@0.3.0, cookie-jar@0.3.0, node-uuid@1.4.1, http-signature@0.10.0, form-data@0.1.1, hawk@1.0.0)

Utilizzo del servizio tabelle in un'applicazione Node
-----------------------------------------------------

In questa sezione si estenderà l'applicazione di base creata dal comando **express** aggiungendo un file **task.js** contenente il modello per le attività. Si modificherà inoltre il file **app.js** esistente e si creerà un nuovo file **tasklist.js** che utilizzi il modello.

### Creare il modello

1.  Nella directory **tasklist** creare una nuova directory denominata **models**.

2.  Nella directory **models** creare un nuovo file denominato **task.js**. Questo file conterrà il modello per le attività create dall'applicazione.

3.  All'inizio del file **task.js**, aggiungere il codice seguente per fare riferimento alle librerie necessarie:

         var azure = require('azure');

    var uuid = require('node-uuid');

4.  Quindi, aggiungere il codice per definire ed esportare l'oggetto Task. Tale oggetto è responsabile per la connessione alla tabella.

module.exports = Task;

     function Task(storageClient, tableName, partitionKey) {
          this.storageClient = storageClient;
          this.tableName = tableName;
          this.partitionKey = partitionKey;
          this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
            if(error) {
              throw error;
            }
          });
        };

1.  Aggiungere quindi il codice seguente per definire metodi aggiuntivi nell'oggetto Task che consentano l'interazione con i dati archiviati nella tabella:

         Task.prototype = {
           find: function(query, callback) {
             self = this;
             self.storageClient.queryEntities(query, function entitiesQueried(error, entities) {
               if(error) {
                 callback(error);
               } else {
                 callback(null, entities);
               }
             });
           },

           addItem: function(item, callback) {
             self = this;
             item.RowKey = uuid();
             item.PartitionKey = self.partitionKey;
             item.completed = false;
             self.storageClient.insertEntity(self.tableName, item, function entityInserted(error) {
               if(error){  
                 callback(error);
               }
               callback(null);
             });
           },

           updateItem: function(item, callback) {
             self = this;
             self.storageClient.queryEntity(self.tableName, self.partitionKey, item, function entityQueried(error, entity) {
               if(error) {
                 callback(error);
               }
               entity.completed = true;
               self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
                 if(error) {
                   callback(error);
                 }
                 callback(null);
               });
             });
           }
         }

2.  Salvare e chiudere il file **task.js**.

### Creare il controller

1.  Nella directory **tasklist/routes** creare un nuovo file denominato **tasklist.js** e aprirlo in un editor di testo.

2.  Aggiungere il codice seguente al file **tasklist.js**. Ciò consente il caricamento dei moduli di Azure e di quelli asincroni, utilizzati da **tasklist.js**. Viene inoltre definita la funzione **TaskList**, che viene passata come un'istanza dell'oggetto **Task** definito in precedenza:

         var azure = require('azure');
         var async = require('async');

         module.exports = TaskList;

         function TaskList(task) {
           this.task = task;
         }

3.  Continuare ad aggiungere codice al file **tasklist.js** aggiungendo i metodi **showTasks**, **addTask** e **completeTasks**:

         TaskList.prototype = {
           showTasks: function(req, res) {
             self = this;
             var query = azure.TableQuery
               .select()
               .from(self.task.tableName)
               .where('completed eq 
         ', false);
             self.task.find(query, function itemsFound(error, items) {
               res.render('index',{title: 'My ToDo List ', tasks: items});
              });
            },
         
            addTask: function(req,res) {
             var self = this      
             var item = req.body.item;
             self.task.addItem(item, function itemAdded(error) {
               if(error) {
                 throw error;
                }
                res.redirect('/');
              });
            },
         
            completeTask: function(req,res) {
             var self = this;
             var completedTasks = Object.keys(req.body);
             async.forEach(completedTasks, function taskIterator(completedTask, callback) {
               self.task.updateItem(completedTask, function itemsUpdated(error) {
                 if(error){
                   callback(error);
                 } else {
                   callback(null);
                  }
                });
              }, function goHome(error){
               if(error) {
                 throw error;
               } else {
                res.redirect('/');
                }
              });
            }
          }

4.  Salvare il file **tasklist.js**.

### Modificare il file app.js

1.  Nella directory **tasklist** aprire il file **app.js** in un editor di testo. Questo file è stato creato in precedenza eseguendo il comando **express**.

2.  Aggiungere quanto riportato di seguito all'inizio del file per caricare il modulo di Azure, impostare il nome della tabella, partitionKey, e impostare le credenziali di archiviazione utilizzate da questo esempio:

         var azure = require('azure');
         var nconf = require('nconf');
         nconf.env()
              .file({ file: 'config.json'});
         var tableName = nconf.get("TABLE_NAME")
         var partitionKey = nconf.get("PARTITION_KEY")
         var accountName = nconf.get("STORAGE_NAME")
         var accountKey = nconf.get("STORAGE_KEY");

    **Nota**

    nconf caricherà i valori di configurazione dalle variabili di ambiente oppure dal file **config.json**, che verrà creato più avanti.

3.  Nel file app.js scorrere verso il basso fino a individuare la riga seguente:

         app.get('/', routes.index);
         app.get('/users', user.list);

    Sostituire le righe sopra con il codice seguente. Ciò inizializzerà un'istanza di **Task** con una connessione all'account di archiviazione. Questa viene quindi passata a **TaskList**, che la utilizzerà per la comunicazione con il servizio tabelle:

         var TaskList = require('./routes/tasklist');
         var Task = require('./models/task');
         var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
         var taskList = new TaskList(task);

         app.get('/', taskList.showTasks.bind(taskList));
         app.post('/addtask', taskList.addTask.bind(taskList));
         app.post('/completetask', taskList.completeTask.bind(taskList));

4.  Salvare il file **app.js**.

### Modificare la visualizzazione dell'indice

1.  Passare alla directory **views** e aprire il file **index.jade** in un editor di testo.

2.  Sostituire il contenuto del file **index.jade** con il codice seguente. Verrà definita la visualizzazione delle attività esistenti, nonché un formato per aggiungere nuove attività e contrassegnare quelle esistenti come completate.

         extends layout

         block content
           h1= title
           br

           form(action="/completetask", method="post")
             table.table.table-striped.table-bordered
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
               each task in tasks
                 tr
                   td #{task.name}
                   td #{task.category}
                   - var day   = task.Timestamp.getDate();
                   - var month = task.Timestamp.getMonth() + 1;
                   - var year  = task.Timestamp.getFullYear();
                   td #{month + "/" + day + "/" + year}
                   td
                     input(type="checkbox", name="#{task.RowKey}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
             button.btn(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             label Item Name: 
             input(name="item[name]", type="textbox")
             label Item Category: 
             input(name="item[category]", type="textbox")
             br
             button.btn(type="submit") Add item

3.  Salvare e chiudere il file **index.jade**.

### Modificare il layout globale

Il file **layout.jade** della directory **views** viene utilizzato come modello globale per altri file **.jade**. In questo passaggio verrà modificato in modo da utilizzare [Twitter Bootstrap](https://github.com/twbs/bootstrap), un toolkit che semplifica la progettazione di un sito Web di aspetto gradevole.

1.  Scaricare ed estrarre i file per [Twitter Bootstrap](http://getbootstrap.com/). Copiare il file **bootstrap.min.css** dalla cartella **bootstrap\\dist\\css** alla directory **public\\stylesheets** dell'applicazione tasklist.

2.  Dalla cartella **views** aprire **layout.jade** nell'editor di testo e sostituire il contenuto con quello seguente:

         doctype 5
         html
           head
             title= title
             link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
             link(rel='stylesheet', href='/stylesheets/style.css')
           body.app
             nav.navbar.navbar-default
               div.navbar-header
                 a.navbar-brand(href='/') My Tasks
             block content

3.  Salvare il file **layout.jade**.

### Creare il file di configurazione

Il file **config.json** contiene la stringa di connessione utilizzata per la connessione al database SQL e viene letto dall'applicazione in fase di esecuzione. Per creare questo file, eseguire la procedura seguente:

1.  Nella directory **tasklist** creare un nuovo file denominato **config.json** e aprirlo in un editor di testo.

2.  Il contenuto del file **config.json** risulterà simile al seguente:

         {
             "STORAGE_NAME": "storage account name",
             "STORAGE_KEY": "storage access key",
             "PARTITION_KEY": "mytasks",
             "TABLE_NAME": "tasks"
         }

    Sostituire **storage account name** con il nome dell'account di archiviazione creato in precedenza. Sostituire **storage access key** con la chiave di accesso primaria per l'account di archiviazione.

3.  Salvare il file.

Esecuzione dell'applicazione in locale
--------------------------------------

Per eseguire il test dell'applicazione nel computer locale, eseguire la procedura seguente:

1.  Dalla riga di comando passare alla directory **tasklist**.

2.  Utilizzare il comando seguente per avviare l'applicazione in locale:

         node app.js

3.  Aprire un Web browser e passare a http://127.0.0.1:3000. La pagina Web visualizzata dovrebbe essere simile alla seguente:

    ![Pagina Web con un elenco di attività vuoto](./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png)

4.  Utilizzare i campi **Item Name** e **Item Category** per l'immissione delle informazioni, quindi fare clic su **Add item**.

5.  La pagina verrà aggiornata e l'elemento verrà visualizzato nella tabella dell'elenco attività.

    ![Immagine del nuovo elemento nell'elenco delle attività](./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png)

6.  Per completare un'attività, è sufficiente selezionare la casella di controllo nella colonna Complete e quindi fare clic su **Update tasks**.

7.  Per interrompere il processo di Node, passare alla riga di comando e premere **CTRL**+**C**.

Distribuzione dell'applicazione in Azure
----------------------------------------

Nei passaggi di questa sezione vengono utilizzati gli strumenti da riga di comando di Azure per creare un nuovo sito Web di Azure e quindi viene utilizzato Git per distribuire l'applicazione. Per eseguire questi passaggi, è necessario disporre di una sottoscrizione di Azure.

**Nota**

È possibile eseguire queste procedure anche nel portale di Azure. Per ulteriori informazioni sulla distribuzione di un'applicazione Node.js tramite il portale di Azure, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure](http://content-ppe.windowsazure.com/en-us/develop/nodejs/tutorials/create-a-website-(mac)/).

**Nota**

Se questo è il primo sito Web di Azure che si crea, per distribuire l'applicazione è necessario utilizzare il portale di Azure.

### Abilitare la funzionalità Siti Web di Azure

Se non si dispone già di una sottoscrizione di Azure, è possibile iscriversi [gratuitamente](http://windowsazure.com). Dopo l'iscrizione, eseguire la procedura seguente per abilitare la funzionalità Siti Web di Azure.

[WACOM.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

### Installare lo strumento da riga di comando di Azure per Mac e Linux

Per installare gli strumenti da riga di comando, utilizzare il comando seguente:

    npm install azure-cli -g

**Nota**

Se si è già installato **Azure SDK per Node.js** dal [Azure Developer Center](/en-us/develop/nodejs/), gli strumenti da riga di comando saranno già installati. Per ulteriori informazioni, vedere [Strumenti da riga di comando di Azure per Mac e Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/).

**Nota**

Anche se gli strumenti da riga di comando sono stati creati principalmente per gli utenti Mac e Linux, essendo basati su Node.js, dovrebbero funzionare in qualsiasi sistema in grado di eseguire Node.

### Importare le impostazioni di pubblicazione

Prima di utilizzare gli strumenti da riga di comando con Azure, è necessario scaricare un file contenente informazioni sulla sottoscrizione. Eseguire i passaggi seguenti per scaricare e importare questo file.

1.  Dalla riga di comando passare alla directory **tasklist**.

2.  Digitare il comando seguente per avviare il browser e passare alla pagina di download. Se richiesto, accedere con l'account associato alla sottoscrizione.

         azure account download

    ![Pagina di download](./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png)

    Il download del file dovrebbe iniziare automaticamente. In caso contrario, è possibile fare clic sul collegamento all'inizio della pagina per scaricare manualmente il file.

3.  Al termine del download del file, utilizzare il comando seguente per importare le impostazioni:

         azure account import <path-to-file>

    Specificare il percorso e il nome del file delle impostazioni di pubblicazione scaricato nel passaggio precedente. Dopo il completamento del comando, l'output dovrebbe essere simile al seguente:

         info:   Executing command account import
         info:   Setting service endpoint to: management.core.windows.net
         info:   Setting service port to: 443
         info:   Found subscription: YourSubscription
         info:   Setting default subscription to: YourSubscription
         warn:   The 'C:\users\username\downloads\YourSubscription-6-7-2012-credentials.publishsettings' file contains sensitive information.
         warn:   Remember to delete it now that it has been imported.
         info:   Account publish settings imported successfully
         info:   account import command OK

4.  Al termine dell'importazione, è consigliabile eliminare il file delle impostazioni di pubblicazione perché non è più necessario e contiene informazioni riservate relative alla sottoscrizione di Azure.

### Creare un sito Web di Azure

1.  Dalla riga di comando passare alla directory **tasklist**.

2.  Utilizzare il comando seguente per creare un nuovo sito Web di Azure.

         azure site create --git

    Verrà richiesto di specificare il nome del sito Web e il data center in cui si troverà il sito. Fornire un nome univoco e selezionare un data center geograficamente vicino alla propria posizione.

    Il parametro `--git` creerà un archivio Git per il sito Web in Azure. Verrà inoltre inizializzato un archivio Git nella directory corrente, se non ne esiste già uno. Creerà anche un [archivio Git remoto](http://git-scm.com/docs/git-remote) denominato 'azure' che verrà utilizzato per pubblicare l'applicazione in Azure, oltre a un file **web.config** contenente le impostazioni utilizzate da Azure per ospitare le applicazioni Node.

    **Nota**

    Se questo comando viene eseguito da una directory che contiene già un archivio Git, la directory non verrà reinizializzata.

    **Nota**

    Se il parametro "--git" viene omesso ma la directory contiene un archivio Git, l'archivio "azure" verrà comunque creato.

    Dopo il completamento di questo comando, verrà visualizzato un output simile al seguente. Si noti che la riga che inizia con **Web site created at** contiene l'URL del sito Web.

         info:   Executing command site create
         help:   Need a site name
         Name: TableTasklist
         info:   Using location southcentraluswebspace
         info:   Executing `git init`
         info:   Creating default .gitignore file
         info:   Creating a new web site
         info:   Created web site at  tabletasklist.azurewebsites.net
         info:   Initializing repository
         info:   Repository initialized
         info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
         info:   site create command OK

    **Nota**

    Se questo è il primo sito Web di Azure per la sottoscrizione, verrà indicato di utilizzare il portale per la creazione del sito Web. Per ulteriori informazioni, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/).

### Pubblicare l'applicazione

1.  Nella finestra del terminale passare alla directory **tasklist** se non è già stato fatto.

2.  Utilizzare i comandi seguenti per aggiungere e quindi confermare i file nell'archivio Git locale:

         git add .
         git commit -m "adding files"

3.  Durante il push delle ultime modifiche apportate all'archivio Git nel sito Web di Azure, è necessario specificare che il ramo di destinazione è **master** perché viene utilizzato per il contenuto del sito Web.

         git push azure master

    Al termine della distribuzione, verrà visualizzata un'istruzione simile alla seguente:

         To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
    	 * [new branch] master -\> master

4.  Al termine dell'operazione di push, passare all'URL del sito Web restituito in precedenza utilizzando il comando `azure create site` per visualizzare l'applicazione.

### Passare a una variabile di ambiente

In precedenza è stato implementato codice che cerca una variabile di ambiente **SQL\_CONN** per la stringa di connessione o carica il valore dal file **config.json**. Nella procedura seguente verrà creata una coppia chiave-valore nella configurazione del sito Web a cui l'applicazione accede effettivamente tramite una variabile di ambiente.

1.  Nel portale di gestione di Azure fare clic su **Web Sites**, quindi selezionare il sito Web.

    ![Apertura del dashboard del sito Web](./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png)

2.  Fare clic su **CONFIGURE**, quindi individuare la sezione **app settings** della pagina.

    ![Collegamento Configure](./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png)

3.  Nella sezione **app settings** immettere **STORAGE\_NAME** nel campo **KEY**, quindi il nome dell'account di archiviazione nel campo **VALUE**. Fare clic sul segno di spunta per passare al campo successivo. Ripetere il processo per le chiavi e i valori seguenti:

    -   **STORAGE\_KEY** - la chiave di accesso primaria per l'account di archiviazione.

    -   **PARTITION\_KEY** - "mytasks"

    -   **TABLE\_NAME** - "tasks"

    ![app settings](./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png)

4.  Fare clic sull'icona **SAVE** nella parte inferiore della pagina per eseguire il commit della modifica nell'ambiente di runtime.

    ![Opzione Save in app settings](./media/storage-nodejs-use-table-storage-web-site/savebutton.png)

5.  Dalla riga di comando passare alla directory **tasklist** e immettere il comando seguente per rimuovere il file **config.json**:

         git rm config.json
         git commit -m "Removing config file"

6.  Per distribuire le modifiche in azure, eseguire il comando seguente:

         git push azure master

Dopo la distribuzione delle modifiche in Azure, l'applicazione Web continuerà a funzionare dal momento che legge la stringa di connessione dalla voce **app settings**. Per verificarlo, modificare il valore della voce **STORAGE\_KEY** in **app settings** specificando un valore non valido. Dopo aver salvato questo valore, si verificherà un errore nel sito Web a causa dell'impostazione di una chiave di accesso all'archiviazione non valida.

Passaggi successivi
-------------------

Nei passaggi di questo articolo viene descritto come archiviare informazioni tramite il servizio tabelle. Tuttavia per tale attività è anche possibile utilizzare MongoDB. Per ulteriori informazioni, vedere l'articolo relativo all'[applicazione Web Node.js con MongoDB](/en-us/develop/nodejs/tutorials/website-with-mongodb-(Mac)/).

Risorse aggiuntive
------------------

[Strumento da riga di comando di Azure per Mac e Linux]
 [Creazione e distribuzione di un'applicazione Node.js in Siti Web di Azure]: /en-us/develop/nodejs/tutorials/create-a-website-(mac)/ [Pubblicazione in Siti Web di Azure con Git](../CommonTasks/publishing-with-git): /en-us/develop/nodejs/common-tasks/publishing-with-git/ [Centro per sviluppatori di Azure]: /en-us/develop/nodejs/

