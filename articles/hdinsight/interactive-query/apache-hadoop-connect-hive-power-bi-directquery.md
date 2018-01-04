---
title: Visualizzazione dei dati interattiva Query Hive con Power BI in Azure HDInsight | Documenti Microsoft
description: Imparare a usare Microsoft Power BI per visualizzare i dati interattivi Query Hive elaborati da Azure HDInsight.
keywords: hdinsight, hadoop, hive, interattiva esegue una query, hive interattivo, LLAP, directquery
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: jgao
ms.openlocfilehash: 290e600b7be4a6f9fb57afa50bb771e42e6a0624
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Visualizzare i dati interattivi Query Hive con Microsoft Power BI con query diretta in Azure HDInsight

Informazioni su come connettere Microsoft Power BI ai cluster Azure HDInsight interattivo Query e visualizzare i dati di Hive usando una query diretta. In questa esercitazione si caricano i dati da una tabella Hive hivesampletable in Power BI. La tabella Hive contiene alcuni dati relativi all'utilizzo dei cellulari. Si tracciano quindi i dati sull'utilizzo in una mappa del mondo:

![Report mappa di Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Per informazioni su come connettersi all'Hive tramite ODBC, vedere [Hive visualizzare i dati con Microsoft Power BI tramite ODBC in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Cluster HDInsight**. Il cluster può essere un cluster di HDInsight con Hive o un cluster di Query interattivo rilasciato di recente. Per la creazione dei cluster, vedere [Creare cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. È possibile scaricarne una copia dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Caricare i dati da HDInsight

La tabella Hive hivesampletable è disponibile con tutti i cluster HDInsight.

1. Accedere a Power BI Desktop.
2. Fare clic sulla scheda **Home**, fare clic su **Recupera dati** sulla barra multifunzione **Dati esterni** e quindi selezionare **Altro**.

    ![Power BI in HDInsight - Aprire i dati](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. Dal **recupera dati** riquadro, digitare **hdinsight** nella casella di ricerca. Se non viene visualizzato **Query interattivo HDInsight (Beta)**, è necessario aggiornare la versione più recente di Power BI Desktop.
4. Fare clic su **Query interattivo HDInsight (Beta)**, quindi fare clic su **Connetti**.
5. Fare clic su **continua** per chiudere la **connettore anteprima** finestra di dialogo di avviso.
6. Da **Query interattivo HDInsight**, selezionare o immettere le informazioni seguenti:

    - **Server**: immettere il nome del cluster di Query interattivo, ad esempio *myiqcluster.azurehdinsight.net*.
    - **Database**: per questa esercitazione, immettere **predefinito**.
    - **Modalità di connettività dati**: per questa esercitazione, selezionare **DirectQuery**.

    ![Connettersi a HDInsight query interattivo power bi directquery](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Fare clic su **OK**.
8. Immettere le credenziali dell'utente HTTP e quindi fare clic su **OK**.  Il nome utente predefinito **admin**
9. Nel riquadro sinistro, selezionare **hivesampletale**, quindi fare clic su **carico**.

    ![HDInsight query interattivo power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>Visualizzare i dati

Continuare dall'ultima procedura.

1. Nel riquadro Visualizzazioni selezionare **Mappa**.  Si tratta di un'icona a forma di globo.

    ![Personalizzazione del report in Power BI in HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. Nel riquadro Campi selezionare **paese** e **devicemake**. È possibile visualizzare i dati tracciati sulla mappa.
3. Espandere la mappa.

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come visualizzare i dati di HDInsight usando Power BI.  Per altre informazioni, vedere gli articoli seguenti:

* [Visualizzare i dati di Hive con Microsoft Power BI tramite ODBC in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Usare Zeppelin per eseguire query Hive in Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel a Hadoop mediante Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Hive usando Strumenti Data Lake per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](./../hdinsight-upload-data.md).
