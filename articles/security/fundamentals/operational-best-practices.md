---
title: Procedure di sicurezza consigliate per la protezione degli asset-Microsoft Azure
description: Questo articolo fornisce un set di procedure consigliate operative per la protezione di dati, applicazioni e altre risorse in Azure.
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
ms.openlocfilehash: 69e55b17814c5b5ada0813bd0de66cc3a9a591f4
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219500"
---
# <a name="azure-operational-security-best-practices"></a>Procedure consigliate per la sicurezza operativa di Azure
Questo articolo fornisce un set di procedure consigliate operative per la protezione di dati, applicazioni e altre risorse in Azure.

Le procedure consigliate si basano su opinioni concordanti e funzionino con le caratteristiche e le capacità correnti della piattaforma Azure. Le opinioni e le tecnologie cambiano nel tempo e questo articolo viene aggiornato regolarmente per riflettere tali modifiche.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definire e distribuire procedure di sicurezza operative complesse
La sicurezza operativa di Azure include i servizi, i controlli e le funzionalità offerti agli utenti per proteggere i dati, le applicazioni e gli altri asset di Azure. La sicurezza operativa di Azure si basa su un framework che incorpora le conoscenze acquisite tramite funzionalità univoche di Microsoft, tra cui Microsoft [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), il programma [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) e una profonda consapevolezza del panorama delle minacce per la sicurezza informatica.

## <a name="manage-and-monitor-user-passwords"></a>Gestire e monitorare le password degli utenti
Nella tabella seguente sono elencate alcune procedure consigliate relative alla gestione delle password utente:

**Procedura consigliata**: Assicurarsi di disporre del livello appropriato di protezione delle password nel cloud.   
**Dettagli**: Seguire le istruzioni riportate nella [Guida alle password Microsoft](https://www.microsoft.com/research/publication/password-guidance/), che ha come ambito gli utenti delle piattaforme di identità microsoft (Azure Active Directory, Active Directory e account Microsoft).

**Procedura consigliata**: Monitorare le azioni sospette correlate agli account utente.   
**Dettagli**: Monitorare [gli utenti a rischio](/azure/active-directory/reports-monitoring/concept-user-at-risk) e [accessi a rischio](../../active-directory/reports-monitoring/concept-risk-events.md) usando Azure ad report di sicurezza.

**Procedura consigliata**: Rilevare e correggere automaticamente le password ad alto rischio.   
**Dettagli**: [Azure ad Identity Protection](/azure/active-directory/identity-protection/overview) è una funzionalità dell'edizione Azure ad Premium P2 che consente di:

- Rilevare le potenziali vulnerabilità che interessano le identità dell'organizzazione
- Configurare risposte automatizzate alle azioni sospette rilevate correlate alle identità dell'organizzazione
- Esaminare gli eventi imprevisti sospetti e intraprendere le azioni appropriate per risolverli

## <a name="receive-incident-notifications-from-microsoft"></a>Ricevi notifiche relative agli eventi imprevisti da Microsoft
Assicurarsi che il team delle operazioni di sicurezza riceva notifiche sugli eventi imprevisti di Azure da Microsoft. Una notifica per gli eventi imprevisti consente al team di sicurezza di rilevare le risorse di Azure compromesse, in modo che possano rispondere rapidamente e correggere potenziali rischi per la sicurezza.

Nel portale di registrazione di Azure è possibile verificare che le informazioni di contatto dell'amministratore includano i dettagli che notificano le operazioni di sicurezza. Le informazioni di contatto sono un indirizzo di posta elettronica e un numero di telefono.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizzare le sottoscrizioni di Azure in gruppi di gestione
Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. I [gruppi di gestione di Azure](/azure/governance/management-groups/create) offrono un livello di ambito superiore alle sottoscrizioni. È possibile organizzare le sottoscrizioni in contenitori denominati gruppi di gestione e applicare le condizioni di governance ai gruppi di gestione. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni applicate al gruppo di gestione.

È possibile creare una struttura flessibile di gruppi di gestione e sottoscrizioni in una directory. A ogni directory viene assegnato un singolo gruppo di gestione di primo livello denominato gruppo di gestione radice. Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice consente di applicare i criteri globali e le assegnazioni RBAC a livello di directory.

Ecco alcune procedure consigliate per l'uso dei gruppi di gestione:

**Procedura consigliata**: Assicurarsi che le nuove sottoscrizioni applichino elementi di governance quali criteri e autorizzazioni Man mano che vengono aggiunti.   
**Dettagli**: Usare il gruppo di gestione radice per assegnare elementi di sicurezza a livello aziendale che si applicano a tutte le risorse di Azure. I criteri e le autorizzazioni sono esempi di elementi.

**Procedura consigliata**: Allineare i livelli principali dei gruppi di gestione con la strategia di segmentazione per fornire un punto per il controllo e la coerenza dei criteri all'interno di ogni segmento.   
**Dettagli**: Creare un singolo gruppo di gestione per ogni segmento del gruppo di gestione radice. Non creare altri gruppi di gestione nella radice.

**Procedura consigliata**: Limitare la profondità del gruppo di gestione per evitare confusione che ostacola sia le operazioni che la sicurezza.   
**Dettagli**: Limitare la gerarchia a tre livelli, inclusa la radice.

**Procedura consigliata**: Selezionare con attenzione gli elementi da applicare all'intera azienda con il gruppo di gestione radice.   
**Dettagli**: Assicurarsi che gli elementi del gruppo di gestione radice abbiano una chiara necessità di essere applicati a tutte le risorse e che siano a basso effetto.

I candidati validi includono:

- Requisiti normativi che hanno un chiaro effetto aziendale, ad esempio restrizioni relative alla sovranità dei dati
- Requisiti con un potenziale impatto negativo vicino a zero sulle operazioni, ad esempio criteri con effetto di controllo o assegnazioni di autorizzazioni RBAC rivedute attentamente

**Procedura consigliata**: Pianificare e testare attentamente tutte le modifiche a livello aziendale nel gruppo di gestione radice prima di applicarle (criteri, modello RBAC e così via).   
**Dettagli**: Le modifiche apportate al gruppo di gestione radice possono influenzare ogni risorsa in Azure. Sebbene forniscano un modo efficace per garantire la coerenza nell'azienda, gli errori o l'utilizzo non corretto possono influire negativamente sulle operazioni di produzione. Testare tutte le modifiche al gruppo di gestione radice in un Lab di test o in un progetto pilota di produzione.

## <a name="streamline-environment-creation-with-blueprints"></a>Semplifica la creazione dell'ambiente con i progetti
[Il servizio Blueprints di Azure](/azure/governance/blueprints/overview) consente agli architetti di cloud e ai gruppi di tecnologie informatiche centrali di definire un set ripetibile di risorse di Azure che implementa e rispetta gli standard, i modelli e i requisiti di un'organizzazione. I progettisti di Azure consentono ai team di sviluppo di creare e creare rapidamente nuovi ambienti con un set di componenti predefiniti e la sicurezza di creare tali ambienti entro la conformità dell'organizzazione.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorare i servizi di archiviazione per rilevare cambiamenti inattesi nel comportamento
La diagnosi e la risoluzione dei problemi in un'applicazione distribuita ospitata in un ambiente cloud possono essere più complesse di quanto lo siano in ambienti tradizionali. Le applicazioni possono essere distribuite in un'infrastruttura PaaS o IaaS, in locale, su un dispositivo mobile o in una combinazione di questi tipi di ambienti. Il traffico di rete dell'applicazione può passare su reti pubbliche e private e l'applicazione può usare più tecnologie di archiviazione.

È consigliabile monitorare continuamente i servizi di archiviazione usati dall'applicazione per individuare eventuali cambiamenti inattesi nel comportamento, ad esempio tempi di risposta più lunghi. Usare la registrazione per raccogliere dati più dettagliati e analizzare un problema in modo approfondito. Le informazioni di diagnostica che si ottengono con il monitoraggio e la registrazione aiutano a determinare la causa radice del problema incontrato dall'applicazione. È possibile quindi identificare il problema e determinare le misure appropriate per correggerlo.

Il servizio [Analisi archiviazione di Azure](../../storage/common/storage-analytics.md) esegue la registrazione e fornisce i dati di metrica per un account di archiviazione di Azure. È consigliabile usare questi dati per tenere traccia delle richieste, analizzare le tendenze di utilizzo e diagnosticare i problemi relativi al proprio account di archiviazione.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenire le minacce, rilevarle e rispondere
Il [Centro sicurezza di Azure](../../security-center/security-center-intro.md) ti aiuta a prevenire, rilevare e rispondere alle minacce offrendo una maggiore visibilità e controllo sulla sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, aiuta il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con varie soluzioni di sicurezza.

Il livello gratuito del Centro sicurezza offre una protezione limitata solo per le risorse di Azure. Il livello Standard estende queste funzionalità in locale e in altri cloud. Il Centro sicurezza standard consente di trovare e correggere le vulnerabilità di sicurezza, applicare controlli di accesso e applicazioni per bloccare le attività dannose, rilevare le minacce usando le funzionalità di analisi e intelligenza e rispondere rapidamente in caso di attacco. È possibile provare gratuitamente il livello Standard del Centro sicurezza per i primi 60 giorni. Si consiglia [di aggiornare la sottoscrizione di Azure al centro sicurezza standard](../../security-center/security-center-get-started.md).

Usare il Centro sicurezza per ottenere una visualizzazione centralizzata dello stato di sicurezza di tutte le risorse di Azure. Il Centro sicurezza consente di verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente e di identificare rapidamente le risorse che richiedono attenzione.

Il Centro sicurezza si integra anche con [Microsoft Defender Advanced Threat Protection (ATP)](../../security-center/security-center-wdatp.md), che offre funzionalità complete di rilevamento e risposta degli endpoint. Con l'integrazione di Microsoft Defender ATP è possibile individuare le anomalie. È anche possibile rilevare e rispondere agli attacchi avanzati sugli endpoint server monitorati dal centro sicurezza.

Quasi tutte le organizzazioni aziendali dispongono di un sistema SIEM (Security Information and Event Management) che consente di identificare le minacce emergenti consolidando le informazioni di log da diversi dispositivi di raccolta dei segnali. I log vengono quindi analizzati da un sistema di analisi dei dati per identificare gli elementi "interessanti" del rumore inevitabile in tutte le soluzioni di raccolta e analisi dei log.

[Sentinel di Azure](/azure/sentinel/overview) è una soluzione scalabile, nativa del cloud, gestione di informazioni ed eventi di sicurezza (Siem) e di risposta automatica per l'orchestrazione della sicurezza (soar). Azure Sentinel fornisce analisi di sicurezza intelligenti e Intelligence per le minacce tramite il rilevamento degli avvisi, la visibilità delle minacce, la ricerca proattiva e la risposta automatica alle minacce.

Di seguito sono riportate alcune procedure consigliate per prevenire, rilevare e rispondere alle minacce:

**Procedura consigliata**: Aumentare la velocità e la scalabilità della soluzione SIEM usando un SIEM basato sul cloud.   
**Dettagli**: Esaminare le funzionalità e le funzionalità di [Azure Sentinel](/azure/sentinel/overview) e confrontarle con le funzionalità di quelle attualmente in uso in locale. Prendere in considerazione l'adozione di Azure Sentinel se soddisfa i requisiti SIEM dell'organizzazione.

**Procedura consigliata**: Individuare le vulnerabilità più gravi per la sicurezza, in modo da poter definire le priorità dell'indagine.   
**Dettagli**: Esaminare il [Punteggio sicuro di Azure](../../security-center/security-center-secure-score.md) per visualizzare i consigli risultanti dai criteri e dalle iniziative di Azure integrati nel centro sicurezza di Azure. Questi consigli aiutano a affrontare i rischi principali, ad esempio aggiornamenti della sicurezza, Endpoint Protection, crittografia, configurazioni di sicurezza, WAF mancanti, VM connesse a Internet e molto altro ancora.

Il Punteggio sicuro, che si basa su controlli di sicurezza di rete per Internet (CIS), consente di benchmarkare la sicurezza di Azure dell'organizzazione rispetto alle origini esterne. La convalida esterna consente di convalidare e migliorare la strategia di sicurezza del team.

**Procedura consigliata**: Monitora il comportamento di sicurezza di computer, reti, servizi di archiviazione e dati e applicazioni per individuare e classificare in ordine di priorità i potenziali problemi di sicurezza.  
**Dettagli**: Seguire le [raccomandazioni di sicurezza](../../security-center/security-center-recommendations.md) nel centro sicurezza di avvio con gli elementi con la priorità più alta.

**Procedura consigliata**: Integra gli avvisi del Centro sicurezza nella soluzione SIEM (Security Information and Event Management).   
**Dettagli**: La maggior parte delle organizzazioni con SIEM lo usa come centrale per gli avvisi di sicurezza che richiedono una risposta analista. Gli eventi elaborati generati dal centro sicurezza vengono pubblicati nel log attività di Azure, uno dei log disponibili tramite monitoraggio di Azure. Monitoraggio di Azure offre una pipeline consolidata per eseguire il routing dei dati di monitoraggio in uno strumento SIEM. Per istruzioni, vedere [integrare le soluzioni di sicurezza nel centro sicurezza](../../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) . Se si usa Azure Sentinel, vedere [connettere il Centro sicurezza di Azure](../../sentinel/connect-azure-security-center.md).

**Procedura consigliata**: Integrare i log di Azure con SIEM.   
**Dettagli**: Usare [monitoraggio di Azure per raccogliere ed esportare dati](/azure/azure-monitor/overview#integrate-and-export-data). Questa procedura è fondamentale per l'abilitazione dell'analisi degli eventi imprevisti della sicurezza e la conservazione dei log online è limitata. Se si usa Sentinel di Azure, vedere [connettere le origini dati](../../sentinel/connect-data-sources.md).

**Procedura consigliata**: Velocizzare i processi di analisi e caccia e ridurre i falsi positivi integrando le funzionalità di rilevamento e risposta degli endpoint nell'analisi degli attacchi.   
**Dettagli**: [Abilitare l'integrazione di Microsoft Defender ATP](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) tramite i criteri di sicurezza del Centro sicurezza. È consigliabile usare Sentinel di Azure per la ricerca di minacce e la risposta agli eventi imprevisti

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitoraggio della rete basato su scenari end-to-end
Per creare una rete end-to-end in Azure, i clienti combinano varie risorse di rete, ad esempio una rete virtuale, ExpressRoute, un gateway applicazione e servizi di bilanciamento del carico. Il monitoraggio è disponibile in ognuna delle risorse di rete.

[Network Watcher di Azure](../../network-watcher/network-watcher-monitoring-overview.md) è un servizio a livello di area, dotato di strumenti di diagnostica e di visualizzazione che consentono di monitorare e diagnosticare le condizioni a livello di scenario di rete in Azure, verso e da Azure.

Di seguito vengono descritte le procedure consigliate per il monitoraggio della rete e gli strumenti disponibili.

**Procedura consigliata**: automatizzare il monitoraggio di rete remoto con l'acquisizione dei pacchetti.  
**Dettagli**: monitorare e diagnosticare i problemi di rete senza accedere alle macchine virtuali usando Network Watcher. Attivare l'[acquisizione dei pacchetti](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) impostando gli avvisi e ottenere l'accesso alle informazioni sulle prestazioni in tempo reale a livello del pacchetto. Quando viene rilevato un problema, è possibile esaminarlo in dettaglio per una diagnosi migliore.

**Procedura consigliata**: acquisire informazioni dettagliate sul traffico di rete usando i log dei flussi.  
**Dettagli**: ottenere informazioni approfondite sui modelli di traffico di rete con i [log dei flussi del gruppo di sicurezza di rete](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). Le informazioni contenute nei log dei flussi aiutano a raccogliere i dati per la conformità, il controllo e il monitoraggio del profilo di sicurezza della rete.

**Procedura consigliata**: diagnosticare i problemi di connettività della VPN.  
**Dettagli**: usare Network Watcher per [diagnosticare i problemi di connessione e di Gateway VPN più comuni](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Non è solo possibile identificare il problema ma si può anche usare i log dettagliati per altre indagini.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Distribuzione sicura tramite strumenti DevOps collaudati
Usare le seguenti procedure consigliate di DevOps per assicurarsi che i team e l'azienda siano produttivi ed efficienti.

**Procedura consigliata**: automatizzare la creazione e la distribuzione di servizi.  
**Dettagli**: l'[infrastruttura come codice](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) è un set di tecniche e procedure che aiutano i professionisti IT a evitare il carico di lavoro associato alle attività quotidiane di creazione e gestione dell'infrastruttura modulare. Lo scopo è consentire ai professionisti IT di creare e gestire un ambiente server moderno in modo simile a quello usato dagli sviluppatori di software per creare e gestire il codice delle applicazioni.

È possibile usare [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) per effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Lo stesso modello viene usato per distribuire ripetutamente l'applicazione in ogni fase del ciclo di vita dell'applicazione.

**Procedura consigliata**: creare e distribuire automaticamente app Web o servizi cloud di Azure.  
**Dettagli**: È possibile configurare il Azure DevOps Projects per la [compilazione e la distribuzione automatiche](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) in app Web o servizi cloud di Azure. Azure DevOps distribuisce automaticamente i file binari dopo una compilazione in Azure dopo ogni archiviazione del codice. Il processo di compilazione del pacchetto equivale al comando Pacchetto di Visual Studio, mentre i passaggi per la pubblicazione equivalgono al comando Pubblica di Visual Studio.

**Procedura consigliata**: Automatizzare Release Management.  
**Dettagli**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) è una soluzione per automatizzare la distribuzione in più fasi e la gestione del processo di rilascio. È possibile creare pipeline gestite di distribuzione continua per rilasciare versioni in modo rapido, semplice e frequente. Con Azure Pipelines è possibile automatizzare il processo di rilascio e definire flussi di lavoro predefiniti per l'approvazione. Sono supportate la distribuzione locale e nel cloud, l'estensione e la personalizzazione in base alle specifiche esigenze.

**Procedura consigliata**: verificare le prestazioni dell'app prima di avviarla o distribuire aggiornamenti nell'ambiente di produzione.  
**Dettagli**: Eseguire [test di carico](/azure/devops/test/load-test/overview#alternatives) basati sul cloud per:

- Individuare problemi di prestazioni nell'app.
- Migliorare la qualità della distribuzione.
- Assicurarsi che l'app sia sempre disponibile.
- Assicurarsi che l'app possa gestire il traffico per la prossima campagna di lancio o di marketing.

[Apache JMeter](https://jmeter.apache.org/) è uno strumento open source gratuito e diffuso con un solido supporto della community.

**Procedura consigliata**: monitorare le prestazioni dell'applicazione.  
**Dettagli**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. È possibile usare Application Insights per monitorare l'applicazione Web mentre è in esecuzione. Il servizio rileva automaticamente le anomalie nelle prestazioni e include strumenti di analisi che consentono di diagnosticare i problemi e di conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

## <a name="mitigate-and-protect-against-ddos"></a>Attenuazione e protezione da attacchi Distributed Denial of Service (DDoS)
Il Distributed Denial of Service (DDoS) è un tipo di attacco che tenta di esaurire le risorse dell'applicazione. L'obiettivo è compromettere la disponibilità e la capacità dell'applicazione di gestire richieste legittime. Gli attacchi stanno diventando più sofisticati con dimensioni e impatto maggiori. Possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.

La progettazione e la creazione per la resilienza agli attacchi Distributed Denial of Service (DDoS) richiedono pianificazione e progettazione per un'ampia gamma di modalità di errore. Di seguito vengono descritte le procedure consigliate per la creazione di servizi resilienti a DDoS in Azure.

**Procedura consigliata**: Garantire la sicurezza durante l'intero ciclo di vita di un'applicazione, dalla progettazione e implementazione alla distribuzione e alle operazioni. Le applicazioni possono contenere bug che consentono a un volume relativamente basso di richieste di usare una quantità elevata di risorse, provocando un'interruzione del servizio.  
**Dettagli**: Per proteggere un servizio in esecuzione in Microsoft Azure, i clienti devono avere una buona conoscenza dell'architettura delle applicazioni e concentrarsi sui [cinque punti chiave della qualità del software](https://docs.microsoft.com/azure/architecture/guide/pillars). I clienti devono conoscere i volumi di traffico tipici, il modello di connettività tra l'applicazione e le altre applicazioni e gli endpoint di servizio esposti a Internet pubblico.

Garantire che un'applicazione sia abbastanza resiliente da riuscire a gestire un attacco Denial of Service destinato all'applicazione stessa è di fondamentale importanza. Sicurezza e privacy sono integrate direttamente nella piattaforma di Azure, a partire dal processo [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl). SDL si rivolge alla sicurezza in ogni fase di sviluppo e assicura che Azure sia continuamente aggiornato per renderlo ancora più sicuro.

**Procedura consigliata**: progettare le applicazioni con [scalabilità orizzontale](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) per soddisfare la richiesta di un carico amplificato, in particolare in caso di attacco DDoS. Se l'applicazione dipende da una singola istanza di un servizio, crea un singolo punto di errore. Il provisioning di più istanze rende il sistema più resiliente e scalabile.  
**Dettagli**: Per [Servizio app di Azure](/azure/app-service/app-service-value-prop-what-is) selezionare un [piano di servizio app](../../app-service/overview-hosting-plans.md) che offra più istanze.

Per Servizi cloud di Azure, configurare ognuno dei ruoli in modo da usare [più istanze](../../cloud-services/cloud-services-choose-me.md).

Per [Macchine virtuali di Microsoft Azure](/azure/virtual-machines/windows/overview), verificare che l'architettura di VM includa più macchine virtuali e che ogni macchina virtuale sia inclusa in un [set di disponibilità](/azure/virtual-machines/virtual-machines-windows-manage-availability). Si consiglia di usare set di scalabilità di macchine virtuali per le funzionalità di scalabilità automatica.

**Procedura consigliata**: La disposizione delle difese su più livelli in un'applicazione riduce le probabilità di riuscita degli attacchi. Implementare progettazioni sicure per le applicazioni tramite le funzionalità integrate della piattaforma di Azure.  
**Dettagli**: il rischio di attacco aumenta con le dimensioni (superficie di attacco) dell'applicazione. È possibile ridurre la superficie usando l'elenco degli elementi consentiti per chiudere lo spazio indirizzi IP esposto e le porte in ascolto non necessarie sui servizi di bilanciamento del carico ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) e [gateway applicazione di Azure](/azure/application-gateway/application-gateway-create-probe-portal)).

I [gruppi di sicurezza di rete](../../virtual-network/security-overview.md) rappresentano un altro modo per ridurre la superficie di attacco. È possibile usare [tag di servizio](../../virtual-network/security-overview.md#service-tags) e [gruppi di sicurezza dell'applicazione](../../virtual-network/security-overview.md#application-security-groups) per ridurre la complessità per la creazione della regola di sicurezza e configurare la sicurezza di rete come estensione naturale della struttura di un'applicazione.

Distribuire i servizi di Azure in una [rete virtuale](../../virtual-network/virtual-networks-overview.md), laddove possibile. Ciò consente alle risorse del servizio di comunicare attraverso indirizzi IP privati. Il traffico del servizio di Azure da una rete virtuale usa indirizzi IP pubblici come indirizzi IP di origine per impostazione predefinita.

Tramite gli [endpoint di servizio](../../virtual-network/virtual-network-service-endpoints-overview.md) il traffico del servizio passa all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale.

Le risorse locali dei clienti che vengono spesso attaccate insieme alle risorse in Azure. Se si connette un ambiente locale ad Azure, ridurre al minimo l'esposizione delle risorse locali alla rete Internet pubblica.

Azure prevede due [offerte di servizio](../../virtual-network/ddos-protection-overview.md) contro gli attacchi DDoS per la protezione della rete:

- La protezione di base è integrata in Azure per impostazione predefinita senza costi aggiuntivi. La scala e la capacità complete della rete globalmente distribuita di Azure forniscono una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio costante del traffico e la mitigazione in tempo reale. La protezione di base non richiede alcuna modifica della configurazione utente o dell'applicazione e aiuta a proteggere tutti i servizi di Azure, inclusi i servizi PaaS come DNS di Azure.
- La protezione standard offre funzionalità avanzate di attenuazione degli attacchi DDoS contro gli attacchi alla rete e viene ottimizzata automaticamente per proteggere le specifiche risorse di Azure. La protezione è semplice da abilitare durante la creazione di reti virtuali. Può essere abilitata anche dopo la creazione e non richiede alcuna modifica delle applicazioni o delle risorse.

## <a name="enable-azure-policy"></a>Abilitare criteri di Azure
[Criteri di Azure](/azure/governance/policy/overview) è un servizio di Azure che è possibile usare per creare, assegnare e gestire i criteri. Questi criteri applicano le regole e gli effetti sulle risorse, in modo che le risorse siano conformi agli standard aziendali e ai contratti di servizio. Criteri di Azure soddisfa questa esigenza valutando le risorse per la mancata conformità con i criteri assegnati.

Abilitare criteri di Azure per il monitoraggio e l'applicazione dei criteri scritti dall'organizzazione. In questo modo si garantisce la conformità ai requisiti di sicurezza aziendali o normativi gestendo centralmente i criteri di sicurezza nei carichi di lavoro cloud ibridi. Informazioni su come [creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md). Per una panoramica degli elementi di un criterio, vedere [struttura delle definizioni di criteri di Azure](../../governance/policy/concepts/definition-structure.md) .

Di seguito sono riportate alcune procedure consigliate per la sicurezza dopo l'adozione di criteri di Azure:

**Procedura consigliata**: Il criterio supporta diversi tipi di effetti. È possibile leggere informazioni su di essi nella [struttura delle definizioni di criteri di Azure](../../governance/policy/concepts/definition-structure.md#policy-rule). Le operazioni aziendali possono essere influenzate negativamente dall'effetto di **negazione** e dall'effetto di **correzione** , quindi iniziare con l'effetto di **controllo** per limitare il rischio di impatto negativo dai criteri.   
**Dettagli**: [Avviare le distribuzioni dei criteri in modalità di controllo](../../governance/policy/concepts/definition-structure.md#policy-rule) e quindi procedere in seguito per **negare** o **correggere**. Testare ed esaminare i risultati dell'effetto di controllo prima di procedere con la **negazione** o la **correzione**.

Per altre informazioni, vedere [creare e gestire i criteri per applicare la conformità](../../governance/policy/tutorials/create-and-manage.md).

**Procedura consigliata**: Identificare i ruoli responsabili del monitoraggio delle violazioni dei criteri e verificare che l'azione correttiva venga eseguita rapidamente.   
**Dettagli**: Verificare che il monitoraggio del ruolo assegnato sia conforme tramite il [portale di Azure](../../governance/policy/how-to/get-compliance-data.md#portal) o tramite la [riga di comando](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Procedura consigliata**: Criteri di Azure è una rappresentazione tecnica dei criteri scritti di un'organizzazione. Eseguire il mapping di tutti i criteri di Azure ai criteri dell'organizzazione per ridurre la confusione e aumentare la coerenza.   
**Dettagli**: Mapping dei documenti nella documentazione dell'organizzazione o nei criteri di Azure aggiungendo un riferimento ai criteri dell'organizzazione nella descrizione di [criteri](../../governance/policy/concepts/definition-structure.md#display-name-and-description) di Azure o nella descrizione di un' [iniziativa](../../governance/policy/concepts/definition-structure.md#initiatives) di criteri di Azure.

## <a name="monitor-azure-ad-risk-reports"></a>Monitorare Azure AD report di rischio
La maggior parte delle violazioni della sicurezza si verifica quando utenti malintenzionati ottengono l'accesso a un ambiente impadronendosi dell'identità di un utente. Trovare le identità compromesse non è un compito facile. Azure AD usa l'euristica e gli algoritmi adattivi di Machine Learning per rilevare azioni sospette correlate agli account dell'utente. Ogni azione sospetta rilevata viene archiviata in un record denominato [rilevamento del rischio](../../active-directory/reports-monitoring/concept-risk-events.md). I rilevamenti dei rischi vengono registrati nei report di sicurezza Azure AD. Per altre informazioni, vedere il report sulla sicurezza per [gli utenti a rischio](../../active-directory/reports-monitoring/concept-user-at-risk.md) e il [report sulla sicurezza degli accessi a rischio](../../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](best-practices-and-patterns.md).

Le risorse seguenti offrono altre informazioni più generali sulla sicurezza di Azure e sui servizi Microsoft correlati:
* [Blog del team di sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/): per informazioni aggiornate sulla sicurezza in Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): consente di segnalare le vulnerabilità della sicurezza di Microsoft, inclusi i problemi relativi ad Azure, tramite posta elettronica all'indirizzo secure@microsoft.com
