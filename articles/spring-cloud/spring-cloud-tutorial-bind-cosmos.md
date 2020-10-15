---
title: Associare un'istanza di Azure Cosmos DB all'applicazione Azure Spring Cloud
description: Informazioni su come associare un'istanza di Azure Cosmos DB all'applicazione Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 20926988d554d562f36587734bd99c34b4731e69
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088721"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Associare un database di Azure Cosmos DB all'applicazione Azure Spring Cloud

**Questo articolo si applica a:** ✔️ Java

Anziché configurare manualmente le applicazioni Spring Boot, è possibile associare automaticamente determinati servizi di Azure alle applicazioni tramite Azure Spring Cloud. Questo articolo illustra come associare l'applicazione a un database di Azure Cosmos DB.

Prerequisiti:

* Un'istanza di Azure Spring Cloud distribuita. Per iniziare, seguire la guida di [avvio rapido sulla distribuzione tramite l'interfaccia della riga di comando di Azure](spring-cloud-quickstart.md).
* Un account Azure Cosmos DB con un livello minimo di autorizzazioni di Collaboratore.

## <a name="bind-azure-cosmos-db"></a>Associare un'istanza di Azure Cosmos DB

Azure Cosmos DB dispone di cinque tipi diversi di API che supportano l'associazione. La procedura seguente mostra come usarli:

1. Crea un database di Azure Cosmos DB. Per informazioni, vedere la guida di avvio rapido sulla [creazione di un database](../cosmos-db/create-cosmosdb-resources-portal.md). 

1. Prendere nota del nome del database. Per questa procedura, il nome del database è **testdb**.

1. Aggiungere una delle dipendenze seguenti al file pom.xml dell'applicazione Azure Spring Cloud. Scegliere la dipendenza appropriata per il tipo di API.

    * Tipo di API: Core (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * Tipo di API: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Tipo di API: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Tipo di API: Gremlin (graph)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * Tipo di API: tabella di Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Usare `az spring-cloud app update` per aggiornare la distribuzione corrente oppure usare `az spring-cloud app deployment create` per crearne una nuova. Questi comandi aggiornano o creano l'applicazione con la nuova dipendenza.

1. Passare alla pagina del servizio Azure Spring Cloud nel portale di Azure. Passare al **Dashboard dell'applicazione** e selezionare l'applicazione da associare ad Azure Cosmos DB. Si tratta della stessa applicazione aggiornata o distribuita nel passaggio precedente.

1. Selezionare **Service binding** (Associazione al servizio), quindi selezionare **Create service binding** (Crea associazione al servizio). Per compilare il modulo, selezionare:
   * Per **Tipo di associazione** specificare il valore **Azure Cosmos DB**.
   * Il tipo di API.
   * Il nome del database.
   * L'account Azure Cosmos DB.

    > [!NOTE]
    > Se si usa Cassandra, usare uno spazio delle chiavi per il nome del database.

1. Riavviare l'applicazione selezionando **Riavvia** nella pagina dell'applicazione.

1. Per verificare la corretta associazione del servizio, selezionare il nome dell'associazione e controllarne i dettagli. Il campo `property` sarà simile all'esempio seguente:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come associare l'applicazione Azure Spring Cloud a un database di Azure Cosmos DB. Per altre informazioni sul binding di servizi all'applicazione, vedere [Eseguire il binding a una cache di Azure per Redis](spring-cloud-tutorial-bind-redis.md).