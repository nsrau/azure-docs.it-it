---
title: File di inclusione
description: File di inclusione
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83721231"
---
I limiti seguenti si applicano agli argomenti di sistema di Griglia di eventi di Azure e agli argomenti personalizzati, *non* ai domini eventi.

| Risorsa | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato (ingresso) | 5\.000 eventi al secondo per argomento |
| Dimensioni degli eventi | 1 MB. Tuttavia, le operazioni vengono addebitate in incrementi di 64 kB. Pertanto, gli eventi oltre 64 kB comporteranno addebiti di operazione come se fossero più eventi. Ad esempio, un evento da 130 kB comporterebbe addebiti di operazione come se si trattasse di 3 eventi distinti.  |
| Argomenti per dominio eventi | 100,000 |
| Sottoscrizioni di eventi per argomento all'interno di un dominio | 500 |
| Sottoscrizioni di eventi per ambito di dominio | 50 |
| Frequenza di pubblicazione per un dominio eventi (in ingresso) | 5\.000 eventi al secondo |
| Domini eventi per sottoscrizione di Azure | 100 |
| Connessioni a endpoint privato per argomento o dominio | 64 | 
| Regole del firewall IP per argomento o dominio | 16 | 