<properties
	pageTitle="Come usare Cache Redis di Azure con Node.js | Microsoft Azure"
	description="Introduzione all'uso di Cache Redis di Azure con Node.js e node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/16/2016"
	ms.author="sdanie"/>

# Come usare Cache Redis di Azure con Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Cache Redis di Azure consente di accedere a una cache Redis sicura e dedicata, gestita da Microsoft. È possibile accedere alla cache da qualsiasi applicazione in Microsoft Azure.

Questo argomento descrive come usare Cache Redis di Azure con Node.js. Per un altro esempio dell'uso di Cache Redis di Azure con Node,js, vedere [Creare un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## Prerequisiti

Installare [node\_redis](https://github.com/mranney/node_redis):

    npm install redis

Questa esercitazione usa [node\_redis](https://github.com/mranney/node_redis). Per esempi relativi all'uso di altri client Node.js, vedere la documentazione specifica dei client Node.js disponibile in [Node.js Redis clients](http://redis.io/clients#nodejs) (Client Redis Node.js).

## Creare una cache Redis in Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Recuperare il nome host e le chiavi di accesso

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## Connettersi in modo sicuro alla cache usando SSL

Le build più recenti di [node\_redis](https://github.com/mranney/node_redis) forniscono il supporto per la connessione a Cache Redis di Azure con SSL. L'esempio seguente mostra come connettersi a Cache Redis di Azure usando l'endpoint SSL 6380. Sostituire `<name>` con il nome della cache e `<key>` con la chiave primaria o secondaria, come illustrato nella sezione [Recuperare il nome host e le chiavi di accesso](#retrieve-the-host-name-and-access-keys) precedente.

	 var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## Aggiungere dati alla cache e recuperarli

L'esempio seguente mostra come connettersi a un'istanza di Cache Redis di Azure e come archiviare e recuperare un elemento dalla cache. Per altri esempi relativi all'uso di Redis con il client [node\_redis](https://github.com/mranney/node_redis), vedere [http://redis.js.org/](http://redis.js.org/).

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


## Passaggi successivi

- [Abilitare la diagnostica della cache](cache-how-to-monitor.md#enable-cache-diagnostics) per poter [monitorare](cache-how-to-monitor.md) l'integrità della cache.
- Leggere la [documentazione ufficiale di Redis](http://redis.io/documentation).

<!---HONumber=AcomDC_0817_2016-->