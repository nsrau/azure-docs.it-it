---
title: Come aggiungere un set di dati di riferimento all'ambiente Azure Time Series Insights | Microsoft Docs
description: In questo articolo viene descritto come aggiungere un set di dati di riferimento per aumentare i dati nell'ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3aca1f64d469ce228a4b2738c72c0b4bc1956051
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991287"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Creare un set di dati di riferimento per l'ambiente Time Series Insights usando il portale di Azure

In questo articolo viene descritto come aggiungere un set di dati di riferimento all'ambiente Azure Time Series Insights. Risulta utile associare i dati di riferimento ai dati di origine per aumentarne il valore.

Un set di dati di riferimento è una raccolta di elementi che aumentano gli eventi dell'origine evento. Il motore in ingresso di Time Series Insights crea un join tra ogni evento dell'origine evento e la riga di dati corrispondente nel set di dati di riferimento. Questo evento aumentato sarà quindi disponibile per le query. Questo join è basato sulla colonna o le colonne di chiavi primarie definite nel set di dati di riferimento.

Non viene creato un join dei dati di riferimento in maniera retroattiva. In questo modo, solo i dati in ingresso correnti e futuri vengono abbinati e aggiunti al set di date di riferimento, una volta che è stato configurato e caricato.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Informazioni sul modello di dati di riferimento di Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Aggiungere un set di dati di riferimento

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Individuare l'ambiente di Azure Time Series Insights esistente. Selezionare **Tutte le risorse** nel menu a sinistra del portale di Azure. Selezionare l'ambiente Time Series Insights.

1. Selezionare la pagina **Panoramica**. Individuare l'**URL di Time Series Insights Explorer** e aprire il collegamento.  

   Visualizzare la finestra di esplorazione per l'ambiente Time Series Insights.

1. Espandere il selettore di ambiente in Esplora Time Series Insights. Scegliere l'ambiente attivo. Selezionare l'icona dei dati di riferimento nell'angolo superiore destro della pagina dell'Explorer.

   [![aggiungere i dati di riferimento](media/add-reference-data-set/add-reference-data.png)](media/add-reference-data-set/add-reference-data.png#lightbox)

1. Selezionare il pulsante **+ Aggiungi un set di dati** per iniziare ad aggiungere un nuovo set di dati.

   [![Aggiungi set di dati](media/add-reference-data-set/add-data-set.png)](media/add-reference-data-set/add-data-set.png#lightbox)

1. Nella pagina **Nuovo set di dati di riferimento** scegliere il formato dei dati:

   - Scegliere **CSV** per i dati delimitati da virgole. La prima riga viene considerata una riga di intestazione.
   - Scegliere la **matrice JSON** per i dati formattati JSON (JavaScript Object Notation).

   [![scegliere il formato dati.](media/add-reference-data-set/add-data.png)](media/add-reference-data-set/add-data.png#lightbox)

1. Specificare i dati usando uno dei due metodi seguenti:

   - Incollare i dati nell'editor di testo. Quindi scegliere il pulsante **Analizza dati di riferimento**.
   - Selezionare il pulsante **Scegli file** per aggiungere i dati da un file di testo locale.

   Ad esempio, incollare i dati CSV: [![i dati CSV incollati](media/add-reference-data-set/csv-data-pasted.png)](media/add-reference-data-set/csv-data-pasted.png#lightbox)

   Ad esempio, incollare dati di matrici JSON: [![incollare i dati JSON](media/add-reference-data-set/json-data-pasted.png)](media/add-reference-data-set/json-data-pasted.png#lightbox)

   In caso di errore di analisi dei valori dei dati, l'errore viene visualizzato in rosso in fondo alla pagina, ad esempio: `CSV parsing error, no rows extracted`.

1. Se l'analisi dei dati riesce, viene visualizzata una griglia con le colonne e le righe che rappresentano i dati.  Esaminare la griglia dei dati per verificarne la correttezza.

   [![aggiungere i dati di riferimento](media/add-reference-data-set/parse-data.png)](media/add-reference-data-set/parse-data.png#lightbox)

1. Esaminare ogni colonna per verificare il tipo di dati presupposto e cambiarlo se necessario.  Selezionare il simbolo del tipo di dati nell'intestazione di colonna: **#** per il tipo di dati Double (dati numerici), **T|F** per il tipo di dati booleano o **Abc** per il tipo di dati stringa.

   [![scegliere i tipi di dati nelle intestazioni di colonna.](media/add-reference-data-set/choose-datatypes.png)](media/add-reference-data-set/choose-datatypes.png#lightbox)

1. Rinominare le intestazioni di colonna se necessario. Il nome di colonna chiave è necessario per creare un join con la proprietà corrispondente nell'origine evento. Verificare che i nomi di colonna chiave dei dati di riferimento corrispondano esattamente al nome evento nei dati in entrata, inclusa la combinazione di maiuscole/minuscole. I nomi non di colonna chiave vengono usati per aumentare i dati in entrata con i valori dei dati di riferimento corrispondenti.

1. Selezionare **Aggiungi una riga** o **Aggiungi una colonna** per aggiungere altri valori dei dati di riferimento, in base alle esigenze.

1. Digitare un valore nel campo **Filtra righe** per esaminare righe specifiche in base alle necessità. Il filtro è utile per esaminare i dati, ma non viene applicato quando si caricano i dati.

1. Assegnare un nome al set di dati specificandolo nel campo **Nome del set di dati** sopra la griglia dei dati.

    [![assegnare un nome al set di dati.](media/add-reference-data-set/name-reference-dataset.png)](media/add-reference-data-set/name-reference-dataset.png#lightbox)

1. Specificare la colonna **Chiave primaria** nel set di dati, selezionandola dall'elenco a discesa sopra la griglia dei dati.

    [![selezionare la colonna o le colonne chiave.](media/add-reference-data-set/set-primary-key.png)](media/add-reference-data-set/set-primary-key.png#lightbox)

    Facoltativamente, selezionare il pulsante **+** per aggiungere una colonna chiave secondaria, come una chiave primaria composta. Se occorre annullare la selezione, selezionare il valore vuoto nell'elenco a discesa per rimuovere la chiave secondaria.

1. Per caricare i dati, selezionare il pulsante **Carica righe**.

    [Caricamento![](media/add-reference-data-set/upload-rows.png)](media/add-reference-data-set/upload-rows.png#lightbox)

    La pagina conferma il completamento del caricamento e visualizza il messaggio **Il set di dati è stato caricato**.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i dati di riferimento](time-series-insights-manage-reference-data-csharp.md) a livello di codice.

* Per le informazioni di riferimento complete per l'API, vedere il documento relativo all'[API dei dati di riferimento](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api).
