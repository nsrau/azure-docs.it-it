---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272834"
---
| Tipo di dati | Funzionalità consentite nelle espressioni lambda con`any` | Funzionalità consentite nelle espressioni lambda con`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Tutto tranne `search.ismatch` e`search.ismatchscoring` | Uguale |
| `Collection(Edm.String)` | Confronti `eq` con o`search.in` <br/><br/> Combinazione di sottoespressioni con`or` | Confronti `ne` con o`not search.in()` <br/><br/> Combinazione di sottoespressioni con`and` |
| `Collection(Edm.Boolean)` | Confronti `eq` con o`ne` | Uguale |
| `Collection(Edm.GeographyPoint)` | Utilizzo `geo.distance` di `lt` con o`le` <br/><br/> `geo.intersects` <br/><br/> Combinazione di sottoespressioni con`or` | Utilizzo `geo.distance` di `gt` con o`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Combinazione di sottoespressioni con`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Confronti `eq`con `ne`, `lt`, `gt`, `le`, o`ge` <br/><br/> Combinare i confronti con altre sottoespressioni usando`or` <br/><br/> Combinare i confronti tranne `ne` con altre sottoespressioni usando`and` <br/><br/> Espressioni che usano combinazioni `and` di `or` e in [formato normale disgiuntiva (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Confronti `eq`con `ne`, `lt`, `gt`, `le`, o`ge` <br/><br/> Combinare i confronti con altre sottoespressioni usando`and` <br/><br/> Combinare i confronti tranne `eq` con altre sottoespressioni usando`or` <br/><br/> Espressioni che usano combinazioni `and` di `or` e nel [formato normale congiuntiva (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
