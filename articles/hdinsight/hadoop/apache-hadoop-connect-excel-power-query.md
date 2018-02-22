---
title: Connettere Excel a Hadoop mediante Power Query - Azure HDInsight | Microsoft Docs
description: Informazioni su come sfruttare i componenti di business intelligence e usare Power Query per Excel per accedere ai dati archiviati in Hadoop in HDInsight.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: jgao
ms.openlocfilehash: 1f5139f3fdb1cc73bb40828021256bed4290e1a2
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="connect-excel-to-hadoop-by-using-power-query"></a>Connettere Excel a Hadoop mediante Power Query
Una funzionalità chiave della soluzione Big Data di Microsoft è l'integrazione dei componenti di business intelligence (BI) Microsoft con i cluster Hadoop in Azure HDInsight. Un esempio importante è la possibilità di connettere Excel all'account di Archiviazione di Azure che contiene i dati associati al cluster Hadoop usando il componente aggiuntivo Microsoft Power Query per Excel. Questo articolo illustra come configurare e usare Power Query per eseguire una query sui dati associati a un cluster Hadoop gestito con HDInsight.

### <a name="prerequisites"></a>prerequisiti
Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Un cluster HDInsight**. Per configurarne uno, vedere [Introduzione ad Azure HDInsight][hdinsight-get-started].
* **Una workstation** che esegue il sistema operativo Windows 7, Windows Server 2008 R2 o una versione successiva.
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone oppure Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Installare Power Query
Power Query consente di importare dati derivati o generati da un processo Hadoop in esecuzione su un cluster HDInsight.

In Excel 2016, Power Query è stata integrata nella barra multifunzione Dati nella sezione Get & Transform (Recupera e trasforma). Per le versioni meno recenti di Excel, scaricare Microsoft Power Query per Excel dall'[Area download Microsoft][powerquery-download] e installarlo.

## <a name="import-hdinsight-data-into-excel"></a>Importare dati di HDInsight in Excel
Il componente aggiuntivo Power Query per Excel consente di importare facilmente dati dal cluster HDInsight in Excel dove strumenti BI quali PowerPivot e Power Map possono essere usati per esaminare, analizzare e presentare i dati.

**Per importare dati da un cluster HDInsight**

1. Aprire Excel.
2. Creare una nuova cartella di lavoro vuota.
3. Eseguire la procedura seguente in base alla versione di Excel:

    - Excel 2016

        - Fare clic sul menu **Dati**, fare clic su **Ottieni dati** della barra multifunzione **Get & Transform Data** (Recupera e trasforma i dati), quindi su **From Azure** (Da Azure) e quindi fare clic su **From Azure HDInsight(HDFS)** (Da Azure HDInsight (HDFS)).

        ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

    - Excel 2013/2010

        - Dal menu **Power Query** scegliere **Da Azure**, quindi fare clic su **Da Microsoft Azure HDInsight**.
   
        ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
        **Nota:** se il menu **Power Query** non è visibile, passare a **File** > **Opzioni** > **Componenti aggiuntivi** e selezionare **Componenti aggiuntivi COM** nella casella di riepilogo **Gestisci** nella parte inferiore della pagina. Fare clic sul pulsante **Vai** e verificare che la casella per il componente aggiuntivo Power Query per Excel sia selezionata.
       
        **Nota:** Power Query consente inoltre di importare dati da HDFS facendo clic su **Da altre origini**.
4. Per **Nome account** immettere il nome dell'account di archiviazione BLOB di Azure associato al cluster, quindi fare clic su **OK**. Questo può essere l'[account di archiviazione predefinito](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account) o un account di archiviazione collegato.  Il formato è *https://&lt;StorageAccountName>.blob.core.windows.net/*.
5. Per **Chiave account** immettere la chiave dell'account di archiviazione BLOB, quindi fare clic su **Salva**. È necessario immettere le informazioni sull'account solo la prima volta che si accede a questo archivio.
6. Nel riquadro **Strumento di navigazione** a sinistra dell'editor di query fare doppio clic sul nome del contenitore di archiviazione BLOB. Per impostazione predefinita, il nome del contenitore è uguale al nome del cluster.
7. Individuare **HiveSampleData.txt** nella colonna **Name**. Il percorso della cartella è **../hive/warehouse/hivesampletable/**. Fare quindi clic su **Binary** a sinistra di HiveSampleData.txt. HiveSampleData.txt include tutto il cluster. Facoltativamente, è possibile usare un proprio file.
   
    ![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Se necessario, è possibile ridenominare le colonne. Quando si è pronti, fare clic sul pulsante **Chiudi e carica**.  I dati vengono caricati nella cartella di lavoro:
   
    ![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare Power Query per recuperare dati da HDInsight in Excel. È analogamente possibile recuperare dati da HDInsight nel database SQL di Azure. È inoltre possibile caricare dati in HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Visualizzare i dati Hive con Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize Interactive Query Hive data with Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight).
* [Usare Zeppelin per eseguire query Hive in Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettersi ad Azure HDInsight ed eseguire query Hive usando Strumenti Data Lake per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
