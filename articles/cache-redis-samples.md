<properties 
	pageTitle="Esempi di Cache Redis di Azure" 
	description="Informazioni su come usare Cache Redis di Azure." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="sdanie"/>

# Esempi di Cache Redis di Azure 

Questo argomento fornisce un elenco di esempi di Cache Redis di Azure relativi a scenari come la connessione a una cache, la lettura e la scrittura di dati nella cache e l'uso di provider di Cache Redis ASP.NET. Alcuni esempi sono progetti scaricabili e alcuni forniscono informazioni dettagliate e includono frammenti di codice, ma non un collegamento a un progetto scaricabile.

## Esempi Hello World

Gli esempi in questa sezione illustrano le nozioni di base della connessione a un'istanza di Cache Redis di Azure, nonché della lettura e della scrittura di dati nella cache con numerosi linguaggi e client Redis.

L'esempio [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) mostra come leggere e scrivere elementi in una cache usando il client .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

[Come usare Cache Redis di Azure con Node.js](cache-nodejs-get-started.md) mostra come usare Cache Redis di Azure con Node.js e il client [node_redis](https://github.com/mranney/node_redis).

[Come usare Cache Redis di Azure con Java](cache-java-get-started.md) mostra come usare Cache Redis di Azure con Java e il client [Jedis](https://github.com/xetorthio/jedis).

[Come usare Cache Redis di Azure con Python](cache-python-get-started.md) mostra come usare Cache Redis di Azure con Python e il client [redis-py](https://github.com/andymccurdy/redis-py).

L'[esempio PHP](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample) mostra come usare Cache Redis di Azure con PHP e il client [predis](https://github.com/nrk/predis).

[Gestire gli oggetti .NET nella cache](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects) mostra come serializzare gli oggetti .NET per poterli scrivere e leggere in un'istanza di Cache Redis di Azure. 

## Usare Cache Redis come backplane con scalabilità orizzontale per ASP.NET SignalR

L'esempio [Usare Cache Redis come backplane con scalabilità orizzontale per ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) mostra come usare Cache Redis di Azure come backplane SignalR. Per altre informazioni sul backplane, vedere l'argomento relativo a [scalabilità orizzontale di SignalR con Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## Esempio di query dei clienti su Cache Redis

Questo esempio mette a confronto le prestazioni di accesso ai dati da una cache e di accesso ai dati da un archivio di salvataggio permanente. L'esempio contiene due progetti.

-	[Demo su come Cache Redis può migliorare le prestazioni grazie alla memorizzazione dei dati nella cache](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[Effettuare il seeding del database e della cache per la demo](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## Memorizzazione nella cache dell'output e dello stato sessione ASP.NET

L'esempio relativo all'[uso di Cache Redis di Azure per archiviare SessionState e OutputCache ASP.NET](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) mostra come usare Cache Redis di Azure per archiviare la cache di output e la sessione ASP.NET usando i provider SessionState e OutputCache per Redis.

## Gestire Cache Redis di Azure con MAML

L'esempio relativo alla [gestione di Cache Redis di Azure con le librerie di gestione di Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) mostra come usare le librerie di gestione di Azure per gestire (creare/aggiornare/eliminare) la cache. 

## Esempio di monitoraggio personalizzato

L'esempio relativo all'[accesso ai dati di monitoraggio di Cache Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) mostra come accedere ai dati di monitoraggio per Cache Redis di Azure all'esterno del portale di Azure.



<!--HONumber=49-->