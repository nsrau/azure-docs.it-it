---
title: Usare l'API di Azure Cosmos DB per MongoDB per creare un'app Node.js
description: Un'esercitazione che crea un database online usando l'API di Azure Cosmos DB per MongoDB.
keywords: esempi di mongodb
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: 98a11f6ba0608a9ab39bf258056f0fa17a41653e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788345"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Creare un'app usando Node.js e l'API di Azure Cosmos DB per MongoDB 
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js per MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Questo esempio mostra come creare un'app console usando Node.js e l'API di Azure Cosmos DB per MongoDB.

Per usare questo esempio è necessario:

* [Creare](create-mongodb-dotnet.md#create-account) un account Cosmos configurato per usare l'API di Azure Cosmos DB per MongoDB.
* Recuperare le informazioni relative alla [stringa di connessione](connect-mongodb-account.md).

## <a name="create-the-app"></a>Creare l'app

1. Creare un file *app.js* e copiare e incollare il codice seguente.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Facoltativo**: Se si usa il **driver Node.js 2.2 di MongoDB**, sostituire il frammento di codice seguente:

    Originale:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Deve essere sostituito con:

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Modificare le variabili seguenti nel file *app.js* in base alle impostazioni dell'account (informazioni su come trovare la [stringa di connessione](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > Il **driver Node.js 3.0 di MongoDB** richiede la codifica dei caratteri speciali nella password di Cosmos DB. Assicurarsi di codificare i caratteri '=' come %3D
    >
    > Esempio: La password *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* viene codificata in *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > Il **driver Node.js 2.2 di MongoDB** non richiede la codifica dei caratteri speciali nella password di Cosmos DB.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Aprire il terminale preferito, eseguire **npm install mongodb --save**, quindi eseguire l'app con **node app.js**

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con l'API Azure Cosmos DB per MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con l'API Azure Cosmos DB per MongoDB.
- Esplorare gli [esempi](mongodb-samples.md) di MongoDB con l'API Azure Cosmos DB per MongoDB.
