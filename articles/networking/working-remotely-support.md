---
title: Lavorare in remoto usando i servizi di rete di Azure
description: Questa pagina descrive come usare i servizi di rete di Azure disponibili per consentire il funzionamento in modalità remota e come mitigare i problemi di traffico derivanti dall'aumento del numero di persone che lavorano in remoto.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982855"
---
# <a name="working-remotely-using-azure-networking-services"></a>Lavorare in remoto usando i servizi di rete di Azure

>[!NOTE]
> Questo articolo descrive come sfruttare i servizi di rete di Azure, la rete Microsoft e l'ecosistema di partner Azure per lavorare in modalità remota e mitigare i problemi di rete che potrebbero essere rilevati a causa della crisi COVID-19.

Questo articolo descrive le opzioni disponibili per le organizzazioni per configurare l'accesso remoto per gli utenti o per integrare le soluzioni esistenti con capacità aggiuntiva durante i periodi di picco dell'utilizzo. Gli architetti di rete affrontano i problemi seguenti:

- Risolvere un aumento dell'utilizzo della rete.
- Fornire connettività affidabile e sicura a più dipendenti della società e dei clienti.
- Fornire connettività a posizioni remote in tutto il mondo.

Non tutte le reti, ad esempio la rete WAN privata e le reti aziendali core, comportano la congestione dal carico di lavoro remoto di picco. I colli di bottiglia sono comunemente segnalati solo nelle reti a banda larga e nei gateway VPN delle reti locali delle aziende.

I pianificatori di rete possono semplificare i colli di bottiglia e alleviare la congestione della rete tenendo presente che i diversi tipi di traffico necessitano di diverse priorità di trattamento della rete e da un reindirizzamento o una distribuzione intelligente del carico. Ad esempio, il traffico tele-medicina in tempo reale dell'interazione medico-paziente è molto importante e sensibile a ritardo/jitter. Mentre la replica dello stesso traffico tra le archiviazioni non è sensibile al ritardo. Il traffico precedente deve essere instradato tramite il percorso di rete più ottimale con una qualità del servizio superiore; mentre è accettabile indirizzare il traffico in un secondo momento tramite una route non ottimale.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Condivisione delle procedure consigliate: la rete di Azure è progettata per l'elasticità e la disponibilità elevata

Azure è progettato per resistere a modifiche improvvise nell'utilizzo delle risorse e può essere molto utile durante i periodi di picco dell'utilizzo. Microsoft gestisce e gestisce anche una delle reti più grandi del mondo. La rete Microsoft è stata progettata per garantire un'elevata disponibilità in grado di supportare diversi tipi di errore: da un singolo elemento di rete in errore in caso di errore di un'intera area.

La rete Microsoft è progettata per soddisfare i requisiti e garantire prestazioni ottimali per diversi tipi di traffico di rete, incluso il traffico multimediale sensibile al ritardo per Skype e i team, la rete CDN, l'analisi Big Data in tempo reale, archiviazione di Azure, Bing e Xbox. Per garantire prestazioni ottimali per diversi tipi di traffico, la rete Microsoft attrae tutto il traffico destinato a o che desidera transitare attraverso le relative risorse il più vicino possibile all'origine del traffico.

>[!NOTE] 
>L'uso delle funzionalità di rete di Azure descritte di seguito sfrutta il comportamento di attrazione del traffico della rete globale Microsoft per offrire una migliore esperienza di rete dei clienti. Il comportamento di attrazione del traffico della rete Microsoft contribuisce a non caricare il traffico il prima possibile dalle reti prime/ultime Mile che potrebbero riscontrare congestione durante i periodi di picco dell'utilizzo.
>

## <a name="enable-employees-to-work-remotely"></a>Consentire ai dipendenti di lavorare in modalità remota

Il gateway VPN di Azure supporta connessioni VPN da punto a sito (P2S) e da sito a sito (S2S). Usando il gateway VPN di Azure è possibile ridimensionare le connessioni del dipendente per accedere in modo sicuro alle risorse distribuite di Azure e alle risorse locali. Per ulteriori informazioni, vedere [come consentire agli utenti di lavorare in modalità remota](../vpn-gateway/work-remotely-support.md). 

Se si utilizza il protocollo SSTP (Secure Sockets Tunneling Protocol), il numero di connessioni simultanee è limitato a 128. Per ottenere un numero maggiore di connessioni, è consigliabile passare a OpenVPN o a IKEv2. Per ulteriori informazioni, vedere la pagina relativa [alla transizione al protocollo OpenVPN o IKEv2 da SSTP](../vpn-gateway/ikev2-openvpn-from-sstp.md
).

Per accedere alle risorse distribuite in Azure, gli sviluppatori remoti possono usare la soluzione Azure Bastion, invece della connessione VPN per ottenere l'accesso Secure Shell (RDP o SSH) senza richiedere indirizzi IP pubblici nelle VM a cui si accede. Per altre informazioni, vedere [lavorare in remoto con Azure Bastion](../bastion/work-remotely-support.md).

Per l'aggregazione della connessione VPN su larga scala, per supportare le connessioni any-to-any tra le risorse in posizioni globali locali diverse, in reti virtuali diverse e in reti virtuali spoke e per ottimizzare l'utilizzo di più reti a banda larga domestica è possibile usare la rete WAN virtuale di Azure. Per altre informazioni, vedere [difficoltà a soddisfare le esigenze del lavoro da casa? Ecco dove è possibile aiutare la rete WAN virtuale di Azure](../virtual-wan/work-remotely-support.md).

Un altro modo per supportare una forza lavoro remota consiste nel distribuire un'infrastruttura VDI (Virtual Desktop Infrastructure) ospitata nella rete virtuale di Azure, protetta con un firewall di Azure. Ad esempio, desktop virtuale Windows (WVD) è un servizio di virtualizzazione di applicazioni e desktop eseguito in Azure. Con desktop virtuale di Windows, è possibile configurare un ambiente scalabile e flessibile nella sottoscrizione di Azure senza la necessità di eseguire altri server gateway. L'utente è responsabile solo delle macchine virtuali WVD nella rete virtuale. Per altre informazioni, vedere [supporto per il lavoro remoto del firewall di Azure](../firewall/remote-work-support.md). 

Azure dispone anche di un ampio set di partner di sistema eco. I nostri partner di rete appliance virtuali in Azure consentono anche di ridimensionare la connettività VPN. Per altre informazioni, vedere [considerazioni sull'appliance virtuale di rete per il lavoro remoto](../vpn-gateway/nva-work-remotely-support.md).

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Estendere la connessione dei dipendenti per accedere alle risorse distribuite a livello globale

I servizi di Azure seguenti consentono ai dipendenti di accedere alle risorse distribuite a livello globale. Le risorse possono trovarsi in qualsiasi area di Azure, in reti locali o anche in altri cloud pubblici o privati. 

- **Peering di rete virtuale di Azure**: se le risorse vengono distribuite in più aree di Azure e/o se si aggrega la connettività dei dipendenti che lavorano in remoto usando più reti virtuali, è possibile stabilire la connettività tra più reti virtuali di Azure usando il peering di rete virtuale. Per altre informazioni, vedere [Peering di rete virtuale][VNet-peer].

- **Soluzione basata su VPN di Azure**: per i dipendenti remoti connessi ad Azure tramite la VPN P2S o S2S, è possibile abilitare l'accesso alle reti locali configurando la VPN S2S tra le reti locali e il gateway VPN di Azure. Per ulteriori informazioni, vedere [la pagina relativa alla creazione di una connessione da sito a sito][S2S].

- **ExpressRoute**: uso del peering privato di ExpressRoute è possibile abilitare la connettività privata tra le distribuzioni di Azure e l'infrastruttura locale o l'infrastruttura in una struttura di condivisione percorso. ExpressRoute, tramite il peering Microsoft, consente anche l'accesso agli endpoint pubblici in Microsoft dalla rete locale. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. Offrono connettività sicura, affidabilità, velocità effettiva superiore, con latenze inferiori e coerenti rispetto alle connessioni Internet tipiche. Per altre informazioni, vedere [Panoramica di ExpressRoute][ExR]. Sfruttando il provider di rete esistente che fa già parte dell' [ecosistema di partner ExpressRoute][ExR-eco] , è possibile ridurre il tempo necessario per ottenere connessioni con larghezza di banda elevata a Microsoft.  Con [ExpressRoute Direct][ExR-D] è possibile connettere direttamente la rete locale al backbone Microsoft. ExpressRoute Direct offre due diverse opzioni di frequenza di riga di doppio 10 Gbps o 100 Gbps. 

- **Rete WAN virtuale**di Azure: la rete WAN virtuale di Azure consente l'interoperabilità senza problemi tra le connessioni VPN e i circuiti ExpressRoute. Come accennato in precedenza, la rete WAN virtuale di Azure supporta anche le connessioni any-to-any tra le risorse in posizioni globali locali diverse, in diverse reti virtuali di hub e spoke

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Ridimensionare la connettività dei clienti alle risorse front-end

Durante i periodi in cui più persone passano online, molti siti Web aziendali riscontrano un aumento del traffico dei clienti. Applicazione Azure gateway può aiutare a gestire questo carico di lavoro front-end maggiore. Per altre informazioni, vedere [supporto del traffico elevato del gateway applicazione](../application-gateway/high-traffic-support.md).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Supporto Microsoft per il traffico su più cloud

Per le distribuzioni in altri cloud pubblici, Microsoft può offrire connettività globale. WAN virtuale di Azure, VPN o ExpressRoute possono essere utili per questo aspetto. Per estendere la connettività da Azure ad altri cloud, è possibile configurare la VPN S2S tra i due cloud. È anche possibile stabilire la connettività da Azure ad altri cloud pubblici usando ExpressRoute. Oracle Cloud fa parte dell'ecosistema di partner ExpressRoute. È possibile [configurare un'interconnessione diretta tra Azure e l'infrastruttura cloud Oracle][Az-OCI]. La maggior parte dei provider di servizi che fanno parte dell'ecosistema partner ExpressRoute offrono anche connettività privata ad altri cloud pubblici. Sfruttando questi provider di servizi, è possibile stabilire la connettività privata tra le distribuzioni in Azure e in altri cloud tramite ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti illustrano come usare le diverse funzionalità di rete di Azure per ridimensionare gli utenti per lavorare in modalità remota:

| **Articolo** | **Descrizione** |
| --- | --- |
| [Come consentire agli utenti di lavorare in modalità remota](../vpn-gateway/work-remotely-support.md) | Esaminare le opzioni disponibili per configurare l'accesso remoto per gli utenti o per integrare le soluzioni esistenti con capacità aggiuntiva per la propria organizzazione.|
| [Si fatica a soddisfare le esigenze del lavoro da casa? Ecco dove è possibile aiutare la rete WAN virtuale di Azure](../virtual-wan/work-remotely-support.md) | Usare la rete WAN virtuale di Azure per soddisfare le esigenze di connettività remota della propria organizzazione.|
| [Supporto del traffico elevato nel gateway applicazione](../application-gateway/high-traffic-support.md) | Usare il gateway applicazione con Web Application Firewall (WAF) per gestire il traffico verso le applicazioni Web in modo sicuro e scalabile. |
| [Considerazioni sull'appliance virtuale di rete per il lavoro remoto](../vpn-gateway/nva-work-remotely-support.md)|Vedere le indicazioni sull'uso di appliance virtuali in Azure per fornire soluzioni di accesso remoto. |
| [Transizione al protocollo OpenVPN o IKEv2 da SSTP](https://go.microsoft.com/fwlink/?linkid=2124112) | Superare il limite di 128 della connessione simultanea di SSTP eseguendo la transizione al protocollo OpenVPN o a IKEv2.|
| [Lavorare in remoto con Azure Bastion](../bastion/work-remotely-support.md) | Fornire connettività RDP/SSH sicura e trasparente alle macchine virtuali all'interno della rete virtuale di Azure, direttamente nel portale di Azure, senza l'uso di un indirizzo IP pubblico. |
| [Uso di Azure ExpressRoute per creare una connettività ibrida per supportare gli utenti remoti](../expressroute/work-remotely-support.md) | Usare ExpressRoute per la connettività ibrida per consentire agli utenti dell'organizzazione di lavorare in modalità remota.|
| [Supporto per lavoro remoto del firewall di Azure](../firewall/remote-work-support.md)|Proteggi le risorse della rete virtuale di Azure usando il firewall di Azure. |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
