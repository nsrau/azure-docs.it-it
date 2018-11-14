---
title: Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight
description: Informazioni su come usare Apache Zeppelin per eseguire query Apache Hive.
keywords: hdinsight,hadoop,hive,query interattiva,LLAP
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: hrasheed
ms.openlocfilehash: 75ec0e17e9866d2cd3420ff6ecf648bf22a8ae8e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277954"
---
# <a name="use-apache-zeppelin-to-run-apache-hive-queries-in-azure-hdinsight"></a>Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight 

I cluster Interactive Query HDInsight includono notebook Apache Zeppelin che è possibile usare per eseguire query Hive interattive. Questo articolo illustra come usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight. 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Cluster Interactive Query HDInsight**. Vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) per creare un cluster HDInsight.  Assicurarsi di scegliere il tipo Interactive Query. 

## <a name="create-a-apache-zeppelin-note"></a>Creare una nota di Apache Zeppelin

1. Passare all'URL seguente:

        https://CLUSTERNAME.azurehdinsight.net/zeppelin
    Sostituire **CLUSTERNAME** con il nome del cluster.

2. Immettere nome utente e password Hadoop. Dalla pagina di Zeppelin è possibile creare una nuova nota o aprire le note esistenti. HiveSample contiene alcune query Hive di esempio.  

    ![Zeppelin Interactive Query HDInsight](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)
3. Fare clic su **Create new Note** (Crea una nuova nota).
4. Digitare o selezionare i valori seguenti:

    - Nome della nota: immettere un nome per la nota.
    - Interprete predefinito: selezionare **JDBC**.

5. Fare clic su **Create Note** (Crea nota).
6. Eseguire la query Hive seguente:

        %jdbc(hive)
        show tables

    ![Zeppelin Interactive Query HDInsight esegue la query](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    L'istruzione **%jdbc(hive)** nella prima riga indica al notebook di usare l'interprete JDBC Hive.

    La query dovrebbe restituire una tabella Hive denominata *hivesampletable*.

    Di seguito sono illustrate altre due query Hive che è possibile eseguire su hivesampletable. 

        %jdbc(hive)
        select * from hivesampletable limit 10

        %jdbc(hive)
        select ${group_name}, count(*) as total_count
        from hivesampletable
        group by ${group_name=market,market|deviceplatform|devicemake}
        limit ${total_count=10}

    Rispetto a Hive tradizionale, i risultati della query vengono restituiti più velocemente.


## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come visualizzare i dati di HDInsight usando Power BI.  Per altre informazioni, vedere gli articoli seguenti:

* [Visualizzare i dati Hive con Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md).
* [Visualize Interactive Query Hive data with Power BI in Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel a Hadoop mediante Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Hive usando Strumenti Data Lake per Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](./hdinsight-upload-data.md).
