---
title: Creare un'applicazione di esempio end-to-end Azure Cosmos DB Java SDK v4 usando il feed delle modifiche
description: Questa guida dettagliata illustra una semplice applicazione API SQL Java che inserisce documenti in un contenitore Azure Cosmos DB, mantenendo al tempo stesso una vista materializzata del contenitore con il feed delle modifiche.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: anfeldma
ms.openlocfilehash: 9e28eb4f766677ebbd5cfcc5f61fe54e53a45523
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996518"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Come creare un'applicazione Java che usa Azure Cosmos DB API SQL e il processore di feed di modifiche

> [!IMPORTANT]  
> Per altre informazioni su Azure Cosmos DB Java SDK v4, vedere le note sulla versione di Azure Cosmos DB Java SDK v4, il [repository maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), i [suggerimenti](performance-tips-java-sdk-v4-sql.md)per le prestazioni di Azure Cosmos DB Java SDK v4 e la [Guida alla risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md)di Azure Cosmos DB SDK v4.
>

Questa guida dettagliata illustra una semplice applicazione Java che usa l'API SQL Azure Cosmos DB per inserire documenti in un contenitore Azure Cosmos DB, mantenendo al tempo stesso una vista materializzata del contenitore usando il feed delle modifiche e il processore dei feed delle modifiche. L'applicazione Java comunica con l'API SQL di Azure Cosmos DB usando Azure Cosmos DB Java SDK v4.

## <a name="prerequisites"></a>Prerequisiti

* URI e chiave dell'account Azure Cosmos DB

* Maven

* Java 8

## <a name="background"></a>Background

Il feed di modifiche di Azure Cosmos DB fornisce un'interfaccia basata su eventi per attivare le azioni in risposta all'inserimento di documenti e offre numerosi vantaggi. Nelle applicazioni con un numero elevato di operazioni di lettura e scrittura, ad esempio, l'uso principale del feed di modifiche consiste nel creare una **vista materializzata** in tempo reale di un contenitore durante l'inserimento di documenti. La vista materializzata del contenitore presenterà gli stessi dati, ma partizionati per letture efficienti, ottimizzando così l'applicazione sia in lettura che in scrittura.

L'attività di gestione degli eventi del feed di modifiche viene principalmente gestita dalla libreria del processore del feed di modifiche incorporata nell'SDK. Questa libreria è sufficientemente potente da distribuire gli eventi del feed di modifiche tra più ruoli di lavoro, se desiderato. È sufficiente fornire alla libreria del feed di modifiche un callback.

Questo semplice esempio illustra la libreria del processore del feed di modifiche con un singolo ruolo di lavoro che crea ed elimina documenti da una vista materializzata.

## <a name="setup"></a>Configurazione

Se non è già stato fatto, clonare il repository di esempio per l'app:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Aprire un terminale nella directory del repository. Per creare l'app, eseguire

```bash
mvn clean package
```

## <a name="walkthrough"></a>Procedura dettagliata

1. Per prima cosa, è necessario disporre di un account Azure Cosmos DB. Aprire il **portale di Azure** nel browser, passare all'account Azure Cosmos DB e nel riquadro sinistro passare a **Esplora dati**.

    ![Account Azure Cosmos DB](media/create-sql-api-java-changefeed/cosmos_account_empty.JPG)

1. Eseguire l'app nel terminale usando questo comando:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Premere INVIO quando viene visualizzato il messaggio

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    tornare quindi al Esplora dati portale di Azure nel browser. Si osserverà che è stato aggiunto un database **GroceryStoreDatabase** con tre contenitori vuoti: 

    * **InventoryContainer**: il record di inventario per il negozio di alimentari di esempio, partizionato sull'elemento ```id```, che è un UUID.
    * **InventoryContainer-PKType**: una vista materializzata del record di inventario, ottimizzata per le query sull'elemento ```type```
    * **InventoryContainer-leases**: un contenitore di lease è sempre necessario per il feed delle modifiche. I lease tengono traccia dello stato dell'app durante la lettura del feed di modifiche.


    ![Contenitori vuoti](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. Nel terminale verrà visualizzato il messaggio seguente

    ```bash
    Press enter to start creating the materialized view...
    ```

    Premere INVIO. A questo punto, il blocco di codice seguente eseguirà e inizializzerà il processore del feed di modifiche in un altro thread: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) API Async

    ```java
    changeFeedProcessorInstance = getChangeFeedProcessor("SampleHost_1", feedContainer, leaseContainer);
    changeFeedProcessorInstance.start()
        .subscribeOn(Schedulers.elastic())
        .doOnSuccess(aVoid -> {
            isProcessorRunning.set(true);
        })
        .subscribe();

    while (!isProcessorRunning.get()); //Wait for Change Feed processor start
    ```

    ```"SampleHost_1"``` è il nome del ruolo di lavoro del processore del feed di modifiche. ```changeFeedProcessorInstance.start()``` è l'elemento che avvia effettivamente il processore del feed di modifiche.

    Tornare al Esplora dati portale di Azure nel browser. Nel contenitore **InventoryContainer-leases** fare clic su **items** (elementi) per visualizzarne il contenuto. Si noterà che il processore del feed di modifiche ha popolato il contenitore di lease, ovvero il processore ha assegnato il ruolo di lavoro ```SampleHost_1``` a un lease in alcune partizioni di **InventoryContainer**.

    ![Lease](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Premere di nuovo INVIO nel terminale. Verranno attivati 10 documenti per l'inserimento in **InventoryContainer**. Ogni inserimento di documento viene visualizzato nel feed di modifiche come JSON. Il codice di callback seguente gestisce questi eventi eseguendo il mirroring dei documenti JSON in una vista materializzata:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) API Async

    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosAsyncContainer feedContainer, CosmosAsyncContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.setFeedPollDelay(Duration.ofMillis(100));
        cfOptions.setStartFromBeginning(true);
        return ChangeFeedProcessor.changeFeedProcessorBuilder()
            .setOptions(cfOptions)
            .setHostName(hostName)
            .setFeedContainer(feedContainer)
            .setLeaseContainer(leaseContainer)
            .setHandleChanges((List<JsonNode> docs) -> {
                for (JsonNode document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(JsonNode document) {
        typeContainer.upsertItem(document).subscribe();
    }
    ```

1. Attendere 5-10 secondi mentre viene eseguito il codice. Tornare quindi al Esplora dati portale di Azure e passare a **InventoryContainer > Items**. Si può osservare che gli elementi vengono inseriti nel contenitore di inventario. Prendere nota della chiave di partizione (```id```).

    ![Contenitore feed](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. A questo punto, in Esplora dati passare a **InventoryContainer-PKType > items** (InventoryContainer-pktype > elementi). Questa è la vista materializzata. È stato eseguito il mirroring degli elementi di **InventoryContainer** in questo contenitore perché sono stati inseriti a livello di codice dal feed di modifiche. Si noti la chiave di partizione (```type```). Questa vista materializzata è quindi ottimizzata per le query che filtrano in base a ```type```, operazione che non risulterebbe efficiente in **InventoryContainer** perché è partizionato su ```id```.

    ![Vista materializzata](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Verrà ora eliminato un documento da **InventoryContainer** e **InventoryContainer-PKType** usando solo una singola chiamata a ```upsertItem()```. Per prima cosa, vedere portale di Azure Esplora dati. Verrà eliminato il documento per cui ```/type == "plums"``` è cerchiato in rosso sotto

    ![Vista materializzata](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Premere di nuovo INVIO per chiamare la funzione ```deleteDocument()``` nel codice di esempio. Questa funzione, mostrata sotto, esegue l'upsert di una nuova versione del documento con ```/ttl == 5```, che imposta il valore della durata TTL del documento su 5 secondi. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK v4 (Maven com. Azure:: Azure-Cosmos) API Async

    ```java
    public static void deleteDocument() {

        String jsonString =    "{\"id\" : \"" + idToDelete + "\""
                + ","
                + "\"brand\" : \"Jerry's\""
                + ","
                + "\"type\" : \"plums\""
                + ","
                + "\"quantity\" : \"50\""
                + ","
                + "\"ttl\" : 5"
                + "}";

        ObjectMapper mapper = new ObjectMapper();
        JsonNode document = null;

        try {
            document = mapper.readTree(jsonString);
        } catch (Exception e) {
            e.printStackTrace();
        }

        feedContainer.upsertItem(document,new CosmosItemRequestOptions()).block();
    }    
    ```

    Il feed di modifiche ```feedPollDelay``` è impostato su 100 ms, di conseguenza il feed di modifiche risponde quasi immediatamente a questo aggiornamento e chiama ```updateInventoryTypeMaterializedView()```, come illustrato in precedenza. L'ultima chiamata di funzione esegue l'upsert del nuovo documento con una durata TTL di 5 secondi in **InventoryContainer-pktype**.

    L'effetto è che, dopo circa 5 secondi, il documento scadrà e verrà eliminato da entrambi i contenitori.

    Questa procedura è necessaria perché il feed di modifiche rilascia solo gli eventi per l'inserimento o l'aggiornamento di un elemento, non per l'eliminazione di elementi.

1. Premere INVIO ancora una volta per chiudere il programma e pulire le risorse.
