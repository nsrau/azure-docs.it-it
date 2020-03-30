---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272834"
---
| Tipo di dati | Funzionalità consentite nelle espressioni lambda con`any` | Funzionalità consentite nelle espressioni lambda con`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Tutto `search.ismatch` tranne e`search.ismatchscoring` | Uguale |
| `Collection(Edm.String)` | Confronti con `eq` o`search.in` <br/><br/> Combinazione di sottoespressioni con`or` | Confronti con `ne` o`not search.in()` <br/><br/> Combinazione di sottoespressioni con`and` |
| `Collection(Edm.Boolean)` | Confronti con `eq` o`ne` | Uguale |
| `Collection(Edm.GeographyPoint)` | Utilizzo `geo.distance` `lt` con o`le` <br/><br/> `geo.intersects` <br/><br/> Combinazione di sottoespressioni con`or` | Utilizzo `geo.distance` `gt` con o`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Combinazione di sottoespressioni con`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Confronti con `eq` `ne`, `lt` `gt`, `le`, , o`ge` <br/><br/> Combinazione di confronti con altre sottoespressioni`or` <br/><br/> Combinazione di `ne` confronti ad eccezione di altre sottoespressioni`and` <br/><br/> Espressioni che utilizzano combinazioni di `and` e `or` in forma normale [disgiuntiva (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Confronti con `eq` `ne`, `lt` `gt`, `le`, , o`ge` <br/><br/> Combinazione di confronti con altre sottoespressioni`and` <br/><br/> Combinazione di `eq` confronti ad eccezione di altre sottoespressioni`or` <br/><br/> Espressioni che utilizzano combinazioni di `and` e `or` in forma normale [congiuntiva (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
