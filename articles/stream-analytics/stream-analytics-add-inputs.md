---
title: Aggiungere un input di dati ai processi di analisi di flusso | Microsoft Docs
description: Informazioni su come associare un&quot;origine dati al processo di analisi di flusso come input di dati di streaming proveniente dall&quot;hub eventi o dati di riferimento provenienti dall&quot;archiviazione BLOB.
keywords: input di dati, flusso di dati
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9e59bd24-2a80-4ecb-b6b2-309a07c70bcd
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f95fe2afab1205998f4c8401e162748b50ae9850


---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Aggiungere un input di dati di streaming o dati di riferimento a un processo di analisi di flusso
Informazioni su come associare un'origine dati al processo di Analisi di flusso come input di dati di streaming provenienti da Hub eventi o dati di riferimento provenienti dall'archivio BLOB.

I processi di analisi di flusso di Azure possono essere connessi a uno o più input di dati, che definiscono una connessione a un'origine dati esistente. Quando i dati vengono inviati a tale origine dati, vengono usati dal processo di Analisi di flusso ed elaborati in tempo reale come flussi di dati. L'analisi di flusso si integra perfettamente con gli [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) e con l'[archivio BLOB di Azure](../storage/storage-dotnet-how-to-use-blobs.md) all'interno e all'esterno della sottoscrizione del processo.

Questo articolo è un passaggio nel [percorso di apprendimento di analisi di flusso](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Input di dati: dati di streaming e dati di riferimento
Esistono due tipi di input distinti in Analisi di flusso: dati di riferimento e flussi di dati.

* **Flussi di dati**: I processi di analisi di flusso devono includere almeno un input del flusso dei dati che deve essere utilizzato e trasformato dal processo stesso. L'archivio BLOB di Azure e Hub eventi di Azure sono supportati come origini di input del flusso dei dati. Gli Hub eventi di Azure vengono usati per raccogliere flussi di eventi da più dispositivi, servizi e applicazioni connessi. Si può usare l'archivio BLOB di Azure come origine di input per l'inserimento di dati in blocco come flusso.  
* **Dati di riferimento**: l’analisi di flusso supporta un secondo tipo di input ausiliario denominato dati di riferimento.  Al contrario dei dati in continua evoluzione, questi dati sono statici o cambiano molto lentamente.  In genere vengono utilizzati per l'esecuzione di ricerche e correlazioni con flussi di dati per creare un set di dati più ampio.  L'archivio BLOB di Azure attualmente è l'unica origine di input supportata per i dati di riferimento.  

Per aggiungere un input al processo di analisi di flusso:

1. Nel Portale di Azure fare clic su **Input** e poi scegliere **Aggiungere un input** nel processo di analisi di flusso.
   
    ![Portale di Azure classico - aggiungere un input.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    Nel portale di Azure fare clic sul riquadro **Input** nel processo di analisi di flusso.  
   
    ![Portale di Azure - aggiungere un input di dati.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. Specificare il tipo di input: **Flusso dei dati** o **Dati di riferimento**.
   
    ![Aggiungere l'input di dati corretto, di flusso o di riferimento](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![Aggiungere l'input di dati corretto, di flusso o di riferimento](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. Se si crea un input del flusso di dati, specificare il tipo di origine per l'input.  Questo passaggio può essere ignorato durante la creazione dei dati di riferimento, poiché al momento è supportato soltanto l’archivio BLOB.
   
    ![Aggiungere l'input di dati del flusso di dati](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![Aggiungere l'input di dati del flusso di dati](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. Fornire un nome descrittivo per l'input nella finestra di Alias di Input.  Questo nome verrà utilizzare nella query del processo in un secondo momento per fare riferimento all'input.
   
    Compilare il resto delle proprietà di connessione necessarie per connettersi all'origine dati. Questi campi variano in base al tipo di origine e di input e vengono definiti in modo dettagliato [qui](stream-analytics-create-a-job.md).  
   
    ![Aggiungere l'input di dati dell'hub eventi](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. Specificare le impostazioni di serializzazione per i dati di input:
   
   * Per assicurarsi che le query funzionino nel modo previsto, specificare il **Formato di serializzazione eventi** dei dati in arrivo.  I formati di serializzazione supportati sono JSON, CSV e Avro.
   * Verificare la **Codifica** per i dati.  Al momento UTF-8 è l'unico formato di codifica supportato.
     
     ![Impostazioni di serializzazione dei dati per l'input di dati](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![Impostazioni di serializzazione dei dati per l'input di dati](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. Dopo aver completato la creazione dell’input, l’analisi di flusso verificherà che è possibile connettersi all'origine di input.  È possibile visualizzare lo stato dell'operazione di connessione di prova nell'hub di notifica.
   
    ![Verificare la connessione dell'input dei flussi di dati](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![Verificare la connessione dell'input dei flussi di dati](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Ottenere aiuto per gli input dei dati di streaming
Per ulteriore assistenza, provare il [Forum di Analisi dei flussi di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-get-started.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


