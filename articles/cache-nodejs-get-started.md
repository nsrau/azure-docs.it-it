<properties 
   pageTitle="Come usare Cache Redis di Azure con Node.js" 
   description="Introduzione all'uso di Cache Redis di Azure con Node.js e node_redis." 
   services="redis-cache" 
   documentationCenter="" 
   authors="MikeWasson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required" 
   ms.date="02/23/2015"
   ms.author="mwasson"/>

# Come usare Cache Redis di Azure con Node.js 

Cache Redis di Azure consente di accedere a una cache Redis sicura e dedicata, gestita da Microsoft. È possibile accedere alla cache da qualsiasi applicazione in Microsoft Azure.

Questo argomento descrive come usare Cache Redis di Azure con Node.js. Per un altro esempio di come usare Cache Redis di Azure con Node,js, vedere [Creare un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure][].


## Prerequisiti

Installare [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Questa esercitazione usa [node_redis](https://github.com/mranney/node_redis), ma è possibile usare qualsiasi client Node.js tra quelli elencati in [http://redis.io/clients](http://redis.io/clients).

## Creare una cache Redis in Azure

Nella [versione di anteprima del portale di gestione di Azure](http://go.microsoft.com/fwlink/?LinkId=398536) fare clic su **Nuovo** e selezionare **Cache Redis**. 

  ![][1]

Immettere un nome host DNS nel formato `<nome>.redis.cache.windows.net`. Fare clic su **Crea**.

  ![][2]


Dopo aver creato la cache, fare clic su di essa nel portale per visualizzarne le impostazioni. Fare clic sul collegamento sotto **Chiavi** e copiare la chiave primaria. Tale chiave sarà necessaria per autenticare le richieste.

  ![][4]


## Abilitare l'endpoint non SSL


Fare clic sul collegamento sotto **Porte** e quindi su **No** per "Consenti l'accesso solo tramite SSL". In questo modo, la porta non SSL verrà abilitata per la cache. Il client node_redis attualmente non supporta SSL.

  ![][3]


## Aggiungere dati alla cache e recuperarli

	var redis = require("redis");
	
    // Put in your cache name and access key.
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

- [Abilitare la diagnostica della cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) per poter [monitorare](https://msdn.microsoft.com/library/azure/dn763945.aspx) l'integrità della cache. 
- Leggere la [documentazione ufficiale di Redis](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

[Creare un'applicazione di chat Node.js con Socket.IO in un sito Web di Azure]: web-sites-nodejs-chat-app-socketio.md


<!--HONumber=49-->