---
title: Ambito delle query su log in Log Analytics di Monitoraggio di Azure | Microsoft Docs
description: Descrive l'ambito e l'intervallo di tempo per query su log in Log Analytics di Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/01/2020
ms.openlocfilehash: 2840e5b8ff16d44f76aaafcf68264c65e4401ff7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199018"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Ambito e intervallo di tempo delle query su log in Log Analytics di Monitoraggio di Azure
Quando si esegue una [query su log](log-query-overview.md) in [Log Analytics nel portale di Azure](get-started-portal.md), il set di dati valutato dalla query dipende dall'ambito e dall'intervallo di tempo selezionati. Questo articolo descrive l'ambito e l'intervallo di tempo e spiega come impostarli in base alle esigenze. Illustra anche il comportamento dei diversi tipi di ambiti.


## <a name="query-scope"></a>Ambito della query
L'ambito della query definisce i record valutati dalla query. Include in genere tutti i record in una singola area di lavoro Log Analytics o applicazione di Application Insights. Log Analytics consente anche di impostare un ambito per una particolare risorsa di Azure monitorata. In questo modo un proprietario di risorse può concentrarsi solo sui dati, anche se la risorsa scrive in più aree di lavoro.

L'ambito viene sempre visualizzato in alto a sinistra nella finestra di Log Analytics. Un'icona indica se l'ambito è un'area di lavoro Log Analytics o un'applicazione di Application Insights. Non viene visualizzata alcuna icona indica un'altra risorsa di Azure.

![Ambito](media/scope/scope.png)

L'ambito è determinato dal metodo usato per avviare Log Analytics. In alcuni casi è sufficiente fare clic sull'ambito per modificarlo. La tabella seguente elenca i diversi tipi di ambito usati e riporta i dettagli per ognuno di essi.

> [!IMPORTANT]
> Se si usa Application Performance Monitoring 2.1, le applicazioni di Application Insights vengono archiviate in un'area di lavoro Log Analytics con tutti gli altri dati del log e l'ambito Application Insights non è disponibile. Selezionare **Log** nel menu Application Insights equivale a selezionare l'ambito **Altre risorse di Azure** e nelle tabelle di Application Insights sono disponibili solo i dati di tale applicazione.

| Ambito della query | Record nell'ambito | Modalità di selezione | Modifica dell'ambito |
|:---|:---|:---|:---|
| Area di lavoro Log Analytics | Tutti i record nell'area di lavoro Log Analytics. | Selezionare **Log** nel menu **Monitoraggio di Azure** oppure il menu **Aree di lavoro Log Analytics**.  | È possibile impostare l'ambito su qualsiasi altro tipo di risorsa. |
| Applicazione di Application Insights | Tutti i record nell'applicazione di Application Insights. | Selezionare **Analisi** nella pagina **Panoramica** di Application Insights. | È possibile impostare l'ambito solo su un'altra applicazione di Application Insights. |
| Gruppo di risorse | Record creati da tutte le risorse nel gruppo di risorse. Può includere dati di più aree di lavoro Log Analytics. | Selezionare **Log** nel menu del gruppo di risorse. | L'ambito non è modificabile.|
| Sottoscrizione | Record creati da tutte le risorse della sottoscrizione. Può includere dati di più aree di lavoro Log Analytics. | Selezionare **Log** dal menu della sottoscrizione.   | L'ambito non è modificabile. |
| Altre risorse di Azure | Record creati dalla risorsa. Può includere dati di più aree di lavoro Log Analytics.  | Selezionare **Log** nel menu delle risorse.<br>OPPURE<br>Selezionare **Log** nel menu **Monitoraggio di Azure** e quindi selezionare un nuovo ambito. | È possibile impostare l'ambito solo sullo stesso tipo di risorsa. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitazioni nel caso in cui l'ambito è impostato su una risorsa

Quando l'ambito della query è un'area di lavoro Log Analytics o un'applicazione di Application Insights, sono disponibili tutte le opzioni nel portale e tutti i comandi di query. Se l'ambito è limitato a una risorsa, le opzioni seguenti del portale non sono disponibili perché sono associate a una singola applicazione o area di lavoro:

- Salva
- Esplora query
- Nuova regola di avviso

Non è possibile usare i comandi seguenti in una query se l'ambito è una risorsa perché l'ambito della query includerà già tutte le aree di lavoro con i dati relativi a tale risorsa o set di risorse:

- [app](app-expression.md)
- [workspace](workspace-expression.md)
 

## <a name="query-limits"></a>Limiti delle query
La scrittura di dati relativi a una risorsa di Azure in più aree di lavoro Log Analytics può essere soggetta a requisiti aziendali. L'area di lavoro non deve trovarsi nella stessa area della risorsa e una singola area di lavoro può raccogliere dati di risorse in numerose aree.  

L'impostazione dell'ambito su una risorsa o su un set di risorse è una funzionalità particolarmente efficace di Log Analytics perché consente di consolidare automaticamente i dati distribuiti in un'unica query. Questo comportamento può influire significativamente sulle prestazioni, anche se i dati devono essere recuperati da aree di lavoro in più aree di Azure.

Log Analytics consente di evitare il sovraccarico eccessivo generato da query che interessano aree di lavoro in più aree perché restituisce un avviso o un errore quando si usa un determinato numero di aree. La query riceverà un avviso se l'ambito include aree di lavoro in cinque o più aree. Verrà comunque eseguita, ma il completamento richiederà moltissimo tempo.

![Avviso per la query](media/scope/query-warning.png)

L'esecuzione della query verrà bloccata se l'ambito include aree di lavoro in 20 o più aree. In questo caso verrà richiesto di ridurre il numero di aree dell'area di lavoro e verrà effettuato un nuovo tentativo di esecuzione della query. Nell'elenco a discesa verranno visualizzate tutte le aree incluse nell'ambito della query e sarà necessario ridurne il numero prima di provare a eseguire di nuovo la query.

![Query non riuscita](media/scope/query-failed.png)


## <a name="time-range"></a>Intervallo di tempo
L'intervallo di tempo consente di specificare il set di record che vengono valutati per la query in base alla data e all'ora di creazione del record. Questo valore viene definito da una proprietà standard per ogni record nell'area di lavoro o nell'applicazione come specificato nella tabella seguente.

| Posizione | Proprietà |
|:---|:---|
| Area di lavoro Log Analytics          | TimeGenerated |
| Applicazione di Application Insights | timestamp     |

Per impostare l'intervallo di tempo, selezionarlo dal controllo di selezione di data e ora nella parte superiore della finestra di Log Analytics.  È possibile selezionare un periodo predefinito oppure selezionare **Personalizzato** per indicare un intervallo di tempo specifico.

![Controllo di selezione di data e ora](media/scope/time-picker.png)

Se nella query si imposta un filtro che usa la proprietà time standard come illustrato nella tabella precedente, il controllo di selezione di data e ora diventa **Imposta nella query** e viene disabilitato. In questo caso, risulta più efficace inserire il filtro all'inizio della query in modo che qualsiasi operazione di elaborazione successiva venga eseguita solo sui record filtrati.

![Query filtrata](media/scope/query-filtered.png)

Se si usa il comando [workspace](workspace-expression.md) o [app](app-expression.md) per recuperare i dati di un'altra applicazione o area di lavoro, il controllo di selezione di data e ora potrebbe comportarsi in modo diverso. Se l'ambito è un'area di lavoro Log Analytics e si usa **app** o se l'ambito è un'applicazione di Application Insights e si usa **workspace**, Log Analytics potrebbe non riconoscere che il filtro temporale deve essere determinato dalla proprietà usata nel filtro.

Nell'esempio seguente l'ambito è impostato su un'area di lavoro Log Analytics.  La query usa **workspace** per recuperare i dati di un'altra area di lavoro Log Analytics. Il controllo di selezione di data e ora diventa **Imposta nella query** perché rileva un filtro che usa la proprietà **TimeGenerated** prevista.

![Query con workspace](media/scope/query-workspace.png)

Se la query usa **app** per recuperare i dati di un'applicazione di Application Insights, Log Analytics non riconosce la proprietà **timestamp** nel filtro e il controllo di selezione di data e ora rimane invariato. In questo caso vengono applicati entrambi i filtri. Nella query dell'esempio vengono inclusi solo i record creati nelle ultime 24 ore, anche se la clausola **where** è impostata su 7 giorni.

![Query con app](media/scope/query-app.png)

## <a name="next-steps"></a>Passaggi successivi

- Seguire un'[esercitazione sull'uso di Log Analytics nel portale di Azure](get-started-portal.md).
- Seguire un'[esercitazione sulla scrittura di query](get-started-queries.md).