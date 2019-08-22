---
title: Riferimento di selezione OData-ricerca di Azure
description: Informazioni di riferimento sul linguaggio OData per la sintassi SELECT nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 64e9ad75d88f595ab5def6fe8b63fee9407ae0fe
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647881"
---
# <a name="odata-select-syntax-in-azure-search"></a>Sintassi di $select OData in ricerca di Azure

 È possibile usare il [parametro OData **$Select** ](query-odata-filter-orderby-syntax.md) per scegliere i campi da includere nei risultati della ricerca di ricerca di Azure. Questo articolo descrive in dettaglio la sintassi dei **$Select** . Per informazioni più generali su come usare **$Select** quando si presentano i risultati della ricerca, vedere [come usare i risultati della ricerca in ricerca di Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintassi

Il parametro **$Select** determina i campi di ogni documento che vengono restituiti nel set di risultati della query. Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica per il parametro **$SELECT** :

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Vedere informazioni di [riferimento sulla sintassi di espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il EBNF completo.

Il parametro **$Select** viene visualizzato in due formati:

1. Una singola stella (`*`) che indica che devono essere restituiti tutti i campi recuperabili oppure
1. Elenco delimitato da virgole dei percorsi dei campi, che identificano i campi da restituire.

Quando si usa il secondo form, è possibile specificare solo i campi recuperabili nell'elenco.

Se si elenca un campo complesso senza specificare i campi secondari in modo esplicito, tutti i campi secondari recuperabili verranno inclusi nel set di risultati della query. Si supponga, ad esempio, che l' `Address` indice includa `City`un campo `Country` con `Street`i campi, e che possono essere recuperati. Se si specifica `Address` in **$Select**, i risultati della query includeranno tutti e tre i campi secondari.

## <a name="examples"></a>Esempi

Includere i `HotelId`campi `HotelName`di primo `Rating` livello, e nei risultati, nonché il `City` campo secondario di `Address`:

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

Includere il `HotelName` campo di primo livello nei risultati, nonché tutti i sottocampi di `Address`e i `Type` sottocampi e `BaseRate` di ogni oggetto `Rooms` della raccolta:

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

- [Come usare i risultati della ricerca in ricerca di Azure](search-pagination-page-layout.md)
- [Panoramica del linguaggio di espressioni OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Guida di riferimento alla sintassi delle espressioni OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
