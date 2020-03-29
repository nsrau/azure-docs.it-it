---
title: Visualizzare i dati usando il connettore di Azure Data Explorer per Microsoft Excel
description: In this article, you learn how to use the Excel connector for Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849055"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Visualizzare i dati usando il connettore di Azure Data Explorer per ExcelDisplay data using the Azure Data Explorer connector for Excel

Azure Data Explorer provides two options for connecting to data in Excel: use the native connector or import a query from Azure Data Explorer. Questo articolo illustra come usare il connettore nativo in Excel e connettersi al cluster Azure Data Explorer per ottenere e visualizzare i dati.

The Azure Data Explorer Excel native connector offers the ability to export query results to Excel. È inoltre possibile aggiungere una query KQL come origine dati Excel per ulteriori calcoli o visualizzazioni.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Definire la query Kusto come origine dati di Excel e caricare i dati in Excel

1. Aprire **Microsoft Excel**.
1. Nella scheda **Dati** selezionare **Ottieni dati** > **da Azure** > **da Azure Data Explorer.**

    ![Ottenere dati da Esplora dati di Azure](media/excel-connector/get-data-from-adx.png)

1. Nella finestra **Azure Data Explorer (Kusto)** completare i campi seguenti e selezionare **OK**.

    ![Finestra Esplora dati di Azure (Kusto)Azure Data Explorer (Kusto) window](media/excel-connector/adx-connection-window.png)
    
    |Campo   |Descrizione |
    |---------|---------|
    |**grappolo**   |   Nome del cluster (obbligatorio)      |    
    |**Database**     |    Nome del database      |    
    |**Nome tabella o query di Azure Data Explorer**    |     Nome della tabella o della query di Azure Data ExplorerName of table or Azure Data Explorer query    | 
    
    **Opzioni avanzate:**

     |Campo   |Descrizione |
    |---------|---------|
    |**Limitare il numero di record dei risultati della query**     |     Limitare il numero di record caricati in Excel  |    
    |**Limitare le dimensioni dei dati dei risultati della query (byte)Limit query result data size (bytes)**    |    Limitare le dimensioni dei dati      |   
    |**Disabilitare il troncamento del set di risultatiDisable result-set truncation**    |         |      
    |**Istruzioni Set aggiuntive (separate da punto e virgola)**    |    Aggiungere `set` istruzioni da applicare all'origine datiAdd statements to apply to data source     |   

1.  Nel riquadro **Strumento di navigazione,** passare alla tabella corretta. Nel riquadro di anteprima della tabella selezionare Trasforma dati per apportare modifiche ai dati o **selezionare Carica** per caricarli in Excel.In the table preview pane, select **Transform Data** to make changes to your data or select Load to Load it to Excel.

![Finestra di anteprima tabella](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Se **Database** il database e/o il nome della **tabella o la query** di Azure Data Explorer sono già specificati, verrà aperto automaticamente il riquadro di anteprima della tabella corretto. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analizzare e visualizzare i dati in Excel

Una volta che i dati vengono caricati in Excel ed è disponibile nel foglio di Excel, è possibile analizzare, riepilogare e visualizzare i dati creando relazioni e oggetti visivi. 

1.  Nella scheda **Struttura tabella** selezionare **Riepiloga con tabella pivot**. Nella finestra **Crea tabella pivot** selezionare la tabella pertinente e scegliere **OK**.

    ![Crea tabella pivot](media/excel-connector/create-pivot-table.png)

1. Nel riquadro **Campi tabella pivot** selezionare le colonne della tabella pertinenti per creare tabelle di riepilogo. Nell'esempio seguente vengono selezionati **EventId** e **State.**
    
    ![Selezionare campi di tabella pivot](media/excel-connector/pivot-table-pick-fields.png)

1. Nella scheda **Analizza tabella pivot** selezionare Grafico **pivot** per creare oggetti visivi basati sulla tabella. 

    ![Grafico pivot](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. Nell'esempio riportato di seguito, utilizzare **ID evento**, **StartTime**e **EventType** per visualizzare informazioni aggiuntive sugli eventi meteo.

    ![Visualizzare i dati](media/excel-connector/visualize-excel-data.png)

1. Creare dashboard completi per monitorare i dati.

## <a name="next-steps"></a>Passaggi successivi

[Visualizzare i dati usando una query Kusto di Azure Data Explorer importata in Microsoft Excel](excel-blank-query.md)