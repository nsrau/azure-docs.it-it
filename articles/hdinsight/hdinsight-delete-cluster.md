<properties
pageTitle="Come eliminare un cluster HDInsight | Azure"
description="Informazioni sui vari modi per eliminare un cluster HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="03/07/2016"
ms.author="larryfr"/>

#Come eliminare un cluster HDInsight

I cluster HDInsight vengono fatturati con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più. In questo documento si apprenderà come eliminare un cluster tramite il portale di Azure, Azure PowerShell e l’interfaccia della riga di comando di Azure.

> [AZURE.IMPORTANT] L'eliminazione di un cluster HDInsight non comporta l'eliminazione degli account di archiviazione di Azure associati al cluster. Ciò consente di mantenere e riutilizzare i dati archiviati dal cluster.

##Portale di Azure

1. Eseguire l’accesso al [portale di Azure](https://portal.azure.com) e selezionare il cluster HDInsight. Se il cluster HDInsight non è stato bloccato sul dashboard, è possibile cercarlo per nome usando il campo Cerca (icona della lente di ingrandimento) sul lato destro della barra di spostamento.

    ![ricerca nel portale](./media/hdinsight-delete-cluster/navbar.png)

2. Dopo che il pannello si è aperto, selezionare l’icona __Elimina__. Quando richiesto, selezionare __Sì__ per eliminare il cluster.

    ![icona di eliminazione](./media/hdinsight-delete-cluster/deletecluster.png)

##Azure PowerShell

> [AZURE.NOTE] Se Azure PowerShell non è stato installato e configurato, usare la procedura riportata nel documento [Installare e configurare Azure PowerShell](../powershell-install-configure.md).

Da un prompt di PowerShell, usare il comando seguente per eliminare il cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Sostituire __CLUSTERNAME__ con il nome del cluster HDInsight.

##Interfaccia della riga di comando di Azure

> [AZURE.NOTE] Se l’interfaccia della riga di comandi di Azure non è stata installata e configurata, usare la procedura riportata nel documento [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

Da un prompt, usare il comando seguente per eliminare il cluster:

    azure hdinsight cluster delete CLUSTERNAME
    
Sostituire __CLUSTERNAME__ con il nome del cluster HDInsight.

<!---HONumber=AcomDC_0309_2016-->