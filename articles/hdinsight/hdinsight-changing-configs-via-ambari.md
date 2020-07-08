---
title: Ottimizzare i cluster con Apache Ambari in Azure HDInsight
description: Usare l'interfaccia utente Web di Apache Ambari per configurare e ottimizzare i cluster HDInsight di Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 52eb1b6c89ff02cb44fe731c2463ab02c284f26c
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086450"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Ottimizzare i cluster con Apache Ambari in Azure HDInsight

HDInsight fornisce i cluster Apache Hadoop per le applicazioni di elaborazione dei dati su larga scala. La gestione, il monitoraggio e l'ottimizzazione di questi complessi cluster multinodo possono risultare difficili. Apache Ambari è un'interfaccia Web per la gestione e il monitoraggio di cluster HDInsight Linux.

Per un'introduzione all'uso dell'interfaccia utente Web Ambari, vedere [Gestire i cluster HDInsight usando l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md)

Accedere ad Ambari all'indirizzo `https://CLUSTERNAME.azurehdidnsight.net` con le credenziali del cluster. La schermata iniziale visualizza un dashboard generale.

![Visualizzazione del dashboard utente di Apache Ambari](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

L'interfaccia utente Web di Ambari viene utilizzata per gestire host, servizi, avvisi, configurazioni e visualizzazioni. Non è possibile usare Ambari per creare un cluster HDInsight o aggiornare i servizi. Non è inoltre in grado di gestire stack e versioni, rimuovere le autorizzazioni o ricommissionare host o aggiungere servizi al cluster.

## <a name="manage-your-clusters-configuration"></a>Gestire la configurazione del cluster

Le impostazioni di configurazione consentono di ottimizzare un determinato servizio. Per modificare le impostazioni di configurazione di un servizio, selezionare il servizio dalla barra laterale dei **Servizi** (a sinistra). Passare quindi alla scheda **configs (configurazioni** ) nella pagina dei dettagli del servizio.

![Barra laterale dei servizi Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>Modificare le dimensioni dell'heap Java NameNode

Le dimensioni dell'heap Java NameNode dipendono da molti fattori, ad esempio il carico del cluster. Inoltre, il numero di file e il numero di blocchi. Le dimensioni predefinite, pari a 1 GB, vanno bene per la maggior parte dei cluster, anche se alcuni carichi di lavoro richiedono più o meno memoria.

Per modificare le dimensioni dell'heap Java NameNode:

1. Selezionare **HDFS** dalla barra laterale Services (Servizi) e passare alla scheda **Configs** (Configurazioni).

    ![Configurazione di Apache Ambari HDFS](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Trovare l'impostazione **NameNode Java heap size** (Dimensioni dell'heap Java NameNode). È anche possibile usare la casella di testo **filter** (filtro) per digitare e trovare una determinata impostazione. Selezionare l'icona **pen** (penna) accanto al nome dell'impostazione.

    ![Dimensioni heap Java Ambari NameNode Apache](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Digitare il nuovo valore nella casella di testo, quindi premere **INVIO** per salvare la modifica.

    ![Ambari modificare NameNode heap Java size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. Le dimensioni dell'heap Java NameNode sono state modificate in 1 GB da 2 GB.

    ![Modificato NameNode heap Java size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Salvare le modifiche facendo clic sul pulsante verde **Save** (Salva) nella parte superiore della schermata di configurazione.

    ![' Apache Ambari Save Configurations '](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight con l'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Ottimizzare Apache HBase](./optimize-hbase-ambari.md)
* [Ottimizzare Apache Hive](./optimize-hive-ambari.md)
* [Ottimizzare Apache Pig](./optimize-pig-ambari.md)
