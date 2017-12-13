---
title: Spark BI usando gli strumenti di visualizzazione di dati in Azure HDInsight | Microsoft Docs
description: Usare gli strumenti di visualizzazione di dati per l'analisi con Apache Spark BI nei cluster HDInsight
keywords: apache spark bi, spark bi, visualizzazione dei dati spark, spark business intelligence
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 18f495864befafd26e7adafb5c01612222d2cfdf
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI usando gli strumenti di visualizzazione di dati con Azure HDInsight

Informazioni sull'uso di [Microsoft Power BI](http://powerbi.microsoft.com) e [Tableau](http://www.tableau.com) per visualizzare i dati nel cluster Apache Spark in Azure HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* **Completare [Eseguire query interattive nei cluster Spark in HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) e [sottoscrizione di valutazione di Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (facoltativa).
* **Tableau**: [Tableau Desktop](http://www.tableau.com/products/desktop) e [driver ODBC di Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229).


## <a name="hivetable"></a>Verificare i dati

Il notebook Jupyter creato nell'[esercitazione precedente](apache-spark-load-data-run-query.md) include codice per creare una tabella `hvac`. Questa tabella si basa sui file con estensione csv in tutti i cluster HDInsight Spark all'indirizzo **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Usare la procedura seguente per verificare i dati.

1. Dal notebook di Jupyter incollare il codice seguente e quindi premere **MAIUSC + INVIO**. Il codice verifica l'esistenza delle tabelle.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    L'output è simile al seguente:

    ![Mostra tabelle in Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Se il notebook è stato chiuso prima di iniziare questa esercitazione, `hvactemptable` è stato eliminato e non è stato quindi incluso nell'output.
    Dagli strumenti BI è possibile accedere solo alle tabelle Hive archiviate nel metastore (indicato da **False** nella colonna **isTemporary**). In questa esercitazione si esegue la connessione alla tabella **hvac** creata.

2. Incollare il codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO**. Il codice verifica i dati nella tabella.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    L'output è simile al seguente:

    ![Visualizzazione delle righe dalla tabella hvac in Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Dal menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi). Arrestare il notebook per rilasciare le risorse. 















## <a name="powerbi"></a>Usare Power BI

In questa sezione si usa Power BI per creare visualizzazioni, report e dashboard dai dati del cluster Spark. 

### <a name="create-a-report-in-power-bi-desktop"></a>Creare un report in Power BI Desktop
I primi passaggi nell'uso di Spark sono la connessione al cluster in Power BI Desktop, il caricamento dei dati dal cluster e la creazione di una visualizzazione basata su tali dati.

> [!NOTE]
> Il connettore illustrato in questo articolo è attualmente in anteprima. Inviare eventuali commenti tramite il sito della [Community di Power BI](https://community.powerbi.com/) o il forum [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Aprire [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. Nella scheda **Home** fare clic su **Recupera dati** e quindi su **Altro**.

    ![Ottenere i dati in Power BI Desktop da HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Ottenere i dati in Power BI Desktop da HDInsight Apache Spark")


2. Immettere `Spark` nella casella di ricerca, selezionare **Azure HDInsight Spark (beta)** e quindi fare clic su **Connetti**.

    ![Ottenere i dati in Power BI da Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Ottenere i dati in Power BI da Apache Spark BI")

3. Immettere l'URL del cluster (nel formato `mysparkcluster.azurehdinsight.net`), selezionare **DirectQuery** e quindi fare clic su **OK**.

    Con Spark è possibile usare entrambe le modalità di connettività dati. Se si usa DirectQuery, le modifiche si riflettono nei report senza l'aggiornamento dell'intero set di dati. Se si importano i dati, per visualizzare le modifiche è necessario aggiornare il set di dati. Per altre informazioni su come e quando usare DirectQuery, vedere [Uso di DirectQuery in Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Immettere le informazioni account di accesso HDInsight, quindi fare clic su **Connetti**. Il nome account predefinito è *admin*.

5. Selezionare la tabella `hvac`, attendere la visualizzazione dell'anteprima dei dati e quindi fare clic su **Carica**.

    ![Nome utente e password del cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nome utente e password del cluster Spark")

    Power BI Desktop ha le informazioni necessarie per connettersi al cluster Spark e caricare dati dalla tabella `hvac`. La tabella e le relative colonne sono visualizzate nel riquadro **Campi**.  Vedere lo screenshot seguente.

6. Visualizzare la variazione tra temperatura di destinazione e temperatura effettiva per ogni edificio: 

    1. Nel riquadro **VISUALIZZAZIONI** selezionare **Grafico ad aree**. 
    2. Trascinare il campo **BuildingID** in **Asse**e trascinare i campi **ActualTemp** e **TargetTemp** in **Valore**.

        ![Creare le visualizzazioni di dati usando Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Creare le visualizzazioni di dati usando Apache Spark BI")

        Il diagramma è simile al seguente:

        ![Creare le visualizzazioni di dati usando Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Creare le visualizzazioni di dati usando Apache Spark BI")

        Per impostazione predefinita, la visualizzazione mostra la somma di **ActualTemp** e **TargetTemp**. Fare clic sulla freccia verso il basso accanto ad **ActualTemp** e a **TragetTemp** nel riquadro Visualizzazioni per osservare che è selezionato **Somma**.

    3. Fare clic sulle frecce verso il basso accanto ad **ActualTemp** e a **TragetTemp** nel riquadro Visualizzazioni e selezionare **Media** per ottenere una media della temperatura effettiva e di quella di destinazione per ogni edificio.

        ![Creare le visualizzazioni di dati usando Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Creare le visualizzazioni di dati usando Apache Spark BI")

        La visualizzazione dei dati sarà simile a quella nello screenshot. Spostare il cursore sopra la visualizzazione per ottenere suggerimenti con i dati rilevanti.

        ![Creare le visualizzazioni di dati usando Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Creare le visualizzazioni di dati usando Apache Spark BI")

7. Fare clic su **File**, su **Salva** e quindi immettere il nome `BuildingTemperature.pbix` per il file. 

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

L'oggetto visivo è stato aggiunto al dashboard. È possibile aggiungere altri oggetti visivi al report e aggiungerli poi allo stesso dashboard. Per altre informazioni su report e dashboard, vedere [Report in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)e [Dashboard in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="tableau"></a>Usare Tableau Desktop 

> [!NOTE]
> Questa sezione è applicabile solo per i cluster Spark 1.5.2 creati in Azure HDInsight.
>
>

1. Installare [Tableau Desktop](http://www.tableau.com/products/desktop) nel computer in cui si sta eseguendo questa esercitazione di Apache Spark BI.

2. Assicurarsi che nel computer sia installato un driver ODBC di Microsoft Spark. È possibile installare il driver da [qui](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Avviare Tableau Desktop. Nel riquadro di sinistra, nell'elenco dei server a cui connettersi, fare clic su **Spark SQL**. Se Spark SQL non è presente per impostazione predefinita nel riquadro sinistro, è possibile trovarlo facendo clic su **More Servers**.
2. Nella finestra di dialogo della connessione a Spark SQL, specificare i valori come illustrato nello screenshot e quindi fare clic su **OK**.

    ![Connettersi a un cluster per Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connettersi a un cluster per Apache Spark BI")

    L'elenco a discesa di autenticazione indica **Microsoft Azure HDInsight Service** come opzione solo se nel computer è installato il [driver ODBC di Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) .
3. Nella schermata successiva fare clic sull'icona **Find** (Trova) dal menu a discesa **Schema** e quindi su **default** (predefinito).

    ![Trovare lo schema per Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Trovare lo schema per Apache Spark BI")
4. Per il campo **Table** (Tabella) fare di nuovo clic sull'icona **Find** (Trova) per elencare tutte le tabelle Hive disponibili nel cluster. Verrà visualizzata la tabella **hvac** creata in precedenza tramite il notebook.

    ![Trovare la tabella per Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Trovare la tabella per Apache Spark BI")
5. Trascinare e rilasciare la tabella nella casella superiore a destra. Tableau importa i dati e lo schema viene visualizzato come evidenziato dalla casella rossa.

    ![Aggiungere tabelle a Tableau per Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Aggiungere tabelle a Tableau per Apache Spark BI")
6. Fare clic sulla scheda **Sheet1** nella parte inferiore sinistra. Apportare una visualizzazione che mostra la temperatura effettiva e di destinazione media per tutti gli edifici e per ogni data. Trascinare **Date** (Data) e **Building ID** (ID compilazione) su **Columns** (Colonne) e **Actual Temp**/**Target Temp** (Temp effettiva/Temp di destinazione) su **Rows** (Righe). In **Marks** (Marcatori) selezionare **Area** per usare una mappa di area per la visualizzazione dei dati Spark.

     ![Aggiungere campi per la visualizzazione dei dati Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Aggiungere campi per la visualizzazione dei dati Spark")
7. Per impostazione predefinita, i campi di temperatura vengono visualizzati come funzione di aggregazione. Se si vuole visualizzare la media delle temperature invece, è possibile farlo dall'elenco a discesa, come illustrato nello screenshot seguente.

    ![Eseguire la media della temperatura per la visualizzazione dei dati Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Eseguire la media della temperatura per la visualizzazione dei dati Spark")

8. È anche possibile super-imporre una mappa di temperatura rispetto a un'altra per farsi un'idea migliore della differenza tra le temperature effettive e di destinazione. Spostare il puntatore del mouse nell'angolo del grafico ad aree inferiore fino a visualizzare la forma evidenziata in un cerchio rosso. Trascinare il grafico sull'altro grafico nella parte superiore e rilasciare il mouse quando viene visualizzata la forma evidenziata in un rettangolo rosso.

    ![Unire mappe per la visualizzazione dei dati Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Unire mappe per la visualizzazione dei dati Spark")

     La visualizzazione dei dati dovrebbe cambiare come mostrato nello screenshot:

    ![Output di Tableau per la visualizzazione dei dati Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Output di Tableau per la visualizzazione dei dati Spark")
9. Fare clic su **Save** per salvare il foglio di lavoro. È possibile creare dashboard e aggiungervi uno o più fogli.

## <a name="next-steps"></a>Passaggi successivi

Finora è stato descritto come creare un cluster, frame di dati Spark per i dati query e quindi accedere ai dati dagli strumenti di BI. È ora possibile esaminare le istruzioni su come gestire le risorse del cluster ed eseguire il debug dei processi in esecuzione in un cluster HDInsight Spark.

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](apache-spark-job-debugging.md)

