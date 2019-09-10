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
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2019
ms.locfileid: "66376930"
---
I limiti seguenti si applicano agli argomenti del sistema di griglia di eventi di Azure e agli argomenti personalizzati, *non* ai domini di eventi.

| Risorsa | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato (ingresso) | 5\.000 eventi al secondo per argomento |
| Pubblica richieste | 250 al secondo |
| Dimensioni evento | Supporto per 64 KB nella versione di disponibilità generale (GA). Il supporto per 1 MB è attualmente in versione di anteprima. |

I limiti seguenti si applicano solo ai domini di evento.

| Risorsa | Limite |
| --- | --- |
| Argomenti per dominio evento | 1000 durante la fase di anteprima pubblica |
| Sottoscrizioni di eventi per argomento all'interno di un dominio | 50 durante la fase di anteprima pubblica |
| Sottoscrizioni di eventi ambito dominio | 50 durante la fase di anteprima pubblica |
| Frequenza di pubblicazione per un dominio di evento (in ingresso) | 5000 eventi al secondo durante la fase di anteprima pubblica |
| Pubblica richieste | 250 al secondo |