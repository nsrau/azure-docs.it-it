<properties linkid="develop-node-website-with-mongodb-mac" urlDisplayName="Website with MongoDB" pageTitle="Node.js website with MongoDB on a VM - Azure tutorial" metaKeywords="Azure tutorial MongoDB, MongoDB store data, access data MongoDB Node, Azure Node app" description="A tutorial that teaches you how to use MongoDB to store and access data from a Node application hosted on Azure." metaCanonical="http://www.windowsazure.com/it-it/develop/nodejs/tutorials/website-with-mongodb-mongolab/" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Node.js Web Application with Storage on MongoDB (Virtual Machine)" authors="larryfr"  solutions="" writer="" manager="" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Creazione di un'applicazione Node.js in Azure con MongoDB in una macchina virtuale

In questa esercitazione viene illustrato come usare [MongoDB][MongoDB] ospitato in una macchina virtuale di Azure per archiviare i dati e accedervi da un'applicazione [node][node] ospitata in un sito Web di Azure. [MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate.

Si apprenderà come:

-   Configurare una macchina virtuale che esegue Ubuntu e MongoDB da VM Depot.
-   Accedere a MongoDB da un'applicazione Node.
-   Usare gli strumenti multipiattaforma per Azure per creare un sito Web di Azure.

In questa esercitazione verrà creata una semplice applicazione di gestione delle attività basata su Web che consente di creare, recuperare e completare le attività. Le attività sono archiviate in MongoDB.

> [WACOM.NOTE] In questa esercitazione viene usata un'istanza di MongoDB installata in una macchina virtuale. Se si desidera usare un'istanza di MongoDB ospitata fornita da MongoLabs, vedere [Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab][Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab].

I file di progetto per questa esercitazione verranno archiviati in una directory denominata **tasklist** e l'applicazione completata presenterà un aspetto simile al seguente:

![Pagina Web con un elenco di attività vuoto][Pagina Web con un elenco di attività vuoto]

> [WACOM.NOTE] Molte delle procedure illustrate di seguito prevedono l'uso della riga di comando. Per tali procedure, usare la riga di comando per il sistema operativo in uso, ad esempio **Windows PowerShell** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione Terminale.

## Prerequisiti

Per le procedure illustrate in questa esercitazione viene usato Node.js, pertanto nell'ambiente di sviluppo è necessario che sia installata una versione recente di [Node.js][node].

È inoltre necessario che il sistema [Git][Git] sia disponibile dalla riga di comando dell'ambiente di sviluppo, in quanto viene usato per distribuire l'applicazione in un sito Web di Azure.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Creare una macchina virtuale

<!--This tutorial assumes you have created a virtual machine in Azure. After creating the virtual machine you need to install MongoDB on the virtual machine:  * To create a Linux virtual machine and install MongoDB, see [Installing MongoDB on a Linux Virtual machine].  After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

Sebbene sia possibile creare una nuova macchina virtuale e quindi installarvi MongoDB attenendosi alle [guide all'installazione di MongoDB][guide all'installazione di MongoDB], la maggior parte del lavoro è già stata svolta dalla community ed è disponibile in VM Depot. Nella procedura seguente viene illustrato come usare un'immagine in cui è già installato e configurato MongoDB da VM Depot.

> [WACOM.NOTE] L'immagine della community usata in questa esercitazione archivia i dati di MongoDB sul disco del sistema operativo. Pur essendo sufficiente per gli scopi dell'esercitazione, l'archiviazione dei dati di MongoDB su un disco dati offre un livello di prestazioni superiore. Per la procedura che illustra come creare una nuova macchina virtuale, incluso un disco dati, e come archiviare i dati di MongoDB sul disco dati, vedere [Installare MongoDB su Linux in Azure][Installare MongoDB su Linux in Azure].

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], selezionare **Macchine virtuali**, **Immagini** e quindi **VM Depot**.

    ![schermata della selezione di VM Depot][schermata della selezione di VM Depot]

2.  Selezionare un'immagine che includa MongoDB. In questo caso, è stato selezionato Ubuntu per limitare l'elenco alle sole immagini basate sulla distribuzione di Ubuntu Linux. Infine, selezionare un'immagine di MongoDB v2.2.3 su Hardened Ubuntu

    ![schermata della selezione dell'immagine di MongoDB v2.2.3 su Hardened Ubuntu][schermata della selezione dell'immagine di MongoDB v2.2.3 su Hardened Ubuntu]

    > [WACOM.NOTE] Accertarsi di selezionare **More** per visualizzare tutte le informazioni relative all'immagine. Alcune immagini potrebbero richiedere configurazioni aggiuntive da specificare dopo la creazione della macchina virtuale tramite l'immagine.

    Fare clic sulla freccia nella parte inferiore per passare alla schermata successiva.

3.  Selezionare l'area e l'account di archiviazione che verranno usati per archiviare il disco rigido virtuale per l'immagine. Fare clic sul segno di spunta per continuare.

    ![schermata relativa alla scelta dell'account di archiviazione][schermata relativa alla scelta dell'account di archiviazione]

    > [WACOM.NOTE] Verrà avviato un processo di copia che copia l'immagine da VM Depot all'account di archiviazione specificato. L'operazione può richiedere anche 15 minuti o più.

4.  Quando lo stato dell'immagine passa a **Pending registration**, selezionare **Register** e digitare un nome descrittivo per la nuova immagine. Fare clic sul segno di spunta per continuare.

    ![schermata della registrazione dell'immagine][schermata della registrazione dell'immagine]

5.  Quando lo stato dell'immagine passa a **Available**, selezionare **+ New**, **Virtual Machine**, **From Gallery**. Quando viene visualizzata l'opzione **Choose an Image**, selezionare **My Images** e quindi l'immagine creata nei passaggi precedenti. Fare clic sulla freccia per continuare.

    ![schermata dell'immagine][schermata dell'immagine]

6.  Specificare il nome, le dimensioni e il nome utente della macchina virtuale. Fare clic sulla freccia per continuare.

    ![schermata di nome, nome utente della macchina virtuale e così via.][schermata di nome, nome utente della macchina virtuale e così via.]

    > [WACOM.NOTE] Per questa esercitazione, non è necessario usare il protocollo SSH per la connessione remota alla macchina virtuale. Selezionare **Use a password** e fornire una password se non si ha familiarità con l'uso dei certificati tramite SSH.
    >
    > Per altre informazioni sull'uso del protocollo SSH per accedere a una macchina virtuale Linux in Azure, vedere la pagina relativa all'[uso di SSH con Linux in Azure][uso di SSH con Linux in Azure].

7.  Selezionare se usare un servizio cloud nuovo o esistente e l'area in cui verrà creata la macchina virtuale. Fare clic sulla freccia per continuare.

    ![schermata di configurazione della macchina virtuale][schermata di configurazione della macchina virtuale]

8.  Impostare altri endpoint per la macchina virtuale. Poiché si accederà a MongoDB da questa macchina virtuale, aggiungere un nuovo endpoint usando le informazioni seguenti:

    -   Nome - MongoDB
    -   Protocollo - TCP
    -   Porta pubblica - 27017
    -   Porta privata - 27017

    Per esporre il portale Web di MongoDB, aggiungere un altro endpoint usando le informazioni seguenti:

    -   Nome - MongoDBWeb
    -   Protocollo - TCP
    -   Porta pubblica - 28017
    -   Porta privata - 28017

    Infine, fare clic sul segno di spunta per configurare la macchina virtuale.

    ![schermata di configurazione dell'endpoint][schermata di configurazione dell'endpoint]

9.  Quando lo stato della macchina virtuale passa a **In esecuzione**, dovrebbe essere possibile aprire un browser Web all'indirizzo **http://\<YourVMDNSName\>.cloudapp.net:28017** per verificare che MongoDB sia in esecuzione. Nella parte inferiore della pagina dovrebbe essere presente un log che visualizza informazioni sul servizio, simile al seguente:

        Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

    Se nel log vengono visualizzati errori, consultare la [documentazione MongoDB][documentazione MongoDB] per informazioni sulla risoluzione dei problemi.

## Installazione dei moduli e generazione dello scaffolding

In questa sezione verrà creata una nuova applicazione Node nell'ambiente di sviluppo e verrà usato npm per aggiungere pacchetti di modulo. Per l'applicazione tasklist verranno usati i moduli [Express][Express] e [Mongoose][Mongoose]. Il modulo Express fornisce un framework View Controller per node, mentre Mongoose è un driver per comunicare con MongoDB.

### Installare Express e generare lo scaffolding

1.  Dalla riga di comando passare alla directory **tasklist**. Se la directory **tasklist** non esiste, crearla.

    > [WACOM.NOTE] In questa esercitazione si fa riferimento alla cartella **tasklist**. Il percorso completo della cartella è omesso, in quanto la semantica dei percorsi varia da un sistema operativo all'altro. È consigliabile creare la cartella in un percorso di facile accesso nel file system locale, ad esempio **~/node/tasklist** o **c:\\node\\tasklist**

2.  Immettere il comando seguente per installare il comando express.

    npm install express-generator -g

    > [WACOM.NOTE] Durante l'uso del parametro '-g' in alcuni sistemi operativi, è possibile che vengano restituiti l'errore ***Error: EPERM, chmod '/usr/local/bin/express'*** con la richiesta di provare ad eseguire l'account come amministratore. In questo caso, usare il comando `sudo` per eseguire npm a un livello di privilegi più elevato.

    L'output di questo comando dovrebbe apparire simile al seguente:

        express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
        ├── mkdirp@0.3.5
        └── commander@1.3.2 (keypress@0.1.0)                                                                         

    > [WACOM.NOTE] Se si usa il parametro '-g' durante l'installazione del modulo Express, questo viene installato a livello globale, affinché sia possibile accedere al comando ***express*** per generare lo scaffolding del sito Web senza dover generare informazioni aggiuntive sul percorso.

3.  Per creare lo scaffolding che verrà usato per questa applicazione, usare il comando **express**:

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

4.  Copiare il file **tasklist/bin/www** in un file denominato **server.js** nella cartella **tasklist**. In Siti Web di Azure è previsto che il punto di ingresso per un'applicazione Node.js sia **server.js** o **app.js**. Poiché **app.js** esiste già, ma non è il punto di ingresso, è necessario usare **server.js**.

5.  Modificare il file **server.js** rimuovendo uno dei caratteri '.' dalla riga seguente.

        var app = require('../app');

    La riga modificata dovrebbe essere simile alla seguente.

        var app = require('./app');

    Questa modifica è necessaria perché **server.js** (in precedenza **bin/www**,) si trova nella stessa cartella del file **app.js** richiesto.

### Installare moduli aggiuntivi

Il file **package.json** è uno di quelli creati dal comando **express**. Questo file contiene un elenco di moduli aggiuntivi necessari per un'applicazione Express. In seguito, quando si distribuirà questa applicazione in un sito Web di Azure, il file verrà usato per determinare quali moduli devono essere installati in Azure per supportare l'applicazione.

1.  Dalla cartella **tasklist** usare quanto segue per installare i moduli descritti nel file **package.json**:

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

2.  Immettere quindi il comando seguente per installare il modulo Mongoose localmente e per salvare una voce per il modulo nel file **package.json**:

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

    > [WACOM.NOTE] È possibile ignorare qualsiasi messaggio sull'installazione del parser bson C++.

## Uso di MongoDB in un'applicazione Node

In questa sezione si estenderà l'applicazione di base creata dal comando **express** aggiungendo un file **task.js** contenente il modello per le attività. Si modificherà inoltre il file **app.js** esistente e si creerà un nuovo file controller **tasklist.js** per usare il modello.

### Creare il modello

1.  Nella directory **tasklist** creare una nuova directory denominata **models**.

2.  Nella directory **models** creare un nuovo file denominato **task.js**. Questo file conterrà il modello per le attività create dall'applicazione.

3.  All'inizio del file **task.js**, aggiungere il codice seguente per fare riferimento alle librerie necessarie:

        var mongoose = require('mongoose'),
            Schema = mongoose.Schema;

4.  Quindi, aggiungere il codice per definire ed esportare il modello. Il modello verrà usato per eseguire interazioni con il database MongoDB.

        var TaskSchema = new Schema({
            itemName      : String,
            itemCategory  : String,
            itemCompleted : { type: Boolean, default: false },
            itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5.  Salvare e chiudere il file **task.js**.

### Creare il controller

1.  Nella directory **tasklist/routes** creare un nuovo file denominato **tasklist.js** e aprirlo in un editor di testo.

2.  Aggiungere il codice seguente al file **tasklist.js**. Verranno caricati il modulo mongoose e il modello di attività definito in **task.js**. La funzione TaskList viene usata per creare la connessione al server MongoDB in base al valore **connection**:

        var mongoose = require('mongoose'),
            task = require('../models/task.js');

        module.exports = TaskList;

        function TaskList(connection) {
          mongoose.connect(connection);
        }

3.  Continuare ad aggiungere codice al file **tasklist.js** aggiungendo i metodi **showTasks**, **addTask** e **completeTasks**:

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

4.  Salvare il file **tasklist.js**.

### Modificare il file app.js

1.  Nella directory **tasklist** aprire il file **app.js** in un editor di testo. Questo file è stato creato in precedenza eseguendo il comando **express**.

2.  Aggiungere il codice seguente all'inizio del file **app.js**. Verrà inizializzato **TaskList** con la stringa di connessione per il server MongoDB:

        var TaskList = require('./routes/tasklist');
        var taskList = new TaskList(process.env.MONGODB_URI);

    Si noti la seconda riga: si accede a una variabile di ambiente che si configurerà in seguito, contenente le informazioni sulla connessione per l'istanza di mongo. Se un'istanza locale di mongo è in esecuzione a scopo di sviluppo, potrebbe essere necessario impostare temporaneamente questo valore su "localhost" anziché su process.env.MONGODB\_URI.

3.  Trovare le righe seguenti:

        app.use('/', routes);
        app.use('/users', users);

    Sostituire le due righe precedenti con le seguenti:

        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));

    Verranno aggiunte le funzioni definite in **tasklist.js** come route.

4.  Salvare il file **app.js**.

### Modificare la visualizzazione dell'indice

1.  Passare alla directory **views** e aprire il file **index.jade** in un editor di testo.

2.  Sostituire il contenuto del file **index.jade** con il codice seguente. Verrà definita la visualizzazione delle attività esistenti, nonché un formato per aggiungere nuove attività e contrassegnare quelle esistenti come completate.

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

3.  Salvare e chiudere il file **index.jade**.

<!-- ##Run your application locally  To test the application on your local machine, perform the following steps:  1. From the command-line, change directories to the **tasklist** directory.  2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.      On a Windows system, use the following to set the environment variable.          set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks      On an OS X or Linux-based system, use the following to set the environment variable.          set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks         export MONGODB_URI      This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.  2. Use the following command to launch the application locally:          node app.js  3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:      ![A webpage displaying an empty tasklist][node-mongo-finished]  4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.      ![An image of the add item field with populated values.][node-mongo-add-item]  5. The page should update to display the item in the ToDo List table.      ![An image of the new item in the list of tasks][node-mongo-list-items]  6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.  7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

## Distribuire l'applicazione in Azure

Nei passaggi di questa sezione vengono usati gli strumenti da riga di comando di Azure per creare un nuovo sito Web di Azure e quindi viene usato Git per distribuire l'applicazione. Per questa procedura, è necessario disporre di una sottoscrizione di Azure.

> [WACOM.NOTE] È possibile eseguire queste procedure anche nel portale di Azure. Per altre informazioni sulla distribuzione di un'applicazione Node.js tramite il portale di Azure, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure][Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure].

> [WACOM.NOTE] Se questo è il primo sito Web di Azure che si crea, per distribuire l'applicazione è necessario usare il portale di Azure.

### Installare l'interfaccia della riga di comando multipiattaforma di Azure

L'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli) consente di effettuare operazioni di gestione per i servizi di Azure. Se l'interfaccia della riga di comando multipiattaforma (xplat-cli) non è ancora stata installata e configurata nell'ambiente di sviluppo, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure][Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure] per istruzioni.

### Creare un sito Web di Azure

1.  Dalla riga di comando passare alla directory **tasklist**.

2.  Usare il comando seguente per creare un nuovo sito Web di Azure. Sostituire "myuniquesitename" con un nome univoco del sito Web. Questo valore è usato come parte dell'URL per il sito Web risultante.

        azure site create myuniquesitename --git

    Verrà richiesto di specificare il data center in cui si troverà il sito. Selezionare il data center geograficamente vicino alla propria posizione.

    Il parametro `--git` creerà un repository Git locale nella cartella **tasklist**, se non ne esiste giù uno. Creerà anche un [repository Git remoto][repository Git remoto] denominato 'azure' che verrà usato per pubblicare l'applicazione in Azure, oltre a un file [iisnode.yml][iisnode.yml] contenente le impostazioni usate da Azure per ospitare le applicazioni Node. Infine, creerà un file .gitignore per escludere la cartella dei moduli di Node dalla pubblicazione su .git.

    > [WACOM.NOTE] Se questo comando viene eseguito da una directory che contiene già un repository Git, la directory non verrà reinizializzata.

    > [WACOM.NOTE] Se il parametro '--git' viene omesso ma la directory contiene un repository Git, il repository 'azure' verrà comunque creato.

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

    > [WACOM.NOTE\> Se questo è il primo sito Web di Azure per la sottoscrizione, verrà indicato di usare il portale per la creazione del sito Web. Per altre informazioni, vedere [Creazione e distribuzione di un sito Web Node.js in Azure][Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure].

### Impostare la variabile di ambiente MONGODB\_URI

L'applicazione prevede che la stringa di connessione per l'istanza MongoDB sia disponibile nella variabile di ambiente MONGODB\_URI. Per impostare questo valore per il sito Web, usare il comando seguente:

    azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Verrà creata una nuova impostazione dell'applicazione per il sito Web che sarà quindi usata per popolare la variabile di ambiente MONGODB\_URI letta dal sito Web. Sostituire il valore di 'mymongodb.cloudapp.net' con il nome della macchina virtuale in cui è stato installato MongoDB.

### Pubblicare l'applicazione

1.  Nella finestra del terminale passare alla directory **tasklist** se non è già stato fatto.

2.  Usare i comandi seguenti per aggiungere e quindi confermare i file nel repository Git locale:

        git add .
        git commit -m "adding files"

3.  Durante il push delle ultime modifiche apportate a repository Git nel sito Web di Azure, è necessario specificare che il ramo di destinazione è **master** perché viene usato per il contenuto del sito Web.

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

4.  Al termine dell'operazione di push, passare al sito Web usando il comando `azure site browse` per visualizzare l'applicazione.

## Passaggi successivi

Le procedure in questo articolo descrivono l'uso di MongoDB per archiviare le informazioni. Per questo stesso scopo è tuttavia possibile usare il servizio tabelle di Azure. Per altre informazioni, vedere l'articolo relativo all'[applicazione Web Node.js con il servizio tabelle di Azure][applicazione Web Node.js con il servizio tabelle di Azure].

Per informazioni su come usare un'istanza ospitata di MongoDB fornita da MongoLab, vedere [Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab][Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab].

Per altre informazioni su come proteggere MongoDB, vedere l'articolo relativo alla [sicurezza di MongoDB][sicurezza di MongoDB].

## Risorse aggiuntive

[Strumenti da riga di comando di Azure per Mac e Linux][Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure]

[Pubblicazione in Siti Web di Azure con Git][Pubblicazione in Siti Web di Azure con Git]

  [MongoDB]: http://www.mongodb.org
  [node]: http://nodejs.org
  [Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab]: /it-it/develop/nodejs/tutorials/website-with-mongodb-mongolab/
  [Pagina Web con un elenco di attività vuoto]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
  [Git]: http://git-scm.com
  [guide all'installazione di MongoDB]: http://docs.mongodb.org/manual/installation/
  [Installare MongoDB su Linux in Azure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [schermata della selezione di VM Depot]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
  [schermata della selezione dell'immagine di MongoDB v2.2.3 su Hardened Ubuntu]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
  [schermata relativa alla scelta dell'account di archiviazione]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
  [schermata della registrazione dell'immagine]: ./media/web-sites-nodejs-store-data-mongodb/register.png
  [schermata dell'immagine]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
  [schermata di nome, nome utente della macchina virtuale e così via.]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
  [uso di SSH con Linux in Azure]: http://www.windowsazure.com/it-it/documentation/articles/linux-use-ssh-key/
  [schermata di configurazione della macchina virtuale]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
  [schermata di configurazione dell'endpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
  [documentazione MongoDB]: http://docs.mongodb.org/manual/
  [Express]: http://expressjs.com
  [Mongoose]: http://mongoosejs.com
  [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure]: /it-it/develop/nodejs/tutorials/create-a-website-(mac)/
  [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure]: /it-it/documentation/articles/xplat-cli/
  [repository Git remoto]: http://git-scm.com/docs/git-remote
  [iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
  [applicazione Web Node.js con il servizio tabelle di Azure]: /it-it/develop/nodejs/tutorials/web-site-with-storage/
  [sicurezza di MongoDB]: http://docs.mongodb.org/manual/security/
  [Pubblicazione in Siti Web di Azure con Git]: /it-it/develop/nodejs/common-tasks/publishing-with-git/
