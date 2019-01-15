---
title: "Esercitazione: Caricare dati di esempio in una tabella di API Cassandra in Azure Cosmos DB con un'applicazione Java"
description: Questa esercitazione illustra come caricare i dati utente di esempio in una tabella di API Cassandra in Azure Cosmos DB usando un'applicazione Java.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: govindk
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Java application to load data to a Cassandra API table in Azure Cosmos DB so that customers can store and manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 68baffea86ce5c877217ec05d477288b71830a42
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037430"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Esercitazione: Caricare dati di esempio in una tabella di API Cassandra in Azure Cosmos DB

Gli sviluppatori possono avere applicazioni che usano coppie chiave/valore. È possibile usare un account dell'API Cassandra in Azure Cosmos DB per archiviare e gestire i dati chiave/valore. Questa esercitazione illustra come caricare i dati utente di esempio in una tabella in un account di API Cassandra in Azure Cosmos DB usando un'applicazione Java. L'applicazione Java usa il [driver Java](https://github.com/datastax/java-driver) e carica i dati utente, tra cui ID utente, nome utente e città dell'utente. 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Caricare i dati in una tabella di Cassandra
> * Esecuzione dell'app

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Questo articolo fa parte di un'esercitazione in più parti. Prima di iniziare a leggere questo documento, [creare l'account, il keyspace e la tabella dell'API Cassandra](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Caricare i dati nella tabella

Usare la procedura seguente per caricare i dati nella tabella dell'API Cassandra:

1. Aprire il file "UserRepository.java" nella cartella "src\main\java\com\azure\cosmosdb\cassandra" e aggiungere il codice per inserire i campi user_id, user_name e user_bcity nella tabella:

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. Aprire il file "UserProfile.java" nella cartella "src\main\java\com\azure\cosmosdb\cassandra". Questa classe contiene il metodo principale che chiama i metodi createKeyspace e createTable definiti in precedenza. A questo punto aggiungere il codice seguente per inserire alcuni dati di esempio nella tabella di API Cassandra.

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>Esecuzione dell'app

Aprire un prompt dei comandi o una finestra del terminale e modificare il percorso della cartella con il percorso in cui è stato creato il progetto. Eseguire il comando "mvn clean install" per generare il file cosmosdb-cassandra-examples.jar all'interno della cartella di destinazione ed eseguire l'applicazione. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

È ora possibile aprire Esplora dati nel portale di Azure per confermare che le informazioni dell'utente siano state aggiunte alla tabella.
    
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come caricare i dati utente di esempio in un account dell'API Cassandra in Azure Cosmos DB. È possibile passare all'articolo successivo:

> [!div class="nextstepaction"]
> [Query data from the Cassandra API account](cassandra-api-query-data.md) (Eseguire query sui dati dall'account di API Cassandra)
