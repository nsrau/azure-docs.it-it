---
title: Creare cluster HDInsight con Archivio Data Lake di Azure tramite il portale | Microsoft Docs
description: Utilizzare il portale di Azure per creare e usare cluster HDInsight con Archivio Data Lake di Azure
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
ms.date: 11/18/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 3a9a4df2d260e8eebd8621b22efdb2927e9f5ecf
ms.openlocfilehash: a4fb47f9f517d66cf0ff9fde039d7bfd8edc29eb


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-portal"></a>Creare un cluster HDInsight con Archivio Data Lake tramite il portale di Azure
> [!div class="op_single_selector"]
> * [Uso del portale](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Tramite PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Utilizzo di Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Informazioni su come usare il portale di Azure per creare un cluster HDInsight con accesso ad Azure Data Lake Store. Per i tipi di cluster supportati, Data Lake Store deve essere usato come risorsa di archiviazione predefinita o come account di archiviazione aggiuntivo. Quando Data Lake Store viene usato come risorsa di archiviazione aggiuntiva, l'account di archiviazione predefinito per i cluster saranno i BLOB del servizio di archiviazione di Azure (WASB) e i file correlati ai cluster (ad esempio log e così via) vengono scritti nella risorsa di archiviazione predefinita, mentre i dati da elaborare possono essere archiviati in un account di Data Lake Store. L'uso di Archivio Data Lake come account di archiviazione aggiuntivo non ha impatto sulle prestazioni o sulla possibilità di leggere/scrivere nella risorsa di archiviazione dal cluster.

Alcune considerazioni importanti:

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione predefinita è disponibile per HDInsight versione 3.5.

* L'opzione per creare cluster HDInsight con accesso a Data Lake Store come risorsa di archiviazione aggiuntiva è disponibile per HDInsight versioni 3.2, 3.4 e 3.5.

* Per i cluster HBase (Windows e Linux), Data Lake Store **non è supportato** come opzione di archiviazione, per la risorsa di archiviazione predefinita o aggiuntiva.

* Per i cluster Storm (Windows e Linux), Data Lake Store può essere usato per scrivere dati da una topologia Storm. È anche possibile usare Archivio Data Lake per archiviare dati di riferimento che possono essere letti da una topologia Storm. Per altre informazioni, vedere [Usare Archivio Data Lake in una topologia Storm](#use-data-lake-store-in-a-storm-topology).


## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Account di Archivio Data Lake di Azure**. Seguire le istruzioni fornite in [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md).

* **Entità servizio di Azure Active Directory**. Questa esercitazione fornisce tutte le istruzioni utili su come creare un'entità servizio in Azure AD. Tuttavia, è necessario essere un amministratore di Azure AD per creare un'entità servizio. Se si è un amministratore di Azure AD, è possibile ignorare questo prerequisito e procedere con l'esercitazione.

    **Se non si è un amministratore di Azure AD**, non sarà possibile eseguire i passaggi necessari per creare un'entità servizio. In tal caso, l'amministratore di Azure AD deve creare un'entità servizio prima di creare un cluster HDInsight con l'archivio Data Lake Store. Inoltre, l'entità servizio deve essere creata usando un certificato, come descritto in [Creare un'entità servizio con certificato](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="do-you-learn-faster-with-videos"></a>Apprendimento rapido con i video
Guardare i video seguenti per informazioni su come effettuare il provisioning di cluster HDInsight con accesso ad Archivio Data Lake.

* [Creare un cluster HDInsight con accesso ad Archivio Data Lake](https://mix.office.com/watch/l93xri2yhtp2)
* Dopo aver configurato il cluster, [accedere ai dati in Archivio Data Lake usando gli script Hive e Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)

## <a name="create-an-hdinsight-cluster-with-access-to-azure-data-lake-store"></a>Creare un cluster Azure HDInsight con accesso ad Archivio Azure Data Lake.
In questa sezione si creerà un cluster HDInsight Hadoop che usa Archivio Data Lake come risorsa di archiviazione aggiuntiva. In questa versione, Archivio Data Lake può essere usato solo come risorsa di archiviazione aggiuntiva per i cluster Hadoop. L'archivio predefinito continuerà a essere WASB (BLOB di Archiviazione di Azure). Si procederà quindi prima di tutto alla creazione dell'account di archiviazione e dei contenitori di archiviazione richiesti per il cluster.

1. Accedere al nuovo [portale di Azure](https://portal.azure.com).

2. Seguire i passaggi descritti in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-provision-clusters.md) per avviare il provisioning di un cluster HDInsight.

3. Nel pannello **Origine dati** specificare se si desidera impostare Data Lake Store o Archiviazione di Azure (WASB) come risorsa di archiviazione predefinita. Se si desidera usare Azure Data Lake Store, andare al passaggio successivo. 

    Se si vuole usare i BLOB di archiviazione di Azure come risorsa di archiviazione predefinita, fare clic su **Archiviazione di Azure** per **Primary Storage Type** (Tipo di archiviazione primaria). Specificare i dettagli per l'account di archiviazione e il contenitore di archiviazione, impostare la **Località** su **Stati Uniti orientali 2** e fare clic su **Identità AAD del cluster**.
    
    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Add service principal to HDInsight cluster")


4. Se si vuole usare Azure Data Lake Store come risorsa di archiviazione predefinita, fare clic su **Data Lake Store** per **Primary Storage Type** (Tipo di archiviazione primaria). Selezionare un account di Data Lake Store già esistente, indicare un percorso in cui archiviare i file specifici del cluster (vedere nota sotto), specificare **Posizione** come **Stati Uniti orientali 2**, quindi fare clic su **Identità AAD del cluster**. È possibile usare questa opzione solo con i cluster HDInsight 3.5. Questa opzione non è disponibile per il tipo di cluster HBase all'interno dei cluster HDInsight 3.5.

    Nella schermata riportata di seguito, il percorso della cartella radice è /clusters/myhdiadlcluster, dove **myhdiadlcluster** rappresenta il nome del cluster da creare. In questo caso, assicurarsi che la cartella **/cluster** esista già nell'account di Data Lake Store. La cartella **myhdiadlcluster** verrà creata durante la creazione del cluster. Analogamente, se il percorso radice è stato impostato su /hdinsight/clusters/data/myhdiadlcluter, è necessario assicurarsi che il percorso **/hdinsight/cluster/data/** esista già nell'account di Data Lake Store.
        
    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Add service principal to HDInsight cluster")

5. Nel pannello **Identità AAD cluster** è possibile scegliere di selezionare un'entità servizio esistente o crearne una nuova. Se si vuole usare un'entità servizio esistente, andare al passaggio successivo.

    Se si vuole creare una nuova entità servizio, nel pannello **Identità AAD del cluster** fare clic su **Crea nuovo**, poi su **Entità servizio** e specificare i valori per creare una nuova entità servizio nel pannello **Creare un'entità servizio**. Nell'ambito questa operazione vengono creati anche un certificato e un'applicazione Azure Active Directory. Fare clic su **Create**.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Add service principal to HDInsight cluster")

    È anche possibile fare clic su **Scarica certificato** per scaricare il certificato associato all'entità servizio creata. Questa operazione è utile se si vuole usare la stessa entità servizio in futuro durante la creazione di altri cluster HDInsight. Fare clic su **Seleziona**.

6. Se si vuole usare un'entità servizio esistente, nel pannello **Identità AAD del cluster** fare clic su **Usa esistente**, su **Entità servizio** e cercare un'entità servizio esistente all'interno del pannello **Selezione entità servizio**. Fare clic sul nome di un'entità servizio e quindi fare clic su **Seleziona**.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Add service principal to HDInsight cluster")

    Nel pannello **Identità AAD cluster** caricare il certificato con estensione pfx associato all'entità servizio selezionata e quindi specificare la password del certificato.

6. Nel pannello **Identità AAD del cluster** fare clic su **Gestisci l'accesso ad Archivio Azure Data Lake**. Nel riquadro successivo l'opzione **Selezionare le autorizzazioni file** è già selezionata per impostazione predefinita ed elenca tutti gli account di Data Lake Store inclusi nella sottoscrizione. Selezionare l'account di Data Lake Store che si vuole associare al cluster per elencare i file e le cartelle in tale account. Dopodiché, è possibile assegnare le autorizzazioni a livello di file o cartella. Se si vuole associare le autorizzazioni al livello radice dell'account, selezionare la casella di controllo accanto al nome dell'account.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Add service principal to HDInsight cluster")

    > [!NOTE]
    > Se si usa l'account di Data Lake Store come risorsa di archiviazione predefinita per un cluter, **è necessario** assegnare le autorizzazioni all'entità servizio a livello radice dell'account di Data Lake Store.

7. Se si vuole assegnare autorizzazioni per i file o le cartelle all'interno di un account, selezionare l'account di Data Lake Store per visualizzare i file e le cartelle nel riquadro successivo. Selezionare file e cartelle, quindi scegliere le relative autorizzazioni (LETTURA/SCRITTURA/ESECUZIONE) da assegnare ad essi, specificare se queste autorizzazioni devono essere applicate in modo ricorsivo anche agli elementi figlio e fare clic su **Seleziona**.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Add service principal to HDInsight cluster")

8. Nella schermata successiva, fare clic su **Esegui** per assegnare le autorizzazioni per l'entità di servizio di Azure Active Directory sull'account, sul file e/o sulla cartella selezionati.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-2.png "Add service principal to HDInsight cluster")

9. Dopo aver assegnato l'autorizzazione, fare clic su **Fine** in tutti i pannelli finché non si torna al pannello **Identità AAD del cluster**.

4. Fare clic su **Seleziona** in **Identità AAD del cluster** e continuare con la creazione del cluster come descritto in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

10. Al termine del provisioning del cluster, è possibile verificare che l'entità servizio sia effettivamente associata al cluster HDInsight. A tale scopo, nel pannello del cluster fare clic su **Identità AAD del cluster** per visualizzare l'entità servizio associata.

    ![Aggiungere entità servizio a cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Add service principal to HDInsight cluster")

## <a name="show-me-some-examples"></a>Di seguito sono riportati alcuni esempi

Dopo aver eseguito il provisioning del cluster con Data Lake Store come risorsa di archiviazione, ecco alcuni esempi che spiegano come usare il cluster HDInsight per analizzare i dati archiviati in Data Lake Store.

### <a name="run-a-hive-query-against-data-stored-in-data-lake-store"></a>Eseguire una query Hive sui dati archiviati in Data Lake Store

Per eseguire una query Hive, è possibile usare l'interfaccia delle visualizzazioni Hive disponibile dal portale di Ambari. Per istruzioni su come usare le visualizzazioni Hive di Ambari, vedere [Usare la visualizzazione Hive con Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md). Ci sono un paio di cose da modificare quando si usano i dati in Data Lake Store.

* Se ci si attiene all'esempio del cluster creato in precedenza, il percorso ai dati sarà `adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file`. Una query Hive per creare una tabella dai dati di esempio archiviati nell'account di Data Lake Store avrà un aspetto simile al seguente:

        CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Nella query precedente:

* `adl://hdiadlstorage.azuredatalakestore.net` è la radice dell'account di Data Lake Store.
* `/clusters/myhdiadlcluster` è la radice dei dati del cluster specificati durante la creazione del cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` è il percorso del file di esempio usato nella query


### <a name="use-data-lake-store-with-spark-cluster"></a>Usare Archivio Data Lake con un cluster Spark
È possibile usare un cluster Spark per eseguire i processi Spark sui dati archiviati in Data Lake Store. Per istruzioni al riguardo, vedere [Usare il cluster Spark di HDInsight per analizzare i dati in Data Lake Store](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Usare Archivio Data Lake in una topologia Storm
È possibile usare Archivio Data Lake per scrivere dati da una topologia Storm. Per istruzioni su come ottenere questo scenario, vedere [Usare Azure Data Lake Store con Apache Storm in HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="see-also"></a>Vedere anche
* [PowerShell: Creare un cluster HDInsight per usare Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx



<!--HONumber=Nov16_HO4-->


