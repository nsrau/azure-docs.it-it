---
title: Configurazione dei criteri di ordinamento degli eventi per Analisi di flusso di AzureConfiguring event ordering policies for Azure Stream Analytics
description: Questo articolo descrive come configurare le impostazioni di ordinamento uniforme in Analisi di flusso
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461186"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Configurazione dei criteri di ordinamento degli eventi per Analisi di flusso di AzureConfiguring event ordering policies for Azure Stream Analytics

Questo articolo descrive come configurare e usare i criteri di eventi in ritardo e di arrivo in ordine in Analisi di flusso di Azure.This article describes how to setup and use late arrival and out-of-order event policies in Azure Stream Analytics. Questi criteri vengono applicati solo quando si utilizza la clausola [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) nella query.

## <a name="event-time-and-arrival-time"></a>Ora dell'evento e ora di arrivo

Il processo di Analisi di flusso può elaborare gli eventi in base *all'ora dell'evento* o *all'ora di arrivo.* **L'ora dell'evento/applicazione** è il timestamp presente nel payload dell'evento (quando l'evento è stato generato). **L'ora di arrivo** è il timestamp quando l'evento è stato ricevuto nell'origine di input (archiviazione Hubs/IoT Hub/Blob). 

Per impostazione predefinita, Analisi di flusso elabora gli eventi in base all'ora di *arrivo,* ma è possibile scegliere di elaborare gli eventi in base *all'ora dell'evento* utilizzando la clausola [TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) nella query. I criteri di arrivo in ritardo e non in ordine sono applicabili solo se si elaborano gli eventi in base all'ora dell'evento. Prendere in considerazione i requisiti di latenza e correttezza per lo scenario durante la configurazione di queste impostazioni. 

## <a name="what-is-late-arrival-policy"></a>Che cosa sono i criteri di arrivo in ritardo?

A volte gli eventi arrivano in ritardo per vari motivi. Ad esempio, un evento che arriva con 40 secondi di ritardo avrà l'ora dell'evento: 00:10:00 e l'ora di arrivo è 00:10:40. Se imposti i criteri di arrivo in ritardo su 15 secondi, qualsiasi evento che arriva dopo 15 secondi verrà eliminato (non elaborato da Analisi di flusso) o avrà il tempo dell'evento regolato. Nell'esempio precedente, quando l'evento è arrivato con 40 secondi di ritardo (più del set di criteri), l'ora dell'evento verrà regolata al massimo della politica di arrivo in ritardo 00:10:25 (valore della politica di arrivo - arrivo in ritardo). La politica di arrivo in ritardo predefinita è 5 secondi.

## <a name="what-is-out-of-order-policy"></a>Che cos'è la politica non in ordine? 

Anche l'evento potrebbe arrivare fuori uso. Dopo che l'orario dell'evento viene regolato in base ai criteri di arrivo in ritardo, è anche possibile scegliere di eliminare o regolare automaticamente gli eventi non in ordine. Se si imposta questo criterio su 8 secondi, tutti gli eventi che arrivano non in ordine ma all'interno della finestra di 8 secondi vengono riordinati in base all'ora dell'evento. Gli eventi che arriveranno in un secondo momento verranno eliminati o modificati al valore massimo dei criteri di out-of-order. Il criterio non in ordine predefinito è 0 secondi. 

## <a name="adjust-or-drop-events"></a>Regolare o eliminare gli eventi

Se gli eventi arrivano in ritardo o non in ordine in base ai criteri configurati, puoi eliminare tali eventi (non elaborati da Analisi di flusso) o modificare il tempo degli eventi.

Vediamo un esempio di queste politiche in azione.
<br> **Politica di arrivo tardivo:** 15 secondi
<br> **Politica non in ordine:** 8 secondi

| N. evento | Ora dell'evento | Ora di arrivo | System.Timestamp | Spiegazione |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | L'evento è arrivato in ritardo e al di fuori del livello di tolleranza. Così l'ora dell'evento viene regolata alla tolleranza massima di arrivo in ritardo.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | L'evento è arrivato in ritardo ma entro il livello di tolleranza. Quindi l'ora dell'evento non viene regolata.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | L'evento è arrivato in tempo. Nessuna regolazione necessaria.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | Evento è arrivato fuori ordine, ma entro la tolleranza di 8 secondi. Quindi, l'ora dell'evento non viene regolata. A scopo di analisi, questo evento verrà considerato come numero di evento precedente 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Evento è arrivato fuori ordine e tolleranza esterna di 8 secondi. Pertanto, il tempo dell'evento viene regolato al massimo della tolleranza fuori ordine. |

## <a name="can-these-settings-delay-output-of-my-job"></a>Queste impostazioni possono ritardare l'output del mio lavoro? 

Sì. Per impostazione predefinita, i criteri non in ordine sono impostati su zero (00 minuti e 00 secondi). Se si modifica l'impostazione predefinita, il primo output del processo viene ritardato di questo valore (o superiore). 

Se una delle partizioni degli input non riceve eventi, è necessario prevedere che l'output venga ritardato in base al valore dei criteri di arrivo in ritardo. Per informazioni sui motivi, leggere la sezione relativa all'errore InputPartition riportata di seguito. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>Nel log attività vengono visualizzati messaggi LateInputEvents

Questi messaggi vengono visualizzati per informare l'utente che gli eventi sono arrivati in ritardo e vengono eliminati o modificati in base alla configurazione. È possibile ignorare questi messaggi se i criteri di arrivo in ritardo sono stati configurati in modo appropriato. 

L'esempio di questo messaggio è: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Nel log attività viene visualizzato InputPartitionNotProgressing

L'origine di input (Hub evento/Hub IoT) probabilmente ha più partizioni. Analisi di flusso di Azure produce output per il timestamp t1 solo dopo che tutte le partizioni combinate sono almeno al momento t1. Ad esempio, si supponga che la query legga da una partizione dell'hub eventi che ha due partizioni. Una delle partizioni, P1, ha eventi fino all'ora t1. L'altra partizione, P2, ha eventi fino all'ora t1 + x. L'output viene quindi generato fino all'ora t1. Tuttavia, se è presente una clausola esplicita Partition by PartitionId, entrambe le partizioni avanzano in modo indipendente. 

Quando vengono combinate più partizioni dallo stesso flusso di input, la tolleranza di arrivo in ritardo è la quantità massima di tempo che ogni partizione attende nuovi dati. Se è presente una partizione nell'hub eventi o se l'hub IoT non riceve gli input, la sequenza temporale della partizione non avanza fino a quando non raggiunge la soglia di tolleranza di arrivo in ritardo. In questo modo l'output viene ritardato in base alla soglia di tolleranza di arrivo in ritardo. In questi casi, è possibile che venga visualizzato il seguente messaggio:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Questo messaggio per informare che almeno una partizione nell'input è vuota e ritarderà l'output entro la soglia di arrivo in ritardo. Per ovviare a questo problema, si consiglia di:  
1. Assicurati che tutte le partizioni dell'hub eventi/ioT Hub ricevano l'input. 
2. Usare la clausola Partition by PartitionID nella query. 

## <a name="next-steps"></a>Passaggi successivi
* [Considerazioni sulla gestione del tempo](stream-analytics-time-handling.md)
* [Metriche disponibili in Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
