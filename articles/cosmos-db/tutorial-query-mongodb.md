---
title: "Azure Cosmos DB: procedura per l'esecuzione di query con l'API MongoDB | Microsoft Docs"
description: Informazioni su come eseguire query con l'API MongoDB per Azure Cosmos DB
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/29/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: d8d524038d6483ff5da195648ee763f8faa1dad4
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-mongodb-api"></a>Esercitazione: Eseguire query in Azure Cosmos DB con l'API di MongoDB

L'[API per MongoDB](mongodb-introduction.md) supporta l'esecuzione di [query nella shell di MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

Questo articolo illustra le attività seguenti: 

> [!div class="checklist"]
> * Esecuzione di query sui dati con MongoDB

Per iniziare, guardare questo video con Andy Hoh, Program Manager di Azure Cosmos DB, sull'esecuzione di query in MongoDB:

>[!VIDEO https://www.youtube.com/tVk8S7lFWMA]

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
    
    db.families.find({ id: “WakefieldFamily”})

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
    
    db.families.find( { id: “WakefieldFamily” }, { children: true } )

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
> * È stato appreso come eseguire una query usando MongoDB 

È ora possibile passare all'esercitazione successiva per imparare a distribuire i dati a livello globale.

> [!div class="nextstepaction"]
> [Distribuire i dati a livello globale](tutorial-global-distribution-sql-api.md)

