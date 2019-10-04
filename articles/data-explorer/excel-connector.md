---
title: Visualizzare i dati con Azure Esplora dati Connector per Microsoft Excel
description: Questo articolo illustra come usare il connettore Excel per Azure Esplora dati.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 18c6499afe450a0387b0d9f0f13ee4378e0a21a9
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173833"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Visualizzare i dati con Azure Esplora dati Connector per Excel

Azure Esplora dati offre due opzioni per la connessione ai dati in Excel: usare il connettore nativo o importare una query da Azure Esplora dati. Questo articolo illustra come usare il connettore nativo in Excel e connettersi al cluster di Azure Esplora dati per ottenere e visualizzare i dati.

Il connettore Azure Esplora dati Excel native offre la possibilità di esportare i risultati delle query in Excel. È anche possibile aggiungere una query KQL come origine dati di Excel per calcoli o visualizzazioni aggiuntive.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Definire una query kusto come origine dati di Excel e caricare i dati in Excel

1. Aprire **Microsoft Excel**.
1. Nella scheda **dati** selezionare **Ottieni dati** > **da** > Azure**Esplora dati**.

    ![Ottenere dati da Esplora dati di Azure](media/excel-connector/get-data-from-adx.png)

1. Nella finestra di **Esplora dati di Azure (kusto)** completare i campi seguenti e fare clic su **OK**.

    ![Finestra di Esplora dati di Azure (kusto)](media/excel-connector/adx-connection-window.png)
    
    |Campo   |Descrizione |
    |---------|---------|
    |**Cluster**   |   Nome del cluster (obbligatorio)      |    
    |**Database**     |    Nome del database      |    
    |**Nome tabella o query Esplora dati di Azure**    |     Nome della tabella o della query Esplora dati di Azure    | 
    
    **Opzioni avanzate:**

     |Campo   |Descrizione |
    |---------|---------|
    |**Limita il numero di record del risultato della query**     |     Limitare il numero di record caricati in Excel  |    
    |**Limita dimensioni dati risultati query (byte)**    |    Limitare le dimensioni dei dati      |   
    |**Disabilitare il troncamento del set di risultati**    |         |      
    |**Istruzioni set aggiuntive (separate da punti e virgola)**    |    Aggiungere `set` istruzioni da applicare all'origine dati     |   

1.  Nel riquadro **strumento di navigazione** passare alla tabella corretta. Nel riquadro di anteprima della tabella selezionare **trasforma dati** per apportare modifiche ai dati oppure selezionare **carica** per caricarla in Excel.

![Finestra di Anteprima tabella](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Se il nome del **database** e/o della **tabella o della query di Azure Esplora dati** è già specificato, il riquadro di anteprima della tabella corretto verrà aperto automaticamente. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analizza e Visualizza i dati in Excel

Una volta caricati i dati in Excel ed è disponibile nel foglio di Excel, è possibile analizzare, riepilogare e visualizzare i dati creando relazioni e oggetti visivi. 

1.  Nella scheda **Progettazione tabella** selezionare **riepiloga con tabella pivot**. Nella finestra **Crea tabella pivot** selezionare la tabella pertinente e quindi fare clic su **OK**.

    ![Crea tabella pivot](media/excel-connector/create-pivot-table.png)

1. Nel riquadro **campi tabella pivot** selezionare le colonne della tabella pertinente per creare tabelle di riepilogo. Nell'esempio seguente sono stati selezionati EventID e **state** .
    
    ![Selezione campi tabella pivot](media/excel-connector/pivot-table-pick-fields.png)

1. Nella scheda **Analizza tabella pivot** selezionare **grafico pivot** per creare oggetti visivi in base alla tabella. 

    ![Grafico pivot](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. Nell'esempio seguente usare **ID evento**, **StartTime**e **eventType** per visualizzare informazioni aggiuntive sugli eventi meteorologici.

    ![Visualizzare i dati](media/excel-connector/visualize-excel-data.png)

1. Consente di creare dashboard completi per il monitoraggio dei dati.

