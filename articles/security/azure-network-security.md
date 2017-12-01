---
title: Sicurezza di rete di Azure | Microsoft Docs
description: "Informazioni sui servizi di calcolo basati sul cloud che includono un'ampia gamma di istanze e servizi di calcolo con scalabilità automatica per soddisfare le esigenze dell'applicazione o dell'organizzazione."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f0cc1716daa70bf7c860373819568774cf6f95d9
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-network-security"></a>Sicurezza di rete di Azure

La sicurezza è la priorità principale nel cloud ed è importante che l'utente trovi informazioni accurate e tempestive sulla sicurezza di Azure. Uno dei motivi migliori per usare Azure per le proprie applicazioni e i propri servizi consiste nella possibilità di sfruttare una vasta gamma di strumenti e funzionalità di Azure per la sicurezza. Questi strumenti e queste funzionalità consentono di creare soluzioni sicure sulla piattaforma Azure.

Microsoft Azure garantisce la riservatezza, l'integrità e la disponibilità dei dati dei clienti, assicurando anche al tempo stesso una rendicontazione trasparente. Per consentire una migliore comprensione della raccolta di controlli di sicurezza di rete implementati in Microsoft Azure dal punto di vista del cliente, questo articolo sulla sicurezza di rete di Azure offre una panoramica completa dei controlli di sicurezza di rete disponibili in Microsoft Azure.

Questo documento illustra l'ampia gamma di controlli di rete che è possibile configurare per migliorare la sicurezza delle soluzioni distribuite in Azure. Per informazioni sul modo in cui Microsoft protegge l'infrastruttura di rete della stessa piattaforma Azure, vedere la sezione relativa alla sicurezza di Azure nel [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/azure-security).

## <a name="azure-platform"></a>Piattaforma Azure

Azure è una piattaforma di servizi cloud pubblici che supporta un'ampia gamma di sistemi operativi, linguaggi di programmazione, framework, strumenti, database e dispositivi.  Può eseguire contenitori Linux con integrazione con Docker, compilare app con JavaScript, Python, .NET, PHP, Java e Node.js e creare back-end per dispositivi iOS, Android e Windows. I servizi cloud di Azure supportano le stesse tecnologie già considerate affidabili e usate da milioni di sviluppatori e professionisti IT.

Quando si compilano asset IT o se ne esegue la migrazione in un provider di servizi di cloud pubblico, si dipende dalla capacità di tale organizzazione di proteggere le applicazioni e i dati con i servizi e i controlli offerti per gestire la sicurezza degli asset basati sul cloud.

L'infrastruttura di Azure è stata progettata, dalla struttura fino alle applicazioni, per ospitare milioni di clienti contemporaneamente e offre alle aziende una solida base per poter soddisfare le loro esigenze di sicurezza. Azure offre anche una vasta gamma di opzioni di sicurezza configurabili, con la possibilità di controllarle per poter personalizzare il livello di sicurezza e soddisfare così i requisiti univoci di distribuzione dell'organizzazione.

## <a name="abstract"></a>Sunto

I servizi cloud pubblici Microsoft offrono servizi e infrastruttura su scala elevata, capacità di livello aziendale e molte opzioni per la connettività ibrida. È possibile scegliere di accedere a questi servizi tramite Internet o con Azure ExpressRoute, che offre connettività di rete privata. La piattaforma Microsoft Azure consente di estendere con facilità la propria infrastruttura nel cloud e di sviluppare architetture a più livelli. Inoltre, le terze parti possono abilitare capacità avanzate offrendo servizi di sicurezza e appliance virtuali.

I servizi di rete di Azure permettono di ottimizzare la flessibilità, la disponibilità, la resilienza, la sicurezza e l'integrità da progettazione. Questo white paper illustra in modo dettagliato le funzioni di rete di Azure e contiene informazioni su come usare le funzionalità di sicurezza native di Azure per proteggere gli asset di informazione.

I destinatari di questo white paper includono:

- Responsabili tecnici, amministratori di rete e sviluppatori che cercano soluzioni di sicurezza disponibili e supportate in Azure.

-   PMI o responsabili dei processi aziendali che cercano una panoramica generale dei servizi e delle tecnologie di rete di Azure attinenti a discussioni sulla sicurezza di rete nel cloud pubblico di Azure.

## <a name="azure-networking-big-picture"></a>Quadro generale della rete di Azure
Microsoft Azure include una solida infrastruttura di rete per supportare i requisiti di connettività di applicazioni e servizi. La connettività di rete è possibile tra le risorse disponibili in Azure, le risorse locali e quelle ospitate in Azure, nonché da e verso Internet e Azure.

![Quadro generale della rete di Azure](media/azure-network-security/azure-network-security-fig-1.png)

L'[infrastruttura di rete di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) consente di connettere tra loro le risorse di Azure in modo sicuro con reti virtuali. Una rete virtuale è una rappresentazione della propria rete nel cloud. È un isolamento logico della rete nel cloud di Azure dedicato alla sottoscrizione. È possibile connettere le reti virtuali alle reti locali.

Azure supporta la connettività con collegamento WAN dedicato alla rete locale e una rete virtuale di Azure con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Il collegamento tra Azure e il sito dell'utente si avvale di una connessione dedicata che non usa la rete Internet pubblica. Se l'applicazione Azure è in esecuzione in più data center, è possibile usare [Gestione traffico di Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) per indirizzare le richieste degli utenti in modo intelligente tra le istanze dell'applicazione. È anche possibile instradare il traffico ai servizi non in esecuzione in Azure, se sono accessibili da Internet.

## <a name="enterprise-view-of-azure-networking-components"></a>Visualizzazione a livello aziendale dei componenti di rete di Azure
Azure ha diversi componenti di rete rilevanti per le discussioni sulla sicurezza di rete, che qui vengono descritti con particolare attenzione ai problemi di sicurezza a essi correlati.

> [!Note]
> Non vengono descritti tutti gli aspetti della rete di Azure, ma soltanto quelli considerati fondamentali nella pianificazione e progettazione di un'infrastruttura di rete protetta per i servizi e le applicazioni distribuiti in Azure.

Questo documento illustra le seguenti funzionalità aziendali della rete di Azure:

-   Connettività di rete di base

-   Connettività ibrida

-   Security Controls

-   Convalida della rete

### <a name="basic-network-connectivity"></a>Connettività di rete di base

Il servizio [Rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) consente di connettere tra loro le risorse di Azure in modo sicuro con reti virtuali. Una rete virtuale è una rappresentazione della propria rete nel cloud. È un isolamento logico dell'infrastruttura di rete di Azure dedicato alla sottoscrizione. È anche possibile connettere le reti virtuali tra loro e alle reti locali tramite VPN da sito a sito e [collegamenti WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) dedicati.

![Connettività di rete di base](media/azure-network-security/azure-network-security-fig-2.png)

Le macchine virtuali vengono usate per ospitare i server in Azure. Occorre però approfondire il modo in cui tali macchine virtuali si connettono a una rete. Ciò avviene tramite una [rete virtuale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Le reti virtuali di Azure sono simili alle reti virtuali usate in locale con soluzioni di piattaforma di virtualizzazione proprie, come Microsoft Hyper-V o VMware.

#### <a name="intra-vnet-connectivity"></a>Connettività tra reti virtuali

È possibile connettere tra loro le reti virtuali in modo che le risorse connesse a una di esse possano comunicare tra loro attraverso le reti virtuali. Per connettere tra loro le reti virtuali è possibile usare una o entrambe le opzioni seguenti.

- **Peering:** consente alle risorse connesse a diverse reti virtuali di Azure nella stessa località di Azure di comunicare tra loro. La larghezza di banda e la latenza della rete virtuale sono uguali a quelle che si riscontrerebbero se le risorse fossero connesse alla stessa rete virtuale. Per altre informazioni sul peering, vedere [Peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Peering](media/azure-network-security/azure-network-security-fig-3.png)

- **Connessione da rete virtuale a rete virtuale:** abilita le risorse connesse a diverse reti virtuali di Azure nella stessa località o in località diverse. A differenza del peering, la larghezza di banda tra le reti virtuali è limitata perché il traffico deve passare attraverso un gateway VPN di Azure.

![Connessione da rete virtuale a rete virtuale](media/azure-network-security/azure-network-security-fig-4.png)


Per altre informazioni sulla connessione di reti virtuali con una connessione da rete virtuale a rete virtuale, vedere l'articolo [Configurare una connessione da rete virtuale a rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Funzionalità della rete virtuale di Azure:

Una rete virtuale di Azure fornisce le macchine virtuali per la connessione alla rete, in modo che possano connettersi ad altre risorse di rete in modo sicuro. Tuttavia, la connettività di base è solo l'inizio. Le seguenti funzionalità del servizio di rete virtuale di Azure espongono le caratteristiche di sicurezza della rete virtuale di Azure:

-   Isolamento

-   Connettività Internet

-   Connettività delle risorse di Azure

-   Connettività di rete virtuale

-   Connettività locale

-   Filtro del traffico

-   Routing.

**Isolamento**

Le reti virtuali sono [isolate](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) le une dalle altre. È possibile creare reti virtuali separate per sviluppo, test e produzione che usano gli stessi blocchi di indirizzi [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). È altrimenti possibile creare più reti virtuali che usano blocchi di indirizzi CIDR diversi e connettere tra loro le reti. Una rete virtuale può essere segmentata in più subnet.

Azure offre la risoluzione dei nomi interna per le VM e le istanze del ruolo [Servizi cloud](https://azure.microsoft.com/services/cloud-services/) connesse a una rete virtuale. Facoltativamente, è possibile configurare una rete virtuale in modo da usare server DNS personalizzati anziché la risoluzione dei nomi interna di Azure.

È possibile implementare più reti virtuali in ogni [sottoscrizione](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) e [area](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure. Ogni rete virtuale è isolata dalle altre. Per ogni rete virtuale è possibile:

-   Specificare uno spazio indirizzi IP privato personalizzato con indirizzi pubblici e privati (RFC 1918). Azure assegna alle risorse connesse alla rete virtuale un indirizzo IP privato dello spazio indirizzi specificato.

-   Segmentare la rete virtuale in una o più subnet e allocare una parte dello spazio indirizzi della rete virtuale a ogni subnet.

-   Usare la risoluzione dei nomi fornita da Azure oppure specificare un server DNS personalizzato che verrà usato dalle risorse connesse a una rete virtuale. Per altre informazioni sulla risoluzione dei nomi nelle reti virtuali, vedere [Risoluzione dei nomi per macchine virtuali e servizi cloud](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Connettività Internet**

Per impostazione predefinita, tutte le [macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/) e le istanze del ruolo Servizi cloud connesse a una rete virtuale hanno accesso a Internet. È anche possibile abilitare l'accesso in ingresso a risorse specifiche, se necessario. Per impostazione predefinita, le macchine virtuali e le istanze del ruolo Servizi cloud connesse a una rete virtuale hanno accesso a Internet. È anche possibile abilitare l'accesso in ingresso a risorse specifiche, se necessario.

Per impostazione predefinita, tutte le risorse connesse a una rete virtuale hanno la connettività in uscita a Internet. L'indirizzo IP privato della risorsa viene convertito (con Source Network Address Translation, SNAT) in un indirizzo IP pubblico dall'infrastruttura di Azure. È possibile modificare la connettività predefinita implementando il routing personalizzato e applicando filtri al traffico. Per altre informazioni sulla connettività Internet in uscita, vedere [Informazioni sulle connessioni in uscita in Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Per la comunicazione in ingresso verso le risorse di Azure da Internet o la comunicazione in uscita verso Internet senza SNAT, è necessario che a una risorsa sia assegnato un indirizzo IP pubblico. Per altre informazioni sugli indirizzi IP pubblici, vedere [Indirizzi IP pubblici](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Connettività delle risorse di Azure**

Le [risorse di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) come Servizi cloud e VM possono essere connesse alla stessa rete virtuale. Le risorse possono essere connesse tra loro usando indirizzi IP privati, anche se si trovano in subnet diverse. Azure offre il routing predefinito tra subnet, reti virtuali e reti locali, quindi non è necessario configurare e gestire le route.

È possibile connettere a una rete virtuale diverse risorse di Azure, ad esempio macchine virtuali (VM), Servizi cloud, ambienti del servizio app e set di scalabilità di macchine virtuali. Le VM si connettono a una subnet di una rete virtuale tramite un'interfaccia di rete. Per altre informazioni sulle interfacce di rete, vedere [Interfacce di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Connettività di rete virtuale**

Le [reti virtuali](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) possono essere connesse tra loro, in modo che le risorse connesse a qualsiasi rete virtuale possano comunicare con qualsiasi risorsa in un'altra rete virtuale.

È possibile connettere tra loro le reti virtuali in modo che le risorse connesse a una di esse possano comunicare tra loro attraverso le reti virtuali. Per connettere tra loro le reti virtuali è possibile usare una o entrambe le opzioni seguenti.

- **Peering:** consente alle risorse connesse a diverse reti virtuali di Azure nella stessa località di Azure di comunicare tra loro. La larghezza di banda e la latenza tra le reti virtuali sono uguali a quelle che si riscontrerebbero se le risorse fossero connesse alla stessa rete virtuale. Per altre informazioni sul peering, vedere [Peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Connessione da rete virtuale a rete virtuale:** abilita le risorse connesse a diverse reti virtuali di Azure nella stessa località o in località diverse. A differenza del peering, la larghezza di banda tra le reti virtuali è limitata perché il traffico deve passare attraverso un gateway VPN di Azure. Per altre informazioni sulla connessione di reti virtuali con una connessione di rete virtuale, vedere [Configurare una connessione da rete virtuale a rete virtuale](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Connettività locale**

È possibile connettere le reti virtuali a reti [locali](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) tramite connessioni di rete private tra la rete e Azure o tramite una connessione VPN da sito a sito su Internet.

È possibile connettere la rete locale a una rete virtuale usando qualsiasi combinazione delle opzioni seguenti.

- **Rete privata virtuale (VPN) da punto a sito:** viene stabilita tra un singolo PC connesso alla rete e la rete virtuale. Questo tipo di connessione è ideale se si sta appena iniziando a usare Azure o per gli sviluppatori, perché richiede modifiche minime o nessuna modifica alla rete esistente. La connessione usa il protocollo SSTP per fornire la comunicazione crittografata su Internet tra il PC e la rete virtuale. La latenza per una VPN da punto a sito è imprevedibile, dal momento che il traffico attraversa Internet.

- **VPN da sito a sito:** viene stabilita una connessione tra il dispositivo VPN e un gateway VPN di Azure. Questo tipo di connessione consente a qualsiasi risorsa locale autorizzata dall'utente di accedere a una rete virtuale. La connessione è una VPN IPSec/IKE che fornisce la comunicazione crittografata su Internet tra il dispositivo locale e il gateway VPN di Azure. La latenza per una connessione da sito a sito è imprevedibile, perché il traffico attraversa Internet.

- **Azure ExpressRoute:** viene stabilita una connessione tra la rete e Azure tramite un partner ExpressRoute. La connessione è privata. Il traffico non attraversa Internet. La latenza per una connessione ExpressRoute è prevedibile, perché il traffico non attraversa Internet. Per altre informazioni su tutte le opzioni di connessione riportate sopra, vedere [Diagrammi delle topologie di connessione](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtro del traffico**

Il [traffico di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) delle VM e delle istanze del ruolo Servizi cloud può essere filtrato in ingresso e in uscita per indirizzo IP e porta di origine, indirizzo IP e porta di destinazione e protocollo.

È possibile filtrare il traffico di rete tra subnet usando una o entrambe le opzioni seguenti.

- **Gruppi di sicurezza di rete (NSG):** ogni gruppo di sicurezza di rete contiene più regole di sicurezza in ingresso e in uscita che consentono di filtrare il traffico per protocollo, porta e indirizzo IP di origine e di destinazione. È possibile applicare un gruppo di sicurezza di rete a qualsiasi interfaccia di rete di una VM, nonché alla subnet a cui è connessa un'interfaccia di rete o un'altra risorsa di Azure. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Appliance di rete virtuale:** un'appliance di rete virtuale è una VM che esegue software con una funzione di rete, ad esempio un firewall. Per un elenco delle appliance di rete virtuale disponibili, vedere Azure Marketplace. Sono anche disponibili appliance virtuali di rete che eseguono l'ottimizzazione WAN e altre funzioni per il traffico di rete. Le appliance virtuali di rete vengono in genere usate con route BGP o definite dall'utente. È anche possibile usare un'appliance virtuale di rete per filtrare il traffico tra reti virtuali.

**Routing**

Facoltativamente, è possibile sostituire il routing predefinito di Azure configurando route personalizzate o usando route BGP attraverso un gateway di rete.

Per impostazione predefinita, Azure crea tabelle di route che consentono alle risorse connesse a qualsiasi subnet di qualsiasi rete virtuale di comunicare tra loro. Per sostituire le route predefinite create da Azure, è possibile implementare una o entrambe le opzioni seguenti.

- **Route definite dall'utente:** è possibile creare tabelle di route personalizzate con route che controllano l'instradamento del traffico per ogni subnet. Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Route BGP:** se si connette la rete virtuale alla rete locale con un gateway VPN di Azure o una connessione ExpressRoute, è possibile propagare le route BGP alle reti virtuali.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Connettività Internet ibrida: connettersi a una rete locale
È possibile connettere la rete locale a una rete virtuale usando qualsiasi combinazione delle opzioni seguenti.

-   Connettività Internet

-   VPN da punto a sito

-   VPN da sito a sito

-   ExpressRoute

#### <a name="internet-connectivity"></a>Connettività Internet

Come suggerisce il nome, la connettività Internet rende i carichi di lavoro accessibili da Internet. Ciò avviene esponendo diversi endpoint pubblici a carichi di lavoro che si trovano all'interno della rete virtuale. Tali carichi di lavoro possono essere esposti tramite un [servizio di bilanciamento del carico Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) o semplicemente assegnando un indirizzo IP pubblico alla macchina virtuale. In questo modo è possibile raggiungere la macchina virtuale da Internet, passando attraverso un firewall host, [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) e [route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

In questo scenario è possibile esporre a Internet un'applicazione che deve essere pubblica e connettersi all'applicazione da un punto qualsiasi o da percorsi specifici, a seconda della configurazione dei carichi di lavoro.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>VPN da punto a sito o VPN da sito a sito
Questi due tipi di VPN rientrano nella stessa categoria. In entrambi i casi la rete virtuale deve avere un gateway VPN a cui sia possibile connettersi tramite un client VPN per la workstation, come parte della [configurazione da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) oppure configurando il [dispositivo VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) locale in modo che possa terminare una connessione VPN da sito a sito. In questo modo i dispositivi locali possono connettersi alle risorse all'interno della rete virtuale.

Una configurazione da punto a sito (P2S) permette di creare una connessione protetta da un singolo computer client a una rete virtuale. P2S è una connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol).

![VPN da punto a sito](media/azure-network-security/azure-network-security-fig-5.png)

Una soluzione da punto a sito è utile quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o da un centro congressi, oppure quando solo pochi client devono connettersi a una rete virtuale.

Le connessioni P2S non richiedono un dispositivo VPN o un indirizzo IP pubblico. La connessione VPN viene avviata dal computer client. Non è quindi consigliabile usare una VPN da punto a sito per connettersi ad Azure quando è necessario stabilire una connessione permanente tra più dispositivi e computer locali e la rete di Azure.

![VPN da sito a sito](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Per altre informazioni al riguardo, vedere le [domande frequenti sulla connettività da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Una connessione gateway VPN da sito a sito viene usata per connettere la rete locale a una rete virtuale di Azure tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2).

Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Questa connessione viene eseguita via Internet e consente di inviare le informazioni attraverso un "tunnel" all'interno di un collegamento crittografato tra la rete e Azure. La rete VPN da sito a sito è una tecnologia sicura e collaudata, che viene distribuita da aziende di ogni dimensione ormai da decenni. La crittografia del tunnel viene eseguita usando la [modalità tunnel IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Nonostante la tecnologia VPN da sito a sito sia attendibile, affidabile e consolidata, il traffico all'interno del tunnel attraversa comunque Internet. Per di più, la larghezza di banda è relativamente vincolata a un massimo di circa 200 Mbps.

Se si richiede un livello di sicurezza o prestazioni eccezionale per le connessioni cross-premise, è consigliabile usare Azure ExpressRoute per la connettività cross-premise. ExpressRoute è un collegamento WAN dedicato tra il percorso locale o un provider di hosting di Exchange. Dal momento che si tratta di una connessione di telecomunicazioni, i dati non vengono trasmessi via Internet e quindi non sono esposti a potenziali rischi inerenti alle comunicazioni Internet.

> [!Note]
> Per altre informazioni al riguardo, vedere [Informazioni sul gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Collegamento WAN dedicato
Microsoft Azure ExpressRoute consente di estendere le reti locali in Azure tramite una connessione privata dedicata fornita da un provider di connettività.

Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. In questo modo possono offrire un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

![ Collegamento WAN dedicato](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Per informazioni sulla procedura di connessione della rete a Microsoft tramite ExpressRoute, vedere [Modelli di connettività di ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) e [Panoramica di ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Come per le opzioni VPN da sito a sito, anche ExpressRoute consente di connettersi a risorse che non si trovano necessariamente in una sola rete virtuale. A seconda dello SKU, di fatto è possibile connettersi a 10 reti virtuali. Con un [componente aggiuntivo Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), è possibile stabilire fino a 100 connessioni a reti virtuali, a seconda della larghezza di banda. Per altre informazioni sull'aspetto di questi tipi di connessione, vedere [Diagrammi delle topologie di connessione](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Controlli di sicurezza
Una rete virtuale di Azure offre una rete logica e sicura, isolata dalle altre reti virtuali, e supporta molti controlli di sicurezza usati nelle reti locali. I clienti possono creare una struttura personale con le subnet, usando un proprio intervallo di indirizzi IP privati e configurando tabelle di routing, gruppi di sicurezza di rete, elenchi di controllo di accesso (ACL), gateway e appliance virtuali per eseguire i carichi di lavoro nel cloud.

Di seguito sono riportati i controlli di sicurezza che è possibile usare nelle reti virtuali di Azure:

-   Controlli di accesso alla rete

-   Route definite dall'utente

-   Appliance di sicurezza di rete

-   gateway applicazione

-   Web application firewall di Azure

-   Controllo di disponibilità di rete

#### <a name="network-access-controls"></a>Controlli di accesso alla rete
Se da un lato la rete virtuale di Azure è il fulcro del modello di rete di Azure e offre isolamento e protezione, i [gruppi di sicurezza di rete](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) rappresentano lo strumento principale per applicare e controllare le regole del traffico di rete a livello di rete.

![ Controlli di accesso alla rete](media/azure-network-security/azure-network-security-fig-8.png)


È possibile controllare l'accesso autorizzando o negando la comunicazione tra i carichi di lavoro all'interno di una rete virtuale, da sistemi nelle reti dei clienti tramite la connettività cross-premise oppure la comunicazione Internet diretta.

Nel diagramma, sia le reti virtuali che i gruppi di sicurezza di rete si trovano a un livello specifico dello stack di sicurezza di Azure, in cui è possibile usare gruppi di sicurezza di rete, route definite dall'utente e appliance virtuali di rete per creare limiti di sicurezza e proteggere le distribuzioni delle applicazioni nella rete protetta.

I gruppi di sicurezza di rete usano 5 tuple per valutare il traffico (usate nelle regole configurate per il gruppo di sicurezza di rete):

-   [Indirizzo IP di origine e di destinazione](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Porta di origine e di destinazione](https://technet.microsoft.com/library/dd197515)

-   Protocollo: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) o [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Ciò significa che è possibile controllare l'accesso tra una macchina virtuale singola e un gruppo di macchine virtuali o una macchina virtuale singola e un'altra macchina virtuale singola o tra intere subnet. Anche in questo caso, occorre tenere presente che si tratta di un semplice filtro dei pacchetti con stato, non di un'ispezione completa dei pacchetti. In un gruppo di sicurezza di rete non c'è alcuna convalida del protocollo, IDS a livello di rete o funzionalità IPS.

Il gruppo di sicurezza di rete include alcune regole predefinite che è opportuno conoscere. Si tratta di:

-   **Consenti tutto il traffico all'interno di una rete virtuale specifica:** tutte le macchine virtuali nella stessa rete virtuale di Azure possono comunicare tra loro.

-   **Consenti il traffico in ingresso per il bilanciamento del carico di Azure:**  questa regola consente il traffico da qualsiasi indirizzo di origine a qualsiasi indirizzo di destinazione per il servizio di bilanciamento del carico di Azure.

-   **Nega tutto il traffico in ingresso:**  questa regola blocca tutto il traffico proveniente da Internet che è stato consentito in modo esplicito.

-   **Consenti tutto il traffico in uscita verso Internet:** questa regola consente alle macchine virtuali di avviare connessioni a Internet. Se si preferisce che tali connessioni non vengano avviate, è necessario creare una regola per bloccare le connessioni o applicare il tunneling forzato.

#### <a name="system-routes-and-user-defined-routes"></a>Route di sistema e route definite dall'utente

Quando si aggiungono macchine virtuali a una rete virtuale in Azure, si nota che le macchine virtuali possono automaticamente comunicare tra loro in rete. Non è necessario specificare un gateway, anche se le macchine virtuali si trovano in subnet diverse.

Lo stesso vale per la comunicazione tra macchine virtuali e rete Internet pubblica e anche rete locale se è presente una connessione ibrida tra Azure e il proprio Data Center.

![Le route di sistema](media/azure-network-security/azure-network-security-fig-9.png)

Questo flusso di comunicazione è possibile perché Azure utilizza una serie di route di sistema per definire il flusso di traffico IP. I route di sistema controllano il flusso delle comunicazioni negli scenari seguenti:

-   All'interno della stessa subnet.

-   Da una subnet a altra all'interno di una rete virtuale.

-   Da macchine virtuali a Internet.

-   Da una rete virtuale a un'altra rete virtuale tramite un gateway VPN.

-   Da una rete virtuale a un'altra tramite il peering di rete virtuale ([concatenamento dei servizi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Da una rete virtuale alla rete locale tramite un gateway VPN.

Molte aziende hanno rigidi requisiti di sicurezza e conformità che richiedono l'ispezione locale di tutti i pacchetti di rete per l'applicazione di criteri specifici. Azure offre un meccanismo denominato [tunneling forzato](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) che instrada il traffico dalle macchine virtuali alla rete locale, creando una route personalizzata o per mezzo di annunci [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) tramite ExpressRoute o VPN.

Il tunneling forzato in Azure viene configurato tramite route di rete virtuale definite dall'utente. Il reindirizzamento del traffico a un sito locale viene espresso come route predefinita al gateway VPN di Azure.

Nella sezione seguente viene elencata la limitazione attuale della tabella di routing e delle route per una rete virtuale di Azure:

-   Ciascuna subnet della rete virtuale dispone di una tabella di routing di sistema integrata. La tabella di routing di sistema include i tre gruppi di route seguenti:

 -  **Route della rete virtuale locale:** direttamente alla destinazione di macchine virtuali nella stessa rete virtuale

 - **Route locale:** al gateway VPN di Azure

 -  **Route predefinita:** direttamente a Internet. I pacchetti destinati agli indirizzi IP privati che non rientrano nelle due route precedenti vengono eliminati.

-   Con il rilascio di route definite dall'utente, è possibile creare una tabella di routing per aggiungere una route predefinita, quindi associare la tabella di routing alla subnet della rete virtuale per abilitare il tunneling forzato su tali subnet.

-   È necessario impostare un "sito predefinito" tra i siti locali cross-premise connessi alla rete virtuale.

-   Il tunneling forzato deve essere associato a una rete virtuale che disponga di un gateway VPN (non un gateway statico).

- Il tunneling forzato ExpressRoute non viene configurato mediante questo meccanismo, ma è abilitato annunciando una route predefinita tramite le sessioni di peering BGP ExpressRoute.

> [!Note]
> Per altre informazioni, vedere la [documentazione di ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

#### <a name="network-security-appliances"></a>Appliance di sicurezza di rete
Anche se i gruppi di sicurezza di rete e le route definite dall'utente possono offrire un certo grado di sicurezza di rete ai livelli di rete e di trasporto del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), in alcune situazioni è opportuno o necessario abilitare la sicurezza a livelli più alti dello stack di rete. In tali situazioni, è consigliabile distribuire i dispositivi di sicurezza di rete virtuale forniti dai partner di Azure.

![Appliance di sicurezza di rete](./media/azure-network-security/azure-network-security-fig-10.png)

Le appliance di sicurezza di rete di Azure migliorano la sicurezza della rete virtuale e le funzioni di rete. In [Azure Marketplace](https://azuremarketplace.microsoft.com) sono disponibili appliance di diversi fornitori. È possibile distribuire le appliance di sicurezza virtuale allo scopo di garantire quanto segue:

-   Firewall a disponibilità elevata

-   Prevenzione delle intrusioni

-   Rilevamento delle intrusioni

-   Web application firewall

-   Ottimizzazione WAN

-   Routing.

-   Bilanciamento del carico.

-   VPN

-   Gestione dei certificati

-   Active Directory

-   Autenticazione a più fattori

#### <a name="application-gateway"></a>gateway applicazione

Il [gateway applicazione di Microsoft Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) è un'appliance virtuale dedicata che offre un servizio di controller per la distribuzione di applicazioni.

 ![gateway applicazione](./media/azure-network-security/azure-network-security-fig-11.png)

Il gateway applicazione consente di ottimizzare le prestazioni e la disponibilità delle Web farm eseguendo l'offload al gateway applicazione della terminazione SSL con utilizzo elevato di CPU (offload SSL). Offre inoltre funzionalità di routing di livello 7, tra cui:

-   Distribuzione round-robin del traffico in ingresso

-   Affinità di sessione basata su cookie

-   Routing basato su percorsi URL

-   Possibilità di ospitare più siti Web dietro un gateway applicazione singolo


Nel gateway applicazione è incluso anche un [Web application firewall (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview). Questo offre alle applicazioni Web la protezione da exploit e vulnerabilità Web comuni. Il gateway applicazione può essere configurato come gateway con connessione Internet, come gateway solo interno o come una combinazione di queste due opzioni.

È possibile eseguire il Web application firewall del gateway applicazione in modalità di rilevamento o di prevenzione. Gli amministratori lo eseguono comunemente in modalità di rilevamento per tenere traccia di eventuali schemi dannosi nel traffico. Quando vengono rilevati exploit potenziali, il passaggio alla modalità di prevenzione blocca il traffico in ingresso sospetto.

 ![gateway applicazione](./media/azure-network-security/azure-network-security-fig-12.png)

Il Web application firewall del gateway applicazione permette anche di monitorare le applicazioni Web contro gli attacchi, grazie a un log WAF in tempo reale integrato con [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) e con il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/) per tenere traccia degli avvisi WAF e monitorare facilmente le tendenze.

Il log in formato JSON passa direttamente all'account di archiviazione del cliente. L'utente ha il controllo completo sui log e può applicare i propri criteri di conservazione.

È anche possibile inserire i log nel proprio sistema di analisi usando l'[integrazione dei log di Azure](https://aka.ms/AzLog). I log WAF sono anche integrati con [Operations Management Suite (OMS)](https://www.microsoft.com/cloud-platform/operations-management-suite). Questo permette di usare Log Analytics di OMS per eseguire query sofisticate con granularità fine.

#### <a name="azure-web-application-firewall-waf"></a>Web application firewall (WAF) di Azure

Le applicazioni Web sono sempre più spesso oggetto di attacchi che sfruttano vulnerabilità comuni note, come gli attacchi SQL injection, cross site scripting e altri attacchi indicati tra le [10 vulnerabilità Web OWASP più diffuse](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Impedire questo tipo di exploit nell'applicazione richiede una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a più livelli della topologia dell'applicazione.

 ![Web application firewall (WAF) di Azure](./media/azure-network-security/azure-network-security-fig-13.png)

Un [Web application firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) centralizzato permette di proteggersi dagli attacchi Web e semplifica la gestione della sicurezza, senza richiedere alcuna modifica alle applicazioni.

Una soluzione WAF è anche in grado di reagire più velocemente a una minaccia alla sicurezza tramite l'applicazione di patch su una vulnerabilità nota in una posizione centrale, anziché proteggere ogni singola applicazione Web. È possibile convertire facilmente i gateway applicazione esistenti in un gateway applicazione con Web application firewall.

#### <a name="network-availability-controls"></a>Controlli di disponibilità di rete

Sono disponibili diverse opzioni per distribuire il traffico di rete tramite Microsoft Azure. Queste opzioni funzionano in modo diverso, vantano un set di funzionalità differenti e supportano diversi scenari. Possono essere utilizzate singolarmente o in combinazione.

Di seguito sono indicati i controlli di disponibilità di rete:

-   Azure Load Balancer

-   gateway applicazione

-   Gestione traffico

**Azure Load Balancer**

Garantisce livelli elevati di disponibilità e prestazioni di rete per le applicazioni. Si tratta di un servizio di bilanciamento del carico di livello 4 (TCP, UDP) che distribuisce il traffico in ingresso tra istanze integre di servizi definiti in un set con carico bilanciato.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Azure Load Balancer può essere configurato per:

-   Bilanciare il carico del traffico Internet in ingresso nelle macchine virtuali. Questa configurazione è nota come [bilanciamento del carico Internet tra più macchine virtuali o servizi](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Bilanciare il carico del traffico tra macchine virtuali in una rete virtuale, tra macchine virtuali nei servizi cloud o tra computer locali e macchine virtuali in una rete virtuale cross-premise. Questa configurazione è nota come [bilanciamento del carico interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Inoltrare il traffico esterno a una specifica macchina virtuale.

Tutte le risorse nel cloud richiedono un indirizzo IP pubblico per poter essere raggiungibili da Internet. L'infrastruttura cloud di Azure usa per le proprie risorse indirizzi IP non instradabili. Azure usa Network Address Translation (NAT) con indirizzi IP pubblici per comunicare con Internet.

 **Gateway applicazione**

 Il gateway applicazione funziona a livello di applicazione (livello 7 nello stack di riferimento di rete OSI). Agisce come un servizio di proxy inverso, terminando la connessione di client e inoltrando richieste a endpoint di back-end.

 **Gestione traffico**

Gestione traffico di Microsoft Azure consente di controllare la distribuzione del traffico utente per gli endpoint di servizio in diversi data center. Gli endpoint di servizio supportati da Gestione traffico includono servizi cloud, app Web e macchine virtuali di Azure. È anche possibile usare Gestione traffico con endpoint esterni, non di Azure.

Gestione traffico usa il sistema DNS (Domain Name System) per indirizzare le richieste del client all'endpoint più appropriato in base a un [metodo di routing del traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) e all'integrità degli endpoint. Gestione traffico offre diversi metodi di routing del traffico per soddisfare le diverse esigenze delle applicazioni. Offre inoltre [monitoraggio](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) dell'integrità degli endpoint e failover automatico. Gestione traffico è resiliente agli errori, incluso l'errore di un'intera area di Azure.

Gestione traffico di Azure consente di controllare in che modo il traffico viene distribuito tra gli endpoint dell'applicazione. Un endpoint è un servizio con connessione Internet ospitato all'interno o all'esterno di Azure.

Gestione traffico offre due vantaggi principali:

-   Distribuzione del traffico in base a uno dei diversi [metodi di routing del traffico](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Monitoraggio continuo dell'integrità degli endpoint](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) e failover automatico quando si verificano errori sugli endpoint.

Quando un client tenta di connettersi a un servizio, è necessario prima risolvere il nome DNS del servizio a un indirizzo IP. Il client si connette quindi a questo indirizzo IP per accedere al servizio. Gestione traffico usa DNS per indirizzare i client a specifici endpoint di servizio in base alle regole del metodo di routing del traffico. I client si connettono direttamente all'endpoint selezionato. Gestione traffico non è un proxy o un gateway. Gestione traffico non visualizza il traffico tra il client e il servizio.

### <a name="azure-network-validation"></a>Convalida della rete di Azure

La convalida della rete di Azure permette di accertare il funzionamento della rete di Azure in base alla configurazione e può essere eseguita usando i servizi e le funzionalità disponibili per il monitoraggio della rete. Con Azure Network Watcher è possibile accedere a una vasta gamma di funzionalità di registrazione e diagnostica, con informazioni che permettono di conoscere le prestazioni e l'integrità della rete. Tali funzionalità sono accessibili tramite il portale, PowerShell, l'interfaccia della riga di comando, API Rest e SDK.

La sicurezza operativa di Azure include i servizi, i controlli e le funzionalità offerti ai clienti per proteggere i dati, le applicazioni e gli altri asset di Microsoft Azure. La sicurezza operativa di Azure è basata su un framework che incorpora le conoscenze acquisite tramite varie funzionalità esclusive di Microsoft, tra cui Microsoft Security Development Lifecycle (SDL), il programma Microsoft Security Response Center e una profonda consapevolezza del panorama delle minacce per la sicurezza informatica.

-   [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

-   [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Gestione risorse di Azure

#### <a name="azure-resource-manager"></a>Azure Resource Manager

Le persone e i processi che usano Microsoft Azure sono forse la funzionalità di sicurezza più importante della piattaforma. Questa sezione illustra le funzionalità dell'infrastruttura del data center globale di Microsoft che consentono di migliorare e gestire la sicurezza, la continuità e la privacy.

L'infrastruttura per l'applicazione è in genere costituita da vari componenti, ad esempio una macchina virtuale, un account di archiviazione e una rete virtuale oppure un'app Web, un database, un server di database e servizi di terze parti. Questi componenti non appaiono come entità separate, ma come parti correlate e interdipendenti di una singola entità e devono essere distribuite, gestite e monitorate come gruppo. Gestione risorse di Azure consente di usare le risorse incluse nella soluzione come un gruppo.

È possibile distribuire, aggiornare o eliminare tutte le risorse della soluzione con un'unica operazione coordinata. Per la distribuzione viene usato un modello; questo modello può essere usato per diversi ambienti, ad esempio di testing, staging e produzione. Gestione risorse offre funzionalità di sicurezza, controllo e categorizzazione che semplificano la gestione delle risorse dopo la distribuzione.

**Vantaggi offerti dall'uso di Resource Manager**

Gestione risorse offre numerosi vantaggi:

-   È possibile distribuire, gestire e monitorare tutte le risorse per la soluzione come un gruppo, anziché gestire singolarmente tali risorse.

-   È possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita dello sviluppo garantendo al contempo che le risorse vengano distribuite in uno stato coerente.

-   È possibile gestire l'infrastruttura con modelli dichiarativi, piuttosto che con script.

-   È possibile definire le dipendenze tra le risorse in modo che vengano distribuite nell'ordine corretto.

-   è possibile applicare il controllo di accesso a tutti i servizi nel gruppo di risorse perché il controllo di accesso basato sui ruoli (RBAC) è integrato in modo nativo nella piattaforma di gestione.

-   È possibile applicare tag alle risorse per organizzare in modo logico tutte le risorse nella sottoscrizione.

-   È possibile ottenere informazioni dettagliate sulla fatturazione per l'organizzazione visualizzando i costi di un gruppo di risorse che condividono un tag.

> [!Note]
> Gestione risorse offre un nuovo modo per distribuire e gestire le soluzioni. Per informazioni sulle modifiche introdotte rispetto al modello di distribuzione precedente, vedere l'articolo relativo alle [informazioni sulla distribuzione di Gestione risorse e sulla distribuzione classica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

## <a name="azure-network-logging-and-monitoring"></a>Registrazione e monitoraggio della rete di Azure

Azure offre diversi strumenti che permettono di monitorare, prevenire, rilevare e rispondere agli eventi di sicurezza di rete. Alcuni degli strumenti più potenti disponibili includono:

-   Network Watcher

-   Monitoraggio a livello di risorsa di rete

-   Log Analytics

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): tra le funzionalità del servizio Network Watcher è incluso il monitoraggio basato su scenari. Il servizio include l'acquisizione pacchetti, l'hop successivo, la verifica del flusso IP, la visualizzazione dei gruppi di sicurezza e i registri dei flussi dei gruppi di sicurezza di rete. A differenza del monitoraggio a livello di singole risorse di rete, il monitoraggio a livello di scenario consente una visualizzazione completa delle risorse di rete.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Network Watcher è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete da, verso e all'interno di Azure. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure.

Di seguito sono elencate le funzionalità attualmente disponibili in Network Watcher.

#### <a name="topology"></a>Topologia

La [topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) restituisce un grafico delle risorse di rete in una rete virtuale. Il grafico illustra l'interconnessione tra le risorse per rappresentare in modo completo la connettività di rete. Nel portale la topologia restituisce gli oggetti risorsa per ogni singola rete virtuale. Le relazioni sono rappresentate dalle linee tra le risorse all'esterno dell'area di Network Watcher, anche se non vengono visualizzate nel gruppo di risorse. Le risorse restituite nella vista del portale sono un sottoinsieme di componenti di rete rappresentati in un grafico. Per visualizzare l'elenco completo delle risorse di rete è possibile usare [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) o [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Quando le risorse vengono restituite, la connessione tra di esse viene modellata in due relazioni.

- **Contenimento**: la rete virtuale contiene una subnet che contiene una scheda di interfaccia di rete.

- **Associazione**: una scheda di interfaccia di rete è associata a una macchina virtuale.

#### <a name="variable-packet-capture"></a>Acquisizione pacchetti variabile

L'[acquisizione pacchetti variabile](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) di Network Watcher consente di creare sessioni di acquisizione di pacchetti per registrare il traffico da e verso una macchina virtuale. Il servizio di acquisizione di pacchetti consente di individuare eventuali anomalie di rete in modo proattivo e reattivo. Altri usi comprendono la raccolta di statistiche di rete, informazioni sulle intrusioni nella rete, debug delle comunicazioni client-server e molto altro ancora.

L'acquisizione di pacchetti è un'estensione macchina virtuale che viene avviata da remoto tramite Network Watcher. Questa funzionalità evita di dover eseguire manualmente questa operazione sulla macchina virtuale desiderata, consentendo un notevole risparmio di tempo. L'acquisizione di pacchetti può essere attivata tramite il portale, PowerShell, l'interfaccia della riga di comando o l'API REST. Un esempio di modalità di attivazione è rappresentata dagli avvisi della macchina virtuale.

#### <a name="ip-flow-verify"></a>Verifica del flusso IP

La [verifica del flusso IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) controlla se un pacchetto viene accettato o rifiutato in ingresso o in uscita da una macchina virtuale in base a informazioni a 5 tuple. Tali informazioni sono costituite da direzione, protocollo, indirizzo IP locale, indirizzo IP remoto, porta locale e porta remota. Se il pacchetto viene rifiutato da un gruppo di sicurezza, viene restituito il nome della regola che ha rifiutato il pacchetto. Anche se è possibile scegliere qualsiasi indirizzo IP di origine o di destinazione, questa funzionalità permette agli amministratori di diagnosticare rapidamente problemi di connettività in ingresso o in uscita da Internet e in ingresso o in uscita dall'ambiente locale.

La verifica del flusso IP esamina l'interfaccia di rete di una macchina virtuale. Il flusso di traffico in ingresso o in uscita dall'interfaccia di rete viene quindi verificato in base alle impostazioni configurate. Questa funzionalità permette di confermare se una regola di un gruppo di sicurezza di rete sta bloccando il traffico in ingresso o in uscita da una macchina virtuale.

#### <a name="next-hop"></a>Hop successivo

Determina l'[hop successivo](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) per i pacchetti indirizzati nell'infrastruttura di rete di Azure, permettendo così di diagnosticare eventuali route definite dall'utente non configurate in modo corretto. Il traffico proveniente da una macchina virtuale viene inviato a una destinazione in base alle route valide associate alla scheda di interfaccia di rete. L'hop successivo ottiene il tipo di hop successivo e l'indirizzo IP di un pacchetto da una macchina virtuale e una scheda di interfaccia di rete specifiche. Ciò consente di determinare se il pacchetto viene indirizzato a destinazione o se il traffico viene inviato a un black hole.

L'hop successivo restituisce anche la tabella di route associata all'hop successivo. Quando si eseguono query su un hop successivo, viene restituita la route, se definita dall'utente. In caso contrario l'hop successivo restituisce la route di sistema.

#### <a name="security-group-view"></a>Visualizzazione dei gruppi di sicurezza

Ottiene le regole di sicurezza valide e applicate in una macchina virtuale. I gruppi di sicurezza di rete sono associati a un livello di subnet o a un livello di scheda di interfaccia di rete. Se associato a livello di subnet, si applica a tutte le istanze delle VM della subnet. La [visualizzazione dei gruppi di sicurezza](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) di rete restituisce tutti i gruppi di sicurezza di rete configurati e le regole associate a livello di scheda di interfaccia di rete e di subnet per una macchina virtuale, offrendo così informazioni approfondite sulla configurazione. Vengono anche restituite le regole di sicurezza effettive per ogni scheda di interfaccia di rete in una VM. Usando la visualizzazione dei gruppi di sicurezza di rete, è possibile valutare le vulnerabilità di rete di una VM, ad esempio le porte aperte. È anche possibile verificare se il gruppo di sicurezza di rete funziona come previsto [confrontando le regole di sicurezza configurate e quelle effettive](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Registrazione dei flussi dei gruppi di sicurezza di rete

 La registrazione dei flussi dei gruppi di sicurezza di rete permette di acquisire i log relativi al traffico consentito o negato dalle regole di sicurezza nel gruppo. Il flusso è definito da informazioni a 5 tuple, ovvero l'indirizzo IP di destinazione, l'indirizzo IP di origine, la porta di destinazione, la porta di origine e il protocollo.

I [log dei flussi del gruppo di sicurezza di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) sono una funzionalità di Network Watcher che consente di visualizzare le informazioni sul traffico IP in entrata e in uscita tramite un gruppo di sicurezza di rete.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Risoluzione dei problemi di connessione e del gateway di rete virtuale

Network Watcher offre numerose funzionalità che consentono di comprendere le risorse di rete in Azure. Una di queste funzionalità è la risoluzione dei problemi riscontrati con le risorse. La funzionalità di [risoluzione dei problemi delle risorse](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) può essere chiamata da PowerShell, dall'interfaccia della riga di comando o dall'API REST. Quando chiamata, Network Watcher controlla l'integrità di un gateway di rete virtuale o di una connessione e restituisce i risultati.

Questa sezione illustra le diverse attività di gestione attualmente disponibili per la risoluzione dei problemi relativi alle risorse.

-   [Risolvere i problemi relativi a un gateway di rete virtuale](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Risolvere i problemi relativi a una connessione](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Limite sottoscrizioni di rete

[Limite sottoscrizioni di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) fornisce informazioni dettagliate sull'utilizzo delle singole risorse di rete di una sottoscrizione in un'area rispetto al numero massimo di risorse disponibili.

#### <a name="configuring-diagnostics-log"></a>Configurazione dei log di diagnostica

Network Watcher offre una visualizzazione dei [log di diagnostica](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). contenente tutte le risorse di rete che supportano la registrazione diagnostica. Da questa visualizzazione è possibile abilitare e disabilitare le risorse di rete in modo facile e veloce.

### <a name="network-resource-level-monitoring"></a>Monitoraggio a livello di risorsa di rete

Per il monitoraggio a livello di risorsa sono disponibili le funzionalità seguenti:

#### <a name="audit-log"></a>Log di controllo

Le operazioni eseguite come parte della configurazione delle reti vengono registrate. I log di controllo sono fondamentali per stabilire varie conformità. È possibile visualizzare i relativi log nel portale di Azure o recuperarli usando strumenti Microsoft, come Power BI, o strumenti di terze parti. I log di controllo sono disponibili tramite il portale, PowerShell, l'interfaccia della riga di comando e l'API REST.

> [!Note]
> Per altre informazioni sui log di controllo, vedere [Operazioni di controllo con Gestione risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
I log di controllo sono disponibili per le operazioni eseguite su tutte le risorse di rete.


#### <a name="metrics"></a>Metriche

Le metriche sono costituite da contatori e misurazioni delle prestazioni raccolti in un determinato periodo di tempo. Attualmente le metriche sono disponibili per il gateway applicazione. Le metriche possono essere usate per attivare avvisi in base a una soglia. Per impostazione predefinita, il gateway applicazione di Azure monitora l'integrità di tutte le risorse nel pool back-end e rimuove automaticamente dal pool le risorse considerate non integre. Il gateway applicazione continua a monitorare le istanze non integre e le riaggiunge al pool back-end integro, dopo che sono diventate disponibili e rispondono ai probe di integrità. Il gateway applicazione invia i probe di integrità con la stessa porta definita nelle impostazioni HTTP del back-end. Questa configurazione garantisce che il probe testi la stessa porta che verrebbe usata dai clienti per connettersi al back-end.

> [!Note]
> Per informazioni su come usare le metriche per creare avvisi, vedere l'articolo relativo alla [diagnostica del gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

#### <a name="diagnostic-logs"></a>Log di diagnostica

Le risorse di rete creano eventi periodici e spontanei, che vengono registrati negli account di archiviazione e inviati a un hub eventi oppure a Log Analytics. Questi log contengono informazioni dettagliate sull'integrità delle singole risorse e possono essere visualizzati con strumenti quali Power BI e Log Analytics. Per informazioni su come visualizzare i log di diagnostica, vedere l'articolo relativo a [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Sono disponibili log di diagnostica per il [servizio di bilanciamento del carico](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), le route e il [gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Network Watcher offre una visualizzazione dei log di diagnostica contenente tutte le risorse di rete che supportano la registrazione diagnostica. Da questa visualizzazione è possibile abilitare e disabilitare le risorse di rete in modo facile e veloce.

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) è un servizio di [Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) che consente di monitorare gli ambienti cloud e locali per garantirne la disponibilità e le prestazioni. Raccoglie i dati generati dalle risorse negli ambienti cloud e locali e da altri strumenti di monitoraggio per analizzare più origini.

Log Analytics offre le seguenti soluzioni per il monitoraggio delle reti:

-   Monitoraggio delle prestazioni di rete

-   Analisi gateway applicazione di Azure

-   Analisi gruppo di sicurezza di rete di Azure

#### <a name="network-performance-monitor-npm"></a>Monitoraggio prestazioni rete (NPM)
La soluzione di gestione [Monitoraggio prestazioni rete](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) consente di monitorare l'integrità, la disponibilità e la raggiungibilità delle reti.

Viene usata per monitorare la connettività tra:

-   cloud pubblico e risorse locali

-   data center e percorsi utente (filiali)

-   subnet che ospita i diversi livelli di un'applicazione a più livelli.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Analisi gateway applicazione di Azure in Log Analytics

I log seguenti sono supportati per i gateway applicazione:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Le metriche seguenti sono supportate per i gateway applicazione:

-   Velocità effettiva in cinque minuti

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Analisi gruppo di sicurezza di rete di Azure in Log Analytics

I log seguenti sono supportati per i [gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** contiene voci relative alle regole dei gruppi di sicurezza di rete applicate alle VM e ai ruoli delle istanze in base all'indirizzo MAC. Lo stato di queste regole viene raccolto ogni 60 secondi.

- **NetworkSecurityGroupRuleCounter:** contiene voci che indicano quante volte ogni regola dei gruppi di sicurezza di rete viene applicata per rifiutare o consentire il traffico.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla sicurezza, vedere alcuni degli approfondimenti sull'argomento:

-   [Log Analytics per i gruppi di sicurezza di rete (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Networking innovations that drive the cloud disruption (Innovazioni di rete che determinano l'interruzione del cloud)](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: The networking switch software that powers the Microsoft Global Cloud (SONiC: il software di commutazione di rete su cui si basa il cloud globale Microsoft)](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [How Microsoft builds its fast and reliable global network (Informazioni sul modo in cui Microsoft crea una rete globale veloce e affidabile)](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Lighting up network innovation (Realizzare l'innovazione della rete)](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
