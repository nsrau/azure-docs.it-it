---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079639"
---
| Tipo di dati | Funzionalità consentita nelle espressioni lambda con `any` | Funzionalità consentita nelle espressioni lambda con `all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Tutti gli elementi tranne `search.ismatch` e `search.ismatchscoring` | Uguale |
| `Collection(Edm.String)` | I confronti con `eq` o `search.in` <br/><br/> Se si combinano espressioni secondarie con `or` | I confronti con `ne` o `not search.in()` <br/><br/> Se si combinano espressioni secondarie con `and` |
| `Collection(Edm.Boolean)` | I confronti con `eq` o `ne` | Uguale |
| `Collection(Edm.GeographyPoint)` | Usando `geo.distance` con `lt` o `le` <br/><br/> `geo.intersects` <br/><br/> Se si combinano espressioni secondarie con `or` | Usando `geo.distance` con `gt` o `ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Se si combinano espressioni secondarie con `and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | I confronti usando `eq`, `ne`, `lt`, `gt`, `le`, o `ge` <br/><br/> Confronti in combinazione con altre espressioni secondarie tramite `or` <br/><br/> La combinazione di confronti, ad eccezione `ne` con altre espressioni secondarie tramite `and` <br/><br/> Utilizzando combinazioni di espressioni `and` e `or` in [rimandate Normal Form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | I confronti usando `eq`, `ne`, `lt`, `gt`, `le`, o `ge` <br/><br/> Confronti in combinazione con altre espressioni secondarie tramite `and` <br/><br/> La combinazione di confronti, ad eccezione `eq` con altre espressioni secondarie tramite `or` <br/><br/> Utilizzando combinazioni di espressioni `and` e `or` in [congiunzione Normal Form (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
