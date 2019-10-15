---
title: Come associare Cache di Azure per Redis all'applicazione Azure Spring Cloud | Microsoft Docs
description: Informazioni su come associare Cache di Azure per Redis all'applicazione Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d8fa31207baecc80674fb11b492927800676b8a2
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038240"
---
# <a name="tutorial-bind-azure-services-to-your-azure-spring-cloud-application-azure-cache-for-redis"></a>Esercitazione: Associare i servizi di Azure all'applicazione Azure Spring Cloud: Cache Redis di Azure

Azure Spring Cloud consente di associare automaticamente determinati servizi di Azure alle applicazioni, invece di configurare manualmente l'applicazione Spring Boot. Questo articolo illustra come associare l'applicazione a Cache di Azure per Redis.

## <a name="prerequisites"></a>Prerequisiti

* Un'istanza di Azure Spring Cloud distribuita
* Un'istanza del servizio Cache di Azure per Redis
* Estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure

Se necessario, installare l'estensione Azure Spring Cloud per l'interfaccia della riga di comando di Azure usando il comando seguente:

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

>[!TIP]
> Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo.  Include i comuni strumenti di Azure preinstallati, tra cui le ultime versioni di Git, JDK, Maven e dell'interfaccia della riga di comando di Azure. Se è stato eseguito l'accesso alla sottoscrizione di Azure, avviare [Azure Cloud Shell](https://shell.azure.com) da shell.azure.com.  Per altre informazioni su Azure Cloud Shell, [leggere la documentazione](../cloud-shell/overview.md)

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