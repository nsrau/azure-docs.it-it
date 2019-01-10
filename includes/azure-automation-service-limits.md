---
title: File di inclusione
description: File di inclusione
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: f3ae2289112948dea7d2649c4fad6b1cafb3804b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444197"
---
#### <a name="process-automation"></a>Automazione dei processi

| Risorsa | Limite massimo |Note|
| --- | --- |---|
| Numero massimo di nuovi processi che possono essere inviati ogni 30 secondi per account di Automazione (processi non pianificati) |100 |Quando si raggiunge questo limite, le richieste successive di creazione di un processo avranno esito negativo. Il client riceve una risposta di errore.|
| Numero massimo di processi in esecuzione simultaneamente alla stessa istanza di tempo per account di Automazione (processi non pianificati) |200 |Quando si raggiunge questo limite, le richieste successive di creazione di un processo avranno esito negativo. Il client riceve una risposta di errore.|
| Dimensione massima di archiviazione dei metadati del processo per un periodo continuo di 30 giorni. | 10 GB (circa 4 milioni di processi)|Quando si raggiunge questo limite, le richieste successive di creazione di un processo avranno esito negativo. |
| Numero massimo di moduli che possono essere importati ogni 30 secondi per ogni account di Automazione |5 ||
| Dimensione massima di un modulo |100 MB ||
| Tempo di esecuzione processo - livello gratuito |500 minuti per sottoscrizione al mese di calendario ||
| Quantità massima di spazio su disco consentita per sandbox **<sup>1</sup>** |1 GB |Si applica solo alla sandbox Azure|
| Quantità massima di memoria assegnata a una sandbox **<sup>1</sup>** |400 MB |Si applica solo alla sandbox Azure|
| Numero massimo di socket di rete consentiti per sandbox **<sup>1</sup>** |1000 |Si applica solo alla sandbox Azure|
| Runtime massimo consentito per ogni runbook  **<sup>1</sup>** |3 ore |Si applica solo alla sandbox Azure|
| Numero massimo di account di automazione in una sottoscrizione |Nessun limite ||
|Numero massimo di processi simultanei da eseguire su un singolo ruolo di lavoro ibrido per runbook|50 ||
| Dimensione massima dei parametri del processo di runbook   | 512 kb||
| Numero massimo di parametri di runbook   | 50|È possibile passare una stringa JSON o XML a un parametro e analizzarla con il runbook se si raggiunge il limite di 50 parametri|
| Dimensione massima del payload del webhook |  512 kb|
| Numero massimo di giorni di conservazione dei dati del processo|30 giorni|
| Dimensioni massime dello stato del flusso di lavoro PowerShell |5 MB| Valido per i runbook del flusso di lavoro PowerShell durante l’impostazione dei checkpoint del flusso di lavoro.|

**<sup>1</sup>** Una sandbox è un ambiente condiviso che può essere usato da più processi. I processi che usano la stessa sandbox sono vincolati dalle limitazioni delle risorse della sandbox.

#### <a name="change-tracking-and-inventory"></a>Rilevamento modifiche e inventario

La tabella seguente illustra i limiti dell'elemento di rilevamento per ogni macchina per il rilevamento modifiche.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|File|500||
| Registro|250||
|Software Windows|250|Non include gli aggiornamenti software|
|Pacchetti Linux|1250||
|Services|250||
|daemon|250||
