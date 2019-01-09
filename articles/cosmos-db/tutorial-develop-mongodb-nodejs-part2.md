---
title: Creare un'app Angular con l'API di Azure Cosmos DB per MongoDB - Creare un'app Node.js Express
titleSuffix: Azure Cosmos DB
description: Parte 2 della serie di esercitazioni sulla creazione di un'app MongoDB con Angular e Node in Azure Cosmos DB mediante le stesse API usate per MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.openlocfilehash: 8e122ab62009463ae12d5635323655be708aa29d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788056"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-nodejs-express-app"></a>Creare un'app Angular con l'API di Azure Cosmos DB per MongoDB - Creare un'app Node.js Express

Questa esercitazione in più parti illustra come creare una nuova app scritta in Node.js con Express e Angular e quindi connetterla all'[account Cosmos configurato con l'API di Cosmos DB per MongoDB](mongodb-introduction.md).

La Parte 2 dell'esercitazione è basata sull'[introduzione](tutorial-develop-mongodb-nodejs.md) e illustra le attività seguenti:

> [!div class="checklist"]
> * Installare l'interfaccia della riga di comando di Angular e TypeScript
> * Creare un nuovo progetto usando Angular
> * Compilare l'app usando il framework Express
> * Testare l'app in Postman

## <a name="video-walkthrough"></a>Procedura dettagliata video

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa parte dell'esercitazione, guardare il [video introduttivo](tutorial-develop-mongodb-nodejs.md).

Per completare questa esercitazione, sono necessari anche: 
* [Node.js](https://nodejs.org/) versione 8.4.0 o successiva.
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) o un altro editor di codice.

> [!TIP]
> Questa esercitazione illustra in modo dettagliato la procedura per la creazione dell'applicazione. Se si vuole scaricare il progetto finito, è possibile ottenere l'applicazione completa dal [repository angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) su GitHub.

## <a name="install-the-angular-cli-and-typescript"></a>Installare l'interfaccia della riga di comando di Angular e TypeScript

1. Aprire un prompt dei comandi di Windows o una finestra del terminale di Mac e installare l'interfaccia della riga di comando di Angular.

    ```bash
    npm install -g @angular/cli
    ```

2. Installare TypeScript immettendo il comando seguente nel prompt. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Usare l'interfaccia della riga di comando di Angular per creare un nuovo progetto

1. Al prompt dei comandi, passare alla cartella in cui si vuole creare il nuovo progetto, quindi usare il comando seguente. Questo comando crea una nuova cartella e un nuovo progetto denominato angular-cosmosdb e installa i componenti di Angular necessari per una nuova app. Usa la configurazione minima (--minimal) e specifica che il progetto usa Sass (una sintassi simile a CSS con il flag --style scss).

    ```bash
    ng new angular-cosmosdb --minimal --style scss
    ```

2. Dopo che il comando è stato completato, modificare le directory nella cartella src/client.

    ```bash
    cd angular-cosmosdb
    ```

3. Aprire quindi la cartella in Visual Studio Code.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Compilare l'app usando il framework Express

1. Nel riquadro **Esplora**  di Visual Studio Code fare clic con il pulsante destro del mouse sulla cartella **src**, scegliere **Nuova cartella** e assegnare alla nuova cartella il nome *server*.

2. Nel riquadro **Esplora** fare clic con il pulsante destro del mouse sulla cartella **server**, scegliere **Nuovo file** e specificare *index.js* come nome per il nuovo file.

3. Al prompt dei comandi, usare il comando seguente per installare il parser del corpo, che consente all'app di analizzare i dati JSON passati tramite le API.

    ```bash
    npm i express body-parser --save
    ```

4. In Visual Studio Code copiare il codice seguente nel file index.js. Questo codice:
    * Fa riferimento a Express
    * Esegue il pull del parser del corpo per leggere i dati JSON nel corpo delle richieste
    * Usa una funzionalità predefinita denominata path
    * Imposta le variabili radice per trovare più facilmente dove si trova il codice
    * Configura una porta
    * Avvia Express
    * Comunica all'app come usare il middleware per gestire il server
    * Gestisce tutto il contenuto della cartella dist, che costituirà il contenuto statico
    * Gestisce l'applicazione e index.html per le richieste GET non trovate sul server (per i collegamenti diretti)
    * Avvia il server con app.listen
    * Usa una funzione arrow per registrare che la porta è attiva
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist/angular-cosmosdb')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/angular-cosmosdb/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. Nel riquadro **Esplora**  di Visual Studio Code fare clic con il pulsante destro del mouse sulla cartella **server** e quindi scegliere **Nuovo file**. Assegnare al nuovo file il nome *routes.js*. 

6. Copiare il codice seguente in **routes.js**. Questo codice:
   * Fa riferimento al router Express
   * Ottiene l'elemento heroes
   * Rimanda il codice JSON per un elemento hero definito

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Salvare tutti i file modificati. 

8. In Visual Studio Code fare clic sul pulsante **Debug** ![Icona Debug in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png) e quindi sul pulsante Ingranaggio ![Pulsante Ingranaggio in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png). Il nuovo file launch.json viene aperto in Visual Studio Code.

8. Alla riga 11 del file launch.json sostituire `"${workspaceFolder}\\server"` con `"program": "${workspaceRoot}/src/server/index.js"` e salvare il file.

9. Fare clic sul pulsante **Avvia debug** ![Icona Debug in Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png) per eseguire l'app.

    L'app verrà eseguita senza errori.

## <a name="use-postman-to-test-the-app"></a>Usare Postman per testare l'app

1. Aprire ora Postman e inserire `http://localhost:3000/api/heroes` nella casella GET. 

2. Fare clic sul pulsante **Send** (Invia) e ottenere la risposta json dall'app. 

    Questa risposta indica che l'app è operativa localmente. 

    ![Richiesta e risposta in Postman](./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png)


## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * È stato creato un progetto Node.js usando l'interfaccia della riga di comando di Angular
> * È stata testata l'app usando Postman

È possibile passare alla parte successiva dell'esercitazione per compilare l'interfaccia utente.

> [!div class="nextstepaction"]
> [Build the UI with Angular (Compilare l'interfaccia utente con Angular)](tutorial-develop-mongodb-nodejs-part3.md)
