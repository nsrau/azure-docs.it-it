---
title: Monitorare le risorse di Azure con Monitoraggio di Azure Documenti Microsoft
description: Descrive come raccogliere e analizzare i dati di monitoraggio dalle risorse in Azure usando Monitoraggio di Azure.Describes how to collect and analyze monitoring data from resources in Azure using Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249269"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitoraggio delle risorse di Azure con Monitoraggio di Monitoraggio di AzureMonitoring Azure resources with Azure Monitor
Quando si dispone di applicazioni e processi aziendali critici che si basano sulle risorse di Azure, si vuole monitorare tali risorse per la disponibilità, le prestazioni e il funzionamento. Questo articolo descrive i dati di monitoraggio generati dalle risorse di Azure e come è possibile usare le funzionalità di Monitoraggio di Azure per analizzare e avvisare questi dati.

> [!IMPORTANT]
> Questo articolo si applica a tutti i servizi in Azure che usano Monitoraggio di Azure.This article applies to all services in Azure that use Azure Monitor. Le risorse di calcolo, incluse le macchine virtuali e il servizio app, generano gli stessi dati di monitoraggio descritti di seguito, ma dispongono anche di un sistema operativo guest che può anche generare log e metriche. Vedere la documentazione di monitoraggio per questi servizi per informazioni dettagliate su come raccogliere e analizzare questi dati.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
Monitoraggio di Azure è un servizio di monitoraggio dello stack completo in Azure che offre un set completo di funzionalità per monitorare le risorse di Azure oltre alle risorse in altri cloud e in locale. La piattaforma dati di [Monitoraggio di Azure](../platform/data-platform.md) raccoglie i dati in [log](../platform/data-platform-logs.md) e [metriche](../platform/data-platform-metrics.md) in cui possono essere analizzati insieme usando un set completo di strumenti di monitoraggio come descritto nelle sezioni seguenti.

- [Cosa è possibile fare con le metriche di Monitoraggio di Azure?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Cosa è possibile fare con i log di Monitoraggio di Azure?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Non appena si crea una risorsa di Azure, Monitoraggio di Azure viene abilitato e inizia a raccogliere metriche e log attività che è possibile [visualizzare e analizzare nel portale](#monitoring-in-the-azure-portal)di Azure. Con alcune configurazioni, è possibile raccogliere dati di monitoraggio aggiuntivi e abilitare funzionalità aggiuntive. Vedere [dati di monitoraggio](#monitoring-data) di seguito per informazioni dettagliate sui requisiti di configurazione.


## <a name="costs-associated-with-monitoring"></a>Costi associati al monitoraggio
Non sono previsti costi per l'analisi dei dati di monitoraggio raccolti per impostazione predefinita. Sono inclusi gli elementi seguenti:

- Raccolta delle metriche della piattaforma e analisi con Esplora metriche.
- Raccolta del log attività e analisi nel portale di Azure.Collecting Activity log and analyzing it in the Azure portal.
- Creazione di una regola di avviso del log attività.

Non sono previsti costi di Monitoraggio di Azure per la raccolta e l'esportazione di log e metriche, ma potrebbero essere presenti costi correlati associati alla destinazione:There are no Azure Monitor costs for collecting and exporting logs and metrics, but there may be related costs associated with the destination:

- Costi associati all'inserimento e alla conservazione dei dati durante la raccolta di log e metriche nell'area di lavoro di Log Analytics.Costs associated with data ingestion and retention when collecting logs and metrics in Log Analytics workspace. Vedere [Prezzi di Monitoraggio di Azure per Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Costi associati all'archiviazione dei dati durante la raccolta di log e metriche in un account di archiviazione di Azure.Costs associated with data storage when collecting logs and metrics to an Azure storage account. Vedere [Prezzi di Archiviazione di Azure per l'archiviazione BLOB.](https://azure.microsoft.com/pricing/details/storage/blobs/)
- Costi associati allo streaming dell'hub eventi durante l'inoltro di log e metriche agli hub eventi di Azure.Costs associated with event hub streaming when forwarding logs and metrics to Azure Event Hubs. Vedere [Prezzi di Hub di eventi di Azure.](https://azure.microsoft.com/pricing/details/event-hubs/)

Potrebbero esserci costi di Monitoraggio di Azure associati a quanto segue. Vedere [Prezzi di Monitoraggio di Azure:](https://azure.microsoft.com/pricing/details/monitor/)

- Esecuzione di una query di log.
- Creazione di una regola di avviso di metrica o log query.
- Invio di una notifica da qualsiasi regola di avviso.
- Accesso alle metriche tramite API.

## <a name="monitoring-data"></a>Dati di monitoraggio
Le risorse in Azure generano [log](../platform/data-platform-logs.md) e [metriche,](../platform/data-platform-metrics.md) illustrato nel diagramma seguente. Fare riferimento alla documentazione per ogni servizio di Azure per i dati specifici che generano e le eventuali soluzioni o informazioni aggiuntive fornite.

![Panoramica](media/monitor-azure-resource/logs-metrics.png)



- [Metriche della piattaforma:](../platform/data-platform-metrics.md) valori numerici che vengono raccolti automaticamente a intervalli regolari e descrivono alcuni aspetti di una risorsa in un determinato momento. 
- [Log delle risorse:](../platform/platform-logs-overview.md) fornire informazioni dettagliate sulle operazioni eseguite all'interno di una risorsa di Azure (il piano dati), ad esempio per ottenere un segreto da un insieme di credenziali delle chiavi o effettuare una richiesta a un database. Il contenuto e la struttura dei log delle risorse variano in base al servizio e al tipo di risorsa di Azure.The content and structure of resource logs varies by the Azure service and resource type.
- [Log attività:](../platform/platform-logs-overview.md) fornisce informazioni dettagliate sulle operazioni su ogni risorsa di Azure nella sottoscrizione dall'esterno (piano di gestione), ad esempio la creazione di una nuova risorsa o l'avvio di una macchina virtuale. Si tratta di informazioni su cosa, chi e quando per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguite sulle risorse nella sottoscrizione.


## <a name="configuration-requirements"></a>Requisiti di configurazione

### <a name="configure-monitoring"></a>Configurare il monitoraggio
Alcuni dati di monitoraggio vengono raccolti automaticamente, ma potrebbe essere necessario eseguire alcune operazioni di configurazione a seconda dei requisiti. Vedere le informazioni seguenti per informazioni specifiche per ogni tipo di dati di monitoraggio.

- [Metriche della piattaforma:](../platform/data-platform-metrics.md) le metriche della piattaforma vengono raccolte automaticamente nelle [metriche](../platform/data-platform-metrics.md) di Monitoraggio di Azure senza alcuna configurazione richiesta. Creare un'impostazione di diagnostica per inviare voci ai log di monitoraggio di Azure o per inoltrarle all'esterno di Azure.Create a diagnostic setting to send entries to Azure Monitor Logs or to forward them outside of Azure.
- [Log delle risorse:](../platform/platform-logs-overview.md) i log delle risorse vengono generati automaticamente dalle risorse di Azure ma non vengono raccolti senza un'impostazione di diagnostica.  Creare un'impostazione di diagnostica per inviare voci ai log di monitoraggio di Azure o per inoltrarle all'esterno di Azure.Create a diagnostic setting to send entries to Azure Monitor Logs or to forward them outside of Azure.
- [Log attività:](../platform/platform-logs-overview.md) il log attività viene raccolto automaticamente senza alcuna configurazione richiesta e può essere visualizzato nel portale di Azure.Activity log - The Activity log is collected automatically with no configuration required and can be view in the Azure portal. Creare un'impostazione di diagnostica per copiarli nei log di monitoraggio di Azure o per inoltrarli all'esterno di Azure.Create a diagnostic setting to copy them to Azure Monitor Logs or to forward them outside of Azure.

### <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
La raccolta dei dati nei log di Monitoraggio di Azure richiede un'area di lavoro Log Analytics.Collecting data into Azure Monitor Logs requires a Log Analytics workspace. È possibile avviare rapidamente il monitoraggio del servizio creando una nuova area di lavoro, ma potrebbe essere utile usare un'area di lavoro che raccoglie dati da altri servizi. Per informazioni dettagliate sulla creazione di un'area di lavoro e Progettazione della distribuzione dei log di Monitoraggio di Azure, vedere [Creare un'area](../learn/quick-create-workspace.md) di lavoro di Log Analytics nel portale di Azure per informazioni dettagliate sulla creazione di un'area di lavoro e [Progettazione](../platform/design-logs-deployment.md) della distribuzione dei log di Monitoraggio di Azure per determinare la progettazione dell'area di lavoro migliore per i requisiti. Se si usa un'area di lavoro esistente nell'organizzazione, saranno necessarie le autorizzazioni appropriate, come descritto in [Gestire l'accesso per registrare dati e aree di lavoro in Monitoraggio di Azure](../platform/manage-access.md). 





## <a name="diagnostic-settings"></a>Impostazioni di diagnostica
Le impostazioni di diagnostica definiscono dove devono essere inviati i log delle risorse e le metriche per una determinata risorsa. Le destinazioni possibili sono:

- [Area](../platform/resource-logs-collect-workspace.md) di lavoro di Log Analytics che consente di analizzare i dati con altri dati di monitoraggio raccolti da Monitoraggio di Azure usando potenti query di log e anche di sfruttare altre funzionalità di Monitoraggio di Azure, ad esempio avvisi e visualizzazioni di log. 
- [Hub eventi](../platform/resource-logs-stream-event-hubs.md) per lo streaming di dati a sistemi esterni, ad esempio SIEM di terze parti e altre soluzioni di analisi dei log. 
- [Account di archiviazione](../platform/resource-logs-collect-storage.md) di Azure utile per il controllo, l'analisi statica o il backup.

Seguire la procedura descritta in [Creare l'impostazione di diagnostica per raccogliere i log e](../platform/diagnostic-settings.md) le metriche della piattaforma in Azure per creare e gestire le impostazioni di diagnostica tramite il portale di Azure.Follow the procedure in Create diagnostic setting to collect platform logs and metrics in Azure to create and manage diagnostic settings through the Azure portal. Vedere [Creare l'impostazione di diagnostica in Azure usando un modello](../platform/diagnostic-settings-template.md) di Resource Manager per definirle in un modello e abilitare il monitoraggio completo per una risorsa quando viene creata.


## <a name="monitoring-in-the-azure-portal"></a>Monitoraggio nel portale di Azure
 È possibile accedere ai dati di monitoraggio per la maggior parte delle risorse di Azure dal menu della risorsa nel portale di Azure.You can access monitoring data for most Azure resources from the resource's menu in the Azure portal. In questo modo sarà possibile accedere ai dati di una singola risorsa usando gli strumenti standard di Monitoraggio di Azure.This will give you access to a single resource's data using standard Azure Monitor tools. Alcuni servizi di Azure forniranno opzioni diverse, pertanto è necessario fare riferimento alla documentazione relativa a tale servizio per ulteriori informazioni. Usare il menu **Monitoraggio di Azure** per analizzare i dati da tutte le risorse monitorate. 

### <a name="overview"></a>Panoramica
Molti servizi includeranno i dati di monitoraggio nella pagina **Panoramica** come riepilogo rapido del proprio funzionamento. Questo riepilogo sarà in genere basato su un sottoinsieme delle metriche della piattaforma archiviate nelle metriche di Monitoraggio di Azure. Altre opzioni di monitoraggio saranno in genere disponibili in una sezione **Monitoraggio** dei servizi. .

![Pagina di panoramica](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Approfondimenti e soluzioni 
Alcuni servizi forniranno strumenti che vanno oltre le funzionalità standard di Monitoraggio di Azure.Some services will provide tools beyond the standard features of Azure Monitor. [Le informazioni dettagliate](../insights/insights-overview.md) offrono un'esperienza di monitoraggio personalizzata basata sulla piattaforma dati di Azure Monitor e sulle funzionalità standard. [Le soluzioni](../insights/solutions.md) forniscono una logica di monitoraggio predefinita basata sui log di Monitoraggio di Azure.Solutions provide predefined monitoring logic built on Azure Monitor Logs. 

Se un servizio ha un'analisi dettagliata di Monitoraggio di Azure, è possibile accedervi da **Monitoraggio** nel menu di ogni risorsa. Accedi a tutte le informazioni dettagliate e soluzioni dal menu **Monitoraggio di Azure.**

![Informazioni dettagliate](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metriche
Analizzare le metriche nel portale di Azure usando [Esplora metriche](../platform/metrics-getting-started.md) disponibile dalla voce di menu **Metriche** per la maggior parte dei servizi. Questo strumento consente di lavorare con singole metriche o combinare più metriche per identificare correlazioni e tendenze. 

- Per informazioni di base sull'uso di Esplora metriche, [vedere Introduzione a Azure Metrics Explorer.See Getting started with Azure Metrics Explorer](../platform/metrics-getting-started.md) for the basics of using metrics explorer.
- Vedere [Funzionalità avanzate di Azure Metrics Explorer](../platform/metrics-charts.md) per funzionalità avanzate di Esplora metriche, ad esempio l'uso di più metriche e l'applicazione di filtri e divisione.

![Metriche](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Log attività 
Visualizzare le voci nel log attività nel portale di Azure con il filtro iniziale impostato sulla risorsa corrente. Copiare il log attività in un'area di lavoro di Log Analytics per accedervi e utilizzarlo nelle query di log e nelle cartelle di lavoro. 

- Per informazioni dettagliate sulla visualizzazione del log attività e sul recupero delle voci usando diversi metodi, vedere Visualizzare e recuperare gli eventi del log attività di Azure.See View and [retrieve Azure Activity log events](../platform/activity-log-view.md) for details on viewing the Activity log and retrieving entries using a variety of methods.
- Vedere la documentazione per il servizio di Azure per gli eventi specifici che vengono registrati.

![Log attività](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure
I log di Monitoraggio di Azure consolidano i log e le metriche di più servizi e altre origini dati per l'analisi con un potente strumento di query. Come descritto in precedenza, creare un'impostazione di diagnostica per raccogliere le metriche della piattaforma, il log attività e i log delle risorse in un'area di lavoro di Log Analytics in Monitoraggio di Azure.As described above, create a diagnostic setting to collect platform metrics, activity log, and resource logs into a Log Analytics workspace in Azure Monitor.

[Log Analytics](../log-query/get-started-portal.md) consente di usare le query di [log,](../log-query/log-query-overview.md)una potente funzionalità di Monitoraggio di Azure che consente di eseguire un'analisi avanzata dei dati di log usando un linguaggio di query completo. Aprire Log Analytics da **Logs** nel menu **Monitoraggio** per una risorsa di Azure per usare le query di log usando la risorsa come ambito della [query.](../log-query/scope.md#query-scope) In questo modo è possibile analizzare i dati in più tabelle solo per tale risorsa. Usare **Log** dal menu Monitoraggio di Azure per accedere ai log per tutte le risorse. 

- Vedere Introduzione alle query di [log in Monitoraggio di Azure](../log-query/get-started-queries.md) per un'esercitazione sull'uso del linguaggio di query usato per scrivere query di log.
- Per informazioni su come vengono raccolti i log delle risorse nei log di Monitoraggio di Azure e dettagli su come accedervi in una query, vedere Raccogliere log delle risorse di [Azure nell'area](../platform/resource-logs-collect-workspace.md) di lavoro Log Analytics in Monitoraggio di Azure.See Collect Azure resource logs in Log Analytics workspace in Azure Monitor for information on how resource logs are collected in Azure Monitor Logs and details for how to access them in a query.
- Vedere [Modalità](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) di raccolta per una spiegazione della struttura dei dati del log delle risorse nei log di monitoraggio di Azure.See Collection mode for an explanation of how resource log data is structured in Azure Monitor Logs.
- Vedere la documentazione per ogni servizio di Azure per informazioni dettagliate sulla relativa tabella in Log di monitoraggio di Azure.See the documentation for each Azure service for details on its table in Azure Monitor Logs.

![Log](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitoraggio dalla riga di comando
È possibile accedere al monitoraggio dei dati raccolti dalla risorsa da una riga di comando o includere in uno script usando [Azure PowerShell](/powershell/azure/) o l'interfaccia della riga di comando di [Azure.](/cli/azure/) 

- Vedere Informazioni di [riferimento sulle metriche CLI](/cli/azure/monitor/metrics) per accedere ai dati delle metriche dall'interfaccia della riga di comando.
- Vedere Informazioni di riferimento su [Analisi dei log dell'interfaccia della riga di](/cli/azure/ext/log-analytics/monitor/log-analytics) comando per l'accesso ai dati dei log di Monitoraggio di Azure usando una query di log dall'interfaccia della riga di comando.
- Vedere Informazioni di riferimento sulle metriche di Azure PowerShell per accedere ai dati delle metriche da Azure PowerShell.See [Azure PowerShell metrics reference](/powershell/module/azurerm.insights/get-azurermmetric) for accessing metric data from Azure PowerShell.
- Vedere Informazioni di riferimento sulle query del log di Azure PowerShell per accedere ai dati dei log di Monitoraggio di Azure usando una query di log da Azure PowerShell.See [Azure PowerShell log query reference](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) for accessing Azure Monitor Logs data using a log query from Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitoraggio dall'API RESTMonitoring from REST API
Includere i dati di monitoraggio raccolti dalla risorsa in un'applicazione personalizzata usando un'API REST.

- Per informazioni dettagliate sull'accesso alle metriche dall'API REST di Monitoraggio di Azure, vedere Procedura [dettagliata dell'API REST](../platform/rest-api-walkthrough.md) di monitoraggio di Azure.See Azure Monitoring REST API walkthrough for details on accessing metrics from the Azure Monitor REST API.
- Vedere API REST di Azure Log Analytics per informazioni sull'accesso ai dati dei log di Monitoraggio di Azure usando una query di log da Azure PowerShell.See [Azure Log Analytics REST API](https://dev.loganalytics.io/) for information on accessing Azure Monitor Logs data using a log query from Azure PowerShell.

## <a name="alerts"></a>Avvisi
[Gli avvisi](../platform/alerts-overview.md) inviano una notifica proattiva e intervengono potenzialmente quando vengono rilevate condizioni importanti nei dati di monitoraggio. Si crea una regola di avviso che definisce una destinazione per l'avviso, le condizioni per la creazione di un avviso e le azioni da eseguire in risposta.

Tipi diversi di dati di monitoraggio vengono utilizzati per diversi tipi di regole di avviso.

- [Avviso del log attività:](../platform/alerts-activity-log.md) consente di creare un avviso quando viene creata una voce nel log attività che corrisponde a criteri specifici. In questo modo è possibile ricevere una notifica, ad esempio quando viene creato un particolare tipo di risorsa o se una modifica della configurazione non riesce.
- [Avviso metrica:](../platform/alerts-metric.md) creare un avviso quando un valore di metrica supera una determinata soglia. Gli avvisi metrici sono più reattivi rispetto ad altri avvisi e possono essere risolti automaticamente quando il problema viene risolto.
- [Avviso query log:](../platform/alerts-log.md) eseguire una query di log a intervalli regolari e creare un avviso se viene rilevata una determinata condizione. In questo modo è possibile eseguire analisi complesse su più set di dati e .

Usare **Avvisi** dal menu di una risorsa per visualizzare gli avvisi e gestire le regole di avviso per tale risorsa. Solo gli avvisi del log attività e gli avvisi di metrica usano singole risorse di Azure come destinazione. Gli avvisi di query di log usano un'area di lavoro di Log Analytics come destinazione e si basano su una query che può accedere a tutti i log archiviati in tale area di lavoro. Usare il menu Monitoraggio di Azure per visualizzare e gestire gli avvisi per tutte le risorse e le regole di avviso di query di gestione log.

- Per informazioni dettagliate sulla creazione di regole di avviso, vedere gli articoli relativi ai diversi tipi di avvisi precedenti.
- Per informazioni dettagliate sulla creazione di un gruppo di azioni che consente di gestire le risposte agli avvisi, vedere Creare e gestire gruppi di azioni [nel portale di Azure.See Create](../platform/action-groups.md) and manage action groups in the Azure portal for details on creating an action group which allows you to manage responses to alerts.



## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sui log delle risorse per i diversi servizi di Azure, vedere [Servizi, schemi e categorie supportati per i log delle risorse di Azure.See Supported services, schemas, and categories for Azure Resource Logs](../platform/diagnostic-logs-schema.md) for details of resource logs for different Azure services.  
