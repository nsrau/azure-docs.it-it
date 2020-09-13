---
title: Distribuire Monitoraggio di Azure
description: Vengono descritti i diversi passaggi necessari per un'implementazione completa di monitoraggio di Azure per monitorare tutte le risorse nella sottoscrizione di Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 0a5c788b4429b5048a1b94fa8adfb2d9367982da
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033473"
---
# <a name="deploy-azure-monitor"></a>Distribuire Monitoraggio di Azure
L'abilitazione di monitoraggio di Azure per monitorare tutte le risorse di Azure è una combinazione di configurazione dei componenti di monitoraggio di Azure e configurazione delle risorse di Azure per generare dati di monitoraggio per la raccolta di monitoraggio di Azure. Questo articolo descrive i diversi passaggi necessari per un'implementazione completa di monitoraggio di Azure con una configurazione comune per monitorare tutte le risorse nella sottoscrizione di Azure. Le descrizioni di base per ogni passaggio sono fornite con collegamenti ad altra documentazione per informazioni dettagliate sui requisiti di configurazione.

> [!IMPORTANT]
> Le funzionalità di monitoraggio di Azure e la relativa configurazione variano in base ai requisiti aziendali, bilanciati con il costo delle funzionalità abilitate. Ogni passaggio seguente indica se è previsto un costo potenziale ed è necessario valutare questi costi prima di procedere con questo passaggio. Per informazioni complete sui prezzi, vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="configuration-goals"></a>Obiettivi di configurazione
L'obiettivo di un'implementazione completa di monitoraggio di Azure consiste nel raccogliere tutti i dati disponibili da tutte le risorse e le applicazioni cloud e abilitare il maggior numero possibile di funzionalità in monitoraggio di Azure in base a tali dati.

I dati raccolti da monitoraggio di Azure vengono inviati alle [metriche di monitoraggio](platform/data-platform-metrics.md) di Azure o ai log di monitoraggio di [Azure](platform/data-platform-logs.md). In ogni vengono archiviati tipi diversi di dati e vengono abilitati diversi tipi di analisi e di avviso. Per una descrizione dei diversi tipi di avviso, vedere [confrontare le metriche e i log di monitoraggio di Azure](platform/data-platform.md) per un confronto tra i due e la [Panoramica degli avvisi in Microsoft Azure](platform/alerts-overview.md) . 

Alcuni dati possono essere inviati a metriche e log per poter essere usati con diverse funzionalità. In questi casi, potrebbe essere necessario configurare ognuno separatamente. I dati delle metriche, ad esempio, vengono inviati automaticamente dalle risorse di Azure alle metriche, che supportano Esplora metriche e gli avvisi delle metriche. È necessario creare un'impostazione di diagnostica per ogni risorsa per inviare gli stessi dati delle metriche ai log, consentendo di analizzare le tendenze delle prestazioni con altri dati di log con Log Analytics. Le sezioni seguenti identificano la posizione in cui vengono inviati i dati e includono ogni passaggio necessario per inviare i dati a tutte le posizioni possibili.

È possibile che si disponga di requisiti aggiuntivi, ad esempio il monitoraggio di risorse esterne ad Azure e l'invio di dati all'esterno di monitoraggio di Azure. È possibile ottenere requisiti come questi con la configurazione aggiuntiva delle funzionalità descritte in questo articolo. Per altre opzioni di configurazione, seguire i collegamenti alla documentazione in ogni passaggio.

## <a name="collect-data-from-azure-resources"></a>Raccogliere dati dalle risorse di Azure

> [!NOTE]
> Per una guida completa sul monitoraggio delle macchine virtuali con monitoraggio di Azure, vedere [monitoraggio delle risorse di Azure con](insights/monitor-azure-resource.md) monitoraggio di Azure.

Il monitoraggio delle risorse di Azure è disponibile automaticamente senza che sia necessaria alcuna configurazione, mentre è necessario eseguire i passaggi di configurazione per raccogliere dati di monitoraggio aggiuntivi. La tabella seguente illustra i passaggi di configurazione necessari per raccogliere tutti i dati disponibili dalle risorse di Azure, tra cui i dati del passaggio inviati alle metriche di monitoraggio di Azure e ai log di monitoraggio di Azure. Le sezioni seguenti descrivono ogni passaggio in modo più dettagliato.

[![Distribuire il monitoraggio ](media/deploy/deploy-azure-resources.png) delle risorse di Azure](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>Nessuna configurazione
Le funzionalità seguenti di monitoraggio di Azure sono abilitate senza alcuna configurazione necessaria quando si crea una sottoscrizione di Azure. Al monitoraggio non è associato alcun costo.

[Log Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) : fornisce la cronologia a livello di tenant per l'attività di accesso e audit trail di modifiche apportate in Azure Active Directory. Vedere i [report delle attività di controllo nel portale di Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) e i report delle attività di [accesso nel portale di Azure Active Directory](../active-directory/reports-monitoring/concept-sign-ins.md) per informazioni dettagliate sui log di Azure Active Directory e su come visualizzarli nella portale di Azure.

[Log attività](platform/platform-logs-overview.md) : fornisce informazioni sul gruppo di gestione e sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Gli eventi vengono scritti automaticamente nel log attività quando si crea una nuova risorsa di Azure, si modifica una risorsa o si eseguono attività significative. È possibile visualizzare gli eventi nel portale di Azure e creare gli avvisi del log attività quando vengono creati eventi specifici. Vedere [log attività di Azure](platform/activity-log.md) per informazioni dettagliate sul log attività e come visualizzarlo nella portale di Azure.

[Metriche della piattaforma](platform/metrics-supported.md) : raccolte automaticamente dai servizi di Azure nelle [metriche di monitoraggio di Azure](platform/data-platform-metrics.md). Questi dati vengono spesso presentati nella pagina **Panoramica** nel portale di Azure per i diversi servizi. Per informazioni dettagliate sull'analisi delle metriche della piattaforma nell'portale di Azure, vedere [Introduzione ad Azure Esplora metriche](platform/metrics-getting-started.md) . 


### <a name="create-log-analytics-workspace"></a>Creare un'area di lavoro Log Analytics
È necessario almeno un'area di lavoro di Log Analytics per abilitare i [log di monitoraggio di Azure](platform/data-platform-logs.md), necessaria per la raccolta di tali dati come log dalle risorse di Azure, la raccolta di dati dal sistema operativo guest di macchine virtuali di Azure e la maggior parte delle informazioni dettagliate di monitoraggio di Azure. Altri servizi, ad esempio Azure Sentinel e il Centro sicurezza di Azure, usano anche un'area di lavoro Log Analytics e possono condividere lo stesso valore usato per monitoraggio di Azure. È possibile iniziare con un'unica area di lavoro per supportare questo monitoraggio, ma vedere  [progettazione della distribuzione dei log di monitoraggio di Azure](platform/design-logs-deployment.md) per indicazioni su quando usare più aree di lavoro.

Non è previsto alcun costo per la creazione di un'area di lavoro Log Analytics, ma è possibile che si verifichi un addebito quando si configurano i dati da raccogliere al suo interno. Per informazioni dettagliate, vedere [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](platform/manage-cost-storage.md).  

Per creare un'area di lavoro Log Analytics iniziale, vedere [creare un'area di lavoro log Analytics nel portale di Azure](learn/quick-create-workspace.md) . Per configurare l'accesso, vedere [gestire l'accesso ai dati di log e alle aree di lavoro in monitoraggio di Azure](platform/manage-access.md) . 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>Creare un'impostazione di diagnostica per raccogliere i log dei tenant e delle sottoscrizioni
Mentre i [log di Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) per il tenant e il [log attività](platform/platform-logs-overview.md) per la sottoscrizione vengono raccolti automaticamente, inviarli a un'area di lavoro log Analytics consente di analizzare questi eventi con altri dati di log usando le query di log in log Analytics. Questo consente anche di creare avvisi di query di log, che rappresentano l'unico modo per avvertire i log Azure Active Directory e fornire una logica più complessa rispetto agli avvisi del log attività.

Non è previsto alcun costo per l'invio del log attività a un'area di lavoro, ma è previsto un addebito per l'inserimento e la conservazione dei dati per i log Azure Active Directory. 

Vedere [integrare Azure ad log con i log di monitoraggio di Azure](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) e [creare le impostazioni di diagnostica per inviare le metriche e i log della piattaforma a destinazioni diverse](platform/diagnostic-settings.md) per creare un'impostazione di diagnostica per il tenant e la sottoscrizione per inviare le voci di log all'area di lavoro di log Analytics. 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>Creare un'impostazione di diagnostica per raccogliere i log delle risorse e le metriche della piattaforma
Le risorse in Azure generano automaticamente i [log delle risorse](platform/platform-logs-overview.md) che forniscono informazioni dettagliate sulle operazioni eseguite all'interno della risorsa. Diversamente dalle metriche della piattaforma, tuttavia, è necessario configurare i log delle risorse da raccogliere. Creare un'impostazione di diagnostica per inviarli a un'area di lavoro di Log Analytics per combinarli con gli altri dati usati con i log di monitoraggio di Azure. La stessa impostazione di diagnostica può essere usata anche per inviare le metriche della piattaforma per la maggior parte delle risorse alla stessa area di lavoro, consentendo di analizzare i dati delle metriche usando le query di log con altri dati raccolti.

Per questa raccolta è previsto un costo, quindi fare riferimento ai [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) prima dell'implementazione in un numero significativo di risorse. Vedere anche [gestire l'utilizzo e i costi con i log di monitoraggio di Azure](platform/manage-cost-storage.md) per informazioni dettagliate sull'ottimizzazione del costo della raccolta di log.

Vedere [creare un'impostazione di diagnostica per raccogliere i log delle risorse e le metriche in Azure](platform/diagnostic-settings.md#create-in-azure-portal) per creare un'impostazione di diagnostica per una risorsa di Azure. Poiché è necessario creare un'impostazione di diagnostica per ogni risorsa di Azure, vedere [distribuire monitoraggio di Azure su larga scala](deploy-scale.md) per informazioni dettagliate sull'uso di [criteri di Azure](../governance/policy/overview.md) per creare automaticamente le impostazioni ogni volta che viene creata una risorsa di Azure.

### <a name="enable-insights-and-solutions"></a>Abilita informazioni dettagliate e soluzioni
Le informazioni e le soluzioni forniscono il monitoraggio specializzato per un servizio o una soluzione particolare. Le informazioni dettagliate usano le funzionalità più recenti di monitoraggio di Azure, ad esempio le cartelle di lavoro, per cui è necessario usare un'analisi se disponibile per il servizio. Sono automaticamente disponibili in ogni sottoscrizione di Azure, ma possono richiedere una configurazione per la funzionalità completa. Usano in genere i registri di risorse e le metriche della piattaforma precedentemente configurati e potrebbero raccogliere dati aggiuntivi.

È necessario aggiungere le soluzioni a ogni sottoscrizione e usare esclusivamente i dati dei log di monitoraggio di Azure e possono raccogliere dati di log aggiuntivi.

Non sono previsti costi per le soluzioni o le informazioni, ma è possibile che vengano addebitati tutti i dati raccolti.

Vedere [che cosa viene monitorato da monitoraggio di Azure?](monitor-reference.md) per un elenco di informazioni dettagliate e soluzioni disponibili in monitoraggio di Azure. Per una configurazione univoca o per informazioni sui prezzi, vedere la documentazione relativa a ognuno di essi. 

## <a name="collect-data-from-virtual-machines"></a>Raccogliere dati da macchine virtuali

> [!NOTE]
> Per una guida completa sul monitoraggio delle macchine virtuali con monitoraggio di Azure, vedere [monitoraggio delle macchine virtuali di Azure con](insights/monitor-vm-azure.md) monitoraggio di Azure. 

Le macchine virtuali generano dati simili come altre risorse di Azure, ma è necessario un agente per raccogliere dati dal sistema operativo guest. Per un confronto degli agenti usati da monitoraggio di Azure, vedere [Panoramica degli agenti di monitoraggio di Azure](platform/agents-overview.md) . 

[Monitoraggio di Azure per le macchine virtuali](insights/vminsights-overview.md) usa l'agente di log Analytics e l'agente di dipendenza per raccogliere dati dal sistema operativo guest delle macchine virtuali, in modo che sia possibile distribuire questi agenti come parte dell'implementazione di questa analisi. Ciò consente all'agente di Log Analytics per altri servizi che lo usano, ad esempio il Centro sicurezza di Azure.


[![Distribuire una macchina virtuale ](media/deploy/deploy-azure-vm.png) di Azure](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-azure-monitor-for-vms"></a>Configura area di lavoro per Monitoraggio di Azure per le macchine virtuali
Monitoraggio di Azure per le macchine virtuali richiede un'area di lavoro Log Analytics che in genere corrisponde a quella creata per raccogliere dati da altre risorse di Azure. Prima di abilitare le macchine virtuali, è necessario aggiungere la soluzione necessaria per Monitoraggio di Azure per le macchine virtuali all'area di lavoro.

Per informazioni dettagliate sulla configurazione dell'area di lavoro Log Analytics per Monitoraggio di Azure per le macchine virtuali, vedere [configurare l'area di lavoro di log Analytics per monitoraggio di Azure per le macchine virtuali](insights/vminsights-configure-workspace.md) .

### <a name="enable-azure-monitor-for-vms-on-each-virtual-machine"></a>Abilita Monitoraggio di Azure per le macchine virtuali in ogni macchina virtuale
Una volta configurata un'area di lavoro, è possibile abilitare ogni macchina virtuale installando l'agente di Log Analytics e l'agente di dipendenza. Sono disponibili diversi metodi per l'installazione di questi agenti, inclusi i criteri di Azure, che consentono di configurare automaticamente ogni macchina virtuale creata. I dati sulle prestazioni e i dettagli del processo raccolti da Monitoraggio di Azure per le macchine virtuali vengono archiviati nei log di monitoraggio di Azure.

Vedere [abilitare monitoraggio di Azure per le macchine virtuali Panoramica](insights/vminsights-enable-overview.md) per le opzioni per distribuire gli agenti nelle macchine virtuali e abilitarli per il monitoraggio.

### <a name="configure-workspace-to-collect-events"></a>Configura area di lavoro per la raccolta di eventi
Monitoraggio di Azure per le macchine virtuali raccoglierà i dati sulle prestazioni e i dettagli e le dipendenze dei processi dal sistema operativo guest di ogni macchina virtuale. L'agente di Log Analytics può inoltre raccogliere log dal Guest, incluso il registro eventi di Windows e syslog da Linux. Recupera la configurazione per questi log dall'area di lavoro Log Analytics a cui è connessa. È sufficiente configurare l'area di lavoro una sola volta e ogni volta che un agente si connette, scaricherà tutte le modifiche apportate alla configurazione. 

Per informazioni dettagliate sulla configurazione dell'area di lavoro di Log Analytics per la raccolta di dati aggiuntivi dalle macchine virtuali dell'agente, vedere [origini dati degli agenti in monitoraggio di Azure](platform/agent-data-sources.md) .

> [!NOTE]
> È inoltre possibile configurare l'area di lavoro per la raccolta dei contatori delle prestazioni, ma probabilmente sarà ridondante con i dati sulle prestazioni raccolti da Monitoraggio di Azure per le macchine virtuali. I dati sulle prestazioni raccolti dall'area di lavoro verranno archiviati nella tabella *Perf* , mentre i dati sulle prestazioni raccolti da monitoraggio di Azure per le macchine virtuali vengono archiviati nella tabella *InsightsMetrics* . Configurare la raccolta delle prestazioni nell'area di lavoro solo se sono necessari contatori che non sono già raccolti da Monitoraggio di Azure per le macchine virtuali.

### <a name="diagnostic-extension-and-telegraf-agent"></a>Estensione di diagnostica e agente Telegraf
Monitoraggio di Azure per le macchine virtuali usa l'agente di Log Analytics che invia i dati sulle prestazioni a un'area di lavoro Log Analytics ma non alle metriche di monitoraggio di Azure. L'invio di questi dati alle metriche consente di analizzarli con Esplora metriche e di usarli con gli avvisi delle metriche. Questa operazione richiede l'estensione di diagnostica in Windows e l'agente Telegraf in Linux.

Per informazioni dettagliate sull'installazione e la configurazione di questi agenti, vedere [installare e configurare l'estensione diagnostica di Microsoft Azure (WAD)](platform/diagnostics-extension-windows-install.md) e [raccogliere metriche personalizzate per una VM Linux con l'agente Telegraf InfluxData](platform/collect-custom-metrics-linux-telegraf.md) .


## <a name="monitor-applications"></a>Monitorare le applicazioni
Monitoraggio di Azure monitora le applicazioni personalizzate usando [Application Insights](app/app-insights-overview.md), che è necessario configurare per ogni applicazione che si vuole monitorare. Il processo di configurazione può variare a seconda del tipo di applicazione monitorato e del tipo di monitoraggio che si desidera eseguire. I dati raccolti da Application Insights vengono archiviati nelle metriche di monitoraggio di Azure, nei log di monitoraggio di Azure e nell'archiviazione BLOB di Azure, a seconda della funzionalità. I dati sulle prestazioni vengono archiviati sia nelle metriche di monitoraggio di Azure che nei log di monitoraggio di Azure, senza che sia necessaria alcuna configurazione aggiuntiva.

### <a name="create-an-application-resource"></a>Creare una risorsa dell'applicazione
È necessario creare una risorsa in Application Insights per ogni applicazione che si intende monitorare. I dati di log raccolti da Application Insights vengono archiviati nei log di monitoraggio di Azure per un'applicazione basata sull'area di lavoro. I dati di log per le applicazioni classiche vengono archiviati separatamente dall'area di lavoro Log Analytics come descritto in [struttura dei dati](platform/data-platform-logs.md#structure-of-data).

 Quando si crea l'applicazione, è necessario scegliere se usare il modello classico o quello basato su area di lavoro. Per creare un'applicazione classica, vedere [creare una risorsa Application Insights](app/create-new-resource.md) . Per creare un'applicazione basata su area di lavoro, vedere [risorse Application Insights basate sull'area di lavoro (anteprima)](app/create-workspace-resource.md) .

### <a name="configure-codeless-or-code-based-monitoring"></a>Configurare il monitoraggio basato sul codice o sul codice
Per abilitare il monitoraggio per un'applicazione, è necessario decidere se utilizzare il monitoraggio basato sul codice o sul codice. Il processo di configurazione varia in base a questa decisione e al tipo di applicazione che si intende monitorare.

Il **monitoraggio non codificato** è più semplice da implementare e può essere configurato dopo lo sviluppo del codice. Non richiede aggiornamenti al codice. Vedere le risorse seguenti per informazioni dettagliate sull'abilitazione del monitoraggio in base all'applicazione.

- [Applicazioni ospitate in app Web di Azure](app/azure-web-apps.md)
- [Applicazioni Java](app/java-in-process-agent.md)
- [Applicazioni ASP.NET ospitate in IIS in macchine virtuali di Azure o set di scalabilità di macchine virtuali di Azure](app/azure-vm-vmss-apps.md)
- [Applicazioni ASP.NET ospitate in IIS in macchine virtuali locali](app/monitor-performance-live-website-now.md)


Il **monitoraggio basato sul codice** è più personalizzabile e raccoglie dati di telemetria aggiuntivi, ma richiede l'aggiunta di una dipendenza al codice nei pacchetti NuGet di Application Insights SDK. Per informazioni dettagliate sull'abilitazione del monitoraggio in base all'applicazione, vedere le risorse seguenti.

- [Applicazioni ASP.NET](app/asp-net.md)
- [Applicazioni ASP.NET Core](app/asp-net-core.md)
- [Applicazioni console .NET](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Altre piattaforme](app/platforms.md)

### <a name="configure-availability-testing"></a>Configurare i test di disponibilità
I test di disponibilità in Application Insights sono test ricorrenti che monitorano la disponibilità e la velocità di risposta dell'applicazione a intervalli regolari da punti in tutto il mondo. È possibile creare un semplice test ping gratuitamente o creare una sequenza di richieste Web per simulare le transazioni utente a cui è associato il costo. 

Vedere [monitorare la disponibilità di un sito Web per un](app/monitor-web-app-availability.md) Riepilogo dei diversi tipi di test e dettagli sulla relativa creazione.

### <a name="configure-profiler"></a>Configurare il profiler
Profiler in Application Insights fornisce tracce delle prestazioni per le applicazioni .NET. Consente di identificare il percorso del codice "Hot" che richiede il tempo più lungo quando gestisce una determinata richiesta Web. Il processo di configurazione del profiler varia a seconda del tipo di applicazione. 

Per informazioni dettagliate sulla configurazione di Profiler, vedere [applicazioni di produzione di profili in Azure con Application Insights](app/profiler-overview.md) .

### <a name="configure-snapshot-debugger"></a>Configurare Snapshot Debugger
Snapshot Debugger in Application Insights monitora i dati di telemetria delle eccezioni dall'applicazione .NET e raccoglie gli snapshot sulle eccezioni generate in modo da ottenere le informazioni necessarie per diagnosticare i problemi nell'ambiente di produzione. Il processo per la configurazione di Snapshot Debugger varia a seconda del tipo di applicazione. 

Per informazioni dettagliate sulla configurazione di Snapshot Debugger, vedere snapshot di debug per le [eccezioni nelle app .NET](app/snapshot-debugger.md) .


## <a name="visualize-data"></a>Visualizzare i dati
Le informazioni e le soluzioni includono cartelle di lavoro e visualizzazioni personalizzate per l'analisi dei dati. Oltre a questi, è possibile creare [visualizzazioni](visualizations.md) personalizzate, incluse le cartelle di lavoro per i dati e i dashboard di monitoraggio di Azure, per combinare i dati di monitoraggio di Azure con i dati di altri servizi in Azure.


### <a name="create-workbooks"></a>Creazione di cartelle di lavoro
Le [cartelle di lavoro](platform/workbooks-overview.md) di monitoraggio di Azure consentono di creare report visivi avanzati nell'portale di Azure. È possibile combinare diversi set di dati dalle metriche di monitoraggio di Azure e dai log di monitoraggio di Azure per creare esperienze interattive unificate. È possibile accedere a una raccolta di cartelle di lavoro di nella scheda **cartelle di lavoro** del menu monitoraggio di Azure. 

Vedere [cartelle di lavoro di monitoraggio di Azure](platform/workbooks-overview.md) per informazioni dettagliate sulla creazione di cartelle di lavoro personalizzate.

### <a name="create-dashboards"></a>Creare i dashboard
I [dashboard di Azure](../azure-portal/azure-portal-dashboards.md) rappresentano la principale tecnologia di dashboard per Azure e consentono di combinare i dati di monitoraggio di Azure con i dati di altri servizi per offrire un unico riquadro di vetro nell'infrastruttura di Azure. Per informazioni dettagliate sulla creazione di un dashboard che includa i dati dei log di monitoraggio [di Azure, vedere creare e condividere i dashboard dei dati log Analytics](learn/tutorial-logs-dashboards.md) . 

Per informazioni dettagliate sulla creazione di un dashboard che includa i dati Application Insights, vedere [creare dashboard KPI personalizzati con applicazione Azure Insights](learn/tutorial-app-dashboards.md) . 

## <a name="alerts"></a>Avvisi
Gli avvisi in monitoraggio di Azure notificano in modo proattivo i dati o i modelli importanti identificati nei dati di monitoraggio. Alcune informazioni dettagliate genereranno avvisi senza configurazione. Per altri scenari, è necessario creare [regole di avviso](platform/alerts-overview.md) che includono i dati da analizzare e i criteri per la generazione di un avviso e i gruppi di azioni che definiscono l'azione da intraprendere quando viene generato un avviso. 


### <a name="create-action-groups"></a>Creare gruppi di azioni
I [gruppi di azioni](platform/action-groups.md) sono una raccolta di preferenze di notifica utilizzate dalle regole di avviso per determinare l'azione da eseguire quando viene generato un avviso. Esempi di azioni includono l'invio di un messaggio di posta elettronica o di un testo, la chiamata di un webhook o l'invio di dati a uno strumento ITSM. Ogni regola di avviso richiede almeno un gruppo di azioni e un singolo gruppo di azioni può essere utilizzato da più regole di avviso.

Per informazioni dettagliate sulla creazione di un gruppo di azioni e una descrizione delle diverse azioni che possono includere, vedere [creare e gestire gruppi di azioni nella portale di Azure](platform/action-groups.md) .


### <a name="create-alert-rules"></a>Creazione di regole di avviso
Esistono più tipi di regole di avviso definite dal tipo di dati che usano. Ognuna presenta funzionalità diverse e un costo diverso. La strategia di base da seguire consiste nell'usare il tipo di regola di avviso con il costo più basso che fornisce la logica necessaria.

- [Regole del log attività](platform/activity-log-alerts.md). Crea un avviso in risposta a un nuovo evento del log attività che corrisponde alle condizioni specificate. Questi avvisi non sono costosi, quindi dovrebbero essere la prima scelta. Per informazioni dettagliate sulla creazione di un avviso del log attività [, vedere creare, visualizzare e gestire gli avvisi del log attività usando monitoraggio di Azure](platform/alerts-activity-log.md) .
- [Regole di avviso della metrica](platform/alerts-metric-overview.md). Crea un avviso in risposta a uno o più valori di metrica che superano una soglia. Gli avvisi delle metriche sono con stato il significato che l'avviso verrà chiuso automaticamente quando il valore scende sotto la soglia e invierà notifiche solo quando lo stato cambia. È previsto un costo per gli avvisi di metrica, ma ciò è significativamente inferiore agli avvisi del log. Per informazioni dettagliate sulla creazione di un avviso di metrica [, vedere creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](platform/alerts-metric.md) .
- [Regole di avviso del log](platform/alerts-unified-log.md). Crea un avviso quando i risultati di una query di pianificazione corrispondono ai criteri specificati. Sono le più dispendiose delle regole di avviso, ma consentono i criteri più complessi. Per informazioni dettagliate sulla creazione di un avviso di query di log [, vedere creare, visualizzare e gestire gli avvisi di log con monitoraggio di Azure](platform/alerts-log.md) .
- Gli [avvisi delle applicazioni](app/monitor-web-app-availability.md) consentono di eseguire il test proattivo delle prestazioni e della disponibilità dell'applicazione Web. È possibile eseguire un semplice test ping senza costi aggiuntivi, ma sono previsti costi per un test più complesso. Vedere [monitorare la disponibilità di un sito Web](app/monitor-web-app-availability.md) per una descrizione dei diversi test e informazioni dettagliate sulla relativa creazione.


## <a name="next-steps"></a>Passaggi successivi

- Vedere [distribuire monitoraggio di Azure su larga scala usando criteri di Azure](deploy-scale.md).