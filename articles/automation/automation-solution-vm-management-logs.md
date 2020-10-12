---
title: Eseguire query sui log da Avvio/Arresto di macchine virtuali durante gli orari di minore attività
description: Questo articolo descrive come usare Monitoraggio di Azure per eseguire query sui dati dei log generati da Avvio/Arresto di macchine virtuali durante gli orari di minore attività.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: de013b6ccd924f50ffe12fcba1285b121eece5f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83827557"
---
# <a name="query-logs-from-startstop-vms-during-off-hours"></a>Eseguire query sui log da Avvio/Arresto di macchine virtuali durante gli orari di minore attività

Automazione di Azure invia due tipi di record all'area di lavoro Log Analytics collegata: log di processo e flussi di processo. Questo articolo esamina i dati disponibili per [query](../azure-monitor/log-query/log-query-overview.md) in Monitoraggio di Azure.

## <a name="job-logs"></a>Log di processo

|Proprietà | Descrizione|
|----------|----------|
|Chiamante |  Chi ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati.|
|Category | La classificazione del tipo di dati. Per Automazione, il valore è JobLogs.|
|CorrelationId | GUID che rappresenta l'ID di correlazione del processo del runbook.|
|JobId | GUID che rappresenta l'ID del processo del runbook.|
|operationName | Specifica il tipo di operazione eseguita in Azure. Per Automazione, il valore è Job.|
|resourceId | Specifica il tipo di risorsa in Azure. Per Automazione, il valore è l'account di Automazione associato al runbook.|
|ResourceGroup | Specifica il nome del gruppo di risorse del processo del runbook.|
|ResourceProvider | Specifica il servizio di Azure che fornisce le risorse da distribuire e gestire. Per Automazione, il valore è Automazione di Azure.|
|ResourceType | Specifica il tipo di risorsa in Azure. Per Automazione, il valore è l'account di Automazione associato al runbook.|
|resultType | Lo stato del processo di runbook. I valori possibili sono:<br>- Avviato<br>- Interrotto<br>- Sospeso<br>- Non riuscito<br>- Completato|
|resultDescription | Descrive lo stato del risultato del processo di runbook. I valori possibili sono:<br>- Processo avviato<br>- Processo non riuscito<br>- Processo completato|
|RunbookName | Specifica il nome del runbook.|
|SourceSystem | Specifica il sistema di origine per i dati inviati. Per Automazione, il valore è OpsManager|
|StreamType | Specifica il tipo di evento. I valori possibili sono:<br>- Dettagliato<br>- Output<br>- Errore<br>- Avviso|
|SubscriptionId | Specifica l'ID sottoscrizione del processo.
|Tempo | Data e ora di esecuzione del processo del runbook.|

## <a name="job-streams"></a>Flussi di processo

|Proprietà | Descrizione|
|----------|----------|
|Chiamante |  Chi ha avviato l'operazione. I valori possibili sono un indirizzo di posta elettronica o il sistema per i processi pianificati.|
|Category | La classificazione del tipo di dati. Per Automazione, il valore è JobStreams.|
|JobId | GUID che rappresenta l'ID del processo del runbook.|
|operationName | Specifica il tipo di operazione eseguita in Azure. Per Automazione, il valore è Job.|
|ResourceGroup | Specifica il nome del gruppo di risorse del processo del runbook.|
|resourceId | Specifica l'ID risorsa in Azure. Per Automazione, il valore è l'account di Automazione associato al runbook.|
|ResourceProvider | Specifica il servizio di Azure che fornisce le risorse da distribuire e gestire. Per Automazione, il valore è Automazione di Azure.|
|ResourceType | Specifica il tipo di risorsa in Azure. Per Automazione, il valore è l'account di Automazione associato al runbook.|
|resultType | Risultato del processo del runbook al momento in cui è stato generato l'evento. Un valore possibile è:<br>- InProgress|
|resultDescription | Include il flusso di output dal runbook.|
|RunbookName | Il nome del runbook.|
|SourceSystem | Specifica il sistema di origine per i dati inviati. Per Automazione, il valore è OpsManager.|
|StreamType | Il tipo di flusso del processo. I valori possibili sono:<br>- Avanzamento<br>- Output<br>- Avviso<br>- Errore<br>- Debug<br>- Dettagliato|
|Tempo | Data e ora di esecuzione del processo del runbook.|

Quando si esegue una ricerca log che restituisce record di categoria di **JobLogs** o **JobStreams**, è possibile selezionare la visualizzazione **JobLogs** o **JobStreams** che presenta una serie di riquadri di riepilogo degli aggiornamenti restituiti dalla ricerca.

## <a name="sample-log-searches"></a>Ricerche di log di esempio

La tabella seguente contiene esempi di ricerche nei log per i record dei processi raccolti da Avvio/Arresto di macchine virtuali durante gli orari di minore attività.

|Query | Descrizione|
|----------|----------|
|Trova i processi completati per il runbook ScheduledStartStop_Parent | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Trova i processi non completati per il runbook ScheduledStartStop_Parent | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Trova i processi completati per il runbook SequencedStartStop_Parent | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Trova i processi non completati per il runbook SequencedStartStop_Parent | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Passaggi successivi

* Per configurare la funzionalità, vedere [Configurare Avvio/Arresto di macchine virtuali durante gli orari di minore attività](automation-solution-vm-management-config.md).
* Per informazioni sugli avvisi dei log durante la distribuzione della funzionalità, vedere [Creare avvisi di log con Monitoraggio di Azure](../azure-monitor/platform/alerts-log.md).
* Per risolvere gli errori della funzionalità, vedere [Risolvere i problemi relativi ad Avvio/Arresto di macchine virtuali durante gli orari di minore attività](troubleshoot/start-stop-vm.md).