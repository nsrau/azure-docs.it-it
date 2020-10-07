---
title: Avvio rapido - Usare Spring Data Azure Cosmos DB v3 per creare un database di documenti usando Azure Cosmos DB
description: Questo argomento di avvio rapido presenta un esempio di codice Spring Data Azure Cosmos DB v3 che è possibile usare per connettersi all'API SQL di Azure Cosmos DB ed eseguire query su di essa
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 0ccab295d39e463d4b6d1e764862678469fba751
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776409"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Avvio rapido: Creare un'app Spring Data Azure Cosmos DB v3 per gestire i dati dell'API SQL di Azure Cosmos DB


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

In questo argomento di avvio rapido verrà creato e gestito un account API SQL di Azure Cosmos DB dal portale di Azure e usando un'app Spring Data Azure Cosmos DB v3 clonata da GitHub. Creare prima di tutto un account API SQL di Azure Cosmos DB usando il portale di Azure, quindi creare un'app Spring Boot usando il connettore Spring Data Azure Cosmos DB v3 e infine aggiungere le risorse all'account Cosmos DB usando l'applicazione Spring Boot. Azure Cosmos DB è un servizio di database modello che consente di creare ed eseguire rapidamente query su database di documenti, tabelle, valori chiave e grafi, con funzionalità di scalabilità orizzontale e distribuzione globale.

> [!IMPORTANT]  
> Queste note sulla versione sono relative alla versione 3 di Spring Data Azure Cosmos DB. È possibile trovare [qui le note sulla versione per la versione 2](sql-api-sdk-java-spring-v2.md). 
>
> Spring Data Azure Cosmos DB supporta solo l'API SQL.
>
> Per informazioni su Spring Data in altre API di Azure Cosmos DB, vedere questi articoli:
> * [Spring Data per Apache Cassandra con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Spring Data MongoDB con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin con Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
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
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>Esaminare il codice

Questo passaggio è facoltativo. Per scoprire in che modo le risorse del database vengono create nel codice, è possibile esaminare i frammenti di codice seguenti. Altrimenti è possibile passare direttamente a [Esecuzione dell'app](#run-the-app). 

### <a name="application-configuration-file"></a>File di configurazione dell'applicazione

Si vedrà ora come Spring Boot e Spring Data migliorano l'esperienza utente: il processo di creazione di un client Cosmos e la sua connessione alle risorse Cosmos avviene ora tramite configurazione anziché usando il codice. All'avvio dell'applicazione, Spring Boot gestisce il boilerplate usando le impostazioni in **application.properties**:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Dopo aver creato un account, un database e un contenitore Azure Cosmos DB, è sufficiente inserire le informazioni nel file di configurazione e tramite Spring Boot/Spring Data verranno eseguite automaticamente le operazioni seguenti: (1) creazione di un'istanza di `CosmosClient` Java SDK sottostante con l'URI e la chiave e (2) connessione al database e al contenitore. Tutta la configurazione viene eseguita **senza codice di gestione delle risorse**.

### <a name="java-source"></a>Origine Java

Il valore aggiunto di Spring Data deriva anche dall'interfaccia semplice, chiara, standardizzata e indipendente dalla piattaforma per la gestione degli archivi dati. In base all'esempio Spring Data di GitHub di cui è stato fornito un collegamento in precedenza, di seguito sono riportati esempi di operazioni CRUD e di query per la modifica dei documenti di Azure Cosmos DB con Spring Data Azure Cosmos DB.

* Creazione e aggiornamento di elementi tramite il metodo `save`.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Letture punto usando il metodo di query derivata definito nel repository. `findByIdAndLastName` esegue letture punto per `UserRepository`. I campi indicati nel nome del metodo fanno in modo che Spring Data esegua una lettura punto definita dai campi `id` e `lastName`:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Eliminazione di elementi usando `deleteAll`:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Query derivata basata sul nome del metodo del repository. Spring Data implementa il metodo `findByFirstName` di `UserRepository` come una query SQL Java SDK nel campo `firstName` (la query non può essere implementata come lettura punto):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Eseguire l'app

Tornare ora al portale di Azure per recuperare le informazioni sulla stringa di connessione e avviare l'app con le informazioni sugli endpoint. Questo consente all'app di comunicare con il database ospitato.

1. Nella finestra del terminale Git passare con il comando `cd` alla cartella del codice di esempio.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. Nella finestra del terminale Git usare il comando seguente per installare i pacchetti Spring Data Azure Cosmos DB necessari.

    ```bash
    mvn clean package
    ```

3. Nella finestra del terminale Git usare il comando seguente per avviare l'applicazione Spring Data Azure Cosmos DB:

    ```bash
    mvn spring-boot:run
    ```
    
4. L'app carica **application.properties** e connette le risorse nell'account Azure Cosmos DB.
5. L'app eseguirà le operazioni punto CRUD descritte in precedenza.
6. L'app eseguirà una query derivata.
7. L'app non elimina le risorse. Tornare al portale per eseguire la [pulizia delle risorse](#clean-up-resources) dal proprio account per evitare che vengano addebitati costi.

## <a name="review-slas-in-the-azure-portal"></a>Esaminare i contratti di servizio nel portale di Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account API SQL di Azure Cosmos DB, come creare un database di documenti e un contenitore usando Esplora dati e come eseguire un'app Spring Data per ottenere lo stesso risultato a livello di codice. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importare dati in Azure Cosmos DB](import-data.md)
