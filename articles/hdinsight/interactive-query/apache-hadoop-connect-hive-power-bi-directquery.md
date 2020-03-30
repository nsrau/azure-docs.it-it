---
title: Vedere Dati di Query interattiva Hive con Power BI in Azure HDInsightSee Interactive Query Hive data with Power BI in Azure HDInsight
description: Usare Microsoft Power BI per visualizzare i dati Hive di Interactive Query da Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: adcd4d9e81eecad9540a4ef1be5e675f940ffb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367993"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualizzare i dati di Interactive Query Apache Hive con Microsoft Power BI usando la query diretta in HDInsight

Questo articolo descrive come connettere Microsoft Power BI ai cluster Interactive Query di Azure HDInsight e visualizzare i dati Apache Hive mediante DirectQuery. L'esempio fornito carica `hivesampletable` i dati da una tabella Hive a Power BI. La `hivesampletable` tabella Hive contiene alcuni dati sull'utilizzo del telefono cellulare. Si tracciano quindi i dati sull'utilizzo in una mappa del mondo:

![Report mappa di Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

È possibile sfruttare il [driver ODBC Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) per eseguire l'importazione tramite il connettore ODBC generico in Power BI Desktop. Tuttavia, non è consigliato per i carichi di lavoro di BI considerata la natura non interattiva del motore di query Hive. Il [connettore HDInsight Interactive Query](./apache-hadoop-connect-hive-power-bi-directquery.md) e il [connettore HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) rappresentano una scelta migliore per le prestazioni.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Cluster HDInsight**. Il cluster può essere un cluster HDInsight con Apache Hive o un cluster Interactive Query, rilasciato di recente. Per la creazione dei cluster, vedere [Creare cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. È possibile scaricarne una copia dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Caricare i dati da HDInsight

La `hivesampletable` tabella Hive include tutti i cluster HDInsight.The Hive table comes with all HDInsight clusters.

1. Avviare Power BI Desktop.

2. Dalla barra dei menu, vai a **Home** > **Ottieni dati** > **di più...**.

    ![HDInsight Power BI Ottieni i dati di più](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Nella finestra **Ottieni dati** immettere **hdinsight** nella casella di ricerca.  

4. Dai risultati della ricerca selezionare **HDInsight Interactive Query**, quindi **scegliere Connetti**.  Se **HDInsight Interactive Query**non è visualizzato, è necessario aggiornare Power BI Desktop alla versione più recente.

5. Selezionare **Continua** per chiudere la finestra di dialogo **Connessione a un servizio di terze parti.**

6. Nella finestra **Query interattiva HDInsight** immettere le informazioni seguenti e quindi selezionare **OK**:

    |Proprietà | valore |
    |---|---|
    |Server |Immettere il nome del cluster, ad esempio *myiqcluster.azurehdinsight.net*.|
    |Database |Immettere **l'impostazione predefinita** per questo articolo.|
    |Modalità di connettività dei dati |Selezionare **DirectQuery** per questo articolo.|

    ![Connessione di HDInsight Interactive Query in Power BI tramite DirectQuery](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Immettere le credenziali HTTP e quindi selezionare **Connetti**. Il nome utente predefinito è **admin**

8. Dalla finestra **Navigatore** nel riquadro sinistro, selezionare **hivesampletale**.

9. Selezionare **Carico** dalla finestra principale.

    ![Tabella hivesampletable di HDInsight Interactive Query in Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualizzare i dati su una mappa

Continuare dall'ultima procedura.

1. Nel riquadro Visualizzazioni selezionare **Mappa**, l'icona a forma di globo. Una mappa generica viene quindi visualizzata nella finestra principale.

    ![Personalizzazione del report in Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. Nel riquadro Campi selezionare **paese** e **devicemake**. Dopo alcuni istanti, nella finestra principale viene visualizzata una mappa del mondo con i punti dati.

3. Espandere la mappa.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come visualizzare i dati di HDInsight usando Microsoft Power BI.  Per altre informazioni sulla visualizzazione dei dati, vedere gli articoli seguenti:

* [Visualizzare i dati di Apache Hive con Microsoft Power BI usando ODBC in Azure HDInsight.](../hadoop/apache-hadoop-connect-hive-power-bi.md) 
* [Usare Apache per eseguire query Apache Hive in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel ad Apache Hadoop utilizzando Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Apache Hive utilizzando Data Lake Tools per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Usare lo strumento Azure HDInsight per il codice di Visual Studio](../hdinsight-for-vscode.md).
* [Caricare i dati in HDInsight](./../hdinsight-upload-data.md).
