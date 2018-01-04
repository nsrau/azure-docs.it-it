---
title: Usare Zeppelin per eseguire query Hive in Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Zeppelin per eseguire query Hive.
keywords: hdinsight,hadoop,hive,query interattiva,LLAP
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
ms.date: 09/26/2017
ms.author: jgao
ms.openlocfilehash: 39f99bef252e93db55e0493ee284ef78b7d087a1
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="use-zeppelin-to-run-hive-queries-in-azure-hdinsight"></a>Usare Zeppelin per eseguire query Hive in Azure HDInsight 

I cluster Interactive Query HDInsight includono notebook Zeppelin che è possibile usare per eseguire query Interactive Hive. Questo articolo illustra come usare Zeppelin per eseguire query Hive in Azure HDInsight. 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Cluster Interactive Query HDInsight**. Vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) per creare un cluster HDInsight.  Assicurarsi di scegliere il tipo Interactive Query. 

## <a name="create-a-zeppelin-note"></a>Creare una nota di Zeppelin

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
* [Visualizzazione dei dati interattiva Query Hive con Power BI in Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettere Excel a Hadoop mediante Power Query](hadoop/apache-hadoop-connect-excel-power-query.md).
* [Connettersi ad Azure HDInsight ed eseguire query Hive usando Strumenti Data Lake per Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](./hdinsight-upload-data.md).
