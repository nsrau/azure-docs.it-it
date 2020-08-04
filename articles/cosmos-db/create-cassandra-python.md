---
title: 'Avvio rapido: API Cassandra con Python - Azure Cosmos DB'
description: Questa guida introduttiva illustra come usare l'API Apache Cassandra di Azure Cosmos DB per creare un'applicazione di profilo con Python.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: python
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: tracking-python
ms.openlocfilehash: a388cde60e0d19aed1af9620f82b7baa6d5b08fc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87067315"
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

* I valori di nome utente e password sono stati impostati usando la pagina della stringa di connessione nel portale di Azure. `path\to\cert` consente di specificare il percorso di un certificato X509. 

   ```python
    ssl_opts = {
            'ca_certs': 'path\to\cert',
            'ssl_version': ssl.PROTOCOL_TLSv1_2
            }
    auth_provider = PlainTextAuthProvider( username=cfg.config['username'], password=cfg.config['password'])
    cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider, ssl_options=ssl_opts)
    session = cluster.connect()
   
   ```

* `cluster` viene inizializzato con le informazioni di contactPoint. Il valore di contactPoint viene recuperato dal portale di Azure.

    ```python
   cluster = Cluster([cfg.config['contactPoint']], port = cfg.config['port'], auth_provider=auth_provider)
    ```

* `cluster` si connette all'API Cassandra di Azure Cosmos DB.

    ```python
    session = cluster.connect()
    ```

* Viene creato un nuovo keyspace.

    ```python
   session.execute('CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }')
    ```

* Viene creata una nuova tabella.

   ```
   session.execute('CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)');
   ```

* Le entità chiave/valore vengono inserite.

    ```Python
    insert_data = session.prepare("INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)")
    session.execute(insert_data, [1,'Lybkov','Seattle'])
    session.execute(insert_data, [2,'Doniv','Dubai'])
    session.execute(insert_data, [3,'Keviv','Chennai'])
    session.execute(insert_data, [4,'Ehtevs','Pune'])
    session.execute(insert_data, [5,'Dnivog','Belgaum'])
    ....
    
    ```

* Eseguire una query per ottenere tutti i valori di chiave.

    ```Python
    rows = session.execute('SELECT * FROM uprofile.user')
    ```  
    
* Eseguire una query per ottenere un valore di chiave.

    ```Python
    
    rows = session.execute('SELECT * FROM uprofile.user where user_id=1')
    ```  

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

   Il certificato ha il numero di serie `02:00:00:b9` e l'impronta digitale SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

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

    :::image type="content" source="./media/create-cassandra-python/output.png" alt-text="Visualizzare e verificare l'output":::
    
4. Nel portale di Azure aprire **Esplora dati** per modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

    :::image type="content" source="./media/create-cassandra-python/data-explorer.png" alt-text="Visualizzare i dati in Esplora dati":::

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account di Azure Cosmos DB con l'API Cassandra e come eseguire un'app Python Cassandra per creare un database e contenitore Cassandra. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare i dati di Cassandra in Azure Cosmos DB](cassandra-import-data.md)

