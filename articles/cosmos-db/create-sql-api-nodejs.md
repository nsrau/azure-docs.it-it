---
title: "Azure Cosmos DB: Compilare un'app Node.js con JavaScript SDK per gestire i dati API SQL di Azure Cosmos DB"
description: Presenta un esempio di codice Node.js che permette di connettersi all'API SQL di Azure Cosmos DB e di eseguire query su di essa
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: dech
ms.openlocfilehash: ec6f676933f5c275c49fa4da1af0e1c3084dab89
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754573"
---
# <a name="quickstart-build-a-nodejs-app-using-azure-cosmos-db-sql-api-account"></a>Guida introduttiva: Compilare un'app Node.js usando un account API SQL di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (Preview)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave-valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questa guida introduttiva illustra come creare un account dell'[API SQL](sql-api-introduction.md) di Azure Cosmos DB, un database di documenti e un contenitore usando il portale di Azure. Quindi, si creerà ed eseguirà un'app console basata su [SQL JavaScript SDK](sql-api-sdk-node.md). Questa guida introduttiva usa la versione 2.0 di [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Eseguire anche queste operazioni:
    * [Node.js](https://nodejs.org/en/) versione v6.0.0 o successiva
    * [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Aggiungere un contenitore

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Aggiungere dati di esempio

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Eseguire query sui dati

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app per le API SQL da GitHub, impostare la stringa di connessione ed eseguirla.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string). 

Si noti che, se si ha familiarità con la versione precedente di JavaScript SDK, ci si potrebbe aspettare di vedere termini come "raccolta" e "documento". Poiché Azure Cosmos DB supporta [più modelli di API](https://docs.microsoft.com/azure/cosmos-db/introduction), nella versione 2.0+ di JavaScript SDK vengono usati i termini generici "contenitore", che può essere una raccolta, un grafo o una tabella, ed "elemento" per descrivere il contenuto del contenitore.

Tutti i frammenti di codice seguenti sono tratti dal file **app.js**.

* Viene inizializzato `CosmosClient`.

    ```javascript
    const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
    ```

* Viene creato un nuovo database.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* Viene creato un nuovo contenitore (raccolta).

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Viene creato un elemento (documento).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* Viene eseguita una query SQL su JSON.

    ```javascript
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
    ```    

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Nell'account Azure Cosmos nel [portale di Azure](https://portal.azure.com/) fare clic su **Chiavi** nel riquadro di spostamento sinistro e quindi su **Chiavi di lettura/scrittura**. Usare i pulsanti di copia sul lato destro dello schermo per copiare l'URI e la chiave primaria nel file `config.js` nel passaggio seguente.

    ![Visualizzazione e copia di una chiave di accesso nel portale di Azure, pannello Chiavi](./media/create-sql-api-dotnet/keys.png)

2. Aprire il file `config.js`. 

3. Copiare il valore di URI dal portale (usando il pulsante di copia) e impostarlo come valore della chiave di endpoint in `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Copiare quindi il valore di CHIAVE PRIMARIA dal portale e impostarlo come valore di `config.primaryKey` in `config.js`. L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 

    `config.primaryKey = "FILLME"`
    
## <a name="run-the-app"></a>Esecuzione dell'app
1. Eseguire `npm install` in un terminale per installare i moduli npm necessari

2. Eseguire `node app.js` in un terminale per avviare l'applicazione Node.js.

È ora possibile tornare a Esplora dati e visualizzare, modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare un account Azure Cosmos, come creare un contenitore con Esplora dati e come eseguire un'app. È ora possibile importare dati aggiuntivi nell'account Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare dati in Azure Cosmos DB](import-data.md)


