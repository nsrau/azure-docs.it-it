---
title: Come eliminare un cluster HDInsight - Azure
description: Informazioni sui vari modi in cui è possibile eliminare un cluster HDInsight di Azure
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 97aa37d5bc99295de7b827467b8c6db551c34ef7
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547538"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure

La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione è pro-rated al minuto, quindi è sempre necessario eliminare il cluster quando non è più in uso. Questo documento illustra come eliminare un cluster usando il [portale di Azure](https://portal.azure.com), [Azure PowerShell AZ Module](/powershell/azure/)e l'interfaccia della riga di comando di [Azure](/cli/azure/).

> [!IMPORTANT]  
> L'eliminazione di un cluster HDInsight non comporta l'eliminazione degli account di archiviazione di Azure o Data Lake Storage associati al cluster. È possibile usare nuovamente in futuro i dati archiviati in questi servizi.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Dal menu a sinistra passare a **tutti i servizi**  >  **analisi**  >  **HDInsight cluster** e selezionare il cluster.

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