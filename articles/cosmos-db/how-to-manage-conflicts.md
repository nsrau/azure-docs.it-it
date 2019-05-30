---
title: Informazioni su come gestire i conflitti tra le aree in Azure Cosmos DB
description: Informazioni su come gestire i conflitti in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 63d8b0dc8c446f70b981aacd1a18e6a60a217983
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240941"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Gestire i criteri di risoluzione dei conflitti in Azure Cosmos DB

Con le scritture in più aree, se più client scrivono nello stesso elemento si potrebbero verificare conflitti. Se si verifica un conflitto, è possibile risolverlo usando diversi criteri di risoluzione. Questo articolo descrive come gestire i criteri di risoluzione dei conflitti.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Creare un criterio di risoluzione dei conflitti in cui prevale l'ultima scrittura

Questi esempi illustrano come configurare un contenitore con un criterio di risoluzione dei conflitti in cui prevale l'ultima scrittura. Il percorso predefinito del criterio per cui prevale l'ultima scrittura corrisponde al campo timestamp o alla proprietà `_ts`. Può essere impostato su un percorso definito dall'utente per un tipo numerico. In un conflitto prevale il valore più alto. Se il percorso non è impostato o non è valido, per impostazione predefinita si usa `_ts`. I conflitti risolti con questo criterio non vengono visualizzati nel feed di conflitti. Questo criterio può essere usato da tutte le API.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET SDK

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Java Sync SDK

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
                'id': self.udp_collection_name,
                'conflictResolutionPolicy': {
                    'mode': 'LastWriterWins',
                    'conflictResolutionPath': '/myCustomId'
                    }
                }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Creare un criterio personalizzato di risoluzione dei conflitti con una stored procedure

Questi esempi illustrano come configurare un contenitore con un criterio di risoluzione dei conflitti personalizzato con una stored procedure per risolvere il conflitto. Questi conflitti non vengono visualizzati nel feed dei conflitti, a meno che non vi sia un errore nella stored procedure. Dopo aver creato il criterio con il contenitore, è necessario creare la stored procedure. Di seguito è riportato un esempio di .NET SDK. Questo criterio è supportato solo dall'API Core (SQL).

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Esempio di stored procedure per la risoluzione personalizzata di conflitti

Le stored procedure per la risoluzione personalizzata di conflitti devono essere implementate usando la firma della funzione indicata di seguito. Anche se non è necessario, scegliendo per la funzione lo stesso nome usato per la registrazione della stored procedure con il contenitore si semplifica la denominazione. Ecco una descrizione dei parametri che è necessario implementare per questa stored procedure.

- **incomingItem**: l'elemento inserito o aggiornato nel commit che genera i conflitti. Per le operazioni di eliminazione è Null.
- **existingItem**: l'elemento di cui è stato eseguito il commit. Questo valore è non Null in un aggiornamento e Null per un'operazione di inserimento o eliminazione.
- **isTombstone**: valore booleano che indica se incomingItem è in conflitto con un elemento eliminato in precedenza. Se True, existingItem è Null.
- **conflictingItems**: matrice della versione sottoposta a commit di tutti gli elementi del contenitore in conflitto con incomingItem per l'ID o qualsiasi altra proprietà di indice univoca.

> [!IMPORTANT]
> Come per qualsiasi stored procedure, una procedura personalizzata di risoluzione dei conflitti può accedere ai dati con la stessa chiave di partizione e può eseguire qualsiasi operazione di inserimento, aggiornamento o eliminazione per risolvere i conflitti.


Questo esempio di stored procedure risolve i conflitti selezionando il valore minimo dal percorso `/myCustomId`.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET SDK

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Dopo aver creato il contenitore, è necessario creare la stored procedure `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>Java Sync SDK

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Dopo aver creato il contenitore, è necessario creare la stored procedure `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Dopo aver creato il contenitore, è necessario creare la stored procedure `resolver`.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
  'id': self.udp_collection_name,
  'conflictResolutionPolicy': {
      'mode': 'Custom',
      'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
      }
  }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

Dopo aver creato il contenitore, è necessario creare la stored procedure `resolver`.


## <a name="create-a-custom-conflict-resolution-policy"></a>Creare un criterio di risoluzione dei conflitti personalizzato

Questi esempi illustrano come configurare un contenitore con un criterio di risoluzione dei conflitti personalizzato. Questi conflitti vengono visualizzati nel feed dei conflitti.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET SDK

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>Java Sync SDK

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
                    'id': self.manual_collection_name,
                    'conflictResolutionPolicy': {
                          'mode': 'Custom'
                        }
                    }
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Leggere dal feed dei conflitti

Questi esempi illustrano come leggere dal feed dei conflitti di un contenitore. I conflitti vengono visualizzati nel feed dei conflitti solo se non sono stati risolti automaticamente oppure se si usa un criterio personalizzato di risoluzione.

### <a id="read-from-conflict-feed-dotnet"></a>.NET SDK

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-java-async"></a>Java Async SDK

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>Java Sync SDK

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Passaggi successivi

Apprendere i concetti di Azure Cosmos DB seguenti:

* [Distribuzione globale - Informazioni sul funzionamento](global-dist-under-the-hood.md)
* [Come configurare funzionalità multimaster nelle applicazioni](how-to-multi-master.md)
* [Configurare i client per il multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Aggiungere o rimuovere aree dall'account Azure Cosmos DB](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Come configurare funzionalità multimaster nelle applicazioni](how-to-multi-master.md).
* [Partizionamento e distribuzione dei dati](partition-data.md)
* [Indicizzazione in Azure Cosmos DB](indexing-policies.md)