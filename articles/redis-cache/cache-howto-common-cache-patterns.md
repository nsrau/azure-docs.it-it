<properties 
   pageTitle="Criteri comuni di memorizzazione nella cache con Cache Redis di Azure" 
   description="Informazioni su quando e perché usare Cache Redis di Azure" 
   services="redis-cache" 
   documentationCenter="" 
   authors="Rick-Anderson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="all"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd" 
   ms.date="08/11/2015"
   ms.author="riande"/>

# Criteri comuni di memorizzazione nella cache con Cache Redis di Azure

Questa pagina illustra i vantaggi più comuni dell'uso della cache.

## Ottimizzazione dell'accesso ai dati con la cache

L'uso della cache può accelerare notevolmente l'accesso ai dati rispetto ai tempi necessari per recuperare i dati da un archivio dati. La cache offre elevata velocità effettiva e bassa latenza. Recuperando i dati più usati dalla cache, non solo si accelera l'app, ma è possibile ridurre il carico di accesso ai dati e aumentare la velocità di risposta alle altre query. Archiviando le informazioni nella cache è possibile risparmiare risorse e migliorare la scalabilità man mano che aumentano le richieste per l'applicazione. Poiché potrà recuperare i dati in modo efficiente dalla cache, l'app risponderà molto più velocemente a flussi intermittenti di carico.

## Stato sessione distribuito
Sebbene sia consigliabile evitare l'uso dello stato sessione, alcune applicazioni possono trarre vantaggio in termini di prestazioni e riduzione della complessità dall'uso dei dati dello stato sessione, mentre altre app richiedono in modo esplicito lo stato sessione. Il provider in memoria predefinito per lo stato sessione non consente la scalabilità orizzontale (esecuzione di più istanze del sito Web). Il provider di stato sessione SQL Server ASP.NET consente a più siti Web di usare lo stato sessione, ma comporta costi elevati in termini di latenza rispetto a un provider in memoria. Il provider di cache di stato sessione Redis è un'alternativa a bassa latenza molto semplice da configurare e impostare. Se l'app usa solo una quantità di stato sessione limitata, è possibile usare la maggior parte della cache per la memorizzazione dei dati e una piccola quantità per lo stato sessione.

## Risoluzione dei problemi legati ai tempi di inattività del servizio (fallback nella cache)
 Archiviando i dati nella cache, è possibile evitare problemi all'applicazione in caso di errori di sistema, come latenza di rete, problemi del servizio Web ed errori hardware. Spesso è preferibile rendere disponibili i dati memorizzati nella cache fino a quando non si ripristina il servizio Web o il database, anziché lasciare l'app completamente non funzionante.

## Passaggi successivi
Per altre informazioni sull'uso di Cache Redis di Azure:
 
- [Documentazione relativa a Cache Redis di Azure](http://azure.microsoft.com/documentation/services/cache/): in questa pagina vengono forniti numerosi collegamenti utili relativi all’uso di Cache Redis di Azure.
- [App per filmati MVC con Cache Redis di Azure in 15 minuti](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/): nel post di blog vengono fornite informazioni di avvio rapido per l’uso di Cache Redis di Azure in un’app MVC ASP.NET.
- [Come utilizzare lo stato della sessione ASP.NET con i siti Web di Azure](../app-service-web/web-sites-dotnet-session-state-caching.md): in questo argomento viene illustrato come utilizzare il servizio Cache Redis di Azure per lo stato della sessione.

<!---HONumber=August15_HO7-->