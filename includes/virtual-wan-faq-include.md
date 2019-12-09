---
title: File di inclusione
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b65cf26bcea628f784eb086d1b9c88febade25f6
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74829024"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Qual è la differenza tra un gateway di rete virtuale di Azure (gateway VPN) e un gateway VPN di rete WAN virtuale di Azure?

La rete WAN virtuale offre connettività da sito a sito su larga scala ed è ideale per velocità effettiva, scalabilità e semplicità d'uso. La connessione di un sito a un gateway VPN WAN virtuale è diversa da un normale gateway di rete virtuale che usa un gateway di tipo "VPN". Analogamente, quando si esegue la connessione di un circuito ExpressRoute a un hub WAN virtuale, viene usata una risorsa diversa per il gateway ExpressRoute rispetto al normale gateway di rete virtuale che usa il tipo di gateway "ExpressRoute". La rete WAN virtuale supporta fino a 20 Gbps di velocità effettiva aggregata sia per VPN che per ExpressRoute. La rete WAN virtuale dispone anche di automazione per la connettività con un ecosistema di partner per dispositivi del ramo CPE. I dispositivi del ramo CPE eseguono il provisioning automatico e si connettono alla rete WAN virtuale di Azure. Questi dispositivi sono disponibili da un ecosistema di partner VPN e SD-WAN in continua crescita. Vedere l'[elenco dei partner preferiti](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>In che modo la rete WAN virtuale è diversa dal gateway di rete virtuale di Azure?

Una VPN di gateway di rete virtuale è limitata a 30 tunnel. Per le connessioni, è consigliabile usare la rete WAN virtuale per VPN su larga scala. È possibile eseguire fino a 1.000 connessioni di ramo per regione (hub virtuale) con un aggregato di 20 Gbps per hub. Una connessione è un tunnel attivo-attivo dal dispositivo VPN locale all'hub virtuale. È possibile avere un unico hub per area, ovvero è possibile connettere più di 1.000 rami tra gli hub.

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Quali provider di dispositivo (partner di WAN virtuale) sono supportati?

Al momento, molti partner supportano l'esperienza di rete WAN virtuale completamente automatizzata. Per altre informazioni, vedere [Virtual WAN partners](../articles/virtual-wan/virtual-wan-locations-partners.md)(Partner di WAN virtuali). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quali sono i passaggi di Virtual WAN partner automation (Automazione dei partner della rete WAN virtuale)?

Per i passaggi di automazione dei partner, vedere [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automazione dei partner della rete WAN virtuale).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>È necessario usare il dispositivo di un partner preferito?

No. È possibile usare qualsiasi dispositivo che supporti la VPN e soddisfi i requisiti di Azure per il supporto di IKEv2/IKEv1 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>In che modo i partner di WAN virtuale automatizzano la connettività con la WAN virtuale di Azure?

Soluzioni di connettività definite dal software gestiscono in genere i propri dispositivi di ramo usando un controller o un centro di provisioning dei dispositivi. Il controller può usare le API di Azure per automatizzare la connettività alla WAN virtuale di Azure. L'automazione include il caricamento di informazioni sui rami, il download della configurazione di Azure, la configurazione di tunnel IPSec in hub virtuali di Azure e la configurazione automatica della connettività dal dispositivo di ramo alla rete WAN virtuale di Azure. Quando si dispone di centinaia di rami, la connessione tramite i partner CPE di WAN virtuale è semplice poiché l'esperienza di onboarding elimina la necessità di impostare, configurare e gestire la connettività IPsec su larga scala. Per altre informazioni, vedere [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automazione dei partner di WAN virtuale).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>È necessario usare il dispositivo di un partner preferito?

No. È possibile usare qualsiasi dispositivo che supporti la VPN e soddisfi i requisiti di Azure per il supporto di IKEv2/IKEv1 IPsec.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>In che modo la rete WAN virtuale supporta i dispositivi SD-WAN?

I partner della rete WAN virtuale automatizzano la connettività IPsec agli endpoint VPN di Azure. Se il partner della rete WAN virtuale è un provider di SD-WAN, il controller SD-WAN gestisce l'automazione e la connettività IPsec agli endpoint VPN di Azure. Se il dispositivo SD-WAN richiede un proprio endpoint invece di quelli VPN di Azure per una funzionalità SD-WAN proprietaria, è possibile distribuire l'endpoint SD-WAN in una rete virtuale di Azure in coesistenza con la rete WAN virtuale di Azure.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>La WAN virtuale cambia qualche funzionalità di connettività esistente?

Non sono state apportate modifiche alle funzionalità di connettività di Azure esistenti.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Sono disponibili nuove risorse di Gestione risorse per la WAN virtuale?
  
Sì, la WAN virtuale introduce nuove risorse di Gestione risorse. Per altre informazioni, vedere la [panoramica](../articles/virtual-wan/virtual-wan-about.md).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quanti dispositivi VPN possono connettersi a un singolo hub?

Sono supportate fino a 1.000 connessioni per ogni hub virtuale. Ogni connessione è costituita da quattro collegamenti e ciascuna connessione del collegamento supporta due tunnel in una configurazione attivo-attivo. I tunnel terminano in un gateway VPN di hub virtuale di Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Il dispositivo VPN locale può connettersi a più hub?

Sì. Il flusso del traffico inizialmente proviene dal dispositivo locale verso la rete perimetrale Microsoft più vicina e quindi all'hub virtuale.

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>È possibile distribuire e usare l'appliance virtuale di rete preferita (in una rete virtuale di appliance virtuali di rete) con la WAN virtuale di Azure?

Sì, è possibile connettere la rete virtuale dell'appliance virtuale di rete preferita (NVA) alla rete WAN virtuale di Azure. Per prima cosa, connettere la rete virtuale dell'appliance virtuale di rete all'hub con una connessione hub di rete virtuale. Quindi, creare una route di hub virtuale con un hop successivo che punti all'appliance virtuale. È possibile applicare più route alla tabella di routing dell'hub virtuale. Tutti gli spoke connessi alla rete virtuale dell'appliance virtuale di rete devono inoltre essere connessi all'hub virtuale per assicurare che le route di rete virtuale spoke vengano propagate ai sistemi locali.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Posso creare un'appliance virtuale di rete all'interno dell'hub virtuale?

Non è possibile distribuire un'appliance virtuale di rete (NVA) all'interno di un hub virtuale. Tuttavia, è possibile crearla in una rete virtuale spoke connesso all'hub virtuale e abilitare una route nell'hub per indirizzare il traffico verso la rete virtuale di destinazione tramite l'indirizzo IP dell'appliance virtuale di rete (della scheda di interfaccia di rete).

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Un rete virtuale spoke può avere un gateway di rete virtuale?

No. La rete virtuale spoke non può avere un gateway di rete virtuale se è connessa all'hub virtuale.

### <a name="is-there-support-for-bgp"></a>È disponibile il supporto per BGP?

Sì, BGP è supportato. Quando si crea un sito VPN, è possibile inserirvi i parametri BGP. Ciò implica che qualsiasi connessione creata in Azure per tale sito sarà abilitata per BGP. Inoltre, se è già disponibile una rete virtuale con appliance virtuale di rete, collegata a un hub di rete WAN virtuale, per assicurarsi che le route provenienti da tale rete vengano annunciate correttamente, gli spoke ad essa collegati devono disabilitare BGP. Inoltre, connettere queste reti virtuali spoke alla rete virtuale hub per assicurarsi che le route della rete virtuale spoke vengano propagate ai sistemi locali.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>È possibile indirizzare il traffico usando route definite dall'utente nell'hub virtuale?

Sì, è possibile indirizzare il traffico verso una rete virtuale usando una tabella di route dell'hub virtuale. In questo modo è possibile impostare le route per reti virtuali di destinazione in Azure tramite un indirizzo IP specifico, in genere la scheda di interfaccia di rete dell'appliance virtuale di rete.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Sono disponibili informazioni sulle licenze o i prezzi per la WAN virtuale?

Sì. Vedere la pagina dei [Prezzi](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-i-calculate-price-of-a-hub"></a>Come si calcola il prezzo di un hub?

* È previsto il pagamento per i servizi nell'hub. Ad esempio, 10 rami o dispositivi locali che richiedono la connessione alla rete WAN virtuale di Azure implicano la connessione agli endpoint VPN nell'hub. Se ad esempio si tratta di una rete VPN di 1 unità di scala = 500 Mbps, viene addebitato il costo di $ 0,361/h. Per ogni connessione viene addebitato il costo di $ 0,05/h. Per 10 connessioni, il costo totale del servizio/h sarebbe $ 0,361 + $ 0,5/h. Per il traffico in uscita da Azure si applicano i costi dei dati.

* Sono previsti costi aggiuntivi per l'hub. Vedere la pagina dei [Prezzi](https://azure.microsoft.com/pricing/details/virtual-wan/).

* Se il gateway ExpressRoute è dovuto a circuiti ExpressRoute che si connettono a un hub virtuale, è necessario pagare per il prezzo dell'unità di scala. Ogni unità di scala in ER è di 2 Gbps e ogni unità di connessione viene addebitata alla stessa tariffa dell'unità di connessione VPN.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>In che modo vengono accettati i nuovi partner non inclusi nell'elenco dei partner iniziale?

Tutte le API WAN virtuali sono API aperte. È possibile esaminare la documentazione per valutare la fattibilità tecnica. In caso di domande, inviare un messaggio di posta elettronica all'indirizzo azurevirtualwan@microsoft.com. Un partner ideale è quello che ha un dispositivo di cui è possibile eseguire il provisioning per la connettività IPSec IKEv1 o IKEv2.

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Cosa succede se un dispositivo in uso non è incluso nell'elenco di partner della rete WAN virtuale? È comunque possibile usarlo per connettersi alla VPN della rete WAN virtuale di Azure?

Sì, purché il dispositivo supporti IPsec IKEv1 o IKEv2. I partner della rete WAN virtuale automatizzano la connettività dal dispositivo agli endpoint VPN. Ciò implica l'automazione di passaggi come 'caricamento di informazioni sui rami', 'IPsec e configurazione' e 'connettività'. Poiché il dispositivo in uso non proviene da un ecosistema di partner della rete WAN virtuale, sarà necessario assumersi il carico di effettuare manualmente la configurazione di Azure e aggiornare il dispositivo per configurare la connettività IPsec.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>È possibile costruire la rete WAN virtuale di Azure con un modello di Resource Manager?

Una semplice configurazione di una rete WAN virtuale con un hub e un sito VPN può essere creata usando un [modello di avvio rapido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). La rete WAN virtuale è principalmente un REST o un servizio gestito dal Portale.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>È supportato il peering di reti virtuali globale con la WAN virtuale di Azure? 

È possibile connettere una rete virtuale in un'area diversa rispetto alla rete WAN virtuale.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>Le reti virtuali spoke connesse a un hub virtuale possono comunicare tra loro?

Sì. La rete WAN virtuale standard supporta la connettività transitiva da rete virtuale a rete virtuale tramite l'hub rete WAN virtuale a cui sono connesse le reti virtuali. Nella terminologia della rete WAN virtuale, si fa riferimento a questi percorsi come "transito locale di rete virtuale in rete WAN virtuale" per le reti virtuali connesse a un hub rete WAN virtuale e "transito globale di rete virtuale in rete WAN virtuale" per le reti virtuali connesse tramite più hub rete WAN virtuale in due o più aree. Il transito di rete virtuale supporta fino a 3 Gbps di velocità effettiva durante l'anteprima pubblica. La velocità effettiva verrà aumentata quando il transito globale sarà disponibile a livello generale.   

Per alcuni scenari, le reti virtuali spoke possono anche essere collegate direttamente in peering tra loro usando il [peering di reti virtuali](../articles/virtual-network/virtual-network-peering-overview.md) oltre al transito locale o globale di rete virtuale in rete WAN virtuale. In questo caso, il peering di reti virtuali ha la precedenza sulla connessione transitiva tramite l'hub rete WAN virtuale. 

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Che cosa si intende per connessione da dispositivo di ramo alla rete WAN virtuale di Azure?

Una connessione da un dispositivo di ramo alla rete WAN virtuale di Azure supporta fino a quattro collegamenti. Un collegamento è il collegamento di connettività fisica alla posizione del ramo, ad esempio: ATT, Verizon ecc.). Ogni connessione di collegamento è costituita da due tunnel IPsec attivo-attivo.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>La connettività tra succursali è consentita nella rete WAN virtuale?

Sì, la connettività tra succursali è disponibile nella rete WAN virtuale per la VPN e da VPN a ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Il traffico da ramo a ramo passa attraverso la rete WAN virtuale di Azure?

Sì.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>La rete WAN virtuale richiede ExpressRoute da ogni sito?

No, la rete WAN virtuale non richiede ExpressRoute da ogni sito. Usa la connettività da sito a sito IPSec standard tramite collegamenti Internet dal dispositivo a un hub della rete WAN virtuale di Azure. I siti possono essere connessi a una rete del provider usando un circuito ExpressRoute. Per i siti connessi tramite ExpressRoute nell'hub virtuale, questi possono avere un flusso di traffico tra succursali tra la VPN ed ExpressRoute.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>È previsto un limite di velocità effettiva di rete quando si usa la rete WAN virtuale di Azure?

Il numero di succursali è limitato a 1000 connessioni per hub/area e a un totale di 20 Gbps nell'hub. È possibile avere 1 hub per area.

### <a name="how-many-vpn-connections-does-a-virtual-wan-hub-support"></a>Quante connessioni VPN può supportare un hub della rete WAN virtuale?

Un hub di Azure rete WAN virtuale può supportare contemporaneamente fino a 1.000 connessioni da sito a sito, 10.000 connessioni da punto a sito e 4 connessioni ExpressRoute.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Qual è la velocità effettiva totale della VPN di un tunnel VPN e di una connessione?

La velocità effettiva totale della VPN di un hub non supera 20 Gbps in base all'unità di scala scelta. La velocità effettiva è condivisa da tutte le connessioni esistenti. Ogni tunnel in una connessione può supportare fino a 1 Gbps.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-the-portal-how-do-i-configure-that"></a>L'impostazione da 20 Gbps per l'hub virtuale non è visibile nel portale. Come è possibile configurarla?

Passare al gateway VPN all'interno di un hub del portale e fare clic sull'unità di scala per eseguire la regolazione nell'impostazione appropriata.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>La rete WAN virtuale consente al dispositivo locale di usare più ISP in parallelo o si tratta sempre di un singolo tunnel VPN?

Una connessione in ingresso a una VPN della rete WAN virtuale è sempre un tunnel attivo-attivo (per la resilienza nello stesso hub/area) che usa un collegamento disponibile nel ramo. Questo collegamento potrebbe essere un collegamento di ISP nel ramo locale. La rete WAN virtuale "VPNSite" consente di aggiungere informazioni di collegamento al sito. Se si dispone di più ISP sul ramo e ciascuno degli ISP ha fornito un collegamento, tali informazioni possono essere configurate nelle informazioni sul sito VPN in Azure. Tuttavia, la gestione del failover tra ISP nel ramo è completamente un'operazione di routing incentrata sul ramo.

### <a name="what-is-global-transit-architecture"></a>Che cos'è l'architettura di transito globale?

Per informazioni sull'architettura di transito globale, vedere [Architettura della rete di transito globale e WAN virtuale](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>In quale modo il traffico viene indirizzato sul backbone di Azure?

Il traffico segue il modello: dispositivo del ramo -> ISP -> rete perimetrale Microsoft - > controller di dominio Microsoft (rete virtuale hub) -> rete perimetrale Microsoft -> ISP -> dispositivo del ramo

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>In questo modello, cosa serve in ogni sito? Semplicemente una connessione Internet?

Sì. Una connessione a Internet e un dispositivo fisico che supporti IPsec, preferibilmente dei [partner di WAN virtuale](../articles/virtual-wan/virtual-wan-locations-partners.md) integrati Microsoft. Facoltativamente è possibile gestire manualmente la configurazione e la connettività ad Azure dal dispositivo preferito.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Come abilitare la route predefinita (0.0.0.0/0) in una connessione (VPN, ExpressRoute o Rete virtuale):

Un hub virtuale può propagare una route predefinita appresa a una connessione di rete virtuale/VPN da sito a sito/ExpressRoute se il flag è su "Abilitato" nella connessione. Questo flag è visibile quando l'utente modifica una connessione di rete virtuale, VPN o ExpressRoute. Per impostazione predefinita, questo flag è disabilitato quando un sito o un circuito ExpressRoute sono connessi a un hub. Questa funzionalità è abilitata per impostazione predefinita quando si aggiunge una connessione di rete virtuale per connettere una rete virtuale a un hub virtuale. La route predefinita non ha origine nell'hub della rete WAN virtuale; la route predefinita viene propagata se è già stata appresa dall'hub della rete WAN virtuale a seguito della distribuzione di un firewall nell'hub o se per un altro sito connesso è abilitato il tunneling forzato.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quali sono le differenze tra i tipi di rete WAN virtuale (Basic e Standard)?

Il tipo di rete WAN "Basic" consente di creare un hub di base (SKU = Basic). Un tipo di rete WAN "Standard" consente di creare hub standard (SKU = Standard). Gli hub di base sono limitati alla funzionalità VPN da sito a sito. Gli hub standard consentono di usare ExpressRoute, VPN utente (connessione da punto a sito), l'hub mesh completo e il transito da rete virtuale a rete virtuale attraverso gli hub. È dovuto un addebito di base di $ 0,25/h per gli hub standard e una tariffa per l'elaborazione dati per il transito attraverso gli hub durante la connettività da rete virtuale a rete virtuale, nonché per l'elaborazione dati per il traffico da hub a hub. Per altre informazioni, vedere [Reti WAN virtuali Basic e Standard](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Per i prezzi, vedere la pagina dei [Prezzi](https://azure.microsoft.com/pricing/details/virtual-wan/).
