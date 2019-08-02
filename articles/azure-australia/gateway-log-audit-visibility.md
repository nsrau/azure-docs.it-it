---
title: Registrazione, controllo e visibilità del gateway in Azure Australia
description: Come configurare la registrazione, il controllo e la visibilità nelle aree australiane per soddisfare i requisiti specifici dei criteri, delle normative e delle normative del governo australiano.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 5971fe53f52725a88c484edcc9a5a672fceceb07
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571367"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Registrazione, controllo e visibilità del gateway in Azure Australia

Il rilevamento e la risposta alle minacce per la sicurezza informatica si basa sulla generazione, la raccolta e l'analisi dei dati relativi al funzionamento di un sistema.

Microsoft offre strumenti integrati in Azure che consentono di implementare la registrazione, il controllo e la visibilità per gestire la sicurezza dei sistemi distribuiti in Azure. È inoltre disponibile un'architettura di riferimento che si allinea al materiale sussidiario ACSC (Australian Cyber Security Centre) e allo scopo del manuale di Information Security (ISM).

I gateway fungono da meccanismi di controllo del flusso di informazioni a livello di rete e possono inoltre controllare le informazioni a livelli più elevati del modello di interconnessione del sistema aperto (OSI). I gateway sono necessari per controllare il flusso di dati tra domini di sicurezza e impedire accessi non autorizzati da reti esterne. Data la criticità dei gateway per il controllo del flusso di informazioni tra domini di sicurezza, qualsiasi errore, in particolare per le classificazioni più elevate, potrebbe avere conseguenze gravi. Di conseguenza, i meccanismi affidabili per gli avvisi del personale in situazioni che possono causare incidenti di sicurezza informatici sono particolarmente importanti per i gateway.

L'implementazione di funzionalità di registrazione e avviso per i gateway può essere utile per rilevare gli eventi imprevisti della sicurezza informatica, le intrusioni tentate e modelli di utilizzo insoliti. Inoltre, l'archiviazione dei registri eventi in un server di log sicuro separato aumenta le difficoltà per l'eliminazione di informazioni di registrazione da parte di un antagonista, in modo da eliminare le evidenze di un'intrusione informatica mirata.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Requisiti di ACSC (Australian Cyber Security Centre)

I requisiti di sicurezza generali per i sistemi Commonwealth sono definiti in ACSC Information Security Manual (ISM). Per aiutare le entità del Commonwealth a soddisfare questi requisiti in Azure, la guida per gli *utenti di ACSC, Microsoft Azure al report sulla* certificazione protected and *ACSC, Microsoft Azure* le pubblicazioni illustrano in dettaglio i seguenti requisiti specifici correlati per la registrazione, il controllo e la visibilità:

1. Per attenuare i rischi derivanti dall'uso di risorse cloud sottostanti condivise, le entità Commonwealth devono acconsentire esplicitamente a Microsoft Azure funzionalità fornite, tra cui il Centro sicurezza di Azure, monitoraggio di Azure, criteri di Azure e Azure Advisor per facilitare l'esecuzione delle entità. monitoraggio in tempo reale dei carichi di lavoro di Azure

2. Il ACSC suggerisce anche che le entità Commonwealth inoltrino tutti i registri di sicurezza imposti al ACSC per l'intero monitoraggio del governo australiano

3. Per facilitare la mitigazione dei rischi, le entità Commonwealth devono configurare nelle sottoscrizioni di Azure:

    * Abilitare il Centro sicurezza di Azure
    * Eseguire l'aggiornamento al livello standard
    * Abilitare il provisioning automatico del Microsoft Monitoring Agent alle VM di Azure supportate
    * Esaminare regolarmente, assegnare priorità e attenuare le raccomandazioni e gli avvisi di sicurezza nel dashboard del Centro sicurezza

4. Le entità governative devono consentire l'invio di log ed eventi dalla sottoscrizione di Azure al ACSC per fornire al ACSC la visibilità della mancata conformità con queste linee guida. Hub eventi di Azure offre la possibilità di eseguire lo streaming di log esterno per i sistemi ACSC o locali di proprietà dell'entità Commonwealth

5. Le entità Commonwealth devono allineare la registrazione abilitata in Azure ai requisiti specificati in ISM

6. Microsoft conserva i log in Azure per 90 giorni. Le entità Customer devono implementare un regime di archiviazione dei log per assicurarsi che i log possano essere conservati per i sette anni necessari con la AFDA di NAA

7. Anche le entità Commonwealth con funzionalità di gestione degli eventi e delle informazioni di sicurezza basate su Azure o locali possono anche inviare i log a tali sistemi

8. Le entità Commonwealth devono implementare i log di flusso Network Watcher per i gruppi di sicurezza di rete (gruppi) e le macchine virtuali. Questi log devono essere archiviati in un account di archiviazione dedicato contenente solo i registri di sicurezza e l'accesso all'account di archiviazione deve essere protetto con i controlli degli accessi in base al ruolo

9. Le entità del Commonwealth devono implementare le linee guida per i clienti di ACSC per garantire che i carichi di lavoro di Azure soddisfino lo scopo di ISM per la registrazione e Le entità del Commonwealth devono anche acconsentire esplicitamente alle funzionalità di Azure che assistono la ACSC a ricevere monitoraggio, avvisi e log in tempo reale associati all'uso di Azure per enti pubblici australiani

## <a name="architecture"></a>Architettura

Per comprendere in modo sicuro il traffico di rete in ingresso e in uscita dall'ambiente Azure, è necessario abilitare la registrazione necessaria sul set di componenti corretto. Ciò garantisce la visibilità completa dell'ambiente e fornisce i dati necessari per l'esecuzione dell'analisi.

![Architettura di monitoraggio di Azure](media/visibility.png)

## <a name="components"></a>Componenti

L'architettura illustrata in precedenza è costituita da componenti discreti che forniscono la funzione di origini log, raccolta di log, conservazione dei log, analisi dei log o risposta agli eventi imprevisti. Questa architettura include singoli componenti che in genere sono interessati alle distribuzioni di Azure accessibili da Internet.

|Funzioni|Componenti|
|---|---|
|Origini log|<ul><li>Gateway applicazione</li><li>Gateway VPN</li><li>Firewall di Azure</li><li>Appliance virtuali di rete</li><li>Azure Load Balancer</li><li>Macchine virtuali</li><li>Server DNS (Domain Naming System)</li><li>Server di raccolta syslog e/o log</li><li>Gruppi di sicurezza di rete</li><li>Azure Activity Log</li><li>Log di diagnostica di Azure</li><li>Criteri di Azure</li></ul>|
|Raccolta log|<ul><li>Hub eventi</li><li>Network Watcher</li><li>Log Analytics</li></ul>|
|Conservazione dei log|<ul><li>Archiviazione di Azure</li></ul>|
|Analisi dei log|<ul><li>Centro sicurezza di Azure (ASC)</li><li>Azure Advisor</li><li>Soluzioni di Log Analytics<ul><li>Analisi del traffico</li><li>DNS Analytics (anteprima)</li><li>Activity Log Analytics</li></ul></li><li>Informazioni di sicurezza e gestione degli eventi</li><li>ACSC</li></ul>|
|Risposta agli eventi imprevisti|<ul><li>Avvisi di Azure</li><li>Automazione di Azure</li></ul>|
|

L'architettura funziona generando prima di tutto i log dalle origini necessarie e quindi li raccoglie in repository centralizzati. Una volta raccolti i log, è possibile:

* usato da Azure Analysis Services per ottenere informazioni dettagliate,
* viene inviato a sistemi esterni o
* viene archiviato in archiviazione per la conservazione a lungo termine.

Per rispondere a eventi chiave o eventi imprevisti identificati da strumenti di analisi, è possibile configurare gli avvisi e l'automazione è stata sviluppata per eseguire le azioni necessarie per la gestione e la risposta proattive.

## <a name="general-guidance"></a>Indicazioni generali

Quando si implementano i componenti elencati in questo articolo, si applicano le linee guida generali seguenti:

* Convalidare la disponibilità dell'area dei servizi, assicurandosi che tutti i dati rimangano entro i percorsi autorizzati e distribuiti in AU Central o AU Central 2 come prima preferenza per i carichi di lavoro protetti

* Per informazioni sullo stato della certificazione dei singoli servizi, vedere la pubblicazione *2018 protetta del report sulla certificazione Azure-ACSC* ed eseguire la valutazione automatica di tutti i componenti rilevanti non inclusi nel report in base alla guida per i *consumatori di ACSC: Microsoft Azure in protetto*

* Per i componenti a cui non viene fatto riferimento in questo articolo, le entità del Commonwealth devono attenersi ai principi inclusi relativi alla generazione, acquisizione, analisi e conservazione dei log.

* Identificare e assegnare priorità alla registrazione, al controllo e alla visibilità su sistemi di valore elevato, oltre a tutti i punti di ingresso e uscita di rete per i sistemi ospitati in Azure

* Consolidare i log e ridurre al minimo il numero di istanze di strumenti di registrazione, ad esempio account di archiviazione, aree di lavoro Log Analytics e hub eventi

* Limitare i privilegi amministrativi tramite i controlli degli accessi in base al ruolo

* Usare multi-factor authentication per gli account che gestiscono o configurano le risorse in Azure

* Quando si centralizza la raccolta di log tra più sottoscrizioni, assicurarsi che gli amministratori dispongano dei privilegi necessari in ogni sottoscrizione

* Assicurarsi che la connettività di rete e qualsiasi configurazione proxy necessaria per le macchine virtuali, incluse le appliance virtuali di rete (appliance virtuali), i server di raccolta dei log e i server DNS, per connettersi ai servizi di Azure necessari, ad esempio le aree di lavoro Log Analytics, Hub eventi , e archiviazione

* Configurare il Microsoft Monitoring Agent (MMA) per l'uso di TLS versione 1,2

* Usare i criteri di Azure per monitorare e applicare la conformità ai requisiti

* Applicare la crittografia su tutti i repository di dati, ad esempio archiviazione e database

* Usare l'archiviazione con ridondanza locale (con ridondanza locale) e gli snapshot per la disponibilità di account di archiviazione e dati associati

* Considerare l'archiviazione con ridondanza geografica o l'archiviazione fuori sede per l'allineamento con le strategie di ripristino di emergenza

|Risorsa|URL|
|---|---|
|Documenti australiani sulla conformità alle normative e ai criteri|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Prodotti Azure-aree australiane e non internazionali|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional, Australia centrale, Australia centrale-2, Australia orientale, Australia sudorientale](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|White paper sulla gestione di Microsoft Azure Sicurezza e controllo log|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](http://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Configurazione di Microsoft Monitoring Agent|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)|
|

## <a name="component-guidance"></a>Linee guida sui componenti

In questa sezione vengono fornite informazioni sullo scopo di ogni componente e sul relativo ruolo nell'architettura complessiva di registrazione, controllo e visibilità. Sono disponibili collegamenti aggiuntivi per accedere a risorse utili, ad esempio documentazione di riferimento, guide ed esercitazioni.

## <a name="log-sources"></a>Origini log

Prima di eseguire analisi, avvisi o report, è necessario generare i log necessari. I log di Azure vengono categorizzati in log di controllo/gestione, log del piano dati ed eventi elaborati.

|Type|Descrizione|
|---|---|
|Log di controllo/gestione|Fornire informazioni sulle operazioni di Azure Resource Manager|
|Log del piano dati|Fornire informazioni sugli eventi generati come parte dell'uso delle risorse di Azure, ad esempio i log in una macchina virtuale e i log di diagnostica disponibili tramite monitoraggio di Azure|
|Eventi elaborati|Fornire informazioni sugli eventi/avvisi analizzati che sono stati elaborati da Azure, ad esempio il Centro sicurezza di Azure ha elaborato e analizzato le sottoscrizioni per fornire avvisi di sicurezza|
|

### <a name="application-gateway"></a>Gateway applicazione

Applicazione Azure gateway è uno dei possibili punti di ingresso in un ambiente Azure, pertanto è necessario acquisire informazioni correlate alle connessioni in ingresso che comunicano con le applicazioni Web. Il gateway applicazione può fornire informazioni cruciali sull'utilizzo delle applicazioni Web, oltre ad assistere al rilevamento di problemi di sicurezza informatici. Il gateway applicazione invia i metadati al log attività e ai log di diagnostica in monitoraggio di Azure, dove può essere usato in Log Analytics o distribuito in un hub eventi o in un account di archiviazione.

|Risorse|Collegamento|
|---|---|
|Documentazione sul gateway applicazione|[https://docs.microsoft.com/azure/application-gateway/](https://docs.microsoft.com/azure/application-gateway/)|
|Guida introduttiva al gateway applicazione|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)|
|

### <a name="vpn-gateway"></a>Gateway VPN

Il gateway VPN è un potenziale punto di ingresso per un'ampia gamma di comunicazioni nell'ambiente Azure, ad esempio la connessione a un ambiente locale e il traffico amministrativo. La registrazione nei gateway VPN fornisce informazioni dettagliate e tracciabilità per le connessioni effettuate all'ambiente Azure. La registrazione può fornire funzionalità di controllo e analisi, nonché supportare il rilevamento o l'analisi di connessioni dannose o anomale. I log del gateway VPN vengono inviati al log attività di monitoraggio di Azure, dove possono essere usati in Log Analytics o distribuiti in un hub eventi o in un account di archiviazione.

|Risorse|Collegamento|
|---|---|
|Documentazione del gateway VPN|[https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway)|
|Linee guida specifiche del gateway VPN per il governo australiano|[https://aka.ms/AzGovAUSecurity](https://aka.ms/AzGovAUSecurity)|
|

### <a name="azure-firewall"></a>Firewall di Azure

Il firewall di Azure fornisce un punto di uscita controllato da un ambiente Azure e i log generati, che includono informazioni sulle connessioni in uscita tentate e con esito positivo, rappresentano un elemento importante della strategia di registrazione. Questi log possono verificare che i sistemi funzionino come previsto, oltre a consentire il rilevamento di codice dannoso o di attori che tentano di connettersi a sistemi esterni non autorizzati. Il firewall di Azure scrive i log nel log attività e nei log di diagnostica in monitoraggio di Azure, dove possono essere usati in Log Analytics o distribuiti in un hub eventi o in un account di archiviazione.

|Risorse|Collegamento|
|---|---|
|Documentazione del firewall di Azure|[https://docs.microsoft.com/azure/firewall/](https://docs.microsoft.com/azure/firewall)|
|Esercitazione: Monitorare i log e le metriche di Firewall di Azure|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics)|
|

### <a name="network-virtual-appliances-nva"></a>Appliance virtuali di rete

Appliance virtuali può essere usato per integrare le funzionalità di sicurezza disponibili in modo nativo in Azure. I log generati in appliance virtuali possono essere risorse preziose per rilevare gli eventi imprevisti della sicurezza informatica e costituiscono una parte essenziale di una strategia complessiva di registrazione, controllo e visibilità. Per acquisire i log da appliance virtuali, utilizzare il Microsoft Monitoring Agent (MMA). Per appliance virtuali che non supportano l'installazione di MMA, provare a usare un syslog o un altro server di raccolta di log per inoltrare i log.

|Risorse|Collegamento|
|---|---|
|Panoramica delle appliance virtuali di rete|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|Documentazione di NVA|Vedere la documentazione del fornitore sull'implementazione dell'appliance virtuale di sistema pertinente in Azure|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

I log Azure Load Balancer vengono usati per ottenere informazioni utili sulle connessioni e sull'utilizzo correlati ai sistemi distribuiti in Azure. Questo può essere usato per il monitoraggio dello stato e della disponibilità, ma costituisce anche un altro componente chiave per ottenere le informazioni necessarie sul traffico delle comunicazioni e rilevare modelli di traffico dannosi o anomali. Azure Load Balancer registri ai log attività e ai log di diagnostica in monitoraggio di Azure, dove possono essere usati in Log Analytics o distribuiti in un hub eventi o in un account di archiviazione.

|Risorse|Collegamento|
|---|---|
|Documentazione di Azure Load Balancer|[https://docs.microsoft.com/azure/load-balancer](https://docs.microsoft.com/azure/load-balancer)|
|Metriche e diagnostica dell'integrità per Load Balancer Standard|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)|
|

### <a name="virtual-machines"></a>Macchine virtuali

Le macchine virtuali sono endpoint che inviano e ricevono comunicazioni di rete, elaborano i dati e forniscono servizi. Poiché le macchine virtuali possono ospitare i dati o i servizi di sistema cruciali, verificare che funzionino correttamente e rilevare eventuali eventi imprevisti della sicurezza informatica. Le macchine virtuali raccolgono diversi log eventi e di controllo che consentono di tenere traccia del funzionamento del sistema e delle azioni eseguite su tale sistema. I log raccolti nelle macchine virtuali possono essere trasmessi a un'area di lavoro Log Analytics usando i Microsoft Monitoring Agent in cui possono essere analizzati dal centro sicurezza di Azure e dalle soluzioni Log Analytics applicabili. Le macchine virtuali possono anche essere integrate direttamente con hub eventi di Azure o a SIEM, direttamente o tramite un server di raccolta di log.

|Risorse|Collegamento|
|---|---|
|Macchine virtuali|[https://docs.microsoft.com/azure/virtual-machines](https://docs.microsoft.com/azure/virtual-machines)|
|Raccogliere dati da macchine virtuali|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|
|Trasmettere i log della macchina virtuale a hub eventi|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs)|
|

### <a name="domain-name-services-dns-servers"></a>Server DNS (Domain Name Services)

I log del server DNS forniscono informazioni chiave relative ai servizi a cui i sistemi stanno tentando di accedere, internamente o esternamente. L'acquisizione dei log DNS consente di identificare un evento imprevisto di sicurezza informatico e di fornire informazioni sul tipo di evento imprevisto e sui sistemi che potrebbero essere interessati. Microsoft Management Agent (MMA) può essere usato nei server DNS per l'invio dei log tramite a Log Analytics per l'uso in Analisi DNS (anteprima).

|Risorse|Collegamento|
|---|---|
|Risoluzione dei nomi di Azure per le reti virtuali|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances)|
|

### <a name="syslog-and-log-collection-servers"></a>Server di raccolta syslog e log

Per ricevere log da appliance virtuali di rete o log di sicurezza personalizzati da altri sistemi da usare all'interno di un SIEM, i server dedicati possono essere distribuiti all'interno di Azure reti virtuali. I log syslog possono essere raccolti in un server syslog e inoltrati a Log Analytics per l'analisi. Un server di raccolta di log è un termine generico per qualsiasi funzionalità di aggregazione e distribuzione dei log utilizzata dai sistemi di monitoraggio centralizzati o da SIEM. Questi possono essere usati per semplificare l'architettura di rete e la sicurezza e per filtrare e aggregare i log prima di essere distribuiti alla funzionalità centralizzata.

|Risorse|Collegamento|
|---|---|
|Origini dati Syslog in Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)|
|Server di raccolta log|Per informazioni dettagliate sul monitoraggio e sull'architettura SIEM, vedere la documentazione del fornitore.|
|

### <a name="network-security-groups-nsgs"></a>Gruppi di sicurezza di rete (NGS)

Gruppi controlla il traffico all'interno e all'esterno delle reti virtuali in Azure. Gruppi applicano le regole per i flussi di traffico consentiti o negati, che includono il traffico in Azure e tra Azure e reti esterne, ad esempio in locale o in Internet. Gruppi vengono applicate alle subnet all'interno di una rete virtuale o a singole interfacce di rete. Per acquisire informazioni sul traffico che entra e lascia i sistemi in Azure, i log di NSG possono essere abilitati tramite la funzionalità log dei flussi di NSG Network Watcher. Questi log vengono usati per creare una linea di base per il funzionamento standard di un sistema e sono l'origine dati per Analisi del traffico, che offre informazioni dettagliate sui modelli di traffico dei sistemi ospitati in Azure.

|Risorse|Collegamento|
|---|---|
|Documentazione del gruppo di sicurezza di rete|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Introduzione alla registrazione dei flussi per i gruppi di sicurezza di rete|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|
|Esercitazione: Registrare il traffico di rete da e verso una macchina virtuale usando il portale di Azure|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)|
|

### <a name="azure-activity-log"></a>Azure Activity Log

Il log attività di Azure, che fa parte di monitoraggio di Azure, è un log delle sottoscrizioni che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Il log attività consente di determinare il "cosa, chi e quando" per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita ***sulle*** risorse in una sottoscrizione. Il log attività è essenziale per tenere traccia delle modifiche di configurazione apportate all'interno dell'ambiente Azure. I log attività di Azure sono automaticamente disponibili per l'uso in soluzioni Log Analytics e possono essere inviati a hub eventi o archiviazione di Azure per l'elaborazione o la conservazione.

|Risorse|Collegamento|
|---|---|
|Documentazione del log attività di Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|Trasmettere il log attività di Azure a Hub eventi|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs)|
|

### <a name="azure-diagnostic-log"></a>Log di diagnostica di Azure

I log di diagnostica di monitoraggio di Azure sono log emessi da un servizio di Azure che forniscono dati avanzati e frequenti sul funzionamento di tale servizio. I log di diagnostica forniscono informazioni approfondite sul funzionamento di una risorsa a un livello dettagliato e possono essere usati per una vasta gamma di requisiti, ad esempio il controllo o la risoluzione dei problemi. I log di diagnostica di Azure sono automaticamente disponibili per l'uso in soluzioni Log Analytics e possono essere inviati a hub eventi o archiviazione di Azure per l'elaborazione o la conservazione.

|Risorse|Collegamento|
|---|---|
|Documentazione del log di diagnostica di Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|Servizi di supporto per i log di diagnostica|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema)|
|

### <a name="azure-policy"></a>Criteri di Azure

Criteri di Azure impone le regole per la distribuzione delle risorse, ad esempio il tipo, la posizione e la configurazione. I criteri di Azure possono essere configurati per garantire che le risorse possano essere distribuite solo se sono conformi ai requisiti. Criteri di Azure è un componente fondamentale per mantenere l'integrità di un ambiente Azure. Gli eventi correlati ai criteri di Azure vengono registrati nel log attività di Azure e sono automaticamente disponibili per l'uso nelle soluzioni Log Analytics o possono essere inviati a hub eventi o archiviazione di Azure per l'elaborazione o la conservazione.

|Risorse|Collegamento|
|---|---|
|Documentazione per Criteri di Azure|[https://docs.microsoft.com/azure/governance/policy](https://docs.microsoft.com/azure/governance/policy)|
|Uso dei modelli di criteri e Gestione risorse di Azure con i progetti di Azure|[https://docs.microsoft.com/azure/governance/blueprints/overview](https://docs.microsoft.com/azure/governance/blueprints/overview)|
|

## <a name="log-collection"></a>Raccolta registri

Una volta generati da più origini log, i log devono essere archiviati in una posizione centralizzata per l'accesso e l'analisi in corso. In Azure sono disponibili più metodi e opzioni per la raccolta di log che possono essere utilizzati a seconda del tipo di log e dei requisiti.

### <a name="event-hubs"></a>Hub eventi

Lo scopo di un hub eventi consiste nell'aggregare i dati di log per le varie origini per la distribuzione. Dall'hub eventi, i dati di log possono essere inviati a un SIEM, alla ACSC per la conformità e all'archiviazione per la conservazione a lungo termine.

|Risorse|Collegamento|
|---|---|
|Documentazione di Hub eventi|[https://docs.microsoft.com/azure/event-hubs](https://docs.microsoft.com/azure/event-hubs)|
|Linee guida su Hub eventi e strumenti esterni|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics fa parte di monitoraggio di Azure e viene usato per l'analisi dei log. Log Analytics usa un'area di lavoro come meccanismo di archiviazione in cui è possibile rendere disponibili i dati di log per un'ampia gamma di strumenti di analisi e soluzioni disponibili in Azure. Log Analytics si integra con un'ampia gamma di componenti di Azure direttamente e con le macchine virtuali tramite l'Microsoft Monitoring Agent.

|Risorse|Collegamento|
|---|---|
|Documentazione su Log Analytics|[https://docs.microsoft.com/azure/azure-monitor](https://docs.microsoft.com/azure/azure-monitor)|
|Esercitazione: Analizzare i dati in Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)|
|

### <a name="network-watcher"></a>Network Watcher

L'uso di Network Watcher è consigliato dal ACSC per semplificare la comprensione e l'acquisizione del traffico di rete in una sottoscrizione di Azure. I log di flusso NSG forniscono l'input alla soluzione Analisi del traffico in Log Analytics, che offre maggiore visibilità, analisi e report in modo nativo tramite Azure. Network Watcher fornisce anche una funzionalità di acquisizione di pacchetti direttamente dall'portale di Azure senza dover accedere alla macchina virtuale. L'acquisizione di pacchetti consente di creare sessioni di acquisizione di pacchetti per tenere traccia del traffico da e verso una macchina virtuale.

|Risorse|Collegamento|
|---|---|
|Network Watcher|[https://docs.microsoft.com/azure/network-watcher](https://docs.microsoft.com/azure/network-watcher)|
|Panoramica dell'acquisizione di pacchetti|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)|
|

## <a name="log-retention"></a>Conservazione dei log

Per le organizzazioni governative australiane, i log acquisiti all'interno di Azure devono essere conservati in conformità all'archivio nazionale dell' [autorità di eliminazione delle funzioni amministrative dell'Australia (afda)](http://www.naa.gov.au/information-management/records-authorities/types-of-records-authorities/AFDA/index.aspx), che specifica la conservazione dei log fino a sette anni.

|Percorso log|Periodo di conservazione|
|---|---|
|Azure Activity Log|Fino a 90 giorni|
|Area di lavoro Log Analytics|Fino a due anni|
|Hub eventi|Fino a sette giorni|
|

È responsabilità dell'utente assicurarsi che i log vengano archiviati in modo appropriato per conformarsi a AFDA e altri requisiti legislativi.

### <a name="azure-storage"></a>Archiviazione di Azure

Archiviazione di Azure è il repository per i log per la conservazione a lungo termine in Azure. Archiviazione di Azure può essere usato per archiviare i log da Azure, inclusi Hub eventi, log attività di Azure e log di diagnostica di Azure. Il periodo di conservazione dei dati nell'archiviazione può essere impostato su zero oppure può essere specificato come numero di giorni. Se il periodo di conservazione è pari a zero giorni, i log vengono conservati per sempre. in caso contrario, il valore può essere un numero qualsiasi di giorni compreso tra 1 e 2147483647.

|Risorse|Collegamento|
|---|---|
|Documentazione di Archiviazione di Azure|[https://docs.microsoft.com/azure/storage](https://docs.microsoft.com/azure/storage)|
|Acquisire eventi tramite Hub eventi di Azure in Archiviazione BLOB di Azure o Azure Data Lake Storage|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Esercitazione: Archiviare dati di metrica e log di Azure con Archiviazione di Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data)|
|Replica di archiviazione di Azure|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](https://docs.microsoft.com/azure/storage/common/storage-redundancy)|
|Creazione di uno snapshot di un BLOB|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>Log Analytics

Una volta generati e archiviati in una posizione centralizzata, i log devono essere analizzati per facilitare il rilevamento di tentativi o eventi imprevisti della sicurezza. Quando vengono rilevati eventi di sicurezza, un'agenzia deve poter rispondere a questi eventi imprevisti e per tenere traccia, contenere e correggere le minacce.

### <a name="azure-security-center-asc"></a>Centro sicurezza di Azure (ASC)

Il Centro sicurezza di Azure offre la gestione unificata della sicurezza e la protezione avanzata dalle minacce. Il Centro sicurezza di Azure può applicare criteri di sicurezza tra i carichi di lavoro, limitare l'esposizione alle minacce e rilevare e rispondere agli attacchi. Il Centro sicurezza di Azure fornisce dashboard e analisi in un'ampia gamma di componenti di Azure. L'uso del Centro sicurezza di Azure viene specificato come requisito nelle linee guida per i clienti di ACSC.

|Risorse|Collegamento|
|---|---|
|Documentazione del Centro sicurezza di Azure|[https://docs.microsoft.com/azure/security-center](https://docs.microsoft.com/azure/security-center)|
|Avvio rapido: Caricamento della sottoscrizione di Azure al livello Standard del Centro di sicurezza|[https://docs.microsoft.com/azure/security-center/security-center-get-started](https://docs.microsoft.com/azure/security-center/security-center-get-started)|
|

### <a name="traffic-analytics"></a>Analisi del traffico

Analisi del traffico è una soluzione basata sul cloud che fornisce visibilità sulle attività dell'utente e dell'applicazione in Azure. Analisi del traffico analizza Network Watcher log dei flussi NSG per fornire informazioni dettagliate sul flusso del traffico in Azure. Analisi del traffico viene usato per fornire dashboard, report, analisi e funzionalità di risposta agli eventi correlati al traffico di rete visualizzato tra le reti virtuali. Analisi del traffico offre informazioni significative e consente di identificare e risolvere gli eventi imprevisti relativi alla sicurezza informatica.

|Risorse|Collegamento|
|---|---|
|Documentazione di Analisi del traffico|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)|
|

### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor analizza la configurazione delle risorse e altri dati per consigliare le soluzioni che consentono di migliorare le prestazioni, la sicurezza e la disponibilità elevata delle risorse, cercando al tempo stesso la possibilità di ridurre la spesa complessiva di Azure. Azure Advisor è consigliato dal ACSC e fornisce consigli facilmente accessibili e dettagliati sulla configurazione dell'ambiente Azure.

|Risorse|Collegamento|
|---|---|
|Documentazione su Azure Advisor|[https://docs.microsoft.com/azure/advisor](https://docs.microsoft.com/azure/advisor)|
|Introduzione ad Azure Advisor|[https://docs.microsoft.com/azure/advisor/advisor-get-started](https://docs.microsoft.com/azure/advisor/advisor-get-started)|
|

### <a name="dns-analytics-preview"></a>DNS Analytics (anteprima)

Analisi DNS è una soluzione Log Analytics che raccoglie, analizza e mette in correlazione i registri di analisi e di controllo DNS di Windows e altri dati correlati. Analisi DNS identifica i client che tentano di risolvere i nomi di dominio dannosi, i record di risorse non aggiornati, i nomi di dominio di frequente query e i client DNS loquaci. Analisi DNS fornisce inoltre informazioni sul carico delle richieste sui server DNS e sugli errori di registrazione DNS dinamici. Analisi DNS viene usato per fornire dashboard, report, analisi e funzionalità di risposta agli eventi correlati alle query DNS effettuate in un ambiente Azure. Analisi DNS offre informazioni significative e consente di identificare e risolvere gli eventi imprevisti relativi alla sicurezza informatica.

|Risorse|Collegamento|
|---|---|
|Documentazione di DNS Analytics|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)|
|

### <a name="activity-log-analytics"></a>Activity Log Analytics

Analisi log attività è una soluzione Log Analytics che consente di analizzare e cercare il log attività di Azure tra più sottoscrizioni di Azure. Analisi log attività viene usato per fornire dashboard, report, analisi e funzionalità di risposta agli eventi centralizzati correlati alle azioni eseguite sulle risorse nell'intero ambiente Azure. Analisi log attività possono essere utili per il controllo e l'analisi.

|Risorse|Collegamento|
|---|---|
|Raccogliere e analizzare i log attività di Azure in Log Analytics|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)|
|

### <a name="security-information-and-event-management-siem"></a>Gestione delle informazioni e degli eventi di sicurezza (SIEM)

SIEM è un sistema che fornisce archiviazione centralizzata, controllo e analisi dei log di sicurezza, con meccanismi definiti per l'inserimento di un'ampia gamma di dati di log e strumenti intelligenti per l'analisi, la creazione di report e il rilevamento e la risposta agli eventi imprevisti. È possibile usare le funzionalità SIEM che includono le informazioni di registrazione di Azure per integrare le funzionalità di sicurezza fornite in modo nativo in Azure. Le entità Commonwealth possono usare un SIEM ospitato su macchine virtuali in Azure, in locale o come funzionalità SaaS (software as a Service) in base a requisiti specifici.

|Risorse|Collegamento|
|---|---|
|Sentinel di Azure (anteprima)|[https://azure.microsoft.com/services/azure-sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|Documentazione di SIEM|Vedere la documentazione del fornitore per l'architettura e le linee guida di SIEM|
|Usare monitoraggio di Azure per l'integrazione con gli strumenti SIEM|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>Centro sicurezza Cyber australiano

L'australiano Cyber Security Center (ACSC) è il responsabile australiano del governo della protezione informatica nazionale. Riunisce le funzionalità di sicurezza informatica di tutti i governi australiani per migliorare la resilienza del cyber della community australiana e supportare la prosperità economica e sociale dell'Australia in età digitale. Il ACSC suggerisce che le entità Commonwealth inoltrino tutti i file di log, gli eventi e i log generati dal sistema richiesti al ACSC per l'intero monitoraggio del governo australiano.

|Risorse|Collegamento|
|---|---|
|Sito Web del Centro sicurezza Cyber australiano|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>Risposta agli eventi imprevisti

La generazione dei log appropriati, la relativa raccolta in repository centralizzati e l'analisi delle prestazioni aumentano la comprensione dei sistemi e fornisce meccanismi per rilevare gli eventi imprevisti di sicurezza informatici. Dopo che gli eventi imprevisti o gli eventi sono stati rilevati, il passaggio successivo consiste nel reagire a tali eventi ed eseguire azioni per mantenere l'integrità del sistema e proteggere i servizi e i dati da compromessi. Azure offre una combinazione di servizi per rispondere efficacemente a tutti gli eventi che si verificano.

### <a name="azure-alerts"></a>Avvisi di Azure

Gli avvisi di Azure possono essere usati per notificare il supporto e il personale della sicurezza in risposta a determinati eventi. Questo consente a un'entità Commonwealth di rispondere in modo proattivo al rilevamento degli eventi rilevanti generati da Analysis Services elencati in questo articolo.

|Risorse|Collegamento|
|---|---|
|Panoramica degli avvisi in Microsoft Azure|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts)|
|Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)|
|Rispondere agli eventi con gli avvisi di Monitoraggio di Azure|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)|
|

### <a name="azure-automation"></a>Automazione di Azure

Automazione di Azure consente alle entità Commonwealth di attivare azioni in risposta a eventi. Questo potrebbe essere l'avvio di un'acquisizione di pacchetti in macchine virtuali, l'esecuzione di un flusso di lavoro, l'arresto o l'avvio di macchine virtuali o servizi o di un'ampia gamma di altre attività. L'automazione consente una rapida risposta agli avvisi senza interventi manuali, riducendo così il tempo di risposta e la gravità di un evento o di un evento imprevisto.

|Risorse|Collegamento|
|---|---|
|Documentazione di automazione di Azure|[https://docs.microsoft.com/azure/automation](https://docs.microsoft.com/azure/automation)|
|Guida alle procedure: Usare un avviso per attivare un runbook di Automazione di Azure|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)|
|

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sulla gestione sicura dell'ambiente gateway in Azure, vedere l'articolo relativo all' [amministrazione remota sicura del gateway](gateway-secure-remote-administration.md) .
