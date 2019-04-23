---
title: 'Esercitazione: Visualizzare i dati da Esplora dati di Azure in Power BI'
description: Questa esercitazione fornisce informazioni su come connettersi a Esplora dati di Azure con Power BI e visualizzare i dati.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 70a06b75db9ff49222c2de4aa78519b32d863478
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049881"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Esercitazione: Visualizzare i dati da Esplora dati di Azure in Power BI

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Power BI è una soluzione di analisi aziendale che consente di visualizzare i dati e condividere i risultati all'interno dell'organizzazione. In questa esercitazione vengono anzitutto fornite informazioni su come eseguire il rendering degli oggetti visivi in Esplora dati di Azure. In secondo luogo, ci si connette a Esplora dati di Azure con Power BI, si compila un report basato sui dati di esempio e si pubblica il report nel servizio Power BI.

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare. Se non si è iscritti a Power BI Pro, [iscriversi a una versione di valutazione gratuita](https://app.powerbi.com/signupredirect?pbi_source=web) prima di iniziare.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Eseguire il rendering degli oggetti visivi in Esplora dati di Azure
> * Collegarsi a Esplora dati di Azure in Power BI Desktop
> * Lavorare con i dati in Power BI Desktop
> * Creare un report con gli oggetti visivi
> * Pubblicare e condividere il report

## <a name="prerequisites"></a>Prerequisiti

Oltre alle sottoscrizioni ad Azure e Power BI, per completare quest'esercitazione è necessario quanto segue:

* [Un cluster e un database di test](create-cluster-database-portal.md)

* [I dati di esempio di StormEvents](ingest-sample-data.md). [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selezionare **SCARICA GRATUITAMENTE**)

## <a name="render-visuals-in-azure-data-explorer"></a>Eseguire il rendering degli oggetti visivi in Esplora dati di Azure

Prima di passare a Power BI si vedrà come eseguire il rendering degli oggetti visivi in Esplora dati di Azure. Questa operazione è ideale per un'analisi rapida.

1. Accedere a [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Nel riquadro a sinistra, selezionare il database di test contenente i dati di esempio di StormEvents.

1. Incollare la query seguente nella finestra a destra e selezionare **Esegui**.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    Questa query effettua il conteggio degli eventi meteorologici per Stato. Esegue quindi il rendering creando un istogramma per tutti gli Stati che presentano oltre 1800 eventi meteorologici.

    ![Istogramma degli eventi](media/visualize-power-bi/events-column-chart.png)

1. Incollare la query seguente nella finestra a destra e selezionare **Esegui**.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    Questa query effettua il conteggio degli eventi meteorologici per tipo, per il mese di luglio e nello Stato di Washington. Esegue quindi il rendering creando un grafico a torta che mostra le percentuali per ogni tipo di evento.

    ![Grafico a torta degli eventi](media/visualize-power-bi/events-pie-chart.png)

Si passa ora a Power BI, sebbene gli oggetti visivi offrano molte più possibilità in Esplora dati di Azure.

## <a name="connect-to-azure-data-explorer"></a>Connettersi a Esplora dati di Azure

Collegarsi ora a Esplora dati di Azure in Power BI Desktop.

1. Nella scheda **Home** di Power BI Desktop selezionare **Recupera dati** e quindi **Altro**.

    ![Ottenere i dati](media/visualize-power-bi/get-data-more.png)

1. Cercare *Esplora dati di Azure*, selezionare **Esplora dati di Azure (beta)** e quindi **Connetti**.

    ![Cercare e ottenere i dati](media/visualize-power-bi/search-get-data.png)

1. Nella schermata **Anteprima connettore** selezionare **Continua**.

1. Nella schermata successiva immettere il nome dei cluster e del database di test. Il cluster deve essere nel formato `https://<ClusterName>.<Region>.kusto.windows.net`. Immettere *StormEvents* come nome della tabella. Lasciare i valori predefiniti per tutte le altre opzioni e selezionare **OK**.

    ![Opzioni di cluster, database e tabelle](media/visualize-power-bi/cluster-database-table.png)

1. Nella schermata di anteprima dei dati selezionare **Modifica**.

    La tabella verrà aperta nell'Editor di Power Query, in cui è possibile modificare righe e colonne prima di importare i dati.

## <a name="work-with-data-in-power-bi-desktop"></a>Lavorare con i dati in Power BI Desktop

Dopo aver creato una connessione a Esplora dati di Azure, si modificano i dati nell'Editor di Power Query. Rilasciare le righe contenenti valori Null nella colonna **BeginLat** e rilasciare interamente la colonna JSON **StormSummary** (riepilogo perturbazioni). Queste sono operazioni semplici, ma quando si importano i dati è possibile eseguire trasformazioni anche complesse.

1. Selezionare la freccia per la colonna **BeginLat**, deselezionare la casella di controllo **Null**, quindi selezionare **OK**.

    ![Filtrare la colonna](media/visualize-power-bi/filter-column.png)

1. Fare clic con il pulsante destro del mouse sull'intestazione della colonna **StormSummary** (Riepilogo perturbazioni) e quindi selezionare **Rimuovi**.

    ![Rimuovere la colonna](media/visualize-power-bi/remove-column.png)

1. Nel riquadro **IMPOSTAZIONI QUERY**, modificare il nome da *Query1* a *StormEvents*.

    ![Modificare il nome query](media/visualize-power-bi/query-name.png)

1. Nella scheda **Home** della barra multifunzione, selezionare **Chiudi e applica**.

    ![Chiudere e applicare](media/visualize-power-bi/close-apply.png)

    Power Query consente di applicare le modifiche e di importare quindi i dati di esempio in un *modello di dati*. I passaggi successivi illustrano come migliorare tale modello. Anche in questo caso, si tratta solo di un semplice esempio per dare un'idea delle operazioni possibili.

1. Sul lato sinistro della finestra principale, selezionare la visualizzazione dati.

    ![Visualizzazione dati](media/visualize-power-bi/data-view.png)

1. Nella scheda **Modellazione** della barra multifunzione, selezionare **Nuova colonna**.

    ![Nuova colonna](media/visualize-power-bi/new-column.png)

1. Immettere la seguente formula Data Analysis Expressions (DAX) nella barra della formula, quindi premere INVIO.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Barra della formula](media/visualize-power-bi/formula-bar.png)

    Questa formula crea la colonna *DurationHours* (durata in ore), che calcola il numero di ore della durata di ogni evento meteorologico. Si userà questa colonna in un oggetto visivo nella sezione successiva.

1. Scorrere verso il lato destro della tabella per visualizzare la colonna.

## <a name="create-a-report-with-visuals"></a>Creare un report con gli oggetti visivi

Ora che i dati sono stati importati e il modello di dati migliorato, è possibile compilare un report con gli oggetti visivi. Aggiungere un istogramma basato sulla durata degli eventi e una mappa che mostra i danni alle colture.

1. Sul lato sinistro della finestra, selezionare la visualizzazione report.

    ![Visualizzazione report](media/visualize-power-bi/report-view.png)

1. Nel riquadro**VISUALIZZAZIONI** selezionare l'istogramma a colonne di cluster.

    ![Aggiungere un istogramma](media/visualize-power-bi/add-column-chart.png)

    Un grafico vuoto viene aggiunto al pannello Canvas.

    ![Grafico vuoto](media/visualize-power-bi/blank-chart.png)

1. Nell'elenco **CAMPI** selezionare **DurationHours** (durata in ore) e **State** (Stato).

    ![Selezionare i campi](media/visualize-power-bi/select-fields.png)

    Ora è disponibile un grafico che mostra le ore totali degli eventi meteorologici per Stato nel corso di un anno.

    ![Istogramma della durata](media/visualize-power-bi/duration-column-chart.png)

1. Fare clic in qualsiasi punto del pannello Canvas al di fuori dell'istogramma.

1. Nel riquadro **VISUALIZZAZIONI** selezionare la mappa.

    ![Aggiungere la mappa](media/visualize-power-bi/add-map.png)

1. Nell'elenco **CAMPI** selezionare **CropDamage** (danni alle colture) e **State** (Stato). Ridimensionare la mappa in modo da poter visualizzare chiaramente gli Stati degli USA.

    ![Mappa dei danni alle colture](media/visualize-power-bi/crop-damage-map.png)

    La dimensione delle bolle rappresenta il valore in dollari dei danni alle colture. Passare il mouse sulle bolle per visualizzare i dettagli.

1. Spostare e ridimensionare gli oggetti visivi in modo da creare un report simile all'immagine seguente.

    ![Report completato](media/visualize-power-bi/finished-report.png)

1. Salvare il report con il nome *storm-events.pbix*.

## <a name="publish-and-share-the-report"></a>Pubblicare e condividere il report

Fino a questo punto, le operazioni effettuate in Power BI sono state tutte svolte in locale con Power BI Desktop. Ora si pubblica il report nel servizio Power BI in cui è possibile condividerlo con altri utenti.

1. Nella scheda **Home** della barra multifunzione di Power BI Desktop selezionare **Pubblica**.

    ![Pulsante Publish](media/visualize-power-bi/publish-button.png)

1. Se non è già stato effettuato l'accesso a Power BI, completare il processo di accesso.

1. Selezionare **Area di lavoro personale**, quindi **Seleziona**.

    ![Selezionare l'area di lavoro](media/visualize-power-bi/select-workspace.png)

1. Al termine della pubblicazione, selezionare **Apri storm-events.pbix in Power BI**.

    ![Pubblicazione riuscita](media/visualize-power-bi/publishing-succeeded.png)

    Il report verrà aperto nel servizio, con gli stessi oggetti visivi e layout definiti in Power BI Desktop.

1. Nell'angolo superiore destro del report selezionare **Condividi**.

    ![Pulsante per la condivisione](media/visualize-power-bi/share-button.png)

1. Nella schermata di **condivisione del report** aggiungere un collega nell'organizzazione, aggiungere una nota, quindi selezionare **Condividi**.

    ![Condivisione del report](media/visualize-power-bi/share-report.png)

    Se il collega dispone delle autorizzazioni appropriate, potrà accedere al report condiviso.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si desidera mantenere il report creato, è sufficiente eliminare il file *storm-events.pbix*. Se si desidera rimuovere il report pubblicato, seguire questa procedura.

1. In **Area di lavoro personale**, scorrere verso il basso fino a **Report** e individuare **storms-events**.

1. Selezionare i puntini di sospensione (**...**) accanto a **storm-events**, quindi selezionare **RIMUOVI**.

    ![Rimuovere il report](media/visualize-power-bi/remove-report.png)

1. Confermare la rimozione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scrivere query](write-queries.md)
