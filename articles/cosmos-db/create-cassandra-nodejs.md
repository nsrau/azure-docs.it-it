---
title: 'Guida introduttiva: API Cassandra con Node.js - Azure Cosmos DB | Microsoft Docs'
description: Questa guida introduttiva illustra come usare l'API Cassandra di Azure Cosmos DB per creare un'applicazione di profilo con Node.js
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4732e57d-32ed-40e2-b148-a8df4ff2630d
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/15/2017
ms.author: govindk
ms.openlocfilehash: 1ce764a3395b0ddb9e78f1247fd55fabbeecb04e
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-and-azure-cosmos-db"></a>Guida introduttiva: Creare un'app Cassandra con Node.js e Azure Cosmos DB

Questa guida introduttiva mostra come usare Node.js e l'[API Cassandra](cassandra-introduction.md) di Azure Cosmos DB per creare un'app di profilo clonando un esempio di GitHub. Questa guida introduttiva illustra anche come creare un account Azure Cosmos DB usando il portale di Azure basato sul Web.

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. 

## <a name="prerequisites"></a>Prerequisiti

* Accesso al programma di anteprima dell'API Cassandra di Azure Cosmos DB. Se non è ancora stato richiesto l'accesso, [iscriversi adesso](https://aka.ms/cosmosdb-cassandra-signup).
* [Node.js](https://nodejs.org/en/) 0.10.29 o versione successiva
* [Git](http://git-scm.com/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]In alternativa, è possibile [provare gratuitamente Microsoft Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure e senza impegno.


## <a name="create-a-database-account"></a>Creare un account di database

Prima di poter creare un database di documenti, è necessario creare un account di Cassandra con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Clonare ora un'app per le API Cassandra da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

1. Aprire una finestra del terminale Git, ad esempio git bash, ed eseguire il comando `cd` per passare a una cartella in cui installare l'app di esempio. 

    ```bash
    cd "C:\git-samples"
    ```

2. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. I frammenti di codice provengono tutti dal file `uprofile.js` che si trova nella cartella C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-string). 

* Il nome utente e la password vengono impostati nella pagina della stringa di connessione nel portale di Azure. `path\to\cert' consente di specificare il percorso di un certificato X509. 

   ```nodejs
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* `client` viene inizializzato con le informazioni di contactPoint. Il valore di contactPoint viene recuperato dal portale di Azure.

    ```nodejs
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* `client` si connette all'API Cassandra di Azure Cosmos DB.

    ```nodejs
    client.connect(next);
    ```

* Viene creato un nuovo keyspace.

    ```nodejs
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Viene creata una nuova tabella.

   ```nodejs
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Le entità chiave/valore vengono inserite.

    ```nodejs
    ...
       {
          query: 'INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)',
          params: [5, 'IvanaV', 'Belgaum', '2017-10-3136']
        }
    ];
    client.batch(queries, { prepare: true}, next);
    ```

* Eseguire una query per ottenere tutti i valori di chiave.

    ```nodejs
   var query = 'SELECT * FROM uprofile.user';
    client.execute(query, { prepare: true}, function (err, result) {
      if (err) return next(err);
      result.rows.forEach(function(row) {
        console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
      }, this);
      next();
    });
    ```  
    
* Eseguire una query per ottenere un valore di chiave.

    ```nodejs
    function selectById(next) {
        console.log("\Getting by id");
        var query = 'SELECT * FROM uprofile.user where user_id=1';
        client.execute(query, { prepare: true}, function (err, result) {
        if (err) return next(err);
            result.rows.forEach(function(row) {
            console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
        }, this);
        next();
        });
    }
    ```  

## <a name="update-your-connection-string"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e copiarle nell'app. Questo consente all'app di comunicare con il database ospitato.

1. Nel [portale di Azure](http://portal.azure.com/) fare clic su **Stringa di connessione**. 

    Usare il ![Pulsante Copia](./media/create-cassandra-nodejs/copy.png) pulsante sul lato destro della schermata per copiare il primo valore, PUNTO DI CONTATTO.

    ![Visualizzare e copiare i valori di PUNTO DI CONTATTO, NOME UTENTE e PASSWORD dalla pagina della stringa di connessione del portale di Azure](./media/create-cassandra-nodejs/keys.png)

2. Aprire il file `config.js`. 

3. Incollare il valore di PUNTO DI CONTATTO dal portale su `<FillMEIN>` nella riga 4.

    La riga 4 è ora simile alla seguente: 

    `config.contactPoint = "cosmos-db-quickstarts.documents.azure.com:10350"`

4. Copiare il valore di NOME UTENTE dal portale e incollarlo su `<FillMEIN>` nella riga 2.

    La riga 2 è ora simile alla seguente: 

    `config.username = 'cosmos-db-quickstart';`
    
5. Copiare il valore di PASSWORD dal portale e incollarlo su `<FillMEIN>` nella riga 3.

    La riga 3 è ora simile alla seguente:

    `config.password = '2Ggkr662ifxz2Mg==';`

6. Salvare il file config.js.
    
## <a name="use-the-x509-certificate"></a>Usare il certificato X509 

1. Se è necessario aggiungere Baltimore CyberTrust Root, il numero di serie è 02:00:00:b9 e l'impronta digitale SHA1 è d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74. Può essere scaricato da https://cacert.omniroot.com/bc2025.crt e salvato in un file locale con estensione cer. 

2. Aprire uprofile.js e modificare 'path\to\cert' in modo che punti al nuovo certificato. 

3. Salvare uprofile.js. 

## <a name="run-the-app"></a>Esecuzione dell'app

1. Nella finestra del terminale Git eseguire `npm install` per installare i moduli npm necessari.

2. Eseguire `node uprofile.js` per avviare l'applicazione Node.js.

3. Verificare i risultati previsti dalla riga di comando.

    Premere CTRL+C per arrestare l'esecuzione del programma e chiudere la finestra della console. 

    È ora possibile aprire Esplora dati nel portale di Azure per visualizzare, modificare e usare questi nuovi dati, nonché eseguire query su di essi. 

    ![Visualizzare i dati in Esplora dati](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare una raccolta con Esplora dati e come eseguire un'app. È ora possibile importare dati aggiuntivi nell'account Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare i dati di Cassandra in Azure Cosmos DB](cassandra-import-data.md)


