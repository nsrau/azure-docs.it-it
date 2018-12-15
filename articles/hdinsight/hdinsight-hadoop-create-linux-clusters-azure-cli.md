---
title: Creare cluster Apache Hadoop con l'interfaccia della riga di comando classica di Azure - Azure HDInsight
description: Informazioni su come creare cluster HDInsight tramite l'interfaccia multipiattaforma della riga di comando classica di Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: f82ac972e54dac6df5a913a8059417b701e2f7e0
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191581"
---
# <a name="create-hdinsight-clusters-using-the-azure-classic-cli"></a>Creare cluster HDInsight tramite l'interfaccia della riga di comando di Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

La procedura presentata in questo documento illustra come creare un cluster HDInsight 3.5 usando l'interfaccia della riga di comando classica di Azure.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* **Interfaccia della riga di comando classica di Azure**. I passaggi descritti nel presente documento sono stati testati con la versione 0.10.14 dell'interfaccia della riga di comando classica di Azure.

## <a name="log-in-to-your-azure-subscription"></a>Accedere alla sottoscrizione di Azure

Seguire i passaggi descritti in [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli) e connettersi alla sottoscrizione usando il metodo **login**.

## <a name="create-a-cluster"></a>Creare un cluster

I passaggi seguenti devono essere eseguiti dalla riga di comando, ad esempio PowerShell o Bash.

1. Per eseguire l'autenticazione della sottoscrizione di Azure, usare il comando seguente:

        azure login

    Occorre specificare il nome e la password. Se si dispone di più sottoscrizioni di Azure, usare `azure account set <subscriptionname>` per impostare la sottoscrizione utilizzata dai comandi dell'interfaccia della riga di comando classica di Azure.

2. Passare alla modalità Gestione risorse di Azure usando il comando seguente:

        azure config mode arm

3. Creare un gruppo di risorse. Questo gruppo di risorse contiene il cluster HDInsight e l'account di archiviazione associato.

        azure group create groupname location

    * Sostituire `groupname` con un nome univoco per il gruppo.

    * Sostituire `location` con l'area geografica in cui si vuole creare il gruppo.

       Per un elenco di località valide, usare il comando `azure location list` e quindi una delle località della colonna `Name`.

4. Creare un account di archiviazione. Questo account di archiviazione verrà usato come risorsa di archiviazione predefinita per il cluster HDInsight.

        azure storage account create -g groupname --sku-name RAGRS -l location --kind Storage storagename

    * Sostituire `groupname` con il nome del gruppo creato nel passaggio precedente.

    * Sostituire `location` con la stessa località usata nel passaggio precedente.

    * Sostituire `storagename` con un nome univoco per l'account di archiviazione.

        > [!NOTE]
        > Per altre informazioni sui parametri usati in questo comando, usare `azure storage account create -h` per visualizzare la Guida relativa a questo comando.

5. Recuperare la chiave usata per accedere all'account di archiviazione.

        azure storage account keys list -g groupname storagename

    * Sostituire `groupname` con il nome del gruppo di risorse.
    * Sostituire `storagename` con il nome per l'account di archiviazione.

      Nei dati restituiti salvare il valore `key` per `key1`.

6. Creare un cluster HDInsight

        azure hdinsight cluster create -g groupname -l location -y Linux --clusterType Hadoop --defaultStorageAccountName storagename.blob.core.windows.net --defaultStorageAccountKey storagekey --defaultStorageContainer clustername --workerNodeCount 3 --userName admin --password httppassword --sshUserName sshuser --sshPassword sshuserpassword clustername

    * Sostituire `groupname` con il nome del gruppo di risorse.

    * Sostituire `Hadoop` con il tipo di cluster da creare, ad esempio, `Hadoop`, `HBase`, `Kafka`, `Spark` o `Storm`.

      > [!IMPORTANT]
      > Sono disponibili molti tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster.

    * Sostituire `location` con la stessa località usata nel passaggio precedente.

    * Sostituire `storagename` con il nome per l'account di archiviazione.

    * Sostituire `storagekey` con la chiave ottenuta nel passaggio precedente.

    * Per il parametro `--defaultStorageContainer` usare lo stesso nome usato per il cluster.

    * Sostituire `admin` e `httppassword` con il nome e la password da usare per l'accesso al cluster tramite HTTPS.

    * Sostituire `sshuser` e `sshuserpassword` con il nome utente e la password da usare per l'accesso al cluster tramite SSH

      > [!IMPORTANT]
      > L'esempio precedente crea un cluster con due nodi di ruolo di lavoro. È inoltre possibile modificare il numero di nodi del ruolo di lavoro dopo la creazione del cluster eseguendo le operazioni di ridimensionamento. Se si prevede di usare più di 32 nodi del ruolo di lavoro, è necessario selezionare una dimensione del nodo head con almeno 8 core e 14 GB di RAM. È possibile impostare le dimensioni del nodo head usando il parametro `--headNodeSize` durante la creazione del cluster.
      >
      > Per altre informazioni sulle dimensioni di nodo e i costi associati, vedere [Prezzi di HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
      
      Il processo di creazione del cluster può richiedere alcuni minuti, in genere circa 15.

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver correttamente creato un cluster HDInsight tramite l'interfaccia della riga di comando di Azure, usare le informazioni seguenti per acquisire familiarità con il cluster:

### <a name="apache-hadoop-clusters"></a>Cluster Apache Hadoop

* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hadoop/hdinsight-use-pig.md)
* [Usare Apache Hadoop MapReduce in HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Cluster Apache HBase

* [Introduzione ad Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Cluster Apache Storm

* [Sviluppare topologie Java per Apache Storm in HDInsight](storm/apache-storm-develop-java-topology.md)
* [Usare i componenti di Python in Apache Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Distribuzione e monitoraggio di topologie con Apache Storm in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
