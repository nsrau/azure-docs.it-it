---
title: Creare un cluster Hadoop con account di archiviazione con trasferimento sicuro in Azure HDInsight
description: Informazioni su come creare cluster HDInsight con account di archiviazione di Azure con trasferimento sicuro abilitato.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/24/2018
ms.openlocfilehash: 5ad49c8e28f0e91b18af6dc485489d11464fbc0d
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413388"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Creare un cluster Apache Hadoop con account di archiviazione con trasferimento sicuro in Azure HDInsight

La funzionalità [Trasferimento sicuro obbligatorio](../storage/common/storage-require-secure-transfer.md) aumenta la sicurezza dell'account di archiviazione di Azure perché consente l'invio di tutte le richieste all'account solo tramite connessioni sicure. Questa funzionalità e lo schema wasbs sono supportati solo dal cluster HDInsight versione 3.6 o successiva.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario avere:

* **Sottoscrizione di Azure**: per creare un account di valutazione gratuito di un mese, passare ad [azure.microsoft.com/free](https://azure.microsoft.com/free).
* **Account di archiviazione di Azure con trasferimento sicuro abilitato**. Per istruzioni, vedere [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md) e [Richiedere il trasferimento sicuro](../storage/common/storage-require-secure-transfer.md).
* **Contenitore BLOB nell'account di archiviazione**.

## <a name="create-cluster"></a>Creare cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


In questa sezione viene creato un cluster Hadoop in HDInsight usando un [modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Il modello è disponibile in [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). Per questa esercitazione non è necessario conoscere il modello di Resource Manager. Per altri metodi di creazione di cluster e per informazioni sulle proprietà usate in questa esercitazione, vedere [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Fare clic sull'immagine seguente per accedere ad Azure e aprire il modello di Resource Manager nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Seguire le istruzioni per creare il cluster con le specifiche seguenti: 

    - Specificare HDInsight versione 3.6. È necessaria la versione 3.6 o successiva.
    - Specificare un account di archiviazione con trasferimento sicuro abilitato.
    - Usare un nome breve per l'account di archiviazione.
    - È necessario creare prima l'account di archiviazione e il contenitore BLOB.

      Per le istruzioni, vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

Se si usa l'azione di script per fornire i file di configurazione, è necessario usare wasbs nelle impostazioni seguenti:

- fs.defaultFS (core-site)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Aggiungere altri account di archiviazione

Sono disponibili diverse opzioni per aggiungere altri account di archiviazione con trasferimento sicuro abilitato:

- Modificare il modello di Azure Resource Manager nell'ultima sezione.
- Creare un cluster usando il [portale di Azure](https://portal.azure.com) e specificare l'account di archiviazione collegato.
- Usare l'azione script per aggiungere altri account di archiviazione con trasferimento sicuro abilitato a un cluster HDInsight esistente. Per altre informazioni, vedere [Aggiungere altri account di archiviazione a HDInsight](hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come creare un cluster HDInsight e abilitare il trasferimento sicuro per gli account di archiviazione.

Per altre informazioni sull'analisi dei dati con HDInsight, vedere gli articoli seguenti:

* Per altre informazioni sull'uso di [Apache Hive](https://hive.apache.org/) con HDInsight, incluse le procedure per eseguire query Hive da Visual Studio, vedere [Usare Apache Hive con HDInsight][hdinsight-use-hive].
* Per informazioni su [Apache Pig](https://pig.apache.org/), un linguaggio usato per la trasformazione dei dati, vedere [Usare Apache Pig con HDInsight][hdinsight-use-pig].
* Per informazioni su [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), che consente di scrivere programmi per l'elaborazione dei dati in Hadoop, vedere [Usare Apache Hadoop MapReduce con HDInsight][hdinsight-use-mapreduce].
* Per informazioni sull'uso di HDInsight Tools per Visual Studio per analizzare i dati in HDInsight, vedere [Introduzione all'uso di Apache Hadoop Tools per Visual Studio per HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Per altre informazioni sulle modalità di archiviazione dei dati in HDInsight o sull'importazione dei dati in HDInsight, vedere gli articoli seguenti:

* Per informazioni sul modo in cui HDInsight usa Archiviazione di Azure, vedere [Usare Archiviazione di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Per informazioni sulle modalità di caricamento di dati in HDInsight, vedere [Caricare dati in HDInsight][hdinsight-upload-data].

Per altre informazioni sulla creazione o la gestione di un cluster HDInsight, vedere gli articoli seguenti:

* Per altre informazioni sulla gestione di cluster HDInsight basati su Linux, vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md).
* Per altre informazioni sulle opzioni che è possibile selezionare durante la creazione di un cluster HDInsight, vedere [Creare cluster Hadoop basati su Linux in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Se si ha familiarità con Linux e Apache Hadoop ma si vogliono informazioni specifiche su Hadoop in HDInsight, vedere [Uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md). In questo articolo sono disponibili informazioni quali:

  * URL per i servizi ospitati nel cluster, ad esempio [Apache Ambari](https://ambari.apache.org/) e [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * Il percorso dei file [Apache Hadoop](https://hadoop.apache.org/) e gli esempi nel file system locale
  * L'uso di archiviazione di Azure (WASB) anziché di [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) come archivio predefinito di dati

[1]: ../HDInsight/hadoop/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
