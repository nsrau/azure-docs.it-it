---
title: Query di controllo nelle query dei log di monitoraggio di Azure
description: Dettagli dei log di controllo delle query di log che forniscono dati di telemetria sulle query di log eseguite in monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/03/2020
ms.openlocfilehash: bfaa9d8908d9401441d8811c3edcd087781b1d89
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89458638"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Controllare le query nei log di monitoraggio di Azure (anteprima)
Log di controllo delle query di log forniscono dati di telemetria sulle query di log eseguite in monitoraggio di Azure. Sono incluse informazioni quali il momento in cui è stata eseguita una query, l'utente che lo ha eseguito, lo strumento usato, il testo della query e le statistiche sulle prestazioni che descrivono l'esecuzione della query.


## <a name="configure-query-auditing"></a>Configurare il controllo delle query
Il controllo delle query è abilitato con un' [impostazione di diagnostica](../platform/diagnostic-settings.md) nell'area di lavoro log Analytics. In questo modo è possibile inviare i dati di controllo all'area di lavoro corrente o a qualsiasi altra area di lavoro nella sottoscrizione, ad hub eventi di Azure per l'invio all'esterno di Azure o all'archiviazione di Azure per l'archiviazione. 

### <a name="azure-portal"></a>Portale di Azure
Accedere all'impostazione di diagnostica per un'area di lavoro Log Analytics nel portale di Azure in uno dei percorsi seguenti:

- Dal menu **monitoraggio di Azure** selezionare **impostazioni di diagnostica**, quindi individuare e selezionare l'area di lavoro.

    [![Monitoraggio ](media/log-query-audit/diagnostic-setting-monitor.png) delle impostazioni di diagnostica di Azure ](media/log-query-audit/diagnostic-setting-monitor.png#lightbox) 

- Dal menu **aree di lavoro log Analytics** selezionare l'area di lavoro, quindi selezionare **impostazioni di diagnostica**.

    [![Impostazioni di diagnostica Log Analytics area di lavoro ](media/log-query-audit/diagnostic-setting-workspace.png)](media/log-query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Modello di Resource Manager
È possibile ottenere un esempio Gestione risorse modello dall' [impostazione di diagnostica per log Analytics area di lavoro](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace).

## <a name="audit-data"></a>Dati di controllo
Viene creato un record di controllo ogni volta che viene eseguita una query. Se si inviano i dati a un'area di lavoro Log Analytics, questi vengono archiviati in una tabella denominata *LAQueryLogs*. Nella tabella seguente vengono descritte le proprietà di ogni record dei dati di controllo.

| Campo | Descrizione |
|:---|:---|
| TimeGenerated         | Ora UTC in cui è stata inviata la query. |
| CorrelationId         | ID univoco per identificare la query. Può essere utilizzato in scenari di risoluzione dei problemi quando si contatta Microsoft per assistenza. |
| AADObjectId           | ID Azure Active Directory dell'account utente che ha avviato la query.  |
| AADTenantId           | ID del tenant dell'account utente che ha avviato la query.  |
| AADEmail              | Indirizzo di posta elettronica del tenant dell'account utente che ha avviato la query.  |
| AADClientId           | ID e nome risolto dell'applicazione utilizzata per avviare la query. |
| RequestClientApp      | Nome risolto dell'applicazione utilizzata per avviare la query. |
| QueryTimeRangeStart   | Inizio dell'intervallo di tempo selezionato per la query. Questa operazione potrebbe non essere popolata in determinati scenari, ad esempio quando la query viene avviata da Log Analytics e l'intervallo di tempo viene specificato all'interno della query anziché nella selezione temporale. |
| QueryTimeRangeEnd     | Fine dell'intervallo di tempo selezionato per la query. Questa operazione potrebbe non essere popolata in determinati scenari, ad esempio quando la query viene avviata da Log Analytics e l'intervallo di tempo viene specificato all'interno della query anziché nella selezione temporale.  |
| QueryText             | Testo della query eseguita. |
| RequestTarget         | L'URL dell'API è stato usato per inviare la query.  |
| RequestContext        | Elenco delle risorse su cui è stata richiesta l'esecuzione della query. Contiene fino a tre matrici di stringhe: aree di lavoro, applicazioni e risorse. Le query destinate a gruppi di risorse o sottoscrizioni vengono visualizzate come *risorse*. Include la destinazione implicita da RequestTarget.<br>Se può essere risolto, verrà incluso l'ID risorsa per ogni risorsa. Potrebbe non essere possibile risolverlo se viene restituito un errore durante l'accesso alla risorsa. In questo caso, verrà usato il testo specifico della query.<br>Se la query usa un nome ambiguo, ad esempio un nome dell'area di lavoro esistente in più sottoscrizioni, verrà usato il nome ambiguo. |
| RequestContextFilters | Set di filtri specificati come parte della chiamata della query. Include fino a tre possibili matrici di stringhe:<br>-ResourceTypes-tipo di risorsa per limitare l'ambito della query<br>-Workspaces: elenco delle aree di lavoro per cui limitare la query<br>-WorkspaceRegions-elenco delle aree dell'area di lavoro per limitare la query |
| ResponseCode          | Codice di risposta HTTP restituito quando la query è stata inviata. |
| ResponseDurationMs    | Tempo necessario per la restituzione della risposta.  |
| ResponseRowCount     | Numero totale di righe restituite dalla query. |
| StatsCPUTimeMs       | Tempo di calcolo totale usato per l'elaborazione, l'analisi e il recupero dei dati. Viene popolato solo se la query restituisce il codice di stato 200. |
| StatsDataProcessedKB | Quantità di dati a cui è stato eseguito l'accesso per elaborare la query. Influenzato dalla dimensione della tabella di destinazione, dall'intervallo di tempo utilizzato, dai filtri applicati e dal numero di colonne a cui si fa riferimento. Viene popolato solo se la query restituisce il codice di stato 200. |
| StatsDataProcessedStart | Ora dei dati meno recenti a cui è stato eseguito l'accesso per elaborare la query. Influenzato dall'intervallo di tempo esplicito della query e dai filtri applicati. Questo può essere maggiore dell'intervallo di tempo esplicito a causa del partizionamento dei dati. Viene popolato solo se la query restituisce il codice di stato 200. |
| StatsDataProcessedEnd  |Ora dei dati più recenti a cui è stato eseguito l'accesso per elaborare la query. Influenzato dall'intervallo di tempo esplicito della query e dai filtri applicati. Questo può essere maggiore dell'intervallo di tempo esplicito a causa del partizionamento dei dati. Viene popolato solo se la query restituisce il codice di stato 200. |
| StatsWorkspaceCount | Numero di aree di lavoro a cui accede la query. Viene popolato solo se la query restituisce il codice di stato 200. |
| StatsRegionCount | Numero di aree a cui la query accede. Viene popolato solo se la query restituisce il codice di stato 200. |

## <a name="considerations"></a>Considerazioni

- Le statistiche sulle prestazioni non sono disponibili per le query provenienti dal proxy di Azure Esplora dati. Tutti gli altri dati per queste query verranno comunque popolati.
- L'hint *h* sulle stringhe che [offusca i valori letterali stringa](/azure/data-explorer/kusto/query/scalar-data-types/string#obfuscated-string-literals) non avrà alcun effetto sui log di controllo della query. Le query verranno acquisite esattamente come inviato senza che la stringa venga offuscata. È necessario assicurarsi che solo gli utenti che dispongono dei diritti di conformità per visualizzare questi dati siano in grado di eseguire questa operazione utilizzando le varie modalità RBAC disponibili nelle aree di lavoro Log Analytics.
- Per le query che includono dati da più aree di lavoro, la query verrà acquisita solo nelle aree di lavoro a cui l'utente ha accesso.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [impostazioni di diagnostica](../platform/diagnostic-settings.md).
- Altre informazioni sull' [ottimizzazione delle query di log](query-optimization.md).