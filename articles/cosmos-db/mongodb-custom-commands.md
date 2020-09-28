---
title: Comandi di estensione MongoDB per gestire i dati nell'API Azure Cosmos DB per MongoDB
description: Questo articolo descrive come usare i comandi di estensione MongoDB per gestire i dati archiviati nell'API Azure Cosmos DB per MongoDB.
author: jasonwhowell
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2020
ms.author: jasonh
ms.custom: devx-track-js
ms.openlocfilehash: a40be5212fb1335482ec5011d24c8eaf5f3d9a00
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409681"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Usare i comandi di estensione MongoDB per gestire i dati archiviati nell'API Azure Cosmos DB per MongoDB 

Il documento seguente contiene i comandi dell'azione personalizzata specifici dell'API Azure Cosmos DB per MongoDB. Questi comandi possono essere utilizzati per creare e ottenere risorse di database specifiche per il [modello di capacità Azure Cosmos DB](databases-containers-items.md).

Usando l'API Azure Cosmos DB per MongoDB, è possibile usufruire dei vantaggi Cosmos DB ad esempio la distribuzione globale, il partizionamento orizzontale automatico, la disponibilità elevata, le garanzie di latenza, la crittografia automatica, la crittografia dei servizi inattivi, i backup e molto altro ancora, conservando gli investimenti nell'app MongoDB. È possibile comunicare con l'API Azure Cosmos DB per MongoDB usando uno dei [driver client MongoDB](https://docs.mongodb.org/ecosystem/drivers)open source. L'API Azure Cosmos DB per MongoDB consente l'uso di driver client esistenti aderendo al [protocollo wire di MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

## <a name="mongodb-protocol-support"></a>Supporto del protocollo MongoDB

L'API di Azure Cosmos DB per MongoDB è compatibile con il server MongoDB versione 3,2 e 3,6. Per altri dettagli, vedere [funzionalità e sintassi supportate](mongodb-feature-support.md) . 

I seguenti comandi di estensione consentono di creare e modificare risorse specifiche di Azure Cosmos DB tramite richieste di database:

* [Crea database](#create-database)
* [Aggiorna database](#update-database)
* [Ottieni database](#get-database)
* [Crea raccolta](#create-collection)
* [Aggiorna raccolta](#update-collection)
* [Ottieni raccolta](#get-collection)

## <a name="create-database"></a><a id="create-database"></a> Crea database

Il comando create database Extension crea un nuovo database MongoDB. Il nome del database può essere utilizzato dal contesto del database impostato dal `use database` comando. Nella tabella seguente vengono descritti i parametri all'interno del comando:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
| `customAction`   |  `string`  |   Nome del comando personalizzato, deve essere "CreateDatabase".      |
| `offerThroughput` | `int`  | Velocità effettiva con provisioning impostata nel database. Questo parametro è facoltativo e, |
| `autoScaleSettings` | `Object` | Obbligatorio per la [modalità di ridimensionamento automatico](provision-throughput-autoscale.md). Questo oggetto contiene le impostazioni associate alla modalità di capacità di scalabilità automatica. È possibile impostare il `maxThroughput` valore, che descrive la quantità più elevata di unità richiesta che la raccolta verrà aumentata in modo dinamico. |

### <a name="output"></a>Output

Se il comando ha esito positivo, verrà restituita la risposta seguente:

```javascript
{ "ok" : 1 }
```

Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

#### <a name="create-a-database"></a>Creazione di un database

Per creare un database denominato `"test"` che utilizza tutti i valori predefiniti, utilizzare il comando seguente:

```javascript
use test
db.runCommand({customAction: "CreateDatabase"});
```

Questo comando creerà un database senza velocità effettiva a livello di database. Ciò significa che le raccolte all'interno di questo database dovranno specificare la quantità di velocità effettiva che è necessario utilizzare.

#### <a name="create-a-database-with-throughput"></a>Creazione di un database con velocità effettiva

Per creare un database denominato `"test"` e per specificare una velocità effettiva con provisioning a [livello di database](set-throughput.md#set-throughput-on-a-database) di 1000 ur, utilizzare il comando seguente:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

In questo modo viene creato un database e viene impostata una velocità effettiva. Tutte le raccolte all'interno di questo database condivideranno la velocità effettiva del set, a meno che le raccolte non vengano create con [un livello di velocità effettiva specifico](set-throughput.md#set-throughput-on-a-database-and-a-container).

#### <a name="create-a-database-with-autoscale-throughput"></a>Creare un database con velocità effettiva di scalabilità automatica

Per creare un database denominato `"test"` e per specificare una velocità effettiva massima di scalabilità automatica di 20.000 UR/s a [livello di database](set-throughput.md#set-throughput-on-a-database), usare il comando seguente:

```javascript
use test
db.runCommand({customAction: "CreateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```

## <a name="update-database"></a><a id="update-database"></a> Aggiorna database

Il comando Update database Extension aggiorna le proprietà associate al database specificato. Nella tabella seguente vengono descritti i parametri all'interno del comando:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
| `customAction`    |    `string`     |   Nome del comando personalizzato. Deve essere "UpdateDatabase".      |
|  `offerThroughput`   |  `int`       |     Nuova velocità effettiva con provisioning che si desidera impostare nel database se il database utilizza una [velocità effettiva a livello di database](set-throughput.md#set-throughput-on-a-database)  |
| `autoScaleSettings` | `Object` | Obbligatorio per la [modalità di ridimensionamento automatico](provision-throughput-autoscale.md). Questo oggetto contiene le impostazioni associate alla modalità di capacità di scalabilità automatica. È possibile impostare il `maxThroughput` valore, che descrive la quantità più elevata di unità richiesta per cui il database verrà aumentato in modo dinamico. |

Questo comando usa il database specificato nel contesto della sessione. Si tratta del database usato nel `use <database>` comando. Al momento non è possibile modificare il nome del database utilizzando questo comando.

### <a name="output"></a>Output

Se il comando ha esito positivo, verrà restituita la risposta seguente:

```javascript
{ "ok" : 1 }
```

Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

#### <a name="update-the-provisioned-throughput-associated-with-a-database"></a>Aggiornare la velocità effettiva con provisioning associata a un database

Per aggiornare la velocità effettiva con provisioning di un database con `"test"` il nome 1200 ur, usare il comando seguente:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

#### <a name="update-the-autoscale-throughput-associated-with-a-database"></a>Aggiornare la velocità effettiva di ridimensionamento automatico associata a un database

Per aggiornare la velocità effettiva con provisioning di un database con `"test"` il nome 20.000 ur o per trasformarlo in un [livello di velocità effettiva di ridimensionamento](provision-throughput-autoscale.md)automatico, usare il comando seguente:

```javascript
use test
db.runCommand({customAction: "UpdateDatabase", autoScaleSettings: { maxThroughput: 20000 } });
```


## <a name="get-database"></a><a id="get-database"></a> Ottieni database

Il comando Get database Extension restituisce l'oggetto di database. Il nome del database viene utilizzato dal contesto del database in cui viene eseguito il comando.

```javascript
{
  customAction: "GetDatabase"
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:


|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Nome del comando personalizzato. Deve essere "GetDatabase"|
        
### <a name="output"></a>Output

Se il comando ha esito positivo, la risposta contiene un documento con i campi seguenti:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Stato della risposta. 1 = = operazione riuscita. 0 = = esito negativo.      |
| `database`    |    `string`        |   Nome del database.      |
|   `provisionedThroughput`  |    `int`      |    Velocità effettiva con provisioning impostata sul database se il database utilizza una  [velocità effettiva a livello di database manuale](set-throughput.md#set-throughput-on-a-database)     |
| `autoScaleSettings` | `Object` | Questo oggetto contiene i parametri di capacità associati al database se utilizza la modalità di [ridimensionamento automatico](provision-throughput-autoscale.md). Il `maxThroughput` valore descrive la quantità più elevata di unità richiesta che il database verrà aumentato in modo dinamico. |

Se il comando ha esito negativo, viene restituita una risposta predefinita del comando personalizzato. Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

#### <a name="get-the-database"></a>Ottenere il database

Per ottenere l'oggetto di database per un database denominato `"test"` , utilizzare il comando seguente:

```javascript
use test
db.runCommand({customAction: "GetDatabase"});
```

Se al database non è associata una velocità effettiva, l'output sarà:

```javascript
{ "database" : "test", "ok" : 1 }
```

Se al database è associata una [velocità effettiva manuale a livello di database](set-throughput.md#set-throughput-on-a-database) , l'output indicherà i `provisionedThroughput` valori:

```javascript
{ "database" : "test", "provisionedThroughput" : 20000, "ok" : 1 }
```

Se al database è associata una [velocità effettiva di scalabilità](provision-throughput-autoscale.md) automatica a livello di database, l'output visualizzerà `provisionedThroughput` , che descrive le UR/sec minime per il database e l' `autoScaleSettings` oggetto `maxThroughput` , incluso, che descrive il numero massimo di ur/sec per il database.

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
                "maxThroughput" : 20000
        },
        "ok" : 1
}
```

## <a name="create-collection"></a><a id="create-collection"></a> Crea raccolta

Il comando create Collection Extension crea una nuova raccolta MongoDB. Il nome del database viene utilizzato dal contesto dei database impostato dal `use database` comando. Il formato del comando CreateCollection è il seguente:

```javascript
{
  customAction: "CreateCollection",
  collection: "<Collection Name>",
  shardKey: "<Shard key path>",
  offerThroughput: (int), // Amount of throughput allocated to a specific collection

}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:

| **Campo** | **Tipo** | **Obbligatorio** | **Descrizione** |
|---------|---------|---------|---------|
| `customAction` | `string` | Obbligatoria | Nome del comando personalizzato. Deve essere "CreateCollection".|
| `collection` | `string` | Obbligatoria | Nome della raccolta. Non sono consentiti spazi o caratteri speciali.|
| `offerThroughput` | `int` | Facoltativo | Velocità effettiva con provisioning da impostare nel database. Se questo parametro non viene specificato, il valore predefinito sarà minimo 400 ur/sec. * Per specificare una velocità effettiva superiore a 10.000 UR/s, il `shardKey` parametro è obbligatorio.|
| `shardKey` | `string` | Obbligatorio per le raccolte con velocità effettiva elevata | Percorso della chiave di partizione per la raccolta partizionata. Questo parametro è obbligatorio se si impostano più di 10.000 UR/sec in `offerThroughput` .  Se specificato, tutti i documenti inseriti richiedono questa chiave e un valore. |
| `autoScaleSettings` | `Object` | Obbligatorio per la [modalità di scalabilità](provision-throughput-autoscale.md) automatica | Questo oggetto contiene le impostazioni associate alla modalità di capacità di scalabilità automatica. È possibile impostare il `maxThroughput` valore, che descrive la quantità più elevata di unità richiesta che la raccolta verrà aumentata in modo dinamico. |

### <a name="output"></a>Output

Restituisce una risposta predefinita del comando personalizzato. Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

#### <a name="create-a-collection-with-the-minimum-configuration"></a>Creare una raccolta con la configurazione minima

Per creare una nuova raccolta con `"testCollection"` il nome e i valori predefiniti, utilizzare il comando seguente: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection"});
```

Si otterrà quindi una nuova raccolta fissa, non partizionata, con 400RU/s e un indice nel `_id` campo creato automaticamente. Questo tipo di configurazione verrà applicato anche durante la creazione di nuove raccolte tramite la `insert()` funzione. Ad esempio: 

```javascript
use test
db.newCollection.insert({});
```

#### <a name="create-a-unsharded-collection"></a>Creare una raccolta non partizionata

Per creare una raccolta non partizionata con `"testCollection"` il nome e la velocità effettiva con provisioning di 1000 ur, usare il comando seguente: 

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

È possibile creare una raccolta con un massimo di 10.000 UR/s come `offerThroughput` senza dover specificare una chiave di partizione. Per le raccolte con una velocità effettiva maggiore, vedere la sezione successiva.

#### <a name="create-a-sharded-collection"></a>Creare una raccolta partizionata

Per creare una raccolta partizionata con `"testCollection"` il nome e la velocità effettiva con provisioning di 11.000 UR e una `shardkey` Proprietà "a. b", usare il comando seguente:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 11000, shardKey: "a.b" });
```

Questo comando richiede ora il `shardKey` parametro, dal momento che sono state specificate più di 10.000 UR/sec nella `offerThroughput` .

#### <a name="create-an-unsharded-autoscale-collection"></a>Creare una raccolta di scalabilità automatica non partizionata

Per creare una raccolta non partizionata denominata `'testCollection'` che usa la [capacità della velocità effettiva di scalabilità](provision-throughput-autoscale.md) automatica impostata su 4.000 UR/s, usare il comando seguente:

```javascript
use test
db.runCommand({ 
    customAction: "CreateCollection", collection: "testCollection", 
    autoScaleSettings:{
      maxThroughput: 4000
    } 
});
```

Per il `autoScaleSettings.maxThroughput` valore è possibile specificare un intervallo compreso tra 4.000 UR/s e 10.000 UR/s senza una chiave di partizione. Per una maggiore velocità effettiva di scalabilità automatica, è necessario specificare il `shardKey` parametro.

#### <a name="create-a-sharded-autoscale-collection"></a>Creare una raccolta di scalabilità automatica partizionata

Per creare una raccolta partizionata denominata `'testCollection'` con una chiave di partizione denominata `'a.b'` e che usa la [capacità della velocità effettiva di scalabilità](provision-throughput-autoscale.md) automatica impostata su 20.000 UR/s, usare il comando seguente:

```javascript
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", shardKey: "a.b", autoScaleSettings: { maxThroughput: 20000 }});
```

## <a name="update-collection"></a><a id="update-collection"></a> Aggiorna raccolta

Il comando Update Collection Extension aggiorna le proprietà associate alla raccolta specificata.

```javascript
{
  customAction: "UpdateCollection",
  collection: "<Name of the collection that you want to update>",
  offerThroughput: (int) // New throughput that will be set to the collection
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `customAction`   |   `string`      |   Nome del comando personalizzato. Deve essere "Updatecollection".      |
|  `collection`   |   `string`      |   Nome della raccolta.       |
| `offerThroughput` | `int` |   Velocità effettiva con provisioning da impostare nella raccolta.|
| `autoScaleSettings` | `Object` | Obbligatorio per la [modalità di ridimensionamento automatico](provision-throughput-autoscale.md). Questo oggetto contiene le impostazioni associate alla modalità di capacità di scalabilità automatica. Il `maxThroughput` valore descrive la quantità più elevata di unità richiesta che l'insieme verrà aumentato in modo dinamico. |

## <a name="output"></a>Output

Restituisce una risposta predefinita del comando personalizzato. Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

#### <a name="update-the-provisioned-throughput-associated-with-a-collection"></a>Aggiornare la velocità effettiva con provisioning associata a una raccolta

Per aggiornare la velocità effettiva con provisioning di una raccolta con `"testCollection"` il nome 1200 ur, usare il comando seguente:

```javascript
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a> Ottieni raccolta

Il comando Get Collection Custom restituisce l'oggetto Collection.

```javascript
{
  customAction: "GetCollection",
  collection: "<Name of the collection>"
}
```

Nella tabella seguente vengono descritti i parametri all'interno del comando:


|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
| `customAction`    |   `string`      |   Nome del comando personalizzato. Deve essere "GetCollection".      |
| `collection`    |    `string`     |    Nome della raccolta.     |

### <a name="output"></a>Output

Se il comando ha esito positivo, la risposta contiene un documento con i campi seguenti


|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stato della risposta. 1 = = operazione riuscita. 0 = = esito negativo.      |
| `database`    |    `string`     |   Nome del database.      |
| `collection`    |    `string`     |    Nome della raccolta.     |
|  `shardKeyDefinition`   |   `document`      |  Documento di specifica dell'indice usato come chiave di partizione. Si tratta di un parametro di risposta facoltativo.       |
|  `provisionedThroughput`   |   `int`      |    Velocità effettiva con provisioning da impostare nella raccolta. Si tratta di un parametro di risposta facoltativo.     |
| `autoScaleSettings` | `Object` | Questo oggetto contiene i parametri di capacità associati al database se utilizza la modalità di [ridimensionamento automatico](provision-throughput-autoscale.md). Il `maxThroughput` valore descrive la quantità più elevata di unità richiesta che l'insieme verrà aumentato in modo dinamico. |

Se il comando ha esito negativo, viene restituita una risposta predefinita del comando personalizzato. Vedere l' [output predefinito](#default-output) del comando personalizzato per i parametri nell'output.

### <a name="examples"></a>Esempi

#### <a name="get-the-collection"></a>Ottenere la raccolta

Per ottenere l'oggetto raccolta per una raccolta denominata `"testCollection"` , utilizzare il comando seguente:

```javascript
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

Se alla raccolta è associata una capacità di velocità effettiva, includerà il `provisionedThroughput` valore e l'output sarà:

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 400,
        "ok" : 1
}
```

Se la raccolta ha una velocità effettiva di ridimensionamento automatico associata, includerà l' `autoScaleSettings` oggetto con il `maxThroughput` parametro, che definisce la velocità effettiva massima che la raccolta aumenterà in modo dinamico. Inoltre, includerà anche il `provisionedThroughput` valore, che definisce la velocità effettiva minima che questa raccolta ridurrà in se non sono presenti richieste nella raccolta: 

```javascript
{
        "database" : "test",
        "collection" : "testCollection",
        "provisionedThroughput" : 1000,
        "autoScaleSettings" : {
            "maxThroughput" : 10000
        },
        "ok" : 1
}
```

Se la raccolta condivide la [velocità effettiva a livello di database](set-throughput.md#set-throughput-on-a-database), sia in modalità di scalabilità automatica che manuale, l'output sarà:

```javascript
{ "database" : "test", "collection" : "testCollection", "ok" : 1 }
```

```javascript
{
        "database" : "test",
        "provisionedThroughput" : 2000,
        "autoScaleSettings" : {
            "maxThroughput" : 20000
        },
        "ok" : 1
}
```


## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a> Output predefinito di un comando personalizzato

Se non specificato, una risposta personalizzata contiene un documento con i campi seguenti:

|**Campo**|**Tipo** |**Descrizione** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Stato della risposta. 1 = = operazione riuscita. 0 = = esito negativo.      |
| `code`    |   `int`      |   Restituito solo quando il comando ha esito negativo, ad esempio OK = = 0. Contiene il codice di errore MongoDB. Si tratta di un parametro di risposta facoltativo.      |
|  `errMsg`   |  `string`      |    Restituito solo quando il comando ha esito negativo, ad esempio OK = = 0. Contiene un messaggio di errore descrittivo. Si tratta di un parametro di risposta facoltativo.      |

Ad esempio:

```javascript
{ "ok" : 1 }
```

## <a name="next-steps"></a>Passaggi successivi

A questo punto è possibile procedere con i concetti seguenti Azure Cosmos DB: 

* [Indicizzazione in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Impostare la scadenza automatica dei dati in Azure Cosmos DB con la durata (TTL)](../cosmos-db/time-to-live.md)
