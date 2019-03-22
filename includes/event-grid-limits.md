---
title: File di inclusione
description: File di inclusione
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 443db1b4609e62fb7c57de417e42a2b4d0737ada
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553624"
---
I limiti seguenti si applicano agli argomenti di sistema griglia di eventi di Azure e gli argomenti personalizzati, *non* domini di eventi.

| Risorsa | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato (ingresso) | 5.000 eventi al secondo per argomento |

I limiti seguenti si applicano a solo i domini di eventi.

| Risorsa | Limite |
| --- | --- |
| Argomenti per ogni dominio di evento | 1000 durante la fase di anteprima pubblica |
| Sottoscrizioni di eventi per ogni argomento all'interno di un dominio | 50 durante la fase di anteprima pubblica |
| Sottoscrizioni di eventi di ambito di dominio | 50 durante la fase di anteprima pubblica |
| Pubblicare velocità per un dominio di eventi (traffico in ingresso) | 5000 eventi al secondo durante la fase di anteprima pubblica |