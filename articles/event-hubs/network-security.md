---
title: Sicurezza di rete per hub eventi di Azure
description: Questo articolo descrive come configurare l'accesso da endpoint privati
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: de4c5c6ddc658aab549ccf6960edbca3285e338d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85312841"
---
# <a name="network-security-for-azure-event-hubs"></a>Sicurezza di rete per hub eventi di Azure 
Questo articolo descrive come usare le funzionalità di sicurezza seguenti con hub eventi di Azure: 

- Tag di servizio
- Regole del firewall IP
- Endpoint servizio di rete
- Endpoint privati (anteprima)


## <a name="service-tags"></a>Tag di servizio
Un tag del servizio rappresenta un gruppo di prefissi di indirizzi IP di un determinato servizio di Azure. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag in base alla modifica degli indirizzi, riducendo la complessità degli aggiornamenti frequenti alle regole di sicurezza di rete. Per altre informazioni sui tag di servizio, vedere [Cenni preliminari sui tag di servizio](../virtual-network/service-tags-overview.md).

È possibile usare i tag del servizio per definire i controlli di accesso alla rete nei [gruppi di sicurezza di rete](../virtual-network/security-overview.md#security-rules) o in [Firewall di Azure](../firewall/service-tags.md). Usare i tag del servizio anziché indirizzi IP specifici quando si creano regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, **EventHub**) nel *source*   campo di origine o di *destinazione*appropriato   di una regola, è possibile consentire o negare il traffico per il servizio corrispondente.

| Tag di servizio | Scopo | È possibile usarlo in ingresso o in uscita? | Può essere regionale? | È possibile usarlo con Firewall di Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Hub eventi di Azure. | In uscita | Sì | Sì |


## <a name="ip-firewall"></a>Firewall IP 
Per impostazione predefinita, gli spazi dei nomi di Hub eventi sono accessibili da Internet, purché la richiesta sia accompagnata da un'autenticazione e da un'autorizzazione valide. Con un firewall per gli indirizzi IP, è possibile limitare ulteriormente l'accesso a un set di indirizzi IPv4 o a intervalli di indirizzi IPv4 in notazione [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Questa funzionalità è utile negli scenari in cui Hub eventi di Azure deve essere accessibile solo da siti noti specifici. Le regole del firewall consentono di configurare regole di ammissione del traffico proveniente da indirizzi IPv4 specifici. Se ad esempio si usa Hub eventi con [Azure ExpressRoute](/azure/expressroute/expressroute-faqs#supported-services), è possibile creare una **regola del firewall** per consentire traffico solo dagli indirizzi IP dell'infrastruttura locale. 

Le regole del firewall IP vengono applicate a livello dello spazio dei nomi di Hub eventi. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponde a una regola di indirizzi IP consentiti nello spazio dei nomi di Hub eventi viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Per ulteriori informazioni, vedere [come configurare un firewall IP per un hub eventi](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Endpoint servizio di rete
L'integrazione di Hub eventi con gli [endpoint del servizio della rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) consente di proteggere l'accesso alle funzionalità di messaggistica da carichi di lavoro come macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto a entrambe le estremità.

Una volta configurata per l'associazione ad almeno un endpoint del servizio subnet della rete virtuale, il rispettivo spazio dei nomi di hub eventi non accetta più il traffico da tutte le subnet autorizzate nelle reti virtuali. Dal punto di vista della rete virtuale, l'associazione di uno spazio dei nomi di Hub eventi a un endpoint del servizio consente di configurare un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica. 

Il risultato è una relazione privata e isolata tra i carichi di lavoro associati alla subnet e lo spazio dei nomi di Hub eventi corrispondente, nonostante l'indirizzo di rete osservabile dell'endpoint del servizio di messaggistica sia in un intervallo di IP pubblici. Si è verificata un'eccezione a questo comportamento. Per impostazione predefinita, l'abilitazione di un endpoint del servizio Abilita la `denyall` regola nel [firewall IP](event-hubs-ip-filtering.md) associato alla rete virtuale. È possibile aggiungere indirizzi IP specifici nel firewall IP per abilitare l'accesso all'endpoint pubblico dell'hub eventi. 

> [!IMPORTANT]
> Le reti virtuali sono supportate nei livelli **standard** e **dedicato** di Hub eventi. Il livello **Basic** non è supportato.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenari di sicurezza avanzati resi possibili dall'integrazione della rete virtuale 

Le soluzioni che richiedono una sicurezza rigida e compartimentata e dove le subnet della rete virtuale forniscono la segmentazione tra i servizi di compartimentazione, richiedono comunque percorsi di comunicazione tra i servizi che risiedono in questi raggruppamenti.

Qualsiasi route IP immediata tra i compartimenti, incluse quelle destinate al traffico HTTPS su TCP/IP, comportano il rischio di sfruttamento delle vulnerabilità dal livello rete verso l'alto. I servizi di messaggistica forniscono percorsi di comunicazione isolati, in cui i messaggi vengono scritti su disco durante la transizione tra le parti. I carichi di lavoro in due reti virtuali distinte associate alla stessa istanza di Hub eventi possono comunicare in modo efficiente e affidabile tramite messaggi, pur mantenendo l'integrità del confine di isolamento rete.
 
Questo significa che le soluzioni cloud con requisiti di sicurezza elevati non solo possono ottenere l'accesso alle funzionalità di messaggistica asincrona scalabili e affidabili leader del settore di Azure, ma possono ora usare la messaggistica per creare percorsi di comunicazione tra compartimenti sicuri della soluzione, intrinsecamente più sicuri di quanto sia possibile con qualsiasi modalità di comunicazione peer-to-peer, inclusi i protocolli HTTPS e altri protocolli socket protetti da TLS.

### <a name="bind-event-hubs-to-virtual-networks"></a>Associare Hub eventi a reti virtuali

Le **regole di rete virtuale** rappresentano la funzionalità di sicurezza firewall che controlla se lo spazio dei nomi di Hub eventi di Azure accetta le connessioni da una specifica subnet della rete virtuale.

L'associazione di uno spazio dei nomi di Hub eventi a una rete virtuale è un processo in due passaggi. Per prima cosa è necessario creare un **endpoint di servizio di rete virtuale** nella subnet di una rete virtuale e abilitarlo per **Microsoft. EventHub** come illustrato nell'articolo [Panoramica dell'endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) . Dopo aver aggiunto l'endpoint del servizio, è necessario associare lo spazio dei nomi di Hub eventi all'endpoint con una **regola di rete virtuale**.

La regola di rete virtuale è un'associazione dello spazio dei nomi di Hub eventi e una subnet della rete virtuale. Fino a quando esiste la regola, a tutti i carichi di lavoro associati alla subnet viene concesso l'accesso allo spazio dei nomi di Hub eventi. Hub eventi stesso non stabilisce mai le connessioni in uscita, non è necessario ottenere l'accesso e pertanto non viene mai concesso l'accesso alla subnet abilitando questa regola.

Per ulteriori informazioni, vedere [come configurare gli endpoint del servizio rete virtuale per un hub eventi](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Endpoint privati

Il [servizio di collegamento privato di Azure](../private-link/private-link-overview.md) consente di accedere ai servizi di Azure (ad esempio, Hub eventi di Azure, archiviazione di azure e Azure Cosmos DB) e ai servizi cliente/partner ospitati in Azure tramite un **endpoint privato** nella rete virtuale.

Un endpoint privato è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su Collegamento privato di Azure. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

> [!NOTE]
> Questa funzionalità è supportata solo con il livello **dedicato**. Per altre informazioni sul livello dedicato, vedere [Panoramica di Hub eventi Dedicato](event-hubs-dedicated-overview.md). 
>
> Questa funzionalità è attualmente disponibile in **anteprima**. 


Per altre informazioni, vedere [come configurare gli endpoint privati per un hub eventi](private-link-service.md)


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Come configurare un firewall IP per un hub eventi](event-hubs-ip-filtering.md)
- [Come configurare gli endpoint del servizio rete virtuale per un hub eventi](event-hubs-service-endpoints.md)
- [Come configurare gli endpoint privati per un hub eventi](private-link-service.md)