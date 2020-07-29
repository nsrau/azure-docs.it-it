---
title: Eseguire la migrazione da CouchBase all'API SQL di Azure Cosmos DB
description: Istruzioni dettagliate per la migrazione da CouchBase all'API SQL di Azure Cosmos DB
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: b0c9ef99e4cbb0683273d613d3a85e7f6455a40d
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87366722"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Eseguire la migrazione da CouchBase all'API SQL di Azure Cosmos DB

Azure Cosmos DB è un database scalabile, distribuito a livello globale e completamente gestito. Offre un accesso ai dati con bassa latenza garantita. Per altre informazioni su Azure Cosmos DB, vedere l'articolo [Panoramica](introduction.md). Questo articolo illustra come eseguire la migrazione di applicazioni Java connesse a Couchbase a un account API SQL in Azure Cosmos DB.

## <a name="differences-in-nomenclature"></a>Differenze nella nomenclatura

Di seguito sono riportate le funzionalità chiave che funzionano in modo diverso in Azure Cosmos DB rispetto a Couchbase:

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|Server Couchbase| Account       |
|Bucket           | Database      |
|Bucket           | Contenitore/raccolta |
|Documento JSON    | Elemento/documento |

## <a name="key-differences"></a>Differenze principali

* Azure Cosmos DB dispone di un campo "ID" all'interno del documento, mentre in Couchbase l'ID è incluso nel bucket. Il campo "ID" è univoco nell'intera partizione.

* Azure Cosmos DB esegue il ridimensionamento mediante la tecnica di partizionamento o partizionamento orizzontale, suddividendo quindi i dati in più partizioni/partizioni di database. Queste partizioni/partizioni di database vengono create in base alla proprietà della chiave di partizione fornita. È possibile selezionare la chiave di partizione per ottimizzare le operazioni di lettura e scrittura o anche di lettura/scrittura ottimizzata. Per altre informazioni, vedere l'articolo sul [partizionamento](./partition-data.md).

* In Azure Cosmos DB non è necessario che la gerarchia di primo livello denoti la raccolta perché il nome della raccolta esiste già. Questa funzionalità rende molto più semplice la struttura JSON. Di seguito è riportato un esempio che mostra le differenze nel modello di dati tra Couchbase e Azure Cosmos DB:

   **Couchbase**: ID documento = "99FF4444"

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

   **Azure Cosmos DB**: Vedere "ID" all'interno del documento, come illustrato di seguito

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
         
## <a name="java-sdk-support"></a>Supporto SDK per Java

Azure Cosmos DB dispone degli SDK seguenti per supportare diversi framework Java:

* SDK asincrono
* SDK Spring Boot

Le sezioni seguenti descrivono quando usare ognuno di questi SDK. Si consideri un esempio in cui sono disponibili tre tipi di carichi di lavoro:

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>Couchbase come repository di documenti e query personalizzate basate su dati Spring

Se il carico di lavoro di cui si sta eseguendo la migrazione si basa sull'SDK basato su Spring Boot, è possibile procedere come segue:

1. Aggiungere l'elemento padre al file POM.xml:

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

1. Aggiungere proprietà dell'applicazione nelle risorse e specificare quanto segue. Assicurarsi di sostituire i parametri URL, chiave e nome del database:

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. Definire il nome della raccolta nel modello. È anche possibile specificare altre annotazioni. Ad esempio, ID e chiave di partizione per identificarli in modo esplicito:

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

Dove *_repo* è l'oggetto del repository e *doc* è l'oggetto della classe POJO. È possibile usare `.save` per inserire o eseguire l'upsert (se è stato trovato un documento con l'ID specificato). Nel frammento di codice seguente viene illustrato come inserire o aggiornare un oggetto Doc:

```_repo.save(doc);```

### <a name="delete-operation"></a>Operazioni di eliminazione

Si consideri il frammento di codice seguente, in cui l'oggetto Doc avrà l'ID e la chiave di partizione obbligatori per individuare ed eliminare l'oggetto:

```_repo.delete(doc);```

### <a name="read-operation"></a>Operazione di lettura

È possibile leggere il documento specificando o meno la chiave di partizione. Se non si specifica la chiave di partizione, la query verrà trattata come query tra partizioni. Si considerino gli esempi di codice seguenti: il primo esegue l'operazione usando i campi ID e Chiave di partizione. Nel secondo esempio viene usato un campo normale, senza specificare il campo Chiave di partizione.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

A questo punto, è possibile usare l'applicazione con Azure Cosmos DB. Il codice di esempio completo per l'esempio descritto in questo documento è disponibile nel repository [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) di GitHub.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>Couchbase come repository di documenti e uso di query N1QL

Le query N1QL permettono di definire le query in Couchbase.

|Query N1QL | Query di Azure CosmosDB|
|-------------------|-------------------|
|SELECT META(`TravelDocument`).id AS id, `TravelDocument`.* FROM `TravelDocument` WHERE `_type` = "com.xx.xx.xx.xxx.xxx.xxxx " and country = 'India’ and ANY m in Visas SATISFIES m.type == 'Multi-Entry' and m.Country IN ['India', Bhutan’] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0   | SELECT c.id,c FROM c JOIN m in  c.country=’India’ WHERE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" and c.country = 'India' and m.type = 'Multi-Entry' and m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

Nelle query N1QL è possibile notare le modifiche seguenti:

* Non è necessario usare la parola chiave META o fare riferimento al documento di primo livello. È invece possibile creare un riferimento personalizzato al contenitore. In questo esempio, è stato considerato come "c" (può trattarsi di qualsiasi elemento). Questo riferimento viene usato come prefisso per tutti i campi di primo livello. Ad esempio c.id, c.paese e così via.

* Invece di "ANY", è ora possibile creare un join in un sottodocumento e riferirsi ad esso con un alias dedicato come "m". Dopo aver creato l'alias per un documento secondario, è necessario usarlo. Ad esempio, m.Paese.

* La sequenza di OFFSET è diversa nella query di Azure Cosmos DB. Innanzitutto, è necessario specificare i valori OFFSET e quindi LIMIT. Si consiglia di non usare l'SDK Spring Data se si usa il numero massimo di query definite personalizzate, poiché ciò potrebbe comportare un sovraccarico non necessario sul lato del client, passando la query ad Azure Cosmos DB. È invece disponibile un SDK di Java asincrono diretto, che può essere usato in modo molto efficiente in questo caso.

### <a name="read-operation"></a>Operazione di lettura

Usare l'SDK asincrono per Java con i passaggi seguenti:

1. Configurare la dipendenza seguente nel file POM.xml:

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. Creare un oggetto Connection per Azure Cosmos DB usando il metodo `ConnectionBuilder`, come illustrato nell'esempio seguente. Assicurarsi di inserire questa dichiarazione nel bean, in modo che il codice seguente venga eseguito una sola volta:

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

A questo punto, con l'aiuto del metodo precedente, è possibile passare più query ed eseguirle senza problemi. Se si ha la necessità di eseguire una query di grandi dimensioni che può essere suddivisa in più query, provare il frammento di codice seguente anziché quello precedente:

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

Eseguire quindi la sottoscrizione a Mono come:

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

Per l'operazione di upsert è necessario specificare il documento da aggiornare. Per recuperare il documento completo, è possibile usare il frammento indicato nell'operazione di lettura dell'intestazione, per poi modificare i campi necessari. Il seguente frammento di codice esegue l'upsert del documento:

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
Eseguire quindi la sottoscrizione a Mono. Vedere il frammento di sottoscrizione Mono nell'operazione di inserimento.

### <a name="delete-operation"></a>Operazioni di eliminazione

Il frammento di codice seguente esegue l'operazione di eliminazione:

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Eseguire quindi la sottoscrizione a Mono; vedere il frammento di sottoscrizione Mono nell'operazione di inserimento. L'esempio di codice completo è disponibile nel repository [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) di GitHub.

## <a name="couchbase-as-a-keyvalue-pair"></a>Couchbase come coppia chiave/valore

Si tratta di un tipo semplice di carico di lavoro in cui è possibile eseguire ricerche anziché query. Per le coppie chiave/valore, usare la procedura seguente:

1. Provare a usare "/ID" come chiave primaria, in modo da garantire che sia possibile eseguire l'operazione di ricerca direttamente nella partizione specifica. Creare una raccolta e specificare "/ID" come chiave di partizione.

1. Disattivare completamente l'indicizzazione. Poiché si eseguiranno operazioni di ricerca, non si verifica alcun sovraccarico di indicizzazione. Per disattivare l'indicizzazione, accedere al portale di Azure e passare all'account di Azure Cosmos DB. Aprire **Esplora dati**, selezionare il **Database** e il **Contenitore**. Aprire la scheda **Scalabilità e impostazioni** e selezionare **Criteri di indicizzazione**. Attualmente, i criteri di indicizzazione hanno un aspetto simile al seguente:
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   Sostituire i criteri di indicizzazione sopra indicati con i criteri seguenti:

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. Per creare un oggetto Connection, usare il frammento di codice seguente. Oggetto Connection (da inserire in @Bean o rendere statico):

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

A questo punto è possibile eseguire le operazioni CRUD, come indicato di seguito:

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

Eseguire quindi la sottoscrizione a Mono come:

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
Eseguire quindi la sottoscrizione a Mono; vedere il frammento di sottoscrizione Mono nell'operazione di inserimento.

### <a name="delete-operation"></a>Operazioni di eliminazione

Usare il frammento di codice seguente per eseguire l'operazione di eliminazione:

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

Eseguire quindi la sottoscrizione a Mono; vedere il frammento di sottoscrizione Mono nell'operazione di inserimento. L'esempio di codice completo è disponibile nel repository [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) di GitHub.

## <a name="data-migration"></a>Migrazione dei dati

La migrazione dei dati può essere eseguita in due modi.

* **Usare Azure Data Factory:** Si tratta del metodo più consigliato per eseguire la migrazione dei dati. Configurare l'origine come Couchbase ed eseguire il sink come API SQL di Azure Cosmos DB. Per la procedura dettagliata, vedere l'articolo [Connettore Data Factory di Cosmos DB](../data-factory/connector-azure-cosmos-db.md).

* **Usare lo strumento di importazione dati di Azure Cosmos DB:** Questa opzione è consigliata per eseguire la migrazione usando macchine virtuali con una quantità di dati inferiore. Per i passaggi dettagliati, vedere l'articolo relativo all'[Utilità di importazione dati](./import-data.md).

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](./performance-testing.md).
* Per ottimizzare il codice, vedere l'articolo [Suggerimenti sulle prestazioni per Azure Cosmos DB](./performance-tips-async-java.md).
* Esplorare il repository di GitHub [riferimenti SDK](https://github.com/Azure/azure-cosmosdb-java/tree/v3) per l'SDK asincrono per Java V3.
