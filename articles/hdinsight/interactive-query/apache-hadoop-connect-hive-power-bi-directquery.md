---
title: Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight
description: Usare Microsoft Power BI per visualizzare i dati Hive di Interactive Query da Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 04af3f211674458c51bbb5cdbc6012833a790584
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190962"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Visualizzare i dati Apache Hive di Interactive Query con Microsoft Power BI usando DirectQuery in Azure HDInsight

Questo articolo descrive come connettere Microsoft Power BI ai cluster Interactive Query di Azure HDInsight e visualizzare i dati Apache Hive mediante DirectQuery. L'esempio fornito carica i dati da un `hivesampletable` tabella Hive in Power BI. Il `hivesampletable` tabella Hive contiene alcuni dati di utilizzo telefono cellulare. Si tracciano quindi i dati sull'utilizzo in una mappa del mondo:

![Report mappa di Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

È possibile sfruttare il [driver ODBC Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) per eseguire l'importazione tramite il connettore ODBC generico in Power BI Desktop. Tuttavia, non è consigliato per i carichi di lavoro di BI considerata la natura non interattiva del motore di query Hive. Il [connettore HDInsight Interactive Query](./apache-hadoop-connect-hive-power-bi-directquery.md) e il [connettore HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) rappresentano una scelta migliore per le prestazioni.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Cluster HDInsight**. Il cluster può essere un cluster HDInsight con Apache Hive o un cluster Interactive Query, rilasciato di recente. Per la creazione dei cluster, vedere [Creare cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . È possibile scaricarne una copia dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Caricare i dati da HDInsight

Il `hivesampletable` tabella Hive è disponibile con tutti i cluster HDInsight.

1. Avviare Power BI Desktop.

2. Dalla barra dei menu, passare a **casa** > **recupera dati** > **più...** .

    ![Power BI in HDInsight - Aprire i dati](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Dal **recupera dati** finestra, immettere **hdinsight** nella casella di ricerca.  

4. Nei risultati della ricerca, selezionare **Interactive Query HDInsight**, quindi selezionare **Connect**.  Se non viene visualizzata **Interactive Query HDInsight**, è necessario aggiornare la versione più recente di Power BI Desktop.

5. Selezionare **continuazione** per chiudere la **ci si connette a un servizio di terze parti** finestra di dialogo.

6. Nel **Interactive Query HDInsight** finestra, immettere le informazioni seguenti e quindi selezionare **OK**:

    |Proprietà | Value |
    |---|---|
    |Server |Immettere il nome del cluster, ad esempio *myiqcluster.azurehdinsight.net*.|
    |Database |Immettere **predefinito** per questo articolo.|
    |Modalità connettività dati |Selezionare **DirectQuery** per questo articolo.|

    ![Connessione di HDInsight Interactive Query in Power BI tramite DirectQuery](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Immettere le credenziali HTTP e quindi selezionare **Connect**. Il nome utente predefinito è **admin**

8. Dal **Navigator** finestra nel riquadro a sinistra, seleziona **hivesampletale**.

9. Selezionare **carico** dalla finestra principale.

    ![Tabella hivesampletable di HDInsight Interactive Query in Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualizzare i dati su una mappa

Continuare dall'ultima procedura.

1. Nel riquadro visualizzazioni, selezionare **mappa**, l'icona del mondo. Una mappa generica viene quindi visualizzata nella finestra principale.

    ![Personalizzazione del report in Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. Nel riquadro Campi selezionare **paese** e **devicemake**. Una mappa del mondo con i punti dati viene visualizzato nella finestra principale dopo qualche istante.

3. Espandere la mappa.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come visualizzare i dati di HDInsight usando Microsoft Power BI.  Per altre informazioni sulla visualizzazione dei dati, vedere gli articoli seguenti:

* [Visualizzare i dati Apache Hive con Microsoft Power BI tramite ODBC in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel ad Apache Hadoop mediante Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Apache Hive usando Strumenti Data Lake per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](./../hdinsight-upload-data.md).
