---
title: Concetti e requisiti della sicurezza di rete in Azure | Microsoft Docs
description: Questo articolo illustra i concetti fondamentali e i requisiti di sicurezza di rete e descrive i vantaggi che Azure offre in ognuna di queste aree.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 2aabe3d1fa8a6034c2dab38c8d6fa6da4b00ac1b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080340"
---
# <a name="azure-network-security-overview"></a>Panoramica della sicurezza di rete di Azure

La sicurezza di rete può essere definita come il processo di protezione delle risorse da attacchi o accessi non autorizzati tramite l'applicazione di controlli al traffico di rete. L'obiettivo è garantire il transito del solo traffico legittimo. Azure dispone di una solida infrastruttura di rete per supportare i requisiti di connettività di applicazioni e servizi. La connettività di rete è possibile tra le risorse residenti in Azure, le risorse locali e quelle ospitate in Azure, nonché da e verso Internet e Azure.

Questo articolo illustra alcune delle opzioni offerte da Azure nell'area della sicurezza di rete. Gli argomenti trattati includono:

* Rete di Azure
* Controllo di accesso alla rete
* Firewall di Azure
* Accesso remoto sicuro e connettività cross-premise
* Disponibilità
* Risoluzione dei nomi
* Architettura di rete perimetrale
* Protezione DDoS di Azure
* Frontdoor di Azure
* Gestione traffico
* Monitoraggio e rilevamento delle minacce

## <a name="azure-networking"></a>Rete di Azure

Azure richiede che le macchine virtuali siano connesse a una rete virtuale di Azure. Una rete virtuale è un costrutto logico basato sull'infrastruttura di rete fisica di Azure. Ogni rete virtuale è isolata da tutte le altre. Ciò aiuta a garantire che il traffico di rete nelle distribuzioni di un utente non sia accessibile ad altri clienti di Azure.

Altre informazioni:

* [Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Controllo di accesso alla rete

Il controllo di accesso alla rete consiste nel limitare la connettività da e verso subnet o dispositivi specifici all'interno di una rete virtuale. L'obiettivo del controllo di accesso alla rete è di limitare agli utenti e ai dispositivi autorizzati l'accesso a macchine virtuali e servizi. I controlli di accesso si basano sulla decisione di consentire o negare connessioni da e verso una macchina virtuale o un servizio.

Azure supporta numerosi tipi di controllo di accesso alla rete, tra cui:

* Controllo a livello rete
* Controllo di route e tunneling forzato
* Appliance di sicurezza di rete virtuale

### <a name="network-layer-control"></a>Controllo a livello rete

Qualsiasi distribuzione sicura richiede alcune misure di controllo di accesso alla rete. Lo scopo del controllo di accesso alla rete è di limitare la comunicazione delle macchine virtuali ai sistemi necessari e di bloccare gli altri tentativi di comunicazione.

> [!NOTE]
> I firewall di archiviazione vengono analizzati nell’articolo[Panoramica sulla sicurezza di archiviazione di Azure](security-storage-overview.md)

#### <a name="network-security-rules-nsgs"></a>Regole di sicurezza di rete (NSG)

Se è necessario un controllo di accesso di base a livello di rete, basato sull'indirizzo IP e sul protocollo TCP o UDP, è possibile usare i gruppi di sicurezza di rete (NSG). Un gruppo di sicurezza di rete è un firewall di base e con stato che filtra i pacchetti e consente di gestire l'accesso in base a [5 tuple](https://www.techopedia.com/definition/28190/5-tuple). I gruppi di sicurezza di rete includono funzionalità che semplificano la gestione e riducono le probabilità di errori di configurazione:

* Le **regole di sicurezza ottimizzate** semplificano la definizione delle regole di sicurezza di rete e consentono di creare regole complesse invece di molteplici regole semplici per ottenere lo stesso risultato.
* I **tag di servizio** sono etichette create da Microsoft che rappresentano un gruppo di indirizzi IP. Vengono aggiornati in modo dinamico per includere gli intervalli IP che soddisfano le condizioni che definiscono l'inclusione nell'etichetta. Se si vuole ad esempio creare una regola applicabile a tutta l'archiviazione di Azure nell'area orientale, è possibile usare Storage.EastUS.
* I **gruppi di sicurezza delle applicazioni** consentono di distribuire risorse a gruppi di applicazioni e di controllare l'accesso a queste risorse tramite la creazione di regole che usano questi gruppi di applicazioni. Se, ad esempio, sono stati distribuiti server Web al gruppo di applicazioni "Server Web", è possibile creare una regola che applichi un gruppo di sicurezza di rete che consente il traffico 443 da Internet a tutti i sistemi inclusi nel gruppo di applicazioni "Server Web".

Gli NSG non forniscono ispezione a livello dell'applicazione o controlli di accesso autenticato.

Altre informazioni:

* [Gruppi di sicurezza di rete](../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>Accesso JIT alle macchine virtuali tramite il Centro sicurezza di Azure

Il [Centro sicurezza di Azure](../security-center/security-center-intro.md) è in grado di gestire i gruppi di sicurezza di rete nelle macchine virtuali e di bloccare l'accesso alla macchina virtuale fino a quando un utente con autorizzazioni del [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) appropriate non richiede l'accesso. Quando l'utente è stato autorizzato, il Centro sicurezza di Azure apporta ai gruppi di sicurezza di rete le modifiche necessarie per consentire l'accesso alle porte selezionate per il tempo specificato. Alla scadenza del tempo specificato, i gruppi di sicurezza di rete vengono ripristinati allo stato protetto precedente.

Altre informazioni:

* [Accesso JIT (Just-in-Time) in Centro sicurezza di Azure](../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Endpoint di servizio

Gli endpoint di servizio sono un altro modo per applicare il controllo sul traffico. È possibile limitare la comunicazione con i servizi supportati alle sole reti virtuali su una connessione diretta. Il traffico che transita dalla rete virtuale al servizio di Azure specificato rimane nella rete backbone di Microsoft Azure.  

Altre informazioni:

* [Endpoint servizio](../virtual-network/virtual-network-service-endpoints-overview.md#securing-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Controllo di route e tunneling forzato

La possibilità di controllare il comportamento di routing delle reti virtuali è fondamentale. Se il routing non è configurato correttamente, è possibile che le applicazioni e i servizi ospitati nella macchina virtuale si connettano a dispositivi non autorizzati, inclusi sistemi di proprietà e gestiti da potenziali utenti malintenzionati.

La rete di Azure supporta la personalizzazione del comportamento di routing per il traffico di rete nelle reti virtuali. Ciò consente di modificare le voci predefinite della tabella di routing nella rete virtuale. Il controllo del comportamento di routing consente di assicurarsi che tutto il traffico in ingresso o in uscita da un determinato dispositivo o gruppo di dispositivi nella rete virtuale avvenga attraverso un percorso specifico.

Si supponga ad esempio che nella rete virtuale sia presente un'appliance di sicurezza di rete virtuale. Si vuole essere certi che tutto il traffico da e verso la rete virtuale passi attraverso l'appliance di sicurezza virtuale. È possibile a tale scopo configurare [route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md) in Azure.

Il [tunneling forzato](https://www.petri.com/azure-forced-tunneling) è un meccanismo che può essere usato per assicurarsi che ai servizi sia impedito di stabilire una connessione a dispositivi in Internet. Questo approccio è diverso dall'accettare connessioni in ingresso e rispondere alle richieste. I server Web front-end devono rispondere alle richieste dagli host in Internet e, di conseguenza, il traffico originato da Internet è consentito in ingresso a questi server Web, che sono autorizzati a rispondere.

Non si vuole invece consentire a un server Web front-end di avviare una richiesta in uscita. Tali richieste possono rappresentare un rischio per la sicurezza perché queste connessioni possono essere usate per scaricare malware. Anche nel caso in cui si voglia consentire ai server front-end di avviare richieste in uscita verso Internet, è consigliabile imporre il passaggio tramite proxy Web locali. Ciò consente di usufruire dei vantaggi della registrazione e del filtro degli URL.

Per evitare tutto questo, è preferibile usare invece il tunneling forzato. Quando si abilita il tunneling forzato, tutte le connessioni verso Internet devono forzatamente passare attraverso il gateway locale. È possibile configurare il tunneling forzato usufruendo dei vantaggi delle route definite dall'utente.

Altre informazioni:

* [Informazioni sulle route definite dall'utente e sull'inoltro IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Appliance di sicurezza di rete virtuale

Sebbene i gruppi di sicurezza di rete, le route definite dall'utente e il tunneling forzato offrano un certo grado di sicurezza ai livelli di rete e trasporto del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), è possibile che si desideri abilitare la sicurezza a livelli superiori rispetto a quello di rete.

Ad esempio, i requisiti di sicurezza possono includere:

* Autenticazione e autorizzazione prima di consentire l'accesso all'applicazione
* Rilevamento delle intrusioni e relativa risposta
* Ispezione a livello dell'applicazione per i protocolli di alto livello
* Filtro degli URL
* Antivirus e antimalware a livello di rete
* Protezione anti-robot
* Controllo di accesso all'applicazione
* Protezione DDoS aggiuntiva, oltre alla protezione DDoS inclusa nell'infrastruttura stessa di Azure

È possibile accedere a queste funzionalità di sicurezza di rete avanzate usando una soluzione dei partner di Azure. Le soluzioni di sicurezza di rete più recenti offerte dai partner Azure sono disponibili in [Azure Marketplace](https://azure.microsoft.com/marketplace/) cercando "sicurezza" e "sicurezza di rete".

## <a name="azure-firewall"></a>Firewall di Azure

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo come servizio con disponibilità elevata incorporata e scalabilità del cloud senza restrizioni. Alcune funzionalità includono:

* Disponibilità elevata
* Scalabilità del cloud
* Regole di filtro del nome di dominio completo dell'applicazione
* Regole di filtro per il traffico di rete

Altre informazioni:

* [Panoramica del Firewall di Azure](../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Accesso remoto sicuro e connettività cross-premise

Le operazioni di installazione, configurazione e gestione delle risorse di Azure devono essere eseguite in modalità remota. È inoltre possibile che si voglia distribuire soluzioni di [IT ibrido](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) costituite da componenti che si trovano sia in locale che nel cloud pubblico di Azure. Questi scenari richiedono l'accesso remoto sicuro.

Rete di Azure supporta gli scenari di accesso remoto sicuro seguenti:

* Connessione di workstation individuali a una rete virtuale
* Connessione della rete locale a una rete virtuale tramite una VPN
* Connessione della rete locale a una rete virtuale tramite un collegamento WAN dedicato
* Connessione di reti virtuali tra loro

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Connessione di workstation individuali a una rete virtuale

È possibile che si desideri consentire a singoli sviluppatori o al personale operativo di gestire le macchine virtuali e i servizi in Azure. Si supponga ad esempio di dover accedere a una macchina virtuale in una rete virtuale. I criteri di protezione applicati tuttavia non consentono l'accesso remoto RDP o SSH a singole macchine virtuali. In questo caso, è possibile usare una connessione [VPN da punto a sito](../vpn-gateway/point-to-site-about.md).

La connessione VPN da punto a sito consente di configurare una connessione privata e sicura tra l'utente e la rete virtuale. Quando viene stabilita la connessione VPN, l'utente può usare RDP o SSH tramite il collegamento VPN in una macchina virtuale qualsiasi nella rete virtuale. Si presume che l'utente possa eseguire l'autenticazione e disponga delle autorizzazioni. VPN da punto a sito supporta:

* Secure Socket Tunneling Protocol (SSTP), un protocollo VPN di proprietà basato su SSL. Una soluzione VPN SSL può penetrare i firewall perché la maggior parte dei firewall apre la porta TCP 443 usata da SSL. SSTP è supportato solo nei dispositivi Windows. Azure supporta tutte le versioni di Windows che hanno SSTP (Windows 7 e versioni successive).

* VPN IKEv2, una soluzione VPN IPsec basata su standard. VPN IKEv2 può essere usato per connettersi da dispositivi Mac (versioni OSX 10.11 e successive).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Altre informazioni:

* [Configurare una connessione da punto a sito a una rete virtuale con PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Connessione della rete locale a una rete virtuale tramite una VPN

È possibile che si desideri connettere l'intera rete aziendale, o parti di essa, a una rete virtuale. Questo approccio è comune negli scenari di IT ibrido in cui le aziende [estendono il data center locale ad Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). In molti casi, le organizzazioni ospitano parti di un servizio in Azure e parti in locale. Questo approccio potrebbe essere utile, ad esempio, quando una soluzione include server Web front-end in Azure e database back-end locali. Questo tipo di connessioni "cross-premise" rende anche più sicura la gestione delle risorse residenti in Azure e abilita scenari come l'estensione di controller di dominio Active Directory in Azure.

A questo scopo è possibile usare una [VPN da sito a sito](https://www.techopedia.com/definition/30747/site-to-site-vpn). La differenza tra una VPN da sito a sito e una VPN da punto a sito è che quest'ultima connette un dispositivo singolo a una rete virtuale. Una VPN da sito a sito connette un'intera rete, ad esempio quella locale, a una rete virtuale. Le VPN da sito a sito che si connettono a una rete virtuale usano il protocollo VPN in modalità tunnel IPsec altamente sicuro.

Altre informazioni:

* [Creare una rete virtuale di Resource Manager con una connessione VPN da sito a sito usando il portale di Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Informazioni sul gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Connessione della rete locale a una rete virtuale tramite un collegamento WAN dedicato

Le connessioni VPN da punto a sito e da sito a sito consentono di abilitare la connettività cross-premise in modo efficace. Alcune organizzazioni ritengono tuttavia che queste connessioni presentino gli svantaggi seguenti:

* Le connessioni VPN spostano dati attraverso Internet, quindi sono esposte ai potenziali problemi di sicurezza che caratterizzano lo spostamento di dati su una rete pubblica. Per le connessioni Internet non è inoltre possibile garantire l'affidabilità e la disponibilità.
* Le connessioni VPN a reti virtuali possono non avere la larghezza di banda necessaria per alcune applicazioni e adatta ad alcuni scopi, perché raggiungono una velocità massima di circa 200 Mbps.

Le organizzazioni che necessitano del massimo livello di sicurezza e disponibilità per le connessioni cross-premise usano in genere collegamenti WAN dedicati per connettersi a siti remoti. Azure consente di usare un collegamento WAN dedicato per connettere la rete locale a una rete virtuale. Azure ExpressRoute, ExpressRoute Direct e Copertura globale di ExpressRoute abilitano questa opzione.

Altre informazioni:

* [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md)
* [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md)
* [Copertura globale di ExpressRoute](..//expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Connessione di reti virtuali tra loro

Per le distribuzioni si possono usare molte reti virtuali. I motivi sono vari. Si potrebbe volere semplificare la gestione o aumentare la protezione. Indipendentemente dalla motivazione per cui si sceglie di distribuire le risorse tra reti virtuali diverse, a volte può essere opportuno stabilire una connessione tra le risorse disponibili in ognuna delle reti.

Una soluzione può consistere nel connettere i servizi in una rete virtuale a quelli in un'altra rete virtuale con un "loopback" tramite Internet. La connessione viene avviata in una rete virtuale, passa attraverso Internet e quindi ritorna alla rete virtuale di destinazione. Questa opzione espone la connessione ai problemi di sicurezza che caratterizzano tutte le comunicazioni basate su Internet.

Un'opzione migliore potrebbe essere quella di creare una VPN da sito a sito che connette due reti virtuali. Questo metodo usa lo stesso protocollo in [modalità tunnel IPSec](https://technet.microsoft.com/library/cc786385.aspx) della connessione VPN da sito a sito cross-premise citata in precedenza.

Il vantaggio che deriva da questo approccio consiste nel fatto che la connessione VPN viene stabilita sull'infrastruttura di rete di Azure, anziché tramite Internet. Questo approccio offre un livello di sicurezza aggiuntivo rispetto alle VPN da sito a sito che si connettono tramite Internet.

Altre informazioni:

* [Configurare una connessione da rete virtuale a rete virtuale con Azure Resource Manager e PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Un altro modo per connettere le reti virtuali è il [peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md). Questa funzionalità consente di connettere due reti Azure in modo che la comunicazione tra di esse avvenga sull'infrastruttura backbone Microsoft senza passare attraverso la rete Internet. Con il peering di rete virtuale è possibile connettere due reti virtuali all'interno della stessa area oppure due reti virtuali in aree Azure diverse. Per limitare la connettività tra subnet o sistemi diversi è possibile usare gruppi di sicurezza di rete.

## <a name="availability"></a>Disponibilità

La disponibilità è un componente fondamentale di qualsiasi programma di sicurezza. Se gli utenti e i sistemi non possono accedere alle risorse necessarie attraverso la rete, il servizio può essere considerato compromesso. Azure offre tecnologie di rete che supportano i meccanismi a disponibilità elevata seguenti:

* Bilanciamento del carico basato su HTTP
* Bilanciamento del carico a livello di rete
* Bilanciamento del carico globale

Il meccanismo di bilanciamento del carico è progettato per distribuire equamente le connessioni tra più dispositivi. Ecco gli obiettivi del processo di bilanciamento del carico:

* Aumentare la disponibilità. Quando si bilancia il carico delle connessioni tra più dispositivi, uno o più dispositivi possono diventare non disponibili senza compromettere il servizio. I servizi in esecuzione nei restanti dispositivi online possono continuare a rendere disponibile il contenuto dal servizio.
* Aumentare le prestazioni. Quando si bilancia il carico delle connessioni tra più dispositivi, non è un singolo dispositivo a dover assorbire l'intero carico di elaborazione. Al contrario, le richieste di elaborazione e memoria per gestire il contenuto vengono distribuite tra più dispositivi.

### <a name="http-based-load-balancing"></a>Bilanciamento del carico basato su HTTP

Le organizzazioni che eseguono servizi basati sul Web spesso preferiscono implementare un servizio di bilanciamento del carico basato su HTTP a monte di tali servizi Web. Ciò consente di garantire livelli di prestazioni adeguati e disponibilità elevata. I servizi di bilanciamento del carico basati sulla rete tradizionali si basano su protocolli dei livelli di trasporto e di rete. I servizi di bilanciamento del carico basati su HTTP, d'altro canto, prendono decisioni in base alle caratteristiche del protocollo HTTP.

Il gateway applicazione di Azure offre il bilanciamento del carico basato su HTTP per i servizi basati sul Web. Il gateway applicazione supporta:

* Affinità di sessione basata su cookie Questa funzionalità garantisce che le connessioni stabilite a uno dei server controllati dal servizio di bilanciamento del carico restino inalterate tra il client e il server. In questo modo si assicura la stabilità delle transazioni.
* Offload SSL Quando un client si connette con il servizio di bilanciamento del carico, la sessione viene crittografata tramite l'utilizzo del protocollo HTTPS (SSL). Per migliorare le prestazioni, è tuttavia possibile usare il protocollo HTTP (senza crittografia) per la connessione tra il servizio di bilanciamento del carico e il server Web controllato dal servizio di bilanciamento del carico. Questa modalità è definita "offload SSL", perché i server Web controllati dal servizio di bilanciamento del carico non sono soggetti al sovraccarico del processore interessato dalla crittografia. e quindi saranno in grado di soddisfare le richieste più rapidamente.
* Routing di contenuto basato su URL. Questa funzionalità consente al servizio di bilanciamento del carico di scegliere dove inoltrare le connessioni in base all'URL di destinazione, offrendo quindi maggiore flessibilità rispetto alle soluzioni che prendono decisioni sul bilanciamento del carico in base agli indirizzi IP.

Altre informazioni:

* [Panoramica del gateway applicazione](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Bilanciamento del carico a livello di rete

A differenza del bilanciamento del carico basato su HTTP, il bilanciamento del carico a livello di rete prende decisioni in base all'indirizzo IP e ai numeri di porta (TCP o UDP).
È possibile usufruire dei vantaggi del bilanciamento del carico a livello di rete in Azure tramite Azure Load Balancer. Alcune delle caratteristiche principali di Azure Load Balancer includono:

* Bilanciamento del carico a livello di rete in base all'indirizzo IP e ai numeri di porta.
* Supporto per qualsiasi protocollo a livello di applicazione.
* Bilanciamento del carico tra macchine virtuali di Azure e istanze del ruolo di Servizi cloud.
* Può essere usato per applicazioni e macchine virtuali con connessione Internet (bilanciamento del carico esterno) e senza connessione Internet (bilanciamento del carico interno).
* Monitoraggio di endpoint, che viene usato per determinare se i servizi controllati dal servizio di bilanciamento del carico sono diventati non disponibili.

Altre informazioni:

* [Bilanciamento del carico con connessione a Internet tra più macchine virtuali o servizi](../load-balancer/load-balancer-internet-overview.md)
* [Panoramica del bilanciamento del carico interno](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Bilanciamento del carico globale

Alcune organizzazioni vogliono poter contare sul massimo livello di disponibilità possibile. Un modo per raggiungere questo obiettivo consiste nell'ospitare applicazioni in data center distribuiti a livello globale. Quando un'applicazione è ospitata in data center dislocati in tutto il mondo, un'intera area geopolitica può diventare non disponibile, ma l'applicazione continua a essere operativa.

Questa strategia di bilanciamento del carico può avvantaggiare le prestazioni. È possibile indirizzare le richieste per il servizio al data center più vicino al dispositivo che effettua la richiesta.

In Azure è possibile ottenere i vantaggi del bilanciamento del carico globale tramite Gestione traffico di Azure.

Altre informazioni:

* [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Risoluzione dei nomi

La risoluzione dei nomi è una funzione critica per tutti i servizi ospitati in Azure. Dal punto di vista della sicurezza la compromissione della funzione di risoluzione dei nomi può consentire a un utente malintenzionato di reindirizzare le richieste dai siti dell'utente al suo sito. La sicurezza della risoluzione dei nomi è un requisito per tutti i servizi cloud ospitati.

Esistono due tipi di risoluzione dei nomi che occorre considerare:

* Risoluzione dei nomi interna. Viene usata dai servizi nelle reti virtuali, nelle reti locali o in entrambe. I nomi usati per la risoluzione dei nomi interna non sono accessibili tramite Internet. Per una sicurezza ottimale, è importante che lo schema di risoluzione dei nomi interna non sia accessibile a utenti esterni.
* Risoluzione dei nomi esterna. Viene usata da utenti e dispositivi che si trovano all'esterno delle reti locali e delle reti virtuali. Questi sono i nomi visibili su Internet e vengono usati per connettersi direttamente ai servizi basati sul cloud.

Per la risoluzione dei nomi interna sono disponibili due opzioni:

* Un server DNS di rete virtuale. Quando si crea una nuova rete virtuale, viene creato automaticamente un server DNS. Questo server DNS può risolvere i nomi dei computer che si trovano nella rete virtuale. Il server DNS non è configurabile, viene gestito dal servizio di gestione dell'infrastruttura di Azure e aiuta pertanto a rendere sicura la soluzione di risoluzione dei nomi.
* Un server DNS personalizzato. È possibile scegliere di inserire un server DNS a propria scelta nella rete virtuale. Può essere un server DNS integrato con Active Directory oppure una soluzione di server DNS fornita da un partner Azure, che è possibile ottenere da Azure Marketplace.

Altre informazioni:

* [Panoramica di Rete virtuale](../virtual-network/virtual-networks-overview.md)
* [Gestire i server DNS usati da una rete virtuale](../virtual-network/manage-virtual-network.md#change-dns-servers)

Per la risoluzione dei nomi esterna sono disponibili due opzioni:

* Ospitare il server DNS personalizzato esterno in locale.
* Ospitare il server DNS personalizzato esterno presso un provider di servizi.

Molte organizzazioni di grandi dimensioni ospitano i propri server DNS in locale. Lo fanno avendo l'esperienza di rete e la presenza globale necessarie per scegliere questo approccio.

Nella maggior parte dei casi è consigliabile ospitare i servizi di risoluzione dei nomi DNS presso un provider di servizi. I provider di servizi hanno l'esperienza di rete e la presenza globale per garantire una disponibilità molto elevata per i servizi di risoluzione dei nomi. La disponibilità è essenziale per i servizi DNS, perché se la risoluzione dei nomi ha esito negativo, nessuno sarà in grado di raggiungere i servizi con connessione Internet.

Azure offre una soluzione DNS esterna a disponibilità elevata e ad alte prestazioni denominata DNS di Azure. Questa soluzione di risoluzione dei nomi esterna sfrutta l'infrastruttura DNS di Azure in tutto il mondo. Consente di ospitare i domini in Azure con le credenziali, le API, gli strumenti e la fatturazione usati per gli altri servizi di Azure. Come parte di Azure, eredita anche i controlli di sicurezza avanzati integrati nella piattaforma.

Altre informazioni:

* [Panoramica di DNS di Azure](../dns/dns-overview.md)
* Le [zone private DNS di Azure](../dns/private-dns-overview.md) consentono di configurare nomi DNS privati per le risorse di Azure anziché usare i nomi assegnati automaticamente, senza la necessità di aggiungere una soluzione DNS personalizzata.

## <a name="perimeter-network-architecture"></a>Architettura di rete perimetrale

Molte organizzazioni di grandi dimensioni usano reti perimetrali per segmentare le proprie reti e creare una zona buffer tra Internet e i servizi che offrono. La parte di rete perimetrale della rete è considerata una zona a bassa sicurezza e nessun asset critico viene inserito in quel segmento di rete. Si vedono in genere dispositivi di sicurezza di rete con un'interfaccia di rete sul segmento di rete perimetrale. Un'altra interfaccia di rete è connessa a una rete dotata di macchine virtuali e servizi che accettano le connessioni in ingresso da Internet.

È possibile progettare una rete perimetrale in molti modi diversi. La decisione di distribuire una rete perimetrale e l'eventuale tipo di rete perimetrale da usare varia in base ai requisiti di sicurezza di rete.

Altre informazioni:

* [Servizi cloud Microsoft e sicurezza della rete](../best-practices-network-security.md)

## <a name="azure-ddos-protection"></a>Protezione DDoS di Azure

Gli attacchi Distributed Denial of Service (DDoS) sono tra le principali preoccupazioni che riguardano la disponibilità e la sicurezza per quei clienti che spostano le loro applicazioni nel cloud. Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.
Con la piattaforma Azure, Microsoft offre la protezione DDoS denominata **Basic**. Questa protezione non comporta alcun costo aggiuntivo e include il monitoraggio sempre attivo e la mitigazione in tempo reale degli attacchi comuni a livello di rete. Oltre alle protezioni incluse nella protezione DDoS **Basic** è possibile abilitare l'opzione **Standard**. Le funzionalità di Protezione DDoS Standard includono:

* **Integrazione nella piattaforma nativa:** integrazione nativa in Azure. Include la configurazione tramite il portale di Azure. Protezione DDoS Standard comprende le risorse e la loro configurazione.
* **Protezione pronta all'uso:** la configurazione semplificata consente di proteggere immediatamente tutte le risorse in una rete virtuale non appena il servizio Protezione DDoS Standard viene abilitato. Non è necessaria alcuna definizione dell'utente o intervento. Il servizio Protezione DDoS Standard mitiga istantaneamente e automaticamente gli attacchi non appena vengono rilevati.
* **Monitoraggio del traffico sempre attivo:** i modelli di traffico delle applicazioni vengono monitorati 24 ore su 24, 7 giorni su 7, cercando gli indicatori di attacchi DDoS. La mitigazione viene applicata quando vengono superati i criteri di protezione.
* **Report di mitigazione dei rischi di attacco** I report di mitigazione dei rischi di attacco usano i dati del flusso di rete aggregati per fornire informazioni dettagliate sugli attacchi delle risorse mirate.
* **Log del flusso di mitigazione degli attacchi** I log del flusso di mitigazione degli attacchi consentono di esaminare il traffico eliminato, il traffico inoltrato e altri dati sugli attacchi in tempo quasi reale durante un attacco DDoS attivo.
* **Ottimizzazione adattiva:** la profilatura intelligente del traffico apprende il modello di traffico dell'applicazione nel tempo e seleziona e aggiorna il profilo più adatto per il servizio. Il profilo viene modificato in base ai cambiamenti del traffico nel tempo. Protezione dal livello 3 al livello 7: offre protezione DDoS dello stack completo se usata con un web application firewall.
* **Scala di mitigazione completa:** è possibile mitigare più di 60 tipi diversi di attacchi con capacità globale, per una protezione dai più noti attacchi DDoS.
* **Metriche degli attacchi:** tramite Monitoraggio di Azure è possibile accedere al riepilogo delle metriche per ogni attacco.
* **Avviso in caso di attacco:** è possibile configurare avvisi all'inizio e alla fine di un attacco, così come durante l'attacco, usando le metriche integrate relative agli attacchi. Gli avvisi si integrano nel software operativo, ad esempio i log di monitoraggio di Microsoft Azure, Splunk, archiviazione di Azure, messaggio di posta elettronica e il portale di Azure.
* **Garanzia sui costi:**  crediti per il servizio di trasferimento dati e scale-out dell'applicazione per attacchi DDoS documentati.
* **DDoS Rapid Response** I clienti con Protezione DDoS Standard dispongono ora dell'accesso al team Rapid Response durante un attacco in corso. DRR può facilitare l'analisi dell'attacco, le mitigazioni personalizzate durante un attacco e l'analisi post-attacco.


Altre informazioni:

* [Panoramica di Protezione DDoS](../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Frontdoor di Azure

Il servizio Frontdoor di Azure consente di definire, gestire e monitorare l'instradamento globale del traffico Web. Ottimizza il routing del traffico per ottenere prestazioni ottimali e disponibilità elevata. Il servizio Frontdoor di Azure consente di creare regole di Web application firewall (WAF) personalizzate (WAF) per il controllo degli accessi al fine di proteggere il tuo carico di lavoro HTTP/HTTPS dallo sfruttamento basato su indirizzi IP del client, codice paese e parametri HTTP. Frontdoor consente inoltre di creare regole di limitazione di velocità per combattere il traffico bot dannoso e include l'offload SSL, la richiesta per HTTP/HTTPS e l'elaborazione a livello di applicazione.

La stessa piattaforma Frontdoor è protetta da Protezione DDoS di Azure di Siti Web Basic. Per una protezione maggiore, Protezione DDoS Standard di Azure può essere abilitata nelle reti virtuali per proteggere le risorse dagli attacchi a livello di rete (TCP/UDP) attraverso l'ottimizzazione automatica e la mitigazione. Frontdoor è un proxy inverso di livello 7 e consente solo al traffico Web di attraversare i server back-end e bloccare altri tipi di traffico per impostazione predefinita.

Altre informazioni:

* Per altre informazioni sul set di funzionalità completo di Frontdoor di Azure è possibile consultare la [Panoramica di Frontdoor di Azure](../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Gestione traffico di Azure

Gestione traffico di Azure è un servizio di bilanciamento del carico basato su DNS che consente di distribuire il traffico in modo ottimale ai servizi nelle aree globali di Azure, offrendo al tempo stesso disponibilità e velocità di risposta elevate. Gestione traffico usa DNS per indirizzare le richieste client all'endpoint di servizio più appropriato, in base a un metodo di routing del traffico e all'integrità degli endpoint. Un endpoint è un servizio con connessione Internet ospitato all'interno o all'esterno di Azure. Gestione traffico monitora gli endpoint e non indirizzare il traffico ad endpoint non disponibili.

Altre informazioni:

* [Panoramica di Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitoraggio e rilevamento delle minacce

Azure offre funzionalità che aiutano in questa area chiave con il rilevamento precoce, il monitoraggio, la raccolta e l'analisi del traffico di rete.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Azure Network Watcher aiuta a risolvere i problemi e offre un nuovissimo set di strumenti per aiutare nell'identificazione dei problemi di sicurezza.

La [visualizzazione Gruppo di sicurezza](../network-watcher/network-watcher-security-group-view-overview.md) agevola i controlli e la verifica della conformità della sicurezza delle macchine virtuali. Questa funzionalità consente di eseguire controlli a livello di codice, confrontando i criteri di base definiti dalla propria organizzazione con le regole valide per ognuna delle proprie macchine virtuali. Ciò consente di identificare eventuali deviazioni della configurazione.

[Acquisizione pacchetti](../network-watcher/network-watcher-packet-capture-overview.md) consente di acquisire il traffico di rete da e verso la macchina virtuale. È possibile raccogliere statistiche di rete e risolvere i problemi delle applicazioni, che possono rivelarsi estremamente utili nell'analisi delle intrusioni di rete. Questa funzionalità può inoltre essere usata con Funzioni di Azure per avviare acquisizioni di rete in risposta ad avvisi specifici di Azure.

Per altre informazioni su Network Watcher e su come avviare i test di alcune delle funzionalità nei laboratori, vedere [Azure network watcher monitoring overview](../network-watcher/network-watcher-monitoring-overview.md) (Panoramica del monitoraggio di Azure Network Watcher).

> [!NOTE]
> Per ricevere le notifiche più aggiornate sulla disponibilità e lo stato di questo servizio, vedere la [pagina degli aggiornamenti di Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Centro sicurezza di Azure

Il Centro sicurezza di Azure aiuta a impedire, rilevare e rispondere alle minacce offrendo visibilità e controllo avanzati della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio set di soluzioni di sicurezza.

Il servizio Centro sicurezza di Azure aiuta a ottimizzare e a monitorare la sicurezza di rete offrendo:

* Suggerimenti per la sicurezza di rete
* Monitoraggio dello stato della configurazione della sicurezza di rete
* Avvisi relativi alle minacce basate sulla rete a livello di endpoint e di rete.

Altre informazioni:

* [Introduzione al Centro sicurezza di Azure](../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>TAP di rete virtuale

Un TAP (Terminal Access Point) di rete virtuale di Azure consente di trasmettere il traffico di rete della macchina virtuale come flusso continuo a un agente di raccolta di pacchetti di rete o a uno strumento di analisi. L'agente di raccolta o lo strumento di analisi viene fornito da un partner appliance virtuale di rete. È possibile usare la stessa risorsa TAP di rete virtuale per aggregare il traffico da più interfacce di rete nella stessa sottoscrizione o in sottoscrizioni diverse.

Altre informazioni:

* [TAP di rete virtuale](../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Registrazione

La registrazione a livello di rete è una funzione chiave per qualsiasi scenario di sicurezza di rete. In Azure è possibile registrare le informazioni ottenute per i gruppi di sicurezza di rete per avere informazioni di registrazione a livello di rete. Con la registrazione dei gruppi di sicurezza di rete si ottengono informazioni da:

* [Log attività](../azure-monitor/platform/activity-logs-overview.md). Vengono usati per visualizzare tutte le operazioni inviate alle sottoscrizioni di Azure. Questi log sono abilitati per impostazione predefinita e possono essere usati nel portale di Azure. Nelle versioni precedenti sono noti come "log di controllo" o "log operativi".
* Log eventi. Forniscono informazioni sulle regole applicate ai gruppi di sicurezza di rete.
* Log contatori. Consentono di sapere quante volte ogni regola dei gruppi di sicurezza di rete è stata applicata per rifiutare o consentire il traffico.

Per visualizzare e analizzare questi log è anche possibile usare [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), uno strumento avanzato di visualizzazione dei dati.
Altre informazioni:

* [Log di monitoraggio di Azure per i gruppi di sicurezza di rete (Nsg)](../virtual-network/virtual-network-nsg-manage-log.md)
