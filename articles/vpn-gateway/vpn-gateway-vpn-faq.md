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
ms.date: 05/30/2017
ms.author: cherylmc,yushwang
ms.openlocfilehash: b12eab7a430e620d0b6e872551c0252ccb5d4c14
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="vpn-gateway-faq"></a>Domande frequenti sul gateway VPN

## <a name="connecting"></a>Connessione a reti virtuali

### <a name="can-i-connect-virtual-networks-in-different-azure-regions"></a>È possibile connettere reti virtuali in diverse aree di Azure?

Sì. Non esistono vincoli di area. Una rete virtuale può connettersi a un'altra rete virtuale nella stessa area o in un'area diversa di Azure. 

### <a name="can-i-connect-virtual-networks-in-different-subscriptions"></a>È possibile connettere reti virtuali in diverse sottoscrizioni?

Sì.

### <a name="can-i-connect-to-multiple-sites-from-a-single-virtual-network"></a>È possibile connettersi a più siti da una singola rete virtuale?

È possibile connettersi a più siti tramite Windows PowerShell e le API REST di Azure. Vedere la sezione delle domande frequenti relative alla [connettività multisito e tra reti virtuali](#V2VMulti) .

### <a name="what-are-my-cross-premises-connection-options"></a>Quali sono le opzioni di connessione cross-premise?

Le seguenti connessioni cross-premise sono supportate:

* Da sito a sito: connessione VPN tramite IPsec (IKE v1 e IKE v2). Per questo tipo di connessione è necessario un dispositivo VPN o RRAS. Per altre informazioni, vedere [Da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
* Da punto a sito: connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol). Questa connessione non richiede un dispositivo VPN. Per altre informazioni, vedere [Da punto a sito](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
* Da rete virtuale a rete virtuale: questo tipo di connessione è analogo alla configurazione da sito a sito. La connessione tra reti virtuali è una connessione VPN tramite IPsec (IKE v1 e IKE v2). Non richiede un dispositivo VPN. Per altre informazioni, vedere [Da rete virtuale a rete virtuale](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
* Multisito: si tratta di una variante di una configurazione da sito a sito che consente di connettere più siti locali a una rete virtuale. Per altre informazioni, vedere [Multisito](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).
* ExpressRoute: si tratta di una connessione diretta ad Azure dalla rate WAN, anziché di una connessione VPN tramite Internet pubblico. Per altre informazioni, vedere la [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md) e le [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md).

Per altre informazioni sulle connessioni del gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

### <a name="what-is-the-difference-between-a-site-to-site-connection-and-point-to-site"></a>Qual è la differenza tra una connessione da sito a sito e una connessione da punto a sito?

Le configurazioni **da sito a sito** (tunnel VPN IPsec/IKE) connettono il percorso locale ad Azure. È quindi possibile connettersi da qualsiasi computer disponibile in locale a qualsiasi macchina virtuale o istanza del ruolo nella rete virtuale, in base alla configurazione scelta per il routing e le autorizzazioni. Questa opzione è ottimale per una connessione cross-premise sempre disponibile ed è adatta per le configurazioni ibride. Questo tipo di connessione si basa su un dispositivo VPN IPSec (dispositivo hardware o software) che è necessario distribuire in corrispondenza del perimetro della rete. Per creare questo tipo di connessione, è necessario avere un indirizzo IPv4 accessibile pubblicamente che non si trovi dietro un NAT.

Le configurazioni **da punto a sito** (VPN tramite SSTP) consentono di connettersi da un computer singolo a qualsiasi elemento disponibile nella rete virtuale. Questo tipo di connessione usa il client VPN incorporato di Windows. La configurazione da spunto a sito prevede l'installazione di un certificato e di un pacchetto di configurazione client VPN che contiene le impostazioni che consentono al computer di connettersi a qualsiasi macchina virtuale o istanza del ruolo all'interno della rete virtuale. Si tratta di un'ottima opzione quando si desidera connettersi a una rete virtuale ma non ci si trova nella sede locale. È utile anche quando non si ha accesso all'hardware VPN o a un indirizzo IP IPv4 accessibile pubblicamente, entrambi necessari per una connessione da sito a sito.

È possibile configurare la rete virtuale in modo da usare sia la connettività da punto a sito che da sito a sito contemporaneamente, purché la connessione da sito a sito venga creata usando un tipo di VPN basato su route per il gateway. I tipi di VPN basati su route sono detti gateway dinamici nel modello di distribuzione classica.

## <a name="gateways"></a>Gateway di rete virtuale

### <a name="is-a-vpn-gateway-a-virtual-network-gateway"></a>Un gateway VPN corrisponde a un gateway di rete virtuale?

Un gateway VPN è un tipo di gateway di rete virtuale, che invia traffico crittografato tra la rete virtuale e la posizione locale tramite una connessione pubblica. È possibile usare il gateway VPN anche per inviare il traffico tra reti virtuali. Quando si crea un gateway VPN, si usa il valore 'Vpn' per -GatewayType. Per altre informazioni, [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

### <a name="what-is-a-policy-based-static-routing-gateway"></a>Cos'è un gateway basato su criteri (con routing statico)?

I gateway basati su criteri implementano VPN basate su criteri. Le VPN basate su criteri crittografano e reindirizzano i pacchetti tramite tunnel IPsec basati su combinazioni di prefissi di indirizzo tra la rete locale e la rete virtuale di Azure. I criteri (o selettore di traffico) vengono in genere definiti come un elenco di accesso nella configurazione VPN.

### <a name="what-is-a-route-based-dynamic-routing-gateway"></a>Cos'è un gateway basato su route (con routing dinamico)?

I gateway basati su route implementano VPN basate su route. Le VPN basate su route usano "route" nella tabella di inoltro IP o di routing per reindirizzare i pacchetti nelle interfacce tunnel corrispondenti. Le interfacce tunnel consentono quindi di crittografare o decrittografare i pacchetti all'interno e all'esterno dei tunnel. I criteri o selettori di traffico per le VPN basate su route vengono configurati come any-to-any (o caratteri jolly).

### <a name="do-i-need-a-gatewaysubnet"></a>Il valore 'GatewaySubnet' è necessario?

Sì. La subnet del gateway contiene gli indirizzi IP usati dai servizi del gateway di rete virtuale. È necessario creare una subnet del gateway per la rete virtuale per configurare un gateway di rete virtuale. Per poter funzionare correttamente, tutte le subnet del gateway devono essere denominate 'GatewaySubnet'. Non assegnare un nome diverso alla subnet del gateway. Non distribuire VM o altri elementi alla subnet del gateway.

Quando si crea la subnet del gateway, si specifica il numero di indirizzi IP inclusi nella subnet. Gli indirizzi IP inclusi nella subnet del gateway sono allocati al servizio del gateway. Alcune configurazioni necessitano dell'allocazione di più indirizzi IP ai servizi del gateway rispetto ad altre. È consigliabile assicurarsi che la subnet del gateway contenga una quantità di indirizzi IP sufficiente per supportare la crescita futura e per possibili nuove configurazioni aggiuntive per la connessione. Anche se è possibile creare una subnet del gateway con dimensioni pari a /29, è consigliabile crearne una con dimensioni pari a /27 o superiori, ad esempio /27, /26, /25 e così via. Esaminare i requisiti per la configurazione da creare e verificare che la subnet del gateway disponibile rispetti tali requisiti.

### <a name="can-i-deploy-virtual-machines-or-role-instances-to-my-gateway-subnet"></a>È possibile distribuire macchine virtuali o istanze del ruolo alla subnet del gateway?

No.

### <a name="can-i-get-my-vpn-gateway-ip-address-before-i-create-it"></a>È possibile ottenere l'indirizzo IP del gateway VPN prima di crearlo?

No. È necessario prima creare il gateway per ottenere l'indirizzo IP. L'indirizzo IP viene modificato se si elimina e si ricrea il gateway VPN.

### <a name="can-i-request-a-static-public-ip-address-for-my-vpn-gateway"></a>È possibile richiedere un indirizzo IP pubblico statico per il gateway VPN?

No. È supportata solo l'assegnazione di indirizzi IP dinamici. Ciò non significa tuttavia che l'indirizzo IP viene modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP del gateway VPN viene modificato solo quando il gateway viene eliminato e ricreato. L'indirizzo IP pubblico del gateway VPN non cambia in caso di ridimensionamento, reimpostazione o altri aggiornamenti o manutenzioni interne del gateway VPN. 

### <a name="how-does-my-vpn-tunnel-get-authenticated"></a>Come si ottiene l'autenticazione del tunnel VPN?

Il tunnel VPN di Azure usa chiavi precondivise (PSK) per l'autenticazione. È possibile generare una chiave precondivisa (PSK) quando si crea il tunnel VPN. È possibile modificare la chiave precondivisa generata automaticamente con il cmdlet di PowerShell di impostazione della chiave precondivisa o l'API REST.

### <a name="can-i-use-the-set-pre-shared-key-api-to-configure-my-policy-based-static-routing-gateway-vpn"></a>È possibile usare l'API di impostazione della chiave precondivisa per configurare la VPN gateway con routing statico basata su criteri?

Sì, è possibile usare l'API di impostazione della chiave precondivisa e il cmdlet di PowerShell per configurare sia le VPN basate su criteri con routing statico sia le VPN basate su route con routing dinamico di Azure.

### <a name="can-i-use-other-authentication-options"></a>È possibile usare altre opzioni di autenticazione?

Per l'autenticazione possono essere usate solo chiavi precondivise.

### <a name="how-do-i-specify-which-traffic-goes-through-the-vpn-gateway"></a>In che modo è possibile specificare il traffico che può passare attraverso il gateway VPN?

#### <a name="resource-manager-deployment-model"></a>Modello di distribuzione di Gestione risorse

* PowerShell: usare "AddressPrefix" per specificare il traffico per il gateway di rete locale.
* Portale di Azure: passare al gateway di rete locale > Configurazione > Spazio indirizzi.

#### <a name="classic-deployment-model"></a>Modello di distribuzione classica

* Portale di Azure: passare alla rete virtuale classica > Connessioni VPN > Connessioni VPN da sito a sito > Local site name (Nome sito locale) > Sito locale > Spazio indirizzi client. 
* Portale classico: aggiungere tutti gli intervalli da inviare tramite il gateway per la rete virtuale nella pagina Reti in Reti locali. 

### <a name="can-i-configure-force-tunneling"></a>È possibile configurare il tunneling forzato?

Sì. Vedere [Configurare il tunneling forzato](vpn-gateway-about-forced-tunneling.md).

### <a name="can-i-set-up-my-own-vpn-server-in-azure-and-use-it-to-connect-to-my-on-premises-network"></a>È possibile configurare il server VPN in Azure e usarlo per connettersi alla rete locale?

Sì, è possibile distribuire i gateway o i server VPN in Azure da Azure Marketplace o tramite la creazione dei propri router VPN. È necessario configurare route definite dall'utente nella rete virtuale per garantire che il traffico venga indirizzato correttamente tra le reti locali e le subnet della rete virtuale.

### <a name="why-are-certain-ports-opened-on-my-vpn-gateway"></a>Perché determinate porte sono aperte nella mia gateway VPN?

Sono necessarie per la comunicazione di infrastruttura di Azure. Sono protette (bloccate) dai certificati di Azure. Senza certificati appropriati, le entità esterne, compresi i clienti di questi gateway, non potranno causare alcun effetto su tali endpoint.

Un gateway VPN è basicamente un dispositivo multihomed con una scheda di rete che utilizza la rete privata del cliente e una scheda di rete rivolta alla rete pubblica. Le entità dell’infrastruttura di Azure non possono utilizzare le reti private del cliente per motivi di conformità, pertanto devono utilizzare endpoint pubblici per la comunicazione dell’infrastruttura. Gli endpoint pubblici vengono analizzati periodicamente dal controllo di sicurezza di Azure.

### <a name="more-information-about-gateway-types-requirements-and-throughput"></a>Altre informazioni sui tipi di gateway, i requisiti e la velocità effettiva

Per altre informazioni, [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

## <a name="s2s"></a>Connessioni da sito a sito e dispositivi VPN

### <a name="what-should-i-consider-when-selecting-a-vpn-device"></a>Quali sono gli aspetti di cui tenere conto nella scelta di un dispositivo VPN?

È stato convalidato un set di dispositivi VPN da sito a sito standard in collaborazione con fornitori di dispositivi. Per un elenco dei dispositivi VPN sicuramente compatibili, gli esempi o le istruzioni di configurazione corrispondenti e le relative specifiche, vedere l'articolo relativo ai [dispositivi VPN](vpn-gateway-about-vpn-devices.md). Tutti i dispositivi appartenenti ai gruppi di dispositivi di cui è indicata la compatibilità nota dovrebbero funzionare con Rete virtuale. Per agevolare la configurazione del dispositivo VPN, fare riferimento al collegamento o all'esempio di configurazione del dispositivo corrispondente al gruppo di dispositivi appropriato.

### <a name="where-can-i-find-vpn-device-configuration-settings"></a>Dove è possibile reperire le impostazioni di configurazione per i dispositivi VPN?

[!INCLUDE [vpn devices](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

### <a name="how-do-i-edit-vpn-device-configuration-samples"></a>Come è possibile modificare gli esempi di configurazione del dispositivo VPN?

Per informazioni sulla modifica degli esempi, vedere [Modifica degli esempi di configurazione di dispositivo](vpn-gateway-about-vpn-devices.md#editing).

### <a name="where-do-i-find-ipsec-and-ike-parameters"></a>Dove è possibile reperire i parametri IPsec e IKE?

Per informazioni sui parametri IPsec/IKE, vedere [Parametri](vpn-gateway-about-vpn-devices.md#ipsec).

### <a name="why-does-my-policy-based-vpn-tunnel-go-down-when-traffic-is-idle"></a>Perché il tunnel VPN basato su criteri si arresta quando il traffico è inattivo?

Questo comportamento è previsto per gateway VPN basate su criteri (anche note come routing statico). Quando il traffico attraverso il tunnel è inattivo per più di 5 minuti, il tunnel verrà arrestato. Quando il traffico inizierà a scorrere in entrambe le direzioni, il tunnel verrà ripristinato immediatamente.

### <a name="can-i-use-software-vpns-to-connect-to-azure"></a>È possibile usare soluzioni software VPN per connettersi ad Azure?

Sono supportati server RRAS (Routing e Accesso remoto) in Windows Server 2012 per la configurazione da sito a sito cross-premise.

Con il gateway dovrebbero funzionare anche altre soluzioni software VPN, purché siano conformi alle implementazioni di IPSec standard del settore. Per istruzioni sulla configurazione e sull'assistenza, contattare il fornitore del software.

## <a name="P2S"></a>Da punto a sito - autenticazione del certificato nativa di Azure

[!INCLUDE [P2S Azure cert](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="P2SRADIUS"></a>Da punto a sito - autenticazione RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="V2VMulti"></a>Connessioni da rete virtuale a rete virtuale e multisito

[!INCLUDE [vpn-gateway-vnet-vnet-faq-include](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

### <a name="can-i-use-azure-vpn-gateway-to-transit-traffic-between-my-on-premises-sites-or-to-another-virtual-network"></a>È possibile usare un gateway VPN di Azure per il transito del traffico tra i siti locali o verso un'altra rete virtuale?

**Modello di distribuzione Resource Manager**<br>
Sì. Per altre informazioni, vedere la sezione [BGP](#bgp).

**Modello di distribuzione classica**<br>
Con il modello di distribuzione classica il transito del traffico attraverso il gateway VPN di Azure è possibile, ma si basa su spazi di indirizzi definiti in modo statico nel file di configurazione di rete. Il protocollo BGP non è ancora supportato con le reti virtuali di Azure e i gateway VPN con il modello di distribuzione classica. Senza BGP la definizione manuale degli spazi di indirizzi in transito è soggetta a errori e non è consigliata.

### <a name="does-azure-generate-the-same-ipsecike-pre-shared-key-for-all-my-vpn-connections-for-the-same-virtual-network"></a>Azure genera la stessa chiave precondivisa IPsec/IKE per tutte le connessioni VPN per una stessa rete virtuale?

No, per impostazione predefinita vengono generate chiavi precondivise diverse per connessioni VPN diverse. È tuttavia possibile usare la nuova API REST di impostazione della chiave gateway VPN o il cmdlet di PowerShell per impostare il valore di chiave che si preferisce. La chiave DEVE essere una stringa alfanumerica di lunghezza compresa tra 1 e 128 caratteri.

### <a name="do-i-get-more-bandwidth-with-more-site-to-site-vpns-than-for-a-single-virtual-network"></a>Si ottiene maggiore larghezza di banda con più VPN da sito a sito per una singola rete virtuale?

No, tutti i tunnel VPN, incluse le VPN da punto a sito, condividono lo stesso gateway VPN di Azure e la larghezza di banda disponibile.

### <a name="can-i-configure-multiple-tunnels-between-my-virtual-network-and-my-on-premises-site-using-multi-site-vpn"></a>È possibile configurare più tunnel tra una rete virtuale e un sito locale usando una VPN multisito?

Sì, ma è necessario configurare BGP in entrambi i tunnel per la stessa località.

### <a name="can-i-use-point-to-site-vpns-with-my-virtual-network-with-multiple-vpn-tunnels"></a>È possibile usare VPN da punto a sito con la rete virtuale con più tunnel VPN?

Sì, è possibile usare VPN da punto a sito (P2S) con i gateway VPN che si connettono a più siti locali e altre reti virtuali.

### <a name="can-i-connect-a-virtual-network-with-ipsec-vpns-to-my-expressroute-circuit"></a>È possibile connettere una rete virtuale con VPN IPsec a un circuito ExpressRoute?

Sì, questa operazione è supportata. Per altre informazioni, vedere [Configurare connessioni ExpressRoute e VPN da sito a sito coesistenti](../expressroute/expressroute-howto-coexist-classic.md).

## <a name="ipsecike"></a>Criteri IPsec/IKE

[!INCLUDE [vpn-gateway-ipsecikepolicy-faq-include](../../includes/vpn-gateway-faq-ipsecikepolicy-include.md)]


## <a name="bgp"></a>BGP

[!INCLUDE [vpn-gateway-faq-bgp-include](../../includes/vpn-gateway-faq-bgp-include.md)]

## <a name="vms"></a>Connettività cross-premise e macchine virtuali

### <a name="if-my-virtual-machine-is-in-a-virtual-network-and-i-have-a-cross-premises-connection-how-should-i-connect-to-the-vm"></a>Se la macchina virtuale si trova in una rete virtuale e si dispone di una connessione cross-premise, in che modo è possibile connettersi alla macchina virtuale?

Sono disponibili diverse opzioni. Se RDP è abilitato per la macchina virtuale, è possibile connettersi alla macchina virtuale usando l'indirizzo IP privato. In tal caso, specificare l'indirizzo IP privato e la porta a cui si vuole connettersi, in genere la porta 3389. Sarà necessario configurare la porta sulla macchina virtuale per il traffico.

È possibile connettersi alla macchina virtuale anche usando l'indirizzo IP privato di un'altra macchina virtuale presente nella stessa rete virtuale. Non è possibile usare RDP sulla macchina virtuale tramite indirizzo IP privato se si esegue la connessione da una posizione esterna alla rete virtuale. Se ad esempio è configurata una rete virtuale da punto a sito e non si stabilisce una connessione dal computer, non è possibile connettersi alla macchina virtuale tramite indirizzo IP privato.

### <a name="if-my-virtual-machine-is-in-a-virtual-network-with-cross-premises-connectivity-does-all-the-traffic-from-my-vm-go-through-that-connection"></a>Se la macchina virtuale si trova in una rete virtuale con connettività cross-premise, il traffico dalla macchina virtuale passa tutto attraverso tale connessione?

No. Attraverso il gateway della rete virtuale passerà solo il traffico con un IP di destinazione incluso negli intervalli di indirizzi IP di rete locale specificati per la rete virtuale. Il traffico che ha un indirizzo IP di destinazione nella rete virtuale rimane all'interno della rete virtuale. Il restante traffico verrà inviato alle reti pubbliche tramite il bilanciamento del carico o, se si usa il tunneling forzato, tramite il gateway VPN di Azure.

### <a name="how-do-i-troubleshoot-an-rdp-connection-to-a-vm"></a>Come risolvere i problemi di una connessione RDP a una VM

[!INCLUDE [Troubleshoot VM connection](../../includes/vpn-gateway-connect-vm-troubleshoot-include.md)]


## <a name="faq"></a>Domande frequenti su Rete virtuale

Vengono visualizzate informazioni sulla rete virtuale aggiuntive in [Domande frequenti sulla rete virtuale](../virtual-network/virtual-networks-faq.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sui gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).
* Per altre informazioni sulle impostazioni di configurazione del gateway VPN, vedere [Informazioni sulle impostazioni di configurazione del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).
