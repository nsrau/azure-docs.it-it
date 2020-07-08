---
title: Monitorare le risorse di Azure con Monitoraggio di Azure | Microsoft Docs
description: Viene descritto come raccogliere e analizzare i dati di monitoraggio generati dalle risorse di Azure tramite Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 430b1c044ac5fc22dbf3a4f4df33ff9017e21d6d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361956"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitoraggio delle risorse di Azure con Monitoraggio di Azure
Quando si usano applicazioni e processi aziendali critici basati sulle risorse di Azure, è consigliabile monitorare tali risorse per verificarne disponibilità, prestazioni e funzionamento. Questo articolo descrive i dati di monitoraggio generati dalle risorse di Azure e come è possibile usare le funzionalità di Monitoraggio di Azure per analizzare e creare avvisi su tali dati.

> [!IMPORTANT]
> Questo articolo si applica a tutti i servizi di Azure che usano Monitoraggio di Azure. Le risorse di calcolo, tra cui le macchine virtuali e il servizio app, generano gli stessi dati di monitoraggio descritti in questo articolo, ma dispongono inoltre di un sistema operativo guest che può generare anche log e metriche. Per informazioni dettagliate su come raccogliere e analizzare questi dati, vedere la documentazione di monitoraggio relativa a questi servizi.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
Monitoraggio di Azure è un servizio di monitoraggio completo dello stack di Azure che offre un set completo di funzionalità per monitorare le risorse e di Azure e le risorse che si trovano in altri cloud e in locale. La [piattaforma dati di Monitoraggio di Azure](../platform/data-platform.md) raccoglie i dati in [log](../platform/data-platform-logs.md) e [metriche](../platform/data-platform-metrics.md), dove possono essere analizzati insieme grazie a un set completo di strumenti di monitoraggio, come descritto nelle sezioni seguenti.

- [Che cosa è possibile fare con le metriche di Monitoraggio di Azure?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Che cosa è possibile fare con i log di Monitoraggio di Azure?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Non appena si crea una risorsa di Azure, Monitoraggio di Azure viene abilitato e inizia a raccogliere metriche e log attività, che è possibile [visualizzare e analizzare nel portale di Azure](#monitoring-in-the-azure-portal). Con alcune operazioni di configurazione, è possibile raccogliere dati di monitoraggio aggiuntivi e abilitare funzionalità aggiuntive. Per informazioni dettagliate sui requisiti di configurazione, vedere la sezione [Dati di monitoraggio](#monitoring-data) più avanti.


## <a name="costs-associated-with-monitoring"></a>Costi associati al monitoraggio
Non sono previsti costi per l'analisi dei dati di monitoraggio raccolti per impostazione predefinita. Sono inclusi gli elementi seguenti:

- Raccolta delle metriche della piattaforma e successiva analisi con Esplora metriche.
- Raccolta del log attività e successiva analisi nel portale di Azure.
- Creazione di una regola di avviso del log attività.

Non sono previsti costi correlati a Monitoraggio di Azure per la raccolta e l'esportazione di log e metriche, ma potrebbero esserci costi associati alla destinazione:

- Costi associati all'inserimento e alla conservazione dei dati quando si raccolgono log e metriche nell'area di lavoro di Log Analytics. Vedere [Prezzi di Monitoraggio di Azure per Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).
- Costi associati all'archiviazione dei dati quando si raccolgono log e metriche in un account di Archiviazione di Azure. Vedere [Prezzi di Archiviazione di Azure per l'archiviazione BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Costi associati al flusso di Hub eventi quando si inviano log e metriche ad Hub eventi di Azure. Vedere [Prezzi di Hub eventi di Azure](https://azure.microsoft.com/pricing/details/event-hubs/).

È possibile che siano previsti costi correlati a Monitoraggio di Azure per le attività seguenti. Vedere [Prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/):

- Esecuzione di una query di log.
- Creazione di una regola di avviso per una query di metrica o di log.
- Invio di una notifica da una regola di avviso.
- Accesso alle metriche tramite l'API.

## <a name="monitoring-data"></a>Dati di monitoraggio
Le risorse di Azure generano i [log](../platform/data-platform-logs.md) e le [metriche](../platform/data-platform-metrics.md) illustrati nel diagramma seguente. Fare riferimento alla documentazione relativa a ogni servizio di Azure per i dati specifici che genera ed eventuali soluzioni o informazioni dettagliate aggiuntive che fornisce.

![Panoramica](media/monitor-azure-resource/logs-metrics.png)



- [Metriche della piattaforma](../platform/data-platform-metrics.md): i valori numerici che vengono raccolti automaticamente a intervalli regolari e che descrivono un aspetto di una risorsa in un determinato momento. 
- [Log risorse](../platform/platform-logs-overview.md): forniscono informazioni sulle operazioni eseguite all'interno di una risorsa di Azure (piano dati), ad esempio sul recupero di un segreto da un insieme di credenziali delle chiavi o sull'esecuzione di una richiesta a un database. Il contenuto e la struttura dei log risorse variano in base al servizio di Azure e al tipo di risorsa.
- [Log attività](../platform/platform-logs-overview.md): fornisce informazioni dettagliate sulle operazioni eseguite dall'esterno su ogni risorsa di Azure presente nella sottoscrizione (il piano di gestione), ad esempio la creazione di una nuova risorsa o l'avvio di una macchina virtuale. In altre parole, si tratta di informazioni su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse della sottoscrizione.


## <a name="configuration-requirements"></a>Requisiti di configurazione

### <a name="configure-monitoring"></a>Configurare il monitoraggio
Alcuni dati di monitoraggio vengono raccolti automaticamente, ma potrebbe essere necessario eseguire alcune operazioni di configurazione, a seconda delle esigenze. Per informazioni specifiche su ogni tipo di dati di monitoraggio, vedere le sezioni seguenti.

- [Metriche della piattaforma](../platform/data-platform-metrics.md): le metriche della piattaforma vengono raccolte automaticamente nelle [metriche di Monitoraggio di Azure](../platform/data-platform-metrics.md) senza dover eseguire alcuna operazione di configurazione. Creare un'impostazione di diagnostica per inviare le voci ai log di Monitoraggio di Azure o inoltrarli all'esterno di Azure.
- [Log risorse](../platform/platform-logs-overview.md): i log risorse vengono generati automaticamente dalle risorse di Azure, ma non vengono raccolti senza un'impostazione di diagnostica.  Creare un'impostazione di diagnostica per inviare le voci ai log di Monitoraggio di Azure o inoltrarli all'esterno di Azure.
- [Log attività](../platform/platform-logs-overview.md): il log attività viene raccolto automaticamente senza alcuna operazione di configurazione e può essere visualizzato nel portale di Azure. Creare un'impostazione di diagnostica per copiarlo nei log di Monitoraggio di Azure o per inviarlo all'esterno di Azure.

### <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Per raccogliere dati nei log di Monitoraggio di Azure è necessaria un'area di lavoro Log Analytics. È possibile avviare rapidamente il monitoraggio del servizio creando una nuova area di lavoro, sebbene possa essere utile usare un'area di lavoro che raccoglie già dati da altri servizi. Vedere [Creare un'area di lavoro Log Analytics nel portale di Azure](../learn/quick-create-workspace.md) per informazioni dettagliate sulla creazione di un'area di lavoro e [Progettazione della distribuzione dei log di Monitoraggio di Azure](../platform/design-logs-deployment.md) per determinare la progettazione dell'area di lavoro più adatta alle proprie esigenze. Se si usa un'area di lavoro esistente nell'organizzazione, sarà necessario disporre delle autorizzazioni appropriate, come descritto in [Gestire l'accesso ai dati di log e alle aree di lavoro in Monitoraggio di Azure](../platform/manage-access.md). 





## <a name="diagnostic-settings"></a>Impostazioni di diagnostica
Le impostazioni di diagnostica definiscono la destinazione a cui devono essere inviati i log e le metriche inerenti a una risorsa specifica. Le possibili destinazioni sono:

- [Area di lavoro Log Analytics](../platform/resource-logs-collect-workspace.md), che consente di analizzare i dati con altri dati di monitoraggio raccolti da Monitoraggio di Azure tramite efficaci query di log, nonché sfruttare altre funzionalità di Monitoraggio di Azure, ad esempio avvisi e visualizzazioni dei log. 
- [Hub eventi](../platform/resource-logs-stream-event-hubs.md), per trasmettere i dati a sistemi esterni, quali SIEM di terze parti e altre soluzioni di analisi dei log. 
- [Account di archiviazione di Azure](../platform/resource-logs-collect-storage.md), utile per operazioni di controllo, analisi statistica o backup.

Seguire la procedura descritta in [Creare un'impostazione di diagnostica per raccogliere i log e le metriche in Azure](../platform/diagnostic-settings.md) per creare e gestire impostazioni di diagnostica tramite il portale di Azure. Vedere [Creare un'impostazione di diagnostica in Azure usando un modello di Resource Manager](../platform/diagnostic-settings-template.md) per definirli in un modello e abilitare il monitoraggio completo per una risorsa al momento della creazione.


## <a name="monitoring-in-the-azure-portal"></a>Monitoraggio nel portale di Azure
 È possibile accedere ai dati di monitoraggio per la maggior parte delle risorse di Azure dal menu della risorsa nel portale di Azure. In questo modo, sarà possibile accedere ai dati di una singola risorsa usando gli strumenti standard di Monitoraggio di Azure. Per alcuni servizi di Azure saranno disponibili opzioni diverse ed è quindi necessario fare riferimento alla documentazione relativa a ciascun servizio per informazioni aggiuntive. Usare il menu di **Monitoraggio di Azure** per analizzare i dati di tutte le risorse monitorate. 

### <a name="overview"></a>Panoramica
Molti servizi includeranno i dati di monitoraggio nella pagina **Panoramica** come riepilogo rapido del proprio funzionamento. Questo riepilogo sarà in genere basato su un sottoinsieme delle metriche della piattaforma archiviate nelle metriche di Monitoraggio di Azure. Altre opzioni di monitoraggio saranno in genere disponibili in una sezione di **monitoraggio** del menu del servizio.

![Pagina di panoramica](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni 
Alcuni servizi forniranno strumenti aggiuntivi oltre alle funzionalità standard di Monitoraggio di Azure. Le [informazioni dettagliate](../insights/insights-overview.md) offrono un'esperienza di monitoraggio personalizzata basata sulla piattaforma dati e sulle funzionalità standard di Monitoraggio di Azure. Le [soluzioni](../insights/solutions.md) offrono la logica di monitoraggio predefinita integrata nei log di Monitoraggio di Azure. 

Se un servizio dispone di informazioni dettagliate di Monitoraggio di Azure, è possibile accedervi dalla voce **Monitoraggio** del menu di ogni risorsa. È possibile accedere a tutte le soluzioni e le informazioni dettagliate dal menu **Monitoraggio di Azure**.

![Informazioni dettagliate](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Metriche
È possibile analizzare le metriche disponibili nel portale di Azure usando [Esplora metriche](../platform/metrics-getting-started.md) accessibile dalla voce di menu **Metriche** della maggior parte dei servizi. Questo strumento consente di usare singole metriche o combinare più metriche per identificare correlazioni e tendenze. 

- Per informazioni di base sull'uso di Esplora metriche di Azure, vedere [Introduzione a Esplora metriche di Azure](../platform/metrics-getting-started.md).
- Vedere [Funzionalità avanzate di Esplora metriche di Azure](../platform/metrics-charts.md) per informazioni sulle funzionalità avanzate di Esplora metriche, tra cui l'uso di più metriche e l'applicazione di filtri e suddivisioni.

![Metriche](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Log attività 
È possibile visualizzare le voci disponibili nel log attività del portale di Azure con il filtro iniziale impostato sulla risorsa corrente. Copiare il log attività in un'area di lavoro Log Analytics per poterlo usare in query e cartelle di lavoro di log. 

- Vedere [Visualizzare e recuperare eventi del log attività di Azure](../platform/activity-log-view.md) per informazioni sui vari modi disponibili per visualizzare il log attività e recuperare le voci desiderate.
- Vedere la documentazione relativa al servizio di Azure in uso per informazioni sugli eventi specifici che vengono registrati.

![Log attività](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure
I log di Monitoraggio di Azure consolidano i log e le metriche generati da più servizi e altre origini dati per analizzarli con un avanzato strumento di query. Come descritto in precedenza, è necessario creare un'impostazione di diagnostica per raccogliere le metriche della piattaforma, i log attività e i log risorse in un'area di lavoro Log Analytics di Monitoraggio di Azure.

[Log Analytics](../log-query/get-started-portal.md) consente di usare [query di log](../log-query/log-query-overview.md), un'efficace funzionalità di Monitoraggio di Azure che permette di eseguire analisi avanzate dei dati di log usando un linguaggio di query completo. Aprire Log Analytics dalla voce **Log** del menu **Monitoraggio** per consentire l'interazione tra una risorsa di Azure e le query di log usando la risorsa come [ambito delle query](../log-query/scope.md#query-scope). In questo modo è possibile analizzare i dati relativi a una risorsa raccolti da più tabelle. Usare la voce **Log** del menu di Monitoraggio di Azure per accedere ai log relativi a tutte le risorse. 

- Per un'esercitazione sul linguaggio di query usato per scrivere query di log, vedere [Introduzione alle query di log in Monitoraggio di Azure](../log-query/get-started-queries.md).
- Vedere [Raccogliere i log risorse di Azure nell'area di lavoro Log Analytics in Monitoraggio di Azure](../platform/resource-logs-collect-workspace.md) per informazioni sul modo in cui vengono raccolti i log risorse nei log di Monitoraggio di Azure e su come accedervi in una query.
- Per conoscere il modo in cui i dati dei log risorse sono strutturati nei log di Monitoraggio di Azure, vedere [Modalità di raccolta](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode).
- Vedere la documentazione di ogni servizio di Azure per informazioni dettagliate sulla relativa tabella nei log di Monitoraggio di Azure.

![Log](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Monitoraggio dalla riga di comando
È possibile accedere ai dati di monitoraggio raccolti dalla risorsa in uso da una riga di comando o includerli in uno script usando [Azure PowerShell](/powershell/azure/) o l'[interfaccia della riga di comando di Azure](/cli/azure/). 

- Per accedere ai dati delle metriche dall'interfaccia della riga di comando, vedere le [informazioni di riferimento sulle metriche dall'interfaccia della riga di comando](/cli/azure/monitor/metrics).
- Per accedere ai dati dei log di Monitoraggio di Azure tramite una query di log dall'interfaccia della riga di comando, vedere le [informazioni di riferimento su Log Analytics dall'interfaccia della riga di comando](/cli/azure/ext/log-analytics/monitor/log-analytics).
- Per accedere ai dati delle metriche da Azure PowerShell, vedere le [informazioni di riferimento sulle metriche da Azure PowerShell](/powershell/module/azurerm.insights/get-azurermmetric).
- Per accedere ai dati dei log di Monitoraggio di Azure tramite una query di log da Azure PowerShell, vedere le [informazioni di riferimento sulle query di log da Azure PowerShell](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery).

## <a name="monitoring-from-rest-api"></a>Monitoraggio dall'API REST
È possibile includere i dati di monitoraggio raccolti dalla risorsa in uso in un'applicazione personalizzata usando un'API REST.

- Vedere [Procedura dettagliata sull'API REST di monitoraggio di Azure](../platform/rest-api-walkthrough.md) per informazioni dettagliate sull'accesso alle metriche dall'API REST di Monitoraggio di Azure.
- Per informazioni su come accedere ai dati dei log di Monitoraggio di Azure tramite una query di log da Azure PowerShell, vedere [API REST di Azure Log Analytics](https://dev.loganalytics.io/).

## <a name="alerts"></a>Avvisi
Gli [avvisi](../platform/alerts-overview.md) inviano notifiche in modo proattivo e possibilmente prendono misure correttive quando vengono riscontrate condizioni importanti nei dati di monitoraggio. Si crea una regola di avviso che definisce una destinazione per l'avviso, le condizioni per stabilire se creare un avviso e le azioni da intraprendere in risposta.

Per diversi tipi di regole di avviso vengono usati diversi tipi di dati di monitoraggio.

- [Avviso del log attività](../platform/alerts-activity-log.md): crea un avviso quando nel log attività viene creata una voce che corrisponde a criteri specifici. In questo modo, è possibile ricevere una notifica quando, ad esempio, viene creato un particolare tipo di risorsa o se una modifica di configurazione ha esito negativo.
- [Avviso di metrica](../platform/alerts-metric.md): crea un avviso ogni volta che un valore della metrica supera una determinata soglia. Gli avvisi delle metriche sono più reattivi rispetto ad altri avvisi e possono essere automaticamente risolti quando il problema viene corretto.
- [Avviso di query di log](../platform/alerts-log.md): esegue una query di log a intervalli regolari e crea un avviso se viene trovata una determinata condizione. In questo modo, è possibile eseguire analisi complesse su più set di dati.

Selezionare **Avvisi** dal menu di una risorsa per visualizzare e gestire le regole di avviso definite per la risorsa. Solo gli avvisi del log attività e gli avvisi di metrica usano singole risorse di Azure come destinazione. Gli avvisi di query di log usano invece un'area di lavoro Log Analytics come destinazione e sono basati su una query che può accedere a tutti i log archiviati nell'area di lavoro. Usare il menu di Monitoraggio di Azure per visualizzare e gestire gli avvisi relativi a tutte le risorse e gestire le regole di avviso per le query di log.

- Per informazioni dettagliate sulla creazione di regole di avviso, vedere gli articoli relativi ai diversi tipi di avvisi.
- Per informazioni dettagliate sulla creazione di un gruppo di azioni che consenta di gestire le risposte agli avvisi, vedere [Creare e gestire gruppi di azioni nel portale di Azure](../platform/action-groups.md).



## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui log delle risorse di vari servizi di Azure, vedere [Servizi, schemi e categorie supportati per i log delle risorse di Azure](../platform/diagnostic-logs-schema.md).  
