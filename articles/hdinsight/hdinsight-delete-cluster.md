---
title: Come eliminare un cluster HDInsight - Azure
description: Informazioni sui vari modi per eliminare un cluster HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721010"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure

La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più. In questo documento descrive come eliminare un cluster usando il [portale di Azure](https://portal.azure.com), [modulo di Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)e il [CLI di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> L'eliminazione di un cluster HDInsight non comporta l'eliminazione degli account di archiviazione di Azure o Data Lake Storage associati al cluster. È possibile usare nuovamente in futuro i dati archiviati in questi servizi.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu a sinistra, passare a **tutti i servizi** > **Analitica** > **i cluster HDInsight** e selezionare il cluster.

3. Selezionare la visualizzazione predefinita, il **eliminare** icona. Seguire le istruzioni per eliminare il cluster.
   
    ![icona di eliminazione](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Modulo Azure PowerShell Az

Sostituire `CLUSTERNAME` con il nome del cluster HDInsight nel codice seguente. Al prompt di PowerShell, immettere il comando seguente per eliminare il cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Sostituire `CLUSTERNAME` con il nome del cluster HDInsight, e `RESOURCEGROUP` con il nome del gruppo di risorse nel codice seguente.  Da un prompt dei comandi, immettere il comando seguente per eliminare il cluster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
