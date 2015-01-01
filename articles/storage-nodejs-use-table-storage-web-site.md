<properties urlDisplayName="Website with Storage" pageTitle="Sito Web Node.js con il servizio di archiviazione tabelle | Microsoft Azure" metaKeywords="Azure table storage Node.js, Azure Node.js application, Azure Node.js tutorial, Azure Node.js example" description="A tutorial that teaches you how to use the Azure Table service to store data from a Node application hosted on an Azure website." metaCanonical="" services="web-sites,storage" documentationCenter="nodejs" title="Node.js Web Application using the Azure Table Service" authors="larryfr" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="mwasson" />





# Applicazione Web Node.js con il servizio tabelle di Azure

In questa esercitazione viene illustrato come usare il servizio tabelle fornito da Gestione dati di Azure per archiviare e accedere ai dati da un'applicazione [Node] ospitata in Azure. In questa esercitazione si presuppone che l'utente abbia già usato l'applicazione Node e [Git].

Si apprenderà come:

* Usare npm (Node Package Manager) per installare i moduli di Node

* Usare il servizio tabelle di Azure

* Usare lo strumento da riga di comando di Azure per Mac e Linux per creare un sito Web di Azure

In questa esercitazione verrà creata una semplice applicazione di gestione delle attività basata su Web che consente di creare, recuperare e completare le attività. Le attività vengono archiviate nel servizio tabelle.
 
I file di progetto per questa esercitazione verranno archiviati in una directory denominata **tasklist** e l'applicazione completata presenterà un aspetto simile al seguente:

![A web page displaying an empty tasklist][node-table-finished]

> [WACOM.NOTE] In questa esercitazione si fa riferimento alla cartella **tasklist**. Il percorso completo della cartella è omesso, in quanto la semantica dei percorsi varia da un sistema operativo all'altro. È consigliabile creare la cartella in un percorso di facile accesso nel file system locale, ad esempio **~/node/tasklist** o **c:\node\tasklist**.

> [WACOM.NOTE] Molte delle procedure illustrate di seguito prevedono l'uso della riga di comando. Per tali procedure, usare la riga di comando per il sistema operativo in uso, ad esempio **cmd.exe** (Windows) o **Bash** (Unix Shell). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione Terminale.

##Prerequisiti

Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili i seguenti elementi:

* [node] 0.10.24 o versione successiva

* [Git]

* Un editor di testo

* Un Web browser

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Creare un account di archiviazione

Procedere come descritto di seguito per creare un account di archiviazione. Tale account verrà usato in istruzioni successive nella presente esercitazione.

1. Aprire il Web browser e passare al [portale di Azure]. Se richiesto, accedere con le informazioni della sottoscrizione di Azure.

2. Nella parte inferiore della pagina del portale, fare clic su **+ NUOVO**, quindi selezionare **Account di archiviazione**.

	![+new][portal-new]

	![storage account][portal-storage-account]

3. Selezionare **Creazione rapida**, quindi immettere l'URL e l'area/il gruppo di affinità per l'account di archiviazione. Poiché si sta eseguendo un'esercitazione e non è necessaria la replica globale, deselezionare l'opzione **Abilita replica geografica**. Fare clic su "Create Storage Account".

	![quick create][portal-quick-create-storage]

	Prendere nota dell'URL immesso, poiché vi verrà fatto riferimento come nome dell'account nei passaggi successivi.

4. Dopo la creazione dell'account di archiviazione, fare clic su **Gestisci chiavi** nella parte inferiore della pagina. Vengono visualizzate le chiavi di accesso primaria e secondaria per l'account di archiviazione. Copiare e salvare la chiave di accesso primaria, quindi fare clic sul segno di spunta.

	![access keys][portal-storage-access-keys]

##Installazione dei moduli e generazione dello scaffolding

In questa sezione verrà creata una nuova applicazione Node e verrà usato npm per aggiungere pacchetti di modulo. Per l'applicazione tasklist verranno usati i moduli [Express] e [Azure]. Il modulo Express fornisce un modello di framework View Controller per Node, mentre i moduli Azure forniscono la connettività al servizio tabelle.

###Installare Express e generare lo scaffolding

1. Dalla riga di comando passare alla directory **tasklist**. Se la directory **tasklist** non esiste, crearla.

2. Immettere quanto riportato di seguito per installare il comando express.

		npm install express-generator@4.2.0 -g

    > [WACOM.NOTE] Quando si utilizza il parametro '-g' in alcuni sistemi operativi, è possibile che venga restituito l'errore analogo a: **Error: EPERM, chmod '/usr/local/bin/express'** con la richiesta di provare ad eseguire l'account come amministratore. In questo caso, usare il comando **sudo** per eseguire npm a un livello di privilegi più elevato.

    L'output di questo comando dovrebbe apparire simile al seguente:

		express-generator@4.2.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [WACOM.NOTE] Se si usa il parametro '-g' durante l'installazione del modulo Express, questo viene installato a livello globale, affinché sia possibile accedere al comando **express** per generare lo scaffolding del sito Web senza dover generare informazioni aggiuntive sul percorso.**

4. Per creare lo scaffolding che verrà usato per questa applicazione, usare il comando **express**:

        express

	L'output di questo comando dovrebbe apparire simile al seguente:

		   create : .
		   create : ./package.json
		   create : ./app.js
		   create : ./public
		   create : ./public/images
		   create : ./routes
		   create : ./routes/index.js
		   create : ./routes/users.js
		   create : ./public/stylesheets
		   create : ./public/stylesheets/style.css
		   create : ./views
		   create : ./views/index.jade
		   create : ./views/layout.jade
		   create : ./views/error.jade
		   create : ./public/javascripts
		   create : ./bin
		   create : ./bin/www
		
		   install dependencies:
		     $ cd . && npm install
		
		   run the app:
		     $ DEBUG=my-application ./bin/www

	Dopo il completamento di questo comando, nella directory **tasklist** saranno disponibili diverse nuove directory e file.

###Installare moduli aggiuntivi

Il file **package.json** è uno di quelli creati dal comando **express**. Questo file contiene un elenco di moduli aggiuntivi necessari per un'applicazione Express. In seguito, quando si distribuirà questa applicazione in un sito Web di Azure, il file verrà usato per determinare quali moduli devono essere installati in Azure per supportare l'applicazione.

1. Dalla riga di comando passare alla cartella **tasklist** e immettere quanto segue per installare i moduli descritti nel file **package.json**:

        npm install

    L'output di questo comando dovrebbe apparire simile al seguente:

		debug@0.7.4 node_modules\debug
		
		static-favicon@1.0.2 node_modules\static-favicon
		
		morgan@1.0.1 node_modules\morgan
		└── bytes@0.3.0
		
		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0
		
		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.7 (string_decoder@0.10.25-1, bytes@1.0.0)
		└── type-is@1.1.0 (mime@1.2.11)
		
		express@4.2.0 node_modules\express
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── cookie@0.1.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── debug@0.8.1
		├── fresh@0.2.2
		├── qs@0.6.6
		├── range-parser@1.0.0
		├── methods@1.0.0
		├── buffer-crc32@0.2.1
		├── serve-static@1.1.0
		├── path-to-regexp@0.1.2
		├── send@0.3.0 (debug@0.8.0, mime@1.2.11)
		├── type-is@1.1.0 (mime@1.2.11)
		└── accepts@1.0.1 (negotiator@0.4.7, mime@1.2.11)
		
		jade@1.3.1 node_modules\jade
		├── commander@2.1.0
		├── character-parser@1.2.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		└── with@3.0.0 (uglify-js@2.4.15)


	Verranno installati tutti i moduli predefiniti necessari per Express.

2. Digitare quindi il comando seguente per installare i moduli [azure], [node-uuid], [nconf] e [async] in locale e per salvare una voce per tali moduli nel file **package.json**:

		npm install azure-storage node-uuid async nconf --save

	L'output di questo comando dovrebbe apparire simile al seguente:

		async@0.9.0 node_modules\async

		node-uuid@1.4.1 node_modules\node-uuid
		
		nconf@0.6.9 node_modules\nconf
		├── ini@1.2.1
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)
		
		azure-storage@0.3.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── validator@3.1.0
		├── underscore@1.4.4
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (forever-agent@0.5.2, aws-sign@0.3.0, json-stringify-safe@5.0.0, tunnel-agent@0.3.0, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, form-data@0.1.4, hawk@1.0.0, http-signature@0.10.0)

##Uso del servizio tabelle in un'applicazione Node

In questa sezione si estenderà l'applicazione di base creata dal comando **express** aggiungendo un file **task.js** contenente il modello per le attività. Si modificherà inoltre il file **app.js**esistente e si creerà un nuovo file **tasklist.js** che usi il modello.

### Creare il modello

1. Nella directory **tasklist** creare una nuova directory denominata **models**.

2. Nella directory **models** creare un nuovo file denominato **task.js**. Questo file conterrà il modello per le attività create dall'applicazione.

3. All'inizio del file **task.js** aggiungere il codice seguente per fare riferimento alle librerie necessarie:

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. Quindi, aggiungere il codice per definire ed esportare l'oggetto Task. Tale oggetto è responsabile per la connessione alla tabella.

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

5. Aggiungere quindi il codice seguente per definire metodi aggiuntivi nell'oggetto Task che consentano l'interazione con i dati archiviati nella tabella:

		Task.prototype = {
		  find: function(query, callback) {
		    self = this;
		    self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
		      if(error) {
		        callback(error);
		      } else {
		        callback(null, result.entries);
		      }
		    });
		  },

		  addItem: function(item, callback) {
		    self = this;
		    // use entityGenerator to set types
			// NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
		    var itemDescriptor = {
		      PartitionKey: entityGen.String(self.partitionKey),
		      RowKey: entityGen.String(uuid()),
		      name: entityGen.String(item.name),
		      category: entityGen.String(item.category),
		      completed: entityGen.Boolean(false)
		    };
		    self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
		      if(error){  
		        callback(error);
		      }
		      callback(null);
		    });
		  },

		  updateItem: function(rKey, callback) {
		    self = this;
		    self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
		      if(error) {
		        callback(error);
		      }
		      entity.completed._ = true;
		      self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
		        if(error) {
		          callback(error);
		        }
		        callback(null);
		      });
		    });
		  }
		}

6. Salvare e chiudere il file **task.js**.

###Creare il controller

1. Nella directory **tasklist/routes** creare un nuovo file denominato **tasklist.js** e aprirlo in un editor di testo.

2. Aggiungere il codice seguente al file **tasklist.js**. Ciò consente il caricamento dei moduli di Azure e di quelli asincroni, usati da **tasklist.js**. Viene inoltre definita la funzione **TaskList**, che viene passata come un'istanza dell'oggetto **Task** definito in precedenza:

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

		function TaskList(task) {
		  this.task = task;
		}

2. Continuare ad aggiungere codice al file **tasklist.js** aggiungendo i metodi **showTasks**, **addTask** e **completeTasks**:

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = new azure.TableQuery()
		      .where('completed eq ?', false);
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

3. Salvare il file **tasklist.js**.

### Modificare il file app.js

1. Nella directory **tasklist** aprire il file **app.js** in un editor di testo. Questo file è stato creato in precedenza eseguendo il comando **express**.

2. Aggiungere quanto riportato di seguito all'inizio del file per caricare il modulo di Azure, impostare il nome della tabella, partitionKey, e impostare le credenziali di archiviazione usate da questo esempio:

		var azure = require('azure-storage');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json'});
		var tableName = nconf.get("TABLE_NAME");
		var partitionKey = nconf.get("PARTITION_KEY");
		var accountName = nconf.get("STORAGE_NAME");
		var accountKey = nconf.get("STORAGE_KEY");

	> [WACOM.NOTE] nconf caricherà i valori di configurazione dalle variabili di ambiente oppure dal file **config.json**, che verrà creato più avanti.

3. Nel file app.js scorrere verso il basso fino a individuare la riga seguente:

		app.use('/', routes);
		app.use('/users', users);

	Sostituire le righe sopra con il codice seguente. Verrà inizializzata un'istanza di <strong>Attività</strong> con una connessione all'account di archiviazione. Questa viene quindi passata a <strong>TaskList,</strong>che la userà per la comunicazione con il servizio tabelle:

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));
	
4. Salvare il file **app.js**.

###Modificare la visualizzazione dell'indice

1. Passare alla directory **views** e aprire il file **index.jade** in un editor di testo.

2. Sostituire il contenuto del file **index.jade** con il codice seguente. Verrà definita la visualizzazione delle attività esistenti, nonché un formato per aggiungere nuove attività e contrassegnare quelle esistenti come completate.

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
		      if (typeof tasks === "undefined")
		        tr
		          td 
		      else
		        each task in tasks
		          tr
		            td #{task.name._}
		            td #{task.category._}
		            - var day   = task.Timestamp._.getDate();
		            - var month = task.Timestamp._.getMonth() + 1;
		            - var year  = task.Timestamp._.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name: 
		    input(name="item[name]", type="textbox")
		    label Item Category: 
		    input(name="item[category]", type="textbox")
		    br
		    button.btn(type="submit") Add item

3. Salvare e chiudere il file **index.jade**.

###Modificare il layout globale

Il file **layout.jade**della directory **views** viene usato come modello globale per altri file **.jade**. In questo passaggio verrà modificato in modo da usare [Twitter Bootstrap](https://github.com/twbs/bootstrap), un toolkit che semplifica la progettazione di un sito Web di aspetto gradevole.

1. Scaricare ed estrarre i file per [Twitter Bootstrap](http://getbootstrap.com/). Copiare il file **bootstrap.min.css** dalla cartella **bootstrap\\dist\\css** alla directory **public\\stylesheets** dell'applicazione tasklist.

2. Dalla cartella **views** aprire **layout.jade** nell'editor di testo e sostituire il contenuto con quello seguente:

		doctype html
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

3. Salvare il file **layout.jade**.

###Creare il file di configurazione

Il file **config.json** contiene la stringa di connessione usata per la connessione al database SQL e viene letto dall'applicazione al runtime. Per creare questo file, eseguire la procedura seguente:

1. Nella directory **tasklist** creare un nuovo file denominato **config.json** e aprirlo in un editor di testo.

2. Il contenuto del file **config.json** risulterà simile al seguente:

		{
			"STORAGE_NAME": "storage account name",
			"STORAGE_KEY": "storage access key",
			"PARTITION_KEY": "mytasks",
			"TABLE_NAME": "tasks"
		}

	Sostituire **storage account name** con il nome dell'account di archiviazione creato in precedenza. Sostituire **storage access key** con la chiave di accesso primaria per l'account di archiviazione.

3. Salvare il file.

##Eseguire l'applicazione in locale

Per eseguire il test dell'applicazione nel computer locale, eseguire la procedura seguente:

1. Dalla riga di comando passare alla directory **tasklist**.

2. Usare il comando seguente per avviare l'applicazione in locale:

        npm start

3. Aprire un Web browser e passare all'indirizzo http://127.0.0.1:3000. Verrà visualizzata una pagina Web simile alla seguente:

    ![A webpage displaying an empty tasklist][node-table-finished]

4. Usare i campi **Item Name** e **Item Category** per immettere le informazioni, quindi fare clic su **Add item**.

5. La pagina verrà aggiornata e l'elemento verrà visualizzato nella tabella dell'elenco attività.

    ![An image of the new item in the list of tasks][node-table-list-items]

6. Per completare un'attività, è sufficiente selezionare la casella di controllo nella colonna Complete,quindi fare clic su **Update tasks**.

7. Per interrompere il processo di Node, passare alla riga di comando e premere **CTRL** + **C**.

##Distribuire l'applicazione in Azure

Nei passaggi di questa sezione vengono usati gli strumenti da riga di comando di Azure per creare un nuovo sito Web di Azure e quindi viene usato Git per distribuire l'applicazione. Per questa procedura, è necessario disporre di una sottoscrizione di Azure.

> [WACOM.NOTE] È possibile eseguire queste procedure anche nel portale di Azure. Per altre informazioni sulla distribuzione di un'applicazione Node.js tramite il portale di Azure, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure].

> [WACOM.NOTE] Se questo è il primo sito Web di Azure che si crea, per distribuire l'applicazione è necessario usare il portale di Azure.

###Creare una sottoscrizione di Azure

Se non si dispone già di una sottoscrizione di Azure, è possibile iscriversi [gratuitamente]. Dopo l'iscrizione, eseguire la procedura seguente per continuare con questa esercitazione.

[WACOM.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

###Installare lo strumento da riga di comando di Azure per Mac e Linux

Per installare gli strumenti da riga di comando, usare il comando seguente:
	
	npm install azure-cli -g

> [WACOM.NOTE] Per altre informazioni, vedere [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure](/it-it/documentation/articles/xplat-cli/).

> [WACOM.NOTE] Anche se gli strumenti da riga di comando sono stati creati principalmente per gli utenti Mac e Linux, essendo basati su Node.js, dovrebbero funzionare in qualsiasi sistema in grado di eseguire Node.

###Importare le impostazioni di pubblicazione

Prima di usare gli strumenti da riga di comando con Azure, è necessario scaricare un file contenente informazioni sulla sottoscrizione. Eseguire i passaggi seguenti per scaricare e importare questo file.

1. Dalla riga di comando passare alla directory **tasklist**.

2. Digitare il comando seguente per avviare il browser e passare alla pagina di download. Se richiesto, accedere con l'account associato alla sottoscrizione.

		azure account download
	
	![The download page][download-publishing-settings]
	
	Il download del file dovrebbe iniziare automaticamente. In caso contrario, è possibile fare clic sul collegamento all'inizio della pagina per scaricare manualmente il file.

3. Al termine del download del file, usare il comando seguente per importare le impostazioni:

		azure account import <percorso del file>
		
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

4. Al termine dell'importazione, è consigliabile eliminare il file delle impostazioni di pubblicazione perché non è più necessario e contiene informazioni riservate relative alla sottoscrizione di Azure.

###Creare un sito Web di Azure

1. Dalla riga di comando passare alla directory **tasklist**.

2. Usare il comando seguente per creare un nuovo sito Web di Azure.

		azure site create --git
		
	Verrà richiesto di specificare il nome del sito Web e il data center in cui si troverà il sito. Fornire un nome univoco e selezionare un data center geograficamente vicino alla propria posizione.
	
	Il parametro `--git` creerà un repository Git per il sito Web in Azure. Verrà inoltre inizializzato un archivio Git nella directory corrente, se non ne esiste già uno. Creerà anche un [repository Git remoto] denominato 'azure' che verrà usato per pubblicare l'applicazione in Azure, oltre a un file **web.config** contenente le impostazioni usate da Azure per ospitare le applicazioni Node.
	
	> [WACOM.NOTE] Se questo comando viene eseguito da una directory che contiene già un archivio Git, la directory non verrà reinizializzata.
	
	> [WACOM.NOTE] Se il parametro "--git" viene omesso ma la directory contiene un archivio Git, l'archivio "azure" verrà comunque creato.
	
	Dopo il completamento di questo comando, verrà visualizzato un output simile al seguente. Si noti che la riga che inizia con **Website created at** contiene l'URL del sito Web.
	
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

	> [WACOM.NOTE] Se questo è il primo sito Web di Azure per la sottoscrizione, verrà indicato di usare il portale per la creazione del sito Web. Per altre informazioni, vedere [Creazione e distribuzione di un'applicazione Node.js in Siti Web di Azure].

###Pubblicare l'applicazione

1. Nella finestra del terminale passare alla directory **tasklist** se non è già stato fatto.

2. Usare i comandi seguenti per aggiungere e quindi confermare i file nel repository Git locale:

		git add .
		git commit -m "adding files"

3. Durante il push delle ultime modifiche apportate al repository Git nel sito Web di Azure, è necessario specificare che il branch di destinazione è **master** perché viene usato per il contenuto del sito Web.

		git push azure master
	
	Al termine della distribuzione, verrà visualizzata un'istruzione simile alla seguente:
	
		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. Al termine dell'operazione di push, passare all'URL del sito Web restituito in precedenza usando il comando `azure create site` per visualizzare l'applicazione.

###Passare a una variabile di ambiente

In precedenza è stato implementato codice che cerca una variabile di ambiente o carica il valore dal file **config.json**. Nella procedura seguente verranno create coppie chiave-valore nella configurazione del sito Web a cui l'applicazione accede effettivamente tramite una variabile di ambiente.

1. Nel portale di gestione di Azure fare clic su **Siti Web**, quindi selezionare il sito Web.

	![Open website dashboard][go-to-dashboard]

2. Fare clic su **CONFIGURA**, quindi individuare la sezione **Impostazioni app** della pagina. 

	![configure link][web-configure]

3. Nella sezione **Impostazioni app** immettere **STORAGE_NAME** nel campo **CHIAVE**, quindi il nome dell'account di archiviazione nel campo **VALORE**. Fare clic sul segno di spunta per passare al campo successivo. Ripetere il processo per le chiavi e i valori seguenti:

	* **STORAGE_KEY**: la chiave di accesso primaria per l'account di archiviazione
	
	* **PARTITION_KEY** - 'mytasks'

	* **TABLE_NAME** - 'tasks'

	![app settings][app-settings]

4. Infine, fare clic sull'icona **SALVA** nella parte inferiore della pagina per eseguire il commit della modifica nell'ambiente di runtime.

	![app settings save][app-settings-save]

5. Dalla riga di comando passare alla directory **tasklist** e immettere il comando seguente per rimuovere il file **config.json**:

		git rm config.json
		git commit -m "Removing config file"

6. Per distribuire le modifiche in Azure, eseguire il comando seguente:

		git push azure master

Dopo la distribuzione delle modifiche in Azure, l'applicazione Web continuerà a funzionare dal momento che legge la stringa di connessione dalla voce **Impostazioni app**. Per verificarlo, modificare il valore della voce **STORAGE_KEY** in **Impostazioni app** specificando un valore non valido. Dopo avere salvato questo valore, si verificherà un errore nel sito Web a causa dell'impostazione di una chiave di accesso alle risorse di archiviazione non valida.

##Passaggi successivi

Nei passaggi di questo articolo viene descritto come archiviare informazioni tramite il servizio tabelle. Tuttavia per tale attività è anche possibile usare MongoDB. Per altre informazioni, vedere l'articolo relativo alla [creazione di un'applicazione Web Node.js con MongoDB].

##Risorse aggiuntive

[Strumenti da riga di comando di Azure per Mac e Linux]    
[Creazione e distribuzione di un'applicazione Node.js in Siti Web di Azure]: /it-it/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[Pubblicazione in Siti Web di Azure con Git]: /it-it/documentation/articles/web-sites-publish-source-control/
[Centro per sviluppatori di Azure]: /it-it/develop/nodejs/


[nodo]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[gratuitamente]: http://windowsazure.com
[Git remoto]: http://git-scm.com/docs/git-remote

[Applicazione Web Node.js con MongoDB]: /it-it/documentation/articles/web-sites-nodejs-store-data-mongodb/
[Strumento da riga di comando di Azure per Mac e Linux]: /it-it/documentation/articles/xplat-cli/

[Pubblicazione in Siti Web di Azure con Git]: /it-it/documentation/articles/web-sites-publish-source-control/
[azure]: https://github.com/Azure/azure-sdk-for-node


[Portale di Azure]: http://windowsazure.com


[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png

[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png

[Creazione e distribuzione di un'applicazione Node.js in un Sito Web di Azure]: /it-it/documentation/articles/web-sites-nodejs-develop-deploy-mac/

<!--HONumber=35_1-->
