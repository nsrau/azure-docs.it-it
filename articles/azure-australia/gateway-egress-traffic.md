---
title: Controllo del traffico in uscita in Azure Australia
description: Elementi chiave del controllo del traffico in uscita in Azure per soddisfare i requisiti del governo australiano per i gateway Internet sicuri
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: grgale
ms.openlocfilehash: 7922846d161b7a0cbda32101b6bbb40a1741b323
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602093"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>Controllo del traffico in uscita in Azure Australia

Un componente fondamentale della protezione dei sistemi ICT consiste nel controllare il traffico di rete. La limitazione della comunicazione al solo traffico necessario per il funzionamento di un sistema riduce il rischio di compromissione. La visibilità e il controllo dei sistemi esterni con cui le applicazioni e i servizi comunicano con consentono di rilevare sistemi compromessi e di exfiltration di dati con tentativi o riusciti. Questo articolo fornisce informazioni su come funziona il traffico di rete in uscita in Azure e fornisce indicazioni per l'implementazione dei controlli di sicurezza di rete per un sistema connesso a Internet che è allineato con l'australiano Cyber Security Centre (ACSC) Linee guida per gli utenti e finalità del manuale di sicurezza delle informazioni di ACSC (ISM).

## <a name="requirements"></a>Requisiti

I requisiti di sicurezza generali per i sistemi Commonwealth sono definiti in ISM. Per aiutare le entità Commonwealth nell'implementazione della sicurezza di rete, acsc _ha pubblicato ACSC Protect: Implementazione della segmentazione e della_separazione della rete e per facilitare la protezione dei sistemi negli ambienti cloud, acsc ha pubblicato la _sicurezza del cloud computing per i tenant_.

I documenti ACSC descrivono il contesto per l'implementazione della sicurezza di rete e il controllo del traffico e forniscono consigli pratici per la progettazione e la configurazione della rete.

I seguenti requisiti chiave per il controllo del traffico in uscita in Azure sono stati identificati nei documenti ACSC.

Descrizione|Source
--------------- |------------------
**Implementare la segmentazione e la separazione della rete**, ad esempio, usare un'architettura a più livelli, usando firewall basati su host e controlli di accesso alla rete per limitare la connettività di rete in ingresso e in uscita solo alle porte e ai protocolli richiesti.| [Cloud computing per tenant](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
**Implementare una larghezza di banda sufficientemente elevata, bassa latenza, connettività di rete affidabile** tra il tenant (inclusi gli utenti remoti del tenant) e il servizio cloud per soddisfare i requisiti di disponibilità del tenant  | [Protezione ACSC: Implementazione della segmentazione e della separazione della rete](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Applicare tecnologie non solo al livello di rete**. Ogni host e rete deve essere segmentato e separato, laddove possibile, al livello più basso che può essere praticamente gestito. Nella maggior parte dei casi, si applica dal livello di collegamento dati fino al livello applicazione incluso. negli ambienti sensibili, tuttavia, l'isolamento fisico potrebbe essere appropriato. Le misure basate su host e a livello di rete devono essere distribuite in modo complementare ed essere monitorate centralmente. È sufficiente implementare un firewall o un appliance di sicurezza perché l'unica misura di sicurezza non è sufficiente. |[Protezione ACSC: Implementazione della segmentazione e della separazione della rete](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Usare i principi del privilegio minimo e la necessità di essere a conoscenza**. Se un host, un servizio o una rete non deve comunicare con un altro host, servizio o rete, non dovrebbe essere consentito. Se un host, un servizio o una rete devono comunicare solo con un altro host, un servizio o una rete su una porta o un protocollo specifico, deve essere limitato solo a tali porte e protocolli. L'adozione di questi principi in una rete completerà la minimizzazione dei privilegi utente e aumenterà significativamente il comportamento di sicurezza complessivo dell'ambiente. |[Protezione ACSC: Implementazione della segmentazione e della separazione della rete](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Separare host e reti in base alla loro sensibilità o criticità per le operazioni aziendali**. Questa operazione può includere l'uso di hardware o piattaforme diverse a seconda di classificazioni di sicurezza, domini di sicurezza o requisiti di disponibilità/integrità per determinati host o reti. In particolare, le reti di gestione separate e considerano l'isolamento fisico delle reti di gestione fuori banda per ambienti sensibili. |[Protezione ACSC: Implementazione della segmentazione e della separazione della rete](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Identificare, autenticare e autorizzare l'accesso da tutte le entità a tutte le altre entità**. Tutti gli utenti, gli host e i servizi devono disporre dell'accesso a tutti gli altri utenti, host e servizi, limitati solo a quelli necessari per eseguire le funzioni o i compiti designati. Tutti i servizi legacy o locali che ignorano o effettuano il downgrade della forza dei servizi di identificazione, autenticazione e autorizzazione devono essere disabilitati laddove possibile e possono essere monitorati attentamente. |[Protezione ACSC: Implementazione della segmentazione e della separazione della rete](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
**Implementare Consenti elenco di traffico di rete anziché nega elenco**. Consentire l'accesso solo per un traffico di rete valido noto (traffico identificato, autenticato e autorizzato), anziché negare l'accesso a traffico di rete non valido noto, ad esempio bloccando un indirizzo o un servizio specifico. Consenti agli elenchi il risultato di un criterio di sicurezza superiore per negare gli elenchi e migliorare significativamente la capacità di rilevare e valutare potenziali intrusioni di rete. |[Protezione ACSC: Implementazione della segmentazione e della separazione della rete](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
La **definizione di un elenco Consenti di siti Web consentiti e il blocco di tutti i siti Web** non in elenco comporta la rimozione efficace di una delle tecniche più comuni di distribuzione dei dati e exfiltration utilizzate da un antagonista. Se gli utenti hanno un requisito legittimo per accedere a numerosi siti Web o a un elenco di siti Web in rapida evoluzione; è necessario considerare i costi di un'implementazione di questo tipo. Anche un elenco di consentiti relativamente permissivo offre una maggiore sicurezza rispetto a quelli che non si basano su elenchi di accesso negato o a nessuna restrizione, riducendo comunque i costi di implementazione Un esempio di elenco di Consenti permissivo potrebbe consentire l'intero sottodominio australiano, ovvero "*. au", o consentire i primi 1.000 siti dalla classificazione del sito di Alexa (dopo avere filtrato i domini di Dynamic Domain Name System (DDNS) e altri domini non appropriati.| [Manuale per la sicurezza delle informazioni per il governo australiano (ISM)](https://www.cyber.gov.au/ism)
|

Questo articolo fornisce informazioni e indicazioni sul modo in cui il traffico di rete in uscita dall'ambiente Azure è controllato. Vengono illustrati i sistemi distribuiti in Azure tramite l'infrastruttura distribuita come servizio (IaaS) e la piattaforma distribuita come servizio (PaaS).

L'articolo sul [traffico in ingresso del gateway](gateway-ingress-traffic.md) risolve il traffico di rete in ingresso nell'ambiente Azure ed è il compagno di questo articolo per la copertura completa dei controlli di rete.

## <a name="architecture"></a>Architettura

Per controllare adeguatamente il traffico in uscita, quando si progetta e implementa la sicurezza di rete, è necessario innanzitutto comprendere come funziona il traffico di rete in uscita in Azure sia in IaaS che in PaaS. Questa sezione fornisce una panoramica del modo in cui viene elaborato il traffico in uscita generato da una risorsa ospitata in Azure e i controlli di sicurezza disponibili per limitare e controllare il traffico.

### <a name="architecture-components"></a>Componenti dell'architettura

Il diagramma dell'architettura illustrato di seguito illustra i possibili percorsi che il traffico di rete può eseguire quando si esce da un sistema distribuito in una subnet in una rete virtuale. Il traffico in una rete virtuale viene gestito e regolato a livello di subnet, con regole di routing e sicurezza che si applicano alle risorse contenute all'interno di. I componenti correlati al traffico in uscita sono divisi nei sistemi, le route valide, i tipi di hop successivi, i controlli di sicurezza e l'uscita PaaS.

![Architettura](media/egress-traffic.png)

### <a name="systems"></a>Sistemi

I sistemi sono le risorse di Azure e i componenti correlati che generano il traffico in uscita all'interno di una subnet IP che fa parte di una rete virtuale.

| Componente | Descrizione |
| --- | ---|
|Rete virtuale | Un VNet è una risorsa di base all'interno di Azure che fornisce una piattaforma e un limite per la distribuzione delle risorse e l'abilitazione della comunicazione. Il VNet è disponibile all'interno di un'area di Azure e definisce lo spazio degli indirizzi IP e i limiti di routing per le risorse integrate VNet, ad esempio le macchine virtuali.|
|Subnet | Una subnet è un intervallo di indirizzi IP creato all'interno di un VNet. È possibile creare più subnet all'interno di una VNet per la segmentazione della rete.|
|Interfaccia di rete| Un'interfaccia di rete è una risorsa esistente in Azure. Viene collegato a una macchina virtuale a cui è stato assegnato un indirizzo IP instradabile privato non Internet dalla subnet a cui è associato. Questo indirizzo IP viene assegnato in modo dinamico o statico tramite Azure Resource Manager.|
|IP pubblici| Un indirizzo IP pubblico è una risorsa che riserva uno degli indirizzi IP pubblici instradabili di Microsoft, dall'area specificata, da usare all'interno della rete virtuale. Può essere associato a una specifica interfaccia di rete o a una risorsa PaaS, che consente alla risorsa di comunicare con Internet, ExpressRoute e ad altri sistemi PaaS.|
|

### <a name="routes"></a>Route

Il percorso preso dal traffico in uscita dipende dalle route valide per tale risorsa, ovvero il set di route risultante determinato dalla combinazione di route learnt da tutte le origini possibili e dall'applicazione della logica di routing di Azure.

| Componente | DESCRIZIONE |
|--- | ---|
|Le route di sistema| Azure crea automaticamente route di sistema e assegna le route a ogni subnet in una rete virtuale. Non è possibile creare o rimuovere route di sistema, ma è possibile eseguirne l'override con route personalizzate. Azure crea route di sistema predefinite per ogni subnet e aggiunge altre route predefinite facoltative a subnet specifiche o a ogni subnet, quando vengono utilizzate funzionalità specifiche di Azure.|
|Endpoint di servizio| Gli endpoint di servizio forniscono una connessione diretta e privata in uscita da una subnet a una specifica funzionalità PaaS. Gli endpoint di servizio, che sono disponibili solo per un subset di funzionalità PaaS, garantiscono un miglioramento delle prestazioni e della sicurezza per le risorse in un VNet che accede a PaaS.|
|Tabelle di route| Una tabella di route è una risorsa in Azure che può essere creata per specificare le route definite dall'utente (UDR) che possono completare o sostituire le route di sistema o le route learnt tramite Border Gateway Protocol. Ogni UDR specifica una rete, una network mask e un hop successivo. Una tabella di route può essere associata a una subnet e la stessa tabella di route può essere associata a più subnet, ma una subnet può avere solo zero o una tabella di route.|
|Border Gateway Protocol (BGP)| BGP è un protocollo di routing di sistema tra autonomi. Un sistema autonomo è una rete o un gruppo di reti in un'amministrazione comune e con criteri di routing comuni. BGP viene usato per scambiare informazioni di routing tra sistemi autonomi. Il protocollo BGP può essere integrato nelle reti virtuali tramite i gateway di rete virtuale.|
|

### <a name="next-hop-types-defined"></a>Tipi di hop successivi definiti

Ogni route in Azure include l'intervallo di rete e i subnet mask associati e l'hop successivo, che determina la modalità di elaborazione del traffico.

Tipo hop successivo | Descrizione
---- | ----
**Rete virtuale** | Instrada il traffico tra intervalli di indirizzi nello spazio degli indirizzi di una rete virtuale. Azure crea una route con un prefisso degli indirizzi che corrisponde a ogni intervallo di indirizzi definito nello spazio di indirizzi di una rete virtuale. Se per lo spazio di indirizzi della rete virtuale sono definiti più intervalli di indirizzi, Azure crea una singola route per ogni intervallo di indirizzi. Azure instrada automaticamente il traffico tra le subnet all'interno di una VNet usando le route create per ogni intervallo di indirizzi.
**Peering reti virtuali** | Quando viene creato un peering di rete virtuale tra due reti virtuali, viene aggiunta una route per ogni intervallo di indirizzi di ogni rete virtuale alla rete virtuale a cui viene eseguito il peering. Il traffico viene instradato tra le reti virtuali con peering nello stesso modo delle subnet all'interno di una rete virtuale.
**Gateway di rete virtuale** | Una o più route con gateway di rete virtuale elencato come tipo di hop successivo vengono aggiunte quando si aggiunge un gateway di rete virtuale a una rete virtuale. Le route incluse sono quelle configurate all'interno della risorsa gateway di rete locale e tutte le route apprese tramite BGP.
**Appliance virtuale** | Un'appliance virtuale esegue in genere un'applicazione di rete, ad esempio un firewall. Il dispositivo virtuale consente l'elaborazione aggiuntiva del traffico, ad esempio il filtro, l'ispezione o la conversione degli indirizzi. Ogni route con il tipo di hop appliance virtuale deve anche specificare un indirizzo IP hop successivo.
**VirtualNetworkServiceEndpoint** | Gli indirizzi IP pubblici per un servizio specifico vengono aggiunti come route a una subnet con un hop successivo di VirtualNetworkServiceEndpoint quando è abilitato un endpoint di servizio. Gli endpoint di servizio sono abilitati per singoli servizi in singole subnet all'interno di una rete virtuale. Gli indirizzi IP pubblici dei servizi di Azure cambiano periodicamente. Azure gestisce automaticamente gli indirizzi nella tabella di route quando gli indirizzi cambiano.
**Internet** | Il traffico con un hop successivo di Internet uscirà dalla rete virtuale e verrà automaticamente convertito in un indirizzo IP pubblico da un pool dinamico disponibile nell'area di Azure associata o usando un indirizzo IP pubblico configurato per la risorsa. Se l'indirizzo di destinazione è per uno dei servizi di Azure, il traffico viene indirizzato direttamente al servizio tramite la rete backbone di Azure, anziché indirizzare il traffico a Internet. Il traffico tra i servizi di Azure non attraversa Internet, indipendentemente dall'area di Azure in cui si trova la rete virtuale o in cui viene distribuita un'istanza del servizio di Azure.
**None** | Il traffico con un hop successivo di None viene eliminato. Azure crea route predefinite di sistema per i prefissi degli indirizzi riservati con None come tipo di hop successivo. Le route con un hop successivo di None possono essere aggiunte anche usando le tabelle di route per impedire che il traffico venga instradato a reti specifiche.
|

### <a name="security-controls"></a>Controlli di sicurezza

Control | DESCRIZIONE
----- | -----
**Gruppi di sicurezza di rete (gruppi)** | Gruppi controlla il traffico all'interno e all'esterno delle risorse della rete virtuale in Azure. Gruppi applicano le regole per i flussi di traffico consentiti o negati, che includono il traffico in Azure e tra Azure e reti esterne, ad esempio in locale o in Internet. Gruppi vengono applicate alle subnet all'interno di una rete virtuale o a singole interfacce di rete.
**Firewall di Azure** | Il firewall di Azure è un servizio di sicurezza di rete gestito e basato sul cloud che protegge le risorse della rete virtuale di Azure. È un firewall con stato completo come servizio con disponibilità elevata incorporata e scalabilità del cloud senza restrizioni. Il firewall di Azure può essere configurato con regole di filtro di rete tradizionali basate su indirizzi IP, protocolli e porte, ma supporta anche l'applicazione di filtri in base a nomi di dominio completi (FQDN), tag di servizio e Intelligence per le minacce incorporata.
**Appliance virtuale di rete** | Le appliance virtuali di rete sono supporti per macchine virtuali in grado di fornire funzionalità di rete, sicurezza e altre funzioni in Azure. Appliance virtuali supportano le funzionalità e i servizi di rete sotto forma di macchine virtuali in reti virtuali e distribuzioni. Appliance virtuali può essere usato per soddisfare requisiti specifici, integrarsi con gli strumenti operativi e di gestione oppure per garantire la coerenza con i prodotti esistenti. Azure supporta numerose appliance virtuali di rete di terze parti, tra cui Web application firewall (WAF), firewall, gateway/router, controller di distribuzione delle applicazioni e utilità di ottimizzazione WAN.
**Criteri endpoint servizio (anteprima)** | I criteri dell'endpoint del servizio rete virtuale consentono di filtrare il traffico di rete virtuale ai servizi di Azure, consentendo solo le risorse specifiche del servizio di Azure sugli endpoint di servizio. I criteri degli endpoint forniscono il controllo granulare dell'accesso per il traffico di rete virtuale per i servizi di Azure.
**Criteri di Azure** | Criteri di Azure è un servizio di Azure per la creazione, l'assegnazione e la gestione dei criteri. Questi criteri usano regole per controllare i tipi di risorse che è possibile distribuire e la configurazione di tali risorse. I criteri possono essere usati per applicare la conformità impedendo la distribuzione delle risorse se non soddisfano i requisiti o possono essere usati per il monitoraggio per segnalare lo stato di conformità.
|

### <a name="paas-egress"></a>PaaS in uscita

La maggior parte delle risorse di PaaS non genera il traffico in uscita, ma risponde alle richieste in ingresso, ad esempio un gateway applicazione, l'archiviazione, un database SQL e così via, oppure i dati di inoltro di altre risorse, ad esempio il bus di servizio e il servizio di inoltro di Azure. I flussi di comunicazione di rete tra le risorse di PaaS, ad esempio i servizi app, per l'archiviazione o i database SQL sono controllati e contenuti in Azure e protetti tramite identità e altri controlli di configurazione delle risorse anziché la segmentazione o la separazione di rete.

Le risorse PaaS distribuite in una rete virtuale ricevono indirizzi IP dedicati e sono soggette a controlli di routing e gruppi in modo analogo alle altre risorse della rete virtuale. Le risorse PaaS che non esistono in una rete virtuale utilizzeranno un pool di indirizzi IP condivisi tra tutte le istanze della risorsa, che vengono pubblicate tramite documentazione Microsoft o possono essere determinate tramite Azure Resource Manager.

## <a name="general-guidance"></a>Indicazioni generali

Per progettare e creare soluzioni sicure in Azure, è fondamentale comprendere e controllare il traffico di rete in modo che possa verificarsi solo la comunicazione identificata e autorizzata. Lo scopo di questo materiale sussidiario e le linee guida per i componenti specifici nelle sezioni successive sono la descrizione degli strumenti e dei servizi che possono essere utilizzati per applicare i principi [descritti in ACSC Protect: Implementazione della segmentazione e della](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) separazione della rete nei carichi di lavoro di Azure. Questo include informazioni dettagliate su come creare un'architettura virtuale per la protezione delle risorse quando non è possibile applicare gli stessi controlli fisici e di rete tradizionali possibili in un ambiente locale.

### <a name="guidance"></a>Materiale sussidiario

* Limitare il numero di punti in uscita per le reti virtuali
* Eseguire l'override della route predefinita di sistema per tutte le subnet che non necessitano di comunicazione diretta in uscita verso Internet
* Progettare e implementare un'architettura di rete completa per identificare e controllare tutti i punti di ingresso e uscita per le risorse di Azure
* Prendere in considerazione l'uso di una progettazione di rete Hub e spoke per le reti virtuali, come illustrato nella documentazione di Microsoft Virtual Data Center (VCC)
* Usare i prodotti con funzionalità di sicurezza incorporate per le connessioni in uscita a Internet (ad esempio, il firewall di Azure, le appliance virtuali di rete o i proxy Web)
* Usare i controlli Identity, ad esempio l'accesso basato sui ruoli, l'accesso condizionale e l'autenticazione a più fattori per limitare i privilegi di configurazione di rete
* Implementare i blocchi per impedire la modifica o l'eliminazione degli elementi chiave della configurazione di rete
* Distribuire PaaS in una configurazione integrata VNet per aumentare la separazione e il controllo
* Implementare ExpressRoute per la connettività con le reti locali
* Implementare VPN per l'integrazione con reti esterne
* Usare i criteri di Azure per limitare le aree e le risorse solo a quelle necessarie per le funzionalità di sistema
* Usare i criteri di Azure per applicare la configurazione della sicurezza di base per le risorse
* Sfruttare Network Watcher e monitoraggio di Azure per la registrazione, il controllo e la visibilità del traffico di rete in Azure

### <a name="resources"></a>Risorse

Elemento | Collegamento
-----------| ---------
_Documenti australiani sulla conformità alle normative e ai criteri, incluse le linee guida_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Data Center virtuale di Azure_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)
_Segmentazione della rete ACSC_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_Sicurezza cloud ACSC per i tenant_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_Manuale di ACSC Information Security_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>Linee guida sui componenti

Questa sezione fornisce ulteriori indicazioni sui singoli componenti rilevanti per il traffico in uscita per i sistemi distribuiti in Azure. Ogni sezione descrive lo scopo del componente specifico con collegamenti alla documentazione e alle guide di configurazione che possono essere usate per semplificare le attività di progettazione e compilazione.

### <a name="systems-security"></a>Sicurezza dei sistemi

Tutte le comunicazioni con le risorse all'interno di Azure passano attraverso l'infrastruttura di rete gestita da Microsoft, che offre connettività e funzionalità di sicurezza. Una gamma di protezioni viene messa automaticamente a disposizione da Microsoft per proteggere la piattaforma e l'infrastruttura di rete di Azure e sono disponibili funzionalità aggiuntive come servizi in Azure per controllare il traffico di rete e stabilire la segmentazione della rete e segregazione.

### <a name="virtual-network-vnet"></a>Rete virtuale

Le reti virtuali sono uno dei componenti fondamentali per la rete in Azure. Le reti virtuali definiscono uno spazio degli indirizzi IP e un limite di routing da usare in un'ampia gamma di sistemi. Le reti virtuali sono divise in subnet e tutte le subnet all'interno di una rete virtuale hanno una route di rete diretta tra loro. Con i gateway di rete virtuale (ExpressRoute o VPN), i sistemi all'interno di una rete virtuale possono essere integrati con ambienti locali ed esterni e tramite Azure fornito Network Address Translation (NAT) e l'allocazione di indirizzi IP pubblici, i sistemi possono connettersi a Internet o ad altre aree e servizi di Azure. Comprendere le reti virtuali e i parametri di configurazione e il routing associati è fondamentale per la comprensione e il controllo del traffico di rete in uscita.

Poiché le reti virtuali formano lo spazio degli indirizzi di base e il limite di routing all'interno di Azure, è possibile usarlo come blocco predefinito primario di segmentazione e separazione della rete.

| Risorsa | Collegamento |
| --- | --- |
| *Panoramica sulle reti virtuali* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) |
| *Guida alle procedure per la pianificazione delle reti virtuali*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) |
| *Guida introduttiva alla creazione di una rete virtuale* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)
|

### <a name="subnet"></a>Subnet

Le subnet sono un componente fondamentale per la segmentazione e la separazione della rete in Azure. Le subnet possono essere usate per fornire la separazione tra i sistemi. Una subnet è la risorsa all'interno di una rete virtuale in cui vengono applicati gruppi, le tabelle di route e gli endpoint di servizio. Le subnet possono essere usate come indirizzi di origine e di destinazione per le regole del firewall e gli elenchi di controllo di accesso.

Le subnet all'interno di una rete virtuale devono essere pianificate per soddisfare i requisiti dei carichi di lavoro e dei sistemi. I singoli utenti interessati alla progettazione o all'implementazione delle subnet devono fare riferimento alle linee guida ACSC per la segmentazione della rete per determinare il modo in cui i sistemi devono essere raggruppati in una subnet.

|Risorsa|Collegamento|
|---|---|
|*Aggiungere, modificare o eliminare una subnet di rete virtuale* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
|

### <a name="network-interface"></a>Interfaccia di rete

Le interfacce di rete sono l'origine di tutto il traffico in uscita da una macchina virtuale. Le interfacce di rete consentono la configurazione degli indirizzi IP e possono essere usate per applicare gruppi o per il routing del traffico attraverso un'appliance virtuale di rete. Le interfacce di rete per le macchine virtuali devono essere pianificate e configurate in modo appropriato per essere allineate agli obiettivi generali di separazione e segmentazione della rete.

|Risorsa|Collegamento|
|---|---|
|*Creare, modificare o eliminare un'interfaccia di rete* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface) |
|*Indirizzi IP dell'interfaccia di rete*               | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)
|

### <a name="vnet-integrated-paas"></a>PaaS integrato VNet

PaaS può fornire funzionalità e disponibilità avanzate e ridurre il sovraccarico di gestione, ma deve essere protetto in modo appropriato. Per aumentare il controllo, applicare la segmentazione della rete o fornire un punto di uscita sicuro per le applicazioni e i servizi, molte funzionalità PaaS possono essere integrate con una rete virtuale.

Grazie all'utilizzo di PaaS come parte integrante dell'architettura del sistema o dell'applicazione, Microsoft offre diversi meccanismi per la distribuzione di PaaS in una rete virtuale. La metodologia di distribuzione consente di limitare l'accesso garantendo la connettività e l'integrazione con i sistemi e le applicazioni interni. Gli esempi includono gli ambienti del servizio app, le istanze gestite di SQL e altro ancora.

Quando si distribuisce PaaS in una rete virtuale in cui sono stati implementati i controlli routing e NSG, è fondamentale comprendere i requisiti di comunicazione specifici della risorsa, tra cui l'accesso di gestione da parte dei servizi Microsoft e il percorso il traffico delle comunicazioni verrà richiesto quando si risponde alle richieste in ingresso da questi servizi.

| Risorsa  | Collegamento  |
| --- | --- |
| *Integrazione della rete virtuale per i servizi di Azure* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services) |
| *Guida alle procedure per integrare l'app con una rete virtuale di Azure* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
|

### <a name="public-ip"></a>IP pubblico

Gli indirizzi IP pubblici vengono usati durante la comunicazione all'esterno di una rete virtuale. Sono incluse le risorse PaaS e le route con un hop successivo di Internet. Le entità Commonwealth devono pianificare l'allocazione degli indirizzi IP pubblici con attenzione e assegnarle solo alle risorse in cui è presente un requisito autentico. Come procedura di progettazione generale, gli indirizzi IP pubblici devono essere allocati ai punti di uscita controllati per la rete virtuale, ad esempio il firewall di Azure, il gateway VPN o le appliance virtuali di rete.

|Risorsa|Collegamento|
|---|---|
|*Panoramica di Indirizzi IP pubblici*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) |
|*Creare, modificare o eliminare un indirizzo IP pubblico* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)
|

## <a name="effective-routes"></a>Route valide

Le route valide sono il set di route risultante determinato dalla combinazione di route di sistema, endpoint di servizio, tabelle di route e BGP e l'applicazione della logica di routing di Azure. Quando il traffico in uscita viene inviato da una subnet, Azure seleziona una route in base all'indirizzo IP di destinazione, usando l'algoritmo di corrispondenza del prefisso più lungo. Se più route contengono lo stesso prefisso degli indirizzi, Azure seleziona il tipo di route in base alla priorità seguente:

1. Route definita dall'utente
2. Route BGP
3. Route di sistema

È importante notare che le route di sistema per il traffico correlato alla rete virtuale, ai peering di rete virtuale o agli endpoint del servizio di rete virtuale sono le route preferite anche se le route BGP sono più specifiche.

I singoli utenti interessati alla progettazione o all'implementazione delle topologie di routing in Azure devono comprendere il modo in cui Azure instrada il traffico e sviluppare un'architettura che bilancia la funzionalità necessaria dei sistemi con la sicurezza e la visibilità necessarie. È necessario prestare attenzione a pianificare l'ambiente in modo appropriato per evitare interventi eccessivi ed eccezioni ai comportamenti di routing, in quanto questa operazione aumenterà la complessità e potrebbe causare la risoluzione dei problemi e la ricerca di errori più complessi e che richiedono molto tempo.

| Risorsa | Collegamento  |
| --- | --- |
| *Visualizza route valide* | <https://docs.microsoft.com/azure/virtual-network/manage-route-table#view-effective-routes> |
|

### <a name="system-routes"></a>Route di sistema

Per le [Route di sistema](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#system-routes), i singoli utenti interessati alla progettazione o all'implementazione di reti virtuali devono comprendere le route di sistema predefinite e le opzioni disponibili per completare o sostituire tali route.

### <a name="service-endpoints"></a>Endpoint di servizio

L'abilitazione degli [endpoint di servizio](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) in una subnet fornisce un percorso di comunicazione diretta alla risorsa PaaS associata. In questo modo è possibile migliorare le prestazioni e la sicurezza limitando il percorso di comunicazione disponibile a tale servizio. L'uso degli endpoint di servizio introduce un percorso di exfiltration di dati potenziale poiché la configurazione predefinita consente l'accesso a tutte le istanze del servizio PaaS anziché alle istanze specifiche necessarie per un'applicazione o un sistema.

Le entità Commonwealth devono valutare il rischio associato alla fornitura dell'accesso diretto alla risorsa PaaS, incluse la probabilità e la conseguenza del percorso usato in modo improprio.

Per ridurre i rischi potenziali associati agli endpoint di servizio, implementare i criteri dell'endpoint di servizio laddove possibile oppure provare ad abilitare gli endpoint di servizio in un firewall di Azure o una subnet appliance virtuale di Azure e di indirizzare il traffico da subnet specifiche attraverso la è possibile applicare filtri, monitoraggi o ispezioni.

|Risorsa|Collegamento|
|---|---|
|*Esercitazione: Limitare l'accesso di rete alle risorse PaaS con gli endpoint del servizio rete virtuale usando il portale di Azure* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

### <a name="route-tables"></a>Tabelle route

Le tabelle di route forniscono un meccanismo configurato da un amministratore per controllare il traffico di rete in Azure. Le tabelle di route possono essere utilizzate per inoltrare il traffico attraverso un firewall o un appliance virtuale di Azure, connettersi direttamente a risorse esterne o eseguire l'override delle route di sistema di Azure. È anche possibile usare le tabelle di route per impedire che le reti apprese tramite un gateway di rete virtuale siano disponibili per le risorse in una subnet disabilitando la propagazione della route del gateway di rete virtuale.

|Risorsa|Collegamento|
|---|---|
|*Concetti di routing-route personalizzate* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes)|
|*Esercitazione: Instrada il traffico di rete* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)|
|

### <a name="border-gateway-protocol-bgp"></a>Border Gateway Protocol (BGP)

Il protocollo BGP può essere usato dai gateway di rete virtuale per scambiare dinamicamente le informazioni di routing con reti locali o esterne. BGP si applica a una rete virtuale quando viene configurata tramite un gateway di rete virtuale ExpressRoute tramite peering privato ExpressRoute e quando è abilitata in un gateway VPN di Azure.

Per comprendere il comportamento e le opzioni di configurazione disponibili per BGP in Azure, i singoli utenti interessati alla progettazione o all'implementazione di reti virtuali e gateway di rete virtuale in Azure possono richiedere tempo.

|Risorsa|Collegamento|
|---|---|
|*Concetti di routing: BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools)|
|*Requisiti di routing di ExpressRoute* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](https://docs.microsoft.com/azure/expressroute/expressroute-routing)|
|*Informazioni su BGP con il gateway VPN di Azure* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview)|
|*Esercitazione: Configurare una VPN da sito a sito tramite il peering Microsoft ExpressRoute* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering)|
|

## <a name="next-hop-types"></a>Tipi di hop successivi

### <a name="virtual-network"></a>Rete virtuale

Le route con un hop successivo della rete virtuale vengono aggiunte automaticamente come route di sistema, ma possono essere aggiunte anche alle route definite dall'utente per indirizzare il traffico alla rete virtuale nelle istanze in cui è stata sostituita la route di sistema.

### <a name="vnet-peering"></a>Peering di rete virtuale

Il peering VNet consente la comunicazione tra due reti virtuali diversi. La configurazione del peering di VNet deve essere abilitata in ogni rete virtuale, ma non è necessario che le reti virtuali si trovino nella stessa area, sottoscrizione o associata allo stesso tenant di Azure Active Directory (Azure AD).

Quando si configura il peering VNet, è fondamentale che i singoli utenti interessati alla progettazione o all'implementazione del peering VNet comprendano i quattro parametri di configurazione associati e il modo in cui si applicano a ogni lato del peer:

1. **Consenti accesso alla rete virtuale**: Selezionare **abilitato** (impostazione predefinita) per abilitare la comunicazione tra le due reti virtuali. L'abilitazione della comunicazione tra reti virtuali consente alle risorse connesse a una o all'altra delle reti virtuali di comunicare tra loro con la stessa larghezza di banda e latenza che userebbero se fossero connesse alla stessa rete virtuale.
2. **Consenti traffico inoltrato:** Selezionare questa casella per consentire al traffico *trasmesso* da un traffico di rete non originato dalla rete virtuale di passare a questa rete virtuale tramite un peering. Questa impostazione è fondamentale per l'implementazione di una topologia di rete hub-spoke.
3. **Consenti transito gateway:** Selezionare questa casella per consentire alla rete virtuale con peering di utilizzare il gateway di rete virtuale collegato a questa rete virtuale. *Consenti transito gateway* è abilitato nella rete virtuale con la risorsa gateway di rete virtuale, ma si applica solo se l'opzione *Usa gateway remoti* è abilitata nell'altra rete virtuale.
4. **Usa gateway remoti:** Selezionare questa casella per consentire il flusso del traffico da questa rete virtuale attraverso un gateway di rete virtuale collegato alla rete virtuale con peering. *Usare i gateway remoti* è abilitato nella rete virtuale senza un gateway di rete virtuale e si applica solo se l'opzione *Consenti transito gateway* è abilitata nell'altra rete virtuale.

|Risorsa|Collegamento|
|---|---|
| Concetti: Peering di rete virtuale                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) |
| Creare, modificare o eliminare un peering reti virtuali | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="virtual-network-gateway"></a>Gateway di rete virtuale

I gateway di rete virtuale forniscono un meccanismo per l'integrazione di reti virtuali con reti esterne, ad esempio ambienti locali, ambienti partner e altre distribuzioni cloud. I due tipi di gateway di rete virtuale sono ExpressRoute e VPN.

#### <a name="expressroute-gateway"></a>Gateway ExpressRoute

I gateway ExpressRoute forniscono un punto di uscita dalla rete virtuale a un ambiente locale e devono essere distribuiti per soddisfare i requisiti di sicurezza, disponibilità, finanza e prestazioni. I gateway ExpressRoute forniscono una larghezza di banda di rete definita e comportano costi di utilizzo dopo la distribuzione. Le reti virtuali possono avere un solo gateway ExpressRoute, ma possono essere connesse a più circuiti ExpressRoute e possono essere utilizzate da più reti virtuali tramite il peering VNet, consentendo la condivisione della larghezza di banda e della connettività. È necessario prestare attenzione per configurare il routing tra ambienti locali e reti virtuali usando i gateway ExpressRoute per garantire la connettività end-to-end usando punti di uscita di rete noti e controllati. Le entità Commonwealth che usano il peering di ExpressRoute tramite il peering privato di ExpressRoute devono anche distribuire appliance virtuali di rete per stabilire la connettività VPN all'ambiente locale per la conformità con le linee guida per i consumatori ACSC.

È importante notare che i gateway ExpressRoute presentano restrizioni sugli intervalli di indirizzi, le community e altri elementi di configurazione specifici scambiati tramite BGP.

| Risorsa  | Collegamento  |
|---|---|
| Panoramica del gateway ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) |
| Configurare un gateway di rete virtuale per ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)
|

#### <a name="vpn-gateway"></a>Gateway VPN

Il gateway VPN di Azure fornisce un punto di rete in uscita dalla rete virtuale a una rete esterna per la connettività sicura da sito a sito. I gateway VPN forniscono una larghezza di banda di rete definita e comportano costi di utilizzo dopo la distribuzione. Le entità Commonwealth che utilizzano il gateway VPN devono assicurarsi che siano configurate in conformità con le linee guida per i clienti di ACSC. Le reti virtuali possono avere un solo gateway VPN, ma possono essere configurate con più tunnel e possono essere utilizzate da più reti virtuali tramite il peering VNet, consentendo a più reti virtuali di condividere larghezza di banda e connettività. I gateway VPN possono essere stabiliti tramite Internet o tramite ExpressRoute tramite il peering Microsoft.

| Risorsa  | Collegamento |
| --- | --- |
| Panoramica del gateway VPN| [https://docs.microsoft.com/azure/vpn-gateway](https://docs.microsoft.com/azure/vpn-gateway)|
| Pianificazione e progettazione per il gateway VPN | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
| Gateway VPN di Azure in Azure Australia | [Gateway VPN di Azure in Azure Australia](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>Hop successivo dell'appliance virtuale

L'hop successivo dell'appliance virtuale offre la possibilità di elaborare il traffico di rete all'esterno della topologia di rete e di routing di Azure applicata alle reti virtuali. Le appliance virtuali possono applicare regole di sicurezza, tradurre indirizzi, stabilire VPN, il traffico proxy o una gamma di altre funzionalità. L'hop successivo dell'appliance virtuale viene applicato tramite UdR in una tabella di route e può essere usato per indirizzare il traffico a un firewall di Azure, a un appliance virtuale di dispositivi singolo o a Azure Load Balancer offrire disponibilità tra più appliance virtuali. Per usare un'appliance virtuale per il routing, è necessario abilitare le interfacce di rete associate per l'inoltro IP.

| Risorsa  | Collegamento |
| --- | ---|
| Concetti di routing: Route personalizzate | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) |
| Abilitare o disabilitare l'invio IP | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>Hop successivo di VirtualNetworkServiceEndpoint

Le route con un tipo di hop successivo di VirtualNetworkServiceEndpoint vengono aggiunte solo quando un endpoint di servizio viene configurato in una subnet e non può essere configurato manualmente tramite le tabelle di route.

### <a name="next-hop-of-internet"></a>Hop successivo di Internet

L'hop successivo Internet viene usato per raggiungere tutte le risorse che usano un indirizzo IP pubblico, che include Internet, oltre a PaaS e ai servizi di Azure nelle aree di Azure. L'hop successivo Internet non richiede una route predefinita (0.0.0.0/0) che copre tutte le reti, ma può essere usato per abilitare i percorsi di routing per servizi pubblici specifici. L'hop successivo di Internet deve essere usato per aggiungere route ai servizi e alle funzionalità autorizzate necessarie per le funzionalità di sistema, ad esempio gli indirizzi di gestione Microsoft.

Di seguito sono riportati alcuni esempi di servizi che possono essere aggiunti usando l'hop successivo di Internet:

1. Servizi di gestione delle chiavi per l'attivazione di Windows
2. Gestione ambiente del servizio app

|Risorsa|Collegamento|
|---|---|
| Connessioni in uscita in Azure | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) |
| Usare le route personalizzate di Azure per abilitare l'attivazione del KMS | [https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](https://docs.microsoft.com/bs-latn-ba/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation) |
| Blocco di un ambiente del servizio app  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](https://docs.microsoft.com/azure/app-service/environment/firewall-integration) |
|

### <a name="next-hop-of-none"></a>Hop successivo di None

L'hop successivo di None può essere utilizzato per impedire la comunicazione a una rete specifica. A differenza di un NSG, che controlla se il traffico è consentito o negato all'attraversamento di un percorso di rete disponibile, l'uso di un hop successivo di nessuno rimuove completamente il percorso di rete. È necessario prestare attenzione durante la creazione di route con un hop successivo di nessuno, in particolare quando viene applicato a una route predefinita di 0.0.0.0/0, in quanto ciò può avere conseguenze impreviste e può rendere più complesse le problematiche del sistema e richiedere molto tempo.

## <a name="security"></a>Security

L'implementazione di controlli di segmentazione e separazione delle reti sulle funzionalità IaaS e PaaS viene eseguita grazie alla protezione delle funzionalità stesse e all'implementazione dei percorsi di comunicazione controllati dai sistemi che comunicheranno con il funzionalità.

La progettazione e la creazione di soluzioni in Azure è un processo di creazione di un'architettura logica per comprendere, controllare e monitorare le risorse di rete nell'intera presenza di Azure. Questa architettura logica è il software definito all'interno della piattaforma Azure e si occupa di una topologia di rete fisica implementata negli ambienti di rete tradizionali.

L'architettura logica creata deve fornire le funzionalità necessarie per l'usabilità, ma deve fornire anche la visibilità e il controllo necessari per la sicurezza e l'integrità.

Il raggiungimento di questo risultato si basa sull'implementazione degli strumenti necessari per la segmentazione e la separazione di rete, ma anche per la protezione e l'applicazione della topologia di rete e dell'implementazione di questi strumenti.

### <a name="network-security-groups-nsgs"></a>Gruppi di sicurezza di rete (NGS)

Gruppi vengono usati per specificare il traffico in ingresso e in uscita consentito per una subnet o un'interfaccia di rete specifica. Quando si configura gruppi, le entità Commonwealth devono usare un approccio per l'inserimento nell'elenco elementi consentiti in cui le regole sono configurate per consentire il traffico necessario con una regola predefinita configurata per negare tutto il traffico che non corrisponde a un'istruzione Quando si pianifica e si configura gruppi, è necessario prestare attenzione per assicurarsi che tutto il traffico in ingresso e in uscita necessario venga acquisito in modo appropriato. Ciò include l'identificazione e la comprensione di tutti gli intervalli di indirizzi IP privati utilizzati all'interno delle reti virtuali e dell'ambiente locale e di servizi Microsoft specifici, ad esempio Azure Load Balancer e i requisiti di gestione di PaaS. I singoli utenti interessati alla progettazione e all'implementazione di gruppi devono comprendere anche l'uso di tag di servizio e gruppi di sicurezza delle applicazioni per la creazione di regole di sicurezza specifiche per le applicazioni e per i servizi.

È importante notare che la configurazione predefinita per un NSG consente il traffico in uscita verso tutti gli indirizzi all'interno della rete virtuale e tutti gli indirizzi IP pubblici.

|Risorsa|Collegamento|
|---|---|
|Panoramica della sicurezza di rete | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Creare, modificare o eliminare un gruppo di sicurezza di rete | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

### <a name="azure-firewall"></a>Firewall di Azure

Il firewall di Azure può essere usato per creare una topologia di rete hub-spoke e applicare controlli di sicurezza di rete centralizzati. Il firewall di Azure può essere usato per soddisfare i requisiti necessari di ISM per il traffico in uscita implementando un approccio Consenti elenco in cui sono autorizzati solo gli indirizzi IP, i protocolli, le porte e i nomi di dominio completi necessari per le funzionalità di sistema. Le entità Commonwealth devono adottare un approccio basato sui rischi per determinare se le funzionalità di sicurezza fornite da firewall di Azure sono sufficienti per soddisfare i requisiti. Per gli scenari in cui sono necessarie funzionalità di sicurezza aggiuntive, oltre a quelle fornite dal firewall di Azure, le entità Commonwealth devono considerare l'implementazione di appliance virtuali.

|Risorsa|Collegamento|
|---|---|
|*Documentazione di Azure firewall* | [https://docs.microsoft.com/azure/firewall](https://docs.microsoft.com/azure/firewall)|
|*Esercitazione: Distribuire e configurare il firewall di Azure in una rete ibrida usando Azure PowerShell* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps)|
|

### <a name="network-virtual-appliances-nvas"></a>Appliance virtuali di rete (appliance virtuali)

Appliance virtuali può essere usato per creare una topologia di rete hub-spoke, fornire funzionalità di rete avanzate o complementari oppure può essere usata come alternativa ai meccanismi di rete di Azure per la conoscenza e il supporto e la gestione coerenti con i servizi di rete locali. È possibile distribuire appliance virtuali per soddisfare requisiti di sicurezza specifici, ad esempio. scenari in cui è necessario conoscere l'identità associata al traffico di rete, alla decrittografia HTTPS, all'ispezione dei contenuti, al filtraggio o ad altre funzionalità di sicurezza. Appliance virtuali deve essere distribuito in una configurazione a disponibilità elevata e i singoli utenti interessati alla progettazione o all'implementazione di appliance virtuali devono consultare la documentazione del fornitore appropriata per le linee guida sulla distribuzione in Azure.

|Risorsa|Collegamento|
|---|---|
|*Distribuire appliance virtuali di rete a disponibilità elevata* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>Criteri endpoint servizio (anteprima)

Configurare i criteri dell'endpoint di servizio in base alla disponibilità del servizio e una valutazione dei rischi di sicurezza della probabilità e dell'impatto dei dati exfiltration. I criteri dell'endpoint di servizio devono essere presi in considerazione per l'archiviazione di Azure e gestiti in base al caso per altri servizi basati sul profilo di rischio associato.

| Risorsa | Collegamento  |
| --- | --- |
| *Panoramica sui criteri degli endpoint di servizio* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview) |
| *Creare, modificare o eliminare i criteri dell'endpoint di servizio usando il portale di Azure* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal)
|

### <a name="azure-policy"></a>Criteri di Azure

Criteri di Azure è un componente chiave per l'applicazione e la gestione dell'integrità dell'architettura logica dell'ambiente Azure. Sono disponibili un'ampia gamma di servizi e percorsi di traffico di rete in uscita tramite i servizi di Azure. È fondamentale che le entità del Commonwealth siano consapevoli delle risorse presenti all'interno dell'ambiente e dei punti di uscita di rete disponibili. Per assicurarsi che i punti di uscita di rete non autorizzati non vengano creati nell'ambiente Azure, le entità Commonwealth devono usare criteri di Azure per controllare i tipi di risorse che è possibile distribuire e la configurazione di tali risorse. Esempi pratici includono la limitazione delle risorse solo a quelle autorizzate e approvate per l'uso e la necessità di aggiungere gruppi alle subnet.

|Risorsa | Collegamento|
|---|---|
|*Panoramica di criteri di Azure* | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|*Criteri di esempio dei tipi di risorse consentiti* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)|
|*Forzare NSG in un criterio di esempio della subnet*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="paas-egress-capabilities"></a>Funzionalità in uscita PaaS

Le funzionalità di PaaS offrono opportunità per una maggiore funzionalità e una gestione semplificata, ma introducono complessità per soddisfare i requisiti per la segmentazione e la separazione di rete. Le funzionalità di PaaS sono in genere configurate con indirizzi IP pubblici e sono accessibili da Internet.  Se si usano le funzionalità di PaaS all'interno dei sistemi e delle soluzioni, è necessario prestare attenzione per identificare i flussi di comunicazione tra i componenti e creare regole di sicurezza di rete per consentire solo la comunicazione. Come parte di un approccio di difesa in profondità alla sicurezza, le funzionalità di PaaS devono essere configurate con la crittografia, l'autenticazione e i controlli di accesso e le autorizzazioni appropriati.  

### <a name="public-ip-for-paas"></a>IP pubblico per PaaS

Gli indirizzi IP pubblici per le funzionalità PaaS vengono allocati in base all'area in cui il servizio è ospitato o distribuito. Per creare le regole di sicurezza di rete e la topologia di routing appropriate per la segmentazione e la separazione della rete per le reti virtuali di Azure, PaaS e ExpressRoute, è necessario comprendere l'allocazione e le aree di indirizzi IP pubblici. Connettività Internet. Azure alloca gli indirizzi IP da un pool allocato a ogni area di Azure. Microsoft rende disponibili gli indirizzi usati in ogni area per il download, che vengono aggiornati in modo regolare e controllato. Anche i servizi disponibili in ogni area cambiano spesso quando vengono rilasciati nuovi servizi o se i servizi vengono distribuiti in modo più ampio. Le entità del Commonwealth dovrebbero rivedere questi materiali regolarmente e possono usare l'automazione per gestire i sistemi in base alle esigenze. È possibile ottenere indirizzi IP specifici per alcuni servizi ospitati in ogni area contattando il supporto tecnico Microsoft.

| Risorsa | Collegamento |
| --- | --- |
| *Intervalli IP del data center di Microsoft Azure*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *Servizi di Azure per area*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Australia-Central, Australia-Central-2, Australia-Est, Australia-sud-orientale & Products = all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Indirizzi IP in ingresso e in uscita nel servizio app Azure* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips)
|

## <a name="next-steps"></a>Passaggi successivi

Confrontare l'architettura e la progettazione complessiva con i [progetti protetti pubblicati per le applicazioni Web IaaS e PaaS](https://aka.ms/au-protected).
