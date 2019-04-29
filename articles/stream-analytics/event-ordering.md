---
title: Configurazione evento criteri di ordinamento per Azure Stream Analitica
description: Questo articolo descrive come configurare anche le impostazioni di ordinamento in Stream Analitica
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 970eeb871775e24abb87c8b977e214645e514d3b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789485"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Configurazione evento criteri di ordinamento per Azure Stream Analitica

Questo articolo descrive come configurare e usare l'arrivo in ritardo e i criteri non ordinate di eventi in Azure Stream Analitica. Questi criteri vengono applicati solo quando si usa la [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) clausola della query.

## <a name="event-time-and-arrival-time"></a>Ora dell'evento e l'ora di arrivo

Il processo di Stream Analitica può elaborare gli eventi in base a uno *ora evento* oppure *ora di arrivo*. **Tempo evento/applicazione** sia il timestamp presente nel payload dell'evento (quando è stato generato l'evento). **Ora di arrivo** sia il timestamp quando è stato ricevuto l'evento nell'origine di input (archiviazione dell'Hub IoT hub/eventi/Blob). 

Per impostazione predefinita, Stream Analitica elabora gli eventi dal *ora di arrivo*, ma è possibile scegliere di elaborare gli eventi dal *ora evento* usando [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) clausola della query. Criteri in ritardo di arrivo e di non in ordine sono applicabili solo se si elaborano gli eventi dall'ora dell'evento. Prendere in considerazione i requisiti di latenza e correttezza per lo scenario durante la configurazione di queste impostazioni. 

## <a name="what-is-late-arrival-policy"></a>Quali sono i criteri di arrivo in ritardo?

In alcuni casi gli eventi pervengono in ritardo a causa di vari motivi. Ad esempio, un evento in arrivo in ritardo 40 secondi avrà ora evento = ora di arrivo e 10: 00:00 = 00:10:40. Se i criteri di arrivo in ritardo è impostato su 15 secondi, di eventi che arrivano entro 15 secondi verranno entrambi eliminati (non elaborato da Stream Analitica) o è importantissime eventi modificati. Nell'esempio precedente, come l'evento è giunto a 40 secondi in ritardo (più di set di criteri), l'ora dell'evento verrà modificato fino al massimo di ritardo arrivo criteri 10: 00:25 (ora di arrivo - valore dei criteri di arrivo in ritardo). Criterio di arrivo in ritardo predefinito è 5 secondi.

## <a name="what-is-out-of-order-policy"></a>Quali sono i criteri di non in ordine? 

Anche l'evento potrà arrivare non in ordine. Dopo l'ora dell'evento viene regolato in base a criteri di arrivo in ritardo, è possibile scegliere anche automaticamente eliminare o modificare gli eventi che sono in ordine. Se si imposta questo criterio su 8 secondi, tutti gli eventi che non arrivano in ordine ma entro la finestra di 8 secondi vengono riordinati dall'ora dell'evento. Gli eventi che arrivano in un secondo momento verranno essere eliminati o modificati per il valore massimo dei criteri di non in ordine. Criteri di non in ordine predefinito sono 0 secondi. 

## <a name="adjust-or-drop-events"></a>Modificare o eliminare eventi

Se gli eventi pervenuti in ritardo o di ordinati in base ai criteri che è stato configurato, è possibile eliminare tali eventi (non elaborati da Stream Analitica) o avere tempo evento regolato.

Possiamo vedere un esempio di questi criteri in azione.
<br> **Criteri di arrivo in ritardo:** 5 secondi
<br> **Criteri di non in ordine:** 8 secondi

| N. evento | Ora dell'evento | Ora di arrivo | System.Timestamp | Spiegazione |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | Evento è giunto a livello di tolleranza di ritardo e all'esterno. Ora dell'evento Ottiene regolata in modo alla tolleranza per arrivo in ritardo massimo.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | Eventi arrivati in ritardo, ma all'interno di livello di tolleranza. Quindi, ora dell'evento non ottenere regolato.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | Evento è giunto sul tempo. Regolazione non necessita.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Eventi arrivati in ordine ma entro la tolleranza di 8 secondi. Quindi, ora dell'evento non ottenere regolato. Per motivi di analitica, questo evento viene considerato come numero evento 4 precedente.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Evento è giunto a tolleranza di non in ordine e all'esterno di 8 secondi. Quindi, ora dell'evento è allineato al massimo della tolleranza di non in ordine. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Queste impostazioni possono ritardare l'output del processo? 

Sì. Per impostazione predefinita, i criteri di non in ordine sono impostati su zero (00 minuti e 00 secondi). Se si modifica l'impostazione predefinita, nell'output del processo prima viene ritardato dal valore (o versione successiva). 

Se una delle partizioni di input non riceve gli eventi, è necessario prevedere l'output di ritardo per il valore dei criteri di arrivo in ritardo. Per maggiori informazioni, leggere la sezione di errore InputPartition riportato di seguito. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Vengono visualizzati alcuni messaggi LateInputEvents nella mio log attività

Questi messaggi vengono visualizzati per informare l'utente che gli eventi sono arrivati in ritardo e vengono eliminati o modificati in base alla configurazione. Se sono stati configurati criteri di arrivo in ritardo, in modo appropriato, è possibile ignorare questi messaggi. 

Esempio di questo messaggio è: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Vedere InputPartitionNotProgressing nella mio log attività

L'origine di input (Hub IoT Hub/eventi) probabilmente ha più partizioni. Azure Stream Analitica produce output per l'indicatore di data e ora t1 solo dopo che tutte le partizioni che vengono combinate sono almeno all'ora t1. Ad esempio, si supponga che la query legga da una partizione dell'hub eventi che ha due partizioni. Una delle partizioni, P1, ha eventi fino all'ora t1. L'altra partizione, P2, ha eventi fino all'ora t1 + x. Output viene quindi generato fino all'ora t1. Ma se è presente una partizione esplicita dalla clausola PartitionId, entrambe le partizioni avanzano in modo indipendente. 

Quando più partizioni dello stesso flusso di input vengono combinate, la tolleranza per arrivo in ritardo è la quantità massima di tempo in cui ogni partizione resta in attesa di nuovi dati. Se non esiste un'unica partizione nell'Hub eventi oppure se l'IoT Hub non riceve gli input, la sequenza temporale per la partizione non avanzamento fino a raggiunge la soglia di tolleranza di arrivo in ritardo. Consente di ritardare l'output per la soglia di tolleranza di arrivo in ritardo. In questi casi, si può vedere il messaggio seguente:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Questo messaggio per informare l'utente che per almeno una partizione di input è vuota e verrà ritardare l'output per la soglia di arrivo in ritardo. Per ovviare al problema, è consigliabile entrambi: 1. Assicurarsi che tutte le partizioni dell'Hub eventi dell'Hub IoT/ricevano l'input. 2. Usa Partition by PartitionID clausola della query. 

## <a name="next-steps"></a>Passaggi successivi
* [Considerazioni sulla gestione di tempo](stream-analytics-time-handling.md)
* [Metriche disponibili in Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
