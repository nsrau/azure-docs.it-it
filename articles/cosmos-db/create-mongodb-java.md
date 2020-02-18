---
title: "Avvio rapido: Creare un'app Web usando l'API Azure Cosmos DB per MongoDB e Java SDK"
description: Informazioni su come creare un esempio di codice Java che è possibile usare per la connessione e l'esecuzione di query usando l'API Azure Cosmos DB per MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: java
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 35c6944ddcfac1553ffb2c1cc28472f2a56d4515
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061711"
---
# <a name="quickstart-create-a-console-app-with-java-and-the-mongodb-api-in-azure-cosmos-db"></a>Avvio rapido: Creare un'app console con Java e l'API MongoDB in Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

In questo argomento di avvio rapido si apprenderà a creare e gestire un account API di Azure Cosmos DB per MongoDB dal portale di Azure e aggiungere dati usando un'app SDK Java clonata da GitHub. Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale.

## <a name="prerequisites"></a>Prerequisites
- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). In alternativa, è possibile [provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure. È anche possibile usare l'[Emulatore di Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) con la stringa di connessione `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`.
- [Java Development Kit (JDK) versione 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). 
- [Maven](https://maven.apache.org/download.cgi). In alternativa, eseguire `apt-get install maven` per installare Maven.
- [Git](https://git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [mongodb-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="add-a-collection"></a>Aggiungere una raccolta

Assegnare un nome al nuovo database **db** e alla nuova raccolta **coll**.

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)] 

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire un prompt dei comandi, creare una nuova cartella denominata git-samples e quindi chiudere il prompt dei comandi.

    ```bash
    md "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una nuova cartella in cui installare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-getting-started.git
    ```

3. Aprire quindi il codice nell'editor preferito. 

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string). 

Tutti i frammenti di codice seguenti sono tratti dal file *Program.java*.

Questa console usa il [driver Java di MongoDB](https://docs.mongodb.com/ecosystem/drivers/java/). 

* Viene inizializzato DocumentClient.

    ```java
    MongoClientURI uri = new MongoClientURI("FILLME");`

    MongoClient mongoClient = new MongoClient(uri);            
    ```

* Vengono creati un nuovo database e una nuova raccolta.

    ```java
    MongoDatabase database = mongoClient.getDatabase("db");

    MongoCollection<Document> collection = database.getCollection("coll");
    ```

* Vengono inseriti alcuni documenti usando `MongoCollection.insertOne`

    ```java
    Document document = new Document("fruit", "apple")
    collection.insertOne(document);
    ```

* Vengono eseguite alcune query usando `MongoCollection.find`

    ```java
    Document queryResult = collection.find(Filters.eq("fruit", "apple")).first();
    System.out.println(queryResult.toJson());       
    ```

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Nell'account di Azure Cosmos DB selezionare **Avvio rapido**, **Java** e quindi copiare la stringa di connessione negli Appunti.

2. Aprire il file *Program.java*, sostituire l'argomento del costruttore MongoClientURI con la stringa di connessione. L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 
    
## <a name="run-the-console-app"></a>Eseguire l'app console

1. Eseguire `mvn package` in un terminale per installare i moduli npm necessari

2. Eseguire `mvn exec:java -D exec.mainClass=GetStarted.Program` in un terminale per avviare l'applicazione Java.

È ora possibile usare [Robomongo](mongodb-robomongo.md) / [Studio 3T](mongodb-mongochef.md) per modificare e usare i nuovi dati, nonché eseguire query su di essi.

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account API di Azure Cosmos DB per Mongo DB, aggiungere un database e un contenitore usando Esplora dati e aggiungere dati usando un'app console Java. È ora possibile importare dati aggiuntivi nel database Cosmos. 

> [!div class="nextstepaction"]
> [Importare i dati di MongoDB in Azure Cosmos DB](mongodb-migrate.md)
