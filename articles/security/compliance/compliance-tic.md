---
title: Indicazioni su Trusted Internet Connections per Azure
description: Indicazioni su Trusted Internet Connections per Azure e servizi SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: b1a406c15377cb6931f92594f5ce1526a2f2ab99
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017100"
---
# <a name="trusted-internet-connections-guidance"></a>Indicazioni su Trusted Internet Connections

Questo articolo illustra come le agenzie governative statunitensi possono usare Microsoft Azure per enti pubblici per facilitare la conformità all'iniziativa Trusted Internet Connections (TIC). Viene descritto come usare Azure per enti pubblici nell'ambito delle offerte di infrastruttura distribuita come servizio (IaaS) e piattaforma distribuita come servizio (PaaS) di Azure.

## <a name="trusted-internet-connections-overview"></a>Informazioni generali su Trusted Internet Connections

Lo scopo dell'iniziativa TIC è quello di ottimizzare e standardizzare la sicurezza delle singole connessioni di rete esterne attualmente usate dalle agenzie federali. I criteri sono descritti nel memorandum dell'OMB (Office of Management and Budget) [M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

Nel novembre 2007, l'OMB ha stabilito il programma TIC per migliorare la protezione del perimetro di rete federale e le funzioni di risposta agli eventi imprevisti. Il programma TIC è stato progettato per eseguire l'analisi di rete di tutto il traffico .gov in ingresso e in uscita al fine di identificare le firme specifiche e i dati basati su un modello. Consente inoltre di individuare eventuali anomalie di comportamento, ad esempio attività botnet. Alle agenzie è stato richiesto di rafforzare le proprie connessioni di rete esterne e di inoltrare tutto il traffico tramite dispositivi di rilevamento e prevenzione delle intrusioni, noti anche come EINSTEIN. I dispositivi sono ospitati in un numero limitato di endpoint di rete, definiti _Trusted Internet Connections_.

L'obiettivo del programma TIC è quello di tenere informate le agenzie riguardo a quanto segue:
- Chi si trova all'interno della rete (soggetti autorizzati o non autorizzati)
- Quando è stato effettuato l'accesso alla rete e perché
- A quali risorse è stato effettuato l'accesso

Attualmente, tutte le connessioni esterne delle agenzie devono essere inoltrate in base a un programma TIC approvato dall'OMB. Le agenzie federali devono partecipare al programma TIC come provider di accesso TIC (TICAP) o contrattando i servizi con uno dei principali provider di servizi Internet di 1° livello, detti provider MTIPS (Managed Trusted Internet Protocol Service). Il TIC include le funzionalità critiche obbligatorie che vengono eseguite dall'agenzia e dal provider MTIPS. Nella versione corrente del TIC, i dispositivi di rilevamento delle intrusioni EINSTEIN versione 2 e di prevenzione accelerata dalle intrusioni EINSTEIN versione 3 (3A) vengono distribuiti a ogni TICAP e MTIPS. L'agenzia stabilisce un memorandum d'intesa con il dipartimento per la sicurezza interna (Department of Homeland Security, DHS) per distribuire funzionalità EINSTEIN nei sistemi federali.

Come parte della sua responsabilità nella protezione della rete .gov, il DHS necessita dei feed di dati non elaborati del flusso di rete dell'agenzia per stabilire una corrispondenza tra gli eventi imprevisti all'interno dell'azienda federale ed eseguire analisi tramite strumenti specializzati. I router del DHS offrono la possibilità di raccogliere il traffico di rete IP in ingresso o in uscita da un'interfaccia. Gli amministratori di rete possono analizzare i dati del flusso di rete per determinare l'origine e la destinazione del traffico, la classe del servizio e così via. I dati del flusso di rete sono considerati "dati non di contenuto", ad esempio, intestazione, IP di origine, IP di destinazione e così via. Questi dati consentono al DHS di reperire informazioni sul contenuto, ovvero sulle attività degli utenti e sulle relative tempistiche.

L'iniziativa include anche i criteri di sicurezza, le linee guida e i framework che presuppongono l'infrastruttura locale. Mentre le agenzie governative passano al cloud per ottenere risparmi su costi, efficienza operativa e innovazione, i requisiti di implementazione del TIC possono rallentare il traffico di rete. Di conseguenza, la velocità e l'agilità con cui gli utenti degli enti pubblici possono accedere ai dati basati sul cloud sono limitate.

## <a name="azure-networking-options"></a>Opzioni dei servizi di rete di Azure

Sono disponibili tre opzioni principali per connettersi ai servizi di Azure:

- Connessione Internet diretta: connettersi ai servizi di Azure direttamente tramite una connessione Internet aperta. Il supporto e la connessione sono pubblici. L'applicazione e la crittografia a livello di trasporto sono utilizzati per garantire la privacy. La larghezza di banda è limitata dalla connettività di un sito a Internet. È possibile servirsi di più provider attivi per garantire resilienza.
- Rete privata virtuale (VPN): connettersi alla rete virtuale di Azure in privato tramite un gateway VPN.
Il supporto è pubblico perché attraversa una connessione Internet standard di un sito, ma la connessione è crittografata in un tunnel per garantire la privacy. La larghezza di banda è limitata a seconda della configurazione e dei dispositivi VPN selezionati. Le connessioni di Azure da punto a sito sono in genere limitate a 100 Mbps e le connessioni da sito a sito sono limitate a 1,25 Gbps.
- Azure ExpressRoute: ExpressRoute è una connessione diretta ai servizi Microsoft. Poiché la connettività passa attraverso un canale in fibra ottica isolato, la connessione può essere pubblica o privata a seconda della configurazione usata. La larghezza di banda è normalmente limitata a un massimo di 10 Gbps.

Esistono diversi modi per soddisfare i requisiti dell'Appendice H del TIC (Considerazioni sul cloud), come specificato nel documento del DHS "Trusted Internet Connections (TIC) Reference Architecture Document, Version 2.0". In questo articolo le linee guida dell'iniziativa TIC del DHS sono definite **TIC 2.0**.

Per abilitare la connessione **dal dipartimento o dall'agenzia (D/A)** in Azure o Office 365 senza inoltrare il traffico tramite il programma TIC del D/A, il D/A deve usare un tunnel crittografato e/o una connessione dedicata al provider di servizi cloud (CSP). I servizi CSP possono garantire che non sia consentita la connettività agli asset cloud del D/A dalla rete Internet pubblica per l'accesso diretto del personale dell'agenzia.

Office 365 rispetta le disposizioni riportate nell'allegato H del TIC 2.0 usando ExpressRoute con [Peering Microsoft](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) abilitato o una connessione Internet che crittografa tutto il traffico tramite TLS 1.2. Gli utenti finali del D/A nella rete del D/A possono connettersi usando la rete dell'agenzia e l'infrastruttura TIC tramite Internet. L'accesso Internet remoto a Office 365 è bloccato e viene inoltrato attraverso l'agenzia. Il D/A può anche connettersi a Office 365 sfruttando una connessione ExpressRoute con Peering Microsoft, un tipo di peering pubblico.  

Solo per Azure, la seconda opzione (VPN) e la terza opzione (ExpressRoute) possono soddisfare questi requisiti quando sono usate in combinazione con i servizi che limitano l'accesso a Internet.

![Trusted Internet Connection (TIC) Microsoft](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Offerte di infrastruttura distribuita come servizio di Azure

Implementare la conformità ai criteri del TIC tramite l'infrastruttura IaaS di Azure è relativamente semplice perché i clienti di Azure gestiscono il proprio routing di rete virtuale.

Per garantire la conformità all'architettura di riferimento del TIC è essenzialmente necessario assicurarsi che la rete virtuale sia un'estensione privata della rete del D/A. Per essere un'estensione _privata_, i criteri richiedono che il traffico non esca mai dalla rete, se non tramite la connessione di rete TIC locale. Questo processo è noto come _tunneling forzato_. Per la conformità al TIC, il processo instrada tutto il traffico proveniente da qualsiasi sistema nell'ambiente del CSP attraverso un gateway locale nella rete di un'organizzazione fino alla rete Internet tramite il TIC.

La conformità al TIC dell'infrastruttura IaaS di Azure prevede due passaggi principali:

- Passaggio 1: Configurazione.
- Passaggio 2: Controllo.

### <a name="azure-iaas-tic-compliance-configuration"></a>Conformità al TIC dell'infrastruttura IaaS di Azure: Configurazione

Per configurare un'architettura conforme al TIC con Azure, è necessario innanzitutto impedire l'accesso diretto a Internet alla rete virtuale e quindi forzare il traffico Internet attraverso la rete locale.

#### <a name="prevent-direct-internet-access"></a>Impedire l'accesso diretto a Internet

La rete IaaS di Azure viene fornita tramite le reti virtuali costituite da subnet a cui sono associati i controller di interfaccia di rete (NIC) delle macchine virtuali.

Lo scenario più semplice per supportare la conformità al TIC è di far sì che una macchina virtuale, o una raccolta di macchine virtuali, non possa connettersi a risorse esterne. La disconnessione da reti esterne può essere garantita tramite i gruppi di sicurezza di rete (NSG), che possono essere usati per controllare il traffico verso uno o più NIC o subnet nella rete virtuale. Un NSG contiene le regole di controllo di accesso che consentono o negano il traffico in base alla direzione del traffico, al protocollo, all’indirizzo e alla porta di origine e all’indirizzo e alla porta di destinazione. È possibile modificare le regole di un gruppo di sicurezza di rete in qualsiasi momento e le modifiche vengono applicate a tutte le istanze associate. Per altre informazioni su come creare un gruppo di sicurezza di rete, vedere [Filtrare il traffico di rete con un gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>Forzare il traffico Internet tramite una rete locale

Azure crea automaticamente route di sistema e assegna le route a ogni subnet in una rete virtuale. Non è possibile creare né rimuovere route di sistema, ma è possibile ignorare alcune route di sistema usando route personalizzate. Azure crea route di sistema predefinite per ogni subnet. Azure aggiunge route predefinite facoltative a determinate subnet, oppure a ogni subnet, quando si usano specifiche funzionalità di Azure. Questo tipo di routing assicura quanto segue:
- Il traffico destinato all'interno della rete virtuale rimane all'interno di tale rete.
- Gli spazi di indirizzi privati designati per IANA come 10.0.0.0/8 vengono eliminati, a meno che non siano inclusi nello spazio di indirizzi della rete virtuale.
- È disponibile il routing "di emergenza" di 0.0.0.0/0 per l'endpoint Internet della rete virtuale.

![Forzatura di tunneling del TIC](media/tic-diagram-c.png)

Per assicurarsi che tutto il traffico attraversi il TIC del D/A, tutto il traffico in uscita dalla rete virtuale deve essere inoltrato tramite la connessione locale. Le route personalizzate vengono create con route definite dall'utente oppure scambiando route BGP (Border Gateway Protocol) tra un gateway di rete locale e un gateway VPN di Azure. Per altre informazioni sulle route definite dall'utente, vedere [Routing del traffico di rete virtuale: Route definite dall'utente](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined). Per altre informazioni sul protocollo BGP, vedere [Routing del traffico di rete virtuale: Border Gateway Protocol](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol).

#### <a name="add-user-defined-routes"></a>Aggiungere route definite dall'utente

Se si usa un gateway di rete virtuale basato su route, è possibile forzare il tunneling in Azure. A tale scopo, aggiungere una route definita dall'utente che imposta il traffico di 0.0.0.0/0 in modo che sia inoltrato verso un **hop successivo** del gateway di rete virtuale. Azure assegna la priorità alle route definite dall'utente rispetto alle route definite dal sistema. Tutto il traffico di rete non virtuale viene inviato al gateway di rete virtuale, che può quindi inoltrarlo in locale. Dopo aver specificato la route definita dall'utente, associare la route a subnet esistenti o a nuove subnet all'interno di tutte le reti virtuali nell'ambiente di Azure.

![Route definite dall'utente e TIC](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Usare il protocollo BGP

Se si usa ExpressRoute o un gateway di rete virtuale abilitato per BGP, il protocollo BGP è il meccanismo preferito per annunciare le route. Per una route di 0.0.0.0/0 annunciata con BGP, ExpressRoute e i gateway di rete virtuale compatibili con BGP garantiscono che la route predefinita venga applicata a tutte le subnet all'interno delle reti virtuali.

### <a name="azure-iaas-tic-compliance-auditing"></a>Conformità al TIC dell'infrastruttura IaaS di Azure: Controllo

Azure offre diversi modi per controllare la conformità al TIC.

#### <a name="view-effective-routes"></a>Visualizzare le route valide

Verificare che la route predefinita sia stata propagata osservando le _route valide_ per una determinata macchina virtuale, uno specifico NIC o una tabella di route definite dall'utente nel [portale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) o in [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell). Il pannello **Route valide** mostra le route pertinenti definite dall'utente, quelle annunciate tramite BGP e quelle di sistema che si applicano all'entità rilevante, come illustrato nella figura seguente:

![Route valide](media/tic-screen-1.png)

> [!NOTE]
> Non è possibile visualizzare le route valide per un NIC a meno che il NIC non sia associato a una macchina virtuale in esecuzione.

#### <a name="use-azure-network-watcher"></a>Usare Azure Network Watcher

Azure Network Watcher offre vari strumenti che possono essere usati per controllare la conformità al TIC. Per altre informazioni, vedere [questa panoramica su Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

##### <a name="capture-network-security-group-flow-logs"></a>Acquisire i log di flusso dei gruppi di sicurezza di rete 

Usare Network Watcher per acquisire i log di flusso di rete che indicano i metadati correlati al traffico IP. I log di flusso di rete contengono gli indirizzi di origine e di destinazione delle varie destinazioni e anche altri dati. È possibile usare questi dati con i log provenienti dal gateway di rete virtuale, dai dispositivi periferici locali o dal TIC, per monitorare che tutto il traffico venga effettivamente inoltrato in locale. 

## <a name="azure-platform-as-a-service-offerings"></a>Offerte di piattaforma distribuita come servizio di Azure

I servizi PaaS di Azure, come Archiviazione di Azure, sono accessibili tramite URL raggiungibili da Internet. Tutti gli utenti con credenziali approvate possono accedere alla risorsa, ad esempio un account di archiviazione, da qualsiasi posizione senza attraversamento di un TIC. Per questo motivo, molti clienti di enti pubblici deducono erroneamente che i servizi PaaS di Azure non sono conformi ai criteri del TIC. In realtà, molti servizi PaaS di Azure possono essere conformi ai criteri del TIC. Un servizio è conforme quando l'architettura è identica a quella dell'ambiente IaaS conforme al TIC, [come descritto in precedenza](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings), e il servizio è associato a una rete virtuale di Azure.

Quando i servizi PaaS di Azure sono integrati in una rete virtuale, il servizio è accessibile privatamente da tale rete. È possibile applicare il routing personalizzato per 0.0.0.0/0 tramite route definite dall'utente o il protocollo BGP. Il routing personalizzato assicura che tutto il traffico associato a Internet venga inoltrato in locale in modo da attraversare il TIC. Integrare i servizi di Azure nelle reti virtuali usando i modelli seguenti:

- **Distribuire un'istanza dedicata di un servizio**: un numero crescente di servizi PaaS possono essere distribuiti come istanze dedicate con endpoint collegati a una rete virtuale. È possibile distribuire un ambiente del Servizio app per PowerApps in modalità "Isolato" per consentire che l'endpoint di rete sia vincolato a una rete virtuale. L'ambiente del servizio App può quindi ospitare molti servizi PaaS di Azure, ad esempio App Web di Azure, Gestione API di Azure e Funzioni di Azure.
- **Usare endpoint del servizio di rete virtuale**: un numero crescente di servizi PaaS offre la possibilità di spostare l'endpoint a un indirizzo IP privato della rete virtuale anziché a uno pubblico.

Le tabelle seguenti elencano i servizi che supportano la distribuzione di istanze dedicate in una rete virtuale o l'uso di endpoint di servizio, a partire dal mese di maggio 2018.

> [!Note]
> Lo stato di disponibilità corrisponde ai servizi di Azure che sono disponibili in commercio. Lo stato di disponibilità per i servizi di Azure in Azure per enti pubblici è in sospeso.

### <a name="support-for-service-endpoints"></a>Supporto per gli endpoint di servizio

|Service                        |Disponibilità      |
|-------------------------------|------------------|
|Azure Key Vault                | Anteprima privata  |
|Azure Cosmos DB                | Anteprima privata  |
|Servizi di gestione delle identità              | Anteprima privata  |
|Azure Data Lake                | Anteprima privata  |
|Database di Azure per PostgreSQL  | Anteprima privata  |
|Database di Azure per MySQL       | Anteprima privata  |
|Azure SQL Data Warehouse       | Anteprima pubblica   |
|Database SQL di Azure             | Disponibilità generale (GA) |
|Archiviazione di Azure                  | GA               |

### <a name="support-for-virtual-network-injection"></a>Supporto per l'integrazione di una rete virtuale

|Service                               |Disponibilità      |
|--------------------------------------|------------------|
|Istanza gestita di database SQL di Azure   | Anteprima pubblica   |
|Azure Kubernetes Service (AKS)        | Anteprima pubblica   |
|Azure Service Fabric                  | GA               |
|Gestione API di Azure                  | GA               |
|Azure Active Directory                | GA               |
|Azure Batch                           | GA               |
|Ambiente del servizio app               | GA               |
|Cache Redis di Azure                     | GA               |
|HDInsight di Azure                       | GA               |
|Set di scalabilità di macchine virtuali             | GA               |
|Servizi cloud di Azure                  | GA               |


### <a name="virtual-network-integration-details"></a>Dettagli di integrazione della rete virtuale

Il diagramma seguente mostra il flusso di rete generale per l'accesso ai servizi PaaS. L'accesso avviene tramite l'integrazione della rete virtuale e il tunneling del servizio di rete virtuale. Per altre informazioni sui gateway del servizio di rete, le reti virtuali e i tag di servizio, vedere [Gruppi di sicurezza di rete e delle applicazioni: Tag di servizio](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

![Opzioni di connettività PaaS per il TIC](media/tic-diagram-e.png)

1. Viene stabilita una connessione privata ad Azure tramite ExpressRoute. Il peering privato di ExpressRoute con tunneling forzato consente di forzare tutto il traffico di rete virtuale dei clienti tramite ExpressRoute e nuovamente verso l'ambiente locale. Peering Microsoft non è necessario.
2. Il Gateway VPN di Azure, se usato in combinazione con ExpressRoute e Peering Microsoft, può sovrapporre la crittografia IPSec end-to-end alla connessione tra la rete virtuale del cliente e i dispositivi periferici locali. 
3. La connettività alla rete virtuale del cliente viene controllata tramite gruppi di sicurezza di rete che consentono ai clienti di autorizzare/rifiutare le richieste in base all'indirizzo IP, alla porta e al protocollo.
4. La rete virtuale del cliente viene estesa al servizio PaaS tramite la creazione di un endpoint di servizio per il servizio del cliente.
5. L'endpoint di servizio PaaS è protetto tramite l'**impostazione predefinita di negazione totale** o la limitazione dell'accesso dalle subnet specificate all'interno della rete virtuale del cliente. L'impostazione predefinita di negazione totale include anche le connessioni provenienti da Internet.
6. Gli altri servizi di Azure che richiedono l'accesso alle risorse all'interno della rete virtuale del cliente devono essere:  
   - Distribuite direttamente nella rete virtuale.
   - Consentite in modo selettivo, in base alle indicazioni fornite dal servizio di Azure corrispondente.

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>Opzione A: distribuire un'istanza dedicata di un servizio (integrazione di rete virtuale)

Con l'integrazione della rete virtuale, i clienti possono distribuire in modo selettivo istanze dedicate di un determinato servizio di Azure, ad esempio HDInsight, nella propria rete virtuale. Le istanze del servizio vengono distribuite in una subnet dedicata nella rete virtuale di un cliente. L'integrazione della rete virtuale consente l'accesso alle risorse del servizio tramite indirizzi instradabili non Internet. Le istanze locali usano ExpressRoute o una VPN da sito a sito per accedere direttamente alle istanze del servizio tramite lo spazio di indirizzi della rete virtuale, anziché aprire un firewall allo spazio di indirizzi Internet pubblico. Quando un'istanza dedicata è associata a un endpoint, è possibile usare le stesse strategie adottate per la conformità IaaS al TIC. Il routing predefinito assicura che il traffico associato a Internet sia reindirizzato a un gateway di rete virtuale associato per l'istanza locale. È possibile controllare ulteriormente l'accesso in ingresso e in uscita tramite gruppi di sicurezza di rete per la subnet specifica.

![Panoramica relativa all'integrazione di una rete virtuale](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>Opzione B: usare gli endpoint di servizio della rete virtuale (tunnel di servizio)

Un numero crescente di servizi multi-tenant di Azure offrono "endpoint di servizio" che costituiscono un metodo alternativo per l'integrazione di reti virtuali di Azure. Gli endpoint di servizio della rete virtuale estendono al servizio lo spazio di indirizzi IP e l'identità della rete virtuale tramite una connessione diretta. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Azure. 

Dopo aver abilitato un endpoint per un servizio, usare i criteri esposti dal servizio per limitare le connessioni per il servizio alla specifica rete virtuale. Il servizio di Azure applica controlli di accesso alla piattaforma. L'accesso a una risorsa bloccata viene concesso solo se la richiesta ha origine dalla rete virtuale o dalla subnet consentita o dai due indirizzi IP usati per identificare il traffico in locale se si usa ExpressRoute. Usare questo metodo per impedire con efficacia che il traffico in entrata/uscita abbandoni direttamente il servizio PaaS.

![Panoramica degli endpoint di servizio](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>Usare strumenti nativi sul cloud per la consapevolezza situazionale di rete

Azure offre strumenti nativi sul cloud per garantire la consapevolezza situazionale necessaria per comprendere i flussi di traffico della rete. Gli strumenti non sono obbligatori per garantire la conformità con i criteri del TIC, ma possono migliorare notevolmente le funzionalità di sicurezza.

### <a name="azure-policy"></a>Criteri di Azure

[Criteri di Azure](https://azure.microsoft.com/services/azure-policy/) è un servizio di Azure che offre alle organizzazioni migliori possibilità di controllare e applicare le iniziative di conformità. È attualmente disponibile in anteprima pubblica nei servizi di Azure disponibili in commercio, ma non è ancora disponibile in Azure per enti pubblici. I clienti possono iniziare a pianificare e testare le regole di Criteri di Azure per una garanzia di conformità futura al TIC. 

Criteri di Azure è configurato a livello di sottoscrizione. Il servizio offre un'interfaccia centralizzata in cui è possibile eseguire attività di conformità, tra cui:
- Gestire iniziative
- Configurare definizioni di criteri
- Controllare la conformità
- Implementare la conformità
- Gestire eccezioni

Oltre a numerose definizioni predefinite, gli amministratori possono specificare definizioni personalizzate tramite semplici modelli JSON. Microsoft consiglia di dare priorità al controllo rispetto all'implementazione, quando possibile.

I criteri di esempio seguenti possono essere utili per gli scenari di conformità al TIC:

|Criterio  |Scenario di esempio  |Modello  |
|---------|---------|---------|
|Applicare la tabella di route definita dall'utente. | Assicurarsi che la route predefinita in tutte le reti virtuali sia indirizzata verso un gateway di rete virtuale approvato per il routing verso l'ambiente locale.    | Iniziare con questo [modello](https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table). |
|Controllare se Network Watcher non è abilitato per un'area.  | Assicurarsi che Network Watcher sia abilitato per tutte le aree usate.  | Iniziare con questo [modello](https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled). |
|Un gruppo di sicurezza di rete per ogni subnet.  | Assicurarsi che un gruppo di sicurezza di rete (o un set di gruppi di sicurezza di rete approvati) con traffico Internet bloccato sia applicato a tutte le subnet in ogni rete virtuale. | Iniziare con questo [modello](https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet). |
|Un gruppo di sicurezza di rete per ogni NIC. | Assicurarsi che un gruppo di sicurezza di rete con traffico Internet bloccato sia applicato a tutti i NIC in tutte le macchine virtuali. | Iniziare con questo [modello](https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic). |
|Usare una rete virtuale approvata per le interfacce di rete delle macchine virtuali.  | Assicurarsi che tutti i NIC si trovino in una rete virtuale approvata. | Iniziare con questo [modello](https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics). |
|Aree consentite. | Assicurarsi che tutte le risorse siano distribuite in aree con configurazione di Network Watcher e reti virtuali conformi.  | Iniziare con questo [modello](https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs). |
|Tipi di risorse non consentiti, ad esempio **PublicIPs**. | Non consentire la distribuzione di tipi di risorse per cui non è definito un piano di conformità. Usare questo criterio per impedire la distribuzione di risorse con indirizzo IP pubblico. Se le regole relative ai gruppi di sicurezza di rete possono essere usate per bloccare in modo efficace il traffico Internet in ingresso, è possibile ridurre ulteriormente la superficie di attacco impedendo l'uso di indirizzi IP pubblici.   | Iniziare con questo [modello](https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type).  |

### <a name="network-watcher-traffic-analytics"></a>Analisi del traffico di Network Watcher

[Analisi del traffico](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) di Network Watcher usa i dati dei log di flusso e altri log per fornire una panoramica generale del traffico di rete. Questi dati sono utili per controllare la conformità al TIC e identificare le aree problematiche. È possibile usare il dashboard generale per visualizzare rapidamente le macchine virtuali che comunicano con Internet e ottenere un elenco specifico per il routing del TIC.

![Analisi del traffico](media/tic-traffic-analytics-1.png)

Usare la **mappa geografica** per identificare rapidamente le destinazioni fisiche probabili del traffico Internet per le macchine virtuali. È possibile identificare e valutare eventuali aree sospette o modifiche del modello:

![Mappa geografica](media/tic-traffic-analytics-2.png)

La **topologia delle reti virtuali** consente di eseguire un rapido sondaggio delle reti virtuali esistenti:

![Mappa della topologia di rete](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Test di hop successivo in Network Watcher

Le reti nelle aree monitorate da Network Watcher possono eseguire test di hop successivo. Nel portale di Azure è possibile immettere un'origine e una destinazione per un flusso di rete di esempio per consentire a Network Watcher di risolvere la destinazione dell'hop successivo. Eseguire questo test sulle macchine virtuali e sugli indirizzi Internet di esempio per assicurarsi che la destinazione dell'hop successivo sia effettivamente il gateway di rete virtuale previsto.

![Test di hop successivo](media/tic-network-watcher.png)

## <a name="conclusions"></a>Conclusioni

È possibile configurare con facilità l'accesso per Microsoft Azure, Office 365 e Dynamics 365 per favorire la conformità alle linee guida dell'Appendice H del TIC 2.0, come definite nel mese di maggio 2018. Microsoft è consapevole che le linee guida del TIC sono soggette a modifiche e si impegna ad aiutare i clienti a conformarsi a tali linee guida in modo tempestivo al momento del rilascio di una nuova versione.

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>Appendice: Modelli TIC per carichi di lavoro comuni

| Categoria | Carico di lavoro | IaaS | PaaS dedicato/Integrazione di rete virtuale  | Endpoint di servizio  |
|---------|---------|---------|---------|--------|
| Calcolo | Macchine virtuali Linux in Azure | Yes | | |
| Calcolo | Macchine virtuali Windows in Azure | Yes | | |
| Calcolo | set di scalabilità di macchine virtuali | Yes | | |
| Calcolo | Funzioni di Azure | | Ambiente del servizio app | |
| Web e dispositivi mobili | Applicazione Web interna | | Ambiente del servizio app| |
| Web e dispositivi mobili | Applicazione mobile interna | | Ambiente del servizio app | |
| Web e dispositivi mobili | Applicazioni per le API | | Ambiente del servizio app | |
| Contenitori | Servizio contenitore di Azure | | | Yes |
| Contenitori | Servizio Kubernetes di Azure (AKS) \* | | | Yes |
| Database | Database SQL di Azure | | Istanza gestita di database SQL di Azure \* | SQL di Azure |
| Database | Database di Azure per MySQL | | | Yes |
| Database | Database di Azure per PostgreSQL | | | Yes |
| Database | Azure SQL Data Warehouse | | | Yes |
| Database | Azure Cosmos DB | | | Yes |
| Database | Cache Redis di Azure | | Yes | |
| Archiviazione | Archivio BLOB di Azure | Yes | | |
| Archiviazione | File di Azure | Yes | | |
| Archiviazione | Archiviazione code di Azure | Yes | | |
| Archiviazione | Archiviazione tabelle di Azure | Yes | | |
| Archiviazione | Archiviazione su disco di Azure | Yes | | |

\* Anteprima pubblica in Azure per enti pubblici, a partire da maggio 2018.
