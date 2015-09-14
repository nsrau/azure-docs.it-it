<properties 
	pageTitle="App Web con il servizio di archiviazione tabelle (Node.js) | Microsoft Azure"
	description="Esercitazione basata sull'esercitazione per la creazione di un'app Web con Express e in cui vengono aggiunti i servizi di archiviazione di Azure e il modulo di Azure."
	services="cloud-services, storage"
	documentationCenter="nodejs"
	authors="TomArcher"
	manager="wpickett"
	editor=""/>

<tags 
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="09/01/2015"
	ms.author="tarcher"/>






# Creazione di un'applicazione Web Node.js con Archiviazione

## Panoramica

In questa esercitazione, si estenderà l'applicazione creata nell'esercitazione [Creazione di un'applicazione Node.js con Express] utilizzando le librerie client di Microsoft Azure per Node.js per lavorare con i servizi di gestione dati. L'applicazione verrà estesa in modo da creare un'applicazione elenco di attività basata sul Web che sarà possibile distribuire in Azure. L'elenco di attività consente a un utente di recuperare le attività, aggiungerne di nuove e contrassegnarle come completate.

Gli elementi attività vengono archiviati in Archiviazione di Azure. Archiviazione di Azure consente l'archiviazione di dati non strutturati, a tolleranza di errore e a disponibilità elevata. Archiviazione di Azure include diverse strutture di dati in cui è possibile archiviare i dati e accedervi ed è possibile utilizzare i servizi di archiviazione dalle API incluse in Azure SDK per Node.js o tramite le API REST. Per ulteriori informazioni, vedere [Archiviazione e accesso ai dati in Azure].

In questa esercitazione si presume che siano state completate le esercitazioni [Creazione e distribuzione di un'applicazione Node.js a un Servizio cloud di Azure] e [Creazione di un'applicazione Web Node.js utilizzando Express in un servizio cloud di Azure][Node.js Web Application using Express].

Si apprenderà come:

-   Usare il motore dei modelli Jade
-   Usare i servizi di gestione dati di Azure

Di seguito è riportata una schermata dell'applicazione completata:

![Pagina Web completata in Internet Explorer](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Impostazione delle credenziali di archiviazione in Web.Config

Per accedere ad Archiviazione di Azure, è necessario passare le credenziali di archiviazione. A questo scopo, si utilizzano le impostazioni dell'applicazione web.config. Tali impostazioni verranno passate a Node come variabili di ambiente, che verranno quindi lette da Azure SDK.

> [AZURE.NOTE]Le credenziali di archiviazione vengono usate solo quando l'applicazione viene distribuita in Azure. L'applicazione, quando viene eseguita nell'emulatore, utilizzerà l'emulatore di archiviazione.

Eseguire i passaggi seguenti per recuperare le credenziali dell'account di archiviazione e aggiungerle alle impostazioni di web.config:

1.  Se non è già aperta, avviare Azure PowerShell dal menu **Start** espandendo **Tutti i programmi, Azure**, fare clic con il pulsante destro del mouse su **Azure PowerShell** e quindi scegliere **Esegui come amministratore**.

2.  Sostituire le directory con la cartella contenente l'applicazione. Ad esempio, C:\\node\\tasklist\\WebRole1.

3.  Nella finestra Azure Powershell immettere il cmdlet seguente per recuperare le informazioni account di archiviazione:

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

	Verrà recuperato l'elenco di account di archiviazione e di chiavi dell'account associati al servizio ospitato.

	> [AZURE.NOTE]Dal momento che Azure SDK crea un account di archiviazione quando si distribuisce un servizio, esisterà già un account di archiviazione dalla distribuzione dell'applicazione nelle guide precedenti.

4.  Aprire il file **ServiceDefinition.csdef** contenente le impostazioni dell'ambiente usate quando l'applicazione viene distribuita in Azure:

        PS C:\node\tasklist> notepad ServiceDefinition.csdef

5.  Inserire il blocco seguente sotto l'elemento **Environment**, sostituendo {STORAGE ACCOUNT} e {STORAGE ACCESS KEY} con il nome account e con la chiave primaria per l'account di archiviazione che si intende usare per la distribuzione:

        <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
        <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

	![Contenuto file web.cloud.config](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  Salvare il file e chiudere Blocco note.

### Installare moduli aggiuntivi

2. Usare quindi il comando seguente per installare i moduli [azure], [node-uuid], [nconf] e [async] in locale e per salvare una voce per tali moduli nel file **package.json**:

		PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save

	L'output di questo comando dovrebbe apparire simile al seguente:

		node-uuid@1.4.1 node_modules\node-uuid

		nconf@0.6.9 node_modules\nconf
		├── ini@1.1.0
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

##Uso del servizio tabelle in un'applicazione Node

In questa sezione si estenderà l'applicazione di base creata dal comando **express** aggiungendo un file **task.js** contenente il modello per le attività. Si modificherà inoltre il file **app.js** esistente e si creerà un nuovo file **tasklist.js** che utilizzi il modello.

### Creare il modello

1. Nella directory **WebRole1** creare una nuova directory denominata **models**.

2. Nella directory **models** creare un nuovo file denominato **task.js**. Questo file conterrà il modello per le attività create dall'applicazione.

3. All'inizio del file **task.js**, aggiungere il codice seguente per fare riferimento alle librerie necessarie:

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
		    self.storageClient.queryEntities(query, function entitiesQueried(error, result) {
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

### Creare il controller

1. Nella directory **WebRole1/routes** creare un nuovo file denominato **tasklist.js** e aprirlo in un editor di testo.

2. Aggiungere il seguente codice al file **tasklist.js**. Ciò consente il caricamento dei moduli di Azure e di quelli asincroni, utilizzati da **tasklist.js**. Viene inoltre definita la funzione **TaskList**, che viene passata come un'istanza dell'oggetto **Task** definito in precedenza:

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
		    var query = azure.TableQuery()
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

1. Nella directory **WebRole1** aprire il file **app.js** in un editor di testo. 

2. Aggiungere quanto riportato di seguito all'inizio del file per caricare il modulo azure e impostare il nome della tabella e la chiave della partizione:

		var azure = require('azure-storage');
		var tableName = 'tasks';
		var partitionKey = 'hometasks';

3. Nel file app.js scorrere verso il basso fino a individuare la riga seguente:

		app.use('/', routes);
		app.use('/users', users);

	Sostituire le righe sopra con il codice seguente. Verrà in tal modo inizializzata un'istanza di <strong>Task</strong> con una connessione all'account di archiviazione. Questa viene quindi passata a <strong>TaskList</strong>, che la userà per la comunicazione con il servizio tabelle:

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));
	
4. Salvare il file **app.js**.

### Modificare la visualizzazione dell'indice

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
		      if tasks != []
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

### Modificare il layout globale

Il file **layout.jade** della directory **views** viene usato come modello globale per altri file **.jade**. In questo passaggio verrà modificato in modo da usare [Twitter Bootstrap](https://github.com/twbs/bootstrap), un toolkit che semplifica la progettazione di un sito Web di aspetto gradevole.

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

### Esecuzione dell'applicazione nell'emulatore

Usare il comando seguente per avviare l'applicazione nell'emulatore.

	PS C:\node\tasklist\WebRole1> start-azureemulator -launch

Si aprirà il browser e verrà visualizzata la pagina seguente:

![Pagina Web intitolata My Task List con una tabella contenente le attività e i campi per aggiungere una nuova attività.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

Usare il modulo per aggiungere elementi o rimuovere elementi esistenti contrassegnandoli come completati.

## Pubblicazione dell'applicazione in Azure


Nella finestra di Windows PowerShell chiamare il cmdlet seguente per ridistribuire il servizio ospitato in Azure.

    PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

Sostituire **myuniquename** con un nome univoco per questa applicazione. Sostituire **datacentername** con il nome di un data center di Azure, ad esempio **West US**.

Al termine della distribuzione, verrà visualizzata una risposta analoga alla seguente:

	PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
	WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
	WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
	WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
	WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
	WARNING: 2:19:01 PM - Connecting...
	WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
	WARNING: 2:19:40 PM - Upgrading...
	WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
	WARNING: 2:22:48 PM - Initializing...
	WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
	WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.

Come in precedenza, poiché è stata specificata l'opzione **-launch**, il browser si apre e visualizza l'applicazione in esecuzione in Azure al termine della pubblicazione.

![Finestra del browser in cui è visualizzata la pagina My Task List. L'URL indica che la pagina è ora ospitata su Azure.](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Arresto ed eliminazione dell'applicazione

Dopo aver distribuito l'applicazione, se necessario è possibile disabilitarla per evitare costi o creare e distribuire altre applicazioni entro il termine del periodo di valutazione gratuita.

Azure addebita le istanze del ruolo Web al consumo, in base all'utilizzo di tempo del server su base oraria. Un'applicazione distribuita utilizza tempo del server anche se le istanze non sono in esecuzione e sono in stato arrestato.

Nella procedura seguente viene illustrato come arrestare ed eliminare l'applicazione.

1.  Nella finestra di Windows PowerShell arrestare la distribuzione del servizio creata nella sezione precedente con il seguente cmdlet:

        PS C:\node\tasklist\WebRole1> Stop-AzureService

	L'arresto del servizio può richiedere diversi minuti. Dopo l'arresto del servizio, viene visualizzato un messaggio di conferma dell'arresto.

3.  Per eliminare il servizio, chiamare il cmdlet seguente:

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

	Quando richiesto, immettere **Y** per eliminare il servizio.

	L'eliminazione del servizio può richiedere diversi minuti. Al termine dell'eliminazione del servizio, verrà visualizzato un messaggio di conferma dell'eliminazione.

  [Node.js Web Application using Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Creazione di un'applicazione Node.js con Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Creazione e distribuzione di un'applicazione Node.js a un Servizio cloud di Azure]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/
 
 

<!---HONumber=September15_HO1-->