---
title: Creare un cluster Apache Spark in Azure HDInsight | Microsoft Docs
description: Guida introduttiva di HDInsight Spark per la creazione di un cluster Apache Spark in HDInsight.
keywords: guida introduttiva spark,spark interattivo,query interattiva,hdinsight spark,azure spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/21/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: ad4330a1fc7f8de154d9aaa8df3acc2ab59b9dc1
ms.contentlocale: it-it
ms.lasthandoff: 07/24/2017

---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Creare un cluster Apache Spark in Azure HDInsight

Questo articolo illustra la creazione di un cluster Apache Spark in Azure HDInsight. Per informazioni su Spark in HDInsight, vedere [Panoramica: Apache Spark in Azure HDInsight](hdinsight-apache-spark-overview.md).

   ![Diagramma che descrive i passaggi per la creazione di un cluster Apache Spark in Azure HDInsight](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Guida introduttiva di Spark con Apache Spark in HDInsight. Passaggi illustrati: creare un cluster; eseguire una query interattiva Spark")

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Prima di iniziare questa esercitazione, è necessario disporre di un abbonamento ad Azure. Vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Creare un cluster HDInsight Spark

In questa sezione viene creato un cluster HDInsight Spark usando un [modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Per altri metodi di creazione dei cluster, vedere [Creare cluster HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Fare clic sull'immagine seguente per aprire il modello nel portale di Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Immettere i valori seguenti:

    ![Creare un cluster HDInsight Spark usando un modello di Azure Resource Manager](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Creare un cluster Spark in HDInsight usando un modello di Azure Resource Manager")

    * **Sottoscrizione**: selezionare la sottoscrizione di Azure per questo cluster.
    * **Gruppo di risorse**: creare un gruppo di risorse o selezionarne uno esistente. Il gruppo di risorse viene usato per gestire le risorse di Azure per i progetti.
    * **Posizione**: selezionare una posizione per il gruppo di risorse. Il modello usa questa posizione per la creazione del cluster e per l'archiviazione del cluster predefinita.
    * **Nome cluster**: immettere un nome per il cluster HDInsight che si vuole creare.
    * **Versione di Spark**: selezionare **2.0** come versione da installare nel cluster.
    * **Nome utente e password di accesso del cluster**: il nome dell'account di accesso predefinito è admin.
    * **Nome utente e password SSH**.

   Annotare questi valori  perché sarà necessario usarli più avanti nell'esercitazione.

3. Selezionare **Accetto le condizioni riportate sopra**, selezionare **Aggiungi al dashboard** e quindi fare clic su **Acquista**. È possibile visualizzare un nuovo riquadro denominato Invio della distribuzione per Distribuzione modello. La creazione del cluster richiede circa 20 minuti.

Se si verifica un problema con la creazione di cluster HDInsight, è possibile che non si abbiano le autorizzazioni necessarie per questa operazione. Per altre informazioni, vedere i [requisiti dei controlli di accesso](hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> Questo articolo crea un cluster Spark che usa [BLOB del servizio di archiviazione di Azure come risorsa di archiviazione del cluster](hdinsight-hadoop-use-blob-storage.md). È anche possibile creare un cluster Spark che usa [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) come risorsa di archiviazione predefinita. Per istruzioni, vedere [Creare un cluster HDInsight con Archivio Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-a-hive-query-using-spark-sql"></a>Eseguire una query Hive usando Spark SQL

Quando si usa un server Jupyter Notebook configurato per il cluster HDInsight Spark, si ottiene un elemento `sqlContext` predefinito che può essere usato per eseguire query Hive con Spark SQL. Questa sezione illustra come avviare un Jupyter Notebook e quindi eseguire una query Hive di base.

1. Aprire il [portale di Azure](https://portal.azure.com/).

2. Se si è scelto di aggiungere il cluster al dashboard, fare clic sul riquadro del cluster nel dashboard per avviare il relativo pannello.

    Se il cluster non è stato aggiunto al dashboard, nel riquadro sinistro fare clic su **Cluster HDInsight** e quindi sul cluster creato.

3. In **Collegamenti rapidi** fare clic su **Dashboard cluster** e quindi su **Notebook di Jupyter**. Se richiesto, immettere le credenziali per il cluster.

   ![Aprire Jupyter Notebook per eseguire la query interattiva Spark SQL](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Aprire Jupyter Notebook per eseguire la query interattiva Spark SQL")

   > [!NOTE]
   > È anche possibile accedere all'istanza di Jupyter Notebook per il cluster aprendo l'URL seguente nel browser. Sostituire **CLUSTERNAME** con il nome del cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Creare un notebook. Fare clic su **Nuovo** e quindi su **PySpark**.

   ![Creare un'istanza di Jupyter Notebook per eseguire la query interattiva Spark SQL](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Creare un'istanza di Jupyter Notebook per eseguire la query interattiva Spark SQL")

   Un nuovo notebook verrà creato e aperto con il nome Untitled (Untitled.pynb).

4. Fare clic sul nome del notebook nella parte superiore e, se si vuole, immettere un nome descrittivo.

    ![Specificare un nome per l'istanza di Jupyter Notebook dalla quale eseguire la query interattiva Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Specificare un nome per l'istanza di Jupyter Notebook dalla quale eseguire la query interattiva Spark")

5.  Incollare il codice seguente in una cella vuota e quindi premere **MAIUSC + INVIO** per eseguire il codice. Nel codice seguente, `%%sql` (denominato "magic SQL") indica a Jupyter Notebook di usare l'elemento `sqlContext` predefinito per eseguire la query Hive. La query recupera le prime 10 righe di una tabella Hive (**hivesampletable**) disponibile in tutti i cluster HDInsight per impostazione predefinita.

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![Query Hive in HDInsight Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Query Hive in HDInsight Spark")

    Per altre informazioni sul magic `%%sql` e sui contesti predefiniti, vedere i [kernel Jupyter disponibili per un cluster HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

    > [!NOTE]
    > Ogni volta che si esegue una query in Jupyter, il titolo della finestra del Web browser visualizza lo stato **(Occupato)** accanto al titolo del notebook. È anche visibile un cerchio pieno accanto al testo **PySpark** nell'angolo in alto a destra. Al termine del processo viene visualizzato un cerchio vuoto.
    >
    >
    
6. La schermata si aggiornerà per visualizzare l'output della query.

    ![Output della query Hive in HDInsight Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Output della query Hive in HDInsight Spark")

7. Al termine dell'esecuzione dell'applicazione, arrestare il notebook per rilasciare le risorse del cluster. A tale scopo, dal menu **File** del notebook fare clic su **Close and Halt** (Chiudi e interrompi).

8. Se si intende completare i passaggi successivi in un secondo momento, assicurarsi di eliminare il cluster HDInsight creato in questo articolo. 

    [!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>Passaggio successivo 

In questo articolo si è appreso come creare un cluster HDInsight Spark ed eseguire una query Spark SQL di base. Passare all'articolo successivo per informazioni su come usare un cluster HDInsight Spark per eseguire query interattive su dati di esempio.

> [!div class="nextstepaction"]
>[Eseguire query interattive in un cluster di Azure HDInsight Spark](hdinsight-apache-spark-load-data-run-query.md)




