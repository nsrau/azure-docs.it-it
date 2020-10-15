---
title: File di inclusione
description: File di inclusione
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b884bc72381c98af77f2f49336f3dd5762c68734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859660"
---
I limiti seguenti si applicano agli **argomenti** (di sistema, personalizzati e dei partner) di Griglia di eventi di Azure. 

| Risorsa | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato o di partner (in ingresso) | 5\.000 eventi/sec o 1 MB/sec (a seconda di quale limite viene raggiunto per primo) |
| Dimensioni degli eventi | 1 MB  |
| Connessioni a endpoint privato per argomento  | 64 | 
| Regole del firewall IP per argomento | 16 | 

I limiti seguenti si applicano ai **domini** di Griglia di eventi. 

| Risorsa | Limite |
| --- | --- |
| Argomenti per dominio eventi | 100,000 |
| Sottoscrizioni di eventi per argomento all'interno di un dominio | 500 |
| Sottoscrizioni di eventi per ambito di dominio | 50 |
| Frequenza di pubblicazione per un dominio eventi (in ingresso) | 5\.000 eventi/sec o 1 MB/sec (a seconda di quale limite viene raggiunto per primo) |
| Domini eventi per sottoscrizione di Azure | 100 |
| Connessioni a endpoint privato per dominio | 64 | 
| Regole del firewall IP per dominio | 16 | 


