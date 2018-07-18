---
title: File di inclusione
description: File di inclusione
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 6b6e4afa7c8b18c8ce9af8c6abd371b4321e3343
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852042"
---
| Risorsa | Limite massimo |Note|
| --- | --- |---|
| Numero massimo di nuovi processi che possono essere inviati ogni 30 secondi per account di Automazione (processi non pianificati) |100 |Quando si raggiunge questo limite, le richieste successive di creazione di un processo avranno esito negativo. Il client riceve una risposta di errore.|
| Numero massimo di processi in esecuzione simultaneamente alla stessa istanza di tempo per account di Automazione (processi non pianificati) |200 |Quando si raggiunge questo limite, le richieste successive di creazione di un processo avranno esito negativo. Il client riceve una risposta di errore.|
| Numero massimo di moduli che possono essere importati ogni 30 secondi per ogni account di Automazione |5 ||
| Dimensione massima di un modulo |100 MB ||
| Tempo di esecuzione processo - livello gratuito |500 minuti per sottoscrizione al mese di calendario ||
| Quantità massima di spazio su disco consentita per sandbox**<sup>1</sup>** |1 GB |Si applica solo alla sandbox Azure|
| Quantità massima di memoria assegnata a una sandbox**<sup>1</sup>** |400 MB |Si applica solo alla sandbox Azure|
| Numero massimo di socket di rete consentiti per sandbox**<sup>1</sup>** |1000 |Si applica solo alla sandbox Azure|
| Numero massimo di account di automazione in una sottoscrizione |Nessun limite ||
|Numero massimo di processi simultanei da eseguire su un singolo ruolo di lavoro ibrido per runbook|50 ||

**<sup>1</sup>** Una sandbox è un ambiente condiviso che può essere usato da più processi. I processi che usano la stessa sandbox sono vincolati dalle limitazioni delle risorse della sandbox.
