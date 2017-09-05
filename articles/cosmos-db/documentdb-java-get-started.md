---
title: 'Esercitazione su NoSQL: API di DocumentDB per Azure Cosmos DB Java SDK | Microsoft Docs'
description: "Esercitazione su NoSQL che crea un database online e un'applicazione console Java con l'API di DocumentDB per Azure Cosmos DB. Azure DocumentDB è un database NoSQL per JSON."
keywords: esercitazione su nosql, database online, applicazione console java
services: cosmos-db
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 05/22/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 5c4bcda308f001572e1c34e991616fc209250a02
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="nosql-tutorial-build-a-documentdb-api-java-console-application"></a>Esercitazione su NoSQL: Compilare un'applicazione console Java con l'API di DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js per MongoDB](mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Esercitazione su NoSQL per l'API di DocumentDB per Azure Cosmos DB Java SDK. Dopo aver seguito questa esercitazione, si otterrà un'applicazione console che consente di creare e ridefinire le query delle risorse Azure Cosmos DB.

Argomenti trattati:

* Creazione e connessione a un account Azure Cosmos DB
* Configurare una soluzione Visual Studio
* Creazione di un database online
* Creare una raccolta
* Creazione di documenti JSON
* Esecuzione di query sulla raccolta
* Creazione di documenti JSON
* Esecuzione di query sulla raccolta
* Sostituzione di un documento
* Eliminazione di un documento
* Eliminazione del database

Ecco come procedere.

## <a name="prerequisites"></a>Prerequisiti
Assicurarsi che sia disponibile quanto segue:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/). In alternativa, per questa esercitazione è possibile usare l'[emulatore Azure Cosmos DB](local-emulator.md).
* [Git](https://git-scm.com/downloads)
* [Java Development Kit (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passaggio 1: Creare un account di Azure Cosmos DB
Creare prima di tutto un account Azure Cosmos DB. Se è già disponibile un account da usare, è possibile andare direttamente al passaggio [Clonare il progetto GitHub](#GitClone). Se si usa l'emulatore Azure Cosmos DB, seguire la procedura illustrata nell'articolo relativo all'[emulatore Azure Cosmos DB](local-emulator.md) per configurare l'emulatore e proseguire con il passaggio [Clonare il progetto GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Passaggio 2: Clonare il progetto GitHub
Per iniziare, è possibile clonare il repository GitHub per l'[introduzione ad Azure Cosmos DB e Java](https://github.com/Azure-Samples/documentdb-java-getting-started). Eseguire questo comando da una directory locale per recuperare il progetto di esempio in locale.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

La directory contiene un oggetto `pom.xml` per il progetto e una cartella `src` che contiene il codice sorgente Java incluso `Program.java`, che mostra come eseguire semplici operazioni con Azure Cosmos DB, come la creazione di documenti e l'esecuzione di query sui dati all'interno di una raccolta. L'oggetto `pom.xml` include una dipendenza per [DocumentDB Java SDK su Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Passaggio 3: Connettersi a un account Azure Cosmos DB
Tornare al [portale di Azure](https://portal.azure.com) per recuperare l'endpoint e la chiave master primaria. L'endpoint e la chiave primaria di Azure Cosmos DB sono necessari all'applicazione per conoscere la destinazione della connessione e ad Azure Cosmos DB per considerare attendibile la connessione dell'applicazione.

Nel portale di Azure passare all'account Azure Cosmos DB e quindi fare clic su **Chiavi**. Copiare l'URI dal portale e incollarlo in `https://FILLME.documents.azure.com` nel file Program.java. Copiare quindi la CHIAVE PRIMARIA dal portale e incollarla in `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Screenshot del portale di Azure usato nell'esercitazione su NoSQL per creare un'applicazione console Java. Mostra un account Azure Cosmos DB, con l'hub ACTIVE evidenziato, il pulsante CHIAVI evidenziato nel pannello dell'account Azure Cosmos DB e i valori di URI, CHIAVE PRIMARIA e CHIAVE SECONDARIA evidenziati nel pannello Chiavi][keys]

## <a name="step-4-create-a-database"></a>Passaggio 4: Creare un database
È possibile creare un [database](documentdb-resources.md#databases) di Azure Cosmos DB usando il metodo [createDatabase](/java/api/com.microsoft.azure.documentdb._document_client.createdatabase) della classe **DocumentClient**. Un database è il contenitore logico per l'archiviazione di documenti JSON partizionato nelle raccolte.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Passaggio 5: Creare una raccolta
> [!WARNING]
> **createCollection** crea una nuova raccolta con velocità effettiva riservata, che presenta implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

È possibile creare una [raccolta](documentdb-resources.md#collections) usando il metodo [createCollection](/java/api/com.microsoft.azure.documentdb._document_client.createcollection) della classe **DocumentClient**. Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Passaggio 6: Creare documenti JSON
È possibile creare un [documento](documentdb-resources.md#documents) usando il metodo [createDocument](/java/api/com.microsoft.azure.documentdb._document_client.createdocument) della classe **DocumentClient**. I documenti sono contenuto JSON definito dall'utente (arbitrario). Ora è possibile inserire uno o più documenti. Se sono già disponibili dati da archiviare nel database, è possibile usare lo [strumento di migrazione dei dati](import-data.md) di Azure Cosmos DB per importare i dati in un database.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagramma che illustra la relazione gerarchica tra l'account, il database online, la raccolta e i documenti usati nell'esercitazione su NoSQL per creare un'applicazione console Java](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Passaggio 7: Eseguire query sulle risorse di Azure Cosmos DB
Azure Cosmos DB supporta [query complesse](documentdb-sql-query.md) sui documenti JSON archiviati in ogni raccolta.  Il codice di esempio seguente mostra come eseguire query su documenti in Azure Cosmos DB usando la sintassi SQL con il metodo [queryDocuments](/java/api/com.microsoft.azure.documentdb._document_client.querydocuments).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Passaggio 8: Sostituire un documento JSON
Azure Cosmos DB supporta l'aggiornamento di documenti JSON con il metodo [replaceDocument](/java/api/com.microsoft.azure.documentdb._document_client.replacedocument).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Passaggio 9: Eliminare un documento JSON
Analogamente, Azure Cosmos DB supporta l'eliminazione di documenti JSON con il metodo [deleteDocument](/java/api/com.microsoft.azure.documentdb._document_client.deletedocument).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Passaggio 10: Eliminare il database
Se si elimina il database creato, insieme al database vengono rimosse tutte le risorse figlio, come raccolte, documenti e così via.

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Passaggio 11: Eseguire l'intera applicazione console Java
Per eseguire l'applicazione dalla console, passare alla cartella del progetto e compilare usando Maven:
    
    mvn package

L'esecuzione di `mvn package` scarica la libreria di Azure Cosmos DB più recente da Maven e produce `GetStarted-0.0.1-SNAPSHOT.jar`. Eseguire l'app usando quanto segue:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Congratulazioni. L'esercitazione su NoSQL è stata completata ed è stata creata un'applicazione console Java funzionante.

## <a name="next-steps"></a>Passaggi successivi
* Per un'esercitazione su app Web Java, vedere [Creazione di un'applicazione Web Java con Azure Cosmos DB](documentdb-java-application.md).
* Informazioni su come [monitorare un account Azure Cosmos DB](monitor-accounts.md).
* Eseguire query sul set di dati di esempio illustrato nella pagina [Query Playground](https://www.documentdb.com/sql/demo).
* Per altre informazioni sul modello di programmazione, vedere la sezione relativa allo sviluppo nella pagina [Documentazione di Azure Cosmos DB](https://azure.microsoft.com/documentation/services/documentdb/).

[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

