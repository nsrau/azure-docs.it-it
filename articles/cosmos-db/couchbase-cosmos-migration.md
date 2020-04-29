---
title: Eseguire la migrazione da CouchBase a Azure Cosmos DB API SQL
description: Istruzioni dettagliate per la migrazione da CouchBase a Azure Cosmos DB API SQL
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77210944"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Eseguire la migrazione da CouchBase a Azure Cosmos DB API SQL

Azure Cosmos DB è un database scalabile, distribuito a livello globale e completamente gestito. Fornisce accesso a bassa latenza garantito ai dati. Per altre informazioni su Azure Cosmos DB, vedere l'articolo relativo alla [Panoramica](introduction.md) . Questo articolo fornisce istruzioni per eseguire la migrazione di applicazioni Java connesse a Couchbase a un account API SQL in Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Differenze nella nomenclatura

Di seguito sono riportate le funzionalità chiave che funzionano in modo diverso in Azure Cosmos DB rispetto a Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Server Couchbase| Account       |
|Secchio           | Database      |
|Secchio           | Contenitore/raccolta |
|Documento JSON    | Elemento/documento |

## <a name="key-differences"></a>Differenze principali

* Azure Cosmos DB dispone di un campo "ID" all'interno del documento, mentre Couchbase ha l'ID come parte del bucket. Il campo "ID" è univoco in tutta la partizione.

* Azure Cosmos DB ridimensionare usando la tecnica di partizionamento o partizionamento orizzontale. Il che significa che suddivide i dati in più partizioni/partizioni. Le partizioni e le partizioni vengono create in base alla proprietà della chiave di partizione fornita. È possibile selezionare la chiave di partizione per ottimizzare le operazioni di lettura e scrittura o anche per la lettura/scrittura ottimizzata. Per altre informazioni, vedere l'articolo relativo al [partizionamento](./partition-data.md) .

* In Azure Cosmos DB non è necessario che la gerarchia di primo livello denoti la raccolta perché il nome della raccolta esiste già. Questa funzionalità rende molto più semplice la struttura JSON. Di seguito è riportato un esempio che mostra le differenze nel modello di dati tra Couchbase e Azure Cosmos DB:

   **Couchbase**: Document ID = "99FF4444"

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

   **Azure Cosmos DB**: fare riferimento a "ID" all'interno del documento, come illustrato di seguito

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
         
## <a name="java-sdk-support"></a>Supporto Java SDK

Azure Cosmos DB dispone degli SDK seguenti per supportare diversi framework Java:

* SDK asincrono
* Spring boot SDK

Le sezioni seguenti descrivono quando usare ognuno di questi SDK. Si consideri un esempio in cui sono disponibili tre tipi di carichi di lavoro:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase come repository di documenti & query personalizzate basate sui dati di Spring

Se il carico di lavoro di cui si sta eseguendo la migrazione si basa sull'SDK basato su Spring boot, è possibile usare i passaggi seguenti:

1. Aggiungere l'elemento padre al file POM. XML:

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. Aggiungere le proprietà al file POM. XML:

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. Aggiungere le dipendenze al file POM. XML:

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. Aggiungere le proprietà dell'applicazione in risorse e specificare quanto segue. Assicurarsi di sostituire i parametri URL, chiave e nome database:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Definire il nome della raccolta nel modello. È anche possibile specificare altre annotazioni. Ad esempio, ID, chiave di partizione per identificarli in modo esplicito:

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

Di seguito sono riportati i frammenti di codice per le operazioni CRUD:

### <a name="insert-and-update-operations"></a>Operazioni di inserimento e aggiornamento

Dove *_repo* è l'oggetto di repository e *doc* è l'oggetto della classe POJO. È possibile usare `.save` per inserire o Upsert (se è stato trovato un documento con l'ID specificato). Nel frammento di codice seguente viene illustrato come inserire o aggiornare un oggetto doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Operazioni di eliminazione

Si consideri il frammento di codice seguente, in cui l'oggetto doc avrà l'ID e la chiave di partizione obbligatori per individuare ed eliminare l'oggetto:

```_repo.delete(doc);```

### <a name="read-operation"></a>Operazione di lettura

È possibile leggere il documento con o senza specificare la chiave di partizione. Se non si specifica la chiave di partizione, viene considerata come una query tra partizioni. Si considerino gli esempi di codice seguenti, il primo eseguirà l'operazione usando il campo ID e chiave di partizione. Nel secondo esempio viene usato un campo normale & senza specificare il campo chiave di partizione.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

A questo punto, è possibile usare l'applicazione con Azure Cosmos DB. L'esempio di codice completo per l'esempio descritto in questo documento è disponibile nel repository GitHub [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) .

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase come repository di documenti & usando query N1QL

Query N1QL è la modalità di definizione delle query in Couchbase.

|Query N1QL | Query di Azure CosmosDB|
|-------------------|-------------------|
|Selezionare META (`TravelDocument`). ID come ID, `TravelDocument`* da `TravelDocument` Where `_type` = "com. XX. XX. XX. xxx. xxx. xxxx" e Country =' India ' e qualsiasi m nei visti soddisfa m. Type = =' multientry ' e m. Country in [' India ', Bhutan '] order by ` Validity` desc limite 25 offset 0   | SELEZIONARE c. ID, c da c JOIN m in c. Country =' India ' WHERE c. _type = "com. XX. XX. XX. xxx. xxx. xxxx" e c. Country =' India ' e m. Type =' multientry ' e m. Country IN (' India ',' Bhutan ') ORDER BY c. valore di validità DESC OFFSET 0 limite 25 |

Nelle query N1QL è possibile notare le modifiche seguenti:

* Non è necessario usare la parola chiave META o fare riferimento al documento di primo livello. È invece possibile creare un riferimento personalizzato al contenitore. In questo esempio è stato considerato come "c" (può trattarsi di qualsiasi elemento). Questo riferimento viene usato come prefisso per tutti i campi di primo livello. Esempio fr, c.id, c. Country e così via.

* Anziché "ANY", ora è possibile eseguire un join in un documento secondario e farvi riferimento con un alias dedicato, ad esempio "m". Dopo aver creato l'alias per un documento secondario, è necessario usare l'alias. Ad esempio m. Country.

* La sequenza di OFFSET è diversa nella query Azure Cosmos DB, per prima cosa è necessario specificare OFFSET e quindi limite. Si consiglia di non utilizzare Spring data SDK se si utilizza il numero massimo di query definite personalizzate poiché può avere un sovraccarico non necessario sul lato client, passando la query a Azure Cosmos DB. È invece disponibile un SDK di Java asincrono diretto, che può essere usato in modo molto efficiente in questo caso.

### <a name="read-operation"></a>Operazione di lettura

Usare Async Java SDK con i passaggi seguenti:

1. Configurare la dipendenza seguente nel file POM. XML:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Creare un oggetto connessione per Azure Cosmos DB usando il `ConnectionBuilder` metodo, come illustrato nell'esempio seguente. Assicurarsi di inserire questa dichiarazione nel fagiolo, in modo che il codice seguente venga eseguito una sola volta:

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

1. Per eseguire la query, è necessario eseguire il frammento di codice seguente:

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

Ora, con l'aiuto del metodo precedente, è possibile passare più query ed eseguire senza problemi. Se si ha la necessità di eseguire una query di grandi dimensioni, che può essere suddivisa in più query, provare il frammento di codice seguente anziché quello precedente:

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

Con il codice precedente, è possibile eseguire query in parallelo e aumentare le esecuzioni distribuite da ottimizzare. È anche possibile eseguire le operazioni di inserimento e aggiornamento:

### <a name="insert-operation"></a>Operazioni di inserimento

Per inserire il documento, eseguire il codice seguente:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Sottoscrivere quindi mono come:

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

### <a name="upsert-operation"></a>Operazione Upsert

Per l'operazione Upsert è necessario specificare il documento che deve essere aggiornato. Per recuperare il documento completo, è possibile usare il frammento indicato nell'operazione di lettura dell'intestazione, quindi modificare il campo o i campi necessari. Il seguente frammento di codice Upsert il documento:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Sottoscrivere quindi mono. Vedere il frammento di sottoscrizione mono nell'operazione di inserimento.

### <a name="delete-operation"></a>Operazioni di eliminazione

Il frammento di codice seguente esegue l'operazione di eliminazione:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Sottoscrivere quindi mono, fare riferimento al frammento di sottoscrizione mono nell'operazione di inserimento. L'esempio di codice completo è disponibile nel repository GitHub [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) .

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase come coppia chiave/valore

Si tratta di un tipo semplice di carico di lavoro in cui è possibile eseguire ricerche anziché query. Usare la procedura seguente per le coppie chiave/valore:

1. Provare a usare "/ID" come chiave primaria, in modo da garantire che sia possibile eseguire l'operazione di ricerca direttamente nella partizione specifica. Creare una raccolta e specificare "/ID" come chiave di partizione.

1. Disattivare completamente l'indicizzazione. Poiché si eseguiranno operazioni di ricerca, non si verifica alcun sovraccarico di indicizzazione. Per disattivare l'indicizzazione, accedere portale di Azure, goto Azure Cosmos DB account. Aprire il **Esplora dati**, selezionare il **database** e il **contenitore**. Aprire la scheda **scala & impostazioni** e selezionare i **criteri di indicizzazione**. Attualmente i criteri di indicizzazione hanno un aspetto simile al seguente:
    
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

   Sostituire i criteri di indicizzazione sopra indicati con i criteri seguenti:

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. Utilizzare il seguente frammento di codice per creare l'oggetto connessione. Oggetto Connection (da inserire @Bean o rendere statico):

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

A questo punto è possibile eseguire le operazioni CRUD come indicato di seguito:

### <a name="read-operation"></a>Operazione di lettura

Per leggere l'elemento, usare il frammento di codice seguente:

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

Per inserire un elemento, è possibile eseguire il codice seguente:

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

Sottoscrivere quindi mono come:

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

### <a name="upsert-operation"></a>Operazione Upsert

Per aggiornare il valore di un elemento, fare riferimento al frammento di codice seguente:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Sottoscrivere quindi mono, fare riferimento al frammento di sottoscrizione mono nell'operazione di inserimento.

### <a name="delete-operation"></a>Operazioni di eliminazione

Usare il frammento di codice seguente per eseguire l'operazione di eliminazione:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Sottoscrivere quindi mono, fare riferimento al frammento di sottoscrizione mono nell'operazione di inserimento. L'esempio di codice completo è disponibile nel repository GitHub [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) .

## <a name="data-migration"></a>Migrazione dei dati

Esistono due modi per eseguire la migrazione dei dati.

* **Usare Azure Data Factory:** Si tratta del metodo più consigliato per eseguire la migrazione dei dati. Configurare l'origine come Couchbase e sink come Azure Cosmos DB API SQL, vedere l'articolo relativo al [connettore di data factory Azure Cosmos DB](../data-factory/connector-azure-cosmos-db.md) per i passaggi dettagliati.

* **Usare lo strumento di importazione dati Azure Cosmos DB:** Questa opzione è consigliata per eseguire la migrazione usando VM con una quantità di dati inferiore. Per i passaggi dettagliati, vedere l'articolo sull' [utilità di importazione dati](./import-data.md) .

## <a name="next-steps"></a>Passaggi successivi

* Per eseguire il test delle prestazioni, vedere [test delle prestazioni e della scalabilità con Azure Cosmos DB](./performance-testing.md) articolo.
* Per ottimizzare il codice, vedere [suggerimenti sulle prestazioni per Azure Cosmos DB](./performance-tips-async-java.md) articolo.
* Esplora Java Async V3 SDK, repository GitHub di [riferimento SDK](https://github.com/Azure/azure-cosmosdb-java/tree/v3) .
