---
title: 'Esercitazione: Esempio di app per le API SQL Java asincrona end-to-end con il feed di modifiche'
description: Questa esercitazione illustra una semplice app per le API SQL Java che inserisce documenti in un contenitore di Azure Cosmos DB, mantenendo al tempo stesso una vista materializzata del contenitore con il feed di modifiche.
author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 04/01/2020
ms.author: anfeldma
ms.openlocfilehash: 5eab523dde2a13a85b0c8ff5bcbb3ecb5912e78e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80587218"
---
# <a name="tutorial---an-end-to-end-async-java-sql-api-application-sample-with-change-feed"></a>Esercitazione: Esempio di app per le API SQL Java asincrona end-to-end con il feed di modifiche

Questa esercitazione illustra una semplice app per le API SQL Java che inserisce documenti in un contenitore di Azure Cosmos DB, mantenendo al tempo stesso una vista materializzata del contenitore con il feed di modifiche.

## <a name="prerequisites"></a>Prerequisiti

* Personal computer

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

> È possibile scegliere di seguire questa guida di avvio rapido con Java SDK 4.0 o Java SDK 3.7.0. **Se si vuole usare Java SDK 3.7.0, nel terminale digitare ```git checkout SDK3.7.0```** . In caso contrario, restare nel ramo ```master```, che usa per impostazione predefinita Java SDK 4.0.

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

    tornare quindi alla pagina del browser di Esplora dati nel portale di Azure. Si osserverà che è stato aggiunto un database **GroceryStoreDatabase** con tre contenitori vuoti: 

    * **InventoryContainer**: il record di inventario per il negozio di alimentari di esempio, partizionato sull'elemento ```id```, che è un UUID.
    * **InventoryContainer-PKType**: una vista materializzata del record di inventario, ottimizzata per le query sull'elemento ```type```
    * **InventoryContainer-leases**: un contenitore di lease è sempre necessario per il feed delle modifiche. I lease tengono traccia dello stato dell'app durante la lettura del feed di modifiche.


    ![Contenitori vuoti](media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG)


1. Nel terminale verrà visualizzato il messaggio seguente

    ```bash
    Press enter to start creating the materialized view...
    ```

    Premere INVIO. A questo punto, il blocco di codice seguente eseguirà e inizializzerà il processore del feed di modifiche in un altro thread: 


    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
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

    Tornare alla pagina del browser di Esplora dati nel portale di Azure. Nel contenitore **InventoryContainer-leases** fare clic su **items** (elementi) per visualizzarne il contenuto. Si noterà che il processore del feed di modifiche ha popolato il contenitore di lease, ovvero il processore ha assegnato il ruolo di lavoro ```SampleHost_1``` a un lease in alcune partizioni di **InventoryContainer**.

    ![Lease](media/create-sql-api-java-changefeed/cosmos_leases.JPG)

1. Premere di nuovo INVIO nel terminale. Verranno attivati 10 documenti per l'inserimento in **InventoryContainer**. Ogni inserimento di documento viene visualizzato nel feed di modifiche come JSON. Il codice di callback seguente gestisce questi eventi eseguendo il mirroring dei documenti JSON in una vista materializzata:

    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
    ```java
    public static ChangeFeedProcessor getChangeFeedProcessor(String hostName, CosmosContainer feedContainer, CosmosContainer leaseContainer) {
        ChangeFeedProcessorOptions cfOptions = new ChangeFeedProcessorOptions();
        cfOptions.feedPollDelay(Duration.ofMillis(100));
        cfOptions.startFromBeginning(true);
        return ChangeFeedProcessor.Builder()
            .options(cfOptions)
            .hostName(hostName)
            .feedContainer(feedContainer)
            .leaseContainer(leaseContainer)
            .handleChanges((List<CosmosItemProperties> docs) -> {
                for (CosmosItemProperties document : docs) {
                        //Duplicate each document update from the feed container into the materialized view container
                        updateInventoryTypeMaterializedView(document);
                }

            })
            .build();
    }

    private static void updateInventoryTypeMaterializedView(CosmosItemProperties document) {
        typeContainer.upsertItem(document).subscribe();
    }    
    ```

1. Attendere 5-10 secondi mentre viene eseguito il codice. Tornare quindi a Esplora dati nel portale di Azure e passare a **InventoryContainer > items** (InventoryContainer > elementi). Si può osservare che gli elementi vengono inseriti nel contenitore di inventario. Prendere nota della chiave di partizione (```id```).

    ![Contenitore feed](media/create-sql-api-java-changefeed/cosmos_items.JPG)

1. A questo punto, in Esplora dati passare a **InventoryContainer-PKType > items** (InventoryContainer-pktype > elementi). Questa è la vista materializzata. È stato eseguito il mirroring degli elementi di **InventoryContainer** in questo contenitore perché sono stati inseriti a livello di codice dal feed di modifiche. Si noti la chiave di partizione (```type```). Questa vista materializzata è quindi ottimizzata per le query che filtrano in base a ```type```, operazione che non risulterebbe efficiente in **InventoryContainer** perché è partizionato su ```id```.

    ![Vista materializzata](media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG)

1. Verrà ora eliminato un documento da **InventoryContainer** e **InventoryContainer-PKType** usando solo una singola chiamata a ```upsertItem()```. Osservare Esplora dati nel portale di Azure. Verrà eliminato il documento per cui ```/type == "plums"``` è cerchiato in rosso sotto

    ![Vista materializzata](media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG)

    Premere di nuovo INVIO per chiamare la funzione ```deleteDocument()``` nel codice di esempio. Questa funzione, mostrata sotto, esegue l'upsert di una nuova versione del documento con ```/ttl == 5```, che imposta il valore della durata TTL del documento su 5 secondi. 
    
    **Java SDK 4.0**
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

    **Java SDK 3.7.0**
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
