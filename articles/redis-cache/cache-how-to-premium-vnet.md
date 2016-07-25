<properties 
	pageTitle="Come configurare il supporto di una rete virtuale per un'istanza Premium di Cache Redis di Azure | Microsoft Azure" 
	description="In questo articolo viene illustrato come creare e gestire il supporto di una rete virtuale per le istanze di Cache Redis di Azure Premium." 
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
	ms.date="07/12/2016" 
	ms.author="sdanie"/>

# Come configurare il supporto di una rete virtuale per un'istanza Premium di Cache Redis di Azure
Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache, incluso il nuovo livello Premium.

Il livello Premium di Cache Redis di Azure include il supporto di clustering, persistenza e rete virtuale. Una rete virtuale è una rete privata nel cloud. Quando un'istanza di Cache Redis di Azure viene configurata con una rete virtuale, non è indirizzabile pubblicamente ed è accessibile solo da macchine virtuali e applicazioni all'interno della rete virtuale. Questo articolo descrive come configurare il supporto di una rete virtuale per un'istanza Premium di Cache Redis di Azure.

>[AZURE.NOTE] Cache Redis di Azure supporta le reti virtuali classiche e ARM.

Per informazioni su altre funzionalità della cache Premium, vedere [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md) e [Come configurare il clustering Redis per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).

## Perché usare una rete virtuale?
La distribuzione di [Rete virtuale di Azure](https://azure.microsoft.com/services/virtual-network/) offre sicurezza e isolamento maggiori per Cache Redis di Azure, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso a Cache Redis di Azure.

## Supporto della rete virtuale
Il supporto della rete virtuale viene configurato nel pannello **Nuova cache Redis** durante la creazione della cache.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Dopo aver selezionato un piano tariffario Premium, è possibile configurare l'integrazione della rete virtuale di Cache Redis di Azure selezionando una rete virtuale nella stessa sottoscrizione e nella stessa posizione della cache. Per usare una nuova rete virtuale, è necessario prima crearla seguendo i passaggi in [Creare una rete virtuale usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o in [Creare una rete virtuale (classica) usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) e quindi tornare al pannello **Nuova cache Redis** per creare e configurare la cache Premium.

Per configurare la rete virtuale per la nuova cache, fare clic su **Rete virtuale** nel pannello **Nuova cache Redis** e selezionare la rete virtuale desiderata dall'elenco a discesa.

![Rete virtuale][redis-cache-vnet]

Nell'elenco a discesa **Subnet** selezionare la subnet desiderata e specificare l'**indirizzo IP statico** desiderato. Se si usa una rete virtuale classica, il campo **Indirizzo IP statico** è facoltativo e, se non è specificato, ne verrà scelto uno dalla subnet selezionata.

>[AZURE.IMPORTANT] Quando si distribuisce una Cache Redis di Azure in una rete virtuale ARM, la cache deve trovarsi in una subnet dedicata che non contiene altre risorse, ad eccezione delle istanze di Cache Redis di Azure. Se si tenta di distribuire una Cache Redis di Azure in una rete virtuale ARM in una subnet contenente altre risorse, la distribuzione avrà esito negativo.

![Rete virtuale][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] I primi 4 indirizzi in una subnet sono riservati e non possono essere usati. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

Dopo aver creato la cache, è possibile visualizzare la configurazione della rete virtuale facendo clic su **Rete virtuale** nel pannello **Impostazioni**.

![Rete virtuale][redis-cache-vnet-info]


Per connettersi all'istanza di Cache Redis di Azure quando si usa una rete virtuale, specificare il nome host della cache nella stringa di connessione, come illustrato nell'esempio seguente.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
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

-	[Quali sono alcuni problemi comuni di configurazione errata per Cache Redis di Azure e le reti virtuali?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-	[È possibile usare reti virtuali con una cache Standard o Basic?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-	[Perché la creazione di una cache Redis ha esito negativo in alcune subnet e non in altre?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
-	[Tutte le funzionalità della cache funzionano quando si ospita una cache in una rete virtuale?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)


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


Esistono requisiti di connettività di rete per Cache Redis di Azure che potrebbero non essere inizialmente soddisfatti in una rete virtuale. Per il corretto funzionamento quando viene usata all'interno di una rete virtuale, Cache Redis di Azure richiede tutti gli elementi seguenti.

-  Connettività di rete in uscita per endpoint di archiviazione di Azure in tutto il mondo. Sono inclusi gli endpoint che si trovano nella stessa area dell'istanza di Cache Redis di Azure, nonché gli endpoint di archiviazione che si trovano in **altre** aree di Azure. Gli endpoint di Archiviazione di Azure si risolvono nei seguenti domini DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*.
-  Connettività di rete in uscita verso *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*. È necessario per supportare la funzionalità SSL.
-  La configurazione DNS per la rete virtuale deve essere in grado di risolvere tutti gli endpoint e i domini indicati nei punti precedenti. Questi requisiti DNS possono essere soddisfatti garantendo che un'infrastruttura DNS valida venga configurata e mantenuta per la rete virtuale.



### È possibile usare reti virtuali con una cache Standard o Basic?

Le reti virtuali possono essere usate solo con cache Premium.

### Perché la creazione di una cache Redis ha esito negativo in alcune subnet e non in altre?

Quando si distribuisce una Cache Redis di Azure in una rete virtuale ARM, la cache deve trovarsi in una subnet dedicata che non contiene altri tipi di risorse. Se si tenta di distribuire una Cache Redis di Azure in una subnet di rete virtuale ARM contenente altre risorse, la distribuzione avrà esito negativo. Prima di poter creare una nuova cache Redis, è necessario eliminare le risorse esistenti all'interno della subnet.

È possibile distribuire più tipi di risorse in una rete virtuale classica, purché siano disponibili indirizzi IP sufficienti.

### Tutte le funzionalità della cache funzionano quando si ospita una cache in una rete virtuale?

Quando la cache fa parte di una rete virtuale, solo i client nella rete virtuale possono accedere alla cache e quindi le funzionalità di gestione della cache seguenti non funzionano in questo momento.

-	Console di Redis: poiché la Console di Redis usa il client di redis cli.exe ospitato su macchine virtuali che non fanno parte di una rete virtuale, non è possibile connettersi alla cache.


## Usare ExpressRoute con Cache Redis di Azure

I clienti possono connettere un circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) all'infrastruttura di rete virtuale per estendere la rete locale ad Azure.

Per impostazione predefinita, un circuito ExpressRoute appena creato annuncia una route predefinita che consente la connettività Internet in uscita. Con questa configurazione le applicazioni client saranno in grado di connettersi ad altri endpoint di Azure tra cui Cache Redis di Azure.

Tuttavia, secondo una diffusa configurazione, i clienti definiscono la propria route predefinita (0.0.0.0/0) verso la quale viene forzato il traffico Internet in uscita invece di far passare il flusso localmente. Questo flusso di traffico interrompe sempre la connettività con Cache Redis di Azure perché il traffico in uscita è bloccato in locale o convertito tramite NAT in un set non riconoscibile di indirizzi che non usano più i diversi endpoint di Azure.

La soluzione consiste nel definire una o più route definite dall'utente (UDR, User Defined Route) nella subnet contenente la Cache Redis di Azure. Una route UDR definisce le route specifiche della subnet che verranno accettate invece della route predefinita.

Se possibile, è consigliabile utilizzare la seguente configurazione:

- La configurazione di ExpressRoute annuncia 0.0.0.0/0 e per impostazione predefinita esegue il tunneling forzato di tutto il traffico in uscita in un ambiente locale.
- L'UDR applicata alla subnet contenente la Cache Redis di Azure definisce 0.0.0.0/0 con un tipo di hop successivo di Internet. Più avanti in questo articolo, è possibile trovare un esempio.

L'effetto combinato di questi passaggi è che il livello di subnet UDR avrà la precedenza sul tunneling forzato di ExpressRoute, garantendo l'accesso a Internet in uscita da Cache Redis di Azure.

Anche se la connessione a un'istanza di Cache Redis di Azure da un'applicazione locale tramite ExpressRoute non è uno scenario di utilizzo tipico per motivi di prestazioni (per prestazioni ottimali i client di Cache Redis di Azure devono trovarsi nella stessa area di Cache Redis di Azure), in questo scenario il percorso di rete in uscita non può attraversare i proxy aziendali interni, né essere sottoposto a tunneling forzato in locale. In questo modo viene modificato l'indirizzo NAT effettivo del traffico di rete in uscita da Cache Redis di Azure. La modifica dell'indirizzo NAT del traffico di rete in uscita di un'istanza di Cache Redis di Azure provocherà errori di connettività a molti degli endpoint sopra elencati. I tentativi di creazione di un'istanza di Cache Redis di Azure avranno pertanto esito negativo.

**IMPORTANTE:** le route definite in un UDR **devono** essere sufficientemente specifiche per avere la precedenza su qualsiasi route annunciata dalla configurazione di ExpressRoute. Nell'esempio di seguito viene utilizzato l'intervallo di indirizzi ampio 0.0.0.0/0 e pertanto può essere accidentalmente sottoposto a override dagli annunci di route mediante più intervalli di indirizzi specifici.

**MOLTO IMPORTANTE:** Cache Redis di Azure non è supportato con le configurazioni di ExpressRoute che **annunciano erroneamente route dal percorso di peering pubblico al percorso di peering privato**. Le configurazioni di ExpressRoute che dispongono di peering pubblico configurato, riceveranno gli annunci di route da Microsoft per un elevato numero di intervalli di indirizzi IP di Microsoft Azure. Se questi intervalli di indirizzi vengono annunciati in modo non corretto nel percorso di peering privato, il risultato finale è che tutti i pacchetti di rete in uscita dalla subnet dell'istanza di Cache Redis di Azure verranno erroneamente sottoposti a tunneling forzato verso l'infrastruttura di rete locale del cliente. Questo flusso di rete interromperà Cache Redis di Azure. La soluzione a questo problema consiste nell'interrompere l’annuncio di più route dal percorso di peering pubblico al percorso di peering privato.

Le informazioni generali sulle route definite dall'utente sono disponibili in questa [panoramica](../virtual-network/virtual-networks-udr-overview.md).

Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

## Passaggi successivi
Informazioni su come usare altre funzionalità di cache premium.

-	[Come configurare la persistenza per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md)
-	[Come configurare il servizio cluster per una Cache Redis di Azure Premium](cache-how-to-premium-clustering.md)
-	[Importare ed esportare dati in Cache Redis di Azure](cache-how-to-import-export-data.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0713_2016-->