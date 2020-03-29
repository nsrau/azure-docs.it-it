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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845996"
---
I limiti seguenti si applicano agli argomenti di sistema di Griglia di eventi di Azure e agli argomenti personalizzati, *non ai* domini di evento.

| Risorsa | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato (ingresso) | 5.000 eventi al secondo per argomento |
| Richieste di pubblicazione | 250 al secondo |
| Dimensioni evento | 1 MB (caricato in come più eventi da 64 KB) |

I limiti seguenti si applicano solo ai domini di evento.

| Risorsa | Limite |
| --- | --- |
| Argomenti per dominio di eventi | 100,000 |
| Sottoscrizioni di eventi per argomento all'interno di un dominioEvent subscriptions per topic within a domain | 500 |
| Sottoscrizioni di eventi con ambito di dominio | 50 |
| Frequenza di pubblicazione per un dominio evento (ingresso) | 5.000 eventi al secondo |
| Richieste di pubblicazione | 250 al secondo |
| Domini di eventi per sottoscrizione di AzureEvent Domains per Azure Subscription | 100 |