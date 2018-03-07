---
title: Come aggiungere un set di dati di riferimento all'ambiente Azure Time Series Insights
description: In questo articolo viene descritto come aggiungere un set di dati di riferimento per aumentare i dati nell'ambiente Azure Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: MicrosoftDocs/tsidocs
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 02/15/2018
ms.openlocfilehash: e0d11f253d5aa143ff636c4dc8dff7665a80360e
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Creare un set di dati di riferimento per l'ambiente Time Series Insights usando il portale di Azure

In questo articolo viene descritto come aggiungere un set di dati di riferimento all'ambiente Azure Time Series Insights. Risulta utile associare i dati di riferimento ai dati di origine per aumentarne il valore.

Un set di dati di riferimento è una raccolta di elementi che aumentano gli eventi dell'origine evento. Il motore in ingresso di Time Series Insights crea un join tra ogni evento dell'origine evento e la riga di dati corrispondente nel set di dati di riferimento. Questo evento aumentato sarà quindi disponibile per le query. Questo join è basato sulla colonna o le colonne di chiavi primarie definite nel set di dati di riferimento.

Non viene creato un join dei dati di riferimento in maniera retroattiva. Questo significa che solo i dati in ingresso correnti e futuri vengono uniti in join al set di dati di riferimento, appena viene configurato e caricato.

## <a name="add-a-reference-data-set"></a>Aggiungere un set di dati di riferimento

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Individuare l'ambiente Time Series Insights esistente. Fare clic su **Tutte le risorse** nel menu sul lato sinistro del portale di Azure. Selezionare l'ambiente Time Series Insights.

3. Selezionare la pagina **Panoramica**. Individuare l'**URL di Time Series Insights Explorer** e aprire il collegamento.  

   Visualizzare l'Explorer del proprio ambiente Time Series Insights (TSI).

4. Espandere il selettore di ambiente in TSI Explorer. Scegliere l'ambiente attivo. Selezionare l'icona dei dati di riferimento nell'angolo superiore destro della pagina dell'Explorer.

   ![Aggiungere i dati di riferimento](media/add-reference-data-set/add_reference_data.png)

5. Selezionare il pulsante **+ Aggiungi un set di dati** per iniziare ad aggiungere un nuovo set di dati.

   ![Aggiungi un set di dati](media/add-reference-data-set/add_data_set.png)

6. Nella pagina **Nuovo set di dati di riferimento** scegliere il formato dei dati: 
   - Scegliere **CSV** per i dati delimitati da virgole. La prima riga viene considerata una riga di intestazione. 
   - Scegliere **Matrice JSON** per i dati in formato JavaScript Object Notation (JSON).

   ![Scegliere il formato dei dati.](media/add-reference-data-set/add_data.png)

7. Specificare i dati usando uno dei due metodi seguenti:
   - Incollare i dati nell'editor di testo. Quindi scegliere il pulsante **Analizza dati di riferimento**.
   - Selezionare il pulsante **Scegli file** per aggiungere i dati da un file di testo locale. 

   Ad esempio, incollare dati CSV: ![Dati CSV incollati](media/add-reference-data-set/csv_data_pasted.png)

   Ad esempio, incollare dati di matrice JSON: ![Dati di matrice JSON incollati](media/add-reference-data-set/json_data_pasted.png)

   In caso di errore di analisi dei valori dei dati, l'errore viene visualizzato in rosso in fondo alla pagina, ad esempio: `CSV parsing error, no rows extracted`.

8. Se l'analisi dei dati riesce, viene visualizzata una griglia con le colonne e le righe che rappresentano i dati.  Esaminare la griglia dei dati per verificarne la correttezza.

   ![Aggiungere i dati di riferimento](media/add-reference-data-set/parse_data.png)

9. Esaminare ogni colonna per verificare il tipo di dati presupposto e cambiarlo se necessario.  Selezionare il simbolo del tipo di dati nell'intestazione di colonna: **#** per il tipo di dati Double (dati numerici), **T|F** per il tipo di dati booleano o **Abc** per il tipo di dati stringa.

   ![Scegliere i tipi di dati nelle intestazioni di colonna.](media/add-reference-data-set/choose_datatypes.png)

10. Rinominare le intestazioni di colonna se necessario. Il nome di colonna chiave è necessario per creare un join con la proprietà corrispondente nell'origine evento. Verificare che i nomi di colonna chiave dei dati di riferimento corrispondano esattamente al nome evento nei dati in entrata, inclusa la combinazione di maiuscole/minuscole. I nomi non di colonna chiave vengono usati per aumentare i dati in entrata con i valori dei dati di riferimento corrispondenti.

11. Fare clic su **Aggiungi una riga** o **Aggiungi una colonna** per aggiungere altri valori di dati di riferimento, se necessario.

12. Digitare un valore nel campo **Filtra righe** per esaminare righe specifiche in base alle necessità. Il filtro è utile per esaminare i dati, ma non viene applicato quando si caricano i dati.
 
13. Assegnare un nome al set di dati specificandolo nel campo **Nome del set di dati** sopra la griglia dei dati.

   ![Assegnare un nome al set di dati.](media/add-reference-data-set/name_reference_dataset.png)

14. Specificare la colonna **Chiave primaria** nel set di dati, selezionandola dall'elenco a discesa sopra la griglia dei dati.

   ![Selezionare la colonna o le colonne chiave.](media/add-reference-data-set/set_primary_key.png)

   Facoltativamente, selezionare il pulsante **+** per aggiungere una colonna chiave secondaria, come una chiave primaria composta. Se occorre annullare la selezione, selezionare il valore vuoto nell'elenco a discesa per rimuovere la chiave secondaria.

15.  Per caricare i dati, selezionare il pulsante **Carica righe**.

   ![Caricamento](media/add-reference-data-set/upload_rows.png)

   La pagina conferma il completamento del caricamento e visualizza il messaggio **Il set di dati è stato caricato**.

## <a name="next-steps"></a>Passaggi successivi
* [Gestire i dati di riferimento](time-series-insights-manage-reference-data-csharp.md) a livello di codice.
* Per le informazioni di riferimento complete per l'API, vedere il documento relativo all'[API dei dati di riferimento](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
