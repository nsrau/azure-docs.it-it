---
title: Connettere Excel ad Apache Hadoop mediante Power Query - Azure HDInsight
description: Informazioni su come sfruttare i componenti di business intelligence e usare Power Query per Excel per accedere ai dati archiviati in Hadoop in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: e64905cdfeac8d507df1c3dd92c245cb910a79b2
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033562"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Connettere Excel ad Apache Hadoop mediante Power Query
Una funzionalità chiave della soluzione Big Data di Microsoft è l'integrazione dei componenti di business intelligence (BI) Microsoft con i cluster Apache Hadoop in Azure HDInsight. Un esempio importante è la possibilità di connettere Excel all'account di Archiviazione di Azure che contiene i dati associati al cluster Hadoop usando il componente aggiuntivo Microsoft Power Query per Excel. Questo articolo illustra come configurare e usare Power Query per eseguire una query sui dati associati a un cluster Hadoop gestito con HDInsight.

### <a name="prerequisites"></a>Prerequisiti
Per eseguire le procedure descritte nell'articolo sono necessari gli elementi seguenti:

* **Un cluster HDInsight**. Per configurarne uno, vedere [Introduzione ad Azure HDInsight](./apache-hadoop-linux-tutorial-get-started.md).
* **Una workstation** che esegue il sistema operativo Windows 7, Windows Server 2008 R2 o una versione successiva.
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone oppure Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Installare Power Query
Power Query consente di importare dati derivati o generati da un processo Hadoop in esecuzione su un cluster HDInsight.

In Excel 2016, Power Query è stata integrata nella barra multifunzione Dati nella sezione Get & Transform (Recupera e trasforma). Per le versioni precedenti di Excel, scaricare Microsoft Power Query per Excel dall' [area download Microsoft](https://go.microsoft.com/fwlink/?LinkID=286689) e installarlo.

## <a name="import-hdinsight-data-into-excel"></a>Importare dati di HDInsight in Excel
Il componente aggiuntivo Power Query per Excel consente di importare facilmente dati dal cluster HDInsight in Excel dove strumenti BI quali PowerPivot e Power Map possono essere usati per esaminare, analizzare e presentare i dati.

**Per importare dati da un cluster HDInsight**

1. Aprire Excel.
2. Creare una nuova cartella di lavoro vuota.
3. Eseguire la procedura seguente in base alla versione di Excel:

   - Excel 2016

     - Fare clic sul menu **Dati**, fare clic su **Ottieni dati** della barra multifunzione **Get & Transform Data** (Recupera e trasforma i dati), quindi su **From Azure** (Da Azure) e quindi fare clic su **From Azure HDInsight(HDFS)** (Da Azure HDInsight (HDFS)).

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   - Excel 2013/2010

     - Dal menu **Power Query** scegliere **Da Azure**, quindi fare clic su **Da Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)
       
       **Nota:** Se non viene visualizzato il menu **Power query** , passare a **file** > **Opzioni** > **componenti**aggiuntivi e selezionare componenti aggiuntivi **com** dalla casella di riepilogo a **discesa nella parte** inferiore della pagina. Fare clic sul pulsante **Vai** e verificare che la casella per il componente aggiuntivo Power Query per Excel sia selezionata.
       
       **Nota:** Power Query consente inoltre di importare dati da HDFS facendo clic su **Da altre origini**.
4. Per **Nome account** immettere il nome dell'account di archiviazione BLOB di Azure associato al cluster, quindi fare clic su **OK**. Questo account può essere l'account di archiviazione predefinito o un account di archiviazione collegato.  Il formato è *https://&lt;StorageAccountName>.blob.core.windows.net/* .
5. Per **Chiave account** immettere la chiave dell'account di archiviazione BLOB, quindi fare clic su **Salva**. È necessario immettere le informazioni sull'account solo la prima volta che si accede a questo archivio.
6. Nel riquadro **Strumento di navigazione** a sinistra dell'editor di query fare doppio clic sul nome del contenitore di archiviazione BLOB. Per impostazione predefinita, il nome del contenitore è uguale al nome del cluster.
7. Individuare **HiveSampleData.txt** nella colonna **Name**. Il percorso della cartella è **../hive/warehouse/hivesampletable/** . Fare quindi clic su **Binary** a sinistra di HiveSampleData.txt. HiveSampleData.txt include tutto il cluster. Facoltativamente, è possibile usare un proprio file.

    ![Dati di importazione di Power query di Excel HDI](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

8. Se necessario, è possibile ridenominare le colonne. Quando si è pronti, fare clic sul pulsante **Chiudi e carica**.  I dati vengono caricati nella cartella di lavoro:

    ![Tabella di Power query importata di Excel HDI](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato illustrato come usare Power Query per recuperare dati da HDInsight in Excel. È analogamente possibile recuperare dati da HDInsight nel database SQL di Azure. È inoltre possibile caricare dati in HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Visualizzare i dati Apache Hive con Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualize Interactive Query Hive data with Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualizzare i dati Hive di Interactive Query con Power BI in Azure HDInsight).
* [Usare Apache Zeppelin per eseguire query Apache Hive in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettersi ad Azure HDInsight ed eseguire query Apache Hive usando Strumenti Data Lake per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Usare gli strumenti di Azure HDInsight per Visual Studio Code](../hdinsight-for-vscode.md).
* [Caricare dati in HDInsight](./../hdinsight-upload-data.md).
