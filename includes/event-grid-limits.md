---
title: file di inclusione
description: file di inclusione
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 890095718833c90a6764d0799d2081b393b1d0bd
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887790"
---
I limiti seguenti si applicano agli argomenti del sistema di griglia di eventi di Azure e agli argomenti personalizzati, *non* ai domini di eventi.

| Gruppi | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato (ingresso) | 5\.000 eventi al secondo per argomento |
| Pubblica richieste | 250 al secondo |
| Dimensioni evento | Supporto per 64 KB nella versione di disponibilità generale (GA). Il supporto per 1 MB è attualmente in versione di anteprima. |

I limiti seguenti si applicano solo ai domini di evento.

| Gruppi | Limite |
| --- | --- |
| Argomenti per dominio evento | 100,000 |
| Sottoscrizioni di eventi per argomento all'interno di un dominio | 500 |
| Sottoscrizioni di eventi ambito dominio | 50 |
| Frequenza di pubblicazione per un dominio di evento (in ingresso) | 5\.000 eventi al secondo |
| Pubblica richieste | 250 al secondo |
| Domini di eventi per ogni sottoscrizione di Azure | 100 |