---
title: Esercitazione su Node.js per l'API SQL per Azure Cosmos DB
description: Esercitazione su Node.js che illustra come connettersi ad Azure Cosmos DB ed eseguire query usando l'API SQL
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 4441797eb41dc909a98be3c42931140e71e36f80
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270352"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Esercitazione: Compilare un'applicazione console Node.js con l'SDK JavaScript per gestire i dati API SQL di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (Preview)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (Preview)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Gli sviluppatori possono avere applicazioni che usano dati documento NoSQL. È possibile usare un account API SQL in Azure Cosmos DB per archiviare e accedere a tali dati documento. Questa esercitazione illustra come compilare un'applicazione console Node.js per creare risorse di Azure Cosmos DB e sottoporle a query.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare e collegarsi a un account Azure Cosmos DB.
> * Con l'applicazione.
> * Creare un database.
> * Creare un contenitore
> * Aggiungere elementi al contenitore.
> * Eseguire operazioni di base sugli elementi, sul contenitore e sul database.

## <a name="prerequisites"></a>Prerequisiti 

Assicurarsi di disporre delle risorse seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 o versioni successive.

## <a name="create-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Creare prima di tutto un account Azure Cosmos DB. Se è già disponibile un account da usare, è possibile passare a [Configurare l'applicazione Node.js](#SetupNode). Se si usa l'emulatore Azure Cosmos DB, seguire la procedura descritta nell'articolo [Emulatore di Azure Cosmos DB](local-emulator.md) per configurare l'emulatore e proseguire con il passaggio [Configurare l'applicazione Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Configurare l'applicazione Node.js

Prima di iniziare la scrittura del codice per compilare l'applicazione, è possibile compilare il framework per l'applicazione. Eseguire i passaggi seguenti per configurare l'applicazione Node.js che ha il codice del framework:

1. Aprire il terminale preferito.
2. Individuare la cartella o la directory in cui si vuole salvare l'applicazione Node.js.
3. Creare due file JavaScript vuoti con i comandi seguenti:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Creare e inizializzare un file `package.json`. Usare il comando seguente:
   * ```npm init -y```

5. Installare il modulo @azure/cosmos tramite npm. Usare il comando seguente:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Impostare le configurazioni dell'applicazione

Ora che l'applicazione esiste, è necessario assicurarsi che possa comunicare con Azure Cosmos DB. Tramite l'aggiornamento di alcune impostazioni di configurazione, come illustrato nei passaggi seguenti, è possibile impostare l'applicazione per comunicare con Azure Cosmos DB:

1. Aprire il file ```config.js``` in un editor di testo.

1. Copiare e incollare il frammento di codice riportato di seguito e impostare le proprietà ```config.endpoint``` e ```config.primaryKey``` sull'URI e sulla chiave primaria dell'endpoint Azure Cosmos DB. Entrambe le configurazioni sono disponibili nel [portale di Azure](https://portal.azure.com).

   ![Screenshot di acquisizione delle chiavi dal portale di Azure][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. Copiare e incollare i dati di ```database```, ```container``` e ```items``` nell'oggetto ```config``` seguente in cui sono impostate le proprietà ```config.endpoint``` e ```config.primaryKey```. Se sono già disponibili dati da archiviare nel database, è possibile usare lo strumento Migrazione dati di Azure Cosmos DB invece di definire i dati qui.

   ```javascript
   var config = {}

   config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
   config.primaryKey = "~your primary key here~";

   config.database = {
      "id": "FamilyDatabase"
   };

   config.container = {
     "id": "FamilyContainer"
   };

   config.items = {
      "Andersen": {
          "id": "Anderson.1",
          "lastName": "Andersen",
          "parents": [{
            "firstName": "Thomas"
        }, {
                "firstName": "Mary Kay"
            }],
        "children": [{
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [{
                "givenName": "Fluffy"
            }]
        }],
        "address": {
            "state": "WA",
            "county": "King",
            "city": "Seattle"
        }
    },
    "Wakefield": {
        "id": "Wakefield.7",
        "parents": [{
            "familyName": "Wakefield",
            "firstName": "Robin"
        }, {
                "familyName": "Miller",
                "firstName": "Ben"
            }],
        "children": [{
            "familyName": "Merriam",
            "firstName": "Jesse",
            "gender": "female",
            "grade": 8,
            "pets": [{
                "givenName": "Goofy"
            }, {
                    "givenName": "Shadow"
                }]
        }, {
                "familyName": "Miller",
                "firstName": "Lisa",
                "gender": "female",
                "grade": 1
            }],
        "address": {
            "state": "NY",
            "county": "Manhattan",
            "city": "NY"
        },
        "isRegistered": false
      }
   };
   ```

   L'SDK JavaScript usa i termini generici *contenitore* ed *elemento*. Un contenitore può essere una raccolta, un grafo o una tabella. Un elemento può essere un documento, un arco/vertice o una riga ed è il contenuto all'interno di un contenitore. 

1. Esportare infine l'oggetto ```config``` per farvi riferimento nel file ```app.js```.

   ```javascript
        },
        "isRegistered": false
       }
   };

   // ADD THIS PART TO YOUR CODE
   module.exports = config;
   ```

## <a id="Connect"></a>Collegarsi a un account Azure Cosmos DB

1. Aprire il file ```app.js``` vuoto nell'editor di testo. Copiare e incollare il codice seguente per importare il modulo ```@azure/cosmos``` e il modulo ```config``` appena creato.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. Copiare e incollare il codice per usare gli oggetti ```config.endpoint``` e ```config.primaryKey``` salvati in precedenza per creare un nuovo CosmosClient.

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```
   
> [!Note]
> Se ci si connette all'**emulatore di Cosmos DB**, disabilitare la verifica SSL creando un criterio di connessione personalizzata.
>   ```
>   const connectionPolicy = new cosmos.ConnectionPolicy ()
>   connectionPolicy.DisableSSLVerification = true
>
>   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey }, connectionPolicy });
>   ```

Ora che è disponibile il codice per inizializzare il client Azure Cosmos DB, è possibile esaminare come usare le risorse di Azure Cosmos DB.

## <a name="create-a-database"></a>Creare un database

1. Copiare e incollare il codice seguente per impostare l'ID del database e l'ID del contenitore. Tali ID definiscono il modo in cui il client Azure Cosmos DB trova il contenitore e il database corretti.

   ```javascript
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   ```

   È possibile creare un database usando la funzione `createIfNotExists` o creare della classe **Database**. Un database è un contenitore logico di elementi partizionati tra contenitori. 

2. Copiare e incollare i metodi **createDatabase** e **readDatabase** nel file app.js sotto la definizione di ```databaseId``` e ```containerId```. La funzione **createDatabase** creerà un nuovo database con ID ```FamilyDatabase```, specificato dall'oggetto ```config``` se non già esistente. La funzione **readDatabase** leggerà la definizione del database per verificare che il database esista.

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Copiare e incollare il codice seguente in cui vengono impostate le funzioni **createDatabase** e **readDatabase** per aggiungere la funzione helper **exit**, che stampa il messaggio di uscita. 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Copiare e incollare il codice seguente in cui viene impostata la funzione **exit** per chiamare le funzioni **createDatabase** e **readDatabase**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   A questo punto, il codice in ```app.js``` dovrebbe aver l'aspetto seguente:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. Nel terminale trovare il file ```app.js``` ed eseguire il comando: 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>Creare un contenitore

Successivamente, creare un contenitore all'interno dell'account Azure Cosmos DB, in modo da poter archiviare i dati e sottoporli a query. 

> [!WARNING]
La creazione di un contenitore ha implicazioni in termini di prezzi. Visitare la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/) per sapere cosa aspettarsi.

È possibile creare un contenitore usando la funzione `createIfNotExists` o creare della classe **Contenitori**.  Un contenitore è costituito da elementi, che nel caso dell'API SQL sono documenti JSON, e logica dell'applicazione JavaScript associata.

1. Copiare e incollare la funzione **createContainer** e **readContainer** sotto la funzione **readDatabase** nel file app. js. La funzione **createContainer** creerà un nuovo contenitore con l'```containerId``` specificato dall'oggetto ```config``` se non già esistente. La funzione **readContainer** funzione leggerà la definizione del contenitore per verificare l'esistenza del contenitore.

   ```javascript
   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Copiare e incollare il codice sotto la chiamata a **readDatabase** per eseguire le funzioni **createContainer** e **readContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   A questo punto, il codice in ```app.js``` dovrebbe aver l'aspetto seguente:

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
     const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
     console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Nel terminale trovare il file ```app.js``` ed eseguire il comando: 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>Crea un elemento

È possibile creare un elemento usando la funzione creare della classe **Elementi**. Quando si usa l'API SQL, gli elementi vengono proiettati come documenti, ovvero contenuti JSON definiti dall'utente (arbitrario). È ora possibile inserire un elemento in Azure Cosmos DB.

1. Copiare e incollare la funzione **createFamilyItem** sotto la funzione **readContainer**. La funzione **createFamilyItem** crea gli elementi contenenti i dati JSON salvati nell'oggetto ```config```. È necessario assicurarsi che non esista già un elemento con lo stesso ID prima di crearlo.

   ```javascript
   /**
   * Create family item if it does not exist
   */
  async function createFamilyItem(itemBody) {
     try {
         // read the item to see if it exists
         const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
         console.log(`Item with family id ${itemBody.id} already exists\n`);
     }
     catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
     }
   };
   ```

1. Copiare e incollare il codice sotto la chiamata a **readContainer** per eseguire la funzione **createFamilyItem**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Nel terminale trovare il file ```app.js``` ed eseguire il comando: 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Sottoporre a query le risorse di Azure Cosmos DB
Azure Cosmos DB supporta le query complesse sui documenti JSON archiviati in ogni contenitore. L'esempio di codice seguente illustra una query eseguibile nei documenti del contenitore.

1. Copiare e incollare la funzione **queryContainer** sotto la funzione **createFamilyItem** nel file app.js. Azure Cosmos DB supporta query simili a SQL, come illustrato di seguito.

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Copiare e incollare il codice sotto le chiamate a **createFamilyItem** per eseguire la funzione **queryContainer**.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Nel terminale trovare il file ```app.js``` ed eseguire il comando:

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>Sostituire un elemento
Azure Cosmos DB supporta la sostituzione del contenuto degli elementi.

1. Copiare e incollare la funzione **replaceFamilyItem** sotto la funzione **queryContainer** nel file app.js. Si noti che la proprietà 'grade' di un elemento figlio è stata modificata in 6 rispetto al valore precedente 5.

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
      const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
   };
   ```

1. Copiare e incollare il codice sotto la chiamata a **queryContainer** per eseguire la funzione **replaceFamilyItem**. Aggiungere anche il codice per chiamare nuovamente **queryContainer** e verificare che l'elemento sia stato modificato correttamente.

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Nel terminale trovare il file ```app.js``` ed eseguire il comando:

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>Eliminare un elemento

Azure Cosmos DB supporta l'eliminazione di elementi JSON.

1. Copiare e incollare la funzione **deleteFamilyItem** sotto la funzione **replaceFamilyItem**.

   ```javascript
  /**
  * Delete the item by ID.
  */
   async function deleteFamilyItem(itemBody) {
      await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Copiare e incollare il codice sotto la chiamata alla seconda funzione **queryContainer** per eseguire la funzione **deleteFamilyItem**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. Nel terminale trovare il file ```app.js``` ed eseguire il comando: 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>Eliminare il database

Se si elimina il database creato, insieme al database vengono rimosse tutte le risorse figlio, come contenitori, elementi e così via.

1. Copiare e incollare la funzione **cleanup** sotto la funzione **deleteFamilyItem** per rimuovere il database e tutte le risorse figlio.

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Copiare e incollare il codice sotto la chiamata a **deleteFamilyItem** per eseguire la funzione **cleanup**.

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Eseguire l'applicazione Node.js

Il codice finale avrà un aspetto simile al seguente:

```javascript
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

Nel terminale trovare il file ```app.js``` ed eseguire il comando: 

```bash 
node app.js
```

Verrà visualizzato l'output dell'app introduttiva. L'output dovrebbe essere analogo al testo di esempio seguente.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>Ottenere la soluzione completa per l'esercitazione su Node.js 

Se non si ha tempo per completare le procedure dell'esercitazione o se si vogliono solo scaricare gli esempi di codice, è possibile ottenerli da [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). 

Per eseguire la soluzione Guida introduttiva contenente tutto il codice riportato in questo articolo, è necessario avere: 

* Un [account Azure Cosmos DB][create-account]. 
* La soluzione [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) disponibile su GitHub. 

Installare le dipendenze del progetto tramite npm. Usare il comando seguente: 

* ```npm install``` 

Successivamente, nel file ```config.js```, aggiornare i valori config.endpoint e config.primaryKey come illustrato nel [Passaggio 3: Impostare le configurazioni dell'applicazione](#Config).  

Nel terminale trovare quindi il file ```app.js``` ed eseguire il comando:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando tali risorse non servono più, è possibile eliminare il gruppo di risorse, l'account Azure Cosmos DB e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse usato per l'account Azure Cosmos DB, selezionare **Elimina**, quindi confermare il nome del gruppo di risorse da eliminare.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitorare un account Azure Cosmos DB](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
