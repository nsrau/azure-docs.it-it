---
title: Come configurare gli output dei dati per i processi di Analisi di flusso | Documentazione Microsoft
description: Configurare gli output per i processi di Analisi di flusso | segmento del percorso di apprendimento.
keywords: output dei dati, spostamento dei dati
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 3bbea3da-bfce-4af1-a15e-d4b23874034f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 09/26/2016
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aebad3dc5b6fda757713b53edfe9a6d474b7f4cb


---
# <a name="how-to-configure-data-outputs-for-stream-analytics-jobs"></a>Come configurare gli output dei dati per i processi di Analisi di flusso
I processi di Analisi di flusso di Azure possono essere connessi a uno o più output dei dati, che definisce una connessione a un sink di dati esistente. Dato che il processo di Analisi di flusso elabora e trasforma i dati in entrata, un flusso di eventi di output dei dati viene scritto nell'output del processo.

È possibile usare gli output dei dati di Analisi di flusso per creare dashboard o avvisi in tempo reale, attivare i flussi di lavoro degli spostamenti dei dati o semplicemente archiviare i dati per una successiva elaborazione batch. L’analisi di flusso dispone dell’integrazione di prima classe con diversi servizi di Azure, che sono documentati in dettaglio di seguito.

Per aggiungere un output al processo di analisi di flusso:

1. Nel portale di Azure classico fare clic su **Output**, quindi scegliere **Aggiungi output** nel processo di analisi di flusso.
   
    ![Aggiungere output](./media/stream-analytics-add-outputs/1-stream-analytics-add-outputs.png)  
   
    Nel portale di Azure fare clic sul riquadro **Output** nel processo di analisi di flusso.
   
    ![Aggiungere output dal portale di Azure](./media/stream-analytics-add-outputs/5-stream-analytics-add-outputs.png)
2. Specificare il tipo di output:
   
    ![Scegliere il tipo di spostamento dei dati](./media/stream-analytics-add-outputs/2-stream-analytics-add-outputs.png)  
   
    ![Portale di Azure, scegliere il tipo di spostamento dei dati](./media/stream-analytics-add-outputs/6-stream-analytics-add-outputs.png)
3. Fornire un nome descrittivo per l'output nella finestra di **Alias di output** . Questo nome verrà utilizzato nella query del processo in un secondo momento per fare riferimento all'output.  
   
    Compilare il resto delle proprietà di connessione necessarie per connettersi all'output.  Questi campi variano in base al tipo di output e vengono definiti in modo dettagliato di seguito.  
   
    ![Aggiungere proprietà dell'output dei dati](./media/stream-analytics-add-outputs/3-stream-analytics-add-outputs.png)  
4. A seconda del tipo di output, può essere necessario specificare la modalità di serializzazione o formattazione dei dati. Di seguito sono descritte le impostazioni di serializzazione specifiche per ogni tipo di output.
   
    Compilare il resto delle proprietà di connessione necessarie per connettersi all'origine dati. Questi campi variano in base al tipo di origine e di input e vengono definiti in modo dettagliato [qui](stream-analytics-create-a-job.md).  
   
    ![Aggiungere l'output dei dati all'hub eventi](./media/stream-analytics-add-outputs/4-stream-analytics-add-outputs.png)  
   
    ![Portale di Azure, output dei dati nell'hub eventi](./media/stream-analytics-add-outputs/7-stream-analytics-add-outputs.png)  

> [!Note]
> Qualsiasi elemento output aggiunto al processo, deve esistere prima che il processo venga avviato e gli eventi avviino il flusso. Ad esempio, se si utilizza l'archiviazione Blob come output, il processo non creerà un account di archiviazione automaticamente. Deve essere creato dall'utente prima che venga avviato il processo ASA.
> 
> 

## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


