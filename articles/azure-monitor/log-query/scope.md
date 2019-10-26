---
title: Ambito della query di log in monitoraggio di Azure Log Analytics | Microsoft Docs
description: Descrive l'ambito e l'intervallo di tempo per una query di log in monitoraggio di Azure Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2019
ms.openlocfilehash: 03e5e1bc79702a979be352095bb4833a7f5fe1c6
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900227"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Ambito della query di log e intervallo di tempo in monitoraggio di Azure Log Analytics
Quando si esegue una [query di log](log-query-overview.md) in [log Analytics nel portale di Azure](get-started-portal.md), il set di dati valutato dalla query dipende dall'ambito e dall'intervallo di tempo selezionato. Questo articolo descrive l'ambito e l'intervallo di tempo e il modo in cui è possibile impostarli in base alle esigenze. Viene inoltre descritto il comportamento di diversi tipi di ambiti.


## <a name="query-scope"></a>Ambito delle query
L'ambito della query definisce i record valutati dalla query. In genere, tutti i record vengono inclusi in una singola area di lavoro Log Analytics o Application Insights applicazione. Log Analytics consente anche di impostare un ambito per una particolare risorsa di Azure monitorata. Questo consente a un proprietario di risorse di concentrarsi solo sui dati, anche se la risorsa scrive in più aree di lavoro.

L'ambito viene sempre visualizzato in alto a sinistra della finestra di Log Analytics. Un'icona indica se l'ambito è un'area di lavoro Log Analytics o un'applicazione Application Insights. Nessuna icona indica un'altra risorsa di Azure.

![Scope](media/scope/scope.png)

L'ambito è determinato dal metodo usato per avviare Log Analytics e in alcuni casi è possibile modificare l'ambito facendo clic su di esso. Nella tabella seguente sono elencati i diversi tipi di ambito usati e i diversi dettagli per ognuno di essi.

| Ambito delle query | Record nell'ambito | Selezione | Modifica dell'ambito |
|:---|:---|:---|:---|
| Area di lavoro Log Analytics | Tutti i record nell'area di lavoro Log Analytics. | Selezionare **registri** dal menu **monitoraggio di Azure** o dal menu **aree di lavoro log Analytics** .  | Può modificare l'ambito in qualsiasi altro tipo di risorsa. |
| Applicazione Application Insights | Tutti i record nell'applicazione Application Insights. | Selezionare **Analytics** dalla pagina **Panoramica** di Application Insights. | È possibile modificare l'ambito solo in un'altra applicazione Application Insights. |
| Gruppo di risorse | Record creati da tutte le risorse nel gruppo di risorse. Può includere dati da più aree di lavoro Log Analytics. | Selezionare **registri** dal menu gruppo di risorse. | Impossibile modificare l'ambito.|
| Sottoscrizione | Record creati da tutte le risorse nella sottoscrizione. Può includere dati da più aree di lavoro Log Analytics. | Selezionare **registri** dal menu sottoscrizione.   | Impossibile modificare l'ambito. |
| Altre risorse di Azure | Record creati dalla risorsa. Può includere dati da più aree di lavoro Log Analytics.  | Selezionare **registri** dal menu risorsa.<br>Oppure<br>Selezionare **registri** dal menu **monitoraggio di Azure** e quindi selezionare un nuovo ambito. | Può solo modificare l'ambito con lo stesso tipo di risorsa. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitazioni per l'ambito di una risorsa

Quando l'ambito della query è un'area di lavoro Log Analytics o un'applicazione Application Insights, sono disponibili tutte le opzioni nel portale e in tutti i comandi di query. Se l'ambito è limitato a una risorsa, le opzioni seguenti nel portale non sono disponibili perché sono associate a una singola area di lavoro o a un'applicazione:

- Salva
- Esplora query
- Nuova regola di avviso

Non è possibile usare i comandi seguenti in una query se l'ambito è una risorsa perché l'ambito della query includerà già le aree di lavoro con i dati per la risorsa o il set di risorse:

- [app](app-expression.md)
- [workspace](workspace-expression.md)
 

## <a name="query-limits"></a>Limiti delle query
Per la scrittura di dati in più aree di lavoro Log Analytics è possibile che si disponga di requisiti aziendali per una risorsa di Azure. L'area di lavoro non deve trovarsi nella stessa area della risorsa e una singola area di lavoro può raccogliere dati da risorse in un'ampia gamma di aree.  

L'impostazione dell'ambito su una risorsa o un set di risorse è una funzionalità particolarmente efficace di Log Analytics perché consente di consolidare automaticamente i dati distribuiti in un'unica query. Questo può influire significativamente sulle prestazioni, anche se i dati devono essere recuperati dalle aree di lavoro in più aree di Azure.

Log Analytics aiuta a proteggersi da un sovraccarico eccessivo di query che si estendono su aree di lavoro in più aree generando un avviso o un errore quando viene usato un determinato numero di aree. La query riceverà un avviso se l'ambito include le aree di lavoro in 5 o più aree. verrà comunque eseguito, ma il completamento dell'operazione potrebbe richiedere troppo tempo.

![Avviso di query](media/scope/query-warning.png)

L'esecuzione della query verrà bloccata se l'ambito include aree di lavoro in 20 o più aree. In questo caso verrà richiesto di ridurre il numero di aree dell'area di lavoro e di provare a eseguire nuovamente la query. Nell'elenco a discesa verranno visualizzate tutte le aree nell'ambito della query ed è necessario ridurre il numero di aree prima di provare a eseguire nuovamente la query.

![Query non riuscita](media/scope/query-failed.png)


## <a name="time-range"></a>Intervallo di tempo
L'intervallo di tempo specifica il set di record che vengono valutati per la query in base al momento in cui è stato creato il record. Questa operazione viene definita da una proprietà standard per ogni record nell'area di lavoro o nell'applicazione come specificato nella tabella seguente.

| Località | Proprietà |
|:---|:---|
| Area di lavoro Log Analytics          | TimeGenerated |
| Applicazione Application Insights | timestamp     |

Per impostare l'intervallo di tempo, selezionarlo dal selettore temporale nella parte superiore della finestra di Log Analytics.  È possibile selezionare un periodo predefinito oppure selezionare **personalizzato** per specificare un intervallo di tempo specifico.

![Controllo di selezione di data e ora](media/scope/time-picker.png)

Se nella query si imposta un filtro che usa la proprietà ora solare, come illustrato nella tabella precedente, il selettore di ora cambia in **set in query**e la selezione ora è disabilitata. In questo caso, è più efficiente inserire il filtro nella parte superiore della query in modo che qualsiasi elaborazione successiva debba solo funzionare con i record filtrati.

![Query filtrata](media/scope/query-filtered.png)

Se si usa l' [area di lavoro](workspace-expression.md) o il comando [app](app-expression.md) per recuperare i dati da un'altra area di lavoro o un'altra applicazione, il selettore temporale può comportarsi Se l'ambito è un'area di lavoro Log Analytics e si usa l' **app**o se l'ambito è un'applicazione Application Insights e si usa l' **area di lavoro**, log Analytics potrebbe non comprendere che la proprietà utilizzata nel filtro deve determinare il filtro temporale.

Nell'esempio seguente l'ambito è impostato su un'area di lavoro Log Analytics.  La query utilizza l' **area di lavoro** per recuperare i dati da un'altra area di lavoro log Analytics. Il selettore di ora cambia **in set in query** perché vede un filtro che usa la proprietà **TimeGenerated** prevista.

![Eseguire query con l'area di lavoro](media/scope/query-workspace.png)

Se la query usa l' **app** per recuperare i dati da un'applicazione Application Insights, tuttavia, log Analytics non riconosce la proprietà **timestamp** nel filtro e il selettore di tempo rimane invariato. In questo caso, vengono applicati entrambi i filtri. Nell'esempio, nella query sono inclusi solo i record creati nelle ultime 24 ore, anche se specifica 7 giorni nella clausola **where** .

![Eseguire query con l'app](media/scope/query-app.png)

## <a name="next-steps"></a>Passaggi successivi

- Esaminare un' [esercitazione sull'uso di log Analytics nel portale di Azure](get-started-portal.md).
- Esaminare un' [esercitazione sulla scrittura di query](get-started-queries.md).