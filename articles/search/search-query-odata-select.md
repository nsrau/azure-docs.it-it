---
title: Informazioni di riferimento selezionare OData - ricerca di Azure
description: Riferimenti al linguaggio di OData per selezionare la sintassi nelle query di ricerca di Azure.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 9383ae725fffac55854488ffbc6aeb161ae7e0c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079678"
---
# <a name="odata-select-syntax-in-azure-search"></a>Sintassi di OData $select in ricerca di Azure

 È possibile usare la [OData **$select** parametro](query-odata-filter-orderby-syntax.md) per scegliere i campi da includere nei risultati della ricerca da ricerca di Azure. Questo articolo descrive la sintassi del **$select** in modo dettagliato. Per informazioni più generali sull'utilizzo **$select** per presentare i risultati della ricerca, vedere [come usare ricerca di risultati in ricerca di Azure](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintassi

Il **$select** parametro determina quali campi per ogni documento vengono restituiti nel set di risultati della query. Il seguente EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definisce la grammatica per i **$select** parametro:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

È disponibile anche un diagramma della sintassi interattiva:

> [!div class="nextstepaction"]
> [Diagramma della sintassi OData per ricerca di Azure](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Visualizzare [riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md) per il completamento EBNF.

Il **$select** parametro è disponibile in due forme:

1. Un singolo asterisco (`*`), che indica che devono essere restituiti tutti i campi recuperabili, o
1. Un elenco delimitato da virgole di percorsi di campo, che identifica i campi deve essere restituito.

Quando si usa il secondo form, è possibile specificare solo i campi recuperabili nell'elenco.

Se si elenca un campo complesso senza i relativi campi secondari in modo esplicito, tutti i campi secondari possono essere recuperati verranno incluso nel set di risultati della query. Si supponga ad esempio l'indice includa un `Address` campo `Street`, `City`, e `Country` campi secondari che tutti possono essere recuperati. Se si specifica `Address` nelle **$select**, i risultati della query includerà tutti i tre campi secondari.

## <a name="examples"></a>Esempi

Includere il `HotelId`, `HotelName`, e `Rating` campi di livello superiore nei risultati, così come il `City` il sottocampo del `Address`:

    $select=HotelId, HotelName, Rating, Address/City

Un risultato dell'esempio potrebbe essere simile al seguente:

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

Includere il `HotelName` campo principale nei risultati, nonché tutti i campi secondari di `Address`e il `Type` e `BaseRate` campi secondari di ciascun oggetto nel `Rooms` raccolta:

    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate

Un risultato dell'esempio potrebbe essere simile al seguente:

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

- [Come usare ricerca di risultati in ricerca di Azure](search-pagination-page-layout.md)
- [Panoramica del linguaggio di espressione OData per ricerca di Azure](query-odata-filter-orderby-syntax.md)
- [Riferimento alla sintassi di espressione di OData per ricerca di Azure](search-query-odata-syntax-reference.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Cercare documenti - API REST per il servizio Ricerca di Azure)
