---
title: Definire chiavi univoche per un contenitore di Azure Cosmos
description: Informazioni su come definire chiavi univoche per un contenitore di Azure Cosmos
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: thweiss
ms.openlocfilehash: 3b950565e0a44f979c11e3eb67b702c4dcb44769
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104898"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definire chiavi univoche per un contenitore di Azure Cosmos

Questo articolo presenta i diversi modi per definire [chiavi univoche](unique-keys.md) durante la creazione di un contenitore di Azure Cosmos. È attualmente possibile eseguire questa operazione usando il portale di Azure o tramite uno degli SDK.

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. [Creare un nuovo account Azure Cosmos](create-sql-api-dotnet.md#create-account) o selezionarne uno esistente.

1. Aprire il riquadro **Esplora dati** e selezionare il contenitore da usare.

1. Fare clic su **Nuovo contenitore**.

1. Nella finestra di dialogo **Aggiungi contenitore**, fare clic su **+ Aggiungi chiave univoca** per aggiungere una voce di chiave univoca.

1. Immettere il percorso o i percorsi del vincolo della chiave univoca

1. Se necessario, aggiungere altre voci di chiave univoca facendo clic su **+ Aggiungi chiave univoca**

![Screenshot di voce del vincolo della chiave univoca nel portale di Azure](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>Usare .NET SDK v2

Quando si crea un nuovo contenitore usando [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), è possibile usare un oggetto `UniqueKeyPolicy` per definire vincoli di chiave univoca.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    PartitionKey = new PartitionKeyDefinition { Paths = new Collection<string>(new List<string> { "/myPartitionKey" }) },
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "/emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-net-sdk-v3"></a>Usare .NET SDK V3

Quando si crea un nuovo contenitore con [.NET SDK V3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), usare l'API Fluent dell'SDK per dichiarare chiavi univoche in modo conciso e leggibile.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithUniqueKey()
        .Path("/firstName")
        .Path("/lastName")
        .Path("/emailAddress")
    .Attach()
    .WithUniqueKey()
        .Path("/address/zipCode")
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Usare Java SDK

Quando si crea un nuovo contenitore usando [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), è possibile usare un oggetto `UniqueKeyPolicy` per definire vincoli di chiave univoca.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");
// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");
// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);
// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);
// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);
// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Usare Node.js SDK

Quando si crea un nuovo contenitore usando [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos), è possibile usare un oggetto `UniqueKeyPolicy` per definire vincoli di chiave univoca.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Usare Python SDK

Quando si crea un nuovo contenitore usando [Python SDK](https://pypi.org/project/azure-cosmos/), è possibile specificare vincoli di chiave univoca come parte del dizionario passato come parametro.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [partizionamento](partition-data.md)
- Informazioni sul [funzionamento dell'indicizzazione](index-overview.md)