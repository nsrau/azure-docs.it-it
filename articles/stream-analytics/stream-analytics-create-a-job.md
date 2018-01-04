---
title: Come creare un processo di elaborazione di analisi dei dati per Analisi di flusso | Documentazione Microsoft
description: Creare un processo di elaborazione di analisi dei dati per Analisi di flusso | segmento del percorso di apprendimento.
keywords: elaborazione di analisi dei dati
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 82b9c861fee820cdb0ca1891e0e62436f422d838
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Come creare un processo di elaborazione di analisi dei dati per Analisi di flusso
La risorsa di livello principale nell’analisi di flusso di Azure è il processo di analisi del flusso.  È costituito da una o più origini dati di input, una query che esprime la trasformazione dei dati e uno o più destinazioni di output in cui vengono scritti i risultati. Insieme questi consentono all'utente di eseguire elaborazioni di analisi dei dati per scenari di flussi di dati.

Per iniziare a usare Analisi di flusso, creare un nuovo processo di Analisi di flusso.  Si noti che questa azione non ha implicazioni sulla fatturazione fino a quando il processo non viene avviato.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Selezionare **New** > **dati + Analitica** > **processo flusso Analitica**.
3. Selezionare **Create**.
   
3. Specificare la configurazione desiderata per il processo di analisi di flusso.
   
   * Nella casella **Nome processo** immettere un nome per identificare il processo di Analisi di flusso. Quando il **Nome processo** viene convalidato, appare un segno di spunta verde nella relativa casella. Il **Nome processo** può contenere solo caratteri alfanumerici e il carattere '-' e deve avere una lunghezza compresa tra 3 e 63 caratteri.
   * Utilizzare **percorso** per specificare la posizione geografica in cui si desidera eseguire il processo.
   * Specificare un nuovo o esistente **gruppo di risorse** per contenere le risorse correlate per l'applicazione.
4. Selezionare **Create**.
La creazione del processo di analisi di flusso può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nell’hub delle notifiche.
    
   ![Portale di Azure, processo di elaborazione di analisi dei dati, creare un processo](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Il nuovo processo avrà lo stato **Creato**. Si noti che il pulsante **Avvia** è disabilitato. Prima di avviare il processo, configurare l'input, la query e l'output.

   
   ![Portale di Azure, elaborazione dell'analisi dei dati, stato processo](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

