---
title: Gestire l'indicizzazione nell'API Azure Cosmos DB per MongoDB
description: Questo articolo presenta una panoramica delle funzionalità di indicizzazione Azure Cosmos DB usando l'API MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 06/16/2020
author: timsander1
ms.author: tisande
ms.custom: devx-track-javascript
ms.openlocfilehash: 473bc8677c5369833928eb4648f32bb146e83e65
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420652"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Gestire l'indicizzazione nell'API Azure Cosmos DB per MongoDB

L'API di Azure Cosmos DB per MongoDB sfrutta le funzionalità di gestione degli indici principali di Azure Cosmos DB. Questo articolo è incentrato su come aggiungere indici usando l'API di Azure Cosmos DB per MongoDB. È anche possibile leggere una [Panoramica dell'indicizzazione in Azure Cosmos DB](index-overview.md) pertinente in tutte le API.

## <a name="indexing-for-mongodb-server-version-36"></a>Indicizzazione per il server MongoDB versione 3,6

Azure Cosmos DB API per MongoDB Server versione 3,6 indicizza automaticamente il `_id` campo, che non può essere eliminato. Viene automaticamente applicata l'univocità del `_id` campo per ogni chiave di partizione. Nell'API di Azure Cosmos DB per MongoDB, il partizionamento orizzontale e l'indicizzazione sono concetti distinti. Non è necessario indicizzare la chiave di partizione. Tuttavia, come per qualsiasi altra proprietà del documento, se questa proprietà è un filtro comune nelle query, si consiglia di indicizzare la chiave di partizione.

Per indicizzare campi aggiuntivi, applicare i comandi di gestione dell'indice MongoDB. Come in MongoDB, l'API di Azure Cosmos DB per MongoDB indicizza automaticamente `_id` solo il campo. Questo criterio di indicizzazione predefinito differisce dall'API di Azure Cosmos DB SQL, che indicizza tutti i campi per impostazione predefinita.

Per applicare un ordinamento a una query, è necessario creare un indice sui campi utilizzati nell'operazione di ordinamento.

## <a name="index-types"></a>Tipi di indice

### <a name="single-field"></a>Campo singolo

È possibile creare indici in qualsiasi singolo campo. L'ordinamento dell'indice del campo singolo non è rilevante. Il comando che segue crea un indice nel campo `name` :

`db.coll.createIndex({name:1})`

Una query usa più indici dei campi singoli, se disponibili. È possibile creare fino a 500 indici di campi singoli per ogni contenitore.

### <a name="compound-indexes-mongodb-server-version-36"></a>Indici composti (server MongoDB versione 3,6)

L'API di Azure Cosmos DB per MongoDB supporta gli indici composti per gli account che usano il protocollo wire versione 3,6. È possibile includere fino a otto campi in un indice composto. Diversamente da MongoDB, è necessario creare un indice composto solo se la query deve essere ordinata in modo efficiente su più campi in una sola volta. Per le query con più filtri che non devono essere ordinati, creare più indici di campi singoli anziché un singolo indice composto.

Il comando che segue crea un indice composto sui campi `name` e `age` :

`db.coll.createIndex({name:1,age:1})`

È possibile usare indici composti per eseguire l'ordinamento in modo efficiente su più campi contemporaneamente, come illustrato nell'esempio seguente:

`db.coll.find().sort({name:1,age:1})`

È anche possibile usare l'indice composto precedente per ordinare in modo efficiente una query con il tipo di ordinamento opposto su tutti i campi. Ecco un esempio:

`db.coll.find().sort({name:-1,age:-1})`

Tuttavia, la sequenza dei percorsi nell'indice composto deve corrispondere esattamente alla query. Di seguito è riportato un esempio di query che richiederebbe un indice composto aggiuntivo:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Indici join

Azure Cosmos DB crea indici join per indicizzare il contenuto archiviato nelle matrici. Se si indicizza un campo con un valore di matrice, Azure Cosmos DB indicizza automaticamente ogni elemento nella matrice.

### <a name="geospatial-indexes"></a>Indici geospaziali

Molti operatori geospaziali trarranno vantaggio dagli indici geospaziali. Attualmente, l'API di Azure Cosmos DB per MongoDB supporta gli `2dsphere` indici. L'API non supporta ancora gli `2d` indici.

Di seguito è riportato un esempio di creazione di un indice geospaziale nel `location` campo:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Indici di testo

L'API di Azure Cosmos DB per MongoDB attualmente non supporta gli indici di testo. Per le query di ricerca del testo sulle stringhe, è necessario usare l'integrazione di [Azure ricerca cognitiva](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) con Azure Cosmos DB.

## <a name="wildcard-indexes"></a>Indici con caratteri jolly

È possibile usare gli indici con caratteri jolly per supportare le query su campi sconosciuti. Si supponga di avere una raccolta che contiene dati sulle famiglie.

Di seguito è riportato un esempio di documento nella raccolta:

```json
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "grade": "5"
     }
  ]
```

Ecco un altro esempio, questa volta con un set di proprietà leggermente diverso in `children` :

```json
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
      },
      {
        "familyName": "Merriam",
        "givenName": "John",
      }
  ]
```

In questa raccolta i documenti possono avere diverse proprietà possibili. Se si desidera indicizzare tutti i dati nella `children` matrice, sono disponibili due opzioni: creare indici distinti per ogni singola proprietà o creare un indice con caratteri jolly per l'intera `children` matrice.

### <a name="create-a-wildcard-index"></a>Creazione di un indice con caratteri jolly

Il comando che segue crea un indice con caratteri jolly in qualsiasi proprietà all'interno di `children` :

`db.coll.createIndex({"children.$**" : 1})`

**Diversamente da MongoDB, gli indici con caratteri jolly possono supportare più campi nei predicati di query**. Non vi sarà alcuna differenza nelle prestazioni delle query se si usa un singolo indice con caratteri jolly anziché creare un indice separato per ogni proprietà.

È possibile creare i tipi di indice seguenti utilizzando la sintassi con caratteri jolly:

- Campo singolo
- GeoSpatial

### <a name="indexing-all-properties"></a>Indicizzazione di tutte le proprietà

Ecco come è possibile creare un indice con caratteri jolly in tutti i campi:

`db.coll.createIndex( { "$**" : 1 } )`

Quando si avvia lo sviluppo, può essere utile creare un indice con caratteri jolly in tutti i campi. Con l'indicizzazione di più proprietà in un documento, l'addebito delle unità richiesta (UR) per la scrittura e l'aggiornamento del documento aumenterà. Se pertanto si dispone di un carico di lavoro con un elevato numero di operazioni di scrittura, è consigliabile scegliere i percorsi di indicizzazione singolarmente anziché utilizzare gli indici con caratteri jolly

### <a name="limitations"></a>Limitazioni

Gli indici con caratteri jolly non supportano i tipi di indice o le proprietà seguenti:

- Composto
- TTL
- Univoco

**Diversamente da MongoDB**, nell'API Azure Cosmos DB per MongoDB **non** è possibile usare gli indici con caratteri jolly per:

- Creazione di un indice con caratteri jolly che include più campi specifici

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection " :
        {
           "children.givenName" : 1,
           "children.grade" : 1
        }
    }
)`

- Creazione di un indice con caratteri jolly che escluda più campi specifici

`db.coll.createIndex(
    { "$**" : 1 },
    { "wildcardProjection" :
        {
           "children.givenName" : 0,
           "children.grade" : 0
        }
    }
)`

In alternativa, è possibile creare più indici jolly.

## <a name="index-properties"></a>Proprietà degli indici

Le operazioni seguenti sono comuni per gli account che hanno il protocollo wire versione 3,6 e gli account che servono versioni precedenti. Sono disponibili altre informazioni sugli [indici supportati e sulle proprietà indicizzate](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Indici univoci

Gli [indici univoci](unique-keys.md) sono utili per applicare che due o più documenti non contengono lo stesso valore per i campi indicizzati.

> [!IMPORTANT]
> Gli indici univoci possono essere creati solo quando la raccolta è vuota (non contiene documenti).

Il comando che segue crea un indice univoco nel campo `student_id` :

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

Per le raccolte partizionate, è necessario specificare la chiave di partizione (partizione) per creare un indice univoco. In altre parole, tutti gli indici univoci in una raccolta partizionata sono indici composti in cui uno dei campi è la chiave di partizione.

I comandi seguenti creano una raccolta partizionata ```coll``` (la chiave di partizione ```university``` ) con un indice univoco nei campi `student_id` e `university` :

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

Nell'esempio precedente, l'omissione della ```"university":1``` clausola restituisce un errore con il messaggio seguente:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>Indici TTL

Per abilitare la scadenza del documento in una particolare raccolta, è necessario creare un [Indice TTL (time-to-Live)](../cosmos-db/time-to-live.md). Un indice TTL è un indice nel `_ts` campo con un `expireAfterSeconds` valore.

Esempio:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Il comando precedente elimina tutti i documenti nella ```db.coll``` raccolta che non sono stati modificati negli ultimi 10 secondi.

> [!NOTE]
> Il campo **_ts** è specifico per Azure Cosmos DB e non è accessibile dai client MongoDB. Si tratta di una proprietà riservata (di sistema) che contiene il timestamp dell'Ultima modifica del documento.

## <a name="track-index-progress"></a>Rileva stato dell'indice

La versione 3,6 dell'API Azure Cosmos DB per MongoDB supporta il `currentOp()` comando per tenere traccia dello stato di avanzamento dell'indice in un'istanza del database. Questo comando restituisce un documento che contiene informazioni sulle operazioni in corso su un'istanza del database. Usare il `currentOp` comando per tenere traccia di tutte le operazioni in corso in MongoDB nativo. Nell'API di Azure Cosmos DB per MongoDB, questo comando supporta solo il rilevamento dell'operazione sull'indice.

Di seguito sono riportati alcuni esempi che illustrano come usare il `currentOp` comando per tenere traccia dello stato di avanzamento dell'indice:

* Ottenere lo stato di avanzamento dell'indice per una raccolta:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Ottenere lo stato di avanzamento dell'indice per tutte le raccolte in un database:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Ottenere lo stato di avanzamento dell'indice per tutti i database e le raccolte in un account Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Esempi di output dello stato di avanzamento dell'indice

I dettagli sullo stato dell'indice mostrano la percentuale di avanzamento per l'operazione sull'indice corrente. Di seguito è riportato un esempio che mostra il formato del documento di output per diverse fasi di avanzamento dell'indice:

- Un'operazione sugli indici in una raccolta "foo" e un database "barra" con il 60% di completamento avranno il seguente documento di output. Il `Inprog[0].progress.total` campo indica 100 come percentuale di completamento di destinazione.

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

- Se un'operazione sull'indice è stata appena avviata in una raccolta "foo" e in un database a barre, il documento di output potrebbe visualizzare lo stato di avanzamento dello 0% fino a raggiungere un livello misurabile.

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

- Al termine dell'operazione sull'indice in corso, il documento di output Mostra `inprog` le operazioni vuote.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Aggiornamenti degli indici in background

Indipendentemente dal valore specificato per la proprietà indice in **background** , gli aggiornamenti dell'indice vengono sempre eseguiti in background. Poiché gli aggiornamenti dell'indice utilizzano le unità richiesta (UR) con una priorità più bassa rispetto ad altre operazioni di database, le modifiche all'indice non comporteranno tempi di inattività per scritture, aggiornamenti o eliminazioni.

Quando si aggiunge un nuovo indice, le query utilizzeranno immediatamente l'indice. Ciò significa che è possibile che le query non restituiscano tutti i risultati corrispondenti senza restituire alcun errore. Quando la trasformazione dell'indice viene completata, i risultati della query saranno coerenti. È possibile [tenere traccia dello stato dell'indice](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Eseguire la migrazione di raccolte con indici

Attualmente, è possibile creare indici univoci solo quando la raccolta non contiene documenti. Gli strumenti di migrazione MongoDB più diffusi tentano di creare gli indici univoci dopo l'importazione dei dati. Per aggirare questo problema, è possibile creare manualmente le raccolte e gli indici univoci corrispondenti anziché consentire allo strumento di migrazione di provare. È possibile ottenere questo comportamento per ```mongorestore``` usando il `--noIndexRestore` flag nella riga di comando.

## <a name="indexing-for-mongodb-version-32"></a>Indicizzazione per MongoDB versione 3,2

Le funzionalità di indicizzazione e le impostazioni predefinite disponibili sono diverse per gli account Azure Cosmos compatibili con la versione 3,2 del protocollo wire di MongoDB. È possibile [controllare la versione dell'account](mongodb-feature-support-36.md#protocol-support). È possibile eseguire l'aggiornamento alla versione 3,6 inviando una [richiesta di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Se si usa la versione 3,2, in questa sezione vengono descritte le differenze principali con la versione 3,6.

### <a name="dropping-default-indexes-version-32"></a>Eliminazione di indici predefiniti (versione 3,2)

A differenza della versione 3,6 dell'API Azure Cosmos DB per MongoDB, la versione 3,2 indicizza tutte le proprietà per impostazione predefinita. È possibile utilizzare il comando seguente per eliminare questi indici predefiniti per una raccolta ( ```coll``` ):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Dopo aver eliminato gli indici predefiniti, è possibile aggiungere più indici come nella versione 3,6.

### <a name="compound-indexes-version-32"></a>Indici composti (versione 3,2)

Gli indici composti contengono riferimenti a più campi di un documento. Se si vuole creare un indice composto, eseguire l'aggiornamento alla versione 3,6 inviando una [richiesta di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="wildcard-indexes-version-32"></a>Indici con caratteri jolly (versione 3,2)

Se si desidera creare un indice con caratteri jolly, eseguire l'aggiornamento alla versione 3,6 inviando una [richiesta di supporto](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passaggi successivi

* [Indicizzazione in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Impostare la scadenza automatica dei dati in Azure Cosmos DB con la durata (TTL)](../cosmos-db/time-to-live.md)
* Per informazioni sulla relazione tra partizionamento e indicizzazione, vedere l'articolo su come [eseguire query in un contenitore di Azure Cosmos](how-to-query-container.md) .
