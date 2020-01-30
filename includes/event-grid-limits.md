---
title: File di inclusione
description: File di inclusione
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845996"
---
I limiti seguenti si applicano agli argomenti del sistema di griglia di eventi di Azure e agli argomenti personalizzati, *non* ai domini di eventi.

| Gruppi | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato (ingresso) | 5\.000 eventi al secondo per argomento |
| Pubblica richieste | 250 al secondo |
| Dimensioni evento | 1 MB (addebitato come più eventi 64-KB) |

I limiti seguenti si applicano solo ai domini di evento.

| Gruppi | Limite |
| --- | --- |
| Argomenti per dominio evento | 100.000 |
| Sottoscrizioni di eventi per argomento all'interno di un dominio | 500 |
| Sottoscrizioni di eventi ambito dominio | 50 |
| Frequenza di pubblicazione per un dominio di evento (in ingresso) | 5\.000 eventi al secondo |
| Pubblica richieste | 250 al secondo |
| Domini di eventi per ogni sottoscrizione di Azure | 100 |