---
title: Come usare Cache Redis di Azure con Node.js | Documentazione Microsoft
description: Introduzione all'uso di Cache Redis di Azure con Node.js e node_redis.
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: f2c448af24e180db58f3ef3d39e90036dda3f7eb
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Come usare Cache Redis di Azure con Node.js
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Cache Redis di Azure consente di accedere a una cache Redis sicura e dedicata, gestita da Microsoft. È possibile accedere alla cache da qualsiasi applicazione in Microsoft Azure.

Questo argomento descrive come usare Cache Redis di Azure con Node.js. 

## <a name="prerequisites"></a>Prerequisiti
Installare [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Questa esercitazione usa [node_redis](https://github.com/mranney/node_redis). Per esempi relativi all'uso di altri client Node.js, vedere la documentazione specifica dei client Node.js disponibile in [Node.js Redis clients](http://redis.io/clients#nodejs)(Client Redis Node.js).

## <a name="create-a-redis-cache-on-azure"></a>Creare una cache Redis in Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperare il nome host e le chiavi di accesso
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Connettersi in modo sicuro alla cache usando SSL
Le build più recenti di [node_redis](https://github.com/mranney/node_redis) offrono il supporto per la connessione a Cache Redis di Azure con SSL. L'esempio seguente mostra come connettersi a Cache Redis di Azure usando l'endpoint SSL 6380. Sostituire `<name>` con il nome della cache e `<key>` con la chiave primaria o secondaria, come illustrato nella sezione [Recuperare il nome host e le chiavi di accesso](#retrieve-the-host-name-and-access-keys) precedente.

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> Per le nuove istanze della Cache Redis di Azure la porta non SSL è disabilitata. Se si usa un client diverso che non supporta SSL, vedere [Come abilitare la porta non SSL](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Aggiungere dati alla cache e recuperarli
L'esempio seguente mostra come connettersi a un'istanza di Cache Redis di Azure e come archiviare e recuperare un elemento dalla cache. Per altri esempi relativi all'uso di Redis con il client [node_redis](https://github.com/mranney/node_redis), vedere [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });

    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Output:

    OK
    value


## <a name="next-steps"></a>Passaggi successivi
* [Abilitare la diagnostica della cache](cache-how-to-monitor.md#enable-cache-diagnostics) per [monitorare](cache-how-to-monitor.md) l'integrità della cache.
* Leggere la [documentazione ufficiale di Redis](http://redis.io/documentation).


