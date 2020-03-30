---
title: Indicizzazione nell'API di Azure Cosmos DB per MongoDB
description: Presenta una panoramica delle funzionalità di indicizzazione nell'API di Azure Cosmos DB per MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/27/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 7c75f0d6f74fe8cf1417e0dc40a5ad01615d7057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371065"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indicizzazione con l'API di Azure Cosmos DB per MongoDB

L'API di Azure Cosmos DB per MongoDB sfrutta le funzionalità di base di gestione degli indici di Azure Cosmos DB. Questo documento è incentrato su come aggiungere indici usando l'API di Azure Cosmos DB per MongoDB.This document focuses on how to add indexes using Azure Cosmos DB's API for MongoDB. È anche possibile leggere [una panoramica dell'indicizzazione in Azure Cosmos DB](index-overview.md) che è rilevante in tutte le API.

## <a name="indexing-for-version-36"></a>Indicizzazione per la versione 3.6Indexing for version 3.6

Il `_id` campo viene sempre indicizzato automaticamente e non può essere eliminato. L'API di Azure Cosmos DB per MongoDB `_id` applica automaticamente l'univocità del campo per chiave di partizione.

Per indicizzare campi aggiuntivi, è necessario applicare i comandi di gestione dell'indice MongoDB. Come in MongoDB, `_id` solo il campo viene indicizzato automaticamente. Questo criterio di indicizzazione predefinito è diverso dall'API SQL del database Cosmos di Azure, che indicizza tutti i campi per impostazione predefinita.

Per applicare un ordinamento a una query, è necessario creare un indice sui campi utilizzati nell'operazione di ordinamento.

## <a name="index-types"></a>Tipi di indice

### <a name="single-field"></a>Campo singolo

È possibile creare indici in qualsiasi singolo campo. L'ordinamento dell'indice a campo singolo non è rilevante. Il comando seguente consente di `name`creare un indice nel campo :

`db.coll.createIndex({name:1})`

Una query utilizzerà più indici di campo singolo, se disponibile.

### <a name="compound-indexes-36"></a>Indici composti (3.6)

Gli indici composti sono supportati per gli account che utilizzano il protocollo wire 3.6.Compound indexes are supported for accounts using the 3.6 wire protocol. È possibile includere fino a 8 campi in un indice composto. A differenza di MongoDB, è consigliabile creare un indice composto solo se la query deve eseguire l'ordinamento efficiente in base a più campi contemporaneamente. Per le query con più filtri che non è necessario eseguire l'ordinamento, è consigliabile creare più indici di campo singolo anziché un singolo indice composto.

Il comando seguente consente di creare `name` `age`un indice composto nei campi e:

`db.coll.createIndex({name:1,age:1})`

Gli indici composti possono essere usati per ordinare in modo efficiente più campi contemporaneamente, ad esempio:Compound indexes can be used to sort efficiently on multiple fields at once, such as:

`db.coll.find().sort({name:1,age:1})`

L'indice composto precedente può essere utilizzato anche per un ordinamento efficiente in una query con ordinamento opposto in tutti i campi. Ad esempio:

`db.coll.find().sort({name:-1,age:-1})`

Tuttavia, la sequenza dei percorsi nell'indice composto deve corrispondere esattamente alla query. Di seguito è riportato un esempio di una query che richiede un indice composto aggiuntivo:Here's an example of a query that would require an additional compound index:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Indici multichiave

Azure Cosmos DB crea indici multichiave per indicizzare il contenuto archiviato negli array. Se si indicizza un campo con un valore di matrice, Azure Cosmos DB indicizza automaticamente ogni elemento della matrice.

### <a name="geospatial-indexes"></a>Indici geospaziali

Molti operatori geospaziali trarranno vantaggio dagli indici geospaziali. Attualmente, l'API di Azure Cosmos DB `2dsphere` per MongoDB supporta gli indici. `2d`indici non sono ancora supportati.

Di seguito è riportato un esempio `location` per la creazione di un indice geospaziale nel campo:Here's an example for creating a geospatial index on the field:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Indici di testo

Gli indici di testo non sono attualmente supportati. Per le query di ricerca di testo sulle stringhe, è consigliabile usare [l'integrazione di Ricerca cognitiva di Azure](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) con Azure Cosmos DB.

## <a name="index-properties"></a>Proprietà degli indici

Le operazioni seguenti sono comuni sia per gli account che servono la versione 3.6 del protocollo via cavo che per gli account che servono versioni precedenti del protocollo via cavo. Ulteriori informazioni sugli [indici supportati e sulle proprietà indicizzate](mongodb-feature-support-36.md#indexes-and-index-properties)sono disponibili.

### <a name="unique-indexes"></a>Indici univoci

Gli [indici univoci](unique-keys.md) sono utili per impedire che due o più documenti contengano lo stesso valore per i campi indicizzati.

>[!Important]
> Gli indici univoci possono essere creati solo quando la raccolta è vuota (non contiene documenti).

Il comando seguente consente di creare un indice univoco sul campo "student_id":

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

Per le raccolte partizionate, la creazione di un indice univoco richiede la fornitura della chiave di partizione (partizione). In altre parole, tutti gli indici univoci in una raccolta partizionata sono indici composti in cui uno dei campi è la chiave di partizione.

I comandi seguenti creano una raccolta partizionata ```coll``` (la chiave di partizione è ```university```) con un indice univoco sui campi student_id e university:

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

Nell'esempio precedente, se la clausola ```"university":1``` venisse omessa verrebbe restituito un errore con il messaggio seguente:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>Indici TTL

Per abilitare la scadenza dei documenti in una determinata raccolta, è necessario creare un ["indice TTL" (time-to-live)](../cosmos-db/time-to-live.md). Un indice TTL è un indice sul campo _ts con un valore "expireAfterSeconds".

Esempio:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Il comando precedente causerà l'eliminazione di tutti i documenti della raccolta ```db.coll``` che non sono stati modificati negli ultimi 10 secondi.

> [!NOTE]
> **_ts** è un campo specifico di Azure Cosmos DB e non è accessibile dai client MongoDB. Si tratta di una proprietà di sistema riservata che contiene il timestamp dell'ultima modifica del documento.

## <a name="track-the-index-progress"></a>Tenere traccia dell'avanzamento dell'indice

La versione 3.6 dell'API di Azure Cosmos DB `currentOp()` per gli account MongoDB supporta il comando per tenere traccia dello stato di avanzamento dell'indice in un'istanza di database. Questo comando restituisce un documento che contiene informazioni sulle operazioni in corso in un'istanza di database. Il `currentOp` comando viene usato per tenere traccia di tutte le operazioni in corso in MongoDB nativo, mentre nell'API di Azure Cosmos DB per MongoDB questo comando supporta solo il rilevamento dell'operazione sull'indice.

Ecco alcuni esempi che illustrano `currentOp` come usare il comando per tenere traccia dello stato di avanzamento dell'indice:Here are some examples that show how to use the command to track the index progress:

* Ottenere lo stato di avanzamento dell'indice per una raccolta:Get the index progress for a collection:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Ottenere lo stato di avanzamento dell'indice per tutte le raccolte in un database:Get the index progress for all the collections in a database:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Ottenere lo stato di avanzamento dell'indice per tutti i database e le raccolte in un account Cosmos di Azure:Get the index progress for all the databases and collections in an Azure Cosmos account:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

I dettagli dello stato di avanzamento dell'indice contengono percentuale di avanzamento per l'operazione di indice corrente. L'esempio seguente mostra il formato del documento di output per le diverse fasi di avanzamento dell'indice:The following example shows the output document format for different stages of index progress:

1. Se l'operazione sull'indice su una raccolta 'foo' e un database 'bar' con completato l'indicizzazione del 60 %, avrà il seguente documento di output. `Inprog[0].progress.total`mostra 100 come completamento del target.

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

2. Per un'operazione sull'indice appena avviata in una raccolta 'foo' e in un database "bar", il documento di output potrebbe mostrare lo 0% di avanzamento fino a raggiungere un livello misurabile.

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

3. Al termine dell'operazione di indicizzazione in corso, nel documento di output vengono visualizzate operazioni inprog vuote.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Aggiornamenti dell'indice in background

Indipendentemente dal valore specificato per la proprietà dell'indice **Background,** gli aggiornamenti dell'indice vengono sempre eseguiti in background. Gli aggiornamenti dell'indice utilizzano RU con una priorità inferiore rispetto ad altre operazioni di database. Pertanto, le modifiche all'indice non comporteranno tempi di inattività per scritture, aggiornamenti o eliminazioni.

Quando si aggiunge un nuovo indice, le query lo utilizzeranno immediatamente. Ciò significa che le query potrebbero non restituire tutti i risultati corrispondenti e lo faranno senza restituire errori. Una volta completata la trasformazione dell'indice, i risultati delle query saranno coerenti. È possibile tenere traccia dello stato di [avanzamento dell'indice.](#track-the-index-progress)

## <a name="migrating-collections-with-indexes"></a>Migrazione di raccolte con indici

Attualmente, gli indici univoci possono essere creati solo quando la raccolta non contiene alcun documento. Gli strumenti di migrazione più diffusi di MongoDB provano a creare gli indici univoci dopo l'importazione dei dati. Per aggirare questo problema, si consiglia agli utenti di creare manualmente le raccolte corrispondenti ```mongorestore``` e gli indici univoci, anziché consentire lo strumento di migrazione (per questo comportamento si ottiene utilizzando il `--noIndexRestore` flag nella riga di comando).

## <a name="indexing-for-version-32"></a>Indicizzazione per la versione 3.2

Per gli account di Azure Cosmos DB compatibili con la versione 3.2 del protocollo wire MongoDB, le funzionalità di indicizzazione disponibili e i valori predefiniti sono diversi. Puoi [controllare la versione del tuo account.](mongodb-feature-support-36.md#protocol-support) È possibile eseguire l'aggiornamento alla versione 3.6 inviando una richiesta di [supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Se si utilizza la versione 3.2, in questa sezione vengono descritte le differenze principali con la versione 3.6.

### <a name="dropping-the-default-indexes-32"></a>Eliminazione degli indici predefiniti (3.2)

A differenza della versione 3.6 dell'API di Azure Cosmos DB per MongoDB, la versione 3.2 indicizza ogni proprietà per impostazione predefinita. Il comando seguente può essere utilizzato per eliminare ```coll```questi indici predefiniti per una raccolta:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Dopo aver rilasciato gli indici predefiniti, è possibile aggiungere ulteriori indici come fatto nella versione 3.6.After dropping the default indexes, you can add on additional indexes as done in Version 3.6.

### <a name="compound-indexes-32"></a>Indici composti (3.2)

Gli indici composti contengono riferimenti a più campi di un documento. Se si desidera creare un indice composto, si prega di eseguire l'aggiornamento alla versione 3.6 presentando una richiesta di [supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passaggi successivi

* [Indicizzazione in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Impostare la scadenza automatica dei dati in Azure Cosmos DB con la durata (TTL)](../cosmos-db/time-to-live.md)
