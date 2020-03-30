---
title: Comandi di estensione MongoDB per gestire i dati nell'API di Azure Cosmos DB per MongoDB
description: Questo articolo descrive come usare i comandi di estensione MongoDB per gestire i dati archiviati nell'API di Azure Cosmos DB per MongoDB.This article describes how to use MongoDB extension commands to manage data stored in Azure Cosmos DB's API for MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445211"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Usare i comandi di estensione MongoDB per gestire i dati archiviati nell'API di Azure Cosmos DB per MongoDBUse MongoDB extension commands to manage data stored in Azure Cosmos DB's API for MongoDB 

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile comunicare con l'API di Azure Cosmos DB per MongoDB utilizzando uno dei [driver client MongoDB](https://docs.mongodb.org/ecosystem/drivers)open source. L'API di Azure Cosmos DB per MongoDB consente l'utilizzo di driver client esistenti adere al [protocollo wire MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Usando l'API di Azure Cosmos DB per MongoDB, è possibile usufruire dei vantaggi offerti da Cosmos DB, ad esempio distribuzione globale, partizionamento automatico, disponibilità elevata, garanzie di latenza, automatico, crittografia a riposo, backup e molti altri, preservando gli investimenti nell'app MongoDB.

## <a name="mongodb-protocol-support"></a>Supporto del protocollo MongoDB

Per impostazione predefinita, l'API di Azure Cosmos DB per MongoDB è compatibile con la versione 3.2 del server MongoDB, per ulteriori dettagli, vedere [Funzionalità e sintassi supportate.](mongodb-feature-support.md) Le funzionalità o gli operatori di query aggiunti in MongoDB versione 3.4 sono attualmente disponibili come anteprima nell'API di Azure Cosmos DB per MongoDB. The following extension commands support Azure Cosmos DB specific functionality when performing CRUD operations on the data stored in Azure Cosmos DB's API for MongoDB:

* [Crea database](#create-database)
* [Aggiorna database](#update-database)
* [Ottenere il databaseGet database](#get-database)
* [Crea raccolta](#create-collection)
* [Aggiorna raccolta](#update-collection)
* [Ottieni raccolta](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Crea database

Il comando create database extension crea un nuovo database MongoDB. Il nome del database viene utilizzato dal contesto dei database su cui viene eseguito il comando. Il formato del comando CreateDatabase è il seguente:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
| customAction   |  string  |   Nome del comando personalizzato, deve essere "CreateDatabase".      |
| offertaVelocità effettiva | INT  | Velocità effettiva di cui è stato eseguito il provisioning nel database. Questo parametro è facoltativo e, |

### <a name="output"></a>Output

Restituisce una risposta di comando personalizzata predefinita. Vedere [l'output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Creare un database**

Per creare un database denominato "test", utilizzare il comando seguente:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Creare un database con velocità effettivaCreate a database with throughput**

Per creare un database denominato "test" e la velocità effettiva di cui è stato eseguito il provisioning di 1000 RU, utilizzare il comando seguente:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Aggiorna database

Il comando Update database extension aggiorna le proprietà associate al database specificato. Attualmente, è possibile aggiornare solo la proprietà "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
| customAction    |    string     |   Nome del comando personalizzato. Deve essere "UpdateDatabase".      |
|  offertaVelocità effettiva   |  INT       |     Nuova velocità effettiva di cui è stato eseguito il provisioning che si desidera impostare nel database.    |

### <a name="output"></a>Output

Restituisce una risposta di comando personalizzata predefinita. Vedere [l'output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Aggiornare la velocità effettiva di cui è stato eseguito il provisioning associato a un databaseUpdate the provisioned throughput associated with a database**

Per aggiornare la velocità effettiva di cui è stato eseguito il provisioning di un database con nome "test" a 1200 RU, utilizzare il comando seguente:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Ottenere il databaseGet database

Il comando get database extension restituisce l'oggetto di database. Il nome del database viene utilizzato dal contesto del database su cui viene eseguito il comando.

```
{
  customAction: "GetDatabase"
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:


|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  customAction   |   string      |   Nome del comando personalizzato. Deve essere "GetDatabase"|
        
### <a name="output"></a>Output

Se il comando ha esito positivo, la risposta contiene un documento con i seguenti campi:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stato della risposta. 1 e più successo. 0 : errore.      |
| `database`    |    `string`        |   Nome del database.      |
|   `provisionedThroughput`  |    `int`      |    Velocità effettiva di provisioning impostata nel database. Si tratta di un parametro di risposta facoltativo.     |

Se il comando ha esito negativo, viene restituita una risposta al comando personalizzato predefinito. Vedere [l'output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Ottenere il databaseGet the database**

Per ottenere l'oggetto di database per un database denominato "test", utilizzare il comando seguente:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Crea raccolta

Il comando create collection extension crea una nuova raccolta MongoDB. Il nome del database viene utilizzato dal contesto dei database su cui viene eseguito il comando. Il formato del comando CreateCollection è il seguente:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
| customAction    | string | Nome del comando personalizzato. Deve essere "CreateCollection"     |
| collection      | string | Nome della raccolta.                                   |
| offertaVelocità effettiva | INT    | Velocità effettiva di provisioning per impostare il database. È un parametro facoltativo |
| shardKey (chiave di partizione)        | string | Percorso chiave partizioni per creare una raccolta partizionata. È un parametro facoltativo |

### <a name="output"></a>Output

Restituisce una risposta di comando personalizzata predefinita. Vedere [l'output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Creare una raccolta senza problemiCreate a unsharded collection**

Per creare una raccolta senza disco con nome "testCollection" e la velocità effettiva di cui è stato eseguito il provisioning di 1000 RU, utilizzare il comando seguente: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Creare una raccolta partizionataCreate a sharded collection**

Per creare una raccolta partizionata con nome "testCollection" e velocità effettiva di cui è stato eseguito il provisioning di 1000 RU, utilizzare il comando seguente:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Aggiorna raccolta

Il comando di estensione della raccolta di aggiornamento aggiorna le proprietà associate alla raccolta specificata.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  customAction   |   string      |   Nome del comando personalizzato. Deve essere "UpdateCollection".      |
|  collection   |   string      |   Nome della raccolta.       |
| offertaVelocità effettiva   |INT|   Velocità effettiva di provisioning per impostare nella raccolta.|

## <a name="output"></a>Output

Restituisce una risposta di comando personalizzata predefinita. Vedere [l'output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Aggiornare la velocità effettiva di cui è stato eseguito il provisioning associato a una raccoltaUpdate the provisioned throughput associated with a collection**

Per aggiornare la velocità effettiva di cui è stato eseguito il provisioning di una raccolta con nome "testCollection" a 1200 RU, utilizzare il comando seguente:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Ottieni raccolta

Il comando personalizzato get collection restituisce l'oggetto raccolta.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:


|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
| customAction    |   string      |   Nome del comando personalizzato. Deve essere "GetCollection".      |
| collection    |    string     |    Nome della raccolta.     |

### <a name="output"></a>Output

Se il comando ha esito positivo, la risposta contiene un documento con i seguenti campi


|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stato della risposta. 1 e più successo. 0 : errore.      |
| `database`    |    `string`     |   Nome del database.      |
| `collection`    |    `string`     |    Nome della raccolta.     |
|  `shardKeyDefinition`   |   `document`      |  Documento di specifica dell'indice utilizzato come chiave di partizione. Si tratta di un parametro di risposta facoltativo.       |
|  `provisionedThroughput`   |   `int`      |    Velocità effettiva di provisioning per impostare nella raccolta. Si tratta di un parametro di risposta facoltativo.     |

Se il comando ha esito negativo, viene restituita una risposta al comando personalizzato predefinito. Vedere [l'output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Ottieni la collezione**

Per ottenere l'oggetto raccolta per una raccolta denominata "testCollection", utilizzare il comando seguente:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Output predefinito di un comando personalizzato

Se non specificato, una risposta personalizzata contiene un documento con i seguenti campi:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stato della risposta. 1 e più successo. 0 : errore.      |
| `code`    |   `int`      |   Restituito solo quando il comando non è riuscito (ad es. ok ) . Contiene il codice di errore MongoDB. Si tratta di un parametro di risposta facoltativo.      |
|  `errMsg`   |  `string`      |    Restituito solo quando il comando non è riuscito (ad es. ok ) . Contiene un messaggio di errore descrittivo. Si tratta di un parametro di risposta facoltativo.      |

## <a name="next-steps"></a>Passaggi successivi

Successivamente è possibile procedere per apprendere i seguenti concetti di Azure Cosmos DB: 

* [Indicizzazione in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Impostare la scadenza automatica dei dati in Azure Cosmos DB con la durata (TTL)](../cosmos-db/time-to-live.md)
