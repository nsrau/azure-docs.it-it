---
title: Panoramica sulla sicurezza operativa di Azure | Microsoft Docs
description: Questo articolo offre una panoramica sulla sicurezza operativa di Azure.
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: tomsh
ms.openlocfilehash: f2153e783adb955cf9055b09ba9aa2592f51e4b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-operational-security-overview"></a>Panoramica sulla sicurezza operativa di Azure
La sicurezza operativa di Azure include i servizi, i controlli e le funzionalità offerti ai clienti per proteggere i dati, le applicazioni e gli altri asset di Microsoft Azure. La [sicurezza operativa di Azure](https://docs.microsoft.com/azure/security/azure-operational-security) è un framework che incorpora le conoscenze acquisite tramite una varietà di funzionalità esclusive di Microsoft, tra cui Microsoft Security Development Lifecycle (SDL), il programma Microsoft Security Response Center e una profonda consapevolezza del panorama delle minacce per la sicurezza informatica.

Questa panoramica sulla sicurezza operativa di Azure è incentrata sulle aree seguenti:

- Azure Operations Management Suite
-   Centro sicurezza di Azure
-   Monitoraggio di Azure
-   Azure Network Watcher
-   Analisi archiviazione di Azure
-   Azure Active Directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
Il reparto delle operazioni IT è responsabile della gestione dell'infrastruttura del centro di dati, delle applicazioni e dei dati, compresa la stabilità e la sicurezza di questi sistemi. Tuttavia, ottenere informazioni di sicurezza in ambienti IT sempre più complessi spesso richiede alle organizzazioni di assemblare i dati da più sistemi di gestione e sicurezza.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) è la soluzione Microsoft per la gestione IT basata sul cloud che consente di gestire e proteggere l'infrastruttura locale e cloud.

OMS è una soluzione di gestione IT basata sul cloud con molte offerte, quali l'Automazione IT, Sicurezza e Conformità, Log Analytics e Backup e Ripristino. Di conseguenza, è uno strumento perfetto per gestire e proteggere l'infrastruttura IT, in locale e nel cloud.

Le funzionalità principali di OMS vengono fornite da un set di servizi eseguiti in Azure. Ogni servizio fornisce una funzione di gestione specifica ed è possibile combinare i servizi per realizzare scenari di gestione diversi. Lo strumento include:

-   Log Analytics
-   Automazione
-   Backup
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) fornisce servizi di monitoraggio per OMS raccogliendo i dati delle risorse gestite in un repository centrale. Questi dati possono includere eventi, dati sulle prestazioni o dati personalizzati forniti tramite l'API. Dopo essere stati raccolti, i dati sono disponibili per generare avvisi, per l'analisi e per l'esportazione. Questo metodo consente di consolidare i dati di diverse origini per poter combinare i dati dei servizi di Azure con l'ambiente locale esistente. Separa anche nettamente la raccolta dei dati dall'azione eseguita su tali dati in modo che tutte le azioni siano disponibili per tutti i tipi di dati.

### <a name="automation"></a>Automazione
[Automazione di Microsoft](https://docs.microsoft.com/azure/automation/automation-intro) Azure offre agli utenti la possibilità di automatizzare le attività manuali, a esecuzione prolungata, soggette a errori e ripetute di frequente comunemente eseguite negli ambienti cloud e aziendali. Consente di risparmiare tempo e aumenta l'affidabilità delle normali attività amministrative e le pianifica anche per essere eseguite automaticamente a intervalli regolari. È possibile automatizzare i processi utilizzando runbook o automatizzare la gestione della configurazione tramite Configurazione dello stato desiderato.

### <a name="backup"></a>Backup
[Backup di Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) è il servizio basato su Azure che consente di eseguire il backup, la protezione e il ripristino dei dati in Microsoft Cloud. Backup di Azure sostituisce la soluzione di backup locale o esterna esistente con una soluzione basata sul cloud affidabile, sicura e conveniente. Backup di Azure offre più componenti che vengono scaricati e distribuiti nel computer o server appropriato o nel cloud. Il componente o l'agente distribuito dipende da ciò che si intende proteggere. Tutti i componenti di Backup di Azure consentono di eseguire il backup dei dati in un insieme di credenziali di Servizi di ripristino, a prescindere che i dati da proteggere si trovino in locale o nel cloud. Vedere la [scheda dei componenti del Backup di Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) garantisce la continuità aziendale orchestrando la replica delle macchine virtuali e dei computer fisici locali in Azure o in un sito secondario. Se il sito primario non è disponibile, verrà eseguito il failover alla località secondaria in modo che gli utenti possano continuare a lavorare e il failback quando i sistemi riprendono a funzionare normalmente. funzionalità di rilevamento minacce intelligente ed efficace.

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) è una soluzione IDaaS (Identity as a Service) Microsoft completa che offre i vantaggi seguenti:

-   Abilitazione della gestione delle identità e degli accessi come servizio cloud.
-   Gestione centrale dell'accesso, accesso Single Sign-On (SSO) e creazione di report.
-   Supporto per la gestione integrata degli accessi per [migliaia di applicazioni](https://azure.microsoft.com/marketplace/active-directory/) nella raccolta di applicazioni, ad esempio Salesforce, Google Apps, Box, Concur e così via.

Azure AD include anche un insieme completo di [funzionalità per la gestione delle identità](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports), tra cui l'[autenticazione a più fattori](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), la [registrazione dei dispositivi]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview), la [gestione self-service delle password](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/), e dei [gruppi](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), la [gestione degli account con privilegi](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is), il [monitoraggio dell'utilizzo dell'applicazione](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health), il [controllo avanzato](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) e il [monitoraggio e avvisi relativi alla sicurezza](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).

Con Azure Active Directory tutte le applicazioni pubblicate per partner e clienti (aziende o utenti privati) hanno le stesse funzionalità di gestione delle identità e degli accessi. Ciò consente una riduzione notevole dei costi operativi.

## <a name="azure-security-center"></a>Centro sicurezza di Azure
Il [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started) consente di prevenire, rilevare e rispondere alle minacce con un livello di visibilità e controllo più elevato della sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni, facilita il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con un ampio ecosistema di soluzioni di sicurezza.

Il [Centro sicurezza](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) consente di proteggere i dati delle macchine virtuali di Azure visualizzando le impostazioni di sicurezza delle VM e monitorando le minacce. Il Centro sicurezza può monitorare nelle macchine virtuali quanto segue:

-   Impostazioni di sicurezza del sistema operativo con le regole di configurazione consigliate
-   Aggiornamenti della sicurezza del sistema e altri aggiornamenti di importanza critica eventualmente mancanti
-   Consigli per la protezione degli endpoint
-   Convalida della crittografia del disco
-   Attacchi basati sulla rete

Il Centro sicurezza di Azure usa il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure), con [ruoli predefiniti](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) che possono essere assegnati a utenti, gruppi e servizi in Azure.

Centro sicurezza consente di valutare la configurazione delle risorse per identificare problemi di sicurezza e vulnerabilità. In Centro sicurezza gli utenti possono visualizzare solo informazioni relative a una risorsa quando dispongono del ruolo di proprietario, collaboratore o lettore per la sottoscrizione o il gruppo di risorse cui tali risorse appartengono.

>[!Note]
>Per altre informazioni sui ruoli e sulle azioni consentite in Centro sicurezza, vedere [Permissions in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-permissions) (Autorizzazioni in Centro sicurezza di Azure).

Il Centro sicurezza usa Microsoft Monitoring Agent, lo stesso agente usato da Operations Management Suite e dal servizio Log Analytics. I dati raccolti dall'agente vengono archiviati in un'[area di lavoro](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) di Log Analytics esistente associata alla sottoscrizione di Azure o in una o più aree di lavoro nuove, a seconda della georilevazione della macchina virtuale.

## <a name="azure-monitor"></a>Monitoraggio di Azure
I problemi di prestazioni nell'app cloud possono avere un impatto sull'azienda. Con più componenti interconnessi e versioni frequenti, possono verificarsi in qualsiasi momento riduzioni delle prestazioni. Quando si sviluppa un'app, gli utenti in genere individuano problemi non trovati nei test. È consigliabile conoscere questi problemi immediatamente e avere gli strumenti per diagnosticarli e risolverli.

[Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) è lo strumento di base per il monitoraggio dei servizi eseguiti in Azure. Offre dati a livello di infrastruttura sulla velocità effettiva di un servizio e l'ambiente circostante. Se si gestiscono tutte le app in Azure e si deve decidere se aumentare o ridurre le risorse, Monitoraggio di Azure offre un utile punto di partenza.

Inoltre, è possibile usare i dati di monitoraggio per ottenere informazioni approfondite sull'applicazione, utili per migliorarne le prestazioni o la manutenibilità oppure per automatizzare azioni che altrimenti richiederebbero un intervento manuale. Sono inclusi:

-   Azure Activity Log
-   Log di diagnostica di Azure
-   Metriche
-   Diagnostica Azure

### <a name="azure-activity-log"></a>Azure Activity Log
Questo log offre informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione. Il [log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) era noto in precedenza come "log di controllo" o "log operativo", perché segnala eventi del piano di controllo per le sottoscrizioni.

### <a name="azure-diagnostic-logs"></a>Log di diagnostica di Azure
I [log di diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) sono generati da una risorsa che offre dati completi e frequenti sul funzionamento della risorsa stessa. Il contenuto di questi log varia in base al tipo di risorsa.

Ad esempio, i log del sistema eventi di Windows sono una categoria di log di diagnostica per le macchine virtuali, i BLOB e le tabelle, mentre i log della coda sono categorie di log di diagnostica per gli account di archiviazione.

I log di diagnostica sono diversi dai [log attività (precedentemente conosciuti come log di controllo o log operativi)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Il log attività fornisce informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione. I log di diagnostica forniscono informazioni approfondite sulle operazioni che la risorsa esegue automaticamente.

### <a name="metrics"></a>Metriche
Il monitoraggio di Azure consente di usare la telemetria per ottenere visibilità sulle prestazioni e sull'integrità dei carichi di lavoro in Azure. Il tipo di dati di telemetria Azure più importante è rappresentato dalle metriche (altrimenti dette contatori delle prestazioni) generate dalla maggior parte delle risorse di Azure. Il Monitoraggio di Azure offre svariati modi per configurare e usare queste [metriche](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) per il monitoraggio e la risoluzione dei problemi.

### <a name="azure-diagnostics"></a>Diagnostica Azure
È la funzionalità di Azure che consente la raccolta di dati di diagnostica in un'applicazione distribuita. È possibile usare l'estensione di diagnostica da numerose origini diverse. Sono attualmente supportati [ruoli Web e di lavoro del servizio cloud di Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [macchine virtuali di Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) che eseguono Microsoft Windows e [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="network-watcher"></a>Network Watcher
Per creare una rete end-to-end in Azure è possibile orchestrare e comporre varie risorse di rete individuali, quali rete virtuale, ExpressRoute, gateway applicazione, servizi di bilanciamento del carico e altro ancora. Il monitoraggio è disponibile in ognuna delle risorse di rete.

La rete end-to-end può avere interazioni tra le risorse e configurazioni complesse. Network Watcher consente il monitoraggio a livello di scenario per questi scenari complessi.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) semplificherà il monitoraggio e la diagnosi della rete di Azure. Gli strumenti di diagnostica e di visualizzazione disponibili con Network Watcher consentono di eseguire delle acquisizioni del pacchetto remoto in una macchina virtuale di Azure, di ottenere informazioni approfondite del traffico di rete tramite i log di flusso e di diagnosticare Gateway VPN e connessioni.

Di seguito sono elencate le funzionalità attualmente disponibili in Network Watcher.

- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview): offre una visualizzazione a livello di rete che mostra le varie interconnessioni e le associazioni tra le risorse di rete in un gruppo di risorse.
-   [Acquisizione pacchetti variabile](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview): acquisisce i dati dei pacchetti all'interno e all'esterno di una macchina virtuale. La versatilità è data dalle opzioni di filtro avanzato e dai controlli ottimizzati, come la possibilità di impostare l'ora e il limite di dimensioni. È possibile memorizzare i dati dei pacchetti in un archivio BLOB o nel disco locale in formato CAP.
-   [Verifica dei flussi IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview): controlla se un pacchetto viene accettato o rifiutato in base ai parametri di pacchetto costituiti da informazioni a 5 tuple sul flusso, ovvero l'indirizzo IP di destinazione, l'indirizzo IP di origine, la porta di destinazione, la porta di origine e il protocollo. Se il pacchetto viene rifiutato da un gruppo di sicurezza, vengono restituiti la regola e il gruppo che hanno rifiutato il pacchetto.
-   [Hop successivo](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview): determina l'hop successivo per i pacchetti indirizzati nell'infrastruttura di rete di Azure, permettendo così di diagnosticare eventuali route definite dall'utente non configurate in modo corretto.
-   [Visualizzazione dei gruppi di sicurezza](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview): ottiene le regole di sicurezza valide e applicate in una macchina virtuale.
-   [Registrazione dei flussi del gruppo di sicurezza di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview): la registrazione dei flussi del gruppo di sicurezza di rete permette di acquisire i log relativi al traffico consentito o negato dalle regole di sicurezza nel gruppo. Il flusso è definito da informazioni a 5 tuple, ovvero l'indirizzo IP di destinazione, l'indirizzo IP di origine, la porta di destinazione, la porta di origine e il protocollo.
-   [Risoluzione dei problemi di connessione e gateway di rete virtuale](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest): permette di risolvere i problemi relativi al gateway di rete virtuale e alle connessioni.
-   [Limite sottoscrizioni di rete](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): permette di visualizzare l'uso delle risorse di rete rispetto ai limiti.
-   [Configurazione del log di diagnostica](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): permette di abilitare o disabilitare i log di diagnostica per le risorse di rete in un gruppo di risorse da un unico riquadro.

Per altre informazioni sulla configurazione del controllo di rete, vedere [Creare un'istanza di Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="developer-operations-devops"></a>Developer Operations (DevOps)
Prima dello sviluppo dell'applicazione DevOps, i team erano responsabili della raccolta di requisiti commerciali per un programma software e per la scrittura del codice. Un team QA separato verifica quindi il programma in un ambiente di sviluppo isolato, se sono stati soddisfatti i requisiti e rilascia il codice per le operazioni da distribuire. I team di distribuzione vengono ulteriormente frammentati in gruppi silo come quelli di rete e di database. Ogni volta che un programma software viene "gettato sul muro" verso un team indipendente, aggiunge colli di bottiglia.

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) consente ai team di fornire soluzioni più sicure e di alta qualità più rapide ed economiche. I clienti si aspettano un'esperienza affidabile e dinamica quando usano software e servizi.  I team devono scorrere rapidamente gli aggiornamenti software, misurare l'impatto degli aggiornamenti e rispondere rapidamente con nuove iterazioni di sviluppo per risolvere i problemi o fornire maggior valore.  Piattaforme cloud come Microsoft Azure hanno rimosso i colli di bottiglia tradizionali e hanno contribuito a rendere agevole l'infrastruttura. Il software prevale in ogni azienda come il fattore di differenziazione chiave e il fattore di redditività dell'attività. Nessuna organizzazione, sviluppatore o lavoratore IT può o deve evitare lo spostamento di DevOps.

Gli esperti di DevOps avanzati adottano alcune delle procedure seguenti. Queste procedure [coinvolgono persone](https://www.visualstudio.com/learn/what-is-devops-culture/) nelle strategie del modulo in base agli scenari aziendali.  Gli strumenti consentono di automatizzare le varie procedure:

-   Le tecniche di [Gestione di progetto e di pianificazione agile](https://www.visualstudio.com/learn/what-is-agile/) sono usate per pianificare e isolare il lavoro in sprint, gestire la capacità del team e consentire ai team di adattarsi rapidamente alle mutevoli esigenze aziendali.
-   [Controllo della versione, in genere con Git](https://www.visualstudio.com/learn/what-is-git/), consente ai team che si trovano ovunque nel mondo di condividere l'origine e di integrare con strumenti di sviluppo del software per automatizzare la pipeline di rilascio.
-   L'[Integrazione continua](https://www.visualstudio.com/learn/what-is-continuous-integration/) indirizza l'unione in corso e il test di codice, il che porta a individuare tempestivamente i difetti.  Altri vantaggi includono meno tempo impiegato per contrastare problemi di unione e feedback veloci per i team di sviluppo.
-   Il [Recapito continuo](https://www.visualstudio.com/learn/what-is-continuous-delivery/) di soluzioni software per gli ambienti di produzione e di test aiuta le organizzazioni a correggere rapidamente i bug e a rispondere alle mutevoli esigenze aziendali.
-   Il [Monitoraggio](https://www.visualstudio.com/learn/what-is-monitoring/) delle applicazioni in esecuzione, inclusi gli ambienti di produzione per l'integrità dell'applicazione, così come l'uso da parte dell'utente, aiutano le organizzazioni a formulare un'ipotesi e a convalidare rapidamente o disapprovare le strategie.  I dati completi acquisiti e archiviati in vari formati di registrazione.
-   L'[Infrastruttura come Codice (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) è una procedura consigliata che consente l'automazione e la convalida della creazione e dell'eliminazione di reti e macchine virtuali per facilitare la distribuzione sicura e l'applicazione stabile delle piattaforme di hosting.
-   L'architettura [Microservizi](https://www.visualstudio.com/learn/what-are-microservices/) viene sfruttata per isolare i casi di utilizzo commerciale in piccoli servizi riutilizzabili.  Questa architettura consente l'efficienza e scalabilità.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla soluzione Sicurezza e controllo di OMS, vedere gli articoli seguenti:

- [Operations Management Suite | Sicurezza e Conformità](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Monitoraggio e gestione degli avvisi di sicurezza nella soluzione Operations Management Suite per la sicurezza e il controllo](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-responding-alerts).
- [Monitoraggio delle risorse nella soluzione Operations Management Suite per la sicurezza e il controllo](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-monitoring-resources).
