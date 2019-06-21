---
title: Ambito di query del log in Azure Monitor Log Analitica | Microsoft Docs
description: Descrive l'intervallo di tempo e ambito per una query di log in Azure Monitor Log Analitica.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: a948b80f6524339f0908a2fb19c4a83d70b3b140
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297172"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Intervallo di tempo e ambito delle query di log in Azure Monitor Log Analitica
Quando si esegue una [query di log](log-query-overview.md) nelle [Analitica di Log nel portale di Azure](get-started-portal.md), il set di dati valutati dalla query varia a seconda dell'ambito e l'intervallo di tempo selezionato. Questo articolo descrive l'intervallo di tempo e ambito e come è possibile impostare ciascuno in base alle esigenze. Vengono inoltre descritti il comportamento di diversi tipi di ambiti.


## <a name="query-scope"></a>Ambito query
L'ambito di query definisce i record che vengono valutati tramite la query. In genere includerà tutti i record in una singola area di lavoro di Log Analitica o l'applicazione di Application Insights. Log Analitica consente inoltre di impostare un ambito per una particolare risorsa di Azure monitorata. Ciò consente un proprietario della risorsa per concentrarsi solo sui dati, anche se tale risorsa scrive in più aree di lavoro.

L'ambito viene sempre visualizzato nella parte superiore sinistra della finestra Log Analitica. Un'icona indica se l'ambito è un'area di lavoro di Log Analitica o di un'applicazione di Application Insights. Nessuna icona indica un'altra risorsa di Azure.

![`Scope`](media/scope/scope.png)

L'ambito è determinato dal metodo usato per avviare Log Analitica e in alcuni casi è possibile modificare l'ambito facendo clic su di esso. La tabella seguente elenca i diversi tipi di ambito utilizzato e dettagli diversi per ognuno.

| Ambito query | Record nell'ambito | Come selezionare | La modifica dell'ambito |
|:---|:---|:---|:---|
| Area di lavoro Log Analytics | Tutti i record nell'area di lavoro di Log Analitica. | Selezionare **registri** dal **monitoraggio di Azure** menu o la **aree di lavoro di Log Analitica** menu.  | Può modificare l'ambito in qualsiasi altro tipo di risorsa. |
| Applicazione di application Insights | Tutti i record nell'applicazione di Application Insights. | Selezionare **Analitica** dalla **Panoramica** pagina di Application Insights. | Può modificare solo ambito a un'altra applicazione di Application Insights. |
| Gruppo di risorse | Record creati da tutte le risorse nel gruppo di risorse. Possono includere dati da più aree di lavoro di Log Analitica. | Selezionare **registri** dal menu del gruppo di risorse. | Non è possibile modificare l'ambito.|
| Sottoscrizione | Record creati da tutte le risorse nella sottoscrizione. Possono includere dati da più aree di lavoro di Log Analitica. | Selezionare **registri** dal menu di sottoscrizione.   | Non è possibile modificare l'ambito. |
| Altre risorse di Azure | Record creati dalla risorsa. Possono includere dati da più aree di lavoro di Log Analitica.  | Selezionare **registri** dal menu delle risorse.<br>Oppure<br>Selezionare **registri** dalle **monitoraggio di Azure** menu e quindi selezionare un nuovo ambito. | Può modificare solo ambito nello stesso tipo di risorsa. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitazioni quando si ha come ambito una risorsa

Quando nell'ambito della query è un'area di lavoro di Log Analitica o di un'applicazione di Application Insights, sono disponibili tutte le opzioni nel portale e tutti i comandi di query. Quando si hanno tuttavia ambito a una risorsa, le opzioni seguenti nel portale non è disponibile perché sono associate con una singola area di lavoro o l'applicazione:

- Salva
- Esplora query
- Nuova regola di avviso

È possibile utilizzare i comandi seguenti in una query quando nell'ambito di una risorsa perché l'ambito della query includerà già le aree di lavoro con i dati per tale risorsa o un set di risorse:

- [app](app-expression.md)
- [workspace](workspace-expression.md)
 


## <a name="time-range"></a>Intervallo di tempo
L'intervallo di tempo specifica il set di record che vengono valutati per la query in base a quando il record è stato creato. Ciò viene definito da una proprietà standard in tutti i record nell'area di lavoro o dell'applicazione come specificato nella tabella seguente.

| Località | Proprietà |
|:---|:---|
| Area di lavoro Log Analytics          | TimeGenerated |
| Applicazione di application Insights | timestamp     |

Impostare l'intervallo di tempo, selezionarlo dalla selezione tempo nella parte superiore della finestra del Log Analitica.  È possibile selezionare un periodo predefinito o selezionare **Custom** per specificare un intervallo di tempo specifico.

![Controllo di selezione di data e ora](media/scope/time-picker.png)

Se si imposta un filtro nella query che usa la proprietà ora solare come illustrato nella tabella precedente, la selezione di tempo cambia in **impostato nella query**, e la selezione di tempo è disabilitata. In questo caso, è più efficiente per inserire il filtro nella parte superiore della query in modo che qualsiasi elaborazione successive è sufficiente lavorare con i record filtrati.

![Query filtrata](media/scope/query-filtered.png)

Se si usa la [dell'area di lavoro](workspace-expression.md) oppure [app](app-expression.md) comando per recuperare i dati da un'altra area di lavoro o un'applicazione, il controllo di selezione ora può comportarsi in modo diverso. Se l'ambito è un'area di lavoro di Log Analitica e userai **app**, o se l'ambito è un'applicazione di Application Insights e userai **dell'area di lavoro**, quindi Log Analitica potrebbero non comprendere che la proprietà utilizzata nel filtro deve determinare il filtro temporale.

Nell'esempio seguente, l'ambito è impostato su un'area di lavoro di Log Analitica.  La query utilizza **dell'area di lavoro** per recuperare dati da un'altra area di lavoro di Log Analitica. La selezione di tempo cambia in **query impostata** poiché ne viene rilevato un filtro che usi quella prevista **TimeGenerated** proprietà.

![Eseguire una query con area di lavoro](media/scope/query-workspace.png)

Se la query utilizza **app** per recuperare dati da un'applicazione di Application Insights, tuttavia, Log Analitica non riconosce le **timestamp** proprietà nel filtro e le date time picker rimane invariata. In questo caso, entrambi i filtri vengono applicati. Nell'esempio, anche se specifica di 7 giorni nella query sono inclusi solo i record creati nelle ultime 24 ore il **in cui** clausola.

![Eseguire una query con l'app](media/scope/query-app.png)

## <a name="next-steps"></a>Passaggi successivi

- Esaminare una [esercitazione sull'uso di Log Analitica nel portale di Azure](get-started-portal.md).
- Esaminare una [esercitazione sulla scrittura di query](get-started-queries.md).