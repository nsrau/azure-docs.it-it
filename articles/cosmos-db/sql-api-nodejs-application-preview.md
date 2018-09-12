---
title: Compilare un'app Web Node.js per Azure Cosmos DB | Microsoft Docs
description: Questa esercitazione di Node.js illustra come usare il servizio Microsoft Azure Cosmos DB per archiviare e accedere ai dati da un'applicazione Web Node.js Express ospitata in siti Web di Azure.
keywords: Sviluppo di applicazioni, esercitazione su database, apprendere Node.js, esercitazione su Node.js
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: sngun
ms.openlocfilehash: 8e788207e99a87e9635fbf668ad99c21ca101ecf
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697116"
---
# <a name="_Toc395783175"></a>Creare un'applicazione Web Node.js con Azure Cosmos DB e Node.js SDK (anteprima)

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.JS](sql-api-nodejs-application.md)
> * [Node.js - v2](sql-api-nodejs-application-preview.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Questa esercitazione relativa a Node.js mostra come usare l'account API SQL di Azure Cosmos DB per archiviare i dati e accedervi da un'applicazione Node.js Express ospitata in Siti Web di Azure. In questa esercitazione verrà creata una semplice applicazione basata sul Web (app ToDo) che permette di creare, recuperare e completare attività. Le attività vengono memorizzate come documenti JSON in Azure Cosmos DB. L'immagine seguente mostra uno screenshot dell'applicazione ToDo:

![Schermata dell'applicazione My Todo List creata in questa esercitazione Node.js](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

Questa esercitazione descrive come creare un account API SQL di Azure Cosmos DB usando il portale di Azure. È quindi possibile compilare ed eseguire un'app Web basata su Node.js SDK per creare un database e un contenitore e aggiungere elementi al contenitore. Questa esercitazione usa la versione 2.0 di Node.js SDK, che è attualmente in anteprima.

È anche possibile ottenere l'esempio completo in [GitHub][GitHub]. Per istruzioni su come eseguire l'app, vedere il file [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md).

## <a name="_Toc395783176"></a>Prerequisiti

Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili gli elementi seguenti:

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] versione 6.10 o successiva.
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

   ```bash
   express todo
   ```
4. Aprire la nuova directory **todo** e installare le dipendenze.

   ```bash
   cd todo
   npm install
   ```
5. Eseguire la nuova applicazione.

   ```bash
   npm start
   ```

6. È possibile visualizzare la nuova applicazione passando con il browser all'indirizzo [http://localhost:3000](http://localhost:3000).
   
    ![Informazioni su Node.js - schermata dell'applicazione Hello World in una finestra del browser](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Per arrestare l'applicazione, usare CTRL+C nella finestra del terminale e fare clic su **y** per terminare il processo batch.

## <a name="_Toc395783179"></a>Passaggio 3: Installare i moduli necessari

Il file **package.json** è uno di quelli creati nella radice del progetto. Questo file contiene un elenco di moduli aggiuntivi necessari per l'applicazione Node.js. In seguito, quando si distribuirà questa applicazione in un sito Web di Azure, il file verrà usato per determinare quali moduli devono essere installati in Azure per supportare l'applicazione. Ai fini di questa esercitazione, è necessario installare altri due pacchetti.

1. Aprire il terminale e installare il modulo **async** tramite npm.

   ```bash
   npm install async --save
   ```

2. Installare il modulo **@azure/cosmos** tramite npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Passaggio 4: Uso del servizio Azure Cosmos DB in un'applicazione Node
Dopo aver completato l'installazione e la configurazione iniziali, è necessario scrivere il codice che servirà all'applicazione ToDo per comunicare con Azure Cosmos DB.

### <a name="create-the-model"></a>Creare il modello
1. Nella radice della directory del progetto creare una nuova directory denominata **models**  

2. Nella directory **models** (modelli) creare un nuovo file denominato **taskDao.js**. Questo file contiene il codice necessario per creare il database e il contenitore e definisce i metodi per leggere, aggiornare, creare e trovare attività in Azure Cosmos DB. 

3. Copiare il codice seguente nel file **taskDao.js**

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Salvare e chiudere il file **taskDao.js** .  

### <a name="create-the-controller"></a>Creare il controller

1. Nella directory **routes** (route) del progetto creare un nuovo file denominato **tasklist.js**.  

2. Aggiungere il seguente codice al file **tasklist.js**. In questo modo, vengono caricati i moduli CosmosClient e async, usati da **tasklist.js**. Viene inoltre definita la classe **TaskList**, che viene passata come istanza dell'oggetto **TaskDao** definito in precedenza:
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Salvare e chiudere il file **tasklist.js** .

### <a name="add-configjs"></a>Aggiungere config.json

1. Nella radice della directory del progetto creare un nuovo file denominato **config.js**. 

2. Aggiungere il codice seguente al file **config.js**. Questo codice definisce le impostazioni e i valori di configurazione necessari per l'applicazione.
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. Nel file **config.js** aggiornare i valori HOST e AUTH_KEY usando i valori presenti nella pagina Chiavi dell'account Azure Cosmos DB nel [portale di Microsoft Azure](https://portal.azure.com). 

4. Salvare e chiudere il file **config.js** .

### <a name="modify-appjs"></a>Modificare il file app.js
1. Nella directory del progetto aprire il file **app.js** . Questo file è stato creato in precedenza quando è stata creata l'applicazione Web Express.  

2. Aggiungere il codice seguente al file **app.js**. Questo codice definisce il file config da usare e procede alla lettura dei valori da questo file in alcune variabili che verranno usate tra poco. 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Infine, salvare e chiudere il file **app.js**. La procedura è quasi terminata.

## <a name="_Toc395783181"></a>Passaggio 5: Creare un'interfaccia utente
È ora possibile passare alla realizzazione di un'interfaccia utente che consenta a un utente di interagire con l'applicazione. L'applicazione Express creata usa **Jade** come motore di visualizzazione. Per altre informazioni su Jade, vedere [http://jade-lang.com/](http://jade-lang.com/).

1. Il file **layout.jade** della directory **views** viene usato come modello globale per altri file **.jade**. In questo passaggio verrà modificato in modo da usare [Twitter Bootstrap](https://github.com/twbs/bootstrap), un toolkit che semplifica la progettazione di un sito Web di aspetto gradevole.  

2. Aprire il file **layout.jade** nella cartella **views** e sostituire il contenuto con quanto segue:

   ```html
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

   ```html
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
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Questo estende il layout e fornisce contenuto per il segnaposto **content** già visto in precedenza nel file **layout.jade**.
   
In questo layout sono stati creati due moduli HTML.

Il primo modulo contiene una tabella per i dati e un pulsante che permette di aggiornare gli elementi mediante la pubblicazione nel metodo **/completetask** del controller.
    
Il secondo modulo contiene due campi di input e un pulsante che consente di creare un nuovo elemento tramite la pubblicazione nel metodo **/addtask** del controller.

Ciò è tutto quanto è necessario per il funzionamento dell'applicazione.

## <a name="_Toc395783181"></a>Passaggio 6: Esecuzione dell'applicazione in locale
1. Per testare l'applicazione nel computer locale, eseguire `npm start` nel terminale per avviare l'applicazione e quindi aggiornare la pagina [http://localhost:3000](http://localhost:3000) nel browser. L'aspetto della pagina dovrebbe essere simile all'immagine seguente:
   
    ![Schermata dell'applicazione MyTodo List in una finestra del browser](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Se viene visualizzato un errore relativo al rientro nel file layout.jade o nel file index.jade, assicurarsi che le prime due righe in entrambi i file siano giustificate a sinistra, senza spazi. Se sono presenti spazi davanti alle prime due righe, rimuoverli, salvare entrambi i file e quindi aggiornare la finestra del browser. 

2. Usare i campi Elemento, Nome elemento e Categoria per immettere una nuova attività e quindi fare clic su **Aggiungi elementi**. Viene creato un documento in Azure Cosmos DB con queste proprietà. 
3. La pagina verrà aggiornata e verrà visualizzato il nuovo elemento creato nell'elenco ToDo.
   
    ![Schermata dell'applicazione con un nuovo elemento nell'elenco ToDo](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Per completare un'attività, è sufficiente selezionare la casella di controllo nella colonna Complete e quindi fare clic su **Update tasks**. Il documento già creato verrà aggiornato e rimosso dalla visualizzazione.

5. Per arrestare l'applicazione, premere CTRL+C nella finestra del terminale e quindi fare clic su **Y** per interrompere il processo batch.

## <a name="_Toc395783182"></a>Passaggio 7: Distribuire il progetto di sviluppo dell'applicazione in Siti Web di Azure
1. Se non è ancora stato fatto, abilitare un repository Git per il sito Web di Azure. Per istruzioni su come eseguire questa operazione, vedere l'argomento [Distribuzione dell'archivio Git locale nel servizio app di Azure](../app-service/app-service-deploy-local-git.md) .
2. Aggiungere il sito Web di Azure come Git remoto.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Distribuire mediante push al sito remoto.
   
        git push azure master
4. Dopo alcuni secondi, Git completerà la pubblicazione dell'applicazione Web e avvierà un browser in cui sarà possibile visualizzare quanto realizzato in esecuzione in Azure.

    Congratulazioni! Si è creata la prima applicazione Web Express Node.js usando Azure Cosmos DB e la si è pubblicata in Siti Web di Azure.

    Per scaricare o fare riferimento all'applicazione di riferimento completa per questa esercitazione, vedere [GitHub][GitHub].

## <a name="_Toc395637775"></a>Passaggi successivi

* Per la scalabilità e i test delle prestazioni con Azure Cosmos DB, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).
* Informazioni su come [monitorare un account Azure Cosmos DB](monitor-accounts.md).
* Eseguire query sul set di dati di esempio illustrato nella pagina [Query Playground](https://www.documentdb.com/sql/demo).
* Esplorare la [Documentazione di Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

