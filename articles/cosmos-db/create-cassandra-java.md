---
title: Creare un'app Java con l'API Cassandra di Azure Cosmos DB
description: Questa guida introduttiva illustra come usare l'API Cassandra di Azure Cosmos DB per creare un'applicazione di profilo con il portale di Azure e Java
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 0136dce793fea1672b68062480420e5a05c75253
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85118373"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v3-driver"></a>Avvio rapido: Creare un'app Java per gestire i dati dell'API Cassandra di Azure Cosmos DB (driver v3)

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

In questo argomento di avvio rapido si crea un account dell'API Cassandra di Azure Cosmos DB e si usa un'app Java Cassandra clonata da GitHub per creare un database e contenitore Cassandra con i [driver Apache Cassandra v3.x](https://github.com/datastax/java-driver/tree/3.x) per Java. Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). In alternativa, è possibile [provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure.
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Indirizzare la variabile di ambiente `JAVA_HOME` alla cartella di installazione di JDK.
- Un [archivio binario Maven](https://maven.apache.org/download.cgi). In Ubuntu eseguire `apt-get install maven` per installare Maven.
- [Git](https://www.git-scm.com/downloads). In Ubuntu eseguire `sudo apt-get install git` per installare Git.

## <a name="create-a-database-account"></a>Creare un account di database

Prima di poter creare un database di documenti, è necessario creare un account di Cassandra con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Si può ora passare a usare il codice. Clonare un'app Cassandra da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire un prompt dei comandi. Creare una nuova cartella denominata `git-samples`, quindi chiudere il prompt dei comandi.

    ```bash
    md "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una nuova cartella in cui installare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per comprendere in che modo il codice crea le risorse del database, è possibile esaminare i frammenti di codice seguenti. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string). Questi frammenti di codice sono tutti tratti dal file *src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java*.  

* Vengono impostate le opzioni per host, porta, nome utente, password e TLS/SSL di Cassandra. Le informazioni per la stringa di connessione derivano dalla pagina della stringa di connessione nel portale di Azure.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* Il `cluster` si connette all'API Cassandra di Azure Cosmos DB e restituisce una sessione per l'accesso.

    ```java
    return cluster.connect();
    ```

I frammenti di codice seguenti provengono dal file *src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java*.

* Creare un nuovo keyspace.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Creare una nuova tabella.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Inserire le entità utente usando un oggetto istruzione preparata.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Eseguire una query per ottenere tutte le informazioni utente.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Eseguire una query per ottenere le informazioni su un singolo utente.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app. I dettagli della stringa di connessione consentono all'app di comunicare con il database ospitato.

1. Nell'account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com/) selezionare **Stringa di connessione**. 

    :::image type="content" source="./media/create-cassandra-java/copy-username-connection-string-azure-portal.png" alt-text="Visualizzare e copiare un nome utente dalla pagina Stringa di connessione del portale di Azure":::

2. Usare il ![Pulsante Copia](./media/create-cassandra-java/copy-button-azure-portal.png) sul lato destro della schermata per copiare il valore PUNTO DI CONTATTO.

3. Aprire il file *config.properties* dalla cartella *C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources*. 

3. Incollare il valore di PUNTO DI CONTATTO dal portale su `<Cassandra endpoint host>` nella riga 2.

    La riga 2 di *config.properties* dovrebbe ora essere simile alla seguente 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Tornare al portale e copiare il valore di NOME UTENTE. Incollare il valore di NOME UTENTE dal portale su `<cassandra endpoint username>` nella riga 4.

    La riga 4 di *config.properties* dovrebbe ora essere simile alla seguente 

    `cassandra_username=cosmos-db-quickstart`

4. Tornare al portale e copiare il valore di PASSWORD. Incollare il valore di PASSWORD dal portale su `<cassandra endpoint password>` nella riga 5.

    La riga 5 di *config.properties* dovrebbe ora essere simile alla seguente 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Nella riga 6, se si vuole usare un certificato TLS/SSL specifico, sostituire `<SSL key store file location>` con il percorso del certificato TLS/SSL. Se non viene fornito un valore, viene usato il certificato JDK installato in <JAVA_HOME>/jre/lib/security/cacerts. 

6. Se la riga 6 è stata modificata per usare un certificato TLS/ specifico, aggiornare la riga 7 in modo da usare la password per tale certificato. 

7. Salvare il file *config.properties*.

## <a name="run-the-java-app"></a>Eseguire l'app Java

1. Nella finestra del terminale Git passare con il comando `cd` alla cartella `azure-cosmosdb-cassandra-java-getting-started`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started"
    ```

2. Nella finestra del terminale Git usare il comando seguente per generare il file `cosmosdb-cassandra-examples.jar`.

    ```git
    mvn clean install
    ```

3. Nella finestra del terminale Git eseguire il comando seguente per avviare l'applicazione Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    Nella finestra del terminale vengono visualizzate notifiche per il completamento della creazione di keyspace e tabella. Vengono quindi selezionati e restituiti tutti gli utenti nella tabella, viene visualizzato l'output e infine viene selezionata una riga in base all'ID e ne viene visualizzato il valore.  

    Premere CTRL+C per interrompere l'esecuzione del programma e chiudere la finestra della console.

4. Nel portale di Azure aprire **Esplora dati** per modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

    :::image type="content" source="./media/create-cassandra-java/view-data-explorer-java-app.png" alt-text="Visualizzare i dati in Esplora dati - Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account di Azure Cosmos DB con l'API Cassandra e come eseguire un'app Java Cassandra per creare un database e contenitore Cassandra. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare i dati di Cassandra in Azure Cosmos DB](cassandra-import-data.md)
