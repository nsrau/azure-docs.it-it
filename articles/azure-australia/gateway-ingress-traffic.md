---
title: Controllo del traffico in ingresso in Azure Australia
description: Guida per il controllo del traffico in ingresso in Azure Australia per soddisfare i requisiti del governo australiano per i gateway Internet sicuri
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 808a615885129af1be9b7fdcdb64d5a8c5a25e40
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571653"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Controllo del traffico in ingresso in Azure Australia

Un elemento principale della protezione dei sistemi ICT è il controllo del traffico di rete. Il traffico deve essere limitato solo a quello necessario perché un sistema funzioni per ridurre il rischio di compromissione.

Questa guida fornisce informazioni dettagliate su come funziona il traffico di rete in ingresso in Azure e suggerimenti per l'implementazione dei controlli di sicurezza di rete per un sistema connesso a Internet.

I controlli di rete sono allineati con le linee guida per i consumer ACSC (Australian Cyber Security Centre) e lo scopo del manuale di sicurezza delle informazioni di ACSC (ISM).

## <a name="requirements"></a>Requisiti

I requisiti di sicurezza generali per i sistemi Commonwealth sono definiti in ISM. Per aiutare le entità Commonwealth nell'implementazione della sicurezza di rete, acsc [ha pubblicato ACSC Protect: Implementazione della segmentazione e della](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm)separazione della rete e per facilitare la protezione dei sistemi negli ambienti cloud, acsc ha pubblicato la [sicurezza del cloud computing per i tenant](https://www.acsc.gov.au/publications/protect/Cloud_Computing_Security_for_Tenants.pdf).

Queste guide descrivono il contesto per l'implementazione della sicurezza di rete e il controllo del traffico e includono consigli pratici per la progettazione e la configurazione della rete.

Il Microsoft [Cloud Computing Security per i tenant di Microsoft Azure](https://aka.ms/au-irap) guida nella pagina australiano del portale di trust dei servizi mette in evidenza tecnologie Microsoft specifiche che consentono di soddisfare i consigli nelle pubblicazioni ACSC.

I seguenti requisiti chiave, identificati nelle pubblicazioni da ACSC, sono importanti per il controllo del traffico in ingresso in Azure:

|Descrizione|Source|
|---|---|
|**Implementare la segmentazione e la separazione della rete, ad esempio l'architettura a più livelli, usando firewall basati su host e i controlli di accesso alla rete del CSP per limitare la connettività di rete delle macchine virtuali in ingresso e in uscita solo alle porte/protocolli richiesti.**| _Cloud computing per tenant_|
|**Implementare una larghezza di banda sufficientemente elevata, bassa latenza, connettività di rete affidabile** tra il tenant (inclusi gli utenti remoti del tenant) e il servizio cloud per soddisfare i requisiti di disponibilità del tenant  | _Cloud computing per tenant_|
|**Applicare tecnologie non solo al livello di rete**. Ogni host e rete deve essere segmentato e separato, laddove possibile, al livello più basso che può essere praticamente gestito. Nella maggior parte dei casi, la segmentazione e la separazione si applicano dal livello di collegamento dati fino al livello di applicazione incluso; negli ambienti sensibili, tuttavia, l'isolamento fisico potrebbe essere appropriato. Le misure basate su host e a livello di rete devono essere distribuite in modo complementare ed essere monitorate centralmente. L'uso di un firewall o di un appliance di sicurezza come unica misura di sicurezza non è sufficiente. |_Protezione ACSC: Implementazione della segmentazione e della separazione della rete_|
|**Usare i principi del privilegio minimo e la necessità di essere a conoscenza**. Se un host, un servizio o una rete non deve comunicare con un altro host, servizio o rete, non dovrebbe essere consentito. Se un host, un servizio o una rete devono comunicare solo con un altro host, servizio o rete utilizzando porte o protocolli specifici, è necessario disabilitare eventuali altre porte o protocolli. L'adozione di questi principi in una rete completerà la minimizzazione dei privilegi utente e aumenterà significativamente il comportamento di sicurezza complessivo dell'ambiente. |_Protezione ACSC: Implementazione della segmentazione e della separazione della rete_|
|**Separare host e reti in base alla loro sensibilità o criticità per le operazioni aziendali**. La separazione può essere eseguita usando hardware o piattaforme diverse a seconda delle diverse classificazioni di sicurezza, dei domini di sicurezza o dei requisiti di disponibilità/integrità per determinati host o reti. In particolare, le reti di gestione separate e considerano l'isolamento fisico delle reti di gestione fuori banda per ambienti sensibili. |_Protezione ACSC: Implementazione della segmentazione e della separazione della rete_|
|**Identificare, autenticare e autorizzare l'accesso da tutte le entità a tutte le altre entità**. Tutti gli utenti, gli host e i servizi devono disporre di accesso limitato solo ad altri utenti, host e servizi necessari per eseguire le funzioni o i compiti designati. Tutti i servizi legacy o locali che ignorano o effettuano il downgrade della forza dei servizi di identificazione, autenticazione e autorizzazione devono essere disabilitati e il loro utilizzo deve essere monitorato attentamente. |_Protezione ACSC: Implementazione della segmentazione e della separazione della rete_|
|**Implementare Consenti elenco di traffico di rete anziché nega elenco**. Consentire l'accesso solo per il traffico di rete valido noto, ovvero identificato, autenticato e autorizzato, anziché negare l'accesso al traffico di rete non valido noto, ad esempio bloccando un indirizzo o un servizio specifico. L'inserimento nell'elenco degli elementi consentiti comporta un criterio di sicurezza superiore per la blacklist e migliora significativamente la capacità di un'organizzazione di rilevare e valutare potenziali intrusioni di rete. |_Protezione ACSC: Implementazione della segmentazione e della separazione della rete_|
|

Questo articolo fornisce informazioni e consigli su come soddisfare questi requisiti per i sistemi distribuiti in Azure usando sia l'infrastruttura distribuita come servizio (IaaS) che la piattaforma distribuita come servizio (PaaS). Si consiglia inoltre di leggere l'articolo sul [controllo del traffico in uscita in Azure Australia](gateway-egress-traffic.md) per comprendere appieno il controllo del traffico di rete in Azure.

## <a name="architecture"></a>Architettura

Se si è interessati alla progettazione o all'implementazione della sicurezza di rete e ai controlli di traffico in ingresso, è necessario innanzitutto comprendere come funziona il traffico di rete in ingresso in Azure tra IaaS e PaaS. Questa sezione offre una panoramica dei possibili punti di ingresso in cui il traffico di rete può raggiungere una risorsa ospitata in Azure e i controlli di sicurezza disponibili per limitare e controllare il traffico. Ognuno di questi componenti viene descritto in dettaglio nelle sezioni rimanenti di questa guida.

### <a name="architecture-components"></a>Componenti dell'architettura

Il diagramma dell'architettura illustrato di seguito illustra i possibili percorsi che il traffico di rete può eseguire per connettersi a un servizio ospitato in Azure. Questi componenti sono divisi in Azure, in ingresso IaaS, in ingresso PaaS e nel controllo di sicurezza, a seconda della funzione che forniscono per il traffico in ingresso.

![Architettura](media/ingress-traffic.png)

### <a name="azure-components"></a>Componenti di Azure

|Componente | Descrizione|
|---|---|
|**Protezione DDoS** | Gli attacchi Distributed Denial of Service (DDoS) tentano di esaurire le risorse di un'applicazione, rendendo l'applicazione non disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet. Azure include la protezione DDoS automaticamente tramite la piattaforma Azure e fornisce funzionalità di mitigazione aggiuntive che possono essere abilitate per applicazioni specifiche per un controllo più granulare.|
| **Gestione traffico** | Gestione traffico di Azure è un servizio di bilanciamento del carico del traffico basato su Domain Name System (DNS) che consente di distribuire il traffico in modo ottimale ai servizi tra le aree di Azure, garantendo al tempo stesso disponibilità elevata e velocità di risposta. Gestione traffico USA DNS per indirizzare le richieste client all'endpoint più appropriato in base a un metodo di routing del traffico e all'integrità degli endpoint.|
| **ExpressRoute** | ExpressRoute è una connessione di rete dedicata per l'utilizzo di servizi cloud Microsoft. Viene eseguito il provisioning tramite un provider di connettività e offre maggiore affidabilità, velocità più elevate, minori latenze e una maggiore sicurezza rispetto alle connessioni Internet tipiche. Un circuito ExpressRoute rappresenta la connessione logica tra l'infrastruttura locale e i servizi cloud Microsoft tramite un provider di connettività.|
| **Peering privato di ExpressRoute** | Il peering privato di ExpressRoute è una connessione tra l'ambiente locale e le reti virtuali private di Azure. Il peering privato consente l'accesso ai servizi di Azure, come le macchine virtuali, che vengono distribuiti in una rete virtuale. Le risorse e le reti virtuali a cui si accede tramite peering privato sono considerate un'estensione della rete principale di un'organizzazione. Il peering privato offre connettività bidirezionale tra la rete locale e le reti virtuali di Azure usando indirizzi IP privati.|
| **Peering Microsoft ExpressRoute** | Il peering Microsoft ExpressRoute è una connessione tra l'ambiente locale e i servizi pubblici di Microsoft e Azure. Ciò include la connettività ai servizi di Office 365, Dynamics 365 e Azure PaaS. Il peering viene stabilito su indirizzi IP pubblici di proprietà dell'organizzazione o del provider di connettività. Per impostazione predefinita, non è possibile accedere ai servizi tramite il peering Microsoft ExpressRoute e un'organizzazione deve acconsentire esplicitamente ai servizi richiesti. Questo processo fornisce quindi la connettività agli stessi endpoint disponibili su Internet.|
|

### <a name="iaas-ingress-components"></a>Componenti in ingresso IaaS

|Componente | DESCRIZIONE|
|---|---|
|**Interfaccia di rete** | Un'interfaccia di rete è una risorsa esistente in Azure. Viene collegato a una macchina virtuale a cui è stato assegnato un indirizzo IP instradabile privato non Internet dalla subnet a cui è associato. Questo indirizzo IP viene assegnato in modo dinamico o statico tramite Azure Resource Manager.|
|**Subnet** | Una subnet è un intervallo di indirizzi IP creato all'interno di un VNet. È possibile creare più subnet all'interno di una VNet per la segmentazione della rete.|
| **Rete virtuale (VNet)** | Un VNet è una risorsa di base all'interno di Azure che fornisce una piattaforma e un limite per la distribuzione delle risorse e l'abilitazione della comunicazione. Il VNet è disponibile all'interno di un'area di Azure e definisce lo spazio degli indirizzi IP e i limiti di routing per le risorse integrate VNet, ad esempio le macchine virtuali.|
| **Peering reti virtuali** | Il peering VNet è un'opzione di configurazione di Azure che consente la comunicazione diretta tra due reti virtuali senza la necessità di un gateway di rete virtuale. Una volta eseguito il peering, le due reti virtuali possono comunicare direttamente e la configurazione aggiuntiva può controllare l'uso di gateway di rete virtuale e altre opzioni di transito.|
| **IP pubblico** | Un indirizzo IP pubblico è una risorsa che riserva uno degli indirizzi IP pubblici instradabili di Microsoft, dall'area specificata, da usare all'interno della rete virtuale. Può essere associato a un'interfaccia di rete specifica, che consente di accedere alla risorsa dai sistemi Internet, ExpressRoute e PaaS.|
| **Gateway ExpressRoute** | Un gateway ExpressRoute è un oggetto in una rete virtuale che fornisce connettività e routing dalla rete virtuale alle reti locali tramite peering privato in un circuito ExpressRoute.|
| **Gateway VPN** | Un gateway VPN è un oggetto in una rete virtuale che fornisce un tunnel crittografato da una rete virtuale a una rete esterna. Il tunnel crittografato può essere da sito a sito per la comunicazione bidirezionale con un ambiente locale, un'altra rete virtuale o un ambiente cloud o da punto a sito per la comunicazione con un singolo endpoint.|
| **Integrazione di PaaS VNet** | Molte funzionalità di PaaS possono essere distribuite o integrate con una rete virtuale. Alcune funzionalità di PaaS possono essere completamente integrate con una VNet ed essere accessibili solo tramite indirizzi IP privati. Altri, ad esempio Azure Load Balancer e applicazione Azure gateway, possono avere un'interfaccia esterna con un indirizzo IP pubblico e un'interfaccia interna con un indirizzo IP privato all'interno della rete virtuale. In questo caso, il traffico può entrare nella rete virtuale tramite la funzionalità PaaS.|
|

### <a name="paas-ingress-components"></a>Componenti in ingresso PaaS

|Componente | Descrizione|
|---|---|
|**Hostname** (Nome host) | Le funzionalità di Azure PaaS sono identificate da un nome host pubblico univoco assegnato al momento della creazione della risorsa. Questo nome host viene quindi registrato in un dominio DNS pubblico, dove può essere risolto in un indirizzo IP pubblico.|
|**IP pubblico** | A meno che non venga distribuito in una configurazione VNet integrata, le funzionalità di Azure PaaS sono accessibili tramite un indirizzo IP pubblico e instradabile su Internet. Questo indirizzo può essere dedicato a risorse specifiche, ad esempio un Load Balancer pubblico, oppure può essere associato a una funzionalità specifica che dispone di un punto di ingresso condiviso per più istanze, ad esempio archiviazione o SQL. È possibile accedere a questo indirizzo IP pubblico da Internet, ExpressRoute o da indirizzi IP pubblici di IaaS tramite la rete backbone di Azure.|
|**Endpoint servizio** | Gli endpoint di servizio forniscono una connessione privata diretta da una rete virtuale a una specifica funzionalità PaaS. Gli endpoint di servizio, che sono disponibili solo per un subset di funzionalità PaaS, garantiscono un miglioramento delle prestazioni e della sicurezza per le risorse in un VNet che accede a PaaS.|
|

### <a name="security-controls"></a>Controlli di sicurezza

|Componente | Descrizione|
|---|---|
|**Gruppi di sicurezza di rete (gruppi)** | Gruppi controlla il traffico all'interno e all'esterno delle risorse della rete virtuale in Azure. Gruppi applicano le regole per i flussi di traffico consentiti o negati, che includono il traffico in Azure e tra Azure e reti esterne, ad esempio in locale o in Internet. Gruppi vengono applicate alle subnet all'interno di una rete virtuale o a singole interfacce di rete.|
|**Firewall PaaS** | Molte funzionalità di PaaS, ad esempio archiviazione e SQL, includono un firewall integrato per il controllo del traffico di rete in ingresso verso la risorsa specifica. È possibile creare regole per consentire o negare le connessioni da indirizzi IP e/o reti virtuali specifici.|
|**Autenticazione e controllo di accesso PaaS** | Come parte di un approccio a più livelli alla sicurezza, le funzionalità di PaaS forniscono diversi meccanismi per l'autenticazione degli utenti e il controllo dei privilegi e dell'accesso.|
|**Criteri di Azure** | Criteri di Azure è un servizio di Azure per la creazione, l'assegnazione e la gestione dei criteri. Questi criteri usano regole per controllare i tipi di risorse che è possibile distribuire e la configurazione di tali risorse. I criteri possono essere usati per applicare la conformità impedendo la distribuzione delle risorse se non soddisfano i requisiti o possono essere usati per il monitoraggio per segnalare lo stato di conformità.|
|

## <a name="general-guidance"></a>Indicazioni generali

Per progettare e creare soluzioni sicure in Azure, è fondamentale comprendere e controllare il traffico di rete in modo che possa verificarsi solo la comunicazione identificata e autorizzata. Lo scopo di questo materiale sussidiario e le linee guida per i componenti specifici nelle sezioni successive sono la descrizione degli strumenti e dei servizi che possono essere utilizzati per applicare i principi descritti in _ACSC Protect: Implementazione della segmentazione e della_ separazione della rete nei carichi di lavoro di Azure. Questo include informazioni dettagliate su come creare un'architettura virtuale per la protezione delle risorse quando non è possibile applicare gli stessi controlli fisici e di rete tradizionali possibili in un ambiente locale.

### <a name="specific-focus-areas"></a>Aree di interesse specifiche

* Limitare il numero di punti di ingresso alle reti virtuali
* Limitare il numero di indirizzi IP pubblici
* Prendere in considerazione l'uso di una progettazione di rete Hub e spoke per le reti virtuali, come illustrato nella documentazione di Microsoft Virtual Data Center (VCC)
* Usare i prodotti con funzionalità di sicurezza incorporate per le connessioni in ingresso da Internet (ad esempio, il gateway applicazione, il gateway API, le appliance virtuali di rete)
* Limitare i flussi di comunicazione alle funzionalità di PaaS solo a quelli necessari per le funzionalità di sistema
* Distribuire PaaS in una configurazione integrata VNet per aumentare la separazione e il controllo
* Configurare i sistemi per l'uso dei meccanismi di crittografia in linea con le linee guida per gli utenti di ACSC e ISM
* Usare protezioni basate su identità come l'autenticazione e il controllo degli accessi in base al ruolo oltre ai controlli di rete tradizionali
* Implementare ExpressRoute per la connettività con le reti locali
* Implementare VPN per il traffico amministrativo e l'integrazione con reti esterne
* Usare i criteri di Azure per limitare le aree e le risorse solo a quelle necessarie per le funzionalità di sistema
* Usare i criteri di Azure per applicare la configurazione della sicurezza di base per le risorse accessibili da Internet

### <a name="additional-resources"></a>Risorse aggiuntive

|Risorsa | Collegamento|
|---|---|
|Documenti australiani sulla conformità alle normative e ai criteri, incluse le linee guida|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Data Center virtuale di Azure|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)|
|Segmentazione della rete ACSC|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|Sicurezza cloud ACSC per i tenant| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|Manuale di ACSC Information Security|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>Linee guida sui componenti

Questa sezione fornisce altre indicazioni sui singoli componenti rilevanti per il traffico in ingresso verso i sistemi distribuiti in Azure. Ogni sezione descrive lo scopo del componente specifico con collegamenti alla documentazione e alle guide di configurazione che possono essere usate per semplificare le attività di progettazione e compilazione.

## <a name="azure"></a>Azure

Tutte le comunicazioni con le risorse all'interno di Azure passano attraverso l'infrastruttura di rete gestita da Microsoft, che offre connettività e funzionalità di sicurezza. Una gamma di protezioni viene messa automaticamente a disposizione da Microsoft per proteggere la piattaforma e l'infrastruttura di rete di Azure e sono disponibili funzionalità aggiuntive come servizi in Azure per controllare il traffico di rete e stabilire la segmentazione della rete e segregazione.

### <a name="ddos-protection"></a>Protezione DDoS

Le risorse accessibili da Internet sono soggette ad attacchi DDoS. Per proteggersi da questi attacchi, Azure offre protezioni DDoS a livello Basic e standard.

Basic viene abilitato automaticamente come parte della piattaforma Azure, tra cui il monitoraggio del traffico always on e la mitigazione in tempo reale di attacchi comuni a livello di rete, fornendo le stesse difese utilizzate dai Servizi online Microsoft. È possibile usare la scalabilità completa della rete globale di Azure per distribuire e mitigare il traffico degli attacchi nelle diverse aree. La protezione viene fornita per gli indirizzi IP pubblici di Azure IPv4 e IPv6

Standard offre funzionalità di mitigazione aggiuntive per il livello di servizio Basic, ottimizzate in modo specifico per le risorse di rete virtuale di Azure. I criteri di protezione sono ottimizzati tramite il monitoraggio del traffico dedicato e algoritmi di apprendimento automatico. La protezione viene fornita per gli indirizzi IP pubblici di Azure IPv4.

|Risorsa|Collegamento|
|---|---|
|Panoramica di protezione DDoS di Azure|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)|
|Procedure consigliate per DDoS di Azure|[https://docs.microsoft.com/azure/security/azure-ddos-best-practices](https://docs.microsoft.com/azure/security/azure-ddos-best-practices)|
|Gestione della protezione DDoS|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)|
|

### <a name="traffic-manager"></a>Gestione traffico

Gestione traffico viene usato per gestire il traffico in ingresso controllando gli endpoint di un'applicazione che ricevono connessioni. Per evitare la perdita di disponibilità di sistemi o applicazioni a causa di attacchi informatici alla sicurezza o per ripristinare i servizi dopo una compromissione del sistema, Traffic Manager può essere usato per reindirizzare il traffico alle istanze di applicazioni disponibili funzionanti.

|Risorsa|Collegamento|
|---|---|
|Panoramica di Gestione traffico | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)|
|Guida al ripristino di emergenza con DNS di Azure e gestione traffico | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute può essere usato per stabilire un percorso privato da un ambiente locale ai sistemi ospitati in Azure. Questa connessione può garantire maggiore affidabilità e prestazioni garantite grazie alla privacy migliorata per le comunicazioni di rete. Express Route consente alle entità Commonwealth di controllare il traffico in ingresso dall'ambiente locale e definire indirizzi dedicati specifici dell'organizzazione da usare per le regole del firewall in ingresso e gli elenchi di controllo di accesso.

|Risorsa | Collegamento|
|---|---|
|Panoramica di ExpressRoute | [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)|
|Modelli di connettività ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)|
|

### <a name="expressroute-private-peering"></a>Peering privato di ExpressRoute

Il peering privato fornisce un meccanismo per estendere un ambiente locale in Azure usando solo indirizzi IP privati. Ciò consente alle entità Commonwealth di integrare le reti virtuali di Azure e gli intervalli di indirizzi con i sistemi e i servizi locali esistenti. Il peering privato garantisce che la comunicazione tra ExpressRoute sia solo per le reti virtuali autorizzate dall'organizzazione. Se si usa il peering privato, le entità Commonwealth devono implementare appliance virtuali di rete anziché il gateway VPN di Azure per stabilire la comunicazione VPN sicura con le reti locali come richiesto dalle linee guida per i clienti di ACSC.

|Risorsa | Collegamento|
|---|---|
|Panoramica del peering privato di ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Guida alle procedure per il peering privato di ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)|
|

### <a name="expressroute-microsoft-peering"></a>Peering Microsoft ExpressRoute

Il peering Microsoft offre una connessione ad alta velocità e bassa latenza ai servizi pubblici Microsoft senza dover attraversare Internet. Ciò garantisce maggiore affidabilità, prestazioni e privacy per le connessioni. Usando i filtri di route, le entità Commonwealth possono limitare le comunicazioni solo alle aree di Azure necessarie, ma sono inclusi i servizi ospitati da altre organizzazioni e possono richiedere ulteriori funzionalità di filtro o ispezione tra i ambiente locale e Microsoft.

Le entità Commonwealth possono usare gli indirizzi IP pubblici dedicati stabiliti tramite la relazione di peering per identificare in modo univoco l'ambiente locale da usare nei firewall e negli elenchi di controllo di accesso nelle funzionalità PaaS.

In alternativa, le entità Commonwealth possono usare il peering Microsoft ExpressRoute come rete sottostante per stabilire la connettività VPN tramite il gateway VPN di Azure. In questo modello non è presente alcuna comunicazione attiva dalla rete locale interna ai servizi pubblici di Azure tramite ExpressRoute, ma la connettività sicura attraverso le reti virtuali private viene realizzata in conformità con le linee guida per i consumatori di ACSC.

|Risorsa | Collegamento|
|---|---|
|Panoramica del peering Microsoft ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Guida alle procedure per il peering Microsoft ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft)|
|

## <a name="iaas-ingress"></a>IaaS in ingresso

Questa sezione fornisce informazioni aggiuntive sul componente per il controllo del traffico in ingresso verso i componenti IaaS. IaaS include macchine virtuali e altre risorse di calcolo che possono essere distribuite e gestite in una Rete in ingresso virtuale di Azure. Affinché il traffico arrivi ai sistemi distribuiti con IaaS deve avere un punto di ingresso alla rete virtuale, che può essere stabilito tramite un indirizzo IP pubblico, un gateway di rete virtuale o una relazione di peering di rete virtuale.

### <a name="network-interface"></a>Interfaccia di rete

Le interfacce di rete sono punti di ingresso per tutto il traffico verso una macchina virtuale. Le interfacce di rete consentono la configurazione degli indirizzi IP e possono essere usate per applicare gruppi o per il routing del traffico attraverso un'appliance virtuale di rete. Le interfacce di rete per le macchine virtuali devono essere pianificate e configurate in modo appropriato per essere allineate agli obiettivi generali di separazione e segmentazione della rete.

|Risorsa | Collegamento|
|---|---|
|Creare, modificare o eliminare un'interfaccia di rete | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)|
|Indirizzi IP dell'interfaccia di rete | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)|
|

### <a name="subnet"></a>Subnet

Le subnet sono un componente fondamentale per la segmentazione e la separazione della rete in Azure. Le subnet possono essere usate in modo analogo per fornire la separazione tra i sistemi. È possibile applicare gruppi alle subnet per limitare il flusso di comunicazione in ingresso solo a quelle necessarie per le funzionalità di sistema. Le subnet possono essere usate come indirizzi di origine e di destinazione per le regole del firewall e gli elenchi di controllo di accesso e possono essere configurati per gli endpoint di servizio per fornire connettività alle funzionalità PaaS.

|Risorsa | Collegamento|
|---|---|
|Aggiungere, modificare o eliminare le subnet di rete virtuale | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)|
|

### <a name="virtual-network-vnet"></a>Rete virtuale

Reti virtuali sono uno dei componenti fondamentali per la rete in Azure. Le reti virtuali definiscono uno spazio degli indirizzi IP e un limite di routing da usare in un'ampia gamma di sistemi. Le reti virtuali sono divise in subnet e tutte le subnet all'interno di una rete virtuale hanno una route di rete diretta tra loro. Con i gateway di rete virtuale (ExpressRoute o VPN), i sistemi all'interno di una rete virtuale possono essere resi accessibili da ambienti locali ed esterni. Comprendere le reti virtuali e i parametri di configurazione e il routing associati è fondamentale per comprendere e controllare il traffico di rete in ingresso.

|Risorsa | Collegamento|
|---|---|
|Panoramica sulle reti virtuali | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)|
|Guida alle procedure per la pianificazione delle reti virtuali | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
Guida introduttiva alla creazione di una rete virtuale | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)|
|

### <a name="vnet-peering"></a>Peering VNet

Il peering VNet viene usato per fornire un percorso di comunicazione diretta tra due reti virtuali. Una volta stabilito il peering, gli host in una rete virtuale hanno un percorso di routing ad alta velocità direttamente per gli host in un'altra rete virtuale. Gruppi si applicano comunque al traffico perché i parametri di configurazione normali e avanzati possono essere usati per definire se è consentita la comunicazione tramite i gateway di rete virtuale o da altri sistemi esterni.

|Risorsa | Collegamento|
|---|---|
|Panoramica del peering di rete virtuale |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)|
|Creare, modificare o eliminare un peering reti virtuali | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="public-ip-on-vnet"></a>IP pubblico in VNET

Gli indirizzi IP pubblici vengono usati per fornire un percorso di comunicazione in ingresso ai servizi distribuiti in una rete virtuale. Le entità Commonwealth devono pianificare l'allocazione degli indirizzi IP pubblici con attenzione e assegnarle solo alle risorse in cui è presente un requisito autentico. Come procedura di progettazione generale, gli indirizzi IP pubblici devono essere allocati alle risorse con funzionalità di sicurezza incorporate come il gateway applicazione o le appliance virtuali di rete per fornire un punto di ingresso pubblico sicuro e controllato a una rete virtuale.

|Risorsa | Collegamento|
|---|---|
|Panoramica di Indirizzi IP pubblici | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)|
|Creare, modificare o eliminare un indirizzo IP pubblico | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)|
|

### <a name="expressroute-gateway"></a>Gateway ExpressRoute

I gateway ExpressRoute forniscono un punto di ingresso dall'ambiente locale e devono essere distribuiti per soddisfare i requisiti di sicurezza, disponibilità, finanza e prestazioni. I gateway ExpressRoute forniscono una larghezza di banda di rete definita e comportano costi di utilizzo dopo la distribuzione. Le reti virtuali possono avere un solo gateway ExpressRoute, ma possono essere connesse a più circuiti ExpressRoute e possono essere utilizzate da più reti virtuali tramite il peering VNet, consentendo a più reti virtuali di condividere larghezza di banda e connettività. È necessario prestare attenzione per configurare il routing tra ambienti locali e reti virtuali usando i gateway ExpressRoute per garantire la connettività end-to-end usando punti di ingresso di rete controllati noti e controllati. Le entità Commonwealth che usano il gateway ExpressRoute devono anche distribuire appliance virtuali di rete per stabilire la connettività VPN all'ambiente locale per la conformità con le linee guida per i consumatori di ACSC.

|Risorsa | Collegamento|
|---|---|
|Panoramica del gateway ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)|
|Configurare un gateway di rete virtuale per ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)|
|

### <a name="vpn-gateway"></a>Gateway VPN

Il gateway VPN di Azure fornisce un punto di rete in ingresso da una rete esterna per connessioni sicure da sito a sito o da punto a sito. I gateway VPN forniscono una larghezza di banda di rete definita e comportano costi di utilizzo dopo la distribuzione. Le entità Commonwealth che utilizzano il gateway VPN devono assicurarsi che siano configurate in conformità con le linee guida per i clienti di ACSC. Le reti virtuali possono avere un solo gateway VPN, ma possono essere configurate con più tunnel e possono essere utilizzate da più reti virtuali tramite il peering VNet, consentendo a più reti virtuali di condividere larghezza di banda e connettività. I gateway VPN possono essere stabiliti tramite Internet o tramite ExpressRoute tramite il peering Microsoft.

|Risorsa | Collegamento|
|---|---|
|Panoramica del gateway VPN | [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)|
|Pianificazione e progettazione per il gateway VPN | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
|Configurazione del gateway VPN per enti pubblici australiani|[Configurazione IPSEC necessaria per enti pubblici australiani](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>Integrazione di PaaS VNet

L'uso di PaaS può fornire funzionalità e disponibilità avanzate e ridurre il sovraccarico di gestione, ma deve essere protetto in modo appropriato. Per aumentare il controllo, applicare la segmentazione della rete o fornire un punto di ingresso sicuro per le applicazioni e i servizi, molte funzionalità PaaS possono essere integrate con una rete virtuale.

Per fornire un punto di ingresso sicuro, le funzionalità di PaaS, ad esempio il gateway applicazione, possono essere configurate con un'interfaccia esterna, pubblica e un'interfaccia privata interna per la comunicazione con i servizi delle applicazioni. In questo modo si evita la necessità di configurare i server applicazioni con indirizzi IP pubblici ed esporli a reti esterne.

Per usare PaaS come parte integrante dell'architettura del sistema o dell'applicazione, Microsoft offre diversi meccanismi per distribuire PaaS in una rete virtuale. La metodologia di distribuzione limita l'accesso in ingresso da reti esterne, ad esempio Internet, fornendo connettività e integrazione con i sistemi e le applicazioni interni. Gli esempi includono gli ambienti del servizio app, le istanze gestite di SQL e altro ancora.

|Risorsa | Collegamento|
|---|---|
|Integrazione della rete virtuale per i servizi di Azure | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)|
|Guida alle procedure per integrare l'app con una rete virtuale di Azure | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)|
|

## <a name="paas-ingress"></a>PaaS in ingresso

Le funzionalità di PaaS offrono opportunità per migliorare la capacità e la gestione semplificata, ma introducono complessità per soddisfare i requisiti per la segmentazione e la separazione della rete. Le funzionalità di PaaS sono in genere configurate con indirizzi IP pubblici e sono accessibili da Internet.  Quando si compilano sistemi con funzionalità PaaS, è necessario prestare attenzione per identificare tutti i flussi di comunicazione necessari tra i componenti all'interno del sistema e le regole di sicurezza di rete create per consentire solo questa comunicazione. Come parte di un approccio di difesa in profondità alla sicurezza, le funzionalità di PaaS devono essere configurate con la crittografia, l'autenticazione e i controlli di accesso e le autorizzazioni appropriati.  

### <a name="hostname"></a>Nome host

Le funzionalità di PaaS sono identificate in modo univoco dai nomi host per consentire l'hosting di più istanze dello stesso servizio nello stesso indirizzo IP pubblico. I nomi host univoci vengono specificati quando le risorse vengono create e sono presenti all'interno di domini DNS di proprietà di Microsoft. I nomi host specifici per i servizi autorizzati possono essere utilizzati all'interno degli strumenti di sicurezza con funzionalità di filtro a livello di applicazione. Alcuni servizi possono anche essere configurati con domini personalizzati come richiesto.

|Risorsa | Collegamento|
|---|---|
|Molti spazi dei nomi pubblici usati dai servizi di Azure possono essere ottenuti tramite PowerShell eseguendo il comando Get-AzureRMEnvironment | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment)|
|Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure | I servizi app e altri utenti possono avere domini personalizzati[https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal)|
|

### <a name="public-ip-for-paas"></a>IP pubblico per PaaS

Gli indirizzi IP pubblici per le funzionalità PaaS vengono allocati in base all'area in cui il servizio è ospitato o distribuito. Per compilare le regole di sicurezza di rete e la topologia di routing appropriate per la segmentazione e la separazione della rete per le reti virtuali di Azure, PaaS e ExpressRoute e la connettività Internet, è necessario conoscere l'allocazione e le aree di indirizzi IP pubblici Obbligatorio. Azure alloca gli indirizzi IP da un pool allocato a ogni area di Azure. Microsoft rende disponibili gli indirizzi usati in ogni area per il download, che vengono aggiornati in modo regolare e controllato. Anche i servizi disponibili in ogni area cambiano spesso quando vengono rilasciati nuovi servizi o se i servizi vengono distribuiti in modo più ampio. Le entità del Commonwealth dovrebbero rivedere questi materiali regolarmente e possono sfruttare l'automazione per gestire i sistemi in base alle esigenze. È possibile ottenere indirizzi IP specifici per alcuni servizi ospitati in ogni area contattando il supporto tecnico Microsoft.

|Risorsa | Collegamento|
|---|---|
|Microsoft Azure intervalli IP del Data Center | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|Servizi di Azure per area | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Australia-Central, Australia-Central-2, Australia-Est, Australia-sud-orientale & Products = all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>Endpoint di servizio

Gli endpoint di servizio della rete virtuale forniscono una connessione di rete in ingresso privata ad alta velocità per le subnet all'interno di una rete virtuale per l'utilizzo di funzionalità PaaS specifiche. Per la segmentazione e la separazione della rete completa della funzionalità PaaS, la funzionalità PaaS deve essere configurata in modo da accettare le connessioni solo dalle reti virtuali necessarie. Non tutte le funzionalità di PaaS supportano una combinazione di regole del firewall che include gli endpoint di servizio e le regole tradizionali basate sugli indirizzi IP, quindi è necessario prestare attenzione per comprendere il flusso delle comunicazioni necessarie per la funzionalità e l'amministrazione delle applicazioni in modo che l'implementazione di questi controlli di sicurezza non influisca sulla disponibilità del servizio.

|Risorsa | Collegamento|
|---|---|
|Panoramica degli endpoint di servizio | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
|Esercitazione |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

## <a name="security"></a>Security

L'implementazione di controlli di segmentazione e separazione delle reti sulle funzionalità IaaS e PaaS viene eseguita grazie alla protezione delle funzionalità stesse e all'implementazione dei percorsi di comunicazione controllati dai sistemi che comunicheranno con il funzionalità.

La progettazione e la creazione di soluzioni in Azure è un processo di creazione di un'architettura logica per comprendere, controllare e monitorare le risorse di rete nell'intera presenza di Azure. Questa architettura logica è il software definito all'interno della piattaforma Azure e si occupa di una topologia di rete fisica implementata negli ambienti di rete tradizionali.

L'architettura logica creata deve fornire le funzionalità necessarie per l'usabilità, ma deve fornire anche la visibilità e il controllo necessari per la sicurezza e l'integrità.

Il raggiungimento di questo risultato si basa sull'implementazione degli strumenti necessari per la segmentazione e la separazione di rete, ma anche per la protezione e l'applicazione della topologia di rete e dell'implementazione di questi strumenti.

Le informazioni fornite in questa guida possono essere usate per identificare le origini di traffico in ingresso che devono essere consentite e i modi in cui il traffico può essere ulteriormente controllato o vincolato.

### <a name="network-security-groups-nsgs"></a>Gruppi di sicurezza di rete (NGS)

Gruppi vengono usati per specificare il traffico in ingresso e in uscita consentito per una subnet o un'interfaccia di rete specifica. Quando si configura gruppi, le entità Commonwealth devono usare un approccio per l'inserimento nell'elenco elementi consentiti in cui le regole sono configurate per consentire il traffico necessario con una regola predefinita configurata per negare tutto il traffico che non corrisponde a un'istruzione È necessario prestare attenzione durante la pianificazione e la configurazione di gruppi per garantire che tutto il traffico in ingresso e in uscita necessario venga acquisito in modo appropriato. Questo include l'identificazione e la comprensione di tutti gli intervalli di indirizzi IP privati utilizzati nelle reti virtuali di Azure e nell'ambiente locale e in servizi Microsoft specifici, ad esempio Azure Load Balancer e i requisiti di gestione di PaaS. I singoli utenti interessati alla progettazione e all'implementazione dei gruppi di sicurezza di rete devono comprendere anche l'uso di tag di servizio e gruppi di sicurezza delle applicazioni per la creazione di regole di sicurezza specifiche per le applicazioni e per i servizi.

|Risorsa | Collegamento|
|---|---|
|Panoramica della sicurezza di rete | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)
|Creare, modificare o eliminare un gruppo di sicurezza di rete | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

## <a name="paas-firewall"></a>Firewall PaaS

Un firewall PaaS è una funzionalità di controllo dell'accesso alla rete che può essere applicata a determinati servizi di PaaS. Consente di filtrare o filtrare gli indirizzi IP da reti virtuali specifiche per limitare il traffico in ingresso all'istanza di PaaS specifica. Per le funzionalità PaaS che includono un firewall, i criteri di controllo di accesso alla rete devono essere configurati in modo da consentire solo il traffico in ingresso necessario in base ai requisiti dell'applicazione.  

|Risorsa | Collegamento|
|---|---|
|Regole del firewall IP per il database SQL di Azure e SQL Data Warehouse | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)|
|Sicurezza della rete di archiviazione | [https://docs.microsoft.com/azure/storage/common/storage-network-security](https://docs.microsoft.com/azure/storage/common/storage-network-security)|
|

## <a name="paas-authentication-and-access-control"></a>Autenticazione e controllo di accesso PaaS

A seconda della funzionalità PaaS e del suo scopo, l'uso di controlli di rete per limitare l'accesso potrebbe non essere possibile o pratico. Come parte del modello di sicurezza a più livelli per PaaS, Azure offre un'ampia gamma di meccanismi di autenticazione e controllo di accesso per limitare l'accesso a un servizio, anche se il traffico di rete è consentito. I meccanismi di autenticazione tipici per le funzionalità PaaS includono Azure Active Directory, l'autenticazione a livello di applicazione e le chiavi condivise o le firme di accesso. Quando un utente viene identificato in modo sicuro, è possibile utilizzare i ruoli per controllare le azioni che l'utente può eseguire. Questi strumenti possono essere utilizzati come alternativa o come misura gratuita per limitare l'accesso ai servizi.

|Risorsa | Collegamento|
|---|---|
|Controllo e concessione dell'accesso al database SQL e a SQL Data Warehouse | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)|
|Autorizzazione per i servizi di archiviazione di Azure | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Criteri di Azure

Criteri di Azure è un componente chiave per l'applicazione e la gestione dell'integrità dell'architettura logica dell'ambiente Azure. Data la varietà dei servizi e i percorsi di traffico di rete in ingresso disponibili tramite i servizi di Azure, è fondamentale che le entità del Commonwealth siano consapevoli delle risorse presenti all'interno dell'ambiente e dei punti di ingresso della rete disponibili. Per assicurarsi che i punti di ingresso della rete non autorizzati non vengano creati nell'ambiente Azure, le entità Commonwealth dovrebbero sfruttare i criteri di Azure per controllare i tipi di risorse che è possibile distribuire e la configurazione di tali risorse. Esempi pratici includono la limitazione delle risorse solo a quelle autorizzate e approvate per l'uso, l'applicazione della crittografia HTTPS nell'archiviazione e la necessità di aggiungere gruppi alle subnet.

|Risorsa | Collegamento|
|---|---|
|Panoramica di criteri di Azure | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|Criteri di esempio dei tipi di risorse consentiti | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)
|Verificare i criteri di esempio dell'account di archiviazione HTTPS|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account)_
|Forzare NSG in un criterio di esempio della subnet| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sulla gestione dei flussi di traffico dall'ambiente Azure ad altre reti usando i componenti del gateway in Azure, vedere l'articolo sulla [gestione del traffico in uscita del gateway e il controllo](gateway-egress-traffic.md) .
