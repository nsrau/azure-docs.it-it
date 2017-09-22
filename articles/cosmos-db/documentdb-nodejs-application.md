---
title: Compilare un'app Web Node.js per Azure Cosmos DB | Microsoft Docs
description: Questa esercitazione di Node.js illustra come usare il servizio Microsoft Azure Cosmos DB per archiviare e accedere ai dati da un'applicazione Web Node.js Express ospitata in siti Web di Azure.
keywords: Sviluppo di applicazioni, esercitazione sul database, informazioni su node.js, esercitazione su node.js
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 9da9e63b-e76a-434e-96dd-195ce2699ef3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/14/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 32d4b4a7db134975158e53fc964c24b52b14a1e1
ms.contentlocale: it-it
ms.lasthandoff: 09/20/2017

---
# <a name="_Toc395783175"></a>Creare un'applicazione Web Node.js con Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.JS](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

Questa esercitazione relativa a Node.js illustra come usare Azure Cosmos DB e l'API di DocumentDB per archiviare e accedere ai dati da un'applicazione Node.js Express ospitata in Siti Web di Azure. Verrà creata una semplice applicazione di gestione delle attività basata su Web, un'app ToDo, che consente di creare, recuperare e completare le attività. Le attività vengono memorizzate come documenti JSON in Azure Cosmos DB. Questa esercitazione illustra la creazione e la distribuzione dell'app e fornisce informazioni dettagliate sulle operazioni in ogni frammento di codice.

![Schermata dell'applicazione My Todo List creata in questa esercitazione Node.js](./media/documentdb-nodejs-application/cosmos-db-node-js-mytodo.png)

Non si ha tempo di completare l'esercitazione e si preferisce ottenere semplicemente la soluzione completa. Non è un problema, è possibile ottenere la soluzione di esempio completa da [GitHub][GitHub]. Per istruzioni su come eseguire l'app, vedere il file [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md).

## <a name="_Toc395783176"></a>Prerequisiti
> [!TIP]
> Questa esercitazione Node.js presuppone già una certa esperienza nell'uso di Node.js e di Siti Web di Azure.
> 
> 

Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili gli elementi seguenti:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] 0.10.29 o versione successiva.
* [Generatore di Express](http://www.expressjs.com/starter/generator.html), installabile tramite `npm install express-generator -g`
* [Git][Git].

## <a name="_Toc395637761"></a>Passaggio 1: Creare un account del database di Azure Cosmos DB
Creare prima di tutto un account Azure Cosmos DB. Se si ha già un account o si usa l'emulatore Azure Cosmos DB per questa esercitazione, è possibile andare al [Passaggio 2: Creare una nuova applicazione Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Passaggio 2: Creare una nuova applicazione Node.js
A questo punto si creerà un progetto base Node.js Hello World usando il framework [Express](http://expressjs.com/) .

1. Aprire il terminale preferito, ad esempio il prompt dei comandi di Node.js.
2. Passare alla directory in cui si vuole archiviare la nuova applicazione.
3. Usare il generatore di Express per generare una nuova applicazione denominata **todo**.
   
        express todo
4. Aprire la nuova directory **todo** e installare le dipendenze.
   
        cd todo
        npm install
5. Eseguire la nuova applicazione.
   
        npm start
6. È possibile visualizzare la nuova applicazione passando con il browser all'indirizzo [http://localhost:3000](http://localhost:3000).
   
    ![Informazioni su Node.js - schermata dell'applicazione Hello World in una finestra del browser](./media/documentdb-nodejs-application/cosmos-db-node-js-express.png)

    Per arrestare l'applicazione, premere quindi CTRL+C nella finestra del terminale e fare clic su **y** per interrompere il processo batch.

## <a name="_Toc395783179"></a>Passaggio 3: Installare moduli aggiuntivi
Il file **package.json** è uno di quelli creati nella radice del progetto. Questo file contiene un elenco di moduli aggiuntivi necessari per l'applicazione Node.js. In seguito, quando si distribuirà questa applicazione in un sito Web di Azure, il file verrà usato per determinare quali moduli devono essere installati in Azure per supportare l'applicazione. Per l'esercitazione, è necessario installare altri due pacchetti.

1. Tornare al terminale e installare il modulo **async** tramite npm.
   
        npm install async --save
2. Installare il modulo **documentdb** tramite npm. Questo è il modulo in cui risiedono tutte le funzionalità avanzate di Azure Cosmos DB.
   
        npm install documentdb --save
3. Con un rapido controllo del file **package.json** dell'applicazione si dovrebbero notare i moduli aggiuntivi. Questo file indica ad Azure quali pacchetti scaricare e installare durante l'esecuzione dell'applicazione. Dovrebbe essere simile all'esempio seguente.
   
        {
          "name": "todo",
          "version": "0.0.0",
          "private": true,
          "scripts": {
            "start": "node ./bin/www"
          },
          "dependencies": {
            "async": "^2.1.4",
            "body-parser": "~1.15.2",
            "cookie-parser": "~1.4.3",
            "debug": "~2.2.0",
            "documentdb": "^1.10.0",
            "express": "~4.14.0",
            "jade": "~1.11.0",
            "morgan": "~1.7.0",
            "serve-favicon": "~2.3.0"
          }
        }
   
    Ciò indicherà a Node (e più avanti ad Azure) che l'applicazione dipende da questi moduli aggiuntivi.

## <a name="_Toc395783180"></a>Passaggio 4: Uso del servizio Azure Cosmos DB in un'applicazione Node
Al termine delle operazioni iniziali di installazione e configurazione, si può passare allo scopo effettivo di questa esercitazione, che consiste nello scrivere qualche riga di codice usando Azure Cosmos DB.

### <a name="create-the-model"></a>Creare il modello
1. Nella directory del progetto creare una nuova directory denominata **models** nella stessa directory del file package.json.
2. Nella directory **models** (modelli) creare un nuovo file denominato **taskDao.js**. Questo file conterrà il modello per le attività create dall'applicazione.
3. Nella stessa directory **models** (modelli) creare un altro nuovo file denominato **docdbUtils.js**. Questo file conterrà porzioni di codice utile e riutilizzabile, che verrà usato nell'applicazione. 
4. Copiare il codice seguente in **docdbUtils.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
   
        var DocDBUtils = {
            getOrCreateDatabase: function (client, databaseId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id= @id',
                    parameters: [{
                        name: '@id',
                        value: databaseId
                    }]
                };
   
                client.queryDatabases(querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        if (results.length === 0) {
                            var databaseSpec = {
                                id: databaseId
                            };
   
                            client.createDatabase(databaseSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            },
   
            getOrCreateCollection: function (client, databaseLink, collectionId, callback) {
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id=@id',
                    parameters: [{
                        name: '@id',
                        value: collectionId
                    }]
                };               
   
                client.queryCollections(databaseLink, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {        
                        if (results.length === 0) {
                            var collectionSpec = {
                                id: collectionId
                            };
   
                            client.createCollection(databaseLink, collectionSpec, function (err, created) {
                                callback(null, created);
                            });
   
                        } else {
                            callback(null, results[0]);
                        }
                    }
                });
            }
        };
   
        module.exports = DocDBUtils;
   
5. Salvare e chiudere il file **docdbUtils.js** .
6. All'inizio del file **taskDao.js** aggiungere il codice seguente per fare riferimento a **DocumentDBClient** e **docdbUtils.js** creati in precedenza:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var docdbUtils = require('./docdbUtils');
7. Quindi, aggiungere il codice per definire ed esportare l'oggetto Task. Si occupa dell'inizializzazione dell'oggetto Task e della configurazione del database e della raccolta documenti che verranno usati.
   
        function TaskDao(documentDBClient, databaseId, collectionId) {
          this.client = documentDBClient;
          this.databaseId = databaseId;
          this.collectionId = collectionId;
   
          this.database = null;
          this.collection = null;
        }
   
        module.exports = TaskDao;
8. Aggiungere quindi il codice seguente per definire metodi aggiuntivi nell'oggetto Task che consentano l'interazione con i dati archiviati in Azure Cosmos DB.
   
        TaskDao.prototype = {
            init: function (callback) {
                var self = this;
   
                docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function (err, db) {
                    if (err) {
                        callback(err);
                    } else {
                        self.database = db;
                        docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function (err, coll) {
                            if (err) {
                                callback(err);
   
                            } else {
                                self.collection = coll;
                            }
                        });
                    }
                });
            },
   
            find: function (querySpec, callback) {
                var self = this;
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results);
                    }
                });
            },
   
            addItem: function (item, callback) {
                var self = this;
   
                item.date = Date.now();
                item.completed = false;
   
                self.client.createDocument(self.collection._self, item, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, doc);
                    }
                });
            },
   
            updateItem: function (itemId, callback) {
                var self = this;
   
                self.getItem(itemId, function (err, doc) {
                    if (err) {
                        callback(err);
   
                    } else {
                        doc.completed = true;
   
                        self.client.replaceDocument(doc._self, doc, function (err, replaced) {
                            if (err) {
                                callback(err);
   
                            } else {
                                callback(null, replaced);
                            }
                        });
                    }
                });
            },
   
            getItem: function (itemId, callback) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.id = @id',
                    parameters: [{
                        name: '@id',
                        value: itemId
                    }]
                };
   
                self.client.queryDocuments(self.collection._self, querySpec).toArray(function (err, results) {
                    if (err) {
                        callback(err);
   
                    } else {
                        callback(null, results[0]);
                    }
                });
            }
        };
9. Salvare e chiudere il file **taskDao.js** . 

### <a name="create-the-controller"></a>Creare il controller
1. Nella directory **routes** (route) del progetto creare un nuovo file denominato **tasklist.js**. 
2. Aggiungere il seguente codice al file **tasklist.js**. In questo modo vengono caricati i moduli DocumentDBClient e asincroni usati da **tasklist.js**. Viene anche definita la funzione **TaskList** a cui viene passata un'istanza dell'oggetto **Task** definito in precedenza:
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var async = require('async');
   
        function TaskList(taskDao) {
          this.taskDao = taskDao;
        }
   
        module.exports = TaskList;
3. Continuare ad aggiungere codice al file **tasklist.js** aggiungendo i metodi **showTasks, addTask** e **completeTasks**:
   
        TaskList.prototype = {
            showTasks: function (req, res) {
                var self = this;
   
                var querySpec = {
                    query: 'SELECT * FROM root r WHERE r.completed=@completed',
                    parameters: [{
                        name: '@completed',
                        value: false
                    }]
                };
   
                self.taskDao.find(querySpec, function (err, items) {
                    if (err) {
                        throw (err);
                    }
   
                    res.render('index', {
                        title: 'My ToDo List ',
                        tasks: items
                    });
                });
            },
   
            addTask: function (req, res) {
                var self = this;
                var item = req.body;
   
                self.taskDao.addItem(item, function (err) {
                    if (err) {
                        throw (err);
                    }
   
                    res.redirect('/');
                });
            },
   
            completeTask: function (req, res) {
                var self = this;
                var completedTasks = Object.keys(req.body);
   
                async.forEach(completedTasks, function taskIterator(completedTask, callback) {
                    self.taskDao.updateItem(completedTask, function (err) {
                        if (err) {
                            callback(err);
                        } else {
                            callback(null);
                        }
                    });
                }, function goHome(err) {
                    if (err) {
                        throw err;
                    } else {
                        res.redirect('/');
                    }
                });
            }
        };
4. Salvare e chiudere il file **tasklist.js** .

### <a name="add-configjs"></a>Aggiungere config.json
1. Nella directory del progetto creare un nuovo file denominato **config.js**.
2. Aggiungere quanto segue a **config.json**. Definisce le impostazioni e i valori di configurazione necessari per l'applicazione.
   
        var config = {}
   
        config.host = process.env.HOST || "[the URI value from the Azure Cosmos DB Keys blade on http://portal.azure.com]";
        config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the Azure Cosmos DB Keys blade on http://portal.azure.com]";
        config.databaseId = "ToDoList";
        config.collectionId = "Items";
   
        module.exports = config;
3. Nel file **config.js** aggiornare i valori HOST e AUTH_KEY usando i valori presenti nel pannello Chiavi dell'account Azure Cosmos DB nel [portale di Microsoft Azure](https://portal.azure.com).
4. Salvare e chiudere il file **config.js** .

### <a name="modify-appjs"></a>Modificare il file app.js
1. Nella directory del progetto aprire il file **app.js** . Questo file è stato creato in precedenza quando è stata creata l'applicazione Web Express.
2. Aggiungere il codice seguente all'inizio del file **app.js**
   
        var DocumentDBClient = require('documentdb').DocumentClient;
        var config = require('./config');
        var TaskList = require('./routes/tasklist');
        var TaskDao = require('./models/taskDao');
3. Questo codice definisce il file di configurazione da usare e procede con la lettura dei valori da questo file in alcune variabili che si useranno a breve.
4. Sostituire le due righe seguenti nel file **app.js** :
   
        app.use('/', index);
        app.use('/users', users); 
   
      con il frammento seguente:
   
        var docDbClient = new DocumentDBClient(config.host, {
            masterKey: config.authKey
        });
        var taskDao = new TaskDao(docDbClient, config.databaseId, config.collectionId);
        var taskList = new TaskList(taskDao);
        taskDao.init();
   
        app.get('/', taskList.showTasks.bind(taskList));
        app.post('/addtask', taskList.addTask.bind(taskList));
        app.post('/completetask', taskList.completeTask.bind(taskList));
        app.set('view engine', 'jade');
5. Queste righe definiscono una nuova istanza dell'oggetto **TaskDao** con una nuova connessione a Azure Cosmos DB, usando i valori letti dal file **config.js**, inizializzano l'oggetto task e quindi associano le azioni modulo ai metodi nel controller **TaskList**. 
6. Infine, salvare e chiudere il file **app.js**. La procedura è quasi terminata.

## <a name="_Toc395783181"></a>Passaggio 5: Creare un'interfaccia utente
È ora possibile passare alla realizzazione di un'interfaccia utente che consenta a un utente di interagire con l'applicazione. L'applicazione Express creata usa **Jade** come motore di visualizzazione. Per altre informazioni su Jade, vedere [http://jade-lang.com/](http://jade-lang.com/).

1. Il file **layout.jade** della directory **views** viene usato come modello globale per altri file **.jade**. In questo passaggio verrà modificato in modo da usare [Twitter Bootstrap](https://github.com/twbs/bootstrap), un toolkit che semplifica la progettazione di un sito Web di aspetto gradevole. 
2. Aprire il file **layout.jade** nella cartella **views** e sostituire il contenuto con quanto segue:

    ```
    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body
        nav.navbar.navbar-inverse.navbar-fixed-top
          div.navbar-header
            a.navbar-brand(href='#') My Tasks
        block content
        script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
        script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
    ```

    Ciò in effetti comunica al motore **Jade** di eseguire il rendering di una parte del codice HTML per l'applicazione e crea un **blocco** denominato **content** tramite cui è possibile fornire il layout per le pagine di contenuto.

    Salvare e chiudere il file **layout.jade** .

3. Aprire il file **index.jade** , la visualizzazione che sarà usata dall'applicazione, quindi sostituire il contenuto del file con quanto segue:
   
        extends layout
        block content
           h1 #{title}
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
                     td #{task.name}
                     td #{task.category}
                     - var date  = new Date(task.date);
                     - var day   = date.getDate();
                     - var month = date.getMonth() + 1;
                     - var year  = date.getFullYear();
                     td #{month + "/" + day + "/" + year}
                     td
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn.btn-primary(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             .form-group
               label(for="name") Item Name:
               input.form-control(name="name", type="textbox")
             .form-group
               label(for="category") Item Category:
               input.form-control(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   

Questo estende il layout e fornisce contenuto per il segnaposto **content** già visto in precedenza nel file **layout.jade**.
   
In questo layout sono stati creati due moduli HTML.

Il primo modulo contiene una tabella per i dati e un pulsante che consente di aggiornare gli elementi mediante la pubblicazione nel metodo **/completetask** del controller.
    
Il secondo modulo contiene due campi di input e un pulsante che consente di creare un nuovo elemento tramite la pubblicazione nel metodo **/addtask** del controller.

Ciò è tutto quanto è necessario per il funzionamento dell'applicazione.

## <a name="_Toc395783181"></a>Passaggio 6: Esecuzione dell'applicazione in locale
1. Per testare l'applicazione nel computer locale, eseguire `npm start` nel terminale per avviare l'applicazione, quindi aggiornare la pagina [http://localhost:3000](http://localhost:3000) del browser. L'aspetto della pagina dovrebbe essere simile all'immagine seguente:
   
    ![Schermata dell'applicazione MyTodo List in una finestra del browser](./media/documentdb-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Se viene visualizzato un errore relativo al rientro nel file layout.jade o nel file index.jade, assicurarsi che le prime due righe in entrambi i file siano giustificate a sinistra, senza spazi. Se sono presenti spazi davanti alle prime due righe, rimuoverli, salvare entrambi i file e quindi aggiornare la finestra del browser. 

2. Usare i campi Elemento, Nome elemento e Categoria per immettere una nuova attività e quindi fare clic su **Aggiungi elementi**. Viene creato un documento in Azure Cosmos DB con queste proprietà. 
3. La pagina verrà aggiornata e verrà visualizzato il nuovo elemento creato nell'elenco ToDo.
   
    ![Schermata dell'applicazione con un nuovo elemento nell'elenco ToDo](./media/documentdb-nodejs-application/cosmos-db-node-js-added-task.png)
4. Per completare un'attività, è sufficiente selezionare la casella di controllo nella colonna Complete e quindi fare clic su **Update tasks**. Viene aggiornato il documento già creato.

5. Per arrestare l'applicazione, premere CTRL+C nella finestra del terminale e quindi fare clic su **Y** per interrompere il processo batch.

## <a name="_Toc395783182"></a>Passaggio 7: Distribuire il progetto di sviluppo dell'applicazione in Siti Web di Azure
1. Se non è ancora stato fatto, abilitare un repository Git per il sito Web di Azure. Per istruzioni su come eseguire questa operazione, vedere l'argomento [Distribuzione dell'archivio Git locale nel servizio app di Azure](../app-service/app-service-deploy-local-git.md) .
2. Aggiungere il sito Web di Azure come Git remoto.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Distribuire mediante push al sito remoto.
   
        git push azure master
4. Dopo alcuni secondi, Git completerà la pubblicazione dell'applicazione Web e avvierà un browser in cui sarà possibile visualizzare quanto realizzato in esecuzione in Azure.

    Congratulazioni. Si è creata la prima applicazione Web Express Node.js usando Azure Cosmos DB e la si è pubblicata in Siti Web di Azure.

    Per scaricare o fare riferimento all'applicazione di riferimento completa per questa esercitazione, vedere [GitHub][GitHub].

## <a name="_Toc395637775"></a>Passaggi successivi

* Per la scalabilità e i test delle prestazioni con Azure Cosmos DB, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).
* Informazioni su come [monitorare un account Azure Cosmos DB](monitor-accounts.md).
* Eseguire query sul set di dati di esempio illustrato nella pagina [Query Playground](https://www.documentdb.com/sql/demo).
* Esplorare la [Documentazione di Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app


