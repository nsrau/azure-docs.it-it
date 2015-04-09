<properties 
	pageTitle="Sito Web Node.js con MongoDB in MongoLab - Azure" 
	description="Informazioni su come creare un sito Web Node.js in Azure che si connette a un'istanza di MongoDB ospitata in MongoLab." 
	services="web-sites, virtual-machines" 
	documentationCenter="nodejs" 
	authors="chrischang12" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/04/2014" 
	ms.author="mwasson"/>






# Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab

<p><em>Autore: Eric Sedor, MongoLab</em></p>

Salve, benvenuti nel database MongoDB distribuito come servizio. In questa esercitazione si apprenderà come:

1. [Eseguire il provisioning del database][provision] - Il componente aggiuntivo [MongoLab](http://mongolab.com) di Azure Store fornisce un database MongoDB ospitato nel cloud di Azure e gestito dalla piattaforma database cloud di MongoLab.
2. [Creare l'app][create] - Si tratterà di una semplice app Node.js per la gestione di un elenco di attività.
3. [Distribuire l'app][deploy] - Collegando alcuni hook di configurazione, il push del codice diventerà davvero semplice.
4. [Gestire il database][manage] - Verrà infine illustrato il portale di gestione del database basato sul Web di MongoLab, in cui è possibile cercare, visualizzare e modificare i dati con facilità.

In qualsiasi momento nel corso di questa esercitazione, sarà possibile inviare un messaggio di posta elettronica con eventuali domande a [support@mongolab.com](mailto:support@mongolab.com).

Prima di continuare, assicurarsi di avere installato quanto segue:

* [Node.js] versione 0.10.29+

* [Git]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Avvio rapido
Se si conosce già Azure Store, usare questa sezione per iniziare rapidamente. In caso contrario, passare alla sezione [Provisioning del database][provision] più avanti.
 
1. Aprire Azure Store.  
![Store][button-store]
2. Fare clic sul componente aggiuntivo MongoLab.  
![MongoLab][entry-mongolab]
3. Fare clic sul componente aggiuntivo MongoLab nell'elenco Componenti aggiuntivi e fare clic su **Informazioni di connessione**.  
![ConnectionInfoButton][button-connectioninfo]  
4. Copiare MONGOLAB_URI negli Appunti.  
![ConnectionInfoScreen][screen-connectioninfo]  
**Questo URI contiene il nome utente e la password per il database.  Trattare queste informazioni come dati sensibili e non condividerle.**
5. Aggiungere il valore all'elenco Stringhe di connessione nel menu Configurazione dell'applicazione Web di Azure:  
![WebSiteConnectionStrings][focus-website-connectinfo]
6. In **Nome** immettere MONGOLAB\_URI.
7. In **Valore** incollare la stringa di connessione ottenuta nella sezione precedente.
8. Selezionare **Personalizzato** nell'elenco a discesa Tipo (anziché la voce predefinita **SQLAzure**).
9. Eseguire  `npm install mongoose` per ottenere Mongoose, un driver del nodo MongoDB.
10. Impostare un hook nel codice per ottenere l'URI di connessione MongoLab da una variabile di ambiente e stabilire la connessione:

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

Nota: Azure aggiunge il prefisso **CUSTOMCONNSTR\_** alla stringa di connessione dichiarata in origine. Per questo motivo il codice fa riferimento **CUSTOMCONNSTR\_MONGOLAB\_URI** anziché a **MONGOLAB\_URI**.

Si passerà ora all'esercitazione completa.

<h2><a name="provision"></a>Provisioning del database</h2>

[AZURE.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

<h2><a name="create"></a>Creare l'app</h2>

In questa sezione si imposterà l'ambiente di sviluppo e si preparerà il codice per un'applicazione Web per un elenco di attività di base mediante Node.js, Express e MongoDB. [Express] fornisce un framework View Controller per il nodo, mentre [Mongoose] è un driver per comunicare con MongoDB nel nodo.

### Impostazione

#### Generare lo scaffolding e installare i moduli

1. Alla riga di comando creare e accedere alla directory **tasklist**. Questa sarà la directory del progetto.
2. Immettere il comando seguente per installare Express.

		npm install express -g
 
	`-g` indica la modalità globale, usata per rendere il modulo <strong>express</strong> disponibile senza specificare un percorso di directory. Se viene visualizzato <strong>Error: EPERM, chmod '/usr/local/bin/express'</strong>, usare <strong>sudo</strong> per eseguire npm a un livello di privilegi più elevato.

    L'output di questo comando dovrebbe apparire simile al seguente:

		express@4.9.1 C:\Users\mongolab\AppData\Roaming\npm\node_modules\express
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── fresh@0.2.4
		├── cookie@0.1.2
		├── range-parser@1.0.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── parseurl@1.3.0
		├── serve-static@1.6.2
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── debug@2.0.0 (ms@0.6.2)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── etag@1.3.1 (crc@3.0.0)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)
		└── type-is@1.5.1 (mime-types@2.0.1)
 
3. Per creare lo scaffolding che verrà usato per questa applicazione, usare il comando **express**:

    	express

    Si noti che per questa esercitazione viene usato Express v4.x.x. Se nel sistema è già installato il generatore di app Express 3, è necessario prima disinstallarlo:

    	npm uninstall -g express

    A questo punto installare il nuovo generatore per la versione 4.x.x:

    	npm install -g express-generator

	Una volta eseguito il comando **express**, l'output sarà simile al seguente:

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
		create : ./routes/users.js
		create : ./views
		create : ./views/index.jade
		create : ./views/layout.jade
		create : ./views/error.jade
		create : ./bin
		create : ./bin/www

		install dependencies:
		$ cd . && npm install


	Dopo il completamento di questo comando, nella directory **tasklist** saranno disponibili diverse nuove directory e file.
	
4. Immettere quanto segue per installare i moduli descritti nel file **package.json**:

        npm install

    L'output di questo comando dovrebbe apparire simile al seguente:

		cookie-parser@1.3.3 node_modules/cookie-parser
		├── cookie@0.1.2
		└── cookie-signature@1.0.5

		debug@2.0.0 node_modules/debug
		└── ms@0.6.2

		serve-favicon@2.1.4 node_modules/serve-favicon
		├── ms@0.6.2
		├── fresh@0.2.4
		└── etag@1.3.1 (crc@3.0.0)

		morgan@1.3.1 node_modules/morgan
		├── basic-auth@1.0.0
		├── depd@0.4.5
		└── on-finished@2.1.0 (ee-first@1.0.5)

		express@4.9.1 node_modules/express
		├── utils-merge@1.0.0
		├── merge-descriptors@0.0.2
		├── cookie@0.1.2
		├── fresh@0.2.4
		├── escape-html@1.0.1
		├── range-parser@1.0.2
		├── cookie-signature@1.0.5
		├── finalhandler@0.2.0
		├── vary@1.0.0
		├── media-typer@0.3.0
		├── serve-static@1.6.2
		├── parseurl@1.3.0
		├── methods@1.1.0
		├── path-to-regexp@0.1.3
		├── depd@0.4.5
		├── qs@2.2.3
		├── etag@1.3.1 (crc@3.0.0)
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── proxy-addr@1.0.1 (ipaddr.js@0.1.2)
		├── send@0.9.2 (destroy@1.0.3, ms@0.6.2, mime@1.2.11)
		├── type-is@1.5.1 (mime-types@2.0.1)
		└── accepts@1.1.0 (negotiator@0.4.7, mime-types@2.0.1)

		body-parser@1.8.2 node_modules/body-parser
		├── media-typer@0.3.0
		├── raw-body@1.3.0
		├── bytes@1.0.0
		├── depd@0.4.5
		├── on-finished@2.1.0 (ee-first@1.0.5)
		├── qs@2.2.3
		├── iconv-lite@0.4.4
		└── type-is@1.5.1 (mime-types@2.0.1)

		jade@1.6.0 node_modules/jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── void-elements@1.0.0
		├── mkdirp@0.5.0 (minimist@0.0.8)
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		└── with@3.0.1 (uglify-js@2.4.15)

	Il file **package.json** è uno di quelli creati dal comando **express**. Questo file contiene un elenco di moduli aggiuntivi necessari per un'applicazione Express. In seguito, quando si distribuirà questa applicazione in un sito Web di Azure, il file verrà usato per determinare quali moduli devono essere installati in Azure per supportare l'applicazione.

5. Immettere quindi il comando seguente per installare il modulo Mongoose localmente e per salvare una voce per il modulo nel file **package.json**:

		npm install mongoose --save

	L'output di questo comando dovrebbe apparire simile al seguente:

		mongoose@3.8.16 node_modules/mongoose
		├── regexp-clone@0.0.1
		├── muri@0.3.1
		├── sliced@0.0.5
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.8.0 (debug@0.7.4)
		└── mongodb@1.4.9 (readable-stream@1.0.31, kerberos@0.0.3, bson@0.2.12)
	
### Codice

Ora che l'ambiente e lo scaffolding sono pronti, si estenderà l'applicazione di base creata dal comando **express** aggiungendo un file **task.js** contenente il modello per le attività. Si modificherà inoltre il file **app.js** esistente e si creerà un nuovo file controller **tasklist.js** per usare il modello.

#### Creare il modello

1. Nella directory **tasklist** creare una nuova directory denominata **models**.

2. Nella directory **models** creare un nuovo file denominato **task.js**. Questo file conterrà il modello per le attività create dall'applicazione.

3. Aggiungere il seguente codice al file **task.js**:

        var mongoose = require('mongoose'), 
          Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	      itemName      : String, 
	      itemCategory  : String, 
	      itemCompleted : { type: Boolean, default: false },
	      itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Salvare e chiudere il file **task.js**.

#### Creare il controller

1. Nella directory **tasklist/routes** creare un nuovo file denominato **tasklist.js** e aprirlo in un editor di testo.

2. Aggiungere il codice seguente al file **tasklist.js**. Verranno caricati il modulo mongoose e il modello di attività definito in **task.js**. La funzione TaskList viene usata per creare la connessione al server MongoDB in base al valore **connection** e fornisce i metodi **showTasks**, **addTask** e **completeTasks**:

		var mongoose = require('mongoose'), 
 		  task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
		  mongoose.connect(connection);
		}

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    task.find({ itemCompleted : false }, function foundTasks(err, items) {
		    res.render('index', { title: 'My ToDo List', tasks: items })
		    });
		  },

		  addTask: function(req,res) {
		    var item = req.body;
		    var newTask = new task();
		    newTask.itemName = item.itemName;
		    newTask.itemCategory = item.itemCategory;
		    newTask.save(function savedTask(err) {
		      if(err) {
		        throw err;
		      }
		    });
		    res.redirect('/');
		  },


		  completeTask: function(req,res) {
		    var completedTasks = req.body;
		    for(taskId in completedTasks) {
		      if(completedTasks[taskId]=='true') {
		        var conditions = { _id: taskId };
		        var updates = { itemCompleted: completedTasks[taskId] };
		        task.update(conditions, updates, function updatedTask(err) {
		          if(err) {
		            throw err;
		          }
		        });
		      }
		    }
		    res.redirect('/');
		  }
		}

3. Salvare il file **tasklist.js**.

#### Modificare la visualizzazione dell'indice

1. Passare alla directory **views** e aprire il file **index.jade** in un editor di testo.

2. Sostituire il contenuto del file **index.jade** con il codice seguente. Verrà definita la visualizzazione delle attività esistenti, nonché un formato per aggiungere nuove attività e contrassegnare quelle esistenti come completate.

		h1 #{title}
		form(action="/completetask", method="post")
		  table(border="1")
		    tr
		      td Name
		      td Category
		      td Date
		      td Complete
		    each task in tasks
		      tr
		        td #{task.itemName}
		        td #{task.itemCategory}
		        - var day   = task.itemDate.getDate();
		        - var month = task.itemDate.getMonth() + 1;
		        - var year  = task.itemDate.getFullYear();
		        td #{month + "/" + day + "/" + year}
		        td
		          input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
		  input(type="submit", value="Update tasks")
		hr
		form(action="/addtask", method="post")
		  table(border="1") 
		    tr
		      td Item Name: 
		      td 
		        input(name="itemName", type="textbox")
		    tr
		      td Item Category: 
		      td 
		        input(name="itemCategory", type="textbox")
		  input(type="submit", value="Add item")

3. Salvare e chiudere il file **index.jade**.

#### Sostituire app.js

1. Nella directory **tasklist** aprire il file **app.js** in un editor di testo. Questo file è stato creato in precedenza eseguendo il comando **express**.
2. Aggiungere il codice seguente all'inizio del file **app.js**. Verrà inizializzato **TaskList** con la stringa di connessione per il server MongoDB:

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	Si noti la seconda riga: si accede a una variabile di ambiente che si configurerà in seguito, contenente le informazioni sulla connessione per l'istanza di mongo. Se un'istanza locale di mongo è in esecuzione a scopo di sviluppo, può essere utile impostare temporaneamente questo valore su "localhost" anziché su  `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

3. Individuare le seguenti righe:
		
		app.use('/', routes);
		app.use('/users', users);

	Sostituirle con:

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	Verranno aggiunte le funzioni definite in **tasklist.js** come route.

4. Per inizializzare l'app, aggiungere le seguenti righe nella parte finale del file **app.js**:

		app.listen(3000); // Listen on port 3000
		module.exports = app;

5. Salvare il file **app.js**.

<h2><a name="deploy"></a>Distribuire l'app</h2>

Ora che l'applicazione è stata sviluppata, è il momento di creare un sito Web di Azure per ospitarla, configurare tale sito e distribuire il codice. L'argomento principale di questa sezione è l'utilizzo della stringa di connessione MongoDB (URI). Si configurerà una variabile di ambiente nel sito Web con questo URI per mantenere l'URI separato dal codice.  È consigliabile considerare l'URI come un'informazione riservata in quanto contiene le credenziali per connettersi al database.

Nei passaggi di questa sezione vengono usati gli strumenti da riga di comando di Azure per creare un nuovo sito Web di Azure e quindi viene usato Git per distribuire l'applicazione. Per questa procedura, è necessario disporre di una sottoscrizione di Azure.

### Installare lo strumento da riga di comando di Azure per Mac e Linux

Per installare gli strumenti da riga di comando, usare il comando seguente:
	
	npm install azure-cli -g

Se si è già installato <strong>Azure SDK per Node.js</strong> dal <a href="/develop/nodejs/">Centro per sviluppatori Azure</a>, gli strumenti da riga di comando saranno già installati. Per altre informazioni, vedere <a href="virtual-machines-command-line-tools.md">Strumenti da riga di comando di Azure per Mac e Linux</a>.

Anche se gli strumenti da riga di comando di Azure sono stati creati principalmente per gli utenti Mac e Linux, essendo basati su Node.js, dovrebbero funzionare in qualsiasi sistema in grado di eseguire Node.

### Importare le impostazioni di pubblicazione

Prima di usare gli strumenti da riga di comando con Azure, è necessario scaricare un file contenente informazioni sulla sottoscrizione. Eseguire i passaggi seguenti per scaricare e importare questo file.

1. Nella riga di comando digitare il comando seguente per avviare il browser e passare alla pagina di download. Se richiesto, accedere con l'account associato alla sottoscrizione.

		azure account download
	
	![La pagina di download][download-publishing-settings]
	
	Il download del file dovrebbe iniziare automaticamente. In caso contrario, è possibile fare clic sul collegamento all'inizio della pagina per scaricare manualmente il file.

2. Al termine del download del file, usare il comando seguente per importare le impostazioni:

		azure account import <path-to-file>
		
	Specificare il percorso e il nome del file delle impostazioni di pubblicazione scaricato nel passaggio precedente. Dopo il completamento del comando, l'output dovrebbe essere simile al seguente:
	
		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/mongolab/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


3. Al termine dell'importazione, è consigliabile eliminare il file delle impostazioni di pubblicazione perché non è più necessario e contiene informazioni riservate relative alla sottoscrizione di Azure.

### Creare un nuovo sito Web ed eseguire il push del codice

Creare un sito Web in Azure è molto semplice. Se si tratta del primo sito Web di Azure, è necessario usare il portale. Se si dispone già di almeno un sito, andare al passaggio 7.

1. Nel portale di Azure fare clic su **Nuovo**.    
![Nuovo][button-new]
2. Selezionare **Calcolo > Sito Web > Creazione rapida**. 
![CreateSite][screen-mongolab-newwebsite]
3. Immettere un prefisso URL. Scegliere il nome desiderato, ma tenere presente che deve essere univoco. "mymongoapp" non sarà probabilmente disponibile.
4. Fare clic su **Crea sito Web**.
5. Al termine della creazione del sito Web, fare clic sul nome del sito nell'elenco. Verrà visualizzato il dashboard del sito Web.  
![WebSiteDashboard][screen-mongolab-websitedashboard]
6. Fare clic su **Imposta distribuzione dal controllo del codice sorgente** in **riepilogo rapido**, selezionare GitHub e immettere nome utente e password desiderati per git. Si userà questa password durante il push al sito Web (nel passaggio 9).  
7. Se il sito Web è stato creato attenendosi ai passaggi precedenti, il comando seguente completerà il processo. Se, tuttavia, si dispone già di più di un sito Web di Azure, è possibile saltare i passaggi precedenti e creare un nuovo sito Web mediante questo stesso comando. Nella directory del progetto **tasklist**: 

		azure site create myuniquesitename --git  
	Sostituire  'myuniquesitename' con il nome univoco del sito Web. Se il sito Web viene creato come parte di questo comando, verrà richiesto di specificare il data center in cui si troverà il sito. Selezionare il data center geograficamente vicino al database di MongoLab.
	
	Il parametro `--git` creerà:
	* un repository Git locale nella cartella **tasklist** se non esiste già.
	* un repository [Git remoto] denominato  'azure' che verrà usato per pubblicare l'applicazione in Azure.
	* un file [iisnode.yml] contenente le impostazioni usate da Azure per ospitare le applicazioni Node.
	* un file .gitignore per escludere la cartella dei moduli di Node dalla pubblicazione su .git.  
	  
	Dopo il completamento di questo comando, verrà visualizzato un output simile al seguente. Si noti che la riga che inizia con **Created web site at** contiene l'URL del sito Web.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquesitename.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

8. Usare i comandi seguenti per aggiungere e quindi confermare i file nell'archivio Git locale:

		git add .
		git commit -m "adding files"

9. Eseguire il push del codice:

		git push azure master  
	Durante il push delle ultime modifiche apportate a repository Git nel sito Web di Azure, è necessario specificare che il ramo di destinazione è **master** perché viene usato per il contenuto del sito Web. Se viene richiesto di specificare una password, immettere la password creata sopra durante l'impostazione della pubblicazione git per il sito Web.
	
	L'output sarà simile al seguente. Durante l'esecuzione della distribuzione, Azure scaricherà tutti i moduli npm. 

		Counting objects: 17, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (13/13), done.
		Writing objects: 100% (17/17), 3.21 KiB, done.
		Total 17 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id 'ef276f3042'.
		remote: Preparing files for deployment.
		remote: Running NPM.
		...
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
 		 * [new branch]      master -> master
 
La procedura è quasi completa.

### Configurare l'ambiente
La variabile di ambiente process.env.CUSTOMCONNSTR\_MONGOLAB\_URI nel codice verrà ora popolata con il valore fornito ad Azure durante il provisioning del database di MongoLab.

#### Ottenere la stringa di connessione MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Aggiungere la stringa di connessione alle variabili di ambiente del sito Web

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

## Completamento della procedura

Eseguire  `azure site browse` dalla directory del progetto per aprire automaticamente un browser oppure per aprire un browser e passare manualmente all'URL del sito Web (myuniquesite.azurewebsites.net):

![Pagina Web contenente un elenco attività vuoto][node-mongo-finished]

<h2><a name="manage"></a>Gestire il database</h2>

[AZURE.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Congratulazioni. verrà lanciata un'applicazione Node.js supportata da un database MongoDB ospitato da MongoLab. Ora che si dispone di un database MongoLab, è possibile contattare [support@mongolab.com](mailto:support@mongolab.com) per eventuali domande o problemi sul database o per assistenza con MongoDB o con il driver del nodo. È ora possibile usare l'applicazione.



[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
[button-store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png 
[button-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage
[Node.js]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[gratuitamente]: /pricing/free-trial
[Git remoto]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Strumento da riga di comando di Azure per Mac e Linux]: virtual-machines-command-line-tools.md
[Centro per sviluppatori di Azure]: /develop/nodejs/
[Creare e distribuire un'applicazione Node.js in Siti Web di Azure]: /develop/nodejs/tutorials/create-a-website-(mac)/
[Pubblicazione in Siti Web di Azure con Git]: /develop/nodejs/common-tasks/publishing-with-git/
[MongoLab]: http://mongolab.com
[Applicazione Web Node.js con archiviazione in MongoDB (macchina virtuale)]: /develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png




<!--HONumber=49-->