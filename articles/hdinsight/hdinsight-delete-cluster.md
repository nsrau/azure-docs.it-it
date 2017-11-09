---
title: Come eliminare un cluster HDInsight - Azure | Microsoft Docs
description: Informazioni sui vari modi per eliminare un cluster HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 6bacd40627b815c949491b70f8290e40b79e488c
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2017
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure

La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più. Questo documento spiega come eliminare un cluster tramite il portale di Azure, Azure PowerShell e l'interfaccia della riga di comando di Azure 1.0.

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

## <a name="azure-cli-10"></a>Interfaccia della riga di comando di Azure 1.0

Da un prompt, usare il comando seguente per eliminare il cluster:

    azure hdinsight cluster delete CLUSTERNAME

Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

> [!NOTE]
> L'interfaccia della riga di comando di Azure 2.0 non supporta attualmente l'eliminazione dei cluster HDInsight (23 ottobre 2017).