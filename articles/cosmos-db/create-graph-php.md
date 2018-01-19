---
title: 'Guida introduttiva: API Graph con PHP - Azure Cosmos DB | Microsoft Docs'
description: Questa guida introduttiva descrive come usare l'API Graph di Azure Cosmos DB per creare un'applicazione console con il portale di Azure e PHP
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
ms.assetid: dfce0de9-a326-401c-9940-406ac0414461
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: 
ms.topic: quickstart
ms.date: 01/05/2018
ms.author: lbosq
ms.openlocfilehash: 3f80e682a89dcf6b5d3d5d6f2f15c4eeab2046e7
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2018
---
# <a name="azure-cosmos-db-create-a-graph-database-using-php-and-the-azure-portal"></a>Azure Cosmos DB: Creare un database di grafi con PHP e il portale di Azure

Questa guida introduttiva descrive come usare PHP e l'[API Graph](graph-introduction.md) di Azure Cosmos DB per creare un'app console clonando un esempio di GitHub. Questa guida introduttiva illustra anche come creare un account Azure Cosmos DB usando il portale di Azure basato sul Web.   

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, sfruttando in ognuno dei casi i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB.  

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]In alternativa, è possibile [provare gratuitamente Microsoft Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure e senza impegno.

Eseguire anche queste operazioni:
* [PHP](http://php.net/) 5.6 o versioni successive
* [Composer](https://getcomposer.org/download/)

## <a name="create-a-database-account"></a>Creare un account di database

Prima di potere creare un database a grafo, è necessario creare un account di database Gremlin (Graph) con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Aggiungere un grafo

È ora possibile usare lo strumento Esplora dati nel portale di Azure per creare un database a grafo. 

1. Fare clic su **Esplora dati** > **New Graph** (Nuovo grafo).

    Viene visualizzata l'area **Add Graph** (Aggiungi grafo) all'estrema destra. Per vederla potrebbe essere necessario scorrere la schermata.

    ![Pagina Esplora dati, Add Graph (Aggiungi grafico) nel portale di Azure](./media/create-graph-php/azure-cosmosdb-data-explorer-graph.png)

2. Nella pagina **Add Graph** (Aggiungi grafo) immettere le impostazioni per il nuovo grafo.

    Impostazione|Valore consigliato|DESCRIZIONE
    ---|---|---
    ID database|sample-database|Immettere *sample-database* come nome del nuovo database. I nomi dei database devono avere una lunghezza compresa tra 1 e 255 caratteri e non possono contenere `/ \ # ?` o spazi finali.
    ID grafo|sample-graph|Immettere *sample-graph* come nome della nuova raccolta. I nomi dei grafi presentano gli stessi requisiti relativi ai caratteri degli ID di database.
    Capacità di archiviazione|Fissa (10 GB)|Lasciare il valore predefinito **Fissa (10 GB)**. Questo valore indica la capacità di archiviazione del database.
    Velocità effettiva|400 UR/s|Modificare la velocità effettiva in 400 unità richiesta al secondo (UR/sec). Se si vuole ridurre la latenza, è possibile aumentare la velocità effettiva in un secondo momento.

3. Dopo aver compilato il modulo, fare clic su **OK**.

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Si può ora passare a usare il codice. È possibile clonare ora un'app API Graph da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice.  

1. Aprire un prompt dei comandi, creare una nuova cartella denominata git-samples e quindi chiudere il prompt dei comandi.

    ```bash
    md "C:\git-samples"
    ```

2. Aprire una finestra del terminale Git, ad esempio git bash, ed eseguire il comando `cd` per passare a una cartella in cui installare l'app di esempio.  

    ```bash
    cd "C:\git-samples"
    ```

3. Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-php-getting-started.git
    ```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. I frammenti di codice provengono tutti dal file `connect.php`, che si trova nella cartella C:\git-samples\azure-cosmos-db-graph-php-getting-started\. In alternativa, è possibile passare ad [Aggiornare la stringa di connessione](#update-your-connection-information). 

* Il Gremlin `connection` viene inizializzato all'inizio del file `connect.php` usando l'oggetto `$db`.

    ```php
    $db = new Connection([
        'host' => '<your_server_address>.graphs.azure.com',
        'username' => '/dbs/<db>/colls/<coll>',
        'password' => 'your_primary_key'
        ,'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

* Viene eseguita una serie di passaggi di Gremlin tramite il metodo `$db->send($query);`.

    ```php
    $query = "g.V().drop()";
    ...
    $result = $db->send($query);
    $errors = array_filter($result);
    }
    ```

## <a name="update-your-connection-information"></a>Aggiornare la stringa di connessione

Tornare ora al portale di Azure per recuperare la stringa di connessione e copiarla nell'app. Queste impostazioni consentono all'app di comunicare con il database ospitato.

1. Nel [portale di Azure](http://portal.azure.com/) fare clic su **Chiavi**. 

    Copiare la prima parte del valore dell'URI.

    ![Visualizzare e copiare una chiave di accesso nella pagina Chiavi del portale di Azure](./media/create-graph-php/keys.png)
2. Aprire il file `connect.php` e alla riga 8 incollare il valore di URI su `your_server_address`.

    L'inizializzazione dell'oggetto Connection deve essere simile all'output seguente:

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.graphs.azure.com',
        'username' => '/dbs/<db>/colls/<coll>',
        'password' => 'your_primary_key'
        ,'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

3. Se l'account di database Graph è stato creato il 20 dicembre 2017 o successivamente, sostituire `graphs.azure.com` nel nome host con `gremlin.cosmosdb.azure.com`.

4. Modificare il parametro `username` nell'oggetto Connection con il nome del database e del grafo. Se sono stati usati i valori consigliati `sample-database` e `sample-graph`, l'output sarà simile al seguente:

    `'username' => '/dbs/sample-database/colls/sample-graph'`

    Ecco l'aspetto dell'intero oggetto Connection a questo punto:

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.graphs.azure.com',
        'username' => '/dbs/sample-database/colls/sample-graph',
        'password' => 'your_primary_key',
        'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

5. Nel portale di Azure usare il pulsante Copia per copiare la CHIAVE PRIMARIA e incollarla su `your_primary_key` nel parametro password.

    L'inizializzazione dell'oggetto Connection dovrebbe ora essere simile all'output seguente:

    ```php
    $db = new Connection([
        'host' => 'testgraphacct.graphs.azure.com',
        'username' => '/dbs/sample-database/colls/sample-graph',
        'password' => '2Ggkr662ifxz2Mg==',
        'port' => '443'

        // Required parameter
        ,'ssl' => TRUE
    ]);
    ```

6. Salvare il file.`connect.php`

## <a name="run-the-console-app"></a>Eseguire l'app console

1. Nella finestra del terminale Git eseguire il comando `cd` per passare alla cartella azure-cosmos-db-graph-php-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-php-getting-started"
    ```

2. Nella finestra del terminale Git usare il comando seguente per installare le dipendenze PHP necessarie.

   ```
   composer install
   ```

3. Nella finestra del terminale Git usare il comando seguente per avviare l'applicazione PHP.
    
    ```
    php connect.php
    ```

    La finestra del terminale mostra l'aggiunta dei vertici al grafo. 
    
    Se si verificano errori di timeout, controllare di avere aggiornato correttamente le informazioni di connessione in [Aggiornare le informazioni di connessione](#update-your-connection-information) e provare a eseguire di nuovo l'ultimo comando. 
    
    All'arresto del programma premere Invio per tornare al portale di Azure nel browser Internet. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Verificare e aggiungere dati di esempio

È ora possibile tornare a Esplora dati e visualizzare i vertici aggiunti al grafo, quindi aggiungere altri punti dati.

1. Fare clic su **Esplora dati**, espandere **sample-graph**, fare clic su **Grafo** e quindi su **Applica filtro**. 

   ![Creare nuovi documenti in Esplora dati nel portale di Azure](./media/create-graph-php/azure-cosmosdb-data-explorer-expanded.png)

2. Nell'elenco **Risultati** verificare i nuovi utenti aggiunti al grafo. Selezionare **ben**. Come si può notare, è connesso a robin. È possibile spostare i vertici intorno mediante trascinamento della selezione, applicare lo zoom mediante lo scorrimento della rotellina del mouse ed espandere le dimensioni del grafo usando la doppia freccia. 

   ![Nuovi vertici nel grafo in Esplora dati nel portale di Azure](./media/create-graph-php/azure-cosmosdb-graph-explorer-new.png)

3. Vengono ora aggiunti alcuni nuovi utenti. Fare clic sul pulsante **New Vertex** (Nuovo vertice) per aggiungere dati al grafo.

   ![Creare nuovi documenti in Esplora dati nel portale di Azure](./media/create-graph-php/azure-cosmosdb-data-explorer-new-vertex.png)

4. Immettere un'etichetta di *persona*.

5. Fare clic su **Add property** (Aggiungi proprietà) per aggiungere ognuna delle proprietà seguenti. Si noti che è possibile creare proprietà univoche per ogni persona del grafo. È necessaria solo la chiave id.

    key|value|Note
    ----|----|----
    id|ashley|Identificatore univoco per il vertice. Se non si specifica alcun ID, ne verrà generato automaticamente uno.
    gender|female| 
    tech | java | 

    > [!NOTE]
    > In questa esercitazione introduttiva viene creata una raccolta non partizionata. Se tuttavia si crea una raccolta partizionata specificando una chiave di partizione durante la creazione della raccolta, sarà necessario includere la chiave di partizione come chiave in ogni nuovo vertice. 

6. Fare clic su **OK**. Potrebbe essere necessario espandere la schermata per vedere il pulsante **OK** nella parte inferiore dello schermo.

7. Fare di nuovo clic su **New Vertex** (Nuovo vertice) e aggiungere un altro nuovo utente. 

8. Immettere un'etichetta di *persona*.

9. Fare clic su **Add property** (Aggiungi proprietà) per aggiungere ognuna delle proprietà seguenti:

    key|value|Note
    ----|----|----
    id|rakesh|Identificatore univoco per il vertice. Se non si specifica alcun ID, ne verrà generato automaticamente uno.
    gender|male| 
    school|MIT| 

10. Fare clic su **OK**. 

11. Fare clic sul pulsante **Applica filtro** con il filtro `g.V()` predefinito per visualizzare tutti i valori nel grafo. Tutti gli utenti sono ora visualizzati nell'elenco **Risultati**. 

    Quando si aggiungono altri dati, è possibile usare i filtri per limitare i risultati visualizzati. Per impostazione predefinita, Esplora dati usa `g.V()` per recuperare tutti i vertici di un grafo. È possibile modificarlo in un'altra [query di grafo](tutorial-query-graph.md), ad esempio `g.V().count()`, per restituire un conteggio di tutti i vertici del grafo in formato JSON. Se è stato modificato il filtro, reimpostarlo su `g.V()` e fare clic su **Applica filtro** per visualizzare di nuovo tutti i risultati.

12. È ora possibile connettere rakesh e ashley. Assicurarsi che il valore **ashley** sia selezionato nell'elenco **Risultati**, quindi fare clic sul pulsante di modifica accanto a **Destinazioni** in basso a destra. Potrebbe essere necessario allargare la finestra per visualizzare l'area **Proprietà**.

   ![Cambiare la destinazione di un vertice in un grafo](./media/create-graph-php/azure-cosmosdb-data-explorer-edit-target.png)

13. Nella casella **Destinazione** digitare *rakesh* e nella casella **Edge label** (Etichetta arco) digitare *knows*, quindi selezionare la casella di controllo.

   ![Aggiungere una connessione tra ashley e rakesh in Esplora dati](./media/create-graph-php/azure-cosmosdb-data-explorer-set-target.png)

14. Selezionare ora **rakesh** dall'elenco Risultati. Come si può notare ashley e rakesh sono connessi. 

   ![Due vertici connessi in Esplora dati](./media/create-graph-php/azure-cosmosdb-graph-explorer.png)

   È stata completata la parte della creazione di risorse di questa esercitazione. È possibile continuare a aggiungere vertici al grafo, modificare quelli esistenti o modificare le query. Vengono ora esaminate le metriche di Azure Cosmos DB e quindi pulite le risorse. 

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come creare un account Azure Cosmos DB, come creare un grafo con Esplora dati e come eseguire un'app. È ora possibile creare query più complesse e implementare la potente logica di attraversamento dei grafi usando Gremlin. 

> [!div class="nextstepaction"]
> [Eseguire query con Gremlin](tutorial-query-graph.md)

