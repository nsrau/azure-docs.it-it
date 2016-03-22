<properties
	pageTitle="Come usare Cache Redis di Azure con Node.js | Microsoft Azure"
	description="Introduzione all'uso di Cache Redis di Azure con Node.js e node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="03/09/2016"
	ms.author="sdanie"/>

# Come usare Cache Redis di Azure con Node.js

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Cache Redis di Azure consente di accedere a una cache Redis sicura e dedicata, gestita da Microsoft. È possibile accedere alla cache da qualsiasi applicazione in Microsoft Azure.

Questo argomento descrive come usare Cache Redis di Azure con Node.js. Per un altro esempio dell'uso di Cache Redis di Azure con Node,js, vedere [Creare un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure][].


## Prerequisiti

Installare [node\_redis](https://github.com/mranney/node_redis):

    npm install redis

In questa esercitazione si utilizza [node\_redis](https://github.com/mranney/node_redis), ma è possibile usare qualsiasi client Node.js elencato in [http://redis.io/clients](http://redis.io/clients).

## Creare una cache Redis in Azure

Nel [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=398536) fare clic su **Nuovo**, **Dati e archiviazione** e selezionare **Cache Redis**.

  ![][1]

Immettere un nome host DNS Avrà il formato `<name>.redis.cache.windows.net`. Fare clic su **Crea**.

  ![][2]


  Dopo avere creato la cache, [individuarla](cache-configure.md#configure-redis-cache-settings) per visualizzare le impostazioni. Fare clic in **Chiavi** e copiare la chiave principale. Tale chiave sarà necessaria per autenticare le richieste.

  ![][4]

  ## Aggiungere dati alla cache e recuperarli

```js
  var redis = require("redis");

  // Add your cache name and access key.
var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

client.set("key1", "value", function(err, reply) {
	    console.log(reply);
	});

client.get("key1",  function(err, reply) {
	    console.log(reply);
	});
```

Output:

	OK
	value


## Passaggi successivi

- [Abilitare la diagnostica della cache](cache-how-to-monitor.md#enable-cache-diagnostics) per poter [monitorare](cache-how-to-monitor.md) l'integrità della cache.
- Leggere la [documentazione ufficiale di Redis](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Creare un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=AcomDC_0316_2016-->