---
title: includere il file
description: includere file
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 06/29/2020
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: aa7b997fe112be5d974d0e82597e786fa219e1f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85570191"
---
#### <a name="process-automation"></a>Automazione dei processi

| Risorsa | Limite |Note|
| --- | --- |---|
| Numero massimo di nuovi processi che possono essere inviati ogni 30 secondi per ogni account di automazione di Azure (processi non pianificati) |100 |Quando viene raggiunto questo limite, le successive richieste di creazione di un processo hanno esito negativo. Il client riceve una risposta di errore.|
| Numero massimo di processi in esecuzione simultanei nella stessa istanza di tempo per account di automazione (processi non pianificati) |200 |Quando viene raggiunto questo limite, le successive richieste di creazione di un processo hanno esito negativo. Il client riceve una risposta di errore.|
| Dimensioni massime di archiviazione dei metadati del processo per un periodo in sequenza di 30 giorni | 10 GB (circa 4 milioni processi)|Quando viene raggiunto questo limite, le successive richieste di creazione di un processo hanno esito negativo. |
| Limite massimo di flussi di processi|1 MiB|Un singolo flusso non può essere maggiore di 1 MB.|
| Numero massimo di moduli che possono essere importati ogni 30 secondi per account di automazione |5 ||
| Dimensioni massime di un modulo |100 MB ||
| Tempo di esecuzione processo, livello gratuito |500 minuti per sottoscrizione al mese di calendario ||
| Quantità massima di spazio su disco consentita per sandbox<sup>1</sup> |1 GB |Si applica solo alle sandbox di Azure.|
| Quantità massima di memoria assegnata a un sandbox<sup>1</sup> |400 MB |Si applica solo alle sandbox di Azure.|
| Numero massimo di socket di rete consentiti per sandbox<sup>1</sup> |1\.000 |Si applica solo alle sandbox di Azure.|
| Massimo Runtime consentito per Runbook<sup>1</sup> |3 ore |Si applica solo alle sandbox di Azure.|
| Numero massimo di account di automazione in una sottoscrizione |Nessun limite ||
| Numero massimo di gruppi di lavoro ibridi per account di automazione|4.000||
|Numero massimo di processi simultanei che possono essere eseguiti in un singolo ruolo di lavoro ibrido per Runbook|50 ||
| Dimensioni massime parametro processo Runbook   | 512 kilobyte||
| Numero massimo di parametri Runbook   | 50|Se si raggiunge il limite di 50 parametri, è possibile passare una stringa JSON o XML a un parametro e analizzarla con Runbook.|
| Dimensioni massime del payload del webhook |  512 kilobyte|
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
|Pacchetti Linux|1.250||
|Servizi|250||
|Daemon|250||

#### <a name="update-management"></a>Gestione degli aggiornamenti

La tabella seguente illustra i limiti per Gestione aggiornamenti.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|Numero di computer per ogni distribuzione di aggiornamenti|1000||