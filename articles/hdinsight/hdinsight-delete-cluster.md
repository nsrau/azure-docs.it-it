---
title: Come eliminare un cluster HDInsight | Documentazione Microsoft
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
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 67442bf4b04f6f3799d30f7ce26547c8145d9168


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>Come eliminare un cluster HDInsight
La fatturazione del cluster HDInsight è calcolata al minuto, inizia dopo che il cluster è stato creato e termina quando il cluster viene eliminato, perciò è sempre opportuno eliminare un cluster quando non lo si usa più. In questo documento si apprenderà come eliminare un cluster tramite il portale di Azure, Azure PowerShell e l’interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> L'eliminazione di un cluster HDInsight non comporta l'eliminazione degli account di archiviazione di Azure associati al cluster. Ciò consente di mantenere e riutilizzare i dati archiviati dal cluster.
> 
> 

## <a name="azure-portal"></a>Portale di Azure
1. Eseguire l’accesso al [portale di Azure](https://portal.azure.com) e selezionare il cluster HDInsight. Se il cluster HDInsight non è stato bloccato sul dashboard, è possibile cercarlo per nome usando il campo Cerca (icona della lente di ingrandimento) sul lato destro della barra di spostamento.
   
    ![ricerca nel portale](./media/hdinsight-delete-cluster/navbar.png)
2. Dopo l'apertura del pannello per il cluster, selezionare l'icona **Elimina**. Quando richiesto, selezionare **Sì** per eliminare il cluster.
   
    ![icona di eliminazione](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell
Da un prompt di PowerShell, usare il comando seguente per eliminare il cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Da un prompt, usare il comando seguente per eliminare il cluster:

    azure hdinsight cluster delete CLUSTERNAME

Sostituire **CLUSTERNAME** con il nome del cluster HDInsight.




<!--HONumber=Nov16_HO3-->


