---
title: Eseguire query sui dati con l'API Azure Cosmos DB per MongoDB
description: Informazioni su come eseguire query sui dati dall'API di Azure Cosmos DB per MongoDB usando i comandi della shell MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 5b9bc78f6af833d89a3404de0295ddad78ebdf20
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870140"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Eseguire query sui dati usando l'API Azure Cosmos DB per MongoDB

L'[API Azure Cosmos DB per MongoDB](mongodb-introduction.md) supporta l'esecuzione di [query su MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Esecuzione di query sui dati archiviati nel database Cosmos tramite la shell MongoDB

Per iniziare, è possibile usare gli esempi in questo documento e guardare il video sull'[esecuzione di query su Azure Cosmos DB con la shell MongoDB](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/).

## <a name="sample-document"></a>Documento di esempio

Le query di questo articolo usano il documento di esempio seguente.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a id="examplequery1"></a>Query di esempio 1 

Nel precedente documento della famiglia, la query seguente restituisce i documenti in cui il campo ID corrisponde a `WakefieldFamily`.

**Query**
    
    db.families.find({ id: "WakefieldFamily"})

**Risultati**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a>Query di esempio 2 

La query seguente restituisce tutti i figli della famiglia. 

**Query**
    
    db.families.find( { id: "WakefieldFamily" }, { children: true } )

**Risultati**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a>Query di esempio 3 

La query seguente restituisce tutte le famiglie registrate. 

**Query**
    
    db.families.find( { "isRegistered" : true })
**Risultati** Non viene restituito alcun documento. 

## <a id="examplequery4"></a>Query di esempio 4

La query seguente restituisce tutte le famiglie non registrate. 

**Query**
    
    db.families.find( { "isRegistered" : false })
**Risultati**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a>Query di esempio 5

La query seguente restituisce tutte le famiglie non registrate e il cui stato di residenza è NY. 

**Query**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**Risultati**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a>Query di esempio 6

La query seguente restituisce tutte le famiglie in cui il grado della classe frequentata dai figli corrisponde a 8.

**Query**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**Risultati**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a>Query di esempio 7

La query seguente restituisce tutte le famiglie in cui la dimensione della matrice dei figli corrisponde a 3.

**Query**
  
      db.Family.find( {children: { $size:3} } )

**Risultati**

Non verranno restituiti risultati perché non ci sono famiglie con più di due figli. La query avrà esito positivo e restituirà l'intero documento solo quando il parametro è 2.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state eseguite le operazioni seguenti:

> [!div class="checklist"]
> * Si è appreso come eseguire una query usando l'API Cosmos DB per MongoDB

È ora possibile passare all'esercitazione successiva per imparare a distribuire i dati a livello globale.

> [!div class="nextstepaction"]
> [Distribuire i dati a livello globale](tutorial-global-distribution-sql-api.md)

