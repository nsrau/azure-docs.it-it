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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66238151"
---
#### <a name="process-automation"></a>Automazione dei processi

| Resource | Limite massimo |Note|
| --- | --- |---|
| Numero massimo di nuovi processi che possono essere inviati ogni 30 secondi per ogni account di automazione di Azure (nonscheduled processi) |100 |Quando viene raggiunto questo limite, le richieste successive per creare un processo esito negativo. Il client riceve una risposta di errore.|
| Numero massimo di processi in esecuzione simultaneamente alla stessa istanza di tempo per ogni account di automazione (processi nonscheduled) |200 |Quando viene raggiunto questo limite, le richieste successive per creare un processo esito negativo. Il client riceve una risposta di errore.|
| Dimensioni massime di archiviazione di metadati del processo per un periodo di 30 giorni in sequenza | 10 GB (circa 4 milioni di processi)|Quando viene raggiunto questo limite, le richieste successive per creare un processo esito negativo. |
| Limite di flusso del processo massima|1 MB|Un singolo flusso non può essere maggiore di 1 MB.|
| Numero massimo di moduli che possono essere importati ogni 30 secondi per ogni account di automazione |5 ||
| Dimensioni massime di un modulo |100 MB ||
| Tempo, livello gratuito di esecuzione processo |500 minuti per sottoscrizione al mese di calendario ||
| Quantità massima di spazio su disco consentito per ogni ambiente sandbox<sup>1</sup> |1 GB |Si applica a solo sandbox di Azure.|
| Quantità massima di memoria assegnata a una sandbox<sup>1</sup> |400 MB |Si applica a solo sandbox di Azure.|
| Numero massimo di socket di rete consentiti per sandbox<sup>1</sup> |1\.000 |Si applica a solo sandbox di Azure.|
| Tempo di esecuzione massimo consentito per ogni runbook<sup>1</sup> |3 ore |Si applica a solo sandbox di Azure.|
| Numero massimo di account di automazione in una sottoscrizione |Nessun limite ||
| Numero massimo di gruppi di lavoro ibridi per ogni Account di automazione|4\.000||
|Numero massimo di processi simultanei che possono essere eseguiti in Hybrid Runbook Workers singolo|50 ||
| Dimensioni parametro processi runbook massimo   | 512 kilobit||
| Parametri del runbook massimo   | 50|Se si raggiunge il limite di 50 parametri, è possibile passare una stringa JSON o XML per un parametro e analizzarlo con il runbook.|
| Dimensioni del payload massimo webhook |  512 kilobit|
| Numero massimo di giorni di conservazione dei dati di processo|30 giorni|
| Massima dimensione dello stato del flusso di lavoro di PowerShell |5 MB| Si applica ai runbook del flusso di lavoro di PowerShell quando flusso di lavoro di creazione di checkpoint.|

<sup>1</sup>una sandbox è un ambiente condiviso che può essere usato da più processi. I processi che usano la stessa funzione di sandbox opera con le limitazioni di risorse di sandbox.

#### <a name="change-tracking-and-inventory"></a>Rilevamento modifiche e inventario

La tabella seguente illustra i limiti di elemento di rilevamento per ogni macchina per il rilevamento delle modifiche.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|File|500||
|Registro|250||
|Software Windows|250|Non include gli aggiornamenti software.|
|Pacchetti Linux|1,250||
|Servizi|250||
|Daemon|250||

#### <a name="update-management"></a>Gestione degli aggiornamenti

Nella tabella seguente illustra i limiti per la gestione degli aggiornamenti.

| **Risorsa** | **Limite**| **Note** |
|---|---|---|
|Numero di computer per ogni distribuzione degli aggiornamenti|1000||