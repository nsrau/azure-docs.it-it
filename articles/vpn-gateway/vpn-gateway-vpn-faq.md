---
title: Domande frequenti sul gateway VPN di Azure | Documentazione Microsoft
description: Domande frequenti sul gateway VPN. Domande frequenti relative alle connessioni cross-premise di Rete virtuale di Microsoft Azure, alle connessioni con configurazioni ibride e ai gateway VPN.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: 6ce36765-250e-444b-bfc7-5f9ec7ce0742
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: fa909503d13805bec32698e039ad24424d5fa8e9
ms.openlocfilehash: 964b0f781891bce671172e2cf27557624e8ddbd4


---
# <a name="vpn-gateway-faq"></a>Domande frequenti sul gateway VPN
## <a name="connecting-to-virtual-networks"></a>Connessione a reti virtuali
### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>È possibile connettere reti virtuali in diverse aree di Azure?
Sì. Non esistono vincoli di area. Una rete virtuale può connettersi a un'altra rete virtuale nella stessa area o in un'area diversa di Azure. 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>È possibile connettere reti virtuali in diverse sottoscrizioni?
Sì.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>È possibile connettersi a più siti da una singola rete virtuale?
È possibile connettersi a più siti tramite Windows PowerShell e le API REST di Azure. Vedere la sezione delle domande frequenti relative alla [connettività multisito e tra reti virtuali](#multi-site-and-vnet-to-vnet-connectivity) .

## <a name="what-are-my-cross-premises-connection-options"></a>Quali sono le opzioni di connessione cross-premise?
Le seguenti connessioni cross-premise sono supportate:

* [Site-to-Site](vpn-gateway-site-to-site-create.md) : connessione VPN tramite IPsec (IKE v1 e IKE v2). Per questo tipo di connessione è necessario un dispositivo VPN o RRAS.
* [Point-to-Site](vpn-gateway-point-to-site-create.md) : connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol). Questa connessione non richiede un dispositivo VPN.
* [Da rete virtuale a rete virtuale](virtual-networks-configure-vnet-to-vnet-connection.md) : questo tipo di connessione è analogo alla configurazione d sito a sito. La connessione tra reti virtuali è una connessione VPN tramite IPsec (IKE v1 e IKE v2). Non richiede un dispositivo VPN.
* [Multisito](vpn-gateway-multi-site.md) : si tratta di una variante di una configurazione da sito a sito che consente di connettere più siti locali a una rete virtuale.
* [ExpressRoute](../expressroute/expressroute-introduction.md) - ExpressRoute è una connessione diretta ad Azure dalla rate WAN, non sulla rete Internet pubblica. Per altre informazioni, vedere [ExpressRoute Technical Overview](../expressroute/expressroute-introduction.md) (Panoramica tecnica su ExpressRoute) e [ExpressRoute FAQ](../expressroute/expressroute-faqs.md) (Domande frequenti su ExpressRoute).

Per altre informazioni sulle connessioni, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Qual è la differenza tra una connessione da sito a sito e una connessione da punto a sito?
**Site-to-Site** consentono di connettere qualsiasi computer locale a qualsiasi macchina virtuale o istanza del ruolo all'interno della rete virtuale, a seconda della configurazione di routing. Questa opzione è ottimale per una connessione cross-premise sempre disponibile ed è adatta per le configurazioni ibride. Questo tipo di connessione si basa su un dispositivo VPN IPSec (dispositivo hardware o software) che è necessario distribuire in corrispondenza del perimetro della rete. Per creare questo tipo di connessione, è necessario avere l'hardware VPN richiesto e un indirizzo IPv4 accessibile pubblicamente.

**Point-to-Site** consentono di connettere un singolo computer a qualsiasi elemento all'interno della rete virtuale. Questo tipo di connessione usa il client VPN incorporato di Windows. La configurazione da spunto a sito prevede l'installazione di un certificato e di un pacchetto di configurazione client VPN che contiene le impostazioni che consentono al computer di connettersi a qualsiasi macchina virtuale o istanza del ruolo all'interno della rete virtuale. Si tratta di un'ottima opzione quando si desidera connettersi a una rete virtuale ma non ci si trova nella sede locale. È utile anche quando non si ha accesso all'hardware VPN o a un indirizzo IP IPv4 accessibile pubblicamente, entrambi necessari per una connessione da sito a sito.

È possibile configurare la rete virtuale in modo da usare sia la connettività da punto a sito che da sito a sito contemporaneamente, purché la connessione da sito a sito venga creata usando un tipo di VPN basato su route per il gateway. I tipi di VPN basati su route sono detti gateway dinamici nel modello di distribuzione classica.

### <a name="what-is-expressroute"></a>Che cos'è ExpressRoute?
ExpressRoute permette di creare connessioni private tra i data center Microsoft e l'infrastruttura disponibile localmente o in un ambiente con percorso condiviso. Con ExpressRoute è possibile stabilire connessioni a servizi cloud Microsoft, ad esempio Microsoft Azure e Office 365 in una struttura di partner ExpressRoute con percorso condiviso, oppure connettersi direttamente dalla rete WAN esistente, ad esempio una VPN MPLS fornita da un provider di servizi di rete.

Le connessioni ExpressRoute offrono un livello di sicurezza migliore, maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tradizionali. In alcuni casi, l'uso di connessioni ExpressRoute per trasferire i dati tra la rete locale e Azure può offrire vantaggi significativi in termini di costi. Se già stata creata una connessione cross-premise dalla rete locale ad Azure, è possibile eseguire la migrazione a una connessione ExpressRoute, mantenendo intatta la rete virtuale.

Per altre informazioni, vedere le [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md) .

## <a name="site-to-site-connections-and-vpn-devices"></a>Connessioni Site-to-Site e dispositivi VPN
### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Quali sono gli aspetti di cui tenere conto nella scelta di un dispositivo VPN?
È stato convalidato un set di dispositivi VPN da sito a sito standard in collaborazione con fornitori di dispositivi. Per un elenco dei dispositivi VPN sicuramente compatibili, per gli esempi o le istruzioni di configurazione corrispondenti e le relative specifiche, fare clic [qui](vpn-gateway-about-vpn-devices.md). Tutti i dispositivi appartenenti ai gruppi di dispositivi di cui è indicata la compatibilità nota dovrebbero funzionare con Rete virtuale. Per agevolare la configurazione del dispositivo VPN, fare riferimento al collegamento o all'esempio di configurazione del dispositivo corrispondente al gruppo di dispositivi appropriato.

### <a name="what-do-i-do-if-i-have-a-vpn-device-that-isnt-in-the-known-compatible-device-list"></a>Quali operazioni è opportuno eseguire se si possiede un dispositivo VPN non incluso nell'elenco di dispositivi con compatibilità nota?
Se un dispositivo non è elencato tra i dispositivi VPN con compatibilità nota e lo si vuole usare per la connessione VPN, è necessario verificare che soddisfi i parametri e le opzioni di configurazione IPsec/IKE supportati elencati [qui](vpn-gateway-about-vpn-devices.md). I dispositivi che soddisfano i requisiti minimi dovrebbero funzionare correttamente con i gateway VPN. Contattare il produttore del dispositivo per assistenza e istruzioni di configurazione.

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Perché il tunnel VPN basato su criteri si arresta quando il traffico è inattivo?
Questo comportamento è previsto per gateway VPN basate su criteri (anche note come routing statico). Quando il traffico attraverso il tunnel è inattivo per più di 5 minuti, il tunnel verrà arrestato. Quando il traffico inizierà a scorrere in entrambe le direzioni, il tunnel verrà ripristinato immediatamente.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>È possibile usare soluzioni software VPN per connettersi ad Azure?
Sono supportati server RRAS (Routing e Accesso remoto) in Windows Server 2012 per la configurazione da sito a sito cross-premise.

Con il gateway dovrebbero funzionare anche altre soluzioni software VPN, purché siano conformi alle implementazioni di IPSec standard del settore. Per istruzioni sulla configurazione e sull'assistenza, contattare il fornitore del software.

## <a name="point-to-site-connections"></a>Connessioni Point-to-Site
### <a name="what-operating-systems-can-i-use-with-point-to-site"></a>Quali sistemi operativi è possibile usare con la connettività da punto a sito?
Sono supportati i sistemi operativi seguenti:

* Windows 7 (a 32 e 64 bit)
* Windows Server 2008 R2 (solo a 64 bit)
* Windows 8 (a 32 e 64 bit)
* Windows 8.1 (a 32 e 64 bit)
* Windows Server 2012 (solo a 64 bit)
* Windows Server 2012 R2 (solo a 64 bit)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp"></a>Per la connettività da punto a sito è possibile usare qualsiasi client VPN software che supporta SSTP?
No. L'assistenza è limitata solo alle versioni dei sistemi operativi Windows elencati in precedenza.

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Quanti endpoint client VPN è possibile includere nella configurazione da punto sito?
Sono supportati fino a 128 client VPN da poter connettere contemporaneamente a una rete virtuale.

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>È possibile usare la CA radice della PKI interna per la connettività da punto a sito?
Sì. In precedenza, era possibile utilizzare solo certificati radice autofirmati. È ancora possibile caricare 20 certificati radice.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>È possibile attraversare proxy e firewall con la funzionalità Da punto a sito
Sì. Viene usato SSTP (Secure Sockets Tunneling Protocol) per effettuare il tunneling tramite firewall. Questo tunnel verrà visualizzato come connessione HTTPs.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Se si riavvia un computer client configurato per la funzionalità Da punto a sito, la VPN verrà riconnessa automaticamente?
Per impostazione predefinita, tramite il computer client non verrà ristabilita automaticamente la connessione VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>La funzionalità Da punto a sito supporta la riconnessione automatica e il DNS dinamico nei client VPN?
La riconnessione automatica e il DNS dinamico non sono supportati attualmente nelle VPN da punto a sito.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>È possibile la coesistenza di configurazioni da sito a sito e punto a sito per la stessa rete virtuale?
Sì. Entrambe queste soluzioni funzionano se si ha un tipo di VPN basata su route per il gateway. Per il modello di distribuzione classica è necessario un gateway dinamico. Non viene fornito il supporto per i gateway VPN con routing statico da punto a sito o i gateway che usano il cmdlet `-VpnType PolicyBased`.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>È possibile configurare un client da punto a sito per connettersi contempo a più reti virtuali?
Sì, è possibile. I prefissi IP delle reti virtuali non devono tuttavia essere sovrapposti e gli spazi di indirizzi da punto a sito non devono sovrapporsi tra le reti virtuali.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Che velocità effettiva è possibile prevedere usando connessioni da sito a sito o da punto a sito?
È difficile mantenere esattamente la velocità effettiva tramite i tunnel VPN. IPSec e SSTP sono protocolli VPN con un elevato livello di crittografia. La velocità effettiva è limitata inoltre dalla latenza e dalla larghezza di banda tra le sedi locali e Internet.

## <a name="gateways"></a>Gateway
### <a name="what-is-a-policy-based-static-routing-gateway"></a>Cos'è un gateway basato su criteri (con routing statico)?
I gateway basati su criteri implementano VPN basate su criteri. Le VPN basate su criteri crittografano e reindirizzano i pacchetti tramite tunnel IPsec basati su combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure. I criteri (o selettore di traffico) vengono in genere definiti come un elenco di accesso nella configurazione VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Cos'è un gateway basato su route (con routing dinamico)?
I gateway basati su route implementano VPN basate su route. Le VPN basate su route usano "route" nella tabella di inoltro IP o di routing per reindirizzare i pacchetti nelle interfacce tunnel corrispondenti. Le interfacce tunnel consentono quindi di crittografare o decrittografare i pacchetti all'interno e all'esterno dei tunnel. I criteri o selettori di traffico per le VPN basate su route vengono configurati come any-to-any (o caratteri jolly).

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>È possibile ottenere l'indirizzo IP del gateway VPN prima di crearlo?
No. È necessario prima creare il gateway per ottenere l'indirizzo IP. L'indirizzo IP viene modificato se si elimina e si ricrea il gateway VPN.

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Come si ottiene l'autenticazione del tunnel VPN?
Il tunnel VPN di Azure usa chiavi precondivise (PSK) per l'autenticazione. È possibile generare una chiave precondivisa (PSK) quando si crea il tunnel VPN. È possibile modificare la chiave precondivisa generata automaticamente con il cmdlet di PowerShell di impostazione della chiave precondivisa o l'API REST.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>È possibile usare l'API di impostazione della chiave precondivisa per configurare la VPN gateway con routing statico basata su criteri?
Sì, è possibile usare l'API di impostazione della chiave precondivisa e il cmdlet di PowerShell per configurare sia le VPN basate su criteri con routing statico sia le VPN basate su route con routing dinamico di Azure.

### <a name="can-i-use-other-authentication-options"></a>È possibile usare altre opzioni di autenticazione?
Per l'autenticazione possono essere usate solo chiavi precondivise.

### <a name="what-is-the-gatewaysubnet-and-why-is-it-needed"></a>Che cos'è "GatewaySubnet" e perché è necessaria?
È disponibile un servizio gateway che viene eseguito per abilitare la connettività cross-premise.

È necessario creare una subnet del gateway per la rete virtuale configurare un gateway VPN. Per poter funzionare correttamente, tutte le subnet del gateway devono essere denominate GatewaySubnet. Non assegnare un nome diverso alla subnet del gateway. Non distribuire VM o altri elementi alla subnet del gateway.

Le dimensioni minime della subnet del gateway dipendono interamente dalla configurazione che si vuole creare. Anche se è possibile creare una subnet del gateway pari a/29 per alcune configurazioni, è consigliabile creare una subnet del gateway di /28 o superiore (/&28;, /27, /26 e così via.).

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>È possibile distribuire macchine virtuali o istanze del ruolo alla subnet del gateway?
No.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>In che modo è possibile specificare il traffico che può passare attraverso il gateway VPN?

####<a name="resource-manager-deployment-model"></a>Modello di distribuzione di Gestione risorse
* PowerShell: usare "AddressPrefix" per specificare il traffico per il gateway di rete locale.
* Portale di Azure: passare al gateway di rete locale > Configurazione > Spazio indirizzi.

####<a name="classic-deployment-model"></a>Modello di distribuzione classica
* Portale di Azure: passare alla rete virtuale classica > Connessioni VPN > Connessioni VPN da sito a sito > Local site name (Nome sito locale) > Sito locale > Spazio indirizzi client. 
* Portale classico: aggiungere tutti gli intervalli che si vuole inviare tramite il gateway per la rete virtuale nella pagina Reti in Reti locali. 

### <a name="can-i-configure-forced-tunneling"></a>È possibile configurare il tunneling forzato?
Sì. Vedere [Configurare il tunneling forzato](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>È possibile configurare il server VPN in Azure e usarlo per connettersi alla rete locale?
Sì, è possibile distribuire i gateway o i server VPN in Azure da Azure Marketplace o tramite la creazione dei propri router VPN. Sarà necessario configurare route definite dall'utente nella rete virtuale per garantire che il traffico venga indirizzato correttamente tra le reti locali e le subnet della rete virtuale.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Perché determinate porte sono aperte nella mia gateway VPN?
Sono necessarie per la comunicazione di infrastruttura di Azure. Sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati, le entità esterne, compresi i clienti di questi gateway, non potranno causare alcun effetto su tali endpoint.

Un gateway VPN è basicamente un dispositivo multihomed con una scheda di rete che utilizza la rete privata del cliente e una scheda di rete rivolta alla rete pubblica. Le entità dell’infrastruttura di Azure non possono utilizzare le reti private del cliente per motivi di conformità, pertanto devono utilizzare endpoint pubblici per la comunicazione dell’infrastruttura. Gli endpoint pubblici vengono analizzati periodicamente dal controllo di sicurezza di Azure.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Altre informazioni sui tipi di gateway, i requisiti e la velocità effettiva
Per altre informazioni, vedere [About VPN Gateway Settings](vpn-gateway-about-vpn-gateway-settings.md) (Informazioni sulle impostazioni del gateway VPN).

## <a name="multi-site-and-vnet-to-vnet-connectivity"></a>connettività multisito e tra reti virtuali
### <a name="which-type-of-gateways-can-support-multi-site-and-vnet-to-vnet-connectivity"></a>Quale tipo di gateway supporta la connettività multisito e tra reti virtuali?
Solo VPN basate su route (routing dinamico).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>È possibile connettere una rete virtuale con un tipo di VPN RouteBased a un'altra rete virtuale con un tipo di VPN PolicyBased?
No, entrambe le reti virtuali devono usare VPN basate su route con routing dinamico.

### <a name="is-the-vnet-to-vnet-traffic-secure"></a>Il traffico tra reti virtuali è sicuro?
Sì, è protetto mediante la crittografia IPsec/IKE.

### <a name="does-vnet-to-vnet-traffic-travel-over-the-azure-backbone"></a>Il traffico da rete virtuale a rete virtuale scorre attraverso il backbone di Azure?
Sì, questo traffico attraversa il backbone di Azure. Non passa da Internet.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>A quanti siti locali e reti virtuali può connettersi una rete virtuale?
Max. 10 combinate per i gateway con routing dinamico base e standard, 30 per i gateway VPN con Prestazioni elevate.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>È possibile usare VPN da punto a sito con la rete virtuale con più tunnel VPN?
Sì, è possibile usare VPN da punto a sito (P2S) con i gateway VPN che si connettono a più siti locali e altre reti virtuali.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>È possibile configurare più tunnel tra una rete virtuale e un sito locale usando una VPN multisito?
Sì, ma è necessario configurare BGP in entrambi i tunnel per la stessa località.

### <a name="can-there-be-overlapping-address-spaces-among-the-connected-virtual-networks-and-on-premises-local-sites"></a>Possono esistere spazi di indirizzi sovrapposti tra le reti virtuali connesse e i siti locali?
No. In presenza di spazi di indirizzi sovrapposti, il caricamento del file di configurazione di rete o la creazione della rete virtuale hanno esito negativo.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Si ottiene maggiore larghezza di banda con più VPN da sito a sito per una singola rete virtuale?
No, tutti i tunnel VPN, incluse le VPN da punto a sito, condividono lo stesso gateway VPN di Azure e la larghezza di banda disponibile.

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>È possibile usare un gateway VPN di Azure per il transito del traffico tra i siti locali o verso un'altra rete virtuale?

####<a name="resource-manager-deployment-model"></a>Modello di distribuzione di Gestione risorse
Sì. Per altre informazioni, vedere la sezione [BGP](#bgp).

####<a name="classic-deployment-model"></a>Modello di distribuzione classica
Con il modello di distribuzione classica il transito del traffico attraverso il gateway VPN di Azure è possibile, ma si basa su spazi di indirizzi definiti in modo statico nel file di configurazione di rete. Il protocollo BGP non è ancora supportato con le reti virtuali di Azure e i gateway VPN con il modello di distribuzione classica. Senza BGP la definizione manuale degli spazi di indirizzi in transito è soggetta a errori e non è consigliata.<br>

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure genera la stessa chiave precondivisa IPsec/IKE per tutte le connessioni VPN per una stessa rete virtuale?
No, per impostazione predefinita vengono generate chiavi precondivise diverse per connessioni VPN diverse. È tuttavia possibile usare la nuova API REST di impostazione della chiave gateway VPN o il cmdlet di PowerShell per impostare il valore di chiave che si preferisce. La chiave DEVE essere una stringa alfanumerica di lunghezza compresa tra 1 e 128 caratteri.

### <a name="does-azure-charge-for-traffic-between-virtual-networks"></a>È previsto un addebito per il traffico tra le reti virtuali?
Per il traffico tra diverse reti virtuali di Azure, in Azure viene addebitato un costo solo per il traffico che passa da un'area di Azure a un'altra. La tariffa è riportata nella pagina [Prezzi del gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/) di Azure .

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>È possibile connettere una rete virtuale con VPN IPsec a un circuito ExpressRoute?
Sì, questa operazione è supportata. Per altre informazioni, vedere [Configurare connessioni ExpressRoute e VPN da sito a sito coesistenti](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="a-namebgpabgp"></a><a name="bgp"></a>BGP
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## <a name="cross-premises-connectivity-and-vms"></a>Connettività cross-premise e macchine virtuali
### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Se la macchina virtuale si trova in una rete virtuale e si dispone di una connessione cross-premise, in che modo è possibile connettersi alla macchina virtuale?
Sono disponibili diverse opzioni. Se RDP è abilitato ed è stato creato un endpoint, è possibile connettersi alla macchina virtuale tramite il VIP. In tal caso, specificare il VIP e la porta a cui si desidera connettersi. Sarà necessario configurare la porta sulla macchina virtuale per il traffico. In genere, è possibile passare al portale di Azure classico e salvare le impostazioni per la connessione RDP al computer. Le impostazioni contengono le informazioni necessarie sulla connessione.

Se si dispone di una rete virtuale per cui è configurata la connettività cross-premise, è possibile connettersi alla macchina virtuale usando il DIP interno o l'indirizzo IP privato. È possibile connettersi alla macchina virtuale anche usando il DIP interno di un'altra macchina virtuale presente nella stessa rete virtuale. Non è possibile usare RDP sulla macchina virtuale tramite DIP se si esegue la connessione da una posizione esterna alla rete virtuale. Se ad esempio è configurata una rete virtuale da punto a sito e non si stabilisce una connessione dal computer, non è possibile connettersi alla macchina virtuale tramite DIP.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Se la macchina virtuale si trova in una rete virtuale con connettività cross-premise, il traffico dalla macchina virtuale passa tutto attraverso tale connessione?
No. Attraverso il gateway della rete virtuale passerà solo il traffico con un IP di destinazione incluso negli intervalli di indirizzi IP di rete locale specificati per la rete virtuale. Il traffico che ha un indirizzo IP di destinazione nella rete virtuale rimane all'interno della rete virtuale. Il restante traffico verrà inviato alle reti pubbliche tramite il bilanciamento del carico o, se si usa il tunneling forzato, tramite il gateway VPN di Azure. Per la risoluzione dei problemi, è importante assicurarsi che nella rete locale siano elencati tutti gli intervalli da inviare tramite il gateway. Verificare che gli intervalli di indirizzi nella rete locale non si sovrappongano agli intervalli di indirizzi presenti nella rete virtuale. È opportuno anche verificare che il server DNS usato risolva il nome nell'indirizzo IP appropriato.

## <a name="virtual-network-faq"></a>Domande frequenti su Rete virtuale
Vengono visualizzate informazioni sulla rete virtuale aggiuntive in [Domande frequenti sulla rete virtuale](../virtual-network/virtual-networks-faq.md).



<!--HONumber=Jan17_HO4-->


