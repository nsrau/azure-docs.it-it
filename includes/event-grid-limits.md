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
ms.openlocfilehash: e084256d9c2043d4382ca180ef3178175b301367
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745421"
---
I limiti seguenti si applicano agli **argomenti** (di sistema, personalizzati e dei partner) di Griglia di eventi di Azure. 

| Risorsa | Limite |
| --- | --- |
| Argomenti personalizzati per sottoscrizione di Azure | 100 |
| Sottoscrizioni di eventi per argomento | 500 |
| Velocità di pubblicazione per un argomento personalizzato o di partner (in ingresso) | 5\.000 eventi/sec o 1 MB/sec (a seconda di quale limite viene raggiunto per primo)<br/>Non si applica agli argomenti di sistema. |
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


