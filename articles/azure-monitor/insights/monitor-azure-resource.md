---
title: Monitorare le risorse di Azure con monitoraggio di Azure | Microsoft Docs
description: Viene descritto come raccogliere e analizzare i dati di monitoraggio dalle risorse in Azure tramite monitoraggio di Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 8ba50e5b55e31c6542e81879068bf231918ce1b0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244889"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Monitoraggio delle risorse di Azure con monitoraggio di Azure
Quando le applicazioni e i processi aziendali critici si basano sulle risorse di Azure, è opportuno monitorarle per la disponibilità, le prestazioni e il funzionamento. Questo articolo descrive i dati di monitoraggio generati dalle risorse di Azure e come è possibile usare le funzionalità di monitoraggio di Azure per analizzare e generare avvisi su questi dati.

> [!IMPORTANT]
> Questo articolo si applica a tutti i servizi di Azure che usano monitoraggio di Azure. Le risorse di calcolo, incluse le VM e il servizio app, generano gli stessi dati di monitoraggio descritti qui, ma dispongono anche di un sistema operativo guest che può generare anche log e metriche. Per informazioni dettagliate su come raccogliere e analizzare i dati, vedere la documentazione di monitoraggio per questi servizi.

## <a name="what-is-azure-monitor"></a>Informazioni su Monitoraggio di Azure
Monitoraggio di Azure è un servizio di monitoraggio completo dello stack in Azure che offre un set completo di funzionalità per il monitoraggio delle risorse di Azure, oltre alle risorse in altri cloud e in locale. La [piattaforma dati di monitoraggio di Azure](../platform/data-platform.md) raccoglie dati in [log](../platform/data-platform-logs.md) e [metriche](../platform/data-platform-metrics.md) in cui possono essere analizzati insieme usando un set completo di strumenti di monitoraggio, come descritto nelle sezioni seguenti.

- [Che cosa è possibile fare con le metriche di monitoraggio di Azure?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Che cosa è possibile fare con i log di monitoraggio di Azure?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Non appena si crea una risorsa di Azure, monitoraggio di Azure viene abilitato e inizia a raccogliere le metriche e i log attività che è possibile [visualizzare e analizzare nella portale di Azure](#monitoring-in-the-azure-portal). Con alcune configurazioni è possibile raccogliere dati di monitoraggio aggiuntivi e abilitare funzionalità aggiuntive. Per informazioni dettagliate sui requisiti di configurazione, vedere [monitoraggio dei dati](#monitoring-data) di seguito.


## <a name="costs-associated-with-monitoring"></a>Costi associati al monitoraggio
Non sono previsti costi per l'analisi dei dati di monitoraggio raccolti per impostazione predefinita. Sono inclusi gli elementi seguenti:

- Raccolta delle metriche della piattaforma e relativa analisi con Esplora metriche.
- Raccolta del log attività e relativa analisi nella portale di Azure.
- Creazione di una regola di avviso del log attività.

Non sono previsti costi di monitoraggio di Azure per la raccolta e l'esportazione di log e metriche, ma potrebbero esserci costi correlati associati alla destinazione:

- Costi associati all'inserimento e alla conservazione dei dati quando si raccolgono log e metriche nell'area di lavoro Log Analytics. [Per log Analytics, vedere prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/).
- Costi associati all'archiviazione dei dati quando si raccolgono log e metriche in un account di archiviazione di Azure. Vedere [prezzi di archiviazione di Azure per l'archiviazione BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/).
- Costi associati al flusso di hub eventi quando si inviano log e metriche a hub eventi di Azure. Vedere [prezzi di hub eventi di Azure](https://azure.microsoft.com/pricing/details/event-hubs/).

È possibile che i costi di monitoraggio di Azure siano associati a quanto segue. Vedere i [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/):

- Esecuzione di una query di log.
- Creazione di una regola di avviso per una metrica o una query di log.
- Invio di una notifica da qualsiasi regola di avviso.
- Accesso alle metriche tramite l'API.

## <a name="monitoring-data"></a>Dati di monitoraggio
Le risorse in Azure generano [log](../platform/data-platform-logs.md) e [metriche](../platform/data-platform-metrics.md) illustrate nel diagramma seguente. Fare riferimento alla documentazione per ogni servizio di Azure per i dati specifici che generano ed eventuali soluzioni o informazioni aggiuntive fornite.

![Panoramica](media/monitor-azure-resource/logs-metrics.png)



- [Metriche della piattaforma](../platform/data-platform-metrics.md) : valori numerici che vengono raccolti automaticamente a intervalli regolari e descrivono un aspetto di una risorsa in un determinato momento. 
- [Log delle risorse](../platform/resource-logs-overview.md) : forniscono informazioni approfondite sulle operazioni eseguite all'interno di una risorsa di Azure (il piano dati), ad esempio il recupero di un segreto da un Key Vault o l'esecuzione di una richiesta a un database. Il contenuto e la struttura dei log delle risorse variano in base al servizio di Azure e al tipo di risorsa.
- [Log attività](../platform/activity-logs-overview.md) : fornisce informazioni approfondite sulle operazioni su ogni risorsa di Azure nella sottoscrizione dall'esterno (il piano di gestione), ad esempio la creazione di una nuova risorsa o l'avvio di una macchina virtuale. Si tratta di informazioni su cosa, chi e quando per qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione.


## <a name="configuration-requirements"></a>Requisiti di configurazione
Alcuni dati di monitoraggio vengono raccolti automaticamente, ma potrebbe essere necessario eseguire alcune configurazioni a seconda delle esigenze. Per informazioni specifiche su ogni tipo di dati di monitoraggio, vedere le informazioni riportate di seguito.

- [Metriche della piattaforma](../platform/data-platform-metrics.md) : le metriche della piattaforma vengono raccolte automaticamente nelle [metriche di monitoraggio di Azure](../platform/data-platform-metrics.md) senza che sia necessaria alcuna configurazione. Creare un'impostazione di diagnostica per inviare le voci ai log di monitoraggio di Azure o inoltrarli all'esterno di Azure.
- [Log delle](../platform/resource-logs-overview.md) risorse: i log delle risorse vengono generati automaticamente dalle risorse di Azure, ma non vengono raccolti senza un'impostazione di diagnostica.  Creare un'impostazione di diagnostica per inviare le voci ai log di monitoraggio di Azure o inoltrarli all'esterno di Azure.
- [Log attività](../platform/activity-logs-overview.md) : il log attività viene raccolto automaticamente senza alcuna configurazione necessaria e può essere visualizzato nella portale di Azure. Creare un'impostazione di diagnostica per copiarla nei log di monitoraggio di Azure o per inviarli all'esterno di Azure.


## <a name="diagnostic-settings"></a>Impostazioni di diagnostica
Le impostazioni di diagnostica definiscono dove devono essere inviati i log delle risorse e le metriche per una determinata risorsa. Le destinazioni possibili sono:

- [Log Analytics area di lavoro](../platform/resource-logs-collect-workspace.md) che consente di analizzare i dati con altri dati di monitoraggio raccolti da monitoraggio di Azure con potenti query di log e anche per sfruttare altre funzionalità di monitoraggio di Azure, ad esempio avvisi e visualizzazioni del log. 
- [Hub eventi](../platform/resource-logs-stream-event-hubs.md) per trasmettere i dati a sistemi esterni, ad esempio Siem di terze parti e altre soluzioni di log Analytics. 
- [Account di archiviazione di Azure](../platform/resource-logs-collect-storage.md) , utile per il controllo, l'analisi statica o il backup.

Seguire la procedura descritta in [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](../platform/diagnostic-settings.md) per creare e gestire le impostazioni di diagnostica tramite il portale di Azure. Vedere [creare un'impostazione di diagnostica in Azure usando un modello di gestione risorse](../platform/diagnostic-settings-template.md) per definirli in un modello e abilitare il monitoraggio completo per una risorsa al momento della creazione.


## <a name="monitoring-in-the-azure-portal"></a>Monitoraggio nel portale di Azure
 È possibile accedere ai dati di monitoraggio per la maggior parte delle risorse di Azure dal menu della risorsa nel portale di Azure. In questo modo sarà possibile accedere ai dati di una singola risorsa usando gli strumenti standard di monitoraggio di Azure. Alcuni servizi di Azure forniranno opzioni diverse, quindi è necessario fare riferimento alla documentazione per tale servizio per altre informazioni. Usare il menu **monitoraggio di Azure** per analizzare i dati di tutte le risorse monitorate. 

Molti servizi includeranno i dati di monitoraggio nella pagina **Panoramica** come una rapida occhiata al proprio funzionamento. Questa operazione sarà in genere basata su un subset di metriche della piattaforma archiviate nelle metriche di monitoraggio di Azure. Altre opzioni di monitoraggio saranno in genere disponibili in una sezione di **monitoraggio** dei servizi. menu.

![Pagina di panoramica](media/monitor-azure-resource/overview-page.png)

## <a name="analyzing-metrics"></a>Analisi delle metriche
Analizzare le singole metriche e correlare più metriche per identificare le correlazioni e le tendenze usando [Esplora metriche](../platform/metrics-getting-started.md). Alcuni servizi offriranno un'esperienza personalizzata per l'uso delle metriche quando si aprono le **metriche** dal menu delle risorse.

- Per le nozioni di base sull'uso di Esplora metriche, vedere [Introduzione ad Azure Esplora metriche](../platform/metrics-getting-started.md) .
- Vedere [funzionalità avanzate di Azure Esplora metriche](../platform/metrics-charts.md) per le funzionalità avanzate di Esplora metriche, ad esempio l'uso di più metriche e l'applicazione di filtri e suddivisione.

![metrics](media/monitor-azure-resource/metrics.png)


## <a name="analyzing-logs"></a>Analisi dei log

### <a name="activity-log"></a>Log attività 
Consente di visualizzare le voci nel log attività del portale di Azure con il filtro iniziale impostato sulla risorsa corrente. Copiare il log attività in un'area di lavoro di Log Analytics per accedervi per usarlo nelle query e nelle cartelle di lavoro di log. 

- Vedere [visualizzare e recuperare gli eventi del log attività di Azure](../platform/activity-log-view.md) per informazioni dettagliate sulla visualizzazione del log attività e il recupero di voci con diversi metodi.
- Vedere la documentazione per il servizio di Azure per gli eventi specifici che vengono registrati.

![Log attività](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure
I log di monitoraggio di Azure consolidano i log e le metriche da più servizi e altre origini dati per l'analisi con uno strumento di query potente. Come descritto in precedenza, creare un'impostazione di diagnostica per raccogliere metriche della piattaforma, log attività e log delle risorse in un'area di lavoro Log Analytics in monitoraggio di Azure.

[Log Analytics](../log-query/get-started-portal.md) consente di lavorare con le [query di log](../log-query/log-query-overview.md), una funzionalità potente di monitoraggio di Azure che consente di eseguire analisi avanzate dei dati di log usando un linguaggio di query con funzionalità complete. Aprire Log Analytics dai **log** nel menu **monitoraggio** per una risorsa di Azure per usare le query di log usando la risorsa come [ambito della query](../log-query/scope.md#query-scope). In questo modo è possibile analizzare i dati in più tabelle solo per tale risorsa. Usare i **log** dal menu monitoraggio di Azure per accedere ai log di tutte le risorse. 

- Per una procedura dettagliata sull'uso di Log Analytics per scrivere una query e usare i risultati, vedere [Introduzione a log Analytics in monitoraggio di Azure](../log-query/get-started-portal.md) .
- Per un'esercitazione sull'uso del linguaggio di query usato per scrivere query di log, vedere [Introduzione alle query di log in monitoraggio di Azure](../log-query/get-started-queries.md) .
- Vedere [raccogliere i log delle risorse di Azure nell'area di lavoro log Analytics in monitoraggio di Azure](../platform/resource-logs-collect-workspace.md) per informazioni su come vengono raccolti i log delle risorse nei log di monitoraggio di Azure e informazioni dettagliate su come accedervi in una query.
- Vedere [modalità di raccolta](../platform/resource-logs-collect-workspace.md#collection-mode) per una spiegazione del modo in cui i dati del log delle risorse sono strutturati nei log di monitoraggio di Azure.
- Per informazioni dettagliate sulla tabella nei log di monitoraggio di Azure, vedere la documentazione per ogni servizio di Azure.

![Log](media/monitor-azure-resource/logs.png)


## <a name="alerts"></a>Avvisi
Gli [avvisi](../platform/alerts-overview.md) inviano notifiche in modo proattivo e potenzialmente intervenire quando si trovano condizioni importanti nei dati di monitoraggio. Si crea una regola di avviso che definisce una destinazione per l'avviso, le condizioni per stabilire se creare un avviso e le azioni da intraprendere in risposta.

Per diversi tipi di regole di avviso vengono usati tipi diversi di dati di monitoraggio.

- [Avviso del log attività](../platform/alerts-activity-log.md) : creare un avviso quando viene creata una voce nel log attività che corrisponde a criteri specifici. Questo consente di ricevere una notifica ad esempio quando viene creato un particolare tipo di risorsa o se una modifica della configurazione ha esito negativo.
- [Avviso metrica](../platform/alerts-metric.md) : creare un avviso quando un valore della metrica supera una determinata soglia. Gli avvisi delle metriche sono più reattivi rispetto ad altri avvisi e possono essere risolti automaticamente quando il problema viene corretto.
- [Avviso query log](../platform/alerts-log.md) : esegue una query di log a intervalli regolari e crea un avviso se viene trovata una determinata condizione. In questo modo è possibile eseguire analisi complesse su più set di dati e.

Usare gli **avvisi** dal menu di una risorsa per visualizzare gli avvisi e gestire le regole di avviso per la risorsa. Solo gli avvisi del log attività e le metriche usano le singole risorse di Azure come destinazione. Gli avvisi di query di log usano un'area di lavoro Log Analytics come destinazione e sono basati su una query che può accedere a tutti i log archiviati nell'area di lavoro. Usare il menu monitoraggio di Azure per visualizzare e gestire gli avvisi per tutte le risorse e per gestire le regole di avviso per le query di log.

- Per informazioni dettagliate sulla creazione di regole di avviso, vedere gli articoli relativi ai diversi tipi di avviso.
- Per informazioni dettagliate sulla creazione di un gruppo di azioni che consente di gestire le risposte agli avvisi, vedere [creare e gestire gruppi di azioni nella portale di Azure](../platform/action-groups.md) .

## <a name="insights-and-solutions"></a>Informazioni dettagliate e soluzioni 
Alcuni servizi forniranno strumenti oltre le funzionalità standard di monitoraggio di Azure. Le [soluzioni](../insights/solutions.md) forniscono la logica di monitoraggio predefinita basata sulle funzionalità di monitoraggio standard di Azure. [Insights](../insights/insights-overview.md) offre un'esperienza di monitoraggio personalizzata basata sulla piattaforma dati di monitoraggio di Azure e sulle funzionalità standard.

Se una risorsa ha un'analisi disponibile, è possibile accedervi da **informazioni dettagliate nel menu** delle risorse. Accedere a tutte le informazioni e alle soluzioni dal menu monitoraggio di Azure.

- Vedere la documentazione di monitoraggio per ogni servizio per determinare se sono disponibili informazioni dettagliate o soluzioni.

![Informazioni dettagliate](media/monitor-azure-resource/insights.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sui log delle risorse per i diversi servizi di Azure [, vedere servizi, schemi e categorie supportati per i log di diagnostica di Azure](../platform/diagnostic-logs-schema.md) .  
