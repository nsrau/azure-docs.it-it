<properties 
	pageTitle="Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium" 
	description="In questo articolo viene illustrato come creare e gestire il supporto di una rete virtuale per le istanze di Cache Redis di Azure Premium." 
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
	ms.date="02/04/2016" 
	ms.author="sdanie"/>

# Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium
Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache, incluso il nuovo livello Premium.

Il livello Premium di Cache Redis di Azure include il supporto di clustering, persistenza e rete virtuale (VNET). Una rete virtuale è una rappresentazione della propria rete personalizzata nel cloud. Quando un'istanza di Cache Redis di Azure viene configurata con una rete virtuale, non è indirizzabile pubblicamente ed è accessibile solo dai client all'interno della rete virtuale. Questo articolo descrive come configurare il supporto di una rete virtuale per un'istanza di Cache Redis di Azure Premium.

Per informazioni su altre funzionalità della cache Premium, vedere [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md) e [Come configurare il clustering Redis per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).

## Perché usare una rete virtuale?
La distribuzione di [Rete virtuale di Azure (VNET)](https://azure.microsoft.com/services/virtual-network/) offre maggiori sicurezza e isolamento per Cache Redis di Azure, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso a Cache Redis di Azure.

## Supporto della rete virtuale
Il supporto della rete virtuale viene configurato nel pannello **Nuova cache Redis** durante la creazione della cache. Per creare una cache, accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Nuovo** >**Dati e archiviazione** > **Cache Redis**.

![Creare una Cache Redis][redis-cache-new-cache-menu]

Per configurare il supporto della rete virtuale, selezionare innanzitutto una delle cache **Premium** nel pannello **Scegliere il piano tariffario**.

![Scegliere il livello di prezzo][redis-cache-premium-pricing-tier]

L'integrazione della rete virtuale in Cache Redis di Azure viene configurata nel pannello **Rete virtuale (classico)**. Da questa posizione è possibile selezionare una rete virtuale classica esistente. Per usare una nuova rete virtuale, seguire i passaggi in [Creare una rete virtuale (classica) usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e quindi tornare al pannello **Rete virtuale di Cache Redis** per selezionarla.

>[AZURE.NOTE] Cache Redis di Azure funziona con reti virtuali classiche. Per informazioni sulla creazione di una rete virtuale classica, vedere [Creare una rete virtuale (classica) usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Per informazioni sulla connessione di reti virtuali classiche a reti virtuali di ARM, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](../virtual-network/virtual-networks-arm-asm-s2s.md).

Fare clic su **Rete virtuale (classico)** nel pannello **Nuova cache Redis** e selezionare la rete virtuale desiderata dall'elenco a discesa in modo da selezionare e configurare la propria rete virtuale.

![Rete virtuale][redis-cache-vnet]

Selezionare la subnet desiderata nell'elenco a discesa **Subnet**.

![Rete virtuale][redis-cache-vnet-ip]

Il campo **indirizzo IP statico** è facoltativo. Se non sono specificati indirizzi qui, ne verrà scelto uno dalla subnet selezionata. Se si desidera un IP statico specifico, digitare l'**Indirizzo IP statico** e fare clic su **OK** per salvare la configurazione della rete virtuale. Se l'indirizzo IP statico selezionato è già in uso, viene visualizzato un messaggio di errore.

Dopo aver creato la cache, è possibile visualizzare l'indirizzo IP e altre informazioni sulla rete virtuale facendo clic su **Rete virtuale** dal pannello **Impostazioni**.

![Rete virtuale][redis-cache-vnet-info]

>[AZURE.IMPORTANT] Per accedere all'istanza della cache Redis di Azure quando si utilizza una rete virtuale, passare l'indirizzo IP statico della cache nella rete virtuale come primo parametro e passare un parametro `sslhost` con l'endpoint della cache. Nell'esempio seguente l'indirizzo IP statico è `172.160.0.99` e l'endpoint della cache è `contoso5.redis.cache.windows.net`.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("172.160.0.99,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Domande frequenti sulla rete virtuale di Cache Redis di Azure

Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sulla rete virtuale di Cache Redis di Azure.

## Quali sono alcuni problemi comuni di configurazione errata per Cache Redis di Azure e le reti virtuali?

Quando Cache Redis di Azure è ospitata in una rete virtuale, vengono usate le porte indicate nella tabella seguente. Se le porte sono bloccate, la cache potrebbe non funzionare correttamente. Il blocco di una o più di queste porte è il problema di configurazione più comune nell'uso di Cache Redis di Azure in una rete virtuale.

| Porte | Direzione | Protocollo di trasporto | Scopo | IP remoto |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443 | In uscita | TCP | Dipendenze Redis in Archiviazione di Azure/PKI (Internet) | * |
| 53 | In uscita | TCP/UDP | Dipendenze Redis nel DNS (Internet/rete virtuale) | * |
| 6379, 6380 | In ingresso | TCP | Comunicazione tra client e Redis, bilanciamento del carico di Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 8443 | In ingresso/In uscita | TCP | Dettaglio di implementazione per Redis | VIRTUAL\_NETWORK |
| 8500 | In ingresso | TCP/UDP | Bilanciamento del carico di Azure | AZURE\_LOADBALANCER |
| 10221-10231 | In ingresso/In uscita | TCP | Dettaglio di implementazione per Redis, è possibile limitare l'endpoint remoto a VIRTUAL\_NETWORK | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 13000-13999 | In ingresso | TCP | Comunicazione tra client e cluster Redis, bilanciamento del carico di Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 15000-15999 | In ingresso | TCP | Comunicazione tra client e cluster Redis, bilanciamento del carico di Azure | VIRTUAL\_NETWORK, AZURE\_LOADBALANCER |
| 16001 | In ingresso | TCP/UDP | Bilanciamento del carico di Azure | AZURE\_LOADBALANCER |
| 20226 | In ingresso + In uscita | TCP | Dettaglio di implementazione per cluster Redis | VIRTUAL\_NETWORK |




## È possibile usare reti virtuali con una cache Standard o Basic?

Le reti virtuali possono essere usate solo con cache Premium.

## Passaggi successivi
Informazioni su come usare altre funzionalità di cache premium.

-	[Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md)
-	[Come configurare il servizio cluster per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0211_2016-->