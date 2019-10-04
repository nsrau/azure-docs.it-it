---
title: Informazioni sul linguaggio di query
description: Vengono descritti gli operatori e le funzioni kusto disponibili utilizzabili con Azure Resource Graph.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c6e35d688581d0839e12806117e63c7d71fbc459
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231506"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Informazioni sul linguaggio di query di Azure Resource Graph

Il linguaggio di query di Azure Resource Graph supporta un certo numero di operatori e funzioni, ognuno dei quali opera in base ad [Esplora dati di Azure](../../../data-explorer/data-explorer-overview.md).

Il modo migliore per apprendere il linguaggio di query usato da Resource Graph è quello di iniziare con la documentazione relativa al [linguaggio di query](/azure/kusto/query/index) di Esplora dati di Azure, Illustra come è strutturato il linguaggio e in che modo interagiscono i vari operatori e le funzioni supportati.

## <a name="supported-tabular-operators"></a>Operatori tabulari supportati

Ecco l'elenco degli operatori tabulari supportati in Resource Graph:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [sort by](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Funzioni supportate

Ecco l'elenco delle funzioni supportate in Resource Graph:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>Caratteri di escape

Alcuni nomi di proprietà, ad esempio quelli che includono `.` o `$`, devono essere racchiusi o preceduti da un carattere di escape nella query o il nome della proprietà non è interpretato correttamente e non fornisce i risultati previsti.

- `.`-Eseguire il wrapping del nome della proprietà come segue:`['propertyname.withaperiod']`
  
  Query di esempio che esegue il wrapping della proprietà _OData. Type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-Escape per il carattere nel nome della proprietà. Il carattere di escape usato dipende dal grafico delle risorse della shell che viene eseguito da.

  - **bash** - `\`

    Query di esempio che esegue l'escape del  _\$tipo_ di proprietà in bash:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** : non escludere `$` il carattere di escape.

  - **PowerShell** - ``` ` ```

    Query di esempio che esegue l'escape del  _\$tipo_ di proprietà in PowerShell:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso nelle [query di base](../samples/starter.md)
- Vedere gli usi avanzati nelle [query avanzate](../samples/advanced.md)
- Informazioni su come [esplorare le risorse](explore-resources.md)