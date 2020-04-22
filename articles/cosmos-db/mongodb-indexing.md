---
title: Gestire l'indicizzazione nell'API di Azure Cosmos DB per MongoDBManage indexing in Azure Cosmos DB's API for MongoDB
description: Questo articolo illustra una panoramica delle funzionalità di indicizzazione di Azure Cosmos DB usando l'API MongoDB.This article presents an overview of Azure Cosmos DB indexing capabilities using the MongoDB API.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732713"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Gestire l'indicizzazione nell'API di Azure Cosmos DB per MongoDBManage indexing in Azure Cosmos DB's API for MongoDB

L'API di Azure Cosmos DB per MongoDB sfrutta le funzionalità di base di gestione degli indici di Azure Cosmos DB. Questo articolo è incentrato su come aggiungere indici usando l'API di Azure Cosmos DB per MongoDB.This article focuses on how to add indexes using Azure Cosmos DB's API for MongoDB. È anche possibile leggere [una panoramica dell'indicizzazione in Azure Cosmos DB](index-overview.md) che è rilevante in tutte le API.

## <a name="indexing-for-mongodb-server-version-36"></a>Indicizzazione per il server MongoDB versione 3.6

L'API di Azure Cosmos DB per MongoDB server `_id` versione 3.6 indicizza automaticamente il campo, che non può essere eliminato. Applica automaticamente l'univocità `_id` del campo per chiave di partizione.

Per indicizzare campi aggiuntivi, applicare i comandi di gestione dell'indice MongoDB. Come in MongoDB, l'API di Azure Cosmos DB `_id` per MongoDB indicizza automaticamente solo il campo. Questo criterio di indicizzazione predefinito è diverso dall'API SQL del database Cosmos di Azure, che indicizza tutti i campi per impostazione predefinita.

Per applicare un ordinamento a una query, è necessario creare un indice sui campi utilizzati nell'operazione di ordinamento.

## <a name="index-types"></a>Tipi di indice

### <a name="single-field"></a>Campo singolo

È possibile creare indici in qualsiasi singolo campo. L'ordinamento dell'indice a campo singolo non è rilevante. Il comando seguente crea un `name`indice nel campo :

`db.coll.createIndex({name:1})`

Una query utilizza più indici di campo singolo, se disponibili. È possibile creare fino a 500 indici di campo singolo per contenitore.

### <a name="compound-indexes-mongodb-server-version-36"></a>Indici composti (server MongoDB versione 3.6)Compound indexes (MongoDB server version 3.6)

L'API di Azure Cosmos DB per MongoDB supporta gli indici composti per gli account che usano il protocollo di rete versione 3.6.Azure Cosmos DB's API for MongoDB supports compound indexes for accounts that use the version 3.6 wire protocol. È possibile includere fino a otto campi in un indice composto. A differenza di MongoDB, è consigliabile creare un indice composto solo se la query deve eseguire l'ordinamento efficiente in più campi contemporaneamente. Per le query con più filtri che non è necessario eseguire l'ordinamento, creare più indici di campo singolo anziché un singolo indice composto.

Il comando seguente crea un `name` indice `age`composto sui campi e:

`db.coll.createIndex({name:1,age:1})`

È possibile utilizzare gli indici composti per ordinare in modo efficiente su più campi contemporaneamente, come illustrato nell'esempio seguente:You can use compound indexes to sort efficiently on multiple fields at once, as shown in the following example:

`db.coll.find().sort({name:1,age:1})`

È inoltre possibile utilizzare l'indice composto precedente per ordinare in modo efficiente in una query con l'ordinamento opposto in tutti i campi. Ad esempio:

`db.coll.find().sort({name:-1,age:-1})`

Tuttavia, la sequenza dei percorsi nell'indice composto deve corrispondere esattamente alla query. Di seguito è riportato un esempio di una query che richiede un indice composto aggiuntivo:Here's an example of a query that would require an additional compound index:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Indici multichiave

Azure Cosmos DB crea indici multichiave per indicizzare il contenuto archiviato negli array. Se si indicizza un campo con un valore di matrice, Azure Cosmos DB indicizza automaticamente ogni elemento della matrice.

### <a name="geospatial-indexes"></a>Indici geospaziali

Molti operatori geospaziali trarranno vantaggio dagli indici geospaziali. Attualmente, l'API di Azure Cosmos DB `2dsphere` per MongoDB supporta gli indici. L'API non `2d` supporta ancora gli indici.

Di seguito è riportato un esempio `location` di creazione di un indice geospaziale nel campo:Here's an example of creating a geospatial index on the field:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Indici di testo

L'API di Azure Cosmos DB per MongoDB non supporta attualmente gli indici di testo. Per le query di ricerca di testo sulle stringhe, è consigliabile usare l'integrazione di [Ricerca cognitiva di Azure](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) con Azure Cosmos DB.

## <a name="index-properties"></a>Proprietà degli indici

Le operazioni seguenti sono comuni per gli account che servono la versione 3.6 del protocollo via cavo e gli account che servono le versioni precedenti. Ulteriori informazioni sugli [indici supportati e sulle proprietà indicizzate](mongodb-feature-support-36.md#indexes-and-index-properties)sono in grado di ottenere ulteriori informazioni.

### <a name="unique-indexes"></a>Indici univoci

[Gli indici univoci](unique-keys.md) sono utili per fare in modo che due o più documenti non contengano lo stesso valore per i campi indicizzati.

> [!IMPORTANT]
> Gli indici univoci possono essere creati solo quando la raccolta è vuota (non contiene documenti).

Il comando seguente crea un `student_id`indice univoco nel campo :

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Per le raccolte partizionate, è necessario fornire la chiave di partizione (partizione) per creare un indice univoco. In altre parole, tutti gli indici univoci in una raccolta partizionata sono indici composti in cui uno dei campi è la chiave di partizione.

I comandi seguenti creano una ```coll``` raccolta partizionata ```university```(la chiave di `student_id` partizione è ) con un indice univoco nei campi e `university`:

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

Nell'esempio precedente, l'omissione della ```"university":1``` clausola restituisce un errore con il seguente messaggio:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>Indici TTL

Per abilitare la scadenza dei documenti in una raccolta specifica, è necessario creare un [indice TTL (Time-To-Live).](../cosmos-db/time-to-live.md) Un indice TTL è `_ts` un indice `expireAfterSeconds` nel campo con un valore.

Esempio:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Il comando precedente elimina tutti ```db.coll``` i documenti della raccolta che non sono stati modificati negli ultimi 10 secondi.

> [!NOTE]
> Il campo **_ts** è specifico di Azure Cosmos DB e non è accessibile dai client MongoDB. Si tratta di una proprietà riservata (di sistema) che contiene il timestamp dell'ultima modifica del documento.

## <a name="track-index-progress"></a>Tenere traccia dello stato di avanzamento dell'indice

La versione 3.6 dell'API di Azure Cosmos `currentOp()` DB per MongoDB supporta il comando per tenere traccia dello stato di avanzamento dell'indice in un'istanza di database. Questo comando restituisce un documento che contiene informazioni sulle operazioni in corso in un'istanza di database. Utilizzare il `currentOp` comando per tenere traccia di tutte le operazioni in corso in MongoDB nativo. Nell'API di Azure Cosmos DB per MongoDB, questo comando supporta solo il rilevamento dell'operazione sull'indice.

Ecco alcuni esempi che illustrano `currentOp` come usare il comando per tenere traccia dello stato di avanzamento dell'indice:Here are some examples that show how to use the command to track index progress:

* Ottenere lo stato di avanzamento dell'indice per una raccolta:Get the index progress for a collection:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Ottenere lo stato di avanzamento dell'indice per tutte le raccolte in un database:Get the index progress for all collections in a database:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Ottenere lo stato di avanzamento dell'indice per tutti i database e le raccolte in un account Cosmos di Azure:Get the index progress for all databases and collections in an Azure Cosmos account:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Esempi di output di avanzamento dell'indice

I dettagli sullo stato di avanzamento dell'indice mostrano la percentuale di avanzamento dell'operazione sull'indice corrente. Ecco un esempio che mostra il formato del documento di output per le diverse fasi di avanzamento dell'indice:Here's an example that shows the output document format for different stages of index progress:

- Un'operazione sull'indice in una raccolta "foo" e un database "bar" che è completato al 60% avranno il seguente documento di output. Nel `Inprog[0].progress.total` campo viene visualizzato 100 come percentuale di completamento prevista.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

- Se un'operazione sull'indice è appena iniziata in una raccolta "foo" e in un database "barra", il documento di output potrebbe mostrare lo 0 percento di avanzamento fino a raggiungere un livello misurabile.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

- Al termine dell'operazione di indicizzazione in corso, `inprog` nel documento di output vengono visualizzate operazioni vuote.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Aggiornamenti dell'indice in background

Indipendentemente dal valore specificato per la proprietà dell'indice **Background,** gli aggiornamenti dell'indice vengono sempre eseguiti in background. Poiché gli aggiornamenti dell'indice utilizzano le unità richiesta (RU) con una priorità inferiore rispetto ad altre operazioni di database, le modifiche all'indice non comporteranno tempi di inattività per scritture, aggiornamenti o eliminazioni.

Quando si aggiunge un nuovo indice, le query utilizzeranno immediatamente l'indice. Ciò significa che le query potrebbero non restituire tutti i risultati corrispondenti e lo faranno senza restituire errori. Al termine della trasformazione dell'indice, i risultati della query saranno coerenti. È possibile tenere traccia dello stato di [avanzamento dell'indice.](#track-index-progress)

## <a name="migrate-collections-with-indexes"></a>Eseguire la migrazione di raccolte con indiciMigrate collections with indexes

Attualmente, è possibile creare indici univoci solo quando la raccolta non contiene documenti. Gli strumenti di migrazione MongoDB più diffusi tentano di creare gli indici univoci dopo l'importazione dei dati. Per aggirare questo problema, è possibile creare manualmente le raccolte corrispondenti e gli indici univoci anziché consentire allo strumento di migrazione di provare. (È possibile ottenere ```mongorestore``` questo comportamento `--noIndexRestore` per utilizzando il flag nella riga di comando.)

## <a name="indexing-for-mongodb-version-32"></a>Indicizzazione per MongoDB versione 3.2

Le funzionalità di indicizzazione e le impostazioni predefinite disponibili sono diverse per gli account Cosmos di Azure compatibili con la versione 3.2 del protocollo wire MongoDB.Available indexing features and defaults are different for Azure Cosmos accounts that are compatible with version 3.2 of the MongoDB wire protocol. Puoi [controllare la versione del tuo account.](mongodb-feature-support-36.md#protocol-support) È possibile eseguire l'aggiornamento alla versione 3.6 inviando una richiesta di [supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Se si utilizza la versione 3.2, in questa sezione vengono descritte le differenze principali con la versione 3.6.

### <a name="dropping-default-indexes-version-32"></a>Eliminazione degli indici predefiniti (versione 3.2)Dropping default indexes (version 3.2)

A differenza della versione 3.6 dell'API di Azure Cosmos DB per MongoDB, la versione 3.2 indicizza ogni proprietà per impostazione predefinita. È possibile utilizzare il comando seguente per eliminare questi```coll```indici predefiniti per una raccolta ( ):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Dopo aver rilasciato gli indici predefiniti, è possibile aggiungere altri indici come si farebbe nella versione 3.6.After dropping the default indexes, you can add more indexes as you would in version 3.6.

### <a name="compound-indexes-version-32"></a>Indici composti (versione 3.2)Compound indexes (version 3.2)

Gli indici composti contengono riferimenti a più campi di un documento. Se si desidera creare un indice composto, eseguire l'aggiornamento alla versione 3.6 inviando una richiesta di [supporto.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="next-steps"></a>Passaggi successivi

* [Indicizzazione in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Impostare la scadenza automatica dei dati in Azure Cosmos DB con la durata (TTL)](../cosmos-db/time-to-live.md)
