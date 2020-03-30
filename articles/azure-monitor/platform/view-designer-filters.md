---
title: Filtri nelle visualizzazioni di Monitor di Azure Documenti Microsoft
description: Un filtro in una visualizzazione Di Monitoraggio di Azure consente agli utenti di filtrare i dati nella visualizzazione in base al valore di una determinata proprietà senza modificare la visualizzazione stessa.  Questo articolo descrive come usare un filtro e aggiungerne uno a una vista personalizzata.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: b4840ed30eb1f6dc8d6e6cef47da17807f9644d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658575"
---
# <a name="filters-in-azure-monitor-views"></a>Filtri nelle visualizzazioni di Monitoraggio di AzureFilters in Azure Monitor views
Un **filtro** in una [visualizzazione Di Monitoraggio di Azure](view-designer.md) consente agli utenti di filtrare i dati nella visualizzazione in base al valore di una determinata proprietà senza modificare la visualizzazione stessa.  È ad esempio possibile consentire agli utenti della vista di filtrare solo i dati di un computer o un set di computer specifico.  È possibile creare più filtri in una singola vista per consentire agli utenti di filtrare in base a più proprietà.  Questo articolo descrive come usare un filtro e aggiungerne uno a una vista personalizzata.

## <a name="using-a-filter"></a>Uso di un filtro
Fare clic sull'intervallo di data e ora nella parte superiore di una visualizzazione per aprire l'elenco a discesa in cui è possibile modificare l'intervallo di data e ora per la visualizzazione.

![Esempio di filtro](media/view-designer-filters/filters-example-time.png)

Fare **+** clic su per aggiungere un filtro utilizzando filtri personalizzati definiti per la visualizzazione. Selezionare un valore per il filtro dall'elenco a discesa. Continuare ad aggiungere filtri **+** facendo clic sul pulsante . 


![Esempio di filtro](media/view-designer-filters/filters-example-custom.png)

Se si rimuovono tutti i valori per un filtro, il filtro non verrà più applicato.


## <a name="creating-a-filter"></a>Creazione di un filtro

Creare un filtro dalla scheda **Filtri** quando si [modifica una vista](view-designer.md).  Il filtro è globale per la vista e si applica a tutte le parti della vista.  

![Impostazioni dei filtri](media/view-designer-filters/filters-settings.png)

La tabella seguente descrive le impostazioni per un filtro.

| Impostazione | Descrizione |
|:---|:---|
| Nome campo | Nome del campo usato per filtrare.  Questo campo deve corrispondere al campo riepilogare in **Query per valori**. |
| Esegui query per valori | Query da eseguire per popolare l'elenco a discesa relativo al filtro per l'utente.  Questa query deve utilizzare [sum](/azure/kusto/query/summarizeoperator) o [distinct](/azure/kusto/query/distinctoperator) per fornire valori univoci per un determinato campo e deve corrispondere a **Nome campo**.  È possibile usare l'opzione [sort](/azure/kusto/query/sortoperator) per ordinare i valori visualizzati dall'utente. |
| Tag | Nome del campo usato nelle query che supportano il filtro e visualizzato dall'utente. |

### <a name="examples"></a>Esempi

La tabella seguente include alcuni esempi di filtri comuni.  

| Nome campo | Esegui query per valori | Tag |
|:--|:--|:--|
| Computer   | Heartbeat &#124; distinct Computer &#124; sort by Computer asc | Computers (Computer) |
| EventLevelName | Event &#124; distinct EventLevelName | Gravità |
| SeverityLevel | Syslog &#124; distinct SeverityLevel | Gravità |
| SvcChangeType | ConfigurationChange &#124; distinct svcChangeType | ChangeType |


## <a name="modify-view-queries"></a>Modificare le query della vista

Affinché un filtro abbia effetto, è necessario modificare le query nella vista per filtrare i valori selezionati.  Se non si modificano le query nella visualizzazione, i valori selezionati dall'utente non avranno alcun effetto.

La sintassi per l'uso di un valore di filtro in una query è: 

    where ${filter name}  

Ad esempio, se la visualizzazione dispone di una query che restituisce eventi e utilizza un filtro denominato _Computer_, è possibile utilizzare la query seguente.

    Event | where ${Computers} | summarize count() by EventLevelName

Se si aggiunge un altro filtro denominato Severity, è possibile usare la query seguente per applicare entrambi i filtri.

    Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName

## <a name="next-steps"></a>Passaggi successivi
* Leggere altre informazioni sulle [parti di visualizzazione](view-designer-parts.md) che è possibile aggiungere alla vista personalizzata.
