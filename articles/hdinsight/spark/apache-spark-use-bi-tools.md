---
title: 'Esercitazione: Analizzare dati di Apache Spark usando Power BI in Azure HDInsight '
description: 'Esercitazione: Usare Microsoft Power BI per visualizzare i dati Apache Spark archiviati nei cluster HDInsight'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/16/2019
ms.openlocfilehash: 319668ac81b390f9a8bf70da36dd3cf1508f5c37
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885169"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Esercitazione: Analizzare dati di Apache Spark usando Power BI in HDInsight

In questa esercitazione si apprenderà come usare [Microsoft Power BI](https://powerbi.microsoft.com/) per visualizzare i dati nel cluster Apache Spark in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Visualizzare i dati di Spark usando Power BI

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

* Completare l'articolo [Esercitazione: Caricare i dati ed eseguire query in un cluster Apache Spark in Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Facoltativo: [Sottoscrizione di valutazione di Power BI](https://app.powerbi.com/signupredirect?pbi_source=web)

## <a name="verify-the-data"></a>Verificare i dati

Il [notebook Jupyter](https://jupyter.org/) creato nell'[esercitazione precedente](apache-spark-load-data-run-query.md) include il codice per creare una tabella `hvac`. Questa tabella si basa sul file CSV disponibile in tutti i cluster HDInsight Spark all'indirizzo `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Usare la procedura seguente per verificare i dati.

1. Dal notebook di Jupyter incollare il codice seguente e quindi premere **MAIUSC + INVIO**. Il codice verifica l'esistenza delle tabelle.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    L'output è simile al seguente:

    ![Mostra tabelle in Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Se il notebook è stato chiuso prima di iniziare questa esercitazione, `hvactemptable` è stato eliminato e non è stato quindi incluso nell'output.  Dagli strumenti BI è possibile accedere solo alle tabelle Hive archiviate nel metastore (indicato da **False** nella colonna **isTemporary**). In questa esercitazione si esegue la connessione alla tabella **hvac** creata.

2. Incollare il codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**. Il codice verifica i dati nella tabella.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    L'output è simile al seguente:

    ![Visualizzazione delle righe dalla tabella hvac in Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Nel menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi). Arrestare il notebook per rilasciare le risorse.

## <a name="visualize-the-data"></a>Visualizzare i dati

In questa sezione si usa Power BI per creare visualizzazioni, report e dashboard dai dati del cluster Spark.

### <a name="create-a-report-in-power-bi-desktop"></a>Creare un report in Power BI Desktop

I primi passaggi nell'uso di Spark sono la connessione al cluster in Power BI Desktop, il caricamento dei dati dal cluster e la creazione di una visualizzazione basata su tali dati.

> [!NOTE]  
> Il connettore illustrato in questo articolo è attualmente in anteprima. Inviare eventuali commenti tramite il sito della [Community di Power BI](https://community.powerbi.com/) o il forum [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Aprire Power BI Desktop. Se si apre la schermata iniziale, chiuderla.

2. Nella scheda **Home** passare a **Recupera dati** > **Altro**.

    ![Ottenere i dati in Power BI Desktop da HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Ottenere i dati in Power BI Desktop da HDInsight Apache Spark")

3. Immettere `Spark` nella casella di ricerca, selezionare **Azure HDInsight Spark** e quindi selezionare **Connetti**.

    ![Ottenere i dati in Power BI da Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Ottenere i dati in Power BI da Apache Spark BI")

4. Immettere l'URL del cluster (nel formato `mysparkcluster.azurehdinsight.net`) nella casella di testo **Server**.

5. In **Modalità Connettività dati** selezionare **DirectQuery**. Selezionare **OK**.

    Con Spark è possibile usare entrambe le modalità di connettività dati. Se si usa DirectQuery, le modifiche si riflettono nei report senza l'aggiornamento dell'intero set di dati. Se si importano i dati, per visualizzare le modifiche è necessario aggiornare il set di dati. Per altre informazioni su come e quando usare DirectQuery, vedere [Uso di DirectQuery in Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Immettere le informazioni dell'account di accesso di HDInsight, quindi selezionare **Connetti**. Il nome account predefinito è *admin*.

7. Selezionare la tabella `hvac`, attendere la visualizzazione dell'anteprima dei dati e quindi selezionare **Carica**.

    ![Nome utente e password del cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nome utente e password del cluster Spark")

    Power BI Desktop ha le informazioni necessarie per connettersi al cluster Spark e caricare dati dalla tabella `hvac`. La tabella e le relative colonne sono visualizzate nel riquadro **Campi**.

8. Visualizzare la variazione tra temperatura di destinazione e temperatura effettiva per ogni edificio:

    1. Nel riquadro **VISUALIZZAZIONI** selezionare **Grafico ad aree**.

    2. Trascinare il campo **BuildingID** in **Asse**e trascinare i campi **ActualTemp** e **TargetTemp** in **Valore**.

        ![aggiungere colonne di valori](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "aggiungere colonne di valori")

        Il diagramma è simile al seguente:

        ![somma grafico ad area](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "somma grafico ad area")

        Per impostazione predefinita, la visualizzazione mostra la somma di **ActualTemp** e **TargetTemp**. Selezionare la freccia verso il basso accanto ad **ActualTemp** e a **TragetTemp** nel riquadro Visualizzazioni per osservare che è selezionato **Somma**.

    3. Selezionare le frecce verso il basso accanto ad **ActualTemp** e a **TragetTemp** nel riquadro Visualizzazioni e quindi selezionare **Media** per ottenere una media della temperatura effettiva e di quella di destinazione per ogni edificio.

        ![media dei valori](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "media dei valori")

        La visualizzazione dei dati sarà simile a quella nello screenshot. Spostare il cursore sopra la visualizzazione per ottenere suggerimenti con i dati rilevanti.

        ![grafico ad area](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "grafico ad area")

9. Passare a **File** > **Salva**, immettere il nome `BuildingTemperature` per il file e quindi selezionare **Salva**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Pubblicare il report nel servizio Power BI (facoltativo)

Il servizio Power BI consente di condividere report e dashboard nell'organizzazione. In questa sezione prima si pubblicano i set di dati e il report, quindi si aggiunge il report a un dashboard. I dashboard sono in genere usati per concentrare l'attenzione su un subset di dati di un report. Nel report in uso è presente solo una visualizzazione, ma è comunque utile eseguire i passaggi.

1. Aprire Power BI Desktop.
2. Nella scheda **Home** fare clic su **Pubblica**.

    ![Pubblicazione da Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Pubblicazione da Power BI Desktop")

2. Selezionare un'area di lavoro in cui pubblicare il set di dati e il report e quindi fare clic su **Seleziona**. Nella figura seguente è selezionato il valore predefinito **Area di lavoro personale**.

    ![Selezionare l'area di lavoro in cui pubblicare il set di dati e il report](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Selezionare l'area di lavoro in cui pubblicare il set di dati e il report") 

3. Al termine della pubblicazione, fare clic su **Apri 'BuildingTemperature.pbix' in Power BI**.

    ![Pubblicazione riuscita, fare clic per immettere le credenziali](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Pubblicazione riuscita, fare clic per immettere le credenziali") 

4. Nel servizio Power BI fare clic su **Immettere le credenziali**.

    ![Immettere le credenziali nel servizio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Immettere le credenziali nel servizio Power BI")

5. Fare clic su **Modifica credenziali**.

    ![Modificare le credenziali nel servizio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Modificare le credenziali nel servizio Power BI")

6. Immettere le informazioni account di accesso HDInsight e quindi fare clic su **Accedi**. Il nome account predefinito è *admin*.

    ![Accesso al cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Accesso al cluster Spark")

7. Nel riquadro a sinistra passare ad **Aree di lavoro** > **Area di lavoro personale** > **REPORT** e quindi fare clic su **BuildingTemperature**.

    ![Report elencato tra i report nel riquadro a sinistra](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Report elencato tra i report nel riquadro a sinistra")

    Il report **BuildingTemperature** è visualizzato anche in **SET DI DATI** nel riquadro a sinistra.

    L'oggetto visivo creato in Power BI Desktop è ora disponibile nel servizio Power BI. 

8. Passare il cursore sopra la visualizzazione e quindi fare clic sull'icona della puntina nell'angolo in alto a destra.

    ![Report nel servizio Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Report nel servizio Power BI")

9. Selezionare "Nuovo dashboard", immettere il nome `Building temperature` e quindi fare clic su **Aggiungi**.

    ![Aggiunta al nuovo dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Aggiunta al nuovo dashboard")

10. All'interno del report fare clic su **Vai al dashboard**. 

L'oggetto visivo è stato aggiunto al dashboard. È possibile aggiungere altri oggetti visivi al report e aggiungerli poi allo stesso dashboard. Per altre informazioni su report e dashboard, vedere [Report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) e [Dashboard in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, è consigliabile eliminare il cluster. Con HDInsight, i dati vengono archiviati in Archiviazione di Azure ed è possibile eliminare tranquillamente un cluster quando non viene usato. Vengono addebitati i costi anche per i cluster HDInsight che non sono in uso. Poiché i costi per il cluster sono decisamente superiori a quelli per l'archiviazione, economicamente ha senso eliminare i cluster quando non vengono usati.

Per eliminare un cluster, vedere [Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare [Microsoft Power BI](https://powerbi.microsoft.com/) per visualizzare i dati nel cluster Apache Spark in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Passare all'articolo successivo per scoprire come eseguire il pull dei dati registrati in Spark in uno strumento di analisi BI come Power BI.

> [!div class="nextstepaction"]
> [Eseguire un processo di streaming Apache Spark](apache-spark-eventhub-streaming.md)