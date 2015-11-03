<properties
   pageTitle="Come usare Cache Redis di Azure con Java | Microsoft Azure"
	description="Introduzione all'uso di Cache Redis di Azure con Java"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="10/23/2015"
	ms.author="sdanie"/>

# Come usare Cache Redis di Azure con Java

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Cache Redis di Azure consente di accedere a una cache Redis dedicata, gestita da Microsoft. È possibile accedere alla cache da qualsiasi applicazione in Microsoft Azure.

Questo argomento descrive come usare Cache Redis di Azure con Java.


## Prerequisiti

[Jedis](https://github.com/xetorthio/jedis): client Java per Redis

In questa esercitazione viene utilizzato Jedis, ma è possibile utilizzare qualsiasi cliente Java elencato in [http://redis.io/clients](http://redis.io/clients).


## Creare una cache Redis in Azure

Nel [portale di anteprima di Azure](http://go.microsoft.com/fwlink/?LinkId=398536) fare clic su **Nuovo**, **Dati e archiviazione** e selezionare **Cache Redis**.

  ![][1]

Immettere un nome host DNS Avrà il formato `<name>.redis.cache.windows.net`. Fare clic su **Crea**.

  ![][2]


Dopo aver creato la cache, fare clic su di essa nel portale di anteprima per visualizzarne le impostazioni. Fare clic sul collegamento in **Chiavi** e copiare la chiave primaria. Tale chiave sarà necessaria per autenticare le richieste.

  ![][4]


## Abilitare l'endpoint non SSL


Fare clic sul collegamento in **Porte**, quindi su **No** per "Consenti l’accesso solo tramite SSL". In questo modo, la porta non SSL viene abilitata per la cache. Il client Jedis attualmente non supporta SSL.

  ![][3]


## Aggiungere dati alla cache e recuperarli

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the preview portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	}


## Passaggi successivi

- [Abilitare la diagnostica della cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) per poter [monitorare](https://msdn.microsoft.com/library/azure/dn763945.aspx) l'integrità della cache.
- Leggere la [documentazione ufficiale di Redis](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

<!---HONumber=Nov15_HO1-->