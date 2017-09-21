---
title: Usare Interactive Hive con Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Interactive Hive (Hive LLAP) con HDInsight.
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
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 6c4e3d0e97eb9ad4500d684c7da3b19c5669994f
ms.contentlocale: it-it
ms.lasthandoff: 09/09/2017

---
# <a name="use-interactive-hive-with-hdinsight-preview"></a>Usare Interactive Hive con HDInsight (Anteprima)
Interactive Hive (detto anche Hive LLAP o [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) è un nuovo [tipo di cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) di Azure HDInsight. Interactive Hive supporta la memorizzazione nella cache in memoria, che rende le query Hive più veloci e molto più interattive. 

Un cluster Interactive Hive è diverso da un cluster Hadoop in quanto contiene solo il servizio Hive. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie e altri servizi saranno presto rimossi da questo cluster.
> È possibile accedere al servizio Hive nel cluster Interactive Hive solo tramite la vista Hive di Ambari, Beeline e il driver Microsoft Hive Open Database Connectivity (Hive ODBC). Non è possibile accedervi tramite la console Hive, Templeton, lo strumento da riga di comando di Azure (Interfaccia della riga di comando di Azure) o Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>Creare un cluster Interactive Hive
È possibile usare i cluster Interactive Hive solo nei cluster basati su Linux. Per informazioni su come creare un cluster HDInsight, vedere [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="execute-hive-queries-from-interactive-hive"></a>Eseguire le query Hive da Interactive Hive
Per eseguire query Hive, sono disponibili le opzioni seguenti:

* Eseguire Hive tramite la vista Hive di Ambari.
  
    Per altre informazioni sull'uso della vista Hive, vedere [Usare la vista Hive di Ambari con Hadoop in HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).
* Eseguire Hive tramite Beeline.
  
    Per informazioni sull'uso di Beeline in HDInsight, vedere [Usare Hive con Hadoop in HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md).
  
    È possibile usare Beeline dal nodo head o da un nodo perimetrale vuoto. È consigliabile usare Beeline da un nodo perimetrale vuoto. Per informazioni sulla creazione di un cluster HDInsight usando un nodo perimetrale vuoto, vedere [Usare i nodi perimetrali vuoti in HDInsight](hdinsight-apps-use-edge-node.md).
* Eseguire Hive tramite Hive ODBC.
  
    Per informazioni sull'uso di Hive ODBC, vedere [Connettere Excel a Hadoop mediante il driver Microsoft Hive ODBC](hdinsight-connect-excel-hive-odbc-driver.md).

Per trovare la stringa di connessione JDBC (Java Database Connectivity):

1. Accedere ad Ambari usando l'URL seguente: https://\<nome cluster\>.AzureHDInsight.net.
2. Nel menu a sinistra selezionare **Hive**.
3. Per copiare l'URL, selezionare l'icona degli Appunti:
   
   ![JDBC di HDInsight Hadoop Interactive Hive LLAP](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Passaggi successivi
* Leggere le informazioni su come [creare cluster Interactive Hive in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Leggere le informazioni su come [usare Beeline per inviare query Hive in HDInsight](hdinsight-hadoop-use-hive-beeline.md).
* Leggere le informazioni su come [connettere Excel a Hadoop con il driver Microsoft Hive ODBC](hdinsight-connect-excel-hive-odbc-driver.md).


