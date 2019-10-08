---
title: Associare un Azure Cosmos DB all'applicazione Azure Spring cloud | Microsoft Docs
description: Informazioni su come associare Azure Cosmos DB all'applicazione Azure Spring cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d051fd431180e9cb86f1df4642fb5e947103c007
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038730"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Esercitazione: Associare un Azure Cosmos DB all'applicazione Azure Spring cloud

Azure Spring cloud consente di associare automaticamente i servizi di Azure Select alle applicazioni, anziché configurare manualmente l'applicazione Spring boot. Questo articolo illustra come associare l'applicazione a un Azure Cosmos DB.

Prerequisiti:
* Istanza distribuita di Azure Spring cloud.  Per iniziare, seguire la [Guida introduttiva](spring-cloud-quickstart-launch-app-cli.md) .
* Un account Azure Cosmos DB con un livello minimo di autorizzazioni di collaboratore.

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB di binding

Azure Cosmos DB dispone di cinque tipi diversi di API che supportano l'associazione:

1. Crea un database di Azure Cosmos DB. Per informazioni sulla creazione del database, [vedere questo articolo](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) . Registrare il nome del database. La nostra è denominata `testdb`.

1. Aggiungere una delle seguenti dipendenze nel `pom.xml` dell'applicazione Spring cloud in base al tipo di API.
    
    #### <a name="api-type-core-sql"></a>Tipo di API: Core (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>Tipo di API: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>Tipo di API: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>Tipo di API: Gremlin (graph)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>Tipo di API: tabella di Azure

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Aggiornare la distribuzione corrente utilizzando `az spring-cloud app update` o creare una nuova distribuzione per questa modifica utilizzando `az spring-cloud app deployment create`.  Questi comandi aggiorneranno o creeranno l'applicazione con la nuova dipendenza.

1. Passare alla pagina del servizio cloud di Azure Spring nel portale di Azure. Si tratta della stessa applicazione aggiornata o distribuita nel passaggio precedente. Individuare il **Dashboard dell'applicazione** e selezionare l'applicazione da associare al Cosmos DB. Selezionare quindi `Service binding` e selezionare il pulsante `Create service binding`. Compilare il modulo, selezionando il **tipo di Binding** `Azure Cosmos DB`, il tipo di API, il nome del database e l'account Azure Cosmos DB.

    > [!NOTE]
    > Se si usa Cassandra, usare uno spazio di chiave per il nome del database.

1. Riavviare l'applicazione selezionando il pulsante **Riavvia** nella pagina dell'applicazione.

1. Per assicurarsi che il servizio sia associato correttamente, selezionare il nome dell'associazione e verificarne i dettagli. Il campo `property` dovrebbe essere simile al seguente:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come associare l'applicazione Azure Spring cloud a un CosmosDB.  Per informazioni su come associare l'applicazione a una cache Redis di Azure, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Associare un'applicazione Azure Spring cloud a una cache Redis di Azure](spring-cloud-tutorial-bind-redis.md).
