---
title: Creare un'app Angular con l'API di Azure Cosmos DB per MongoDB - Usare Mongoose per connettersi a Cosmos DB
titleSuffix: Azure Cosmos DB
description: Questa esercitazione descrive come compilare un'applicazione Node.js usando Express e Angular per gestire i dati archiviati in Cosmos DB. In questa parte si usa Mongoose per connettersi ad Azure Cosmos DB.
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
Customer intent: As a developer, I want to build a Node.js application, so that I can manage the data stored in Cosmos DB.
ms.openlocfilehash: 03b1f1d5fa1320f08059ed755f6f4f7d1c08d35e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793702"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Creare un'app Angular con l'API di Azure Cosmos DB per MongoDB - Usare Mongoose per connettersi a Cosmos DB

Questa esercitazione in più parti illustra come creare un'app Node.js con Express e Angular e quindi connetterla all'[account Cosmos configurato con l'API di Cosmos DB per MongoDB](mongodb-introduction.md). Questo articolo descrive la Parte 5 dell'esercitazione e si basa sulla [Parte 4](tutorial-develop-mongodb-nodejs-part4.md).

In questa parte dell'esercitazione si apprenderà come:

> [!div class="checklist"]
> * Usare Mongoose per connettersi a Cosmos DB.
> * Ottenere la stringa di connessione di Cosmos DB.
> * Creare il modello Hero.
> * Creare il servizio Hero per ottenere dati Hero.
> * Eseguire l'app in locale.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Prima di iniziare questa esercitazione, completare i passaggi della [Parte 4](tutorial-develop-mongodb-nodejs-part4.md).

* Questa esercitazione richiede l'esecuzione dell'interfaccia della riga di comando di Azure nell'ambiente locale. È necessario che sia installata l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Questa esercitazione illustra in modo dettagliato la procedura per la creazione dell'applicazione. Se si vuole scaricare il progetto finito, è possibile ottenere l'applicazione completa dal [repository angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) in GitHub.

## <a name="use-mongoose-to-connect"></a>Usare Mongoose per eseguire la connessione

Mongoose è una libreria di Object Data Modeling (ODM) per MongoDB e Node.js. Si può usare Mongoose per connettersi all'account Azure Cosmos DB. Seguire questa procedura per installare Mongoose e connettersi ad Azure Cosmos DB:

1. Installare il modulo mongoose npm, che è un'API usata per comunicare con MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. Nella cartella **server** creare un file denominato **mongo.js**. Si aggiungeranno i dettagli della connessione dell'account Azure Cosmos DB a questo file.

1. Copiare il codice seguente nel file **mongo.js**. Il codice fornisce le funzionalità seguenti:

    * Richiede Mongoose.
    * Esegue l'override della promessa Mongo per usare invece la promessa di base integrata in ES6/ES2015 e versioni successive.
    * Chiama un file env che consente di configurare alcuni aspetti a seconda che ci si trovi nell'ambiente di staging, produzione o sviluppo. Si creerà il file nella sezione successiva.
    * Include la stringa di connessione MongoDB, che è impostata nel file env.
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
    
1. Nel riquadro Esplora, sotto **server**, creare una cartella denominata **environment**. Nella cartella **environment** creare un file denominato **environment.js**.

1. Dal file mongo.js, è necessario includere i valori per parametri `dbName`, `key`e `cosmosPort`. Copiare il codice seguente nel file **environment.js**:

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

Per connettere l'applicazione ad Azure Cosmos DB, è necessario aggiornare le impostazioni di configurazione per l'applicazione. Usare la procedura seguente per aggiornare le impostazioni: 

1. Nel portale di Azure ottenere i valori di numero di porta, nome account e chiave primaria per l'account Azure Cosmos DB.

1. Nel file **environment.js** modificare il valore di `port` in 10255. 

    ```javascript
    const port = 10255;
    ```

1. Nel file **environment.js** sostituire il valore di `accountName` con il nome dell'account Azure Cosmos DB creato nella [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) dell'esercitazione. 

1. Recuperare la chiave primaria per l'account di Azure Cosmos DB usando il comando dell'interfaccia della riga di comando seguente nella finestra del terminale: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> è il nome dell'account Azure Cosmos DB creato nella [Parte 4](tutorial-develop-mongodb-nodejs-part4.md) dell'esercitazione.

1. Copiare la chiave primaria nel file **environment.js** come valore `key`.

A questo punto l'applicazione ha tutte le informazioni necessarie per connettersi ad Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Creare un modello Hero

Successivamente, è necessario definire lo schema dei dati da archiviare in Azure Cosmos DB con la definizione di un file di modello. Usare la procedura seguente per creare un _modello Hero_ che definisce lo schema dei dati:

1. Nel riquadro Esplora creare un file denominato **hero.model.js** nella cartella **server**.

1. Copiare il codice seguente nel file **hero.model.js**. Il codice fornisce le funzionalità seguenti:

   * Richiede Mongoose.
   * Crea un nuovo schema con un ID, un nome e una massima.
   * Crea un modello usando lo schema.
   * Esporta il modello. 
   * Assegna alla raccolta il nome **Heroes** invece di **Heros**, che sarebbe il nome predefinito della raccolta in base alle regole di denominazione plurale di Mongoose.

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

Dopo aver creato il modello Hero, è necessario definire un servizio per leggere i dati ed eseguire le operazioni di elenco, creazione, eliminazione e aggiornamento. Usare la procedura seguente per creare un _servizio Hero_ che esegue query sui dati da Azure Cosmos DB:

1. Nel riquadro Esplora creare un file denominato **hero.service.js** nella cartella **server**.

1. Copiare il codice seguente nel file **hero.service.js**. Il codice fornisce le funzionalità seguenti:

   * Ottiene il modello creato.
   * Si connette al database.
   * Crea una variabile `docquery` che usa il metodo `hero.find` per definire una query che restituisce tutti gli elementi Hero.
   * Esegue una query con la funzione `docquery.exec` con una promessa per ottenere un elenco di tutti gli elementi Hero in cui lo stato della risposta è 200. 
   * Restituisce il messaggio di errore se lo stato è 500.
   * Dato che si stanno usano i moduli, ottiene gli elementi Hero. 

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

## <a name="configure-routes"></a>Configurare le route

Successivamente, è necessario configurare le route per gestire gli URL per le richieste Get, Create, Read e Delete. I metodi di routing specificano le funzioni di callback, chiamate anche _funzioni del gestore_. Queste funzioni vengono chiamate quando l'applicazione riceve una richiesta all'endpoint specificato e il metodo HTTP. Usare la procedura seguente per aggiungere il servizio Hero e definire le route:

1. In Visual Studio Code, nel file **routes.js** impostare come commento la funzione `res.send` che invia i dati Hero di esempio. Aggiungere una riga per chiamare invece la funzione `heroService.getHeroes`.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. In **routes.js** `require` il servizio Hero:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. Nel file **hero.service.js** aggiornare la funzione `getHeroes` in modo che acquisisca i parametri `req` e `res` come segue:

    ```javascript
    function getHeroes(req, res) {
    ```

Esaminare ora il codice precedente. In primo luogo c'è il file index.js, che configura il server del nodo. Si noti che configura e definisce le route. Il file routes.js comunica con il servizio Hero e gli indica di ottenere le funzioni come **getHeroes** e passare la richiesta e la risposta. Il file hero.service.js ottiene il modello e si connette a Mongo. Quindi esegue **getHeroes** quando viene chiamato e restituisce una risposta 200. 

## <a name="run-the-app"></a>Esecuzione dell'app

A questo punto, eseguire l'app seguendo questa procedura:

1. In Visual Studio Code salvare tutte le modifiche. Fare clic sul pulsante **Debug** ![Icona Debug in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png) a sinistra, quindi sul pulsante **Avvia debug** ![Icona Debug in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

1. Passare ora al browser. Aprire gli **Strumenti di sviluppo** e la **scheda Rete**. Passare a http://localhost:3000 per visualizzare l'applicazione.

    ![Nuovo account Azure Cosmos DB nel portale di Azure](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

Nell'app non sono ancora archiviati elementi Hero. Nella parte successiva di questa esercitazione, si aggiungeranno le funzionalità Put, Push e Delete. Sarà quindi possibile aggiungere, aggiornare ed eliminare elementi Hero dall'interfaccia utente usando connessioni Mongoose al database di Azure Cosmos DB. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse non servono più, è possibile eliminare il gruppo di risorse, l'account Azure Cosmos DB e tutte le risorse correlate. Per eliminare il gruppo di risorse, completare i passaggi seguenti:

 1. Passare al gruppo di risorse in cui si è creato l'account Azure Cosmos DB.
 1. Selezionare **Elimina gruppo di risorse**.
 1. Confermare il nome del gruppo di risorse da eliminare, quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

Continuare con la Parte 6 dell'esercitazione per aggiungere le funzioni Post, Put e Delete all'app:

> [!div class="nextstepaction"]
> [Parte 6: Aggiungere le funzioni Post, Put e Delete all'app](tutorial-develop-mongodb-nodejs-part6.md)
