---
title: "Esercitazione: Come associare Cache di Azure per Redis all'applicazione Azure Spring Cloud"
description: Questa esercitazione mostra come associare Cache di Azure per Redis all'applicazione Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 1653db3619fd569238872ca1fcfd6d0c439e84c9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708777"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Esercitazione: Associare i servizi di Azure all'applicazione Azure Spring Cloud: Cache Redis di Azure

Azure Spring Cloud consente di associare automaticamente determinati servizi di Azure alle applicazioni, invece di configurare manualmente l'applicazione Spring Boot. Questo articolo illustra come associare l'applicazione a Cache di Azure per Redis.

## <a name="prerequisites"></a>Prerequisiti

* Un'istanza di Azure Spring Cloud distribuita
* Un'istanza del servizio Cache di Azure per Redis
* Estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure

Se non si ha un'istanza di Azure Spring Cloud distribuita, seguire la procedura descritta in questo [Avvio rapido](spring-cloud-quickstart-launch-app-portal.md) per distribuire la prima app Spring Cloud.

## <a name="bind-azure-cache-for-redis"></a>Associare Cache di Azure per Redis

1. Aggiungere la dipendenza seguente al file `pom.xml` del progetto

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Rimuovere le proprietà `spring.redis.*`, se presenti, dal file `application.properties`

1. Aggiornare la distribuzione corrente usando `az spring-cloud app update` oppure crearne una nuova usando `az spring-cloud app deployment create`.

1. Passare alla pagina del servizio Azure Spring Cloud nel portale di Azure. Trovare il **Dashboard dell'applicazione** e selezionare l'applicazione da associare a Cache di Azure per Redis.  Si tratta della stessa applicazione aggiornata o distribuita nel passaggio precedente. Scegliere quindi `Service binding` e selezionare il pulsante `Create service binding`. Compilare il modulo, assicurandosi di selezionare il **tipo di binding** `Azure Cache for Redis`, il server Redis e l'opzione della chiave primaria. 

1. Riavviare l'app. Il binding dovrebbe funzionare ora.

1. Per verificare se il binding di servizi è corretto, selezionarne il nome e controllarne i dettagli. Il campo `property` dovrà essere simile al seguente:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come associare l'applicazione Azure Spring Cloud a Cache di Azure per Redis.  Per altre informazioni sul binding di servizi all'applicazione, continuare con il tutorial su come associare un'applicazione a un database MySQL.

> [!div class="nextstepaction"]
> [Informazioni su come associare un servizio Azure MySQL al servizio Azure Spring Cloud](spring-cloud-tutorial-bind-mysql.md).