<properties linkid="develop-node-website-with-mongodb-mac" urlDisplayName="Web site with MongoDB" pageTitle="Node.js web site with MongoDB on a VM - Azure tutorial" metaKeywords="Azure tutorial MongoDB, MongoDB store data, access data MongoDB Node, Azure Node app" description="A tutorial that teaches you how to use MongoDB to store and access data from a Node application hosted on Azure." metaCanonical="http://www.windowsazure.com/en-us/develop/nodejs/tutorials/website-with-mongodb-mongolab/" services="web-sites,virtual-machines" documentationCenter="Node.js" title="Node.js Web Application with Storage on MongoDB (Virtual Machine)" authors="" solutions="" writer="" manager="" editor="" />

Creazione di un'applicazione Node.js in Azure con MongoDB in una macchina virtuale
==================================================================================

In questa esercitazione viene illustrato come utilizzare [MongoDB](http://www.mongodb.org) ospitato in una macchina virtuale di Azure per archiviare i dati e accedervi da un'applicazione [node](http://nodejs.org) ospitata in un sito Web di Azure. [MongoDB](http://www.mongodb.org) è un diffuso database NoSQL open source a prestazioni elevate.

Si apprenderà come:

-   Configurare una macchina virtuale che esegue Ubuntu e MongoDB da VM Depot.
-   Accedere a MongoDB da un'applicazione Node.
-   Utilizzare gli strumenti multipiattaforma per Azure per creare un sito Web di Azure.

In questa esercitazione verrà creata una semplice applicazione di gestione delle attività basata su Web che consente di creare, recuperare e completare le attività. Le attività sono archiviate in MongoDB.

> [WACOM.NOTE] In questa esercitazione viene utilizzata un'istanza di MongoDB installata in una macchina virtuale. Se si desidera utilizzare un'istanza di MongoDB ospitata fornita da MongoLabs, vedere [Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab](/en-us/develop/nodejs/tutorials/website-with-mongodb-mongolab/).

I file di progetto per questa esercitazione verranno archiviati in una directory denominata **tasklist** e l'applicazione completata presenterà un aspetto simile al seguente:

![Pagina Web con un elenco di attività vuoto](./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png)

> [WACOM.NOTE] Molte delle procedure illustrate di seguito prevedono l'utilizzo della riga di comando. Per tali procedure, utilizzare la riga di comando per il sistema operativo in uso, ad esempio **Windows PowerShell** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione Terminale.

Prerequisiti
------------

Per le procedure illustrate in questa esercitazione viene utilizzato Node.js, pertanto nell'ambiente di sviluppo è necessario che sia installata una versione recente di [Node.js](http://nodejs.org).

È inoltre necessario che il sistema [Git](http://git-scm.com) sia disponibile dalla riga di comando dell'ambiente di sviluppo, in quanto viene utilizzato per distribuire l'applicazione in un sito Web di Azure.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Creazione di una macchina virtuale
----------------------------------

Sebbene sia possibile creare una nuova macchina virtuale e quindi installarvi MongoDB attenendosi alle [guide all'installazione di MongoDB](http://docs.mongodb.org/manual/installation/), la maggior parte del lavoro è già stata svolta dalla community ed è disponibile in VM Depot. Nella procedura seguente viene illustrato come utilizzare un'immagine in cui è già installato e configurato MongoDB da VM Depot.

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), selezionare **Virtual Machines**, **Images** e quindi **VM Depot**.

    ![schermata della selezione di VM Depot](./media/web-sites-nodejs-store-data-mongodb/browsedepot.png)

2.  Selezionare un'immagine che includa MongoDB. In questo caso, è stato selezionato Ubuntu per limitare l'elenco alle sole immagini basate sulla distribuzione di Ubuntu Linux. Infine, selezionare un'immagine di MongoDB v2.2.3 su Hardened Ubuntu

    ![schermata della selezione dell'immagine di MongoDB v2.2.3 su Hardened Ubuntu](./media/web-sites-nodejs-store-data-mongodb/selectimage.png)

    > [WACOM.NOTE] Accertarsi di selezionare **More** per visualizzare tutte le informazioni relative all'immagine. Alcune immagini potrebbero richiedere configurazioni aggiuntive da specificare dopo la creazione della macchina virtuale tramite l'immagine.

    Fare clic sulla freccia nella parte inferiore per passare alla schermata successiva.

3.  Selezionare l'area e l'account di archiviazione che verranno utilizzati per archiviare il disco rigido virtuale per l'immagine. Fare clic sul segno di spunta per continuare.

    ![schermata relativa alla scelta dell'account di archiviazione](./media/web-sites-nodejs-store-data-mongodb/storageaccount.png)

    > [WACOM.NOTE] Verrà avviato un processo di copia che copia l'immagine da VM Depot all'account di archiviazione specificato. L'operazione può richiedere anche 15 minuti o più.

4.  Quando lo stato dell'immagine passa a **Pending registration**, selezionare **Register** e digitare un nome descrittivo per la nuova immagine. Fare clic sul segno di spunta per continuare.

    ![schermata della registrazione dell'immagine](./media/web-sites-nodejs-store-data-mongodb/register.png)

5.  Quando lo stato dell'immagine passa a **Available**, selezionare **+ New**, **Virtual Machine**, **From Gallery**. Quando viene visualizzata l'opzione **Choose an Image**, selezionare **My Images** e quindi l'immagine creata nei passaggi precedenti. Fare clic sulla freccia per continuare.

    ![schermata dell'immagine](./media/web-sites-nodejs-store-data-mongodb/myimages.png)

6.  Specificare il nome, le dimensioni e il nome utente della macchina virtuale. Fare clic sulla freccia per continuare.

    ![schermata di nome, nome utente della macchina virtuale e così via.](./media/web-sites-nodejs-store-data-mongodb/vmname.png)

    > [WACOM.NOTE] Per questa esercitazione, non è necessario utilizzare il protocollo SSH per la connessione remota alla macchina virtuale. Selezionare **Use a password** e fornire una password se non si ha familiarità con l'utilizzo dei certificati tramite SSH.
    >
    > Per ulteriori informazioni sull'utilizzo del protocollo SSH per accedere a una macchina virtuale Linux in Azure, vedere la pagina relativa all'[utilizzo di SSH con Linux in Azure](http://www.windowsazure.com/en-us/documentation/articles/linux-use-ssh-key/).

7.  Selezionare se utilizzare un servizio cloud nuovo o esistente e l'area in cui verrà creata la macchina virtuale. Fare clic sulla freccia per continuare.

    ![schermata di configurazione della macchina virtuale](./media/web-sites-nodejs-store-data-mongodb/vmconfig.png)

8.  Impostare altri endpoint per la macchina virtuale. Poiché si accederà a MongoDB da questa macchina virtuale, aggiungere un nuovo endpoint utilizzando le informazioni seguenti:

    -   Nome - MongoDB
    -   Protocollo - TCP
    -   Porta pubblica - 27017
    -   Porta privata - 27017

    Per esporre il portale Web di MongoDB, aggiungere un altro endpoint utilizzando le informazioni seguenti:

    -   Nome - MongoDBWeb
    -   Protocollo - TCP
    -   Porta pubblica - 28017
    -   Porta privata - 28017

    Infine, fare clic sul segno di spunta per configurare la macchina virtuale.

    ![schermata di configurazione dell'endpoint](./media/web-sites-nodejs-store-data-mongodb/endpoints.png)

9.  Quando lo stato della macchina virtuale passa a **Running**, dovrebbe essere possibile aprire un browser Web all'indirizzo **http://&lt;YourVMDNSName\>.cloudapp.net:28017/** per verificare che MongoDB sia in esecuzione. Nella parte inferiore della pagina dovrebbe essere presente un log che visualizza informazioni sul servizio, simile al seguente:

         Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
            18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
            18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
            18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
            18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
            18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
            18:57:16 [initandlisten] recover : no journal files present, no recovery needed
            18:57:17 [initandlisten] waiting for connections on port 27017

    Se nel log vengono visualizzati errori, consultare la [documentazione MongoDB](http://docs.mongodb.org/manual/) per informazioni sulla risoluzione dei problemi.

Installazione dei moduli e generazione dello scaffolding
--------------------------------------------------------

In questa sezione verrà creata una nuova applicazione Node nell'ambiente di sviluppo e verrà utilizzato npm per aggiungere pacchetti di modulo. Per l'applicazione tasklist verranno utilizzati i moduli [Express](http://expressjs.com) e [Mongoose](http://mongoosejs.com). Il modulo Express fornisce un framework View Controller per node, mentre Mongoose è un driver per comunicare con MongoDB.

### Installare Express e generare lo scaffolding

1.  Dalla riga di comando passare alla directory **tasklist**. Se la directory **tasklist** non esiste, crearla.

    > [WACOM.NOTE] In questa esercitazione si fa riferimento alla cartella **tasklist**. Il percorso completo della cartella è omesso, in quanto la semantica dei percorsi varia da un sistema operativo all'altro. È consigliabile creare la cartella in un percorso di facile accesso nel file system locale, ad esempio **\~/node/tasklist** o **c:\\node\\tasklist**

2.  Immettere il comando seguente per installare Express.

    npm install express -g

    > [WACOM.NOTE] Durante l'utilizzo del parametro '-g' in alcuni sistemi operativi, è possibile che vengano restituiti l'errore ***Error: EPERM, chmod '/usr/local/bin/express'*** con la richiesta di provare ad eseguire l'account come amministratore. In questo caso, utilizzare il comando `sudo` per eseguire npm a un livello di privilegi più elevato.

    L'output di questo comando dovrebbe apparire simile al seguente:

         express@3.5.0 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express                                        
         ├── methods@0.1.0                                                                                              
         ├── merge-descriptors@0.0.2                                                                                    
         ├── fresh@0.2.2                                                                                                
         ├── cookie-signature@1.0.3                                                                                     
         ├── range-parser@1.0.0                                                                                         
         ├── debug@0.7.4                                                                                                
         ├── buffer-crc32@0.2.1                                                                                         
         ├── cookie@0.1.1                                                                                               
         ├── mkdirp@0.3.5                                                                                               
         ├── commander@1.3.2 (keypress@0.1.0)                                                                           
         ├── send@0.2.0 (mime@1.2.11)                                                                                   
         ├── connect@2.14.1 (response-time@1.0.0, pause@0.0.1, connect-timeout@1.0.0, method-override@1.0.0, vhost@1.0.0, static-favicon@1.0.0, morgan@1.0.0, serve-static@1.0.2, basic-auth-connect@1.0.0, qs@0.6.6, bytes@0.2.1, raw-body@1.1.3, errorhandler@1.0.0, cookie-parser@1.0.1, express-session@1.0.2, compression@1.0.0, csurf@1.0.0, serve-index@1.0.1, multiparty@2.2.0)                                                                              

    > [WACOM.NOTE] Se si utilizza il parametro '-g' durante l'installazione del modulo Express, questo viene installato a livello globale, affinché sia possibile accedere al comando ***express*** per generare lo scaffolding del sito Web senza dover generare informazioni aggiuntive sul percorso.

3.  Per creare lo scaffolding che verrà utilizzato per questa applicazione, utilizzare il comando **express**:

    express

    L'output di questo comando dovrebbe apparire simile al seguente:

            create : .
            create : ./package.json
            create : ./app.js
            create : ./public
            create : ./public/stylesheets
            create : ./public/stylesheets/style.css
            create : ./public/images
            create : ./public/javascripts
            create : ./routes
            create : ./routes/index.js
            create : ./routes/user.js
            create : ./views
            create : ./views/layout.jade
            create : ./views/index.jade

            install dependencies:
              $ cd . && npm install

            run the app:
              $ node app

    Dopo il completamento di questo comando, nella directory **tasklist** saranno disponibili diverse nuove directory e file.

### Installare moduli aggiuntivi

Il file **package.json** è uno di quelli creati dal comando **express**. Questo file contiene un elenco di moduli aggiuntivi necessari per un'applicazione Express. In seguito, quando si distribuirà questa applicazione in un sito Web di Azure, il file verrà utilizzato per determinare quali moduli devono essere installati in Azure per supportare l'applicazione.

1.  Dalla cartella **tasklist** utilizzare quanto segue per installare i moduli descritti nel file **package.json**:

         npm install

    L'output di questo comando dovrebbe apparire simile al seguente:

         express@3.5.0 node_modules\express                                                                            
         ├── methods@0.1.0                                                                                             
         ├── merge-descriptors@0.0.2                                                                                   
         ├── cookie-signature@1.0.3                                                                                    
         ├── fresh@0.2.2                                                                                               
         ├── debug@0.7.4                                                                                               
         ├── range-parser@1.0.0                                                                                        
         ├── buffer-crc32@0.2.1                                                                                        
         ├── cookie@0.1.1                                                                                              
         ├── mkdirp@0.3.5                                                                                              
         ├── commander@1.3.2 (keypress@0.1.0)                                                                          
         ├── send@0.2.0 (mime@1.2.11)                                                                                  
         ├── connect@2.14.1 (response-time@1.0.0, pause@0.0.1, connect-timeout@1.0.0, method-override@1.0.0, vhost@1.0., static-favicon@1.0.0, qs@0.6.6, morgan@1.0.0, basic-auth-connect@1.0.0, serve-static@1.0.2, bytes@0.2.1, rawbody@1.1.3, errorhandler@1.0.0, cookie-parser@1.0.1, csurf@1.0.0, compression@1.0.0, express-session@1.0.2, seve-index@1.0.1, multiparty@2.2.0)                                                                             
                                                                                                                  
         jade@1.3.0 node_modules\jade                                                                                  
         ├── character-parser@1.2.0                                                                                    
         ├── commander@2.1.0                                                                                           
         ├── mkdirp@0.3.5                                                                                              
         ├── monocle@1.1.51 (readdirp@0.2.5)                                                                           
         ├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)                                            
         ├── with@3.0.0 (uglify-js@2.4.12)                                                                             
         ├── constantinople@2.0.0 (uglify-js@2.4.12)                                                                   

    Verranno installati tutti i moduli predefiniti utilizzati da un'applicazione Express.

2.  Immettere quindi il comando seguente per installare il modulo Mongoose localmente e per salvare una voce per il modulo nel file **package.json**:

         npm install mongoose --save

    L'output di questo comando dovrebbe apparire simile al seguente:

         mongoose@3.8.8 node_modules\mongoose                     
         ├── regexp-clone@0.0.1                                   
         ├── muri@0.3.1                                           
         ├── sliced@0.0.5                                         
         ├── hooks@0.2.1                                          
         ├── mpath@0.1.1                                          
         ├── mpromise@0.4.3                                       
         ├── ms@0.1.0                                             
         ├── mquery@0.5.3 (debug@0.7.4)                           
         ├── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)          

    > [WACOM.NOTE] È possibile ignorare qualsiasi messaggio sull'installazione del parser bson C++.

Utilizzo di MongoDB in un'applicazione Node
-------------------------------------------

In questa sezione si estenderà l'applicazione di base creata dal comando **express** aggiungendo un file **task.js** contenente il modello per le attività. Si modificherà inoltre il file **app.js** esistente e si creerà un nuovo file controller **tasklist.js** per utilizzare il modello.

### Creare il modello

1.  Nella directory **tasklist** creare una nuova directory denominata **models**.

2.  Nella directory **models** creare un nuovo file denominato **task.js**. Questo file conterrà il modello per le attività create dall'applicazione.

3.  All'inizio del file **task.js**, aggiungere il codice seguente per fare riferimento alle librerie necessarie:

         var mongoose = require('mongoose'),
             Schema = mongoose.Schema;

4.  Quindi, aggiungere il codice per definire ed esportare il modello. Il modello verrà utilizzato per eseguire interazioni con il database MongoDB.

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

2.  Aggiungere il codice seguente al file **tasklist.js**. Verranno caricati il modulo mongoose e il modello di attività definito in **task.js**. La funzione TaskList viene utilizzata per creare la connessione al server MongoDB in base al valore **connection**:

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

1.  Salvare il file **tasklist.js**.

### Modificare il file app.js

1.  Nella directory **tasklist** aprire il file **app.js** in un editor di testo. Questo file è stato creato in precedenza eseguendo il comando **express**.

2.  Aggiungere il codice seguente all'inizio del file **app.js**. Verrà inizializzato **TaskList** con la stringa di connessione per il server MongoDB:

         var TaskList = require('./routes/tasklist');
         var taskList = new TaskList(process.env.MONGODB_URI);

    Si noti la seconda riga: si accede a una variabile di ambiente che si configurerà in seguito, contenente le informazioni sulla connessione per l'istanza di mongo. Se un'istanza locale di mongo è in esecuzione a scopo di sviluppo, potrebbe essere necessario impostare temporaneamente questo valore su "localhost" anziché su process.env.MONGODB\_URI.

3.  Trovare le righe che iniziano con `app.get` e sostituirle con le righe seguenti:

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

Distribuzione dell'applicazione in Azure
----------------------------------------

Nei passaggi di questa sezione vengono utilizzati gli strumenti da riga di comando di Azure per creare un nuovo sito Web di Azure e quindi viene utilizzato Git per distribuire l'applicazione. Per eseguire questi passaggi, è necessario disporre di una sottoscrizione di Azure.

> [WACOM.NOTE] È possibile eseguire queste procedure anche nel portale di Azure. Per ulteriori informazioni sulla distribuzione di un'applicazione Node.js tramite il portale di Azure, vedere [Creazione e distribuzione di un'applicazione Node.js in un sito Web di Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/).

> [WACOM.NOTE] Se questo è il primo sito Web di Azure che si crea, per distribuire l'applicazione è necessario utilizzare il portale di Azure.

### Installare l'interfaccia della riga di comando multipiattaforma di Azure

L'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli) consente di effettuare operazioni di gestione per i servizi di Azure. Se l'interfaccia della riga di comando multipiattaforma (xplat-cli) non è ancora stata installata e configurata nell'ambiente di sviluppo, vedere [Installazione e configurazione dell'interfaccia della riga di comando multipiattaforma di Azure](/en-us/documentation/articles/xplat-cli/) per istruzioni.

### Creare un sito Web di Azure

1.  Dalla riga di comando passare alla directory **tasklist**.

2.  Utilizzare il comando seguente per creare un nuovo sito Web di Azure. Sostituire "myuniquesitename" con un nome univoco del sito Web. Questo valore è utilizzato come parte dell'URL per il sito Web risultante.

         azure site create myuniquesitename --git

    Verrà richiesto di specificare il data center in cui si troverà il sito. Selezionare il data center geograficamente vicino alla propria posizione.

    Il parametro `--git` creerà un archivio Git locale nella cartella **tasklist**, se non ne esiste già uno. Creerà anche un [archivio Git remoto](http://git-scm.com/docs/git-remote) denominato 'azure' che verrà utilizzato per pubblicare l'applicazione in Azure, oltre a un file [iisnode.yml](https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml) contenente le impostazioni utilizzate da Azure per ospitare le applicazioni Node. Infine, creerà un file .gitignore per escludere la cartella dei moduli di Node dalla pubblicazione su .git.

    > [WACOM.NOTE] Se questo comando viene eseguito da una directory che contiene già un archivio Git, la directory non verrà reinizializzata.

    > [WACOM.NOTE] Se il parametro '--git' viene omesso ma la directory contiene un archivio Git, l'archivio 'azure' verrà comunque creato.

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

    > [WACOM.NOTE\> Se questo è il primo sito Web di Azure per la sottoscrizione, verrà indicato di utilizzare il portale per la creazione del sito Web. Per ulteriori informazioni, vedere l'articolo relativo alla [creazione e alla distribuzione di un'applicazione Node.js in Siti Web di Azure](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/).

### Impostare la variabile di ambiente MONGODB\_URL

L'applicazione prevede che la stringa di connessione per l'istanza MongoDB sia disponibile nella variabile di ambiente MONGODB\_URL. Per impostare questo valore per il sito Web, utilizzare il comando seguente:

    azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

Verrà creata una nuova impostazione dell'applicazione per il sito Web che verrà quindi utilizzata per popolare la variabile di ambiente MONGODB\_URI letta dal sito Web. Sostituire il valore di 'mymongodb.cloudapp.net' con il nome della macchina virtuale in cui è stato installato MongoDB.

### Pubblicare l'applicazione

1.  Nella finestra del terminale passare alla directory **tasklist** se non è già stato fatto.

2.  Utilizzare i comandi seguenti per aggiungere e quindi confermare i file nell'archivio Git locale:

         git add .
         git commit -m "adding files"

3.  Durante il push delle ultime modifiche apportate all'archivio Git nel sito Web di Azure, è necessario specificare che il ramo di destinazione è **master** perché viene utilizzato per il contenuto del sito Web.

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
          * [new branch] master -\> master

4.  Al termine dell'operazione di push, passare al sito Web utilizzando il comando `azure site browse` per visualizzare l'applicazione.

Passaggi successivi
-------------------

Le procedure in questo articolo descrivono l'utilizzo di MongoDB per archiviare le informazioni. Per questo stesso scopo è tuttavia possibile utilizzare il servizio tabelle di Azure. Per ulteriori informazioni, vedere l'articolo relativo all'[applicazione Web Node.js con il servizio tabelle di Azure](/en-us/develop/nodejs/tutorials/web-site-with-storage/).

Per informazioni su come utilizzare un'istanza ospitata di MongoDB fornita da MongoLabs, vedere [Creazione di un'applicazione Node.js in Azure con MongoDB mediante il componente aggiuntivo MongoLab](/en-us/develop/nodejs/tutorials/website-with-mongodb-mongolab/).

Per ulteriori informazioni su come proteggere MongoDB, vedere l'articolo relativo alla [sicurezza di MongoDB](http://docs.mongodb.org/manual/security/).

Risorse aggiuntive
------------------

[Strumenti da riga di comando di Azure per Mac e Linux](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/)

 [Pubblicazione in Siti Web di Azure con Git](/en-us/develop/nodejs/common-tasks/publishing-with-git/)

