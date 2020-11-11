---
title: Log di Monitoraggio di Azure
description: Descrive i log di monitoraggio di Azure usati per l'analisi avanzata dei dati di monitoraggio.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 462242b001da5a5a6d2eba8e4bd06315c0b263a6
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491856"
---
# <a name="azure-monitor-logs-overview"></a>Panoramica dei log di monitoraggio di Azure
Log di monitoraggio di Azure è una funzionalità di monitoraggio di Azure che raccoglie e organizza i dati di log e delle prestazioni dalle [risorse monitorate](../monitor-reference.md). I dati provenienti da origini diverse, ad esempio i [log della piattaforma](platform-logs-overview.md) dei servizi di Azure, i dati di log e delle prestazioni dagli [agenti delle macchine virtuali](agents-overview.md)e i dati di utilizzo e prestazioni delle [applicazioni](../app/app-insights-overview.md) possono essere consolidati in un'unica area di lavoro in modo da poter essere analizzati insieme usando un linguaggio di query sofisticato in grado di analizzare rapidamente milioni di record. È possibile eseguire una query semplice che recupera solo un set specifico di record o esegue un'analisi sofisticata dei dati per identificare i modelli critici nei dati di monitoraggio. Usare le query di log e i relativi risultati in modo interattivo usando Log Analytics, usarli in una regola di avviso per inviare notifiche in modo proattivo ai problemi o visualizzare i risultati in una cartella di lavoro o in un dashboard.

> [!NOTE]
> Log di monitoraggio di Azure è una metà della piattaforma dati che supporta monitoraggio di Azure. L'altra è la [metrica di monitoraggio di Azure](data-platform-metrics.md) che archivia dati numerici in un database di serie temporali. Questo rende i dati più leggeri dei log di monitoraggio di Azure e in grado di supportare scenari quasi in tempo reale, rendendoli particolarmente utili per gli avvisi e il rilevamento rapido dei problemi. Le metriche possono tuttavia archiviare solo dati numerici in una determinata struttura, mentre i log possono archiviare una varietà di tipi di dati diversi ognuno con la propria struttura. È anche possibile eseguire un'analisi complessa sui dati dei log usando query di log che non possono essere usati per l'analisi dei dati di metrica.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Che cosa è possibile fare con i log di Monitoraggio di Azure?
La tabella seguente descrive alcuni dei diversi modi in cui è possibile usare i log in monitoraggio di Azure:

|  |  |
|:---|:---|
| **Analizzare** | Usare [log Analytics](../log-query/get-started-portal.md) nel portale di Azure per scrivere [query di log](../log-query/log-query-overview.md) e analizzare i dati di log in modo interattivo usando un potente motore di analisi |
| **Avviso** | Configurazione di una [regola di avviso per il log](alerts-log.md) che invia una notifica o esegue un'[azione automatica](action-groups.md) quando i risultati della query corrispondono a un risultato specifico. |
| **Visualizzazione** | Aggiungere i risultati delle query visualizzati come tabelle o grafici in un [dashboard di Azure](../../azure-portal/azure-portal-dashboards.md).<br>Creare una [cartella di lavoro](../app/usage-workbooks.md) per combinare più set di dati in un report interattivo. <br>Esportazione dei risultati di una query in [Power BI](powerbi.md) per usare diverse visualizzazioni e condividerle con utenti esternamente ad Azure.<br>Esportare i risultati di una query in [Grafana](grafana-plugin.md) per sfruttarne le capacità di creazione dashboard e combinare i risultati con altre origini dati.|
| **Insights** | Supporto di [informazioni dettagliate](../monitor-reference.md#insights-and-core-solutions) che forniscono un'esperienza di monitoraggio personalizzata per applicazioni e servizi specifici.  |
| **Recupero** | Accedere ai risultati delle query di log da una riga di comando usando l'[interfaccia della riga di comando di Azure](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>Accedere ai risultati delle query di log da una riga di comando usando i [cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Accedere ai risultati delle query di log da un'applicazione personalizzata usando l'[API REST](https://dev.loganalytics.io/). |
| **Export** | Configurare l' [esportazione automatizzata dei dati di log](logs-data-export.md) nell'account di archiviazione di Azure o in hub eventi di Azure.<br>Compilare un flusso di lavoro per recuperare i dati di log e copiarli in una posizione esterna usando [App per la logica](logicapp-flow-connector.md). |

![Panoramica dei log](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>Raccolta dati
Dopo aver creato un'area di lavoro Log Analytics, è necessario configurare origini diverse per inviare i dati. Non vengono raccolti dati automaticamente. Questa configurazione sarà diversa a seconda dell'origine dati. Ad esempio, [creare le impostazioni di diagnostica](diagnostic-settings.md) per inviare i log delle risorse dalle risorse di Azure all'area di lavoro. [Abilitare monitoraggio di Azure per le macchine virtuali](../insights/vminsights-enable-overview.md) per la raccolta di dati dalle macchine virtuali. Configurare [le origini dati nell'area di lavoro](data-sources.md) per raccogliere eventi aggiuntivi e dati sulle prestazioni.

- Vedere [che cosa viene monitorato da monitoraggio di Azure?](../monitor-reference.md) per un elenco completo delle origini dati che è possibile configurare per inviare dati ai log di monitoraggio di Azure.


## <a name="log-analytics-workspaces"></a>Aree di lavoro di Log Analytics
I dati raccolti dai log di monitoraggio di Azure vengono archiviati in un'altra [area di lavoro log Analytics](./design-logs-deployment.md). L'area di lavoro definisce la posizione geografica dei dati, i diritti di accesso che definiscono quali utenti possono accedere ai dati e le impostazioni di configurazione quali il piano tariffario e la conservazione dei dati.  

Per usare i log di monitoraggio di Azure, è necessario creare almeno un'area di lavoro. Una singola area di lavoro può essere sufficiente per tutti i dati di monitoraggio oppure è possibile scegliere di creare più aree di lavoro in base alle esigenze. Ad esempio, potrebbe essere presente un'area di lavoro per i dati di produzione e un'altra per il test. 

- Per creare una nuova area di lavoro, vedere [creare un'area di lavoro log Analytics nel portale di Azure](../learn/quick-create-workspace.md) .
- Vedere [progettazione della distribuzione dei log di monitoraggio di Azure](design-logs-deployment.md) per considerazioni sulla creazione di più aree di lavoro.

## <a name="data-structure"></a>Struttura dei dati
Le query di log recuperano i dati da un'area di lavoro Log Analytics. Ogni area di lavoro contiene più tabelle che sono organizzate in colonne separate con più righe di dati. Ogni tabella è definita da un set univoco di colonne condivise dalle righe di dati fornite dall'origine dati. 

[![Struttura dei log di monitoraggio di Azure](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


I dati di log da Application Insights vengono anche archiviati nei log di monitoraggio di Azure, ma vengono archiviati in modo diverso a seconda della configurazione dell'applicazione. Per un'applicazione basata su un'area di lavoro, i dati vengono archiviati in un'area di lavoro Log Analytics in un set di tabelle standard per archiviare dati quali richieste di applicazioni, eccezioni e visualizzazioni pagina. Più applicazioni possono utilizzare la stessa area di lavoro. Per un'applicazione classica, i dati non vengono archiviati in un'area di lavoro Log Analytics. USA lo stesso linguaggio di query ed è possibile creare ed eseguire query usando lo stesso strumento Log Analytics nel portale di Azure. I dati per le applicazioni classiche vengono tuttavia archiviati separatamente tra loro. La struttura generale è identica a quella delle applicazioni basate sull'area di lavoro, anche se i nomi di tabella e colonna sono diversi. Per un confronto dettagliato dello schema per le applicazioni classiche e basate sull'area di lavoro, vedere [modifiche alle risorse basate sull'area di lavoro](../app/apm-tables.md) .


> [!NOTE]
> È ancora disponibile la completa compatibilità con le versioni precedenti per le query classiche sulle risorse di Application Insights, le cartelle di lavoro e gli avvisi basati su log all'interno dell'esperienza di Application Insights. Per eseguire query o visualizzare la [nuova struttura di tabella o lo schema basati sull'area di lavoro](../app/apm-tables.md) è prima necessario passare all'area di lavoro Log Analytics. Durante l'anteprima, selezionare **Log** all'interno dei riquadri di Application Insights per accedere all'esperienza di query classica di Application Insights. Per altri dettagli, vedere [ambito della query](../log-query/scope.md) .


[![Struttura dei log di monitoraggio di Azure per Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>Query di log
I dati vengono recuperati da un'area di lavoro Log Analytics usando una query di log che è una richiesta di sola lettura per elaborare i dati e restituire risultati. Le query di log sono scritte in [kusto Query Language (KQL)](/azure/data-explorer/kusto/query/), che è lo stesso linguaggio di query usato da Azure Esplora dati. È possibile scrivere query di log in Log Analytics per analizzare in modo interattivo i risultati, usarli nelle regole di avviso per ricevere notifiche proattive dei problemi o includere i risultati in cartelle di lavoro o dashboard. Le informazioni dettagliate includono query predefinite per supportare le visualizzazioni e le cartelle di lavoro.

- Per un elenco della posizione in cui vengono usate le query di log e i riferimenti alle esercitazioni e ad altre documentazioni per iniziare, vedere [query di log in monitoraggio di Azure](log-query/../../log-query/log-query-overview.md) .

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Usare Log Analytics, uno strumento della portale di Azure, per modificare ed eseguire query di log e analizzare i risultati in modo interattivo. È quindi possibile usare le query create per supportare altre funzionalità di monitoraggio di Azure, ad esempio gli avvisi di query di log e le cartelle di lavoro. Accedere Log Analytics dall'opzione **logs** nel menu monitoraggio di Azure o dalla maggior parte degli altri servizi nel portale di Azure.

- Per una descrizione del Log Analytics, vedere [Panoramica di log Analytics in monitoraggio di Azure](/log-query/log-analytics-overview.md) . 
- Vedere [log Analytics esercitazione](/log-query/log-analytics-tutorial.md) per esaminare l'uso delle funzionalità log Analytics per creare una semplice query di log e analizzarne i risultati.



## <a name="relationship-to-azure-data-explorer"></a>Relazione con Esplora dati di Azure
I log di monitoraggio di Azure si basano su Esplora dati di Azure. Un'area di lavoro Log Analytics è approssimativamente l'equivalente di un database in Azure Esplora dati, le tabelle sono strutturate allo stesso modo e usano entrambi lo stesso linguaggio di query kusto (KQL). L'esperienza di utilizzo di Log Analytics per lavorare con le query di monitoraggio di Azure nel portale di Azure è simile all'esperienza con l'interfaccia utente Web di Azure Esplora dati. È anche possibile [includere dati da un'area di lavoro log Analytics in una query di Esplora dati di Azure](/azure/data-explorer/query-monitor-data). 


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [query di log](../log-query/log-query-overview.md) per recuperare e analizzare i dati da un'area di lavoro log Analytics.
- Informazioni sulle [metriche in Monitoraggio di Azure](data-platform-metrics.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.
