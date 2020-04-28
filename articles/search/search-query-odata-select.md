---
title: Riferimento di selezione OData
titleSuffix: Azure Cognitive Search
description: Guida di riferimento al linguaggio e alla sintassi per la selezione esplicita dei campi da restituire nei risultati della ricerca di query ricerca cognitiva di Azure.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 64f15bf3d262249cdda2760c7ddf768be2590419
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113094"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>Sintassi di $select OData in Azure ricerca cognitiva

 È possibile usare il [parametro OData **$Select** ](query-odata-filter-orderby-syntax.md) per scegliere i campi da includere nei risultati della ricerca da Azure ricerca cognitiva. Questo articolo descrive in dettaglio la sintassi dei **$Select** . Per informazioni generali su come usare **$Select** quando si presentano i risultati della ricerca, vedere [come usare i risultati della ricerca in Azure ricerca cognitiva](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintassi

Il parametro **$Select** determina i campi di ogni documento che vengono restituiti nel set di risultati della query. Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica per il parametro **$SELECT** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca cognitiva di Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Per la EBNF completa, vedere informazioni [di riferimento sulla sintassi delle espressioni OData per Azure ricerca cognitiva](search-query-odata-syntax-reference.md) .

Il parametro **$Select** viene visualizzato in due formati:

1. Una singola stella (`*`) che indica che devono essere restituiti tutti i campi recuperabili oppure
1. Elenco delimitato da virgole dei percorsi dei campi, che identificano i campi da restituire.

Quando si usa il secondo form, è possibile specificare solo i campi recuperabili nell'elenco.

Se si elenca un campo complesso senza specificare i campi secondari in modo esplicito, tutti i campi secondari recuperabili verranno inclusi nel set di risultati della query. Si supponga, ad esempio, che l' `Address` indice includa `City`un campo `Country` con `Street`i campi, e che possono essere recuperati. Se si specifica `Address` in **$SELECT**, i risultati della query includeranno tutti e tre i campi secondari.

## <a name="examples"></a>Esempi

Includere i `HotelId`campi `HotelName`di primo `Rating` livello, e nei risultati, nonché il `City` campo secondario di: `Address`

    $select=HotelId, HotelName, Rating, Address/City

Un risultato di esempio può essere simile al seguente:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Includere il `HotelName` `Address`campo di primo livello nei risultati, nonché tutti i sottocampi di e i `Type` sottocampi e `BaseRate` di ogni oggetto della `Rooms` raccolta:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Un risultato di esempio può essere simile al seguente:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi  

- [Come usare i risultati della ricerca in Azure ricerca cognitiva](search-pagination-page-layout.md)
- [Cenni preliminari sul linguaggio di espressioni OData per ricerca cognitiva di Azure](query-odata-filter-orderby-syntax.md)
- [Informazioni di riferimento sulla sintassi delle espressioni OData per ricerca cognitiva di Azure](search-query-odata-syntax-reference.md)
- [Eseguire ricerche nei documenti &#40;API REST di Azure ricerca cognitiva&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
