---
title: Elaborare i dati da Hub eventi di Azure usando Analisi di flusso Documenti Microsoft
description: Questo articolo illustra come elaborare i dati dall'hub eventi di Azure usando un processo di Analisi di flusso di Azure.This article shows you how to process data from your Azure event hub using an Azure Stream Analytics job.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 531426656fe833752c9c4685688c00de3894895b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69991938"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Elaborare i dati dall'hub eventi usando Analisi di flusso di AzureProcess data from your event hub using Azure Stream Analytics 
Il servizio Analisi di flusso di Azure semplifica l'inserimento, l'elaborazione e l'analisi dei dati in streaming dagli hub eventi di Azure, consentendo informazioni dettagliate avanzate per guidare azioni in tempo reale. Questa integrazione consente di creare rapidamente una pipeline di analisi dei percorsi a caldo. È possibile usare il portale di Azure per visualizzare i dati in ingresso e scrivere una query di Analisi di flusso. Quando la query è pronta, è possibile spostarla nell'ambiente di produzione in pochi clic. 

## <a name="key-benefits"></a>Vantaggi principali
Ecco i vantaggi principali degli hub eventi di Azure e dell'integrazione di Azure Stream Analytics:Here are the key benefits of Azure Event Hubs and Azure Stream Analytics integration: 
- **Anteprima dei dati:** è possibile visualizzare in anteprima i dati in ingresso da un hub eventi nel portale di Azure.Preview data – You can preview incoming data from an event hub in the Azure portal.
- **Testare la query:** preparare una query di trasformazione e testarla direttamente nel portale di Azure.Test your query – Prepare a transformation query and test it directly in the Azure portal. Per la sintassi del linguaggio di query, vedere la documentazione del [linguaggio](/stream-analytics-query/built-in-functions-azure-stream-analytics) di query di Analisi di flusso.
- **Distribuire la query nell'ambiente di produzione:** è possibile distribuire la query nell'ambiente di produzione creando e avviando un processo di Analisi di flusso di Azure.Deploy your query to production – You can deploy the query into production by creating and starting an Azure Stream Analytics job.

## <a name="end-to-end-flow"></a>Flusso end-to-end

1. Accedere al [portale](https://portal.azure.com)di Azure . 
1. Passare allo **spazio dei nomi Hub eventi** e quindi all'hub eventi, che contiene i dati in ingresso. **event hub** 
1. Selezionare **Elabora dati** nella pagina hub eventi.  

    ![Riquadro dei dati del processo](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Seleziona **Esplora** nel riquadro **Abilita informazioni dettagliate in tempo reale dagli eventi.** 

    ![Selezionare analisi di flusso](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Viene visualizzata una pagina di query con i valori già impostati per i campi seguenti:
    1. **L'hub eventi** come input per la query.
    1. Query SQL di **esempio** con istruzione SELECT. 
    1. Alias di **output** per fare riferimento ai risultati del test di query. 

        ![Editor di query](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Quando si usa questa funzionalità per la prima volta, questa pagina richiede l'autorizzazione per creare un gruppo di consumer e un criterio per l'hub eventi per visualizzare in anteprima i dati in ingresso.
1. Selezionare **Crea** nel riquadro **di anteprima Input** come mostrato nell'immagine precedente. 
1. Vedrai immediatamente un'istantanea degli ultimi dati in entrata in questa scheda.
    - Il tipo di serializzazione nei dati viene rilevato automaticamente (JSON/CSV). È anche possibile modificarlo manualmente in JSON/CSV/AVRO.
    - È possibile visualizzare in anteprima i dati in entrata nel formato tabella o in formato non elaborato. 
    - Se i dati visualizzati non sono aggiornati, selezionare **Aggiorna** per visualizzare gli eventi più recenti. 

        Ecco un esempio di **table format**dati nel ![formato tabella : Risultati nel formato tabella](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Di seguito è riportato un esempio di dati in **formato non elaborato:** 

        ![Risultati nel formato non elaborato](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Selezionare **Test query** per visualizzare lo snapshot dei risultati del test della query nella scheda **Risultati test.** È inoltre possibile scaricare i risultati.

    ![Risultati delle query di test](./media/process-data-azure-stream-analytics/test-results.png)
1. Scrivere una query personalizzata per trasformare i dati. Vedere Informazioni di riferimento sul [linguaggio di query di Analisi di flusso](/stream-analytics-query/stream-analytics-query-language-reference).
1. Dopo aver testato la query e averla spostata nell'ambiente di produzione, selezionare **Distribuisci query**. Per distribuire la query, creare un processo di Analisi di flusso di Azure in cui è possibile impostare un output per il processo e avviarlo. Per creare un processo di Analisi di flusso, specificare un nome per il processo e selezionare **Crea**.

      ![Creare un processo di Analisi di flusso di Azure](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  È consigliabile creare un gruppo di consumer e un criterio per ogni nuovo processo di Analisi di flusso di Azure creato dalla pagina Hub eventi. I gruppi di consumer consentono solo cinque lettori simultanei, pertanto fornire un gruppo di consumer dedicato per ogni processo eviterà eventuali errori che potrebbero derivare dal superamento di tale limite. Un criterio dedicato consente di ruotare la chiave o revocare le autorizzazioni senza influire sulle altre risorse. 
1. Il processo di Analisi di flusso viene ora creato dove la query è la stessa testata e l'input è l'hub eventi. 

9.  Per completare la pipeline, impostare **l'output** della query e selezionare **Avvia** per avviare il processo.

    > [!NOTE]
    > Prima di avviare il processo, non dimenticare di sostituire outputalias con il nome di output creato in Analisi di flusso di Azure.Before starting the job, don't forget to replace the outputalias by the output name you created in Azure Stream Analytics.

      ![Impostare l'output e avviare il processo](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Limitazioni note
Durante il test della query, il caricamento dei risultati del test richiede circa 6 secondi. Stiamo lavorando per migliorare le prestazioni dei test. Tuttavia, quando viene distribuito nell'ambiente di produzione, Analisi di flusso di Azure avrà una latenza inferiore.

## <a name="streaming-units"></a>Unità di streaming
Per impostazione predefinita, il processo di Analisi di flusso di Azure è di tre unità di streaming. Per modificare questa impostazione, selezionare Scala nel menu a sinistra nella pagina processo Analisi di flusso nel portale di Azure.To adjust this setting, select **Scale** on the left menu in the **Stream Analytics job** page in the Azure portal. Per ulteriori informazioni sulle unità di streaming, consultate [Comprendere e regolare](../stream-analytics/stream-analytics-streaming-unit-consumption.md)le unità di streaming.

![Scalare le unità di streaming](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle query di Analisi di flusso, vedere Lingua di query di [Analisi di flussoTo](/stream-analytics-query/built-in-functions-azure-stream-analytics) learn more about Stream Analytics queries, see Stream Analytics Query Language
