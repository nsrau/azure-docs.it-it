---
title: Testare un processo di Analisi di flusso di Azure con dati di esempio
description: Questo articolo descrive come usare il portale di Azure per testare un processo di Analisi di flusso di Azure, un input di esempio e caricare i dati di esempio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: de2b99666dbed4eaec3db3e56800e9a7352e10e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898393"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Testare un processo di Analisi di flusso di Azure nel portaleTest an Azure Stream Analytics job in the portal

In Analisi di flusso di Azure è possibile testare la query senza avviare o arrestare il processo. È possibile testare le query sui dati in ingresso dalle origini di streaming o caricare dati di esempio da un file locale nel portale di Azure.You can test queries on incoming data from your streaming sources or upload sample data from a local file on Azure Portal. È inoltre possibile testare le query in locale dai dati di esempio locali o dai dati attivi in [Visual Studio](stream-analytics-live-data-local-testing.md) e Visual [Studio Code](visual-studio-code-local-run-live-input.md).

## <a name="automatically-sample-incoming-data-from-input"></a>Campionare automaticamente i dati in ingresso dall'input

Analisi di flusso di Azure recupera automaticamente gli eventi dagli input di streaming. È possibile eseguire query sull'esempio predefinito o impostare un intervallo di tempo specifico per l'esempio.

1. Accedere al portale di Azure.

2. Individua e seleziona il processo di Analisi di flusso esistente.

3. Nella pagina del processo di Analisi di flusso selezionare **Query** nell'intestazione **Topologia processo** per aprire la finestra dell'editor Query. 

4. Per visualizzare un elenco di esempio di eventi in ingresso, selezionare l'icona di input con file e gli eventi di esempio verranno visualizzati automaticamente **nell'anteprima di input**.

   a. Il tipo di serializzazione per i dati viene rilevato automaticamente se è JSON o CSV. È possibile modificarlo manualmente anche in JSON, CSV, AVRO modificando l'opzione nel menu a discesa.
    
   b. Utilizzare il selettore per visualizzare i dati in formato **Tabella** o **Non elaborato.**
    
   c. Se i dati visualizzati non sono aggiornati, selezionare **Aggiorna** per visualizzare gli eventi più recenti.

   La tabella seguente è un esempio di dati nel **formato Tabella:**

   ![Input dell'esempio di Analisi di flusso di Azure in formato tabellaAzure Stream Analytics sample input in table format](./media/stream-analytics-test-query/asa-sample-table.png)

   La tabella seguente è un esempio di dati in **formato Raw:**

   ![Input di esempio di Analisi di flusso di Azure in formato non elaboratoAzure Stream Analytics sample input in raw format](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Per testare la query con i dati in ingresso, selezionare **Test query**. I risultati vengono visualizzati nella scheda **Risultati test.** È anche possibile selezionare **Scarica risultati** per scaricare i risultati.

   ![Risultati delle query di test di esempio di Analisi di flusso di Azure Azure](./media/stream-analytics-test-query/asa-test-query.png)

6. Per testare la query rispetto a un intervallo di tempo specifico di eventi in ingresso, selezionare **Seleziona intervallo di tempo**.
   
   ![Intervallo di tempo di Analisi di flusso di Azure per gli eventi di esempio in ingressoAzure Stream Analytics time range for incoming sample events](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Impostare l'intervallo di tempo degli eventi che si desidera utilizzare per testare la query e selezionare **Esempio**. All'interno di tale intervallo di tempo, è possibile recuperare fino a 1000 eventi o 1 MB, a seconda di quale viene prima.

   ![Azure Analisi flusso imposta l'intervallo di tempo per gli eventi di esempio in ingressoAzure Stream Analytics set time range for incoming sample events](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Una volta campionati gli eventi, questi vengono visualizzati nella scheda **Anteprima input.**

   ![Azure Stream Analytics visualizza i risultati dei testAzure Stream Analytics view test results](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Selezionare **Reimposta** per visualizzare l'elenco di esempio degli eventi in ingresso. Se si seleziona **Reimposta**, la selezione dell'intervallo di tempo andrà persa. Selezionare **Test query** per testare la query ed esaminare i risultati nella scheda **Risultati test.**

10. Quando si apportano modifiche alla query, selezionare **Salva query** per testare la nuova logica di query. In questo modo è possibile modificare in modo iterativo la query e testarla di nuovo per vedere come cambia l'output.

11. Dopo aver verificato i risultati visualizzati nel browser, è possibile **avviare** il processo.

## <a name="upload-sample-data-from-a-local-file"></a>Caricare dati di esempio da un file localeUpload sample data from a local file

Anziché usare i dati dinamici, è possibile usare i dati di esempio da un file locale per testare la query di Analisi di flusso di Azure.Instead of using live data, you can use sample data from a local file to test your Azure Stream Analytics query.

1. Accedere al portale di Azure.
   
2. Individuare il processo di Analisi di flusso di Azure esistente e selezionarlo.

3. Nella pagina del processo di Analisi di flusso selezionare **Query** nell'intestazione **Topologia processo** per aprire la finestra dell'editor Query.

4. Per testare la query con un file locale, selezionare **Carica input di esempio** nella scheda Anteprima **input.** 

   ![File di esempio di caricamento di Analisi di flusso di AzureAzure Stream Analytics upload sample file](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Caricare il file locale per testare la query. È possibile caricare file solo con i formati JSON, CSV o AVRO. Selezionare **OK**.

   ![File di esempio di caricamento di Analisi di flusso di AzureAzure Stream Analytics upload sample file](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Non appena si carica il file, è anche possibile visualizzare il contenuto del file nel modulo come tabella o in formato non elaborato. Se si seleziona **Reimposta**, i dati di esempio torneranno ai dati di input in ingresso illustrati nella sezione precedente. È possibile caricare qualsiasi altro file per testare la query in qualsiasi momento.

7. Selezionare **Testquery** per testare la query rispetto al file di esempio caricato.

8. I risultati dei test vengono visualizzati in base alla query. È possibile modificare la query e selezionare **Salva query** per testare la nuova logica di query. In questo modo è possibile modificare in modo iterativo la query e testarla di nuovo per vedere come cambia l'output.

9. Quando si utilizzano più output nella query, i risultati vengono visualizzati in base all'output selezionato. 

   ![Output selezionato di Analisi di flusso di AzureAzure Stream Analytics selected output](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Dopo aver verificato i risultati visualizzati nel browser, è possibile **avviare** il processo.

## <a name="next-steps"></a>Passaggi successivi
* Crea una [soluzione IoT usando Analisi di flusso:](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)questa esercitazione ti guiderà a creare una soluzione end-to-end con un generatore di dati che simulerà il traffico in un casello.

* [Guida di riferimento al linguaggio di query di Analisi di flusso di AzureAzure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Esempi di query per modelli di uso comune di Analisi di flusso](stream-analytics-stream-analytics-query-patterns.md)

* [Informazioni sugli input per Analisi di flusso di Azure](stream-analytics-add-inputs.md)

* [Informazioni sugli output di Analisi di flusso di Azure](stream-analytics-define-outputs.md)
