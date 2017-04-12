---
title: Usare il portale di Azure per creare cluster HDInsight con Data Lake Store | Microsoft Docs
description: Usare il portale di Azure per creare e usare cluster HDInsight con Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 73ee330c276263a21931a7b9a16cc33f86c58a26
ms.openlocfilehash: 2aa5542dc095beaf951bad2b0361a3acc4468f2d
ms.lasthandoff: 04/05/2017


---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Creare cluster HDInsight con Data Lake Store tramite il portale di Azure
> [!div class="op_single_selector"]
> * [Usare il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usare PowerShell (per l'archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usare PowerShell (per l'archiviazione aggiuntiva)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usare Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Questo articolo descrive come usare il portale di Azure per creare cluster HDInsight con accesso ad Azure Data Lake Store. Per i tipi di cluster supportati, è possibile usare Data Lake Store come risorsa di archiviazione predefinita o come account di archiviazione aggiuntivo.

Quando si usa Data Lake Store come risorsa di archiviazione aggiuntiva, l'account di archiviazione predefinito per i cluster resta archiviazione BLOB di Azure, mentre i file correlati ai cluster, ad esempio i log, continuano a essere scritti nella risorsa di archiviazione predefinita. Tuttavia, i dati da elaborare possono essere archiviati in un account Data Lake Store. L'uso di Data Lake Store come account di archiviazione aggiuntivo non ha impatto sulle prestazioni o sulla possibilità di leggere o scrivere nella risorsa di archiviazione dal cluster.

Di seguito sono riportate alcune considerazioni importanti per l'uso di HDInsight con Data Lake Store:

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione predefinita è disponibile per HDInsight versione 3.5.

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione predefinita *non è disponibile* per i cluster HDInsight Premium.

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione aggiuntiva è disponibile per HDInsight versioni 3.2, 3.4 e 3.5.

* Per i cluster HBase (Windows e Linux), Data Lake Store *non è supportato* come opzione di archiviazione, sia predefinita che aggiuntiva.

* Per i cluster Storm (Windows e Linux), è possibile usare Data Lake Store per scrivere dati da una topologia Storm. È anche possibile usare Data Lake Store per archiviare dati di riferimento che possono essere letti da una topologia Storm. Per altre informazioni, vedere [Usare Data Lake Store in una topologia Storm](#use-data-lake-store-in-a-storm-topology).


## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare l'esercitazione, verificare di aver soddisfatto i requisiti seguenti:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Un account Azure Data Lake Store**. Seguire le istruzioni fornite in [Introduzione all'uso di Azure Data Lake Store tramite il portale di Azure](data-lake-store-get-started-portal.md).

* **Un'entità servizio di Azure Active Directory**. Questa esercitazione fornisce tutte le istruzioni utili su come creare un'entità servizio in Azure Active Directory (Azure AD). Tuttavia, per creare un'entità servizio è necessario essere un amministratore di Azure AD. Se si è un amministratore, è possibile ignorare questo prerequisito e procedere con l'esercitazione.

    >[!NOTE]
    >È possibile creare un'entità servizio solo se si è un amministratore di Azure AD. Prima di poter creare un cluster HDInsight con Data Lake Store, un amministratore di Azure AD deve creare un'entità servizio. Inoltre, l'entità servizio deve essere creata usando un certificato, come descritto in [Creare un'entità servizio con certificato](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-certificate-from-certificate-authority).
    >

## <a name="create-an-hdinsight-cluster-with-access-to-a-data-lake-store"></a>Creare un cluster HDInsight con accesso a Data Lake Store
In questa sezione si creerà un cluster HDInsight Hadoop che usa Data Lake Store come risorsa di archiviazione predefinita o aggiuntiva.

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Creare un cluster con Data Lake Store come risorsa di archiviazione predefinita

>[!NOTE]
>È possibile usare questa opzione solo con i cluster HDInsight 3.5 (edizione Standard). Questa opzione non è disponibile per il tipo di cluster HBase all'interno dei cluster HDInsight 3.5.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per avviare il provisioning di un cluster HDInsight, seguire le istruzioni contenute in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

3. Nel pannello **Archiviazione**, in **Tipo di archiviazione primario** fare clic su **Data Lake Store**.

4. Selezionare un account Data Lake Store esistente e immettere un percorso della cartella radice in cui archiviare i file specifici del cluster.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Aggiungere l'entità servizio al cluster HDInsight")


    Nella schermata precedente il percorso della cartella radice è /clusters/myhdiadlcluster, dove *myhdiadlcluster* è il nome del cluster da creare. In questo caso, assicurarsi che la cartella */cluster* esista già nell'account Data Lake Store. La cartella *myhdiadlcluster* verrà creata durante la creazione del cluster. Analogamente, se il percorso radice è stato impostato su */hdinsight/clusters/data/myhdiadlcluter*, assicurarsi che il percorso */hdinsight/cluster/data/* esista già nell'account Data Lake Store.

5. Fare clic su **Accesso a Data Lake Store** per configurare l'accesso tra l'account Data Lake Store e i cluster HDInsight. Per istruzioni, vedere [Configurare l'accesso tra i cluster HDInsight e Data Lake Store](#configure-access-between-hdinsight-cluster-and-data-lake-store).


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>Creare un cluster con Data Lake Store come risorsa di archiviazione aggiuntiva

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Per avviare il provisioning di un cluster HDInsight, seguire le istruzioni contenute in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

3. Nel pannello **Archiviazione**, in **Tipo di archiviazione primario** selezionare **Archiviazione di Azure**.

4. In **Metodo di selezione**, effettuare una delle operazioni seguenti:

    * Per specificare un account di archiviazione che fa parte della sottoscrizione di Azure, selezionare **Sottoscrizioni personali**, quindi selezionare l'account di archiviazione.

    * Per specificare un account di archiviazione esterno alla sottoscrizione di Azure, selezionare **Chiave di accesso**, quindi immettere le informazioni per l'account di archiviazione esterno.

5. In **Contenitore predefinito** mantenere il nome del contenitore predefinito suggerito dal portale oppure specificarne uno personale.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Aggiungere l'entità servizio al cluster HDInsight")

6. Quando si usa l'archiviazione BLOB come risorsa di archiviazione predefinita, è comunque possibile usare Data Lake Store come risorsa di archiviazione aggiuntiva per il cluster. Per farlo, fare clic su **Accesso a Data Lake Store** per configurare l'accesso tra l'account Data Lake Store e i cluster HDInsight. Per istruzioni, vedere [Configurare l'accesso tra i cluster HDInsight e Data Lake Store](#configure-access-between-hdinsight-cluster-and-data-lake-store).

## <a name="configure-access-between-hdinsight-cluster-and-data-lake-store"></a>Configurare l'accesso tra i cluster HDInsight e Data Lake Store

In questa sezione è possibile configurare l'accesso tra i cluster HDInsight e Data Lake Store usando un'entità servizio Azure Active Directory

1. Nel pannello **Accesso a Data Lake Store**, specificare se si desidera usare una nuova entità servizio o un'entità servizio esistente. In questo passaggio viene creata una nuova entità servizio. Per usare un'entità servizio esistente, andare al passaggio successivo.

    a. Nel pannello **Accesso a Data Lake Store** fare clic su **Crea nuovo** e quindi su **Entità servizio**.

    b. Nel pannello **Crea un'entità servizio** immettere i valori richiesti.  

    c. Fare clic su **Crea**. Verranno creati automaticamente un certificato e un'applicazione Azure AD.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Aggiungere l'entità servizio al cluster HDInsight")

    È anche possibile fare clic su **Scarica certificato** per scaricare il certificato associato all'entità servizio creata. Il download del certificato è un'operazione utile se in futuro si vorrà usare la stessa entità servizio per creare altri cluster HDInsight. Fare clic su **Seleziona**.

2. Per usare un'entità servizio esistente, seguire i passaggi riportati di seguito.

    a. Nel pannello **Accesso a Data Lake Store** fare clic su **Usa esistente** e quindi su **Entità servizio**.

    b. Nel pannello **Seleziona un'entità servizio** cercare un'entità servizio esistente. Fare clic sull'entità servizio che si vuole usare e quindi su **Seleziona**.

    c. Nel pannello **Accesso a Data Lake Store** caricare il certificato (file PFX) associato all'entità servizio selezionata e quindi immettere la password del certificato.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Aggiungere l'entità servizio al cluster HDInsight")

7. Nel pannello **Accesso a Data Lake Store** fare clic su **Accesso**. Per impostazione predefinita, verrà visualizzato il pannello per la** **selezione delle autorizzazioni per i file. Questo pannello elenca tutti gli account Data Lake Store nella sottoscrizione.

8. Selezionare l'account Data Lake Store che si vuole associare al cluster.

    **Se si usa Data Lake Store come risorsa di archiviazione predefinita**, è necessario assegnare autorizzazioni a due livelli.

    a. In primo luogo, è necessario assegnare autorizzazioni al livello radice dell'account Data Lake Store. A tale scopo, passare il mouse (non fare clic) sul nome dell'account Data Lake Store per rendere visibile la casella di controllo. Selezionare la casella di controllo.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Aggiungere l'entità servizio al cluster HDInsight")

    b. Quindi, è consigliabile assegnare l'autorizzazione alla radice di archiviazione del cluster HDInsight. In base alla schermata precedente, la radice di archiviazione del cluster è la cartella **/cluster** specificata durante la selezione di Data Lake Store come risorsa di archiviazione predefinita.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.add.png "Aggiungere l'entità servizio al cluster HDInsight")

    **Se si usa Data Lake Store come risorsa di archiviazione aggiuntiva**, è necessario assegnare autorizzazioni solo alla cartella a cui si desidera accedere dal cluster HDInsight. Ad esempio, nella schermata seguente, garantire l'accesso solo alla cartella **hdiaddonstorage** in un account Data Lake Store.

    ![Assegnare le autorizzazioni dell'entità servizio al cluster HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Assegnare le autorizzazioni dell'entità servizio al cluster HDInsight")

9. Per tutti gli account e i percorsi selezionati, selezionare le autorizzazioni (lettura, scrittura o esecuzione) e specificare se queste autorizzazioni devono essere applicate in modo ricorsivo anche agli elementi figlio e fare clic su **Seleziona**.

11. Nella finestra **Assegna autorizzazioni selezionate** fare clic su **Esegui** per assegnare le autorizzazioni per l'entità servizio di Azure Active Directory all'account, alle cartelle o ai file selezionati.

    ![Assegnare le autorizzazioni dell'entità servizio al cluster HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Assegnare le autorizzazioni dell'entità servizio al cluster HDInsight")

12. Dopo aver assegnato le autorizzazioni, fare clic su **Fine** in tutti i pannelli per tornare al pannello **Accesso a Data Lake Store**.

13. In **Accesso a Data Lake Store** fare clic su **Seleziona** e continuare con la creazione del cluster come descritto in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="verify-cluster-set-up"></a>Verificare la configurazione del cluster

Al termine della configurazione del cluster, nel pannello del cluster verificare i risultati eseguendo una o entrambe le operazioni seguenti:

* Per verificare che la risorsa di archiviazione associata per il cluster sia l'account Data Lake Store specificato, fare clic su **Account di archiviazione** nel riquadro sinistro.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Aggiungere l'entità servizio al cluster HDInsight")

* Per verificare che l'entità servizio sia correttamente associata al cluster HDInsight, fare clic su **Accesso a Data Lake Store** nel riquadro sinistro.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Aggiungere l'entità servizio al cluster HDInsight")


## <a name="examples"></a>esempi

Dopo aver configurato il cluster con Data Lake Store come risorsa di archiviazione, fare riferimento a questi esempi su come usare il cluster HDInsight per analizzare i dati archiviati in Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Eseguire una query Hive sui dati archiviati in Data Lake Store (come risorsa di archiviazione primaria)

Per eseguire una query Hive, usare l'interfaccia delle visualizzazioni Hive disponibile nel portale di Ambari. Per istruzioni su come usare le visualizzazioni Hive di Ambari, vedere [Usare la visualizzazione Hive con Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md).

Quando si usano dati in Data Lake Store, è necessario modificare alcune stringhe.

Ad esempio, se si usa il cluster creato con Data Lake Store come risorsa di archiviazione primaria, il percorso dei dati è: *adl://<nome_account_data_lake_store>/azuredatalakestore.net/path/to/file*. Una query Hive per creare una tabella dai dati di esempio archiviati nell'account Data Lake Store avrà un aspetto simile al seguente:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descrizioni:
* `adl://hdiadlstorage.azuredatalakestore.net/` è la radice dell'account di Data Lake Store.
* `/clusters/myhdiadlcluster` è la radice dei dati del cluster specificata durante la creazione del cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` è il percorso del file di esempio usato nella query.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Eseguire una query Hive sui dati archiviati in Data Lake Store (come risorsa di archiviazione aggiuntiva)

Se il cluster creato usa l'archiviazione BLOB come risorsa di archiviazione predefinita, i dati di esempio non si troveranno nell'account Azure Data Lake Store usato come risorsa di archiviazione aggiuntiva. In questi casi, trasferire innanzitutto i dati dall'archiviazione BLOB a Data Lake Store e quindi eseguire le query come mostrato nell'esempio precedente.

Per informazioni su come copiare dati dall'archiviazione BLOB a Data Lake Store, vedere gli articoli seguenti:

* [Usare Distcp per copiare dati tra i BLOB di archiviazione di Azure e Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Usare lo strumento AdlCopy per copiare i dati da BLOB di Archiviazione di Azure a Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Usare Data Lake Store con un cluster Spark
È possibile usare un cluster Spark per eseguire processi Spark sui dati archiviati in Data Lake Store. Per altre informazioni, vedere [Usare il cluster HDInsight Spark per analizzare i dati in Data Lake Store](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Usare Data Lake Store in una topologia Storm
È possibile usare Data Lake Store per scrivere dati da una topologia Storm. Per istruzioni su come ottenere questo scenario, vedere [Usare Azure Data Lake Store con Apache Storm in HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="see-also"></a>Vedere anche
* [PowerShell: Creare un cluster HDInsight per usare Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx

