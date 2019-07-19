---
title: Elaborare dati da Azure Hub eventi con analisi di flusso | Microsoft Docs
description: Questo articolo illustra come elaborare i dati dall'hub eventi di Azure usando un processo di analisi di flusso di Azure.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 003e68b36ff71fb2991cf087ef33f72aba73a8be
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233967"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics-preview"></a>Elaborare i dati dall'hub eventi usando analisi di flusso di Azure (anteprima)
Il servizio analisi di flusso di Azure semplifica l'inserimento, l'elaborazione e l'analisi dei dati di streaming dagli hub eventi di Azure, consentendo di ottenere informazioni avanzate per l'esecuzione di azioni in tempo reale. Questa integrazione consente di creare rapidamente una pipeline di analisi del percorso critico. È possibile usare la portale di Azure per visualizzare i dati in ingresso e scrivere una query di analisi di flusso. Quando la query è pronta, è possibile spostarla in produzione solo con pochi clic. 

> [!NOTE]
> Questa funzionalità è attualmente in anteprima. 

## <a name="key-benefits"></a>Vantaggi principali
Ecco i vantaggi principali di hub eventi di Azure e l'integrazione di analisi di flusso di Azure: 
- **Anteprima dati** : è possibile visualizzare in anteprima i dati in ingresso da un hub eventi nel portale di Azure.
- **Testare la query** : preparare una query di trasformazione e testarla direttamente nell'portale di Azure. Per la sintassi del linguaggio di query, vedere documentazione sul [linguaggio di query di analisi di flusso](/stream-analytics-query/built-in-functions-azure-stream-analytics) .
- **Distribuire la query in produzione** : è possibile distribuire la query nell'ambiente di produzione creando e avviando un processo di analisi di flusso di Azure.

## <a name="end-to-end-flow"></a>Flusso end-to-end

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare allo **spazio dei nomi di hub eventi** , quindi passare all' **Hub eventi**con i dati in arrivo. 
1. Selezionare **elabora dati** nella pagina Hub eventi.  

    ![Riquadro dati processo](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Selezionare **Esplora** nel riquadro **Abilita informazioni dettagliate in tempo reale da eventi** . 

    ![Selezionare analisi di flusso](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Viene visualizzata una pagina di query con valori già impostati per i campi seguenti:
    1. **Hub eventi** come input per la query.
    1. **Query SQL** di esempio con l'istruzione SELECT. 
    1. Alias di **output** per fare riferimento ai risultati del test di query. 

        ![Editor di query](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Quando si usa questa funzionalità per la prima volta, questa pagina richiede l'autorizzazione per creare un gruppo di consumer e un criterio per l'hub eventi per visualizzare l'anteprima dei dati in arrivo.
1. Selezionare **Crea** nel riquadro di **Anteprima input** come illustrato nell'immagine precedente. 
1. In questa scheda verrà immediatamente visualizzato uno snapshot dei dati in arrivo più recenti.
    - Il tipo di serializzazione nei dati viene rilevato automaticamente (JSON/CSV). È possibile modificarla manualmente anche in JSON/CSV/AVRO.
    - È possibile visualizzare in anteprima i dati in ingresso nel formato tabella o in formato non elaborato. 
    - Se i dati visualizzati non sono aggiornati, selezionare **Aggiorna** per visualizzare gli eventi più recenti. 

        Di seguito è riportato un esempio di dati nel **formato tabella**:   ![Risultati in formato tabella](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Di seguito è riportato un esempio di dati in **formato non elaborato**: 

        ![Genera il formato non elaborato](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Selezionare **query di test** per visualizzare lo snapshot dei risultati del test della query nella scheda **risultati test** . È anche possibile scaricare i risultati.

    ![Testare i risultati della query](./media/process-data-azure-stream-analytics/test-results.png)
1. Scrivere una query personalizzata per trasformare i dati. Vedere [riferimento al linguaggio di query di analisi di flusso](/stream-analytics-query/stream-analytics-query-language-reference).
1. Una volta testata la query e si vuole spostarla in produzione, selezionare Distribuisci **query**. Per distribuire la query, creare un processo di analisi di flusso di Azure in cui è possibile impostare un output per il processo e avviare il processo. Per creare un processo di analisi di flusso, specificare un nome per il processo e selezionare **Crea**.

      ![Creare un processo di Analisi di flusso di Azure](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Si consiglia di creare un gruppo di consumer e un criterio per ogni nuovo processo di analisi di flusso di Azure creato dalla pagina Hub eventi. I gruppi di consumer consentono solo cinque lettori simultanei, pertanto la fornitura di un gruppo di consumer dedicato per ogni processo eviterà eventuali errori che potrebbero derivare dal superamento di tale limite. Un criterio dedicato consente di ruotare la chiave o revocare le autorizzazioni senza influisca sulle altre risorse. 
1. Il processo di analisi di flusso viene ora creato quando la query è la stessa testata e l'input è l'hub eventi. 

9.  Per completare la pipeline, impostare l' **output** della query e selezionare **Avvia** per avviare il processo.

    > [!NOTE]
    > Prima di avviare il processo, non dimenticare di sostituire elaborate nella con il nome di output creato in analisi di flusso di Azure.

      ![Impostare l'output e avviare il processo](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Limitazioni note
Durante il test della query, il caricamento dei risultati del test richiede circa 6 secondi. Stiamo lavorando per migliorare le prestazioni dei test. Tuttavia, in caso di distribuzione nell'ambiente di produzione, analisi di flusso di Azure avrà una latenza di sottosecondo.

## <a name="streaming-units"></a>Unità di streaming
Il processo di analisi di flusso di Azure viene impostato per impostazione predefinita su tre unità di streaming (SUs). Per modificare questa impostazione, scegliere **scala** nel menu a sinistra nella pagina del **processo di analisi di flusso** nel portale di Azure. Per altre informazioni sulle unità di streaming, vedere [comprendere e modificare le unità di streaming](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Ridimensionare le unità di streaming](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle query di analisi di flusso, vedere [linguaggio di query di analisi di flusso](/stream-analytics-query/built-in-functions-azure-stream-analytics)
