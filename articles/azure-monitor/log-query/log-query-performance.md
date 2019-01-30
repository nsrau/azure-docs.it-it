---
title: Scrittura di query di log efficienti in Monitoraggio di Azure | Microsoft Docs
description: Riferimenti alle risorse per l'apprendimento di come scrivere query in Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: 25d6b582ed4d4e24df3841f4191471296e25abd8
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54436333"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Scrittura di query di log efficienti in Monitoraggio di Azure
Questo articolo fornisce indicazioni per la scrittura di query di log efficienti in Monitoraggio di Azure. Queste strategie garantiscono infatti che le query vengano eseguite rapidamente e con un sovraccarico minimo.

## <a name="scope-your-query"></a>Definire l'ambito della query
Se nel processo di query sono presenti più dati di quanti siano effettivamente necessari, è possibile che venga generata una query con esecuzione prolungata e che i risultati contengano troppi dati per poter essere analizzati in modo efficace. In casi estremi, la query può addirittura raggiungere il timeout e avere esito negativo.

### <a name="specify-your-data-source"></a>Specificare l'origine dati
Il primo passaggio per la scrittura di una query efficiente consiste nel limitarne l'ambito alle origini dati necessarie. Specificare una tabella è sempre preferibile rispetto all'esecuzione di una ricerca di testo a livello, ad esempio `search *`. Per eseguire una query su una tabella specifica, avviare la query con il nome della tabella come illustrato di seguito:

``` Kusto
requests | ...
```

È possibile usare [search](/azure/kusto/query/searchoperator) per cercare un valore in più colonne di determinate tabelle definendo una query simile alla seguente:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Usare [union](/azure/kusto/query/unionoperator) per eseguire una query su più tabelle, come illustrato di seguito:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Specificare un intervallo di tempo
È necessario anche limitare la query all'intervallo di tempo dei dati necessari. Per impostazione predefinita, la query include i dati raccolti nelle ultime 24 ore. È possibile modificare questa opzione nel [selettore dell'intervallo di tempo](get-started-portal.md#select-a-time-range) o aggiungerla in modo esplicito alla query. È consigliabile aggiungere il filtro temporale immediatamente dopo il nome della tabella, in modo che il resto della query elabori solo i dati contenuti all'interno dell'intervallo:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Ottenere solo i log più recenti

Se si preferisce che vengano restituiti solo i record più recenti, usare l'operatore *top* come nella query seguente, che restituisce i 10 record più recenti registrati nella tabella *traces*:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtrare i record
Per esaminare solo i log che corrispondono a una determinata condizione, usare l'operatore *where* come nella query seguente, che restituisce solo i record in cui il valore _severityLevel_ è maggiore di 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Confronti di stringhe
Quando si [valutano le stringhe](/azure/kusto/query/datatypes-string-operators), in genere è preferibile usare `has` anziché `contains` per la ricerca di token completi. `has` è più efficiente poiché non richiede la ricerca di sottostringhe.

## <a name="returned-columns"></a>Colonne restituite

Usare [project](/azure/kusto/query/projectoperator) per restringere il set di colonne in fase di elaborazione a quelle effettivamente necessarie:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Anche se si usa [extend](/azure/kusto/query/extendoperator) per calcolare i valori e creare colonne personalizzate, in genere è più efficiente usare un filtro su una colonna della tabella.

La prima query riportata di seguito, ad esempio, che usa un filtro su _operation\_Name_, è più efficiente rispetto alla seconda, che crea una nuova colonna _subscription_, a cui applica un filtro:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Uso dei join
Quando si usa l'operatore [join](/azure/kusto/query/joinoperator), scegliere la tabella con il minor numero di righe da posizionare sul lato sinistro della query.


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle procedure consigliate per le query, vedere [Procedure consigliate per le query](/azure/kusto/query/best-practices).