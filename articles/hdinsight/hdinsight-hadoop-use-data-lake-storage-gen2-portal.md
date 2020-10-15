---
title: Creare Azure HDInsight-Azure Data Lake Storage Gen2-Portal
description: Informazioni su come usare Azure Data Lake Storage Gen2 con i cluster HDInsight di Azure tramite il portale.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: 104424c1e3bd1df69106db7da45b744755b51e82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858793"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Creare un cluster con Data Lake Storage Gen2 usando il portale di Azure

Il portale di Azure è uno strumento di gestione basato sul Web per servizi e risorse ospitati nel cloud di Microsoft Azure. In questo articolo viene illustrato come creare cluster Azure HDInsight basati su Linux tramite il portale. Ulteriori dettagli sono disponibili nella pagina relativa alla [creazione di cluster HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per creare un cluster HDInsight che USA Data Lake Storage Gen2 per l'archiviazione, seguire questa procedura per configurare un account di archiviazione con uno spazio dei nomi gerarchico.

## <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'identità gestita assegnata dall'utente, se non è già disponibile.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. In alto a sinistra fare clic su **Crea una risorsa**.
1. Nella casella di ricerca digitare **User assegnata** e fare clic su **identità gestita assegnata dall'utente**.
1. Fare clic su **Crea**.
1. Immettere un nome per l'identità gestita, selezionare la sottoscrizione, il gruppo di risorse e la località corretti.
1. Fare clic su **Crea**.

Per altre informazioni sul funzionamento delle identità gestite in Azure HDInsight, vedere [identità gestite in Azure HDInsight](hdinsight-managed-identities.md).

![Creare un'identità gestita assegnata dall'utente](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Creare un account di archiviazione da usare con Data Lake Storage Gen2

Creare un account di archiviazione da usare con Azure Data Lake Storage Gen2.

1. Accedere al [portale di Azure](https://portal.azure.com).
1. In alto a sinistra fare clic su **Crea una risorsa**.
1. Nella casella di ricerca digitare **storage** e fare clic su **account di archiviazione**.
1. Fare clic su **Crea**.
1. Nella schermata **Crea account di archiviazione** :
    1. Selezionare la sottoscrizione e il gruppo di risorse corretti.
    1. Immettere un nome per l'account di archiviazione con Data Lake Storage Gen2.
    1. Fare clic sulla scheda **Avanzate** .
    1. Fare clic su **abilitato** accanto a **spazio dei nomi gerarchico** in **Data Lake storage Gen2**.
    1. Fare clic su **Rivedi e crea**.
    1. Fare clic su **Crea**

Per altre informazioni sulle altre opzioni durante la creazione dell'account di archiviazione, vedere [Guida introduttiva: creare un account di archiviazione per Azure Data Lake storage Gen2](../storage/blobs/create-data-lake-storage-account.md).

![Screenshot che mostra la creazione dell'account di archiviazione nel portale di Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Configurare le autorizzazioni per l'identità gestita nel Data Lake Storage Gen2

Assegnare l'identità gestita al ruolo di **proprietario dei dati del BLOB di archiviazione** nell'account di archiviazione.

1. Nel [portale di Azure](https://portal.azure.com) passare all'account di archiviazione.
1. Selezionare l'account di archiviazione e quindi selezionare **controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per l'account. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.

    ![Screenshot che mostra le impostazioni di controllo di accesso per l'archiviazione](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Selezionare il pulsante **+ Aggiungi assegnazione ruolo** per aggiungere un nuovo ruolo.
1. Nella finestra **Aggiungi assegnazione ruolo** selezionare il ruolo **proprietario dati BLOB di archiviazione** . Selezionare quindi la sottoscrizione a cui sono associati l'identità gestita e l'account di archiviazione. Individuare l'identità gestita assegnata dall'utente creata in precedenza. Infine, selezionare l'identità gestita, che verrà elencata in **membri selezionati**.

    ![Screenshot che illustra come assegnare un ruolo di Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Selezionare **Salva**. L'identità assegnata dall'utente selezionata è ora elencata sotto il ruolo selezionato.
1. Al termine della configurazione iniziale, è possibile creare un cluster tramite il portale. Il cluster deve trovarsi nella stessa area di Azure dell'account di archiviazione. Nella scheda **archiviazione** del menu di creazione del cluster selezionare le opzioni seguenti:

    * Per **tipo di archiviazione primario**selezionare **Azure Data Lake storage Gen2**.
    * In **account di archiviazione primario**cercare e selezionare l'account di archiviazione appena creato con data Lake storage Gen2 archiviazione.

    * In **Identity (identità**) selezionare l'identità gestita assegnata dall'utente appena creata.

        ![Impostazioni di archiviazione per l'uso di Data Lake Storage Gen2 con Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Per aggiungere un account di archiviazione secondario con Data Lake Storage Gen2, a livello di account di archiviazione, è sufficiente assegnare l'identità gestita creata in precedenza alla nuova Data Lake Storage Gen2 che si desidera aggiungere. Si noti che l'aggiunta di un account di archiviazione secondario con Data Lake Storage Gen2 tramite il pannello "account di archiviazione aggiuntivi" in HDInsight non è supportata.
    > * È possibile abilitare RA-GRS o RA-ZRS nell'account di archiviazione BLOB di Azure usato da HDInsight. Tuttavia, la creazione di un cluster con l'endpoint secondario RA-GRS o RA-ZRS non è supportata.

## <a name="delete-the-cluster"></a>Eliminare il cluster

Vedere [eliminare un cluster HDInsight usando il browser, PowerShell o l'interfaccia della](./hdinsight-delete-cluster.md)riga di comando di Azure.

## <a name="troubleshoot"></a>Risolvere problemi

Se si verificano problemi di creazione dei cluster HDInsight, vedere i [requisiti dei controlli di accesso](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Passaggi successivi

È stato creato un cluster HDInsight. È ora possibile procedere e scoprire come utilizzare il cluster.

### <a name="apache-spark-clusters"></a>Cluster Apache Spark

* [Personalizzare i cluster HDInsight basati su Linux tramite azioni script](hdinsight-hadoop-customize-cluster-linux.md)
* [Creare un'applicazione autonoma con Scala](spark/apache-spark-create-standalone-application.md)
* [Eseguire processi in modalità remota in un cluster Apache Spark usando Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark con Business Intelligence: eseguire l'analisi interattiva dei dati con strumenti di Business Intelligence mediante Spark in HDInsight](spark/apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: utilizzare Spark in HDInsight per prevedere i risultati di un controllo alimentare](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Cluster Apache Hadoop

* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
* [Usare MapReduce con HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Cluster Apache HBase

* [Introduzione ad Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Sviluppare applicazioni Java per Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
