---
title: Registrare l'ambito della query in Analisi dei log di Monitoraggio di Azure. Documenti Microsoft
description: Descrive l'ambito e l'intervallo di tempo per una query di log in Azure Monitor Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 897eff62fcbab5996b6b9493bd825ae412aa4c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249594"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Log query scope and time range in Azure Monitor Log Analytics
Quando si esegue una [query di log](log-query-overview.md) in Log Analytics nel portale di [Azure,](get-started-portal.md)il set di dati valutati dalla query dipende dall'ambito e dall'intervallo di tempo selezionato. In questo articolo vengono descritti l'ambito e l'intervallo di tempo e come è possibile impostare ciascuno in base alle proprie esigenze. Viene inoltre descritto il comportamento dei diversi tipi di ambiti.


## <a name="query-scope"></a>Ambito delle query
L'ambito della query definisce i record valutati dalla query. In genere include tutti i record in una singola area di lavoro di Log Analytics o in un'applicazione di Application Insights.This will usually include all records in a single Log Analytics workspace or Application Insights application. Log Analytics consente inoltre di impostare un ambito per una particolare risorsa di Azure monitorata. Ciò consente al proprietario della risorsa di concentrarsi solo sui dati, anche se la risorsa scrive in più aree di lavoro.

L'ambito viene sempre visualizzato nella parte superiore sinistra della finestra log analytics. Un'icona indica se l'ambito è un'area di lavoro di Log Analytics o un'applicazione di Application Insights.An icon indicates whether the scope is a Log Analytics workspace or an Application Insights application. Nessuna icona indica un'altra risorsa di Azure.No icon indicates another Azure resource.

![Scope](media/scope/scope.png)

L'ambito è determinato dal metodo utilizzato per avviare Log Analytics e in alcuni casi è possibile modificare l'ambito facendo clic su di esso. Nella tabella seguente sono elencati i diversi tipi di ambito utilizzati e i diversi dettagli per ciascuno di essi.

| Ambito delle query | Record nell'ambito | Come selezionare | Modifica dell'ambito |
|:---|:---|:---|:---|
| Area di lavoro Log Analytics | Tutti i record nell'area di lavoro di Log Analytics. | Selezionare Log dal menu Monitoraggio di Azure o dal menu delle aree di lavoro di **Log Analytics.Select** **Logs** from the **Azure Monitor** menu or the Log Analytics workspaces menu.  | Può modificare l'ambito in qualsiasi altro tipo di risorsa. |
| Applicazione Application Insights | Tutti i record nell'applicazione Application Insights. | Selezionare Analytics dalla pagina **Panoramica** di Application Insights.Select **Analytics** from Overview page of Application Insights. | Può solo modificare l'ambito in un'altra applicazione application Insights. |
| Resource group | Record creati da tutte le risorse nel gruppo di risorse. Può includere dati da più aree di lavoro di Log Analytics. | Selezionare **Registri** dal menu del gruppo di risorse. | Impossibile modificare l'ambito.|
| Subscription | Record creati da tutte le risorse nella sottoscrizione. Può includere dati da più aree di lavoro di Log Analytics. | Selezionare **Registri** dal menu di sottoscrizione.   | Impossibile modificare l'ambito. |
| Altre risorse di Azure | Record creati dalla risorsa. Può includere dati da più aree di lavoro di Log Analytics.  | Selezionare **Registri** dal menu delle risorse.<br>o<br>Selezionare **Log** dal menu **Monitoraggio di Azure** e quindi selezionare un nuovo ambito. | Può solo modificare l'ambito nello stesso tipo di risorsa. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitazioni nell'ambito di una risorsaLimitations when scoped to a resource

Quando l'ambito della query è un'area di lavoro di Log Analytics o un'applicazione di Application Insights, sono disponibili tutte le opzioni nel portale e tutti i comandi di query. Quando l'ambito di una risorsa, tuttavia, le opzioni seguenti nel portale non sono disponibili perché sono associate a una singola area di lavoro o applicazione:When scoped to a resource though, the following options in the portal not available because they're associated with a single workspace or application:

- Salvare
- Esplora query
- Nuova regola di avviso

Non è possibile usare i comandi seguenti in una query quando viene definito nell'ambito di una risorsa poiché l'ambito della query includerà già tutte le aree di lavoro con i dati per la risorsa o il set di risorse:

- [App](app-expression.md)
- [Workspace](workspace-expression.md)
 

## <a name="query-limits"></a>Limiti delle query
Si potrebbero avere requisiti aziendali per una risorsa di Azure per scrivere i dati in più aree di lavoro di Log Analytics.You may have business requirements for an Azure resource to write data to multiple Log Analytics workspaces. Non è necessario che l'area di lavoro si trovara nella stessa area geografica della risorsa e una singola area di lavoro potrebbe raccogliere dati dalle risorse in diverse aree.  

L'impostazione dell'ambito su una risorsa o un set di risorse è una funzionalità particolarmente potente di Log Analytics poiché consente di consolidare automaticamente i dati distribuiti in una singola query. Può influire in modo significativo sulle prestazioni se i dati devono essere recuperati dalle aree di lavoro in più aree di Azure.It can significantly affect performance though if if data needs to be retrieved from workspaces across multiple Azure regions.

Log Analytics consente di proteggere da un sovraccarico eccessivo da query che si estendono su aree di lavoro in più aree emettendo un avviso o un errore quando viene utilizzato un determinato numero di aree. La query riceverà un avviso se l'ambito include aree di lavoro in 5 o più aree. verrà comunque eseguito, ma il completamento potrebbe richiedere tempo eccessivo.

![Avviso di query](media/scope/query-warning.png)

L'esecuzione della query verrà impedita se l'ambito include aree di lavoro in 20 o più aree. In questo caso verrà richiesto di ridurre il numero di aree dell'area di lavoro e di tentare di eseguire nuovamente la query. Nell'elenco a discesa verranno visualizzate tutte le aree nell'ambito della query ed è necessario ridurre il numero di aree prima di tentare di eseguire nuovamente la query.

![Query non riuscita](media/scope/query-failed.png)


## <a name="time-range"></a>Intervallo di ore
L'intervallo di tempo specifica il set di record che vengono valutati per la query in base al momento della creazione del record. Questo è definito da una proprietà standard in ogni record nell'area di lavoro o nell'applicazione come specificato nella tabella seguente.

| Location | Proprietà |
|:---|:---|
| Area di lavoro Log Analytics          | TimeGenerated |
| Applicazione Application Insights |  timestamp     |

Impostare l'intervallo di tempo selezionandolo dalla selezione ora nella parte superiore della finestra di Log Analytics.  È possibile selezionare un periodo predefinito o selezionare **Personalizzato** per specificare un intervallo di tempo specifico.

![Controllo di selezione di data e ora](media/scope/time-picker.png)

Se si imposta un filtro nella query che utilizza la proprietà standard time come illustrato nella tabella precedente, la selezione ora viene modificata in **Imposta nella query**e la selezione ora viene disabilitata. In questo caso, è più efficiente inserire il filtro all'inizio della query in modo che qualsiasi elaborazione successiva debba solo funzionare con i record filtrati.

![Query filtrata](media/scope/query-filtered.png)

Se si usa il comando [dell'area di lavoro](workspace-expression.md) o [dell'app](app-expression.md) per recuperare dati da un'altra area di lavoro o da un'altra applicazione, la selezione ora potrebbe comportarsi in modo diverso. Se l'ambito è un'area di lavoro di Log Analytics e si usa **l'app**o se l'ambito è un'applicazione di Application Insights e si usa **l'area di lavoro**, Log Analytics potrebbe non comprendere che la proprietà utilizzata nel filtro deve determinare il filtro temporale.

Nell'esempio seguente l'ambito è impostato su un'area di lavoro di Log Analytics.In the following example, the scope is set to a Log Analytics workspace.  La query utilizza **l'area di lavoro** per recuperare dati da un'altra area di lavoro di Log Analytics.The query uses workspace to retrieve data from another Log Analytics workspace. La selezione ora cambia **in Set nella query** perché visualizza un filtro che utilizza la proprietà **TimeGenerated prevista.**

![Eseguire query con l'area di lavoro](media/scope/query-workspace.png)

Se la query usa **l'app** per recuperare dati da un'applicazione di Application Insights, Log Analytics non riconosce la proprietà **timestamp** nel filtro e la selezione ora rimane invariata. In questo caso, vengono applicati entrambi i filtri. Nell'esempio, solo i record creati nelle ultime 24 ore vengono inclusi nella query anche se specifica 7 giorni nella clausola **where.**

![Query con app](media/scope/query-app.png)

## <a name="next-steps"></a>Passaggi successivi

- Eseguire [un'esercitazione sull'uso](get-started-portal.md)di Log Analytics nel portale di Azure.Walk through a tutorial on using Log Analytics in the Azure portal .
- Scorrere [un'esercitazione sulla scrittura di query](get-started-queries.md).
