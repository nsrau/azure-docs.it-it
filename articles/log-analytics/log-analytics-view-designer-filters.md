---
title: Filtri nelle viste di Azure Log Analytics | Microsoft Docs
description: Un filtro in una vista di Log Analytics consente agli utenti di filtrare i dati della vista in base al valore di una proprietà specifica senza modificare la vista stessa.  Questo articolo descrive come usare un filtro e aggiungerne uno a una vista personalizzata.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: bwren
ms.openlocfilehash: 5c2201292eb085dcc043e4257580c7971dbaffbd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="filters-in-log-analytics-views"></a>Filtri nelle viste di Log Analytics
Un **filtro** in una [vista di Log Analytics](log-analytics-view-designer.md) consente agli utenti di filtrare i dati della vista in base al valore di una proprietà specifica senza modificare la vista stessa.  È ad esempio possibile consentire agli utenti della vista di filtrare solo i dati di un computer o un set di computer specifico.  È possibile creare più filtri in una singola vista per consentire agli utenti di filtrare in base a più proprietà.  Questo articolo descrive come usare un filtro e aggiungerne uno a una vista personalizzata.

## <a name="using-a-filter"></a>Uso di un filtro
Fare clic su **Filtro** per aprire il riquadro del filtro per una vista.  Qui è possibile selezionare un intervallo di tempo e i valori per i filtri disponibili per la vista.  Quando si seleziona un filtro, viene visualizzato un elenco dei valori disponibili.  È possibile selezionare uno o più valori oppure digitarli. La vista viene aggiornata automaticamente per filtrare i valori specificati. 

Se per un filtro non viene selezionato alcun valore, tale filtro non viene applicato alla vista.  Se si rimuovono tutti i valori per un filtro, il filtro non verrà più applicato.


![Esempio di filtro](media/log-analytics-view-designer/filters-example.png)


## <a name="creating-a-filter"></a>Creazione di un filtro

Creare un filtro dalla scheda **Filtri** quando si [modifica una vista](log-analytics-view-designer.md).  Il filtro è globale per la vista e si applica a tutte le parti della vista.  

![Impostazioni dei filtri](media/log-analytics-view-designer/filters-settings.png)

La tabella seguente descrive le impostazioni per un filtro.

| Impostazione | DESCRIZIONE |
|:---|:---|
| Nome campo | Nome del campo usato per filtrare.  Deve corrispondere al campo summarize in **Esegui query per valori**. |
| Esegui query per valori | Query da eseguire per popolare l'elenco a discesa relativo al filtro per l'utente.  Per la query è necessario usare l'opzione [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) o [distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator) per fornire valori univoci per un campo specifico e il campo deve corrispondere a **Nome campo**.  È possibile usare l'opzione [sort](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator) per ordinare i valori visualizzati dall'utente. |
| Tag | Nome del campo usato nelle query che supportano il filtro e visualizzato dall'utente. |

### <a name="examples"></a>Esempi

La tabella seguente include alcuni esempi di filtri comuni.  

| Nome campo | Esegui query per valori | Tag |
|:--|:--|:--|
| Computer   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Computer |
| EventLevelName | Event &#124; distinct EventLevelName | Gravità |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Gravità |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modificare le query della vista

Affinché un filtro abbia effetto, è necessario modificare le query nella vista per filtrare i valori selezionati.  Se non si modificano le query nella vista, i valori selezionati dall'utente non avranno effetto.

La sintassi per l'uso di un valore di filtro in una query è: 

    where ${filter name}  

Se, ad esempio, la vista ha una query che restituisce eventi e usa un filtro denominato Computers, è possibile usare quanto segue.

    Event | where ${Computers} | summarize count() by EventLevelName

Se si aggiunge un altro filtro denominato Severity, è possibile usare la query seguente per applicare entrambi i filtri.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Passaggi successivi
* Leggere altre informazioni sulle [parti di visualizzazione](log-analytics-view-designer-parts.md) che è possibile aggiungere alla vista personalizzata.