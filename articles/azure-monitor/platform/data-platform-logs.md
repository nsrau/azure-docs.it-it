---
title: Log in Monitoraggio di Azure | Microsoft Docs
description: Descrive i log in monitoraggio di Azure, che vengono usati per l'analisi avanzata dei dati di monitoraggio.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032775"
---
# <a name="azure-monitor-logs-overview"></a>Panoramica dei log di monitoraggio di Azure
Log di monitoraggio di Azure è una funzionalità di monitoraggio di Azure che raccoglie e organizza i dati di log da diverse origini e li rende disponibili per l'analisi usando un linguaggio di query sofisticato. I dati provenienti da origini diverse possono essere consolidati in un'unica area di lavoro e analizzati insieme per eseguire attività quali analisi delle tendenze, avvisi e visualizzazione.

## <a name="relationship-to-azure-monitor-metrics"></a>Relazione con le metriche di monitoraggio di Azure
Le metriche di monitoraggio di Azure archiviano dati numerici in un database di serie temporali, rendendo questi dati più leggeri dei log di monitoraggio di Azure e in grado di supportare scenari quasi in tempo reale, rendendoli particolarmente utili per gli avvisi e il rilevamento rapido dei problemi. Le metriche possono tuttavia archiviare solo dati numerici in una determinata struttura, mentre i log possono archiviare una varietà di tipi di dati diversi ognuno con la propria struttura. È anche possibile eseguire un'analisi complessa sui dati dei log usando query di log che non possono essere usati per l'analisi dei dati di metrica.

I dati numerici vengono spesso inviati dalle origini dati ai log, oltre alle metriche. Sebbene sia previsto un addebito aggiuntivo per la raccolta e il mantenimento dei dati nei log, è possibile includere i dati delle metriche nelle query di log e analizzarli con gli altri dati di monitoraggio.

## <a name="relationship-to-azure-data-explorer"></a>Relazione con Esplora dati di Azure
I log di monitoraggio di Azure si basano su Esplora dati di Azure. Un'area di lavoro Log Analytics è approssimativamente l'equivalente di un database in Azure Esplora dati, le tabelle sono strutturate allo stesso modo e usano entrambi lo stesso linguaggio di query kusto (KQL). L'esperienza di utilizzo di Log Analytics per lavorare con le query di monitoraggio di Azure nel portale di Azure è simile all'esperienza con l'interfaccia utente Web di Azure Esplora dati. È anche possibile [includere dati da un'area di lavoro log Analytics in una query di Esplora dati di Azure](/azure/data-explorer/query-monitor-data). 


## <a name="structure-of-data"></a>Struttura dei dati
I dati raccolti dai log di monitoraggio di Azure vengono archiviati in un' [area di lavoro di log Analytics](./design-logs-deployment.md) contenente più tabelle, ognuna delle quali archivia i dati di una determinata origine. L'area di lavoro definisce la posizione geografica dei dati, i diritti di accesso che definiscono quali utenti possono accedere ai dati e le impostazioni di configurazione quali il piano tariffario e la conservazione dei dati. È possibile usare una singola area di lavoro per tutti i dati di monitoraggio o creare più aree di lavoro in base alle esigenze. Vedere [progettazione della distribuzione dei log di monitoraggio di Azure](design-logs-deployment.md) per considerazioni sulla creazione di più aree di lavoro.

Ogni area di lavoro contiene più tabelle che sono organizzate in colonne separate con più righe di dati. Ogni tabella è definita da un set univoco di colonne condivise dalle righe di dati fornite dall'origine dati. 

[![Struttura dei log di monitoraggio di Azure](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


I dati di log da Application Insights vengono anche archiviati nei log di monitoraggio di Azure, ma vengono archiviati in modo diverso a seconda della configurazione dell'applicazione. Per un'applicazione basata su un'area di lavoro, i dati vengono archiviati in un'area di lavoro Log Analytics in un set di tabelle standard per archiviare dati quali richieste di applicazioni, eccezioni e visualizzazioni pagina. Più applicazioni possono utilizzare la stessa area di lavoro. Per un'applicazione classica, i dati non vengono archiviati in un'area di lavoro Log Analytics. USA lo stesso linguaggio di query ed è possibile creare ed eseguire query usando lo stesso strumento Log Analytics nel portale di Azure. I dati per le applicazioni classiche vengono tuttavia archiviati separatamente tra loro. La struttura generale è identica a quella delle applicazioni basate sull'area di lavoro, anche se i nomi di tabella e colonna sono diversi. Per un confronto dettagliato dei due, vedere [modifiche alle risorse basate sull'area di lavoro](../app/apm-tables.md) .


> [!NOTE]
> È ancora disponibile la completa compatibilità con le versioni precedenti per le query classiche sulle risorse di Application Insights, le cartelle di lavoro e gli avvisi basati su log all'interno dell'esperienza di Application Insights. Per eseguire query o visualizzare la [nuova struttura di tabella o lo schema basati sull'area di lavoro](../app/apm-tables.md) è prima necessario passare all'area di lavoro Log Analytics. Durante l'anteprima, selezionare **Log** all'interno dei riquadri di Application Insights per accedere all'esperienza di query classica di Application Insights. Per altri dettagli, vedere [ambito della query](../log-query/scope.md) .


[![Struttura dei log di monitoraggio di Azure per Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>Query di log
I dati vengono recuperati da un'area di lavoro Log Analytics usando una [query di log](../log-query/log-query-overview.md) che è una richiesta di sola lettura per elaborare i dati e restituire risultati. Le query di log sono scritte in [kusto Query Language (KQL)](/azure/data-explorer/kusto/query/), ovvero il linguaggio di query usato da Azure Esplora dati. Utilizzare Log Analytics, uno strumento della portale di Azure per modificare ed eseguire query di log e analizzarne i risultati in modo interattivo. È quindi possibile usare le query create per supportare altre funzionalità di monitoraggio di Azure, ad esempio gli avvisi di query di log e le cartelle di lavoro.


## <a name="sources-of-data-for-azure-monitor-logs"></a>Origini dei dati per i log di monitoraggio di Azure
Monitoraggio di Azure raccoglie i dati di log da diverse origini, incluse le risorse in monitoraggio di Azure e gli agenti in esecuzione nelle macchine virtuali. Vedere [che cosa viene monitorato da monitoraggio di Azure?](../monitor-reference.md) per un elenco completo delle origini dati che inviano dati a un'area di lavoro log Analytics.



## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [query di log](../log-query/log-query-overview.md) per recuperare e analizzare i dati da un'area di lavoro log Analytics.
- Informazioni sulle [metriche in Monitoraggio di Azure](data-platform-metrics.md).
- Informazioni sui [dati di monitoraggio disponibili](data-sources.md) per diverse risorse in Azure.

