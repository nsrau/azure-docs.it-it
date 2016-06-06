<properties 
	pageTitle="Come ridimensionare Cache Redis di Azure | Microsoft Azure" 
	description="Informazioni su come ridimensionare le istanze di Cache Redis di Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/23/2016" 
	ms.author="sdanie"/>

# Come ridimensionare Cache Redis di Azure

>[AZURE.NOTE] Al momento la funzionalità di ridimensionamento di Cache Redis di Azure è in anteprima.

Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache. Se i requisiti dell'applicazione cambiano dopo la creazione di una cache, è possibile ridimensionare la cache usando il pannello **Modifica piano tariffario** nel [portale di Azure](https://portal.azure.com).

## Quando è necessario ridimensionare la cache

È possibile usare le funzionalità di [monitoraggio](cache-how-to-monitor.md) di Cache Redis di Azure per monitorare l'integrità e le prestazioni delle applicazioni nella cache e per determinare se è necessario ridimensionare la cache.

Per determinare se è necessario un ridimensionamento è possibile monitorare le metriche seguenti.

-	Carico server Redis
-	Utilizzo della memoria
-	Larghezza di banda della rete
-	Utilizzo di CPU

Se si determina che la cache non soddisfa più i requisiti dell'applicazione, è possibile passare a un piano tariffario della cache superiore o inferiore che sia adatto all'applicazione. Per altre informazioni su come determinare il tipo di piano tariffario della cache appropriato, vedere l'articolo relativo all'[offerta e alle dimensioni di Cache Redis da usare](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Ridimensionare una cache
Per ridimensionare la cache, [passare alla cache](cache-configure.md#configure-redis-cache-settings) nel [portale di Azure](https://portal.azure.com) e fare clic su **Impostazioni**, **Piano tariffario**.

È anche possibile fare clic su **Piano tariffario** nel pannello **Cache Redis**.

![Piano tariffario][redis-cache-pricing-tier-part]

Selezionare il piano tariffario desiderato dal pannello **Piano tariffario** e fare clic su **Seleziona**.

![Piano tariffario][redis-cache-pricing-tier-blade]

>[AZURE.NOTE] Il passaggio a un piano tariffario diverso è soggetto alle restrizioni seguenti.
>
>-	Non è possibile passare da un piano tariffario superiore a uno inferiore.
>    -    Non è possibile passare da una cache **Premium** a una cache **Standard** o **Basic**.
>    -    Non è possibile passare da una cache **Standard** a una cache **Basic**.
>-	È possibile passare da una cache **Basic** a una cache **Standard**, ma non modificare contemporaneamente la dimensione. Se occorre una dimensione diversa, è possibile eseguire successivamente un'operazione di ridimensionamento in base ai propri requisiti.
>-	Non è possibile passare direttamente da una cache **Basic** a una cache **Premium**. È necessario passare da **Basic** a **Standard** con una prima operazione di ridimensionamento e quindi da **Standard** a **Premium** con una successiva operazione.
>-	Non è possibile passare da una dimensione maggiore alla dimensione **C0 (250 MB)**.

Mentre è in corso il ridimensionamento della cache in base al nuovo piano tariffario, nel pannello **Cache Redis** viene visualizzato lo stato **Ridimensionamento**.

![Ridimensionamento][redis-cache-scaling]

Al termine dell'operazione, lo stato passa da **Ridimensionamento** a **In esecuzione**.

## Come automatizzare un'operazione di ridimensionamento

Oltre a usare il portale di Azure, per ridimensionare le istanze di Cache Redis di Azure è possibile eseguire i cmdlet di PowerShell per Cache Redis di Azure, l'interfaccia della riga di comando di Azure e le librerie di gestione di Microsoft Azure.

-	[Ridimensionare la cache tramite PowerShell](#scale-using-powershell)
-	[Ridimensionare la cache tramite l'interfaccia della riga di comando di Azure](#scale-using-azure-cli)
-	[Ridimensionare la cache tramite le librerie di gestione di Microsoft Azure](#scale-using-maml)

### Ridimensionare la cache tramite PowerShell

È possibile ridimensionare le istanze di Cache Redis di Azure con PowerShell usando il cmdlet [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) quando le proprietà `Size`, `Sku` o `ShardCount` vengono modificate. L'esempio seguente illustra come ridimensionare una cache denominata `myCache` configurando una dimensione di 2,5 GB.

	Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Per altre informazioni sul ridimensionamento con PowerShell, vedere le istruzioni per [ridimensionare una cache Redis mediante PowerShell](cache-howto-manage-redis-cache-powershell.md#scale).

### Ridimensionare la cache tramite l'interfaccia della riga di comando di Azure

Per ridimensionare le istanze di Cache Redis di Azure con l'interfaccia della riga di comando di Azure, chiamare il comando `azure rediscache set` e passare le modifiche di configurazione desiderate che includono una nuova dimensione della cache, SKU o dimensione del cluster, a seconda dell'operazione di ridimensionamento desiderata.

Per altre informazioni sul ridimensionamento tramite l'interfaccia della riga di comando di Azure, vedere [Modificare le impostazioni di una Cache Redis esistente](cache-manage-cli.md#scale).

### Ridimensionare la cache tramite le librerie di gestione di Microsoft Azure

Per ridimensionare le istanze di Cache Redis di Azure Redis usando le [librerie di gestione di Microsoft Azure](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), chiamare il metodo `IRedisOperations.CreateOrUpdate` e passare la nuova dimensione di `RedisProperties.SKU.Capacity`.

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

Per altre informazioni, vedere l'esempio relativo alla [gestione di Cache Redis tramite le librerie di gestione di Microsoft Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML).

## Domande frequenti relative al ridimensionamento

Nell'elenco seguente sono riportate le risposte alle domande poste comunemente sul ridimensionamento di Cache Redis di Azure.

-	[È possibile eseguire un ridimensionamento verso, da o in una cache Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
-	[Dopo il ridimensionamento, è necessario modificare il nome della cache o le chiavi di accesso?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
-	[Come funziona il ridimensionamento?](#how-does-scaling-work)
-	[Durante il ridimensionamento i dati nella cache andranno persi?](#will-i-lose-data-from-my-cache-during-scaling)
-	[L'impostazione databases personalizzata viene modificata durante il ridimensionamento?](#is-my-custom-databases-setting-affected-during-scaling)
-	[La cache rimarrà disponibile durante il ridimensionamento?](#will-my-cache-be-available-during-scaling)
-	[Operazioni non supportate](#operations-that-are-not-supported)
-	[Quanto tempo richiede il ridimensionamento?](#how-long-does-scaling-take)
-	[Come è possibile stabilire quando il ridimensionamento è completato?](#how-can-i-tell-when-scaling-is-complete)
-	[Perché questa funzionalità è in anteprima?](#why-is-this-feature-in-preview)

### È possibile eseguire un ridimensionamento verso, da o in una cache Premium?

-	Non è possibile passare da una cache **Premium** a un piano tariffario **Basic** o **Standard**.
-	È possibile scalare dal piano tariffario di una cache **Premium** a un altro.
-	Non è possibile passare direttamente da una cache **Basic** a una cache **Premium**. È necessario prima passare da **Basic** a **Standard** con un'operazione di ridimensionamento e quindi da **Standard** a **Premium** con una successiva operazione.
-	Se è stato abilitato il clustering durante la creazione della cache **Premium**, è possibile [modificare la dimensione della cache](cache-how-to-premium-clustering.md#cluster-size). Attualmente non è possibile abilitare il clustering in una cache esistente che è stata creata senza clustering.

    Per altre informazioni, vedere [Come configurare il clustering per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).

### Dopo il ridimensionamento, è necessario modificare il nome della cache o le chiavi di accesso?

No, il nome della cache e le chiavi restano invariati durante un'operazione di ridimensionamento.

### Come funziona il ridimensionamento?

-	Quando viene ridimensionata una cache **Basic**, essa viene chiusa e viene eseguito il provisioning di una nuova cache utilizzando la nuova dimensione. Durante questo periodo, la cache non è disponibile e tutti i dati nella cache vengono persi.
-	Quando una cache **Basic** viene scalata in una cache **Standard**, viene eseguito il provisioning di una cache di replica e i dati vengono copiati dalla cache principale alla cache di replica. Durante il processo di ridimensionamento, la cache rimane disponibile.
-	Quando si ridimensiona una cache **Standard** passando a una dimensione maggiore o a una cache **Premium**, una delle repliche viene arrestata, ne viene rieffettuato il provisioning in base alla nuova dimensione e i dati vengono trasferiti. Viene quindi eseguito il failover dell'altra replica e successivamente ne viene rieffettuato il provisioning, in modo analogo a quanto accade in caso di errore di uno dei nodi della cache.

### Durante il ridimensionamento i dati nella cache andranno persi?

-	Quando una cache **Basic** viene ridimensionata, tutti i dati vengono persi e la cache non è disponibile durante l'operazione di ridimensionamento.
-	Quando una cache **Basic** viene ridimensionata in una cache**Standard**, generalmente i dati nella cache vengono mantenuti.
-	Quando si aumenta la dimensione di una cache **Standard** o si passa a un piano tariffario superiore oppure quando si aumenta la dimensione di una cache **Premium**, in genere tutti i dati vengono mantenuti. Quando si riduce la dimensione di una cache **Standard** o **Premium**, i dati potrebbero andare persi in base al rapporto tra la quantità di dati nella cache e la nuova dimensione configurata. Se durante la riduzione i dati vengono persi, le chiavi vengono rimosse mediante il criterio di rimozione [allkeys-lru](http://redis.io/topics/lru-cache). 

### L'impostazione databases personalizzata viene modificata durante il ridimensionamento?

Alcuni piani tariffari hanno [limiti di databases](cache-configure.md#databases) diversi, quindi esistono alcune considerazioni da tenere presente durante il passaggio a un piano inferiore se si è configurato un valore personalizzato per l'impostazione `databases` durante la creazione della cache.

-	Quando si passa a piano tariffario con un limite di `databases` inferiore di quello del piano corrente:
	-	Se si usa il numero predefinito di `databases` che è 16 per tutti i piani tariffari, non viene perso alcun dato.
	-	Se si usa un numero personalizzato di `databases` compreso nei limiti per il piano a cui si passa, questa impostazione di `databases` viene mantenuta e non viene perso alcun dato.
	-	Se si usa un numero personalizzato di `databases` che supera i limiti del nuovo piano, l'impostazione di `databases` viene ridotta ai limiti del nuovo piano e tutti i dati nei database rimossi vengono persi.
-	Quando si passa a un piano tariffario con un limite di `databases` uguale o superiore a quello del piano corrente, l'impostazione di `databases` viene mantenuta e non viene perso alcun dato.

Si noti che mentre le cache Standard e Premium dispongono di un contratto di servizio pari al 99,9% di disponibilità, non esiste un contratto di servizio per la perdita dei dati.

### La cache rimarrà disponibile durante il ridimensionamento?

-	Le cache **Standard** e **Premium** rimangono disponibili durante l'operazione di ridimensionamento.
-	Le cache **Basic** sono offline durante le operazioni di ridimensionamento a una dimensione diversa, ma rimangono disponibili quando si esegue il ridimensionamento da una cache **Basic** a una **Standard**.

### Operazioni non supportate

-	Non è possibile passare da un piano tariffario superiore a uno inferiore.
    -    Non è possibile passare da una cache **Premium** a una cache **Standard** o **Basic**.
    -    Non è possibile passare da una cache **Standard** a una cache **Basic**.
-	È possibile scalare da una cache **Basic** a una cache **Standard**, ma non è possibile modificare contemporaneamente la dimensione. Se occorre una dimensione diversa, è possibile eseguire successivamente un'operazione di ridimensionamento in base ai propri requisiti.
-	Non è possibile passare direttamente da una cache **Basic** a una cache **Premium**. È necessario passare da **Basic** a **Standard** con una prima operazione di ridimensionamento e quindi da **Standard** a **Premium** con una successiva operazione.
-	Non è possibile passare da una dimensione maggiore alla dimensione **C0 (250 MB)**.

Se l'operazione di ridimensionamento non riesce, il servizio tenta di annullare l'operazione e la dimensione originale della cache viene ripristinata.

### Quanto tempo richiede il ridimensionamento?

Il ridimensionamento richiede circa 20 minuti, a seconda della quantità di dati nella cache.

### Come è possibile stabilire quando il ridimensionamento è completato?

Nel portale di Azure è possibile visualizzare l'operazione di ridimensionamento in corso. Quando il ridimensionamento è completo, lo stato della cache passa a **In esecuzione**.

### Perché questa funzionalità è in anteprima?

Questa funzionalità viene rilasciata allo scopo di ottenere commenti e suggerimenti. In base ai commenti, questa funzionalità sarà rilasciata a breve per la disponibilità generale.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=AcomDC_0525_2016-->