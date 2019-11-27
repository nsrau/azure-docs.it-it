---
title: Configurare una rete virtuale per una cache di Azure Premium per Redis
description: In questo articolo viene spiegato come creare e gestire il supporto di una rete virtuale per le istanze di Cache Redis di Azure Premium
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: yegu
ms.openlocfilehash: 03cc5bd4e6e7198a6a3a916226c72e9b0f9ff1b2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233122"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium
Cache Redis di Azure include diverse soluzioni cache che offrono flessibilità di scelta riguardo alle dimensioni e alle funzionalità della cache, tra cui le funzionalità del livello Premium come clustering, persistenza e supporto per reti virtuali. Una rete virtuale è una rete privata nel cloud. Quando un'istanza di Cache Redis di Azure viene configurata con una rete virtuale, non è indirizzabile pubblicamente ed è accessibile solo da macchine virtuali e applicazioni all'interno della rete virtuale. Questo articolo descrive come configurare il supporto di una rete virtuale per un'istanza Premium di Cache Redis di Azure.

> [!NOTE]
> Cache Redis di Azure supporta le reti virtuali classiche e di Resource Manager.
> 
> 

Per informazioni su altre funzionalità della cache di livello Premium, vedere [Introduction to the Azure Cache for Redis Premium tier](cache-premium-tier-intro.md) (Introduzione al livello Premium di Cache Redis di Azure).

## <a name="why-vnet"></a>Perché usare una rete virtuale?
La distribuzione di [Rete virtuale (VNet) di Azure](https://azure.microsoft.com/services/virtual-network/) offre sicurezza e isolamento migliorate per Cache Redis di Azure, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso.

## <a name="virtual-network-support"></a>Supporto della rete virtuale
Il supporto della rete virtuale viene configurato nel pannello **Nuova cache Redis di azure** durante la creazione della cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Dopo aver selezionato un piano tariffario Premium, è possibile configurare l'integrazione rete virtuale di Cache Redis selezionando una rete virtuale nella stessa sottoscrizione e nella stessa posizione della cache. Per usare una nuova rete virtuale, è necessario prima crearla seguendo i passaggi in [Creare una rete virtuale usando il portale di Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) o in [Creare una rete virtuale (classica) usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md), quindi tornare al pannello **Nuova cache Redis di Azure** per creare e configurare la cache Premium.

Per configurare la rete virtuale per la nuova cache, fare clic su **Rete virtuale** nel pannello **Nuova cache Redis di Azure** e selezionare la rete virtuale desiderata dall'elenco a discesa.

![rete virtuale][redis-cache-vnet]

Nell'elenco a discesa **Subnet** selezionare la subnet desiderata e specificare l'**indirizzo IP statico**. Se si usa una rete virtuale classica, il campo **Indirizzo IP statico** è facoltativo e, se non è specificato, ne verrà scelto uno dalla subnet selezionata.

> [!IMPORTANT]
> Quando si distribuisce Cache Redis di Azure in una rete virtuale di Resource Manager, la cache deve trovarsi in una subnet dedicata che non contenga altre risorse, ad eccezione delle istanze di Cache Redis di Azure. Se si tenta di distribuire un'istanza di Cache Redis di Azure in una rete virtuale di Resource Manager all'interno di una subnet contenente altre risorse, la distribuzione non riesce.
> 
> 

![rete virtuale][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Oltre agli indirizzi IP usati dall'infrastruttura di rete virtuale di Azure, ogni istanza di Cache Redis nella subnet usa due indirizzi IP per partizione e un altro indirizzo IP per il bilanciamento del carico. Si presuppone che una cache non cluster includa una sola partizione.
> 
> 

Dopo aver creato la cache, è possibile visualizzare la configurazione della rete virtuale facendo clic su **Rete virtuale** dal **menu Risorse**.

![rete virtuale][redis-cache-vnet-info]

Per connettersi all'istanza di Cache Redis di Azure quando si usa una rete virtuale, specificare il nome host della cache nella stringa di connessione, come mostrato nell'esempio seguente:

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

## <a name="azure-cache-for-redis-vnet-faq"></a>Domande frequenti su Cache Redis di Azure
Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sulla rete virtuale di Cache Redis di Azure.

* Quali sono alcuni problemi comuni di configurazione errata per Cache Redis di Azure e le reti virtuali?
* [Come è possibile verificare che la cache funzioni in una rete virtuale?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* Quando si tenta di connettersi alla cache Redis di Azure in una rete virtuale, perché viene visualizzato un errore che indica che il certificato remoto non è valido?
* [È possibile usare reti virtuali con una cache Standard o Basic?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* Perché la creazione di una cache Redis di Azure ha esito negativo in alcune subnet e non in altre?
* [Quali sono i requisiti di spazio per gli indirizzi di subnet?](#what-are-the-subnet-address-space-requirements)
* [Tutte le funzionalità della cache funzionano quando si ospita una cache in una rete virtuale?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets"></a>Quali sono alcuni problemi comuni di configurazione errata per Cache Redis di Azure e le reti virtuali?
Quando Cache Redis di Azure è ospitata in una rete virtuale, vengono usate le porte indicate nelle tabelle seguenti. 

>[!IMPORTANT]
>Se le porte nelle tabelle seguenti sono bloccate, la cache potrebbe non funzionare correttamente. Il blocco di una o più di queste porte è il problema di configurazione più comune nell'uso di Cache Redis di Azure in una rete virtuale.
> 
> 

- [Requisiti delle porte in uscita](#outbound-port-requirements)
- [Requisiti delle porte in ingresso](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisiti delle porte in uscita

Sono previsti nove requisiti per le porte in uscita. Le richieste in uscita in questi intervalli sono in uscita con altri servizi necessari per il funzionamento della cache o interni alla subnet Redis per la comunicazione tra nodi. Per la replica geografica sono disponibili altri requisiti in uscita per la comunicazione tra subnet della cache primaria e secondaria.

| Porte | Direzione | Protocollo di trasporto | Scopo | IP locale | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |In uscita |TCP |Dipendenze Redis in Archiviazione di Azure/PKI (Internet) | (Subnet Redis) |* |
| 443 | In uscita | TCP | Dipendenza Redis da Azure Key Vault | (Subnet Redis) | AzureKeyVault <sup>1</sup> |
| 53 |In uscita |TCP/UDP |Dipendenze Redis nel DNS (Internet/rete virtuale) | (Subnet Redis) | 168.63.129.16 e 169.254.169.254 <sup>2</sup> e qualsiasi server DNS personalizzato per la subnet <sup>3</sup> |
| 8443 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) | (Subnet Redis) |
| 10221-10231 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) | (Subnet Redis) |
| 20226 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |
| 13000-13999 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |
| 15000-15999 |In uscita |TCP |Comunicazioni interne per Redis e la replica geografica | (Subnet Redis) |(Subnet Redis) (Subnet peer di replica geografica) |
| 6379-6380 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |

<sup>1</sup> è possibile usare il tag di servizio ' AzureKeyVault ' con gestione risorse gruppi di sicurezza di rete.

<sup>2</sup> questi indirizzi IP di proprietà di Microsoft vengono usati per gestire la macchina virtuale host che funge da DNS di Azure.

<sup>3</sup> non necessario per le subnet senza server DNS personalizzato o cache Redis più recenti che ignorano il DNS personalizzato.

#### <a name="geo-replication-peer-port-requirements"></a>Requisiti della porta peer per la replica geografica

Se si usa la replica georeplica tra le cache nelle reti virtuali di Azure, si noti che la configurazione consigliata prevede di sbloccare le porte 15000-15999 per l'intera subnet in entrambe le direzioni in ingresso e in uscita per entrambe le cache, in modo che tutti i componenti di replica nella subnet è possibile comunicare direttamente tra loro anche nel caso di un futuro failover geografico.

#### <a name="inbound-port-requirements"></a>Requisiti delle porte in ingresso

Vi sono otto requisiti delle porte in ingresso. Le richieste in ingresso in questi intervalli provengono da altri servizi ospitati nella stessa VNET o sono interne alle comunicazioni subnet di Redis.

| Porte | Direzione | Protocollo di trasporto | Scopo | IP locale | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |In ingresso |TCP |Comunicazione tra client e Redis, bilanciamento del carico di Azure | (Subnet Redis) | (Subnet Redis), rete virtuale, Azure Load Balancer <sup>1</sup> |
| 8443 |In ingresso |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |
| 8500 |In ingresso |TCP/UDP |Bilanciamento del carico di Azure | (Subnet Redis) |Servizio di bilanciamento del carico di Azure |
| 10221-10231 |In ingresso |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis), bilanciamento del carico di Azure |
| 13000-13999 |In ingresso |TCP |Comunicazione tra client e cluster Redis, bilanciamento del carico di Azure | (Subnet Redis) |Rete virtuale, Bilanciamento carico di Azure |
| 15000-15999 |In ingresso |TCP |Comunicazione tra client e cluster Redis, bilanciamento del carico di Azure e replica geografica | (Subnet Redis) |Rete virtuale, Azure Load Balancer, (subnet peer di replica geografica) |
| 16001 |In ingresso |TCP/UDP |Bilanciamento del carico di Azure | (Subnet Redis) |Servizio di bilanciamento del carico di Azure |
| 20226 |In ingresso |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |

<sup>1</sup> è possibile usare il tag di servizio ' AzureLoadBalancer ' (Gestione risorse) (o ' AZURE_LOADBALANCER ' per la distribuzione classica) per la creazione delle regole NSG.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Ulteriori requisiti di connettività della rete VNET

Esistono requisiti di connettività di rete per Cache Redis di Azure che potrebbero non essere inizialmente soddisfatti in una rete virtuale. Per il corretto funzionamento se usato all'interno di una rete virtuale, Cache Redis di Azure richiede tutti gli elementi seguenti.

* Connettività di rete in uscita per endpoint di archiviazione di Azure in tutto il mondo. Sono inclusi gli endpoint che si trovano nella stessa area dell'istanza di Cache Redis di Azure, nonché gli endpoint di archiviazione che si trovano in **altre** aree di Azure. Gli endpoint di Archiviazione di Azure vengono risolti nei seguenti domini DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*. 
* Connettività di rete in uscita verso *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*. Tale connettività è necessaria per il supporto della funzionalità SSL.
* La configurazione DNS per la rete virtuale deve essere in grado di risolvere tutti gli endpoint e i domini indicati nei punti precedenti. Questi requisiti DNS possono essere soddisfatti garantendo che un'infrastruttura DNS valida venga configurata e mantenuta per la rete virtuale.
* Connettività di rete in uscita agli endpoint di Monitoraggio di Azure seguenti, che vengono risolti nei domini DNS seguenti: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Come è possibile verificare che la cache funzioni in una rete virtuale?

>[!IMPORTANT]
>Quando ci si connette a una cache di Azure per l'istanza di redis ospitata in una VNET, i client della cache devono trovarsi nello stesso VNET o in un VNET con peering VNET abilitato nella stessa area di Azure. La Peering reti virtuali globale non è attualmente supportata. Questo vale anche per le applicazioni di test e per gli strumenti per l'esecuzione di ping di diagnostica. Indipendentemente dalla posizione che ospita l'applicazione client, i gruppi di sicurezza di rete devono essere configurati in modo che il traffico di rete del client sia autorizzato a raggiungere l'istanza di Redis.
>
>

Dopo aver configurato i requisiti delle porte come descritto nella sezione precedente, è possibile verificare che la cache funzioni eseguendo la procedura seguente.

- [Riavviare](cache-administration.md#reboot) tutti i nodi della cache. Se non è possibile raggiungere tutte le dipendenze della cache richieste (come descritto in [Requisiti delle porte in ingresso](cache-how-to-premium-vnet.md#inbound-port-requirements) e [Requisiti delle porte in uscita](cache-how-to-premium-vnet.md#outbound-port-requirements)), la cache non sarà in grado di riavviarsi.
- Dopo il riavvio dei nodi della cache (come riportato dallo stato della cache nel portale di Azure), è possibile eseguire i test seguenti:
  - eseguire il ping dell'endpoint della cache (tramite la porta 6380) da un computer che si trova all'interno della stessa rete virtuale della cache tramite [tcping](https://www.elifulkerson.com/projects/tcping.php). Ad esempio:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se lo strumento `tcping` indica che la porta è aperta, la cache è disponibile per la connessione dai client nella rete virtuale.

  - Un altro modo per eseguire il test è quello di creare un client della cache di test (che potrebbe essere una semplice applicazione della console tramite StackExchange.Redis) che si connette alla cache, aggiunge e recupera alcuni elementi dalla cache. Installare l'applicazione client di esempio in una macchina virtuale che si trova nella stessa rete virtuale della cache ed eseguirla per verificare la connettività alla cache.


### <a name="when-trying-to-connect-to-my-azure-cache-for-redis-in-a-vnet-why-am-i-getting-an-error-stating-the-remote-certificate-is-invalid"></a>Quando si tenta di connettersi alla cache Redis di Azure in una rete virtuale, perché viene visualizzato un errore che indica che il certificato remoto non è valido?

Quando si tenta di connettersi alla cache Redis di Azure in una rete virtuale, viene visualizzato un errore di convalida del certificato come il seguente:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

La causa potrebbe essere che ci si sta connettendo all'host tramite l'indirizzo IP. Si consiglia di usare il nome host. In altri termini, usare il comando seguente:     

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evitare di usare l'indirizzo IP simile alla stringa di connessione seguente:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Se non si è in grado di risolvere il nome DNS, alcune librerie client includono delle opzioni di configurazione come `sslHost` che viene fornita dal client StackExchange.Redis. In questo modo è possibile sostituire il nome host usato per la convalida del certificato. Ad esempio:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>È possibile usare reti virtuali con una cache Standard o Basic?
Le reti virtuali possono essere usate solo con cache Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-fail-in-some-subnets-but-not-others"></a>Perché la creazione di una cache Redis di Azure ha esito negativo in alcune subnet e non in altre?
Quando si distribuisce un'istanza di Cache Redis di Azure in una rete virtuale di Resource Manager, la cache deve trovarsi in una subnet dedicata che non contenga altri tipi di risorse. Se si tenta di distribuire un'istanza di Cache Redis di Azure in una rete virtuale di Resource Manager all'interno di una subnet contenente altre risorse, la distribuzione non riesce. Prima di poter creare una nuova cache Redis di Azure, è necessario eliminare le risorse esistenti all'interno della subnet.

È possibile distribuire più tipi di risorse in una rete virtuale classica, purché siano disponibili indirizzi IP sufficienti.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quali sono i requisiti di spazio per gli indirizzi di subnet?
Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oltre agli indirizzi IP usati dall'infrastruttura di rete virtuale di Azure, ogni istanza di Cache Redis nella subnet usa due indirizzi IP per partizione e un altro indirizzo IP per il bilanciamento del carico. Si presuppone che una cache non cluster includa una sola partizione.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Tutte le funzionalità della cache funzionano quando si ospita una cache in una rete virtuale?
Quando la cache fa parte di una rete virtuale, solo i client nella rete virtuale possono accedere alla cache. Di conseguenza, le seguenti funzionalità di gestione della cache non funzionano in questo momento.

* Console Redis: poiché la console Redis viene eseguita nel browser locale, che si trova esternamente alla rete virtuale, non può connettersi alla cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Usare ExpressRoute con Cache Redis di Azure

I clienti possono connettere un circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) all'infrastruttura di rete virtuale per estendere la rete locale ad Azure. 

Per impostazione predefinita, un circuito ExpressRoute appena creato non esegue il tunneling forzato (annuncio della route predefinita, 0.0.0.0/0) in una rete virtuale. Di conseguenza, la connettività Internet in uscita è consentita direttamente dalla rete virtuale e le applicazioni client sono in grado di connettersi agli altri endpoint di Azure tra cui la Cache Redis di Azure.

Tuttavia, una configurazione comune dei clienti è quella di usare il tunneling forzato (segnalare una route predefinita) verso la quale viene forzato il traffico Internet in uscita invece di far passare il flusso localmente. Questo flusso di traffico interrompe la connettività con Cache Redis di Azure se il traffico in uscita viene quindi bloccato in locale in modo da impedire all'istanza di Cache Redis di Azure di comunicare con le proprie dipendenze.

La soluzione consiste nel definire una o più route definite dall'utente (UDR, User Defined Route) nella subnet contenente la Cache Redis di Azure. Una route UDR definisce le route specifiche della subnet che verranno accettate invece della route predefinita.

Se possibile, è consigliabile utilizzare la seguente configurazione:

* La configurazione di ExpressRoute annuncia 0.0.0.0/0 e per impostazione predefinita esegue il tunneling forzato di tutto il traffico in uscita in un ambiente locale.
* La routine definita dall'utente applicata alla subnet contenente Cache Redis di Azure definisce 0.0.0.0/0 con una route di lavoro per il traffico TCP/IP verso la rete Internet pubblica, ad esempio impostando il tipo di hop successivo su "Internet".

L'effetto combinato di questi passaggi è che il livello di subnet UDR avrà la precedenza sul tunneling forzato di ExpressRoute, garantendo l'accesso a Internet in uscita da Cache Redis di Azure.

Per motivi di prestazioni, la connessione a un'istanza di Cache Redis di Azure da un'applicazione locale tramite ExpressRoute non è uno scenario di utilizzo tipico. Per ottenere prestazioni ottimali, i client di Cache Redis di Azure devono trovarsi nella stessa area di Cache Redis di Azure.

>[!IMPORTANT] 
>Le route definite in un UDR **devono** essere sufficientemente specifiche per avere la precedenza su qualsiasi route annunciata dalla configurazione di ExpressRoute. Nell'esempio di seguito viene utilizzato l'intervallo di indirizzi ampio 0.0.0.0/0 e pertanto può essere accidentalmente sottoposto a override dagli annunci di route mediante più intervalli di indirizzi specifici.

>[!WARNING]  
>Cache Redis di Azure non è supportato con le configurazioni di ExpressRoute che **annunciano erroneamente route dal percorso di peering pubblico al percorso di peering privato**. Le configurazioni di ExpressRoute per cui è configurato il peering pubblico riceveranno gli annunci delle route da Microsoft per un elevato numero di intervalli di indirizzi IP di Microsoft Azure. Se questi intervalli di indirizzi vengono annunciati in modo non corretto nel percorso di peering privato, il risultato finale è che tutti i pacchetti di rete in uscita dalla subnet dell'istanza di Cache Redis di Azure verranno erroneamente sottoposti a tunneling forzato verso l'infrastruttura di rete locale del cliente. Questo flusso di rete interromperà Cache Redis di Azure. La soluzione a questo problema consiste nell'interrompere l’annuncio di più route dal percorso di peering pubblico al percorso di peering privato.


Le informazioni generali sulle route definite dall'utente sono disponibili in questa [panoramica](../virtual-network/virtual-networks-udr-overview.md).

Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come usare altre funzionalità di cache premium.

* [Introduzione al livello Premium di Cache Redis di Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
