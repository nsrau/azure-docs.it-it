---
title: Le procedure consigliate per la protezione dei tuoi asset - Microsoft Azure
description: Questo articolo fornisce un set di procedure operative ottimali per la protezione dei dati, applicazioni e gli altri asset di Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 4a4677b5db730001df75d201d8e6d3149cb928e6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409837"
---
# <a name="azure-operational-security-best-practices"></a>Procedure consigliate per la sicurezza operativa di Azure
Questo articolo fornisce un set di procedure operative ottimali per la protezione dei dati, applicazioni e gli altri asset di Azure.

Le procedure consigliate si basano su opinioni concordanti e funzionino con le caratteristiche e le capacità correnti della piattaforma Azure. Le opinioni e le tecnologie cambiano nel tempo e questo articolo viene aggiornato regolarmente per riflettere tali modifiche.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definire e distribuire elevati di sicurezza operativa
La sicurezza operativa di Azure include i servizi, i controlli e le funzionalità offerti agli utenti per proteggere i dati, le applicazioni e gli altri asset di Azure. La sicurezza operativa di Azure si basa su un framework che incorpora le conoscenze acquisite tramite funzionalità univoche di Microsoft, tra cui Microsoft [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl), il programma [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) e una profonda consapevolezza del panorama delle minacce per la sicurezza informatica.

## <a name="manage-and-monitor-user-passwords"></a>Gestire e monitorare le password utente
Nella tabella seguente sono elencate alcune procedure consigliate relative alla gestione delle password utente:

**Procedura consigliata**: Assicurarsi di che avere il livello appropriato di protezione con password nel cloud.   
**Dettagli**: Seguire le indicazioni fornite in [indicazioni sulle Password Microsoft](https://www.microsoft.com/research/publication/password-guidance/), che ha come ambito per gli utenti tra le piattaforme di identità di Microsoft (account di Azure Active Directory, Active Directory e Microsoft).

**Procedura consigliata**: Monitoraggio per le azioni sospette correlate agli account utente.   
**Dettagli**: Esegue il monitoraggio [gli utenti a rischio](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk) e [accessi a rischio](../active-directory/reports-monitoring/concept-risk-events.md) mediante la sicurezza di Azure AD i report.

**Procedura consigliata**: Automaticamente a rilevare e correggere le password ad alto rischio.   
**Dettagli**: [Azure AD Identity Protection](../active-directory/identity-protection/overview.md) è una funzionalità di Azure AD Premium P2 edition che consente di:

- Rilevare le potenziali vulnerabilità che interessano le identità dell'organizzazione
- Configurare risposte automatizzate alle azioni sospette rilevate correlate alle identità dell'organizzazione
- Esaminare gli eventi imprevisti sospetti e adottare misure appropriate per risolverli

## <a name="receive-incident-notifications-from-microsoft"></a>Ricevere notifiche degli eventi imprevisti da parte di Microsoft
Assicurarsi che il team di operazioni di sicurezza riceve le notifiche degli eventi imprevisti di Azure da Microsoft. Consente una notifica degli eventi imprevisti conoscenza il team di protezione si siano compromesse le risorse di Azure affinché possano rispondere a rapidamente e risolvere potenziali rischi di sicurezza.

Nel portale di registrazione di Azure, è possibile garantire informazioni di contatto amministratore includono i dettagli che inviano una notifica di operazioni di sicurezza. Le informazioni di contatto sono un indirizzo di posta elettronica e un numero di telefono.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizzare le sottoscrizioni di Azure in gruppi di gestione
Se l'organizzazione dispone di molte sottoscrizioni, potrebbe essere necessario gestire in modo efficace l'accesso, i criteri e la conformità per tali sottoscrizioni. [I gruppi di gestione di Azure](../governance/management-groups/create.md) forniscono un livello di ambito di sopra delle sottoscrizioni. È organizzare le sottoscrizioni in contenitori chiamati gruppi di gestione e si applicano le condizioni di governance ai gruppi di gestione. Tutte le sottoscrizioni all'interno di un gruppo di gestione ereditano automaticamente le condizioni applicate al gruppo di gestione.

È possibile compilare una struttura flessibile di gruppi di gestione e sottoscrizioni in una directory. Ogni directory viene assegnato un gruppo di gestione principale denominato gruppo di gestione radice. Questo gruppo di gestione radice è integrato nella gerarchia in modo da ricondurre al suo interno tutti i gruppi di gestione e le sottoscrizioni. Il gruppo di gestione radice consente i criteri globali e assegnazioni di accessi da applicare a livello di directory.

Ecco alcune procedure consigliate per l'uso di gruppi di gestione:

**Procedura consigliata**: Assicurarsi che le nuove sottoscrizioni vengano applicati gli elementi di governance, ad esempio i criteri e autorizzazioni quando vengono aggiunti.   
**Dettagli**: Usare il gruppo di gestione radice per assegnare gli elementi di sicurezza a livello aziendale che si applicano a tutte le risorse di Azure. I criteri e le autorizzazioni sono esempi di elementi.

**Procedura consigliata**: Allinea i livelli superiori di gruppi di gestione con la strategia di segmentazione per offrire un punto di controllo e criteri di coerenza all'interno di ogni segmento.   
**Dettagli**: Creare un gruppo di gestione singolo per ogni segmento del gruppo di gestione radice. Non creare altri gruppi di gestione sotto la radice.

**Procedura consigliata**: Limitare profondità di gruppo di gestione per evitare confusione che ostacola operazioni sia la sicurezza.   
**Dettagli**: Limitare a tre livelli, inclusa la radice della gerarchia.

**Procedura consigliata**: Scegliere con attenzione gli elementi da applicare a tutta l'azienda con il gruppo di gestione radice.   
**Dettagli**: Verificare che gli elementi di gruppo di gestione radice hanno un chiaro devono essere applicati in tutte le risorse e che hanno impatto basso.

Di seguito sono riportati i buoni candidati:

- Requisiti normativi che hanno un impatto aziendale non crittografato (ad esempio, le restrizioni correlate alla titolarità dei dati)
- Potenziale prossima a zero negativo influiscono sulle operazioni, ad esempio i criteri con effetto di controllo o RBAC assegnazioni di autorizzazioni che sono state verificate con attenzione

**Procedura consigliata**: Con attenzione, pianificare e testare tutte le modifiche a livello aziendale sul gruppo di gestione radice prima di applicarle (criteri, modello RBAC e così via).   
**Dettagli**: Le modifiche nel gruppo di gestione radice possono interessano tutte le risorse in Azure. Anche se offrono una potente funzionalità per garantire la coerenza all'interno dell'azienda, errori o utilizzo non corretto può influire negativamente sulle operazioni di produzione. Tutte le modifiche apportate al gruppo di gestione radice di test in un lab di test o produzione pilota.

## <a name="streamline-environment-creation-with-blueprints"></a>Semplificare la creazione dell'ambiente con i progetti
[Linee guida di Azure](../governance/blueprints/overview.md) servizio consente gli architetti di cloud e i gruppi di tecnologia centrale delle informazioni definire un set ripetibile delle risorse di Azure che implementa ed è conforme alle norme, modelli e i requisiti dell'organizzazione. I progetti Azure rende possibile per i team di sviluppo compilare rapidamente e realizza nuovi ambienti con un set di componenti predefiniti e la certezza che si sta creando tali ambienti all'interno di conformità dell'organizzazione.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorare i servizi di archiviazione per rilevare cambiamenti inattesi nel comportamento
La diagnosi e la risoluzione dei problemi in un'applicazione distribuita ospitata in un ambiente cloud possono essere più complesse di quanto lo siano in ambienti tradizionali. Le applicazioni possono essere distribuite in un'infrastruttura PaaS o IaaS, in locale, su un dispositivo mobile o in una combinazione di questi tipi di ambienti. Il traffico di rete dell'applicazione può passare su reti pubbliche e private e l'applicazione può usare più tecnologie di archiviazione.

È consigliabile monitorare continuamente i servizi di archiviazione usati dall'applicazione per individuare eventuali cambiamenti inattesi nel comportamento, ad esempio tempi di risposta più lunghi. Usare la registrazione per raccogliere dati più dettagliati e analizzare un problema in modo approfondito. Le informazioni di diagnostica che si ottengono con il monitoraggio e la registrazione aiutano a determinare la causa radice del problema incontrato dall'applicazione. È possibile quindi identificare il problema e determinare le misure appropriate per correggerlo.

Il servizio [Analisi archiviazione di Azure](../storage/storage-analytics.md) esegue la registrazione e fornisce i dati di metrica per un account di archiviazione di Azure. È consigliabile usare questi dati per tenere traccia delle richieste, analizzare le tendenze di utilizzo e diagnosticare i problemi relativi al proprio account di archiviazione.

## <a name="prevent-detect-and-respond-to-threats"></a>Prevenire le minacce, rilevarle e rispondere
[Centro sicurezza di Azure](../security-center/security-center-intro.md) consente di prevenire, rilevare e rispondere alle minacce tramite livelli superiori di visibilità (e il controllo di) la sicurezza delle risorse di Azure. Offre funzionalità integrate di monitoraggio della sicurezza e gestione dei criteri tra le sottoscrizioni di Azure, aiuta il rilevamento delle minacce che altrimenti passerebbero inosservate e funziona con varie soluzioni di sicurezza.

Il livello gratuito del Centro sicurezza offre sicurezza limitata delle tue risorse di Azure. Il livello Standard estende queste funzionalità in locale e in altri cloud. Standard del Centro sicurezza consente di trovare e correggere le vulnerabilità di sicurezza, applicare i controlli di accesso e delle applicazioni per bloccare le attività dannose, rilevare le minacce con intelligence e analitica e rispondere rapidamente in caso di attacco. È possibile provare gratuitamente il livello Standard del Centro sicurezza per i primi 60 giorni. È consigliabile che si [aggiornare la sottoscrizione di Azure a livello Standard del Centro sicurezza](../security-center/security-center-get-started.md).

Usare il Centro sicurezza per ottenere una visualizzazione centrale dello stato della protezione di tutte le risorse di Azure. Il Centro sicurezza consente di verificare subito che i controlli di sicurezza appropriati siano implementati e configurati correttamente e di identificare rapidamente le risorse che richiedono attenzione.

Il Centro sicurezza si integra inoltre con [Windows Defender Advanced Threat Protection (ATP)](../security-center/security-center-wdatp.md), che offre funzionalità complete di Endpoint di rilevamento e risposta (EDR). Con l'integrazione di Windows Defender ATP, è possibile individuare eventuali anomalie, È anche possibile rilevare e rispondere agli attacchi avanzati negli endpoint server monitorati dal Centro sicurezza.

Quasi tutte le organizzazioni hanno un sistema di gestione (SIEM) informazioni ed eventi sicurezza per consentire di identificare le minacce emergenti consolidando le informazioni di log dai dispositivi di raccolta dei segnali diversi. I log vengono quindi analizzati da un sistema di analitica dei dati per facilitare l'identificazione "interessante" le informazioni importanti che è inevitabile in tutte le soluzioni di analitica e di raccolta log.

[Azure Sentinel](../sentinel/overview.md) è una soluzione di risposta (visibilità) orchestrazione automatizzata di sicurezza e il scalabile e native del cloud, informazioni ed eventi gestione della sicurezza (SIEM). Azure Sentinel fornisce sicurezza intelligente analitica e threat intelligence tramite il rilevamento degli avvisi, la visibilità delle minacce, caccia proattiva e risposta ai rischi automatizzata.

Ecco alcune procedure consigliate per evitare, rilevare e rispondere alle minacce:

**Procedura consigliata**: Aumentare la velocità e scalabilità della soluzione SIEM tramite un sistema SIEM e basato sul cloud.   
**Dettagli**: Provare a utilizzare le caratteristiche e funzionalità di [Sentinel Azure](../sentinel/overview.md) e confrontarli con le funzionalità di quello che si sta attualmente usando un'istanza locale. Considerare l'adozione di Azure Sentinel se soddisfa i requisiti dell'organizzazione SIEM.

**Procedura consigliata**: Trovare le vulnerabilità di sicurezza più grave, pertanto è possibile assegnare una priorità analisi.   
**Dettagli**: Rivedere le [Azure punteggio sicuro](../security-center/security-center-secure-score.md) per visualizzare le indicazioni risultanti dai criteri di Azure e le iniziative incorporate in Centro sicurezza di Azure. Queste indicazioni consentono di rischi principali di indirizzo, ad esempio gli aggiornamenti della sicurezza, endpoint protection, crittografia, le configurazioni di sicurezza, mancante WAF, le macchine virtuali connessi a internet e molto altro ancora.

Il punteggio sicuro, basata sul centro per i controlli di Internet Security (CIS), è possibile effettuare un benchmark sicurezza di Azure dell'organizzazione da origini esterne. La convalida esterna consente di convalidare e migliorare la strategia di sicurezza del tuo team.

**Procedura consigliata**: Monitorare il comportamento di sicurezza del computer, reti, servizi di archiviazione e i dati e alle applicazioni di individuare e classificare i potenziali problemi di sicurezza.  
**Dettagli**: Seguire le [raccomandazioni sulla sicurezza](../security-center/security-center-recommendations.md) nel Centro sicurezza inizia, con gli elementi con priorità più alti.

**Procedura consigliata**: Integrare gli avvisi del Centro sicurezza in una soluzione SIEM (gestione) di informazioni ed eventi di sicurezza.   
**Dettagli**: La maggior parte delle organizzazioni con un sistema SIEM usano come una risorsa principale per gli avvisi di sicurezza che richiedono una risposta di analista. Gli eventi elaborati prodotti dal Centro sicurezza vengono pubblicati nel log attività di Azure, uno dei registri disponibili tramite Monitoraggio di Azure. Monitoraggio di Azure offre una pipeline consolidata per eseguire il routing dei dati di monitoraggio in uno strumento SIEM. Visualizzare [integrare soluzioni di sicurezza nel Centro sicurezza](../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) per le istruzioni. Se si usa Azure Sentinel, vedere [Centro sicurezza di Azure Connect](../sentinel/connect-azure-security-center.md).

**Procedura consigliata**: Integrare Azure i log con il sistema SIEM.   
**Dettagli**: Uso [monitoraggio di Azure per raccogliere ed esportare dati](../azure-monitor/overview.md#integrate-and-export-data). Questa pratica è fondamentale per l'abilitazione di un'analisi degli eventi imprevisti di sicurezza e conservazione dei log online è limitato. Se si usa Azure Sentinel, vedere [connettere origini dati](../sentinel/connect-data-sources.md).

**Procedura consigliata**: Velocizzare l'analisi e ai processi di ricerca e ridurre i falsi positivi grazie all'integrazione delle funzionalità di rilevamento degli Endpoint e risposta (EDR) nell'indagine di attacco.   
**Dettagli**: [Abilitare l'integrazione di Windows Defender ATP](../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) tramite i criteri di sicurezza del Centro sicurezza. È consigliabile usare Azure Sentinel per ricerca di minacce e risposta agli eventi imprevisti.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Monitoraggio della rete basato su scenari end-to-end
Per creare una rete end-to-end in Azure, i clienti combinano varie risorse di rete, ad esempio una rete virtuale, ExpressRoute, un gateway applicazione e servizi di bilanciamento del carico. Il monitoraggio è disponibile in ognuna delle risorse di rete.

[Network Watcher di Azure](../network-watcher/network-watcher-monitoring-overview.md) è un servizio a livello di area, dotato di strumenti di diagnostica e di visualizzazione che consentono di monitorare e diagnosticare le condizioni a livello di scenario di rete in Azure, verso e da Azure.

Di seguito vengono descritte le procedure consigliate per il monitoraggio della rete e gli strumenti disponibili.

**Procedura consigliata**: automatizzare il monitoraggio di rete remoto con l'acquisizione dei pacchetti.  
**Dettagli**: monitorare e diagnosticare i problemi di rete senza accedere alle macchine virtuali usando Network Watcher. Attivare l'[acquisizione dei pacchetti](../network-watcher/network-watcher-alert-triggered-packet-capture.md) impostando gli avvisi e ottenere l'accesso alle informazioni sulle prestazioni in tempo reale a livello del pacchetto. Quando viene rilevato un problema, è possibile esaminarlo in dettaglio per una diagnosi migliore.

**Procedura consigliata**: acquisire informazioni dettagliate sul traffico di rete usando i log dei flussi.  
**Dettagli**: ottenere informazioni approfondite sui modelli di traffico di rete con i [log dei flussi del gruppo di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Le informazioni contenute nei log dei flussi aiutano a raccogliere i dati per la conformità, il controllo e il monitoraggio del profilo di sicurezza della rete.

**Procedura consigliata**: diagnosticare i problemi di connettività della VPN.  
**Dettagli**: usare Network Watcher per [diagnosticare i problemi di connessione e di Gateway VPN più comuni](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Non è solo possibile identificare il problema ma si può anche usare i log dettagliati per altre indagini.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Distribuzione sicura tramite strumenti DevOps collaudati
Usare le seguenti procedure consigliate di DevOps per assicurarsi che i team e l'azienda siano produttivi ed efficienti.

**Procedura consigliata**: automatizzare la creazione e la distribuzione di servizi.  
**Dettagli**: l'[infrastruttura come codice](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) è un set di tecniche e procedure che aiutano i professionisti IT a evitare il carico di lavoro associato alle attività quotidiane di creazione e gestione dell'infrastruttura modulare. Lo scopo è consentire ai professionisti IT di creare e gestire un ambiente server moderno in modo simile a quello usato dagli sviluppatori di software per creare e gestire il codice delle applicazioni.

È possibile usare [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) per effettuare il provisioning delle applicazioni usando un modello dichiarativo. In un unico modello, è possibile distribuire più servizi con le relative dipendenze. Lo stesso modello viene usato per distribuire ripetutamente l'applicazione in ogni fase del ciclo di vita dell'applicazione.

**Procedura consigliata**: creare e distribuire automaticamente app Web o servizi cloud di Azure.  
**Dettagli**: È possibile configurare i progetti Azure DevOps [compilare e distribuire automaticamente](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) ad App web di Azure o servizi cloud. DevOps di Azure distribuisce automaticamente i file binari dopo una compilazione in Azure dopo ogni check-in codice. Il processo di compilazione del pacchetto equivale al comando Pacchetto di Visual Studio, mentre i passaggi per la pubblicazione equivalgono al comando Pubblica di Visual Studio.

**Procedura consigliata**: Automatizzare la gestione del rilascio.  
**Dettagli**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) è una soluzione per automatizzare la distribuzione in più fasi e la gestione del processo di rilascio. È possibile creare pipeline gestite di distribuzione continua per rilasciare versioni in modo rapido, semplice e frequente. Con Azure Pipelines è possibile automatizzare il processo di rilascio e definire flussi di lavoro predefiniti per l'approvazione. Sono supportate la distribuzione locale e nel cloud, l'estensione e la personalizzazione in base alle specifiche esigenze.

**Procedura consigliata**: verificare le prestazioni dell'app prima di avviarla o distribuire aggiornamenti nell'ambiente di produzione.  
**Dettagli**: Esecuzione basata sul cloud [test di carico](https://docs.microsoft.com/azure/devops/test/load-test/overview.md?view=azure-devops#alternatives) per:

- Individuare problemi di prestazioni nell'app.
- Migliorare la qualità della distribuzione.
- Assicurarsi che l'app sia sempre disponibile.
- Assicurarsi che l'app possa gestire il traffico per la prossima campagna di lancio o di marketing.

[Apache JMeter](https://jmeter.apache.org/) è uno strumento gratuito, popolari open source con una forte community di backup.

**Procedura consigliata**: monitorare le prestazioni dell'applicazione.  
**Dettagli**: [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) è un servizio estendibile di gestione delle prestazioni delle applicazioni per sviluppatori Web su più piattaforme. È possibile usare Application Insights per monitorare l'applicazione Web mentre è in esecuzione. Il servizio rileva automaticamente le anomalie nelle prestazioni e include strumenti di analisi che consentono di diagnosticare i problemi e di conoscere come viene effettivamente usata l'app dagli utenti. Il servizio è progettato per supportare il miglioramento continuo delle prestazioni e dell'usabilità.

## <a name="mitigate-and-protect-against-ddos"></a>Attenuazione e protezione da attacchi Distributed Denial of Service (DDoS)
Il Distributed Denial of Service (DDoS) è un tipo di attacco che tenta di esaurire le risorse dell'applicazione. L'obiettivo è compromettere la disponibilità e la capacità dell'applicazione di gestire richieste legittime. Gli attacchi stanno diventando più sofisticati con dimensioni e impatto maggiori. Possono avere come obiettivo qualsiasi endpoint che è raggiungibile pubblicamente tramite Internet.

La progettazione e la creazione per la resilienza agli attacchi Distributed Denial of Service (DDoS) richiedono pianificazione e progettazione per un'ampia gamma di modalità di errore. Di seguito vengono descritte le procedure consigliate per la creazione di servizi resilienti a DDoS in Azure.

**Procedura consigliata**: Garantire la sicurezza durante l'intero ciclo di vita di un'applicazione, dalla progettazione e implementazione alla distribuzione e alle operazioni. Le applicazioni possono contenere bug che consentono a un volume relativamente basso di richieste di usare una quantità elevata di risorse, provocando un'interruzione del servizio.  
**Dettagli**: Per proteggere un servizio in esecuzione in Microsoft Azure, i clienti devono avere una buona conoscenza dell'architettura delle applicazioni e concentrarsi sui [cinque punti chiave della qualità del software](https://docs.microsoft.com/azure/architecture/guide/pillars). I clienti devono conoscere i volumi di traffico tipici, il modello di connettività tra l'applicazione e le altre applicazioni e gli endpoint di servizio esposti a Internet pubblico.

Garantire che un'applicazione sia abbastanza resiliente da riuscire a gestire un attacco Denial of Service destinato all'applicazione stessa è di fondamentale importanza. Sicurezza e privacy sono integrate direttamente nella piattaforma di Azure, a partire dal processo [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl). SDL si rivolge alla sicurezza in ogni fase di sviluppo e assicura che Azure sia continuamente aggiornato per renderlo ancora più sicuro.

**Procedura consigliata**: progettare le applicazioni con [scalabilità orizzontale](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) per soddisfare la richiesta di un carico amplificato, in particolare in caso di attacco DDoS. Se l'applicazione dipende da una singola istanza di un servizio, crea un singolo punto di errore. Il provisioning di più istanze rende il sistema più resiliente e scalabile.  
**Dettagli**: Per [Servizio app di Azure](../app-service/app-service-value-prop-what-is.md) selezionare un [piano di servizio app](../app-service/overview-hosting-plans.md) che offra più istanze.

Per Servizi cloud di Azure, configurare ognuno dei ruoli in modo da usare [più istanze](../cloud-services/cloud-services-choose-me.md).

Per [Macchine virtuali di Microsoft Azure](../virtual-machines/windows/overview.md), verificare che l'architettura di VM includa più macchine virtuali e che ogni macchina virtuale sia inclusa in un [set di disponibilità](../virtual-machines/virtual-machines-windows-manage-availability.md). Si consiglia di usare set di scalabilità di macchine virtuali per le funzionalità di scalabilità automatica.

**Procedura consigliata**: La disposizione delle difese su più livelli in un'applicazione riduce le probabilità di riuscita degli attacchi. Implementare progettazioni sicure per le applicazioni tramite le funzionalità integrate della piattaforma di Azure.  
**Dettagli**: il rischio di attacco aumenta con le dimensioni (superficie di attacco) dell'applicazione. È possibile ridurre la superficie usando l'elenco degli elementi consentiti per chiudere lo spazio indirizzi IP esposto e le porte in ascolto non necessarie sui servizi di bilanciamento del carico ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) e [gateway applicazione di Azure](../application-gateway/application-gateway-create-probe-portal.md)).

I [gruppi di sicurezza di rete](../virtual-network/security-overview.md) rappresentano un altro modo per ridurre la superficie di attacco. È possibile usare [tag di servizio](../virtual-network/security-overview.md#service-tags) e [gruppi di sicurezza dell'applicazione](../virtual-network/security-overview.md#application-security-groups) per ridurre la complessità per la creazione della regola di sicurezza e configurare la sicurezza di rete come estensione naturale della struttura di un'applicazione.

Distribuire i servizi di Azure in una [rete virtuale](../virtual-network/virtual-networks-overview.md), laddove possibile. Ciò consente alle risorse del servizio di comunicare attraverso indirizzi IP privati. Il traffico del servizio di Azure da una rete virtuale usa indirizzi IP pubblici come indirizzi IP di origine per impostazione predefinita.

Tramite gli [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) il traffico del servizio passa all'uso di indirizzi privati della rete virtuale come indirizzi IP di origine per l'accesso al servizio di Azure dalla rete virtuale.

Le risorse locali dei clienti che vengono spesso attaccate insieme alle risorse in Azure. Se si connette un ambiente locale ad Azure, ridurre al minimo l'esposizione delle risorse locali alla rete Internet pubblica.

Azure prevede due [offerte di servizio](../virtual-network/ddos-protection-overview.md) contro gli attacchi DDoS per la protezione della rete:

- La protezione di base è integrata in Azure per impostazione predefinita senza costi aggiuntivi. La scala e la capacità complete della rete globalmente distribuita di Azure forniscono una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio costante del traffico e la mitigazione in tempo reale. La protezione di base non richiede alcuna modifica della configurazione utente o dell'applicazione e aiuta a proteggere tutti i servizi di Azure, inclusi i servizi PaaS come DNS di Azure.
- La protezione standard offre funzionalità avanzate di attenuazione degli attacchi DDoS contro gli attacchi alla rete e viene ottimizzata automaticamente per proteggere le specifiche risorse di Azure. La protezione è semplice da abilitare durante la creazione di reti virtuali. Può essere abilitata anche dopo la creazione e non richiede alcuna modifica delle applicazioni o delle risorse.

## <a name="enable-azure-policy"></a>Abilita criteri di Azure
[Criteri di Azure](../governance/policy/overview.md) è un servizio di Azure che consente di creare, assegnare e gestire i criteri. Questi criteri applicano regole e azioni in relazione alle risorse, in modo che le risorse rimangano conformi con gli standard aziendali e i contratti di servizio. Criteri di Azure soddisfa questa esigenza valutando le risorse per la mancata conformità con i criteri assegnati.

Abilitare criteri di Azure per monitorare e applicare i criteri dell'organizzazione scritto. In questo modo i requisiti di sicurezza normativi o di conformità con l'azienda dalla gestione centralizzata dei criteri di sicurezza sui carichi di lavoro cloud ibridi. Informazioni su come [creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md). Visualizzare [struttura delle definizioni di criteri di Azure](../governance/policy/concepts/definition-structure.md) per una panoramica degli elementi di un criterio.

Ecco alcune le procedure consigliate da seguire dopo l'implementazione di criteri di Azure:

**Procedura consigliata**: Criterio supporta diversi tipi di effetti. Per ulteriori informazioni, vedere [struttura delle definizioni di criteri di Azure](../governance/policy/concepts/definition-structure.md#policy-rule). Operazioni aziendali possono essere influenzate negativamente dal **negare** effetto e il **monitora e aggiorna** effetto, pertanto, iniziare con il **audit** effetto per limitare il rischio di impatto negativo da criteri.   
**Dettagli**: [Avviare le distribuzioni di criteri in modalità di controllo](../governance/policy/concepts/definition-structure.md#policy-rule) e quindi procedere con versioni successive **negare** oppure **monitora e aggiorna**. Testare ed esaminare i risultati dell'effetto di controllo prima di spostare **negare** oppure **correggere**.

Per altre informazioni, vedere [creare e gestire i criteri per applicare la conformità](../governance/policy/tutorials/create-and-manage.md).

**Procedura consigliata**: Identificare i ruoli di responsabili del monitoraggio per le violazioni dei criteri e garantendo l'azione di correzione a destra viene eseguita rapidamente.   
**Dettagli**: Dispone di monitorare la conformità al ruolo assegnato tramite la [portale di Azure](../governance/policy/how-to/get-compliance-data.md#portal) o tramite il [della riga di comando](../governance/policy/how-to/get-compliance-data.md#command-line).

**Procedura consigliata**: Criteri di Azure è una rappresentazione di tecnica di criteri scritti di un'organizzazione. Eseguire il mapping di tutti i criteri di Azure per i criteri dell'organizzazione per ridurre la confusione e aumentare la coerenza.   
**Dettagli**: Mapping di documenti nella documentazione della propria organizzazione o nel criterio Azure stesso aggiungendo un riferimento ai criteri dell'organizzazione nel portale di Azure [descrizione del criterio](../governance/policy/concepts/definition-structure.md#display-name-and-description) o i criteri di Azure [iniziativa](../governance/policy/concepts/definition-structure.md#initiatives) Descrizione.

## <a name="monitor-azure-ad-risk-reports"></a>Report di rischio di monitoraggio di Azure AD
La maggior parte delle violazioni della sicurezza si verifica quando utenti malintenzionati ottengono l'accesso a un ambiente impadronendosi dell'identità di un utente. Trovare le identità compromesse non è un compito facile. Azure AD usa l'euristica e gli algoritmi adattivi di Machine Learning per rilevare azioni sospette correlate agli account dell'utente. Ogni azione sospetta rilevata viene archiviata in un record detto [evento di rischio](../active-directory/reports-monitoring/concept-risk-events.md). Vengono registrati gli eventi di rischio nella sicurezza di Azure AD i report. Per altre informazioni, vedere la [report utenti a rischio security](../active-directory/reports-monitoring/concept-user-at-risk.md) e il [report di sicurezza di accessi a rischio](../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Passaggi successivi
Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](security-best-practices-and-patterns.md).

Le risorse seguenti offrono altre informazioni più generali sulla sicurezza di Azure e sui servizi Microsoft correlati:
* [Blog del team di sicurezza di Azure](https://blogs.msdn.microsoft.com/azuresecurity/): per informazioni aggiornate sulla sicurezza in Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx): consente di segnalare le vulnerabilità della sicurezza di Microsoft, inclusi i problemi relativi ad Azure, tramite posta elettronica all'indirizzo secure@microsoft.com
