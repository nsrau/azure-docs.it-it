---
title: Spark BI usando gli strumenti di visualizzazione di dati in Azure HDInsight | Microsoft Docs
description: Usare gli strumenti di visualizzazione di dati per l'analisi con Apache Spark BI nei cluster HDInsight
keywords: apache spark bi, spark bi, visualizzazione dei dati spark, spark business intelligence
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.openlocfilehash: 869a000909813e607620c47ef802b4043e37dfa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI usando gli strumenti di visualizzazione di dati con Azure HDInsight

Informazioni su come usare gli strumenti di visualizzazione dei dati, come Power BI e Tableau per analizzare un set di dati di esempio non elaborati usando Apache Spark BI nei cluster HDInsight.

> [!NOTE]
> La connettività con gli strumenti di business intelligence descritta in questo articolo non è supportata su Spark 2.1 in Azure HDInsight 3.6 Preview. Solo le versioni 1.6 e 2.0 di Spark (HDInsight 3.4 e 3.5 rispettivamente) sono supportate.
>

Questa esercitazione è disponibile anche come notebook di Jupyter in un cluster Spark HDInsight. L'esperienza offerta dal notebook consente di eseguire i frammenti di codice Python dal notebook stesso. Per eseguire l'esercitazione da un notebook, creare un cluster Spark, avviare un notebook di Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`) e quindi eseguire il notebook **Use BI tools with Apache Spark on HDInsight.ipynb** nella cartella **Python**.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo relativo alla [creazione di cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).


## <a name="hivetable"></a>Preparare i dati per la visualizzazione dei dati di Spark

In questa sezione viene usato il notebook di [Jupyter](https://jupyter.org) da un cluster Spark in HDInsight per eseguire i processi che elaborano i dati di esempio non elaborati e li salvano come tabella. I dati di esempio sono un file con estensione csv (hvac.csv) disponibile in tutti i cluster per impostazione predefinita. Dopo avere salvato i dati come tabella, nella sezione successiva vengono usati gli strumenti di business intelligence per connettersi alla tabella ed eseguire visualizzazioni di dati.

> [!NOTE]
> Se si eseguono i passaggi descritti in questo articolo dopo aver completato le istruzioni in [Eseguire query interattive in un cluster HDInsight Spark](hdinsight-apache-spark-load-data-run-query.md), è possibile procedere al passaggio 8 riportato di seguito.
>

1. Dalla Schermata iniziale del [portale di Azure](https://portal.azure.com/) fare clic sul riquadro del cluster Spark, se è stato aggiunto sulla Schermata iniziale. È anche possibile passare al cluster da **Esplora tutto** > **Cluster HDInsight**.   

2. Nel pannello del cluster Spark fare clic su **Dashboard cluster** e quindi su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

   > [!NOTE]
   > È anche possibile raggiungere il notebook di Jupyter per il cluster aprendo l'URL seguente nel browser. Sostituire **CLUSTERNAME** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Creare un notebook. Fare clic su **Nuovo** e quindi su **PySpark**.

    ![Creare un notebook di Jupyter per Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/create-jupyter-notebook-for-spark-bi.png "Creare un notebook di Jupyter per Apache Spark BI")

4. Un nuovo notebook verrà creato e aperto con il nome Untitled.pynb. Fare clic sul nome del notebook nella parte superiore e immettere un nome descrittivo.

    ![Specificare un nome per il notebook per Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/jupyter-notebook-name-for-spark-bi.png "Specificare un nome per il notebook per Apache Spark BI")

5. Poiché il notebook è stato creato tramite il kernel PySpark, non è necessario creare contesti in modo esplicito. I contesti Spark e Hive vengono creati automaticamente quando si esegue la prima cella di codice. È possibile iniziare con l'importazione dei tipi necessari per questo scenario. A tale scopo, posizionare il cursore nella cella e premere **MAIUSC + INVIO**.

        from pyspark.sql import *

6. Caricare i dati di esempio in una tabella temporanea. Quando si crea un cluster Spark in HDInsight, il file di dati di esempio **hvac.csv** viene copiato nell'account di archiviazione associato in **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    In una cella vuota incollare il frammento di codice seguente e premere **MAIUSC+INVIO**. Questo frammento di codice consente di registrare i dati in una tabella denominata **hvac**.

        # Create an RDD from sample data
        hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))

        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. Verificare che la tabella è stata creata correttamente. È possibile usare il comando speciale `%%sql` per eseguire direttamente query Hive. Per altre informazioni sul magic `%%sql` e sugli altri magic disponibili con il kernel PySpark, vedere [Kernel disponibili per notebook di Jupyter nel cluster Spark per HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SHOW TABLES

    Viene visualizzato un output come il seguente:

        +---------------+-------------+
        |tableName      |isTemporary  |
        +---------------+-------------+
        |hvactemptable  |true        |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    Solo le tabelle in cui il valore sotto la colonna **isTemporary** è false sono tabelle hive che vengono archiviate in metastore ed è possibile accedervi con strumenti di business intelligence. In questa esercitazione si esegue la connessione alla tabella **hvac** creata.

8. Verificare che la tabella contiene i dati desiderati. In una cella vuota del notebook copiare il frammento di codice seguente e premere **MAIUSC+INVIO**.

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Arrestare il notebook per rilasciare le risorse. A tale scopo, dal menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi).

## <a name="powerbi"></a>Usare Power BI per la visualizzazione dei dati di Spark

> [!NOTE]
> Questa sezione è applicabile solo a Spark 1.6 in HDInsight 3.4 e Spark 2.0 in HDInsight 3.5.
>
>

Dopo aver salvato i dati come una tabella, è possibile usare Power BI per connettersi ai dati e visualizzarli per creare rapporti, dashboard e così via.

1. Accertarsi di avere accesso a Power BI. È possibile ottenere una sottoscrizione di anteprima gratuita di Power BI da [http://www.powerbi.com/](http://www.powerbi.com/).

2. Accedere a [Power BI](http://www.powerbi.com/).

3. Nella parte inferiore del riquadro di sinistra fare clic su **Recupera dati**.

4. Nella pagina **Recupera dati** sotto **Connettersi ai dati o importarli** per **Database** fare clic su **Recupera**.

    ![Ottenere i dati in Power BI per Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Ottenere i dati in Power BI per Apache Spark BI")

5. Nella schermata successiva fare clic su **Spark in Azure HDInsight** e quindi su **Connetti**. Quando richiesto, immettere l'URL del cluster (`mysparkcluster.azurehdinsight.net`) e le credenziali per connettersi al cluster.

    ![Connettersi ad Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Connettersi ad Apache Spark BI")

    Dopo aver stabilito la connessione, Power BI avvia l'importazione di dati dal cluster Spark in HDInsight.

6. Power BI importa i dati e aggiunge un set di dati **Spark** nell'intestazione **Set di dati**. Fare clic sul set di dati per aprire un nuovo foglio di lavoro per la visualizzazione dei dati. È inoltre possibile salvare il foglio di lavoro come report. A tale scopo, scegliere **Salva** dal menu **File**.

    ![Riquadro di Apache Spark BI nel dashboard di Power BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-tile-dashboard.png "Riquadro di Apache Spark BI nel dashboard di Power BI")
7. Si noti che nell'elenco **Campi** sulla destra è presente la tabella **hvac** creata prima. Espandere la tabella per visualizzare i campi della tabella, come definito in precedenza nel notebook.

      ![Elencare le tabelle nel dashboard di Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "Elencare le tabelle nel dashboard di Apache Spark BI")

8. Creare una visualizzazione per mostrare la variazione tra temperatura di destinazione e temperatura effettiva per ogni edificio. Per visualizzare i dati, selezionare **Grafico ad aree** (visualizzato nella casella rossa). Per definire l'asse, trascinare e rilasciare il campo **BuildingID** nei campi **Asse** e **ActualTemp**/**TargetTemp** in **Valore**.

    ![Creare le visualizzazioni di dati usando Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Creare le visualizzazioni di dati usando Apache Spark BI")

9. Per impostazione predefinita, la visualizzazione mostra la somma di **ActualTemp** e **TargetTemp**. Per entrambi i campi, dall'elenco a discesa selezionare **Media** per ottenere una media della temperature effettiva e di destinazione per entrambe le compilazioni.

    ![Creare le visualizzazioni di dati usando Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Creare le visualizzazioni di dati usando Apache Spark BI")

10. La visualizzazione dei dati deve essere simile a quella nello screenshot. Spostare il cursore sopra la visualizzazione per ottenere suggerimenti con i dati rilevanti.

    ![Creare le visualizzazioni di dati usando Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Creare le visualizzazioni di dati usando Apache Spark BI")

11. Fare clic su **Salva** nel menu principale e specificare un nome per il report. È inoltre possibile aggiungere l'oggetto visivo. Quando si aggiunge una visualizzazione, viene archiviata nel dashboard consentendo di tenere traccia dell'ultimo valore in modo immediato.

   È possibile aggiungere tutte le visualizzazioni per lo stesso set di dati e aggiungerle al dashboard per uno snapshot dei dati. Inoltre, i cluster Spark in HDInsight sono direttamente connessi a Power BI. Ciò assicura che i dati di Power BI siano sempre aggiornati dal cluster e non è quindi necessario pianificare gli aggiornamenti per il set di dati.

## <a name="tableau"></a>Usare Tableau Desktop per la visualizzazione dei dati Spark

> [!NOTE]
> Questa sezione è applicabile solo per i cluster Spark 1.5.2 creati in Azure HDInsight.
>
>

1. Installare [Tableau Desktop](http://www.tableau.com/products/desktop) nel computer in cui si sta eseguendo questa esercitazione di Apache Spark BI.

2. Assicurarsi che nel computer sia installato un driver ODBC di Microsoft Spark. È possibile installare il driver da [qui](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Avviare Tableau Desktop. Nel riquadro di sinistra, nell'elenco dei server a cui connettersi, fare clic su **Spark SQL**. Se Spark SQL non è presente per impostazione predefinita nel riquadro sinistro, è possibile trovarlo facendo clic su **More Servers**.
2. Nella finestra di dialogo della connessione a Spark SQL, specificare i valori come illustrato nello screenshot e quindi fare clic su **OK**.

    ![Connettersi a un cluster per Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connettersi a un cluster per Apache Spark BI")

    L'elenco a discesa di autenticazione indica **Microsoft Azure HDInsight Service** come opzione solo se nel computer è installato il [driver ODBC di Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) .
3. Nella schermata successiva fare clic sull'icona **Find** (Trova) dal menu a discesa **Schema** e quindi su **default** (predefinito).

    ![Trovare lo schema per Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Trovare lo schema per Apache Spark BI")
4. Per il campo **Table** (Tabella) fare di nuovo clic sull'icona **Find** (Trova) per elencare tutte le tabelle Hive disponibili nel cluster. Verrà visualizzata la tabella **hvac** creata in precedenza tramite il notebook.

    ![Trovare la tabella per Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Trovare la tabella per Apache Spark BI")
5. Trascinare e rilasciare la tabella nella casella superiore a destra. Tableau importa i dati e lo schema viene visualizzato come evidenziato dalla casella rossa.

    ![Aggiungere tabelle a Tableau per Apache Spark BI](./media/hdinsight-apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Aggiungere tabelle a Tableau per Apache Spark BI")
6. Fare clic sulla scheda **Sheet1** nella parte inferiore sinistra. Apportare una visualizzazione che mostra la temperatura effettiva e di destinazione media per tutti gli edifici e per ogni data. Trascinare **Date** (Data) e **Building ID** (ID compilazione) su **Columns** (Colonne) e **Actual Temp**/**Target Temp** (Temp effettiva/Temp di destinazione) su **Rows** (Righe). In **Marks** (Marcatori) selezionare **Area** per usare una mappa di area per la visualizzazione dei dati Spark.

     ![Aggiungere campi per la visualizzazione dei dati Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Aggiungere campi per la visualizzazione dei dati Spark")
7. Per impostazione predefinita, i campi di temperatura vengono visualizzati come funzione di aggregazione. Se si vuole visualizzare la media delle temperature invece, è possibile farlo dall'elenco a discesa, come illustrato di seguito.

    ![Eseguire la media della temperatura per la visualizzazione dei dati Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Eseguire la media della temperatura per la visualizzazione dei dati Spark")

8. È anche possibile super-imporre una mappa di temperatura rispetto a un'altra per farsi un'idea migliore della differenza tra le temperature effettive e di destinazione. Spostare il puntatore del mouse nell'angolo del grafico ad aree inferiore fino a visualizzare la forma evidenziata in un cerchio rosso. Trascinare il grafico sull'altro grafico nella parte superiore e rilasciare il mouse quando viene visualizzata la forma evidenziata in un rettangolo rosso.

    ![Unire mappe per la visualizzazione dei dati Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Unire mappe per la visualizzazione dei dati Spark")

     La visualizzazione dei dati dovrebbe cambiare come mostrato nello screenshot:

    ![Output di Tableau per la visualizzazione dei dati Spark](./media/hdinsight-apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Output di Tableau per la visualizzazione dei dati Spark")
9. Fare clic su **Save** per salvare il foglio di lavoro. È possibile creare dashboard e aggiungervi uno o più fogli.

## <a name="next-steps"></a>Passaggi successivi

Finora è stato descritto come creare un cluster, frame di dati Spark per i dati query e quindi accedere ai dati dagli strumenti di BI. È ora possibile esaminare le istruzioni su come gestire le risorse del cluster ed eseguire il debug dei processi in esecuzione in un cluster HDInsight Spark.

* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Tenere traccia ed eseguire il debug di processi in esecuzione nel cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-job-debugging.md)

