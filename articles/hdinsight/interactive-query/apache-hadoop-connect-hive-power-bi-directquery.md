---
title: Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight
description: Usare Microsoft Power BI per visualizzare i dati Hive di Interactive Query da Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 0f273f75c33362bc99efbd7ac6bc46c3778ae88b
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123027"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Visualizzare i dati di Apache Hive Interactive query con Microsoft Power BI tramite query diretta in HDInsight

Questo articolo descrive come connettere Microsoft Power BI ai cluster Interactive Query di Azure HDInsight e visualizzare i dati Apache Hive mediante DirectQuery. L'esempio fornito carica i dati da una `hivesampletable` tabella hive a Power bi. La `hivesampletable` tabella hive contiene alcuni dati di utilizzo del telefono cellulare. Si tracciano quindi i dati sull'utilizzo in una mappa del mondo:

![Report mappa di Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

È possibile sfruttare il [driver ODBC Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) per eseguire l'importazione tramite il connettore ODBC generico in Power BI Desktop. Tuttavia, non è consigliato per i carichi di lavoro di BI considerata la natura non interattiva del motore di query Hive. Il [connettore HDInsight Interactive Query](./apache-hadoop-connect-hive-power-bi-directquery.md) e il [connettore HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) rappresentano una scelta migliore per le prestazioni.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Cluster HDInsight**. Il cluster può essere un cluster HDInsight con Apache Hive o un cluster Interactive Query, rilasciato di recente. Per la creazione dei cluster, vedere [Creare cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . È possibile scaricarne una copia dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Caricare i dati da HDInsight

La `hivesampletable` tabella hive viene fornita con tutti i cluster HDInsight.

1. Avviare Power BI Desktop.

2. Dalla barra dei menu passare a **Home** > **Ottieni dati** > **altro...** .

    ![HDInsight Power BI ottenere ulteriori dati](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Nella finestra **Get Data** immettere **HDInsight** nella casella di ricerca.  

4. Nei risultati della ricerca selezionare **HDInsight Interactive query**, quindi selezionare **Connetti**.  Se **HDInsight Interactive query**non è visibile, è necessario aggiornare la Power bi desktop alla versione più recente.

5. Selezionare **continua** per chiudere la finestra di dialogo **connessione a un servizio di terze parti** .

6. Nella finestra **HDInsight Interactive query** immettere le informazioni seguenti e quindi selezionare **OK**:

    |Proprietà | Value |
    |---|---|
    |Server |Immettere il nome del cluster, ad esempio *myiqcluster.azurehdinsight.NET*.|
    |Database |Immettere il **valore predefinito** per questo articolo.|
    |Modalità di connettività dei dati |Selezionare **DirectQuery** per questo articolo.|

    ![Connessione di HDInsight Interactive Query in Power BI tramite DirectQuery](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Immettere le credenziali HTTP, quindi selezionare **Connetti**. Il nome utente predefinito è **admin**

8. Nella finestra **strumento di navigazione** nel riquadro sinistro selezionare **hivesampletale**.

9. Selezionare **carica** nella finestra principale.

    ![Tabella hivesampletable di HDInsight Interactive Query in Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Visualizzare i dati su una mappa

Continuare dall'ultima procedura.

1. Dal riquadro Visualizzazioni selezionare **mappa**, l'icona del globo. Una mappa generica viene quindi visualizzata nella finestra principale.

    ![Personalizzazione del report in Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. Nel riquadro Campi selezionare **paese** e **devicemake**. Una mappa globale con i punti dati viene visualizzata nella finestra principale dopo alcuni istanti.

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
