---
title: Usare le API di MongoDB per creare un'app di Azure Cosmos DB
description: Un'esercitazione che crea un database online usando le API di Azure Cosmos DB per MongoDB.
keywords: esempi di mongodb
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: e24c64f0042d65cc499e02dff7b53f3ca812f356
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52864078"
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Creare un'app basata su Azure Cosmos DB: API per MongoDB usando Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js per MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

In questo esempio viene mostrato come creare un'app console basata su Azure Cosmos DB: API per MongoDB usando Node.js.

Per usare questo esempio è necessario:

* [Creare](create-mongodb-dotnet.md#create-account) un account Azure Cosmos DB: API per MongoDB.
* Recuperare le informazioni sulla [stringa di connessione](connect-mongodb-account.md) di MongoDB.

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
    
    **Facoltativo**: se si usa il **driver Node.js 2.2 di MongoDB**, sostituire il frammento di codice seguente:

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
    > Esempio: la password *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* viene codificata in *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > Il **driver Node.js 2.2 di MongoDB** non richiede la codifica dei caratteri speciali nella password di Cosmos DB.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Aprire il terminale preferito, eseguire **npm install mongodb --save**, quindi eseguire l'app con **node app.js**

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come [usare MongoChef](mongodb-mongochef.md) con l'account Azure Cosmos DB: API per MongoDB.
