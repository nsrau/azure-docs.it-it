---
title: Usare Grafana in Azure HDInsight
description: Informazioni su come accedere al dashboard di Grafana con cluster di Apache Hadoop in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79082864"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Accedere a Grafana in Azure HDInsight

[Grafana](https://grafana.com/) è un noto grafico open source e un generatore di dashboard. Ricco di funzionalità, Grafana non solo consente agli utenti di creare dashboard personalizzabili e condivisibili, ma offre anche dashboard con modelli/script, l'integrazione LDAP, più origini dati e altro ancora.

Attualmente, in Azure HDInsight, Grafana è supportato con i tipi di cluster Spark, HBase, Kafka e Interactive query. Non è supportata per i cluster con Enterprise Security Pack abilitato.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-an-apache-hadoop-cluster"></a>Creare un cluster Apache Hadoop

Vedere [creare cluster Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md). Per **tipo di cluster**selezionare **Spark**, **Kafka**, **HBase**o **Interactive query**.

## <a name="access-the-grafana-dashboard"></a>Accedere al dashboard di Grafana

1. Da un Web browser passare a `https://CLUSTERNAME.azurehdinsight.net/grafana/` dove clustername è il nome del cluster.

1. Immettere le credenziali dell'utente del cluster Hadoop.

1. Il dashboard di Grafana è simile a questo esempio:

    ![Dashboard Web HDInsight Grafana](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "Dashboard di HDInsight Grafana")

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare il cluster creato con i passaggi seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Nella casella **Ricerca** in alto digitare **HDInsight**.

1. Selezionare **Cluster HDInsight** in **Servizi**.

1. Nell'elenco dei cluster HDInsight visualizzato selezionare il **...** accanto al cluster che è stato creato.

1. Selezionare **Elimina**. Selezionare **Sì**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'analisi dei dati con HDInsight, vedere gli articoli seguenti:

* [Usare Apache hive con HDInsight](../hadoop/hdinsight-use-hive.md).

* [Usare MapReduce con HDInsight](../hadoop/hdinsight-use-mapreduce.md).

* Iniziare [a usare gli strumenti di Visual Studio Hadoop per HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
