<properties 
	pageTitle="Sito Web Node.js con MongoDB su una macchina virtuale - Esercitazione di Azure" 
	description="Un'esercitazione che illustra come usare MongoDB per memorizzare e accedere ai dati da un'applicazione Node ospitata in Azure." 
	services="web-sites, virtual-machines" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	writer="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>


# Creazione di un'applicazione Node.js in Azure con MongoDB in una macchina virtuale

In questa esercitazione viene illustrato come usare [MongoDB] ospitato in una macchina virtuale di Azure per archiviare i dati e accedervi da un'applicazione [node] ospitata in un sito Web di Azure. [MongoDB] è un diffuso database NoSQL open source a prestazioni elevate.

Si apprenderà come:

* Configurare una macchina virtuale che esegue Ubuntu e MongoDB da VM Depot.
* Accedere a MongoDB da un'applicazione Node.
* Usare gli strumenti multipiattaforma per Azure per creare un sito Web di Azure.

In questa esercitazione verrà creata una semplice applicazione di gestione delle attività basata su Web che consente di creare, recuperare e completare le attività. Le attività sono archiviate in MongoDB.

> [AZURE.NOTE] In questa esercitazione viene usata un'istanza di MongoDB installata in una macchina virtuale. Se si desidera usare un'istanza di MongoDB ospitata fornita da MongoLabs, vedere <a href="/it-it/develop/nodejs/tutorials/website-with-mongodb-mongolab/">Creare un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab</a>.
 
I file di progetto per questa esercitazione verranno archiviati in una directory denominata **tasklist** e l'applicazione completata presenterà un aspetto simile al seguente:

![A web page displaying an empty tasklist][node-mongo-finished]

> [AZURE.NOTE] Molte delle procedure illustrate di seguito prevedono l'uso della riga di comando. Per tali procedure, usare la riga di comando per il sistema operativo in uso, ad esempio __Windows PowerShell__ (Windows) o __Bash__ (Unix Shell). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione Terminale.

##Prerequisiti

Per le procedure illustrate in questa esercitazione viene usato Node.js, pertanto nell'ambiente di sviluppo è necessario che sia installata una versione recente di [Node.js][node].

È inoltre necessario che il sistema [Git] sia disponibile dalla riga di comando dell'ambiente di sviluppo, in quanto viene usato per distribuire l'applicazione in un sito Web di Azure.

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##Creare una macchina virtuale

<!--In questa esercitazione si presuppone che l'utente abbia creato una macchina virtuale in Azure. Dopo aver creato la macchina virtuale è necessario installarvi MongoDB:

* In alternativa, per creare una macchina virtuale Linux e installare MongoDB, vedere [Installazione di MongoDB in una macchina virtuale Linux].

Dopo aver creato la macchina virtuale in Azure e installato MongoDB, prendere nota del nome DNS della macchina virtuale (ad esempio; "testlinuxvm.cloudapp.net") e della porta esterna per MongoDB specificata nell'endpoint.  Queste informazioni saranno necessarie più avanti nell'esercitazione..-->

Sebbene sia possibile creare una nuova macchina virtuale e quindi installarvi MongoDB attenendosi alle [guide all'installazione di MongoDB][installguides], la maggior parte del lavoro è già stata svolta dalla community ed è disponibile in VM Depot. La seguente procedura illustra come usare un'immagine in cui è già installato e configurato MongoDB da VM Depot. 

> [AZURE.NOTE] L'immagine della community usata in questa esercitazione archivia i dati di MongoDB sul disco del sistema operativo. Pur essendo sufficiente per gli scopi dell'esercitazione, l'archiviazione dei dati di MongoDB su un disco dati offre un livello di prestazioni superiore. Per la procedura che illustra come creare una nuova macchina virtuale, incluso un disco dati, e come archiviare i dati di MongoDB sul disco dati, vedere [Installare MongoDB su Linux in Azure][mongodbonazure].

1. Accedere al [portale di gestione di Azure][azureportal], selezionare __Virtual Machines__, __Images__ e quindi __VM Depot__.

	![screenshot of selecting VM Depot][selectdepo]

2. Selezionare un'immagine che includa MongoDB. In questo caso, è stato selezionato Ubuntu per limitare l'elenco alle sole immagini basate sulla distribuzione di Ubuntu Linux. Infine, selezionare un'immagine di MongoDB v2.2.3 su Hardened Ubuntu

	![screenshot of selected mongodb v2.2.3 on hardened ubuntu image][selectedimage]

	> [AZURE.NOTE] Accertarsi di selezionare __More__ per visualizzare tutte le informazioni relative all'immagine. Alcune immagini potrebbero richiedere configurazioni aggiuntive da specificare dopo la creazione della macchina virtuale tramite l'immagine.

	Fare clic sulla freccia nella parte inferiore per passare alla schermata successiva.

3. Selezionare l'area e l'account di archiviazione che verranno usati per archiviare il disco rigido virtuale per l'immagine. Fare clic sul segno di spunta per continuare.
	
	![screenshot of choose a storage account][selectstorage]

	> [AZURE.NOTE] Verrà avviato un processo di copia che copia l'immagine da VM Depot all'account di archiviazione specificato. L'operazione può richiedere anche 15 minuti o più.

4. Quando lo stato dell'immagine passa a __Pending registration__, selezionare __Register__ e digitare un nome descrittivo per la nuova immagine. Fare clic sul segno di spunta per continuare.

	![screenshot of registering an image][register]

5. Quando lo stato dell'immagine passa a __Available__, selezionare __+ New__, __Virtual Machine__ e __From Gallery__. Quando viene visualizzata l'opzione __Choose an Image__, selezionare __My Images__ e quindi l'immagine creata nei passaggi precedenti. Fare clic sulla freccia per continuare.

	![screenshot of the image][myimage]

6. Specificare il nome, le dimensioni e il nome utente della macchina virtuale. Fare clic sulla freccia per continuare.

	![screenshot of the vm name, user name, etc.][vmname]

	>[AZURE.NOTE] Per questa esercitazione, non è necessario usare il protocollo SSH per la connessione remota alla macchina virtuale. Selezionare **Use a password** e fornire una password se non si ha familiarità con l'uso dei certificati tramite SSH.
	>
	> Per altre informazioni sull'uso del protocollo SSH per accedere a una macchina virtuale Linux in Azure, vedere [Come usare SSH con Linux in Azure][sshazure].

7. Selezionare se usare un servizio cloud nuovo o esistente e l'area in cui verrà creata la macchina virtuale. Fare clic sulla freccia per continuare.

	![screenshot of the vm configuration][vmconfig]

8. Impostare altri endpoint per la macchina virtuale. Poiché si accederà a MongoDB da questa macchina virtuale, aggiungere un nuovo endpoint usando le seguenti informazioni:

	* Name - MongoDB
	* Protocol - TCP
	* Public port - 27017
	* private port - 27017

	Per esporre il portale Web di MongoDB, aggiungere un altro endpoint usando le seguenti informazioni:

	* Name - MongoDBWeb
	* Protocol - TCP
	* Public port - 28017
	* Private port - 28017
	
	Infine, fare clic sul segno di spunta per configurare la macchina virtuale.

	![screenshot of the endpoint configuration][vmendpoint]

9. Quando lo stato della macchina virtuale passa a __Running__, dovrebbe essere possibile aprire un browser Web all'indirizzo __http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/__ per verificare che MongoDB sia in esecuzione. Nella parte inferiore della pagina dovrebbe essere presente un log che visualizza informazioni sul servizio, simile al seguente:

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

In questa sezione verrà creata una nuova applicazione Node nell'ambiente di sviluppo e verrà usato npm per aggiungere pacchetti di modulo. Per l'applicazione tasklist verranno usati i moduli [Express] e [Mongoose]. Il modulo Express fornisce un framework View Controller per node, mentre Mongoose è un driver per comunicare con MongoDB.

###Installare Express e generare lo scaffolding

1. Dalla riga di comando passare alla directory **tasklist**. Se la directory **tasklist** non esiste, crearla.

	> [AZURE.NOTE] In questa esercitazione si fa riferimento alla cartella __tasklist__. Il percorso completo della cartella è omesso, in quanto la semantica dei percorsi varia da un sistema operativo all'altro. È consigliabile creare la cartella in un percorso di facile accesso nel file system locale, ad esempio __~/node/tasklist__ o __c:\node\tasklist_.

2. Immettere il seguente comando per installare il comando express.

	npm install express-generator -g
 
	> [AZURE.NOTE] Quando si usa il parametro '-g' in alcuni sistemi operativi, è possibile che venga restituito l'errore ___Error: EPERM, chmod '/usr/local/bin/express'___ con la richiesta di provare a eseguire l'account come amministratore. In questo caso, usare il comando  `sudo` per eseguire npm a un livello di privilegi più elevato.

    L'output di questo comando dovrebbe apparire simile al seguente:

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)                                                                         
 
	> [AZURE.NOTE] Se si usa il parametro '-g' durante l'installazione del modulo Express, questo viene installato a livello globale, affinché sia possibile accedere al comando ___express___ per generare lo scaffolding del sito Web senza dover generare informazioni aggiuntive sul percorso.

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

3. Copiare il file **tasklist/bin/www** in un file denominato **server.js** nella cartella **tasklist**. In Siti Web di Azure è previsto che il punto di ingresso per un'applicazione Node.js sia **server.js** o **app.js**. Poiché **app.js** esiste già, ma non è il punto di ingresso, è necessario usare **server.js**.

4. Modificare il file **server.js** rimuovendo uno dei caratteri '.' dalla seguente riga.

		var app = require('../app');

	La riga modificata dovrebbe essere simile alla seguente.

		var app = require('./app');

	Questa modifica è necessaria perché **server.js** (in precedenza **bin/www**) si trova ora nella stessa cartella del file **app.js** richiesto.

###Installare moduli aggiuntivi

Il file **package.json** è uno di quelli creati dal comando **express**. Questo file contiene un elenco di moduli aggiuntivi necessari per un'applicazione Express. In seguito, quando si distribuirà questa applicazione in un sito Web di Azure, il file verrà usato per determinare quali moduli devono essere installati in Azure per supportare l'applicazione.
	
1. Dalla cartella **tasklist** usare quanto segue per installare i moduli descritti nel file **package.json**:

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

2. Immettere quindi il seguente comando per installare il modulo Mongoose localmente e per salvare una voce per il modulo nel file **package.json**:

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

    > [AZURE.NOTE] È possibile ignorare qualsiasi messaggio sull'installazione del parser bson C++.

##Uso di MongoDB in un'applicazione Node

In questa sezione si estenderà l'applicazione di base creata dal comando **express** aggiungendo un file **task.js** contenente il modello per le attività. Si modificherà inoltre il file **app.js** esistente e si creerà un nuovo file controller **tasklist.js** per usare il modello.

### Creare il modello

1. Nella directory **tasklist** creare una nuova directory denominata **models**.

2. Nella directory **models** creare un nuovo file denominato **task.js**. Questo file conterrà il modello per le attività create dall'applicazione.

3. All'inizio del file **task.js** aggiungere il seguente codice per fare riferimento alle librerie necessarie:

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

2. Aggiungere il seguente codice al file **tasklist.js**. Verranno caricati il modulo mongoose e il modello di attività definito in **task.js**. La funzione TaskList viene usata per creare la connessione al server MongoDB in base al valore **connection**:

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

2. Aggiungere il seguente codice all'inizio del file **app.js**. Verrà inizializzato **TaskList** con la stringa di connessione per il server MongoDB:

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	Si noti la seconda riga: si accede a una variabile di ambiente che si configurerà in seguito, contenente le informazioni sulla connessione per l'istanza di mongo. Se un'istanza locale di mongo è in esecuzione a scopo di sviluppo, può essere utile impostare temporaneamente questo valore su "localhost" anziché su process.env.MONGODB_URI.

3. Trovare le seguenti righe:

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

2. Sostituire il contenuto del file **index.jade** con il seguente codice. Verrà definita la visualizzazione delle attività esistenti, nonché un formato per aggiungere nuove attività e contrassegnare quelle esistenti come completate.

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

<!-- ##Eseguire l'applicazione in locale

Per eseguire il test dell'applicazione nel computer locale, seguire questa procedura:

1. Dalla riga di comando passare alla directory **tasklist**.

2. Impostare la variabile di ambiente MONGODB_URI nell'ambiente di sviluppo in modo che punti alla macchina virtuale che ospita MongoDB. Nel seguente esempio sostituire __mymongodb__ con il nome della macchina virtuale.

	In un sistema Windows usare quanto segue per impostare la variabile di ambiente.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	In un sistema basato su OS X o Linux, usare quanto segue per impostare la variabile di ambiente.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	In questo modo si indicherà all'applicazione di connettersi a MongoDB nella macchina virtuale __mymongodb.cloudapp.net__ creata in precedenza e di usare un database denominato 'tasks'.

2. Usare il seguente comando per avviare l'applicazione in locale:

        node app.js

3. Aprire un Web browser e passare all'indirizzo http://localhost:3000. Verrà visualizzata una pagina Web simile alla seguente:

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. Usare i campi **Item Name** e **Item Category** per immettere le informazioni, quindi fare clic su **Add item**.

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. La pagina verrà aggiornata e l'elemento verrà visualizzato nella tabella dell'elenco attività.

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. Per completare un'attività, è sufficiente selezionare la casella di controllo nella colonna Complete,quindi fare clic su **Update tasks**. Anche se non viene modificata dopo aver scelto **Update tasks**, la voce relativa al documento in MongoDB ora è stata contrassegnata come completata.

7. Per interrompere il processo di Node, passare alla riga di comando e premere **CTRL** + **C**. -->

##Distribuire l'applicazione in Azure

Nei passaggi di questa sezione vengono usati gli strumenti da riga di comando di Azure per creare un nuovo sito Web di Azure e quindi viene usato Git per distribuire l'applicazione. Per questa procedura, è necessario disporre di una sottoscrizione Azure.

> [AZURE.NOTE] È possibile eseguire queste procedure anche nel portale Azure. Per altre informazioni sulla distribuzione di un'applicazione Node.js tramite il portale Azure, vedere <a href="/it-it/develop/nodejs/tutorials/create-a-website-(mac)/">Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure</a>.

> [AZURE.NOTE] Se questo è il primo sito Web di Azure che si crea, per distribuire l'applicazione è necessario usare il portale Azure.

###Installare l'interfaccia della riga di comando multipiattaforma di Azure

L'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli) consente di effettuare operazioni di gestione per i servizi di Azure. Se l'interfaccia della riga di comando multipiattaforma (xplat-cli) non è ancora stata installata e configurata nell'ambiente di sviluppo, vedere [Installare e configurare l'interfaccia della riga di comando multipiattaforma di Azure][xplatcli] per istruzioni.

###Creare un sito Web di Azure

1. Dalla riga di comando passare alla directory **tasklist**.

2. Usare il seguente comando per creare un nuovo sito Web di Azure. Sostituire  'myuniquesitename' con un nome univoco del sito Web. Questo valore è usato come parte dell'URL per il sito Web risultante.

		azure site create myuniquesitename --git
		
	Verrà richiesto di specificare il data center in cui si troverà il sito. Selezionare il data center geograficamente vicino alla propria posizione.
	
	Il parametro `--git` creerà un repository Git locale nella cartella **tasklist**, se non ne esiste giù uno. Creerà anche un repository [Git remoto] denominato 'azure' che verrà usato per pubblicare l'applicazione in Azure, oltre a un file [iisnode.yml] contenente le impostazioni usate da Azure per ospitare le applicazioni Node. Infine, creerà un file .gitignore per escludere la cartella dei moduli di Node dalla pubblicazione su .git.
	
	> [AZURE.NOTE] Se questo comando viene eseguito da una directory che contiene già un archivio Git, la directory non verrà reinizializzata.
	
	> [AZURE.NOTE] Se il parametro '--git' viene omesso, ma la directory contiene un repository Git, il repository  'azure' verrà comunque creato.
	
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

	> [AZURE.NOTE]> Se questo è il primo sito Web di Azure per la sottoscrizione, verrà indicato di usare il portale per la creazione del sito Web. Per altre informazioni, vedere <a href="/it-it/develop/nodejs/tutorials/create-a-website-(mac)/">Creazione e distribuzione di un sito Web Node.js in Azure</a>.

###Impostare la variabile di ambiente MONGODB_URI

L'applicazione prevede che la stringa di connessione per l'istanza MongoDB sia disponibile nella variabile di ambiente MONGODB_URI. Per impostare questo valore per il sito Web, usare il seguente comando:

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Verrà creata una nuova impostazione dell'applicazione per il sito Web che sarà quindi usata per popolare la variabile di ambiente MONGODB_URI letta dal sito Web. Sostituire il valore di  'mymongodb.cloudapp.net' con il nome della macchina virtuale in cui è stato installato MongoDB.

###Pubblicare l'applicazione

1. Nella finestra del terminale passare alla directory **tasklist** se non è già stato fatto.

2. Usare i seguenti comandi per aggiungere e quindi confermare i file nel repository Git locale:

		git add .
		git commit -m "adding files"

3. Durante il push delle ultime modifiche apportate all'archivio Git nel sito Web di Azure, è necessario specificare che il ramo di destinazione è **master** perché viene usato per il contenuto del sito Web.

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
 
4. Al termine dell'operazione di push, passare al sito Web usando il comando  `azure site browse` per visualizzare l'applicazione.

##Passaggi successivi

Le procedure in questo articolo descrivono l'uso di MongoDB per archiviare le informazioni. Per questo stesso scopo è tuttavia possibile usare il servizio tabelle di Azure. Per altre informazioni, vedere l'articolo relativo all'[applicazione Web Node.js con il servizio tabelle di Azure].

Per informazioni su come usare un'istanza ospitata di MongoDB fornita da MongoLab, vedere [Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab](/it-it/develop/nodejs/tutorials/website-with-mongodb-mongolab/).

Per altre informazioni su come proteggere MongoDB, vedere l'articolo relativo alla [sicurezza di MongoDB][mongosecurity].

##Risorse aggiuntive

[Strumento da riga di comando di Azure per Mac e Linux]    
[Creazione e distribuzione di un'applicazione Node.js in Siti Web di Azure]    
[Pubblicazione in Siti Web di Azure con Git]    

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /it-it/pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Strumento da riga di comando di Azure per Mac e Linux]: /it-it/develop/nodejs/how-to-guides/command-line-tools/
[Centro per sviluppatori di Azure]: /it-it/develop/nodejs/
[Creazione e distribuzione di un'applicazione Node.js in Siti Web di Azure]: /it-it/develop/nodejs/tutorials/create-a-website-(mac)/
[Pubblicazione in Siti Web di Azure con Git]: /it-it/develop/nodejs/common-tasks/publishing-with-git/
[Installazione di MongoDB in una macchina virtuale Linux]: /it-it/manage/linux/common-tasks/mongodb-on-a-linux-vm/
[Applicazione Web Node.js con il servizio tabelle di Azure]: /it-it/develop/nodejs/tutorials/web-site-with-storage/
[node-mongo-finished]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_add_item.png
[node-mongo-list-items]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://manage.windowsazure.com/
[mongodocs]: http://docs.mongodb.org/manual/
[xplatcli]: /it-it/documentation/articles/xplat-cli/

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[sshazure]: http://azure.microsoft.com/documentation/articles/linux-use-ssh-key/
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/ 

<!--HONumber=42-->
