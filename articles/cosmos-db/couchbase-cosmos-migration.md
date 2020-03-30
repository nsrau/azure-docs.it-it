---
title: Eseguire la migrazione da CouchBase all'API SQL del database Cosmos di AzureMigrate from CouchBase to Azure Cosmos DB SQL API
description: Istruzioni dettagliate per la migrazione da CouchBase a Azure Cosmos DB SQL API
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77210944"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Eseguire la migrazione da CouchBase all'API SQL del database Cosmos di AzureMigrate from CouchBase to Azure Cosmos DB SQL API

Azure Cosmos DB è un database scalabile, distribuito a livello globale e completamente gestito. Fornisce un accesso garantito a bassa latenza ai dati. Per altre informazioni su Azure Cosmos DB, vedere l'articolo [Panoramica.](introduction.md) Questo articolo fornisce istruzioni per eseguire la migrazione di applicazioni Java connesse a Couchbase a un account API SQL in Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Differenze nella nomenclatura

The following are the key features that work differently in Azure Cosmos DB when compared to Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Server Couchbase| Account       |
|Secchio           | Database      |
|Secchio           | Contenitore/Raccolta |
|Documento JSON    | Articolo / Documento |

## <a name="key-differences"></a>Differenze principali

* Azure Cosmos DB ha un campo "ID" all'interno del documento, mentre Couchbase ha l'ID come parte del bucket. Il campo "ID" è univoco in tutta la partizione.

* Azure Cosmos DB viene ridimensionato usando la tecnica di partizionamento o partizionamento orizzontale. Il che significa che suddivide i dati in più partizioni/partizioni. Queste partizioni/partizioni vengono create in base alla proprietà della chiave di partizione fornita. È possibile selezionare la chiave di partizione per ottimizzare anche le operazioni di lettura di scrittura o anche le operazioni di lettura/scrittura ottimizzate. Per altre informazioni, vedere l'articolo [sul partizionamento.](./partition-data.md)

* In Azure Cosmos DB non è necessario che la gerarchia di primo livello denoti la raccolta perché il nome della raccolta esiste già. Questa funzionalità rende la struttura JSON molto più semplice. The following is an example that shows differences in the data model between Couchbase and Azure Cosmos DB:

   **Couchbase**: ID documento - "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: Fare riferimento a "ID" all'interno del documento come illustrato di seguitoAzure Cosmos DB : Refer "ID" within the document as shown below

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Supporto per Java SDK

Azure Cosmos DB ha i seguenti SDK per supportare diversi framework Java:Azure Cosmos DB has following SDKs to support different Java frameworks:

* SDK asincrono
* Spring Boot SDK

Le sezioni seguenti descrivono quando usare ognuno di questi SDK. Si consideri un esempio in cui sono disponibili tre tipi di carichi di lavoro:Consider an example where we have three types of workloads:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase come archivio documenti & query personalizzate basate sui dati di primavera

Se il carico di lavoro di cui si esegue la migrazione è basato su Spring Boot Based SDK, è possibile utilizzare la procedura seguente:If the workload that you are migrating is based on Spring Boot Based SDK, then you can use the following steps:

1. Aggiungere il padre al file POM.xml:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Aggiungere proprietà al file POM.xml:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Aggiungere dipendenze al file POM.xml:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Aggiungere le proprietà dell'applicazione in risorse e specificare quanto segue. Assicurarsi di sostituire i parametri URL, chiave e nome del database:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Definire il nome della raccolta nel modello. È inoltre possibile specificare ulteriori annotazioni. Ad esempio, ID, chiave di partizione per indicarli in modo esplicito:For example, ID, partition key to denote them explicitly:

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

Di seguito sono riportati i frammenti di codice per le operazioni CRUD:The following are the code snippets for CRUD operations:

### <a name="insert-and-update-operations"></a>Operazioni di inserimento e aggiornamento

Dove *_repo* è l'oggetto del repository e *doc* è l'oggetto della classe POJO. È possibile `.save` utilizzare per inserire o upsert (se il documento con l'ID specificato trovato). Il frammento di codice seguente mostra come inserire o aggiornare un oggetto doc:The following code snippet shows how to insert or update a doc object:

```_repo.save(doc);```

### <a name="delete-operation"></a>Operazioni di eliminazione

Si consideri il frammento di codice seguente, in cui l'oggetto doc avrà ID e chiave di partizione obbligatori per individuare ed eliminare l'oggetto:Consider the following code snippet, where doc object will have ID and partition key mandatory to locate and delete the object:

```_repo.delete(doc);```

### <a name="read-operation"></a>Operazione di lettura

È possibile leggere il documento con o senza specificare la chiave di partizione. Se non si specifica la chiave di partizione, questa viene considerata come una query tra partizioni. Si considerino gli esempi di codice seguenti, il primo eseguirà l'operazione usando ID e il campo della chiave di partizione. Nel secondo esempio viene utilizzato un & di campo regolare senza specificare il campo della chiave di partizione.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

In questo modo, è ora possibile usare l'applicazione con Azure Cosmos DB. L'esempio di codice completo per l'esempio descritto in questo documento è disponibile nel repository CouchbaseToCosmosDB-SpringCosmos GitHub.Complete code sample for the example described in this doc is available in the [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub repo.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase come & dell'archivio documenti tramite query N1QL

Le query N1QL sono il modo per definire le query in Couchbase.

|N1QL Query | Query CosmosDB di AzureAzure CosmosDB Query|
|-------------------|-------------------|
|SELECT`TravelDocument`META( ).id `TravelDocument`AS id, . . . . . . `TravelDocument` . . . . . . . . . `_type` . . . . . . . . . . . . ` Validity` . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .   | SELECT c.id.c FROM c JOIN m in c.country''India' WHERE c._type - " com.xx.xx.xx.xxx.xxx.xxxx" e c.country ' India' e m.type ' 'Multi-Entry' e m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC 0 OFFSET 25 |

È possibile notare le seguenti modifiche nelle query N1QL:

* Non è necessario utilizzare la parola chiave META o fare riferimento al documento di primo livello. È invece possibile creare un riferimento personalizzato al contenitore. In questo esempio, l'abbiamo considerata come "c" (può essere qualsiasi cosa). Questo riferimento viene utilizzato come prefisso per tutti i campi di primo livello. Fr esempio, c.id, c.country ecc.

* Invece di "QUALSIASI" ora puoi fare un join su un documento secondario e farlo riferimento con un alias dedicato come "m". Dopo aver creato l'alias per un documento secondario, è necessario utilizzare l'alias. Ad esempio, m.Country.

* La sequenza di OFFSET è diversa nella query del database Cosmos di Azure, prima è necessario specificare OFFSET, quindi LIMIT. Si consiglia di non usare Spring Data SDK se si usano il numero massimo di query personalizzate definite in quanto può presentare un sovraccarico non necessario sul lato client durante il passaggio della query a Azure Cosmos DB. Invece abbiamo un SDK Java asincrono diretto, che può essere utilizzato in modo molto efficiente in questo caso.

### <a name="read-operation"></a>Operazione di lettura

Utilizzare Async Java SDK con la procedura seguente:

1. Configurare la dipendenza seguente dal file POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Creare un oggetto connessione per Azure `ConnectionBuilder` Cosmos DB usando il metodo come illustrato nell'esempio seguente. Assicurarsi di inserire questa dichiarazione nel bean in modo che il codice seguente deve essere eseguito una sola volta:

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. Per eseguire la query, è necessario eseguire il frammento di codice seguente:To execute the query, you need to run the following code snippet:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Ora, con l'aiuto del metodo sopra è possibile passare più query ed eseguire senza alcun problema. Nel caso in cui si abbia la necessità di eseguire una query di grandi dimensioni, che può essere suddivisa in più query, quindi provare il frammento di codice seguente anziché quello precedente:

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

Con il codice precedente, è possibile eseguire query in parallelo e aumentare le esecuzioni distribuite da ottimizzare. Inoltre è possibile eseguire le operazioni di inserimento e aggiornamento troppo:

### <a name="insert-operation"></a>Operazioni di inserimento

Per inserire il documento, eseguire il codice seguente:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Quindi iscriviti a Mono come:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Operazione di upsert

L'operazione Upsert richiede di specificare il documento da aggiornare. Per recuperare il documento completo, è possibile utilizzare il frammento di codice menzionato sotto l'operazione di lettura dell'intestazione, quindi modificare i campi obbligatori. Il frammento di codice seguente consente di il backup del documento:The following code snippet upserts the document:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Poi iscriviti a mono. Fare riferimento al frammento di sottoscrizione mono nell'operazione di inserimento.

### <a name="delete-operation"></a>Operazioni di eliminazione

Il frammento di codice seguente eseguirà l'operazione di eliminazione:Following snippet will do delete operation:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Quindi sottoscrivere mono, fare riferimento a snippet di sottoscrizione mono nell'operazione di inserimento. L'esempio di codice completo è disponibile nel repo CouchbaseToCosmosDB-AsyncInSpring GitHub.The complete code sample is available in the [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub repo.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase come coppia chiave/valore

Si tratta di un tipo semplice di carico di lavoro in cui è possibile eseguire ricerche anziché query. Utilizzare la procedura seguente per le coppie chiave/valore:Use the following steps for key/value pairs:

1. Considerare la possibilità di avere "/ID" come chiave primaria, che consente di eseguire l'operazione di ricerca direttamente nella partizione specifica. Creare una raccolta e specificare "/ID" come chiave di partizione.

1. Disattivare completamente l'indicizzazione. Poiché verranno eseguite le operazioni di ricerca, non è necessario portare l'overhead di indicizzazione. Per disattivare l'indicizzazione, accedere al portale di Azure, passare all'account database di Azure Cosmos.To turn off indexing, sign into Azure portal, goto Azure Cosmos DB Account. Aprire **Esplora dati**, selezionare il **database** e il **contenitore**. Aprire la scheda **Impostazioni & scala** e selezionare Criteri di **indicizzazione**. I criteri di indicizzazione attualmente sono simili al seguente:Currently indexing policy looks like the following:
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   Sostituire il criterio di indicizzazione precedente con il criterio seguente:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. Utilizzare il frammento di codice seguente per creare l'oggetto connessione. Oggetto connessione (da @Bean inserire o renderlo statico):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

Ora è possibile eseguire le operazioni CRUD come segue:

### <a name="read-operation"></a>Operazione di lettura

Per leggere l'elemento, utilizzare il frammento di codice seguente:To read the item, use the following snippet:

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>Operazioni di inserimento

Per inserire un elemento, è possibile eseguire il codice seguente:To insert an item, you can perform the following code:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Quindi iscriviti a mono come:

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Operazione di upsert

Per aggiornare il valore di un elemento, fare riferimento al frammento di codice seguente:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Quindi sottoscrivere mono, fare riferimento a snippet di sottoscrizione mono nell'operazione di inserimento.

### <a name="delete-operation"></a>Operazioni di eliminazione

Utilizzare il frammento di codice seguente per eseguire l'operazione di eliminazione:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Quindi sottoscrivere mono, fare riferimento a snippet di sottoscrizione mono nell'operazione di inserimento. L'esempio di codice completo è disponibile nel repository CouchbaseToCosmosDB-AsyncKeyValue GitHub.The complete code sample is available in the [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub repo.

## <a name="data-migration"></a>Migrazione dei dati

Esistono due modi per eseguire la migrazione dei dati.

* **Usare Azure Data Factory:Use Azure Data Factory:** Questo è il metodo più consigliato per eseguire la migrazione dei dati. Configurare l'origine come Couchbase e sink come API SQL di Azure Cosmos DB, vedere l'articolo sul connettore Azure [Cosmos DB Data Factory](../data-factory/connector-azure-cosmos-db.md) per la procedura dettagliata.

* Usare lo strumento di **importazione dei dati di Azure Cosmos DB:Use the Azure Cosmos DB data import tool:** Questa opzione è consigliata per eseguire la migrazione usando macchine virtuali con meno quantità di dati. Per la procedura dettagliata, vedere l'articolo Utilità di [importazione dati.](./import-data.md)

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire test delle prestazioni, vedere l'articolo Test delle prestazioni e scalabilità con il database Cosmos di Azure.To do performance testing, see [Performance and scale testing with Azure Cosmos DB](./performance-testing.md) article.
* Per ottimizzare il codice, vedere l'articolo Suggerimenti sulle prestazioni per Database Cosmos di [Azure.To](./performance-tips-async-java.md) optimize the code, see Performance tips for Azure Cosmos DB article.
* Esplora Java Async V3 SDK, [SDK di riferimento](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub repo.
