---
title: Domande frequenti su monitoraggio di Azure | Microsoft Docs
description: Risposte alle domande frequenti su monitoraggio di Azure.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/23/2020
ms.openlocfilehash: 33af9c8f3fbe4de57d65be432f249d4aeb5d3e27
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485336"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Domande frequenti su monitoraggio di Azure

Queste domande frequenti su Microsoft sono un elenco di domande frequenti su monitoraggio di Azure.

## <a name="general"></a>Generale

### <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
[Monitoraggio di Azure](overview.md) è un servizio di Azure che offre il monitoraggio delle prestazioni e della disponibilità per le applicazioni e i servizi in Azure, in altri ambienti cloud o in locale. Monitoraggio di Azure raccoglie dati da più origini in una piattaforma dati comune in cui possono essere analizzati per individuare tendenze e anomalie. Le funzionalità avanzate di monitoraggio di Azure consentono di identificare e rispondere rapidamente a situazioni critiche che potrebbero influire sull'applicazione.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Qual è la differenza tra monitoraggio di Azure, Log Analytics e Application Insights?
A settembre 2018, Microsoft ha combinato monitoraggio di Azure, Log Analytics e Application Insights in un unico servizio per fornire un potente monitoraggio end-to-end delle applicazioni e dei componenti su cui si basano. Le funzionalità di Log Analytics e Application Insights non sono state modificate, anche se alcune funzionalità sono state rimarcate in monitoraggio di Azure per riflettere meglio il nuovo ambito. Il motore dati di log e il linguaggio di query di Log Analytics sono ora definiti log di monitoraggio di Azure. Vedere [aggiornamenti della terminologia di monitoraggio di Azure](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Quali sono i costi di monitoraggio di Azure?
Le funzionalità di monitoraggio di Azure abilitate automaticamente, ad esempio la raccolta di metriche e i log attività, sono disponibili gratuitamente. È previsto un costo associato ad altre funzionalità, ad esempio query di log e avvisi. Per informazioni dettagliate sui prezzi, vedere la [pagina dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/) .

### <a name="how-do-i-enable-azure-monitor"></a>Ricerca per categorie abilitare monitoraggio di Azure?
Monitoraggio di Azure è abilitato nel momento in cui si crea una nuova sottoscrizione di Azure e le [metriche](platform/data-platform-metrics.md) del [log attività](platform/activity-logs-overview.md) e della piattaforma vengono raccolte automaticamente. Creare [le impostazioni di diagnostica](platform/diagnostic-settings.md) per raccogliere informazioni più dettagliate sul funzionamento delle risorse di Azure e aggiungere [soluzioni di monitoraggio](insights/solutions.md) e [approfondimenti](insights/insights-overview.md) per offrire un'ulteriore analisi sui dati raccolti per determinati servizi. 

### <a name="how-do-i-access-azure-monitor"></a>Ricerca per categorie accedere a monitoraggio di Azure?
Accedere a tutte le funzionalità e i dati di monitoraggio di Azure dal menu **monitoraggio** della portale di Azure. La sezione **monitoraggio** del menu per diversi servizi di Azure fornisce l'accesso agli stessi strumenti con dati filtrati per una determinata risorsa. I dati di monitoraggio di Azure sono accessibili anche per diversi scenari usando l'interfaccia della riga di comando, PowerShell e un'API REST.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Esiste una versione locale di monitoraggio di Azure?
No. Monitoraggio di Azure è un servizio cloud scalabile che elabora e archivia grandi quantità di dati, anche se monitoraggio di Azure è in grado di monitorare le risorse locali e in altri cloud.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Monitoraggio di Azure può monitorare le risorse locali?
Sì, oltre a raccogliere i dati di monitoraggio dalle risorse di Azure, monitoraggio di Azure può raccogliere dati da macchine virtuali e applicazioni in altri cloud e in locale. Vedere [le origini dei dati di monitoraggio per monitoraggio di Azure](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Monitoraggio di Azure si integra con System Center Operations Manager?
È possibile connettere il gruppo di gestione di System Center Operations Manager esistente a monitoraggio di Azure per raccogliere i dati dagli agenti nei log di monitoraggio di Azure. In questo modo è possibile utilizzare query e soluzioni di log per analizzare i dati raccolti dagli agenti. È anche possibile configurare gli agenti di System Center Operations Manager esistenti per inviare i dati direttamente a monitoraggio di Azure. Vedere [connettere Operations Manager a monitoraggio di Azure](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Quali indirizzi IP vengono usati da monitoraggio di Azure?
Vedere [gli indirizzi IP usati da Application Insights e log Analytics](app/ip-addresses.md) per un elenco degli indirizzi IP e delle porte necessarie per gli agenti e altre risorse esterne per accedere a monitoraggio di Azure. 

## <a name="monitoring-data"></a>Dati di monitoraggio

### <a name="where-does-azure-monitor-get-its-data"></a>Dove monitoraggio di Azure ottiene i dati?
Monitoraggio di Azure raccoglie dati da un'ampia gamma di origini, tra cui log e metriche della piattaforma e delle risorse di Azure, applicazioni personalizzate e agenti in esecuzione su macchine virtuali. Altri servizi come il Centro sicurezza di Azure e Network Watcher raccolgono i dati in un'area di lavoro Log Analytics in modo che possano essere analizzati con i dati di monitoraggio di Azure. È anche possibile inviare dati personalizzati a monitoraggio di Azure usando l'API REST per i log o le metriche. Vedere [le origini dei dati di monitoraggio per monitoraggio di Azure](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Quali dati vengono raccolti da monitoraggio di Azure? 
Monitoraggio di Azure raccoglie i dati da un'ampia gamma di origini nei [log](platform/data-platform-logs.md) o nelle [metriche](platform/data-platform-metrics.md). Ogni tipo di dati presenta vantaggi relativi e ognuno supporta un particolare set di funzionalità in monitoraggio di Azure. Per ogni sottoscrizione di Azure è disponibile un singolo database di metriche, mentre è possibile creare più aree di lavoro Log Analytics per raccogliere i log in base ai requisiti. Vedere [piattaforma dati di monitoraggio di Azure](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Esiste una quantità massima di dati che è possibile raccogliere in monitoraggio di Azure?
Non esiste alcun limite alla quantità di dati delle metriche che è possibile raccogliere, ma questi dati vengono archiviati per un massimo di 93 giorni. Vedere [conservazione delle metriche](platform/data-platform-metrics.md#retention-of-metrics). Non esiste alcun limite alla quantità di dati di log che è possibile raccogliere, ma potrebbe essere influenzato dal piano tariffario scelto per l'area di lavoro Log Analytics. Vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Ricerca per categorie accedere ai dati raccolti da monitoraggio di Azure?
Le informazioni e le soluzioni offrono un'esperienza personalizzata per l'uso dei dati archiviati in monitoraggio di Azure. È possibile lavorare direttamente con i dati di log usando una query di log scritta in kusto Query Language (KQL). Nel portale di Azure è possibile scrivere ed eseguire query e analizzare i dati in modo interattivo usando Log Analytics. Analizzare le metriche nel portale di Azure con il Esplora metriche. Vedere [analizzare i dati del log in monitoraggio di Azure](log-query/log-query-overview.md) e [iniziare a usare Azure Esplora metriche](platform/metrics-getting-started.md).





## <a name="solutions-and-insights"></a>Soluzioni e informazioni dettagliate

### <a name="what-is-an-insight-in-azure-monitor"></a>Informazioni dettagliate su monitoraggio di Azure
Le informazioni dettagliate forniscono un'esperienza di monitoraggio personalizzata per determinati servizi di Azure. Usano le stesse metriche e i log delle altre funzionalità di monitoraggio di Azure, ma possono raccogliere dati aggiuntivi e fornire un'esperienza univoca nel portale di Azure. Vedere [informazioni dettagliate in monitoraggio di Azure](insights/insights-overview.md).

Per visualizzare informazioni dettagliate nella portale di Azure, vedere la sezione **Insights** del menu **monitoraggio** o la sezione **monitoraggio** del menu del servizio.

### <a name="what-is-a-solution-in-azure-monitor"></a>Che cos'è una soluzione di monitoraggio di Azure?
Le soluzioni di monitoraggio sono set di logica in pacchetto per il monitoraggio di una particolare applicazione o servizio in base alle funzionalità di monitoraggio di Azure. Raccolgono i dati di log in monitoraggio di Azure e forniscono query e visualizzazioni di log per l'analisi usando un'esperienza comune nell'portale di Azure. Vedere [monitoraggio delle soluzioni in monitoraggio di Azure](insights/solutions.md).

Per visualizzare le soluzioni nella portale di Azure, fare clic su **altro** nella sezione **Insights** del menu **monitoraggio** . Fare clic su **Aggiungi** per aggiungere ulteriori soluzioni all'area di lavoro.






## <a name="logs"></a>Log

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Qual è la differenza tra i log di monitoraggio di Azure e Esplora dati di Azure?
Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. I log di monitoraggio di Azure si basano su Esplora dati di Azure e usano lo stesso linguaggio di query kusto (KQL) con alcune differenze minime. Vedere le [differenze del linguaggio di query del log di monitoraggio di Azure](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Ricerca per categorie recuperare i dati di log?
Tutti i dati vengono recuperati da un'area di lavoro Log Analytics usando una query di log scritta con kusto Query Language (KQL). È possibile scrivere query personalizzate o usare soluzioni e informazioni dettagliate che includono query di log per un'applicazione o un servizio specifico. Vedere [Panoramica delle query di log in monitoraggio di Azure](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>Che cos'è un'area di lavoro Log Analytics?
Tutti i dati di log raccolti da monitoraggio di Azure vengono archiviati in un'area di lavoro Log Analytics. Un'area di lavoro è essenzialmente un contenitore in cui i dati di log vengono raccolti da diverse origini. È possibile che si disponga di una singola area di lavoro Log Analytics per tutti i dati di monitoraggio o che siano necessari requisiti per più aree di lavoro. Vedere [progettazione della distribuzione dei log di monitoraggio di Azure](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>È possibile spostare un'area di lavoro Log Analytics esistente in un'altra sottoscrizione di Azure?
È possibile spostare un'area di lavoro tra gruppi di risorse o sottoscrizioni, ma non in un'area diversa. Vedere [spostare un'area di lavoro log Analytics a una sottoscrizione o a un gruppo di risorse diverso](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Perché non è possibile visualizzare Esplora query e salvare I pulsanti in Log Analytics?

I pulsanti **Esplora query**, **Salva** e **nuova regola di avviso** non sono disponibili quando l' [ambito della query](log-query/scope.md) è impostato su una risorsa specifica. Per creare avvisi, salvare o caricare una query, Log Analytics necessario avere come ambito un'area di lavoro. Per aprire Log Analytics nel contesto dell'area di lavoro, selezionare **registri** dal menu **monitoraggio di Azure** . L'ultima area di lavoro utilizzata è selezionata, ma è possibile selezionare qualsiasi altra area di lavoro. Vedere l' [ambito della query di log e l'intervallo di tempo in monitoraggio di Azure log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Perché viene ricevuto l'errore: "registrare il provider di risorse ' Microsoft. Insights ' per questa sottoscrizione per abilitare questa query" durante l'apertura di Log Analytics da una macchina virtuale? 
Molti provider di risorse vengono registrati automaticamente, ma potrebbe essere necessario registrare manualmente alcuni provider di risorse. L'ambito per la registrazione è sempre la sottoscrizione. Per altre informazioni, vedere [Provider e tipi di risorse](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Perché non viene ricevuto alcun messaggio di errore di accesso quando si apre Log Analytics da una macchina virtuale? 
Per visualizzare i log della macchina virtuale, è necessario disporre dell'autorizzazione di lettura per le aree di lavoro in cui sono archiviati i log della macchina virtuale. In questi casi, l'amministratore deve concedere all'utente le autorizzazioni in Azure.




## <a name="alerts"></a>Avvisi

### <a name="what-is-an-alert-in-azure-monitor"></a>Che cos'è un avviso in monitoraggio di Azure?
Gli avvisi notificano in modo proattivo quando vengono riscontrate importanti condizioni nei dati di monitoraggio. Consentono di identificare e risolvere i problemi prima che si palesino agli utenti. Sono disponibili più tipi di avvisi:

- Metrica: il valore della metrica supera una soglia.
- Log query: risultati di una query di log che corrispondono ai criteri definiti.
- Log attività: l'evento del log attività corrisponde ai criteri definiti.
- Test Web: risultati dei test di disponibilità corrispondenti ai criteri definiti.


Vedere [Panoramica degli avvisi in Microsoft Azure](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Che cos'è un gruppo di azioni?
Un gruppo di azioni è una raccolta di notifiche e azioni che possono essere attivate da un avviso. Più avvisi possono usare un singolo gruppo di azioni che consente di sfruttare insiemi comuni di notifiche e azioni. Vedere [creare e gestire gruppi di azioni nella portale di Azure](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Che cos'è una regola di azione?
Una regola di azione consente di modificare il comportamento di un set di avvisi che corrispondono a un determinato criterio. In questo modo è possibile eseguire tali requisiti come disabilitare le azioni di avviso durante una finestra di manutenzione. È anche possibile applicare un gruppo di azioni a un set di avvisi anziché applicarli direttamente alle regole di avviso. Vedere [regole di azione](platform/alerts-action-rules.md).


## <a name="agents"></a>Agenti

### <a name="does-azure-monitor-require-an-agent"></a>Monitoraggio di Azure richiede un agente?
Un agente è necessario solo per raccogliere i dati dal sistema operativo e dai carichi di lavoro nelle macchine virtuali. Le macchine virtuali possono trovarsi in Azure, in un altro ambiente cloud o in locale. Vedere [Panoramica degli agenti di monitoraggio di Azure](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Qual è la differenza tra gli agenti di monitoraggio di Azure?
L'estensione diagnostica di Azure è destinata alle macchine virtuali di Azure e raccoglie dati in metriche di monitoraggio di Azure, archiviazione di Azure e hub eventi di Azure. L'agente di Log Analytics è per le macchine virtuali in Azure, un altro ambiente cloud o in locale e raccoglie i dati nei log di monitoraggio di Azure. Dependency Agent richiede l'agente di Log Analytics e i dettagli e le dipendenze del processo raccolti. Vedere [Panoramica degli agenti di monitoraggio di Azure](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Il traffico dell'agente usa la connessione ExpressRoute?
Il traffico verso monitoraggio di Azure usa il circuito ExpressRoute di peering Microsoft. Per una descrizione dei diversi tipi di traffico ExpressRoute, vedere la [documentazione di ExpressRoute](../expressroute/expressroute-faqs.md#supported-services) . 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Come è possibile verificare che l'agente di Log Analytics sia in grado di comunicare con monitoraggio di Azure?
Nel pannello di controllo del computer agente selezionare **impostazioni & sicurezza**, **Microsoft Monitoring Agent** . Nella scheda **log Analytics di Azure (OMS)** un'icona con un segno di spunta verde conferma che l'agente è in grado di comunicare con monitoraggio di Azure. Un'icona di avviso gialla indica che si sono verificati problemi nell'agente. Un motivo comune è che il servizio **Microsoft Monitoring Agent** è stato arrestato. Usare Gestione controllo servizi per riavviare il servizio.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Ricerca per categorie arrestare la comunicazione dell'agente Log Analytics con monitoraggio di Azure?
Per gli agenti connessi direttamente a Log Analytics, aprire il pannello di controllo e selezionare **impostazioni & sicurezza**, **Microsoft Monitoring Agent**. Nella scheda **log Analytics di Azure (OMS)** rimuovere tutte le aree di lavoro elencate. In System Center Operations Manager rimuovere il computer dall'elenco Log Analytics computer gestiti. Operations Manager aggiorna la configurazione dell'agente affinché non invii altri report a Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Quanti dati vengono inviati per ogni agente?
La quantità di dati inviati per ciascun agente dipende da:

* le soluzioni abilitate
* il numero di log e di contatori delle prestazioni che vengono raccolti
* il volume di dati nei log

Per informazioni dettagliate, vedere [gestire l'utilizzo e i costi con i log di monitoraggio di Azure](platform/manage-cost-storage.md) .

Per i computer che possono eseguire l'agente WireData, usare la query seguente per visualizzare la quantità di dati inviati:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Quanta larghezza di banda di rete viene usata dall'agente di gestione Microsoft (MMA) quando si inviano dati a monitoraggio di Azure?
La larghezza di banda è una funzione della quantità di dati inviati. I dati vengono compressi quando vengono inviati in rete.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Come è possibile ricevere una notifica quando la raccolta di dati dall'agente Log Analytics viene arrestata?

Per ricevere una notifica quando la raccolta dati si interrompe, seguire la procedura descritta in [Creare un nuovo avviso del log](platform/alerts-metric.md). Usare le impostazioni seguenti per la regola di avviso:

- **Definire la condizione di avviso**: specificare l'area di lavoro log Analytics come destinazione della risorsa.
- **Criteri di avviso** 
   - **Nome del segnale**: *Ricerca log personalizzata*
   - **Query di ricerca**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Logica di avviso**: **basata sul** *numero di risultati*, **condizione** *maggiore di*, **valore soglia** *0*
   - **Valutato in base a**: **periodo (in minuti)** *30*, **frequenza (in minuti)** *10*
- **Definisci dettagli avviso** 
   - **Nome**: *raccolta dati arrestata*
   - **Gravità**: *avviso*

Specificare un [gruppo di azioni](platform/action-groups.md) nuovo o esistente in modo che quando l'avviso del log corrisponda ai criteri, si riceve una notifica se manca un heartbeat per più di 15 minuti.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Quali sono i requisiti del firewall per gli agenti di monitoraggio di Azure?
Per informazioni dettagliate sui requisiti del firewall, vedere [requisiti del firewall di rete](platform/log-analytics-agent.md#network-firewall-requirements).


## <a name="visualizations"></a>Visualizzazioni

### <a name="why-cant-i-cant-see-view-designer"></a>Perché non è possibile visualizzare Progettazione viste?

Progettazione viste è disponibile solo per gli utenti assegnati con autorizzazioni di collaboratore o superiori nell'area di lavoro Log Analytics.


## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Problemi di configurazione
*Problemi nella configurazione di:*

* [App .NET](app/asp-net-troubleshoot-no-data.md)
* [Monitoraggio di un'applicazione già in esecuzione](app/monitor-performance-live-website-now.md#troubleshoot)
* [Diagnostica di Azure](platform/diagnostics-extension-to-application-insights.md)
* [App Web Java](app/java-troubleshoot.md)

*Non sono disponibili dati dal server*

* [Impostare le eccezioni del firewall](app/ip-addresses.md)
* [Configurare un server ASP.NET](app/monitor-performance-live-website-now.md)
* [Configurare un server Java](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Si può usare Application Insights con ...?

* [App Web in un server IIS in una VM di Azure o in un set di scalabilità di macchine virtuali di Azure](app/azure-vm-vmss-apps.md)
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

### <a name="q14"></a>Quali modifiche apporta Application Insights al progetto?
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
* (Solo nuovi progetti: se si [aggiungono Application Insights a un progetto esistente][start], è necessario eseguire questa operazione manualmente). Inserisce frammenti nel codice client e server per inizializzarli con l'ID di risorsa Application Insights. Ad esempio, in un'app MVC, il codice viene inserito nelle visualizzazioni pagina master/condivisione/\_layout. cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>In che modo è possibile effettuare l'aggiornamento da versioni dell'SDK meno recenti?
Vedere le [note sulla versione](app/release-notes.md) dell'SDK appropriato per il tipo di applicazione.

### <a name="update"></a>In che modo è possibile modificare la risorsa di Azure che riceve i dati del progetto?
In Esplora soluzioni fare clic con il pulsante destro del mouse su `ApplicationInsights.config` e scegliere **Aggiorna Application Insights**. È possibile inviare i dati a una risorsa nuova o esistente in Azure. L'aggiornamento guidato modifica la chiave di strumentazione in ApplicationInsights.config, che determina la destinazione dei dati inviati dall'SDK del server. A meno che non venga deselezionata l'opzione "Aggiorna tutto", modificherà anche la chiave in cui appare nelle pagine Web.

### <a name="what-is-status-monitor"></a>Che cos'è Status Monitor?

Un'app desktop che è possibile usare nel server Web IIS per configurare Application Insights nelle app Web. Non raccoglie dati di telemetria: è possibile interromperlo se non si sta configurando un'app. 

[Altre informazioni](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Quali dati di telemetria vengono raccolti da Application Insights?

Da app Web del server:

* Richieste HTTP
* [Dipendenze](app/asp-net-dependencies.md). Chiamate a: database SQL, chiamate HTTP a servizi esterni, Azure Cosmos DB, tabelle, archiviazione BLOB e code. 
* [Eccezioni](app/asp-net-exceptions.md) e analisi dello stack.
* [Contatori delle prestazioni](app/performance-counters.md) : se si usa [Status Monitor](app/monitor-performance-live-website-now.md), [monitoraggio di Azure per i servizi app](app/azure-web-apps.md), [monitoraggio di Azure per VM o set di scalabilità di macchine virtuali](app/azure-vm-vmss-apps.md)o il [writer Application Insights collectd](app/java-collectd.md).
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

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Come vengono calcolati città, paese/area geografica e altri dati relativi alla località geografica?

Viene cercato l'indirizzo IP (IPv4 o IPv6) del client Web tramite [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/).

* Telemetria del browser: vengono raccolti gli indirizzi IP del mittente.
* Telemetria del server: il modulo di Application Insights raccoglie l'indirizzo IP del client. L'indirizzo non viene raccolto se è impostato `X-Forwarded-For`.
* Per altre informazioni su come vengono raccolti i dati relativi all'indirizzo IP e alla georilevazione in Application Insights fare riferimento a questo [articolo](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


È possibile configurare `ClientIpHeaderTelemetryInitializer` per ottenere l'indirizzo IP da un'intestazione diversa. Ad esempio, in alcuni sistemi viene spostato da un proxy, da un bilanciamento del carico o da una rete CDN a `X-Originating-IP`. [Altre informazioni](https://apmtips.com/blog/2016/07/05/client-ip-address/).

È possibile [usare Power BI](app/export-power-bi.md ) per visualizzare i dati di telemetria della richiesta in una mappa.


### <a name="data"></a>Per quanto tempo vengono conservati i dati nel portale? Tale conservazione è sicura?
Esaminare la [conservazione e la privacy dei dati][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Cosa accade ai dati di telemetria di Application Insight quando un server o un dispositivo perde la connessione con Azure?

Tutti gli SDK, incluso SDK Web, includono "trasporto affidabile" o "trasporto affidabile". Quando il server o il dispositivo perde la connessione con Azure, i dati di telemetria vengono [archiviati localmente nel file System](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (SDK del server) o nell'archiviazione della sessione HTML5 (Web SDK). L'SDK riproverà periodicamente a inviare i dati di telemetria finché il servizio di inserimento non lo considererà "obsoleto" (48 ore per i log, 30 minuti per le metriche). I dati di telemetria non aggiornati verranno eliminati. In alcuni casi, ad esempio quando l'archiviazione locale è piena, il nuovo tentativo non verrà eseguito.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>È possibile che le informazioni personali vengano inviate nei dati di telemetria?

Ciò si verifica se il codice invia tali dati. Può succedere anche se le variabili delle analisi dello stack includono informazioni personali. Il team di sviluppo deve eseguire attività di valutazione dei rischi per garantire che le informazioni personali vengano gestite in maniera appropriata. Vedere [altre informazioni sulla conservazione e privacy dei dati](app/data-retention-privacy.md).

**Tutti** gli ottetti dell'indirizzo Web del client sono sempre impostati su 0 dopo che viene eseguita la ricerca degli attributi relativi alla località geografica.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>La chiave di strumentazione è visibile nell'origine della pagina Web. 

* Questo è una pratica comune nelle soluzioni di monitoraggio.
* Questo valore non può essere usato per appropriarsi dei dati personali.
* Potrebbe essere usato per alterare gli avvisi su dati o trigger.
* Nessun cliente ha segnalato tali problemi.

È possibile:

* Usare due chiavi di strumentazione separate (separate Application Insights risorse) per i dati client e server. Oppure
* Scrivere un proxy che viene eseguito nel server in modo che il client Web invii i dati tramite tale proxy.

### <a name="post"></a>Come visualizzare dati POST in Ricerca diagnostica?
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
* Se non c'è uno script lato client, è possibile [impostare cookie per il server](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Se un utente reale usa il sito in browser diversi o esegue una navigazione privata o in incognito oppure usa computer diversi, viene inclusi più di una volta nei conteggi.
* Per identificare un utente connesso a più computer e browser, aggiungere una chiamata a [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users).

### <a name="q17"></a> In Application Insights sono state abilitate tutte le funzionalità?
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

Usare l'[API REST](https://dev.applicationinsights.io/) per eseguire le query di [Analytics](app/analytics.md).

### <a name="how-can-i-set-an-alert-on-an-event"></a>Come è possibile impostare un avviso per un evento?

Gli avvisi di Azure si applicano solo alle metriche. Creare una metrica personalizzata che supera una soglia di valori ogni volta che si verifica l'evento. Impostare quindi un avviso per la metrica. Si riceverà una notifica ogni volta che la metrica supera la soglia in entrambe le direzioni. non si riceverà alcuna notifica fino al primo attraversamento, indipendentemente dal fatto che il valore iniziale sia alto o basso. C'è sempre una latenza di pochi minuti.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Ci sono costi per il trasferimento dati tra un'app Web di Azure e Application Insights?

* Se l'app Web di Azure è ospitata in un data center in cui è presente un endpoint di raccolta di Application Insights, non è previsto alcun addebito. 
* Se non c'è nessun endpoint nel data center host, la telemetria dell'app sarà soggetta agli [addebiti in uscita di Azure](https://azure.microsoft.com/pricing/details/bandwidth/).

Ciò non dipende da dove è ospitata la risorsa di Application Insights. Dipende solo dalla distribuzione degli endpoint.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>È possibile inviare dati di telemetria al portale di Application Insights?

È consigliabile usare gli SDK di Microsoft e l'[API SDK](app/api-custom-events-metrics.md). Sono disponibili varianti dell'SDK per diverse [piattaforme](app/platforms.md). Gli SDK gestiscono bufferring, compressione, limitazione, tentativi e così via. Tuttavia, lo [schema di inserimento](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) e il [protocollo di endpoint](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) sono pubblici.

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
> ApplicationIdProvider è disponibile a partire da v 2.6.0.



#### <a name="proxy-passthrough"></a>Passthrough del proxy

È possibile ottenere il pass-through del proxy configurando un proxy a livello di computer o di applicazione.
Per altre informazioni, vedere l'articolo di DotNet in [defaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
 
 Web. config di esempio:
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



## <a name="azure-monitor-for-containers"></a>Monitoraggio di Azure per contenitori

Queste domande frequenti Microsoft sono un elenco di domande comuni su Monitoraggio di Azure per i container. Per altre domande sulla soluzione, visitare il [forum di discussione](https://feedback.azure.com/forums/34192--general-feedback) e inviare le proprie domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Quando si esegue una query sulla tabella ContainerLog, i valori delle proprietà Image e Name non vengono visualizzati.

Per la versione dell'agente ciprod12042019 e versioni successive, per impostazione predefinita queste due proprietà non vengono popolate per ogni riga di log per ridurre al minimo i costi legati ai dati di log raccolti. Sono disponibili due opzioni per eseguire una query sulla tabella che include queste proprietà con i rispettivi valori:

#### <a name="option-1"></a>Opzione 1 

Unire le altre tabelle per includere i valori delle proprietà nei risultati.

Modificare le query in modo da includere le proprietà Image e ImageTag dalla tabella ```ContainerInventory``` tramite join alla proprietà ContainerID. È possibile includere la proprietà Name, come in precedenza nella tabella ```ContainerLog```, dal campo ContaineName della tabella KubepodInventory tramite join alla proprietà ContainerID. Questa è l'opzione consigliata.

Nell'esempio seguente viene illustrata una query dettagliata di esempio in cui viene illustrato come ottenere questi valori di campo con join.

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

Se la prima opzione non è comoda a causa delle modifiche di query necessarie, è possibile riabilitare la raccolta di questi campi abilitando l'impostazione ```log_collection_settings.enrich_container_logs``` nella mappa di configurazione dell'agente come descritto nelle [impostazioni di configurazione della raccolta dati](insights/container-insights-agent-config.md).

> [!NOTE]
> La seconda opzione non è consigliata con cluster di grandi dimensioni con più di 50 nodi perché genera chiamate del server API da ogni nodo del cluster per eseguire questa operazione di arricchimento. Questa opzione consente anche di aumentare le dimensioni dei dati per ogni riga di log raccolta.

### <a name="can-i-view-metrics-collected-in-grafana"></a>È possibile visualizzare le metriche raccolte in Grafana?

Il monitoraggio di Azure per i contenitori supporta la visualizzazione delle metriche archiviate nell'area di lavoro Log Analytics nei dashboard di Grafana. È disponibile un modello che è possibile scaricare dal [repository dashboard](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) di Grafana per iniziare e fare riferimento a per informazioni su come eseguire query sui dati aggiuntivi dai cluster monitorati per visualizzare i dashboard Grafana personalizzati. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>È possibile monitorare il cluster del motore AKS con monitoraggio di Azure per i contenitori?

Monitoraggio di Azure per contenitori supporta il monitoraggio dei carichi di lavoro del contenitore distribuiti nei cluster AKS-Engine (in precedenza noti come ACS-Engine) ospitati in Azure. Per altri dettagli e una panoramica dei passaggi necessari per abilitare il monitoraggio per questo scenario, vedere [uso di monitoraggio di Azure per i contenitori per il motore AKS](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Perché non vengono visualizzati i dati nell'area di lavoro Log Analytics?

Se non si riesce a visualizzare i dati nell'area di lavoro Log Analytics in un determinato momento ogni giorno, è possibile che sia stato raggiunto il limite di 500 MB predefinito o il limite giornaliero specificato per controllare la quantità di dati da raccogliere quotidianamente. Quando viene raggiunto il limite per il giorno, la raccolta dei dati si interrompe e riprende solo il giorno successivo. Per esaminare l'utilizzo dei dati ed eseguire l'aggiornamento a un piano tariffario diverso in base ai modelli di utilizzo previsti, vedere l' [utilizzo dei dati di log e i costi](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Quali sono gli Stati del contenitore specificati nella tabella ContainerInventory?

La tabella ContainerInventory contiene informazioni sia sui contenitori arrestati che su quelli in esecuzione. La tabella viene popolata da un flusso di lavoro all'interno dell'agente che esegue una query in Docker per tutti i contenitori (in esecuzione e arrestati) e inoltra tali dati nell'area di lavoro Log Analytics.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Ricerca per categorie risolvere un errore di *registrazione della sottoscrizione mancante* ?

Se viene visualizzato un errore di **registrazione della sottoscrizione mancante per Microsoft. OperationsManagement**, è possibile risolverlo registrando il provider di risorse **Microsoft. OperationsManagement** nella sottoscrizione in cui è definita l'area di lavoro. La documentazione che illustra come eseguire questa operazione è disponibile [qui](../azure-resource-manager/templates/error-register-resource-provider.md).

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>È previsto il supporto per i cluster AKS abilitati per RBAC?

La soluzione di monitoraggio dei contenitori non supporta RBAC, ma è supportata con monitoraggio di Azure per i contenitori. Le informazioni presenti nei pannelli che visualizzano i dati relativi a tali cluster all'interno della pagina dei dettagli della soluzione possono non essere corrette.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Come si abilita la raccolta di log per i contenitori nello spazio dei nomi kube-system tramite Helm?

La raccolta di log dai contenitori nello spazio dei nomi kube-system è disabilitata per impostazione predefinita. È possibile abilitare la raccolta di log impostando una variabile di ambiente in omsagent. Per ulteriori informazioni, vedere la pagina [di GitHub monitoraggio di Azure per contenitori](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) . 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Come si aggiorna omsagent alla versione rilasciata più recente?

Per informazioni su come aggiornare l'agente, vedere [Gestione dell'agente](insights/container-insights-manage-agent.md).

### <a name="how-do-i-enable-multi-line-logging"></a>Come si abilita la registrazione su più righe?

Monitoraggio di Azure per i contenitori attualmente non supporta la registrazione su più righe, ma sono disponibili soluzioni alternative. È possibile configurare tutti i servizi per la scrittura in formato JSON. Docker/Moby eseguirà quindi la scrittura su un'unica riga.

È ad esempio possibile eseguire il wrapping del log come oggetto JSON, come illustrato nell'esempio seguente per un'applicazione node.js di esempio:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Questi dati appariranno come nell'esempio seguente in monitoraggio di Azure per i log quando si esegue una query:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Per informazioni dettagliate sul problema, vedere il [collegamento di GitHub](https://github.com/moby/moby/issues/22920)seguente.

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Ricerca per categorie risolvere gli errori Azure AD quando si abilitano I log Live? 

È possibile che venga visualizzato l'errore seguente: **l'URL di risposta specificato nella richiesta non corrisponde agli URL di risposta configurati per l'applicazione:' < ID applicazione\>'** . La soluzione per risolverla è disponibile nell'articolo [come visualizzare i dati del contenitore in tempo reale con monitoraggio di Azure per i contenitori](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication). 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Perché non è possibile aggiornare il cluster dopo l'onboarding?

Se dopo l'abilitazione di monitoraggio di Azure per i contenitori per un cluster AKS si elimina l'area di lavoro Log Analytics cui il cluster stava inviando i dati, quando si tenta di aggiornare il cluster l'operazione avrà esito negativo. Per risolvere il problema, sarà necessario disabilitare il monitoraggio e quindi riabilitarlo facendo riferimento a un'area di lavoro valida diversa nella sottoscrizione. Quando si tenta di eseguire di nuovo l'aggiornamento del cluster, il processo dovrebbe essere elaborato e completato correttamente.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Quali porte e domini è necessario aprire/inserire nell'elenco elementi consentiti per l'agente?

Vedere i [requisiti del firewall di rete](insights/container-insights-onboard.md#network-firewall-requirements) per le informazioni di configurazione del proxy e del firewall necessarie per l'agente in contenitori con Azure, Azure US Government e Azure Cina 21ViaNet cloud.

## <a name="azure-monitor-for-vms-preview"></a>Monitoraggio di Azure per le macchine virtuali (anteprima)
Le Domande frequenti Microsoft sono un elenco di domande frequenti su Monitoraggio di Azure per le macchine virtuali. Per altre domande sulla soluzione, visitare il [forum di discussione](https://feedback.azure.com/forums/34192--general-feedback) e inviare le proprie domande. Se una domanda viene posta più volte, viene aggiunta a questo articolo per poter essere recuperata in modo rapido e semplice.

### <a name="can-i-onboard-to-an-existing-workspace"></a>È possibile eseguire l'onboarding in un'area di lavoro esistente?
Se le macchine virtuali sono già connesse a un'area di lavoro Log Analytics, è possibile continuare a usare tale area di lavoro quando si esegue l'onboarding per Monitoraggio di Azure per le macchine virtuali, purché si trovi in una delle aree supportate elencate [qui](insights/vminsights-enable-overview.md#prerequisites).

Quando si esegue l'onboarding, si configurano i contatori delle prestazioni per l'area di lavoro in modo che tutte le macchine virtuali che inviano dati all'area di lavoro inizino a raccogliere queste informazioni per la visualizzazione e l'analisi in Monitoraggio di Azure per le macchine virtuali.  Di conseguenza, verranno visualizzati i dati sulle prestazioni di tutte le macchine virtuali connesse all'area di lavoro selezionata.  Le funzionalità di integrità e mappa sono abilitate solo per le macchine virtuali specificate per l'onboarding.

Per altre informazioni sui contatori delle prestazioni abilitati, vedere l'articolo relativo all' [Abilitazione della panoramica](insights/vminsights-enable-overview.md#performance-counters-enabled) .

### <a name="can-i-onboard-to-a-new-workspace"></a>È possibile eseguire l'onboarding in una nuova area di lavoro? 
Se le macchine virtuali non attualmente connesse a un'area di lavoro Log Analytics esistente, è necessario crearne una nuova per archiviare i dati. La creazione di una nuova area di lavoro predefinita avviene automaticamente nel caso in cui si configuri una singola macchina virtuale di Azure per Monitoraggio di Azure per le macchine virtuali tramite il portale di Azure.

Se si sceglie di usare il metodo basato su script, questi passaggi vengono descritti nell'articolo [abilitare monitoraggio di Azure per le macchine virtuali (anteprima) con Azure PowerShell o gestione risorse modello](insights/vminsights-enable-at-scale-powershell.md) . 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Cosa occorre fare se la macchina virtuale invia già informazioni a un'area di lavoro esistente?
Se già si raccolgono dati dalle macchine virtuali, è possibile che sia già stato configurato l'invio di dati a un'area di lavoro Log Analytics esistente.  Se quest'area di lavoro è in una delle aree supportate, è possibile abilitare Monitoraggio di Azure per le macchine virtuali per tale area di lavoro preesistente.  Se l'area di lavoro già in uso non si trova in una delle aree supportate, non è attualmente possibile eseguire l'onboarding per Monitoraggio di Azure per le macchine virtuali.  Microsoft sta lavorando attivamente per supportare altre aree.

>[!NOTE]
>Microsoft configura i contatori delle prestazioni per l'area di lavoro con effetto su tutte le macchine virtuali che inviano informazioni all'area di lavoro, anche se non si è scelto di eseguirne l'onboarding per Monitoraggio di Azure per le macchine virtuali. Per altre informazioni sulla configurazione dei contatori delle prestazioni per l'area di lavoro, vedere la [documentazione](platform/data-sources-performance-counters.md). Per informazioni sui contatori configurati per Monitoraggio di Azure per le macchine virtuali, vedere l'articolo relativo all' [Abilitazione di monitoraggio di Azure per le macchine virtuali](insights/vminsights-enable-overview.md#performance-counters-enabled) .  

### <a name="why-did-my-vm-fail-to-onboard"></a>Perché la macchina virtuale non è riuscita a eseguire l'onboarding?
Quando si esegue l'onboarding di una macchina virtuale di Azure dal portale di Azure, si verificano i passaggi seguenti:

* Viene creata un'area di lavoro Log Analytics predefinita, se tale opzione è stata selezionata.
* I contatori delle prestazioni vengono configurati per l'area di lavoro selezionata. Se questo passaggio non riesce, si noterà che alcuni grafici delle prestazioni e alcune tabelle non contengono i dati relativi alla macchina virtuale di cui è stato eseguito l'onboarding. Per risolvere il problema, è possibile eseguire lo script di PowerShell documentato [qui](insights/vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* L'agente di Log Analytics viene installato nelle macchine virtuali di Azure mediante un'estensione VM, se necessario.  
* Dependency Agent per la mappa di Monitoraggio di Azure per le macchine virtuali viene installato nelle macchine virtuali di Azure mediante un'estensione, se necessario.  
* I componenti di Monitoraggio di Azure che supportano la funzionalità Integrità vengono configurati, se necessario, e la macchina virtuale viene configurata per l'invio dei dati sull'integrità.

Durante il processo di onboarding, viene controllato lo stato di ciascun passaggio, visualizzando una notifica nel portale. La configurazione dell'area di lavoro e l'installazione dell'agente richiedono in genere 5-10 minuti. La visualizzazione dei dati di monitoraggio e sull'integrità nel portale richiedere altri 5-10 minuti.  

Se l'onboarding è stato avviato e vengono visualizzati dei messaggi che indicano che occorre eseguire l'onboarding della macchina virtuale, attendere 30 minuti affinché la macchina virtuale completi il processo. 

### <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Anche se è stato abilitato solo Monitoraggio di Azure per le macchine virtuali, perché vengono visualizzate tutte le macchine virtuali monitorate dalla funzionalità di integrità?
La funzionalità di integrità è abilitata per tutte le macchine virtuali connesse all'area di lavoro Log Analytics, anche quando l'operazione viene avviata per una singola macchina virtuale.

### <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>È possibile modificare il tempo pianificato per la valutazione di una condizione da parte dei criteri di integrità?
No, in questa versione non è possibile modificare il periodo di tempo e la frequenza dei criteri di integrità. 

### <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>È possibile disabilitare i criteri di integrità per una condizione che non è necessario monitorare?
In questa versione i criteri di integrità non possono essere disabilitati.

### <a name="are-the-health-alert-severities-configurable"></a>È possibile configurare i livelli di gravità degli avvisi di integrità?  
La gravità degli avvisi di integrità non può essere modificata. Gli avvisi possono solo essere abilitati o disabilitati. Inoltre, alcuni livelli di gravità degli avvisi vengono aggiornati in base allo stato dei criteri di integrità. 

### <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Se si riconfigurano le impostazioni di un determinato criterio di integrità, è possibile limitarne l'ambito a un'istanza specifica?  
Se si modifica una qualsiasi impostazione di un'istanza di criterio di integrità, nella macchina virtuale di Azure vengono modificate tutte le istanze di criterio di integrità dello stesso tipo. Se ad esempio viene modificata la soglia dell'istanza del criterio di integrità relativo allo spazio libero su disco che corrisponde al disco logico C:, questa soglia viene applicata a tutti gli altri dischi logici individuati e monitorati per la stessa macchina virtuale.

### <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>La funzionalità di integrità esegue il monitoraggio di core e processori logici?
No, i criteri di integrità a livello di singolo processore e di processore logico non sono inclusi per Windows. Per impostazione predefinita, viene monitorato solo l'utilizzo della CPU totale allo scopo di valutare in modo efficace l'utilizzo elevato della CPU in base al numero totale di CPU logiche disponibili per la macchina virtuale di Azure. 

### <a name="are-all-health-criteria-thresholds-configurable"></a>Tutte le soglie dei criteri di integrità sono configurabili?  
Le soglie per i criteri di integrità per una macchina virtuale Windows non sono modificabili perché i relativi stati di integrità sono impostati su *in esecuzione* o *disponibile*. Quando viene eseguita una query sullo stato di integrità dall'[API di monitoraggio del carico di lavoro](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), per il valore *comparisonOperator* (operatore di confronto) viene visualizzato **LessThan** (minore di) o **GreaterThan** (maggiore di) con un valore *threshold* (soglia) pari a **4** per il servizio o l'entità se:
   - Integrità del servizio client DNS - Il servizio non è in esecuzione. 
   - Integrità del servizio client DHCP - Il servizio non in esecuzione. 
   - Integrità del servizio RPC - Il servizio non è in esecuzione. 
   - Integrità del servizio Windows Firewall - Il servizio non è in esecuzione.
   - Integrità del servizio log di eventi di Windows - Il servizio non è in esecuzione. 
   - Integrità del servizio server - Il servizio non è in esecuzione. 
   - Integrità del servizio Gestione remota Windows - Il servizio non è in esecuzione. 
   - Errore o danneggiamento del file system - Il disco logico non è disponibile.

Le soglie per i criteri di integrità di Linux seguenti non possono essere modificate, perché il relativo stato di integrità è già impostato su *true*. Quando viene eseguita una query sullo stato di integrità dall'API di monitoraggio del carico di lavoro per l'entità in base al contesto, per lo stato di integrità viene visualizzato il valore di *comparisonOperator* (operatore di confronto) **LessThan** (minore di) con un valore *threshold* (soglia) pari a **1**:
   - Stato disco logico - Il disco logico non è online/disponibile
   - Stato disco - Il disco non è online/disponibile
   - Stato scheda di rete - Scheda di rete disabilitata

### <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Come si modificano gli avvisi che sono inclusi con la funzionalità di integrità?
Le regole di avviso definite per ogni criterio di integrità non sono esposte nel portale di Azure. È possibile abilitare o disabilitare una regola di avviso per l'integrità solo nell'[API di monitoraggio del carico di lavoro](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Inoltre, nel portale di Azure non è possibile assegnare un [gruppo di azioni di Monitoraggio di Azure](platform/action-groups.md) per gli avvisi di integrità. È possibile usare solo l'API di impostazione delle notifiche per configurare un gruppo di azioni in modo che venga attivato ogni volta che viene generato un avviso di integrità. Attualmente è possibile assegnare gruppi di azioni a una macchina virtuale, in modo che tutti gli *avvisi di integrità* generati per la macchina virtuale attivino gli stessi gruppi di azioni. A differenza degli avvisi tradizionali di Azure, non è possibile usare un gruppo di azioni distinto per ogni regola di avviso di integrità. Inoltre, quando vengono generati avvisi di integrità, sono supportati solo i gruppi di azioni configurati per inviare notifiche tramite posta elettronica o SMS. 

### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>I grafici delle prestazioni della macchina virtuale non contengono alcuni dati o non ne contengono affatto
I grafici delle prestazioni sono stati aggiornati per l'uso dei dati archiviati nella tabella *InsightsMetrics* .  Per visualizzare i dati in questi grafici, sarà necessario eseguire l'aggiornamento per usare la nuova soluzione VM Insights.  Per ulteriori informazioni, vedere le [domande frequenti su GA](insights/vminsights-ga-release-faq.md) .

Se non compaiono i dati sulle prestazioni nella tabella del disco o in alcuni grafici delle prestazioni, è possibile che i contatori delle prestazioni non siano stati configurati nell'area di lavoro. Per risolvere il problema, eseguire lo [script di PowerShell](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell) seguente.


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>In cosa differisce la funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali da Mapping dei servizi?
La funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali si basa su Mapping dei servizi, ma presenta le differenze seguenti:

* È possibile accedere alla vista della mappa dal pannello VM e da Monitoraggio di Azure per le macchine virtuali in Monitoraggio di Azure.
* Le connessioni nella mappa ora sono selezionabili e mostrano i dati delle metriche di connessione nel pannello laterale relativo alla connessione selezionata.
* È disponibile una nuova API che consente di creare le mappe in modo da supportare meglio quelle più complesse.
* Le macchine virtuali monitorate sono ora incluse nel nodo del gruppo client e il grafico ad anello mostra la proporzione tra le macchine virtuali monitorate e non monitorate presenti nel gruppo.  Può anche essere utilizzato per filtrare l'elenco delle macchine quando il gruppo viene espanso.
* Le macchine virtuali monitorate sono ora incluse nei nodi del gruppo di porte server e il grafico ad anello mostra la proporzione tra le macchine monitorate e non monitorate presenti nel gruppo.  Può anche essere utilizzato per filtrare l'elenco delle macchine quando il gruppo viene espanso.
* Lo stile della mappa è stato aggiornato per maggiore coerenza con la mappa delle app di Application Insights.
* I pannelli laterali sono stati aggiornati e non hanno il set completo di integrazione supportato in Mapping dei servizi-Gestione aggiornamenti, Rilevamento modifiche, sicurezza e service desk. 
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

La pagina di panoramica di una VM di Azure mostra i grafici basati sulla misurazione dell'attività dell'host nella VM guest.  Per il grafico di rete nella panoramica della VM di Azure, viene visualizzato solo il traffico che verrà fatturato  Non include il traffico di rete tra le reti virtuali.  I dati e i grafici visualizzati per Monitoraggio di Azure per le macchine virtuali si basano sui dati della macchina virtuale guest e il grafico di rete Visualizza tutto il traffico TCP/IP in ingresso e in uscita verso tale macchina virtuale, inclusa la rete tra le reti virtuali.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Come viene misurato il tempo di risposta per i dati archiviati in VMConnection e visualizzati nel pannello di connessione e nelle cartelle di lavoro?

Il tempo di risposta è un'approssimazione. Poiché non viene instrumentato il codice dell'applicazione, non si è in grado di stabilire l'inizio di una richiesta e l'arrivo della risposta. Al contrario, osserviamo i dati inviati in una connessione e quindi i dati che tornano a tale connessione. L'agente tiene traccia di questi invii e riceve e tenta di associarli: una sequenza di invii, seguita da una sequenza di ricevute viene interpretata come una coppia richiesta-risposta. L'intervallo tra queste operazioni è il tempo di risposta. Include la latenza di rete e il tempo di elaborazione del server.

Questa approssimazione funziona bene per i protocolli basati su richiesta/risposta: una singola richiesta viene inviata alla connessione e viene ricevuta una singola risposta. Questo è il caso per HTTP (S) (senza pipelining), ma non è soddisfatto per altri protocolli.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Sono previste limitazioni se è in vigore il piano tariffario Gratuito di Log Analytics?
Se Monitoraggio di Azure è stato configurato con un'area di lavoro Log Analytics che usa il piano tariffario *Gratuito*, la funzionalità della mappa Monitoraggio di Azure per le macchine virtuali supporterà solo cinque computer connessi all'area di lavoro. Se si hanno cinque macchine virtuali connesse a un'area di lavoro gratuita, si disconnette una delle macchine virtuali e in un secondo momento si connette una nuova macchina virtuale, la nuova macchina virtuale non viene monitorata e riflessa nella pagina della mappa.  

In questo caso, verrà visualizzata l'opzione **Prova adesso** quando si apre la macchina virtuale e si seleziona **Insights (preview)** (Insights - Anteprima) nel riquadro a sinistra, anche dopo che è già stato installato nella macchina virtuale.  Non vengono tuttavia visualizzate le normali opzioni come avverrebbe normalmente se non fosse stato eseguito l'onboarding di questa macchina virtuale in Monitoraggio di Azure per le macchine virtuali. 


## <a name="next-steps"></a>Passaggi successivi
Se la domanda non è disponibile, è possibile fare riferimento ai forum seguenti per ulteriori domande e risposte.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Per commenti generali su monitoraggio di Azure, visitare il [Forum dei commenti](https://feedback.azure.com/forums/34192--general-feedback).
