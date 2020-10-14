---
title: 'Avvio rapido: API Cassandra con Python - Azure Cosmos DB'
description: Questa guida introduttiva illustra come usare l'API Apache Cassandra di Azure Cosmos DB per creare un'applicazione di profilo con Python.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 08/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: 06e0d3618e96b44eca60f32a0977e7f03d6f5603
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824627"
---
# <a name="quickstart-build-a-cassandra-app-with-python-sdk-and-azure-cosmos-db"></a>Avvio rapido: Creare un'app Cassandra con Python SDK e Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

In questo argomento di avvio rapido si apprende come creare un account dell'API Cassandra di Azure Cosmos DB e usare un'app Python Cassandra clonata da GitHub per creare un database e contenitore Cassandra. Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). In alternativa, è possibile [provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure.
- [Python 2.7.14+ o 3.4+](https://www.python.org/downloads/).
- [Git](https://git-scm.com/downloads).
- [Driver Python per Apache Cassandra](https://github.com/datastax/python-driver).

## <a name="create-a-database-account"></a>Creare un account di database

Prima di poter creare un database di documenti, è necessario creare un account di Cassandra con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app per le API Cassandra da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-python-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per comprendere in che modo il codice crea le risorse del database, è possibile esaminare i frammenti di codice seguenti. I frammenti di codice provengono tutti dal file *pyquickstart.py*. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string). 

* `cluster` viene inizializzato con le informazioni su `contactPoint` e `port` recuperate dal portale di Azure. `cluster` si connette quindi all'API Cassandra di Azure Cosmos DB tramite il metodo `connect()`. Viene stabilita una connessione autorizzata usando il nome utente, la password e il certificato predefinito oppure un certificato esplicito se ne viene fornito uno con il file config.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="authenticateAndConnect":::

* Viene creato un nuovo keyspace.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createKeyspace":::

* Viene creata una nuova tabella.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="createTable":::

* Le entità chiave/valore vengono inserite.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="insertData":::

* Eseguire una query per ottenere tutti i valori di chiave.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryAllItems":::
    
* Eseguire una query per ottenere un valore di chiave.

  :::code language="python" source="~/cosmosdb-cassandra-python-sample/pyquickstart.py" id="queryByID":::

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app. La stringa di connessione consente all'app di comunicare con il database ospitato.

1. Nell'account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com/) selezionare **Stringa di connessione**. 

1. Usare il pulsante :::image type="icon" source="./media/create-cassandra-python/copy.png"::: sul lato destro della schermata per copiare il primo valore, PUNTO DI CONTATTO. 

   :::image type="content" source="./media/create-cassandra-python/keys.png" alt-text="Visualizzare e copiare i valori di nome utente, password e punto di contatto dal pannello della stringa di connessione del portale di Azure":::

1. Aprire il file *config.py*. 

1. Incollare il valore di PUNTO DI CONTATTO dal portale su `<FILLME>` nella riga 10.

    La riga 10 è ora simile alla seguente: 

    `'contactPoint': 'cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350'`

1. Copiare il valore di NOME UTENTE dal portale e incollarlo su `<FILLME>` nella riga 6.

    La riga 6 è ora simile alla seguente: 

    `'username': 'cosmos-db-quickstart',`
    
1. Copiare il valore di PASSWORD dal portale e incollarlo su `<FILLME>` nella riga 8.

    La riga 8 è ora simile alla seguente:

    `'password' = '2Ggkr662ifxz2Mg==`';`

1. Salvare il file *config.py*.
    
## <a name="use-the-x509-certificate"></a>Usare il certificato X509

1. Scaricare il certificato Baltimore CyberTrust Root in locale da [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Rinominare il file usando l'estensione file *.cer*.

   Il certificato ha il numero di serie `02:00:00:b9` e l'impronta digitale SHA1 `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Aprire *pyquickstart.py* e modificare `path\to\cert` in modo che punti al nuovo certificato.

3. Salvare *pyquickstart.py*.

## <a name="run-the-python-app"></a>Eseguire l'app Python

1. Usare il comando cd nel terminale git per passare alla cartella `azure-cosmos-db-cassandra-python-getting-started`. 

2. Per installare i moduli richiesti, eseguire i comandi seguenti:

    ```python
    python -m pip install cassandra-driver
    python -m pip install prettytable
    python -m pip install requests
    python -m pip install pyopenssl
    ```

2. Usare il comando seguente per avviare l'applicazione Python:

    ```
    python pyquickstart.py
    ```

3. Verificare i risultati previsti dalla riga di comando.

    Premere CTRL+C per interrompere l'esecuzione del programma e chiudere la finestra della console. 

    :::image type="content" source="./media/create-cassandra-python/output.png" alt-text="Visualizzare e copiare i valori di nome utente, password e punto di contatto dal pannello della stringa di connessione del portale di Azure":::
    
4. Nel portale di Azure aprire **Esplora dati** per modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

    :::image type="content" source="./media/create-cassandra-python/data-explorer.png" alt-text="Visualizzare e copiare i valori di nome utente, password e punto di contatto dal pannello della stringa di connessione del portale di Azure":::

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account di Azure Cosmos DB con l'API Cassandra e come eseguire un'app Python Cassandra per creare un database e contenitore Cassandra. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare i dati di Cassandra in Azure Cosmos DB](cassandra-import-data.md)

