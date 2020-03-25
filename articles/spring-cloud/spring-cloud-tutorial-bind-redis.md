---
title: "Esercitazione: Associare la cache di Azure Cache for Redis all'applicazione Azure Spring Cloud"
description: Questa esercitazione mostra come associare Cache di Azure per Redis all'applicazione Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 94f7b5a2363b7c53e0f70500e5a0a8cb6f64e611
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277512"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Associare la cache di Azure Cache for Redis all'applicazione Azure Spring Cloud 

Anziché configurare manualmente le applicazioni Spring Boot, è possibile associare automaticamente determinati servizi di Azure alle applicazioni tramite Azure Spring Cloud. Questo articolo illustra come associare l'applicazione alla cache di Azure Cache for Redis.

## <a name="prerequisites"></a>Prerequisites

* Un'istanza di Azure Spring Cloud distribuita
* Un'istanza del servizio Cache di Azure per Redis
* L'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure

Se non è disponibile un'istanza di Azure Spring Cloud distribuita, seguire la procedura illustrata nella guida di [avvio rapido sulla distribuzione dun'app Azure Spring Cloud](spring-cloud-quickstart-launch-app-portal.md).

## <a name="bind-azure-cache-for-redis"></a>Associare Cache di Azure per Redis

1. Aggiungere la dipendenza seguente al file pom.xml del progetto:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Rimuovere le eventuali proprietà `spring.redis.*` dal file `application.properties`

1. Aggiornare la distribuzione corrente usando `az spring-cloud app update` oppure crearne una nuova usando `az spring-cloud app deployment create`.

1. Passare alla pagina del servizio Azure Spring Cloud nel portale di Azure. Passare al **Dashboard dell'applicazione** e selezionare l'applicazione da associare ad Azure Cache for Redis. Si tratta della stessa applicazione aggiornata o distribuita nel passaggio precedente.

1. Selezionare **Service binding** (Associazione al servizio), quindi selezionare **Create service binding** (Crea associazione al servizio). Compilare il modulo, assicurandosi che per **Tipo di associazione** sia selezionato il valore **Azure Cache for Redis** e di selezionare il server di Azure Cache for Redis in uso e l'opzione **Chiave primaria**.

1. Riavviare l'app. A questo punto, l'associazione dovrebbe funzionare.

1. Per verificare se il binding di servizi è corretto, selezionarne il nome e controllarne i dettagli. Il campo `property` dovrà essere simile al seguente:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come associare l'applicazione Azure Spring Cloud ad Azure Cache for Redis. Per altre informazioni sull'associazione di servizi all'applicazione, continuare con l'esercitazione su come associare un'applicazione a un'istanza di database di Azure per MySQL.

> [!div class="nextstepaction"]
> [Come eseguire l'associazione a un'istanza di database di Azure per MySQL](spring-cloud-tutorial-bind-mysql.md)
