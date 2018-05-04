---
title: "Azure Cosmos DB: Creare un'app Web con Python e l'API SQL | Microsoft Docs"
description: Presenta un esempio di codice Python che permette di connettersi all'API SQL di Azure Cosmos DB ed eseguire query su di essa
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 51c11be2-af6d-425f-a86a-39cbfe61da29
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 04/13/2018
ms.author: sngun
ms.openlocfilehash: 2e439b260ae2964aeab33c100db3f62e0bd06f33
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="azure-cosmos-db-build-a-sql-api-app-with-python-and-the-azure-portal"></a>Azure Cosmos DB: Creare un'app per le API SQL con Python e il portale di Azure

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare ed eseguire rapidamente query su database di documenti, coppie chiave-valore e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

Questa guida introduttiva mostra come creare un account [API SQL](sql-api-introduction.md), un database di documenti e una raccolta di Azure Cosmos DB usando il portale di Azure. Quindi, si creerà ed eseguirà un'app console basata sull'[API Python SQL](sql-api-sdk-python.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)][!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="prerequisites"></a>prerequisiti

* [Python 3.6](https://www.python.org/downloads/) con \<percorso di installazione\>\Python36 e \<percorso di installazione>\Python36\Scripts aggiunti al PERCORSO. 
* [Visual Studio Code](https://code.visualstudio.com/)
* [Estensione di Python per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python#overview)

## <a name="create-a-database-account"></a>Creare un account di database

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Aggiungere una raccolta

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Aggiungere dati di esempio

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Eseguire query sui dati

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app per le API SQL da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-python-getting-started.git
    ```  
    
## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string). 

Tutti i frammenti di codice seguenti sono tratti dal file DocumentDBGetStarted.py.

* Viene inizializzato DocumentClient.

    ```python
    # Initialize the Python client
    client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})
    ```

* Viene creato un nuovo database.

    ```python
    # Create a database
    db = client.CreateDatabase({ 'id': config['SQL_DATABASE'] })
    ```

* Viene creata una nuova raccolta.

    ```python
    # Create collection options
    options = {
        'offerEnableRUPerMinuteThroughput': True,
        'offerVersion': "V2",
        'offerThroughput': 400
    }

    # Create a collection
    collection = client.CreateCollection(db['_self'], { 'id': config['SQL_COLLECTION'] }, options)
    ```

* Vengono creati alcuni documenti.

    ```python
    # Create some documents
    document1 = client.CreateDocument(collection['_self'],
        { 
            'id': 'server1',
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': 'some' 
        })
    ```

* Viene eseguita una query con SQL

    ```python
    # Query them in SQL
    query = { 'query': 'SELECT * FROM server s' }    
            
    options = {} 
    options['enableCrossPartitionQuery'] = True
    options['maxItemCount'] = 2

    result_iterable = client.QueryDocuments(collection['_self'], query, options)
    results = list(result_iterable);

    print(results)
    ```

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app.

1. Nell'account Azure Cosmos DB nel [portale di Azure](http://portal.azure.com/) nel riquadro di spostamento a sinistra fare clic su **Chiavi**. Usare i pulsanti di copia sul lato destro dello schermo per copiare l'**URI** e la **chiave primaria** nel file DocumentDBGetStarted.py nel passaggio seguente.

    ![Visualizzazione e copia di una chiave di accesso nel portale di Azure, pannello Chiavi](./media/create-sql-api-dotnet/keys.png)

2. Aprire il file C:\git-samples\azure-cosmos-db-documentdb-python-getting-startedDocumentDBGetStarted.py in Visual Studio Code. 

3. Copiare il valore di **URI** dal portale (usando il pulsante di copia) e impostarlo come valore della chiave di **endpoint** in DocumentDBGetStarted.py. 

    `'ENDPOINT': 'https://FILLME.documents.azure.com',`

4. Copiare quindi il valore di **CHIAVE PRIMARIA** dal portale e impostarlo come valore di **config.MASTERKEY** in DocumentDBGetStarted.py. L'app è stata aggiornata con tutte le informazioni necessarie per comunicare con Azure Cosmos DB. 

    `'MASTERKEY': 'FILLME',`

5. Salvare il file DocumentDBGetStarted.py.
    
## <a name="run-the-app"></a>Esecuzione dell'app

1. In Visual Studio Code selezionare **Visualizza**>**Riquadro comandi**. 

2. Al prompt dei comandi immettere **Python: Select Interpreter** e quindi selezionare la versione di Python da usare.

    Il piè di pagina in Visual Studio Code viene aggiornato per indicare l'interprete selezionato. 

3. Selezionare **Visualizza** > **Terminale integrato** per aprire il terminale integrato di Visual Studio Code.

4. Nella finestra del terminale integrato verificare di essere in esecuzione nella cartella azure-cosmos-db-documentdb-python-getting-started. In caso contrario, eseguire il comando seguente per passare alla cartella di esempio. 

    ```
    cd "C:\git-samples\azure-cosmos-db-documentdb-python-getting-started"`
    ```

5. Eseguire il comando seguente per installare il pacchetto pydocumentdb. 

    ```
    pip3 install pydocumentdb
    ```

    Se si riceve un errore di accesso negato durante il tentativo di installazione di pydocumentdb, sarà necessario [eseguire VS Code come amministratore](https://stackoverflow.com/questions/37700536/visual-studio-code-terminal-how-to-run-a-command-with-administrator-rights).

6. Eseguire il comando seguente per eseguire l'esempio e creare e archiviare nuovi documenti in Azure Cosmos DB.

    ```
    python DocumentDBGetStarted.py
    ```

7. Per verificare che i nuovi documenti siano stati creati e salvati, nel portale di Azure selezionare **Esplora dati**, espandere **coll**, espandere **Documenti** e quindi selezionare il documento **server1**. Il contenuto del documento server1 corrisponde al contenuto restituito nella finestra del terminale integrato. 

    ![Visualizzare i nuovi documenti nel portale di Azure](./media/create-sql-api-python/azure-cosmos-db-confirm-documents.png)

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare una raccolta con Esplora dati e come eseguire un'app. È ora possibile importare dati aggiuntivi nell'account Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare dati in Azure Cosmos DB per l'API SQL](import-data.md)


