---
title: Testare un processo di Analisi di flusso di Azure con dati di esempio
description: Questo articolo descrive come usare il portale di Azure per testare un processo di Analisi di flusso di Azure, un input di esempio e caricare i dati di esempio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2019
ms.custom: seodec18
ms.openlocfilehash: c0a76ecd12143e0bbaa9997bfc6d7295df9c4ec7
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340870"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testare una query di Analisi di flusso con dati di esempio

Usando Azure Stream Analitica, è possibile campionare dati di input o caricare i dati di esempio per testare le query nel portale di Azure senza dover avviare o arrestare un processo.

## <a name="upload-sample-data-and-test-the-query"></a>Caricare i dati di esempio e testare la query

1. Accedere al portale di Azure. 

2. Individuare il processo di Analisi di flusso di Azure esistente e selezionarlo.

3. Nella pagina del processo di Analisi di flusso selezionare **Query** nell'intestazione **Topologia processo** per aprire la finestra dell'editor Query. 

4. Per testare la query si possono quindi essere Campiona dati da un input in tempo reale o caricamento da un file. I dati devono essere serializzati in JSON, CSV o AVRO. L'esempio di input deve essere codificato in UTF-8 e non compresso. Solo i delimitatori di virgola (,) sono supportati per testare gli input CSV nel portale.

    1. Utilizzando l'input in tempo reale: pulsante destro del mouse su uno degli input. Quindi selezionare **Campiona dati da input**. Nella schermata successiva, è possibile impostare la durata dell'esempio.

    1. Utilizzo di file: fare clic su uno degli input. Selezionare quindi **Upload sample data from file** (Carica dati di esempio da file). 

    ![Pulsante per il testo delle query nell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Dopo aver completato il campionamento o caricamento, selezionare **testare** per testare la query usando i dati di esempio è stato specificato.

    ![dati di esempio per il test nell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Se si desidera usare i risultati del test in futuro, l'output della query viene visualizzato nel browser con un collegamento ai risultati del download. 

7. Modificare in modalità iterativa la query e testarla di nuovo per vedere come cambia l'output.

   ![Output di esempio dell'editor query della funzione di analisi di flusso](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Quando si usano più output in una query, è possibile visualizzarne i risultati in schede separate e passare facilmente da un output all'altro.

8. Dopo aver verificato i risultati mostrati nel browser **salvare** la query. Quindi **avviare** il processo e attendere che gli eventi in ingresso vengano elaborati.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
