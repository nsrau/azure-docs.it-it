---
title: Come creare un processo di elaborazione di analisi dei dati per Analisi di flusso | Documentazione Microsoft
description: Creare un processo di elaborazione di analisi dei dati per Analisi di flusso | segmento del percorso di apprendimento.
keywords: elaborazione di analisi dei dati
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 790ecf99acaf1d32a37b0f17757361b9d71778ff


---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Come creare un processo di elaborazione di analisi dei dati per Analisi di flusso
La risorsa di livello principale nell’analisi di flusso di Azure è il processo di analisi del flusso.  È costituito da una o più origini dati di input, una query che esprime la trasformazione dei dati e uno o più destinazioni di output in cui vengono scritti i risultati. Insieme questi consentono all'utente di eseguire elaborazioni di analisi dei dati per scenari di flussi di dati.

Per iniziare a usare Analisi di flusso, creare un nuovo processo di Analisi di flusso.  Si noti che questa azione non ha implicazioni sulla fatturazione fino a quando il processo non viene avviato.

1. Accedere al [portale di Azure classico](http://manage.windowsazure.com) online o al [portale di Azure](https://portal.azure.com/).
2. Nel portale: **fare clic su Nuovo**, fare clic su **Servizi dati** o **Analisi dei dati** a seconda del portale, fare clic su **Analisi di flusso di Azure** o **Analisi di flusso** e quindi su **Creazione rapida**.
   
   ![Procedura guidata del processo di elaborazione di analisi dei dati](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  
   
   ![Creare un processo di elaborazione di analisi dei dati](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  
3. Specificare la configurazione desiderata per il processo di analisi di flusso.
   
   * Nella casella **Nome processo** immettere un nome per identificare il processo di Analisi di flusso. Quando il **Nome processo** viene convalidato, appare un segno di spunta verde nella relativa casella. Il **Nome processo** può contenere solo caratteri alfanumerici e il carattere '-' e deve avere una lunghezza compresa tra 3 e 63 caratteri.
   * Usare **Area** o **Posizione** nel portale di Azure per specificare la posizione geografica in cui si intende eseguire il processo.
   * Se si usa il portale di Azure, selezionare o creare un account di archiviazione da usare come **Account di archiviazione di monitoraggio regionale**. Questo account di archiviazione viene utilizzato per archiviare i dati di monitoraggio per tutti i processi di Analisi del flusso in esecuzione all'interno dell'area.
   * Se si utilizza il portale di Azure, specificare un **gruppo di risorse** nuovo o esistente per contenere le risorse correlate per l'applicazione.
4. Dopo aver configurato le nuove opzioni del processo di Analisi di flusso, fare clic su **Crea processo di Analisi di flusso**. La creazione del processo di analisi di flusso può richiedere alcuni minuti. Per verificare lo stato, è possibile monitorare l'avanzamento nell’hub delle notifiche.
   
   ![Processo di elaborazione di analisi dei dati, hub di notifica](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  
   
   ![Portale di Azure, processo di elaborazione di analisi dei dati, creare processo](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. Il nuovo processo verrà visualizzato nell'elenco con uno stato **Creato**. Si noti che il pulsante **Avvia** è disabilitato. Prima di poter avviare il processo, è necessario configurare input, output, e la relativa query.
   
   ![Processo di elaborazione di analisi dei dati, stato processo](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  
   
   ![Portale di Azure, processo di elaborazione di analisi dei dati, stato processo](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


