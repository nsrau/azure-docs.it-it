---
title: Caricare dati di esempio in una tabella di API Cassandra di Azure Cosmos DB tramite un'applicazione Java | Microsoft Docs
description: Questo articolo illustra come caricare i dati utente di esempio in una tabella in un account di API Cassandra di Azure Cosmos DB usando un'applicazione Java.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d659004e94c51f173bc1e9ae42fd9c62ae45912f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972539"
---
# <a name="load-sample-data-into-an-azure-cosmos-db-cassandra-api-table"></a>Caricare dati di esempio in una tabella di API Cassandra di Azure Cosmos DB

Questa esercitazione illustra come caricare i dati utente di esempio in una tabella in un account di API Cassandra di Azure Cosmos DB usando un'applicazione Java. L'applicazione Java usa il [driver Java Datastax](https://github.com/datastax/java-driver) per caricare i dati utente, tra cui ID utente, nome utente, città dell'utente. 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Caricare i dati nella tabella di Cassandra
> * Esecuzione dell'app

## <a name="prerequisites"></a>Prerequisiti

* Questo articolo fa parte di un'esercitazione in più parti. Prima di iniziare a leggere questo documento, [creare l'account, il keyspace e la tabella di API Cassandra](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Caricare i dati nella tabella

Aprire il file UserRepository.java nella cartella src\main\java\com\azure\cosmosdb\cassandra e aggiungere il codice per inserire i campi user_id, user_name e user_bcity nella tabella:

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
 
Aprire il file UserProfile.java nella cartella src\main\java\com\azure\cosmosdb\cassandra. Questa classe contiene il metodo principale che chiama i metodi createKeyspace e createTable definiti in precedenza. A questo punto aggiungere il codice seguente per inserire alcuni dati di esempio nella tabella di API Cassandra.

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

Aprire il prompt dei comandi o la finestra del terminale e modificare il percorso della cartella con il percorso in cui è stato creato il progetto. Eseguire il comando "mvn clean install" per generare il file cosmosdb-cassandra-examples.jar all'interno della cartella di destinazione ed eseguire l'applicazione. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

È ora possibile aprire Esplora dati nel portale di Azure per confermare che le informazioni dell'utente siano state aggiunte alla tabella.
    
## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come caricare i dati utente di esempio in un account di API Cassandra di Azure Cosmos DB. È possibile passare all'articolo successivo:

> [!div class="nextstepaction"]
> [Query data from the Cassandra API account](cassandra-api-query-data.md) (Eseguire query sui dati dall'account di API Cassandra)
