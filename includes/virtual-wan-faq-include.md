---
title: includere file
description: includere file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/26/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 28ea1e68441a57d67fef1e78153e00eb1bd09211
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143921"
---
### <a name="does-the-user-need-to-have-hub-and-spoke-with-sd-wanvpn-devices-to-use-azure-virtual-wan"></a>Per usare la rete WAN virtuale di Azure, è necessario avere un'architettura hub-spoke con dispositivi SD-WAN/VPN?

La rete WAN virtuale offre molte funzionalità integrate in un unico riquadro, ad esempio connettività VPN da sito a sito, connettività utente/P2S, connettività ExpressRoute, connettività di rete virtuale, interconnettività ExpressRoute VPN, connettività transitiva VNet-VNet, routing centralizzato, sicurezza firewall di Azure e gestione firewall, monitoraggio, crittografia ExpressRoute e molte altre funzionalità. Non è necessario avere tutti questi casi d'uso per iniziare a usare la rete WAN virtuale. È possibile iniziare con un solo caso di utilizzo. L'architettura WAN virtuale è un'architettura Hub e spoke con scalabilità e prestazioni compilate in cui i rami (dispositivi VPN/SD-WAN), gli utenti (client VPN di Azure, openVPN o client IKEv2), i circuiti ExpressRoute, le reti virtuali servono come spoke per gli hub virtuali. Tutti gli hub sono connessi in una mesh completa in una rete WAN virtuale standard, per cui è possibile usare il backbone Microsoft per la connettività tra qualsiasi spoke. Gli utenti possono configurare manualmente l'architettura hub-spoke con dispositivi SD-WAN/VPN nel portale della rete WAN virtuale di Azure oppure usare le apparecchiature CPE dei partner di rete WAN virtuale (SD-WAN/VPN) per configurare la connettività con Azure. I partner WAN virtuali forniscono l'automazione per la connettività, ovvero la possibilità di esportare le informazioni sul dispositivo in Azure, scaricare la configurazione di Azure e stabilire la connettività all'hub WAN virtuale di Azure. Per la connettività VPN da punto a sito/utente, è supportato il client [VPN di Azure](https://go.microsoft.com/fwlink/?linkid=2117554), OpenVPN o il client IKEv2. 

### <a name="can-you-disable-fully-meshed-hubs-in-a-virtual-wan"></a>È possibile disabilitare gli hub completamente mesh in una rete WAN virtuale?

La rete WAN virtuale è in due tipi: Basic e standard. Nella rete WAN virtuale di base gli hub non vengono mesh. In una rete WAN virtuale standard, gli hub vengono collegati e connessi automaticamente quando la rete WAN virtuale viene configurata per la prima volta. Non è necessario che l'utente esegua alcuna operazione specifica. L'utente non deve inoltre disabilitare o abilitare la funzionalità per ottenere gli hub con rete. La rete WAN virtuale offre molte opzioni di routing per gestire il traffico tra qualsiasi spoke (VNet, VPN o ExpressRoute). Offre la facilità di hub completamente mesh e anche la flessibilità di routing del traffico in base alle esigenze. 

### <a name="how-are-availability-zones-and-resiliency-handled-in-virtual-wan"></a>Come vengono gestiti zone di disponibilità e resilienza nella rete WAN virtuale?

La rete WAN virtuale è una raccolta di hub e servizi resi disponibili all'interno dell'hub. L'utente può disporre di un numero qualsiasi di WAN virtuale in base alle esigenze. In un hub WAN virtuale sono disponibili più servizi, ad esempio VPN, ExpressRoute e così via. Ognuno di questi servizi, ad eccezione del firewall di Azure, viene distribuito in un'area zone di disponibilità, ovvero se l'area supporta zone di disponibilità. Se un'area diventa una zona di disponibilità dopo la distribuzione iniziale nell'hub, l'utente può ricreare i gateway, che attiverà una distribuzione della zona di disponibilità. Il provisioning di tutti i gateway viene eseguito in un hub come attivo-attivo, implicando la resilienza incorporata all'interno di un hub. Gli utenti possono connettersi a più hub se desiderano la resilienza tra le aree. Anche se il concetto di WAN virtuale è globale, la risorsa WAN virtuale effettiva è basata su Gestione risorse e distribuita a livello di area. Se per l'area WAN virtuale si verifica un problema, tutti gli hub nella rete WAN virtuale continueranno a funzionare così come sono, ma l'utente non sarà in grado di creare nuovi hub fino a quando non sarà disponibile l'area WAN virtuale.

### <a name="what-client-does-the-azure-virtual-wan-user-vpn-point-to-site-support"></a>Quali client sono supportati dalla VPN utente (da punto a sito) della rete WAN virtuale di Azure?

La rete WAN virtuale supporta il [client VPN di Azure](https://go.microsoft.com/fwlink/?linkid=2117554), il client OpenVPN o qualsiasi client IKEv2. L'autenticazione di Azure AD è supportata con il client VPN di Azure. È necessario almeno il sistema operativo client Windows 10 versione 17763.0 o successiva.  I client OpenVPN possono supportare l'autenticazione basata su certificati. Una volta selezionata l'autenticazione basata su certificato nel gateway, verrà visualizzato il file con *estensione ovpn* da scaricare nel dispositivo. IKEv2 supporta sia il certificato che l'autenticazione RADIUS. 

### <a name="for-user-vpn-point-to-site--why-is-the-p2s-client-pool-split-into-two-routes"></a>Per la VPN utente (da punto a sito), perché il pool di client da punto a sito è diviso in due route?

Ogni gateway ha due istanze; la divisione serve a fare in modo che ogni istanza del gateway possa allocare in modo indipendente gli IP client per i client connessi e che il traffico dalla rete virtuale venga reinstradato all'istanza corretta per evitare l'hop tra istanze.

### <a name="how-do-i-add-dns-servers-for-p2s-clients"></a>Come si possono aggiungere server DNS per i client da punto a sito?

Per aggiungere server DNS per i client da punto a sito, sono disponibili due opzioni. Il primo metodo è preferibile perché aggiunge i server DNS personalizzati al gateway anziché al client.

1. Usare lo script di PowerShell seguente per aggiungere i server DNS personalizzati. Sostituire i valori per l'ambiente in uso.

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

Ogni gateway VPN utente da punto a sito include due istanze, ognuna delle quali supporta fino a un determinato numero di utenti in base all'unità di scala. Unità di scala 1-3 supporta connessioni 500, unità di scala 4-6 supporta 1000 connessioni, unità di scala 7-12 supporta le connessioni 5000 e l'unità di scala 13-20 supporta fino a 10.000 connessioni. 

Si supponga, ad esempio, che l'utente scelga l'unità di scala 1. Ogni unità di scala implica la distribuzione di un gateway attivo-attivo e ognuna delle istanze (in questo caso 2) supporta fino a 500 connessioni. Poiché è possibile ottenere le connessioni 500 * 2 per ogni gateway, non significa che si pianifica 1000 anziché il 500 per questa unità di scala. Se si supera il numero consigliato di connessioni, potrebbe essere necessario gestire le istanze durante le quali è possibile che la connettività per l'ulteriore 500 venga interrotta. Inoltre, assicurarsi di pianificare i tempi di inattività nel caso in cui si decida di aumentare o ridurre le prestazioni dell'unità di scala o di modificare la configurazione da punto a sito nel gateway VPN.

### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpn-gateway"></a>Qual è la differenza tra un gateway di rete virtuale di Azure (gateway VPN) e un gateway VPN di rete WAN virtuale di Azure?

La rete WAN virtuale offre connettività da sito a sito su larga scala ed è ideale per velocità effettiva, scalabilità e semplicità d'uso. La connessione di un sito a un gateway VPN WAN virtuale è diversa da un normale gateway di rete virtuale che usa un gateway di tipo "VPN". Analogamente, quando si esegue la connessione di un circuito ExpressRoute a un hub WAN virtuale, viene usata una risorsa diversa per il gateway ExpressRoute rispetto al normale gateway di rete virtuale che usa il tipo di gateway "ExpressRoute". 

La rete WAN virtuale supporta fino a 20 Gbps di velocità effettiva aggregata sia per VPN che per ExpressRoute. La rete WAN virtuale dispone anche di automazione per la connettività con un ecosistema di partner per dispositivi del ramo CPE. I dispositivi Branch CPE hanno un'automazione incorporata che esegue il provisioning e la connessione a una rete WAN virtuale di Azure. Questi dispositivi sono disponibili da un ecosistema di partner VPN e SD-WAN in continua crescita. Vedere l'[elenco dei partner preferiti](../articles/virtual-wan/virtual-wan-locations-partners.md).

### <a name="how-is-virtual-wan-different-from-an-azure-virtual-network-gateway"></a>In che modo la rete WAN virtuale è diversa dal gateway di rete virtuale di Azure?

Una VPN di gateway di rete virtuale è limitata a 30 tunnel. Per le connessioni, è consigliabile usare la rete WAN virtuale per VPN su larga scala. È possibile eseguire fino a 1.000 connessioni di ramo per regione (hub virtuale) con un aggregato di 20 Gbps per hub. Una connessione è un tunnel attivo-attivo dal dispositivo VPN locale all'hub virtuale. È possibile avere un unico hub per area, ovvero è possibile connettere più di 1.000 rami tra gli hub.

### <a name="what-is-a-virtual-wan-gateway-scale-unit"></a>Informazioni sull'unità di scala del gateway della rete WAN virtuale

Un'unità di scala è un'unità definita per scegliere una velocità effettiva aggregata di un gateway nell'hub virtuale. 1 unità di scala VPN = 500 Mbps. 1 unità di scala di ExpressRoute = 2 Gbps. Esempio: 10 unità di scala della VPN implicano 500 Mbps * 10 = 5 Gbps

### <a name="which-device-providers-virtual-wan-partners-are-supported"></a>Quali provider di dispositivo (partner di WAN virtuale) sono supportati?

Al momento, molti partner supportano l'esperienza di rete WAN virtuale completamente automatizzata. Per altre informazioni, vedere [Virtual WAN partners](../articles/virtual-wan/virtual-wan-locations-partners.md)(Partner di WAN virtuali).

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quali sono i passaggi di Virtual WAN partner automation (Automazione dei partner della rete WAN virtuale)?

Per i passaggi di automazione dei partner, vedere [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automazione dei partner della rete WAN virtuale).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>È necessario usare il dispositivo di un partner preferito?

No. È possibile usare qualsiasi dispositivo che supporti la VPN e soddisfi i requisiti di Azure per il supporto di IKEv2/IKEv1 IPsec. La rete WAN virtuale include anche soluzioni partner CPE che consentono di automatizzare la connettività alla rete WAN virtuale di Azure, semplificando la configurazione di connessioni VPN IPsec su larga scala.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>In che modo i partner di WAN virtuale automatizzano la connettività con la WAN virtuale di Azure?

Soluzioni di connettività definite dal software gestiscono in genere i propri dispositivi di ramo usando un controller o un centro di provisioning dei dispositivi. Il controller può usare le API di Azure per automatizzare la connettività alla WAN virtuale di Azure. L'automazione include il caricamento di informazioni sui rami, il download della configurazione di Azure, la configurazione di tunnel IPSec in hub virtuali di Azure e la configurazione automatica della connettività dal dispositivo di ramo alla rete WAN virtuale di Azure. Quando si dispone di centinaia di rami, la connessione tramite i partner CPE di WAN virtuale è semplice poiché l'esperienza di onboarding elimina la necessità di impostare, configurare e gestire la connettività IPsec su larga scala. Per altre informazioni, vedere [Virtual WAN partner automation](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) (Automazione dei partner di WAN virtuale).

### <a name="what-if-a-device-i-am-using-is-not-in-the-virtual-wan-partner-list-can-i-still-use-it-to-connect-to-azure-virtual-wan-vpn"></a>Cosa succede se un dispositivo in uso non è incluso nell'elenco di partner della rete WAN virtuale? È comunque possibile usarlo per connettersi alla VPN della rete WAN virtuale di Azure?

Sì, purché il dispositivo supporti IPsec IKEv1 o IKEv2. I partner della rete WAN virtuale automatizzano la connettività dal dispositivo agli endpoint VPN. Ciò implica l'automazione di passaggi come 'caricamento di informazioni sui rami', 'IPsec e configurazione' e 'connettività'. Poiché il dispositivo in uso non proviene da un ecosistema di partner della rete WAN virtuale, sarà necessario assumersi il carico di effettuare manualmente la configurazione di Azure e aggiornare il dispositivo per configurare la connettività IPsec.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>In che modo vengono accettati i nuovi partner non inclusi nell'elenco dei partner iniziale?

Tutte le API WAN virtuali sono API aperte. È possibile passare alla documentazione [automazione del partner WAN virtuale](../articles/virtual-wan/virtual-wan-configure-automation-providers.md) per valutare la fattibilità tecnica. Un partner ideale è quello che ha un dispositivo di cui è possibile eseguire il provisioning per la connettività IPSec IKEv1 o IKEv2. Una volta che l'azienda ha completato il lavoro di automazione per il dispositivo CPE in base alle linee guida di automazione fornite in precedenza, è possibile contattare azurevirtualwan@microsoft.com per elencare la [connettività tramite partner]( ../articles/virtual-wan/virtual-wan-locations-partners.md#partners). Per i clienti che desiderano elencare una determinata soluzione aziendale come partner WAN virtuale, contattare la rete WAN virtuale inviando un messaggio di posta elettronica a azurevirtualwan@microsoft.com .

### <a name="how-is-virtual-wan-supporting-sd-wan-devices"></a>In che modo la rete WAN virtuale supporta i dispositivi SD-WAN?

I partner della rete WAN virtuale automatizzano la connettività IPsec agli endpoint VPN di Azure. Se il partner della rete WAN virtuale è un provider di SD-WAN, il controller SD-WAN gestisce l'automazione e la connettività IPsec agli endpoint VPN di Azure. Se il dispositivo SD-WAN richiede un proprio endpoint invece di quelli VPN di Azure per una funzionalità SD-WAN proprietaria, è possibile distribuire l'endpoint SD-WAN in una rete virtuale di Azure in coesistenza con la rete WAN virtuale di Azure.

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quanti dispositivi VPN possono connettersi a un singolo hub?

Sono supportate fino a 1.000 connessioni per ogni hub virtuale. Ogni connessione è costituita da quattro collegamenti e ciascuna connessione del collegamento supporta due tunnel in una configurazione attivo-attivo. I tunnel terminano in un gateway VPN dell'hub virtuale di Azure. I collegamenti rappresentano il collegamento ISP fisico nel dispositivo Branch/VPN.

### <a name="what-is-a-branch-connection-to-azure-virtual-wan"></a>Che cosa si intende per connessione da dispositivo di ramo alla rete WAN virtuale di Azure?

Una connessione da un ramo o un dispositivo VPN in una rete WAN virtuale di Azure non è nient'altro che una connessione VPN che connette praticamente il sito VPN e il gateway VPN di Azure in un hub virtuale.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>Il dispositivo VPN locale può connettersi a più hub?

Sì. Il flusso del traffico inizialmente proviene dal dispositivo locale verso la rete perimetrale Microsoft più vicina e quindi all'hub virtuale.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Sono disponibili nuove risorse di Gestione risorse per la WAN virtuale?
  
Sì, la rete WAN virtuale ha nuove risorse Gestione risorse. Per altre informazioni, vedere la [panoramica](../articles/virtual-wan/virtual-wan-about.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>È possibile distribuire e usare l'appliance virtuale di rete preferita (in una rete virtuale di appliance virtuali di rete) con la WAN virtuale di Azure?

Sì, è possibile connettere la rete virtuale dell'appliance virtuale di rete preferita (NVA) alla rete WAN virtuale di Azure.

### <a name="can-i-create-a-network-virtual-appliance-inside-the-virtual-hub"></a>Posso creare un'appliance virtuale di rete all'interno dell'hub virtuale?

Non è possibile distribuire un'appliance virtuale di rete (NVA) all'interno di un hub virtuale. Tuttavia, è possibile crearlo in un VNet spoke connesso all'hub virtuale e consentire il routing appropriato per indirizzare il traffico in base alle esigenze.

### <a name="can-a-spoke-vnet-have-a-virtual-network-gateway"></a>Un rete virtuale spoke può avere un gateway di rete virtuale?

No. La rete virtuale spoke non può avere un gateway di rete virtuale se è connessa all'hub virtuale.

### <a name="is-there-support-for-bgp-in-vpn-connectivity"></a>È disponibile il supporto per BGP nella connettività VPN?

Sì, BGP è supportato. Quando si crea un sito VPN, è possibile inserirvi i parametri BGP. Ciò implica che qualsiasi connessione creata in Azure per tale sito sarà abilitata per BGP.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Sono disponibili informazioni sulle licenze o i prezzi per la WAN virtuale?

Sì. Vedere la pagina dei [Prezzi](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>È possibile costruire la rete WAN virtuale di Azure con un modello di Resource Manager?

Una semplice configurazione di una rete WAN virtuale con un hub e un sito VPN può essere creata usando un [modello di avvio rapido](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). La rete WAN virtuale è principalmente un REST o un servizio gestito dal Portale.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other-v2v-transit"></a>Le reti virtuali spoke connesse a un hub virtuale possono comunicare tra loro (transito V2V)?

Sì. La rete WAN virtuale standard supporta la connettività transitiva da VNet a VNet tramite l'hub WAN virtuale a cui sono connesse le reti virtuali. Nella terminologia della rete WAN virtuale, si fa riferimento a questi percorsi come "transito locale di rete virtuale in rete WAN virtuale" per le reti virtuali connesse a un hub rete WAN virtuale e "transito globale di rete virtuale in rete WAN virtuale" per le reti virtuali connesse tramite più hub rete WAN virtuale in due o più aree. Per alcuni scenari, spoke reti virtuali può anche essere sottoposto direttamente a peering usando il [peering di rete virtuale](../articles/virtual-network/virtual-network-peering-overview.md) , oltre al transito VNet WAN virtuale locale o globale. In questo caso, il peering VNet ha la precedenza sulla connessione transitiva tramite l'hub WAN virtuale.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>La connettività tra succursali è consentita nella rete WAN virtuale?

Sì, la connettività tra succursali è disponibile nella rete WAN virtuale. Il ramo è concettualmente applicabile a un sito VPN, a circuiti ExpressRoute o a utenti VPN da punto a sito/utente. L'abilitazione del ramo al ramo è abilitata per impostazione predefinita e può essere individuata nelle impostazioni di configurazione della rete WAN. Ciò consente ai Branch/utenti VPN di connettersi ad altri rami VPN, oltre che alla connettività di transito, tra gli utenti VPN e ExpressRoute.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>Il traffico da ramo a ramo passa attraverso la rete WAN virtuale di Azure?

Sì.

### <a name="does-virtual-wan-require-expressroute-from-each-site"></a>La rete WAN virtuale richiede ExpressRoute da ogni sito?

No. La rete WAN virtuale non richiede ExpressRoute da ciascun sito. I siti possono essere connessi a una rete del provider usando un circuito ExpressRoute. Per i siti connessi tramite ExpressRoute a un hub virtuale e alla VPN IPsec nello stesso hub, l'hub virtuale fornisce la connettività di transito tra l'utente VPN e ExpressRoute.

### <a name="is-there-a-network-throughput-or-connection-limit-when-using-azure-virtual-wan"></a>Esiste una velocità effettiva di rete o un limite di connessione quando si usa la rete WAN virtuale di Azure?

La velocità effettiva della rete è per servizio in un hub WAN virtuale. Sebbene sia possibile avere un numero così elevato di WAN virtuali, ogni WAN virtuale consente 1 hub per ogni area. In ogni hub la velocità effettiva aggregata VPN è fino a 20 Gbps, la velocità effettiva aggregata ExpressRoute è fino a 20 Gbps e la velocità effettiva di aggregazione VPN da punto a sito dell'utente è fino a 20 Gbps. Il router nell'hub virtuale supporta fino a 50 Gbps per i flussi di traffico da VNet a VNet e presuppone un totale del carico di lavoro della macchina virtuale 2000 in tutti i reti virtuali negli hub WAN virtuali.

Quando i siti VPN si connettono a un hub, lo eseguono con le connessioni. La rete WAN virtuale supporta fino a 1000 connessioni o 2000 tunnel IPsec per hub virtuale. Quando gli utenti remoti si connettono all'hub virtuale, si connettono al gateway VPN P2S, che supporta fino a 10.000 utenti a seconda dell'unità di scala (larghezza di banda) scelta per il gateway VPN P2S nell'hub virtuale.

### <a name="what-is-the-total-vpn-throughput-of-a-vpn-tunnel-and-a-connection"></a>Qual è la velocità effettiva totale della VPN di un tunnel VPN e di una connessione?

La velocità effettiva totale della VPN di un hub è fino a 20 Gbps in base all'unità di scala scelta del gateway VPN. La velocità effettiva è condivisa da tutte le connessioni esistenti. Ogni tunnel in una connessione può supportare fino a 1 Gbps.

### <a name="i-dont-see-the-20-gbps-setting-for-the-virtual-hub-in-portal-how-do-i-configure-that"></a>Non è possibile visualizzare l'impostazione di 20 Gbps per l'hub virtuale nel portale. Come è possibile configurarla?

Passare al gateway VPN all'interno di un hub del portale e fare clic sull'unità di scala per eseguire la regolazione nell'impostazione appropriata.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>La rete WAN virtuale consente al dispositivo locale di usare più ISP in parallelo o si tratta sempre di un singolo tunnel VPN?

Le soluzioni per dispositivi locali possono applicare criteri di traffico per indirizzare il traffico tra più tunnel nell'hub WAN virtuale di Azure (gateway VPN nell'hub virtuale).

### <a name="what-is-global-transit-architecture"></a>Che cos'è l'architettura di transito globale?

Per informazioni sull'architettura di transito globale, vedere [Architettura della rete di transito globale e WAN virtuale](../articles/virtual-wan/virtual-wan-global-transit-network-architecture.md).

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>In quale modo il traffico viene indirizzato sul backbone di Azure?

Il traffico segue il modello: dispositivo del ramo -> ISP -> rete perimetrale Microsoft - > controller di dominio Microsoft (rete virtuale hub) -> rete perimetrale Microsoft -> ISP -> dispositivo del ramo

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>In questo modello, cosa serve in ogni sito? Semplicemente una connessione Internet?

Sì. Una connessione a Internet e un dispositivo fisico che supporti IPsec, preferibilmente dei [partner di WAN virtuale](../articles/virtual-wan/virtual-wan-locations-partners.md) integrati Microsoft. Facoltativamente è possibile gestire manualmente la configurazione e la connettività ad Azure dal dispositivo preferito.

### <a name="how-do-i-enable-default-route-00000-in-a-connection-vpn-expressroute-or-virtual-network"></a>Come abilitare la route predefinita (0.0.0.0/0) in una connessione (VPN, ExpressRoute o Rete virtuale):

Un hub virtuale può propagare una route predefinita appresa a una connessione di rete virtuale/VPN da sito a sito/ExpressRoute se il flag è su "Abilitato" nella connessione. Questo flag è visibile quando l'utente modifica una connessione di rete virtuale, VPN o ExpressRoute. Per impostazione predefinita, questo flag è disabilitato quando un sito o un circuito ExpressRoute sono connessi a un hub. Questa funzionalità è abilitata per impostazione predefinita quando si aggiunge una connessione di rete virtuale per connettere una rete virtuale a un hub virtuale. La route predefinita non ha origine nell'hub della rete WAN virtuale; la route predefinita viene propagata se è già stata appresa dall'hub della rete WAN virtuale a seguito della distribuzione di un firewall nell'hub o se per un altro sito connesso è abilitato il tunneling forzato.

### <a name="how-does-the-virtual-hub-in-a-virtual-wan-select-the-best-path-for-a-route-from-multiple-hubs"></a>In che modo l'hub virtuale in una rete WAN virtuale seleziona il percorso migliore per una route da più hub

Se un hub virtuale apprende la stessa route da più Hub remoti, l'ordine in cui decide è il seguente:

1. Corrispondenza del prefisso più lungo.
2. Route locali su Interhub.
3. Route statiche su BGP: si tratta del contesto della decisione presa dal router dell'hub virtuale. Tuttavia, se il Decision Maker è il gateway VPN in cui un sito annuncia le route tramite BGP o fornisce prefissi di indirizzo statici, le route statiche potrebbero essere preferite rispetto alle route BGP.
4. ExpressRoute (ER) su VPN: ER è preferibile tramite VPN quando il contesto è un Hub locale. La connettività di transito tra circuiti ExpressRoute è disponibile solo tramite Copertura globale. Pertanto negli scenari in cui il circuito ExpressRoute è connesso a un hub ed è presente un altro circuito ExpressRoute connesso a un hub diverso con connessione VPN, è possibile che la VPN sia preferita per gli scenari tra hub.
5. COME lunghezza del percorso.

### <a name="does-virtual-wan-hub-allow-connectivity-between-expressroute-circuits"></a>L'hub WAN virtuale consente la connettività tra circuiti ExpressRoute.

Il transito tra ER-to-ER è sempre tramite REACH globale. I gateway dell'hub virtuale vengono distribuiti in un controller di dominio o in aree di Azure. Quando due circuiti ExpressRoute si connettono tramite la copertura globale, non è necessario che il traffico arrivi dal router perimetrale al controller di dominio dell'hub virtuale.

### <a name="is-there-a-concept-of-weight-in-azure-virtual-wan-circuits-or-vpn-connections"></a>Esiste un concetto di peso nei circuiti WAN virtuali di Azure o nelle connessioni VPN

Quando più circuiti ExpressRoute sono connessi a un hub virtuale, il peso del routing sulla connessione fornisce un meccanismo che consente all'ExpressRoute nell'hub virtuale di preferire un circuito rispetto all'altro. Non è disponibile alcun meccanismo per impostare un peso in una connessione VPN. Azure preferisce sempre una connessione ExpressRoute su una connessione VPN all'interno di un singolo hub.

### <a name="when-two-hubs-hub-1-and-2-are-connected-and-there-is-an-expressroute-circuit-connected-as-a-bow-tie-to-both-the-hubs-what-is-the-path-for-a-vnet-connected-to-hub-1-to-reach-a-vnet-connected-in-hub-2"></a>Quando sono connessi due hub (hub 1 e 2) ed è presente un circuito ExpressRoute connesso come un papillon a entrambi gli hub, qual è il percorso di un VNet connesso all'hub 1 per raggiungere un VNet connesso nell'hub 2?

Il comportamento corrente è quello di preferire il percorso del circuito ExpressRoute tramite hub a hub per la connettività da VNet a VNet. Tuttavia, questo non è consigliato in una configurazione WAN virtuale. Il team WAN virtuale sta lavorando a una correzione per abilitare la preferenza per hub-hub sul percorso ExpressRoute. Si consiglia di usare più circuiti ExpressRoute (provider diversi) per connettersi a un hub e usare la connettività da Hub a hub fornita dalla rete WAN virtuale per i flussi di traffico tra aree.

### <a name="is-there-support-for-ipv6-in-virtual-wan"></a>È disponibile il supporto per IPv6 nella rete WAN virtuale?

IPv6 non è supportato nell'hub WAN virtuale e nei relativi gateway. Se si dispone di un VNet con supporto IPv6 e si desidera connettere VNet alla rete WAN virtuale, questo scenario non è attualmente supportato.

### <a name="what-are-the-differences-between-the-virtual-wan-types-basic-and-standard"></a>Quali sono le differenze tra i tipi di rete WAN virtuale (Basic e Standard)?

Vedere [le reti WAN virtuali Basic e standard](../articles/virtual-wan/virtual-wan-about.md#basicstandard). Per i prezzi, vedere la pagina dei [Prezzi](https://azure.microsoft.com/pricing/details/virtual-wan/).
