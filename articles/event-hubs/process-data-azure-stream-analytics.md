---
title: Elaborare i dati da Azure di hub eventi usando Analitica Stream | Microsoft Docs
description: Questo articolo illustra come elaborare i dati dall'hub eventi di Azure tramite un processo Azure Stream Analitica.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 07/09/2019
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: f179687b0983e145244e228a3d3b06b4eabead48
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723415"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Elaborare i dati dall'hub eventi usando Azure Stream Analitica
Il servizio Azure Stream Analitica semplifica di inserimento, l'elaborazione e analizzare i dati in streaming da hub eventi di Azure, l'abilitazione di insights potenti per favorire azioni in tempo reale. Questa integrazione consente di creare rapidamente una pipeline di analitica percorso critico. È possibile usare il portale di Azure per visualizzare i dati in arrivo e scrivere una query Analitica Stream. Quando la query è pronta, è possibile spostarlo nell'ambiente di produzione in pochi clic. 

## <a name="key-benefits"></a>Vantaggi principali
Ecco i principali vantaggi dell'integrazione di hub eventi di Azure e Azure Stream Analitica: 
- **Anteprima dati** – è possibile visualizzare in anteprima i dati in ingresso da un hub eventi nel portale di Azure.
- **Testare la query** : preparare una query di trasformazione e testarla direttamente nel portale di Azure. Per informazioni sulla sintassi del linguaggio di query, vedere [linguaggio di Query Analitica Stream](/stream-analytics-query/built-in-functions-azure-stream-analytics) documentazione.
- **Distribuire la query nell'ambiente di produzione** – è possibile distribuire la query nell'ambiente di produzione creando e avviando un processo Azure Stream Analitica.

## <a name="end-to-end-flow"></a>Flusso end-to-end

1. Accedere al [portale di Azure](https://portal.azure.com). 
1. Passare alle **dello spazio dei nomi di hub eventi** e quindi passare al **dell'hub eventi**, che contiene i dati in ingresso. 
1. Selezionare **elaborare i dati** nella pagina dell'hub eventi.  

    ![Riquadro dei dati di processo](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. Selezionare **Explore** nel **Abilita informazioni approfondite in tempo reale da eventi** riquadro. 

    ![Selezionare analisi di flusso](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Verrà visualizzata una pagina di query con valori già impostati per i campi seguenti:
    1. I **hub eventi** come input per la query.
    1. Campione **query SQL** con l'istruzione SELECT. 
    1. Un' **output** alias per fare riferimento ai risultati del test. 

        ![Editor di query](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Quando si usa questa funzionalità per la prima volta, questa pagina chiede l'autorizzazione creare un gruppo di consumer e un criterio per l'hub eventi per visualizzare in anteprima i dati in ingresso.
1. Selezionare **Create** nel **anteprima Input** riquadro come illustrato nell'immagine precedente. 
1. Si noterà immediatamente uno snapshot dei dati in ingresso più recenti in questa scheda.
    - Il tipo di serializzazione dei dati viene automaticamente rilevato (JSON o CSV). È possibile modificare manualmente, nonché per JSON o CSV/AVRO.
    - È possibile visualizzare in anteprima i dati in ingresso nel formato di tabella o formato non elaborato. 
    - Se i dati visualizzati, non sono aggiornati, selezionare **Aggiorna** per visualizzare gli eventi più recenti. 

        Ecco un esempio di dati nel **formato di tabella**:   ![Risultati nel formato di tabella](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Ecco un esempio di dati nel **formato non elaborato**: 

        ![Risultati in formato non elaborato](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Selezionare **query di prova** per visualizzare lo snapshot dei risultati dei test della query nella **i risultati dei Test** scheda. È anche possibile scaricare i risultati.

    ![Testare i risultati della query](./media/process-data-azure-stream-analytics/test-results.png)
1. Scrivere query per trasformare i dati. Visualizzare [riferimenti al linguaggio di Query Analitica Stream](/stream-analytics-query/stream-analytics-query-language-reference).
1. Dopo aver testato la query e si desidera spostarla nell'ambiente di produzione, seleziona **Distribuisci query**. Per distribuire la query, creare un processo di Analitica Stream di Azure in cui è possibile impostare un output per il processo e avviare il processo. Per creare un processo di Stream Analitica, specifica un nome per il processo e selezionare **Create**.

      ![Creare un processo di Analisi di flusso di Azure](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  È consigliabile creare un gruppo di consumer e un criterio per ogni nuovo processo Azure Stream Analitica creati dalla pagina di hub eventi. Gruppi di consumer consentono solo cinque reader concorrenti, in modo che fornisce un gruppo di consumer dedicato per ogni processo di evitare eventuali errori che potrebbero verificarsi quando si supera tale limite. Un criterio dedicated consente di ruotare la chiave o revocare le autorizzazioni senza influire sulle altre risorse. 
1. Il processo di Stream Analitica è stato creato in cui la query è lo stesso che è stata testata e l'input è l'hub eventi. 

9.  Per completare la pipeline, impostare il **output** della query e selezionare **avviare** per avviare il processo.

    > [!NOTE]
    > Prima di iniziare il processo, non dimenticare di sostituire l'elaborate dal nome dell'output che è stato creato in Azure Stream Analitica.

      ![Impostare l'output e avviare il processo](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Limitazioni note
Durante il test della query, i risultati del test richiedere circa 6 secondi per caricare. Stiamo lavorando per migliorare le prestazioni dei test. Tuttavia, se distribuite in produzione, Azure Stream Analitica avranno latenza a frazioni di secondo.

## <a name="streaming-units"></a>Unità di streaming
Il valore predefinito è tre unità di streaming (SUs) del processo di Analitica di Stream di Azure. Per modificare questa impostazione, selezionare **scalabilità** nel menu a sinistra il **processo di Stream Analitica** pagina nel portale di Azure. Per altre informazioni sulle unità di streaming, vedere [comprensione e modifica delle unità di Streaming](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Ridimensionare le unità di streaming](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle query Analitica Stream, vedere [linguaggio di Query Analitica di Stream](/stream-analytics-query/built-in-functions-azure-stream-analytics)