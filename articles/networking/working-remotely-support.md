---
title: Utilizzo remoto dei servizi di rete di AzureWorking remotely using Azure networking services
description: In questa pagina viene descritto come usare i servizi di rete di Azure disponibili per consentire il funzionamento in remoto e come ridurre i problemi di traffico derivanti dall'aumento del numero di persone che lavorano in remoto.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: 78081329b70edff5ed2c728cb2f7c3e562a44e56
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346279"
---
# <a name="working-remotely-using-azure-networking-services"></a>Utilizzo remoto dei servizi di rete di AzureWorking remotely using Azure networking services

>[!NOTE]
> Questo articolo descrive come sfruttare i servizi di rete di Azure, la rete Microsoft e l'ecosistema dei partner di Azure per lavorare in remoto e ridurre i problemi di rete che potrebbero verificarsi a causa della crisi DI COVID-19.

In questo articolo vengono descritte le opzioni disponibili per le organizzazioni per impostare l'accesso remoto per gli utenti o per integrare le soluzioni esistenti con capacità aggiuntiva durante i periodi di picco di utilizzo. Gli architetti di rete si trovano ad affrontare le seguenti sfide:

- Affrontare un aumento dell'utilizzo della rete.
- Fornire connettività affidabile e sicura a un maggior numero di dipendenti dell'azienda e dei clienti.
- Fornisci connettività a sedi remote in tutto il mondo.

Non tutte le reti (ad esempio, WAN privata e reti core aziendali) subiscono una congestione del carico di lavoro remoto di picco. I colli di bottiglia sono comunemente segnalati solo nelle reti a banda larga domestica e nei gateway VPN delle reti locali delle aziende.

I pianificatori di rete possono contribuire ad alleviare i colli di bottiglia e ad alleviare la congestione della rete tenendo presente che diversi tipi di traffico richiedono priorità di trattamento di rete diverse e mediante un reindirizzamento/distribuzione intelligente del carico. Ad esempio, il traffico tele-medecine in tempo reale dell'interazione medico-paziente è di grande importanza e sensibile al ritardo/jitter. Mentre, la replica dello stesso traffico tra gli archivi non è sensibile al ritardo. Il primo traffico deve essere instradato attraverso il percorso di rete ottimale con una maggiore qualità del servizio; mentre è accettabile instradare il traffico successivo attraverso un percorso non ottimale.

>[!NOTE] 
>Verso la fine di questo articolo, vengono elencati i collegamenti agli articoli che sfruttano diverse funzionalità e ecosistemi di rete di Azure per supportare gli utenti che lavorano in remoto.


## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Condivisione delle procedure consigliate: la rete di Azure è progettata per elasticità e disponibilità elevata

Azure è progettato per resistere a cambiamenti improvvisi nell'utilizzo delle risorse e può essere di grande aiuto durante i periodi di picco di utilizzo. Inoltre, Microsoft gestisce e gestisce una delle reti più grandi del mondo. La rete Microsoft è stata progettata per la disponibilità elevata in grado di sopportare diversi tipi di errore: da un singolo elemento di rete non riuscito a un errore di un'intera area.

La rete Microsoft è progettata per soddisfare i requisiti e fornire prestazioni ottimali per diversi tipi di traffico di rete, tra cui traffico multimediale sensibile al ritardo per Skype e Teams, CDN, analisi dei Big Data in tempo reale, archiviazione di Azure, Bing e Xbox. Per garantire prestazioni ottimali per diversi tipi di traffico, la rete Microsoft attira tutto il traffico a cui è destinato o che desidera transitare attraverso le sue risorse il più vicino possibile all'origine del traffico.

>[!NOTE] 
>L'uso delle funzionalità di rete di Azure descritte di seguito sfrutta il comportamento di attrazione sul traffico della rete globale Microsoft per offrire un'esperienza di rete migliore per i clienti. Il comportamento di attrazione per il traffico della rete Microsoft consente di disattivare il traffico il più presto possibile dalle reti del primo/ultimo miglio che potrebbero verificarsi congestione durante i periodi di picco di utilizzo.
>

## <a name="enable-employees-to-work-remotely"></a>Consentire ai dipendenti di lavorare in remoto

Il gateway VPN di Azure supporta connessioni VPN da punto a sito (P2S) e S2S (Site-to-Site). Usando il gateway VPN di Azure è possibile scalare le connessioni dei dipendenti per accedere in modo sicuro sia alle risorse distribuite di Azure che alle risorse locali. Per ulteriori informazioni, vedere [Come consentire agli utenti di lavorare in remoto.](https://go.microsoft.com/fwlink/?linkid=2123770) 

Se si utilizza il protocollo SSTP (Secure Sockets Tunneling Protocol), il numero di connessioni simultanee è limitato a 128. Per ottenere un numero maggiore di connessioni, si consiglia la transizione a OpenVPN o IKEv2. Per ulteriori informazioni, vedere [Transizione al protocollo OpenVPN o IKEv2 da SSTP](https://go.microsoft.com/fwlink/?linkid=2124112).

Per accedere alle risorse distribuite in Azure, gli sviluppatori remoti potrebbero usare la soluzione Azure Bastion, anziché la connessione VPN per ottenere l'accesso sicuro alla shell (RDP o SSH) senza richiedere indirizzi IP pubblici nelle macchine virtuali a cui si accede. Per altre informazioni, vedere [Usare Azure Bastion in remoto.](https://go.microsoft.com/fwlink/?linkid=2123939)

Per l'aggregazione di connessioni VPN su larga scala, per supportare connessioni any-to-any tra risorse in diverse sedi globali locali, in reti virtuali hub e spoke diverse e per ottimizzare l'utilizzo di più reti a banda larga domestica è possibile usare la rete WAN virtuale di Azure.You can use Azure Virtual WAN. Per ulteriori informazioni, vedere [Lotta per soddisfare il lavoro dalle esigenze domestiche? Ecco dove la rete WAN virtuale di Azure può essere d'aiuto.](https://go.microsoft.com/fwlink/?linkid=2123769)

Un altro modo per supportare una forza lavoro remota consiste nel distribuire un'infrastruttura VDI (Virtual Desktop Infrastructure) ospitata nella rete virtuale di Azure, protetta con un firewall di Azure.Another way to support a remote workforce is to deploy a Virtual Desktop Infrastructure (VDI) hosted in your Azure virtual network, secured with an Azure Firewall. Ad esempio, Windows Virtual Desktop (WVD) è un servizio di virtualizzazione di app e desktop che viene eseguito in Azure.For example, Windows Virtual Desktop (WVD) is a desktop and app virtualization service that runs in Azure. Con Windows Virtual Desktop, è possibile configurare un ambiente scalabile e flessibile nella sottoscrizione di Azure senza la necessità di eseguire server gateway aggiuntivi. L'utente è responsabile solo delle macchine virtuali WVD nella rete virtuale. Per altre informazioni, vedere Supporto del [lavoro remoto di Firewall di Azure.For](../firewall/remote-work-support.md)more information, see Azure Firewall remote work support . 

Azure include anche un set completo di partner di sistema ecologici. I nostri partner Network Virtual Appliances in Azure possono anche aiutare a scalare la connettività VPN. Per ulteriori informazioni, vedere [Considerazioni sull'appliance virtuale di rete (NVA) per](https://go.microsoft.com/fwlink/?linkid=2123771)il lavoro remoto.

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>Estendere la connessione dei dipendenti per accedere alle risorse distribuite a livello globale

I servizi di Azure seguenti consentono ai dipendenti di accedere alle risorse distribuite a livello globale. Le risorse potrebbero trovarsi in qualsiasi area di Azure, in reti locali o anche in altri cloud pubblici o privati. 

- **Peering**della rete virtuale di Azure: se si distribuiscono le risorse in più aree di Azure e/o se si aggrega la connettività dei dipendenti che lavorano in remoto usando più reti virtuali, è possibile stabilire la connettività tra più reti virtuali di Azure usando il peering della rete virtuale. Per altre informazioni, vedere [Peering di rete virtuale][VNet-peer].

- **Soluzione basata su VPN**di Azure: per i dipendenti remoti connessi ad Azure tramite P2S o VPN S2S, è possibile abilitare l'accesso alle reti locali configurando la VPN S2S tra le reti locali e il gateway VPN di Azure.Azure VPN-based solution : For your remote employees connected to Azure via P2S or S2S VPN, you can enable access to on-premises networks by configuring S2S VPN between your on-premises networks and Azure VPN gateway. Per ulteriori informazioni, vedere [Creare una connessione da sito a sito][S2S].

- **ExpressRoute:** tramite il peering privato ExpressRoute è possibile abilitare la connettività privata tra le distribuzioni di Azure e l'infrastruttura locale o l'infrastruttura in una struttura di co-location. ExpressRoute, tramite peering Microsoft, consente anche l'accesso agli endpoint pubblici in Microsoft dalla rete locale. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. Offrono connettività sicura, affidabilità, maggiore produttività, con latenze inferiori e coerenti rispetto alle connessioni tipiche su Internet. Per altre informazioni, vedere [Panoramica di ExpressRoute][ExR]. L'utilizzo del provider di rete esistente che fa già parte dell'ecosistema dei partner ExpressRoute può contribuire a ridurre il tempo necessario per ottenere connessioni con larghezza di banda di grandi dimensioni a Microsoft.Leveraging your existing network provider that is already part of our [ExpressRoute partner ecosystem][ExR-eco] can help reduce the time to get large bandwidth connections to Microsoft.  Usando ExpressRoute Direct è possibile connettere direttamente la rete locale alla backbone Microsoft.Using [ExpressRoute Direct][ExR-D] you can directly connect your on-premises network to the Microsoft backbone. ExpressRoute Direct offre due diverse opzioni di velocità di linea di doppio 10 Gbps o 100 Gbps. 

- **WAN virtuale di Azure:** la rete WAN virtuale di Azure consente un'interoperabilità senza soluzione di continuità tra le connessioni VPN e i circuiti ExpressRoute.Azure Virtual WAN : Azure Virtual WAN allows seamless interoperability between your VPN connections and ExpressRoute circuits. Come accennato in precedenza, la rete WAN virtuale di Azure supporta anche connessioni any-to-any tra le risorse in diverse posizioni globali punsituose, in reti virtuali diverse e hub aree orarie

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Scalare la connettività dei clienti alle risorse front-end

Durante i periodi in cui più persone vanno online, molti siti web aziendali sperimentano un aumento del traffico dei clienti. Il gateway applicazione di Azure consente di gestire questo carico di lavoro front-end aumentato. Per ulteriori informazioni, vedere [Supporto del traffico elevato del gateway applicazione](https://go.microsoft.com/fwlink/?linkid=2123940).

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Supporto Microsoft per il traffico multi-cloud

Per le distribuzioni in altri cloud pubblici, Microsoft può fornire connettività globale. La rete WAN virtuale di Azure, VPN o ExpressRoute può essere di aiuto in questo senso. Per estendere la connettività da Azure ad altri cloud, è possibile configurare la VPN S2S tra i due cloud. È anche possibile stabilire la connettività da Azure ad altri cloud pubblici usando ExpressRoute.You can also establish connectivity from Azure to other public clouds using ExpressRoute. Il cloud Oracle fa parte dell'ecosistema dei partner ExpressRoute.Oracle cloud is part of ExpressRoute partner ecosystem. È possibile [configurare un'interconnessione diretta tra Azure e Oracle Cloud Infrastructure.][Az-OCI] La maggior parte dei provider di servizi che fanno parte dell'ecosistema dei partner ExpressRoute offrono anche la connettività privata ad altri cloud pubblici. Sfruttando questi provider di servizi, è possibile stabilire la connettività privata tra le distribuzioni in Azure e altri cloud tramite ExpressRoute.Leveraging these service providers, you can establish private connectivity between your deployments in Azure and other clouds via ExpressRoute.

## <a name="next-steps"></a>Passaggi successivi

Gli articoli seguenti illustrano come diverse funzionalità di rete di Azure possono essere usate per ridimensionare gli utenti per lavorare in remoto:The following articles discuss how different Azure networking features can be used to scale users to work remotely:

| **Articolo** | **Ultimo aggiornamento** |
| --- | --- |
| [Come consentire agli utenti di lavorare in remoto](https://go.microsoft.com/fwlink/?linkid=2123770) | del 23 marzo 2020 |
| [Lottando per soddisfare il lavoro da esigenze domestiche? Ecco dove può essere utile la rete WAN virtuale di AzureHere are where Azure Virtual WAN can help](https://go.microsoft.com/fwlink/?linkid=2123769) | del 23 marzo 2020 |
| [Supporto per traffico elevato del gateway applicazioneApplication Gateway high traffic support](https://go.microsoft.com/fwlink/?linkid=2123940) | del 23 marzo 2020 |
| [Considerazioni sull'appliance virtuale di rete per il lavoro remoto](https://go.microsoft.com/fwlink/?linkid=2123771)| del 23 marzo 2020 |
| [Transizione al protocollo OpenVPN o IKEv2 da SSTP](https://go.microsoft.com/fwlink/?linkid=2124112) | del 23 marzo 2020 |
| [Lavorare in remoto con Azure BastionWorking remotely using Azure Bastion](https://go.microsoft.com/fwlink/?linkid=2123939) | del 23 marzo 2020 |
| [Uso di Azure ExpressRoute per creare connettività ibrida per supportare gli utenti remotiUsing Azure ExpressRoute to create hybrid connectivity to support remote users](https://go.microsoft.com/fwlink/?linkid=2123768) | del 23 marzo 2020 |
| [Supporto del lavoro remoto di Firewall di AzureAzure Firewall remote work support](../firewall/remote-work-support.md)|del 25 marzo 2020|

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
