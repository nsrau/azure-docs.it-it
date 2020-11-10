---
title: Testare un processo di Analisi di flusso di Azure con dati di esempio
description: Questo articolo descrive come usare il portale di Azure per testare un processo di Analisi di flusso di Azure, un input di esempio e caricare i dati di esempio.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: 524b34c48146dc9e6102ed8d20ff8d1076706ba7
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444963"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Testare un processo di analisi di flusso di Azure nel portale

In analisi di flusso di Azure è possibile testare la query senza avviare o arrestare il processo. È possibile testare le query sui dati in ingresso dalle origini di streaming o caricare i dati di esempio da un file locale nel portale di Azure. È anche possibile testare le query localmente da dati di esempio locali o dati dinamici in [Visual Studio](stream-analytics-live-data-local-testing.md) e [Visual Studio Code](visual-studio-code-local-run-live-input.md).

## <a name="automatically-sample-incoming-data-from-input"></a>Campionamento automatico dei dati in ingresso dall'input

Analisi di flusso di Azure recupera automaticamente gli eventi dagli input di streaming. È possibile eseguire query sull'esempio predefinito o impostare un intervallo di tempo specifico per l'esempio.

1. Accedere al portale di Azure.

2. Individuare e selezionare il processo di analisi di flusso esistente.

3. Nella pagina del processo di Analisi di flusso selezionare **Query** nell'intestazione **Topologia processo** per aprire la finestra dell'editor Query. 

4. Per visualizzare un elenco di eventi in ingresso di esempio, selezionare l'icona input con file. gli eventi di esempio verranno visualizzati automaticamente nell' **Anteprima di input**.

   a. Il tipo di serializzazione per i dati viene rilevato automaticamente se il relativo JSON o CSV. È possibile modificarlo manualmente in JSON, CSV e AVRO cambiando l'opzione nel menu a discesa.
    
   b. Usare il selettore per visualizzare i dati in formato **tabella** o non **elaborato** .
    
   c. Se i dati visualizzati non sono aggiornati, selezionare **Aggiorna** per visualizzare gli eventi più recenti.

   La tabella seguente è un esempio di dati nel **formato tabella** :

   ![Input di esempio di analisi di flusso di Azure in formato tabella](./media/stream-analytics-test-query/asa-sample-table.png)

   La tabella seguente è un esempio di dati in **formato non elaborato** :

   ![Input di esempio di analisi di flusso di Azure in formato non elaborato](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Per testare la query con i dati in ingresso, selezionare **Test query**. I risultati vengono visualizzati nella scheda **risultati test** . È anche possibile selezionare **Scarica risultati** per scaricare i risultati.

   ![Risultati della query di esempio di analisi di flusso di Azure](./media/stream-analytics-test-query/asa-test-query.png)

6. Per testare la query in base a un intervallo di tempo specifico di eventi in ingresso, selezionare **Seleziona intervallo di tempo**.
   
   ![Intervallo di tempo di analisi di flusso di Azure per gli eventi di esempio in ingresso](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Impostare l'intervallo di tempo degli eventi che si vuole usare per testare la query e selezionare **Sample (esempio** ). All'interno di tale intervallo di tempo, è possibile recuperare fino a 1000 eventi o 1 MB, a seconda di quale si verifichi per primo.

   ![Intervallo di tempo set di analisi di flusso di Azure per gli eventi di esempio in ingresso](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Una volta campionati gli eventi per l'intervallo di tempo selezionato, verranno visualizzati nella scheda **Anteprima input** .

   ![Analisi di flusso di Azure visualizzare i risultati dei test](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Selezionare **Reimposta** per visualizzare l'elenco degli eventi in ingresso di esempio. Se si seleziona **Reimposta** , la selezione dell'intervallo di tempo andrà persa. Selezionare **query di test** per testare la query ed esaminare i risultati nella scheda **risultati test** .

10. Quando si apportano modifiche alla query, fare clic su **Salva query** per testare la nuova logica di query. In questo modo è possibile modificare la query in modo iterativo e testarla di nuovo per vedere come cambia l'output.

11. Dopo aver verificato i risultati visualizzati nel browser, si è pronti per **avviare** il processo.

## <a name="upload-sample-data-from-a-local-file"></a>Carica dati di esempio da un file locale

Invece di usare i dati dinamici, è possibile usare dati di esempio da un file locale per testare la query di analisi di flusso di Azure.

1. Accedere al portale di Azure.
   
2. Individuare il processo di Analisi di flusso di Azure esistente e selezionarlo.

3. Nella pagina del processo di Analisi di flusso selezionare **Query** nell'intestazione **Topologia processo** per aprire la finestra dell'editor Query.

4. Per testare la query con un file locale, selezionare **carica input di esempio** nella scheda **Anteprima input** . 

   ![Screenshot che mostra l'opzione Upload Sample input.](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Caricare il file locale per testare la query. È possibile caricare solo file con i formati JSON, CSV o AVRO. Selezionare **OK**.

   ![Screenshot mostra la finestra di dialogo Carica dati di esempio in cui è possibile selezionare un file.](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Non appena si carica il file, è anche possibile visualizzare il contenuto del file nel form come tabella o nel formato non elaborato. Se si seleziona **Reimposta** , i dati di esempio torneranno ai dati di input descritti nella sezione precedente. È possibile caricare qualsiasi altro file per testare la query in qualsiasi momento.

7. Selezionare **Test query** per testare la query sul file di esempio caricato.

8. I risultati dei test vengono visualizzati in base alla query. È possibile modificare la query e selezionare **Salva query** per testare la nuova logica di query. In questo modo è possibile modificare la query in modo iterativo e testarla di nuovo per vedere come cambia l'output.

9. Quando si utilizzano più output nella query, i risultati vengono visualizzati in base all'output selezionato. 

   ![Output selezionato di analisi di flusso di Azure](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Dopo aver verificato i risultati visualizzati nel browser, è possibile **avviare** il processo.

## <a name="limitations"></a>Limitazioni

1.  Il criterio di ora non è supportato nel test del portale:

   * Non ordinato: verranno ordinati tutti gli eventi in ingresso.
   * Arrivo in ritardo: non ci sarà un evento di arrivo in ritardo perché analisi di flusso può usare solo i dati esistenti per il test.
   
2.  La funzione definita dall'utente C# non è supportata.

3.  Tutti i test verranno eseguiti con un processo con un'unità di streaming.

4.  Le dimensioni del timeout sono pari a un minuto. Pertanto, qualsiasi query con una dimensione della finestra maggiore di un minuto non può recuperare dati.

5.  Machine Learning non è supportato.


## <a name="troubleshooting"></a>Risoluzione dei problemi

1.  Se viene generato questo errore, si è verificato un problema di connettività di rete durante il recupero dei risultati. Verificare le impostazioni di rete e del firewall. attenersi alla procedura seguente:

  * Per verificare la connessione al servizio, aprire [https://queryruntime.azurestreamanalytics.com/api/home/index](https://queryruntime.azurestreamanalytics.com/api/home/index) in un browser. Se non è possibile aprire questo collegamento, aggiornare le impostazioni del firewall.
  
2. Se viene ricevuto questo errore, la dimensione della richiesta è troppo grande. Ridurre le dimensioni dei dati di input e riprovare ". attenersi alla procedura seguente:

  * Ridurre le dimensioni di input: testare la query con un file di esempio di dimensioni inferiori o con un intervallo di tempo inferiore.
  * Ridurre le dimensioni della query: per testare una selezione di query, selezionare una parte della query, quindi fare clic su **Verifica query selezionata**.


## <a name="next-steps"></a>Passaggi successivi
* [Creare una soluzione Internet delle cose usando analisi di flusso](./stream-analytics-build-an-iot-solution-using-stream-analytics.md): questa esercitazione illustra come creare una soluzione end-to-end con un generatore di dati che simula il traffico presso un casello.

* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](/stream-analytics-query/stream-analytics-query-language-reference)

* [Esempi di query per modelli di uso comune di Analisi di flusso](stream-analytics-stream-analytics-query-patterns.md)

* [Informazioni sugli input per Analisi di flusso di Azure](stream-analytics-add-inputs.md)

* [Informazioni sugli output di Analisi di flusso di Azure](stream-analytics-define-outputs.md)