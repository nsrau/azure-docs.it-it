---
title: Visualizzazioni del riquadro della cartella di lavoro di monitoraggio di Azure
description: Informazioni su tutte le visualizzazioni dei riquadri della cartella di lavoro di monitoraggio di Azure.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664394"
---
# <a name="tile-visualizations"></a>Tipi di visualizzazione dei riquadri

I riquadri sono un modo utile per presentare i dati di riepilogo nelle cartelle di lavoro di. L'immagine seguente mostra un caso d'uso comune dei riquadri con riepilogo a livello di app sopra una griglia dettagliata.

[![Screenshot della visualizzazione Riepilogo dei riquadri](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

Il supporto dei riquadri della cartella di lavoro mostra un titolo, un sottotitolo, testo di grandi dimensioni, icone, sfumature basate su metriche, linee/barre Spark, piè di pagina e così via.

## <a name="adding-a-tile"></a>Aggiunta di un riquadro

1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce _Modifica_ sulla barra degli strumenti.
2. Selezionare **Aggiungi** e quindi *Aggiungi query* per aggiungere un controllo query di log alla cartella di lavoro.
3. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
4. Utilizzare l'editor di query per immettere il KQL per l'analisi.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. Impostare dimensione su **completo**.
6. Impostare la visualizzazione sui **riquadri**.
7. Selezionare il pulsante **Impostazioni** riquadro per aprire il riquadro Impostazioni.
    1. In *title*impostare:
        * USA colonna: `name` .
    2. A *sinistra*, impostare:
        * USA colonna: `Requests` .
        * Renderer della colonna: `Big Number` .
        * Tavolozza colori: `Green to Red`
        * Valore minimo: `0` .
    3. In *basso*impostare:
        * USA colonna: `appName` .
8. Selezionare il pulsante **Salva e Chiudi** nella parte inferiore del riquadro.

[![Screenshot della visualizzazione Riepilogo dei riquadri con le impostazioni di query e riquadro sopra elencate.](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

I riquadri in modalità di lettura:

[![Screenshot della visualizzazione Riepilogo dei riquadri in modalità lettura.](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>Linee Spark nei riquadri

1. Impostare la cartella di lavoro in modalità di modifica facendo clic sulla voce _Modifica_ sulla barra degli strumenti.
2. Aggiungere un parametro di intervallo di tempo denominato `TimeRange` .
    1. Selezionare **Aggiungi** e quindi *Aggiungi parametri*.
    2. Nel controllo parametro selezionare **Aggiungi parametro**.
    3. Immettere `TimeRange` nel campo *nome parametro* e scegliere `Time range picker` per *tipo di parametro*.
    4. Selezionare **Save (Salva** ) nella parte superiore del riquadro e quindi fare clic su **modifica completato** nel controllo del parametro.
3. Selezionare **Aggiungi** quindi *Aggiungi query* per aggiungere un controllo query di log sotto il controllo parametro.
4. Selezionare il tipo di query come **log**, il tipo di risorsa (ad esempio, Application Insights) e le risorse di destinazione.
5. Utilizzare l'editor di query per immettere il KQL per l'analisi.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. Selezionare **Esegui query**. Assicurarsi di impostare il `TimeRange` valore desiderato prima di eseguire la query.
7. Impostare la *visualizzazione* su "tiles".
8. Impostare la *dimensione* su "full".
9. Selezionare **le impostazioni del riquadro**.
    1. In *riquadro*, impostare:
        * USA colonna: `name` .
    2. In *Subtile*impostare:
        *  USA colonna: `appNAme` .
    3. A *sinistra*, impostare:
        *  USA colonna: `Requests` .
        * Renderer della colonna: `Big Number` .
        * Tavolozza colori: `Green to Red` .
        * Valore minimo: `0` .
    4. In *basso*impostare:
        * USA colonna: `Trend` .
        * Renderer della colonna: `Spark line` .
        * Tavolozza colori: `Green to Red` .
        * Valore minimo: `0` .
10. Selezionare **Salva e Chiudi** nella parte inferiore del riquadro.

![Screenshot della visualizzazione dei riquadri con una linea Spark](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Dimensioni del riquadro

L'autore ha un'opzione per impostare la larghezza del riquadro nelle impostazioni del riquadro.

* `fixed` (impostazione predefinita)

    Il comportamento predefinito dei riquadri è la stessa larghezza fissa, approssimativamente 160 pixel in larghezza, più lo spazio intorno ai riquadri.

    ![Screenshot che Visualizza i riquadri a larghezza fissa](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Ogni titolo viene compattato o ampliato per adattarsi al contenuto, tuttavia, i riquadri sono limitati alla larghezza della visualizzazione dei riquadri (senza scorrimento orizzontale).

    ![Screenshot che Visualizza i riquadri di larghezza automatica](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Ogni titolo sarà sempre la larghezza completa della visualizzazione dei riquadri, un titolo per riga.

     ![Screenshot che Visualizza i riquadri di larghezza dimensione completa](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Passaggi successivi

* I riquadri supportano anche il renderer della barra composita. Per altre informazioni, vedere la [documentazione sulla barra composita](workbooks-composite-bar.md).
* Per ulteriori informazioni sui parametri temporali `TimeRange` , ad esempio visita la [documentazione sui parametri temporali della cartella di lavoro](workbooks-time.md)