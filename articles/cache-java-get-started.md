<properties 
   pageTitle="Come usare Cache Redis di Azure con Java" 
   description="Introduzione all'uso di Cache Redis di Azure con Java" 
   services="redis-cache" 
   documentationCenter="" 
   authors="MikeWasson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required" 
   ms.date="02/20/2015"
   ms.author="mwasson"/>

# Come usare Cache Redis di Azure con Java 

Cache Redis di Azure consente di accedere a una cache Redis sicura e dedicata, gestita da Microsoft. È possibile accedere alla cache da qualsiasi applicazione in Microsoft Azure.

Questo argomento descrive come usare Cache Redis di Azure con Java.


## Prerequisiti

[Jedis](https://github.com/xetorthio/jedis) - Client Java per Redis

Questa esercitazione usa Jedis, ma è possibile usare qualsiasi client Java tra quelli elencati in [http://redis.io/clients](http://redis.io/clients).


## Creare una cache Redis in Azure

Nella [versione di anteprima del portale di gestione di Azure](http://go.microsoft.com/fwlink/?LinkId=398536) fare clic su **Nuovo** e selezionare **Cache Redis**. 

  ![][1]

Immettere un nome host DNS nel formato `<nome>.redis.cache.windows.net`. Fare clic su **Crea**.

  ![][2]


Dopo aver creato la cache, fare clic su di essa nel portale per visualizzarne le impostazioni. Fare clic sul collegamento sotto **Chiavi** e copiare la chiave primaria. Tale chiave sarà necessaria per autenticare le richieste.

  ![][4]


## Abilitare l'endpoint non SSL


Fare clic sul collegamento sotto **Porte** e quindi su **No** per "Consenti l'accesso solo tramite SSL". In questo modo, la porta non SSL verrà abilitata per la cache. Il client Jedis attualmente non supporta SSL.

  ![][3]


## Aggiungere dati alla cache e recuperarli

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;
	 
	/* Make sure your turn on non SSL port in Azure Redis using the Configuration section in the Azure portal */
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


<!--HONumber=49-->