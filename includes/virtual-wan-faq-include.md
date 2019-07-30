---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c46c11ead645b93d7710d1e11636037e4dcaf8e7
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444552"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Qual è la differenza tra un gateway di rete virtuale di Azure (Gateway VPN) e un gateway di rete virtualedella WAN virtuale di Azure?

La rete WAN virtuale offre connettività da sito a sito su larga scala ed è ideale per velocità effettiva, scalabilità e semplicità d'uso. La funzionalità di connettività ExpressRoute per la rete WAN virtuale è attualmente disponibile in anteprima. I dispositivi del ramo CPE effettuano il provisioning automatico e si connettono alla rete WAN virtuale di Azure. Questi dispositivi sono disponibili da un ecosistema di partner VPN e SD-WAN in continua crescita. Vedere l'[elenco dei partner preferiti](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Che cosa si intende per connessione da dispositivo di ramo alla rete WAN virtuale di Azure?

Una connessione da dispositivo di ramo alla rete WAN virtuale di Azure, costituita da due tunnel IPSec attivo/attivo.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Quali provider di dispositivo (partner di WAN virtuale) sono supportati in fase di lancio?

Al momento, molti partner supportano l'esperienza di rete WAN virtuale completamente automatizzata. Per altre informazioni, vedere [Virtual WAN partners](https://go.microsoft.com/fwlink/p/?linkid=2019615)(Partner di WAN virtuali). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quali sono i passaggi di Virtual WAN partner automation (Automazione dei partner della rete WAN virtuale)?

Per i passaggi di automazione dei partner, vedere [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automazione dei partner della rete WAN virtuale).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>È necessario usare il dispositivo di un partner preferito?

No. È possibile usare qualsiasi dispositivo che supporti la VPN e soddisfi i requisiti di Azure per il supporto di IKEv2/IKEv1 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>In che modo i partner di WAN virtuale automatizzano la connettività con la WAN virtuale di Azure?

Soluzioni di connettività definite dal software gestiscono in genere i propri dispositivi di ramo usando un controller o un centro di provisioning dei dispositivi. Il controller può usare le API di Azure per automatizzare la connettività alla WAN virtuale di Azure. Per altre informazioni, vedere Virtual WAN partner automation (Automazione dei partner della rete WAN virtuale).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>La WAN virtuale cambia qualche funzionalità di connettività esistente?   

Non sono state apportate modifiche alle funzionalità di connettività di Azure esistenti.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Sono disponibili nuove risorse di Gestione risorse per la WAN virtuale?
  
Sì, la WAN virtuale introduce nuove risorse di Gestione risorse. Per altre informazioni, vedere la [panoramica](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quanti dispositivi VPN possono connettersi a un singolo hub?

Sono supportate fino a 1.000 connessioni per ogni hub virtuale. Ogni connessione è costituita da due tunnel in una configurazione attivo-attivo. I tunnel terminano in un gateway VPN di hub virtuale di Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Il dispositivo VPN locale può connettersi a più hub?

Sì. Il flusso del traffico quando inizia proviene dal dispositivo locale verso la rete perimetrale Microsoft più vicina e quindi all'hub virtuale.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>È supportato il peering di reti virtuali globale con la WAN virtuale di Azure? 

 No.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Le reti virtuali spoke connesse a un hub virtuale possono comunicare tra loro?

Sì. Le reti virtuali spoke possono comunicare direttamente tramite il peering di rete virtuale. Tuttavia, non sono supportate le reti virtuali che comunicano in modo transitorio tramite l'hub. Per altre informazioni, vedere [Peering di rete virtuale](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>È possibile distribuire e usare l'appliance virtuale di rete preferita (in una rete virtuale di appliance virtuali di rete) con la WAN virtuale di Azure?

Sì, è possibile connettere la rete virtuale dell'appliance virtuale di rete preferita (NVA) alla rete WAN virtuale di Azure. Per prima cosa, connettere la rete virtuale dell'appliance virtuale di rete all'hub con una connessione hub di rete virtuale. Quindi, creare una route di hub virtuale con un hop successivo che punti all'appliance virtuale. È possibile applicare più route alla tabella di route dell'hub virtuale. Tutti gli spoke connessi alla rete virtuale dell'appliance virtuale di rete devono inoltre essere connessi all'hub virtuale per assicurare che le route di rete virtuale spoke vengano propagate ai sistemi locali.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Una rete virtuale di appliance virtuali di rete può avere un gateway di rete virtuale?

No. La rete virtuale di appliance virtuali di rete non può avere un gateway di rete virtuale se è connessa all'hub virtuale. 

### <a name="is-there-support-for-bgp"></a>È disponibile il supporto per BGP?

Sì, BGP è supportato. Quando si crea un sito VPN, è possibile inserirvi i parametri BGP. Ciò implica che qualsiasi connessione creata in Azure per tale sito sarà abilitata per BGP. Inoltre, se è già disponibile una rete virtuale con appliance virtuale di rete, collegata a un hub di rete WAN virtuale, per assicurarsi che le route provenienti da tale rete vengano annunciate correttamente, gli spoke ad essa collegati devono disabilitare BGP. Inoltre, connettere queste reti virtuali spoke alla rete virtuale hub per assicurarsi che le route della rete virtuale spoke vengano propagate ai sistemi locali.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>È possibile indirizzare il traffico usando route definite dall'utente nell'hub virtuale?

Sì, è possibile indirizzare il traffico verso una rete virtuale usando una tabella di route dell'hub virtuale.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Sono disponibili informazioni sulle licenze o i prezzi per la WAN virtuale?
 
Sì. Vedere la pagina dei [Prezzi](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Come si calcola il prezzo di un hub?
 
È previsto il pagamento per il servizio nell'hub. Ad esempio, 10 rami o dispositivi locali che richiedono la connessione alla rete WAN virtuale di Azure implicano la connessione agli endpoint VPN nell'hub. Se ad esempio si tratta di una rete VPN di 1 unità di scala = 500 Mbps, viene addebitato il costo di $ 0,361/h. Per ogni connessione viene addebitato il costo di $ 0,08/h. Per 10 connessioni, il costo totale del servizio/h sarebbe $ 0,361 + $ 0,8/h. Per il traffico in uscita da Azure si applicano i costi dei dati. 

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>In che modo vengono accettati i nuovi partner non inclusi nell'elenco dei partner iniziale?

Inviare un messaggio di posta elettronica all'indirizzo azurevirtualwan@microsoft.com. Un partner ideale è quello che ha un dispositivo di cui è possibile eseguire il provisioning per la connettività IPSec IKEv1 o IKEv2.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Cosa succede se un dispositivo in uso non è incluso nell'elenco di partner della rete WAN virtuale? È comunque possibile usarlo per connettersi alla VPN della rete WAN virtuale di Azure?

Sì, purché il dispositivo supporti IPsec IKEv1 o IKEv2. I partner della rete WAN virtuale automatizzano la connettività dal dispositivo agli endpoint VPN. Ciò implica l'automazione di passaggi come 'caricamento di informazioni sui rami', 'IPsec e configurazione' e 'connettività'. Poiché il dispositivo in uso non proviene da un ecosistema di partner della rete WAN virtuale, sarà necessario assumersi il carico di effettuare manualmente la configurazione di Azure e aggiornare il dispositivo per configurare la connettività IPsec. 

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>È possibile costruire la rete WAN virtuale di Azure con un modello di Resource Manager?

Una semplice configurazione di una rete WAN virtuale con un hub e un sito VPN può essere creata usando un [Modello di avvio rapido in Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). La rete WAN virtuale è principalmente un REST o un servizio gestito dal Portale.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>La connettività tra succursali è consentita nella rete WAN virtuale?

Sì, la connettività tra succursali è disponibile nella rete WAN virtuale per la VPN e da VPN a ExpressRoute. Mentre la VPN da sito a sito è disponibile a livello generale, ExpressRoute è attualmente disponibile in anteprima.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Il traffico da succursale a succursale passa attraverso la rete WAN virtuale di Azure?

Sì.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>In che modo la rete WAN virtuale è diversa dal Gateway di rete virtuale di Azure esistente?

La rete VPN di Gateway di rete virtuale è limitata a 30 tunnel. Per le connessioni, è consigliabile usare la rete WAN virtuale per VPN su larga scala. È possibile collegare fino a 1.000 connessioni di succursali con 20 Gbps nell'hub per tutte le aree. Una connessione è un tunnel attivo-attivo dal dispositivo VPN locale all'hub virtuale. È possibile avere un unico hub per area, ovvero è possibile connettere più di 1.000 rami tra gli hub.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>In che modo la rete WAN virtuale supporta i dispositivi SD-WAN?

I partner della rete WAN virtuale automatizzano la connettività IPsec agli endpoint VPN di Azure. Se il partner della rete WAN virtuale è un provider di SD-WAN, il controller SD-WAN gestisce l'automazione e la connettività IPsec agli endpoint VPN di Azure. Se il dispositivo SD-WAN richiede un proprio endpoint invece di quelli VPN di Azure per una funzionalità SD-WAN proprietaria, è possibile distribuire l'endpoint SD-WAN in una rete virtuale di Azure in coesistenza con la rete WAN virtuale di Azure.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Questa rete WAN virtuale richiede ExpressRoute da ogni sito?

No, la rete WAN virtuale non richiede ExpressRoute da ogni sito. Usa la connettività da sito a sito IPSec standard tramite collegamenti Internet dal dispositivo a un hub della rete WAN virtuale di Azure. I siti possono essere connessi a una rete del provider usando un circuito ExpressRoute. Per i siti connessi tramite ExpressRoute nell'hub virtuale (in anteprima), questi possono avere un flusso di traffico tra succursali tra la VPN ed ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>È previsto un limite di velocità effettiva di rete quando si usa la rete WAN virtuale di Azure?

Il numero di succursali è limitato a 1000 connessioni per hub/area e a un totale di 20 Gbps nell'hub.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Quante connessioni VPN può supportare un hub della rete WAN virtuale?

Un hub di Azure rete WAN virtuale può supportare contemporaneamente fino a 1.000 connessioni da sito a sito e 10.000 connessioni da punto a sito.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Qual è la velocità effettiva totale della VPN di un tunnel VPN e di una connessione?

La velocità effettiva totale della VPN di un hub non supera 20 Gbps in base all'unità di scala scelta. La velocità effettiva è condivisa da tutte le connessioni esistenti.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>La rete WAN virtuale consente al dispositivo locale di usare più ISP in parallelo o si tratta sempre di un singolo tunnel VPN?

Una connessione in ingresso alla VPN della rete WAN virtuale è sempre un tunnel attivo-attivo (per la resilienza nello stesso hub/area) che usa un collegamento disponibile nel ramo. Questo collegamento potrebbe essere un collegamento di ISP nel ramo locale. La rete WAN virtuale non prevede una logica speciale per configurare più ISP in parallelo. La gestione del failover tra ISP nel ramo è interamente un'operazione di rete incentrata sul ramo. È possibile usare la propria soluzione SD-WAN preferita per effettuare la selezione del percorso nel ramo.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>In quale modo il traffico viene indirizzato sul backbone di Azure?

Il traffico segue il modello: dispositivo del ramo -> ISP -> rete perimetrale Microsoft - > controller di dominio Microsoft (rete virtuale hub) -> rete perimetrale Microsoft -> ISP -> dispositivo del ramo

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>In questo modello, cosa serve in ogni sito? Semplicemente una connessione Internet?

Sì. Una connessione a Internet e un dispositivo fisico che supporti IPsec, preferibilmente dei [partner](https://go.microsoft.com/fwlink/p/?linkid=2019615) integrati Microsoft. Facoltativamente è possibile gestire manualmente la configurazione e la connettività ad Azure dal dispositivo preferito.
