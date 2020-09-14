---
title: Domande frequenti su Monitoraggio di Azure | Microsoft Docs
description: Risposte ad alcune domande frequenti sul servizio Monitoraggio di Azure.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/15/2020
ms.openlocfilehash: 4a5203a91ba528328dce7036d4690c6dea137c33
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054263"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Domande frequenti su Monitoraggio di Azure

La sezione Domande frequenti di Microsoft include un elenco di domande frequenti su Monitoraggio di Azure. Per eventuali domande aggiuntive, visitare il [Forum di discussione](https://docs.microsoft.com/answers/questions/topics/single/24223.html) e inserire le domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.


## <a name="general"></a>Generale

### <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
[Monitoraggio di Azure](overview.md) è un servizio di Azure che consente di monitorare le prestazioni e la disponibilità delle applicazioni e dei servizi in Azure, in altri ambienti cloud o in locale. Monitoraggio di Azure raccoglie i dati da più origini in una piattaforma dati comune dove possono essere analizzati per individuare tendenze e anomalie. Le funzionalità avanzate di Monitoraggio di Azure aiutano a identificare e a rispondere rapidamente a situazioni critiche che potrebbero influire sull'applicazione.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Qual è la differenza tra Monitoraggio di Azure, Log Analytics e Application Insights?
Nel settembre 2018, Microsoft ha combinato Monitoraggio di Azure, Log Analytics e Application Insights in un unico servizio per offrire un potente monitoraggio end-to-end delle applicazioni e dei componenti su cui si basano. Le funzionalità di Log Analytics e Application Insights non sono cambiate, anche se alcune di esse sono passate in Monitoraggio di Azure per riflettere meglio il nuovo ambito. Il motore dati di log e il linguaggio di query di Log Analytics sono ora definiti come log di Monitoraggio di Azure. Vedere [Aggiornamenti della terminologia di Monitoraggio di Azure](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Quanto costa Monitoraggio di Azure?
Le funzionalità di Monitoraggio di Azure che sono abilitate automaticamente, ad esempio la raccolta di metriche e i log attività, sono disponibili gratuitamente. Per altre funzionalità, ad esempio le query di log e gli avvisi, è previsto un costo. Per informazioni dettagliate sui prezzi, vedere la [pagina dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-enable-azure-monitor"></a>Come si abilita Monitoraggio di Azure?
Il servizio Monitoraggio di Azure viene abilitato nel momento in cui si crea una nuova sottoscrizione di Azure. Il [log attività](./platform/platform-logs-overview.md) e le [metriche](platform/data-platform-metrics.md) della piattaforma vengono raccolti automaticamente. È possibile creare [impostazioni di diagnostica](platform/diagnostic-settings.md) per raccogliere informazioni più dettagliate sull’utilizzo delle risorse di Azure e aggiungere [soluzioni di monitoraggio](insights/solutions.md) e [dati analitici](insights/insights-overview.md) per ottenere analisi aggiuntive dei dati raccolti per servizi specifici. 

### <a name="how-do-i-access-azure-monitor"></a>Come si accede a Monitoraggio di Azure?
A tutte le funzionalità e i dati di Monitoraggio di Azure si accede dal menu **Monitoraggio** nel portale di Azure. La sezione **Monitoraggio** del menu per diversi servizi di Azure consente di accedere agli stessi strumenti con i dati filtrati su una determinata risorsa. È possibile accedere ai dati di Monitoraggio di Azure anche per diversi scenari usando l'interfaccia della riga di comando, PowerShell e un'API REST.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Esiste una versione locale di Monitoraggio di Azure?
No. Monitoraggio di Azure è un servizio cloud scalabile che elabora e archivia grandi quantità di dati. È comunque in grado di monitorare le risorse in locale e in altri cloud.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Monitoraggio di Azure può monitorare le risorse locali?
Sì, oltre a raccogliere i dati di monitoraggio dalle risorse di Azure, Monitoraggio di Azure può raccoglierli da macchine virtuali e applicazioni che si trovano in altri cloud e in locale. Vedere [Origini dei dati di monitoraggio per Monitoraggio di Azure](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Monitoraggio di Azure si integra con System Center Operations Manager?
È possibile connettere il gruppo di gestione di System Center Operations Manager esistente a Monitoraggio di Azure per raccogliere i dati dagli agenti nei log di Monitoraggio di Azure. Si possono così usare la soluzione e le query di log per analizzare i dati raccolti dagli agenti. È anche possibile configurare gli agenti di System Center Operations Manager esistenti in modo che inviino i dati direttamente a Monitoraggio di Azure. Vedere [Connettere Operations Manager a Monitoraggio di Azure](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Quali indirizzi IP usa Monitoraggio di Azure?
Per un elenco degli indirizzi IP e di porte necessari agli agenti e ad altre risorse esterne per accedere a Monitoraggio di Azure, vedere [Indirizzi IP usati da Application Insights e Log Analytics](app/ip-addresses.md). 

## <a name="monitoring-data"></a>Dati di monitoraggio

### <a name="where-does-azure-monitor-get-its-data"></a>Da dove provengono i dati di Monitoraggio di Azure?
Monitoraggio di Azure raccoglie dati da un'ampia gamma di origini, tra cui log e metriche della piattaforma e delle risorse di Azure, applicazioni personalizzate e agenti in esecuzione su macchine virtuali. Altri servizi, quali Centro sicurezza di Azure e Network Watcher, raccolgono i dati in un’area di lavoro Log Analytics in modo che possano essere analizzati con i dati di Monitoraggio di Azure. È anche possibile inviare dati personalizzati a Monitoraggio di Azure usando l’API REST per i log o le metriche. Vedere [Origini dei dati di monitoraggio per Monitoraggio di Azure](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Che tipo di dati raccoglie Monitoraggio di Azure? 
Monitoraggio di Azure raccoglie i dati da un'ampia gamma di origini in [log](platform/data-platform-logs.md) o [metriche](platform/data-platform-metrics.md). Ciascun tipo di dati offre specifici vantaggi e ciascuno supporta un determinato set di funzionalità in Monitoraggio di Azure. Per ogni sottoscrizione di Azure è disponibile un singolo database di metriche. È anche possibile creare più aree di lavoro Log Analytics per raccogliere i log in base alle proprie esigenze. Vedere [Piattaforma dati di Monitoraggio di Azure](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Esiste una quantità massima di dati che è possibile raccogliere in Monitoraggio di Azure?
Non esiste alcun limite alla quantità di dati di metrica che è possibile raccogliere, ma tali dati restano archiviati per un massimo di 93 giorni. Vedere [Conservazione delle metriche](platform/data-platform-metrics.md#retention-of-metrics). Non esiste alcun limite alla quantità di dati di log che è possibile raccogliere, tuttavia potrebbe esistere un limite in base al piano tariffario che si sceglie per l'area di lavoro Log Analytics. Vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Come si accede ai dati raccolti da Monitoraggio di Azure?
I dati analitici e le soluzioni offrono un'esperienza personalizzata per utilizzare i dati archiviati in Monitoraggio di Azure. È possibile lavorare direttamente con i dati di log usando una query di log scritta nel linguaggio di query Kusto (KQL). Nel portale di Azure è possibile scrivere ed eseguire query e analizzare i dati in modo interattivo usando Log Analytics. È possibile analizzare le metriche nel portale di Azure con Esplora metriche. Vedere [Analizzare i dati di log in Monitoraggio di Azure](log-query/log-query-overview.md) e [Introduzione a Esplora metriche di Azure](platform/metrics-getting-started.md).

## <a name="solutions-and-insights"></a>Soluzioni e dati analitici

### <a name="what-is-an-insight-in-azure-monitor"></a>Che cosa sono i dati analitici in Monitoraggio di Azure?
I dati analitici forniscono un'esperienza di monitoraggio personalizzata per determinati servizi di Azure. Usano le stesse metriche e gli stessi log di altre funzionalità di Monitoraggio di Azure, ma possono raccogliere dati aggiuntivi e offrire un'esperienza univoca nel portale di Azure. Vedere [Dati analitici in Monitoraggio di Azure](insights/insights-overview.md).

Per visualizzare i dati analitici nel portale di Azure, vedere la sezione **Informazioni dettagliate** del menu **Monitoraggio** o la sezione **Monitoraggio** del menu del servizio.

### <a name="what-is-a-solution-in-azure-monitor"></a>Che cos'è una soluzione di Monitoraggio di Azure?
Le soluzioni di monitoraggio sono set di logica compressi per il monitoraggio di un’applicazione o un servizio specifico basato sulle funzionalità di Monitoraggio di Azure. Le soluzioni raccolgono i dati di log in Monitoraggio di Azure e presentano viste e query di log per l'analisi usando un'esperienza comune nel portale di Azure. Vedere [Soluzioni di monitoraggio in Monitoraggio di Azure](insights/solutions.md).

Per visualizzare le soluzioni nel portale di Azure, fare clic su **Altro** nella sezione **Informazioni dettagliate** del menu **Monitoraggio**. Fare clic su **Aggiungi** per aggiungere ulteriori soluzioni all'area di lavoro.

## <a name="logs"></a>Log

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Qual è la differenza tra i log di Monitoraggio di Azure ed Esplora dati di Azure?
Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. I log di Monitoraggio di Azure si basano su Esplora dati di Azure e usano lo stesso linguaggio di query Kusto (KQL) con alcune differenze minime. Vedere [Differenze nel linguaggio di query di log di Monitoraggio di Azure](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Come si recuperano i dati di log?
Tutti i dati vengono recuperati da un'area di lavoro Log Analytics tramite una query di log scritta nel linguaggio di query Kusto (KQL). È possibile scrivere query personalizzate o usare soluzioni e informazioni dettagliate che includono query di log per un'applicazione o un servizio specifico. Vedere [Panoramica delle query di log in Monitoraggio di Azure](log-query/log-query-overview.md).

### <a name="can-i-delete-data-from-a-log-analytics-workspace"></a>È possibile eliminare dati da un'area di lavoro Log Analytics?
I dati vengono rimossi da un'area di lavoro in base al [periodo di conservazione](platform/manage-cost-storage.md#change-the-data-retention-period). È possibile eliminare dati specifici per motivi di privacy o di conformità. Per ulteriori informazioni [, vedere come esportare ed eliminare dati privati](platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) .


### <a name="what-is-a-log-analytics-workspace"></a>Che cos’è un'area di lavoro Log Analytics?
Tutti i dati di log raccolti da Monitoraggio di Azure vengono archiviati in un’area di lavoro Log Analytics. Un'area di lavoro è essenzialmente un contenitore in cui i dati di log vengono raccolti da diverse origini. È possibile avere un’unica area di lavoro Log Analytics per tutti i dati di monitoraggio oppure può essere necessario dotarsi di più aree di lavoro. Vedere [Progettazione della distribuzione dei log di Monitoraggio di Azure](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>È possibile spostare un'area di lavoro Log Analytics esistente in un'altra sottoscrizione di Azure?
È possibile spostare un'area di lavoro tra gruppi di risorse o sottoscrizioni, ma non in un'altra area di Azure. Vedere [Spostare un’area di lavoro Log Analytics in un’altra sottoscrizione o un altro gruppo di risorse](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Perché i pulsanti Esplora query e Salva non sono visibili in Log Analytics?

I pulsanti **Esplora query**, **Salva** e **Nuova regola di avviso** sono ora disponibili quando l’[ambito della query](log-query/scope.md) è impostato su una risorsa specifica. Per creare avvisi, salvare o caricare una query, Log Analytics deve avere come ambito un'area di lavoro. Per aprire Log Analytics nel contesto dell’area di lavoro, selezionare **Log** dal menu **Monitoraggio di Azure**. L'ultima area di lavoro utilizzata è selezionata, ma è possibile selezionare qualsiasi altra area di lavoro. Vedere [Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Perché viene visualizzato l'errore "Register resource provider 'Microsoft.Insights' for this subscription to enable this query" (Registrare il provider di risorse 'Microsoft.Insights' per questa sottoscrizione per abilitare questa query) quando si apre Log Analytics da una macchina virtuale? 
Molti provider di risorse sono registrati automaticamente. Tuttavia, alcuni potrebbero dover essere registrati manualmente. L'ambito per la registrazione è sempre la sottoscrizione. Per altre informazioni, vedere [Provider e tipi di risorse](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Perché non viene ricevuto alcun messaggio di errore di accesso quando si apre Log Analytics da una macchina virtuale? 
Per visualizzare i log della macchina virtuale, è necessario disporre dell'autorizzazione di lettura per le aree di lavoro in cui sono archiviati i log della macchina virtuale. In questi casi, l'amministratore deve concedere all'utente le autorizzazioni in Azure.

## <a name="metrics"></a>Metriche

### <a name="why-are-metrics-from-the-guest-os-of-my-azure-virtual-machine-not-showing-up-in-metrics-explorer"></a>Perché le metriche del sistema operativo guest della macchina virtuale di Azure non vengono visualizzate in Esplora metriche?
Le [metriche della piattaforma](insights/monitor-azure-resource.md#monitoring-data) vengono raccolte automaticamente per le risorse di Azure. Tuttavia, per raccogliere le metriche dal sistema operativo guest di una macchina virtuale, è necessario eseguire alcune configurazioni. Per una macchina virtuale Windows, installare l'estensione di diagnostica e configurare il sink di Monitoraggio di Azure come descritto in [Installare e configurare l'estensione diagnostica di Microsoft Azure (WAD)](platform/diagnostics-extension-windows-install.md). Per Linux, installare l'agente Telegraf come descritto in [Raccogliere metriche personalizzate per una VM Linux con l'agente InfluxData Telegraf](platform/collect-custom-metrics-linux-telegraf.md).

## <a name="alerts"></a>Avvisi

### <a name="what-is-an-alert-in-azure-monitor"></a>Che cos’è un avviso in Monitoraggio di Azure?
Gli avvisi notificano in modo proattivo quando vengono riscontrate importanti condizioni nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. Esistono tre tipologie di avvisi:

- Metrica: il valore della metrica supera una soglia.
- Query log: i risultati di una query di log corrispondono ai criteri definiti.
- Log attività: l'evento nel log attività corrisponde ai criteri definiti.
- Test Web: i risultati del test di disponibilità corrispondono ai criteri definiti.


Vedere [Panoramica degli avvisi in Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Che cos’è un gruppo di azioni?
Un gruppo di azioni è una raccolta di notifiche e di azioni che vengono attivate da un avviso. Più avvisi possono usare un singolo gruppo di azioni e consentire così di usufruire di set di notifiche e di azioni comuni. Vedere [Creare e gestire gruppi di azioni nel portale di Azure](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Che cos'è una regola di azione?
Una regola di azione consente di modificare il comportamento di un set di avvisi che soddisfano determinati criteri. Questo consente di eseguire tali requisiti come disabilitare le azioni di avviso durante una finestra di manutenzione. È anche possibile applicare un gruppo di azioni a un set di avvisi anziché applicarlo direttamente alle regole di avviso. Vedere [Regole di azione](platform/alerts-action-rules.md).

## <a name="agents"></a>Agenti

### <a name="does-azure-monitor-require-an-agent"></a>Monitoraggio di Azure necessita di un agente?
È necessario usare un agente solo per raccogliere i dati dal sistema operativo e dai carichi di lavoro nelle macchine virtuali. Le macchine virtuali possono trovarsi in Azure, in un altro ambiente cloud o in locale. Vedere [Panoramica degli agenti di Monitoraggio di Azure](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Qual è la differenza tra gli agenti di Monitoraggio di Azure?
L'estensione diagnostica di Azure è destinata alle macchine virtuali di Azure e raccoglie dati in Metrica di Monitoraggio di Azure, Archiviazione di Azure e Hub eventi di Azure. L'agente di Log Analytics è destinato alle macchine virtuali in Azure, un altro ambiente cloud o in locale e raccoglie i dati in Log di Monitoraggio di Azure. Dependency Agent necessita dell’agente di Log Analytics nonché delle dipendenze e dei dettagli dei processi raccolti. Vedere [Panoramica degli agenti di Monitoraggio di Azure](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Il traffico dell’agente usa la connessione ExpressRoute?
Il traffico verso Monitoraggio di Azure usa il circuito ExpressRoute di peering Microsoft. Per una descrizione dei diversi tipi di traffico ExpressRoute, vedere la [documentazione di ExpressRoute](../expressroute/expressroute-faqs.md#supported-services). 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Come è possibile verificare se l’agente di Log Analytics è in grado di comunicare con Monitoraggio di Azure?
Nel pannello di controllo del computer agente selezionare **impostazioni & sicurezza**, * * Microsoft Monitoring Agent. Nella scheda **Azure Log Analytics (OMS)** , la presenza di un’icona segno di spunta verde conferma che l’agente è in grado di comunicare con Monitoraggio di Azure. Un’icona di avviso gialla significa che l’agente incontra problemi nella comunicazione. Spesso il motivo è che il servizio **Microsoft Monitoring Agent** è stato arrestato. Usare Gestione controllo servizi per riavviare il servizio.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Come si arresta la comunicazione tra l’agente di Log Analytics e Monitoraggio di Azure?
Per gli agenti connessi direttamente a Log Analytics, aprire il Pannello di controllo e selezionare **Security & Settings** (Sicurezza e impostazioni), **Microsoft Monitoring Agent**. Nella scheda **Azure Log Analytics (OMS)** rimuovere tutte le aree di lavoro elencate. In System Center Operations Manager, rimuovere il computer dall'elenco di computer gestiti da Log Analytics. Operations Manager aggiorna la configurazione dell'agente affinché non invii altri report a Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Quanti dati vengono inviati per ogni agente?
La quantità di dati inviati per ciascun agente dipende da:

* le soluzioni abilitate
* il numero di log e di contatori delle prestazioni che vengono raccolti
* il volume di dati nei log

Per informazioni dettagliate, vedere [Gestire l'utilizzo e i costi con i log di Monitoraggio di Azure](platform/manage-cost-storage.md).

Per i computer che possono eseguire l'agente WireData, usare la query seguente per visualizzare la quantità di dati inviati:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Quanta larghezza di banda di rete viene usata da Microsoft Management Agent (MMA) durante l’invio di dati a Monitoraggio di Azure?
La larghezza di banda è una funzione della quantità di dati inviati. I dati vengono compressi quando vengono inviati in rete.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Come si fa a ricevere una notifica quando la raccolta di dati dall'agente di Log Analytics si interrompe?

Per ricevere una notifica quando la raccolta dati si interrompe, seguire la procedura descritta in [Creare un nuovo avviso del log](platform/alerts-metric.md). Utilizzare le impostazioni seguenti per la regola di avviso:

- **Definire la condizione dell’avviso**: Specificare l’area di lavoro Log Analytics interessata come destinazione di risorsa.
- **Criteri di avviso** 
   - **Nome segnale**: *Ricerca log personalizzata*
   - **Query di ricerca**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Logica avvisi**: **In base a** *Numero di risultati*, **Condizione** *Maggiore di*, **Valore soglia** *0*
   - **Valutata in base a**: **Periodo (in minuti)** *30*, **Frequenza (in minuti)** *10*
- **Definire i dettagli dell'avviso** 
   - **Name**: *Data collection stopped* (Raccolta dati interrotta)
   - **Gravità**: *Warning*

Specificare un [Gruppo di azioni](platform/action-groups.md) esistente o crearne uno nuovo, in modo che se l'avviso del log corrisponde ai criteri e sono assenti heartbeat per più di 15 minuti, si riceve una notifica.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Quali sono i requisiti del firewall per gli agenti di Monitoraggio di Azure?
Per informazioni dettagliate sui requisiti del firewall, vedere [Requisiti del firewall di rete](platform/log-analytics-agent.md#network-requirements).


## <a name="visualizations"></a>Visualizzazioni

### <a name="why-cant-i-see-view-designer"></a>Perché non si riesce a vedere Progettazione visualizzazioni?

Progettazione visualizzazioni è disponibile solo per gli utenti con autorizzazioni di collaboratore o superiori nell’area di lavoro Log Analytics.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Problemi di configurazione
*Problemi nella configurazione di:*

* [App .NET](app/asp-net-troubleshoot-no-data.md)
* [Monitoraggio di un'applicazione già in esecuzione](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostica di Azure](platform/diagnostics-extension-to-application-insights.md)
* [App Web Java](app/java-troubleshoot.md)

*Non sono disponibili dati dal server:*

* [Impostare le eccezioni del firewall](app/ip-addresses.md)
* [Configurare un server ASP.NET](app/monitor-performance-live-website-now.md)
* [Configurare un server Java](app/java-agent.md)

*Il numero di risorse Application Insights da distribuire:*

* [Come progettare la distribuzione di Application Insights: Una a molte risorse di Application Insights?](app/separate-resources.md)

### <a name="can-i-use-application-insights-with-"></a>Si può usare Application Insights con ...?

* [App Web in un server IIS in una macchina virtuale di Azure o in un set di scalabilità di macchine virtuali di Azure](app/azure-vm-vmss-apps.md)
* [App Web in un server IIS locale o in una macchina virtuale](app/asp-net.md)
* [App Web Java](app/java-get-started.md)
* [App Node.js](app/nodejs.md)
* [App Web in Azure](app/azure-web-apps.md)
* [Servizi cloud in Azure](app/cloudservices.md)
* [Server app eseguiti in Docker](app/docker.md)
* [App Web a pagina singola](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [App desktop Windows](app/windows-desktop.md)
* [Altre piattaforme](app/platforms.md)

### <a name="is-it-free"></a>È gratuito?

Sì, per l'uso sperimentale. Nel piano tariffario Basic l'applicazione può inviare una determinata quantità di dati gratuitamente ogni mese. La capacità massima disponibile è sufficiente per lo sviluppo e la pubblicazione di un'app per un numero ridotto di utenti. È possibile impostare un limite per evitare l'elaborazione di una quantità di dati maggiore a quella specificata.

I volumi più grandi di telemetria vengono addebitati per GB. Vedere alcuni suggerimenti su come [limitare gli addebiti](app/pricing.md).

Il piano Enterprise comporta un costo per ogni giorno in cui i singoli nodi del server Web inviano dati di telemetria. È appropriato se si vuole usare un'esportazione continua su larga scala.

[Leggere il piano tariffario](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Quanto costa?

* Aprire la pagina **Utilizzo e costi stimati** in una risorsa di Application Insights. È incluso un grafico dell'utilizzo recente. Se si vuole, è possibile impostare un limite di volume di dati.
* Aprire il [pannello Fatturazione di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) per visualizzare i costi di tutte le risorse.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Quali modifiche apporta Application Insights al progetto?
Dipende dal tipo di progetto. Per un'applicazione Web:

* Aggiunge tre file al progetto:
  * ApplicationInsights.config
  * ai.js
* Installa i pacchetti NuGet seguenti:
  * *Application Insights API* , ovvero l'API principale
  * *Application Insights API for Web Applications* , che consente di inviare i dati di telemetria dal server
  * *Application Insights API for JavaScript Applications* , che consente di inviare i dati di telemetria dal client
* I pacchetti includono gli assembly seguenti:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Inserire elementi in:
  * Web.config
  * packages.config
* Solo per i nuovi progetti. Se si [aggiunge Application Insights a un progetto esistente][start], l'operazione deve essere eseguita manualmente. Inserisce frammenti nel codice client e server per inizializzarli con l'ID risorsa di Application Insights. In un'app MVC, ad esempio, il codice viene inserito nella pagina master Views/Shared/\_Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>In che modo è possibile effettuare l'aggiornamento da versioni dell'SDK meno recenti?
Vedere le [note sulla versione](app/release-notes.md) dell'SDK appropriato per il tipo di applicazione.

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>In che modo è possibile modificare la risorsa di Azure che riceve i dati del progetto?
In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. È possibile inviare i dati a una risorsa nuova o esistente in Azure. L'aggiornamento guidato modifica la chiave di strumentazione in ApplicationInsights.config, che determina la destinazione dei dati inviati dall'SDK del server. A meno che non venga deselezionata l'opzione "Aggiorna tutto", modificherà anche la chiave in cui appare nelle pagine Web.

### <a name="can-i-use-providersmicrosoftinsights-componentsapiversions0-in-my-azure-resource-manager-deployments"></a>È possibile usare `providers('Microsoft.Insights', 'components').apiVersions[0]` nelle distribuzioni di Azure Resource Manager?

Non è consigliabile usare questo metodo per popolare la versione dell'API. La versione più recente può rappresentare versioni di anteprima che possono contenere modifiche di rilievo. Anche con le versioni non di anteprima più recenti, le versioni dell'API non sono sempre compatibili con le versioni precedenti dei modelli esistenti o in alcuni casi la versione dell'API potrebbe non essere disponibile per tutte le sottoscrizioni.

### <a name="what-is-status-monitor"></a>Che cos'è Status Monitor?

Un'app desktop che è possibile usare nel server Web IIS per configurare Application Insights nelle app Web. Non raccoglie dati di telemetria: è possibile interromperlo se non si sta configurando un'app. 

[Altre informazioni](app/monitor-performance-live-website-now.md#questions)

### <a name="what-telemetry-is-collected-by-application-insights"></a>Quali dati di telemetria vengono raccolti da Application Insights?

Da app Web del server:

* Richieste HTTP
* [Dipendenze](app/asp-net-dependencies.md). Chiamate a: database SQL, chiamate HTTP a servizi esterni, Azure Cosmos DB, tabelle, archiviazione BLOB e code. 
* [Eccezioni](app/asp-net-exceptions.md) e analisi dello stack.
* [Contatori delle prestazioni](app/performance-counters.md): se si usa [Status Monitor](app/monitor-performance-live-website-now.md), il [monitoraggio di Azure per Servizi app](app/azure-web-apps.md), il [monitoraggio di Azure per macchina virtuale o set di scalabilità di macchine virtuali](app/azure-vm-vmss-apps.md) o il [writer collectd di Application Insights](app/java-collectd.md).
* [Metrica ed eventi personalizzati](app/api-custom-events-metrics.md) codificati.
* [Log di traccia](app/asp-net-trace-logs.md) se si configura l'agente di raccolta appropriato.

Da [pagine Web dei client](app/javascript.md):

* [Conteggi delle visualizzazioni pagina](app/usage-overview.md)
* [Chiamate AJAX](app/asp-net-dependencies.md): richieste effettuate da uno script in esecuzione.
* Dati di carico della visualizzazione pagina
* Conteggi di utenti e sessioni
* [ID utente autenticato](app/api-custom-events-metrics.md#authenticated-users)

Da altre origini, se sono configurate:

* [Diagnostica di Azure](platform/diagnostics-extension-to-application-insights.md)
* [Dati di importazione in Analytics](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>È possibile filtrare o modificare alcuni dati di telemetria?

Sì, nel server è possibile scrivere:

* Un processore di telemetria per filtrare o aggiungere proprietà agli elementi di telemetria selezionati prima che vengano inviati dall'app.
* Un inizializzatore di telemetria per aggiungere proprietà a tutti gli elementi della telemetria.

Vedere altre informazioni per [ASP.NET](app/api-filtering-sampling.md) o [Java](app/java-filter-telemetry.md).

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Come vengono calcolati i dati su città, paesi, aree geografiche e altre località geografiche?

Viene cercato l'indirizzo IP (IPv4 o IPv6) del client Web tramite [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria del browser: viene raccolto l'indirizzo IP del mittente.
* Telemetria del server: viene raccolto l'indirizzo IP del client. L'indirizzo non viene raccolto se è impostato `X-Forwarded-For`.
* Per altre informazioni su come vengono raccolti i dati relativi all'indirizzo IP e alla georilevazione in Application Insights fare riferimento a questo [articolo](./app/ip-collection.md).


È possibile configurare `ClientIpHeaderTelemetryInitializer` per ottenere l'indirizzo IP da un'intestazione diversa. Ad esempio, in alcuni sistemi viene spostato da un proxy, da un bilanciamento del carico o da una rete CDN a `X-Originating-IP`. [Altre informazioni](https://apmtips.com/posts/2016-07-05-client-ip-address/)

È possibile [usare Power BI](app/export-power-bi.md ) per visualizzare i dati di telemetria della richiesta in una mappa.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Per quanto tempo vengono conservati i dati nel portale? Tale conservazione è sicura?
Vedere [Conservazione di dati e privacy][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Cosa accade ai dati di telemetria di Application Insight quando un server o un dispositivo perde la connessione con Azure?

Tutti gli SDK Microsoft, incluso SDK Web, includono il "trasporto affidabile". Quando il server o il dispositivo perde la connessione con Azure, i dati di telemetria vengono [archiviati localmente nel file system](./app/data-retention-privacy.md#does-the-sdk-create-temporary-local-storage) (SDK del server) o nell'archiviazione della sessione HTML5 (Web SDK). L'SDK proverà periodicamente a inviare i dati di telemetria finché il servizio di inserimento non li considererà "non aggiornati" (48 ore per i log, 30 minuti per le metriche). I dati di telemetria non aggiornati verranno eliminati. In alcuni casi, ad esempio quando la risorsa di archiviazione locale è piena, non verranno eseguiti nuovi tentativi.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>È possibile che le informazioni personali vengano inviate nei dati di telemetria?

Ciò si verifica se il codice invia tali dati. Può succedere anche se le variabili delle analisi dello stack includono informazioni personali. Il team di sviluppo deve eseguire attività di valutazione dei rischi per garantire che le informazioni personali vengano gestite in maniera appropriata. Vedere [altre informazioni sulla conservazione e privacy dei dati](app/data-retention-privacy.md).

**Tutti** gli ottetti dell'indirizzo Web del client sono sempre impostati su 0 dopo che viene eseguita la ricerca degli attributi relativi alla località geografica.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>La chiave di strumentazione personale è visibile nell'origine della pagina Web. 

* Questo è una pratica comune nelle soluzioni di monitoraggio.
* Questo valore non può essere usato per appropriarsi dei dati personali.
* Potrebbe essere usato per alterare gli avvisi su dati o trigger.
* Nessun cliente ha segnalato tali problemi.

È possibile:

* Usare due chiavi di strumentazione separate (risorse di Application Insights separate) per i dati del client e del server. Oppure
* Scrivere un proxy che viene eseguito nel server in modo che il client Web invii i dati tramite tale proxy.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Come visualizzare dati POST in Ricerca diagnostica?
I dati POST non vengono registrati automaticamente, ma è possibile usare una chiamata TrackTrace; inserire i dati nel parametro del messaggio, che ha limiti di dimensione più ampi rispetto alle proprietà della stringa, nonostante non sia possibile applicare filtri.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>È preferibile usare una o più risorse di Application Insights?

Usare una singola risorsa per tutti i componenti o i ruoli in un singolo sistema aziendale. Usare risorse separate per sviluppo, test e versioni di rilascio e per applicazioni indipendenti.

* [Vedere questo articolo](app/separate-resources.md)
* [Esempio: servizio cloud con ruoli di lavoro e Web](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Come è possibile modificare dinamicamente la chiave di strumentazione?

* [Vedere questo articolo](app/separate-resources.md)
* [Esempio: servizio cloud con ruoli di lavoro e Web](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Che cosa sono i conteggi utente e sessione?

* JavaScript SDK imposta un cookie utente nel client Web, per identificare gli utenti abituali, e un cookie di sessione per raggruppare le attività.
* Se non c'è uno script lato client, è possibile [impostare cookie per il server](https://apmtips.com/posts/2016-07-09-tracking-users-in-api-apps/).
* Se un utente reale usa il sito in browser diversi o esegue una navigazione privata o in incognito oppure usa computer diversi, viene inclusi più di una volta nei conteggi.
* Per identificare un utente connesso a più computer e browser, aggiungere una chiamata a [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> In Application Insights sono state abilitate tutte le funzionalità?
| Elementi che dovrebbero essere visualizzati | Come ottenerli | Perché si vuole ottenerli |
| --- | --- | --- |
| Grafici di disponibilità |[Test Web](app/monitor-web-app-availability.md) |Stabilire se l'app Web è attiva |
| Prestazioni dell'app server: tempi di risposta, ... |[Aggiungere Application Insights al progetto](app/asp-net.md) o [installare Status Monitor di Application Insights nel server](app/monitor-performance-live-website-now.md) (o scrivere il codice per [tenere traccia delle dipendenze](app/api-custom-events-metrics.md#trackdependency)) |Rilevare i problemi di prestazioni |
| Telemetria di dipendenza |[Installare Status Monitor di Application Insights nel server](app/monitor-performance-live-website-now.md) |Diagnosticare i problemi relativi a database o altri componenti esterni |
| Ricavare analisi dello stack dalle eccezioni |[Inserire chiamate TrackException nel codice](app/asp-net-exceptions.md) (ma alcune sono segnalate automaticamente) |Rilevare e diagnosticare le eccezioni |
| Eseguire la ricerca di tracce dei log |[Aggiungere un adattatore di registrazione](app/asp-net-trace-logs.md) |Diagnosticare le eccezioni, problemi di prestazioni |
| Nozioni di base dell'utilizzo del client: visualizzazioni pagina, sessioni, ... |[Inizializzatore JavaScript nelle pagine Web](app/javascript.md) |Analisi dell'utilizzo |
| Metriche personalizzate client |[Rilevamento delle chiamate nelle pagine Web](app/api-custom-events-metrics.md) |Migliorare l'esperienza utente |
| Metriche personalizzate server |[Rilevamento delle chiamate nel server](app/api-custom-events-metrics.md) |Business intelligence |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Perché i conteggi nei grafici di ricerca e di metrica non sono uguali?

Il [campionamento](app/sampling.md) riduce il numero di elementi di telemetria (richieste, eventi personalizzati e così via) inviati effettivamente dall'app al portale. Nel grafico di ricerca, viene visualizzato il numero di elementi effettivamente ricevuti. Nei grafici di metrica che visualizzano un conteggio degli eventi, è possibile vedere il numero di eventi originali che si sono verificati. 

Ogni elemento trasmesso include una proprietà `itemCount` che visualizza il numero di eventi originali rappresentati da questo elemento. Per osservare un campionamento in esecuzione, è possibile eseguire questa query in Analytics:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automazione

#### <a name="configuring-application-insights"></a>Configurazione di Application Insights

È possibile [scrivere script di PowerShell](app/powershell.md) tramite Monitoraggio risorse di Azure per:

* Creare e aggiornare risorse di Application Insights.
* Impostare il piano tariffario.
* Ottenere la chiave di strumentazione.
* Aggiungere un avviso di metrica.
* Aggiungere un test di disponibilità.

Non è possibile impostare un report di esplorazione delle metriche o impostare un'esportazione continua.

#### <a name="querying-the-telemetry"></a>Esecuzione di query sui dati di telemetria

Usare l'[API REST](https://dev.applicationinsights.io/) per eseguire le query di [Analytics](./log-query/log-query-overview.md).

### <a name="how-can-i-set-an-alert-on-an-event"></a>Come è possibile impostare un avviso per un evento?

Gli avvisi di Azure si applicano solo alle metriche. Creare una metrica personalizzata che supera una soglia di valori ogni volta che si verifica l'evento. Impostare quindi un avviso per la metrica. Si noti che verrà inviata una notifica ogni volta che la metrica supera la soglia in entrambe le direzioni. Non si riceverà una notifica fino al primo superamento, a prescindere che il valore iniziale sia alto o basso. C'è sempre una latenza di pochi minuti.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Ci sono costi per il trasferimento dati tra un'app Web di Azure e Application Insights?

* Se l'app Web di Azure è ospitata in un data center in cui è presente un endpoint di raccolta di Application Insights, non è previsto alcun addebito. 
* Se non c'è nessun endpoint nel data center host, la telemetria dell'app sarà soggetta agli [addebiti in uscita di Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Ciò non dipende da dove è ospitata la risorsa di Application Insights. Dipende solo dalla distribuzione degli endpoint.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>È possibile inviare dati di telemetria al portale di Application Insights?

È consigliabile usare gli SDK di Microsoft e l'[API SDK](app/api-custom-events-metrics.md). Sono disponibili varianti dell'SDK per diverse [piattaforme](app/platforms.md). Gli SDK gestiscono bufferring, compressione, limitazione, tentativi e così via. Tuttavia, lo [schema di inserimento](https://github.com/microsoft/ApplicationInsights-dotnet/tree/master/BASE/Schema/PublicSchema) e il [protocollo di endpoint](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) sono pubblici.

### <a name="can-i-monitor-an-intranet-web-server"></a>È possibile monitorare un server Web Intranet?

Sì, ma sarà necessario consentire il traffico ai servizi da eccezioni del firewall o reindirizzamenti del proxy.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Esaminare l'elenco completo dei servizi offerti e gli indirizzi IP in [questo articolo](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Eccezione del firewall

Consentire al server Web di inviare la telemetria agli endpoint. 

#### <a name="gateway-redirect"></a>Reindirizzamento del gateway

Instradare il traffico dal server a un gateway nella rete Intranet, sovrascrivendo gli endpoint nella configurazione. Se queste proprietà "Endpoint" non sono presenti nel file config, queste classi useranno i valori predefiniti illustrati di seguito nell'esempio ApplicationInsights.config. 

Il gateway deve indirizzare il traffico all'indirizzo di base dell'endpoint. Nella configurazione sostituire i valori predefiniti con `http://<your.gateway.address>/<relative path>`.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Esempio Applicationinsights.config con endpoint predefiniti:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> ApplicationIdProvider è disponibile a partire dalla versione 2.6.0.



#### <a name="proxy-passthrough"></a>Pass-through del proxy

È possibile ottenere il pass-through del proxy configurando un proxy a livello di computer o di applicazione.
Per altre informazioni, vedere l'articolo di DotNet su [DefaultProxy](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Web.config di esempio:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>È possibile eseguire test Web di disponibilità in un server Intranet?

I [test Web](app/monitor-web-app-availability.md) vengono eseguiti in punti di presenza distribuiti in tutto il globo. Sono disponibili due soluzioni:

* Porta di firewall: consentire al server di ricevere richieste dall'[elenco esteso e modificabile degli agenti di test Web](app/ip-addresses.md).
* Scrivere un codice personalizzato per inviare richieste periodiche al server dall'interno della rete Intranet. A tale scopo è anche possibile eseguire test Web di Visual Studio. Il tester può inviare i risultati ad Application Insights tramite l'API TrackAvailability().

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Quanto tempo occorre per raccogliere i dati di telemetria?

Quasi tutti i dati di Application Insights hanno una latenza inferiore a 5 minuti. Alcuni dati, in genere i file di log di maggiori dimensioni, possono richiedere tempi più lunghi. Per altre informazioni, vedere il [Contratto di Servizio per Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md

### <a name="http-502-and-503-responses-are-not-always-captured-by-application-insights"></a>Le risposte HTTP 502 e 503 non vengono sempre acquisite da Application Insights

gli errori "502 gateway non valido" e "503 servizio non disponibile" non vengono sempre acquisiti da Application Insights. Se per il monitoraggio è usato solo JavaScript sul lato client, questo comportamento è previsto perché la risposta di errore viene restituita prima della pagina contenente l'intestazione HTML con il rendering del frammento JavaScript di monitoraggio sottoposto a rendering. 

Se la risposta 502 o 503 è stata inviata da un server con monitoraggio lato server abilitato, gli errori verranno raccolti da Application Insights SDK. 

Tuttavia, esistono ancora casi in cui anche quando il monitoraggio lato server è abilitato nel server Web di un'applicazione che non viene acquisito da Application Insights un errore 502 o 503. Molti server Web moderni non consentono a un client di comunicare direttamente, ma utilizzano soluzioni come i proxy inversi per passare le informazioni tra il client e i server Web front-end. 

In questo scenario è possibile che a un client venga restituita una risposta 502 o 503 a causa di un problema al livello del proxy inverso e che questa non venga acquisita in modo predefinito da Application Insights. Per consentire il rilevamento di problemi a questo livello, potrebbe essere necessario inviare i log dal proxy inverso a Log Analytics e creare una regola personalizzata per verificare la presenza di 502/503 risposte. Per ulteriori informazioni sulle cause comuni degli errori 502 e 503, consultare l' [articolo sulla risoluzione dei problemi del servizio app Azure per "502 gateway non valido" e "servizio 503 non disponibile"](../app-service/troubleshoot-http-502-http-503.md).     


## <a name="opentelemetry"></a>OpenTelemetry

### <a name="what-is-opentelemetry"></a>Informazioni su OpenTelemetry

Un nuovo standard open source per l'osservabilità. Ulteriori informazioni sono disponibili all'indirizzo [https://opentelemetry.io/](https://opentelemetry.io/) .

### <a name="why-is-microsoft--azure-monitor-investing-in-opentelemetry"></a>Perché Microsoft/monitoraggio di Azure investe in OpenTelemetry?

Microsoft ritiene che i clienti soddisfino meglio i nostri clienti per tre motivi:
   1. Abilita il supporto per più scenari dei clienti.
   2. Instrumentare senza temere il blocco del fornitore.
   3. Aumentare la trasparenza e il coinvolgimento dei clienti.

Si allinea inoltre alla strategia di Microsoft per [adottare Open Source](https://opensource.microsoft.com/).

### <a name="what-additional-value-does-opentelemetry-give-me"></a>Quale valore aggiuntivo offre OpenTelemetry?

Oltre ai motivi precedenti, OpenTelemetry è più efficiente su larga scala e offre funzionalità di progettazione/configurazione coerenti tra le varie lingue.

### <a name="how-can-i-test-out-opentelemetry"></a>Come è possibile testare OpenTelemetry?

Iscriversi per partecipare al Application Insights di monitoraggio di Azure early adopter community all'indirizzo [https://aka.ms/AzMonOtel](https://aka.ms/AzMonOtel) .

### <a name="what-does-ga-mean-in-the-context-of-opentelemetry"></a>Cosa significa GA nel contesto di OpenTelemetry?

La community di OpenTelemetry definisce disponibile a livello generale ( [GA).](https://medium.com/opentelemetry/ga-planning-f0f6d7b5302) Tuttavia, OpenTelemetry "GA" non significa la parità di funzionalità con gli SDK di Application Insights esistenti. Monitoraggio di Azure continuerà a consigliare gli attuali SDK di Application Insights per i clienti che richiedono funzionalità quali [metriche pre-aggregate](app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics), [metriche attive](app/live-stream.md), [campionamento adattivo](app/sampling.md#adaptive-sampling), [Profiler](app/profiler-overview.md)e [debugger snapshot](app/snapshot-debugger.md) finché gli SDK di OpenTelemetry non raggiungono la maturità delle funzionalità.

### <a name="can-i-use-preview-builds-in-production-environments"></a>È possibile usare le compilazioni di anteprima negli ambienti di produzione?

Non è consigliabile. Per ulteriori informazioni, vedere le [condizioni per l'utilizzo aggiuntive per Microsoft Azure anteprime](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

### <a name="whats-the-difference-between-opentelemetry-sdk-and-auto-instrumentation"></a>Qual è la differenza tra OpenTelemetry SDK e la strumentazione automatica?

La specifica OpenTelemetry definisce [SDK](https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/glossary.md#telemetry-sdk). In breve, "SDK" è un pacchetto specifico del linguaggio che raccoglie i dati di telemetria tra i vari componenti dell'applicazione e invia i dati a monitoraggio di Azure tramite un esportatore.

Il concetto di strumentazione automatica (a volte definito inserimento di bytecode, senza codice o basato su agenti) si riferisce alla capacità di instrumentare l'applicazione senza modificare il codice. Ad esempio, per altre informazioni, vedere il [file Leggimi relativo alla strumentazione automatica di OpenTelemetry Java](https://github.com/open-telemetry/opentelemetry-java-instrumentation/blob/master/README.md) .

### <a name="whats-the-opentelemetry-collector"></a>Che cos'è l'agente di raccolta OpenTelemetry?

L'agente di raccolta OpenTelemetry è descritto nel [file Leggimi di GitHub](https://github.com/open-telemetry/opentelemetry-collector#opentelemetry-collector). Attualmente Microsoft non usa l'agente di raccolta OpenTelemetry e dipende dagli esportatori diretti che inviano al Application Insights di monitoraggio di Azure.

### <a name="whats-the-difference-between-opencensus-and-opentelemetry"></a>Qual è la differenza tra OpenCensus e OpenTelemetry?

[OpenCensus](https://opencensus.io/) è il precursore di [OpenTelemetry](https://opentelemetry.io/). Microsoft ha contribuito a riunire [OpenTracing](https://opentracing.io/) e OpenCensus per creare OpenTelemetry, un unico standard di osservabilità per il mondo. L' [SDK Python consigliato](app/opencensus-python.md) per la produzione di monitoraggio di Azure è basato su OpenCensus, ma alla fine tutti gli SDK di monitoraggio di Azure saranno basati su OpenTelemetry.


## <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori

### <a name="health-feature-is-in-private-preview"></a>Questa funzionalità è in anteprima privata.

Intendiamo apportare una serie di modifiche per aggiungere funzionalità e soddisfare i commenti e suggerimenti ricevuti dagli utenti. La funzionalità di integrità passerà a un'anteprima privata alla fine di giugno 2020 e per altre informazioni, vedere il seguente [annuncio di aggiornamenti ad Azure](https://azure.microsoft.com/updates/ci-health-limited-preview/).

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Che cosa rappresenta *Altri processi* nella visualizzazione Nodo?

**Altri processi** consentono di comprendere chiaramente la causa principale dell'utilizzo elevato delle risorse nel nodo. In questo modo, è possibile distinguere l'utilizzo tra processi in contenitori e processi non in contenitori.

Quali sono gli **Altri processi**? 

Si tratta di processi non in contenitori che vengono eseguiti nel nodo.  

Come si calcola l’utilizzo?

**Altri processi** = *Total usage from CAdvisor (Utilizzo totale da CAdvisor)*  - *Usage from containerized process (Utilizzo da processo in contenitore)*

**Altri processi** include:

- Processi non in contenitori Kubernetes gestiti o autogestiti 

- Processi runtime del contenitore  

- Kubelet  

- Processi di sistema in esecuzione nel nodo 

- Altri carichi di lavoro non Kubernetes in esecuzione su hardware o macchina virtuale del nodo 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Quando si esegue una query sulla tabella ContainerLog, i valori delle proprietà Image e Name non vengono visualizzati.

Per la versione dell'agente ciprod12042019 e versioni successive, per impostazione predefinita queste due proprietà non vengono popolate per ogni riga di log per ridurre al minimo i costi legati ai dati di log raccolti. Sono disponibili due opzioni per eseguire una query sulla tabella che includono queste proprietà con i rispettivi valori:

#### <a name="option-1"></a>Opzione 1 

Unire altre tabelle per includere i valori delle proprietà nei risultati.

Modificare le query in modo da includere le proprietà Image e ImageTag dalla tabella ```ContainerInventory``` tramite join alla proprietà ContainerID. È possibile includere la proprietà Name, come in precedenza nella tabella, ```ContainerLog``` dal campo ContaineName della tabella KubepodInventory tramite join alla proprietà ContainerId. È consigliabile selezionare questa opzione.

L'esempio seguente illustra una query dettagliata di esempio che spiega come ottenere questi valori di campo con i join.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>Opzione 2

Abilitare di nuovo la raccolta per queste proprietà per ogni riga del log del contenitore.

Se la prima opzione non è comoda a causa delle necessarie modifiche alle query, è possibile riabilitare la raccolta di questi campi abilitando l'impostazione ```log_collection_settings.enrich_container_logs``` nella mappa di configurazione dell'agente come descritto in [Impostazioni di configurazione della raccolta dati](insights/container-insights-agent-config.md).

> [!NOTE]
> La seconda opzione non è consigliata con cluster di grandi dimensioni che contengono più di 50 nodi perché, per eseguire questa operazione di raccolta, genera chiamate al server API da ogni nodo del cluster. Questa opzione aumenta anche le dimensioni dei dati per ogni riga di log raccolta.

### <a name="can-i-view-metrics-collected-in-grafana"></a>È possibile visualizzare le metriche raccolte in Grafana?

Monitoraggio di Azure per i contenitori supporta la visualizzazione delle metriche archiviate nell'area di lavoro Log Analytics nei dashboard di Grafana. È disponibile un modello che è possibile scaricare dal [repository di dashboard](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) di Grafana per iniziare e a cui fare riferimento per capire come eseguire query sui dati aggiuntivi dai cluster monitorati da visualizzare in dashboard Grafana personalizzati. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>È possibile monitorare il proprio cluster del motore del servizio Azure Kubernetes con Monitoraggio di Azure per i contenitori?

Monitoraggio di Azure per i contenitori supporta il monitoraggio di carichi di lavoro del contenitore distribuiti a cluster del motore del servizio Azure Kubernetes (precedentemente noto come motore ACS) ospitati in Azure. Per altri dettagli e una panoramica dei passaggi necessari per abilitare il monitoraggio per questo scenario, vedere [Utilizzo di Monitoraggio di Azure per i contenitori per il motore del servizio Azure Kubernetes](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Perché i dati nell'area di lavoro Log Analytics non vengono visualizzati ?

Se non si riesce a visualizzare i dati nell'area di lavoro Log Analytics in un determinato momento ogni giorno, è possibile che sia stato raggiunto il limite di 500 MB predefinito o il limite giornaliero specificato per controllare la quantità di dati da raccogliere quotidianamente. Quando viene raggiunto il limite del giorno, la raccolta dei dati si interrompe e riprende solo il giorno successivo. Per esaminare l'utilizzo dei dati e passare a un piano tariffario diverso in base ai modelli di utilizzo previsti, vedere [Utilizzo dei dati di log e costi](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Che cosa sono gli stati dei contenitori specificati nella tabella ContainerInventory?

La tabella ContainerInventory contiene informazioni sia sui contenitori arrestati che su quelli in esecuzione. La tabella viene popolata da un flusso di lavoro all'interno dell'agente che esegue una query in Docker per tutti i contenitori (in esecuzione e arrestati) e inoltra tali dati nell'area di lavoro Log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Come si risolve l’errore *Registrazione della sottoscrizione mancante*?

Se viene visualizzato l'errore **Registrazione della sottoscrizione mancante per Microsoft.OperationsManagement**, è possibile risolverlo registrando il provider di risorse **Microsoft.OperationsManagement** nella sottoscrizione in cui è definita l'area di lavoro. La documentazione che illustra come eseguire questa operazione è disponibile [qui](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>È previsto il supporto per i cluster del servizio Azure Kubernetes abilitati per il Controllo degli accessi in base al ruolo?

La soluzione di monitoraggio per i contenitori non supporta il Controllo degli accessi in base al ruolo, ma è supportata con Monitoraggio di Azure per i contenitori. Le informazioni presenti nei pannelli che visualizzano i dati relativi a tali cluster all'interno della pagina dei dettagli della soluzione possono non essere corrette.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Come si abilita la raccolta di log per i contenitori nello spazio dei nomi kube-system tramite Helm?

La raccolta di log dai contenitori nello spazio dei nomi kube-system è disabilitata per impostazione predefinita. È possibile abilitare la raccolta di log impostando una variabile di ambiente in omsagent. Per altre informazioni, vedere la pagina [Monitoraggio di Azure per i contenitori](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) in GitHub. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Come si aggiorna omsagent alla versione rilasciata più recente?

Per informazioni su come aggiornare l'agente, vedere [Gestione dell'agente](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Come si abilita la registrazione su più righe?

Attualmente Monitoraggio di Azure per i contenitori non supporta la registrazione su più righe, ma sono disponibili soluzioni alternative. È possibile configurare tutti i servizi per la scrittura in formato JSON. Docker/Moby eseguirà quindi la scrittura su un'unica riga.

È ad esempio possibile eseguire il wrapping del log come oggetto JSON, come illustrato nell'esempio seguente per un'applicazione node.js di esempio:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Quando si esegue una query in Monitoraggio di Azure per i log, i dati si presentano come seguente:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Per un esame dettagliato del problema, vedere il seguente [collegamento a GitHub](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Come si risolvono gli errori di Azure Active Directory quando si abilitano i log in tempo reale? 

Può essere visualizzato l'errore seguente: **The reply url specified in the request does not match the reply urls configured for the application: '<application ID\>'** (L’URL di risposta specificato nella richiesta non corrisponde agli URL di risposta configurati per l’applicazione '<ID applicazione>). La risoluzione di questo errore è disponibile nell'articolo [Come visualizzare i dati dei contenitori in tempo reale con Monitoraggio di Azure per i contenitori](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Perché non si riesce ad aggiornare il cluster dopo l'onboarding?

Se dopo aver abilitato Monitoraggio di Azure per i contenitori per un cluster del servizio Azure Kubernetes si elimina l'area di lavoro Log Analytics a cui il cluster stava inviando i dati, quando si tenta di aggiornare il cluster, l'operazione ha esito negativo. Per risolvere il problema, è necessario disabilitare il monitoraggio e quindi riabilitarlo facendo riferimento a un'altra area di lavoro valida nella sottoscrizione. Quando si tenta di eseguire di nuovo l'aggiornamento del cluster, il processo dovrebbe essere elaborato e completato correttamente.  

### <a name="which-ports-and-domains-do-i-need-to-openallow-for-the-agent"></a>Quali porte e domini sono necessari per aprire o consentire l'agente?

Per informazioni sulla configurazione del proxy e del firewall necessarie per l’agente in contenitore con i cloud Azure, Azure US Government e Azure China (21Vianet), vedere [Requisiti del firewall di rete](insights/container-insights-onboard.md#network-firewall-requirements).


## <a name="azure-monitor-for-vms"></a>Monitoraggio di Azure per le macchine virtuali

### <a name="can-i-onboard-to-an-existing-workspace"></a>È possibile eseguire l'onboarding in un'area di lavoro esistente?
Se le macchine virtuali sono già connesse a un'area di lavoro di Log Analytics, è possibile continuare a usare tale area di lavoro durante l'onboarding per Monitoraggio di Azure per le macchine virtuali, purché si trovi in una delle [aree supportate](insights/vminsights-configure-workspace.md#supported-regions).


### <a name="can-i-onboard-to-a-new-workspace"></a>È possibile eseguire l'onboarding in una nuova area di lavoro? 
Se le macchine virtuali non attualmente connesse a un'area di lavoro Log Analytics esistente, è necessario crearne una nuova per archiviare i dati. La creazione di una nuova area di lavoro predefinita avviene automaticamente nel caso in cui si configuri una singola macchina virtuale di Azure per Monitoraggio di Azure per le macchine virtuali tramite il portale di Azure.

Se si sceglie di utilizzare il metodo basato su script, questi passaggi sono illustrati nell’articolo [Abilitare Monitoraggio di Azure per le macchine virtuali usando modelli di Azure PowerShell o Gestione risorse](./insights/vminsights-enable-powershell.md). 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Cosa occorre fare se la macchina virtuale invia già informazioni a un'area di lavoro esistente?
Se già si raccolgono dati dalle macchine virtuali, è possibile che sia già stato configurato l'invio di dati a un'area di lavoro Log Analytics esistente.  Se quest'area di lavoro è in una delle aree supportate, è possibile abilitare Monitoraggio di Azure per le macchine virtuali per tale area di lavoro preesistente.  Se l'area di lavoro già in uso non si trova in una delle aree di Azure supportate, non è attualmente possibile eseguire l'onboarding per Monitoraggio di Azure per le macchine virtuali.  Microsoft sta lavorando attivamente per supportare altre aree.


### <a name="why-did-my-vm-fail-to-onboard"></a>Perché la macchina virtuale non è riuscita a eseguire l'onboarding?
Quando si esegue l'onboarding di una macchina virtuale di Azure dal portale di Azure, si verificano i passaggi seguenti:

* Viene creata un'area di lavoro Log Analytics predefinita, se tale opzione è stata selezionata.
* L'agente di Log Analytics viene installato nelle macchine virtuali di Azure mediante un'estensione VM, se necessario.  
* Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali viene installato nelle macchine virtuali di Azure mediante un'estensione, se necessario. 

Durante il processo di onboarding, viene controllato lo stato di ciascun passaggio, visualizzando una notifica nel portale. La configurazione dell'area di lavoro e l'installazione dell'agente richiedono in genere 5-10 minuti. La visualizzazione dei dati di monitoraggio nel portale richiede altri 5-10 minuti.  

Se l'onboarding è stato avviato e vengono visualizzati dei messaggi che indicano che occorre eseguire l'onboarding della macchina virtuale, attendere 30 minuti affinché la macchina virtuale completi il processo. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>I grafici delle prestazioni della macchina virtuale non contengono alcuni dati o non ne contengono affatto.
I grafici delle prestazioni sono stati aggiornati per usare i dati archiviati nella tabella *InsightsMetrics*.  Per visualizzare i dati in questi grafici, sarà necessario eseguire l'aggiornamento alla nuova soluzione Informazioni dettagliate sulle macchine virtuali.  Per altre informazioni, fare riferimento alle [Domande frequenti sulla disponibilità generale](insights/vminsights-ga-release-faq.md).

Se i dati sulle prestazioni non compaiono nella tabella del disco o in alcuni grafici delle prestazioni, è possibile che i contatori delle prestazioni non siano stati configurati nell'area di lavoro. Per risolvere il problema, eseguire lo [script di PowerShell](./insights/vminsights-enable-powershell.md) seguente.


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>In cosa differisce la funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali da Mapping dei servizi?
La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali si basa su Mapping dei servizi, ma presenta le differenze seguenti:

* È possibile accedere alla vista della mappa dal pannello VM e da Monitoraggio di Azure per le macchine virtuali in Monitoraggio di Azure.
* Le connessioni nella mappa ora sono selezionabili e mostrano i dati delle metriche di connessione nel pannello laterale relativo alla connessione selezionata.
* È disponibile una nuova API che consente di creare le mappe in modo da supportare meglio quelle più complesse.
* Le macchine virtuali monitorate sono ora incluse nel nodo del gruppo client e il grafico ad anello mostra la proporzione tra le macchine virtuali monitorate e non monitorate presenti nel gruppo.  Può anche essere utilizzato per filtrare l'elenco delle macchine quando il gruppo viene espanso.
* Le macchine virtuali monitorate sono ora incluse nei nodi del gruppo di porte server e il grafico ad anello mostra la proporzione tra le macchine monitorate e non monitorate presenti nel gruppo.  Può anche essere utilizzato per filtrare l'elenco delle macchine quando il gruppo viene espanso.
* Lo stile della mappa è stato aggiornato per maggiore coerenza con la mappa delle app di Application Insights.
* I pannelli laterali sono stati aggiornati e non dispongono del set completo di integrazioni supportate in Mapping dei servizi, ovvero Gestione aggiornamenti, Rilevamento modifiche, Sicurezza e Service Desk. 
* L'opzione per la scelta dei gruppi e delle macchine di cui eseguire il mapping è stata aggiornata e ora supporta sottoscrizioni, gruppi di risorse, set di scalabilità delle macchine virtuali di Azure e servizi cloud.
* Non è possibile creare nuovi gruppi di macchine di Mapping dei servizi nella funzionalità Monitoraggio di Azure per le macchine virtuali.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Perché nei grafici delle prestazioni appaiono delle linee tratteggiate?
I motivi possono essere vari.  Nei casi in cui sia presente un vuoto nella raccolta di dati, le linee si presentano tratteggiate.  Se è stata modificata la frequenza di campionamento dei dati per i contatori delle prestazioni abilitati (l'impostazione predefinita prevede la raccolta di dati ogni 60 secondi), è possibile che appaiano delle linee punteggiate nel grafico quando si sceglie un intervallo di tempo ristretto per il grafico e la frequenza di campionamento è inferiore alle dimensioni del bucket usate nel grafico (ad esempio, la frequenza di campionamento è ogni 10 minuti e ogni bucket del grafico è di 5 minuti).  Se si sceglie di visualizzare un intervallo di tempo più ampio, le linee del grafico appariranno continue e non tratteggiate.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>I gruppi sono supportati con Monitoraggio di Azure per le macchine virtuali?
Sì, dopo aver installato Dependency Agent, vengono raccolte informazioni dalle macchine virtuali per visualizzare i gruppi in base alla sottoscrizione, al gruppo di risorse, al set di scalabilità delle macchine virtuali e ai servizi cloud.  Se si usa Mapping dei servizi e sono stati creati gruppi di macchine, vengono visualizzati anch'essi.  Se per l'area di lavoro che si sta esaminando sono stati creati gruppi di computer, verranno visualizzati nel filtro dei gruppi. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Come visualizzare i dettagli alla base della linea del 95° percentile nei grafici delle prestazioni in forma aggregata?
Per impostazione predefinita, l'elenco è ordinato in modo da mostrare le macchine virtuali con il valore più elevato per il 95° percentile in relazione alla metrica selezionata, ad eccezione del grafico della memoria disponibile, che mostra le macchine con il valore più basso per il 5° percentile.  Se si fa clic sul grafico, appare la visualizzazione **elenco delle prime N** con la metrica appropriata selezionata.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>In che modo la funzionalità di mappa gestisce gli indirizzi IP duplicati su reti virtuali e subnet diverse?
Se si duplicano gli intervalli IP con le VM o i set di scalabilità di macchine virtuali di Azure su subnet e reti virtuali, la mappa di Monitoraggio di Azure per le macchine virtuali potrebbe mostrare informazioni non corrette. Si tratta di un problema noto ed è in corso l'analisi delle opzioni per migliorare questa esperienza.

### <a name="does-map-feature-support-ipv6"></a>La funzionalità di mappa supporta il protocollo IPv6?
La funzionalità di mappa supporta attualmente solo il protocollo IPv4 ed è in corso l'analisi del supporto per IPv6. È supportato anche il protocollo IPv4 con a tunneling all'interno di IPv6.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Quando si carica una mappa per un gruppo di risorse o un altro gruppo di grandi dimensioni, la mappa è di difficile visualizzazione
Anche se sono stati apportati miglioramenti alla mappa per gestire le configurazioni complesse e di grandi dimensioni, ci rendiamo conto che una mappa può contenere molti nodi, connessioni e nodi che funzionano come cluster.  Microsoft si impegna a migliorare costantemente il supporto per aumentare la scalabilità.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Perché il grafico di rete nella scheda Prestazioni ha un aspetto diverso rispetto al grafico di rete nella pagina di panoramica della VM di Azure?

La pagina di panoramica di una VM di Azure mostra i grafici basati sulla misurazione dell'attività dell'host nella VM guest.  Per il grafico di rete nella panoramica della VM di Azure, viene visualizzato solo il traffico che verrà fatturato  e non è incluso il traffico tra reti virtuali.  I dati e i grafici visualizzati per Monitoraggio di Azure per le macchine virtuali si basano sui dati ottenuti dalla VM guest e il grafico di rete mostra tutto il traffico TCP/IP in ingresso e in uscita verso tale VM, incluso quello tra reti virtuali.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Come viene misurato il tempo di risposta per i dati archiviati in VMConnection e visualizzati nel pannello di connessione e nelle cartelle di lavoro?

Il tempo di risposta è un'approssimazione. Poiché non si instrumenta il codice dell'applicazione, non si è in grado di stabilire con certezza quando inizia una richiesta e quando arriva la risposta. Osserviamo invece i dati inviati in una connessione e quindi i dati che tornano in tale connessione. L'agente tiene traccia di questi invii e delle ricevute e tenta di associarli: una sequenza di invii, seguita da una sequenza di ricevute viene interpretata come una coppia richiesta-risposta. L'intervallo di tempo tra queste operazioni è il tempo di risposta. Il tempo di risposta include la latenza di rete e il tempo di elaborazione del server.

Questa approssimazione funziona adeguatamente per i protocolli basati su richiesta/risposta: una singola richiesta viene inviata sulla connessione e viene ricevuta una singola risposta. Questo calcolo è adatto per il protocollo HTTP(S) (senza pipelining), ma non è soddisfacente per altri protocolli.

### <a name="are-there-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Esistono limitazioni se si è Log Analytics piano tariffario gratuito?
Se Monitoraggio di Azure è stato configurato con un'area di lavoro Log Analytics che usa il piano tariffario *Gratuito*, la funzionalità della mappa Monitoraggio di Azure per le macchine virtuali supporterà solo cinque computer connessi all'area di lavoro. Se si hanno cinque macchine virtuali connesse a un'area di lavoro gratuita, si disconnette una delle macchine virtuali e in un secondo momento si connette una nuova macchina virtuale, la nuova macchina virtuale non viene monitorata e riflessa nella pagina della mappa.  

In questo caso, verrà visualizzata l'opzione **Prova adesso** quando si apre la macchina virtuale e si seleziona **Informazioni dettagliate** nel riquadro a sinistra, anche dopo che è già stato installato nella macchina virtuale.  Non vengono tuttavia visualizzate le normali opzioni come avverrebbe normalmente se non fosse stato eseguito l'onboarding di questa macchina virtuale in Monitoraggio di Azure per le macchine virtuali. 


## <a name="next-steps"></a>Passaggi successivi
Se la domanda non è disponibile in questo documento, è possibile fare riferimento ai forum seguenti per ulteriori domande e risposte.

- [Log Analytics](/answers/topics/azure-monitor.html)
- [Application Insights](/answers/topics/azure-monitor.html)

Per commenti e suggerimenti generali su Monitoraggio di Azure, visitare il [forum dei commenti e suggerimenti](https://feedback.azure.com/forums/34192--general-feedback).
