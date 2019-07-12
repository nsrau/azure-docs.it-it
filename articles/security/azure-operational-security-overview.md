---
title: Panoramica sulla sicurezza operativa di Azure | Microsoft Docs
description: Questo articolo offre una panoramica sulla sicurezza operativa di Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2018
ms.author: tomsh
ms.openlocfilehash: ca9feb226a077484ba43bef7cd14d962ea95b37a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653415"
---
# <a name="azure-operational-security-overview"></a>Panoramica sulla sicurezza operativa di Azure

La [sicurezza operativa di Azure](https://docs.microsoft.com/azure/security/azure-operational-security) include i servizi, i controlli e le funzionalità offerti agli utenti per proteggere i dati, le applicazioni e gli altri asset di Microsoft Azure. È un framework che incorpora le conoscenze acquisite attraverso varie funzionalità uniche di Microsoft. Queste funzionalità includono Microsoft Security Development Lifecycle (SDL), il programma Microsoft Security Response Center e una profonda conoscenza del panorama delle minacce della cybersecurity.

## <a name="azure-management-services"></a>Servizi di gestione di Azure

Un team operativo IT è responsabile della gestione dell'infrastruttura dei data center, delle applicazioni e dei dati, nonché della stabilità e della sicurezza di questi sistemi. Tuttavia, ottenere informazioni di sicurezza in ambienti IT sempre più complessi spesso richiede alle organizzazioni di assemblare i dati da più sistemi di gestione e sicurezza.

[I log di Microsoft Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) è una soluzione gestione IT basata sul cloud che consente di gestire e proteggere on-premises e infrastruttura cloud. OMS si basa sui servizi seguenti in esecuzione in Azure. Azure include più servizi che consentono di gestire e proteggere l'infrastruttura locale e cloud. Ogni servizio offre una funzione di gestione specifica. I servizi possono essere combinati tra loro per ottenere scenari di gestione diversi. 

### <a name="azure-monitor"></a>Monitoraggio di Azure

[Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) raccoglie i dati delle origini gestite in archivi dati centrali. Questi dati possono essere eventi, dati sulle prestazioni o dati personalizzati specificati attraverso l'API. Dopo che sono stati raccolti, i dati sono disponibili perla generazione di avvisi, l'analisi e l'esportazione. 

È possibile consolidare i dati da varie origini e combinare i dati disponibili dai servizi di Azure con l'ambiente locale esistente. Log di monitoraggio di Azure separa anche nettamente la raccolta dei dati dall'azione eseguita su tali dati, in modo che tutte le azioni sono disponibili a tutti i tipi di dati.

### <a name="automation"></a>Automazione

[Automazione di Azure](https://docs.microsoft.com/azure/automation/automation-intro) offre agli utenti la possibilità di automatizzare le attività manuali, a esecuzione prolungata, soggette a errori e ripetute di frequente comunemente eseguite negli ambienti cloud e aziendali. Consente di risparmiare tempo e aumenta l'affidabilità delle attività amministrative. Consente persino di pianificare queste attività in modo che vengano eseguite automaticamente a intervalli regolari. È possibile automatizzare i processi utilizzando runbook o automatizzare la gestione della configurazione tramite Configurazione dello stato desiderato.

### <a name="backup"></a>Backup

[Backup di Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) è il servizio basato su Azure che consente di eseguire il backup (o proteggere) e il ripristino dei dati in Microsoft Cloud. Backup di Azure sostituisce la soluzione di backup locale o esterna esistente con una soluzione basata sul cloud affidabile, sicura e concorrenziale in termini di costi. 

Backup di Azure offre componenti che vengono scaricati e distribuiti nel computer o server appropriato o nel cloud. Il componente o l'agente distribuito dipende da ciò che si intende proteggere. Tutti i componenti di Backup di Azure consentono di eseguire il backup dei dati in Servizi di ripristino di Microsoft Azure, a prescindere che i dati da proteggere si trovino in locale o nel cloud. 

Per altre informazioni, vedere la [scheda dei componenti del Backup di Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-backup-agent-should-i-use).

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) garantisce la continuità aziendale orchestrando la replica delle macchine virtuali e dei computer fisici locali in Azure o in un sito secondario. Se il sito primario non è disponibile, viene eseguito il failover nella posizione secondaria in modo che gli utenti possano continuare a lavorare. Nel momento in cui i sistemi tornano operativi, Azure Site Recovery esegue il failback. Usare Centro sicurezza di Azure per eseguire un rilevamento delle minacce più avanzato ed efficace.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) è un servizio per la gestione delle identità completo che offre i vantaggi seguenti:

-   Consente la gestione delle entità e degli accessi (IAM) come servizio cloud.
-   Offre la gestione centrale degli accessi, l'accesso Single Sign-On (SSO) e la creazione di report.
-   Supporta la gestione degli accessi integrata per [migliaia di applicazioni](https://azure.microsoft.com/marketplace/active-directory/) in Azure Marketplace, incluse Salesforce, Google Apps, Box e Concur.

Azure AD include inoltre una suite completa di [funzionalità di gestione delle identità](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), tra cui:

- [Autenticazione a più fattori](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)
- [Gestione delle password self-service](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Gestione di gruppi self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)
- [Gestione degli account con privilegi](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
- [Controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [Monitoraggio dell'utilizzo delle applicazioni](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
- [Controllo completo](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
- [Monitoraggio della sicurezza e invio di avvisi](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts)

Con Azure Active Directory tutte le applicazioni che si pubblicano per i partner e i clienti (aziende o utenti privati) hanno le stesse funzionalità di gestione delle identità e degli accessi. Ciò consente una riduzione notevole dei costi operativi.

## <a name="azure-security-center"></a>Centro sicurezza di Azure

[Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) impedisce, rileva e risponde alle minacce mediante visibilità e controllo avanzati della sicurezza delle risorse di Azure. Integra il monitoraggio della sicurezza e la gestione dei criteri in tutte le sottoscrizioni. Aiuta a rilevare le minacce che potrebbero altrimenti passare inosservate e opera con un ampio ecosistema di soluzioni per la sicurezza.

Centro sicurezza aiuta a [proteggere i dati delle macchine virtuali (VM)](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) in Azure offrendo visibilità sulle impostazioni di sicurezza della macchina virtuale e monitoraggio delle minacce. Il Centro sicurezza può monitorare nelle macchine virtuali quanto segue:

- Impostazioni di sicurezza del sistema operativo con le regole di configurazione consigliate
- Aggiornamenti della sicurezza del sistema e altri aggiornamenti di importanza critica eventualmente mancanti
- Consigli per la protezione degli endpoint
- Convalida della crittografia del disco
- Attacchi basati sulla rete.

Centro sicurezza usa il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal). Questo tipo di controllo prevede l'utilizzo di [ruoli integrati](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza è possibile visualizzare le informazioni correlate a una risorsa solo quando si dispone del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse a cui appartiene la risorsa.

>[!Note]
>Per altre informazioni sui ruoli e sulle azioni consentite nel Centro sicurezza, vedere [Autorizzazioni nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-permissions).

Centro sicurezza usa Microsoft Monitoring Agent. Questo è lo stesso agente usato dal servizio monitoraggio di Azure. I dati che vengono raccolti da questo agente vengono archiviati in un'[area di lavoro](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) esistente di Log Analytics associata a una sottoscrizione di Azure o in un'area di lavoro nuova, in base alla posizione geografica della VM.

## <a name="azure-monitor"></a>Monitoraggio di Azure

I problemi di prestazioni nell'app cloud possono avere un impatto negativo su un'azienda. Con più componenti interconnessi e versioni frequenti, possono verificarsi in qualsiasi momento riduzioni delle prestazioni. Quando si sviluppa un'app, gli utenti in genere individuano problemi non trovati nei test. È consigliabile venire a conoscenza di questi problemi immediatamente e avere gli strumenti per diagnosticarli e risolverli.

[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) è lo strumento di base per il monitoraggio dei servizi eseguiti in Azure. Offre dati a livello di infrastruttura sulla velocità effettiva di un servizio e l'ambiente circostante. Se si gestiscono tutte le app in Azure e si deve decidere se aumentare o ridurre le risorse, Monitoraggio di Azure offre un utile punto di partenza.

Il monitoraggio dei dati offre inoltre informazioni approfondite sull'applicazione, utili per migliorarne le prestazioni o la manutenibilità oppure per automatizzare azioni che altrimenti richiederebbero un intervento manuale. 

Monitoraggio di Azure include i componenti seguenti:

### <a name="azure-activity-log"></a>Azure Activity Log

[Azure Activity Log](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) offre informazioni approfondite in merito alle operazioni eseguite sulle risorse nella sottoscrizione. Questo servizio è noto nelle versioni precedenti come "log di controllo" o "log operativo", perché segnala eventi del piano di controllo per le sottoscrizioni.

### <a name="azure-diagnostic-logs"></a>Log di diagnostica di Azure

I [log di diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sono generati da una risorsa e offrono dati completi e frequenti sul funzionamento della risorsa stessa. Il contenuto di questi log varia in base al tipo di risorsa.

I registri di sistema degli eventi di Windows sono una categoria di log di diagnostica per le macchine virtuali. I log di BLOB, tabelle e code sono categorie di log di diagnostica per gli account di archiviazione.

I log di diagnostica differiscono dal [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Il log attività fornisce informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione. I log di diagnostica forniscono informazioni approfondite sulle operazioni che la risorsa esegue automaticamente.

### <a name="metrics"></a>metrics

Monitoraggio di Azure consente di usare i dati di telemetria per ottenere visibilità sulle prestazioni e sull'integrità dei carichi di lavoro in Azure. Il tipo di dati di telemetria di Azure più importante è rappresentato dalle [metriche](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) (altrimenti dette contatori delle prestazioni) generate dalla maggior parte delle risorse di Azure. Il monitoraggio di Azure offre alcuni modi per configurare e usare queste metriche per il monitoraggio e la risoluzione dei problemi.

### <a name="azure-diagnostics"></a>Diagnostica Azure

Diagnostica di Azure consente la raccolta di dati di diagnostica in un'applicazione distribuita. È possibile usare l'estensione Diagnostica da varie origini. Sono attualmente supportati [ruoli Web e di lavoro del servizio cloud di Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [macchine virtuali di Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) che eseguono Microsoft Windows e [Microsoft Azure Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).

## <a name="azure-network-watcher"></a>Azure Network Watcher

Per creare una rete end-to-end in Azure è possibile orchestrare e comporre varie risorse di rete individuali, quali rete virtuale, Azure ExpressRoute, gateway applicazione di Azure e servizi di bilanciamento del carico. Il monitoraggio è disponibile in ognuna delle risorse di rete.

La rete end-to-end può avere configurazioni e interazioni tra risorse complesse. Ne derivano scenari complessi che necessitano di un monitoraggio specifico attraverso [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

Network Watcher semplifica il monitoraggio e la diagnostica della rete di Azure. È possibile usare gli strumenti di diagnostica e di visualizzazione di Network Watcher per:

- Ottenere acquisizioni dei pacchetti remoti in una macchina virtuale Azure.
- Acquisire informazioni dettagliate sul traffico di rete usando i log dei flussi.
- Diagnosticare le connessioni e il gateway VPN di Azure.

Di seguito sono elencate le funzionalità attualmente disponibili in Network Watcher.

- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): offre una visualizzazione delle varie interconnessioni e associazioni tra le risorse di rete in un gruppo di risorse.
- [Acquisizione pacchetti variabile](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): Acquisisce i dati dei pacchetti in ingresso e in uscita da una macchina virtuale. Opzioni di filtro avanzate e controlli ottimizzati, ad esempio la possibilità di impostare limitazioni di tempo e dimensioni, offrono versatilità. È possibile memorizzare i dati dei pacchetti in un archivio BLOB o nel disco locale in formato CAP.
- [Verifica flusso IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): controlla se un pacchetto viene accettato o rifiutato in base ai parametri di pacchetto a 5 tuple per le informazioni sul flusso, ovvero l'indirizzo IP di destinazione, l'indirizzo IP di origine, la porta di destinazione, la porta di origine e il protocollo. Se il pacchetto viene rifiutato da un gruppo di sicurezza, vengono restituiti la regola e il gruppo che hanno rifiutato il pacchetto.
- [Hop successivo](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): determina l'hop successivo per i pacchetti indirizzati nell'infrastruttura di rete di Azure, consentendo così di diagnosticare eventuali route definite dall'utente non configurate in modo corretto.
- [Visualizzazione dei gruppi di sicurezza](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): Ottiene le regole di sicurezza valide e applicate in una macchina virtuale.
- [Log del flusso NSG per i gruppi di sicurezza di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): consentono di acquisire log correlati al traffico che è consentito o negato dalle regole di sicurezza nel gruppo. Il flusso viene definito da informazioni a 5 tuple, ovvero l'indirizzo IP di origine, l'indirizzo IP di destinazione, la porta di origine, la porta di destinazione e il protocollo.
- [Risoluzione dei problemi di connessione e del gateway di rete virtuale](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): Network Watcher consente di risolvere i problemi delle connessioni e dei gateway di rete virtuale.
- [Limiti delle sottoscrizioni di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Consente di visualizzare l'uso delle risorse di rete rispetto ai limiti.
- [Log di diagnostica](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): consente di abilitare o disabilitare da un unico riquadro i log di diagnostica per le risorse di rete in un gruppo di risorse.

Per altre informazioni, vedere [Configure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create) (Configurare Network Watcher).

## <a name="cloud-service-provider-access-transparency"></a>Trasparenza di accesso del provider di servizi cloud

[Customer Lockbox per Microsoft Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/) è un servizio integrato nel portale di Azure che offre controllo esplicito nel raro caso in cui un tecnico del supporto Microsoft debba accedere ai dati del cliente per risolvere un problema. I casi in cui un tecnico del supporto Microsoft richiede autorizzazioni elevate per risolvere un problema, ad esempio eseguire il debug dell'accesso remoto, sono molto rari. In casi come questi, i tecnici Microsoft usano un servizio di accesso just-in-time che fornisce autorizzazioni limitate, entro un determinato periodo di tempo, con accesso limitato al servizio.  
Microsoft ha sempre richiesto il consenso dei clienti per l'accesso, ma Customer Lockbox offre ora la possibilità di esaminare e approvare o rifiutare richieste di questo tipo dal portale di Azure. I tecnici del supporto Microsoft non possono ottenere l'accesso finché il cliente non approva la richiesta.

## <a name="standardized-and-compliant-deployments"></a>Distribuzioni conformi e standardizzate

[Azure Blueprint](../governance/blueprints/overview.md) consente agli architetti del cloud e ai gruppi centrali del settore IT di definire un set ripetibile di risorse di Azure per implementare e rispettare gli standard, i modelli e i requisiti di un'organizzazione.  
In questo modo, i team DevOps possono creare e implementare rapidamente nuovi ambienti con la certezza di realizzare un'infrastruttura conforme ai requisiti dell'organizzazione. Blueprint offre un modo dichiarativo per orchestrare la distribuzione di più modelli di risorse e altri artefatti, ad esempio: 

- Assegnazioni di ruoli
- Assegnazioni di criteri
- Modelli di Gestione risorse di Azure
- Gruppi di risorse

## <a name="devops"></a>DevOps

Prima dello sviluppo dell'applicazione [Developer Operations (DevOps)](https://www.visualstudio.com/learn/what-is-devops/), i team erano responsabili della raccolta di requisiti aziendali per un programma software e per la scrittura del codice. Un team QA separato verificava quindi il programma in un ambiente di sviluppo isolato. Se i requisiti venivano soddisfatti, il team QA rilasciava il codice al team di Operations per la distribuzione. I team di distribuzione erano ulteriormente frammentati in gruppi come quelli di rete e di database. Ogni volta che un programma software veniva passato a un team indipendente, si aggiungevano colli di bottiglia.

DevOps consente ai team di distribuire soluzioni più sicure e di maggiore qualità in modo più rapido ed economico. I clienti si aspettano un'esperienza affidabile e dinamica quando usano software e servizi. I team devono scorrere rapidamente gli aggiornamenti software, misurare l'impatto degli aggiornamenti e rispondere rapidamente con nuove iterazioni di sviluppo per risolvere i problemi o offrire maggior valore.  

Piattaforme cloud come Microsoft Azure hanno rimosso i colli di bottiglia tradizionali e hanno contribuito a rendere agevole l'infrastruttura. Il software prevale in ogni azienda come il fattore di differenziazione chiave e il fattore di redditività dell'attività. Nessuna organizzazione, sviluppatore o lavoratore IT può o deve evitare lo spostamento di DevOps.

Gli esperti di DevOps avanzati adottano alcune delle procedure seguenti. Queste procedure [coinvolgono persone](https://www.visualstudio.com/learn/what-is-devops-culture/) nelle strategie del modulo in base agli scenari aziendali. Esistono strumenti che automatizzano le varie procedure.

- Le tecniche di [Gestione di progetto e di pianificazione agile](https://www.visualstudio.com/learn/what-is-agile/) sono usate per pianificare e isolare il lavoro in sprint, gestire la capacità del team e consentire ai team di adattarsi rapidamente alle mutevoli esigenze aziendali.
- [Controllo della versione, in genere con Git](https://www.visualstudio.com/learn/what-is-git/), consente ai team che si trovano ovunque nel mondo di condividere l'origine e di integrare con strumenti di sviluppo del software per automatizzare la pipeline di rilascio.
- L'[Integrazione continua](https://www.visualstudio.com/learn/what-is-continuous-integration/) si occupa dell'unione e dei test continui del codice, il che porta a individuare tempestivamente i difetti.  Altri vantaggi includono meno tempo impiegato per contrastare problemi di unione e feedback veloci per i team di sviluppo.
- La [distribuzione continua](https://www.visualstudio.com/learn/what-is-continuous-delivery/) di soluzioni software per gli ambienti di produzione e di test aiuta le organizzazioni a correggere rapidamente i bug e a rispondere alle mutevoli esigenze aziendali.
- Il [monitoraggio](https://www.visualstudio.com/learn/what-is-monitoring/) delle applicazioni in esecuzione, inclusi gli ambienti di produzione per l'integrità dell'applicazione, così come l'uso da parte dell'utente, aiutano le organizzazioni a formulare un'ipotesi e a convalidare o disapprovare rapidamente le strategie.  I dati completi acquisiti e archiviati in vari formati di registrazione.
- L'[Infrastruttura come Codice (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) è una procedura consigliata che consente l'automazione e la convalida della creazione e dell'eliminazione di reti e macchine virtuali per facilitare la distribuzione sicura e l'applicazione stabile delle piattaforme di hosting.
- L'architettura [Microservizi](https://www.visualstudio.com/learn/what-are-microservices/) viene sfruttata per isolare i casi di utilizzo commerciale in piccoli servizi riutilizzabili.  Questa architettura consente l'efficienza e scalabilità.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla soluzione Sicurezza e controllo, vedere gli articoli seguenti:

- [Sicurezza e conformità](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview)
