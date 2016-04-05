<properties
	pageTitle="Esercitazione su Node.js NoSQL per DocumentDB | Microsoft Azure"
	description="Esercitazione su Node.js NoSQL che crea un database nodo e un'applicazione console con DocumentDB Node.js SDK. DocumentDB è un database NoSQL per JSON."
    keywords="esercitazione su node.js, database nodo"
	services="documentdb"
	documentationCenter="node.js"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="hero-article" 
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# Esercitazione su Node.js NoSQL: applicazione console Node.js di DocumentDB  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.JS](documentdb-nodejs-get-started.md)

Esercitazione su Node.js per DocumentDB Node.js SDK Dopo aver seguito questa esercitazione, si otterrà un'applicazione console che consente di creare ed eseguire query sulle risorse di DocumentDB, incluso un database nodo.

Tratteremo questo argomento:

- Creazione e connessione a un account DocumentDB
- Configurazione dell'applicazione
- Creazione di un database nodo
- Creare una raccolta
- Creazione di documenti JSON
- Esecuzione di query sulla raccolta
- Eliminazione del database nodo

Non si ha tempo? Nessun problema. La soluzione completa è disponibile in [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Per istruzioni rapide, vedere [ottenere la soluzione completa](#GetSolution).

Dopo aver completato l'esercitazione su Node.js, usare i pulsanti di voto all'inizio e alla fine di questa pagina per fornire commenti e suggerimenti. Se si desidera contattarci, è possibile includere l'indirizzo di posta elettronica nel commento per il follow-up.

Ecco come procedere.

## Prerequisiti per l'esercitazione su Node.js

Assicurarsi di disporre di quanto segue:

- Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- [Node.js](https://nodejs.org/) v0.10.29 o versioni successive.

## Passaggio 1: Creare un account DocumentDB

Creare un account DocumentDB. Se è già disponibile un account da usare, è possibile passare a [Configurare un'applicazione Node.js](#SetupNode).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> Passaggio 2: Configurare un'applicazione Node.js

1. Aprire il terminale preferito.
2. Individuare la cartella o la directory in cui si vuole salvare l'applicazione Node.js.
3. Creare due file JavaScript vuoti con i comandi seguenti:
	- Windows:    
	    * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
	- Linux/OS X: 
	    * ```touch app.js```
        * ```touch config.js```
4. Installare il modulo documentdb tramite npm. Usare il seguente comando:
    * ```npm install documentdb --save```

L'installazione è riuscita. Dopo avere completato l'installazione, è possibile iniziare a scrivere il codice.

##<a id="Config"></a> Passaggio 3: Impostare le configurazioni dell'app

Aprire il file ```config.js``` in un editor di testo.

Creare quindi un oggetto vuoto denominato ```config``` e impostare le proprietà ```config.endpoint``` e ```config.authKey``` sull'endpoint e sulla di chiave autorizzazione di DocumentDB. Entrambe le configurazioni sono disponibili nel [portale di Azure](https://portal.azure.com).

![Esercitazione su Node.js - Schermata del portale di Azure che mostra un account DocumentDB con l'hub ATTIVO evidenziato, il pulsante CHIAVI evidenziato nel pannello dell'account DocumentDB e i valori di URI, CHIAVE PRIMARIA e CHIAVE SECONDARIA evidenziati nel pannello Chiavi - Database nodo][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

Aggiungere ora ```database id```, ```collection id``` e ```JSON documents``` all'oggetto ```config```. Nel punto in cui vengono impostate le proprietà ```config.endpoint``` e ```config.authKey``` aggiungere il codice seguente. Se sono già disponibili dati da archiviare nel database, è possibile usare lo [strumento di migrazione dati](documentdb-import-data.md) di DocumentDB invece di aggiungere le definizioni dei documenti.

    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl",
        "partitionKey": { "paths": ["/district"], "kind": "Hash" }
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "district": "WA5",
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
            "district": "NY23",
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


Le definizioni del database, della raccolta e del documento verranno usate come ```database id```, ```collection id``` e dati dei documenti di DocumentDB.

Esportare infine l'oggetto ```config``` per farvi riferimento nel file ```app.js```.

    module.exports = config;

##<a id="Connect"></a> Passaggio 4: Connettersi a un account DocumentDB

Aprire il file ```app.js``` vuoto nell'editor di testo. Importare il modulo ```documentdb``` e il modulo ```config``` appena creato.

    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Usare gli oggetti ```config.endpoint``` e ```config.authKey``` salvati in precedenza per creare un nuovo DocumentClient.

    var client = new documentClient(config.endpoint, { "masterKey": config.authKey });

Dopo la connessione a un account DocumentDB, è possibile esaminare l'utilizzo delle risorse di DocumentDB.

## Passaggio 5: Creare un database di Node
È possibile creare un [database](documentdb-resources.md#databases) con la funzione [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) della classe **DocumentClient**. Un database è un contenitore logico di archiviazione documenti partizionato nelle raccolte. Aggiungere una funzione per la creazione del nuovo database nel file app.js con l'```id``` specificato nell'oggetto ```config```. È necessario assicurarsi prima di tutto che non esista già un database con lo stesso ID ```FamilyRegistry```. Se esiste, verrà restituito il database esistente, invece di procedere con la creazione di un nuovo database.

    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    /**
     * Get the database by ID, or create if it doesn't exist.
     * @param {string} database - The database to get or create
     */
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }
##<a id="CreateColl"></a>Passaggio 6: Creare una raccolta  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** crea una nuova raccolta, che ha implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/documentdb/).

È possibile creare una [raccolta](documentdb-resources.md#collections) con la funzione [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) della classe **DocumentClient**. Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. Aggiungere una funzione per la creazione della nuova raccolta nel file app.js con l'```id``` specificato nell'oggetto ```config```. Anche in questo caso è necessario assicurarsi che non esista già una raccolta con lo stesso ID ```FamilyCollection```. Se esiste, verrà restituita la raccolta esistente, invece di procedere con la creazione di una nuova raccolta.

    /**
     * Get the collection by ID, or create if it doesn't exist.
     */
    function getCollection() {
        console.log(`Getting collection:\n${collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

##<a id="CreateDoc"></a>Passaggio 7: Creare un documento
È possibile creare un [documento](documentdb-resources.md#documents) con la funzione [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) della classe **DocumentClient**. I documenti sono contenuto JSON definito dall'utente (arbitrario). È ora possibile inserire un documento in DocumentDB.

Aggiungere quindi una funzione al file app.js per creare i documenti contenenti i dati JSON salvati nell'oggetto ```config```. Anche in questo caso è necessario assicurarsi che non esista già un documento con lo stesso ID.

    /**
     * Get the document by ID, or create if it doesn't exist.
     * @param {function} callback - The callback function on completion
     */
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

La procedura è stata completata. Sono ora disponibili funzioni per la creazione di un database, una raccolta e un documento in DocumentDB.

![Esercitazione su Node.js - Diagramma che illustra la relazione gerarchica tra l'account, il database, la raccolta e i documenti - Database nodo](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Passaggio 8: Eseguire query sulle risorse di DocumentDB

DocumentDB supporta [query complesse](documentdb-sql-query.md) sui documenti JSON archiviati in ogni raccolta. L'esempio di codice seguente illustra una query eseguibile nei documenti della raccolta. Aggiungere la funzione seguente al file ```app.js```. DocumentDB supporta query simili a SQL, come illustrato di seguito. Per altre informazioni sulla creazione di query complesse, vedere la pagina[Query Playground](https://www.documentdb.com/sql/demo) e la [documentazione sulle query](documentdb-sql-query.md).

    /**
     * Query the collection using SQL
     */
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.address.city FROM root r WHERE r.lastName = "Andersen"',
                { enableCrossPartitionQuery: true }
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        console.log(`Query returned ${queryResult}`);
                    }
                    resolve(results);
                }
            });
        });
    };


Il diagramma seguente illustra il modo in cui la sintassi di query SQL di DocumentDB viene chiamata nella raccolta creata.

![Esercitazione su Node.js - Diagramma che illustra l'ambito e il significato della query - Database nodo](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

La parola chiave [FROM](documentdb-sql-query.md/#from-clause) è facoltativa nella query perché le query di DocumentDB sono già limitate a una singola raccolta. Di conseguenza, "FROM Families f" può essere scambiata con "FROM root r" o con il nome di qualsiasi altra variabile scelta. DocumentDB dedurrà che Families, root o il nome della variabile scelta, si riferisce per impostazione predefinita alla raccolta attuale.

##<a id="DeleteDatabase"></a>Passaggio 9: Eliminare il database di Node

Se si elimina il database creato, verrà rimosso il database e tutte le risorse figlio (raccolte, documenti e così via). È possibile eliminare il database aggiungendo il frammento di codice seguente.

    /**
     * Cleanup the database and collection on completion
     */
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

##<a id="Build"></a>Passaggio 10: Operazioni finali

Dopo avere configurato tutte le funzioni necessarie per l'applicazione, è possibile chiamarle.

Aggiungere il frammento di codice seguente alla fine del codice nel file ```app.js```.

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

    getDatabase()
        .then(() => getCollection())
        .then(() => getFamilyDocument(config.documents.Andersen))
        .then(() => getFamilyDocument(config.documents.Wakefield))
        .then(() => queryCollection())
        .then(() => cleanup())
        .then(() => { exit(`Completed successfully`); })
        .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a> Passaggio 11: Eseguire l'applicazione Node.js

È ora possibile eseguire l'applicazione Node.js.

Nel terminale trovare il file ```app.js``` ed eseguire il comando ```node app.js```.

Verrà visualizzato l'output dell'app introduttiva. L'output dovrebbe essere analogo al testo di esempio seguente.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
      Query returned Seattle

    Cleaning up by deleting database FamilyDB

    Completed successfully
    Press any key to exit

Congratulazioni. È stata completata l'esercitazione su Node.js ed stata creata la prima applicazione console DocumentDB.

##<a id="GetSolution"></a> Ottenere la soluzione completa per l'esercitazione su Node.js
Per creare la soluzione GetStarted completa contenente tutti gli esempi riportati in questo articolo, è necessario avere:

-   [Account DocumentDB][documentdb-create-account].
-   La soluzione [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponibile su GitHub.

Installare il modulo **documentdb** tramite npm. Usare il seguente comando:
* ```npm install documentdb --save```

Nel file ```config.js``` aggiornare quindi i valori config.endpoint e config.authKey, come illustrato nel [Passaggio 3: Impostare le configurazioni dell'app](#Config).

## Passaggi successivi

-   Per un esempio più complesso relativo a Node.js, vedere [Creare un'applicazione Web Node.js con DocumentDB](documentdb-nodejs-application.md).
-	Informazioni su come [monitorare un account DocumentDB](documentdb-monitor-accounts.md).
-	Eseguire query sul set di dati di esempio illustrato nella pagina [Query Playground](https://www.documentdb.com/sql/demo).
-	Per altre informazioni sul modello di programmazione, vedere la sezione relativa allo sviluppo nella pagina [Documentazione di DocumentDB](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0330_2016-->