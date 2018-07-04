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
ms.date: 06/22/2018
ms.author: bwren
ms.openlocfilehash: 21b54f60286e25c410b9d51de8be122c450080d3
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752776"
---
# <a name="filters-in-log-analytics-views"></a>Filtri nelle viste di Log Analytics
Un **filtro** in una [vista di Log Analytics](log-analytics-view-designer.md) consente agli utenti di filtrare i dati della vista in base al valore di una proprietà specifica senza modificare la vista stessa.  È ad esempio possibile consentire agli utenti della vista di filtrare solo i dati di un computer o un set di computer specifico.  È possibile creare più filtri in una singola vista per consentire agli utenti di filtrare in base a più proprietà.  Questo articolo descrive come usare un filtro e aggiungerne uno a una vista personalizzata.

## <a name="using-a-filter"></a>Uso di un filtro
Fare clic sull'intervallo di tempo relativo ai dati nella parte superiore della vista per aprire l'elenco a discesa dove è possibile modificare l'intervallo di dati dalla vista.

![Esempio di filtro](media/log-analytics-view-designer/filters-example-time.png)

Fare clic su **+** per aggiungere un filtro mediante i filtri personalizzati definiti per la vista. Selezionare un valore per il filtro dall'elenco a discesa. Continuare ad aggiungere filtri facendo clic su **+**. 


![Esempio di filtro](media/log-analytics-view-designer/filters-example-custom.png)

Se si rimuovono tutti i valori per un filtro, il filtro non verrà più applicato.


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