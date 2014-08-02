<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="Web site with MongoDB" pageTitle="Node.js web site with MongoDB on MongoLab - Azure" metaKeywords="" description="Learn how to create a Node.js Azure Web Site that connects to a MongoDB instance hosted on MongoLab." metaCanonical="" services="web-sites,virtual-machines" documentationCenter="Node.js" title="Create a Node.js Application on Azure with MongoDB using the MongoLab Add-On" authors="" solutions="" manager="" editor="" />

Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab
====================================================================================================

*Autore: Eric Sedor, MongoLab*

Salve, benvenuti nel database MongoDB distribuito come servizio. In questa esercitazione si apprenderà come:

1.  [Eseguire il provisioning del database](#provision) - Il componente aggiuntivo [MongoLab](http://mongolab.com) di Azure Store fornisce un database MongoDB ospitato nel cloud di Azure e gestito dalla piattaforma database cloud di MongoLab.
2.  [Creare l'app](#create) - Si tratterà di una semplice app Node.js per la gestione di un elenco di attività.
3.  [Distribuire l'app](#deploy) - Collegando alcuni hook di configurazione, il push del codice diventerà davvero semplice.
4.  [Gestire il database](#manage) - Verrà infine illustrato il portale di gestione del database basato sul Web di MongoLab, in cui è possibile cercare, visualizzare e modificare i dati con facilità.

In qualsiasi momento nel corso di questa esercitazione, sarà possibile inviare un messaggio di posta elettronica con eventuali domande a <support@mongolab.com>.

Prima di continuare, assicurarsi di avere installato quanto segue:

-   [Node.js](http://nodejs.org) versione 0.8.14+

-   [Git](http://git-scm.com)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Avvio rapido
------------

Se si conosce già Azure Store, utilizzare questa sezione per iniziare rapidamente. In caso contrario, passare alla sezione [Provisioning del database](#provision) più avanti.

1.  Aprire Azure Store.  
![Store](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png)
2.  Fare clic sul componente aggiuntivo MongoLab.  
![MongoLab](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png)
3.  Fare clic sul componente aggiuntivo MongoLab nell'elenco Add-Ons e fare clic su **Connection Info**.  
![ConnectionInfoButton](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png)
4.  Copiare MONGOLAB\_URI negli Appunti.  
![ConnectionInfoScreen](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png)  
**Questo URI contiene il nome utente e la password per il database. Trattare queste informazioni come dati sensibili e non condividerle.**
5.  Aggiungere il valore all'elenco Connection Strings nel menu Configuration dell'applicazione Web di Azure:  
![WebSiteConnectionStrings](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png)
6.  In **Name** immettere MONGOLAB\_URI.
7.  In **Value** incollare la stringa di connessione ottenuta nella sezione precedente.
8.  Selezionare **Custom** nell'elenco a discesa Type (anziché la voce predefinita **SQLAzure**).
9.  Eseguire `npm install mongoose` per ottenere M ongoose, un driver del nodo MongoDB.
10. Impostare un hook nel codice per ottenere l'URI di connessione MongoLab da una variabile di ambiente e stabilire la connessione:

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR\_MONGOLAB\_URI
 		...
 		mongoose.connect(connectionString);

Nota: Azure aggiunge il prefisso **CUSTOMCONNSTR\_** alla stringa di connessione dichiarata in origine. Per questo motivo il codice fa riferimento a **CUSTOMCONNSTR\_MONGOLAB\_URI.** anziché a **MONGOLAB\_URI**.

Si passerà ora all'esercitazione completa.

Provisioning del database
-------------------------

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

Creazione dell'app
------------------

In questa sezione si imposterà l'ambiente di sviluppo e si preparerà il codice per un'applicazione Web per un elenco di attività di base mediante Node.js, Express e MongoDB. [Express](http://expressjs.com) fornisce un framework View Controller per il nodo, mentre [Mongoose](http://mongoosejs.com) è un driver per comunicare con MongoDB nel nodo.

### Impostazione

#### Generare lo scaffolding e installare i moduli

1.  Nella riga di comando creare e accedere alla directory **tasklist**. Questa sarà la directory del progetto.
2.  Immettere il comando seguente per installare Express.

         npm install express -g

    `-g` indica la modalità globale, utilizzata per rendere il modulo **express** disponibile senza specificare un percorso di directory. Se viene visualizzato **Error: EPERM, chmod '/usr/local/bin/express'**, utilizzare **sudo** per eseguire npm a un livello di privilegi più elevato.

    L'output di questo comando dovrebbe apparire simile al seguente:

		express@3.3.4 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
		├── methods@0.0.1
		├── fresh@0.1.0
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.3 (mime@1.2.9)
		└── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)
 
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

4.  Immettere quanto segue per installare i moduli descritti nel file **package.json**:

         npm install

    L'output di questo comando dovrebbe apparire simile al seguente:

		express@3.3.4 node_modules\express
		├── methods@0.0.1
		├── fresh@0.1.0
		├── range-parser@0.0.4
		├── cookie-signature@1.0.1
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.3 (mime@1.2.9)
		└── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

		jade@0.33.0 node_modules\jade
		├── character-parser@1.0.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── with@1.1.0 (uglify-js@2.3.6)
		├── constantinople@1.0.1 (uglify-js@2.3.6)
		├── transformers@2.0.1 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		└── monocle@0.1.48 (readdirp@0.2.5)

    Il file **package.json** è uno di quelli creati dal comando **express**. Questo file contiene un elenco di moduli aggiuntivi necessari per un'applicazione Express. In seguito, quando si distribuirà questa applicazione in un sito Web di Azure, il file verrà utilizzato per determinare quali moduli devono essere installati in Azure per supportare l'applicazione.

5.  Immettere quindi il comando seguente per installare il modulo Mongoose localmente e per salvare una voce per il modulo nel file **package.json**:

         npm install mongoose --save

    L'output di questo comando dovrebbe apparire simile al seguente:

		mongoose@3.6.15 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.3
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── ms@0.1.0
		├── mpromise@0.2.1 (sliced@0.0.4)
		└── mongodb@1.3.11 (bson@0.1.9, kerberos@0.0.3)

    È possibile ignorare qualsiasi messaggio sull'installazione del parser bson C++.

### Codice

Ora che l'ambiente e lo scaffolding sono pronti, si estenderà l'applicazione di base creata dal comando **express** aggiungendo un file **task.js** contenente il modello per le attività. Si modificherà inoltre il file **app.js** esistente e si creerà un nuovo file controller **tasklist.js** per utilizzare il modello.

#### Creare il modello

1.  Nella directory **tasklist** creare una nuova directory denominata **models**.

2.  Nella directory **models** creare un nuovo file denominato **task.js**. Questo file conterrà il modello per le attività create dall'applicazione.

3.  Aggiungere il seguente codice al file **task.js**:

         var mongoose = require('mongoose')
           , Schema = mongoose.Schema;

         var TaskSchema = new Schema({
             itemName      : String
           , itemCategory  : String
           , itemCompleted : { type: Boolean, default: false }
           , itemDate      : { type: Date, default: Date.now }
         });

         module.exports = mongoose.model('TaskModel', TaskSchema);

4.  Salvare e chiudere il file **task.js**.

#### Creare il controller

1.  Nella directory **tasklist/routes** creare un nuovo file denominato **tasklist.js** e aprirlo in un editor di testo.

2.  Aggiungere il seguente codice al file **tasklist.js**. Verranno caricati il modulo mongoose e il modello di attività definito in **task.js**. La funzione TaskList viene utilizzata per creare la connessione al server MongoDB in base al valore **connection** e fornisce i metodi **showTasks**, **addTask** e **completeTasks**:

         var mongoose = require('mongoose')
           , task = require('../models/task.js');

         module.exports = TaskList;

         function TaskList(connection) {
  		  mongoose.connect(connection);
		}

		TaskList.prototype = {
  		  showTasks: function(req, res) {
      	    task.find({itemCompleted: false}, function foundTasks(err, items) {
      		  res.render('index',{title: 'My ToDo List ', tasks: items})
    		});
  		  },

  		  addTask: function(req,res) {
    		var item = req.body.item;
    		newTask = new task();
    		newTask.itemName = item.name;
    		newTask.itemCategory = item.category;
    		newTask.save(function savedTask(err){
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

1.  Salvare il file **tasklist.js**.

#### Modificare la visualizzazione dell'indice

1.  Passare alla directory **views** e aprire il file **index.jade** in un editor di testo.

2.  Sostituire il contenuto del file **index.jade** con il codice seguente. Verrà definita la visualizzazione delle attività esistenti, nonché un formato per aggiungere nuove attività e contrassegnare quelle esistenti come completate.

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
                 input(name="item[name]", type="textbox")
             tr
               td Item Category: 
               td 
                 input(name="item[category]", type="textbox")
           input(type="submit", value="Add item")

3.  Salvare e chiudere il file **index.jade**.

#### Sostituire app.js

1.  Nella directory **tasklist** aprire il file **app.js** in un editor di testo. Questo file è stato creato in precedenza eseguendo il comando **express**.
2.  Aggiungere il codice seguente all'inizio del file **app.js**. Verrà inizializzato **TaskList** con la stringa di connessione per il server MongoDB:

         var TaskList = require('./routes/tasklist');
         var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	Si noti la seconda riga: si accede a una variabile di ambiente che si configurerà in seguito, contenente le informazioni sulla connessione per l'istanza di mongo. Se un'istanza locale di mongo è in esecuzione a scopo di sviluppo, potrebbe essere necessario impostare temporaneamente questo valore su "localhost" anziché su `process.env.CUSTOMCONNSTR_MONGOLAB_URI`.

1.  Trovare le righe che iniziano con `app.get` e sostituirle con le righe seguenti:

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	Verranno aggiunte le funzioni definite in **tasklist.js** come route.

2.  Salvare il file **app.js**.

Distribuzione dell'app
----------------------

Ora che l'applicazione è stata sviluppata, è il momento di creare un sito Web di Azure per ospitarla, configurare tale sito Web e distribuire il codice. L'argomento principale di questa sezione è l'utilizzo della stringa di connessione MongoDB (URI). Si configurerà una variabile di ambiente nel sito Web con questo URI per mantenere l'URI separato dal codice. È consigliabile considerare l'URI come un'informazione riservata in quanto contiene le credenziali per connettersi al database.

Nei passaggi di questa sezione vengono utilizzati gli strumenti da riga di comando di Azure per creare un nuovo sito Web di Azure e quindi viene utilizzato Git per distribuire l'applicazione. Per eseguire questi passaggi, è necessario disporre di una sottoscrizione di Azure.

### Installare lo strumento da riga di comando di Azure per Mac e Linux

Per installare gli strumenti da riga di comando, utilizzare il comando seguente:

	npm install azure-cli -g

Se si è già installato **Azure SDK per Node.js** dal [Centro per sviluppatori Azure](/en-us/develop/nodejs/), gli strumenti da riga di comando saranno già installati. Per ulteriori informazioni, vedere [Strumenti da riga di comando di Azure per Mac e Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/).

Anche se gli strumenti da riga di comando di Azure sono stati creati principalmente per gli utenti Mac e Linux, essendo basati su Node.js, dovrebbero funzionare in qualsiasi sistema in grado di eseguire Node.

### Importare le impostazioni di pubblicazione

Prima di utilizzare gli strumenti da riga di comando con Azure, è necessario scaricare un file contenente informazioni sulla sottoscrizione. Eseguire i passaggi seguenti per scaricare e importare questo file.

1. Nella riga di comando digitare il comando seguente per avviare il browser e passare alla pagina di download. Se richiesto, accedere con l'account associato alla sottoscrizione.

		azure account download
	
	![Pagina di download](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png)
	
	Il download del file dovrebbe iniziare automaticamente. In caso contrario, è possibile fare clic sul collegamento all'inizio della pagina per scaricare manualmente il file.

2. Al termine del download del file, utilizzare il comando seguente per importare le impostazioni:

		azure account import <path-to-file>

	Specificare il percorso e il nome del file delle impostazioni di pubblicazione scaricato nel passaggio precedente. Dopo il completamento del comando, l'output dovrebbe essere simile al seguente:
	
		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/user1/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


3.  Al termine dell'importazione, è consigliabile eliminare il file delle impostazioni di pubblicazione perché non è più necessario e contiene informazioni riservate relative alla sottoscrizione di Azure.

### Creare un nuovo sito Web ed eseguire il push del codice

Creare un sito Web in Azure è molto facile. Se si tratta del primo sito Web di Azure, è necessario utilizzare il portale. Se si dispone già di almeno un sito, andare al passaggio 7.

1. Nel portale di Azure fare clic su **New**.    
![New](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png)
2. Selezionare **Compute \> Web Site \> Quick Create**. 
![CreateSite](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png)
3.  Immettere un prefisso URL. Scegliere il nome desiderato, ma tenere presente che deve essere univoco. "mymongoapp" non sarà probabilmente disponibile.
4.  Fare clic su **Create Web Site**.
5.  Al termine della creazione del sito Web, fare clic sul nome del sito Web nell'elenco di siti Web. Verrà visualizzato il dashboard del sito Web.  
![WebSiteDashboard](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png)
6.  Fare clic su **Set up Git publishing** in **quick glance** e immettere il nome utente e la password git desiderati. Si utilizzerà questa password durante il push nel sito Web (nel passaggio 9).
7.  Se il sito Web è stato creato attenendosi ai passaggi precedenti, il comando seguente completerà il processo. Se, tuttavia, si dispone già di più di un sito Web di Azure, è possibile saltare i passaggi precedenti e creare un nuovo sito Web mediante questo stesso comando. Nella directory del progetto **tasklist**:

		azure site create myuniquesitename --git  
	Sostituire "myuniquesitename" con il nome univoco del sito Web. Se il sito Web viene creato come parte di questo comando, verrà richiesto di specificare il data center in cui si troverà il sito. Selezionare il data center geograficamente vicino al database di MongoLab.

    Il parametro `--git` creerà:
	A. un archivio git locale nella cartella **tasklist**, se non esiste già.
	A. un oggetto [Git remote](http://git-scm.com/docs/git-remote) denominato "azure", che verrà utilizzato per pubblicare l'applicazione in Azure.
	A. un file [iisnode.yml](https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml), contenente le impostazioni utilizzate da Azure per ospitare le applicazioni Node.
	A. un file con estensione gitignore per impedire la pubblicazione della cartella node-modules nel file con estensione git.

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

8.  Utilizzare i comandi seguenti per aggiungere e quindi confermare i file nell'archivio Git locale:

		git add .
		git commit -m "adding files"

9.  Eseguire il push del codice:

		git push azure master  
	Durante il push delle ultime modifiche apportate all'archivio Git nel sito Web di Azure, è necessario specificare che il ramo di destinazione è **master** perché viene utilizzato per il contenuto del sito Web. Se viene richiesto di specificare una password, immettere la password creata sopra durante l'impostazione della pubblicazione git per il sito Web.

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
 		 \* [new branch] master -\> master

La procedura è quasi completa.

### Configurare l'ambiente

La variabile di ambiente process.env.CUSTOMCONNSTR\_MONGOLAB\_URI nel codice verrà ora popolata con il valore fornito ad Azure durante il provisioning del database di MongoLab.

#### Ottenere la stringa di connessione MongoLab

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Aggiungere la stringa di connessione alle variabili di ambiente del sito Web

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

Completamento della procedura
-----------------------------

Eseguire `azure site browse` dalla directory del progetto per aprire automaticamente un browser oppure aprire un browser e passare manualmente all'URL del sito Web (myuniquesite.azurewebsites.net):

![Pagina Web con un elenco di attività vuoto](./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png)

Gestione del database
---------------------

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

A questo punto verrà lanciata un'applicazione Node.js supportata da un database MongoDB ospitato da MongoLab. Ora che si dispone di un database MongoLab, è possibile contattare <support@mongolab.com> per eventuali domande o problemi sul database o per assistenza con MongoDB o con il driver del nodo. È ora possibile utilizzare l'applicazione.

