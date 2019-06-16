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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66376930"
---
I limiti seguenti si applicano agli argomenti di sistema griglia di eventi di Azure e gli argomenti personalizzati, *non* domini di eventi.

| Resource | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato (ingresso) | 5\.000 eventi al secondo per argomento |
| Richieste di pubblicazione | 250 al secondo |
| Dimensione dell'evento | Supporto per 64 KB in generale (GA) di disponibilità. Supporto di 1 MB è attualmente in anteprima. |

I limiti seguenti si applicano a solo i domini di eventi.

| Resource | Limite |
| --- | --- |
| Argomenti per ogni dominio di evento | 1000 durante la fase di anteprima pubblica |
| Sottoscrizioni di eventi per ogni argomento all'interno di un dominio | 50 durante la fase di anteprima pubblica |
| Sottoscrizioni di eventi di ambito di dominio | 50 durante la fase di anteprima pubblica |
| Pubblicare velocità per un dominio di eventi (traffico in ingresso) | 5000 eventi al secondo durante la fase di anteprima pubblica |
| Richieste di pubblicazione | 250 al secondo |