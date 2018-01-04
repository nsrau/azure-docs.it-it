---
title: 'Gestire i cluster Hadoop tramite l''interfaccia della riga di comando di Azure: Azure HDInsight | Microsoft Docs'
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per gestire i cluster Hadoop in Azure HDInsight. L'interfaccia della riga di comando di Azure funziona in Windows, Mac e Linux.
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jgao
ms.openlocfilehash: 491dbd157255dc4fa7f77178f9486959ba4847a1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Gestire cluster Hadoop in HDInsight tramite la CLI di Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Informazioni su come usare l' [Interfaccia della riga di comando di Azure](../cli-install-nodejs.md) per gestire i cluster Hadoop in Azure HDInsight. L'interfaccia della riga di comando di Azure viene implementata in Node.js. Può essere usato in tutte le piattaforme che supportano Node.js, incluse Windows, Mac e Linux. Attualmente HDInsight non supporta l'[interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/overview).

In questo articolo viene illustrato solo l’uso dell’interfaccia della riga di comando di Azure con HDInsight. Per una guida generale sull'uso dell'interfaccia della riga di comando di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo è necessario:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Interfaccia della riga di comando di Azure** : per informazioni sull’installazione e la configurazione vedere [Installare e configurare l’interfaccia della riga di comando di Azure](../cli-install-nodejs.md) .
* **Collegarsi ad Azure**, usando il comando seguente:

    ```cli
    azure login
    ```
  
    Per altre informazioni sull'autenticazione con un account aziendale o dell'istituto di istruzione, vedere [Connettersi a una sottoscrizione Azure dall'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).
* **Passare alla modalità Gestione risorse di Azure**, usando il comando seguente:
  
    ```cli
    azure config mode arm
    ```

Per ottenere assistenza, utilizzare l’opzione **-h** .  Ad esempio: 

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>Creare cluster con l'interfaccia della riga di comando
Vedere [Creare cluster Hadoop in HDInsight tramite l'interfaccia della riga di comando di Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

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
Per modificare le dimensioni del cluster Hadoop:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>Abilitare/disabilitare l'accesso HTTP per un cluster

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Abilitare/disabilitare l'accesso RDP per un cluster

```cli
azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
azure hdinsight cluster disable-rdp-access [options] <Cluster Name>
```

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come eseguire diverse attività amministrative relative ai cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight con il portale di Azure][hdinsight-admin-portal]
* [Amministrare HDInsight con Azure PowerShell][hdinsight-admin-powershell]
* [Introduzione ad Azure HDInsight][hdinsight-get-started]
* [Come usare l'interfaccia della riga di comando di Azure][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "Elencare e visualizzare i cluster"
