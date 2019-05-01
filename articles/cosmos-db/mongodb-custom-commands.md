---
title: Comandi estensione MongoDB per gestire i dati archiviati nell'API di Azure Cosmos DB per MongoDB
description: Questo articolo descrive come usare i comandi di estensione di MongoDB per gestire i dati archiviati nell'API di Azure Cosmos DB per MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: 94b1048befc8716caf5f7f51adb1f95d047d4077
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925648"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Usare i comandi di estensione di MongoDB per gestire i dati archiviati nell'API di Azure Cosmos DB per MongoDB 

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile comunicare con l'API di Azure Cosmos DB per MongoDB usando uno qualsiasi degli open source [driver del client MongoDB](https://docs.mongodb.org/ecosystem/drivers). API di Azure Cosmos DB per MongoDB consente l'utilizzo del driver client esistenti aderendo al [protocollo di trasmissione MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Usando l'API di Azure Cosmos DB per MongoDB, è possibile sfruttare i vantaggi Cosmos DB, ad esempio distribuzione globale, partizionamento orizzontale automatico, la disponibilità elevata, garanzie di latenza, automatic, crittografia dei dati inattivi, backup, e molti più, mentre preservare gli investimenti Nell'app MongoDB.

## <a name="mongodb-protocol-support"></a>Supporto del protocollo per MongoDB

Per impostazione predefinita, API dell'Azure Cosmos DB per MongoDB è compatibile con MongoDB server versione 3.2 e per altri dettagli, vedere [supportate funzionalità e sintassi](mongodb-feature-support.md). Le funzionalità o gli operatori di query aggiunti nella versione 3.4 di MongoDB sono attualmente disponibili in anteprima nell'API di Azure Cosmos DB per MongoDB. I seguenti comandi di estensione supportano funzionalità specifiche di Azure Cosmos DB quando si eseguono operazioni CRUD sui dati archiviati nell'API di Azure Cosmos DB per MongoDB:

* [Creare database](#create-database)
* [Aggiorna database](#update-database)
* [Ottenere il database](#get-database)
* [Crea raccolta](#create-collection)
* [Aggiornare la raccolta](#update-collection)
* [Recuperare la raccolta](#get-collection)

## <a id="create-database"></a> Creare database

Comando create database estensione crea un nuovo database di MongoDB. Il nome del database viene utilizzato dal contesto del database sulla quale viene eseguito il comando. Il formato del comando CreateDatabase è come segue:

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
| offerThroughput | int  | Velocità effettiva con provisioning impostato sul database. Questo parametro è facoltativo e, |

### <a name="output"></a>Output

Restituisce una risposta al comando personalizzato predefinito. Vedere le [predefinito output](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Creare un database**

Per creare un database denominato "test", usare il comando seguente:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Creare un database con una velocità effettiva**

Per creare un database denominato "test" e velocità effettiva con provisioning di 1000 UR, usare il comando seguente:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> Aggiorna database

Il comando di estensione aggiornamento database Aggiorna le proprietà associate al database specificato. Attualmente, è possibile aggiornare solo la proprietà "offerThroughput".

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
|  offerThroughput   |  int       |     Nuova velocità effettiva con provisioning che si desidera impostare nel database.    |

### <a name="output"></a>Output

Restituisce una risposta al comando personalizzato predefinito. Vedere le [predefinito output](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Aggiornare la velocità effettiva con provisioning associata al database**

Per aggiornare la velocità effettiva con provisioning di un database con nome "test" a UR 1200, usare il comando seguente:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> Ottenere il database

Il comando di estensione database get restituisce l'oggetto di database. Il nome del database viene utilizzato dal contesto del database sulla quale viene eseguito il comando.

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

Se il comando ha esito positivo, la risposta contiene un documento con i campi seguenti:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stato della risposta. 1 = = esito positivo. 0 = = errore.      |
| `database`    |    `string`        |   Nome del database.      |
|   `provisionedThroughput`  |    `int`      |    Velocità effettiva con provisioning che viene impostata sul database. Questo è un parametro di risposta opzionale.     |

Se il comando non riesce, viene restituita una risposta di un comando personalizzato predefinito. Vedere le [predefinito output](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Ottenere il database**

Per ottenere l'oggetto di database per un database denominato "test", usare il comando seguente:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> Crea raccolta

Il comando di estensione raccolta create Crea una nuova raccolta MongoDB. Il nome del database viene utilizzato dal contesto del database sulla quale viene eseguito il comando. Il formato del comando CreateCollection è come segue:

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
| collection      | string | Nome della raccolta                                   |
| offerThroughput | int    | Velocità effettiva con provisioning per impostare il database. È un parametro facoltativo |
| shardKey        | string | Percorso di chiave di partizione per creare una raccolta partizionata. È un parametro facoltativo |

### <a name="output"></a>Output

Restituisce una risposta al comando personalizzato predefinito. Vedere le [predefinito output](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Creare una raccolta unsharded**

Per creare una raccolta unsharded con nome "testCollection" e velocità effettiva con provisioning di 1000 UR, usare il comando seguente: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Creare una raccolta partizionata**

Per creare una raccolta partizionata con nome "testCollection" e velocità effettiva con provisioning di 1000 UR, usare il comando seguente:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> Aggiornare la raccolta

Il comando di estensione raccolta update Aggiorna le proprietà associate alla raccolta specificata.

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
| offerThroughput   |int|   Velocità effettiva con provisioning per impostare la raccolta.|

## <a name="output"></a>Output

Restituisce una risposta al comando personalizzato predefinito. Vedere le [predefinito output](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Aggiornare la velocità effettiva con provisioning associata a una raccolta**

Per aggiornare la velocità effettiva con provisioning di una raccolta con nome "testCollection" UR 1200, usare il comando seguente:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> Recuperare la raccolta

Il comando personalizzato insieme get restituisce l'oggetto raccolta.

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

Se il comando ha esito positivo, la risposta contiene un documento con i campi seguenti


|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stato della risposta. 1 = = esito positivo. 0 = = errore.      |
| `database`    |    `string`     |   Nome del database.      |
| `collection`    |    `string`     |    Nome della raccolta.     |
|  `shardKeyDefinition`   |   `document`      |  Documento di specifica dell'indice utilizzato come chiave di partizione. Questo è un parametro di risposta opzionale.       |
|  `provisionedThroughput`   |   `int`      |    Velocità effettiva con provisioning per impostare la raccolta. Questo è un parametro di risposta opzionale.     |

Se il comando non riesce, viene restituita una risposta di un comando personalizzato predefinito. Vedere le [predefinito output](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Ottenere la raccolta**

Per ottenere l'oggetto raccolta di una raccolta denominata "testCollection", usare il comando seguente:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> Output predefinito di un comando personalizzato

Se non specificato, una risposta personalizzata contiene un documento con i campi seguenti:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stato della risposta. 1 = = esito positivo. 0 = = errore.      |
| `code`    |   `int`      |   Restituito solo quando il comando non riuscito (vale a dire ok = = 0). Contiene il codice di errore di MongoDB. Questo è un parametro di risposta opzionale.      |
|  `errMsg`   |  `string`      |    Restituito solo quando il comando non riuscito (vale a dire ok = = 0). Contiene un messaggio di errore descrittivo. Questo è un parametro di risposta opzionale.      |

## <a name="next-steps"></a>Passaggi successivi

Passaggio successivo che è possibile procedere per apprendere i concetti di Azure Cosmos DB seguenti: 

* [Indicizzazione in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Impostare la scadenza automatica dei dati in Azure Cosmos DB con la durata (TTL)](../cosmos-db/time-to-live.md)
