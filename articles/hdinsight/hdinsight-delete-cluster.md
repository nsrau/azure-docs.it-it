---
title: Come eliminare un cluster HDInsight - Azure
description: Informazioni sui vari modi per eliminare un cluster HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 707112f755e6846622d2fde9de466551276925b8
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494359"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Eliminare un cluster HDInsight usando il browser, PowerShell o l'interfaccia della riga di comando classica di Azure

La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più. In questo documento viene descritto come eliminare un cluster usando il [portale di Azure](https://portal.azure.com), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) e l'interfaccia della riga di comando classica di Azure.

> [!IMPORTANT]
> L'eliminazione di un cluster HDInsight non comporta l'eliminazione degli account di archiviazione di Azure o Data Lake Store associati al cluster. È possibile usare nuovamente in futuro i dati archiviati in questi servizi.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare il cluster HDInsight. Se il cluster HDInsight non è bloccato sul dashboard, è possibile cercarlo per nome usando il campo di ricerca.
   
    ![ricerca nel portale](./media/hdinsight-delete-cluster/navbar.png)

2. Nelle impostazioni del cluster selezionare l'icona **Elimina**. Quando richiesto, selezionare **Sì** per eliminare il cluster.
   
    ![icona di eliminazione](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Da un prompt di PowerShell, usare il comando seguente per eliminare il cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

## <a name="azure-classic-cli"></a>Interfaccia della riga di comando classica di Azure

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Da un prompt, usare il comando seguente per eliminare il cluster:

    azure hdinsight cluster delete CLUSTERNAME

Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.
