---
title: Creare un&quot;applicazione Node.js Azure Cosmos DB tramite l&quot;API Graph | Microsoft Docs
description: Presenta un esempio di codice Node.js che permette di connettersi ad Azure Cosmos DB ed eseguire query sul servizio
services: cosmosdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmosdb
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/13/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: d8a6a183d1acd7a06683ec2e402bd866cb5195f4
ms.contentlocale: it-it
ms.lasthandoff: 05/15/2017


---
# <a name="azure-cosmos-db-build-a-nodejs-application-using-the-graph-api"></a>Azure Cosmos DB: Creare un'applicazione Node.js tramite l'API Graph

Azure Cosmos DB è il servizio di database multimodello distribuito a livello globale di Microsoft. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave/valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questa guida di avvio rapido mostra come creare un account, un database e un grafo di Azure Cosmos DB per l'API Graph (anteprima) tramite il portale di Azure. Quindi, si creerà ed eseguirà un'app console tramite il driver OSS [Gremlin Node.js](https://aka.ms/gremlin-node).  

## <a name="prerequisites"></a>Prerequisiti

* Prima di poter eseguire questo esempio, è necessario soddisfare i prerequisiti seguenti:
    * [Node.js](https://nodejs.org/en/) 0.10.29 o versione successiva
    * [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmosdb-create-dbaccount-graph](../../includes/cosmosdb-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Aggiungere un grafo

[!INCLUDE [cosmosdb-create-graph](../../includes/cosmosdb-create-graph.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app per le API Graph da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire una finestra del terminale Git, ad esempio Git Bash, ed eseguire il comando `cd` per passare a una directory di lavoro.  

2. Eseguire il comando seguente per clonare l'archivio di esempio. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started.git
    ```

3. Aprire quindi il file della soluzione in Visual Studio. 

## <a name="review-the-code"></a>Esaminare il codice

Ecco una breve analisi di ciò che accade nell'app. Aprire il file `app.js` e notare queste righe di codice.

* Viene creato il client di Gremlin.

    ```nodejs
    const client = Gremlin.createClient(
        443, 
        "https://<fillme>.graphs.azure.com", 
        { 
            "session": false, 
            "ssl": true, 
            "user": "/dbs/<db>/colls/<coll>",
            "password": "<authKey>"
        });
    ```

* Viene eseguita una serie di passaggi di Gremlin tramite il metodo `client.execute`.

    ```nodejs
    console.log('Running Count'); 
    client.execute("g.V().count()", { }, (err, results) => {
        if (err) return console.error(err);
        console.log(JSON.stringify(results));
        console.log();
    });
    ```

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Nell'account Azure Cosmos DB nel [portale di Azure](http://portal.azure.com/) fare clic su **Chiavi** nel riquadro di spostamento a sinistra e quindi su **Chiavi di lettura/scrittura**. Usare i pulsanti di copia sul lato destro dello schermo per copiare l'URI e la chiave primaria nel file `app.js` nel passaggio seguente.

    ![Visualizzazione e copia di una chiave di accesso nel portale di Azure, pannello Chiavi](./media/create-documentdb-dotnet/keys.png)

2. Copiare il valore dell'URI dal portale (usando il pulsante di copia) e impostarlo come valore della chiave config.endpoint in config.js.

    `config.endpoint = "GRAPHENDPOINT";`

3. Sostituire la parte "documents.azure.com" dell'URI con "graphs.azure.com".

4. Copiare quindi il valore di CHIAVE PRIMARIA dal portale e impostarlo come valore di config.primaryKey in config.js. L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 

    `config.primaryKey = "PRIMARYKEY";`

## <a name="run-the-console-app"></a>Eseguire l'app console

1. Aprire una finestra del terminale e usare `cd` per passare alla directory di installazione del file package.json incluso nel progetto.  

2. Eseguire `npm install gremlin` per installare i moduli npm necessari.

3. Sostituire il contenuto della cartella `node_modules\gremlin` con il codice sorgente del [fork Cosmos DB Gremlin](https://github.com/CosmosDB/gremlin-javascript), che include il supporto per SSL e SASL (necessari per Azure Cosmos DB), ma che al momento non è supportato dal driver (temporaneamente, finché le modifiche non verranno accettate nel driver).

4. Eseguire `node app.js` in un terminale per avviare l'applicazione Node.js.

È ora possibile tornare a Esplora dati e visualizzare, modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

## <a name="browse-using-the-data-explorer"></a>Esplorare i dati con Esplora dati

È ora possibile tornare a Esplora dati nel portale di Azure per esplorare i nuovi dati del grafo ed eseguire query su di essi.

* In Esplora dati il nuovo database viene visualizzato nel riquadro Raccolte. Espandere **graphdb**, **graphcoll** e quindi fare clic su **Graph** (Grafo).

    I dati generati dall'app di esempio vengono visualizzati nel riquadro Graphs (Grafi).

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmosdb-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare l'app, eliminare tutte le risorse create tramite questa guida di avvio rapido nel portale di Azure eseguendo questi passaggi: 

1. Scegliere **Gruppi di risorse** dal menu a sinistra del portale di Azure e quindi fare clic sul nome della risorsa creata. 
2. Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare il nome della risorsa da eliminare nella casella di testo e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare un grafo con Esplora dati e come eseguire un'app. È ora possibile creare query più complesse e implementare la potente logica di attraversamento dei grafi usando Gremlin. 

> [!div class="nextstepaction"]
> [Eseguire query con Gremlin](tutorial-query-graph.md)
