---
title: Risolvere i problemi di fatturazione del contratto EA di Azure con le tabelle pivot del file di utilizzo
description: Questo articolo illustra la risoluzione dei problemi di fatturazione del contratto Enterprise Agreement (EA) mediante le tabelle pivot create dai file di utilizzo in formato CSV.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: b32bb979176af8a8a3751db8edf9c129caf1002e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131990"
---
# <a name="troubleshoot-ea-billing-issues-with-usage-file-pivot-tables"></a>Risolvere i problemi di fatturazione del contratto EA con le tabelle pivot del file di utilizzo

Questo articolo illustra la risoluzione dei problemi di fatturazione del contratto EA mediante le tabelle pivot nei file di utilizzo. I file di utilizzo di Azure contengono tutte le informazioni sull'utilizzo e il consumo di Azure. Le informazioni nel file consentono di:

- Ottenere informazioni sul modo in cui le prenotazioni di Azure vengono usate e applicate
- Riconciliare le informazioni in Gestione costi di Azure con la fattura emessa
- Risolvere i problemi correlati a un picco dei costi
- Calcolare l'importo di un rimborso per un contratto di servizio

L'uso delle informazioni dei file di utilizzo consente di migliorare la comprensione e la diagnostica dei problemi correlati all'utilizzo. I file di utilizzo vengono generati in formato CSV. I file di utilizzo possono essere file CSV di grandi dimensioni e risulta quindi più facile modificarli e visualizzarli come tabelle pivot in un'applicazione di foglio di calcolo come Excel. Gli esempi in questo articolo usano Excel, ma è possibile usare qualsiasi applicazione di foglio di calcolo.

Solo gli amministratori per il contratto EA, i proprietari dell'account e gli amministratori di reparto possono accedere per scaricare i file di utilizzo.

## <a name="get-the-data-and-format-it"></a>Ottenere i dati e formattarli

Poiché i file di utilizzo di Azure sono in formato CSV, è necessario preparare i dati per l'uso in Excel. Seguire questi passaggi per formattare i dati come tabella.

1. Scaricare il file Dettagli utilizzo - Versione 2 con Tutti gli addebiti (utilizzo e acquisti) usando le istruzioni disponibili in [Scaricare informazioni sull'utilizzo per i clienti EA](./download-azure-invoice-daily-usage-date.md#download-usage-for-ea-customers).
1. Apri il file in Excel.
1. I dati non formattati sono simili all'esempio seguente.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" :::
1. Selezionare il primo campo nella tabella, ovvero il campo contenente il titolo della prima colonna, **IDAccountDiFatturazione**.
1. Premere CTRL + MAIUSC + Freccia GIÙ e quindi CTRL + MAIUSC + Freccia destra per selezionare tutte le informazioni nella tabella.
1. Nel menu in alto selezionare **Inserisci** > **Tabella**. Nella casella Crea tabella selezionare **Tabella con intestazioni** e quindi selezionare **OK**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Esempio che mostra i dati non formattati in Excel" :::
1. Nel menu in alto selezionare **Inserisci** > **Tabella pivot** e quindi selezionare **OK**. L'azione crea un nuovo foglio nel file. Visualizza l'area della tabella pivot nel lato destro del foglio.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

L'area Campi di tabella pivot è un'area con trascinamento della selezione. Passare alla sezione successiva per creare la tabella pivot.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Creare una tabella pivot per visualizzare i costi di Azure in base alle risorse

In questa sezione viene creata una tabella pivot in cui è possibile risolvere i problemi dell'utilizzo generale complessivo di Azure. La tabella di esempio può semplificare l'indagine per individuare il servizio che utilizza la quantità più elevata di risorse. In alternativa, è possibile visualizzare le risorse che generano i costi più elevati e ottenere informazioni sul modo in cui vengono applicati addebiti per un servizio.

1. Nell'area Campi di tabella pivot trascinare **Categoria del contatore** e **Prodotto** nella sezione **Righe**. Posizionare **Prodotto** sotto **Categoria del contatore**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Aggiungere quindi la colonna **Costo** alla sezione **Valori**. È anche possibile usare la colonna Quantità utilizzata per ottenere informazioni sulle unità di consumo e sulle transazioni, ad esempio, GB e ore oppure transazioni invece del costo in diverse valute, come USD, EUR e INR.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. È ora disponibile un dashboard per l'indagine generalizzata del consumo. È possibile filtrare per visualizzare un servizio specifico usando le opzioni di filtro nella tabella pivot.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Per filtrare un secondo livello in una tabella pivot, ad esempio una risorsa, selezionare un elemento di secondo livello nella tabella.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Trascinare la colonna **IDRisorsa** nell'area **Righe** in **Prodotto** per visualizzare il costo di ogni servizio in base alla risorsa. Per visualizzare informazioni dettagliate sui prezzi, visualizzare PrezzoUnitario dell'organizzazione e cercare **Prodotto** nella prima colonna del listino prezzi.
1. Aggiungere la colonna **Data** nell'area **Colonne** per visualizzare il consumo giornaliero per il prodotto.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. Espandere e comprimere i mesi con i simboli **+** per la colonna di ogni mese.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::  
    L'aggiunta delle colonne **Costo** e **Quantità** nell'area **Valori** è facoltativa. Questa operazione crea due colonne per ogni sezione di dati sotto ogni mese e ogni giorno quando la colonna Data si trova nella sezione Colonne della tabella pivot.
1. Per filtri aggiuntivi, è possibile aggiungere le colonne IDSottoscrizione, Reparto, GruppoRisorse, Tag o Centro di costo all'area **Filtri** e selezionare l'elemento specifico.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Creare una tabella pivot per visualizzare il costo per una risorsa specifica

Una singola risorsa può comportare diversi addebiti per servizi diversi. È ad esempio possibile che a una macchina virtuale vengano applicati addebiti per risorse di calcolo, licenze del sistema operativo, larghezza di banda (trasferimenti dei dati), utilizzo di istanze riservate e archiviazione per snapshot. Quando si vuole esaminare l'utilizzo complessivo per risorse specifiche, i passaggi seguenti illustrano in modo dettagliato la creazione di un dashboard per visualizzare l'utilizzo complessivo con i file di utilizzo.

1. Nel menu a destra trascinare **IDRisorsa** nella sezione **Filtro** del menu della tabella pivot.
1. Selezionare la risorsa per cui si vuole visualizzare il costo. Digitare nella casella **Cerca** per trovare un nome di risorsa.
1. Aggiungere **Categoria del contatore** e **Prodotto** alla sezione Righe. Posizionare **Prodotto** sotto **Categoria del contatore**.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Esempio che mostra i dati non formattati in Excel" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Aggiungere quindi la colonna **Costo** alla sezione **Valori**. È anche possibile usare la colonna Quantità utilizzata per ottenere informazioni sulle unità di consumo e sulle transazioni, ad esempio, GB e ore oppure transazioni invece del costo in diverse valute, come USD, EUR e INR. È ora disponibile un dashboard che mostra tutti i servizi utilizzati dalla risorsa.
1. Aggiungere la colonna **Data** alla sezione **Colonne**. Mostra il consumo giornaliero.
1. È possibile espandere e ridurre la visualizzazione usando i simboli **+** nella colonna di ogni mese.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Esempio che mostra i dati non formattati in Excel" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare e analizzare i costi con l'analisi dei costi](../costs/quick-acm-cost-analysis.md).