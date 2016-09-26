<properties
pageTitle="Come eliminare un cluster HDInsight | Azure"
description="Informazioni sui vari modi per eliminare un cluster HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>  

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/09/2016"
ms.author="larryfr"/>  

#Come eliminare un cluster HDInsight

La fatturazione del cluster HDInsight è calcolata al minuto, inizia dopo che il cluster è stato creato e termina quando il cluster viene eliminato, perciò è sempre opportuno eliminare un cluster quando non lo si usa più. In questo documento si apprenderà come eliminare un cluster tramite il portale di Azure, Azure PowerShell e l’interfaccia della riga di comando di Azure.

> [AZURE.IMPORTANT] L'eliminazione di un cluster HDInsight non comporta l'eliminazione degli account di archiviazione di Azure associati al cluster. Ciò consente di mantenere e riutilizzare i dati archiviati dal cluster.

##Portale di Azure

1. Eseguire l’accesso al [portale di Azure](https://portal.azure.com) e selezionare il cluster HDInsight. Se il cluster HDInsight non è stato bloccato sul dashboard, è possibile cercarlo per nome usando il campo Cerca (icona della lente di ingrandimento) sul lato destro della barra di spostamento.

    ![ricerca nel portale](./media/hdinsight-delete-cluster/navbar.png)

2. Dopo che il pannello si è aperto, selezionare l’icona __Elimina__. Quando richiesto, selezionare __Sì__ per eliminare il cluster.

    ![icona di eliminazione](./media/hdinsight-delete-cluster/deletecluster.png)

##Azure PowerShell

Da un prompt di PowerShell, usare il comando seguente per eliminare il cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Sostituire __CLUSTERNAME__ con il nome del cluster HDInsight.

##Interfaccia della riga di comando di Azure

Da un prompt, usare il comando seguente per eliminare il cluster:

    azure hdinsight cluster delete CLUSTERNAME
    
Sostituire __CLUSTERNAME__ con il nome del cluster HDInsight.

<!---HONumber=AcomDC_0914_2016-->