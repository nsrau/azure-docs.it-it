---
title: Creare un account API Cassandra in Azure Cosmos DB, un database e una tabella usando un’applicazione Java
description: Questo articolo mostra come creare un account API Cassandra, aggiungere un database (detto anche keyspace) e una tabella a tale account usando un’applicazione Java.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
services: cosmos-db
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: b5e3d87e026b65a602b7bdf2e52365d13b21f62f
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166832"
---
# <a name="create-an-azure-cosmos-db-cassandra-api-account-database-and-a-table-by-using-a-java-application"></a>Creare un account API Cassandra in Azure Cosmos DB, un database e una tabella usando un'applicazione Java

Questa esercitazione descrive come usare un'applicazione Java per creare un account API Cassandra in Azure Cosmos DB, come aggiungere un database (noto anche come keyspace) e aggiungere una tabella. L’applicazione Java usa il [driver Java](https://github.com/datastax/java-driver) per creare un database utente che contiene dettagli come ID utente, nome utente, città utente.  

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un account di database Cassandra
> * Ottenere la stringa di connessione dell’account
> * Creare il progetto e le dipendenze Maven
> * Aggiungere un database e una tabella
> * Esecuzione dell'app

## <a name="prerequisites"></a>Prerequisiti 

* Se non si ha una sottoscrizione di Azure, creare un  [account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  prima di iniziare. In alternativa è possibile [provare Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione Azure, gratuitamente e senza impegno. 

* Ottenere la versione più recente di [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 

* [Scaricare](http://maven.apache.org/download.cgi) e [installare](http://maven.apache.org/install.html) un archivio binario [Maven](http://maven.apache.org/) 
  - In Ubuntu è possibile eseguire `apt-get install maven` per installare Maven. 

## <a name="create-a-database-account"></a>Creare un account di database 

1. Accedere al  [portale di Azure](https://portal.azure.com/). 

2. Selezionare **Crea una risorsa** > **Database** > **Azure Cosmos DB**. 

3. Nel riquadro **Nuovo account** , impostare i parametri per il nuovo account Azure Cosmos DB. 

   |Impostazione   |Valore consigliato  |DESCRIZIONE  |
   |---------|---------|---------|
   |ID   |   Immettere un nome univoco    | Immettere un nome univoco per identificare l'account Azure Cosmos DB. <br/><br/>Poiché cassandra.cosmosdb.azure.azure.com è accodato all'ID fornito per creare il proprio punto di contatto, usare un ID univoco ma facilmente identificabile.         |
   |API    |  Cassandra   |  L'API determina il tipo di account da creare. <br/> Selezionare **Cassandra**, poiché in questo articolo si creerà un database ad ampia colonna che può essere interrogato usando la sintassi CQL.  |
   |Sottoscrizione    |  Sottoscrizione in uso        |  Selezionare la sottoscrizione di Azure da usare per l'account Azure Cosmos DB.        |
   |Gruppo di risorse   | Immettere un nome    |  Selezionare **Crea Nuovo**, quindi immettere il nome di un nuovo gruppo di risorse per il proprio account. Per semplicità si può usare lo stesso nome usato come ID.    |
   |Località    |  Selezionare l'area più vicina agli utenti    |  Selezionare la posizione geografica in cui ospitare l'account Azure Cosmos DB. Usare la località più vicina agli utenti per offrire loro la massima velocità di accesso ai dati.    |

   ![Creare un account con il portale](./media/create-cassandra-api-account-java/create-account.png)

4. Successivamente, selezionare **Crea**. <br/>La creazione dell'account richiede alcuni minuti, Dopo la creazione della risorsa, è possibile visualizzare la notifica la **distribuzione ha avuto esito positivo** nell'angolo a destra del portale.

## <a name="get-the-connection-details-of-your-account"></a>Ottenere i dettagli della connessione dell'account  

Ottenere le informazioni sulla stringa di connessione dal portale di Azure e copiarlo nel file di configurazione di Java. Questo consente all'app di comunicare con il database ospitato. 

1. Nel  [portale di Azure](http://portal.azure.com/), passare all'account Azure Cosmos DB. 

2. Aprire il riquadro **Stringa di connessione**.  

3. Copiare i valori **PUNTO DI CONTATTO**, **PORTA**, **NOME UTENTE** e **PASSWORD PRIMARIA** da usare nei passaggi successivi.

## <a name="create-maven-project-dependencies-and-utility-classes"></a>Creare il progetto, le dipendenze e le classi di utilità Maven 

Il progetto di esempio Java che è usato in questo articolo è ospitato in GitHub. È possibile scaricarlo dal repository [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started). 

Dopo aver scaricato i file, aggiornare le informazioni sulla stringa di connessione all'interno del file `java-examples\src\main\resources\config.properties` ed eseguirlo.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

In alternativa, è possibile compilare l'esempio da zero.  

1. Dal terminale o dal prompt dei comandi, creare un nuovo progetto Maven denominato Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Trova la cartella `cassandra-demo`. Usando un editor di testo, aprire il file `pom.xml` che è stato generato. 

   Aggiungere le dipendenze di Cassandra e compilare i plug-in richiesti dal progetto come mostrato nel file [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml).  

3. Nella cartella `cassandra-demo\src\main`, creare una nuova cartella denominata `resources`.  Nella cartella di risorse, aggiungere i file config.properties e log4j.properties:

   - Il file [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) archivia l'endpoint di connessione di Azure Cosmos DB API Cassandra e i valori chiave. 
   
   - Il file [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) definisce il livello di registrazione richiesto quando si interagisce con API Cassandra.  

4. Quindi, passare alla cartella `src/main/java/com/azure/cosmosdb/cassandra/`. All'interno della cartella di cassandra, creare un'altra cartella denominata `utils`. La nuova cartella archivia le classi di utilità necessarie per connettersi all'account API Cassandra. 

   Aggiungere la classe [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) per creare il cluster e per aprire e chiudere le sessioni di Cassandra. Il cluster si connette all’API Cassandra di Azure Cosmos DB e restituisce una sessione di accesso. Usare la classe [Configurazioni](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) per leggere le informazioni sulla stringa di connessione dal file config.properties. 

5. L'esempio Java crea un database con informazioni sull'utente, ad esempio nome utente, ID utente, città utente. È necessario definire i metodi di get e set per accedere ai dettagli dell'utente nella funzione principale.
 
   Creare una classe [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) nella cartella `src/main/java/com/azure/cosmosdb/cassandra/` con i metodi di get e set. 

## <a name="add-a-database-and-a-table"></a>Aggiungere un database e una tabella  

Questa sezione descrive come aggiungere un database (keyspace) e una tabella, usando Cassandra Query Language (CQL). Per altre informazioni sulla sintassi CQL per questi comandi, fare riferimento alla sintassi di query della [creazione di keyspace](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateKeyspace.html) e della [creazione di tabella](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateTable.html#cqlCreateTable). 

1. Nella cartella `src\main\java\com\azure\cosmosdb\cassandra`, creare una nuova cartella denominata `repository`. 

2. A questo punto, creare la classe Java `UserRepository` per aggiungervi il codice seguente: 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. Trovare la cartella `src\main\java\com\azure\cosmosdb\cassandra` e creare una nuova sottocartella denominata `examples`.

4. A questo punto, creare la classe Java `UserProfile`. Questa classe contiene il metodo principale che chiama i metodi creareKeyspace e creareTabelle definiti in precedenza: 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>Esecuzione dell'app 

1. Aprire un prompt dei comandi o una finestra del terminale. Incollare il codice seguente. 

   Questo codice modifica la directory (cd) nel percorso della cartella in cui è stato creato il progetto. Quindi, esegue il comando `mvn clean install` per generare il file `cosmosdb-cassandra-examples.jar` all'interno della cartella di destinazione. Infine, esegue l'applicazione Java.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   Nella finestra del terminale vengono visualizzate notifiche per il completamento della creazione di keyspace e tabella. 
   
2. A questo punto, nel portale di Azure, aprire **Esplora dati** per confermare che sono stati creati il keyspace e la tabella.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, è stato spiegato come creare un account API Cassandra di Azure Cosmos DB , un database e una tabella usando un'applicazione Java. È possibile passare all'articolo successivo:

> [!div class="nextstepaction"]
> [Caricare i dati campione nella tabella di API Cassandra](cassandra-api-load-data.md).
