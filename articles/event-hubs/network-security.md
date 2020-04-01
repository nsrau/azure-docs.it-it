---
title: Sicurezza di rete per gli hub eventi di AzureNetwork security for Azure Event Hubs
description: In questo articolo viene descritto come configurare l'accesso da endpoint privati
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 46e6a9ecc2ed09aed1076f12c1f61a966485bdad
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422775"
---
# <a name="network-security-for-azure-event-hubs"></a>Sicurezza di rete per gli hub eventi di AzureNetwork security for Azure Event Hubs 
Questo articolo descrive come usare le funzionalità di sicurezza seguenti con gli hub eventi di Azure:This article describes how to use the following security features with Azure Event Hubs: 

- Tag di servizio
- Regole del firewall IP
- Endpoint del servizio di rete
- Endpoint privati (anteprima)Private endpoints (preview)


## <a name="service-tags"></a>Tag di servizio
Un tag di servizio rappresenta un gruppo di prefissi di indirizzi IP da un determinato servizio di Azure.A service tag represents a group of IP address prefixes from a given Azure service. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag di servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano, riducendo al minimo la complessità degli aggiornamenti frequenti alle regole di sicurezza di rete. Per ulteriori informazioni sui tag di servizio, consultate [Panoramica](../virtual-network/service-tags-overview.md)dei tag di servizio.

È possibile usare i tag di servizio per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o in Firewall di Azure.You can use service tags to define network access controls on [network security groups](../virtual-network/security-overview.md#security-rules) or [Azure Firewall](../firewall/service-tags.md). Utilizzare i tag di servizio al posto di indirizzi IP specifici quando si creano regole di sicurezza. Specificando il nome del tag del servizio (ad **esempio, EventHub**) nel campo di *origine* o di *destinazione* appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente.

| Tag di servizio | Scopo | È possibile utilizzare in ingresso o in uscita? | Può essere regionale? | È possibile usare Firewall di Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Hub eventi di Azure.Azure Event Hubs. | In uscita | Sì | Sì |


## <a name="ip-firewall"></a>Firewall IP 
Per impostazione predefinita, gli spazi dei nomi degli hub eventi sono accessibili da Internet finché la richiesta viene fornita con l'autenticazione e l'autorizzazione valide. Con il firewall IP, è possibile limitarlo ulteriormente a un set di indirizzi IPv4 o intervalli di indirizzi IPv4 nella notazione [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Questa funzionalità è utile negli scenari in cui gli hub eventi di Azure devono essere accessibili solo da determinati siti noti. Le regole del firewall consentono di configurare le regole per accettare il traffico proveniente da indirizzi IPv4 specifici. Ad esempio, se si usano Hub eventi con [Azure Express Route,](/azure/expressroute/expressroute-faqs#supported-services)è possibile creare una **regola del firewall** per consentire il traffico solo dagli indirizzi IP dell'infrastruttura locale. 

Le regole del firewall IP vengono applicate a livello di spazio dei nomi Hub eventi. Vengono pertanto applicate a tutte le connessioni provenienti dai client con qualsiasi protocollo supportato. Qualsiasi tentativo di connessione proveniente da un indirizzo IP che non corrisponde a una regola di indirizzi IP consentiti nello spazio dei nomi di Hub eventi viene rifiutato come non autorizzato. Nella risposta non viene fatto riferimento alla regola IP. Le regole del filtro IP vengono applicate in ordine e la prima regola corrispondente all'indirizzo IP determina l'azione di accettazione o rifiuto.

Per altre informazioni, vedere Come configurare il [firewall IP per un hub eventiFor](event-hubs-ip-filtering.md) more information, see How to configure IP firewall for an event hub

## <a name="network-service-endpoints"></a>Endpoint del servizio di rete
L'integrazione di Hub eventi con gli [endpoint del servizio della rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md) consente di proteggere l'accesso alle funzionalità di messaggistica da carichi di lavoro come macchine virtuali associate a reti virtuali, con il percorso del traffico di rete protetto a entrambe le estremità.

Una volta configurato per essere associato ad almeno un endpoint del servizio subnet di rete virtuale, il rispettivo spazio dei nomi Hub eventi non accetta più traffico da qualsiasi luogo, ma subnet autorizzate nelle reti virtuali. Dal punto di vista della rete virtuale, l'associazione di uno spazio dei nomi di Hub eventi a un endpoint del servizio consente di configurare un tunnel di rete isolato dalla subnet della rete virtuale al servizio di messaggistica. 

Il risultato è una relazione privata e isolata tra i carichi di lavoro associati alla subnet e lo spazio dei nomi di Hub eventi corrispondente, nonostante l'indirizzo di rete osservabile dell'endpoint del servizio di messaggistica sia in un intervallo di IP pubblici. Esiste un'eccezione a questo comportamento. L'abilitazione di un endpoint `denyall` del servizio, per impostazione predefinita, abilita la regola nel [firewall IP](event-hubs-ip-filtering.md) associato alla rete virtuale. È possibile aggiungere indirizzi IP specifici nel firewall IP per abilitare l'accesso all'endpoint pubblico dell'hub eventi. 

> [!IMPORTANT]
> Le reti virtuali sono supportate nei livelli **standard** e **dedicato** di Hub eventi. Non è supportato nel livello **di base.**

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Scenari di sicurezza avanzati resi possibili dall'integrazione della rete virtuale 

Le soluzioni che richiedono una sicurezza stretta e compartimentata e in cui le subnet di rete virtuale forniscono la segmentazione tra i servizi compartimentati, necessitano comunque di percorsi di comunicazione tra i servizi che risiedano in tali compartimenti.

Qualsiasi route IP immediata tra i compartimenti, incluse quelle destinate al traffico HTTPS su TCP/IP, comportano il rischio di sfruttamento delle vulnerabilità dal livello rete verso l'alto. I servizi di messaggistica forniscono percorsi di comunicazione isolati, in cui i messaggi vengono anche scritti su disco durante la transizione tra le parti. I carichi di lavoro in due reti virtuali distinte associate alla stessa istanza di Hub eventi possono comunicare in modo efficiente e affidabile tramite messaggi, pur mantenendo l'integrità del confine di isolamento rete.
 
Questo significa che le soluzioni cloud con requisiti di sicurezza elevati non solo possono ottenere l'accesso alle funzionalità di messaggistica asincrona scalabili e affidabili leader del settore di Azure, ma possono ora usare la messaggistica per creare percorsi di comunicazione tra compartimenti sicuri della soluzione, intrinsecamente più sicuri di quanto sia possibile con qualsiasi modalità di comunicazione peer-to-peer, inclusi i protocolli HTTPS e altri protocolli socket protetti da TLS.

### <a name="bind-event-hubs-to-virtual-networks"></a>Associare hub eventi a reti virtualiBind event hubs to virtual networks

Le **regole di rete virtuale** rappresentano la funzionalità di sicurezza firewall che controlla se lo spazio dei nomi di Hub eventi di Azure accetta le connessioni da una specifica subnet della rete virtuale.

L'associazione di uno spazio dei nomi di Hub eventi a una rete virtuale è un processo in due passaggi. È innanzitutto necessario creare un endpoint del **servizio di rete virtuale** nella subnet di una rete virtuale e abilitarlo per **Microsoft.EventHub** come illustrato nell'articolo [Panoramica dell'endpoint del servizio.](../virtual-network/virtual-network-service-endpoints-overview.md) Dopo aver aggiunto l'endpoint del servizio, è necessario associare lo spazio dei nomi di Hub eventi all'endpoint con una **regola di rete virtuale**.

La regola di rete virtuale è un'associazione dello spazio dei nomi di Hub eventi e una subnet della rete virtuale. Fino a quando esiste la regola, a tutti i carichi di lavoro associati alla subnet viene concesso l'accesso allo spazio dei nomi di Hub eventi. Gli hub eventi non stabiliscono mai connessioni in uscita, non devono ottenere l'accesso e pertanto non viene mai concesso l'accesso alla subnet abilitando questa regola.

Per altre informazioni, vedere Come configurare gli endpoint del servizio di rete virtuale per un hub eventiFor more information, see [How to configure virtual network service endpoints for an event hub](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Endpoint privati

Il [servizio Azure Private Link](../private-link/private-link-overview.md) consente di accedere ai servizi di Azure (ad esempio, Hub eventi di Azure, Archiviazione di Azure e database Cosmos di Azure) e ai servizi cliente/partner ospitati di Azure su un endpoint **privato** nella rete virtuale.

A private endpoint is a network interface that connects you privately and securely to a service powered by Azure Private Link. L'endpoint privato usa un indirizzo IP privato della rete virtuale, introducendo efficacemente il servizio nella rete virtuale. Tutto il traffico verso il servizio può essere instradato tramite l'endpoint privato, quindi non sono necessari gateway, dispositivi NAT, ExpressRoute o connessioni VPN oppure indirizzi IP pubblici. Il traffico tra la rete virtuale e il servizio attraversa la rete backbone Microsoft, impedendone l'esposizione alla rete Internet pubblica. È possibile connettersi a un'istanza di una risorsa di Azure, garantendo il massimo livello di granularità nel controllo di accesso.

> [!NOTE]
> Questa funzionalità è supportata solo con il livello **dedicato.** Per ulteriori informazioni sul livello dedicato, vedere [Panoramica degli hub eventi dedicati](event-hubs-dedicated-overview.md). 
>
> Questa funzione è attualmente in **anteprima**. 


Per altre informazioni, vedere [Come configurare gli endpoint privati per un hub eventiFor](private-link-service.md) more information, see How to configure private endpoints for an event hub


## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Come configurare il firewall IP per un hub eventi](event-hubs-ip-filtering.md)
- [Come configurare gli endpoint del servizio di rete virtuale per un hub eventiHow to configure virtual network service endpoints for an event hub](event-hubs-service-endpoints.md)
- [Come configurare gli endpoint privati per un hub eventiHow to configure private endpoints for an event hub](private-link-service.md)