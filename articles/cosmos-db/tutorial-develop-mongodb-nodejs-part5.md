---
title: Esercitazione su MongoDB, Angular e Node per Azure - Parte 5 | Microsoft Docs
description: Parte 5 della serie di esercitazioni sulla creazione di un'app MongoDB con Angular e Node in Azure Cosmos DB tramite le stesse API usate per MongoDB
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: c06247279df9eaaedf128a2eeec801b4e323c535
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-5-use-mongoose-to-connect-to-azure-cosmos-db"></a>Creare un'app MongoDB con Angular e Azure Cosmos DB - Parte 5: Usare Mongoose per connettersi ad Azure Cosmos DB

Questa esercitazione in più parti illustra come creare una nuova app per le [API MongoDB](mongodb-introduction.md) scritta in Node.js con Express, Angular e il database di Azure Cosmos DB.

La Parte 5 dell'esercitazione è basata sulla [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) e illustra le attività seguenti:

> [!div class="checklist"]
> * Usare Mongoose per connettersi ad Azure Cosmos DB
> * Ottenere informazioni sulla stringa di connessione da Azure Cosmos DB
> * Creare il modello hero
> * Creare il servizio hero per ottenere dati hero
> * Eseguire l'app in locale

## <a name="video-walkthrough"></a>Procedura dettagliata video

> [!VIDEO https://www.youtube.com/embed/sI5hw6KPPXI]


## <a name="prerequisites"></a>prerequisiti

Prima di iniziare questa parte dell'esercitazione, assicurarsi di avere completato le procedure illustrate nella [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) dell'esercitazione.

> [!TIP]
> Questa esercitazione illustra in modo dettagliato la procedura per la creazione dell'applicazione. Se si vuole scaricare il progetto finito, è possibile ottenere l'applicazione completa dal [repository angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) in GitHub.

## <a name="use-mongoose-to-connect-to-azure-cosmos-db"></a>Usare Mongoose per connettersi ad Azure Cosmos DB

1. Installare il modulo mongoose npm, che è un'API normalmente usata per comunicare con MongoDB.

    ```bash
    npm i mongoose --save
    ```

2. Creare ora un nuovo file nella cartella **server** denominata **mongo.js**. In questo file, aggiungere tutte le informazioni di connessione per il database di Azure Cosmos DB.

3. Copiare il codice seguente in **mongo.js**. Questo codice:
    * Richiede Mongoose.
    * Esegue l'override della promessa Mongo per usare invece la promessa di base integrata in ES6/ES2015 e versioni successive.
    * Chiama un file env che consente di configurare alcuni aspetti a seconda che ci si trovi nell'ambiente di staging, produzione o sviluppo. Il file verrà creato a breve.
    * Include la stringa di connessione MongoDB, che verrà impostata nel file env.
    * Crea una funzione di connessione che chiama Mongoose.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per http://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
4. Nel riquadro Esplora, creare una cartella in **server** denominata **environment** e nella cartella **environment** creare un nuovo file denominato **environment.js** .

5. Dal file mongo.js si evince che è necessario includere gli elementi `dbName`, `key` e `cosmosPort`, quindi copiare il codice seguente in **environment.js**.

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string-information"></a>Ottenere le informazioni relative alla stringa di connessione

1. In **environment.js** modificare il valore di `port` in 10255. La porta di Cosmos DB si trova nel portale di Azure.

    ```javascript
    const port = 10255;
    ```

2. In **environment.js** sostituire il valore di `accountName` con il nome dell'account Azure Cosmos DB creato nel [passaggio 4](tutorial-develop-mongodb-nodejs-part4.md). 

3. Recuperare la chiave primaria per l'account di Azure Cosmos DB usando il comando dell'interfaccia della riga di comando seguente nella finestra del terminale: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    * `<cosmosdb-name>` è il nome dell'account Azure Cosmos DB creato nel [passaggio 4](tutorial-develop-mongodb-nodejs-part4.md).

4. Copiare la chiave primaria nel file environment.js come valore `key`.

    L'app ha ora tutte le informazioni necessarie per la connessione ad Azure Cosmos DB. Queste informazioni possono essere recuperate anche nel portale. Per altre informazioni, vedere [Ottenere la stringa di connessione MongoDB da personalizzare](connect-mongodb-account.md#GetCustomConnection). Il nome utente nel portale corrisponde a dbName in environments.js. 

## <a name="create-a-hero-model"></a>Creare un modello Hero

1.  Nel riquadro Esplora creare il file **hero.model.js** nella cartella **server**.

2. Copiare il codice seguente in **hero.model.js**. Questo codice:
   * Richiede Mongoose.
   * Crea un nuovo schema con un ID, un nome e una massima.
   * Crea un modello usando lo schema.
   * Esporta il modello. 
   * Assegnare alla raccolta il nome Heroes invece di Heros, che sarebbe il nome predefinito della raccolta in base alle regole di denominazione plurale di Mongoose.

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Creare un servizio Hero

1.  Nel riquadro Esplora creare il file **hero.service.js** nella cartella **server**.

2. Copiare il codice seguente in **hero.service.js**. Questo codice:
   * Ottiene il modello appena creato
   * Si connette al database
   * Crea una variabile docquery che usa il metodo hero.find per definire una query che restituisce tutti gli elementi hero.
   * Esegue una query con docquery.exec con una promessa per ottenere un elenco di tutti gli elementi hero in cui lo stato della risposta è 200. 
   * Se lo stato è 500, restituisce il messaggio di errore
   * Dato che si usano i moduli, si ottengono gli elementi hero. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="add-the-hero-service-to-routesjs"></a>Aggiungere il servizio hero a routes.js

1. In **routes.js** in Visual Studio Code, impostare come commento la funzione `res.send` che invia i dati hero di esempio e aggiungere una riga per chiamare invece la funzione `heroService.getHeroes`.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

2. Richiedere il servizio hero in **routes.js**:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

3. In **hero.service.js** aggiornare la funzione getHeroes in modo che acquisisca i parametri `req` e `res` come segue:

    ```javascript
    function getHeroes(req, res) {
    ```

    Esaminare ora la catena di chiamate. La prima chiamata è `index.js`, che configura il server del nodo. Si noti che configura e definisce anche le route. Il file routes.js comunica quindi con il servizio hero e gli indica di ottenere funzioni come getHeroes e passare la richiesta e la risposta. Il file hero.service.js recupererà il modello e si connetterà a Mongo, quindi eseguirà getHeroes al momento della chiamata, restituendo una risposta 200. Uscirà quindi ripercorrendo la catena. 

## <a name="run-the-app"></a>Esecuzione dell'app

1. Eseguire di nuovo l'app. In Visual Studio Code salvare tutte le modifiche, fare clic sul pulsante **Debug** ![Icona Debug in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png) a sinistra, quindi sul pulsante **Avvia debug** ![Icona Debug in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

3. Passare ora al browser, aprire gli strumenti di sviluppo e la scheda Rete, quindi passare a http://localhost:3000 dove si trova l'applicazione.

    ![Nuovo account Azure Cosmos DB nel portale di Azure](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

   Non sono ancora archiviati elementi hero nell'app, ma nel passaggio successivo dell'esercitazione si aggiungerà la funzionalità put, push e delete per poter aggiungere, aggiornare ed eliminare elementi hero dall'interfaccia utente usando le connessioni Mongoose al database di Azure Cosmos DB. 

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Sono state usate le API di Mongoose per connettere l'app heroes ad Azure Cosmos DB 
> * È stata aggiunta la funzionalità getHeroes all'app

È possibile passare alla parte successiva dell'esercitazione per aggiungere le funzioni Post, Put e Delete all'app.

> [!div class="nextstepaction"]
> [Aggiungere le funzioni Post, Put e Delete all'app](tutorial-develop-mongodb-nodejs-part6.md)
