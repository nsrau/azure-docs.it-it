---
title: Configurazione dei criteri di ordinamento degli eventi per analisi di flusso di Azure
description: Questo articolo descrive come configurare le impostazioni di ordinamento anche in analisi di flusso
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: b4e34befbf28de2b985ff49ce17a87a25842015e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87901692"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>Configurazione dei criteri di ordinamento degli eventi per analisi di flusso di Azure

Questo articolo descrive come configurare e usare i criteri di arrivo in ritardo e di eventi non ordinati in analisi di flusso di Azure. Questi criteri vengono applicati solo quando si usa la clausola [timestamp by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) nella query e vengono applicati solo per le origini di input cloud.

## <a name="event-time-and-arrival-time"></a>Ora dell'evento e ora di arrivo

Il processo di analisi di flusso può elaborare gli eventi in base all' *ora dell'evento* o all' *ora di arrivo*. **Evento/ora applicazione** è il timestamp presente nel payload dell'evento (quando è stato generato l'evento). L' **ora di arrivo** è il timestamp in cui l'evento è stato ricevuto nell'origine di input (hub eventi/archiviazione BLOB/Hub). 

Per impostazione predefinita, l'analisi di flusso elabora gli eventi in base all' *ora di arrivo*, ma è possibile scegliere di elaborare gli eventi in base all'ora dell' *evento* usando la clausola [timestamp by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics) nella query. I criteri di arrivo in ritardo e non ordinati sono applicabili solo se si elaborano gli eventi in base all'ora dell'evento. Prendere in considerazione i requisiti di latenza e correttezza per lo scenario durante la configurazione di queste impostazioni. 

## <a name="what-is-late-arrival-policy"></a>Che cosa sono i criteri di arrivo in ritardo?

A volte gli eventi arrivano in ritardo a causa di vari motivi. Ad esempio, un evento che raggiunge i 40 secondi in ritardo avrà l'ora dell'evento = 00:10:00 e l'ora di arrivo = 00:10:40. Se si impostano i criteri di arrivo in ritardo su 15 secondi, eventuali eventi che raggiungono più di 15 secondi verranno eliminati (non elaborati da analisi di flusso) o l'ora dell'evento verrà regolata. Nell'esempio precedente, poiché l'evento è arrivato in ritardo di 40 secondi (più di un set di criteri), l'ora dell'evento verrà modificata fino al numero massimo di criteri di arrivo in ritardo 00:10:25 (ora di arrivo-valore dei criteri di arrivo in ritardo). Il criterio di arrivo in ritardo predefinito è 5 secondi.

## <a name="what-is-out-of-order-policy"></a>Che cosa sono i criteri non ordinati? 

L'evento può arrivare anche fuori dall'ordine. Quando l'ora dell'evento viene modificata in base ai criteri di arrivo in ritardo, è anche possibile scegliere di eliminare automaticamente o modificare gli eventi non ordinati. Se si imposta questo criterio su 8 secondi, tutti gli eventi che non arrivano in ordine ma all'interno della finestra di 8 secondi vengono riordinati in base all'ora dell'evento. Gli eventi che arrivano in un secondo momento verranno eliminati o modificati in base al valore massimo dei criteri non ordinati. Il criterio predefinito non ordinato è 0 secondi. 

## <a name="adjust-or-drop-events"></a>Modificare o eliminare eventi

Se gli eventi arrivano in ritardo o non sono ordinati in base ai criteri configurati, è possibile eliminare tali eventi (non elaborati da analisi di flusso) o modificare l'ora dell'evento.

Ecco un esempio di questi criteri in azione.
<br> **Criteri di arrivo in ritardo:** 15 secondi
<br> **Criteri non ordinati:** 8 secondi

| N. evento. | Ora dell'evento | Ora di arrivo | System.Timestamp | Spiegazione |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | L'evento è arrivato in ritardo e all'esterno del livello di tolleranza. Il tempo dell'evento viene quindi regolato in modo da ottenere la tolleranza massima per arrivo.  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | L'evento è arrivato in ritardo ma entro il livello di tolleranza. L'ora dell'evento non viene modificata.  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | L'evento è arrivato in tempo. Nessun adattamento necessario.  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | L'evento è arrivato fuori ordine ma entro la tolleranza di 8 secondi. Quindi, l'ora dell'evento non viene modificata. Ai fini dell'analisi, questo evento verrà considerato come precedente evento 4.  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | Evento non ordinato e tolleranza esterna di 8 secondi. Quindi, l'ora dell'evento viene adattata al massimo della tolleranza non ordinata. |

## <a name="can-these-settings-delay-output-of-my-job"></a>È possibile che queste impostazioni ritardino l'output del processo? 

Sì. Per impostazione predefinita, i criteri non ordinati sono impostati su zero (00 minuti e 00 secondi). Se si modifica l'impostazione predefinita, il primo output del processo viene ritardato di questo valore (o maggiore). 

Se una delle partizioni degli input non riceve eventi, è necessario aspettarsi che l'output venga ritardato in base al valore dei criteri di arrivo in ritardo. Per informazioni sul motivo, vedere la sezione errore InputPartition di seguito. 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>I messaggi LateInputEvents vengono visualizzati nel log attività

Questi messaggi vengono visualizzati per indicare che gli eventi sono arrivati in ritardo e sono stati eliminati o modificati in base alla configurazione. È possibile ignorare questi messaggi se i criteri di arrivo in ritardo sono stati configurati in modo appropriato. 

Esempio di questo messaggio: <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>Viene visualizzato InputPartitionNotProgressing nel log attività

È probabile che l'origine di input (hub eventi/Hub eventi) disponga di più partizioni. Analisi di flusso di Azure genera l'output per il timestamp T1 solo dopo che tutte le partizioni combinate sono almeno all'ora T1. Ad esempio, si supponga che la query legga da una partizione dell'hub eventi che ha due partizioni. Una delle partizioni, P1, ha eventi fino all'ora t1. L'altra partizione, P2, ha eventi fino all'ora t1 + x. L'output viene quindi generato fino all'ora t1. Tuttavia, se è presente una clausola esplicita Partition by PartitionId, entrambe le partizioni avanzano in modo indipendente. 

Quando vengono combinate più partizioni dello stesso flusso di input, la tolleranza per arrivo in ritardo è la quantità massima di tempo di attesa di ogni partizione per i nuovi dati. Se è presente una partizione nell'hub eventi o se l'hub Internet non riceve gli input, la sequenza temporale per tale partizione non avanza fino a raggiungere la soglia di tolleranza per arrivo in ritardo. Questa operazione ritarda l'output in base alla soglia di tolleranza per arrivo in ritardo. In questi casi, è possibile che venga visualizzato il messaggio seguente:
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
Questo messaggio indica che almeno una partizione nell'input è vuota e ritarderà l'output in base alla soglia di arrivo in ritardo. Per ovviare a questo problema, è consigliabile effettuare una delle operazioni seguenti:  
1. Verificare che tutte le partizioni dell'hub eventi/Hub eventi ricevano l'input. 
2. Usare la clausola PARTITION BY PartitionID nella query. 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>Perché viene visualizzato un ritardo di 5 secondi anche quando i criteri di arrivo in ritardo sono impostati su 0?
Questo errore si verifica quando è presente una partizione di input che non ha mai ricevuto input. È possibile verificare la metrica di input in base alla partizione per convalidare questo comportamento. 

Quando una partizione non contiene dati per più della soglia di arrivo in ritardo configurata, analisi di flusso sposta il timestamp dell'applicazione come descritto nella sezione Considerazioni sull'ordinamento degli eventi. Questa operazione richiede l'ora di arrivo stimata. Se la partizione non dispone mai di dati, analisi di flusso stima l'ora di arrivo come *ora locale-5 secondi*. A causa di questa partizione che non aveva mai dati, poteva visualizzare un ritardo della filigrana di 5 secondi.  

## <a name="next-steps"></a>Passaggi successivi
* [Considerazioni sulla gestione del tempo](stream-analytics-time-handling.md)
* [Metriche disponibili in Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
