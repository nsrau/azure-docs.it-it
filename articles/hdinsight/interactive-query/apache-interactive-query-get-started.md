---
title: Usare Interactive Query in Azure HDInsight
description: Informazioni su come usare Interactive Query (Hive LLAP) con HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: c7cee3dfd3b091d75f4dadcaa62513fddf0c0e68
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126597"
---
# <a name="use-interactive-query-with-hdinsight"></a>Usare Interactive Query in HDInsight
Interactive Query (chiamato anche Apache Hive LLAP o [Low Latency Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP)) è un [tipo di cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types) di Azure HDInsight. Interactive Query supporta la memorizzazione nella cache in memoria, che rende le query Apache Hive più veloci e molto più interattive.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Un cluster Interactive Query è diverso da un cluster Apache Hadoop in quanto contiene solo il servizio Hive. 

> [!NOTE]  
> È possibile accedere al servizio Hive nel cluster Interactive Query solo tramite la vista Apache Hive di Ambari, Beeline e il driver Microsoft Hive Open Database Connectivity (Hive ODBC). Non è possibile accedervi tramite la console Hive, Templeton, CLI classica di Azure o Azure PowerShell. 

## <a name="create-an-interactive-query-cluster"></a>Creare un cluster Interactive Query
Per informazioni sulla creazione di un cluster HDInsight, vedere [Creare cluster Apache Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Scegliere un tipo di cluster Interactive Query.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Eseguire le query Apache Hive da Interactive Query
Per eseguire query Hive, sono disponibili le opzioni seguenti:

* Usare Microsoft Power BI

    Vedere [Visualizzare i dati Apache Hive di Interactive Query con Power BI in Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) Vedere [Visualizzare Big Data con Power BI in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Usare Apache Zeppelin

    Visualizzare [usare Apache Zeppelin per eseguire query Apache Hive in HDInsight di Azure](../hdinsight-connect-hive-zeppelin.md).

* Usare Visual Studio

    Vedere [Connettersi ad Azure HDInsight ed eseguire query Apache Hive usando Strumenti Data Lake per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).

* Usare Visual Studio Code

    Vedere [Usare Visual Studio Code per Apache Hive, LLAP o PySpark](../hdinsight-for-vscode.md).
* Eseguire Apache Hive tramite la vista Apache Hive di Ambari.
  
    Vedere [Usare la vista Apache Hive di Apache Ambari con Apache Hadoop in Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).

* Eseguire Apache Hive tramite Beeline.
  
    Vedere [Usare Apache Hive con Apache Hadoop in HDInsight con Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    È possibile usare Beeline dal nodo head o da un nodo perimetrale vuoto. È consigliabile usare Beeline da un nodo perimetrale vuoto. Per informazioni sulla creazione di un cluster HDInsight usando un nodo perimetrale vuoto, vedere [Usare i nodi perimetrali vuoti in HDInsight](../hdinsight-apps-use-edge-node.md).
* Eseguire Apache Hive tramite Hive ODBC.
  
    Vedere [Connettere Excel ad Apache Hadoop mediante Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Per trovare la stringa di connessione JDBC (Java Database Connectivity):

1. Accedere ad Apache Ambari usando l'URL seguente: https://\<nome cluster\>.AzureHDInsight.net.
2. Nel menu a sinistra selezionare **Hive**.
3. Per copiare l'URL, selezionare l'icona degli Appunti:
   
   ![JDBC di HDInsight Hadoop Interactive Query LLAP](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni su come [creare cluster Interactive Query in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Leggere le informazioni su come [visualizzare Big Data con Power BI in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Informazioni su come [usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).
* Leggere le informazioni su come [eseguire query Apache Hive usando gli strumenti Data Lake per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).
* Leggere le informazioni su come [usare gli strumenti di HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* Leggere le informazioni su come [usare la vista Apache Hive con Apache Hadoop in HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Leggere le informazioni su come [usare Beeline per inviare query Apache Hive in HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Leggere le informazioni su come [connettere Excel ad Apache Hadoop mediante Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

