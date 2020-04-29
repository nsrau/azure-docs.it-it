---
title: Comandi di estensione MongoDB per gestire i dati nell'API Azure Cosmos DB per MongoDB
description: Questo articolo descrive come usare i comandi di estensione MongoDB per gestire i dati archiviati nell'API Azure Cosmos DB per MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f99c4d096bcbe1fbdc42cac80a491d6017266cb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80583576"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Usare i comandi di estensione MongoDB per gestire i dati archiviati nell'API Azure Cosmos DB per MongoDB 

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile comunicare con l'API Azure Cosmos DB per MongoDB usando uno dei [driver client MongoDB](https://docs.mongodb.org/ecosystem/drivers)open source. L'API Azure Cosmos DB per MongoDB consente l'uso di driver client esistenti aderendo al [protocollo wire di MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Usando l'API Azure Cosmos DB per MongoDB, è possibile usufruire dei vantaggi Cosmos DB ad esempio la distribuzione globale, il partizionamento orizzontale automatico, la disponibilità elevata, le garanzie di latenza, la crittografia automatica, la crittografia dei servizi inattivi, i backup e molto altro ancora, conservando gli investimenti nell'app MongoDB.

## <a name="mongodb-protocol-support"></a>Supporto del protocollo MongoDB

Per impostazione predefinita, l'API Azure Cosmos DB per MongoDB è compatibile con il server MongoDB versione 3,2. per altre informazioni, vedere [funzionalità e sintassi supportate](mongodb-feature-support.md). Le funzionalità o gli operatori di query aggiunti in MongoDB versione 3,4 sono attualmente disponibili come anteprima nell'API Azure Cosmos DB per MongoDB. I comandi di estensione seguenti supportano funzionalità di Azure Cosmos DB specifiche durante l'esecuzione di operazioni CRUD sui dati archiviati nell'API Azure Cosmos DB per MongoDB:

* [Crea database](#create-database)
* [Aggiorna database](#update-database)
* [Ottieni database](#get-database)
* [Crea raccolta](#create-collection)
* [Aggiorna raccolta](#update-collection)
* [Ottieni raccolta](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Crea database

Il comando create database Extension crea un nuovo database MongoDB. Il nome del database viene utilizzato dal contesto dei database in cui viene eseguito il comando. Il formato del comando CreateDatabase è il seguente:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:

|**Campo**|**Type** |**Descrizione** |
|---------|---------|---------|
| customAction   |  stringa  |   Nome del comando personalizzato, deve essere "CreateDatabase".      |
| offerThroughput | INT  | Velocità effettiva con provisioning impostata nel database. Questo parametro è facoltativo e, |

### <a name="output"></a>Output

Restituisce una risposta predefinita del comando personalizzato. Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Creazione di un database**

Per creare un database denominato "test", utilizzare il comando seguente:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Creazione di un database con velocità effettiva**

Per creare un database denominato "test" e una velocità effettiva con provisioning di 1000 ur, usare il comando seguente:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Aggiorna database

Il comando Update database Extension aggiorna le proprietà associate al database specificato. Attualmente, è possibile aggiornare solo la proprietà "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:

|**Campo**|**Type** |**Descrizione** |
|---------|---------|---------|
| customAction    |    stringa     |   Nome del comando personalizzato. Deve essere "UpdateDatabase".      |
|  offerThroughput   |  INT       |     Nuova velocità effettiva con provisioning che si desidera impostare nel database.    |

### <a name="output"></a>Output

Restituisce una risposta predefinita del comando personalizzato. Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Aggiornare la velocità effettiva con provisioning associata a un database**

Per aggiornare la velocità effettiva con provisioning di un database denominato "test" a 1200 ur, utilizzare il comando seguente:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Ottieni database

Il comando Get database Extension restituisce l'oggetto di database. Il nome del database viene utilizzato dal contesto del database in cui viene eseguito il comando.

```
{
  customAction: "GetDatabase"
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:


|**Campo**|**Type** |**Descrizione** |
|---------|---------|---------|
|  customAction   |   stringa      |   Nome del comando personalizzato. Deve essere "GetDatabase"|
        
### <a name="output"></a>Output

Se il comando ha esito positivo, la risposta contiene un documento con i campi seguenti:

|**Campo**|**Type** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stato della risposta. 1 = = operazione riuscita. 0 = = esito negativo.      |
| `database`    |    `string`        |   Nome del database.      |
|   `provisionedThroughput`  |    `int`      |    Velocità effettiva con provisioning impostata nel database. Si tratta di un parametro di risposta facoltativo.     |

Se il comando ha esito negativo, viene restituita una risposta predefinita del comando personalizzato. Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Ottenere il database**

Per ottenere l'oggetto di database per un database denominato "test", utilizzare il comando seguente:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Crea raccolta

Il comando create Collection Extension crea una nuova raccolta MongoDB. Il nome del database viene utilizzato dal contesto dei database in cui viene eseguito il comando. Il formato del comando CreateCollection è il seguente:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:

| **Campo** | **Type** | **Richiesto** | **Descrizione** |
|---------|---------|---------|---------|
| customAction | stringa | Obbligatoria | Nome del comando personalizzato. Deve essere "CreateCollection".|
| collection | stringa | Obbligatoria | Nome della raccolta. Non sono consentiti caratteri speciali.|
| offerThroughput | INT | Opzionale | Velocità effettiva con provisioning da impostare nel database. Se questo parametro non viene specificato, il valore predefinito sarà minimo 400 ur/sec. * Per specificare una velocità effettiva superiore a 10.000 UR/ `shardKey` s, il parametro è obbligatorio.|
| Chiave | stringa | Opzionale | Percorso della chiave di partizione per la raccolta partizionata. Questo parametro è obbligatorio se si impostano più di 10.000 UR/ `offerThroughput`sec in.  Se viene specificato, per tutti i documenti inseriti sarà necessario questo valore. |

### <a name="output"></a>Output

Restituisce una risposta predefinita del comando personalizzato. Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Creare una raccolta non partizionata**

Per creare una raccolta non partizionata con il nome "TestCollection" e la velocità effettiva con provisioning di 1000 ur, usare il comando seguente: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Creare una raccolta partizionata**

Per creare una raccolta partizionata con il nome "TestCollection" e la velocità effettiva con provisioning di 1000 ur e una proprietà chiave "a. b", usare il comando seguente:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Aggiorna raccolta

Il comando Update Collection Extension aggiorna le proprietà associate alla raccolta specificata.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:

|**Campo**|**Type** |**Descrizione** |
|---------|---------|---------|
|  customAction   |   stringa      |   Nome del comando personalizzato. Deve essere "Updatecollection".      |
|  collection   |   stringa      |   Nome della raccolta.       |
| offerThroughput   |INT|   Velocità effettiva con provisioning da impostare nella raccolta.|

## <a name="output"></a>Output

Restituisce una risposta predefinita del comando personalizzato. Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Aggiornare la velocità effettiva con provisioning associata a una raccolta**

Per aggiornare la velocità effettiva con provisioning di una raccolta denominata "TestCollection" a 1200 ur, usare il comando seguente:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Ottieni raccolta

Il comando Get Collection Custom restituisce l'oggetto Collection.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:


|**Campo**|**Type** |**Descrizione** |
|---------|---------|---------|
| customAction    |   stringa      |   Nome del comando personalizzato. Deve essere "GetCollection".      |
| collection    |    stringa     |    Nome della raccolta.     |

### <a name="output"></a>Output

Se il comando ha esito positivo, la risposta contiene un documento con i campi seguenti


|**Campo**|**Type** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stato della risposta. 1 = = operazione riuscita. 0 = = esito negativo.      |
| `database`    |    `string`     |   Nome del database.      |
| `collection`    |    `string`     |    Nome della raccolta.     |
|  `shardKeyDefinition`   |   `document`      |  Documento di specifica dell'indice usato come chiave di partizione. Si tratta di un parametro di risposta facoltativo.       |
|  `provisionedThroughput`   |   `int`      |    Velocità effettiva con provisioning da impostare nella raccolta. Si tratta di un parametro di risposta facoltativo.     |

Se il comando ha esito negativo, viene restituita una risposta predefinita del comando personalizzato. Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

**Ottenere la raccolta**

Per ottenere l'oggetto raccolta per una raccolta denominata "TestCollection", usare il comando seguente:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Output predefinito di un comando personalizzato

Se non specificato, una risposta personalizzata contiene un documento con i campi seguenti:

|**Campo**|**Type** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stato della risposta. 1 = = operazione riuscita. 0 = = esito negativo.      |
| `code`    |   `int`      |   Restituito solo quando il comando ha esito negativo, ad esempio OK = = 0. Contiene il codice di errore MongoDB. Si tratta di un parametro di risposta facoltativo.      |
|  `errMsg`   |  `string`      |    Restituito solo quando il comando ha esito negativo, ad esempio OK = = 0. Contiene un messaggio di errore descrittivo. Si tratta di un parametro di risposta facoltativo.      |

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile procedere con i concetti seguenti Azure Cosmos DB: 

* [Indicizzazione in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Impostare la scadenza automatica dei dati in Azure Cosmos DB con la durata (TTL)](../cosmos-db/time-to-live.md)
