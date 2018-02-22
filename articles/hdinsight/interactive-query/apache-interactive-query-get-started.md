---
title: Usare Interactive Query con Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Interactive Query (Hive LLAP) con HDInsight.
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: jgao
ms.openlocfilehash: 80e96e6bb727e6d5c1331580fad328d570b21494
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="use-interactive-query-with-hdinsight"></a>Usare Interactive Query in HDInsight
Interactive Query (detto anche Hive LLAP o [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) è un [tipo di cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types) di Azure HDInsight. Interactive Query supporta la memorizzazione nella cache in memoria, che rende le query Hive più veloci e molto più interattive.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Un cluster Interactive Query è diverso da un cluster Hadoop in quanto contiene solo il servizio Hive. 

> [!NOTE]
> È possibile accedere al servizio Hive nel cluster Interactive Query solo tramite la vista Hive di Ambari, Beeline e il driver Microsoft Hive Open Database Connectivity (Hive ODBC). Non è possibile accedervi tramite la console Hive, Templeton, lo strumento da riga di comando di Azure (Interfaccia della riga di comando di Azure) o Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Creare un cluster Interactive Query
Per informazioni sulla creazione di un cluster HDInsight, vedere [Creare cluster Hadoop in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Scegliere un tipo di cluster Interactive Query.

## <a name="execute-hive-queries-from-interactive-query"></a>Eseguire le query Hive da Interactive Query
Per eseguire query Hive, sono disponibili le opzioni seguenti:

* Usare Power BI

    Vedere [Visualize Interactive Query Hive data with Power BI in Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight) Vedere [Visualize big data with Power BI in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md) (Visualizzare i Big Data con Power BI in Azure HDInsight).
 
* Usare Zeppelin

    Vedere [Usare Zeppelin per eseguire le query di Hive in Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).

* Usare Visual Studio

    Vedere [Connettersi ad Azure HDInsight ed eseguire query Hive usando Strumenti Data Lake per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).

* Usare Visual Studio Code

    Vedere [Usare Visual Studio Code per Hive, LLAP o PySpark](../hdinsight-for-vscode.md).
* Eseguire Hive tramite la vista Hive di Ambari.
  
    Vedere [Usare la visualizzazione Hive con Hadoop in Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Eseguire Hive tramite Beeline.
  
    Vedere [Usare Hive con Hadoop in HDInsight con Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    È possibile usare Beeline dal nodo head o da un nodo perimetrale vuoto. È consigliabile usare Beeline da un nodo perimetrale vuoto. Per informazioni sulla creazione di un cluster HDInsight usando un nodo perimetrale vuoto, vedere [Usare i nodi perimetrali vuoti in HDInsight](../hdinsight-apps-use-edge-node.md).
* Eseguire Hive tramite Hive ODBC.
  
    Vedere [Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Per trovare la stringa di connessione JDBC (Java Database Connectivity):

1. Accedere ad Ambari usando l'URL seguente: https://\<nome cluster\>.AzureHDInsight.net.
2. Nel menu a sinistra selezionare **Hive**.
3. Per copiare l'URL, selezionare l'icona degli Appunti:
   
   ![JDBC di HDInsight Hadoop Interactive Query LLAP](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni su come [creare cluster Interactive Query in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Leggere le informazioni su come [visualizzare Big Data con Power BI in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Leggere le informazioni su come [usare Zeppelin per eseguire le query di Hive in Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).
* Leggere le informazioni su come [eseguire query Hive usando gli strumenti Data Lake per Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).
* Leggere le informazioni su come [usare gli strumenti di HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* Leggere le informazioni su come [usare la visualizzazione Hive con Hadoop in HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Leggere le informazioni su come [usare Beeline per inviare query Hive in HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Leggere le informazioni su come [connettere Excel a Hadoop con il driver Microsoft Hive ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

