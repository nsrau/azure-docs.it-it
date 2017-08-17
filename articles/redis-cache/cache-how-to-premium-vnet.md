---
title: Configurare una rete virtuale per un'istanza di Cache Redis di Azure Premium | Microsoft Docs
description: In questo articolo viene illustrato come creare e gestire il supporto di una rete virtuale per le istanze di Cache Redis di Azure Premium.
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 8b1e43a0-a70e-41e6-8994-0ac246d8bf7f
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: sdanie
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: c758aa5955362d04abf69c760d2aed7983cdf102
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-redis-cache"></a>Come configurare il supporto di una rete virtuale per un'istanza Premium di Cache Redis di Azure
Cache Redis di Azure include diverse soluzioni cache che offrono flessibilità di scelta riguardo alle dimensioni e alle funzionalità della cache, tra cui le funzionalità del livello Premium come clustering, persistenza e supporto per reti virtuali. Una rete virtuale è una rete privata nel cloud. Quando un'istanza di Cache Redis di Azure viene configurata con una rete virtuale, non è indirizzabile pubblicamente ed è accessibile solo da macchine virtuali e applicazioni all'interno della rete virtuale. Questo articolo descrive come configurare il supporto di una rete virtuale per un'istanza Premium di Cache Redis di Azure.

> [!NOTE]
> Cache Redis di Azure supporta le reti virtuali classiche e di Resource Manager.
> 
> 

Per informazioni su altre funzionalità di cache premium, vedere [Introduzione al piano Premium di Cache Redis di Azure](cache-premium-tier-intro.md).

## <a name="why-vnet"></a>Perché usare una rete virtuale?
La distribuzione di [Rete virtuale di Azure](https://azure.microsoft.com/services/virtual-network/) offre sicurezza e isolamento migliorati per Cache Redis di Azure, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso.

## <a name="virtual-network-support"></a>Supporto della rete virtuale
Il supporto della rete virtuale viene configurato nel pannello **Nuova cache Redis** durante la creazione della cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Dopo aver selezionato un piano tariffario Premium, è possibile configurare l'integrazione rete virtuale di Cache Redis selezionando una rete virtuale nella stessa sottoscrizione e nella stessa posizione della cache. Per usare una nuova rete virtuale, è necessario prima crearla seguendo i passaggi in [Creare una rete virtuale usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o in [Creare una rete virtuale (classica) usando il portale di Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md), quindi tornare al pannello **Nuova cache Redis** per creare e configurare la cache Premium.

Per configurare la rete virtuale per la nuova cache, fare clic su **Rete virtuale** nel pannello **Nuova cache Redis** e selezionare la rete virtuale desiderata dall'elenco a discesa.

![Rete virtuale][redis-cache-vnet]

Nell'elenco a discesa **Subnet** selezionare la subnet desiderata e specificare l'**indirizzo IP statico**. Se si usa una rete virtuale classica, il campo **Indirizzo IP statico** è facoltativo e, se non è specificato, ne verrà scelto uno dalla subnet selezionata.

> [!IMPORTANT]
> Quando si distribuisce Cache Redis di Azure in una rete virtuale di Resource Manager, la cache deve trovarsi in una subnet dedicata che non contiene altre risorse, ad eccezione delle istanze di Cache Redis di Azure. Se si tenta di distribuire un'istanza di Cache Redis di Azure in una rete virtuale di Resource Manager all'interno di una subnet contenente altre risorse, la distribuzione non riesce.
> 
> 

![Rete virtuale][redis-cache-vnet-ip]

> [!IMPORTANT]
> Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
> 
> Oltre agli indirizzi IP usati dall'infrastruttura di rete virtuale di Azure, ogni istanza di Cache Redis nella subnet usa due indirizzi IP per partizione e un altro indirizzo IP per il bilanciamento del carico. Si presuppone che una cache non cluster includa una sola partizione.
> 
> 

Dopo aver creato la cache, è possibile visualizzare la configurazione della rete virtuale facendo clic su **Rete virtuale** dal **menu Risorse**.

![Rete virtuale][redis-cache-vnet-info]

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

## <a name="azure-redis-cache-vnet-faq"></a>Domande frequenti sulla rete virtuale di Cache Redis di Azure
Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sulla rete virtuale di Cache Redis di Azure.

* [Quali sono alcuni problemi comuni di configurazione errata per Cache Redis di Azure e le reti virtuali?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
* [Come è possibile verificare che la cache funzioni in una rete virtuale?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
* [È possibile usare reti virtuali con una cache Standard o Basic?](#can-i-use-vnets-with-a-standard-or-basic-cache)
* [Perché la creazione di una cache Redis ha esito negativo in alcune subnet e non in altre?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)
* [Quali sono i requisiti di spazio per gli indirizzi di subnet?](#what-are-the-subnet-address-space-requirements)
* [Tutte le funzionalità della cache funzionano quando si ospita una cache in una rete virtuale?](#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)

## <a name="what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets"></a>Quali sono alcuni problemi comuni di configurazione errata per Cache Redis di Azure e le reti virtuali?
Quando Cache Redis di Azure è ospitata in una rete virtuale, vengono usate le porte indicate nelle tabelle seguenti. 

>[!IMPORTANT]
>Se le porte nelle tabelle seguenti sono bloccate, la cache potrebbe non funzionare correttamente. Il blocco di una o più di queste porte è il problema di configurazione più comune nell'uso di Cache Redis di Azure in una rete virtuale.
> 
> 

- [Requisiti delle porte in uscita](#outbound-port-requirements)
- [Requisiti delle porte in ingresso](#inbound-port-requirements)

### <a name="outbound-port-requirements"></a>Requisiti delle porte in uscita

Vi sono sette requisiti delle porte in uscita.

- Se lo si desidera, tutte le connessioni a Internet in uscita possono essere eseguite tramite un dispositivo di controllo locale del cliente.
- Tre delle porte instradano il traffico a endpoint di Azure che servono Archiviazione di Azure e Azure DNS.
- Gli intervalli di porte rimanenti sono destinati alle comunicazioni interne subnet di Redis. Per le comunicazioni subnet interne di Redis non è richiesta alcuna regola NSG subnet.

| Porte | Direzione | Protocollo di trasporto | Scopo | IP remoto |
| --- | --- | --- | --- | --- |
| 80, 443 |In uscita |TCP |Dipendenze Redis in Archiviazione di Azure/PKI (Internet) |* |
| 53 |In uscita |TCP/UDP |Dipendenze Redis nel DNS (Internet/rete virtuale) |* |
| 8443 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |
| 10221-10231 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |
| 20226 |In uscita |TCP |Comunicazioni interne per Redis |(Subnet Redis) |
| 13000-13999 |In uscita |TCP |Comunicazioni interne per Redis |(Subnet Redis) |
| 15000-15999 |In uscita |TCP |Comunicazioni interne per Redis |(Subnet Redis) |


### <a name="inbound-port-requirements"></a>Requisiti delle porte in ingresso

Vi sono otto requisiti delle porte in ingresso. Le richieste in ingresso in questi intervalli provengono da altri servizi ospitati nella stessa VNET o sono interne alle comunicazioni subnet di Redis.

| Porte | Direzione | Protocollo di trasporto | Scopo | IP remoto |
| --- | --- | --- | --- | --- |
| 6379, 6380 |In ingresso |TCP |Comunicazione tra client e Redis, bilanciamento del carico di Azure |Rete virtuale, Bilanciamento carico di Azure |
| 8443 |In ingresso |TCP |Comunicazioni interne per Redis |(Subnet Redis) |
| 8500 |In ingresso |TCP/UDP |Bilanciamento del carico di Azure |Azure Load Balancer |
| 10221-10231 |In ingresso |TCP |Comunicazioni interne per Redis |(Subnet Redis), bilanciamento del carico di Azure |
| 13000-13999 |In ingresso |TCP |Comunicazione tra client e cluster Redis, bilanciamento del carico di Azure |Rete virtuale, Bilanciamento carico di Azure |
| 15000-15999 |In ingresso |TCP |Comunicazione tra client e cluster Redis, bilanciamento del carico di Azure |Rete virtuale, Bilanciamento carico di Azure |
| 16001 |In ingresso |TCP/UDP |Bilanciamento del carico di Azure |Azure Load Balancer |
| 20226 |In ingresso |TCP |Comunicazioni interne per Redis |(Subnet Redis) |

### <a name="additional-vnet-network-connectivity-requirements"></a>Ulteriori requisiti di connettività della rete VNET

Esistono requisiti di connettività di rete per Cache Redis di Azure che potrebbero non essere inizialmente soddisfatti in una rete virtuale. Per il corretto funzionamento se usato all'interno di una rete virtuale, Cache Redis di Azure richiede tutti gli elementi seguenti.

* Connettività di rete in uscita per endpoint di archiviazione di Azure in tutto il mondo. Sono inclusi gli endpoint che si trovano nella stessa area dell'istanza di Cache Redis di Azure, nonché gli endpoint di archiviazione che si trovano in **altre** aree di Azure. Gli endpoint di Archiviazione di Azure vengono risolti nei seguenti domini DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*. 
* Connettività di rete in uscita verso *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*. Tale connettività è necessaria per il supporto della funzionalità SSL.
* La configurazione DNS per la rete virtuale deve essere in grado di risolvere tutti gli endpoint e i domini indicati nei punti precedenti. Questi requisiti DNS possono essere soddisfatti garantendo che un'infrastruttura DNS valida venga configurata e mantenuta per la rete virtuale.
* Connettività di rete in uscita agli endpoint di Monitoraggio di Azure seguenti, che vengono risolti nei domini DNS seguenti: shoebox2-black.shoebox2.metrics.nsatc.net, north-prod2.prod2.metrics.nsatc.net, azglobal-black.azglobal.metrics.nsatc.net, shoebox2-red.shoebox2.metrics.nsatc.net, east-prod2.prod2.metrics.nsatc.net, azglobal-red.azglobal.metrics.nsatc.net.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Come è possibile verificare che la cache funzioni in una rete virtuale?

>[!IMPORTANT]
>Quando ci si connette a un'istanza di Cache Redis di Azure ospitata in una rete virtuale, i client della cache devono trovarsi nella stessa rete virtuale, incluse eventuali applicazioni di test o strumenti di diagnostica ping.
>
>

Dopo aver configurato i requisiti delle porte come descritto nella sezione precedente, è possibile verificare che la cache funzioni eseguendo la procedura seguente.

- [Riavviare](cache-administration.md#reboot) tutti i nodi della cache. Se non è possibile raggiungere tutte le dipendenze della cache richieste (come descritto in [Requisiti delle porte in ingresso](cache-how-to-premium-vnet.md#inbound-port-requirements) e [Requisiti delle porte in uscita](cache-how-to-premium-vnet.md#outbound-port-requirements)), la cache non sarà in grado di riavviarsi.
- Dopo il riavvio dei nodi della cache (come riportato dallo stato della cache nel portale di Azure), è possibile eseguire i test seguenti:
  - eseguire il ping dell'endpoint della cache (tramite la porta 6380) da un computer che si trova all'interno della stessa rete virtuale della cache tramite [tcping](https://www.elifulkerson.com/projects/tcping.php). ad esempio:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Se lo strumento `tcping` indica che la porta è aperta, la cache è disponibile per la connessione dai client nella rete virtuale.

  - Un altro modo per eseguire il test è quello di creare un client della cache di test (che potrebbe essere una semplice applicazione della console tramite StackExchange.Redis) che si connette alla cache, aggiunge e recupera alcuni elementi dalla cache. Installare l'applicazione client di esempio in una macchina virtuale che si trova nella stessa rete virtuale della cache ed eseguirla per verificare la connettività alla cache.


### <a name="can-i-use-vnets-with-a-standard-or-basic-cache"></a>È possibile usare reti virtuali con una cache Standard o Basic?
Le reti virtuali possono essere usate solo con cache Premium.

### <a name="why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others"></a>Perché la creazione di una cache Redis ha esito negativo in alcune subnet e non in altre?
Quando si distribuisce un'istanza di Cache Redis di Azure in una rete virtuale di Resource Manager, la cache deve trovarsi in una subnet dedicata che non contiene altri tipi di risorse. Se si tenta di distribuire un'istanza di Cache Redis di Azure in una rete virtuale di Resource Manager all'interno di una subnet contenente altre risorse, la distribuzione non riesce. Prima di poter creare una nuova cache Redis, è necessario eliminare le risorse esistenti all'interno della subnet.

È possibile distribuire più tipi di risorse in una rete virtuale classica, purché siano disponibili indirizzi IP sufficienti.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quali sono i requisiti di spazio per gli indirizzi di subnet?
Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oltre agli indirizzi IP usati dall'infrastruttura di rete virtuale di Azure, ogni istanza di Cache Redis nella subnet usa due indirizzi IP per partizione e un altro indirizzo IP per il bilanciamento del carico. Si presuppone che una cache non cluster includa una sola partizione.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Tutte le funzionalità della cache funzionano quando si ospita una cache in una rete virtuale?
Quando la cache fa parte di una rete virtuale, solo i client nella rete virtuale possono accedere alla cache. Di conseguenza, le seguenti funzionalità di gestione della cache non funzionano in questo momento.

* Console Redis: poiché la console Redis viene eseguita nel browser locale, che si trova esternamente alla rete virtuale, non può connettersi alla cache.

## <a name="use-expressroute-with-azure-redis-cache"></a>Usare ExpressRoute con Cache Redis di Azure
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

* [Introduzione al piano Premium di Cache Redis di Azure](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png


