---
title: Procedure consigliate per la configurazione della rete per i carichi di lavoro migrati in Azure | Microsoft Docs
description: È possibile ottenere le procedure consigliate per configurare la rete per i carichi di lavoro migrati ad Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: raynew
ms.openlocfilehash: 302445038dc9767bd412e232f62fc5249a1a7f09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61296535"
---
# <a name="best-practices-to-set-up-networking-for-workloads-migrated-to-azure"></a>Procedure consigliate per la configurazione della rete per i carichi di lavoro migrati in Azure

Durante la pianificazione e la progettazione per la migrazione, oltre alla migrazione stessa, uno degli aspetti più importanti è la progettazione e l'implementazione della rete di Azure. Questo articolo descrive le procedure consigliate per la rete durante la migrazione alle implementazioni IaaS e PaaS in Azure.

> [!IMPORTANT]
> Le procedure consigliate e le opinioni descritte in questo articolo si basano sulle funzionalità dei servizi e della piattaforma di Azure disponibili al momento della redazione di questo documento. Caratteristiche e funzionalità variano nel tempo. È possibile che non tutti i consigli siano applicabili alla distribuzione in uso. È pertanto necessario scegliere quelli che meglio si adattano alle proprie esigenze.


## <a name="design-virtual-networks"></a>Progettare reti virtuali

Azure offre le reti virtuali:
- Le risorse di Azure comunicano tra loro in modo privato, diretto e sicuro tramite reti le virtuali.
- È possibile configurare connessioni di endpoint nelle reti virtuali per le VM e i servizi che richiedono la comunicazione Internet.
- Una rete virtuale è un isolamento logico del cloud di Azure dedicato alla sottoscrizione.
- È possibile implementare più reti virtuali in ogni sottoscrizione e area di Azure.
- Ogni rete virtuale è isolata dalle altre.
- Le reti virtuali possono contenere indirizzi IP privati e pubblici definiti in [RFC 1918](https://tools.ietf.org/html/rfc1918), espressi nella notazione CIDR. Gli indirizzi IP pubblici non sono accessibili direttamente da Internet.
- Le reti virtuali possono connettersi tra loro tramite il peering reti virtuali. Le reti virtuali connesse possono essere nelle stesse aree o in aree diverse. Di conseguenza, le risorse in una rete virtuale possono connettersi alle risorse in altre reti virtuali.
- Per impostazione predefinita, Azure instrada il traffico tra subnet di una rete virtuale, reti virtuali connesse, reti locali e Internet.


Esistono una serie di aspetti da considerare durante la pianificazione della topologia della rete virtuale, ad esempio come organizzare gli spazi di indirizzi IP, come implementare una rete hub-spoke, segmentare le reti virtuali in subnet, configurare il DNS e implementare le zone di disponibilità di Azure.

## <a name="best-practice-plan-ip-addressing"></a>Procedura consigliata: pianificare l'indirizzamento IP

Quando si creano le reti virtuali come parte della migrazione, è importante pianificare lo spazio di indirizzi IP della rete virtuale.

- È consigliabile assegnare uno spazio di indirizzi che non sia superiore a un intervallo CIDR di /16 per ogni rete virtuale. Le reti virtuali consentono l'uso di 65536 indirizzi IP e l'assegnazione di un prefisso inferiore a /16 comporterebbe la perdita di indirizzi IP. È importante non sprecare gli indirizzi IP, anche se si trovano negli intervalli privati definiti da RFC 1918.
- Lo spazio di indirizzi della rete virtuale non deve sovrapporsi agli intervalli della rete locale.
- NAT (Network Address Translation) non deve essere usato.
- La sovrapposizione degli indirizzi può impedire la connessione delle reti e il corretto funzionamento del routing. Se le reti si sovrappongono, è necessario riprogettare la rete o usare Network Address Translation (NAT).

**Altre informazioni:**

- [Ottenere una panoramica](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) delle reti virtuali di Azure.
- [Leggere](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq) le domande frequenti sulla rete.
- [Informazioni sulle](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) limitazioni della rete.


## <a name="best-practice-implement-a-hub-spoke-network-topology"></a>Procedura consigliata: implementare una topologia di rete hub-spoke

Una topologia di rete hub-spoke isola i carichi di lavoro durante la condivisione di servizi quali identità e sicurezza.
- L'hub è una rete virtuale di Azure che opera come punto centrale della connettività.
- Gli spoke sono reti virtuali che si connettono alla rete virtuale dell'hub tramite il peering reti virtuali.
- I servizi condivisi vengono distribuiti nell'hub, mentre i singoli carichi di lavoro vengono distribuiti come spoke. 

Valutare gli aspetti seguenti:
- L'implementazione di una topologia hub- spoke in Azure consente di centralizzare servizi comuni come le connessioni a reti locali, i firewall e l'isolamento delle reti virtuali. La rete virtuale dell'hub fornisce un punto centrale di connettività per le reti locali e una soluzione per ospitare i servizi usati dai carichi di lavoro ospitati nelle reti virtuali spoke.
- Una configurazione hub-spoke in genere viene usata dalle aziende di grandi dimensioni. Nelle reti di dimensioni inferiori è possibile prendere in considerazione una progettazione più semplice per ridurre i costi e la complessità.
- Le reti virtuali spoke possono essere usate per isolare i carichi di lavoro, gestendo separatamente ogni spoke rispetto agli altri. Ogni carico di lavoro può includere diversi livelli e più subnet connesse con i servizi di bilanciamento del carico di Azure.
- È possibile implementare le reti virtuali hub-spoke in gruppi di risorse diversi e persino in sottoscrizioni diverse. Quando il peering delle reti virtuali viene eseguito in sottoscrizioni diverse, le sottoscrizioni possono essere associate agli stessi tenant di Azure Active Directory (AD) o a tenant diversi. Questo consente di decentralizzare la gestione di ogni carico di lavoro e allo stesso tempo condividere i servizi gestiti nella rete dell'hub.

![Gestione del cambiamento](./media/migrate-best-practices-networking/hub-spoke.png)
*Topologia hub-spoke*

**Altre informazioni:**

- [Informazioni su](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) una topologia hub-spoke.
- Ottenere raccomandazioni sulla rete per l'esecuzione di macchine virtuali [Windows](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm) e [Linux](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/linux-vm) di Azure.
- [Informazioni sul](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) peering reti virtuali.


## <a name="best-practice-design-subnets"></a>Procedura consigliata: progettare le subnet

Per fornire l'isolamento in una rete virtuale, segmentare la rete virtuale in una o più subnet e allocare una parte dello spazio indirizzi della rete virtuale a ogni subnet.
- È possibile creare più subnet all'interno di ogni rete virtuale.
- Per impostazione predefinita, Azure indirizza il traffico di rete tra tutte le subnet in una rete virtuale.
- Le decisioni relative alle subnet dipendono dai requisiti tecnici e aziendali.  
- Le subnet vengono create usando la notazione CIDR.
- Al momento di prendere una decisione in merito all'intervallo di rete per le subnet, è importante tenere presente che Azure mantiene cinque indirizzi IP di ogni subnet che non possono essere usati. Ad esempio, se si crea la subnet più piccola /29 (con otto indirizzi IP), Azure manterrà cinque indirizzi, quindi saranno disponibili solo tre indirizzi utilizzabili che possono essere assegnati agli host nella subnet.
- Nella maggior parte dei casi è consigliabile usare /28 come subnet più piccola.

### <a name="example"></a>Esempio

La tabella illustra un esempio di una rete virtuale con uno spazio degli indirizzi 10.245.16.0/20 segmentato in subnet, per una migrazione pianificata.

**Subnet** | **CIDR** | **Indirizzi** | **Uso**
--- | --- | --- | ---
DEV-FE-EUS2 | 10.245.16.0/22 | 1019 | Front-end/VM livello Web
DEV-APP-EUS2 | 10.245.20.0/22 | 1019 | VM livello app
DEV-DB-EUS2 | 10.245.24.0/23 | 507 | VM database

**Altre informazioni:**
- [Informazioni sulla](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#segmentation) progettazione delle subnet.
- [Informazioni su come](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) una società fittizia (Contoso) ha preparato l'infrastruttura di rete per la migrazione.


## <a name="best-practice-set-up-a-dns-server"></a>Procedura consigliata: configurare un server DNS

Per impostazione predefinita, Azure aggiunge un server DNS quando si distribuisce una rete virtuale. In questo modo è possibile creare le reti virtuali e distribuire le risorse rapidamente. Tuttavia, questo server DNS fornisce servizi solo alle risorse in tale rete virtuale. Per connettere più reti virtuali tra loro o connettersi a un server locale dalle reti virtuali, sono necessarie funzionalità aggiuntive per la risoluzione dei nomi. Ad esempio, potrebbe essere necessario usare Active Directory per risolvere i nomi DNS tra reti virtuali. A tale scopo, distribuire un proprio server DNS personalizzato in Azure.

- I server DNS in una rete virtuale possono inoltrare query DNS ai resolver ricorsivi di Azure. In questo modo è possibile risolvere i nomi host all'interno di tale rete virtuale. Ad esempio, un controller di dominio eseguito in Azure può rispondere a query DNS relative ai propri domini e inoltrare tutte le altre query ad Azure.
- L'inoltro del DNS consente alle macchine virtuali di visualizzare sia le risorse locali, tramite il controller di dominio, sia i nomi host forniti da Azure, tramite il server di inoltro. L'accesso ai resolver ricorsivi di Azure viene fornito tramite l'indirizzo IP virtuale 168.63.129.16.
- L'inoltro del DNS consente anche la risoluzione DNS tra reti virtuali e permette ai computer locali di risolvere i nomi host forniti da Azure.
    - Per risolvere il nome host di una macchina virtuale, la macchina virtuale del server DNS deve trovarsi nella stessa rete virtuale ed essere configurata per l'inoltro di query relative ai nomi host ad Azure.
    - Poiché il suffisso DNS è diverso in ogni rete virtuale, è possibile usare le regole di inoltro condizionale per inviare le query DNS alla rete virtuale corretta per la risoluzione.
- Quando si usano i propri server DNS, è possibile specificare più server DNS per ogni rete virtuale. È anche possibile specificare più server DNS per ogni interfaccia di rete (per Azure Resource Manager) o per ogni servizio cloud (per il modello di distribuzione classica).
- I server DNS specificati per un'interfaccia di rete o un servizio cloud hanno la precedenza su quelli specificati per la rete virtuale.
- Nel modello di distribuzione Azure Resource Manager è possibile specificare i server DNS per una rete virtuale e un'interfaccia di rete, ma la procedura consigliata consiste nell'usare l'impostazione solo per le reti virtuali.

    ![Server DNS](./media/migrate-best-practices-networking/dns2.png) *Server DNS per una rete virtuale*

**Altre informazioni:**
- [Informazioni sulla](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) risoluzione dei nomi quando si usa un proprio server DNS.
- [Informazioni su](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-subscriptions) regole di denominazione e restrizioni per il DNS.


## <a name="best-practice-set-up-availability-zones"></a>Procedura consigliata: Configurazione delle zone di disponibilità

Le zone di disponibilità aumentano la disponibilità elevata per proteggere le app e i dati dai guasti del data center.

- Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure.
- Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete.
- Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate.
- La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center.
- I servizi con ridondanza della zona replicano le applicazioni e i dati tra aree di disponibilità per garantire la protezione da singoli punti di errore. Con le zone di disponibilità, Azure offre un contratto di servizio con tempo di attività delle macchine virtuali del 99,99%.

    ![Zona di disponibilità](./media/migrate-best-practices-networking/availability-zone.png) *Zona di disponibilità*

- È possibile pianificare e configurare la disponibilità elevata nell'architettura di migrazione includendo le risorse di calcolo, archiviazione, rete e dati all'interno di una zona e replicandole in altre zone. I servizi di Azure che supportano le zone di disponibilità rientrano in due categorie:
    - Servizi di zona: le risorse vengono associate a una zona specifica, ad esempio macchine virtuali, dischi gestiti o indirizzi IP.
    - Servizi con ridondanza della zona: le risorse vengono replicate automaticamente tra le zone, ad esempio l'archiviazione con ridondanza della zona o il database SQL di Azure.
- È possibile distribuire un servizio di bilanciamento del carico standard di Azure con carichi di lavoro o livelli app con connessione Internet, per garantire la tolleranza di errore a livello di zona.

    ![Servizio di bilanciamento del carico](./media/migrate-best-practices-networking/load-balancer.png) *Servizio di bilanciamento del carico*

**Altre informazioni:**
-   [Ottenere una panoramica](https://docs.microsoft.com/azure/availability-zones/az-overview) delle zone di disponibilità.



## <a name="design-hybrid-cloud-networking"></a>Progettare reti cloud ibride

Per una corretta migrazione, è fondamentale connettere le reti aziendali locali ad Azure. In tal modo, verrà creata una connessione sempre attiva denominata rete cloud ibrida, in cui i servizi vengono forniti dal cloud di Azure agli utenti aziendali. Sono disponibili due opzioni per la creazione di questo tipo di rete:

- **VPN da sito a sito:** viene stabilita una connessione da sito a sito tra il dispositivo VPN locale compatibile e un gateway VPN di Azure distribuito in una rete virtuale. Qualsiasi risorsa locale autorizzata può accedere alle reti virtuali. Le comunicazioni da sito a sito vengono inviate tramite un tunnel crittografato via Internet. 
- **Azure ExpressRoute:** viene stabilita una connessione Azure ExpressRoute tra la rete locale e Azure tramite un partner ExpressRoute. Questa connessione è privata e il traffico non viene inviato via Internet.

**Altre informazioni:**

- [Altre informazioni](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vpn) sulla rete cloud ibrida.

## <a name="best-practice-implement-a-highly-available-site-to-site-vpn"></a>Procedura consigliata: implementare una VPN da sito a sito a disponibilità elevata

Per implementare una VPN da sito a sito, configurare un gateway VPN in Azure.
- Un gateway VPN è un tipo specifico di gateway di rete virtuale, usato per inviare traffico crittografato tra una rete virtuale di Azure e una posizione locale attraverso la rete Internet pubblica.
- È possibile usare un gateway VPN anche per inviare traffico crittografato tra le reti virtuali di Azure sulla rete Microsoft.
- Ogni rete virtuale può avere un solo gateway VPN.
- È possibile creare più connessioni allo stesso gateway VPN. Quando si creano più connessioni, tutti i tunnel VPN condividono la larghezza di banda disponibile per il gateway.
- Ogni gateway VPN di Azure è costituito da due istanze in una configurazione di tipo attivo-standby.
    - In caso di interruzione imprevista o di manutenzione pianificata nell'istanza attiva, viene eseguito il failover e l'istanza di standby assume automaticamente il controllo e riprende la connessione VPN da sito a sito o da rete virtuale a rete virtuale. 
    - Il passaggio causa una breve interruzione.
    - In caso di manutenzione pianificata, la connettività dovrebbe essere ripristinata entro 10-15 secondi.
    - Per problemi imprevisti, il ripristino della connessione richiederà più tempo, approssimativamente da 1 minuto a 1 minuto e mezzo nel peggiore dei casi.
    - Le connessioni client VPN da punto a sito (P2S) al gateway verranno interrotte e gli utenti dovranno riconnettersi dai computer client.

Per configurare una VPN da sito a sito, procedere nel modo seguente:
 - È necessaria una rete virtuale con un intervallo di indirizzi che non si sovrapponga alla rete locale a cui si connetterà la VPN.
 - Creare una subnet del gateway nella rete.
 - Creare un gateway VPN, specificare il tipo di gateway (VPN) e specificare se il gateway è basato su criteri o basato su route. È consigliata una VPN basata su route perché ha una capacità superiore e garantirà la compatibilità anche in futuro.
 - Creare un gateway di rete locale in locale e configurare il dispositivo VPN locale. 
 - Creare una connessione VPN da sito a sito di failover tra il gateway della rete virtuale e il dispositivo in locale. L'uso di una VPN basata su route consente connessioni attivo/passivo o attivo/attivo ad Azure. Una VPN basata su route inoltre supporta connessioni da sito a sito (da qualsiasi computer) e da punto a sito (da un singolo computer) contemporaneamente.
 - Specificare lo SKU del gateway da usare. Questo elemento varia a seconda dei requisiti del carico di lavoro, delle velocità effettive, delle funzionalità e dei contratti di servizio.
 - Border Gateway Protocol (BGP) è una funzionalità facoltativa che è possibile usare con Azure ExpressRoute e i gateway VPN basati su route per propagare le route BGP locali alle reti virtuali.

![VPN](./media/migrate-best-practices-networking/vpn.png)
*VPN da sito a sito*
 
**Altre informazioni:**

- [Esaminare](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) i dispositivi VPN locali compatibili.
- [Ottenere una panoramica](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) dei gateway VPN.
- [Informazioni](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable) sulle connessioni VPN a disponibilità elevata.
- [Informazioni](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) sulla pianificazione e la progettazione di un gateway VPN.
- [Esaminare](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) le impostazioni del gateway VPN.
- [Esaminare](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) gli SKU del gateway.
- [Informazioni sulla](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview) configurazione di BGP con i gateway VPN di Azure.


### <a name="best-practice-configure-a-gateway-for-vpn-gateways"></a>Procedura consigliata: configurare un gateway per i gateway VPN

Quando si crea un gateway VPN in Azure, è necessario usare una subnet speciale denominata GatewaySubnet. Al momento della creazione di questa subnet, tenere presenti le seguenti procedure consigliate:

- La lunghezza massima del prefisso della subnet del gateway è di 29 (ad esempio, 10.119.255.248/29). La raccomandazione attuale è usare una lunghezza del prefisso pari a 27 (ad esempio, 10.119.255.224/27).
- Quando si definisce lo spazio di indirizzi della subnet del gateway, usare l'ultima parte dello spazio di indirizzi della rete virtuale.
- Quando si usa la subnet GatewaySubnet di Azure, non distribuire macchine virtuali o altri dispositivi, ad esempio il gateway applicazione, nella subnet del gateway.
- Non assegnare un gruppo di sicurezza di rete (NSG) a questa subnet, in quanto verrebbe causata l'interruzione del funzionamento del gateway.

**Altre informazioni:**
- [Usare questo strumento](https://gallery.technet.microsoft.com/scriptcenter/Address-prefix-calculator-a94b6eed) per determinare lo spazio indirizzi IP.

## <a name="best-practice-implement-azure-virtual-wan-for-branch-offices"></a>Procedura consigliata: implementare la rete WAN virtuale di Azure per le succursali

Per più connessioni VPN, la rete WAN virtuale di Azure è un servizio di rete che offre connettività tra succursali ottimizzata e automatizzata tramite Azure.
- La rete WAN virtuale consente di connettere e configurare i dispositivi delle succursali per la comunicazione con Azure. Questa operazione può essere eseguita manualmente oppure usando i dispositivi di provider preferiti tramite un partner di rete WAN virtuale.
- L'uso di dispositivi di provider preferiti consente la facilità d'uso, semplifica la connettività e agevola la gestione della configurazione.
- Il dashboard predefinito della rete WAN di Azure offre informazioni dettagliate immediate per la risoluzione dei problemi, che consentono di risparmiare tempo e di monitorare con facilità la connettività da sito a sito su larga scala. 

**Altre informazioni:**
[Informazioni sulla](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about) rete WAN virtuale di Azure.

### <a name="best-practice-implement-expressroute-for-mission-critical-connections"></a>Procedura consigliata: implementare ExpressRoute per le connessioni di importanza cruciale

Il servizio Azure ExpressRoute consente di estendere l'infrastruttura locale in Microsoft Cloud tramite la creazione di connessioni private tra il data center virtuale di Azure e le reti locali.
- Le connessioni ExpressRoute possono essere basate su una rete any-to-any (IP VPN), una rete Ethernet da punto a punto o un provider di connettività. Non passano dalla rete Internet pubblica.
- Le connessioni ExpressRoute offrono un livello di sicurezza superiore, maggiore affidabilità e velocità più elevate (fino a 10 Gbps), oltre a una latenza coerente.
- ExpressRoute è utile per i data center virtuali, perché i clienti possono sfruttare i vantaggi offerti dalle regole di conformità associate alle connessioni private.
- Con ExpressRoute Direct è possibile connettersi direttamente ai router Microsoft a 100 Gbps, per maggiori esigenze di larghezza di banda.
- ExpressRoute usa il protocollo BGP per lo scambio delle route tra le reti locali, le istanze di Azure e gli indirizzi pubblici Microsoft.

Per distribuire le connessioni ExpressRoute, in genere è necessario un provider di servizi ExpressRoute. Per iniziare rapidamente, in genere viene usata prima una VPN da sito a sito per stabilire la connettività tra il data center virtuale e le risorse locali e quindi viene eseguita la migrazione alla connessione ExpressRoute, dopo aver stabilito un'interconnessione fisica con il provider di servizi.

**Altre informazioni:**
- [Ottenere una panoramica](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) di ExpressRoute.
- [Informazioni su](https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about) ExpressRoute Direct.

### <a name="best-practice-optimize-expressroute-routing-with-bgp-communities"></a>Procedura consigliata: ottimizzare il routing ExpressRoute con le community BGP

In presenza di più circuiti ExpressRoute sono disponibili più percorsi per connettersi a Microsoft. Il routing può quindi risultare non ottimale ed è possibile che il traffico usi un percorso più lungo per raggiungere Microsoft e da Microsoft la rete del cliente. Più lungo è il percorso di rete, maggiore sarà la latenza, che ha un impatto diretto sull'esperienza utente e sulle prestazioni dell'app.

#### <a name="example"></a>Esempio

Di seguito viene illustrato un esempio.

- Si supponga di avere due sedi negli Stati Uniti: una a Los Angeles e una a New York.
- Gli uffici sono connessi tramite una rete WAN, che può essere la propria rete backbone o la VPN IP del provider di servizi.
- Sono disponibili due circuiti ExpressRoute, uno negli Stati Uniti occidentali e uno negli Stati Uniti orientali, anch'essi connessi tramite la rete WAN. Naturalmente, per la connessione alla rete Microsoft esistono due percorsi.


 
**Problema** Si supponga ora di avere una distribuzione di Azure, ad esempio il servizio app di Azure, negli Stati Uniti occidentali e negli Stati Uniti orientali.
- Per un'esperienza ottimale, si vuole che gli utenti in ogni ufficio accedano ai servizi di Azure più vicini.
- Di conseguenza, si vogliono connettere gli utenti di Los Angeles alla distribuzione di Azure negli Stati Uniti occidentali e gli utenti di New York alla distribuzione di Azure negli Stati Uniti orientali.
- Questa soluzione funziona per gli utenti della costa orientale, ma non per quelli della costa occidentale. Il problema è il seguente:
    - In ogni circuito ExpressRoute vengono pubblicati sia il prefisso di Azure negli Stati Uniti orientali (23.100.0.0/16) che quello negli Stati Uniti occidentali (13.100.0.0/16).
    - In mancanza di informazioni sull'area a cui è relativo ciascun prefisso, i prefissi non vengono trattati in modo diverso.
    - La rete WAN potrebbe presupporre che entrambi i prefissi siano più vicini agli Stati Uniti orientali che agli Stati Uniti occidentali e pertanto indirizzare gli utenti di entrambi gli uffici al circuito ExpressRoute negli Stati Uniti orientali, fornendo un'esperienza non ottimale per gli utenti dell'ufficio di Los Angeles.

![VPN](./media/migrate-best-practices-networking/bgp1.png)
*Connessione non ottimizzata di community BGP*

**Soluzione**

Per ottimizzare il routing per gli utenti di entrambi gli uffici, è necessario sapere quale prefisso è relativo ad Azure negli Stati Uniti occidentali e quale ad Azure negli Stati Uniti orientali. Queste informazioni possono essere codificate con i valori delle community BGP.
- È possibile assegnare un valore di community BGP univoco ad ogni area di Azure, ad esempio 12076:51004 per gli Stati Uniti orientali e 12076:51006 per gli Stati Uniti occidentali.
- Ora che è chiara l'area di Azure a cui appartiene il prefisso, è possibile configurare un circuito ExpressRoute preferito.
- Poiché si usa BGP per lo scambio di informazioni di routing, è possibile usare il valore di BGP relativo alla preferenza locale per determinare il routing.
- In questo esempio viene assegnato un valore di preferenza locale 13.100.0.0/16 più alto negli Stati Uniti occidentali di quello negli Stati Uniti orientali e, in modo analogo, un valore di preferenza locale 23.100.0.0/16 più alto negli Stati Uniti orientali rispetto a quello negli Stati Uniti occidentali. 
- Questa configurazione assicura che, quando sono disponibili entrambi i percorsi per la connessione a Microsoft, gli utenti di Los Angeles si connetteranno ad Azure negli Stati Uniti occidentali usando il circuito occidentale, mentre gli utenti di New York si connetteranno ad Azure negli Stati Uniti orientali tramite il circuito orientale. Il routing è ottimizzato su entrambi i lati.

![VPN](./media/migrate-best-practices-networking/bgp2.png)
*Connessione ottimizzata di community BGP*


**Altre informazioni:**
- [Informazioni su](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing) come ottimizzare il routing

## <a name="securing-vnets"></a>Protezione delle reti virtuali

La responsabilità per la protezione delle reti virtuali è condivisa tra Microsoft e il cliente. Microsoft offre molte delle funzionalità di rete, nonché i servizi che aiutano a proteggere le risorse. Quando si progetta la sicurezza per le reti virtuali, vi sono numerose procedure consigliate da seguire, tra cui l'implementazione di una rete perimetrale, l'uso di filtri e gruppi di sicurezza, la protezione dell'accesso alle risorse e agli indirizzi IP e l'implementazione della protezione dagli attacchi.

**Altre informazioni:**

- [Ottenere una panoramica](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices) delle procedure consigliate per la sicurezza di rete.
- [Informazioni su come](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#security) progettare reti sicure.

## <a name="best-practice-implement-an-azure-perimeter-network"></a>Procedura consigliata: implementare una rete perimetrale di Azure

Nonostante gli importanti investimenti Microsoft nella protezione dell'infrastruttura cloud, anche i clienti devono proteggere i propri servizi cloud e i gruppi di risorse. Un approccio a più livelli per la sicurezza offre la difesa migliore. L'implementazione di una rete perimetrale è una parte importante di questa strategia di difesa.

- Una rete perimetrale protegge le risorse di rete interne da una rete non attendibile. 
- Si tratta del livello più esterno, esposto a Internet. In genere risiede tra Internet e l'infrastruttura aziendale, solitamente con qualche tipo di protezione su entrambi i lati. 
- In una tipica topologia di rete aziendale, l'infrastruttura di base viene protetta in modo rilevante nelle aree perimetrali, con più livelli di dispositivi di sicurezza. Il limite di ogni livello è costituito da dispositivi e punti di applicazione dei criteri.
- Ogni livello può includere una combinazione di soluzioni per la sicurezza della rete, come firewall, prevenzione di attacchi DDoS (Distributed Denial of Service), sistemi di identificazione delle intrusioni o di protezione (IDS/IPS) e dispositivi VPN.
- L'applicazione dei criteri nella rete perimetrale può essere effettuata tramite criteri del firewall, elenchi di controllo di accesso (ACL) o routing specifico.
- Il traffico in ingresso ricevuto da Internet viene intercettato e gestito da una combinazione di soluzioni di difesa per bloccare gli attacchi e il traffico dannoso, consentendo al tempo stesso alle richieste legittime di accedere alla rete.
- Il traffico in ingresso può essere instradato direttamente verso le risorse della rete perimetrale. La risorsa della rete perimetrale può quindi comunicare con altre risorse più interne della rete, inoltrando il traffico nella rete dopo la convalida.

La figura seguente illustra un esempio di una rete perimetrale con subnet singola all'interno di una rete aziendale e due limiti di sicurezza.

![VPN](./media/migrate-best-practices-networking/perimeter.png)
*Distribuzione della rete perimetrale*

**Altre informazioni:**
- [Informazioni sulla](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) distribuzione di una rete perimetrale tra Azure e il data center locale.


## <a name="best-practice-filter-vnet-traffic-with-nsgs"></a>Procedura consigliata: filtrare il traffico della rete virtuale con i gruppi di sicurezza di rete

I gruppi di sicurezza di rete (NSG) contengono più regole di sicurezza in ingresso e in uscita che filtrano il traffico da e verso le risorse. Il filtro può essere applicato in base all'indirizzo IP, alla porta e al protocollo di origine e di destinazione. 
- I gruppi di sicurezza di rete contengono regole di sicurezza che consentono o negano il traffico di rete in ingresso o il traffico di rete in uscita rispettivamente verso o da diversi tipi di risorse di Azure. Per ogni regola è possibile specificare l'origine e la destinazione, la porta e il protocollo.
- Le regole NSG vengono valutate in base alla priorità mediante le informazioni a cinque tuple (origine, porta di origine, destinazione, porta di destinazione e protocollo) per consentire o negare il traffico.
- Viene creato un record di flusso per le connessioni esistenti. La comunicazione è consentita o negata in base allo stato di connessione del record di flusso.
- Un record di flusso consente a un gruppo di sicurezza di rete di avere uno stato. Ad esempio, se si specifica una regola di sicurezza in uscita per qualsiasi indirizzo sulla porta 80, non è necessario specificare una regola di sicurezza in ingresso per rispondere al traffico in uscita. È necessario specificare una regola di sicurezza in ingresso solo se la comunicazione viene avviata all'esterno.
- Questa considerazione si applica anche al contrario. Se il traffico in ingresso è consentito su una porta, non è necessario specificare una regola di sicurezza in uscita per rispondere al traffico sulla porta.
- Le connessioni esistenti non vengono interrotte quando si rimuove una regola di sicurezza che abilita il flusso. I flussi di traffico vengono interrotti quando le connessioni vengono arrestate e non è presente alcun flusso di traffico in entrambe le direzioni almeno per alcuni minuti.
- Quando si creano i gruppi di sicurezza di rete, crearne il minor numero possibile, ma comunque tutti quelli necessari.

### <a name="best-practice-secure-northsouth-and-eastwest-traffic"></a>Procedura consigliata: proteggere il traffico nord/sud ed est/ovest

Quando si proteggono le reti virtuali, è importante prendere in considerazione i vettori di attacco.
- L'uso dei gruppi di sicurezza di rete delle subnet semplifica l'ambiente, ma consente di proteggere solo il traffico nella subnet. Questo è denominato traffico nord/sud.
- Il traffico tra le macchine virtuali nella stessa subnet è denominato traffico est/ovest.
- È importante usare entrambe le forme di protezione. In tal modo, se un pirata informatico ottiene l'accesso dall'esterno, verrà fermato durante il tentativo di attaccare i computer che si trovano nella stessa subnet.

### <a name="use-service-tags-on-nsgs"></a>Usare i tag di servizio nei gruppi di sicurezza di rete

Un tag di servizio rappresenta un gruppo di prefissi degli indirizzi IP. L'uso di un tag di servizio consente di ridurre la complessità durante la creazione delle regole NSG.
- È possibile usare tag di servizio invece di indirizzi IP specifici durante la creazione delle regole.
- I prefissi di indirizzo associati a un tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.
- Non è possibile creare tag di servizio personalizzati, né specificare gli indirizzi IP inclusi in un tag.

I tag di servizio eliminano le attività manuali dall'assegnazione di una regola ai gruppi di servizi di Azure. Se ad esempio si vuole consentire a una subnet di rete virtuale che contiene server Web di accedere a un database SQL di Azure, è possibile creare una regola in uscita per la porta 1433 e usare il tag di servizio **Sql**.
- Questo tag **Sql** identifica i prefissi di indirizzo dei servizi Database SQL di Azure e Azure SQL Data Warehouse.
- Se si specifica **Sql** come valore, verrà consentito o impedito il traffico verso SQL.
- Se si vuole consentire l'accesso a SQL solo in una determinata **area**, è possibile specificare tale area. Se ad esempio si vuole consentire l'accesso a Database SQL di Azure solo nell'area Stati Uniti orientali, è possibile specificare **Sql.EastUS** come tag di servizio.
- Il tag rappresenta il servizio, ma non istanze specifiche del servizio. Ad esempio, il tag rappresenta il servizio Database SQL di Azure, ma non un particolare server o database SQL.
- Tutti i prefissi di indirizzo rappresentati da questo tag sono rappresentati anche dal tag **Internet**.


**Altre informazioni:**

- [Informazioni sui](https://docs.microsoft.com/azure/virtual-network/security-overview) gruppi di sicurezza di rete.
- [Esaminare](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) i tag di servizio disponibili per i gruppi di sicurezza di rete.


## <a name="best-practice-use-application-security-groups"></a>Procedura consigliata: usare i gruppi di sicurezza delle applicazioni

I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza della rete come un'estensione naturale della struttura di un'app.

- È possibile raggruppare le macchine virtuali e definire i criteri di sicurezza di rete in base ai gruppi di sicurezza delle applicazioni.
- I gruppi di sicurezza delle applicazioni consentono di riutilizzare i criteri di sicurezza su larga scala senza gestire manualmente indirizzi IP espliciti.
- I gruppi di sicurezza delle applicazioni gestiscono la complessità degli indirizzi IP espliciti e di più set di regole, consentendo all'utente di concentrarsi sulla logica di business. 

### <a name="example"></a>Esempio

![Gruppo di sicurezza delle applicazioni](./media/migrate-best-practices-networking/asg.png)
*Esempio di gruppo di sicurezza delle applicazioni*

**Interfaccia di rete** | **Gruppo di sicurezza delle applicazioni**
--- | ---
NIC1 | AsgWeb
NIC2 | AsgWeb
NIC3 | AsgLogic
NIC4 | AsgDb 

- In questo esempio, ogni interfaccia di rete appartiene a un solo gruppo di sicurezza delle applicazioni, ma di fatto un'interfaccia può appartenere a più gruppi, in conformità con i limiti di Azure.
- Nessuna delle interfacce di rete ha un gruppo di sicurezza di rete associato. NSG1 è associato a entrambe le subnet e contiene le regole seguenti.

    **Nome regola** | **Scopo** | **Dettagli**
    --- | --- | ---   
    Allow-HTTP-Inbound-Internet | Consentire il traffico da Internet verso i server Web. Il traffico in ingresso da Internet viene negato dalla regola di sicurezza predefinita DenyAllInbound, pertanto non sono necessarie regole aggiuntive per i gruppi di sicurezza delle applicazioni AsgLogic o AsgDb. | Priorità: 100<br/><br/> Origine: Internet<br/><br/> Porta di origine: *<br/><br/> Destinazione: AsgWeb<br/><br/> Porta di destinazione: 80<br/><br/> Protocollo: TCP<br/><br/> Accesso: Consenti.
    Deny-Database-All | La regola di sicurezza predefinita AllowVNetInBound consente tutte le comunicazioni tra le risorse nella stessa rete virtuale, pertanto questa regola è necessaria per negare il traffico da tutte le risorse. | Priorità: 120<br/><br/> Origine: *<br/><br/> Porta di origine: *<br/><br/> Destinazione: AsgDb<br/><br/> Porta di destinazione: 1433<br/><br/> Protocollo: Tutti<br/><br/> Accesso: Nega.
    Allow-Database-BusinessLogic | Consentire il traffico dal gruppo di sicurezza delle applicazioni AsgLogic al gruppo di sicurezza delle applicazioni AsgDb. Questa regola ha una priorità superiore a quella della regola Deny-Database-All e viene elaborata prima di tale regola, quindi il traffico dal gruppo di sicurezza delle applicazioni AsgLogic è consentito, mentre tutto il resto del traffico è bloccato. | Priorità: 110<br/><br/> Origine: AsgLogic<br/><br/> Porta di origine: *<br/><br/> Destinazione: AsgDb<br/><br/> Porta di destinazione: 1433<br/><br/> Protocollo: TCP<br/><br/> Accesso: Consenti.

- Le regole che specificano un gruppo di sicurezza delle applicazioni come origine o destinazione vengono applicate solo alle interfacce di rete che sono membri del gruppo di sicurezza delle applicazioni. Se l'interfaccia di rete non è membro di un gruppo di sicurezza delle applicazioni, la regola non viene applicata all'interfaccia di rete, anche se il gruppo di sicurezza di rete è associato alla subnet.

**Altre informazioni:**

- [Informazioni sui](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) gruppi di sicurezza delle applicazioni.


### <a name="best-practice-secure-access-to-paas-using-vnet-service-endpoints"></a>Procedura consigliata: proteggere l'accesso a PaaS con gli endpoint del servizio di rete virtuale

Gli endpoint del servizio di rete virtuale estendono lo spazio di indirizzi privato della rete virtuale e l'identità ai servizi di Azure tramite una connessione diretta.

- Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure.
- A causa della possibile sovrapposizione dello spazio di indirizzi privato della rete virtuale, questo non può essere usato per identificare in modo univoco il traffico proveniente da una rete virtuale.
- Dopo aver abilitato gli endpoint di servizio nella rete virtuale, è possibile proteggere le risorse del servizio di Azure aggiungendo una regola della rete virtuale alle risorse del servizio. In questo modo si ottiene una maggiore sicurezza perché si impedisce completamente l'accesso alle risorse da Internet pubblico, consentendo solo il traffico dalla rete virtuale.

![Endpoint del servizio](./media/migrate-best-practices-networking/endpoint.png)
*Endpoint del servizio*

**Altre informazioni:**

- [Informazioni sugli](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) endpoint del servizio Rete virtuale.


## <a name="best-practice-control-public-ip-addresses"></a>Procedura consigliata: controllare gli indirizzi IP pubblici

Gli indirizzi IP pubblici in Azure possono essere associati a macchine virtuali, servizi di bilanciamento del carico, gateway applicazione e gateway VPN.

- Gli indirizzi IP pubblici consentono alle risorse Internet di comunicare in ingresso con le risorse di Azure e alle risorse di Azure di comunicare in uscita con Internet.
- Gli indirizzi IP pubblici possono essere creati con uno SKU di base o standard, con alcune differenze. Gli SKU standard possono essere assegnati a qualsiasi servizio, ma in genere sono configurati per macchine virtuali, servizi di bilanciamento del carico e gateway applicazione.
- È importante notare che per un indirizzo IP pubblico di base non viene configurato automaticamente un gruppo di sicurezza di rete. È necessario configurarlo autonomamente e assegnare le regole per il controllo dell'accesso. Gli indirizzi IP con uno SKU standard dispongono per impostazione predefinita di un gruppo di sicurezza di rete e di regole assegnate.
- Come procedura consigliata, le macchine virtuali non devono essere configurate con un indirizzo IP pubblico.
    - Se è necessario aprire una porta, deve essere solo per i servizi Web, ad esempio la porta 80 o 443.
    - Le porte di gestione remota standard come SSH (22) e RDP (3389) devono essere impostate su Nega, così come tutte le altre porte, tramite i gruppi di sicurezza di rete.
- Una procedura consigliata consiste nel posizionare le macchine virtuali dietro un gateway applicazione o un servizio di bilanciamento del carico di Azure. Se è necessario l'accesso alle porte di gestione remota, è possibile usare l'accesso JIT alle macchine virtuali nel Centro sicurezza di Azure.

**Altre informazioni:**

- [Informazioni sugli](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) indirizzi IP pubblici in Azure.
- [Informazioni](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) sull'accesso JIT alle macchine virtuali nel Centro sicurezza di Azure.


## <a name="leverage-azure-security-features-for-networking"></a>Sfruttare le funzionalità di sicurezza di Azure per la rete

Azure offre funzionalità di sicurezza della piattaforma che sono facili da usare e forniscono contromisure avanzate agli attacchi di rete più comuni. Tali funzionalità includono Firewall di Azure, Web application firewall e Network Watcher.

## <a name="best-practice-deploy-azure-firewall"></a>Procedura consigliata: distribuire Firewall di Azure

Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti.

![Endpoint del servizio](./media/migrate-best-practices-networking/firewall.png)
*Firewall di Azure*

- In Firewall di Azure è possibile creare, applicare e registrare criteri di connettività di applicazione e di rete in modo centralizzato tra le sottoscrizioni e le reti virtuali.
- Firewall di Azure usa un indirizzo IP pubblico statico per le risorse della rete virtuale, consentendo ai firewall esterni di identificare il traffico proveniente dalla rete virtuale.
- Firewall di Azure è completamente integrato con Monitoraggio di Azure per la registrazione e l'analisi.
- Come procedura consigliata durante la creazione delle regole di Firewall di Azure, usare i tag FQDN per creare le regole.
    - Un tag FQDN rappresenta un gruppo di nomi di dominio completi (FQDN) associati a servizi Microsoft noti.
    - È possibile usare un tag FQDN per consentire il necessario passaggio del traffico in uscita attraverso il firewall.
- Ad esempio, per consentire manualmente il passaggio del traffico di Windows Update attraverso il firewall, sarebbe necessario creare più regole dell'applicazione. Usando i tag FQDN, è sufficiente creare una regola dell'applicazione e includere il tag di Windows Update. Con questa regola, il traffico di rete verso gli endpoint di Microsoft Windows Update può attraversare il firewall.

**Altre informazioni:**

- [Ottenere una panoramica](https://docs.microsoft.com/azure/firewall/overview) del Firewall di Azure.
- [Informazioni sui](https://docs.microsoft.com/azure/firewall/fqdn-tags) tag FQDN.


## <a name="best-practice-deploy-azure-web-application-firewall-waf"></a>Procedura consigliata: distribuire Web application firewall (WAF) di Azure

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni vulnerabilità note. Alcuni esempi sono gli attacchi SQL injection e gli attacchi di tipo cross-site scripting. Impedire questo tipo di attacchi nel codice dell'applicazione può risultare un'operazione complessa e potrebbe richiedere una manutenzione rigorosa, l'applicazione di patch e il monitoraggio a più livelli della topologia dell'applicazione. Un Web application firewall centralizzato semplifica notevolmente la gestione della sicurezza e consente agli amministratori delle app di garantire la protezione contro le minacce o le intrusioni. Un Web application firewall è in grado di reagire più velocemente alle minacce per la sicurezza tramite l'applicazione di patch per le vulnerabilità note in una posizione centrale, invece di proteggere le singole applicazioni Web. È possibile convertire facilmente i gateway applicazione esistenti in un gateway applicazione con Web application firewall.

Web application firewall (WAF) di Azure è una funzionalità del gateway applicazione di Azure.
- WAF fornisce la protezione centralizzata delle applicazioni Web da exploit e vulnerabilità comuni.
- WAF garantisce la protezione senza alcuna modifica al codice di back-end.
- Può proteggere contemporaneamente più app Web con un gateway applicazione
- WAF è integrato con il Centro sicurezza di Azure.
- È possibile personalizzare le regole e i gruppi di regole di WAF in base ai requisiti dell'app.
- Come procedura consigliata, usare un Web application firewall per tutte le app esposte al Web, incluse le app in macchine virtuali di Azure o come un servizio app di Azure.

**Altre informazioni:**
- [Informazioni su](https://docs.microsoft.com/azure/application-gateway/waf-overview) WAF.
- [Esaminare](https://docs.microsoft.com/azure/application-gateway/application-gateway-waf-configuration) le limitazioni e le esclusioni di WAF.


## <a name="best-practice-implement-azure-network-watcher"></a>Procedura consigliata: implementare Azure Network Watcher

Azure Network Watcher offre strumenti per monitorare le risorse e le comunicazioni in una rete virtuale di Azure. È ad esempio possibile monitorare le comunicazioni tra una macchina virtuale e un endpoint come un'altra VM o un FQDN, visualizzare le risorse e le relazioni tra le risorse in una rete virtuale o diagnosticare i problemi del traffico di rete.

![Network Watcher](./media/migrate-best-practices-networking/network-watcher.png)
*Network Watcher*

- Con Network Watcher è possibile monitorare e diagnosticare i problemi di rete senza accedere alle macchine virtuali.
- È possibile attivare l'acquisizione dei pacchetti impostando gli avvisi e ottenere l'accesso alle informazioni sulle prestazioni in tempo reale a livello del pacchetto. Quando viene rilevato un problema, è possibile esaminarlo in dettaglio.
- Come procedura consigliata, usare Network Watcher per esaminare i log dei flussi dei gruppi di sicurezza di rete.
    - I log dei flussi dei gruppi di sicurezza di rete in Network Watcher consentono di visualizzare le informazioni sul traffico IP in ingresso e in uscita tramite un gruppo di sicurezza di rete.
    - I log dei flussi sono scritti in formato JSON
    - e mostrano i flussi in ingresso e in uscita per ogni regola, l'interfaccia di rete (NIC) a cui si applica il flusso, informazioni a 5 tuple relative al flusso (indirizzo IP di origine/destinazione, porta di origine/destinazione e protocollo) e se il traffico è consentito o meno.

**Altre informazioni:**

- [Ottenere una panoramica](https://docs.microsoft.com/azure/network-watcher) di Network Watcher.
- [Informazioni](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) sui log dei flussi dei gruppi di sicurezza di rete.

## <a name="use-partner-tools-in-the-azure-marketplace"></a>Usare gli strumenti dei partner in Azure Marketplace

Per le topologie di rete più complesse, è possibile usare i prodotti per la sicurezza dei partner Microsoft, in particolare le appliance virtuali di rete (NVA).

- Un'appliance virtuale di rete è una macchina virtuale che esegue una funzione di rete, ad esempio un firewall, un'ottimizzazione WAN o un'altra funzione di rete.
- Le appliance virtuali di rete rafforzano la sicurezza e le funzioni di rete della rete virtuale. Possono essere distribuite per firewall a disponibilità elevata, prevenzione delle intrusioni, rilevamento delle intrusioni, Web application firewall (WAF), ottimizzazione WAN, routing, bilanciamento del carico, VPN, gestione dei certificati, Active Directory e autenticazione a più fattori.
- Le appliance virtuali di rete sono disponibili da diversi fornitori in [Azure Marketplace](https://azuremarketplace.microsoft.com/). 
 

## <a name="best-practice-implement-firewalls-and-nvas-in-hub-networks"></a>Procedura consigliata: implementare firewall e appliance virtuali di rete nelle reti hub

Nell'hub la rete perimetrale (con accesso a Internet) in genere viene gestita tramite un Firewall di Azure, una farm di firewall o Web application firewall (WAF). Prendere in considerazione i confronti seguenti.

**Tipo di firewall** | **Dettagli**
--- | ---
Web application firewall | Le app Web sono comuni e tendono a essere soggette a vulnerabilità e potenziali exploit.<br/><br/> I Web application firewall sono progettati per rilevare gli attacchi contro le applicazioni Web (HTTP/HTTPS), in modo più specifico rispetto a un firewall generico.<br/><br/> Rispetto alla tradizionale tecnologia firewall, i Web application firewall hanno un set di funzionalità specifiche per proteggere i server Web interni dalle minacce.
Firewall di Azure | Come le farm di firewall delle appliance virtuali di rete, Firewall di Azure usa un meccanismo di amministrazione comune e un set di regole di sicurezza per proteggere i carichi di lavoro ospitati nelle reti spoke e per controllare l'accesso alle reti locali.<br/><br/> Firewall di Azure dispone di funzionalità integrate per la scalabilità.
Firewall delle appliance virtuali di rete | Come Firewall di Azure, le farm di firewall delle appliance virtuali di rete dispongono di un meccanismo di amministrazione comune e di un set di regole di sicurezza per proteggere i carichi di lavoro ospitati nelle reti spoke e per controllare l'accesso alle reti locali.<br/><br/> I firewall delle appliance virtuali di rete possono essere ridimensionati manualmente dietro un servizio di bilanciamento del carico.<br/><br/> Sebbene un firewall delle appliance virtuali di rete abbia un software meno specializzato rispetto a un WAF, ha un ambito dell'applicazione più ampio per filtrare ed esaminare ogni tipo di traffico in uscita o in ingresso.<br/><br/> Se si vuole usare appliance virtuali di rete, è possibile trovarle in Azure Marketplace.

È consigliabile usare un set di firewall di Azure (o di appliance virtuali di rete) per il traffico che ha origine in Internet e un altro per il traffico che ha origine in locale.
- L'uso di un solo set di firewall per entrambi i tipi di traffico è un rischio per la sicurezza, perché non viene creato un perimetro di sicurezza tra i due set di traffico.
- L'uso di livelli di firewall separati riduce la complessità del controllo delle regole di sicurezza e indica chiaramente la corrispondenza tra le regole e le richieste di rete in ingresso.

**Altre informazioni:**
- [Informazioni](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) sull'uso di appliance virtuali di rete in una rete virtuale di Azure.

## <a name="next-steps"></a>Passaggi successivi 

Esaminare altre procedure consigliate:

- [Procedure consigliate](migrate-best-practices-security-management.md) per la sicurezza e la gestione dopo la migrazione.
- [Procedure consigliate](migrate-best-practices-costs.md) per la gestione dei costi dopo la migrazione.
