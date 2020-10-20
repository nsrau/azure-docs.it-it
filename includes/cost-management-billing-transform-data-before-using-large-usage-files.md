---
title: includere file
description: includere file
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 10/09/2020
ms.author: banders
ms.reviwer: ''
ms.custom: include file
ms.openlocfilehash: fb34b4ef1cf209a15c7e6ac9a59cc11dd4b0056e
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026477"
---
## <a name="transform-data-before-using-large-usage-files"></a>Trasformare i dati prima di usare file di utilizzo di grandi dimensioni

Il file di utilizzo o di riconciliazione è a volte troppo grande per essere aperto oppure è possibile che sia necessaria solo una parte delle informazioni per risolvere un problema. È ad esempio possibile che si vogliano informazioni solo per una risorsa specifica oppure che sia necessario solo l'utilizzo per alcuni servizi o gruppi di risorse. È possibile trasformare i dati per riepilogarli prima di creare tabelle pivot.

1. Aprire una cartella di lavoro vuota in Excel.
1. Nel menu in alto selezionare **Dati** > **Da testo/CSV**, selezionare il file di utilizzo e quindi selezionare **Importa**.
1. Nella parte inferiore della finestra selezionare **Trasforma dati**. Una nuova finestra mostra un riepilogo dei dati.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" alt-text="Esempio che mostra i dati riepilogati" lightbox="./media/cost-management-billing-transform-data-before-using-large-usage-files/summarized-data.png" :::
1. Se si ha un Contratto del cliente Microsoft, ignorare questo passaggio e procedere al successivo perché i file di utilizzo del Contratto del cliente Microsoft hanno titoli di colonna nelle prime righe. Preparare i dati creando la tabella. Rimuovere le prime righe, lasciando solo i titoli. Selezionare **Rimuovi righe** > **Rimuovi prime righe**.  
     :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/remove-top-rows.png" alt-text="Esempio che mostra i dati riepilogati" :::
1. Nella finestra Rimuovi prime righe immettere il numero di righe da rimuovere nella parte superiore. Per il contratto EA è in genere 2 e per CSP è in genere 1. Selezionare **OK**.
1. Selezionare **Usa la prima riga come intestazione**.  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/use-first-row-as-header.png" alt-text="Esempio che mostra i dati riepilogati" :::
    
    La visualizzazione tabella mostra i titoli di colonna nella parte superiore.
1. Aggiungere quindi un filtro. Usare le frecce del selettore a destra di ogni titolo di colonna per filtrare. I filtri suggeriti sono ID sottoscrizione, Nome servizio (Categoria del contatore), ID istanza, gruppo di risorse. È possibile utilizzare più filtri all'interno dello stesso documento. È consigliabile applicare tutti i filtri possibili per ridurre le dimensioni del documento e semplificare le operazioni successive.
1. Dopo l'applicazione dei filtri, selezionare **Close & Load** (Chiudi e carica).  
    :::image type="content" source="./media/cost-management-billing-transform-data-before-using-large-usage-files/close-and-load.png" alt-text="Esempio che mostra i dati riepilogati" :::

Il file viene caricato e mostra una tabella con dati di utilizzo filtrati. È ora possibile creare una nuova tabella pivot per risolvere i problemi di utilizzo.