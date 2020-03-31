---
title: Come aggiungere set di dati di riferimento all'ambiente - Azure Time Series Insights Documenti Microsoft
description: In questo articolo viene descritto come aggiungere un set di dati di riferimento per aumentare i dati nell'ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 79628ed44753577023464ef6208027e1b7996d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087256"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Creare un set di dati di riferimento per l'ambiente Time Series Insights usando il portale di Azure

In questo articolo viene descritto come aggiungere un set di dati di riferimento all'ambiente Azure Time Series Insights. Risulta utile associare i dati di riferimento ai dati di origine per aumentarne il valore.

Un set di dati di riferimento è una raccolta di elementi che aumentano gli eventi dell'origine evento. Il motore in ingresso di Time Series Insights crea un join tra ogni evento dell'origine evento e la riga di dati corrispondente nel set di dati di riferimento. Questo evento aumentato sarà quindi disponibile per le query. Questo join è basato sulla colonna o le colonne di chiavi primarie definite nel set di dati di riferimento.

Non viene creato un join dei dati di riferimento in maniera retroattiva. Pertanto, solo i dati in ingresso correnti e futuri vengono associati e uniti al set di data di riferimento, una volta che sono stati configurati e caricati.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Informazioni sul modello di dati di riferimento di Time Series Insight.Learn about Time Series Insight's reference data model.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Aggiungere un set di dati di riferimento

1. Accedere al [portale](https://portal.azure.com)di Azure .

1. Individuare l'ambiente Azure Time Series Insights esistente. Selezionare **Tutte le risorse** nel menu a sinistra del portale di Azure. Selezionare l'ambiente Time Series Insights.

1. Selezionare la pagina **Panoramica**. Espandere la sezione **Essentials** nella parte superiore della pagina per individuare l'URL di **Esplora risorse di Time Series Insights** e aprire il collegamento.  

   [![Espandere la sezione Essentials](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Visualizzare l'esploratore per l'ambiente Time Series Insights.

1. Espandere il selettore dell'ambiente in Esplora time Series Insights. Scegliere l'ambiente attivo. Selezionare l'icona dei dati di riferimento nell'angolo superiore destro della pagina dell'Explorer.

   [![Aggiungere i dati di riferimento](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Selezionare il pulsante **+ Aggiungi un set di dati** per iniziare ad aggiungere un nuovo set di dati.

   [![Aggiungi un set di dati](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Nella pagina **Nuovo set di dati di riferimento** scegliere il formato dei dati:

   - Scegliere **CSV** per i dati delimitati da virgole. La prima riga viene considerata una riga di intestazione.
   - Scegliere **JSON Array** per i dati in formato JSON (JavaScript object notation).

   [![Scegliere il formato dei dati.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Specificare i dati usando uno dei due metodi seguenti:

   - Incollare i dati nell'editor di testo. Quindi scegliere il pulsante **Analizza dati di riferimento**.
   - Selezionare il pulsante **Scegli file** per aggiungere i dati da un file di testo locale.

   Ad esempio, incollare i dati CSV: [ ![Dati CSV incollati](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Ad esempio, incollare i dati della matrice JSON: incollare i dati JSONFor example, paste JSON array [ ![data: Paste JSON data](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   In caso di errore di analisi dei valori dei dati, l'errore viene visualizzato in rosso in fondo alla pagina, ad esempio: `CSV parsing error, no rows extracted`.

1. Se l'analisi dei dati riesce, viene visualizzata una griglia con le colonne e le righe che rappresentano i dati. Esaminare la griglia dei dati per verificarne la correttezza.

   [![Esaminare i dati di riferimentoReview reference data](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Esaminare ogni colonna per comprendere il tipo di dati presunto e modificare il tipo di dati, se necessario.  Selezionare il simbolo del tipo **#** di dati nell'intestazione di colonna: per double (dati numerici), ** F** per booleano o **Abc** per stringa.

   [![Scegliere i tipi di dati nelle intestazioni di colonna.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Rinominare le intestazioni di colonna se necessario. Il nome di colonna chiave è necessario per creare un join con la proprietà corrispondente nell'origine evento. 

   > [!IMPORTANT]
   > Verificare che i nomi di colonna chiave dei dati di riferimento corrispondano esattamente al nome evento nei dati in entrata, inclusa la combinazione di maiuscole/minuscole. I nomi non di colonna chiave vengono usati per aumentare i dati in entrata con i valori dei dati di riferimento corrispondenti.

1. Digitare un valore nel campo **Filtra righe** per esaminare righe specifiche in base alle necessità. Il filtro è utile per esaminare i dati, ma non viene applicato quando si caricano i dati.

1. Assegnare un nome al set di dati specificandolo nel campo **Nome del set di dati** sopra la griglia dei dati.

    [![Assegnare un nome al set di dati.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Specificare la colonna **Chiave primaria** nel set di dati, selezionandola dall'elenco a discesa sopra la griglia dei dati.

    [![Selezionare la colonna o le colonne chiave.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Facoltativo)** Selezionare **+** il pulsante per aggiungere una colonna chiave secondaria, come chiave primaria composita. Se occorre annullare la selezione, selezionare il valore vuoto nell'elenco a discesa per rimuovere la chiave secondaria.

1. Per caricare i dati, selezionare il pulsante **Carica righe**.

    [![Caricare righe e confermare i dati.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    La pagina conferma il completamento del caricamento e visualizza il messaggio **Il set di dati è stato caricato**.

    > [!WARNING]
    > Colonne o proprietà condivise tra set di dati di riferimento visualizzerà un errore di caricamento **del nome di proprietà Duplica.Columns** or properties shared between reference data sets will display a Duplicate property name upload error. L'errore non impedirà il caricamento corretto dei set di dati di riferimento. Può essere rimosso combinando le righe che condividono il nome della proprietà duplicata.

1. Selezionare **Aggiungi una riga**, Importa **righe**o **Aggiungi colonna** per aggiungere altri valori di dati di riferimento, in base alle esigenze.

    [![Aggiungere una riga, L'importazione bulk delle righe o Aggiungi una colonna.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Qualsiasi riga che condivide una chiave univoca con un'altra riga avrà le colonne sostituite dall'ultima riga aggiunta che condivide tale chiave univoca.

   > [!NOTE]
   > Non **è** necessario che le righe aggiunte siano *rettangolari:* possono avere meno colonne, maggiori o variabili dalle altre voci del set di dati di riferimento.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i dati di riferimento](time-series-insights-manage-reference-data-csharp.md) a livello di codice.

* Per il riferimento completo all'API, leggere il documento [dell'API Dei dati](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) di riferimento.
