---
title: Configurare una rete virtuale-cache di Azure Premium per Redis
description: In questo articolo viene spiegato come creare e gestire il supporto di una rete virtuale per le istanze di Cache Redis di Azure Premium
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 5fd82105c94bb9be2d07c8843834465821acd8bc
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95803782"
---
# <a name="how-to-configure-virtual-network-support-for-a-premium-azure-cache-for-redis"></a>Come configurare il supporto di una rete virtuale per una Cache Redis di Azure Premium
Cache Redis di Azure include diverse soluzioni cache che offrono flessibilità di scelta riguardo alle dimensioni e alle funzionalità della cache, tra cui le funzionalità del livello Premium come clustering, persistenza e supporto per reti virtuali. Una rete virtuale è una rete privata nel cloud. Quando un'istanza di Cache Redis di Azure viene configurata con una rete virtuale, non è indirizzabile pubblicamente ed è accessibile solo da macchine virtuali e applicazioni all'interno della rete virtuale. Questo articolo descrive come configurare il supporto di una rete virtuale per un'istanza Premium di Cache Redis di Azure.

> [!NOTE]
> Cache Redis di Azure supporta le reti virtuali classiche e di Resource Manager.
> 
> 

## <a name="why-vnet"></a>Perché usare una rete virtuale?
La distribuzione di [rete virtuale di Azure (VNet)](https://azure.microsoft.com/services/virtual-network/) offre sicurezza e isolamento avanzati per la cache di Azure per Redis, oltre a subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso.

## <a name="virtual-network-support"></a>Supporto della rete virtuale
Il supporto della rete virtuale viene configurato nel pannello **Nuova cache Redis di azure** durante la creazione della cache. 

1. Per creare una cache Premium, accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**. Si noti che, oltre a creare le cache nella portale di Azure, è anche possibile crearle usando modelli Gestione risorse, PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni sulla creazione di un'istanza di Cache Redis di Azure, vedere [Creare una cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Creare una risorsa.":::
   
2. Nella pagina **Nuovo** selezionare **Database** e quindi **Cache di Azure per Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selezionare Cache di Azure per Redis.":::

3. Nella pagina **nuova cache Redis** configurare le impostazioni per la nuova cache Premium.
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. | 
   | **Sottoscrizione** | A discesa e selezionare la sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
   | **Gruppo di risorse** | A discesa e selezionare un gruppo di risorse oppure selezionare **Crea nuovo** e immettere un nuovo nome per il gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Posizione** | A discesa e selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
   | **Tipo di cache** | A discesa e selezionare una cache Premium per configurare le funzionalità Premium. Per informazioni dettagliate, vedere [cache di Azure per i prezzi di redis](https://azure.microsoft.com/pricing/details/cache/). |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](cache-overview.md). |

4. Selezionare la scheda **Rete** o fare clic sul pulsante **Rete** nella parte inferiore della pagina.

5. Nella scheda **rete** selezionare **reti virtuali** come metodo di connettività. Per usare una nuova rete virtuale, crearla prima di tutto seguendo i passaggi descritti in [creare una rete virtuale usando il portale di Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) o [creare una rete virtuale (classica) usando il portale di Azure](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) e tornare al pannello **nuova cache di Azure per Redis** per creare e configurare la cache Premium.

   > [!IMPORTANT]
   > Quando si distribuisce Cache Redis di Azure in una rete virtuale di Resource Manager, la cache deve trovarsi in una subnet dedicata che non contenga altre risorse, ad eccezione delle istanze di Cache Redis di Azure. Se si tenta di distribuire un'istanza di Cache Redis di Azure in una rete virtuale di Resource Manager all'interno di una subnet contenente altre risorse, la distribuzione non riesce.
   > 
   > 

   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Rete virtuale** | A discesa e selezionare la rete virtuale. | Selezionare una rete virtuale che si trova nella stessa sottoscrizione e nella stessa posizione della cache. | 
   | **Subnet** | A discesa e selezionare la subnet. | L'intervallo di indirizzi della subnet deve essere in notazione CIDR, ad esempio 192.168.1.0/24. Deve essere incluso nello spazio indirizzi della rete virtuale. | 
   | **Indirizzo IP statico** | Opzionale Immettere un indirizzo IP statico. | Se non si specifica un indirizzo IP statico, viene scelto automaticamente un indirizzo IP. | 

   > [!IMPORTANT]
   > Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Oltre agli indirizzi IP usati dall'infrastruttura di rete virtuale di Azure, ogni istanza di Cache Redis nella subnet usa due indirizzi IP per partizione e un altro indirizzo IP per il bilanciamento del carico. Si presuppone che una cache non cluster includa una sola partizione.
   > 
   > 

6. Fare clic sul pulsante **Avanti: Avanzate** oppure fare clic sulla scheda **Avanti: Avanzate** nella parte inferiore della pagina.

7. Nella scheda **Avanzate** per un'istanza di cache Premium configurare le impostazioni per la porta non TLS, il clustering e la persistenza dei dati. 

8. Fare clic sul pulsante **Avanti: Tag** o fare clic sulla scheda **Avanti: Tag** nella parte inferiore della pagina.

9. Facoltativamente, nella scheda **Tag** immettere il nome e il valore se si vuole categorizzare la risorsa. 

10. Selezionare **Rivedi e crea**. Si viene reindirizzati alla scheda Rivedi e crea in cui Azure convalida la configurazione.

11. Quando viene visualizzato il messaggio di convalida verde, selezionare **Crea**.

La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina **Panoramica** della cache di Azure per Redis. Quando l'elemento **Stato** indica **In esecuzione**, la cache è pronta per l'uso. Dopo aver creato la cache, è possibile visualizzare la configurazione della rete virtuale facendo clic su **Rete virtuale** dal **menu Risorse**.

![Rete virtuale][redis-cache-vnet-info]

Per connettersi all'istanza di Cache Redis di Azure quando si usa una rete virtuale, specificare il nome host della cache nella stringa di connessione, come mostrato nell'esempio seguente:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
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
```

## <a name="azure-cache-for-redis-vnet-faq"></a>Domande frequenti su Cache Redis di Azure
Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sulla rete virtuale di Cache Redis di Azure.

* Quali sono alcuni problemi comuni di configurazione errata per Cache Redis di Azure e le reti virtuali?
* [Come è possibile verificare che la cache funzioni in una VNET?](#how-can-i-verify-that-my-cache-is-working-in-a-vnet)
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

Sono previsti nove requisiti per le porte in uscita. Le richieste in uscita in questi intervalli sono in uscita con altri servizi necessari per il funzionamento della cache o interni alla subnet Redis per la comunicazione tra nodi. Per la replica geografica sono disponibili altri requisiti in uscita per la comunicazione tra subnet della cache primaria e di replica.

| Porte | Direzione | Protocollo di trasporto | Scopo | IP locale | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |In uscita |TCP |Dipendenze Redis in Archiviazione di Azure/PKI (Internet) | (Subnet Redis) |* <sup>4</sup> |
| 443 | In uscita | TCP | Dipendenza Redis da Azure Key Vault e monitoraggio di Azure | (Subnet Redis) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |In uscita |TCP/UDP |Dipendenze Redis nel DNS (Internet/rete virtuale) | (Subnet Redis) | 168.63.129.16 e 169.254.169.254 <sup>2</sup> e qualsiasi server DNS personalizzato per la subnet <sup>3</sup> |
| 8443 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) | (Subnet Redis) |
| 10221-10231 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) | (Subnet Redis) |
| 20226 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |
| 13000-13999 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |
| 15000-15999 |In uscita |TCP |Comunicazioni interne per Redis e Geo-Replication | (Subnet Redis) |(Subnet Redis) (Subnet peer di replica geografica) |
| 6379-6380 |In uscita |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |

<sup>1</sup> è possibile usare i tag di servizio ' AzureKeyVault ' è AzureMonitor ' con gestione risorse gruppi di sicurezza di rete.

<sup>2</sup> questi indirizzi IP di proprietà di Microsoft vengono usati per gestire la macchina virtuale host che funge da DNS di Azure.

<sup>3</sup> non necessario per le subnet senza server DNS personalizzato o cache Redis più recenti che ignorano il DNS personalizzato.

<sup>4</sup> per ulteriori informazioni, vedere [requisiti di connettività di rete VNET aggiuntivi](#additional-vnet-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Requisiti per le porte peer per la replica geografica

Se si usa la replica geografica tra le cache nelle reti virtuali di Azure, si noti che la configurazione consigliata prevede di sbloccare le porte 15000-15999 per l'intera subnet in entrambe le direzioni in ingresso e in uscita per entrambe le cache, in modo che tutti i componenti di replica nella subnet possano comunicare direttamente tra loro anche in caso di failover geografico futuro.

#### <a name="inbound-port-requirements"></a>Requisiti delle porte in ingresso

Vi sono otto requisiti delle porte in ingresso. Le richieste in ingresso in questi intervalli provengono da altri servizi ospitati nella stessa VNET o sono interne alle comunicazioni subnet di Redis.

| Porte | Direzione | Protocollo di trasporto | Scopo | IP locale | IP remoto |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |In ingresso |TCP |Comunicazione tra client e Redis, bilanciamento del carico di Azure | (Subnet Redis) | (Subnet Redis), (subnet client), AzureLoadBalancer <sup>1</sup> |
| 8443 |In ingresso |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |
| 8500 |In ingresso |TCP/UDP |Bilanciamento del carico di Azure | (Subnet Redis) | AzureLoadBalancer |
| 10221-10231 |In ingresso |TCP |Comunicazione tra client e cluster Redis, comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis), AzureLoadBalancer, (subnet client) |
| 13000-13999 |In ingresso |TCP |Comunicazione tra client e cluster Redis, bilanciamento del carico di Azure | (Subnet Redis) | (Subnet Redis), (subnet client), AzureLoadBalancer |
| 15000-15999 |In ingresso |TCP |Comunicazione tra client e cluster Redis, bilanciamento del carico di Azure e Geo-Replication | (Subnet Redis) | (Subnet Redis), (subnet client), AzureLoadBalancer, (subnet peer di replica geografica) |
| 16001 |In ingresso |TCP/UDP |Bilanciamento del carico di Azure | (Subnet Redis) | AzureLoadBalancer |
| 20226 |In ingresso |TCP |Comunicazioni interne per Redis | (Subnet Redis) |(Subnet Redis) |

<sup>1</sup> è possibile usare il tag di servizio ' AzureLoadBalancer ' (Gestione risorse) (o ' AZURE_LOADBALANCER ' per la distribuzione classica) per la creazione delle regole NSG.

#### <a name="additional-vnet-network-connectivity-requirements"></a>Ulteriori requisiti di connettività della rete VNET

Esistono requisiti di connettività di rete per Cache Redis di Azure che potrebbero non essere inizialmente soddisfatti in una rete virtuale. Per il corretto funzionamento se usato all'interno di una rete virtuale, Cache Redis di Azure richiede tutti gli elementi seguenti.

* Connettività di rete in uscita per endpoint di archiviazione di Azure in tutto il mondo. Sono inclusi gli endpoint che si trovano nella stessa area dell'istanza di Cache Redis di Azure, nonché gli endpoint di archiviazione che si trovano in **altre** aree di Azure. Gli endpoint di archiviazione di Azure vengono risolti nei seguenti domini DNS: *Table.Core.Windows.NET*, *BLOB.Core.Windows.NET*, *queue.Core.Windows.NET* e *file.Core.Windows.NET*. 
* Connettività di rete in uscita a *OCSP.DigiCert.com*, *CRL4.DigiCert.com*, *OCSP.msocsp.com*, *mscrl.Microsoft.com*, *crl3.DigiCert.com*, *cacerts.DigiCert.com*, *oneocsp.Microsoft.com* e *CRL.Microsoft.com*. Questa connettività è necessaria per supportare la funzionalità TLS/SSL.
* La configurazione DNS per la rete virtuale deve essere in grado di risolvere tutti gli endpoint e i domini indicati nei punti precedenti. Questi requisiti DNS possono essere soddisfatti garantendo che un'infrastruttura DNS valida venga configurata e mantenuta per la rete virtuale.
* Connettività di rete in uscita agli endpoint di monitoraggio di Azure seguenti, che vengono risolti nei seguenti domini DNS: *shoebox2-Black.shoebox2.Metrics.nsatc.NET*, *North-prod2.prod2.Metrics.nsatc.NET*, *azglobal-Black.azglobal.Metrics.nsatc.NET*, *shoebox2-Red.shoebox2.Metrics.nsatc.NET*, *East-prod2.prod2.Metrics.nsatc.NET*, *azglobal-Red.azglobal.Metrics.nsatc.NET*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-vnet"></a>Come è possibile verificare che la cache funzioni in una rete virtuale?

>[!IMPORTANT]
>Quando ci si connette a una cache di Azure per l'istanza di redis ospitata in una VNET, i client della cache devono trovarsi nello stesso VNET o in un VNET con peering VNET abilitato nella stessa area di Azure. La Peering reti virtuali globale non è attualmente supportata. Questo vale anche per le applicazioni di test e per gli strumenti per l'esecuzione di ping di diagnostica. Indipendentemente dalla posizione in cui è ospitata l'applicazione client, i gruppi di sicurezza di rete o altri livelli di rete devono essere configurati in modo che il traffico di rete del client sia autorizzato a raggiungere l'istanza di Redis.
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
Se si distribuisce una cache di Azure per Redis in una VNet, la cache deve trovarsi in una subnet dedicata che non contiene altri tipi di risorse. Se viene effettuato un tentativo di distribuire una cache di Azure per Redis in una Gestione risorse subnet VNet che contiene altre risorse, ad esempio gateway applicazione, NAT in uscita e così via, la distribuzione non riuscirà in genere. Prima di poter creare una nuova cache di Azure per Redis, è necessario eliminare le risorse esistenti di altri tipi.

È inoltre necessario disporre di un numero sufficiente di indirizzi IP disponibili nella subnet.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quali sono i requisiti di spazio per gli indirizzi di subnet?
Azure riserva alcuni indirizzi IP all'interno di ogni subnet e questi indirizzi non possono essere usati. Il primo e l'ultimo indirizzo IP delle subnet sono riservati per motivi di conformità al protocollo, insieme ad altri tre indirizzi usati per i servizi di Azure. Per altre informazioni, vedere [Esistono restrizioni sull'uso di indirizzi IP all'interno di tali subnet?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Oltre agli indirizzi IP usati dall'infrastruttura VNET di Azure, ogni istanza di redis nella subnet usa due indirizzi IP per ogni partizione del cluster (più indirizzi IP aggiuntivi per eventuali repliche aggiuntive) e un indirizzo IP aggiuntivo per il servizio di bilanciamento del carico. Si presuppone che una cache non cluster includa una sola partizione.

### <a name="do-all-cache-features-work-when-hosting-a-cache-in-a-vnet"></a>Tutte le funzionalità della cache funzionano quando si ospita una cache in una rete virtuale?
Quando la cache fa parte di una rete virtuale, solo i client nella rete virtuale possono accedere alla cache. Di conseguenza, le seguenti funzionalità di gestione della cache non funzionano in questo momento.

* Console redis: poiché la console Redis viene eseguita nel browser locale, che in genere si trova in un computer di sviluppo non connesso a VNET, non è in grado di connettersi alla cache.


## <a name="use-expressroute-with-azure-cache-for-redis"></a>Usare ExpressRoute con Cache Redis di Azure

I clienti possono connettere un circuito [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) all'infrastruttura di rete virtuale per estendere la rete locale ad Azure. 

Per impostazione predefinita, un circuito ExpressRoute appena creato non esegue il tunneling forzato (annuncio della route predefinita, 0.0.0.0/0) in una rete virtuale. Di conseguenza, la connettività Internet in uscita è consentita direttamente dalla rete virtuale e le applicazioni client sono in grado di connettersi agli altri endpoint di Azure tra cui la Cache Redis di Azure.

Tuttavia, una configurazione comune dei clienti prevede l'uso del tunneling forzato (annunciare una route predefinita) che forza il traffico Internet in uscita verso il flusso locale. Questo flusso di traffico interrompe la connettività con Cache Redis di Azure se il traffico in uscita viene quindi bloccato in locale in modo da impedire all'istanza di Cache Redis di Azure di comunicare con le proprie dipendenze.

La soluzione consiste nel definire una o più route definite dall'utente (UDR, User Defined Route) nella subnet contenente la Cache Redis di Azure. Una route UDR definisce le route specifiche della subnet che verranno accettate invece della route predefinita.

Se possibile, è consigliabile utilizzare la seguente configurazione:

* La configurazione di ExpressRoute annuncia 0.0.0.0/0 e per impostazione predefinita esegue il tunneling forzato di tutto il traffico in uscita in un ambiente locale.
* La routine definita dall'utente applicata alla subnet contenente Cache Redis di Azure definisce 0.0.0.0/0 con una route di lavoro per il traffico TCP/IP verso la rete Internet pubblica, ad esempio impostando il tipo di hop successivo su "Internet".

L'effetto combinato di questi passaggi è che il livello di subnet UDR avrà la precedenza sul tunneling forzato di ExpressRoute, garantendo l'accesso a Internet in uscita da Cache Redis di Azure.

Per motivi di prestazioni, la connessione a un'istanza di Cache Redis di Azure da un'applicazione locale tramite ExpressRoute non è uno scenario di utilizzo tipico. Per ottenere prestazioni ottimali, i client di Cache Redis di Azure devono trovarsi nella stessa area di Cache Redis di Azure.

>[!IMPORTANT] 
>Le route definite in un UDR **devono** essere sufficientemente specifiche per avere la precedenza su qualsiasi route annunciata dalla configurazione di ExpressRoute. Nell'esempio di seguito viene utilizzato l'intervallo di indirizzi ampio 0.0.0.0/0 e pertanto può essere accidentalmente sottoposto a override dagli annunci di route mediante più intervalli di indirizzi specifici.

>[!WARNING]  
>Cache Redis di Azure non è supportato con le configurazioni di ExpressRoute che **annunciano erroneamente route dal percorso di peering pubblico al percorso di peering privato**. Le configurazioni di ExpressRoute per cui è configurato il peering pubblico riceveranno gli annunci delle route da Microsoft per un elevato numero di intervalli di indirizzi IP di Microsoft Azure. Se questi intervalli di indirizzi vengono annunciati in modo non corretto nel percorso di peering privato, il risultato finale è che tutti i pacchetti di rete in uscita dalla subnet dell'istanza di Cache Redis di Azure verranno erroneamente sottoposti a tunneling forzato verso l'infrastruttura di rete locale del cliente. Questo flusso di rete interromperà Cache Redis di Azure. La soluzione a questo problema consiste nell'interrompere l'annuncio di più route dal percorso di peering pubblico al percorso di peering privato.


Le informazioni generali sulle route definite dall'utente sono disponibili in questa [panoramica](../virtual-network/virtual-networks-udr-overview.md).

Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle funzionalità di cache di Azure per Redis.

* [Cache di Azure per i livelli di servizio di redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
