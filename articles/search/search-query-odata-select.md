---
title: Riferimento di selezione OData
titleSuffix: Azure Cognitive Search
description: Informazioni di riferimento sulla sintassi e sul linguaggio per la selezione esplicita dei campi da restituire nei risultati della ricerca delle query di Ricerca cognitiva di Azure.Syntax and language reference for explicit selection of fields to return in the search results of Azure Cognitive Search queries.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113094"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select syntax in Azure Cognitive Search

 È possibile usare il [parametro **OData $select** ](query-odata-filter-orderby-syntax.md) per scegliere i campi da includere nei risultati della ricerca da Ricerca cognitiva di Azure.You can use the OData $select parameter to choose which fields to include in search results from Azure Cognitive Search. In questo articolo viene descritta in dettaglio la sintassi dei **$select.** Per altre informazioni generali su come usare **$select** quando si presentano i risultati della ricerca, vedere Come usare i risultati della [ricerca in Ricerca cognitiva](search-pagination-page-layout.md)di Azure.For more general information about how to use $select when presenting search results, see How to work with search results in Azure Cognitive Search .

## <a name="syntax"></a>Sintassi

Il **parametro $select** determina quali campi per ogni documento vengono restituiti nel set di risultati della query. Il seguente File EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica per il parametro **$select:**

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

È disponibile anche un diagramma di sintassi interattivo:An interactive syntax diagram is also available:

> [!div class="nextstepaction"]
> [OData syntax diagram for Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Vedere Informazioni di riferimento sulla sintassi delle [espressioni OData per Ricerca cognitiva](search-query-odata-syntax-reference.md) di Azure per l'EBNF completo.

Il parametro **$select** è disponibile in due forme:

1. Una singola`*`stella ( ), che indica che tutti i campi recuperabili devono essere restituiti, o
1. Elenco delimitato da virgole di percorsi di campo che identifica i campi da restituire.

Quando si utilizza il secondo modulo, è possibile specificare solo i campi recuperabili nell'elenco.

Se si elenca un campo complesso senza specificarne i campi secondari in modo esplicito, tutti i sottocampi recuperabili verranno inclusi nel set di risultati della query. Si supponga, ad esempio, che l'indice conleabbia `Address` un campo con `Street`, `City`e `Country` sottocampi tutti recuperabili. Se si `Address` specifica in **$select**, i risultati della query includeranno tutti e tre i sottocampi.

## <a name="examples"></a>Esempi

Includere `HotelId`i `HotelName`campi `Rating` , e di primo livello nei `City` risultati, `Address`nonché il sottocampo di :

    $select=HotelId, HotelName, Rating, Address/City

Un risultato di esempio potrebbe essere simile al seguente:An example result might look like this:

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

Includere `HotelName` il campo di primo livello nei risultati, nonché tutti i sottocampi di `Address`, e i `Type` sottocampi e `BaseRate` di ogni oggetto nell'insieme: `Rooms`

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Un risultato di esempio potrebbe essere simile al seguente:An example result might look like this:

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

- [Come usare i risultati della ricerca in Ricerca cognitiva di AzureHow to work with search results in Azure Cognitive Search](search-pagination-page-layout.md)
- [OData expression language overview for Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [OData expression syntax reference for Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [&#41;API REST di Ricerca documenti &#40;Ricerca cognitiva di AzureSearch Documents &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
