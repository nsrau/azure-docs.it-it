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
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# Come scalare Cache Redis di Azure

>[AZURE.NOTE]Al momento la funzionalità di ridimensionamento di Cache Redis di Azure è in anteprima. Durante il periodo di anteprima, non è possibile ridimensionare, da o in una cache di livello premium.

Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache. Se i requisiti dell'applicazione cambiano dopo la creazione di una cache, è possibile scalare le dimensioni della cache utilizzando il pannello **Modifica livello di prezzo** nel [portale di anteprima di Azure](https://portal.azure.com).

## Quando è necessario scalare

È possibile utilizzare le funzionalità di [monitoraggio](cache-how-to-monitor.md) di Cache Redis di Azure per monitorare l'integrità e le prestazioni delle applicazioni nella cache e per determinare se è necessario scalare la cache.

È possibile monitorare le metriche seguenti per determinare se è necessario scalare.

-	Carico server Redis
-	Utilizzo della memoria
-	Larghezza di banda della rete
-	Utilizzo di CPU

Se si determina che la cache non soddisfa più i requisiti dell'applicazione, è possibile passare a un livello di prezzo della cache superiore o inferiore che sia adatto all'applicazione. Per ulteriori informazioni su come determinare quale tipo di livello di prezzo di cache utilizzare, vedere l’articolo relativo all’[offerta e alle dimensioni di Cache Redis da utilizzare](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Scalare una cache
Per ridimensionare la cache [Sfoglia alla cache](cache-configure.md#configure-redis-cache-settings) nel [portale di anteprima](https://portal.azure.com) e fare clic su **Impostazioni**, **Livello prezzi**.

È inoltre possibile scegliere il **livello Standard** o il **livello Basic** nel pannello **Cache Redis**.

![Pricing tier][redis-cache-pricing-tier-part]

Selezionare il livello di prezzo desiderato dal pannello **Livello di prezzo** e fare clic su **Seleziona**.

![Pricing tier][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]È possibile passare a un livello di prezzo diverso con le restrizioni seguenti.
>
>-	Non è possibile scalare da o verso una cache **Premium**.
>-	Non è possibile effettuare il ridimensionamento da una cache**Standard** a una cache **Basic**.
>-	È possibile passare da una cache **Basic** a una cache **Standard** ma non è possibile modificare contemporaneamente le dimensioni. Se occorre una dimensione diversa, è possibile effettuare successivamente un'operazione di scalabilità per le dimensioni desiderate.
>-	Non è possibile passare da una dimensione maggiore alla dimensione **C0 (250 MB)**.

Mentre viene eseguito il ridimensionamento della cache al nuovo livello di prezzo, nel pannello **Cache Redis** viene visualizzato uno stato **Ridimensionamento**.

![Ridimensionamento][redis-cache-scaling]

Quando il ridimensionamento è completato, lo stato passa da **Ridimensionamento** a **In esecuzione**.

## Come automatizzare un’operazione di ridimensionamento

Oltre a scalare l'istanza di Cache Redis di Azure nel portale di anteprima, è possibile eseguire il ridimensionamento mediante le [librerie di gestione di Microsoft Azure (MAML)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/). Per scalare la cache, chiamare il metodo `IRedisOperations.CreateOrUpdate` e passare la nuova dimensione per `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
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

## È possibile ridimensionare verso, da o in una cache Premium

Durante il periodo di anteprima, la scalabilità non è disponibile per cache **Premium**.

## Dopo il ridimensionamento, è necessario modificare il nome della cache o le chiavi di accesso?

No, il nome della cache e le chiavi restano invariate durante un'operazione di ridimensionamento.

## Come funziona il ridimensionamento?

Quando viene ridimensionata una cache **Basic**, essa viene chiusa e viene eseguito il provisioning di una nuova cache utilizzando la nuova dimensione. Durante questo periodo, la cache non è disponibile e tutti i dati nella cache vengono persi.

Quando una cache **Basic** viene ridimensionata in una cache **Standard**, viene eseguito il provisioning di una cache di replica e i dati vengono copiati dalla cache principale alla cache di replica. La cache rimane disponibile durante il processo di ridimensionamento.

Quando viene scalata una cache **Standard**, una delle repliche viene arrestata e ne viene rieseguito il provisioning alla nuova dimensione. I dati vengono trasferiti in questa replica, quindi si esegue il failover dell'altra replica prima di rieseguirne il provisioning, in modo simile a quanto accade nel processo che si verifica durante un errore di uno dei nodi della cache.

## Durante il ridimensionamento i miei dati dalla cache andranno persi?

Quando viene scalata una cache **Basic**, tutti i dati vengono persi e la cache non è disponibile durante l'operazione di ridimensionamento.

Quando una cache **Basic** viene ridimensionata in una cache**Standard**, generalmente i dati nella cache vengono mantenuti.

Quando si scala una cache **Standard** a una dimensione superiore, in genere vengono mantenuti tutti i dati. Quando si scala una cache **Standard** a una dimensione inferiore, i dati potrebbero andare persi in base al rapporto tra la quantità di dati nella cache e alla nuova dimensione di questa. Se durante la riduzione i dati vengono persi, le chiavi vengono rimosse mediante il criterio di rimozione [allkeys-lru](http://redis.io/topics/lru-cache).

Si noti che mentre le cache Standard dispongono di un contratto di servizio pari al 99,9% di disponibilità, non esiste un contratto di servizio per la perdita dei dati.

## La cache sarà disponibile durante il ridimensionamento?

Le cache **Standard** restano disponibili durante l'operazione di ridimensionamento.

Le cache**Basic** sono offline durante le operazioni di ridimensionamento, ma rimangono disponibili anche quando si ridimensiona da una cache **Basic** a una **Standard**.

## Operazioni non supportate

Non è possibile ridimensionare verso, da o in una cache **Premium**.

Non è possibile passare da una cache**Standard** a una cache **Basic**.

È possibile passare da una cache **Basic** a una cache **Standard** ma non è possibile modificare contemporaneamente le dimensioni. Se occorre una dimensione diversa, è possibile effettuare successivamente un'operazione di scalabilità per le dimensioni desiderate.

È possibile aumentare una cache **C0** (250 MB) a una dimensione superiore, ma non è possibile ridurre una cache di dimensioni superiori a una cache **C0**.

Se l’operazione di ridimensionamento non riesce, il servizio ripristina l’operazione e la cache ritorna alla dimensione originale.

## Quanto tempo richiede il ridimensionamento?

Il ridimensionamento richiede circa 20 minuti, a seconda della quantità di dati nella cache.

## Come è possibile stabilire quando il ridimensionamento è completato?

Nel portale di anteprima è possibile visualizzare l'operazione di ridimensionamento in corso. Quando il ridimensionamento è completo, lo stato della cache passa a **In esecuzione**.

## Perché questa funzionalità è in anteprima?

Questa funzionalità viene rilasciata allo scopo di ottenere commenti e suggerimenti. In base ai commenti, questa funzionalità sarà rilasciata a breve per la disponibilità generale.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=Oct15_HO1-->