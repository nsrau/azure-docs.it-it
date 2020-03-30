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
ms.openlocfilehash: d700dfcf5a7b6e9ada2a755335689ffa571e4c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334914"
---
#### <a name="process-automation"></a>Automazione dei processi

| Risorsa | Limite |Note|
| --- | --- |---|
| Numero massimo di nuovi processi che possono essere inviati ogni 30 secondi per ogni account di Automazione di Azure (processi non pianificati)Maximum number of new jobs that can be submitted every 30 seconds per Azure Automation account (nonscheduled jobs) |100 |Quando viene raggiunto questo limite, le richieste successive di creazione di un processo hanno esito negativo. Il client riceve una risposta di errore.|
| Numero massimo di processi in esecuzione simultanei nella stessa istanza di tempo per account di automazione (processi non pianificati) |200 |Quando viene raggiunto questo limite, le richieste successive di creazione di un processo hanno esito negativo. Il client riceve una risposta di errore.|
| Dimensione massima di archiviazione dei metadati del processo per un periodo di rotazione di 30 giorniMaximum storage size of job metadata for a 30-day rolling period | 10 GB (circa 4 milioni di posti di lavoro)|Quando viene raggiunto questo limite, le richieste successive di creazione di un processo hanno esito negativo. |
| Limite massimo del flusso di lavoro|1 MB|Un singolo flusso non può essere maggiore di 1 MB.|
| Numero massimo di moduli che possono essere importati ogni 30 secondi per account Automation |5 ||
| Dimensione massima di un modulo |100 MB ||
| Tempo di esecuzione del processo, livello gratuito |500 minuti per sottoscrizione al mese di calendario ||
| Quantità massima di spazio su disco consentita per sandbox<sup>1</sup> |1 GB |Si applica solo ai Sandbox di Azure.Applies to Azure sandboxes only.|
| Quantità massima di memoria assegnata a una sandbox<sup>1</sup> |400 MB |Si applica solo ai Sandbox di Azure.Applies to Azure sandboxes only.|
| Numero massimo di socket di rete consentiti per sandbox<sup>1</sup> |1.000 |Si applica solo ai Sandbox di Azure.Applies to Azure sandboxes only.|
| Tempo di esecuzione massimo consentito per runbook<sup>1Maximum</sup> runtime allowed per runbook 1 |3 ore |Si applica solo ai Sandbox di Azure.Applies to Azure sandboxes only.|
| Numero massimo di account di automazione in una sottoscrizioneMaximum number of Automation accounts in a subscription |Nessun limite ||
| Numero massimo di gruppi di ruoli di lavoro ibridi per account di automazioneMaximum number of Hybrid Worker Groups per Automation Account|4.000||
|Numero massimo di processi simultanei che possono essere eseguiti in un singolo ruolo di lavoro ibrido RunbookMaximum number of concurrent jobs that can be run on a single Hybrid Runbook Worker|50 ||
| Dimensione massima dei parametri del processo runbook   | 512 kilobit||
| Numero massimo di parametri del runbook   | 50|Se si raggiunge il limite di 50 parametri, è possibile passare una stringa JSON o XML a un parametro e analizzarlo con il runbook.|
| Dimensione massima payload webhook |  512 kilobit|
| Numero massimo di giorni di conservazione dei dati del processo|30 giorni|
| Dimensione massima dello stato del flusso di lavoro di PowerShellMaximum PowerShell workflow state size |5 MB| Si applica ai runbook del flusso di lavoro di PowerShell durante il checkpoint del flusso di lavoro.|

<sup>1 : il</sup> nome del Una sandbox è un ambiente condiviso che può essere utilizzato da più processi. I processi che utilizzano la stessa sandbox sono vincolati dalle limitazioni delle risorse della sandbox.

#### <a name="change-tracking-and-inventory"></a>Rilevamento modifiche e inventario

Nella tabella seguente vengono illustrati i limiti degli articoli rilevati per computer per il rilevamento delle modifiche.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|File|500||
|Registro|250||
|Software Windows|250|Non include gli aggiornamenti software.|
|Pacchetti Linux|1,250||
|Servizi|250||
|Daemon|250||

#### <a name="update-management"></a>Gestione degli aggiornamenti

Nella tabella seguente vengono illustrati i limiti per la gestione degli aggiornamenti.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|Numero di computer per distribuzione di aggiornamento|1000||