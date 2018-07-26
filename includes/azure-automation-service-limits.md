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
ms.openlocfilehash: b71e6d41dcdd7efb2d179486f9195c14dae97194
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037654"
---
| Risorsa | Limite massimo |Note|
| --- | --- |---|
| Numero massimo di nuovi processi che possono essere inviati ogni 30 secondi per account di Automazione (processi non pianificati) |100 |Quando si raggiunge questo limite, le richieste successive di creazione di un processo avranno esito negativo. Il client riceve una risposta di errore.|
| Numero massimo di processi in esecuzione simultaneamente alla stessa istanza di tempo per account di Automazione (processi non pianificati) |200 |Quando si raggiunge questo limite, le richieste successive di creazione di un processo avranno esito negativo. Il client riceve una risposta di errore.|
| Numero massimo di moduli che possono essere importati ogni 30 secondi per ogni account di Automazione |5 ||
| Dimensione massima di un modulo |100 MB ||
| Tempo di esecuzione processo - livello gratuito |500 minuti per sottoscrizione al mese di calendario ||
| Quantità massima di spazio su disco consentita per sandbox**<sup>1</sup>** |1 GB |Si applica solo alla sandbox Azure|
| Quantità massima di memoria assegnata a una sandbox **<sup>1</sup>** |400 MB |Si applica solo alla sandbox Azure|
| Numero massimo di socket di rete consentiti per sandbox **<sup>1</sup>** |1000 |Si applica solo alla sandbox Azure|
| Runtime massimo consentito per ogni runbook  **<sup>1</sup>** |3 ore |Si applica solo alla sandbox Azure|
| Numero massimo di account di automazione in una sottoscrizione |Nessun limite ||
|Numero massimo di processi simultanei da eseguire su un singolo ruolo di lavoro ibrido per runbook|50 ||

**<sup>1</sup>** Una sandbox è un ambiente condiviso che può essere usato da più processi. I processi che usano la stessa sandbox sono vincolati dalle limitazioni delle risorse della sandbox.
