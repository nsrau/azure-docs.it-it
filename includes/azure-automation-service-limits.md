---
title: includere file
description: includere file
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 10/14/2020
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: f01ca03167c7088c344f72e9943c103d9611bbc6
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92082235"
---
#### <a name="process-automation"></a>Automazione dei processi

| Risorsa | Limite |Note|
| --- | --- |---|
| Numero massimo di nuovi processi che possono essere inviati ogni 30 secondi per account di Automazione di Azure (processi non pianificati) |100 |Quando si raggiunge questo limite, le richieste successive di creazione di un processo non riusciranno. Il client riceve una risposta di errore.|
| Numero massimo di processi in esecuzione simultaneamente alla stessa istanza di tempo per account di Automazione (processi non pianificati) |200 |Quando si raggiunge questo limite, le richieste successive di creazione di un processo non riusciranno. Il client riceve una risposta di errore.|
| Dimensione massima di archiviazione dei metadati del processo per un periodo continuo di 30 giorni | 10 GB (circa quattro milioni di processi)|Quando si raggiunge questo limite, le richieste successive di creazione di un processo non riusciranno. |
| Limite massimo del flusso dei processi|1 MiB|Le dimensioni di un singolo flusso non possono superare 1 MB.|
| Numero massimo di moduli che possono essere importati ogni 30 secondi per ogni account di Automazione |5 ||
| Dimensioni massime di un modulo |100 MB ||
| Tempo di esecuzione processo, livello gratuito |500 minuti per sottoscrizione al mese di calendario ||
| Quantità massima di spazio su disco consentita per sandbox<sup>1</sup> |1 GB |Si applica solo alle sandbox di Azure.|
| Quantità massima di memoria assegnata a una sandbox<sup>1</sup> |400 MB |Si applica solo alle sandbox di Azure.|
| Numero massimo di socket di rete consentiti per sandbox<sup>1</sup> |1\.000 |Si applica solo alle sandbox di Azure.|
| Runtime massimo consentito per ogni runbook<sup>1</sup> |3 ore |Si applica solo alle sandbox di Azure.|
| Numero massimo di account di automazione in una sottoscrizione |Nessun limite ||
| Numero massimo di gruppi di ruolo di lavoro ibridi per account di automazione|4.000||
|Numero massimo di processi simultanei eseguibili su un singolo ruolo di lavoro ibrido per runbook|50 ||
| Dimensioni massime dei parametri del processo di runbook   | 512 kilobyte||
| Numero massimo di parametri di runbook   | 50|Se si raggiunge il limite di 50 parametri, è possibile passare una stringa JSON o XML a un parametro e analizzarla con il runbook.|
| Dimensioni massime del payload del webhook |  512 kilobyte|
| Numero massimo di giorni di conservazione dei dati del processo|30 giorni|
| Dimensioni massime dello stato del flusso di lavoro PowerShell |5 MB| Valido per i runbook del flusso di lavoro PowerShell durante l'impostazione dei checkpoint del flusso di lavoro.|

<sup>1</sup>Una sandbox è un ambiente condiviso che può essere usato da più processi. Le limitazioni relative alle risorse della sandbox si applicano anche ai processi che usano la stessa sandbox.

#### <a name="change-tracking-and-inventory"></a>Rilevamento modifiche e inventario

La tabella seguente illustra i limiti dell'elemento di rilevamento per ogni macchina per il rilevamento modifiche.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|File|500||
|Registro|250||
|Software Windows|250|Non include gli aggiornamenti software.|
|Pacchetti Linux|1\.250||
|Servizi|250||
|Daemon|250||

#### <a name="update-management"></a>Gestione degli aggiornamenti

La tabella seguente mostra i limiti per Gestione aggiornamenti.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|Numero di computer per ogni distribuzione di aggiornamenti|500||