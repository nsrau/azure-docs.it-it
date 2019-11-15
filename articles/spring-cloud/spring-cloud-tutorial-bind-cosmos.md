---
title: Associare un'istanza di Azure Cosmos DB all'applicazione Azure Spring Cloud | Microsoft Docs
description: Informazioni su come associare un'istanza di Azure Cosmos DB all'applicazione Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 76a2b9f305f041a19b8d7ace8234a804825f6a0e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607121"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Esercitazione: Associare un'istanza di Azure Cosmos DB all'applicazione Azure Spring Cloud

Azure Spring Cloud consente di associare automaticamente determinati servizi di Azure alle applicazioni, invece di configurare manualmente l'applicazione Spring Boot. Questo articolo illustra come associare l'applicazione a un'istanza di Azure Cosmos DB.

Prerequisiti:
* Un'istanza di Azure Spring Cloud distribuita.  Per iniziare, seguire la [guida dell'avvio rapido](spring-cloud-quickstart-launch-app-cli.md).
* Un account Azure Cosmos DB con un livello minimo di autorizzazioni di collaboratore.

## <a name="bind-azure-cosmos-db"></a>Associare un'istanza di Azure Cosmos DB

Azure Cosmos DB dispone di cinque tipi diversi di API che supportano l'associazione:

1. Crea un database di Azure Cosmos DB. Per informazioni su come creare il database, [vedere questo articolo](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal). Prendere nota del nome del database. Quello usato qui è denominato `testdb`.

1. Aggiungere una delle dipendenze seguenti nel file `pom.xml` dell'applicazione Spring cloud in base al tipo di API.
    
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

1. Aggiornare la distribuzione corrente usando `az spring-cloud app update` oppure crearne una nuova per questa modifica usando `az spring-cloud app deployment create`.  Questi comandi aggiornano o creano l'applicazione con la nuova dipendenza.

1. Passare alla pagina del servizio Azure Spring Cloud nel portale di Azure. Si tratta della stessa applicazione aggiornata o distribuita nel passaggio precedente. Trovare il **Dashboard dell'applicazione** e selezionare l'applicazione da associare all'istanza di Azure Cosmos DB. Selezionare quindi `Service binding` e poi il pulsante `Create service binding`. Compilare il modulo, selezionare il **tipo di associazione** `Azure Cosmos DB`, il tipo di API, il nome del database e l'account Azure Cosmos DB.

    > [!NOTE]
    > Se si usa Cassandra, usare uno spazio delle chiavi per il nome del database.

1. Riavviare l'applicazione selezionando il pulsante **Riavvia** nella pagina dell'applicazione.

1. Per verificare la corretta associazione del servizio, selezionare il nome dell'associazione e controllarne i dettagli. Il campo `property` sarà simile al seguente:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come associare l'applicazione Azure Spring Cloud a un'istanza di Azure Cosmos DB.  Per informazioni su come associare l'applicazione a una Cache Redis di Azure, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Associare un'applicazione Azure Spring Cloud a una Cache Redis di Azure](spring-cloud-tutorial-bind-redis.md).
