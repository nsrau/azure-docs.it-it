---
title: Come eliminare un cluster HDInsight - Azure
description: Informazioni sui vari modi in cui è possibile eliminare un cluster HDInsight di Azure
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807137"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure

La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione è pro-rated al minuto, quindi è sempre necessario eliminare il cluster quando non è più in uso. Questo documento illustra come eliminare un cluster usando il [portale di Azure](https://portal.azure.com), [Azure PowerShell AZ Module](https://docs.microsoft.com/powershell/azure/overview)e l'interfaccia della riga di comando di [Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> L'eliminazione di un cluster HDInsight non comporta l'eliminazione degli account di archiviazione di Azure o Data Lake Storage associati al cluster. È possibile usare nuovamente in futuro i dati archiviati in questi servizi.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Dal menu a sinistra passare a **tutti i servizi** > **Analytics** > **cluster HDInsight** e selezionare il cluster.

3. Nella visualizzazione predefinita selezionare l'icona **Elimina** . Seguire la richiesta per eliminare il cluster.

    ![Pulsante Elimina cluster HDInsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Sostituire `CLUSTERNAME` con il nome del cluster HDInsight nel codice riportato di seguito. Da un prompt di PowerShell immettere il comando seguente per eliminare il cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Sostituire `CLUSTERNAME` con il nome del cluster HDInsight e `RESOURCEGROUP` con il nome del gruppo di risorse nel codice riportato di seguito.  Da un prompt dei comandi immettere quanto segue per eliminare il cluster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
