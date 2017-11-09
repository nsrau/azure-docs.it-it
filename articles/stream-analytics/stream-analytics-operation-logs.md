---
title: Eseguire il debug con i log dell'operazione e del servizio in analisi di flusso | Documentazione Microsoft
description: Come usare i log delle operazioni di Analisi di flusso
keywords: log dei servizi
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: a2ed9676-f0bd-4398-87c8-a592779ac728
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 2ee0871752dc2a3da345339fb826340d44ae48d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="debug-stream-analytics-jobs-using-service-and-operation-logs"></a>Eseguire il debug dei processi di analisi di flusso con i log dei servizi e delle operazioni
Tutti i servizi di Azure forniscono messaggi di registrazione operativi agli utenti per registrare i dettagli relativi alle operazioni di gestione. Nell’analisi di flusso di Azure, queste informazioni possono essere utilizzate per operazioni relative al debug, quali la visualizzazione dello stato del processo, dell’avanzamento del processo, e dei messaggi di errore per rilevare l’avanzamento di un processo nel tempo, dall’avvio, all’elaborazione, fino all’output.

## <a name="find-operation-logs-in-the-azure-portal"></a>Trovare i log delle operazioni nel portale di Azure
E’ possibile accedere ai log delle operazioni in due modi:  

* Dashboard del processo di analisi di flusso  
* Servizi di gestione nel portale di Azure classico  

## <a name="dashboard-of-the-stream-analytics-job"></a>Dashboard del processo di analisi di flusso
Un collegamento per i log di un processo di analisi di flusso corrispondenti viene visualizzato nella scheda Dashboard del processo. Se si fa clic sul collegamento, i filtri vengono impostati in modo da visualizzare i log più recenti per questo processo specifico.

  ![Selezionare i log dei servizi di gestione](./media/stream-analytics-operation-logs/01-stream-analytics-operation-logs.png)  

## <a name="management-services"></a>Servizi di gestione
Per passare manualmente ai log delle operazioni per Analisi di flusso di Azure e altri servizi nel portale di Azure classico:

1. Fare clic su **Servizi di gestione** nel [portale di Azure classico](https://manage.windowsazure.com).
2. Selezionare **Analisi di flusso** per **Tipo** e il nome del processo per **Nome servizio**.  
   
   ![Selezionare analisi di flusso](./media/stream-analytics-operation-logs/02-stream-analytics-operation-logs.png)  

## <a name="find-audit-logs-in-the-azure-portal"></a>Trovare i log di controllo nel portale di Azure
Per trovare i log operativi del processo di analisi di flusso nel portale di Azure, fare clic su **Sfoglia** e selezionare **Log di controllo**.

  ![Selezione analisi di flusso del portale di Azure](./media/stream-analytics-operation-logs/06-stream-analytics-operation-logs.png)  

Viene aperta una visualizzazione che mostra gli eventi degli ultimi sette giorni per tutte le risorse nella sottoscrizione.  È possibile filtrare per visualizzare gli eventi di un tipo o di un intervallo di tempo specificato selezionando il comando **Filtra** .

  ![Selezione analisi di flusso del portale di Azure](./media/stream-analytics-operation-logs/07-stream-analytics-operation-logs.png)  

## <a name="get-log-details"></a>Ottenere i dettagli dei log
È possibile filtrare per stato e intervallo di tempo per visualizzare i log per il processo.

Nel portale di Azure fare clic sul pulsante **Dettagli** nella parte inferiore della finestra per visualizzare altri dettagli sull'evento selezionato. 

  ![Selezionare i dettagli](./media/stream-analytics-operation-logs/03-stream-analytics-operation-logs.png)  

Nel portale di Azure fare clic su una voce di log per visualizzare gli eventi dettagliati al suo interno.

  ![Selezione dettagli del portale di Azure](./media/stream-analytics-operation-logs/08-stream-analytics-operation-logs.png)  

A questo punto, è possibile aprire la visualizzazione **Dettagli** facendo clic sull'evento.

  ![Selezione dettagli del portale di Azure](./media/stream-analytics-operation-logs/09-stream-analytics-operation-logs.png)  

## <a name="debug-a-failed-job"></a>Debug di un processo non riuscito
Nel portale di Azure fare clic sull'icona Cerca e digitare "non riuscito". In questo modo si visualizzeranno tutti i log dei processi non riusciti. 

  ![Eseguire il debug di un processo non riuscito](./media/stream-analytics-operation-logs/04-stream-analytics-operation-logs.png)  

Nel portale di Azure è possibile filtrare per livello di messaggio per visualizzare gli eventi **Critici** .

  ![Debug del portale di Azure](./media/stream-analytics-operation-logs/10-stream-analytics-operation-logs.png)  

È possibile selezionare uno qualsiasi dei processi non riusciti e fare clic su **Dettagli** per ulteriori informazioni sull'errore.  Alcuni messaggi di errore forniscono inoltre informazioni su come attenuare il problema. 

  ![Dettagli operazione](./media/stream-analytics-operation-logs/05-stream-analytics-operation-logs.png)  

Nel caso in cui sia necessario contattare il [Supporto](https://azure.microsoft.com/support/options/) o fornire informazioni al team tramite il [forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics), tenere presenti i dettagli dell'operazione, in particolare l’**ID di correlazione**. 

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

