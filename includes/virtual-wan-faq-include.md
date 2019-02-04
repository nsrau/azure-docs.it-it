---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 98ea4d78a473123708be6e371587252acad6ffcd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205132"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Qual è la differenza tra un gateway di rete virtuale di Azure (Gateway VPN) e un gateway di rete virtualedella WAN virtuale di Azure?

La rete WAN virtuale offre connettività da sito a sito su larga scala ed è ideale per velocità effettiva, scalabilità e semplicità d'uso. La funzionalità di connettività ExpressRoute e da punto a sito è attualmente in versione di anteprima. I dispositivi del ramo CPE effettuano il provisioning automatico e si connettono alla WAN virtuale di Azure. Questi dispositivi sono disponibili da un ecosistema di partner VPN e SD-WAN in continua crescita. Vedere l'[elenco dei partner preferiti](https://go.microsoft.com/fwlink/p/?linkid=2019615).

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

Sono supportate fino a 1000 connessioni per ogni hub virtuale. Ogni connessione è costituita da due tunnel in una configurazione attivo-attivo. I tunnel terminano in un gateway VPN di hub virtuale di Azure.

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

Sì, BGP è supportato. Per garantire che le route da una rete virtuale di appliance virtuali di rete siano annunciate in modo appropriato, gli spoke devono disabilitare il protocollo BGP se sono connessi a una rete virtuale di appliance virtuali di rete che, a sua volta, è connessa a un hub virtuale. Inoltre, connettere le reti virtuali spoke all'hub virtuale per assicurare che le route delle reti virtuali spoke vengano propagate ai sistemi locali.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>È possibile indirizzare il traffico usando route definite dall'utente nell'hub virtuale?

Sì, è possibile indirizzare il traffico verso una rete virtuale usando una tabella di route dell'hub virtuale.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Sono disponibili informazioni sulle licenze o i prezzi per la WAN virtuale?
 
Sì. Vedere la pagina dei [Prezzi](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>In che modo vengono accettati i nuovi partner non inclusi nell'elenco dei partner iniziale?

Inviare un messaggio di posta elettronica all'indirizzo azurevirtualwan@microsoft.com. Un partner ideale è quello che ha un dispositivo di cui è possibile eseguire il provisioning per la connettività IPSec IKEv1 o IKEv2.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>È possibile costruire la rete WAN virtuale di Azure con un modello di Resource Manager?

Una semplice configurazione di una rete WAN virtuale con un hub e un vpnsite può essere creata usando un [Modello di avvio rapido in Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). La rete WAN virtuale è principalmente un REST o un servizio gestito dal Portale.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>La connettività tra succursali è consentita nella rete WAN virtuale?

Sì, la connettività tra succursali è disponibile nella rete WAN virtuale per la VPN e da VPN a ExpressRoute. Mentre la VPN da sito a sito è in disponibilità generale, ExpressRoute e da punto a sito è attualmente in anteprima.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Il traffico da succursale a succursale passa attraverso la rete WAN virtuale di Azure?

Sì.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>In che modo la rete WAN virtuale è diversa dal Gateway di rete virtuale di Azure esistente?

La rete VPN di Gateway di rete virtuale è limitata a 30 tunnel. Per le connessioni, è consigliabile usare la rete WAN virtuale per VPN su larga scala. È possibile collegare fino a 1000 connessioni di succursali con 2 Gbps nell'hub per tutte le aree geografiche, tranne per gli Stati Uniti centro-occidentali. Per quest'area sono disponibili 20 Gbps. In futuro verranno rilasciati 20 Gbps per altre aree geografiche. Una connessione è un tunnel attivo-attivo dal dispositivo VPN locale all'hub virtuale. È possibile avere un unico hub per area, ovvero è possibile connettere più di 1000 succursali tra gli hub.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Questa rete WAN virtuale richiede ExpressRoute da ogni sito?

No, la rete WAN virtuale non richiede ExpressRoute da ogni sito. Usa la connettività da sito a sito IPSec standard tramite collegamenti Internet dal dispositivo a un hub della rete WAN virtuale di Azure. I siti possono essere connessi a una rete del provider usando un circuito ExpressRoute. Per i siti connessi tramite ExpressRoute nell'hub virtuale (in anteprima), questi possono avere un flusso di traffico tra succursali tra la VPN ed ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>È previsto un limite di velocità effettiva di rete quando si usa la rete WAN virtuale di Azure?

Il numero di succursali è limitato a 1000 connessioni per hub/area e a un totale di 2 Gbps nell'hub. Fanno eccezione gli Stati Uniti centro-occidentali, con un totale di 20 Gbps. In futuro verranno rilasciati 20 Gbps per altre aree geografiche.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>La rete WAN virtuale consente al dispositivo locale di usare più ISP in parallelo o si tratta sempre di un singolo tunnel VPN?

Sì, è possibile avere tunnel attivo-attivo (2 tunnel = 1 connessione WAN virtuale di Azure) da una singola succursale, a seconda del dispositivo della succursale.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>In quale modo il traffico viene indirizzato sul backbone di Azure?

Il traffico segue il modello: dispositivo della succursale -> provider di servizi Internet -> Microsoft Edge - > controller di dominio Microsoft -> Microsoft Edge -> provider di servizi Internet -> dispositivo della succursale

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>In questo modello, cosa serve in ogni sito? Semplicemente una connessione Internet?

Sì. Una connessione a Internet e un dispositivo fisico, preferibilmente dai [partner](https://go.microsoft.com/fwlink/p/?linkid=2019615) integrati Microsoft. Facoltativamente è possibile gestire manualmente la configurazione e la connettività ad Azure dal dispositivo preferito.
