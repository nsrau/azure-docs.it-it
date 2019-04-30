---
title: Gestire i cluster Apache Hadoop tramite l'interfaccia della riga di comando classica di Azure - Azure HDInsight
description: Informazioni su come usare l'interfaccia della riga di comando classica di Azure per gestire i cluster Apache Hadoop in Azure HDInsight.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tyfox
ms.openlocfilehash: 94ef5a60ecc5d943d78b16a386660049cc52d82e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096020"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-using-the-azure-classic-cli"></a>Gestire cluster Apache Hadoop in HDInsight tramite l'interfaccia della riga di comando classica di Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Informazioni su come usare l'[interfaccia della riga di comando classica di Azure](../cli-install-nodejs.md) per gestire i cluster [Apache Hadoop](https://hadoop.apache.org/) in Azure HDInsight. L'interfaccia della riga di comando classica viene implementata in Node.js. Può essere usato in tutte le piattaforme che supportano Node.js, incluse Windows, Mac e Linux.

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Interfaccia della riga di comando classica di Azure**: per informazioni sull'installazione e la configurazione, vedere [Installare e configurare l'interfaccia della riga di comando classica di Azure](../cli-install-nodejs.md).
* **Collegarsi ad Azure**, usando il comando seguente:

    ```cli
    azure login
    ```
  
    Per altre informazioni sull'autenticazione con un account aziendale o dell'istituto di istruzione, vedere [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando classica di Azure](/cli/azure/authenticate-azure-cli).
* **Passare alla modalità Gestione risorse di Azure**, usando il comando seguente:
  
    ```cli
    azure config mode arm
    ```

Per ottenere assistenza, utilizzare l’opzione **-h** .  Ad esempio: 

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Creare cluster con l'interfaccia della riga di comando
Vedere [Creare cluster Hadoop in HDInsight tramite l'interfaccia della riga di comando classica di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Elencare i cluster e visualizzarne i dettagli
Utilizzare i comandi seguenti per elencare e mostrare i dettagli dei cluster:

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![Visualizzazione della riga di comando dell'elenco di cluster][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Eliminare cluster
Utilizzare il comando seguente per eliminare un cluster:

```cli
azure hdinsight cluster delete <Cluster Name>
```

È inoltre possibile eliminare un cluster eliminando il gruppo di risorse che lo contiene. Tenere presente che questa operazione eliminerà tutte le risorse nel gruppo, compreso l’account di archiviazione predefinito.

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>Ridimensionare i cluster
Per modificare le dimensioni del cluster Apache Hadoop:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Abilitare/disabilitare l'accesso HTTP per un cluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come eseguire diverse attività amministrative relative ai cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Gestire cluster Apache Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-portal-linux.md)
* [Amministrare HDInsight con Azure PowerShell][hdinsight-admin-powershell]
* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Come usare l'interfaccia della riga di comando classica di Azure][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Elencare e visualizzare i cluster"
