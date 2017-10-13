---
title: Creare un database di documenti di Azure Cosmos DB con Java | Microsoft Docs | Microsoft Docs
description: Presenta un esempio di codice Java che permette di connettersi all'API DocumentDB di Azure Cosmos DB ed eseguire query su di essa
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 08/02/2017
ms.author: mimig
ms.openlocfilehash: f4bf1992b1f13c61f44695f641b3cb3248b5672b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: Creare un database di documenti con Java e il portale di Azure

Azure Cosmos DB è il servizio di database multimodello distribuito a livello globale di Microsoft. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave/valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questa guida introduttiva illustra come creare un database di documenti con gli strumenti del portale di Azure per Azure Cosmos DB. Illustra anche come creare rapidamente un'app console Java usando l'[API Java DocumentDB](documentdb-sdk-java.md). Le istruzioni di questa guida introduttiva possono essere eseguite in qualsiasi sistema operativo in grado di eseguire Java. Completando questa guida introduttiva si acquisirà familiarità con la creazione e la modifica di risorse database di documenti nell'interfaccia utente o a livello di codice, in base alle proprie preferenze.

## <a name="prerequisites"></a>Prerequisiti

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * In Ubuntu eseguire `apt-get install default-jdk` per installare JDK.
    * Assicurarsi di impostare la variabile di ambiente JAVA_HOME in modo che faccia riferimento alla cartella di installazione di JDK.
* [Scaricare](http://maven.apache.org/download.cgi) e [installare](http://maven.apache.org/install.html) un archivio binario [Maven](http://maven.apache.org/)
    * In Ubuntu è possibile eseguire `apt-get install maven` per installare Maven.
* [Git](https://www.git-scm.com/)
    * In Ubuntu è possibile eseguire `sudo apt-get install git` per installare Git.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Creare un account di database

Prima di poter creare un database di documenti, è necessario creare un account di database SQL (DocumentDB) con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Aggiungere una raccolta

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Aggiungere dati di esempio

È ora possibile aggiungere dati alla nuova raccolta usando Esplora dati.

1. In Esplora dati il nuovo database viene visualizzato nel riquadro Raccolte. Espandere il database **Tasks**, espandere la raccolta **Items**, fare clic su **Documenti** e quindi su **Nuovo documento**. 

   ![Creare nuovi documenti in Esplora dati nel portale di Azure](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-new-document.png)
  
2. Aggiungere ora un documento alla raccolta con la struttura seguente.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Dopo avere aggiunto il codice JSON alla scheda **Documenti**, fare clic su **Salva**.

    ![Copiare i dati JSON e fare clic su Salva in Esplora dati nel portale di Azure](./media/create-documentdb-dotnet/azure-cosmosdb-data-explorer-save-document.png)

4.  Creare e salvare un altro documento inserendo un valore univoco per la proprietà `id` e modificando le altre proprietà come si preferisce. I nuovi documenti possono avere la struttura desiderata, perché Azure Cosmos DB non impone alcuno schema per i dati.

     È ora possibile usare query in Esplora dati per recuperare i dati facendo clic sui pulsanti **Modifica filtro** e **Applica filtro**. Per impostazione predefinita, Esplora dati usa `SELECT * FROM c` per recuperare tutti i documenti della raccolta, ma è possibile usare una[query SQL](documentdb-sql-query.md) diversa, ad esempio `SELECT * FROM c ORDER BY c._ts DESC`, per restituire tutti i documenti in ordine decrescente in base al timestamp. 
 
     È anche possibile usare Esplora dati per creare stored procedure, funzioni definite dall'utente e trigger per eseguire la logica di business sul lato server e aumentare la velocità effettiva. Esplora dati espone tutti i tipi di accesso ai dati a livello di codice predefiniti disponibili nelle API, ma consente anche di accedere facilmente ai dati nel portale di Azure.

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Si può ora passare a usare il codice. Si clonerà un'app per le API DocumentDB da GitHub, si imposterà la stringa di connessione e la si eseguirà. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `CD` per passare a una directory di lavoro.  

2. Eseguire il comando seguente per clonare l'archivio di esempio. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Ecco una breve analisi di ciò che accade nell'app. Aprire il file `Program.java` dalla cartella \src\GetStarted e trovare queste righe di codice che creano le risorse di Azure Cosmos DB. 

* Viene inizializzato `DocumentClient`.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* Viene creato un nuovo database.

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* Viene creata una nuova raccolta.

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* Vengono creati alcuni documenti.

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* Viene eseguita una query SQL su JSON.

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app. In questo modo l'app potrà comunicare con il database ospitato.

1. Nell'account Azure Cosmos DB nel [portale di Azure](http://portal.azure.com/) fare clic su **Chiavi** nel riquadro di spostamento a sinistra e quindi su **Chiavi di lettura/scrittura**. Usare i pulsanti di copia sul lato destro dello schermo per copiare URI e CHIAVE PRIMARIA nel file `Program.java` nel passaggio seguente.

    ![Visualizzazione e copia di una chiave di accesso nel portale di Azure, pannello Chiavi](./media/create-documentdb-dotnet/keys.png)

2. Nel file `Program.java` aperto copiare il valore di URI dal portale (usando il pulsante di copia) e impostarlo come valore dell'endpoint del costruttore DocumentClient in `Program.java`. 

    `"https://FILLME.documents.azure.com"`

4. Copiare quindi il valore di CHIAVE PRIMARIA dal portale e incollarlo sopra "FILLME", come secondo parametro del costruttore DocumentClient. L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 
    
## <a name="run-the-app"></a>Esecuzione dell'app

1. Nella finestra del terminale Git eseguire il comando `cd` per passare alla cartella azure-cosmos-db-documentdb-java-getting-started.

2. Nella finestra del terminale Git digitare `mvn package` per installare i pacchetti Java necessari.

3. Nella finestra del terminale Git eseguire `mvn exec:java -D exec.mainClass=GetStarted.Program` per avviare l'applicazione Java.

    Nella finestra del terminale si riceverà la notifica che indica che il database FamilyDB è stato creato e in cui viene chiesto di premere un tasto per continuare. Premere un tasto per creare il database, quindi passare a Esplora dati e osservare che ora contiene un database FamilyDB. Continuare a premere i tasti per creare la raccolta e i documenti e quindi eseguire una query. Quando viene completato il progetto, le risorse vengono eliminate dall'account. 

    ![Visualizzazione e copia di una chiave di accesso nel portale di Azure, pannello Chiavi](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'app, eliminare tutte le risorse create tramite questa guida di avvio rapido nel portale di Azure eseguendo questi passaggi:

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, un database di documenti e una raccolta usando Esplora dati e come creare eseguire un'app per ottenere lo stesso risultato a livello di codice. È ora possibile importare dati aggiuntivi nell'account Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare dati in Azure Cosmos DB](import-data.md)


