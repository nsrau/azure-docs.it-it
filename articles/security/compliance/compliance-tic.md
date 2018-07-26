---
title: Indicazioni su Trusted Internet Connection con Azure
description: Indicazioni su Trusted Internet Connection con Azure e servizi SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: 9d71efa35713500911c67d1df15612b64c8e97da
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990830"
---
# <a name="trusted-internet-connection-guidance"></a>Indicazioni su Trusted Internet Connection

## <a name="background"></a>Background

Lo scopo dell'iniziativa Trusted Internet Connections (TIC) è quello di ottimizzare e standardizzare la sicurezza delle singole connessioni di rete esterne attualmente in uso dalle agenzie federali. La norma è descritta nel memorandum dell'OMB (Office of Management and Budget) [M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

Nel novembre 2007, l'OMB ha stabilito il programma TIC per migliorare la protezione del perimetro di rete federale e le funzioni di risposta agli eventi imprevisti. TIC è stato progettato per eseguire l'analisi di rete di tutto il traffico .gov in ingresso e in uscita al fine di identificare le firme specifiche e i dati basati su un modello, nonché individuare eventuali anomalie di comportamento, ad esempio attività botnet. Alle agenzie è stato richiesto di rafforzare le proprie connessioni di rete esterne e di indirizzare tutto il traffico tramite dispositivi di rilevamento e prevenzione delle intrusioni (noti anche come EINSTEIN) ospitati in un numero limitato di endpoint di rete (noti come Trusted Internet Connections).

In poche parole, l'obiettivo del TIC per le agenzie è conoscere:
- Chi si trova all'interno della rete (soggetti autorizzati o non autorizzati)
- Quando è stato effettuato l'accesso alla rete e perché
- Quali sono state le risorse a cui è stato effettuato l'accesso

Al giorno d'oggi, tutte le connessioni esterne delle agenzie devono essere indirizzate da un TIC approvato dall'OMB. Le agenzie federali devono partecipare al programma TIC come provider di accesso TIC (TICAP) o contattando i servizi con uno dei maggiori provider di servizi Internet di 1° livello detti provider Managed Trusted Internet Protocol Service (MTIPS).  TIC include le funzionalità critiche obbligatorie che vengono eseguite oggi dall'agenzia e dal provider MTIPS. Nella versione corrente di TIC, i dispositivi di rilevamento delle intrusioni EINSTEIN di versione 2 e di prevenzione accelerata dalle intrusioni (3A) EINSTEIN di versione 3 vengono distribuiti a tutti i TICAP e MTIPS e l'agenzia stabilisce un memorandum d'intesa con il dipartimento per la sicurezza interna (Department of Homeland Security, DHS) per offrire funzionalità EINSTEIN ai sistemi federali.

In quanto parte della sua responsabilità nella protezione della rete .gov, DHS necessita dei feed di dati non elaborati dei dati Netflow dell'agenzia per far coincidere gli eventi imprevisti all'interno dell'azienda federale ed eseguire analisi usando strumenti specializzati. I router del DHS offrono la possibilità di raccogliere il traffico di rete IP in ingresso o in uscita da un'interfaccia. Analizzando i dati del flusso di rete, un amministratore di rete può determinare, ad esempio, l'origine e la destinazione del traffico, la classe del servizio e così via. I dati del flusso di rete sono considerati "dati di non contenuto" (ad esempio, intestazione, IP di origine, IP di destinazione, e così via) e consentono al DHS di reperire informazioni sul contenuto; vale a dire, sulle attività degli utenti e le relative tempistiche.

L'iniziativa include anche i criteri di sicurezza, le linee guida e i framework che presuppongono l'infrastruttura locale. Sebbene le agenzie governative si spostino in direzione del cloud per ottenere risparmi sui costi, efficienza operativa e innovazione, in alcuni casi i requisiti di implementazione del TIC stanno rallentando il traffico di rete e limitando la velocità e l'agilità con cui gli utenti degli enti pubblici possono accedere ai dati basati sul cloud.

Questo articolo illustra come le agenzie governative riescano a usare Microsoft Azure per enti pubblici per facilitare la conformità ai criteri del TIC in ambito di servizi IaaS e PaaS.

## <a name="summary-of-azure-networking-options"></a>Riepilogo delle opzioni dei servizi di rete di Azure

Quando ci si connette ai servizi di Azure, sono disponibili tre opzioni principali:

1. Connessione Internet diretta: connettersi ai servizi di Azure direttamente tramite una connessione Internet aperta. Il supporto è pubblico, nonché la connessione. L'applicazione e la crittografia a livello di trasporto sono utilizzati per garantire la privacy. La larghezza di banda è limitata dalla connettività di un sito a Internet ed è possibile servirsi di più provider attivi per garantire resilienza
1. Rete privata virtuale: connettersi alla rete virtuale di Azure in privato tramite un gateway VPN.
Il supporto è pubblico, poiché attraversa una connessione Internet standard di un sito, ma la connessione è crittografata in un tunnel per garantire la privacy. La larghezza di banda è limitata a seconda della configurazione e dei dispositivi VPN selezionati. Le connessioni di Azure da punto a sito sono in genere limitate a 100 Mbps, mentre le connessioni da sito a sito sono limitate a 1,25 Gbps.
1. Microsoft ExpressRoute: ExpressRoute è una connessione diretta ai servizi Microsoft. Poiché la connettività passa tramite un canale in fibra ottica isolato, la connessione può essere pubblica o privata a seconda della configurazione usata. La larghezza di banda è normalmente limitata a un massimo di 10 Gbps.

Esistono diversi modi per soddisfare i requisiti dell'allegato H (considerazioni sul cloud) Trusted Internet Connection originario del documento del DHS "Trusted Internet Connections (TIC) Reference Architecture Document, Version 2.0". In questo documento, si farà riferimento alle indicazioni al TIC del DHS come TIC 2.0.

Per abilitare la connessione dal dipartimento o agenzia (D/A) in Azure o Office 365 senza indirizzare il traffico tramite il TIC del D/A, il D/A deve usare un tunnel crittografato e/o una connessione dedicata al provider di servizi cloud (CSP). I servizi CSP possono garantire che non vi sia connettività agli asset cloud del D/A a disposizione della rete Internet pubblica allo scopo di accedere in modalità diretta e personale all'agenzia.

Office 365 rispetta le disposizioni presenti nell'allegato H del TIC 2.0 che segnala come alternativo l'uso di ExpressRoute con abilitazione a [Peering Microsoft](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) o una connessione Internet che crittografa tutto il traffico con TLS 1.2.  Gli utenti finali del D/A nella rete del D/A possono connettersi usando la rete dell'agenzia e l'infrastruttura TIC tramite Internet. Tutti gli accessi remoti a Internet a Office 365 sono bloccati e indirizzati dall'agenzia. Il D/A può anche connettersi a Office 365 sfruttando una connessione ExpressRoute con abilitazione a Microsoft Peering, un tipo di peering pubblico.  

Solo nel caso di Azure, le opzioni 2 (VPN) e 3 (ExpressRoute) possono soddisfare questi requisiti quando usate in combinazione con i servizi che limitano l'accesso a Internet.

![Diagramma (TIC) Trusted Internet Connection Microsoft](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Come le offerte dell'infrastruttura distribuita come servizio agevolano la conformità al TIC

La conformità ai criteri del TIC tramite IaaS è relativamente semplice, perché i clienti di Azure gestiscono i propri routing di rete virtuale.

Il requisito principale per garantire la conformità all'architettura di riferimento del TIC è garantire che la rete virtuale (VNet) diventi un'estensione privata della rete del dipartimento o dell'agenzia. Per diventare un'estensione _privata_ i criteri richiedono che nessun traffico lasci la rete, ad eccezione della connessione di rete locale del TIC come tramite. Questo processo è noto come "Forzatura di tunneling", che, quando utilizzata a scopo di conformità al TIC, rappresenta il processo di routing di tutto il traffico proveniente da qualsiasi sistema nell'ambiente di CSP a passare attraverso un gateway da sito locale nella rete dell'organizzazione fino alla rete Internet tramite il TIC.

La conformità al TIC di IaaS di Azure può essere suddivisa in due passaggi principali:

1. Configurazione
1. Controllo

### <a name="azure-iaas-tic-compliance-configuration"></a>Configurazione per la conformità al TIC a IaaS di Azure

Per configurare un'architettura conforme al TIC con Azure, è necessario innanzitutto impedire l'accesso diretto a Internet alla rete virtuale e quindi forzare il traffico Internet attraverso la rete locale.

#### <a name="prevent-direct-internet-access"></a>Impedire l'accesso diretto a Internet

La rete IaaS di Azure viene fornita tramite le reti virtuali costituite da subnet a cui sono associati i controller di interfaccia di rete (NIC) delle macchine virtuali.

Lo scenario più semplice per garantire la conformità al TIC è di far sì che una macchina virtuale o una raccolta di queste non possa connettersi ad alcuna risorsa esterna. La disconnessione da reti esterne può essere garantita tramite i gruppi di sicurezza di rete (NSG), che possono essere usati per controllare il traffico a uno o più NIC o subnet nella rete virtuale. Un NSG contiene le regole di controllo di accesso che consentono o negano il traffico in base alla direzione del traffico, al protocollo, all’indirizzo e alla porta di origine e all’indirizzo e alla porta di destinazione. Le regole di un gruppo di sicurezza di rete possono essere modificate in qualsiasi momento e le modifiche vengono applicate a tutte le istanze associate.  Per altre informazioni su come creare un NSG, vedere questo articolo [Creare un NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-on-premises-network"></a>Forzare il traffico Internet tramite la rete locale

Azure crea automaticamente route di sistema e assegna le route a ogni subnet in una rete virtuale. Non è possibile creare route di sistema né rimuoverle, ma è possibile eseguire l'override di alcune route di sistema usando route personalizzate. Azure crea route di sistema predefinite per ogni subnet e aggiunge altre route predefinite facoltative a subnet specifiche, oppure a ogni subnet, quando svengono usate funzionalità specifiche di Azure. Questo routing assicura che il traffico destinato internamente alla rete virtuale rimanga all'interno della rete virtuale, gli spazi di indirizzamento privati chiamati IANA come 10.0.0.0/8 vengono eliminati (a meno che non siano inclusi nello spazio di indirizzamento della rete virtuale) e il routing di "emergenza" di 0.0.0.0/0 per l'endpoint Internet della rete virtuale.

![Forzatura di tunneling del TIC](media/tic-diagram-c.png)

Per assicurarsi che tutto il traffico attraversi il TIC del D/A, tutto il traffico in uscita dalla rete virtuale deve essere indirizzato tramite la connessione locale.  Le route personalizzate vengono create con route definite dall'utente oppure scambiando route Border Gateway Protocol (BGP) tra un gateway di rete locale e un gateway di rete virtuale di Azure. Altre informazioni sulle route definite dall'utente sono reperibili all'indirizzo https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined. Altre informazioni su Border Gateway Protocol sono inoltre reperibili alla pagina https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol.

#### <a name="user-defined-routes"></a>Route definite dall'utente

Se si usa un gateway di rete virtuale basato su route, la forzatura del tunneling può essere eseguita all'interno di Azure mediante l'aggiunta di una route definita dall'utente (UDR) impostando il traffico 0.0.0.0/0 in modo tale che sia indirizzato verso un "Next Hop" del gateway di rete virtuale. Azure assegna la priorità alle route definite dall'utente rispetto a quelle definite dal sistema, in modo che tutto il traffico non di rete virtuale venga inviato al gateway di rete virtuale, che può quindi indirizzarlo in locale. Una volta predefinita, questa route definita dall'utente deve essere associata a tutte le subnet esistenti o appena create all'interno di tutte le reti virtuali nell'ambiente Azure.

![route definite dall'utente e TIC](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>Border Gateway Protocol

Se si usa un gateway di rete virtuale abilitato per ExpressRoute o un Border Gateway Protocol (BGP), BGP è il meccanismo preferito per l'annuncio delle route. Con una route annunciata BGP di 0.0.0.0/0, i gateway di rete virtuale compatibili con ExpressRoute e BGP garantiranno che la route predefinita venga applicata a tutte le subnet all'interno delle reti virtuali.

### <a name="azure-iaas-tic-compliance-auditing"></a>Verifica di conformità del TIC a IaaS di Azure

Azure offre diversi modi per verificare la conformità al TIC.

#### <a name="effective-routes"></a>Route valide

Per verificare che la route predefinita sia stata propagata, è possibile osservare le "route valide" di una determinata macchina virtuale, uno specifico NIC o una tabella di route definite dall'utente. Questa operazione può essere eseguita tramite il portale di Azure come descritto in https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal, o tramite PowerShell, come descritto in https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell. Il pannello di route valide consentirà di visualizzare che le route pertinenti, ovvero definite dall'utente, quelle annunciate via BGP e quelle di sistema si applichino a entità rilevanti, come illustrato di seguito.

![schermata delle route](media/tic-screen-1.png)

**Nota**: non è possibile visualizzare le route valide per un NIC a meno che non siano associate a una macchina virtuale in esecuzione.

#### <a name="network-watcher"></a>Network Watcher

Azure Network Watcher offre vari strumenti che possono essere usati per verificare la conformità al TIC.  Altre informazioni su Network Watcher alla pagina https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview.

##### <a name="network-security-groups-flow-logs"></a>Log dei flussi del gruppo di sicurezza di rete 

Azure Network Watcher offre la possibilità di acquisire i log dei flussi di rete che indicano i metadati che racchiudono il traffico IP. Oltre ad altri dati, i log dei flussi di rete contengono gli indirizzi di origine e di destinazione delle destinazioni. Questa operazione, in combinazione con i log provenienti dal gateway di rete virtuale, i dispositivi Edge locali e/o il TIC, consentirà di monitorare che tutto il traffico venga effettivamente indirizzato in locale. 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Come fanno le offerte della piattaforma distribuita come servizio ad agevolare la conformità al TIC

I servizi PaaS di Azure come Archiviazione di Azure sono accessibili tramite URL raggiungibili da Internet. Tutti gli utenti con credenziali approvate possono accedere alla risorsa, ad esempio un account di archiviazione, da qualsiasi posizione senza attraversamento di un TIC. Per questo motivo, molti clienti di enti pubblici concludono erroneamente che i servizi PaaS di Azure non sono conformi ai criteri del TIC. In realtà, molti servizi PaaS di Azure possono essere conformi ai criteri del TIC usando la stessa architettura di un ambiente IaaS conforme al TIC descritto in precedenza, se questa può essere associata a una rete virtuale (VNet). L'integrazione dei servizi PaaS di Azure a una rete virtuale di Azure consente al servizio di essere accessibile privatamente da tale rete virtuale e consente al routing personalizzato per 0.0.0.0/0 di essere applicato tramite route definite dall'utente o BGP, garantendo che tutto il traffico associato a Internet venga indirizzato in locale per attraversare il TIC.  Alcuni servizi di Azure possono essere integrati nelle reti virtuali usando i modelli seguenti:

- **Distribuire l'istanza dedicata del servizio**: un numero crescente di servizi Paas possono essere distribuiti come istanze dedicate con endpoint collegati a una rete virtuale. Ad esempio, un ambiente del servizio app (ASE) può essere distribuito in modalità "Isolato", che consente all'endpoint di rete di essere vincolato a una rete virtuale. In questo ambiente del servizio app è quindi possibile ospitare svariati servizi PaaS di Azure, ad esempio App Web, API e Funzioni.
- **Endpoint del servizio rete virtuale**: un numero crescente di servizi PaaS offre la possibilità di spostare l'endpoint a un indirizzo IP privato della rete virtuale anziché a uno pubblico.

Di seguito sono elencati i servizi che a partire da maggio 2018 supportano la distribuzione di istanze dedicate in una rete virtuale o un endpoint servizio: * (disponibilità per Azure Commercial, disponibilità in sospeso per Azure per enti pubblici ).

### <a name="service-endpoints"></a>Endpoint servizio

|Service                   |Status            |
|--------------------------|------------------|
|Azure KeyVault            | Anteprima privata  |
|Cosmos DB                 | Anteprima privata  |
|Identità                  | Anteprima privata  |
|Azure Data Lake           | Anteprima privata  |
|Sql Postgress/Mysql       | Anteprima privata  |
|Azure SQL Data Warehouse  | Anteprima pubblica   |
|SQL di Azure                 | GA               |
|Archiviazione                   | GA               |

### <a name="vnet-injection"></a>Aggiunta della rete virtuale

|Service                            |Status            |
|-----------------------------------|------------------|
|Istanza gestita di SQL               | Anteprima pubblica   |
|Servizio contenitore di Azure (AKS)       | Anteprima pubblica   |
|Service Fabric                     | GA               |
|Gestione API                     | GA               |
|Azure Active Directory             | GA               |
|Azure Batch                        | GA               |
|ASE                                | GA               |
|Redis                              | GA               |
|HDI                                | GA               |
|Calcolare un set di scalabilità di macchine virtuali  | GA               |
|Calcolare un servizio cloud              | GA               |

### <a name="vnet-integration-details"></a>Dettagli di integrazione della rete virtuale

Il diagramma seguente illustra il flusso di rete generico per l'accesso ai servizi PaaS tramite l'aggiunta della rete virtuale e il tunneling del servizio rete virtuale.  Per altre informazioni sui gateway di servizi di rete, tag di rete virtuale e di servizio, vedere https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags.

![Opzioni di connettività di PaaS per il TIC](media/tic-diagram-e.png)

1. Connessione privata ad Azure con ExpressRoute. Il peering privato di ExpressRoute con il tunneling forzato consente di forzare tutto il traffico di rete virtuale del cliente tramite ExpressRoute nuovamente in un sito locale. Peering Microsoft non è obbligatorio.
2. Il gateway VPN di Azure usato in combinazione con il peering Microsoft ExpressRoute è utilizzabile per la sovrapposizione della crittografia IPSec end-to-end tra la rete virtuale del cliente e l'Edge locale. 
3. La connettività di rete alla rete virtuale del cliente viene controllata tramite gruppi di sicurezza di rete (NSG) che consentono ai clienti di autorizzare/rifiutare sulla base di IP, porta e protocollo.
4. La rete virtuale del cliente viene estesa al servizio PaaS tramite la creazione di un endpoint di servizio per il servizio del cliente.
5. L'endpoint di servizio PaaS è protetto per ricorrere all'impostazione predefinita di negazione totale o di autorizzazione al solo accesso dalla subnet specificata all'interno della rete virtuale del cliente.  Il valore predefinito di negazione totale include inoltre le connessioni provenienti da Internet.
6. Qualsiasi altro servizio di Azure che deve accedere alle risorse all'interno della rete virtuale del cliente deve essere:  
  a. Distribuito direttamente nella rete virtuale  
  b. Con accesso selettivo sulla base delle indicazioni fornite dal servizio di Azure corrispondente.

#### <a name="option-1-dedicated-instance-vnet-injection"></a>Opzione 1: istanza dedicata "Aggiunta di rete virtuale"

Con l'aggiunta della rete virtuale, i clienti possono distribuire in modo selettivo istanze dedicate di un determinato servizio di Azure, ad esempio HDInsight, nella propria rete virtuale. Le istanze dei servizi vengono distribuite in una subnet dedicata nella rete virtuale del cliente. L'aggiunta della rete virtuale consente l'accesso alle risorse del servizio tramite gli indirizzi instradabili non Internet.  Le istanze locali possono accedere a queste istanze di servizio tramite lo spazio di indirizzamento della rete virtuale direttamente con ExpressRoute o VPN da sito a sito, invece di aprire i firewall allo spazio di indirizzamento Internet pubblico. Con un'istanza dedicata collegata a un endpoint, possono essere impiegate le stesse strategie usate per la conformità di IaaS al TIC, con un routing predefinito che garantisce che il traffico associato a Internet è reindirizzato a un gateway di rete virtuale associato per la rete locale. L'accesso in entrata e in uscita può essere ulteriormente controllato tramite gruppi di sicurezza di rete (NSG) per la subnet specifica.

![Diagramma della panoramica di aggiunta della rete virtuale](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>Opzione 2: endpoint del servizio di rete virtuale 

Un numero crescente di servizi multi-tenant di Azure offre la funzionalità "Endpoint di servizio", un metodo alternativo per l'integrazione delle reti virtuali di Azure. Gli endpoint del servizio di rete virtuale estendono lo spazio degli indirizzi IP della rete virtuale e l'identità della rete virtuale ai servizi tramite una connessione diretta.  Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure. Dopo aver abilitato un endpoint del servizio per un servizio, le connessioni al servizio possono essere limitate a tale rete virtuale tramite i criteri esposti dal servizio. Vengono applicati i controlli di accesso alla piattaforma dal servizio di Azure e l'accesso alla risorsa bloccata viene concesso solo se la richiesta ha origine dalla rete virtuale/subnet consentite e/o dai due indirizzi IP usati per identificare il traffico in locale se si usa ExpressRoute. Ciò consente di impedire con efficacia che il traffico in entrata/uscita lasci direttamente il servizio PaaS.

![Diagramma della panoramica di endpoint di servizio](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>Usare strumenti nativi su cloud per la consapevolezza situazionale di rete

Azure offre strumenti nativi su cloud per garantire la consapevolezza situazionale necessaria per comprendere i flussi di traffico della rete. Non devono conformarsi obbligatoriamente ai criteri del TIC, ma possono migliorare notevolmente le funzionalità di sicurezza.

### <a name="azure-policy"></a>Criteri di Azure

Criteri di Azure (https://azure.microsoft.com/services/azure-policy/) è un servizio di Azure che offre all'organizzazione migliori possibilità di controllare e applicare le iniziative di conformità.  È attualmente disponibile in anteprima pubblica nei cloud di Azure Commercial, ma non è ancora in Microsoft Azure per enti pubblici; i clienti attenti al TIC possono iniziare a pianificare e testare le regole dei criteri per una garanzia di conformità futura. Criteri di Azure è destinato a livello di sottoscrizione e fornisce un'interfaccia centralizzata per la gestione delle iniziative, le definizioni dei criteri, i risultati di controllo e l'implementazione e la gestione delle eccezioni. Oltre alle molte definizioni di Criteri di Azure predefiniti, gli amministratori possono definire le proprie definizioni personalizzate tramite semplici modelli json. Per molti clienti, Microsoft consiglia l'assegnazione delle priorità al controllo e non all'implementazione, laddove possibile.

I criteri di esempio seguenti possono risultare utili per gli scenari di conformità a TIC:

|Criterio  |Scenario di esempio  |Modello iniziale  |
|---------|---------|---------|
|Applicare la tabella di route definita dall'utente |     Assicurarsi che la route predefinita per tutte le reti virtuali sia rivolta a un gateway di rete virtuale approvato per il routing del sito locale | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|Controllare se Network Watcher non è abilitato per l'area  | Assicurarsi che Network Watcher sia abilitato per tutte le aree utilizzate  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|NSG X in ogni subnet  | Assicurarsi che un NSG (o un set di NSG approvati) con il traffico Internet bloccato venga applicato a tutte le subnet in tutte le reti virtuali | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|NSG X in ogni scheda di interfaccia di rete | Assicurarsi che un NSG con il traffico Internet bloccato venga applicato a tutte i NIC in tutte le macchine virtuali. | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|Usare una rete virtuale approvata per le interfacce di rete delle macchine virtuali  | Assicurarsi che tutte i NIC si trovino in una rete virtuale approvata | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|Percorsi consentiti | Assicurarsi che tutte le risorse vengano distribuite in aree con configurazione di reti virtuali e Network Watcher conformi  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|Tipi di risorse, ad esempio PublicIPs, non consentiti  | Non consentire la distribuzione dei tipi di risorse che non dispongono di un piano di conformità. Ad esempio, questo criterio è stato usato per non consentire la distribuzione delle risorse con indirizzo IP pubblico. Mentre le regole NSG possono essere usate per bloccare in modo efficace il traffico Internet in ingresso, impedendo l'uso di indirizzi IP pubblici si riduce ulteriormente la superficie di attacco.    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>[Analisi del traffico](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/) di Azure

Analisi del traffico di Azure Network Watcher usa i dati del log di flusso e altri log per fornire una panoramica generale del traffico di rete. Questi dati possono essere utili per il controllo di conformità al TIC e per l'identificazione di aree problematiche. Una dashboard generale può essere utilizzata per visualizzare rapidamente quali sono le macchine virtuali a comunicare con Internet e quali potrebbero fornire successivamente un elenco specifico per il routing del TIC.

![Schermata di analisi del traffico](media/tic-traffic-analytics-1.png)

Per identificare rapidamente le destinazioni fisiche probabili del traffico Internet nelle macchine virtuali è possibile servirsi di "mappa geografica"che consente di identificare e valutare i percorsi sospette o le modifiche al modello.

![Schermata di analisi del traffico](media/tic-traffic-analytics-2.png)

Una mappa della topologia di rete consente un rapido sondaggio delle reti virtuali esistenti:

![Schermata di analisi del traffico](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Next Hop in Azure Network Watcher

Le reti in aree monitorate da Network Watcher possono condurre test "Next Hop"; consentendo un facile accesso dal portale al tipo in un'origine e una destinazione per un flusso di rete di esempio, per il quale Network Watcher risolverà la destinazione "Next Hop". Può essere utilizzato al posto delle macchine virtuali e degli indirizzi Internet di esempio per assicurarsi che il "Next Hop" sia effettivamente il gateway della rete virtuale.

![Next Hop in Network Watcher](media/tic-network-watcher.png)

## <a name="conclusions"></a>Conclusioni

L'accesso a Microsoft Azure, Office 365 e Dynamics 365 può essere configurato facilmente per favorire la conformità al materiale sussidiario dell'allegato H di TIC 2.0, come scritto e definito a maggio 2018.  Microsoft è consapevole che questo materiale sussidiario è soggetto a modifiche e si impegnerà ad aiutare i clienti a conformarsi a tali indicazioni in modo tempestivo al momento del rilascio di una nuova versione.

## <a name="appendix-tic-patterns-for-common-workloads"></a>Allegato: modelli TIC per carichi di lavoro comuni

| Categoria | Carico di lavoro | IaaS | PaaS dedicato/Integrazione rete virtuale  | Endpoint servizio  |
|---------|---------|---------|---------|--------|
| Calcolo | Macchine virtuali Linux | sì | | |
| Calcolo | Macchine virtuali Windows | sì | | |
| Calcolo | Set di scalabilità di macchine virtuali | sì | | |
| Calcolo | Funzioni di Azure | | tramite l'ambiente del servizio app (ASE) | |
| Web e dispositivi mobili | Applicazione Web interna | | tramite l'ambiente del servizio app (ASE) | |
| Web e dispositivi mobili | Applicazione mobile interna | | tramite l'ambiente del servizio app (ASE) | |
| Web e dispositivi mobili | App per le API | | tramite l'ambiente del servizio app (ASE) | |
| Contenitori | Servizio contenitore di Azure (ACS) | | | sì |
| Contenitori | Servizio contenitore di Azure (AKS)* | | | sì |
| Database | Database SQL | | Istanza gestita di database SQL di Azure* | SQL di Azure |
| Database | Database di Azure per MySQL | | | sì |
| Database | Database di Azure per PostgreSQL | | | sì |
| Database | SQL Data Warehouse | | | sì |
| Database | Azure Cosmos DB | | | sì |
| Database | Cache Redis | | sì | |
| Archiviazione | Blobs | sì | | |
| Archiviazione | File | sì | | |
| Archiviazione | Queues | sì | | |
| Archiviazione | Tabelle | sì | | |
| Archiviazione | Dischi | sì | | |

*: Anteprima pubblica in Azure per enti pubblici, a partire da maggio 2018  
**: Anteprima privata in Azure per enti pubblici, a partire da maggio 2018
