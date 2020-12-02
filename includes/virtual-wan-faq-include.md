---
title: includere file
description: includere file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c476fe2af1fb4bddff4799609b164aab14f57b00
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187400"
---
### <a name="is-azure-virtual-wan-in-ga"></a>La rete WAN virtuale di Azure è disponibile a livello generale?

Sì, la rete WAN virtuale di Azure è disponibile a livello generale. Tuttavia, la rete WAN virtuale è costituita da diverse funzionalità e diversi scenari. Per alcune funzionalità o alcuni scenari all'interno della rete WAN virtuale, Microsoft applica il tag di anteprima. In questi casi, la funzionalità specifica o lo scenario stesso è disponibile in anteprima. Se non si usa una funzionalità di anteprima specifica, si applica il normale supporto della disponibilità generale. Per altre informazioni sul supporto di anteprima, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Per usare la rete WAN virtuale di Azure, è necessario avere un'architettura hub-spoke con dispositivi SD-WAN/VPN?

La rete WAN virtuale offre molte funzionalità integrate in un singolo pannello di controllo, ad esempio connettività VPN sito/da sito a sito, connettività utente/da punto a sito, connettività ExpressRoute, connettività di rete virtuale, interconnettività VPN ExpressRoute, connettività transitiva da rete virtuale a rete virtuale, routing centralizzato, sicurezza di Firewall e Gestione firewall di Azure, monitoraggio, crittografia ExpressRoute e molte altre. Per iniziare a usare la rete WAN virtuale, non è necessario avere tutti questi casi d'uso. È possibile iniziare semplicemente con uno solo. L'architettura della rete WAN virtuale è di tipo hub-spoke, con scalabilità e prestazioni predefinite in cui i rami (dispositivi VPN/SD-WAN), gli utenti (client VPN di Azure, client openVPN o IKEv2), i circuiti ExpressRoute e le reti virtuali fungono da spoke per uno o più hub virtuali. Tutti gli hub sono connessi in una mesh completa in una rete WAN virtuale standard, per cui è possibile usare il backbone Microsoft per la connettività tra qualsiasi spoke. Gli utenti possono configurare manualmente l'architettura hub-spoke con dispositivi SD-WAN/VPN nel portale della rete WAN virtuale di Azure oppure usare le apparecchiature CPE dei partner di rete WAN virtuale (SD-WAN/VPN) per configurare la connettività con Azure. I partner di reti WAN virtuali forniscono l'automazione per la connettività, ovvero la possibilità di esportare le informazioni sui dispositivi in Azure, scaricare la configurazione di Azure e stabilire la connettività con l'hub della rete WAN virtuale di Azure. Per la connettività VPN utente/da punto a sito, sono supportati il [client VPN di Azure](https://go.microsoft.com/fwlink/?linkid=2117554), il client OpenVPN o il client IKEv2. 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>È possibile disabilitare gli hub completamente connessi in una mesh in una rete WAN virtuale?

La rete WAN virtuale è disponibile in due versioni: Di base e standard. Nella rete WAN virtuale Basic gli hub non sono connessi in una mesh. In una rete WAN virtuale Standard, gli hub sono connessi in una mesh e vengono collegati automaticamente quando la rete WAN virtuale viene configurata per la prima volta. L'utente non deve eseguire nessuna operazione specifica. Non deve inoltre disabilitare o abilitare la funzionalità per ottenere gli hub connessi in una mesh. La rete WAN virtuale offre molte opzioni di routing per indirizzare il traffico tra qualsiasi spoke (rete virtuale, VPN o ExpressRoute). Offre la facilità di hub completamente connessi in una mesh, oltre alla flessibilità di routing del traffico in base alle esigenze. 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Come vengono gestite le zone di disponibilità e la resilienza nella rete WAN virtuale?

La rete WAN virtuale è un insieme di hub e servizi resi disponibili all'interno dell'hub. L'utente può avere un numero qualsiasi di reti WAN virtuali in base alle esigenze. In un hub della rete WAN virtuale sono presenti più servizi, ad esempio VPN, ExpressRoute e così via. Ognuno di questi servizi, ad eccezione di Firewall di Azure, viene distribuito in un'area di zone di disponibilità, cioè se l'area supporta le zone di disponibilità. Se un'area diventa una zona di disponibilità dopo la distribuzione iniziale nell'hub, l'utente può ricreare i gateway, il che attiverà una distribuzione nella zona di disponibilità. Il provisioning di tutti i gateway viene eseguito in un hub in modalità attiva-attiva, il che implica una resilienza incorporata all'interno di un hub. Per ottenere resilienza tra aree, gli utenti possono connettersi a più hub. Anche se il concetto di rete WAN virtuale è globale, la risorsa rete WAN virtuale effettiva è basata su Resource Manager e viene distribuita a livello di area. Se nell'area della rete WAN virtuale si verifica un problema, tutti gli hub al suo interno continueranno a funzionare normalmente, ma l'utente non sarà in grado di creare nuovi hub fino a quando l'area della rete WAN virtuale non sarà disponibile.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Quali client sono supportati dalla VPN utente (da punto a sito) della rete WAN virtuale di Azure?

La rete WAN virtuale supporta il [client VPN di Azure](https://go.microsoft.com/fwlink/?linkid=2117554), il client OpenVPN o qualsiasi client IKEv2. L'autenticazione di Azure AD è supportata con il client VPN di Azure. È necessario almeno il sistema operativo client Windows 10 versione 17763.0 o successiva.  I client OpenVPN possono supportare l'autenticazione basata su certificato. Una volta selezionata l'autenticazione basata su certificato nel gateway, verrà visualizzato il file con estensione *ovpn* da scaricare nel dispositivo. IKEv2 supporta sia l'autenticazione basata su certificato che RADIUS. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Per la VPN utente (da punto a sito), perché il pool di client da punto a sito è diviso in due route?

Ogni gateway ha due istanze; la divisione serve a fare in modo che ogni istanza del gateway possa allocare in modo indipendente gli IP client per i client connessi e che il traffico dalla rete virtuale venga reinstradato all'istanza corretta per evitare l'hop tra istanze.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Come si possono aggiungere server DNS per i client da punto a sito?

Per aggiungere server DNS per i client da punto a sito, sono disponibili due opzioni. Il primo metodo è preferibile, perché aggiunge i server DNS personalizzati al gateway invece che al client.

1. Per aggiungere i server DNS personalizzati, usare lo script di PowerShell seguente. Sostituire i valori con quelli del proprio ambiente.

   ```powershell
   // Define variables
   $rgName = "testRG1"
   $virtualHubName = "virtualHub1"
   $P2SvpnGatewayName = "testP2SVpnGateway1"
   $vpnClientAddressSpaces = 
   $vpnServerConfiguration1Name = "vpnServerConfig1"
   $vpnClientAddressSpaces = New-Object string[] 2
   $vpnClientAddressSpaces[0] = "192.168.2.0/24"
   $vpnClientAddressSpaces[1] = "192.168.3.0/24"
   $customDnsServers = New-Object string[] 2
   $customDnsServers[0] = "7.7.7.7"
   $customDnsServers[1] = "8.8.8.8"
   $virtualHub = $virtualHub = Get-AzVirtualHub -ResourceGroupName $rgName -Name $virtualHubName
   $vpnServerConfig1 = Get-AzVpnServerConfiguration -ResourceGroupName $rgName -Name $vpnServerConfiguration1Name

   // Specify custom dns servers for P2SVpnGateway VirtualHub while creating gateway
   createdP2SVpnGateway = New-AzP2sVpnGateway -ResourceGroupName $rgname -Name $P2SvpnGatewayName -VirtualHub $virtualHub -VpnGatewayScaleUnit 1 -VpnClientAddressPool $vpnClientAddressSpaces -VpnServerConfiguration $vpnServerConfig1 -CustomDnsServer $customDnsServers

   // Specify custom dns servers for P2SVpnGateway VirtualHub while updating existing gateway
   $P2SVpnGateway = Get-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName
   $updatedP2SVpnGateway = Update-AzP2sVpnGateway -ResourceGroupName $rgName -Name $P2SvpnGatewayName  -CustomDnsServer $customDnsServers 

   // Re-generate Vpn profile either from PS/Portal for Vpn clients to have the specified dns servers
   ```
2. In alternativa, se si usa il client VPN di Azure per Windows 10, è possibile modificare il file XML del profilo scaricato e aggiungere i tag **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** prima di importarlo.

   ```powershell
      <azvpnprofile>
      <clientconfig>

          <dnsservers>
              <dnsserver>x.x.x.x</dnsserver>
              <dnsserver>y.y.y.y</dnsserver>
          </dnsservers>
    
      </clientconfig>
      </azvpnprofile>
   ```

### <a name="for-user-vpn-point-to-site--how-many-clients-are-supported"></a>Per la VPN utente (da punto a sito), quanti client sono supportati?

Ogni gateway VPN utente da punto a sito include due istanze, ognuna delle quali supporta fino a un determinato numero di utenti in base all'unità di scala. L'unità di scala 1-3 supporta 500 connessioni, l'unità di scala 4-6 supporta 1000 connessioni, l'unità di scala 7-12 supporta 5000 connessioni e l'unità di scala 13-18 supporta fino a 10.000 connessioni. 

Si supponga, ad esempio, che l'utente scelga l'unità di scala 1. Ogni unità di scala implica la distribuzione di un gateway attivo-attivo e ognuna delle istanze (in questo caso 2) supporta fino a 500 connessioni. Anche se è possibile ottenere 500 * 2 connessioni per ogni gateway, questo non significa che se ne pianificano 1000 invece di 500 per questa unità di scala. Se si supera il numero consigliato di connessioni, possono essere richiesti interventi di manutenzione per le istanze, durante i quali la connettività per le 500 connessioni aggiuntive potrebbe interrompersi. Assicurarsi anche di pianificare i tempi di inattività nel caso in cui si decida di aumentare o ridurre l'unità di scala o di cambiare la configurazione da punto a sito nel gateway VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Qual è la differenza tra un gateway di rete virtuale di Azure (gateway VPN) e un gateway VPN di rete WAN virtuale di Azure?

La rete WAN virtuale offre connettività da sito a sito su larga scala ed è ideale per velocità effettiva, scalabilità e semplicità d'uso. La connessione di un sito a un gateway VPN WAN virtuale è diversa da un normale gateway di rete virtuale che usa un gateway di tipo "VPN". Analogamente, quando si esegue la connessione di un circuito ExpressRoute a un hub WAN virtuale, viene usata una risorsa diversa per il gateway ExpressRoute rispetto al normale gateway di rete virtuale che usa il tipo di gateway "ExpressRoute". 

La rete WAN virtuale supporta fino a 20 Gbps di velocità effettiva aggregata sia per VPN che per ExpressRoute. La rete WAN virtuale dispone anche di automazione per la connettività con un ecosistema di partner per dispositivi del ramo CPE. I dispositivi del ramo CPE eseguono includono automazione incorporata per il provisioning automatico e la connessione alla rete WAN virtuale di Azure. Questi dispositivi sono disponibili da un ecosistema di partner VPN e SD-WAN in continua crescita. Vedere l'[elenco dei partner preferiti](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>In che modo la rete WAN virtuale è diversa dal gateway di rete virtuale di Azure?

Una VPN di gateway di rete virtuale è limitata a 30 tunnel. Per le connessioni, è consigliabile usare la rete WAN virtuale per VPN su larga scala. È possibile eseguire fino a 1.000 connessioni di ramo per regione (hub virtuale) con un aggregato di 20 Gbps per hub. Una connessione è un tunnel attivo-attivo dal dispositivo VPN locale all'hub virtuale. È possibile avere un unico hub per area, ovvero è possibile connettere più di 1.000 rami tra gli hub.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Informazioni sull'unità di scala del gateway della rete WAN virtuale

Un'unità di scala è un'unità definita per selezionare una velocità effettiva aggregata di un gateway nell'hub virtuale. 1 unità di scala della rete VPN = 500 Mbps. 1 unità di scala di ExpressRoute = 2 Gbps. Esempio: 10 unità di scala della rete VPN corrispondono a 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Quali provider di dispositivo (partner di WAN virtuale) sono supportati?

Al momento, molti partner supportano l'esperienza di rete WAN virtuale completamente automatizzata. Per altre informazioni, vedere [Virtual WAN partners](../articles/virtual-wan/virtual-wan-locations-partners.md)(Partner di WAN virtuali).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quali sono i passaggi di Virtual WAN partner automation (Automazione dei partner della rete WAN virtuale)?

Per i passaggi di automazione dei partner, vedere [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automazione dei partner della rete WAN virtuale).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>È necessario usare il dispositivo di un partner preferito?

No. È possibile usare qualsiasi dispositivo che supporti la VPN e soddisfi i requisiti di Azure per il supporto di IKEv2/IKEv1 IPsec. La rete WAN virtuale include anche soluzioni di partner CPE che automatizzano la connettività alla rete WAN virtuale di Azure semplificando la configurazione di connessioni VPN IPSec su larga scala.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>In che modo i partner di WAN virtuale automatizzano la connettività con la WAN virtuale di Azure?

Soluzioni di connettività definite dal software gestiscono in genere i propri dispositivi di ramo usando un controller o un centro di provisioning dei dispositivi. Il controller può usare le API di Azure per automatizzare la connettività alla WAN virtuale di Azure. L'automazione include il caricamento di informazioni sui rami, il download della configurazione di Azure, la configurazione di tunnel IPSec in hub virtuali di Azure e la configurazione automatica della connettività dal dispositivo di ramo alla rete WAN virtuale di Azure. Quando si dispone di centinaia di rami, la connessione tramite i partner CPE di WAN virtuale è semplice poiché l'esperienza di onboarding elimina la necessità di impostare, configurare e gestire la connettività IPsec su larga scala. Per altre informazioni, vedere [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automazione dei partner di WAN virtuale).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Cosa succede se un dispositivo in uso non è incluso nell'elenco di partner della rete WAN virtuale? È comunque possibile usarlo per connettersi alla VPN della rete WAN virtuale di Azure?

Sì, purché il dispositivo supporti IPsec IKEv1 o IKEv2. I partner della rete WAN virtuale automatizzano la connettività dal dispositivo agli endpoint VPN. Ciò implica l'automazione di passaggi quali il "caricamento di informazioni sui rami", "IPsec e configurazione" e "connettività". Poiché il dispositivo in uso non proviene da un ecosistema di partner della rete WAN virtuale, sarà necessario assumersi il carico di effettuare manualmente la configurazione di Azure e aggiornare il dispositivo per configurare la connettività IPsec.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>In che modo vengono accettati i nuovi partner non inclusi nell'elenco dei partner iniziale?

Tutte le API WAN virtuali sono API aperte. Per valutare la fattibilità tecnica, vedere la documentazione sull'[automazione dei partner della rete WAN virtuale](../articles/virtual-wan/virtual-wan-configure-automation-providers.md). Un partner ideale è quello che ha un dispositivo di cui è possibile eseguire il provisioning per la connettività IPSec IKEv1 o IKEv2. Una volta che l'azienda ha completato l'attività di automazione per il dispositivo CPE in base alle linee guida fornite in precedenza, è possibile contattare azurevirtualwan@microsoft.com per essere inseriti nella sezione [Connettività tramite partner]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Se si vuole che una soluzione di una specifica azienda venga inclusa nell'elenco di partner della rete WAN virtuale, chiedere all'azienda di inviare un messaggio di posta elettronica all'indirizzo azurevirtualwan@microsoft.com.

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>In che modo la rete WAN virtuale supporta i dispositivi SD-WAN?

I partner della rete WAN virtuale automatizzano la connettività IPsec agli endpoint VPN di Azure. Se il partner della rete WAN virtuale è un provider di SD-WAN, il controller SD-WAN gestisce l'automazione e la connettività IPsec agli endpoint VPN di Azure. Se il dispositivo SD-WAN richiede un proprio endpoint invece di quelli VPN di Azure per una funzionalità SD-WAN proprietaria, è possibile distribuire l'endpoint SD-WAN in una rete virtuale di Azure in coesistenza con la rete WAN virtuale di Azure.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quanti dispositivi VPN possono connettersi a un singolo hub?

Sono supportate fino a 1.000 connessioni per ogni hub virtuale. Ogni connessione è costituita da quattro collegamenti e ciascuna connessione del collegamento supporta due tunnel in una configurazione attivo-attivo. I tunnel terminano in un gateway VPN dell'hub virtuale di Azure. I collegamenti rappresentano il collegamento all'ISP fisico nel dispositivo ramo/VPN.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Che cosa si intende per connessione da dispositivo di ramo alla rete WAN virtuale di Azure?

Una connessione da un dispositivo ramo o VPN nella rete WAN virtuale di Azure non è altro che una connessione VPN che connette virtualmente il sito VPN e il gateway VPN di Azure in un hub virtuale.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Il dispositivo VPN locale può connettersi a più hub?

Sì. Il flusso del traffico inizialmente proviene dal dispositivo locale verso la rete perimetrale Microsoft più vicina e quindi all'hub virtuale.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Sono disponibili nuove risorse di Gestione risorse per la WAN virtuale?
  
Sì, la rete WAN virtuale include nuove risorse di Resource Manager. Per altre informazioni, vedere la [panoramica](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>È possibile distribuire e usare l'appliance virtuale di rete preferita (in una rete virtuale di appliance virtuali di rete) con la WAN virtuale di Azure?

Sì, è possibile connettere la rete virtuale dell'appliance virtuale di rete preferita (NVA) alla rete WAN virtuale di Azure.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Posso creare un'appliance virtuale di rete all'interno dell'hub virtuale?

Non è possibile distribuire un'appliance virtuale di rete (NVA) all'interno di un hub virtuale. Tuttavia, è possibile crearla in una rete virtuale spoke connessa all'hub virtuale e abilitare il routing appropriato per indirizzare il traffico in base alle esigenze.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Un rete virtuale spoke può avere un gateway di rete virtuale?

No. La rete virtuale spoke non può avere un gateway di rete virtuale se è connessa all'hub virtuale.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>È disponibile il supporto per BGP nella connettività VPN?

Sì, BGP è supportato. Quando si crea un sito VPN, è possibile inserirvi i parametri BGP. Ciò implica che qualsiasi connessione creata in Azure per tale sito sarà abilitata per BGP.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Sono disponibili informazioni sulle licenze o i prezzi per la WAN virtuale?

Sì. Vedere la pagina dei [Prezzi](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>È possibile costruire la rete WAN virtuale di Azure con un modello di Resource Manager?

Una semplice configurazione di una rete WAN virtuale con un hub e un sito VPN può essere creata usando un [modello di avvio rapido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). La rete WAN virtuale è principalmente un REST o un servizio gestito dal Portale.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Le reti virtuali spoke connesse a un hub virtuale possono comunicare tra loro (transito V2V)?

Sì. La rete WAN virtuale Standard supporta la connettività transitiva da rete virtuale a rete virtuale tramite l'hub della rete WAN virtuale a cui sono connesse le reti virtuali. Nella terminologia della rete WAN virtuale, si fa riferimento a questi percorsi come "transito locale di rete virtuale in rete WAN virtuale" per le reti virtuali connesse a un hub rete WAN virtuale e "transito globale di rete virtuale in rete WAN virtuale" per le reti virtuali connesse tramite più hub rete WAN virtuale in due o più aree. Per alcuni scenari, le reti virtuali spoke possono anche essere collegate direttamente in peering tra loro usando il [peering di reti virtuali](../articles/virtual-network/virtual-network-peering-overview.md) oltre al transito locale o globale della rete virtuale nella rete WAN virtuale. In questo caso, il peering di reti virtuali ha la precedenza sulla connessione transitiva tramite l'hub della rete WAN virtuale.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>La connettività tra succursali è consentita nella rete WAN virtuale?

Sì, la connettività tra succursali è disponibile nella rete WAN virtuale. Il ramo è concettualmente applicabile a un sito VPN, a circuiti ExpressRoute o a utenti di VPN utente/da punto a sito. La connessione da ramo a ramo è abilitata per impostazione predefinita e può essere individuata nelle impostazioni di configurazione della rete WAN. Gli utenti/rami VPN possono così connettersi ad altri rami VPN. Inoltre la connettività di transito è abilitata tra utenti di VPN e ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Il traffico da ramo a ramo passa attraverso la rete WAN virtuale di Azure?

Sì.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>La rete WAN virtuale richiede ExpressRoute da ogni sito?

No. La rete WAN virtuale non richiede ExpressRoute da ogni sito. I siti possono essere connessi a una rete del provider usando un circuito ExpressRoute. Per i siti connessi tramite ExpressRoute a un hub virtuale e alla VPN IPsec nello stesso hub, l'hub virtuale fornisce la connettività di transito tra l'utente di VPN e ExpressRoute.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>È previsto un limite di velocità effettiva di rete o di connessioni quando si usa la rete WAN virtuale di Azure?

La velocità effettiva della rete viene assegnata per servizio in un hub della rete WAN virtuale. Anche se è possibile avere qualsiasi numero di reti WAN virtuali, per ognuna è consentito 1 hub per area. In ogni hub la velocità effettiva aggregata della VPN è fino a 20 Gbps, la velocità effettiva aggregata di ExpressRoute è fino a 20 Gbps e la velocità effettiva aggregata della VPN utente/VPN da punto a sito è fino a 20 Gbps. Il router nell'hub virtuale supporta fino a 50 Gbps per i flussi di traffico da rete virtuale a rete virtuale e presuppone un totale di carico di lavoro di 2000 macchine virtuali in tutte le reti virtuali connesse a un singolo hub virtuale.

I siti VPN si connettono a un hub tramite connessioni. La rete WAN virtuale supporta fino a 1000 connessioni o 2000 tunnel IPsec per ogni hub virtuale. Quando gli utenti remoti si connettono all'hub virtuale, si connettono al gateway VPN da punto a sito, che supporta fino a 10.000 utenti a seconda dell'unità di scala (larghezza di banda) scelta per il gateway VPN da punto a sito nell'hub virtuale.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Qual è la velocità effettiva totale della VPN di un tunnel VPN e di una connessione?

La velocità effettiva totale della VPN di un hub è fino a 20 Gbps in base all'unità di scala scelta per il gateway VPN. La velocità effettiva è condivisa da tutte le connessioni esistenti. Ogni tunnel in una connessione può supportare fino a 1 Gbps.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>L'impostazione di 20 Gbps per l'hub virtuale non è visibile nel portale. Come è possibile configurarla?

Passare al gateway VPN all'interno di un hub del portale e fare clic sull'unità di scala per eseguire la regolazione nell'impostazione appropriata.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>La rete WAN virtuale consente al dispositivo locale di usare più ISP in parallelo o si tratta sempre di un singolo tunnel VPN?

Le soluzioni per dispositivi locali possono applicare criteri per distribuire il traffico tra più tunnel nell'hub della rete WAN virtuale di Azure (gateway VPN nell'hub virtuale).

### <a name="what-is-global-transit-architecture"></a>Che cos'è l'architettura di transito globale?

Per informazioni sull'architettura di transito globale, vedere [Architettura della rete di transito globale e WAN virtuale](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>In quale modo il traffico viene indirizzato sul backbone di Azure?

Il traffico segue il modello: dispositivo del ramo -> ISP -> rete perimetrale Microsoft - > controller di dominio Microsoft (rete virtuale hub) -> rete perimetrale Microsoft -> ISP -> dispositivo del ramo

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>In questo modello, cosa serve in ogni sito? Semplicemente una connessione Internet?

Sì. Una connessione a Internet e un dispositivo fisico che supporti IPsec, preferibilmente dei [partner di WAN virtuale](../articles/virtual-wan/virtual-wan-locations-partners.md) integrati Microsoft. Facoltativamente è possibile gestire manualmente la configurazione e la connettività ad Azure dal dispositivo preferito.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Come si abilita la route predefinita (0.0.0.0/0) in una connessione (VPN, ExpressRoute o Rete virtuale)?

Un hub virtuale può propagare una route predefinita appresa a una connessione di rete virtuale/VPN da sito a sito/ExpressRoute se il flag è su "Abilitato" nella connessione. Questo flag è visibile quando l'utente modifica una connessione di rete virtuale, VPN o ExpressRoute. Per impostazione predefinita, questo flag è disabilitato quando un sito o un circuito ExpressRoute sono connessi a un hub. Questa funzionalità è abilitata per impostazione predefinita quando si aggiunge una connessione di rete virtuale per connettere una rete virtuale a un hub virtuale. La route predefinita non ha origine nell'hub della rete WAN virtuale; la route predefinita viene propagata se è già stata appresa dall'hub della rete WAN virtuale a seguito della distribuzione di un firewall nell'hub o se per un altro sito connesso è abilitato il tunneling forzato.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>In che modo l'hub virtuale in una rete WAN virtuale seleziona il percorso migliore per una route da più hub

Se un hub virtuale riconosce la stessa route da più hub remoti, l'ordine in cui decide è il seguente:

1. Corrispondenza di prefisso più lungo.
2. Route locali tramite interhub (l'hub virtuale assegna 65520-65520 come spazio indirizzi dell'interhub)
3. Route statiche tramite il protocollo BGP: se la decisione viene presa dal router dell'hub virtuale. Tuttavia, se il decisore è il gateway VPN in cui un sito annuncia le route tramite BGP o fornisce prefissi di indirizzo statici, le route statiche potrebbero essere preferibili rispetto alle route BGP.
4. ExpressRoute (ER) tramite VPN: ER è preferibile rispetto a VPN se il contesto è un hub locale. La connettività di transito tra circuiti ExpressRoute è disponibile solo tramite Copertura globale. Pertanto, negli scenari in cui il circuito ExpressRoute è connesso a un hub ed è presente un altro circuito ExpressRoute connesso a un hub diverso con connessione VPN, la VPN potrebbe essere preferibile per scenari tra hub.
5. Lunghezza del percorso routing asimmetrico.

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>L'hub della rete WAN virtuale consente la connettività tra circuiti ExpressRoute?

Il transito tra ER e ER avviene sempre tramite Copertura globale. I gateway dell'hub virtuale vengono distribuiti in un controller di dominio o in aree di Azure. Quando due circuiti ExpressRoute si connettono tramite Copertura globale, non è necessario che il traffico passi direttamente dai router perimetrali al controller di dominio dell'hub virtuale.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-expressroute-circuits-or-vpn-connections"></a>Esiste un concetto di peso nei circuiti ExpressRoute o nelle connessioni VPN della rete WAN virtuale di Azure?

Quando più circuiti ExpressRoute sono connessi a un hub virtuale, il peso del routing sulla connessione fornisce un meccanismo che consente a ExpressRoute nell'hub virtuale di preferire un circuito rispetto all'altro. Non è disponibile un meccanismo per impostare un peso in una connessione VPN. Azure preferisce sempre una connessione ExpressRoute rispetto a una connessione VPN all'interno di un singolo hub.

### <a name="does-virtual-wan-prefer-expressroute-over-vpn-for-traffic-egressing-azure"></a>La rete WAN virtuale preferisce ExpressRoute rispetto a VPN per il traffico in uscita da Azure?

Sì.

### <a name="when-a-virtual-wan-hub-has-an-expressroute-circuit-and-a-vpn-site-connected-to-it-what-would-cause-a-vpn-connection-route-to-be-preferred-over-expressroute"></a>Quando un hub della rete WAN virtuale include un circuito ExpressRoute a cui è connesso un sito VPN, per quale motivo una route di connessione VPN verrebbe preferita rispetto a ExpressRoute?

Quando un circuito ExpressRoute è connesso all'hub virtuale, i router perimetrali Microsoft rappresentano il primo nodo per la comunicazione tra ambiente locale e Azure. Questi router perimetrali comunicano con i gateway ExpressRoute della rete WAN virtuale, che a loro volta riconoscono le route del router dell'hub virtuale che controlla tutte le route tra qualsiasi gateway nella rete WAN virtuale. I router perimetrali Microsoft elaborano le route ExpressRoute dell'hub virtuale con una priorità più alta rispetto alle route riconosciute dall'ambiente locale. Se per qualsiasi motivo la connessione VPN diventa il mezzo principale da cui l'hub virtuale riconosce le route (ad esempio negli scenari di failover tra ExpressRoute e VPN), a meno che il sito VPN non abbia un percorso di routing simmetrico più lungo, l'hub virtuale continuerà a condividere le route riconosciute dalla VPN con il gateway ExpressRoute, per cui i router di Microsoft Edge preferiranno le route VPN rispetto a quelle locali.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Quando due hub (hub 1 e 2) sono connessi e un circuito ExpressRoute è connesso a entrambi, qual è il percorso con cui una rete virtuale connessa all'hub 1 raggiunge una rete virtuale connessa all'hub 2?

Il comportamento corrente è quello di preferire il percorso del circuito ExpressRoute rispetto a quello da hub a hub per la connettività da rete virtuale a rete virtuale. Tuttavia, questa scelta non è consigliata in una configurazione di rete WAN virtuale. Il team della rete WAN virtuale sta lavorando a una correzione per consentire di preferire il percorso da hub a hub rispetto a quello ExpressRoute. È consigliabile connettere più circuiti ExpressRoute (provider diversi) a un hub e usare la connettività da hub a hub fornita dalla rete WAN virtuale per i flussi di traffico tra aree.

### <a name="can-hubs-be-created-in-different-resource-group-in-virtual-wan"></a>È possibile creare hub in un gruppo di risorse diverso nella rete WAN virtuale?
Sì. Questa opzione è attualmente disponibile solo tramite PowerShell. Il portale della rete WAN virtuale impone che gli hub si trovino nello stesso gruppo di risorse della rete WAN virtuale stessa.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>È disponibile il supporto per IPv6 nella rete WAN virtuale?

IPv6 non è supportato nell'hub della rete WAN virtuale e nei relativi gateway. Attualmente non è neanche supportata la connessione di una rete virtuale con supporto per IPv4 e IPv6 a una rete WAN virtuale. 

Per lo scenario della VPN da punto a sito (utente) con breakout Internet tramite Firewall di Azure, si dovrà probabilmente disattivare la connettività IPv6 nel dispositivo client per forzare il traffico verso l'hub della rete WAN virtuale. Il motivo è che i moderni dispositivi usano per impostazione predefinita gli indirizzi IPv6.

### <a name="what-is-the-recommended-api-version-to-be-used-by-scripts-automating-various-virtual-wan-functionalities"></a>Qual è la versione consigliata dell'API da usare per gli script che automatizzano varie funzionalità della rete WAN virtuale?

È necessaria almeno la versione 05-01-2020 (1° maggio 2020). 

### <a name="are-there-any-virtual-wan-limits"></a>Sono previsti limiti per la rete WAN virtuale?

Vedere la sezione [Limiti della rete WAN virtuale](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#virtual-wan-limits) nella pagina Limiti della sottoscrizione e del servizio.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quali sono le differenze tra i tipi di rete WAN virtuale (Basic e Standard)?

Vedere [Reti WAN virtuali Basic e Standard](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Per i prezzi, vedere la pagina dei [Prezzi](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="does-virtual-wan-store-customer-data"></a>La rete WAN virtuale archivia i dati dei clienti? 

No. La rete WAN virtuale non archivia alcun dato dei clienti.
