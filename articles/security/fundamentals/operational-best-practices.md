---
title: Procedure consigliate per la sicurezza per le risorse di AzureSecurity best practices for your Azure assets
titleSuffix: Azure security
description: Questo articolo fornisce un set di procedure consigliate operative per la protezione di dati, applicazioni e altri asset in Azure.This article provides a set of operational best practices for protecting your data, applications, and other assets in Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 5724a9e4308f05a82df84ae6a7d5602747f5a140
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757364"
---
# <a name="azure-operational-security-best-practices"></a>Procedure consigliate per la sicurezza operativa di Azure
Questo articolo fornisce un set di procedure consigliate operative per la protezione di dati, applicazioni e altri asset in Azure.This article provides a set of operational best practices for protecting your data, applications, and other assets in Azure.

Le procedure consigliate si basano su opinioni concordanti e funzionino con le caratteristiche e le capacità correnti della piattaforma Azure. Le opinioni e le tecnologie cambiano nel tempo e questo articolo viene aggiornato regolarmente per riflettere tali modifiche.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definire e distribuire procedure di sicurezza operative complesseDefine and deploy strong operational security practices
La sicurezza operativa di Azure include i servizi, i controlli e le funzionalità offerti agli utenti per proteggere i dati, le applicazioni e gli altri asset di Azure. La sicurezza operativa di Azure si basa su un framework che incorpora le conoscenze acquisite tramite funzionalità univoche di Microsoft, tra cui Microsoft [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), il programma [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) e una profonda consapevolezza del panorama delle minacce per la sicurezza informatica.

## <a name="manage-and-monitor-user-passwords"></a>Gestire e monitorare le password degli utenti
Nella tabella seguente sono elencate alcune procedure consigliate relative alla gestione delle password utente:

**Best practice**: Assicurarsi di avere il livello corretto di protezione con password nel cloud.   
**Dettaglio**: seguire le indicazioni fornite in [Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance/), con ambito per gli utenti delle piattaforme di identità Microsoft (Azure Active Directory, Active Directory e account Microsoft).

**Procedura consigliata**: Monitorare le azioni sospette relative agli account utente.   
**Dettaglio:** monitorare [gli utenti a rischio](/azure/active-directory/reports-monitoring/concept-user-at-risk) e gli [accessi rischiosi](../../active-directory/reports-monitoring/concept-risk-events.md) usando i report di sicurezza di Azure AD.

**Procedura consigliata**: Rilevare e correggere automaticamente le password ad alto rischio.   
**Dettaglio:** Azure AD Identity Protection è una funzionalità dell'edizione Azure AD Premium P2 che consente di:Detail: [Azure AD Identity Protection](/azure/active-directory/identity-protection/overview) is a feature of the Azure AD Premium P2 edition that enables you to:

- Rilevare potenziali vulnerabilità che influiscono sulle identità dell'organizzazione
- Configurare risposte automatizzate alle azioni sospette rilevate correlate alle identità dell'organizzazione
- Esaminare gli incidenti sospetti e intraprendere le azioni appropriate per risolverli

## <a name="receive-incident-notifications-from-microsoft"></a>Ricevere notifiche di eventi imprevisti da MicrosoftReceive incident notifications from Microsoft
Assicurarsi che il team delle operazioni di sicurezza riceva le notifiche degli eventi imprevisti di Azure da Microsoft.Be sure your security operations team receives Azure incident notifications from Microsoft. Una notifica degli incidenti consente al team di sicurezza di sapere che le risorse di Azure sono state compromesse in modo che possano rispondere rapidamente e correggere potenziali rischi per la sicurezza.

Nel portale di registrazione di Azure è possibile assicurarsi che le informazioni di contatto dell'amministratore includano i dettagli che notificano le operazioni di sicurezza. Le informazioni di contatto sono un indirizzo di posta elettronica e un numero di telefono.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizzare le sottoscrizioni di Azure in gruppi di gestioneOrganize Azure subscriptions into management groups
Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. [I gruppi](/azure/governance/management-groups/create) di gestione di Azure forniscono un livello di ambito superiore alle sottoscrizioni. Le sottoscrizioni vengono organizzate in contenitori denominati gruppi di gestione e applicano le condizioni di governance ai gruppi di gestione. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni applicate al gruppo di gestione.

È possibile creare una struttura flessibile di gruppi di gestione e sottoscrizioni in una directory. A ogni directory viene assegnato un singolo gruppo di gestione di primo livello denominato gruppo di gestione radice. Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice consente l'applicazione di criteri globali e assegnazioni RBAC a livello di directory.

Di seguito sono riportate alcune procedure consigliate per l'utilizzo dei gruppi di gestione:Here are some best practices for using management groups:

**Procedura consigliata:** assicurarsi che le nuove sottoscrizioni applichino elementi di governance come criteri e autorizzazioni man mano che vengono aggiunte.   
**Dettaglio:** usare il gruppo di gestione radice per assegnare elementi di sicurezza a livello aziendale che si applicano a tutte le risorse di Azure.Detail : Use the root management group to assign enterprise-wide security elements that apply to all Azure assets. I criteri e le autorizzazioni sono esempi di elementi.

**Procedura consigliata:** allineare i livelli superiori dei gruppi di gestione con la strategia di segmentazione per fornire un punto per il controllo e la coerenza dei criteri all'interno di ogni segmento.   
**Dettaglio**: Creare un singolo gruppo di gestione per ogni segmento nel gruppo di gestione radice. Non creare altri gruppi di gestione nella radice.

**Procedura consigliata:** limitare la profondità del gruppo di gestione per evitare confusione che ostacola sia le operazioni che la sicurezza.   
**Dettaglio**: Limitare la gerarchia a tre livelli, inclusa la radice.

**Procedura consigliata:** selezionare con attenzione gli elementi da applicare all'intera azienda con il gruppo di gestione radice.   
**Dettaglio**: Assicurarsi che gli elementi del gruppo di gestione radice abbiano una chiara necessità di essere applicati a ogni risorsa e che siano a basso impatto.

I buoni candidati includono:

- Requisiti normativi che hanno un chiaro impatto aziendale (ad esempio, restrizioni relative alla sovranità dei dati)
- Requisiti con effetti negativi potenziali quasi zero sulle operazioni, ad esempio criteri con effetto di controllo o assegnazioni di autorizzazioni RBAC che sono stati attentamente esaminatiRequirements with near-zero potential negative affect on operations, like policy with audit effect or RBAC permission assignments that have been carefullyed

**Procedura consigliata:** pianificare e testare attentamente tutte le modifiche a livello aziendale nel gruppo di gestione radice prima di applicarle (criteri, modello RBAC e così via).   
**Dettaglio:** le modifiche nel gruppo di gestione radice possono influire su ogni risorsa in Azure.Detail: Changes in the root management group can affect every resource on Azure. Sebbene forniscano un modo efficace per garantire la coerenza all'interno dell'azienda, gli errori o l'utilizzo non corretto possono influire negativamente sulle operazioni di produzione. Testare tutte le modifiche al gruppo di gestione radice in un laboratorio di testing o in un progetto pilota di produzione.

## <a name="streamline-environment-creation-with-blueprints"></a>Semplifica la creazione dell'ambiente con i blueprint
Il servizio [Azure Blueprints](/azure/governance/blueprints/overview) consente agli architetti cloud e ai gruppi IT principali di definire un set ripetibile di risorse di Azure che implementa e rispetta gli standard, i modelli e i requisiti di un'organizzazione. I blueprint di Azure consentono ai team di sviluppo di creare e gestire rapidamente nuovi ambienti con un set di componenti predefiniti e la certezza di creare tali ambienti all'interno della conformità dell'organizzazione.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorare i servizi di archiviazione per rilevare cambiamenti inattesi nel comportamento
La diagnosi e la risoluzione dei problemi in un'applicazione distribuita ospitata in un ambiente cloud possono essere più complesse di quanto lo siano in ambienti tradizionali. Le applicazioni possono essere distribuite in un'infrastruttura PaaS o IaaS, in locale, su un dispositivo mobile o in una combinazione di questi tipi di ambienti. Il traffico di rete dell'applicazione può passare su reti pubbliche e private e l'applicazione può usare più tecnologie di archiviazione.

È consigliabile monitorare continuamente i servizi di archiviazione usati dall'applicazione per individuare eventuali cambiamenti inattesi nel comportamento, ad esempio tempi di risposta più lunghi. Usare la registrazione per raccogliere dati più dettagliati e analizzare un problema in modo approfondito. Le informazioni di diagnostica che si ottengono con il monitoraggio e la registrazione aiutano a determinare la causa radice del problema incontrato dall'applicazione. È possibile quindi identificare il problema e determinare le misure appropriate per correggerlo.

Il servizio [Analisi archiviazione di Azure](../../storage/common/storage-analytics.md) esegue la registrazione e fornisce i dati di metrica per un account di archiviazione di Azure. È consigliabile usare questi dati per tenere traccia delle richieste, analizzare le tendenze di utilizzo e diagnosticare i problemi relativi al proprio account di archiviazione.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenire le minacce, rilevarle e rispondere
[Il Centro sicurezza](../../security-center/security-center-intro.md) di Azure consente di prevenire, rilevare e rispondere alle minacce fornendo una maggiore visibilità sulla sicurezza delle risorse di Azure.Azure Security Center helps you prevent, detect, and respond to threats by providing increased visibilità in the security over your Azure resources. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, aiuta il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con varie soluzioni di sicurezza.

Il livello Gratuito del Centro sicurezza offre sicurezza limitata solo per le risorse di Azure.The Free tier of Security Center offers limited security for only your Azure resources. Il livello Standard estende queste funzionalità in locale e in altri cloud. Security Center Standard consente di individuare e correggere le vulnerabilità della sicurezza, applicare l'accesso e i controlli delle applicazioni per bloccare attività dannose, rilevare le minacce utilizzando analisi e intelligence e rispondere rapidamente quando sono sotto attacco. È possibile provare gratuitamente il livello Standard del Centro sicurezza per i primi 60 giorni. È [consigliabile aggiornare la sottoscrizione](../../security-center/security-center-get-started.md)di Azure a Security Center Standard .

Usare il Centro sicurezza per ottenere una visualizzazione centrale dello stato di sicurezza di tutte le risorse di Azure.Use Security Center to get a central view of the security state of all Your Azure resources. Il Centro sicurezza consente di verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente e di identificare rapidamente le risorse che richiedono attenzione.

Security Center si integra anche con [Microsoft Defender Advanced Threat Protection (ATP),](../../security-center/security-center-wdatp.md)che fornisce funzionalità complete di rilevamento e risposta degli endpoint (EDR). Con l'integrazione di Microsoft Defender ATP, è possibile individuare le anomalie. È inoltre possibile rilevare e rispondere agli attacchi avanzati sugli endpoint server monitorati dal Centro sicurezza.

Quasi tutte le organizzazioni aziendali dispongono di un sistema SIEM (Security Information and Event Management) che consente di identificare le minacce emergenti consolidando le informazioni di registro provenienti da diversi dispositivi di raccolta del segnale. I log vengono quindi analizzati da un sistema di analisi dei dati per identificare ciò che è "interessante" dal rumore che è inevitabile in tutte le soluzioni di raccolta e analisi dei log.

[Azure Sentinel](/azure/sentinel/overview) è una soluzione SOAR (Automate response) di orchestrazione e informazioni di sicurezza (SIEM) scalabile, nativa per il cloud, delle informazioni di sicurezza e degli eventi. Azure Sentinel offre analisi intelligenti della sicurezza e informazioni sulle minacce tramite il rilevamento degli avvisi, la visibilità delle minacce, la caccia proattiva e la risposta alle minacce automatizzate.

Di seguito sono riportate alcune procedure consigliate per la prevenzione, il rilevamento e la risposta alle minacce:

**Best practice**: Aumentare la velocità e la scalabilità della soluzione SIEM utilizzando un SIEM basato su cloud.   
**Dettaglio**: esaminare le caratteristiche e le funzionalità di [Azure Sentinel](/azure/sentinel/overview) e confrontarle con le funzionalità attualmente in uso in locale. Valutare la possibilità di adottare Azure Sentinel se soddisfa i requisiti SIEM dell'organizzazione.

**Best practice**: Individuare le vulnerabilità di sicurezza più gravi in modo da poter assegnare priorità all'analisi.   
**Dettaglio:** esaminare il [punteggio sicuro](../../security-center/secure-score-security-controls.md) di Azure per visualizzare i suggerimenti risultanti dai criteri e dalle iniziative di Azure incorporate nel Centro sicurezza di Azure.Detail: Review your Azure secure score to see the recommendations resulting from the Azure policies and initiatives built into Azure Security Center. Questi consigli consentono di affrontare i principali rischi come gli aggiornamenti della sicurezza, la protezione degli endpoint, la crittografia, le configurazioni di sicurezza, WAF mancanti, le macchine virtuali connesse a Internet e molte altre.

Il punteggio sicuro, basato sui controlli ciS (Center for Internet Security), consente di valutare la sicurezza di Azure dell'organizzazione con origini esterne. La convalida esterna consente di convalidare e arricchire la strategia di sicurezza del team.

**Procedure consigliate**: monitorare il livello di sicurezza di macchine, reti, servizi di archiviazione e dati e applicazioni per individuare e assegnare priorità a potenziali problemi di sicurezza.  
**Dettaglio**: Seguire le [raccomandazioni](../../security-center/security-center-recommendations.md) di sicurezza in Centro sicurezza a partire, con gli elementi con la priorità più alta.

**Procedura consigliata:** integrare gli avvisi del Centro sicurezza nella soluzione SIEM (Security Information and Event Management).   
**Dettaglio**: La maggior parte delle organizzazioni con un SIEM lo usa come centro di compensazione per gli avvisi di sicurezza che richiedono una risposta di un analista. Gli eventi elaborati prodotti dal Centro sicurezza vengono pubblicati nel log attività di Azure, ovvero uno dei log disponibili tramite Monitoraggio di Azure.Processed events produced by Security Center are published to the Azure Activity Log, one of the logs available through Azure Monitor. Monitoraggio di Azure offre una pipeline consolidata per eseguire il routing dei dati di monitoraggio in uno strumento SIEM. Per istruzioni, vedere [Esportare avvisi e suggerimenti](../../security-center/continuous-export.md#configuring-siem-integration-via-azure-event-hubs) per la sicurezza. Se si usa Azure Sentinel, vedere [Connect Azure Security Center](../../sentinel/connect-azure-security-center.md).

**Procedura consigliata:** integrare i log di Azure con il SIEM.   
**Dettaglio**: Usare [Monitoraggio di Azure per raccogliere ed esportare dati](/azure/azure-monitor/overview#integrate-and-export-data). Questa procedura è fondamentale per abilitare l'analisi degli incidenti di sicurezza e la conservazione dei log online è limitata. Se si usa Azure Sentinel, vedere [Connettere origini dati.](../../sentinel/connect-data-sources.md)

**Best practice**: Accelera i processi di indagine e caccia e riduci i falsi positivi integrando le funzionalità EDR (Endpoint Detection and Response) nell'indagine sugli attacchi.   
**Dettaglio:** [abilitare l'integrazione](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) di Microsoft Defender ATP tramite i criteri di sicurezza del Centro sicurezza. Valutare la possibilità di usare Azure Sentinel per la caccia alle minacce e la risposta agli incidenti.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitoraggio della rete basato su scenari end-to-end
Per creare una rete end-to-end in Azure, i clienti combinano varie risorse di rete, ad esempio una rete virtuale, ExpressRoute, un gateway applicazione e servizi di bilanciamento del carico. Il monitoraggio è disponibile in ognuna delle risorse di rete.

[Network Watcher di Azure](../../network-watcher/network-watcher-monitoring-overview.md) è un servizio a livello di area, dotato di strumenti di diagnostica e di visualizzazione che consentono di monitorare e diagnosticare le condizioni a livello di scenario di rete in Azure, verso e da Azure.

Di seguito vengono descritte le procedure consigliate per il monitoraggio della rete e gli strumenti disponibili.

**Procedura consigliata**: automatizzare il monitoraggio della rete in remoto con l'acquisizione pacchetti.  
**Dettaglio**: monitorare e diagnosticare i problemi di rete senza accedere alle macchine virtuali usando Network Watcher. Attivare l'[acquisizione dei pacchetti](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) impostando gli avvisi e ottenere l'accesso alle informazioni sulle prestazioni in tempo reale a livello del pacchetto. Quando viene rilevato un problema, è possibile esaminarlo in dettaglio per una diagnosi migliore.

**Procedura consigliata**: acquisire informazioni dettagliate sul traffico di rete usando i log dei flussi.  
**Dettaglio**: ottenere informazioni approfondite sui modelli di traffico di rete con i [log dei flussi del gruppo di sicurezza di rete](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). Le informazioni contenute nei log dei flussi aiutano a raccogliere i dati per la conformità, il controllo e il monitoraggio del profilo di sicurezza della rete.

**Procedura consigliata**: diagnosticare i problemi di connettività della VPN.  
**Dettaglio**: usare Network Watcher per [diagnosticare i problemi di connessione e di Gateway VPN più comuni](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Non è solo possibile identificare il problema ma si può anche usare i log dettagliati per altre indagini.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Distribuzione sicura tramite strumenti DevOps collaudati
Usare le seguenti procedure consigliate di DevOps per assicurarsi che i team e l'azienda siano produttivi ed efficienti.

**Procedura consigliata**: automatizzare la creazione e la distribuzione di servizi.  
**Dettaglio**: l'[infrastruttura come codice](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) rappresenta un set di tecniche e procedure che aiutano i professionisti IT a evitare il carico di lavoro associato alla creazione e alla gestione quotidiana dell'infrastruttura modulare. Lo scopo è consentire ai professionisti IT di creare e gestire un ambiente server moderno in modo simile a quello usato dagli sviluppatori di software per creare e gestire il codice delle applicazioni.

È possibile usare [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) per effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Lo stesso modello viene usato per distribuire ripetutamente l'applicazione in ogni fase del ciclo di vita dell'applicazione.

**Procedura consigliata**: compilare e distribuire automaticamente app Web o servizi cloud di Azure.  
**Dettaglio:** è possibile configurare i progetti DevOps di Azure per [la compilazione e](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) la distribuzione automatica nelle app Web o nei servizi cloud di Azure.Detail : You can configure your Azure DevOps Projects to automatically build and deploy to Azure web apps or cloud services. Azure DevOps distribuisce automaticamente i file binari dopo aver eseguito una compilazione in Azure dopo ogni archiviazione del codice. Il processo di compilazione del pacchetto equivale al comando Pacchetto di Visual Studio, mentre i passaggi per la pubblicazione equivalgono al comando Pubblica di Visual Studio.

**Procedura consigliata**: automatizzare la gestione del rilascio.  
**Dettaglio**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) è una soluzione per automatizzare la distribuzione in più fasi e la gestione del processo di rilascio. È possibile creare pipeline gestite di distribuzione continua per rilasciare versioni in modo rapido, semplice e frequente. Con Azure Pipelines è possibile automatizzare il processo di rilascio e definire flussi di lavoro predefiniti per l'approvazione. Sono supportate la distribuzione locale e nel cloud, l'estensione e la personalizzazione in base alle specifiche esigenze.

**Procedura consigliata**: verificare le prestazioni dell'app prima di implementarla o di distribuirne gli aggiornamenti nell'ambiente di produzione.  
**Dettaglio**: Eseguire test di [carico](/azure/devops/test/load-test/overview#alternatives) basati su cloud per:

- Individuare problemi di prestazioni nell'app.
- Migliorare la qualità della distribuzione.
- Assicurarsi che l'app sia sempre disponibile.
- Assicurarsi che l'app possa gestire il traffico per la prossima campagna di lancio o di marketing.

[Apache JMeter](https://jmeter.apache.org/) è uno strumento open source gratuito e popolare con un forte sostegno della comunità.

**Procedura consigliata**: monitorare le prestazioni dell'applicazione.  
**Dettaglio**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. È possibile usare Application Insights per monitorare l'applicazione Web mentre è in esecuzione. Il servizio rileva automaticamente le anomalie nelle prestazioni e include strumenti di analisi che consentono di diagnosticare i problemi e di conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

## <a name="mitigate-and-protect-against-ddos"></a>Attenuazione e protezione da attacchi Distributed Denial of Service (DDoS)
Il Distributed Denial of Service (DDoS) è un tipo di attacco che tenta di esaurire le risorse dell'applicazione. L'obiettivo è compromettere la disponibilità e la capacità dell'applicazione di gestire richieste legittime. Gli attacchi stanno diventando più sofisticati con dimensioni e impatto maggiori. Possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.

La progettazione e la creazione per la resilienza agli attacchi Distributed Denial of Service (DDoS) richiedono pianificazione e progettazione per un'ampia gamma di modalità di errore. Di seguito vengono descritte le procedure consigliate per la creazione di servizi resilienti a DDoS in Azure.

**Procedura consigliata**: assicurarsi che la sicurezza sia un aspetto prioritario durante l'intero ciclo di vita di un'applicazione, dalla progettazione e l'implementazione alla distribuzione e al funzionamento. Le applicazioni possono contenere bug che consentono a un volume relativamente basso di richieste di usare una quantità elevata di risorse, provocando un'interruzione del servizio.  
**Dettaglio**: per proteggere un servizio in esecuzione in Microsoft Azure, è consigliabile avere una buona conoscenza dell'architettura delle applicazioni e concentrarsi sui [cinque punti chiave della qualità del software](https://docs.microsoft.com/azure/architecture/guide/pillars). I clienti devono conoscere i volumi di traffico tipici, il modello di connettività tra l'applicazione e le altre applicazioni e gli endpoint di servizio esposti a Internet pubblico.

Garantire che un'applicazione sia abbastanza resiliente da riuscire a gestire un attacco Denial of Service destinato all'applicazione stessa è di fondamentale importanza. Sicurezza e privacy sono integrate direttamente nella piattaforma di Azure, a partire dal processo [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl). SDL si rivolge alla sicurezza in ogni fase di sviluppo e assicura che Azure sia continuamente aggiornato per renderlo ancora più sicuro.

**Procedura consigliata**: progettare le applicazioni con [scalabilità orizzontale](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) per soddisfare la richiesta di un carico amplificato, in particolare in caso di attacco DDoS. Se l'applicazione dipende da una singola istanza di un servizio, crea un singolo punto di errore. Il provisioning di più istanze rende il sistema più resiliente e scalabile.  
**Dettaglio**: per [Servizio app di Azure](/azure/app-service/app-service-value-prop-what-is) selezionare un [piano di servizio app](../../app-service/overview-hosting-plans.md) che offra più istanze.

Per Servizi cloud di Azure, configurare ognuno dei ruoli in modo da usare [più istanze](../../cloud-services/cloud-services-choose-me.md).

Per [Macchine virtuali di Microsoft Azure](/azure/virtual-machines/windows/overview), verificare che l'architettura di VM includa più macchine virtuali e che ogni macchina virtuale sia inclusa in un [set di disponibilità](/azure/virtual-machines/virtual-machines-windows-manage-availability). Si consiglia di usare set di scalabilità di macchine virtuali per le funzionalità di scalabilità automatica.

**Procedura consigliata**: la disposizione delle difese su più livelli in un'applicazione riduce le probabilità di riuscita degli attacchi. Implementare progettazioni sicure per le applicazioni tramite le funzionalità integrate della piattaforma di Azure.  
**Dettaglio**: il rischio di attacco aumenta con le dimensioni (superficie di attacco) dell'applicazione. È possibile ridurre la superficie usando l'elenco degli elementi consentiti per chiudere lo spazio indirizzi IP esposto e le porte in ascolto non necessarie sui servizi di bilanciamento del carico ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) e [gateway applicazione di Azure](/azure/application-gateway/application-gateway-create-probe-portal)).

I [gruppi di sicurezza di rete](../../virtual-network/security-overview.md) rappresentano un altro modo per ridurre la superficie di attacco. È possibile usare [tag di servizio](../../virtual-network/security-overview.md#service-tags) e [gruppi di sicurezza dell'applicazione](../../virtual-network/security-overview.md#application-security-groups) per ridurre la complessità per la creazione della regola di sicurezza e configurare la sicurezza di rete come estensione naturale della struttura di un'applicazione.

Distribuire i servizi di Azure in una [rete virtuale](../../virtual-network/virtual-networks-overview.md), laddove possibile. Ciò consente alle risorse del servizio di comunicare attraverso indirizzi IP privati. Il traffico del servizio di Azure da una rete virtuale usa indirizzi IP pubblici come indirizzi IP di origine per impostazione predefinita.

Tramite gli [endpoint di servizio](../../virtual-network/virtual-network-service-endpoints-overview.md) il traffico del servizio passa all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale.

Le risorse locali dei clienti che vengono spesso attaccate insieme alle risorse in Azure. Se si connette un ambiente locale ad Azure, ridurre al minimo l'esposizione delle risorse locali alla rete Internet pubblica.

Azure prevede due [offerte di servizio](../../virtual-network/ddos-protection-overview.md) contro gli attacchi DDoS per la protezione della rete:

- La protezione di base è integrata in Azure per impostazione predefinita senza costi aggiuntivi. La scala e la capacità complete della rete globalmente distribuita di Azure forniscono una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio costante del traffico e la mitigazione in tempo reale. La protezione di base non richiede alcuna modifica della configurazione utente o dell'applicazione e aiuta a proteggere tutti i servizi di Azure, inclusi i servizi PaaS come DNS di Azure.
- La protezione standard offre funzionalità avanzate di attenuazione degli attacchi DDoS contro gli attacchi alla rete e viene ottimizzata automaticamente per proteggere le specifiche risorse di Azure. La protezione è semplice da abilitare durante la creazione di reti virtuali. Può essere abilitata anche dopo la creazione e non richiede alcuna modifica delle applicazioni o delle risorse.

## <a name="enable-azure-policy"></a>Abilitare i criteri di AzureEnable Azure Policy
[Criteri](/azure/governance/policy/overview) di Azure è un servizio in Azure usato per creare, assegnare e gestire i criteri. Questi criteri applicano regole ed effetti sulle risorse, in modo che tali risorse rimangano conformi agli standard aziendali e ai contratti di servizio. Criteri di Azure soddisfa questa esigenza valutando le risorse per la mancata conformità con i criteri assegnati.

Abilitare Criteri di Azure per monitorare e applicare i criteri scritti dell'organizzazione. Ciò garantirà la conformità con i requisiti di sicurezza aziendali o normativi gestendo centralmente i criteri di sicurezza nei carichi di lavoro cloud ibrido. Informazioni su come [creare e gestire i criteri per applicare la conformità.](../../governance/policy/tutorials/create-and-manage.md) Vedere [Struttura di definizione](../../governance/policy/concepts/definition-structure.md) dei criteri di Azure per una panoramica degli elementi di un criterio.

Ecco alcune procedure consigliate di sicurezza da seguire dopo l'adozione dei criteri di Azure:Here are some security best practices to follow after you adopt Azure Policy:

**Best practice**: La politica supporta diversi tipi di effetti. È possibile leggere informazioni su di essi nella struttura di [definizione dei criteri](../../governance/policy/concepts/definition-structure.md#policy-rule)di Azure. Le operazioni aziendali possono essere influenzate negativamente dall'effetto **di negazione** e dall'effetto **correttivo,** quindi inizia con l'effetto di **controllo** per limitare il rischio di impatto negativo da parte dei criteri.   
**Dettaglio**: [Avviare le distribuzioni dei criteri in modalità di controllo](../../governance/policy/concepts/definition-structure.md#policy-rule) e quindi procedere in un secondo momento per **negare** o **correggere**. Verificare ed esaminare i risultati dell'effetto di controllo prima di passare a **nega** o **correggere**.

Per ulteriori informazioni, vedere [Creare e gestire criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md).

**Best practice**: Identificare i ruoli responsabili del monitoraggio delle violazioni dei criteri e garantire che venga intrapresa rapidamente l'azione di correzione corretta.   
**Dettaglio**: Disporre della conformità del monitoraggio ruolo assegnato tramite il [portale di Azure](../../governance/policy/how-to/get-compliance-data.md#portal) o tramite la riga di [comando](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Procedura consigliata:** Criteri di Azure è una rappresentazione tecnica dei criteri scritti di un'organizzazione. Eseguire il mapping di tutti i criteri di Azure ai criteri dell'organizzazione per ridurre la confusione e aumentare la coerenza.   
**Dettaglio:** mapping dei documenti nella documentazione dell'organizzazione o nei criteri di Azure stessi aggiungendo un riferimento ai criteri dell'organizzazione nella descrizione dei criteri di Azure o nella descrizione [dell'iniziativa](../../governance/policy/concepts/definition-structure.md#initiatives) dei criteri di Azure.Detail: Document mapping in your organization's documentation or in the Azure policy itself by adding a reference to the organizational policy in the Azure [policy description](../../governance/policy/concepts/definition-structure.md#display-name-and-description) or the Azure policy initiative description.

## <a name="monitor-azure-ad-risk-reports"></a>Monitorare i rapporti sui rischi di Azure ADMonitor Azure AD risk reports
La maggior parte delle violazioni della sicurezza si verifica quando utenti malintenzionati ottengono l'accesso a un ambiente impadronendosi dell'identità di un utente. Trovare le identità compromesse non è un compito facile. Azure AD usa l'euristica e gli algoritmi adattivi di Machine Learning per rilevare azioni sospette correlate agli account dell'utente. Ogni azione sospetta rilevata viene archiviata in un record denominato [rilevamento dei rischi](../../active-directory/reports-monitoring/concept-risk-events.md). I rilevamenti dei rischi vengono registrati nei report di sicurezza di Azure AD. Per ulteriori informazioni, leggere informazioni sul report sulla [sicurezza degli utenti a rischio](../../active-directory/reports-monitoring/concept-user-at-risk.md) e sul report di sicurezza degli [equivi-insiemi rischiosi](../../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](best-practices-and-patterns.md).

Le risorse seguenti offrono altre informazioni più generali sulla sicurezza di Azure e sui servizi Microsoft correlati:
* [Blog del team di sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/): per informazioni aggiornate sulla sicurezza in Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): consente di segnalare le vulnerabilità della sicurezza di Microsoft, inclusi i problemi relativi ad Azure, tramite posta elettronica all'indirizzo secure@microsoft.com
