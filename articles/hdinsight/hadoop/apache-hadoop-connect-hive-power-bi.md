---
title: Visualizzare Big Data con Power BI in Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Microsoft Power BI per visualizzare i dati Hive elaborati da Azure HDInsight.
keywords: hdinsight,hadoop,hive,interactive query,interactive hive,LLAP,odbc
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: jgao
ms.openlocfilehash: e543fba4c382501024bd33bed3853e7806d081ae
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="visualize-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Visualizzare i dati Hive con Microsoft Power BI tramite ODBC in Azure HDInsight

Informazioni su come connettere Microsoft Power BI ad Azure HDInsight tramite ODBC e visualizzare i dati Hive. 

>[!IMPORTANT]
> È possibile sfruttare il driver ODBC Hive per eseguire l'importazione tramite il connettore ODBC generico in Power BI Desktop. Tuttavia, non è consigliato per i carichi di lavoro di BI considerata la natura non interattiva del motore di query Hive. Il [connettore HDInsight Interactive Query](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) e il [connettore HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) rappresentano una scelta migliore per le prestazioni.

In questa esercitazione si caricano i dati da una tabella Hive hivesampletable in Power BI. La tabella Hive contiene alcuni dati relativi all'utilizzo dei cellulari. Si tracciano quindi i dati sull'utilizzo in una mappa del mondo:

![Report mappa di Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Le informazioni si applicano anche al nuovo tipo di cluster [Interactive Query](../interactive-query/apache-interactive-query-get-started.md). Per informazioni su come connettersi a HDInsight Interactive Query tramite query dirette, vedere [Visualize Interactive Query Hive data with Microsoft Power BI using direct query in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Microsoft Power BI tramite query dirette in Azure HDInsight).



## <a name="prerequisites"></a>prerequisiti
Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Cluster HDInsight**. Il cluster può essere un cluster HDInsight con Hive o un cluster Interactive Query, rilasciato di recente. Per la creazione dei cluster, vedere [Creare cluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. È possibile scaricarne una copia dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Creare un'origine dati Hive ODBC

Vedere [Creare un'origine dati Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md#create-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Caricare i dati da HDInsight

La tabella Hive hivesampletable è disponibile con tutti i cluster HDInsight.

1. Accedere a Power BI Desktop.
2. Fare clic sulla scheda **Home**, fare clic su **Recupera dati** sulla barra multifunzione **Dati esterni** e quindi selezionare **Altro**.

    ![Power BI in HDInsight - Aprire i dati](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)
3. Nel riquadro **Recupera dati** fare clic su **Altro** a sinistra, fare clic su **ODBC** a destra e quindi fare clic su **Connetti** in basso.
4. Nel riquadro **Da ODBC** selezionare il nome dell'origine dati creata nell'ultima sezione e quindi fare clic su **OK**.
5. Nel riquadro **Strumento di navigazione** espandere **ODBC->HIVE->predefinito**, selezionare **hivesampletable** e quindi fare clic su **Carica**.

## <a name="visualize-data"></a>Visualizzare i dati

Continuare dall'ultima procedura.

1. Nel riquadro Visualizzazioni selezionare **Mappa**.  Si tratta di un'icona a forma di globo.

    ![Personalizzazione del report in Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Nel riquadro Campi selezionare **paese** e **devicemake**. È possibile visualizzare i dati tracciati sulla mappa.
3. Espandere la mappa.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come visualizzare i dati di HDInsight usando Power BI.  Per altre informazioni, vedere gli articoli seguenti:

* [Usare Zeppelin per eseguire query Hive in Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel a Hadoop mediante Power Query](apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Hive usando Strumenti Data Lake per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](./../hdinsight-upload-data.md).
