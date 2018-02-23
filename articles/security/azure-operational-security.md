---
title: Sicurezza operativa di Azure | Microsoft Docs
description: Informazioni su Microsoft Operations Management Suite (OMS), i suoi servizi e il suo funzionamento.
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
ms.openlocfilehash: 8528eeb4eac8397beaa3d0dca37ce9eb33167b32
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-operational-security"></a>Sicurezza operativa di Azure
## <a name="introduction"></a>Introduzione

### <a name="overview"></a>Panoramica
La sicurezza è la priorità principale nel cloud ed è importante che l'utente trovi informazioni accurate e tempestive sulla sicurezza di Azure. Uno dei motivi migliori per usare Azure per le applicazioni e i servizi consiste nella possibilità di sfruttare la vasta gamma di strumenti e funzionalità per la sicurezza disponibili. Questi strumenti e queste funzionalità consentono di creare soluzioni sicure sulla piattaforma Azure protetta. Windows Azure deve garantire riservatezza, integrità e disponibilità dei dati dei clienti, mantenendo allo stesso tempo la totale trasparenza nella rendicontazione.

Per consentire ai clienti una migliore comprensione della gamma di controlli di sicurezza implementati all'interno di Microsoft Azure dal punto di vista operativo sia del cliente che di Microsoft, è stato redatto questo white paper, "Sicurezza operativa di Azure", che offre una panoramica completa della sicurezza operativa garantita da Windows Azure.

### <a name="azure-platform"></a>Piattaforma Azure
Azure è una piattaforma di servizio cloud pubblico che supporta un'ampia gamma di sistemi operativi, linguaggi di programmazione, framework, strumenti, database e dispositivi. Azure può eseguire contenitori Linux con integrazione Docker, compilare app con JavaScript, Python, .NET, PHP, Java e Node.js e creare back-end per dispositivi iOS, Android e Windows. Il servizio cloud di Azure supporta le stesse tecnologie che milioni di sviluppatori e professionisti IT considerano attendibili e su cui già fanno affidamento.

Quando si compilano asset IT o se ne esegue la migrazione in un provider di servizi cloud pubblici, si dipende dalla capacità di tale organizzazione di proteggere le applicazioni e i dati con i servizi e i controlli offerti per gestire la sicurezza degli asset basati sul cloud.

L'infrastruttura di Azure è stata progettata, dalla struttura fino alle applicazioni, per ospitare milioni di clienti contemporaneamente e offre alle aziende una solida base per poter soddisfare le loro esigenze di sicurezza. Azure offre anche un'ampia gamma di opzioni di sicurezza configurabili, con la possibilità di controllarle per poter personalizzare il livello di sicurezza e soddisfare così i requisiti univoci di distribuzione dell'organizzazione. Questo documento offre informazioni sulle modalità con cui le funzionalità di sicurezza di Azure consentono di soddisfare questi requisiti.

### <a name="abstract"></a>Sunto
La sicurezza operativa di Azure include i servizi, i controlli e le funzionalità offerti agli utenti per proteggere i dati, le applicazioni e gli altri asset di Microsoft Azure. Questa soluzione si basa su un framework che incorpora le conoscenze acquisite tramite varie funzionalità univoche di Microsoft, tra cui Microsoft Security Development Lifecycle (SDL), il programma Microsoft Security Response Center e una profonda consapevolezza del panorama delle minacce per la sicurezza informatica.

Questo white paper delinea l'approccio di Microsoft alla sicurezza operativa di Azure all'interno della piattaforma cloud di Microsoft Azure e descrive i servizi seguenti:
1.  [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite

Microsoft Operations Management Suite (OMS) è la soluzione di gestione IT per il cloud ibrido. Usato autonomamente o come estensione per la distribuzione esistente di System Center, OMS offre la massima flessibilità e il massimo controllo per la gestione basata su cloud dell'infrastruttura.

![Microsoft Operations Management Suite](./media/azure-operational-security/azure-operational-security-fig1.png)

Con OMS, è possibile gestire qualsiasi istanza di qualsiasi cloud, inclusi i cloud locali, Azure, AWS, Windows Server, Linux, VMware e OpenStack, a un costo inferiore rispetto alle soluzioni della concorrenza. OMS, realizzato per le realtà orientate principalmente al cloud, offre un nuovo approccio alla gestione dell'azienda, vale a dire il modo più veloce e più economico per affrontare nuove problematiche aziendali e gestire nuovi carichi di lavoro, applicazioni e ambienti cloud.

### <a name="oms-services"></a>Servizi OMS

Le funzionalità principali di OMS vengono fornite da un set di servizi eseguiti in Azure. Ogni servizio fornisce una funzione di gestione specifica ed è possibile combinare i servizi per realizzare scenari di gestione diversi.

| Service  | DESCRIZIONE|
| :------------- | :-------------|
| Log Analytics | Monitora e analizza la disponibilità e le prestazioni di risorse diverse, inclusi i computer fisici e le macchine virtuali. |
|Automazione | Automatizza i processi manuali ed applica le configurazioni per i computer fisici e le macchine virtuali. |
| Backup | Backup e ripristino dei dati critici. |
| Site Recovery | Offre disponibilità elevata per le applicazioni critiche. |

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) fornisce servizi di monitoraggio per OMS raccogliendo i dati delle risorse gestite in un repository centrale. Questi dati possono includere eventi, dati sulle prestazioni o dati personalizzati forniti tramite l'API. Dopo essere stati raccolti, i dati sono disponibili per generare avvisi, per l'analisi e per l'esportazione.


Questo metodo consente di consolidare i dati provenienti da diverse origini, per poter combinare i dati dei servizi di Azure con l'ambiente locale esistente. Separa anche nettamente la raccolta dei dati dall'azione eseguita su tali dati in modo che tutte le azioni siano disponibili per tutti i tipi di dati.


![Log Analytics](./media/azure-operational-security/azure-operational-security-fig2.png)

Il servizio Log Analytics gestisce i dati basati sul cloud in modo sicuro usando i metodi seguenti:
-   separazione dei dati
-   conservazione dei dati
-   sicurezza fisica
-   gestione di eventi imprevisti
-   conformità
-   certificazioni degli standard di sicurezza

### <a name="azure-backup"></a>Backup di Azure

Il [Backup di Azure](http://azure.microsoft.com/documentation/services/backup) offre un servizio di backup e ripristino dei dati e fa parte della suite di prodotti e servizi OMS.
Protegge i dati delle applicazioni e li conserva per anni, senza investimenti di capitali e con costi operativi minimi. Consente di eseguire il backup dei dati da server Windows fisici e virtuali, oltre che dei carichi di lavoro di applicazioni come SQL Server e SharePoint. Può inoltre essere usato da [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) per replicare i dati protetti in Azure per la ridondanza e l'archiviazione a lungo termine.


I dati protetti in Backup di Azure vengono archiviati in un insieme di credenziali di backup che si trova in una determinata area geografica. I dati vengono replicati nella stessa area e, a seconda del tipo di insieme di credenziali, possono anche essere replicati in un'altra area per ottenere una maggiore resilienza.

### <a name="management-solutions"></a>Soluzioni di gestione
[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud.


Le [soluzioni di gestione](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) sono set predefiniti di logica che implementano un particolare scenario di gestione sfruttando uno o più servizi OMS. Sono disponibili diverse soluzioni Microsoft e dei partner che è possibile aggiungere facilmente alla sottoscrizione di Azure per aumentare il valore dell'investimento in OMS. I partner possono creare le proprie soluzioni per supportare le applicazioni e i servizi e offrirle agli utenti tramite Azure Marketplace o i modelli di avvio rapido.


![Soluzioni di gestione](./media/azure-operational-security/azure-operational-security-fig4.png)

Un valido esempio di soluzione che sfrutta più servizi per offrire funzionalità aggiuntive è la [soluzione Gestione aggiornamenti](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Questa soluzione usa l'agente di [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) per Windows e Linux per raccogliere informazioni sugli aggiornamenti necessari in ogni agente. Scrive questi dati nel repository di Log Analytics dove è possibile analizzarli con un dashboard incluso.

Quando si crea una distribuzione, vengono usati i runbook dell'[Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) per installare gli aggiornamenti necessari. L'intero processo viene gestito nel portale, non è necessario preoccuparsi dei dettagli sottostanti.

## <a name="azure-security-center"></a>Centro sicurezza di Azure

Il Centro sicurezza di Azure consente di proteggere le risorse di Azure. Integra il monitoraggio della sicurezza e la gestione dei criteri in tutte le sottoscrizioni di Azure. Nell'ambito del servizio, è possibile definire criteri non solo per le sottoscrizioni di Azure, ma anche per i [gruppi di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), in modo da ottenere una maggiore granularità.

### <a name="security-policies-and-recommendations"></a>Criteri di sicurezza e raccomandazioni

I criteri di sicurezza definiscono il set di controlli consigliati per le risorse all'interno della sottoscrizione o del gruppo di risorse specificato.

Nel Centro sicurezza è possibile definire i criteri in base ai requisiti di sicurezza della società e al tipo di applicazioni o al livello di riservatezza dei dati.

![Criteri di sicurezza e raccomandazioni](./media/azure-operational-security/azure-operational-security-fig5.png)


I criteri abilitati nel livello di sottoscrizione vengono propagati automaticamente a tutti i gruppi di risorse all'interno della sottoscrizione, come illustrato nel diagramma a destra:


### <a name="data-collection"></a>Raccolta dei dati

Il Centro sicurezza raccoglie i dati dalle macchine virtuali per valutarne lo stato della sicurezza, indicare raccomandazioni sulla sicurezza e segnalare le minacce. La prima volta che si accede al Centro sicurezza, la raccolta dati viene abilitata in tutte le macchine virtuali della sottoscrizione. La raccolta dati è consigliata, ma è possibile rifiutare esplicitamente disattivandola nei criteri del Centro sicurezza.

### <a name="data-sources"></a>Origini dati

- Il Centro sicurezza di Azure analizza i dati provenienti dalle origini seguenti per offrire visibilità sullo stato della sicurezza, identificare le vulnerabilità e suggerire le mitigazioni e rilevare minacce attive:

-   Servizi di Azure: usa le informazioni sulla configurazione dei servizi di Azure distribuiti comunicando con il provider di risorse del servizio.

- Traffico di rete: usa i metadati del traffico di rete campionati dall'infrastruttura di Microsoft, ad esempio l'IP/porta di origine/destinazione, le dimensioni del pacchetto e il protocollo di rete.

-   Soluzioni partner: usa gli avvisi di sicurezza dalle soluzioni partner integrate, ad esempio firewall e soluzioni antimalware.

-   Macchine virtuali: usa informazioni sulla configurazione e informazioni sugli eventi di sicurezza, ad esempio registri eventi di Windows e log di controllo, log di IIS, messaggi syslog e file di dump di arresto anomalo del sistema dalle macchine virtuali.

### <a name="data-protection"></a>Protezione dati

Per consentire ai clienti di impedire, rilevare e rispondere alle minacce, il Centro sicurezza di Azure raccoglie ed elabora dati correlati alla sicurezza, tra cui informazioni di configurazione, metadati, registri eventi, file di dump di arresto anomalo del sistema e altro. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio.

-   **Separazione dei dati:**i dati vengono mantenuti separati logicamente in ogni componente del servizio. Tutti i dati vengono contrassegnati in base all'organizzazione. Tale contrassegno persiste per tutto il ciclo di vita dei dati e viene applicato a ogni livello del servizio.

-   **Accesso ai dati**: per offrire consigli sulla sicurezza e analizzare le potenziali minacce, il personale Microsoft può accedere alle informazioni raccolte o analizzate dai servizi di Azure, inclusi file di dump di arresto anomalo del sistema, eventi di creazione di un processo, snapshot del disco della macchina virtuale ed elementi, che potrebbero accidentalmente contenere dati del cliente o dati personali provenienti dalle macchine virtuali. Microsoft rispetta le [condizioni e l'informativa sulla privacy dei Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), in cui è specificato che Microsoft non usa i dati del cliente e non ricava informazioni per scopi pubblicitari o simili finalità commerciali.

-   **Uso dei dati**: Microsoft usa modelli e intelligence per le minacce trovati in più tenant per migliorare le funzionalità di prevenzione e rilevamento, in base alle garanzie relative alla privacy descritte nell'[Informativa sulla privacy](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

### <a name="data-location"></a>Posizione dei dati

Il Centro sicurezza di Azure raccoglie copie temporanee dei file di dump di arresto anomalo del sistema e le analizza per cercare le prove di tentativi di exploit e compromissioni riuscite. Il Centro sicurezza di Azure esegue questa analisi nella stessa area geografica dell'area di lavoro ed elimina le copie temporanee al termine dell'analisi. Gli elementi del computer vengono archiviati centralmente nella stessa area della VM.

-   **Account di archiviazione**: viene specificato un account di archiviazione per ogni area in cui sono in esecuzione macchine virtuali. Questo consente di archiviare i dati nella stessa area della macchina virtuale da cui vengono raccolti i dati.

-   **Archiviazione in Centro sicurezza di Azure**: le informazioni sugli avvisi di sicurezza, inclusi gli avvisi dei partner, le raccomandazioni e lo stato integrità della sicurezza, vengono archiviate a livello centrale, attualmente negli Stati Uniti. Queste informazioni possono includere informazioni sulla configurazione correlate ed eventi di sicurezza raccolti dalle macchine virtuali, necessari per conoscere l'avviso di sicurezza, la raccomandazione o lo stato integrità della sicurezza.


## <a name="azure-monitor"></a>Monitoraggio di Azure

La soluzione [OMS Security](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) and Audit consente all'IT di monitorare attivamente tutte le risorse, per ridurre l'impatto degli eventuali problemi di sicurezza. OMS Security and Audit prevede domini di sicurezza che possono essere usati per il monitoraggio delle risorse. I domini di sicurezza consentono di accedere rapidamente alle opzioni per il monitoraggio della sicurezza. Verranno analizzati in modo più approfondito i domini seguenti:

-   Valutazione di software dannoso
-   Valutazione aggiornamenti
-   Identità e accesso.

Il Monitoraggio di Azure offre puntatori a informazioni su tipi di risorse specifici. Offre anche funzionalità di visualizzazione, query, routing, avviso, ridimensionamento automatico e automazione dei dati sia dall'infrastruttura di Azure (log attività) che da ogni singola risorsa di Azure (log di diagnostica).

![Monitoraggio di Azure](./media/azure-operational-security/azure-operational-security-fig6.png)


Le applicazioni cloud sono complesse e hanno molte parti mobili. Il monitoraggio offre la possibilità di garantire il funzionamento e l'integrità dell'applicazione. Consente anche di prevenire i problemi potenziali o di risolvere quelli precedenti.

Inoltre, è possibile usare i dati di monitoraggio per ottenere informazioni approfondite sull'applicazione, utili per migliorarne le prestazioni o la manutenibilità oppure per automatizzare azioni che altrimenti richiederebbero un intervento manuale.

### <a name="azure-activity-log"></a>Azure Activity Log


Questo log offre informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione. Il log attività era noto in precedenza come "log di controllo" o "log operativo", perché segnala eventi del piano di controllo per le sottoscrizioni.

![Azure Activity Log](./media/azure-operational-security/azure-operational-security-fig7.png)

L'uso del log attività permette di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche. Il log attività non include le operazioni di lettura (GET) o quelle per le risorse che usano il modello classico.

### <a name="azure-diagnostic-logs"></a>Log di diagnostica di Azure

I log di diagnostica vengono generati da una risorsa e contengono informazioni approfondite e frequenti sul funzionamento di tale risorsa. Il contenuto di questi log varia in base al tipo di risorsa.

Ad esempio, i log del sistema eventi di Windows sono una categoria di log di diagnostica per le macchine virtuali, i BLOB e le tabelle, mentre i log della coda sono categorie di log di diagnostica per gli account di archiviazione.

I log di diagnostica sono diversi dai [log attività (precedentemente conosciuti come log di controllo o log operativi)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Il log attività fornisce informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione. I log di diagnostica forniscono informazioni approfondite sulle operazioni che la risorsa esegue automaticamente.

### <a name="metrics"></a>Metriche

Il monitoraggio di Azure consente di usare la telemetria per ottenere visibilità sulle prestazioni e sull'integrità dei carichi di lavoro in Azure. Il tipo di dati di telemetria Azure più importante è rappresentato dalle metriche (altrimenti dette contatori delle prestazioni) generate dalla maggior parte delle risorse di Azure. Il Monitoraggio di Azure offre svariati modi per configurare e usare queste [metriche](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) per il monitoraggio e la risoluzione dei problemi. Le metriche sono un'importante fonte di dati di telemetria e consentono di eseguire le attività seguenti:

-   **Tenere traccia delle prestazioni** della risorsa (ad esempio, una VM, un sito Web o un'app per la logica) tracciandone le metriche in un grafico del portale da aggiungere a un dashboard.

-   **Ricevere la notifica di un problema** che influisce sulle prestazioni della risorsa quando una metrica supera una determinata soglia.

-   **Configurare le azioni automatiche**, ad esempio il ridimensionamento automatico di una risorsa o l'esecuzione di un runbook quando una metrica supera una determinata soglia.

-   **Eseguire analisi avanzate** o creare report relativi alle tendenze delle prestazioni o di uso della risorsa.

-   **Archiviare** la cronologia relativa alle prestazioni o all'integrità della risorsa a scopo di conformità o verifica.

### <a name="azure-diagnostics"></a>Diagnostica Azure

È la funzionalità di Azure che consente la raccolta di dati di diagnostica in un'applicazione distribuita. È possibile usare l'estensione di diagnostica da numerose origini diverse. Sono attualmente supportati [ruoli Web e di lavoro del servizio cloud di Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/overview) che eseguono Microsoft Windows e [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Altri servizi di Azure dispongono di propri strumenti di diagnostica separati.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Il controllo della sicurezza della rete è fondamentale per rilevare le vulnerabilità e garantire la conformità con il modello di governance normativo e della sicurezza IT. Con la visualizzazione Gruppo di sicurezza, è possibile recuperare il gruppo di sicurezza di rete e le regole di sicurezza configurate, ma anche le regole di sicurezza efficaci. Con l'elenco delle regole applicate, è possibile determinare le porte aperte e valutare la vulnerabilità di rete.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di rete da, verso e in Azure. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure. Il servizio include l'acquisizione pacchetti, l'hop successivo, la verifica del flusso IP, la visualizzazione dei gruppi di sicurezza e i registri dei flussi dei gruppi di sicurezza di rete. A differenza del monitoraggio a livello di singole risorse di rete, il monitoraggio a livello di scenario consente una visualizzazione completa delle risorse di rete.

![Azure Network Watcher](./media/azure-operational-security/azure-operational-security-fig8.png)

Di seguito sono elencate le funzionalità attualmente disponibili in Network Watcher.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Log di controllo</a>**- Vengono registrate le operazioni eseguite nell'ambito della configurazione di reti. È possibile visualizzare i relativi log nel portale di Azure o recuperarli usando strumenti Microsoft, come Power BI, o strumenti di terze parti. I log di controllo sono disponibili tramite il portale, PowerShell, l'interfaccia della riga di comando e l'API REST. Per altre informazioni sui log di controllo, vedere l'articolo relativo alle operazioni di controllo con Gestione risorse. I log di controllo sono disponibili per le operazioni eseguite su tutte le risorse di rete.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Verifica flusso IP</a>** - Controlla se un pacchetto viene accettato o rifiutato in base ai relativi parametri sul flusso di informazioni, costituiti da informazioni a 5 tuple, ovvero l'indirizzo IP di destinazione, l'indirizzo IP di origine, la porta di destinazione, la porta di origine e il protocollo. Se il pacchetto viene rifiutato da un gruppo di sicurezza di rete, vengono restituiti la regola e il gruppo di sicurezza di rete che hanno rifiutato il pacchetto.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Hop successivo</a>**: determina l'hop successivo per i pacchetti indirizzati nell'infrastruttura di rete di Azure, permettendo così di diagnosticare eventuali route definite dall'utente non configurate in modo corretto.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Visualizzazione dei gruppi di sicurezza</a>**: ottiene le regole di sicurezza valide e applicate in una macchina virtuale.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Registrazione dei flussi dei gruppi di sicurezza di rete</a>**: la registrazione dei flussi dei gruppi di sicurezza di rete permette di acquisire i log relativi al traffico consentito o negato dalle regole di sicurezza nel gruppo. Il flusso è definito da informazioni a 5 tuple, ovvero l'indirizzo IP di destinazione, l'indirizzo IP di origine, la porta di destinazione, la porta di origine e il protocollo.

## <a name="azure-storage-analytics"></a>Analisi archiviazione di Azure

[Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) è in grado di archiviare le metriche che includono le statistiche delle transazioni aggregate e i dati di capacità relativi alle richieste in un servizio di archiviazione. Le transazioni vengono segnalate sia a livello di operazione API, sia a livello di servizio di archiviazione, mentre la capacità viene segnalata a livello di servizio di archiviazione. I dati delle metriche possono essere utilizzati per analizzare l'uso del servizio di archiviazione, diagnosticare i problemi relativi alle richieste effettuate al servizio di archiviazione e per migliorare le prestazioni delle applicazioni che usano un servizio.

L'[Analisi archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) esegue la registrazione e restituisce i dati delle metriche per un account di archiviazione. È possibile utilizzare questi dati per tenere traccia delle richieste, analizzare le tendenze d'uso e diagnosticare i problemi relativi al proprio account di archiviazione. La registrazione dell'Analisi archiviazione di Azure è disponibile per i [servizi BLOB, code e tabelle](https://docs.microsoft.com/azure/storage/storage-introduction). Analisi archiviazione registra informazioni dettagliate sulle richieste riuscite e non a un servizio di archiviazione.

Queste informazioni possono essere utilizzate per monitorare le singole richieste e per diagnosticare problemi relativi a un servizio di archiviazione. Le richieste vengono registrate in base al massimo sforzo. Le voci di registro vengono create solo se esistono richieste effettuate per l'endpoint di servizio. Se, ad esempio, un account di archiviazione presenta un'attività nell'endpoint BLOB ma non negli endpoint tabella o coda, saranno creati solo registri relativi al servizio BLOB.

Per utilizzare Analisi archiviazione, è necessario abilitarla singolarmente per ciascun servizio che si desidera monitorare. È possibile abilitarla nel [portale di Azure](https://portal.azure.com/); per informazioni dettagliate, vedere [Monitorare un account di archiviazione nel portale di Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). È inoltre possibile abilitare Analisi archiviazione a livello di codice tramite l'API REST o la libreria client. Per abilitare l'Analisi archiviazione per ogni servizio, usare le operazioni che consentono di impostare le proprietà dei servizi.

I dati aggregati vengono archiviati in un BLOB noto (per la registrazione) e in tabelle note (per le metriche), a cui è possibile accedere tramite le API del servizio BLOB e del servizio tabelle.

L'Analisi archiviazione può archiviare fino a un massimo di 20 TB di dati. Tale limite è indipendente dal limite totale dell'account di archiviazione. Tutti i log vengono archiviati in [BLOB in blocchi](https://docs.microsoft.com/azure/storage/storage-analytics) all'interno di un contenitore denominato $logs, che viene creato automaticamente quando viene abilitata l'Analisi archiviazione per un account di archiviazione.

Le seguenti azioni eseguite da Analisi archiviazione sono fatturabili:

-   Richieste di creazione di BLOB per la registrazione
-   Richieste di creazione di entità di tabella per le metriche

> [!Note]
> Per altre informazioni sulla fatturazione e sui criteri di conservazione dei dati, vedere [Storage Analytics and Billing](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing) (Analisi archiviazione e fatturazione).
> Per prestazioni ottimali, è consigliabile limitare il numero di dischi a elevato utilizzo collegati alla macchina virtuale per evitare una possibile limitazione. Se non tutti i dischi presentano un utilizzo elevato nello stesso momento, l'account di archiviazione può supportare un numero maggiore di dischi.

> [!Note]
> Per informazioni sui limiti dell'account di archiviazione, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Vengono registrati i tipi seguenti di richieste autenticate e anonime.

| Autenticata  | Anonima|
| :------------- | :-------------|
| Richieste riuscite | Richieste riuscite |
|Richieste non riuscite, tra cui errori di timeout, limitazione, rete, autorizzazione e di altro tipo | Richieste tramite una firma di accesso condiviso (SAS), incluse le richieste riuscite e non riuscite |
| Richieste tramite una firma di accesso condiviso (SAS), incluse le richieste riuscite e non riuscite |Errori di timeout per client e server |
|   Richieste ai dati di analisi |    Richieste GET non riuscite con codice di errore 304 (non modificate) |
| Le richieste eseguite dalla stessa Analisi archiviazione, ad esempio, la creazione oppure l'eliminazione di log, non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Operazioni registrate in Analisi archiviazione e messaggi di stato](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [Formato log Analisi archiviazione](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Tutte le altre richieste anonime non riuscite non vengono registrate. Un elenco completo dei dati registrati è documentato negli argomenti [Storage Analytics Logged Operations and Status Messages](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) (Operazioni registrate e messaggi di stato nell'Analisi archiviazione ) e [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) (Formato dei registri di Analisi archiviazione). |
## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD include anche una suite completa di funzionalità per la gestione delle identità, tra cui la Multi-Factor Authentication, la registrazione dei dispositivi, la gestione self-service delle password e dei gruppi, la gestione degli account con privilegi, il controllo degli accessi in base al ruolo, il monitoraggio dell'utilizzo dell'applicazione, il controllo avanzato e il monitoraggio e gli avvisi relativi alla sicurezza.

-   Miglioramento della sicurezza delle applicazioni grazie all'autenticazione a più fattori e all'accesso condizionale di Azure AD.

-   Monitoraggio dell'utilizzo delle applicazioni e protezione dell'attività da minacce avanzate, grazie alla creazione di report e al monitoraggio della sicurezza.

Azure Active Directory (Azure AD) include la sicurezza, l’attività e i report di controllo per la directory. La [Guida alla creazione di report in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) consente ai clienti di identificare le azioni con privilegi che si sono verificate nella propria istanza di Azure Active Directory. Le azioni con privilegi includono modifiche di elevazione dei privilegi, ad esempio la creazione dei ruoli o le reimpostazioni delle password, la modifica delle configurazioni dei criteri, ad esempio i criteri delle password o le modifiche alla configurazione della directory, ad esempio le modifiche alle impostazioni di federazione del dominio.

Nei report è incluso il record di controllo per il nome dell'evento, l'attore che ha eseguito l'azione, la risorsa di destinazione interessata dalla modifica e la data e l'ora (UTC). I clienti possono recuperare l'elenco degli eventi di controllo per la propria istanza di Azure Active Directory tramite il [portale di Azure](https://portal.azure.com/), come descritto in [Visualizzare i log di controllo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Di seguito è riportato un elenco dei report compresi:

| Report sulla sicurezza  | Report sull’attività| Report di controllo |
| :------------- | :-------------| :-------------|
|Accessi da origini sconosciute | Utilizzo dell'applicazione: riepilogo | Report di controllo della Directory |
|Accessi dopo più errori | Utilizzo dell'applicazione: dettagliato |   |
|Accessi da più aree geografiche | Dashboard dell'applicazione |  |
|Accessi da indirizzi IP con attività sospette |Errori di provisioning dell'account |  |
|Attività di accesso irregolare |Dispositivi dell’utente singolo |  |
|Accessi da dispositivi potenzialmente infetti |Attività dell’utente singolo |   |
|Utenti con anomalie dell'attività di accesso |Report attività dei gruppi |   |
| |Report attività di registrazione per la reimpostazione password |   |
| |Attività di reimpostazione password |   | |



I dati di questi report possono essere molto utili per le applicazioni, ad esempio i sistemi SIEM e gli strumenti di controllo e business intelligence. Le [API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) di creazione report di Azure AD consentono l'accesso programmatico ai dati tramite un set di API basate su REST. È possibile chiamare queste API da numerosi linguaggi di programmazione e strumenti.

Gli eventi nel report di controllo di Azure Active Directory vengono conservati per 180 giorni.

> [!Note]
> Per altre informazioni sulla conservazione dei report, vedere la pagina relativa ai [criteri di conservazione dei report di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Per i clienti interessati alla conservazione degli [eventi di controllo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) per periodi più lunghi, l'API di creazione report consente di eseguire regolarmente il pull degli eventi di controllo in un archivio dati separato.

## <a name="summary"></a>Summary

Questo articolo riassume le caratteristiche di protezione della privacy e dei dati degli utenti, mettendo a disposizione software e servizi che aiutino a gestire l'infrastruttura IT dell'organizzazione. Microsoft è a conoscenza della necessità di applicare rigorose misure di sicurezza quando si affidano i dati a terzi. Microsoft è conforme alle più rigorose linee guida sulla sicurezza e sulla conformità in tutte le fasi, dalla codifica all'esecuzione di un servizio. La sicurezza e la protezione dei dati sono altamente prioritarie per Microsoft.

Questo articolo descrive

-   Il modo in cui i dati vengono raccolti, elaborati e protetti in Operations Management Suite (OMS).

-   Come analizzare rapidamente gli eventi tra più origini di dati. Identificare i rischi per la sicurezza, l'ambito e l'impatto di minacce e attacchi, per ridurre il danno di una eventuale violazione della sicurezza.

-   Come identificare i modelli di attacco visualizzando il traffico IP dannoso in uscita e i tipi di minaccia pericolosi. Esaminare il comportamento di sicurezza dell'intero ambiente indipendentemente dalla piattaforma.

-   Come acquisire tutti i dati di registro e degli eventi necessari per un controllo di sicurezza o di conformità. Ridurre le risorse e i tempi necessari per eseguire un controllo di sicurezza grazie a un registro e a un set di dati degli eventi completi, esportabili e in cui poter eseguire ricerche.

<ul>
<li>Come raccogliere gli eventi di sicurezza, controllo e analisi di violazione per tenere sotto controllo gli asset:</li>
<ul>
<li>Comportamento di sicurezza</li>
<li>Errori rilevanti</li>
<li>Riepilogo delle minacce</li>
</ul>
</ul>

## <a name="next-steps"></a>Passaggi successivi

- [Progettazione e sicurezza operativa](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft progetta il software e i servizi tenendo a mente la sicurezza degli utenti per assicurare che l'infrastruttura cloud sia resiliente e protetta dagli attacchi.

- [Operations Management Suite | Sicurezza e Conformità](https://www.microsoft.com/cloud-platform/security-and-compliance)

Usare le caratteristiche Microsoft di sicurezza dei dati e analisi per eseguire il rilevamento delle minacce più intelligente ed efficiente.

- [Guida alla pianificazione e alla gestione del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) Una serie di passaggi e attività per ottimizzare l'uso del Centro sicurezza in base ai requisiti di sicurezza e al modello di gestione cloud dell'organizzazione.

