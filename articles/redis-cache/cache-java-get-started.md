---
title: Come usare Cache Redis di Azure con Java | Documentazione Microsoft
description: Introduzione all&quot;uso di Cache Redis di Azure con Java
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 01/06/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: c42aebb3aaf5c32ebdc4f79e2ace2f127e4fb20d
ms.openlocfilehash: fe875fba2651b770d910d257282f5e9f41f8a043


---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Come usare Cache Redis di Azure con Java
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Cache Redis di Azure consente di accedere a una cache Redis dedicata, gestita da Microsoft. È possibile accedere alla cache da qualsiasi applicazione in Microsoft Azure.

Questo argomento descrive come usare Cache Redis di Azure con Java.

## <a name="prerequisites"></a>Prerequisiti
[Jedis](https://github.com/xetorthio/jedis) : client Java per Redis

In questa esercitazione viene utilizzato Jedis, ma è possibile utilizzare qualsiasi cliente Java elencato in [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Creare una cache Redis in Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperare il nome host e le chiavi di accesso
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Connettersi in modo sicuro alla cache usando SSL
Le build più recenti di [jedis](https://github.com/xetorthio/jedis) offrono il supporto per la connessione a Cache Redis di Azure con SSL. L'esempio seguente mostra come connettersi a Cache Redis di Azure usando l'endpoint SSL 6380. Sostituire `<name>` con il nome della cache e `<key>` con la chiave primaria o secondaria, come illustrato nella sezione [Recuperare il nome host e le chiavi di accesso](#retrieve-the-host-name-and-access-keys) precedente.

    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */


## <a name="add-something-to-the-cache-and-retrieve-it"></a>Aggiungere dati alla cache e recuperarli
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    public class App
    {
      public static void main( String[] args )
      {
        boolean useSsl = true;
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379, useSsl);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Passaggi successivi
* [Abilitare la diagnostica della cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) per [monitorare](https://msdn.microsoft.com/library/azure/dn763945.aspx) l'integrità della cache.
* Leggere la [documentazione ufficiale di Redis](http://redis.io/documentation).



<!--HONumber=Dec16_HO3-->


