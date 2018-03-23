---
title: Risoluzione dei problemi relativi agli eventi di input in formato non valido in Analisi di flusso di Azure | Microsoft Docs
description: Come stabilire quale evento nei dati di input causa problemi in un processo di Analisi di flusso
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/05/2018
ms.author: sngun
ms.openlocfilehash: 6b6c154568fe97b7495ae70dc162dc475169afea
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Problemi comuni in Analisi di flusso e passaggi per risolvere tali problemi

## <a name="troubleshoot-for-malformed-input-events"></a>Risoluzione dei problemi relativi agli eventi di input in formato non valido

Quando il flusso di input del processo di Analisi di flusso contiene messaggi in formato non valido, causa problemi di serializzazione. I messaggi in formato non valido includono errori di serializzazione, ad esempio parentesi mancanti in un oggetto JSON o formato del timestamp non corretto. Quando un processo di Analisi di flusso riceve un messaggio in formato non valido, elimina il messaggio e visualizza un messaggio di avviso per l'utente. Un simbolo di avviso viene visualizzato nel riquadro **Input** del processo di Analisi di flusso (il simbolo di avviso rimane finché il processo è in esecuzione):

![Riquadro Input](media/stream-analytics-malformed-events/inputs_tile.png)

È possibile abilitare i log di diagnostica per visualizzare i dettagli dell'avviso. Per gli eventi di input con formato non corretto, i log di esecuzione contengono una voce con un messaggio simile al seguente: "Messaggio: Non è stato possibile deserializzare gli eventi di input dalla risorsa <blob URI> come json)". 

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

1. Passare al riquadro Input e fare clic per visualizzare gli avvisi.
2. Nel riquadro dei dettagli di input viene visualizzato un set di avvisi con i dettagli sul problema. Di seguito è riportato un messaggio di avviso di esempio, che mostra la partizione, l'offset e i numeri di sequenza dei dati JSON in formato non valido. 

   ![Messaggio di avviso con offset](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Per ottenere i dati JSON con formato non corretto, eseguire il codice CheckMalformedEvents.cs. È possibile ottenerlo dal [repository degli esempi di GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Questo codice legge l'ID della partizione, l'offset e stampa i dati presenti nell'offset. 

4. Dopo aver eseguito la lettura dei dati, è possibile analizzare e correggere il formato di serializzazione. 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>Gestione di record duplicati quando si usa il database SQL di Azure come output per un processo di Analisi di flusso

Quando si configura il database SQL di Azure come output di un processo di Analisi di flusso, vengono eseguiti inserimenti bulk dei record nella tabella di destinazione. In generale, Analisi di flusso di Azure garantisce [almeno un recapito]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) nel sink di output, ma è possibile [ottenere esattamente un recapito]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) nell'output SQL quando per la tabella SQL viene definito un vincolo univoco. 

Dopo avere configurato i vincoli di chiave univoca nella tabella SQL, se sono presenti record duplicati da inserire nella tabella SQL, Analisi di flusso di Azure rimuove i record duplicati suddividendo i dati in batch e inserendo in modo ricorsivo i batch finché non viene trovato ogni singolo record duplicato. Se è presente un numero considerevole di righe duplicate nella pipeline, suddividere i dati e inserirli in modo ricorsivo ignorando i duplicati uno alla volta è un processo che può richiede molto tempo. Se vengono visualizzati più messaggi di avviso di violazione della chiave nel log attività nell'ultima ora, è probabile che l'output SQL stia rallentando l'intero processo. Per risolvere questo problema, è necessario [configurare l'indice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) che causa la violazione della chiave, abilitando l'opzione IGNORE_DUP_KEY. L'abilitazione di questa opzione consente a SQL di ignorare i valori duplicati durante gli inserimenti bulk e SQL Azure genera semplicemente un messaggio di avviso anziché un errore. Analisi di flusso di Azure non genera più errori di violazione della chiave primaria.

Quando si configura IGNORE_DUP_KEY per diversi tipi di indici, tenere presente le osservazioni seguenti:

* Non è possibile impostare IGNORE_DUP_KEY in una chiave primaria o in un vincolo univoco che usa ALTER INDEX, ma è necessario eliminare e ricreare l'indice.  
* È possibile impostare l'opzione IGNORE_DUP_KEY con ALTER INDEX per un indice univoco, diverso dal vincolo PRIMARY KEY/UNIQUE e creato con la definizione CREATE INDEX o INDEX.  
* L'opzione IGNORE_DUP_KEY non è valida per gli indici columnstore perché non è possibile applicare l'univocità in tali indici.  

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

