---
title: "Supporto delle funzionalità fornito da Azure Cosmos DB per MongoDB | Microsoft Docs"
description: "Informazioni sul supporto delle funzionalità fornito dall'API MongoDB di Azure Cosmos DB per MongoDB 3.4."
services: cosmos-db
author: alekseys
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 007b530cd7a14f063ae4f86d18daa9742c6655c2
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>Supporto dell'API MongoDB per le funzionalità e la sintassi MongoDB

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile comunicare con l'API MongoDB del database tramite uno dei [driver](https://docs.mongodb.org/ecosystem/drivers)open source del client MongoDB. L'API MongoDB consente di usare driver client esistenti aderendo al [protocollo di trasmissione](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) MongoDB.

Usando l'API MongoDB di Azure Cosmos DB è possibile sfruttare i noti vantaggi delle API MongoDB con tutte le funzionalità aziendali di Azure Cosmos DB: [distribuzione globale](distribute-data-globally.md), [partizionamento orizzontale automatico](partition-data.md), garanzie di disponibilità e latenza, indicizzazione automatica di ogni campo, crittografia di dati inattivi, backup e molto altro.

## <a name="mongodb-query-language-support"></a>Supporto del linguaggio di query MongoDB

L'API MongoDB di Azure Cosmos DB offre il supporto completo dei costrutti del linguaggio di query MongoDB. Di seguito è possibile trovare l'elenco dettagliato di operazioni, operatori, fasi, comandi e opzioni attualmente supportati.


## <a name="database-commands"></a>Comandi del database

Azure Cosmos DB supporta i comandi di database seguenti per tutti gli account API di MongoDB. 

### <a name="query-and-write-operation-commands"></a>Comandi per le operazioni di query e scrittura
- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Comandi di autenticazione
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Comandi di amministrazione
- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Comandi di diagnostica
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Pipeline di aggregazione</a>

Azure Cosmos DB supporta la pipeline di aggregazione nella versione di anteprima pubblica. Per istruzioni sull'onboarding nell'anteprima pubblica, vedere il [blog di Azure](https://aka.ms/mongodb-aggregation).

### <a name="aggregation-commands"></a>Comandi di aggregazione
- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Fasi di aggregazione
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count

### <a name="aggregation-expressions"></a>Espressioni di aggregazione

#### <a name="boolean-expressions"></a>Espressioni booleane
- $and
- $or
- $not

#### <a name="set-expressions"></a>Espressioni Set
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Espressioni di confronto
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Espressioni aritmetiche
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Espressioni stringa
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Espressioni di matrice
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Espressioni di data
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Espressioni condizionali
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Accumulatori di aggregazione
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operatori

Sono supportati gli operatori seguenti con esempi di uso. Vedere questo documento di esempio usato nelle query seguenti:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

operatore | Esempio |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Note

Nelle query $regex, le espressioni ancorate a sinistra consentono la ricerca nell'indice. L'uso del modificatore 'i' (senza distinzione tra maiuscole e minuscole) e 'm' (su più righe) provoca l'analisi della raccolta in tutte le espressioni.
Quando è necessario includere '$' o '|' è consigliabile creare due o più query regex. La query originale ```find({x:{$regex: /^abc$/})``` deve essere ad esempio modificata in ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
La prima parte userà l'indice per limitare la ricerca ai documenti che iniziano con ^abc, mentre la seconda parte individuerà le voci esatte. L'operatore barra '|' funge da funzione "or". La query ```find({x:{$regex: /^abc|^def/})``` individua i documenti in cui il campo 'x' ha un valore che inizia con "abc" o "def". Per usare l'indice è consigliabile per suddividere la query in due query diverse unite dall'operatore $or: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.


### <a name="geospatial-operators"></a>Operatori geospaziali

operatore | Esempio 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sì
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sì
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sì
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sì
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sì
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Sì
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Sì
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Sì
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Sì
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Sì
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Sì

## <a name="additional-operators"></a>Altri operatori

operatore | Esempio | Note 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Non supportati. In alternativa, usare $regex 

### <a name="methods"></a>Metodi

Sono supportati i metodi seguenti:

#### <a name="cursor-methods"></a>Metodi di cursore

Metodo | Esempio | Note 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | I documenti senza chiave di ordinamento non vengono restituiti

## <a name="unique-indexes"></a>Indici univoci

Azure Cosmos DB indicizza tutti i campi dei documenti scritti nel database per impostazione predefinita. Gli indici univoci assicurano che un campo specifico non abbia valori duplicati in tutti i documenti di una raccolta, in modo simile alla preservazione dell'univocità per la chiave "_id" predefinita. Ora è possibile creare indici personalizzati in Azure Cosmos DB usando il comando createIndex, includendo il vincolo 'unique'.

Gli indici univoci sono disponibili per tutti gli account di API MongoDB.

## <a name="time-to-live-ttl"></a>Durata (TTL)

Azure Cosmos DB supporta una durata (TTL) relativa in base al timestamp del documento. La durata (TTL) può essere abilitata per le raccolte di API MongoDB tramite il [portale di Azure](https://portal.azure.com).

## <a name="user-and-role-management"></a>Gestione di utenti e ruoli

Azure Cosmos DB non supporta ancora utenti e ruoli. Azure Cosmos DB supporta il controllo degli accessi in base al ruolo e chiavi/password di lettura/scrittura e sola lettura ottenibili tramite il [portale di Azure](https://portal.azure.com) nella pagina Stringa di connessione.

## <a name="replication"></a>Replica

Azure Cosmos DB supporta la replica automatica e nativa ai livelli più bassi. Questa logica viene estesa per ottenere anche una replica globale a bassa latenza. Azure Cosmos DB non supporta comandi di replica manuali.

## <a name="sharding"></a>Partizionamento orizzontale

Azure Cosmos DB supporta il partizionamento orizzontale automatico lato server. Azure Cosmos DB non supporta comandi di partizionamento orizzontale manuali.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [usare Studio 3T](mongodb-mongochef.md) con un'API per il database MongoDB.
- Informazioni su come [usare Robo 3T](mongodb-robomongo.md) con un'API per il database MongoDB.
- Esplorare Azure Cosmos DB con il supporto del protocollo per trovare [esempi](mongodb-samples.md) di MongoDB.
