---
title: "Esercitazione: Compilare un'app Java con l’SDK Async Java per gestire un account API SQL di Azure Cosmos DB"
description: Questa esercitazione illustra come archiviare e accedere ai dati in un account API SQL di Azure Cosmos DB tramite un'applicazione Async Java.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/15/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Java application with the Async Java SDK to access and manage Azure Cosmos DB resources so that customers can utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: b46914728c684fe4b28cb1325afb1e0b662522ad
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475806"
---
# <a name="tutorial-build-a-java-app-with-the-async-java-sdk-to-manage-data-stored-in-a-sql-api-account"></a>Esercitazione: Compilare un'app Java con l’SDK Async Java per gestire i dati archiviati in un account API SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (Preview)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (Preview)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Gli sviluppatori possono avere applicazioni che usano dati documento NoSQL. È possibile usare un account API SQL in Azure Cosmos DB per archiviare e accedere a tali dati documento. Questa esercitazione illustra come compilare un'applicazione Java con l’SDK Async Java per archiviare e gestire i dati dei documenti. 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Creazione e connessione a un account Azure Cosmos
> * Configurazione della soluzione
> * Creare una raccolta
> * Creazione di documenti JSON
> * Esecuzione di query sulla raccolta

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di disporre delle risorse seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/). 

* [Git](https://git-scm.com/downloads).

* [Java Development Kit (JDK) 8+](https://aka.ms/azure-jdks).

* [Maven](https://maven.apache.org/download.cgi).

## <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Eseguire i seguenti passaggi per creare un account Azure Cosmos:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Clonare il repository GitHub

Clonare il repository GitHub per l'[introduzione ad Azure Cosmos DB e Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Eseguire questo comando da una directory locale per recuperare il progetto di esempio in locale.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

La directory contiene un file `pom.xml` e una cartella `src/main/java/com/microsoft/azure/cosmosdb/sample` contenente il codice sorgente Java, incluso `Main.java`. Il progetto contiene il codice necessario per eseguire operazioni con Azure Cosmos DB, ad esempio la creazione di documenti e le query sui dati all'interno di una raccolta. Il file `pom.xml` include una dipendenza per [Azure Cosmos DB Java SDK su Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Connettersi a un account Azure Cosmos

Tornare al [portale di Azure](https://portal.azure.com) per recuperare l'endpoint e la chiave master primaria. L'endpoint e la chiave primaria di Azure Cosmos DB sono necessari all'applicazione per conoscere la destinazione della connessione e ad Azure Cosmos DB per considerare attendibile la connessione dell'applicazione. Il file `AccountSettings.java` contiene i valori relativi a chiave primaria e URI. 

Nel portale di Azure passare all'account Azure Cosmos e quindi fare clic su **Chiavi**. Copiare l'URI e la CHIAVE PRIMARIA dal portale e incollarli nel file `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Screenshot di acquisizione delle chiavi dal portale][keys]

## <a name="initialize-the-client-object"></a>Inizializzare l'oggetto client

Inizializzare l'oggetto client tramite i valori relativi a chiave primaria e URI host definiti nel file "AccountSettings.java".

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Creare un database

Creare un database di Azure Cosmos DB usando il metodo `createDatabaseIfNotExists()` della classe DocumentClient. Un database è il contenitore logico per l'archiviazione di documenti JSON partizionato nelle raccolte.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>Creare una raccolta

È possibile creare una raccolta usando il metodo `createDocumentCollectionIfNotExists()` della classe DocumentClient. Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata.

> [!WARNING]
> **createCollection** crea una nuova raccolta con velocità effettiva riservata, che presenta implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>Creare documenti JSON

Creare un documento usando il metodo createDocument della classe DocumentClient. I documenti sono contenuto JSON definito dall'utente (arbitrario). Ora è possibile inserire uno o più documenti. Il file "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" definisce i documenti JSON della famiglia. 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>Sottoporre a query le risorse di Azure Cosmos DB

Azure Cosmos DB supporta query avanzate sui documenti JSON archiviati in ogni raccolta. Il codice di esempio seguente mostra come eseguire query su documenti in Azure Cosmos DB usando la sintassi SQL con il metodo `queryDocuments`.

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>Eseguire l'applicazione console Java

Per eseguire l'applicazione dalla console, passare alla cartella del progetto e compilare usando Maven:

```bash
mvn package
```

L'esecuzione di `mvn package` scarica la libreria di Azure Cosmos DB più recente da Maven e produce `GetStarted-0.0.1-SNAPSHOT.jar`. Eseguire l'app usando quanto segue:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

L'esercitazione su NoSQL è stata ora completata ed è stata creata un'applicazione console Java funzionante.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più è possibile eliminare il gruppo di risorse, l'account Azure Cosmos e tutte le risorse correlate. A tale scopo, selezionare il gruppo di risorse per la macchina virtuale, selezionare **Elimina** e quindi confermare il nome del gruppo di risorse da eliminare.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come compilare un'app Java con l’SDK Async Java per gestire i dati API SQL in Azure Cosmos DB. È possibile passare all'articolo successivo:

> [!div class="nextstepaction"]
> [Compilare un'app console Node.js con JavaScript SDK e Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
