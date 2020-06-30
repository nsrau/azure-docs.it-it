---
title: 'Avvio rapido: Usare Java per creare un database di documenti con Azure Cosmos DB'
description: Questa guida di avvio rapido presenta un esempio di codice Java che permette di connettersi all'API SQL di Azure Cosmos DB ed eseguire query su di essa
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 05/11/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: bb711dc67df51edc569f23bd1e6c54eef55012d2
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2020
ms.locfileid: "85115390"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-sql-api-data"></a>Avvio rapido: Compilare un'app Java per gestire i dati dell'API SQL di Azure Cosmos DB


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In questo argomento di avvio rapido si apprenderà a creare e gestire un account API SQL di Azure Cosmos DB dal portale di Azure usando un'app Java clonata da GitHub. Prima di tutto creare un account API SQL di Azure Cosmos DB usando il portale di Azure, quindi creare un'applicazione Java usando SQL Java SDK e quindi aggiungere le risorse all'account Cosmos DB usando l'applicazione Java. Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale.

> [!IMPORTANT]  
> Questo argomento di Avvio rapido è destinato solo a Java SDK per Azure Cosmos DB v4. Per altre informazioni, vedere le [note sulla versione](sql-api-sdk-java-v4.md) di Java SDK per Azure Cosmos DB v4, [repository Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos), i [suggerimenti sulle prestazioni ](performance-tips-java-sdk-v4-sql.md) di Java SDK per Azure Cosmos DB v4 e la [guida alla risoluzione dei problemi](troubleshoot-java-sdk-v4-sql.md) di Java SDK per Azure Cosmos DB v4. Se attualmente si usa una versione precedente a v4, vedere l'articolo [Eseguire la migrazione a Java SDK v4 per Azure Cosmos DB](migrate-java-v4-sdk.md) per informazioni sull'aggiornamento a v4.
>

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [È possibile crearne uno gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). In alternativa, è possibile [provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure. È anche possibile usare l'[Emulatore di Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) con l'URI `https://localhost:8081` e la chiave `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Indirizzare la variabile di ambiente `JAVA_HOME` alla cartella di installazione di JDK.
- Un [archivio binario Maven](https://maven.apache.org/download.cgi). In Ubuntu eseguire `apt-get install maven` per installare Maven.
- [Git](https://www.git-scm.com/downloads). In Ubuntu eseguire `sudo apt-get install git` per installare Git.

## <a name="introductory-notes"></a>Note introduttive

*Struttura di un account Cosmos DB.* Indipendentemente dall'API o dal linguaggio di programmazione, un *account* Cosmos DB contiene zero o più *database*, un *database* (DB) contiene zero o più *contenitori* e un *contenitore* contiene zero o più elementi, come illustrato nel diagramma seguente:

:::image type="content" source="./media/databases-containers-items/cosmos-entities.png" alt-text="Entità dell'account Azure Cosmos DB" border="false":::

Altre informazioni su database, contenitori ed elementi sono disponibili [qui](databases-containers-items.md). Alcune proprietà importanti sono definite a livello del contenitore, tra cui *velocità effettiva con provisioning* e *chiave di partizione*. 

La velocità effettiva con provisioning viene misurata in unità richiesta (*UR*) che hanno un prezzo monetario e rappresentano un fattore determinante sostanziale nel costo operativo dell'account. È possibile selezionare la velocità effettiva con provisioning in base alla granularità per contenitore o alla granularità per database, ma è in genere preferibile specificare la velocità effettiva a livello di contenitore. Per altre informazioni sul provisioning della velocità effettiva, vedere [qui.](set-throughput.md)

Man mano che gli elementi vengono inseriti in un contenitore Cosmos DB, il database aumenta orizzontalmente aggiungendo più risorse di archiviazione e calcolo per gestire le richieste. La capacità di archiviazione e di calcolo viene aggiunta in unità discrete note come *partizioni* ed è necessario scegliere un campo nei documenti come chiave di partizione che esegue il mapping di ogni documento a una partizione. Il modo in cui vengono gestite le partizioni è che a ogni partizione viene assegnata una sezione approssimativamente uguale non compresa nell'intervallo di valori delle chiavi di partizione. È pertanto consigliabile scegliere una chiave di partizione che sia relativamente casuale o distribuita in modo uniforme. In caso contrario, alcune partizioni conterranno sostanzialmente più richieste (*partizione con accesso frequente*) mentre altre partizioni conterranno sostanzialmente meno richieste (*partizione con accesso sporadico*) e questo deve essere evitato. Per altre informazioni sul partizionamento, vedere [qui](partitioning-overview.md).

## <a name="create-a-database-account"></a>Creare un account di database

Per poter creare un database di documenti, è prima necessario creare un account API SQL con Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Aggiungere un contenitore

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Aggiungere dati di esempio

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Eseguire query sui dati

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonare l'applicazione di esempio

Si può ora passare a usare il codice. Clonare un'app per le API SQL da GitHub, impostare la stringa di connessione ed eseguirla. Come si noterà, è facile usare i dati a livello di codice. 

Eseguire il comando seguente per clonare l'archivio di esempio. Questo comando crea una copia dell'app di esempio nel computer in uso.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-getting-started.git
```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. Altrimenti è possibile passare direttamente a [Esecuzione dell'app](#run-the-app). 


# <a name="sync-api"></a>[API sincrona](#tab/sync)

### <a name="managing-database-resources-using-the-synchronous-sync-api"></a>Gestione delle risorse del database con l'API sincrona

* Inizializzazione di `CosmosClient`. L'oggetto `CosmosClient` fornisce una rappresentazione logica lato client per il servizio di database Azure Cosmos. Questo client viene usato per configurare ed eseguire richieste nel servizio.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateSyncClient)]

* Creazione di `CosmosDatabase`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* Creazione di `CosmosContainer`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Creazione di elementi tramite il metodo `createItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateItem)]
   
* Le letture dei punti vengono eseguite con il metodo `readItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=ReadItem)]

* Le query SQL su JSON vengono eseguite usando il metodo `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=QueryItems)]

# <a name="async-api"></a>[API asincrona](#tab/async)

### <a name="managing-database-resources-using-the-asynchronous-async-api"></a>Gestione delle risorse del database con l'API asincrona

* Le chiamate all'API asincrona restituiscono immediatamente un risultato, senza attendere una risposta dal server. Tenendo in considerazione questa caratteristica, i frammenti di codice seguenti mostrano gli schemi progettuali appropriati per completare tutte le precedenti attività di gestione con l'API asincrona.

* Inizializzazione di `CosmosAsyncClient`. L'oggetto `CosmosAsyncClient` fornisce una rappresentazione logica lato client per il servizio di database Azure Cosmos. Questo client viene usato per configurare ed eseguire richieste asincrone sul servizio.
    
    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateAsyncClient)]

* Creazione di `CosmosAsyncDatabase`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateDatabaseIfNotExists)]

* Creazione di `CosmosAsyncContainer`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/sync/SyncMain.java?name=CreateContainerIfNotExists)]

* Come nel caso dell'API sincrona, la creazione di elementi viene eseguita con il metodo `createItem`. Questo esempio illustra come inviare in modo efficiente numerose richieste `createItem` effettuando la sottoscrizione di un flusso reattivo che invia le richieste e stampa le notifiche. Dato che questo semplice esempio viene terminato quando viene completata l'esecuzione, si usano istanze di `CountDownLatch` affinché il programma non venga terminato durante la creazione di elementi. **Il blocco in caso di chiamate asincrone non è la procedura di programmazione asincrona corretta. In casi d'uso realistici, le richieste vengono generate da un ciclo main() con esecuzione illimitata e non è necessario il latch per le chiamate asincrone.**

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=CreateItem)]
   
* Come nel caso dell'API sincrona, le letture dei punti vengono eseguite con il metodo `readItem`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=ReadItem)]

* Come nel caso dell'API sincrona, le query SQL su JSON vengono eseguite con il metodo `queryItems`.

    [!code-java[](~/azure-cosmosdb-java-v4-getting-started/src/main/java/com/azure/cosmos/sample/async/AsyncMain.java?name=QueryItems)]

---

## <a name="run-the-app"></a>Eseguire l'app

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e avviare l'app con le informazioni sugli endpoint. Questo consente all'app di comunicare con il database ospitato.

1. Nella finestra del terminale Git passare con il comando `cd` alla cartella del codice di esempio.

    ```bash
    cd azure-cosmos-java-getting-started
    ```

2. Nella finestra del terminale Git usare il comando seguente per installare i pacchetti Java necessari.

    ```bash
    mvn package
    ```

3. Nella finestra del terminale Git usare il comando seguente per avviare l'applicazione Java, sostituendo SYNCASYNCMODE con `sync` o `async` (a seconda del codice di esempio che si vuole eseguire), YOUR_COSMOS_DB_HOSTNAME con il valore URI del portale tra virgolette e YOUR_COSMOS_DB_MASTER_KEY con la chiave primaria del portale tra virgolette.

    ```bash
    mvn exec:java@SYNCASYNCMODE -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    La finestra del terminale visualizza una notifica di creazione del database FamilyDB. 
    
4. L'app crea un database denominato `AzureSampleFamilyDB`
5. L'app crea un contenitore denominato `FamilyContainer`
6. L'app eseguirà le letture dei punti usando gli ID oggetto e il valore della chiave di partizione (lastName nell'esempio). 
7. L'app eseguirà query sugli elementi per recuperare tutte le famiglie con il cognome ('Andersen', 'Wakefield', 'Johnson')

7. L'app non elimina le risorse create. Tornare al portale per [pulire le risorse](#clean-up-resources)  nel proprio account in modo da non ricevere alcun addebito.

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account API SQL di Azure Cosmos DB, come creare un database di documenti e un contenitore usando Esplora dati e come eseguire un'app Java per ottenere lo stesso risultato a livello di codice. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare dati in Azure Cosmos DB](import-data.md)
