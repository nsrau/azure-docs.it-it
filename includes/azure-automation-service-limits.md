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
ms.openlocfilehash: 2823a33b25812a69ad463433bacd9710655c9176
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67180554"
---
#### <a name="process-automation"></a>Automazione dei processi

| Risorsa | Limite massimo |Note|
| --- | --- |---|
| Numero massimo di nuovi processi che possono essere inviati ogni 30 secondi per ogni account di automazione di Azure (processi non pianificati) |100 |Quando viene raggiunto questo limite, le successive richieste di creazione di un processo hanno esito negativo. Il client riceve una risposta di errore.|
| Numero massimo di processi in esecuzione simultanei nella stessa istanza di tempo per account di automazione (processi non pianificati) |200 |Quando viene raggiunto questo limite, le successive richieste di creazione di un processo hanno esito negativo. Il client riceve una risposta di errore.|
| Dimensioni massime di archiviazione dei metadati del processo per un periodo in sequenza di 30 giorni | 10 GB (circa 4 milioni processi)|Quando viene raggiunto questo limite, le successive richieste di creazione di un processo hanno esito negativo. |
| Limite massimo di flussi di processi|1 MB|Un singolo flusso non può essere maggiore di 1 MB.|
| Numero massimo di moduli che possono essere importati ogni 30 secondi per account di automazione |5 ||
| Dimensioni massime di un modulo |100 MB ||
| Tempo di esecuzione processo, livello gratuito |500 minuti per sottoscrizione al mese di calendario ||
| Quantità massima di spazio su disco consentita per sandbox<sup>1</sup> |1 GB |Si applica solo alle sandbox di Azure.|
| Quantità massima di memoria assegnata a un sandbox<sup>1</sup> |400 MB |Si applica solo alle sandbox di Azure.|
| Numero massimo di socket di rete consentiti per sandbox<sup>1</sup> |1\.000 |Si applica solo alle sandbox di Azure.|
| Massimo Runtime consentito per Runbook<sup>1</sup> |3 ore |Si applica solo alle sandbox di Azure.|
| Numero massimo di account di automazione in una sottoscrizione |Nessun limite ||
| Numero massimo di gruppi di lavoro ibridi per account di automazione|4\.000||
|Numero massimo di processi simultanei che possono essere eseguiti in un singolo ruolo di lavoro ibrido per Runbook|50 ||
| Dimensioni massime parametro processo Runbook   | 512 kilobit||
| Numero massimo di parametri Runbook   | 50|Se si raggiunge il limite di 50 parametri, è possibile passare una stringa JSON o XML a un parametro e analizzarla con Runbook.|
| Dimensioni massime del payload del webhook |  512 kilobit|
| Numero massimo di giorni per cui vengono conservati i dati del processo|30 giorni|
| Dimensioni massime stato del flusso di lavoro PowerShell |5 MB| Si applica ai manuali operativi del flusso di lavoro PowerShell durante il checkpoint del flusso di lavoro.|

<sup>1</sup> Un sandbox è un ambiente condiviso che può essere usato da più processi. I processi che usano la stessa sandbox sono limitati dalle limitazioni delle risorse della sandbox.

#### <a name="change-tracking-and-inventory"></a>Rilevamento modifiche e inventario

La tabella seguente illustra i limiti degli elementi rilevati per computer per il rilevamento delle modifiche.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|File|500||
|Registro|250||
|Software Windows|250|Non include gli aggiornamenti software.|
|Pacchetti Linux|1\.250||
|Servizi|250||
|Daemon|250||

#### <a name="update-management"></a>Gestione degli aggiornamenti

La tabella seguente illustra i limiti per Gestione aggiornamenti.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|Numero di computer per ogni distribuzione di aggiornamenti|1000||