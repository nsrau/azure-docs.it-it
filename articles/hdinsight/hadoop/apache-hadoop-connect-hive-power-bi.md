---
title: Visualizzare i dati Apache Hive con Power BI - Azure HDInsight
description: Informazioni su come usare Microsoft Power BI per visualizzare i dati Hive elaborati da Azure HDInsight.
keywords: hdinsight,hadoop,hive,interactive query,interactive hive,LLAP,odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 0e8f0e6ff6ba4b280d6174b6cec231ddca782912
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058606"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualizzare i dati Apache Hive con Microsoft Power BI tramite ODBC in Azure HDInsight

Informazioni su come connettere Microsoft Power BI Desktop ad Azure HDInsight usando ODBC e visualizzare i dati Apache Hive.

>[!IMPORTANT]
> È possibile sfruttare il driver ODBC Hive per eseguire l'importazione tramite il connettore ODBC generico in Power BI Desktop. Tuttavia, non è consigliato per i carichi di lavoro di BI considerata la natura non interattiva del motore di query Hive. Il [connettore HDInsight Interactive Query](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) e il [connettore HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) rappresentano una scelta migliore per le prestazioni.

In questo articolo vengono caricati i dati da una `hivesampletable` tabella hive a Power bi. La tabella Hive contiene alcuni dati relativi all'utilizzo dei cellulari. Si tracciano quindi i dati sull'utilizzo in una mappa del mondo:

![Report mappa di Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Le informazioni si applicano anche al nuovo tipo di cluster [Interactive Query](../interactive-query/apache-interactive-query-get-started.md). Per informazioni su come connettersi a HDInsight Interactive Query tramite query dirette, vedere [Visualize Interactive Query Hive data with Microsoft Power BI using direct query in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Microsoft Power BI tramite query dirette in Azure HDInsight).

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Cluster HDInsight**. Il cluster può essere un cluster HDInsight con Hive o un cluster Interactive Query, rilasciato di recente. Per la creazione dei cluster, vedere [Creare cluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . È possibile scaricarne una copia dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Creare un'origine dati Hive ODBC

Vedere [Creare un'origine dati Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Caricare i dati da HDInsight

La tabella Hive hivesampletable è disponibile con tutti i cluster HDInsight.

1. Avviare Power BI Desktop.

2. Dal menu in alto passare a **Home** > **Ottieni dati** > **altro...** .

    ![HDInsight Excel Power BI aprire i dati](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. Nella finestra di dialogo **Ottieni dati** selezionare **altro** a sinistra, selezionare **ODBC** a destra e quindi selezionare **Connetti** in basso.

4. Nella finestra **di dialogo da ODBC** selezionare il nome dell'origine dati creata nell'ultima sezione dall'elenco a discesa, quindi fare clic su **OK**.

5. Nella finestra di dialogo **strumento di navigazione** espandere **ODBC > Hive > default**, selezionare **hivesampletable**e quindi selezionare **Load**.

6. Nella finestra di dialogo **driver ODBC** selezionare **predefinito o personalizzato**, quindi selezionare **Connetti**.

## <a name="visualize-data"></a>Visualizzare i dati

Continuare dall'ultima procedura.

1. Nel riquadro Visualizzazioni selezionare **Mappa**.  Si tratta di un'icona a forma di globo.

    ![Personalizzazione del report in Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Dal riquadro **campi** selezionare **Country** e **devicemake**. È possibile visualizzare i dati tracciati sulla mappa.
3. Espandere la mappa.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come visualizzare i dati di HDInsight usando Power BI.  Per altre informazioni, vedere gli articoli seguenti:

* [Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel ad Apache Hadoop mediante Power Query](apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Apache Hive usando Strumenti Data Lake per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](./../hdinsight-upload-data.md).
