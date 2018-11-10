---
title: Eseguire query sui dati da un account dell'API Cassandra di Azure Cosmos DB
description: Questo articolo illustra come eseguire query sui dati utente da un account dell'API Cassandra di Azure Cosmos DB usando un'applicazione Java.
services: cosmos-db
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: a06e7e6159953bfeffa966759d29b91bbcbafd37
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739210"
---
# <a name="tutorial-query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Esercitazione: Eseguire query sui dati da un account dell'API Cassandra di Azure Cosmos DB

Questa esercitazione illustra come eseguire query sui dati utente da un account dell'API Cassandra di Azure Cosmos DB usando un'applicazione Java. L'applicazione Java usa il [driver Java](https://github.com/datastax/java-driver) per eseguire query sui dati utente, tra cui ID utente, nome utente, città dell'utente. 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Eseguire query sui dati dalla tabella di Cassandra
> * Esecuzione dell'app

## <a name="prerequisites"></a>Prerequisiti

* Questo articolo fa parte di un'esercitazione in più parti. Prima di iniziare, assicurarsi di completare i passaggi precedenti per creare account, keyspace e tabella dell'API Cassandra e per [caricare dati di esempio nella tabella](cassandra-api-load-data.md). 

## <a name="query-data"></a>Eseguire query sui dati

Usare la procedura seguente per eseguire query sui dati dall'account dell'API Cassandra:

1. Aprire il file `UserRepository.java` nella cartella `src\main\java\com\azure\cosmosdb\cassandra`. Aggiungere il blocco di codice seguente. Questo codice fornisce tre metodi: 

   * Per eseguire query su tutti gli utenti del database
   * Per eseguire query su un utente specifico filtrato in base all'ID utente
   * Per eliminare una tabella.

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Aprire il file `UserProfile.java` nella cartella `src\main\java\com\azure\cosmosdb\cassandra`. Questa classe contiene il metodo principale che chiama i metodi per l'inserimento dei dati createKeyspace e createTable definiti in precedenza. A questo punto aggiungere il codice seguente che esegue una query per tutti gli utenti o un utente specifico:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Eseguire l'app Java
1. Aprire un prompt dei comandi o una finestra del terminale. Incollare il codice seguente. 

   Questo codice modifica la directory (cd) nel percorso della cartella in cui è stato creato il progetto. Quindi, esegue il comando `mvn clean install` per generare il file `cosmosdb-cassandra-examples.jar` all'interno della cartella di destinazione. Infine, esegue l'applicazione Java.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. A questo punto, nel portale di Azure, aprire **Esplora dati** e verificare che la tabella utente sia eliminata.

## <a name="next-steps"></a>Passaggi successivi

* In questa esercitazione si è appreso come eseguire query sui dati da un account dell'API Cassandra di Azure Cosmos DB. È possibile passare all'articolo successivo:

> [!div class="nextstepaction"]
> [Eseguire la migrazione dei dati nell'account dell'API Cassandra](cassandra-import-data.md)


