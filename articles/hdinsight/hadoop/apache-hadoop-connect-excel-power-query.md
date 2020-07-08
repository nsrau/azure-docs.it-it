---
title: Connettere Excel ad Apache Hadoop mediante Power Query - Azure HDInsight
description: Informazioni su come sfruttare i componenti di business intelligence e usare Power Query per Excel per accedere ai dati archiviati in Hadoop in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75435792"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Connettere Excel ad Apache Hadoop mediante Power Query

Una funzionalità chiave della soluzione Big Data di Microsoft è l'integrazione dei componenti di business intelligence (BI) Microsoft con i cluster Apache Hadoop in Azure HDInsight. Un esempio importante è la possibilità di connettere Excel all'account di Archiviazione di Azure che contiene i dati associati al cluster Hadoop usando il componente aggiuntivo Microsoft Power Query per Excel. Questo articolo illustra come configurare e usare Power Query per eseguire una query sui dati associati a un cluster Hadoop gestito con HDInsight.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).
* Una workstation che esegue Windows 10, 7, Windows Server 2008 R2 o un sistema operativo successivo.
* Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone oppure Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Installare Microsoft Power Query

Power Query consente di importare dati derivati o generati da un processo Hadoop in esecuzione su un cluster HDInsight.

In Excel 2016, Power Query è stata integrata nella barra multifunzione Dati nella sezione Get & Transform (Recupera e trasforma). Per le versioni meno recenti di Excel, scaricare Microsoft Power Query per Excel dall'[Area download Microsoft](https://go.microsoft.com/fwlink/?LinkID=286689) e installarlo.

## <a name="import-hdinsight-data-into-excel"></a>Importare dati di HDInsight in Excel

Il componente aggiuntivo Power Query per Excel consente di importare facilmente dati dal cluster HDInsight in Excel dove strumenti BI quali PowerPivot e Power Map possono essere usati per esaminare, analizzare e presentare i dati.

1. Avviare Excel.

1. Creare una nuova cartella di lavoro vuota.

1. Eseguire la procedura seguente in base alla versione di Excel:

   * Excel 2016

     * Selezionare > **dati**  >  **ottenere i dati**  >  **da Azure**  >  **da Azure HDInsight (HDFS)**.

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * Selezionare **Power query**  >  **da Azure**  >  **da Microsoft Azure HDInsight**.

       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Nota:** Se non viene visualizzato il menu **Power query** , passare a **file**  >  **Opzioni**  >  **componenti**aggiuntivi e selezionare componenti aggiuntivi **com** dalla casella di riepilogo a discesa nella parte **Manage** inferiore della pagina. Fare clic sul pulsante **Vai** e verificare che la casella per il componente aggiuntivo Power Query per Excel sia selezionata.

       **Nota:** Power Query consente inoltre di importare dati da HDFS selezionando **da altre origini**.

1. Nella finestra di dialogo **Azure HDInsight (HDFS)** , nella casella di testo **nome account o URL** , immettere il nome dell'account di archiviazione BLOB di Azure associato al cluster. Selezionare **OK**. Questo può essere l'account di archiviazione predefinito o un account di archiviazione collegato.  Il formato è `https://StorageAccountName.blob.core.windows.net/`.

1. Per **chiave account**immettere la chiave per l'account di archiviazione BLOB e quindi selezionare **Connetti**. È necessario immettere le informazioni sull'account solo la prima volta che si accede a questo archivio.

1. Nel riquadro **strumento di navigazione** a sinistra dell'editor di query fare doppio clic sul nome del contenitore di archiviazione BLOB associato al cluster. Per impostazione predefinita, il nome del contenitore è uguale al nome del cluster.

1. Individuare **HiveSampleData.txt** nella colonna **nome** (il percorso della cartella è **.. /hive/warehouse/hivesampletable/**) e quindi selezionare **Binary** a sinistra di HiveSampleData.txt. HiveSampleData.txt include tutto il cluster. Facoltativamente, è possibile usare un proprio file.

    ![Dati di importazione di Power query di Excel HDI](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Se necessario, è possibile ridenominare le colonne. Quando si è pronti, selezionare **chiudi & caricare**.  I dati vengono caricati nella cartella di lavoro:

    ![Tabella di Power query importata di Excel HDI](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come usare Power Query per recuperare dati da HDInsight in Excel. È analogamente possibile recuperare dati da HDInsight nel database SQL di Azure. È anche possibile caricare i dati in HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Visualizzare i dati di Apache hive con Microsoft Power bi in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualizza i dati di hive Interactive query con Power bi in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Usare Apache Zeppelin per eseguire query Apache hive in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Connettere Excel a HDInsight mediante Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connettersi ad Azure HDInsight ed eseguire query Apache hive usando data Lake Tools per Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Usare lo strumento HDInsight di Azure per Visual Studio Code](../hdinsight-for-vscode.md).
* [Caricare i dati in HDInsight](./../hdinsight-upload-data.md).
