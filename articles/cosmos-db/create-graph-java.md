---
title: Creare un'applicazione Java Azure Cosmos DB tramite l'API Graph | Microsoft Docs
description: Presenta un esempio di codice Java che permette di connettersi ai dati di un grafo ed eseguire query su di essi in Azure Cosmos DB tramite Gremlin.
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/14/2017
ms.author: denlee
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 8eac406c6ef96d7ae8dd5f4931c7d16edb723be8
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="azure-cosmos-db-build-a-java-application-using-the-graph-api"></a>Azure Cosmos DB: Creare un'applicazione Java tramite l'API Graph

Azure Cosmos DB è il servizio di database multimodello distribuito a livello globale di Microsoft. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave/valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questa guida di avvio rapido mostra come creare un account, un database e un grafo di Azure Cosmos DB per l'API Graph (anteprima) tramite il portale di Azure. Quindi, si creerà ed eseguirà un'app console tramite il driver OSS [Gremlin Java](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver).  

## <a name="prerequisites"></a>Prerequisiti

* Prima di poter eseguire questo esempio, è necessario soddisfare i prerequisiti seguenti:
   * JDK 1.7 + (eseguire `apt-get install default-jdk` se JDK non è disponibile) e impostare le variabili di ambiente come `JAVA_HOME`
   * Maven (eseguire `apt-get install maven` se Maven non è disponibile)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Aggiungere un grafo

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app per le API Graph (anteprima) da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una directory di lavoro.  

2. Eseguire il comando seguente per clonare l'archivio di esempio. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Ecco una breve analisi di ciò che accade nell'app. Aprire il file `Program.java` e notare queste righe di codice. 

* Viene inizializzato `Client` di Gremlin dalla configurazione in `src/remote.yaml`.

    ```java
    Cluster cluster = Cluster.build(new File("src/remote.yaml")).create();
    
    Client client = cluster.connect();
    ```

* Viene eseguita una serie di passaggi di Gremlin tramite il metodo `client.submit`.

    ```java
    ResultSet results = client.submit("g.V()");

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```
## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

1. Aprire il file src/remote.yaml. 

3. Immettere le configurazioni per *Hosts*, *Port*, *Username*, *Password*, *ConnectionPool* e *Serializer* nel file src/remote.yaml:

    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    Hosts|[***.graphs.azure.com]|Vedere lo screenshot di seguito. Si tratta del valore URI Gremlin disponibile nella pagina Panoramica del portale di Azure, tra parentesi quadre, senza la parte finale :443/.<br><br>Questo valore può anche essere recuperato dalla scheda Chiavi, usando il valore dell'URI senza https://, sostituendo documents con graphs e rimuovendo la parte :443/ finale.
    Porta|443|Impostare su 443.
    Nome utente|*Nome utente*|Risorsa nel formato `/dbs/<db>/colls/<coll>`, dove `<db>` è il nome del database e `<coll>` è il nome della raccolta.
    Password|*Chiave master primaria*|Vedere il secondo screenshot di seguito. Si tratta della chiave primaria, che può essere recuperata dalla pagina Chiavi del portale di Azure nella casella Chiave primaria. Per copiare il valore, usare il pulsante di copia a sinistra della casella.
    ConnectionPool|{enableSsl: true}|Impostazione del pool di connessioni per SSL.
    Serializer|{ className: org.apache.tinkerpop.gremlin.<br>driver.ser.GraphSONMessageSerializerV1d0,<br> config: { serializeResultToString: true }}|Impostare questo valore ed eliminare qualsiasi interruzione di riga `\n` quando si incolla il valore.

    Per il valore Hosts, copiare il valore **URI Gremlin** dalla pagina **Panoramica**: ![Visualizzare e copiare il valore URI Gremlin nella pagina Panoramica del portale di Azure](./media/create-graph-java/gremlin-uri.png)

    Per il valore Password, copiare la **chiave primaria** dalla pagina **Chiavi**: ![Visualizzare e copiare la chiave primaria nella pagina Chiavi del portale di Azure](./media/create-graph-java/keys.png)

## <a name="run-the-console-app"></a>Eseguire l'app console

1. Eseguire `mvn package` in un terminale per installare i pacchetti Java necessari.

2. Eseguire `mvn exec:java -D exec.mainClass=GetStarted.Program` in un terminale per avviare l'applicazione Java.

È ora possibile tornare a Esplora dati e visualizzare, modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

## <a name="browse-using-the-data-explorer"></a>Esplorare i dati con Esplora dati

È ora possibile tornare a Esplora dati nel portale di Azure per esplorare i nuovi dati del grafo ed eseguire query su di essi.

* In Esplora dati il nuovo database viene visualizzato nel riquadro Raccolte. Espandere **graphdb**, **graphcoll** e quindi fare clic su **Graph** (Grafo).

    I dati generati dall'app di esempio vengono visualizzati nel riquadro Graphs (Grafi).

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'app, eliminare tutte le risorse create tramite questa guida di avvio rapido nel portale di Azure eseguendo questi passaggi: 

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare un grafo con Esplora dati e come eseguire un'app. È ora possibile creare query più complesse e implementare la potente logica di attraversamento dei grafi usando Gremlin. 

> [!div class="nextstepaction"]
> [Eseguire query con Gremlin](tutorial-query-graph.md)


