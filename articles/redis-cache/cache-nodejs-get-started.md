<properties
	pageTitle="Come usare Cache Redis di Azure con Node.js | Microsoft Azure"
	description="Introduzione all'uso di Cache Redis di Azure con Node.js e node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="10/05/2015"
	ms.author="sdanie"/>

# Come usare Cache Redis di Azure con Node.js

Cache Redis di Azure consente di accedere a una cache Redis sicura e dedicata, gestita da Microsoft. È possibile accedere alla cache da qualsiasi applicazione in Microsoft Azure.

Questo argomento descrive come usare Cache Redis di Azure con Node.js. Per un altro esempio dell'uso di Cache Redis di Azure con Node,js, vedere [Creare un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure][].


## Prerequisiti

Installare [node\_redis](https://github.com/mranney/node_redis):

    npm install redis

In questa esercitazione si utilizza [node\_redis](https://github.com/mranney/node_redis), ma è possibile usare qualsiasi client Node.js elencato in [http://redis.io/clients](http://redis.io/clients).

## Creare una cache Redis in Azure

Nel [portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=398536) fare clic su **Nuovo**, **Dati + Archiviazione** e selezionare **Cache Redis**.

  ![][1]

Immettere un nome host DNS Avrà il formato `<name>.redis.cache.windows.net`. Fare clic su **Crea**.

  ![][2]


Dopo aver creato la cache, fare clic su di essa nel portale di anteprima per visualizzarne le impostazioni. Fare clic sul collegamento in **Chiavi** e copiare la chiave primaria. Tale chiave sarà necessaria per autenticare le richieste.

  ![][4]


## Abilitare l'endpoint non SSL


Fare clic sul collegamento in **Porte**, quindi su **No** per "Consenti l’accesso solo tramite SSL". In questo modo, la porta non SSL viene abilitata per la cache. Il client node\_redis attualmente non supporta SSL.

  ![][3]


## Aggiungere dati alla cache e recuperarli

	var redis = require("redis");

    // Add your cache name and access key.
	var client = redis.createClient(6379,'<name>.redis.cache.windows.net', {auth_pass: '<key>' });

	client.set("foo", "bar", function(err, reply) {
	    console.log(reply);
	});

	client.get("foo",  function(err, reply) {
	    console.log(reply);
	});


Output:

	OK
	bar


## Passaggi successivi

- [Abilitare la diagnostica della cache](cache-how-to-monitor.md#enable-cache-diagnostics) per poter [monitorare](cache-how-to-monitor.md) l'integrità della cache.
- Leggere la [documentazione ufficiale di Redis](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Creare un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=Oct15_HO3-->