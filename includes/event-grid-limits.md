---
title: includere il file
description: File di inclusione
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 5992726893b722b0aa46c976a0167793f5ee6bb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131609"
---
I limiti seguenti si applicano agli argomenti del sistema di griglia di eventi di Azure e agli argomenti personalizzati, *non* ai domini di eventi.

| Risorsa | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato (ingresso) | 5.000 eventi al secondo per argomento |
| Pubblica richieste | 250 al secondo |
| Dimensioni evento | 1 MB. Tuttavia, le operazioni vengono addebitate in incrementi di 64 KB. Quindi, gli eventi oltre 64 KB comporteranno addebiti per le operazioni come se fossero più eventi. Ad esempio, un evento 130 KB comporterebbe operazioni come se si trattasse di 3 eventi distinti.  |

I limiti seguenti si applicano solo ai domini di evento.

| Risorsa | Limite |
| --- | --- |
| Argomenti per dominio evento | 100,000 |
| Sottoscrizioni di eventi per argomento all'interno di un dominio | 500 |
| Sottoscrizioni di eventi ambito dominio | 50 |
| Frequenza di pubblicazione per un dominio di evento (in ingresso) | 5.000 eventi al secondo |
| Pubblica richieste | 250 al secondo |
| Domini di eventi per ogni sottoscrizione di Azure | 100 |