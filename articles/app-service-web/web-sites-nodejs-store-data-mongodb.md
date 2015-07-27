<properties
	pageTitle="Creazione di un'applicazione Node.js in Azure con MongoDB in una macchina virtuale"
	description="Modalità di utilizzo di MongoDB per archiviare i dati in un'applicazione Node. js ospitata in Azure."
	tags="azure-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/23/2015"
	ms.author="mwasson"/>


# Creazione di un'applicazione Node.js in Azure con MongoDB in una macchina virtuale

In questa esercitazione viene illustrato come utilizzare [MongoDB] ospitato in una macchina virtuale di Azure per archiviare i dati e accedervi da un'applicazione [node] ospitata in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) App Web. [MongoDB] è un diffuso database NoSQL open source a prestazioni elevate.

Si acquisiranno le nozioni seguenti:

* Configurare una macchina virtuale che esegue Ubuntu e MongoDB da VM Depot.
* Accedere a MongoDB da un'applicazione Node.
* Come utilizzare l'interfaccia della riga di comando di Azure per creare un'app Web in Azure App Service

In questa esercitazione verrà creata una semplice applicazione di gestione delle attività basata su Web che consente di creare, recuperare e completare le attività. Le attività sono archiviate in MongoDB.

> [AZURE.NOTE]In questa esercitazione viene usata un'istanza di MongoDB installata in una macchina virtuale. Se si desidera utilizzare un'istanza di MongoDB ospitata fornita da MongoLabs, vedere [Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab](store-mongolab-web-sites-nodejs-store-data-mongodb).

I file di progetto per questa esercitazione verranno archiviati in una directory denominata **tasklist** e l'applicazione completata presenterà un aspetto simile al seguente:

![Pagina Web con un elenco di attività vuoto][node-mongo-finished]

> [AZURE.NOTE]Molte delle procedure illustrate di seguito prevedono l'utilizzo della riga di comando. Per tali procedure, utilizzare la riga di comando per il sistema operativo in uso, ad esempio **Windows PowerShell** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione Terminale.

##Prerequisiti

Per le procedure illustrate in questa esercitazione viene usato Node.js, pertanto nell'ambiente di sviluppo è necessario che sia installata una versione recente di [Node.js][node].

È inoltre necessario che il sistema [Git] sia disponibile dalla riga di comando dell'ambiente di sviluppo, in quanto viene usato per distribuire l'applicazione in un sito Web di Azure.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Creazione di una macchina virtuale

<!--
After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

Sebbene sia possibile creare una nuova macchina virtuale e quindi installarvi MongoDB attenendosi il [Guide all'installazione di MongoDB][installguides], è disponibile una macchina virtuale preinstallata con MongoDB in Azure Marketplace. I passaggi seguenti viene illustrato come utilizzare uno dei molti modelli di macchina virtuale.

> [AZURE.NOTE]L'immagine della community usata in questa esercitazione archivia i dati di MongoDB sul disco del sistema operativo. Pur essendo sufficiente per gli scopi dell'esercitazione, l'archiviazione dei dati di MongoDB su un disco dati offre un livello di prestazioni superiore. Per la procedura che illustra come creare una nuova macchina virtuale, incluso un disco dati, e come archiviare i dati di MongoDB sul disco dati, vedere [Installare MongoDB su Linux in Azure][mongodbonazure].

1. Accedere al [Portale di Azure][azureportal].

3. Fare clic su **nuova** > **dati + archiviazione** > **Marketplace**.

	![schermata della selezione di VM Depot][selectdepo]

2. Nella casella di ricerca nella parte superiore, digitare "mongodb", quindi selezionare **v2.2.3 MongoDB su Hardened Ubuntu 12.04 LTS**. Fare clic su **Crea** per continuare.

	![schermata della selezione dell'immagine di MongoDB v2.2.3 su Hardened Ubuntu][selectedimage]

	Fare clic sulla freccia nella parte inferiore per passare alla schermata successiva.

7. Configurare la macchina virtuale **nome Host**, amministratore **nome utente** e **Password**, e **gruppo di risorse**. Fare clic su **configurazione facoltativa**.

	![schermata di configurazione della macchina virtuale][vmconfig]

8. Impostare altri endpoint per la macchina virtuale. Poiché si accederà a MongoDB da questa macchina virtuale, aggiungere un nuovo endpoint utilizzando le informazioni seguenti:

	* Nome - MongoDB
	* Protocollo - TCP
	* Porta pubblica - 27017
	* Porta privata - 27017

	Per esporre il portale Web di MongoDB, aggiungere un altro endpoint utilizzando le informazioni seguenti:

	* Nome - MongoDBWeb
	* Protocollo - TCP
	* Porta pubblica - 28017
	* Porta privata - 28017

	![schermata di configurazione dell'endpoint][vmendpoint]

9. Fare clic su **OK** due volte, quindi fare clic su **Crea** per creare la macchina virtuale.

	Una volta creata la macchina virtuale, il viene visualizzato nella schermata iniziale e che è possibile fare clic per aprire il pannello della macchina virtuale. Dovrebbe essere possibile aprire un web browser per **http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/** per verificare che MongoDB sia in esecuzione. Nella parte inferiore della pagina dovrebbe essere presente un log che visualizza informazioni sul servizio, simile al seguente:

		Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

	Se nel log vengono visualizzati errori, consultare la [documentazione MongoDB][mongodocs] per informazioni sulla risoluzione dei problemi.


##Installazione dei moduli e generazione dello scaffolding

In questa sezione verrà creata una nuova applicazione Node nell'ambiente di sviluppo e verrà utilizzato npm per aggiungere pacchetti di modulo. Per l'applicazione tasklist verranno utilizzati i moduli [Express] e [Mongoose]. Il modulo Express fornisce un framework View Controller per node, mentre Mongoose è un driver per comunicare con MongoDB.

###Installare Express e generare lo scaffolding

1. Dalla riga di comando passare alla directory **tasklist**. Se la directory **tasklist** non esiste, crearla.

	> [AZURE.NOTE]In questa esercitazione si fa riferimento alla cartella **tasklist**. Il percorso completo della cartella è omesso, in quanto la semantica dei percorsi varia da un sistema operativo all'altro. È consigliabile creare la cartella in un percorso di facile accesso nel file system locale, ad esempio **~/node/tasklist** o **c:\\node\\tasklist**

2. Immettere il comando seguente per installare il comando express.

	npm install express-generator -g

	> [AZURE.NOTE]Quando si utilizza il '-g' parametro in alcuni sistemi operativi, è possibile che venga visualizzato un errore di ___errore: EPERM, chmod '/ / locale usr/express bin'___ e una richiesta di provare a eseguire l'account come amministratore. In questo caso, usare il comando `sudo` per eseguire npm a un livello di privilegi più elevato.

    L'output di questo comando dovrebbe apparire simile al seguente:

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE]Se si utilizza il parametro '-g' durante l'installazione del modulo Express, questo viene installato a livello globale, affinché sia possibile accedere al comando ___express___ per generare lo scaffolding del sito Web senza dover generare informazioni aggiuntive sul percorso.

4. Per creare lo scaffolding che verrà utilizzato per questa applicazione, utilizzare il comando **express**:

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

3. Copiare il file **tasklist/bin/www** in un file denominato **server.js** nella cartella **tasklist**. In Siti Web di Azure è previsto che il punto di ingresso per un'applicazione Node.js sia **server.js** o **app.js**. Poiché **app.js** esiste già, ma non è il punto di ingresso, è necessario usare **server.js**.

4. Modificare il file **server.js** rimuovendo uno dei caratteri '.' dalla riga seguente.

		var app = require('../app');

	La riga modificata dovrebbe essere simile alla seguente.

		var app = require('./app');

	Questa modifica è necessaria perché **server.js** (in precedenza **bin/www**,) si trova nella stessa cartella del file **app.js** richiesto.

###Installare moduli aggiuntivi

Il file **package.json** è uno di quelli creati dal comando **express**. Questo file contiene un elenco di moduli aggiuntivi necessari per un'applicazione Express. In seguito, quando si distribuirà questa applicazione nelle app Web di Servizio app di Azure, il file verrà usato per determinare i moduli da installare in Azure per supportare l'applicazione.

1. Dalla cartella **tasklist** utilizzare quanto segue per installare i moduli descritti nel file **package.json**:

        npm install

    L'output di questo comando dovrebbe apparire simile al seguente:

		debug@0.7.4 node_modules\debug

		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0

		morgan@1.0.0 node_modules\morgan
		└── bytes@0.2.1

		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.4 (bytes@0.3.0)
		└── type-is@1.1.0 (mime@1.2.11)

		express@4.0.0 node_modules\express
		├── methods@0.1.0
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── fresh@0.2.2
		├── range-parser@1.0.0
		├── buffer-crc32@0.2.1
		├── qs@0.6.6
		├── cookie@0.1.0
		├── path-to-regexp@0.1.2
		├── send@0.2.0 (mime@1.2.11)
		├── type-is@1.0.0 (mime@1.2.11)
		├── accepts@1.0.0 (negotiator@0.3.0, mime@1.2.11)
		└── serve-static@1.0.1 (send@0.1.4)

		jade@1.3.1 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.0 (uglify-js@2.4.13)
		├── with@3.0.0 (uglify-js@2.4.13)
		└── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)

	Verranno installati tutti i moduli predefiniti usati da un'applicazione Express.

2. Immettere quindi il comando seguente per installare il modulo Mongoose localmente e per salvare una voce per il modulo nel file **package.json**:

		npm install mongoose --save

	L'output di questo comando dovrebbe apparire simile al seguente:

		mongoose@3.8.8 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.5
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.5.3
		└── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)

    > [AZURE.NOTE]È possibile ignorare qualsiasi messaggio sull'installazione del parser bson C++.

##Utilizzo di MongoDB in un'applicazione Node

In questa sezione si estenderà l'applicazione di base creata dal comando **express** aggiungendo un file **task.js** contenente il modello per le attività. Si modificherà inoltre il file **app.js** esistente e si creerà un nuovo file controller **tasklist.js** per utilizzare il modello.

### Creare il modello

1. Nella directory **tasklist** creare una nuova directory denominata **models**.

2. Nella directory **models** creare un nuovo file denominato **task.js**. Questo file conterrà il modello per le attività create dall'applicazione.

3. All'inizio del file **task.js**, aggiungere il codice seguente per fare riferimento alle librerie necessarie:

        var mongoose = require('mongoose'),
	        Schema = mongoose.Schema;

4. Quindi, aggiungere il codice per definire ed esportare il modello. Il modello verrà usato per eseguire interazioni con il database MongoDB.

        var TaskSchema = new Schema({
	        itemName      : String,
	        itemCategory  : String,
	        itemCompleted : { type: Boolean, default: false },
	        itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. Salvare e chiudere il file **task.js**.

###Creare il controller

1. Nella directory **tasklist/routes** creare un nuovo file denominato **tasklist.js** e aprirlo in un editor di testo.

2. Aggiungere il codice seguente al file **tasklist.js**. Verranno caricati il modulo mongoose e il modello di attività definito in **task.js**. La funzione TaskList viene utilizzata per creare la connessione al server MongoDB in base al valore **connection**:

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

2. Continuare ad aggiungere codice al file **tasklist.js** aggiungendo i metodi **showTasks**, **addTask** e **completeTasks**:

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

3. Salvare il file **tasklist.js**.

### Modificare il file app.js

1. Nella directory **tasklist** aprire il file **app.js** in un editor di testo. Questo file è stato creato in precedenza eseguendo il comando **express**.

2. Aggiungere il codice seguente all'inizio del file **app.js**. Verrà inizializzato **TaskList** con la stringa di connessione per il server MongoDB:

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	Si noti la seconda riga: si accede a una variabile di ambiente che si configurerà in seguito, contenente le informazioni sulla connessione per l'istanza di mongo. Se un'istanza locale di mongo è in esecuzione a scopo di sviluppo, potrebbe essere necessario impostare temporaneamente questo valore su "localhost" anziché su process.env.MONGODB_URI.

3. Trovare le righe seguenti:

		app.use('/', routes);
		app.use('/users', users);

	Sostituire le due righe precedenti con le seguenti:

    	app.get('/', taskList.showTasks.bind(taskList));
    	app.post('/addtask', taskList.addTask.bind(taskList));
    	app.post('/completetask', taskList.completeTask.bind(taskList));

	Verranno aggiunte le funzioni definite in **tasklist.js** come route.

4. Salvare il file **app.js**.

###Modificare la visualizzazione dell'indice

1. Passare alla directory **views** e aprire il file **index.jade** in un editor di testo.

2. Sostituire il contenuto del file **index.jade** con il codice seguente. Verrà definita la visualizzazione delle attività esistenti, nonché un formato per aggiungere nuove attività e contrassegnare quelle esistenti come completate.

		h1= title
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

3. Salvare e chiudere il file **index.jade**.

<!-- ##Run your application locally

To test the application on your local machine, perform the following steps:

1. From the command-line, change directories to the **tasklist** directory.

2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.

	On a Windows system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	On an OS X or Linux-based system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.

2. Use the following command to launch the application locally:

        node app.js

3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. The page should update to display the item in the ToDo List table.

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.

7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

##Distribuzione dell'applicazione in Azure

Nei passaggi di questa sezione vengono usati gli strumenti da riga di comando di Azure per creare una nuova app Web in Servizio app di Azure e quindi viene usato Git per distribuire l'applicazione. Per eseguire questi passaggi, è necessario disporre di una sottoscrizione di Azure.

> [AZURE.NOTE]È possibile eseguire queste procedure anche nel portale di Azure. Per altre informazioni sulla distribuzione di un'applicazione Node.js tramite il portale di Azure, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure](web-sites-nodejs-develop-deploy-mac.md).

> [AZURE.NOTE]Se questo è il primo sito Web di Azure che si crea, per distribuire l'applicazione è necessario utilizzare il portale di Azure.

###Installare l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure consente di eseguire operazioni di gestione per i servizi di Azure. Se tale interfaccia non è stata già installata e configurata nell'ambiente di produzione, vedere [Installare e configurare la riga di comando di Azure](../xplat-cli-install.md).

###Creare un piano di servizio app

1. Dalla riga di comando passare alla directory **tasklist**.

2. Utilizzare il comando seguente per creare un nuovo sito Web di Azure. Sostituire 'myuniqueappname' con un nome univoco per l'applicazione web. Questo valore è utilizzato come parte dell'URL per il sito Web risultante.

		azure site create myuniqueappname --git

	Verrà richiesto per il Data Center che l'applicazione web si troverà in. Selezionare il data center geograficamente vicino al database MongoLab.

	Il parametro `--git`￼￼￼ creerà un repository Git locale nella cartella **tasklist**, se non ne esiste giù uno. Creerà anche un [archivio Git remoto] denominato 'azure' che verrà utilizzato per pubblicare l'applicazione in Azure, oltre a un file [iisnode.yml] contenente le impostazioni utilizzate da Azure per ospitare le applicazioni Node. Infine, creerà un file .gitignore per escludere la cartella dei moduli di Node dalla pubblicazione su .git.

	> [AZURE.NOTE]Se questo comando viene eseguito da una directory che contiene già un archivio Git, la directory non verrà reinizializzata.

	> [AZURE.NOTE]Se il parametro '--git' viene omesso, ma la directory contiene un repository Git, il repository 'azure' verrà comunque creato.

	Dopo il completamento di questo comando, verrà visualizzato un output simile al seguente. Si noti che la riga che inizia con **Created web site at** contiene l'URL del sito Web.

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

	> [WACOM.NOTE> Se questo è il primo sito Web di Azure per la sottoscrizione, verrà indicato di utilizzare il portale per la creazione del sito Web. Per ulteriori informazioni, vedere [compilare e distribuire un'applicazione web di Node. js in Azure App servizio](web-sites-nodejs-develop-deploy-mac.md).

###Impostare la variabile di ambiente MONGODB_URI

L'applicazione prevede che la stringa di connessione per l'istanza MongoDB sia disponibile nella variabile di ambiente MONGODB_URL. Per impostare questo valore per il sito Web, utilizzare il comando seguente:

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Verrà creata una nuova impostazione dell'applicazione per il sito Web che verrà quindi utilizzata per popolare la variabile di ambiente MONGODB_URI letta dal sito Web. Sostituire il valore di 'mymongodb.cloudapp.net' con il nome della macchina virtuale in cui è stato installato MongoDB.

###Pubblicare l'applicazione

1. Nella finestra del terminale passare alla directory **tasklist** se non è già stato fatto.

2. Utilizzare i comandi seguenti per aggiungere e quindi confermare i file nell'archivio Git locale:

		git add .
		git commit -m "adding files"

3. Durante il push delle ultime modifiche apportate all'app Web in Servizio app di Azure, è necessario specificare che il ramo di destinazione è **master** perché viene usato per il contenuto dell'app Web.

		git push azure master

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

4. Al termine dell'operazione di push, passare al sito Web usando il comando `azure site browse` per visualizzare l'applicazione.

##Passaggi successivi

Le procedure in questo articolo descrivono l'utilizzo di MongoDB per archiviare le informazioni. Per questo stesso scopo è tuttavia possibile utilizzare il servizio tabelle di Azure. Per ulteriori informazioni, vedere l'articolo relativo all'[applicazione Web Node.js con il servizio tabelle di Azure].

Per informazioni su come utilizzare un'istanza ospitata di MongoDB fornita da MongoLabs, vedere [Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab](store-mongolab-web-sites-nodejs-store-data-mongodb.md).

Per ulteriori informazioni su come proteggere MongoDB, vedere l'articolo relativo alla [sicurezza di MongoDB][mongosecurity].

##Risorse aggiuntive

[Strumento da riga di comando di azure per Mac e Linux][Build and deploy a Node.js web app in Azure App Service] [la distribuzione continua utilizzando GIT nel servizio di Azure App]

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[archivio Git remoto]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure command-line tool for Mac and Linux]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Build and deploy a Node.js web app in Azure App Service]: web-sites-nodejs-develop-deploy-mac.md
[la distribuzione continua utilizzando GIT nel servizio di Azure App]: web-sites-publish-source-control.md
[applicazione Web Node.js con il servizio tabelle di Azure]: storage-nodejs-use-table-storage-web-site.md
[node-mongo-finished]: ./media/web-sites-nodejs-store-data-mongodb/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/web-sites-nodejs-store-data-mongodb/todo_add_item.png
[node-mongo-list-items]: ./media/web-sites-nodejs-store-data-mongodb/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://portal.azure.com
[mongodocs]: http://docs.mongodb.org/manual/
[Azure CLI]: ../xplat-cli.md

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
 

<!---HONumber=July15_HO3-->