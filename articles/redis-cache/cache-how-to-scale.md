<properties 
	pageTitle="Come scalare Cache Redis di Azure" 
	description="Informazioni su come ridimensionare le istanze di Cache Redis di Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Come scalare Cache Redis di Azure

>[AZURE.NOTE]Al momento la funzionalità di ridimensionamento di Cache Redis di Azure è in anteprima.

Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache. Se i requisiti dell'applicazione cambiano dopo la creazione di una cache, è possibile scalare le dimensioni della cache utilizzando il pannello **Modifica livello di prezzo** nel [portale di Azure](https://portal.azure.com).

>[AZURE.NOTE]Quando si scala una Cache Redis di Azure è possibile modificare le dimensioni, ma non è possibile passare da una cache Standard a una Basic e viceversa.

## Quando è necessario scalare

È possibile utilizzare le funzionalità di [monitoraggio](cache-how-to-monitor.md) di Cache Redis di Azure per monitorare l'integrità e le prestazioni delle applicazioni nella cache e per determinare se è necessario scalare la cache.

È possibile monitorare le metriche seguenti per determinare se è necessario scalare.

-	Carico server Redis
-	Utilizzo della memoria
-	Larghezza di banda della rete
-	Utilizzo di CPU

Se si determina che la cache non soddisfa più i requisiti dell'applicazione, è possibile passare a un livello di prezzo della cache superiore o inferiore che sia adatto all'applicazione. Per ulteriori informazioni su come determinare quale tipo di livello di prezzo di cache utilizzare, vedere l’articolo relativo all’[offerta e alle dimensioni di Cache Redis da utilizzare](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Scalare una cache
Per scalare la cache, [cercare la cache](https://msdn.microsoft.com/library/azure/dn793612.aspx#RedisCacheConfiguration) nel [portale di Azure](https://portal.azure.com) e fare clic su **Livello Standard** o su **Livello Basic** nel pannello **Cache Redis**.

![Pricing tier][redis-cache-pricing-tier-part]

Selezionare il livello di prezzo desiderato dal pannello **Livello di prezzo** e fare clic su **Seleziona**.

![Pricing tier][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]Le cache non possono modificare i piani da Basic a Standard o viceversa, e non è possibile ridurre a 250 MB una cache di dimensioni maggiori. È possibile aumentare fino a 250 MB una cache, ma non sarà possibile ritornare a un livello di prezzo di 250 MB. Se si desidera passare da Basic a Standard o ridurre la cache a 250 MB, è necessario creare una nuova cache.

Mentre viene eseguito il ridimensionamento della cache al nuovo livello di prezzo, nel pannello **Cache Redis** viene visualizzato uno stato **Ridimensionamento**.

![Ridimensionamento][redis-cache-scaling]

Quando il ridimensionamento è completato, lo stato passa da **Ridimensionamento** a **In esecuzione**.

>[AZURE.IMPORTANT]Durante le operazioni di ridimensionamento, le cache Basic sono offline e tutti i dati nella cache vengono persi. Una volta completata l'operazione di ridimensionamento, la cache Basic sarà nuovamente online, senza dati. Le cache Standard restano online durante un'operazione di ridimensionamento e in genere non viene perso alcun dato quando si esegue il ridimensionamento da una cache Standard a una di dimensioni maggiori. Quando si esegue il ridimensionamento da una cache Standard a una di dimensioni inferiori, alcuni dati potrebbero andare persi se le nuove dimensioni sono inferiori alla quantità di dati memorizzati nella cache. Se durante la riduzione i dati vengono persi, le chiavi vengono rimosse mediante il criterio di rimozione [allkeys lru](http://redis.io/topics/lru-cache). Si noti che mentre le cache Standard dispongono di un contratto di servizio pari al 99,9% di disponibilità, non esiste un contratto di servizio per la perdita dei dati.

## Come automatizzare un’operazione di ridimensionamento

Oltre a scalare l'istanza di Cache Redis di Azure nel portale di Azure, è possibile eseguire il ridimensionamento mediante le [librerie di gestione di Microsoft Azure ](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/). Per scalare la cache, chiamare il metodo `IRedisOperations.CreateOrUpdate` e passare la nuova dimensione per `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://msdn.microsoft.com/it-it/library/azure/dn790557.aspx#bk_portal
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Per ulteriori informazioni, vedere l’esempio relativo alla [gestione di Cache Redis Cache tramite MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML).

## Domande frequenti relative al ridimensionamento

Nell’elenco seguente sono fornite le risposte alle domande poste comunemente sul ridimensionamento di Cache Redis di Azure.

## Dopo il ridimensionamento, è necessario modificare il nome della cache o le chiavi di accesso?

No, il nome della cache e le chiavi restano invariate durante un'operazione di ridimensionamento.

## Come funziona il ridimensionamento?

Quando viene ridimensionata una cache **Basic**, viene chiusa e viene eseguito il provisioning di una nuova cache utilizzando la nuova dimensione. Durante questo periodo, la cache non è disponibile e tutti i dati nella cache vengono persi.

Quando viene scalata una cache **Standard**, una delle repliche viene arrestata e ne viene rieseguito il provisioning alla nuova dimensione. I dati vengono trasferiti in questa replica, quindi si esegue il failover dell'altra replica prima di rieseguirne il provisioning, in modo simile a quanto accade nel processo che si verifica durante un errore di uno dei nodi della cache.

## Durante il ridimensionamento i miei dati dalla cache andranno persi?

Quando viene scalata una cache **Basic**, tutti i dati vengono persi e la cache non è disponibile durante l'operazione di ridimensionamento.

Quando si scala una cache **Standard** a una dimensione superiore, in genere vengono mantenuti tutti i dati. Quando si scala una cache **Standard** a una dimensione inferiore, i dati potrebbero andare persi in base al rapporto tra la quantità di dati nella cache e alla nuova dimensione di questa. Se durante la riduzione i dati vengono persi, le chiavi vengono rimosse mediante il criterio di rimozione [allkeys-lru](http://redis.io/topics/lru-cache). Si noti che mentre le cache Standard dispongono di un contratto di servizio pari al 99,9% di disponibilità, non esiste un contratto di servizio per la perdita dei dati.

## La cache sarà disponibile durante il ridimensionamento?

Le cache **Standard** restano disponibili durante l'operazione di ridimensionamento.

Le cache **Base** sono offline durante l'operazione di ridimensionamento.

## Operazioni non supportate

Non è possibile passare da una cache **Basic** a una cache **Standard** o viceversa durante l’operazione di ridimensionamento.

È possibile aumentare una cache **C0** (250 MB) a una dimensione superiore, ma non è possibile ridurre una cache di dimensioni superiori a una cache **C0**.

Se l’operazione di ridimensionamento non riesce, il servizio ripristina l’operazione e la cache ritorna alla dimensione originale.

## Quanto tempo richiede il ridimensionamento?

Il ridimensionamento richiede circa 20 minuti, a seconda della quantità di dati nella cache.

## Come è possibile stabilire quando il ridimensionamento è completato?

Nel portale è possibile visualizzare l'operazione di ridimensionamento in corso. Quando il ridimensionamento è completo, lo stato della cache passa a **In esecuzione**.

## Perché questa funzionalità è in anteprima?

Questa funzionalità viene rilasciata allo scopo di ottenere commenti e suggerimenti. In base ai commenti, questa funzionalità sarà rilasciata a breve per la disponibilità generale.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=July15_HO4-->