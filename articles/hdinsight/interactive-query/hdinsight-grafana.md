---
title: Usare Grafana in Azure HDInsight
description: Informazioni su come accedere al dashboard di Grafana con i cluster Apache Hadoop in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082864"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Accedere a Grafana in Azure HDInsight

[Grafana](https://grafana.com/) è un popolare grafico open source e generatore di cruscotti. Ricco di funzionalità, Grafana non solo consente agli utenti di creare dashboard personalizzabili e condivisibili, ma offre anche dashboard con modelli/script, l'integrazione LDAP, più origini dati e altro ancora.

Attualmente, in Azure HDInsight, Grafana è supportato con i tipi di cluster Spark, HBase, Kafka e Interactive Query.Currently, in Azure HDInsight, Grafana is supported with the Spark, HBase, Kafka and Interactive Query cluster types. Non è supportato per i cluster con Enterprise Security Pack abilitato.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-apache-hadoop-cluster"></a>Creare un cluster Apache Hadoop

Vedere [Creare cluster Apache Hadoop tramite il portale](../hdinsight-hadoop-create-linux-clusters-portal.md)di Azure. Per **Tipo di cluster**selezionare **Spark**, **Kafka**, **HBase**o **Interactive Query**.

## <a name="access-the-grafana-dashboard"></a>Accedere al dashboard di Grafana

1. Da un Web browser `https://CLUSTERNAME.azurehdinsight.net/grafana/` passare alla posizione in cui CLUSTERNAME è il nome del cluster.

1. Immettere le credenziali dell'utente del cluster Hadoop.

1. Il dashboard di Grafana è simile a questo esempio:

    ![Dashboard Web hdInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Dashboard di HDInsight Grafana")

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il cluster creato con i passaggi seguenti:

1. Accedere al [portale](https://portal.azure.com/)di Azure .

1. Nella casella **Ricerca** in alto digitare **HDInsight**.

1. Selezionare **Cluster HDInsight** in **Servizi**.

1. Nell'elenco di cluster HDInsight visualizzato selezionare il **pulsante ...** accanto al cluster creato.

1. Selezionare **Elimina**. Selezionare **Sì**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'analisi dei dati con HDInsight, vedere gli articoli seguenti:

* [Usare Apache Hive con HDInsight](../hadoop/hdinsight-use-hive.md).

* [Utilizzare MapReduce con HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* [Introduzione all'utilizzo degli strumenti Hadoop di Visual Studio per HDInsight.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
