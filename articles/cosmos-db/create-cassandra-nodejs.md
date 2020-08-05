---
title: 'Avvio rapido: API Cassandra con Node.js - Azure Cosmos DB'
description: Questa guida introduttiva illustra come usare l'API Cassandra di Azure Cosmos DB per creare un'applicazione di profilo con Node.js
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 1fa481911be8eb91db498350e57e2ba42e4aedb5
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421009"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Avvio rapido: Creare un'app Cassandra con Node.js SDK e Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

In questo argomento di avvio rapido si apprende come creare un account dell'API Cassandra di Azure Cosmos DB e usare un'app Node.js Cassandra clonata da GitHub per creare un database e contenitore Cassandra. Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]In alternativa, è possibile [provare gratuitamente Microsoft Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure e senza impegno.

È anche necessario:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) 0.10.29 o versione successiva
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>Creare un account di database

Prima di poter creare un database di documenti, è necessario creare un account di Cassandra con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app per le API Cassandra da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire un prompt dei comandi. Creare una nuova cartella denominata `git-samples`, quindi chiudere il prompt dei comandi.

    ```bash
    md "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio Git Bash. Usare il comando `cd` per passare alla nuova cartella e installare l'app di esempio.

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per comprendere in che modo il codice crea le risorse del database, è possibile esaminare i frammenti di codice seguenti. I frammenti di codice provengono tutti dal file `uprofile.js` nella cartella `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string). 

* I valori di nome utente e password sono stati impostati usando la pagina della stringa di connessione nel portale di Azure. `path\to\cert` consente di specificare il percorso di un certificato X509. 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* `client` viene inizializzato con le informazioni di contactPoint. Il valore di contactPoint viene recuperato dal portale di Azure.

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* `client` si connette all'API Cassandra di Azure Cosmos DB.

    ```javascript
    client.connect(next);
    ```

* Viene creato un nuovo keyspace.

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Viene creata una nuova tabella.

   ```javascript
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Le entità chiave/valore vengono inserite.

    ```javascript
        function insert(next) {
            console.log("\insert");
            const arr = ['INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (1, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (2, \'JiriK\', \'Toronto\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (3, \'IvanH\', \'Mumbai\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (4, \'IvanH\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (5, \'IvanaV\', \'Belgaum\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (6, \'LiliyaB\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (7, \'JindrichH\', \'Buenos Aires\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (8, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (9, \'JozefM\', \'Seattle\')'];
            arr.forEach(element => {
            client.execute(element);
            });
            next();
        },
    ```

* Eseguire una query per ottenere tutti i valori di chiave.

    ```javascript
        function selectAll(next) {
            console.log("\Select ALL");
            var query = 'SELECT * FROM uprofile.user';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        },
    ```  
    
* Eseguire una query per ottenere un valore di chiave.

    ```javascript
        function selectById(next) {
            console.log("\Getting by id");
            var query = 'SELECT * FROM uprofile.user where user_id=1';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        }
    ```  

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app. La stringa di connessione consente all'app di comunicare con il database ospitato.

1. Nell'account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com/) selezionare **Stringa di connessione**. 

1. Usare il pulsante :::image type="icon" source="./media/create-cassandra-nodejs/copy.png"::: sul lato destro della schermata per copiare il primo valore, PUNTO DI CONTATTO.

    :::image type="content" source="./media/create-cassandra-nodejs/keys.png" alt-text="Visualizzare e copiare i valori di PUNTO DI CONTATTO, NOME UTENTE e PASSWORD dalla pagina della stringa di connessione del portale di Azure":::

1. Aprire il file `config.js` . 

1. Incollare il valore di PUNTO DI CONTATTO dal portale su `<FillMEIN>` nella riga 4.

    La riga 4 è ora simile alla seguente: 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

1. Copiare il valore di NOME UTENTE dal portale e incollarlo su `<FillMEIN>` nella riga 2.

    La riga 2 è ora simile alla seguente: 

    `config.username = 'cosmos-db-quickstart';`
    
1. Copiare il valore di PASSWORD dal portale e incollarlo su `<FillMEIN>` nella riga 3.

    La riga 3 è ora simile alla seguente:

    `config.password = '2Ggkr662ifxz2Mg==';`

1. Salvare il file.`config.js`
    
## <a name="use-the-x509-certificate"></a>Usare il certificato X509

1. Scaricare il certificato Baltimore CyberTrust Root in locale da [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Rinominare il file usando l'estensione file `.cer`.

   Il certificato ha il numero di serie `02:00:00:b9` e l'impronta digitale SHA1 `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Aprire `uprofile.js` e cambiare `path\to\cert` in modo che punti al nuovo certificato.

3. Salvare `uprofile.js`.

> [!NOTE]
> Se nei passaggi successivi si verifica un errore relativo al certificato, in caso di esecuzione su un computer Windows assicurarsi di aver seguito questo processo per la conversione corretta di un file con estensione crt nel formato Microsoft con estensione cer.
> 
> Fare doppio clic sul file con estensione crt per aprirlo nella visualizzazione Certificato. 
>
> :::image type="content" source="./media/create-cassandra-nodejs/crtcer1.gif" alt-text="Visualizzare e verificare l'output":::
>
> Fare clic su Avanti in Esportazione guidata certificati. Selezionare Codificato Base 64 X.509 (.CER) e quindi Avanti.
>
> :::image type="content" source="./media/create-cassandra-nodejs/crtcer2.gif" alt-text="Visualizzare e verificare l'output":::
>
> Selezionare Sfoglia per individuare una destinazione e digitare un nome file.
> Selezionare Avanti e quindi Operazione completata.
>
> Sarà ora disponibile un file con estensione cer in formato corretto. Verificare che il percorso in `uprofile.js` punti a questo file.

## <a name="run-the-nodejs-app"></a>Eseguire l'app Node.js

1. Nella finestra del terminale Git assicurarsi di trovarsi nella directory dell'esempio clonata in precedenza:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

2. Eseguire `npm install` per installare i moduli npm necessari.

3. Eseguire `node uprofile.js` per avviare l'applicazione Node.js.

4. Verificare i risultati previsti dalla riga di comando.

    :::image type="content" source="./media/create-cassandra-nodejs/output.png" alt-text="Visualizzare e verificare l'output":::

    Premere CTRL+C per interrompere l'esecuzione del programma e chiudere la finestra della console. 

5. Nel portale di Azure aprire **Esplora dati** per modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

    :::image type="content" source="./media/create-cassandra-nodejs/data-explorer.png" alt-text="Visualizzare i dati in Esplora dati"::: 

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account di Azure Cosmos DB con l'API Cassandra e come eseguire un'app Node.js Cassandra per creare un database e contenitore Cassandra. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare i dati di Cassandra in Azure Cosmos DB](cassandra-import-data.md)