---
title: Risoluzione dei conflitti multimaster in Azure Cosmos DB
description: Questo articolo descrive le categorie di conflitto e le modalità di risoluzione dei conflitti nel multimaster di Azure Cosmos DB, come priorità dell'ultima scrittura (LWW, Last-Writer-Wins), procedura definita dall'utente (personalizzata) e asincrona (personalizzata).
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 5feefdb8fe6204bc8ef42a5e65bf1e30354e0cf9
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393928"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Risoluzione dei conflitti multimaster in Azure Cosmos DB 

Il multimaster di Azure Cosmos DB offre modalità di risoluzione dei conflitti a livello globale per garantire la coerenza dei dati in tutte le aree in cui sono replicati.

## <a name="conflict-categories"></a>Categorie di conflitto

Esistono tre categorie di conflitto che possono essere riscontrate quando si lavora con i dati di Azure Cosmos DB:

* **Conflitti di inserimento:** questo tipo di conflitto si verifica quando l'applicazione inserisce due o più documenti con lo stesso indice univoco (ad esempio, ID) da due o più aree contemporaneamente. In questo caso, tutte le operazioni di scrittura possono avere esito positivo inizialmente ma, in base ai criteri di risoluzione dei conflitti scelti, viene eseguito il commit di un solo documento con l'ID originale.

* **Conflitti di sostituzione:** questo tipo di conflitto si verifica quando l'applicazione aggiorna un singolo documento contemporaneamente da due o più aree.

* **Conflitti di eliminazione:** questo tipo di conflitto si verifica quando l'applicazione elimina un documento da un'area e lo aggiorna da una o più aree. 

## <a name="conflict-resolution-modes"></a>Modalità di risoluzione dei conflitti

Azure Cosmos DB offre tre modalità di risoluzione dei conflitti. Le modalità di risoluzione dei conflitti vengono definite per ogni raccolta.

> [!NOTE]
> Gli utenti di API SQL possono scegliere tra tre diverse modalità di risoluzione dei conflitti. Per tutti gli altri modelli di API (MongoDB, Cassandra, Graph e Tabella), i conflitti vengono risolti usando la priorità dell'ultima scrittura (LWW, Last-Writer-Wins).

### <a name="last-writer-wins-lww"></a>Priorità dell'ultima scrittura (LWW)

La priorità dell'ultima scrittura è la modalità di risoluzione dei conflitti predefinita. In questa modalità i conflitti vengono risolti in base a un valore numerico passato in una proprietà nel documento.

Il frammento di codice seguente è un esempio di come configurare i criteri di risoluzione dei conflitti con la modalità LWW quando si usa .Net SDK. "ConflictResolutionPath" definisce il percorso della proprietà che viene usata per risolvere il conflitto. In questo esempio `/userDefinedId` è il percorso di risoluzione dei conflitti e il documento con il valore `userDefinedId` massimo avrà sempre la priorità nel conflitto. Per registrare una modalità di risoluzione LWW, effettuare il provisioning della raccolta con ConflictResolutionPolicy come illustrato di seguito.

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 La modalità di risoluzione LWW viene applicata a diverse categorie di conflitti di dati come indicato di seguito:

* **Conflitti di inserimento e aggiornamento:** se due o più documenti sono in conflitto nel corso di operazioni di inserimento o sostituzione, ha la priorità il documento contenente il valore massimo per il percorso di risoluzione dei conflitti (vale a dire, userDefinedId). Se più documenti hanno lo stesso valore numerico per il percorso di risoluzione dei conflitti, il documento prioritario selezionato è non deterministico. Tuttavia, tutte le aree convergeranno verso un unico documento prioritario.

* **Conflitto di eliminazione:** in caso di conflitti di eliminazione, l'eliminazione prevale sempre sugli altri conflitti di sostituzione indipendentemente dal valore del percorso di risoluzione dei conflitti.

### <a name="custom--user-defined-procedure"></a>Procedura definita dall'utente (personalizzata)

In questa modalità l'utente controlla la risoluzione dei conflitti registrando una procedura definita dall'utente (UDP, User Defined Procedure) con la raccolta. Questa UDP ha una firma specifica. Se si seleziona questa opzione ma non si riesce a registrare una UDP o se la UDP genera un'eccezione in fase di esecuzione, i conflitti vengono scritti nel feed dei conflitti dove possono essere risolti singolarmente.

Per registrare una modalità di risoluzione dei conflitti UDP (personalizzata), effettuare il provisioning della raccolta con ConflictResolutionPolicy come illustrato di seguito.

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

Aggiungere quindi la procedura definita dall'utente alla raccolta come illustrato di seguito.

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

La stored procedure registrata con la raccolta ha una firma speciale. Nell'esempio seguente la UDP usa la proprietà `UserDefinedId` per risolvere i conflitti. Il documento con il valore massimo ha la priorità nel conflitto.

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

La procedura ha quattro parametri:

* **incomingDocument**: specifica la versione in conflitto del documento. Il conflitto può essere di inserimento, sostituzione o eliminazione. Per un conflitto di eliminazione, si tratterà di un'immagine eliminata (rimozione definitiva) senza contenuto.

* **existingDocument**: specifica la versione del documento di cui è stato eseguito il commit, con lo stesso valore "rid" di incomingDocument. Questo parametro è impostato su null per un conflitto di inserimento ed eliminazione.

* **isDeleteConflict**: flag booleano che indica se il documento in ingresso è in conflitto con un documento eliminato in precedenza. Quando questo parametro è impostato su true, anche existingDocument sarà null.

* **conflictingDocuments**: specifica una raccolta della versione di cui è stato eseguito il commit per tutti i documenti nel database, che sono in conflitto con incomingDocument nella colonna ID o qualsiasi altro campo di indice univoco. Questi documenti avranno un valore "rid" diverso rispetto a incomingDocument.

La procedura definita dall'utente ha accesso completo alla chiave di partizione di Cosmos DB e può eseguire qualsiasi operazione di archiviazione per risolvere i conflitti. Se la procedura definita dall'utente non esegue il commit della versione in conflitto, il sistema eliminerà il conflitto e verrà mantenuto il commit di existingDocument. Se la procedura definita dall'utente non riesce o non esiste, Azure Cosmos DB aggiungerà il conflitto nel feed dei conflitti di sola lettura dove può essere elaborato in modo asincrono, come illustrato nella [modalità di risoluzione dei conflitti asincrona](). 

### <a name="custom--asynchronous"></a>Asincrona (personalizzata)  

In questa modalità Azure Cosmos DB esclude tutti i conflitti (inserimento, sostituzione ed eliminazione) dall'esecuzione del commit e li registra nel feed dei conflitti di sola lettura per la risoluzione posticipata da parte dell'applicazione dell'utente. L'applicazione può eseguire la risoluzione dei conflitti in modo asincrono e usare qualsiasi logica o fare riferimento a qualsiasi servizio, applicazione o origine esterna per risolvere il conflitto.

Per registrare una modalità di risoluzione dei conflitti asincrona (personalizzata), effettuare il provisioning della raccolta con ConflictResolutionPolicy come illustrato di seguito.

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

Per leggere ed elaborare i conflitti nel feed dei conflitti, implementare il codice riportato di seguito. I dati archiviati nel feed dei conflitti contribuiscono ad aumentare i costi di archiviazione. Pertanto, è consigliabile eliminare i dati archiviati nel feed dei conflitti dopo l'elaborazione.

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> Il feed dei conflitti non include un listener per l'invio di notifiche per l'elaborazione downstream, come il feed di modifiche in Cosmos DB. È necessario implementare la logica per eseguire il polling del feed dei conflitti e determinare se sono presenti conflitti.

## <a name="code-samples"></a>Esempi di codice

Di seguito sono riportate alcune applicazioni di esempio che illustrano la risoluzione dei conflitti per le API elencate. Ogni esempio genera conflitti all'interno di un contenitore e quindi spiega come vengono risolti per ogni modalità di risoluzione dei conflitti supportata.

|Modello API  | SDK |Esempio |
|---------|---------|---------|
|API SQL    | .NET    |[azure-cosmos-db-sql-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-dotnet-multi-master)  |
|API SQL    | Nodo    |[azure-cosmos-js/samples/MultiRegionWrite/](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|API SQL    | Java    |[azure-cosmos-db-sql-java-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-java-multi-master)  |
|MongoDB  | .NET    |[azure-cosmos-db-mongodb-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|API Tabella  | .NET    |[azure-cosmos-db-table-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|API Gremlin | .NET | [azure-cosmos-db-gremlin-dontnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-gremlin-dontnet-multi-master)|

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state illustrate le categorie di conflitto e le modalità di risoluzione dei conflitti per Azure Cosmos DB. Esaminare quindi le risorse seguenti:

* [Usare client MongoDB con multimaster](multi-master-oss-nosql.md)
