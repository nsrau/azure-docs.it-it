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
	ms.date="12/03/2015" 
	ms.author="sdanie"/>

# Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium
Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache, incluso il nuovo livello Premium.

Il livello Premium di Cache Redis di Azure include il supporto di clustering, persistenza e rete virtuale (VNET). Una rete virtuale è una rappresentazione della propria rete personalizzata nel cloud. Quando un'istanza di Cache Redis di Azure viene configurata con una rete virtuale, non è indirizzabile pubblicamente ed è accessibile solo dai client all'interno della rete virtuale. Questo articolo descrive come configurare il supporto di una rete virtuale per un'istanza di Cache Redis di Azure Premium.

Per informazioni su altre funzionalità della cache Premium, vedere [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md) e [Come configurare il clustering Redis per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).

## Perché usare una rete virtuale?
La distribuzione di [Rete virtuale di Azure (VNET)](https://azure.microsoft.com/services/virtual-network/) offre maggiori sicurezza e isolamento per Cache Redis di Azure, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso a Cache Redis di Azure.

## Supporto della rete virtuale
Il supporto della rete virtuale viene configurato nel pannello **Nuova cache Redis** durante la creazione della cache. Per creare una cache, accedere al [portale di Azure](https://portal.azure.com) e fare clic su **Nuovo**->**Dati e archiviazione**>**Cache Redis**.

![Creare una Cache Redis][redis-cache-new-cache-menu]

Per configurare il supporto della rete virtuale, selezionare innanzitutto una delle cache **Premium** nel pannello **Scegliere il piano tariffario**.

![Scegliere il livello di prezzo][redis-cache-premium-pricing-tier]

L'integrazione della rete virtuale in Cache Redis di Azure viene configurata nel pannello **Rete virtuale**. Da questa posizione è possibile selezionare una rete virtuale classica esistente. Per usare una nuova rete virtuale, seguire i passaggi in [Creare una rete virtuale (classica) usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e quindi tornare al pannello **Rete virtuale di Cache Redis** per selezionarla.

>[AZURE.NOTE]Cache Redis di Azure funziona con reti virtuali classiche. Per informazioni sulla creazione di una rete virtuale classica, vedere [Creare una rete virtuale (classica) usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Per informazioni sulla connessione di reti virtuali classiche a reti virtuali di ARM, vedere [Connessione di reti virtuali classiche a nuove reti virtuali](../virtual-network/virtual-networks-arm-asm-s2s.md).

![Rete virtuale][redis-cache-vnet]

Fare clic su **Rete virtuale** nel pannello **Rete virtuale** per selezionare e configurare la rete virtuale.

![Rete virtuale][redis-cache-vnet-select]

Fare clic sulla rete virtuale desiderata per selezionarla.

![Rete virtuale][redis-cache-vnet-subnet]

Fare clic su Subnet per selezionare la subnet desiderata.

![Rete virtuale][redis-cache-vnet-ip]

Digitare il valore desiderato per **Indirizzo IP statico** e fare clic su **OK** per salvare la configurazione della rete virtuale. Se l'indirizzo IP statico selezionato è già in uso, viene visualizzato un messaggio di errore.

Dopo la creazione della cache, potranno accedervi solo i client all'interno di stessa rete virtuale.

>[AZURE.IMPORTANT]Per accedere all'istanza di Cache Redis di Azure quando si usa una rete virtuale, passare l'indirizzo IP statico della cache nella rete virtuale come primo parametro e passare un parametro `sslhost` con l'endpoint della cache. Nell'esempio seguente l'indirizzo IP statico è `10.10.1.5` e l'endpoint della cache è `contoso5.redis.cache.windows.net`.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("10.10.1.5,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
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

L'elenco seguente include alcuni errori di configurazione comuni che possono impedire il corretto funzionamento di Cache Redis di Azure.

-	Mancanza di accesso al DNS. Le istanze di Cache Redis di Azure in una rete virtuale richiedono l'accesso al DNS per le parti del sistema di monitoraggio e di runtime della cache. Se l'istanza della cache non dispone dell'accesso al DNS, il monitoraggio non funzionerà e la cache non funzionerà correttamente.
-	Porte TCP bloccate usate dai client per la connessione a Redis, ad esempio 6379 o 6380.
-	Traffico HTTPS in uscita bloccato o intercettato dalla rete virtuale. Cache Redis di Azure usa il traffico HTTPS in uscita verso i servizi di Azure, in particolare il servizio Archiviazione.
-	Macchine virtuali dell'istanza del ruolo Redis che non possono comunicare tra loro all'interno della subnet. Dovrebbe essere consentita la comunicazione tra le istanze del ruolo Redis tramite TCP su qualsiasi porta usata, soggetta a variazione, ma come minimo su tutte le porte usate nel file CSDEF di Redis.
-	Il servizio di bilanciamento del carico di Azure non può connettersi alle macchine virtuali di Redis sulla porta TCP/HTTP 16001. Cache Redis di Azure dipende dal probe predefinito del servizio di bilanciamento del carico di Azure per determinare quali istanze del ruolo sono attive. Il probe predefinito del servizio di bilanciamento del carico si basa sull'esecuzione del ping dell'agente guest di Azure sulla porta 16001. Solo le istanze del ruolo che rispondono al ping verranno inserite nella rotazione per ricevere il traffico inoltrato da ILB. Quando la rotazione non include alcuna istanza perché il ping non riesce a causa del blocco delle porte, ILB non accetterà connessioni TCP in ingresso.
-	Blocco del traffico Web dell'applicazione client usato per la convalida della chiave pubblica SSL. I client di Redis (all'interno della rete virtuale) devono essere in grado di eseguire il traffico HTTP verso la rete Internet pubblica per scaricare i certificati CA e gli elenchi di revoche di certificati per eseguire la convalida dei certificati SSL quando usano la porta 6380 per connettersi a Redis ed eseguire l'autenticazione server SSL.
-	Il servizio di bilanciamento del carico di Azure non riesce a connettersi alle macchine virtuali di Resid in un cluster tramite TCP sulla porta 1300x (13000, 13001 e così via) o 1500x (15000, 15001 e così via). Le reti virtuali sono configurate nel file csdef con un probe di bilanciamento del carico per aprire queste porte. Il servizio di bilanciamento del carico di Azure deve essere consentito dai gruppi di sicurezza di rete. I gruppi di sicurezza di rete predefiniti gestiscono questa condizione con il tag AZURE\_LOADBALANCER. Il servizio di bilanciamento del carico di Azure dispone di un singolo indirizzo IP statico: 168.63.126.16. Per altre informazioni, vedere [Che cos'è un Gruppo di sicurezza di rete (NSG)?](../virtual-network/virtual-networks-nsg.md).

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

[redis-cache-vnet-select]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-select.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-subnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-subnet.png

<!---HONumber=AcomDC_1210_2015-->