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
  ms.date="08/11/2016"
  ms.author="anhoh"/>

# Esercitazione su Node.js NoSQL: applicazione console Node.js di DocumentDB  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.JS](documentdb-nodejs-get-started.md)

Esercitazione su Node.js per Azure DocumentDB Node.js SDK Dopo aver seguito questa esercitazione, si otterrà un'applicazione console che consente di creare ed eseguire query sulle risorse di DocumentDB, incluso un database nodo.

Tratteremo questo argomento:

- Creazione e connessione a un account DocumentDB
- Configurazione dell'applicazione
- Creazione di un database nodo
- Creare una raccolta
- Creazione di documenti JSON
- Esecuzione di query sulla raccolta
- Sostituzione di un documento
- Eliminazione di un documento
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

## <a id="SetupNode"></a>Passaggio 2: Configurare un'applicazione Node.js

1. Aprire il terminale preferito.
2. Individuare la cartella o la directory in cui si vuole salvare l'applicazione Node.js.
3. Creare due file JavaScript vuoti con i comandi seguenti:
  - Windows:
      * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
  - Linux/OS X:
      * ```touch app.js```
        * ```touch config.js```
4. Installare il modulo documentdb tramite npm. Usare il comando seguente:
    * ```npm install documentdb --save```

L'installazione è riuscita. Dopo avere completato l'installazione, è possibile iniziare a scrivere il codice.

## <a id="Config"></a>Passaggio 3: Impostare le configurazioni dell'app

Aprire il file ```config.js``` in un editor di testo.

Quindi, copiare e incollare il frammento di codice riportato di seguito e impostare le proprietà ```config.endpoint``` e ```config.primaryKey``` sull'URI dell'endpoint DocumentDB e la chiave primaria. Entrambe le configurazioni sono disponibili nel [portale di Azure](https://portal.azure.com).

![Esercitazione su Node.js - Schermata del portale di Azure che mostra un account DocumentDB con l'hub ATTIVO evidenziato, il pulsante CHIAVI evidenziato nel pannello dell'account DocumentDB e i valori di URI, CHIAVE PRIMARIA e CHIAVE SECONDARIA evidenziati nel pannello Chiavi - Database nodo][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Copiare e incollare ```database id```, ```collection id``` e ```JSON documents``` nell'oggetto ```config``` seguente in cui sono impostate le proprietà ```config.endpoint``` e ```config.authKey```. Se sono già disponibili dati da archiviare nel database, è possibile usare lo [strumento di migrazione dati](documentdb-import-data.md) di DocumentDB invece di aggiungere le definizioni dei documenti.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
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


Le definizioni del database, della raccolta e del documento verranno usate come ```database id```, ```collection id``` e dati dei documenti di DocumentDB.

Esportare infine l'oggetto ```config``` per farvi riferimento nel file ```app.js```.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

##<a id="Connect"></a> Passaggio 4: Connettersi a un account DocumentDB

Aprire il file ```app.js``` vuoto nell'editor di testo. Copiare e incollare il codice seguente per importare il modulo ```documentdb``` e il modulo ```config``` appena creato.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Copiare e incollare il codice per usare gli oggetti ```config.endpoint``` e ```config.primaryKey``` salvati in precedenza per creare un nuovo DocumentClient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Ora che è disponibile il codice per inizializzare il client DocumentDB, è possibile esaminare l'uso delle risorse di DocumentDB.

## Passaggio 5: Creare un database di Node
Copiare e incollare il codice seguente per impostare lo stato HTTP per l'URL della raccolta, l'URL di database e Non trovato. Tali URL definiscono il modo in cui il client DocumentDB trova il database e la raccolta corretti.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

È possibile creare un [database](documentdb-resources.md#databases) con la funzione [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) della classe **DocumentClient**. Un database è un contenitore logico di archiviazione documenti partizionato nelle raccolte.

Copiare e incollare la funzione **getDatabase** per la creazione del nuovo database nel file app.js con l'oggetto ```id``` specificato in ```config```. La funzione controlla se esiste già un database con lo stesso ID ```FamilyRegistry```. Se esiste, verrà restituito il database esistente, invece di procedere con la creazione di un nuovo database.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
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

Copiare e incollare il codice seguente in cui viene impostata la funzione **getDatabase** per aggiungere la funzione di supporto **exit**, che stampa il messaggio di uscita e la chiamata alla funzione **getDatabase**.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale trovare il file ```app.js``` ed eseguire il comando ```node app.js```.

Congratulazioni. La creazione di un database di DocumentDB è stata completata.

##<a id="CreateColl"></a>Passaggio 6: Creare una raccolta  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** crea una nuova raccolta, che ha implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/documentdb/).

È possibile creare una [raccolta](documentdb-resources.md#collections) con la funzione [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) della classe **DocumentClient**. Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata.

Copiare e incollare la funzione **getCollection** sotto la funzione **getDatabase** per la creazione della nuova raccolta con l'oggetto ```id``` specificato in ```config```. Anche in questo caso è necessario assicurarsi che non esista già una raccolta con lo stesso ID ```FamilyCollection```. Se esiste, verrà restituita la raccolta esistente, invece di procedere con la creazione di una nuova raccolta.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

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

Copiare e incollare il codice sotto la chiamata a **getDatabase** per eseguire la funzione **getCollection**.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale trovare il file ```app.js``` ed eseguire il comando ```node app.js```.

Congratulazioni. La creazione di una raccolta di DocumentDB è stata completata.

##<a id="CreateDoc"></a>Passaggio 7: Creare un documento
È possibile creare un [documento](documentdb-resources.md#documents) con la funzione [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) della classe **DocumentClient**. I documenti sono contenuto JSON definito dall'utente (arbitrario). È ora possibile inserire un documento in DocumentDB.

Copiare e incollare la funzione **getFamilyDocument** sotto la funzione **getCollection** per la creazione di documenti che contengono i dati JSON salvati nell'oggetto ```config```. Anche in questo caso è necessario assicurarsi che non esista già un documento con lo stesso ID.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
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

Copiare e incollare il codice sotto la chiamata a **getCollection** per eseguire la funzione **getFamilyDocument**.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale trovare il file ```app.js``` ed eseguire il comando ```node app.js```.

Congratulazioni. La creazione di un documento di DocumentDB è stata completata.

![Esercitazione su Node.js - Diagramma che illustra la relazione gerarchica tra l'account, il database, la raccolta e i documenti - Database nodo](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>Passaggio 8: Eseguire query sulle risorse di DocumentDB

DocumentDB supporta [query complesse](documentdb-sql-query.md) sui documenti JSON archiviati in ogni raccolta. L'esempio di codice seguente illustra una query eseguibile nei documenti della raccolta.

Copiare e incollare la funzione **queryCollection** sotto la funzione **getFamilyDocument**. DocumentDB supporta query simili a SQL, come illustrato di seguito. Per altre informazioni sulla creazione di query complesse, vedere la pagina[Query Playground](https://www.documentdb.com/sql/demo) e la [documentazione sulle query](documentdb-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


Il diagramma seguente illustra il modo in cui la sintassi di query SQL di DocumentDB viene chiamata nella raccolta creata.

![Esercitazione su Node.js - Diagramma che illustra l'ambito e il significato della query - Database nodo](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

La parola chiave [FROM](documentdb-sql-query.md#from-clause) è facoltativa nella query perché le query di DocumentDB sono già limitate a una singola raccolta. Di conseguenza, "FROM Families f" può essere scambiata con "FROM root r" o con il nome di qualsiasi altra variabile scelta. DocumentDB dedurrà che Families, root o il nome della variabile scelta, si riferisce per impostazione predefinita alla raccolta attuale.

Copiare e incollare il codice sotto la chiamata a **getFamilyDocument** per eseguire la funzione **queryCollection**.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale trovare il file ```app.js``` ed eseguire il comando ```node app.js```.

Congratulazioni. La creazione di documenti di DocumentDB con query è stata completata.

##<a id="ReplaceDocument"></a>Passaggio 9: Sostituire un documento
DocumentDB supporta la sostituzione di documenti JSON.

Copiare e incollare la funzione **replaceDocument** sotto la funzione **queryCollection**.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copiare e incollare il codice sotto la chiamata a **queryCollection** per eseguire la funzione **replaceDocument**. Aggiungere anche il codice per chiamare nuovamente **queryCollection** e verificare che il documento sia stato modificato correttamente.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale trovare il file ```app.js``` ed eseguire il comando ```node app.js```.

Congratulazioni. La sostituzione di un documento di DocumentDB è stata completata.

##<a id="DeleteDocument"></a>Passaggio 10: Eliminare un documento
DocumentDB supporta l'eliminazione di documenti JSON.

Copiare e incollare la funzione **deleteDocument** sotto la funzione **replaceDocument**.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copiare e incollare il codice sotto la chiamata alla seconda funzione **queryCollection** per eseguire la funzione **deleteDocument**.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Nel terminale trovare il file ```app.js``` ed eseguire il comando ```node app.js```.

Congratulazioni. L'eliminazione di un documento di DocumentDB è stata completata.

##<a id="DeleteDatabase"></a>Passaggio 11: Eliminare il database Node

Se si elimina il database creato, verrà rimosso il database e tutte le risorse figlio (raccolte, documenti e così via).

Copiare e incollare il frammento di codice seguente della funzione **cleanup** per rimuovere il database e tutte le risorse figlio.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Copiare e incollare il codice sotto la chiamata a **deleteDocument** per eseguire la funzione **cleanup**.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a> Passaggio 12: Eseguire l'applicazione Node.js

In generale, la sequenza per chiamare le funzioni dovrebbe avere un aspetto simile al seguente:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

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
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Congratulazioni. È stata completata l'esercitazione su Node.js ed stata creata la prima applicazione console DocumentDB.

## <a id="GetSolution"></a>Ottenere la soluzione completa per l'esercitazione su Node.js
Per creare la soluzione GetStarted completa contenente tutti gli esempi riportati in questo articolo, è necessario avere:

-   [Account DocumentDB][documentdb-create-account].
-   La soluzione [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponibile su GitHub.

Installare il modulo **documentdb** tramite npm. Usare il comando seguente:
* ```npm install documentdb --save```

Nel file ```config.js``` aggiornare quindi i valori config.endpoint e config.authKey, come illustrato nel [Passaggio 3: Impostare le configurazioni dell'app](#Config).

## Passaggi successivi

-   Per un esempio più complesso relativo a Node.js, vedere [Creare un'applicazione Web Node.js con DocumentDB](documentdb-nodejs-application.md).
-  Informazioni su come [monitorare un account DocumentDB](documentdb-monitor-accounts.md).
-  Eseguire query sul set di dati di esempio illustrato nella pagina [Query Playground](https://www.documentdb.com/sql/demo).
-  Per altre informazioni sul modello di programmazione, vedere la sezione relativa allo sviluppo nella pagina [Documentazione di DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0817_2016-->