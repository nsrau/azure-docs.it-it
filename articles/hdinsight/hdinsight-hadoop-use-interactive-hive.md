---
title: Usare Interactive Hive in HDInsight - Azure | Microsoft Docs
description: Informazioni su come usare Hive interattiva (Hive in LLAP) in HDInsight.
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
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: fa8b4126865788549217d89f19627f20739f8540
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---
# <a name="use-interactive-hive-in-hdinsight-preview"></a>Usare Interactive Hive in HDInsight (Anteprima)
Interactive Hive (o [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) è un nuovo [tipo di cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) di HDInsight.  Interactive Hive consente il caching delle query Hive, rendendo le stesse molto più interattive e veloci. Questa nuova funzionalità rende HDInsight una delle soluzioni open di Big Data più efficienti e flessibili del mondo, basata su cloud, con cache in memoria (tramite Spark e Hive) e analisi avanzata grazie a una solida integrazione con i servizi R. 

Il cluster di Interactive Hive è diverso dal cluster di Hadoop, poiché contiene solo il servizio Hive. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie e altri servizi saranno presto rimossi da questo cluster.
> Il servizio Hive del cluster Interactive Hive è accessibile solo tramite la vista Ambari Hive, Beeline e Hive ODBC. Non è possibile accedervi tramite console Hive, Templeton, interfaccia della riga di comando di Azure e Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>Creare un cluster Interactive Hive
Il cluster Interactive Hive è supportato solo dai cluster basati su Linux. Per informazioni sulla creazione di cluster HDInsight, vedere [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="execute-hive-from-interactive-hive"></a>Eseguire Hive da Interactive Hive
Sono disponibili diverse opzioni per eseguire query Hive:

* Eseguire Hive tramite la vista Ambari Hive
  
    Per informazioni sull'uso della vista Hive, vedere [Use the Hive View with Hadoop in HDInsight](hdinsight-hadoop-use-hive-ambari-view.md) (Usare la vista Hive con HDInsight).
* Eseguire Hive tramite Beeline
  
    Per altre informazioni sull'uso di Beeline in HDInsight, vedere [Usare Hive con Hadoop in HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md).
  
    È possibile usare Beeline dal nodo head o da un nodo perimetrale vuoto.  È consigliabile usare Beeline da un nodo perimetrale vuoto.  Per informazioni sulla creazione di un cluster HDInsight con un nodo perimetrale vuoto, vedere [Usare nodi perimetrali vuoti in HDInsight](hdinsight-apps-use-edge-node.md).
* Eseguire Hive tramite Hive ODBC
  
    Per informazioni sull'uso di Hive ODBC, vedere [Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-odbc-driver.md).

**Per trovare la stringa di connessione JDBC:**

1. Accedere ad Ambari all'URL seguente: https://<ClusterName>.AzureHDInsight.net.
2. Fare clic su **Hive** nel menu di sinistra.
3. Fare clic sull'icona evidenziata per copiare l'URL:
   
   ![JDBC di HDInsight Hadoop Interactive Hive LLAP](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>Vedere anche
* [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md): informazioni sulle modalità di creazione di cluster Interactive Hive in HDInsight.
* [Usare Hive con Hadoop in HDInsight con Beeline](hdinsight-hadoop-use-hive-beeline.md): informazioni su come usare Beeline per inviare query Hive.
* [Connettere Excel a Hadoop mediante Microsoft Hive ODBC Driver](hdinsight-connect-excel-hive-odbc-driver.md): informazioni sulle modalità di connessione di Excel ad Hive.


