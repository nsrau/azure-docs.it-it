---
title: Informazioni sul linguaggio di query
description: Descrive gli operatori di Kusto disponibili e funzioni utilizzabili con Graph di Azure Resource.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 08e4f09665a3501073f55b7f5b82bf51cf508ea9
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276678"
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
- [clausola Order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [Project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [esempio-distinct](/azure/kusto/query/sampledistinctoperator)
- [ordina per](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [Top-hitters](/azure/kusto/query/tophittersoperator)
- [dove](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Funzioni supportate

Ecco l'elenco delle funzioni supportate in Resource Graph:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="next-steps"></a>Passaggi successivi

- Vedere il linguaggio in uso nelle [query di base](../samples/starter.md)
- Vedere gli usi avanzati nelle [query avanzate](../samples/advanced.md)
- Informazioni su come [esplorare le risorse](explore-resources.md)