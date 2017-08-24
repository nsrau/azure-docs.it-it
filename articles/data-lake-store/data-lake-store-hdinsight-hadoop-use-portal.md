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
ms.date: 08/14/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: 9dd56efb89e07ea61ae431d1ea2accd721cd6502
ms.contentlocale: it-it
ms.lasthandoff: 08/15/2017

---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Creare cluster HDInsight con Data Lake Store tramite il portale di Azure
> [!div class="op_single_selector"]
> * [Usare il portale di Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usare PowerShell (per l'archiviazione predefinita)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usare PowerShell (per l'archiviazione aggiuntiva)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usare Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Informazioni su come usare il portale di Azure per creare un cluster HDInsight con un account Azure Data Lake Store come risorsa di archiviazione predefinita o risorsa di archiviazione aggiuntiva. Anche se la risorsa di archiviazione aggiuntiva è facoltativa per un cluster HDInsight, è consigliabile archiviare i dati aziendali negli account di archiviazione aggiuntivi.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare l'esercitazione, verificare di aver soddisfatto i requisiti seguenti:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account di Archivio Data Lake di Azure**. Seguire le istruzioni fornite in [Introduzione all'uso di Azure Data Lake Store tramite il portale di Azure](data-lake-store-get-started-portal.md). È anche necessario creare una cartella radice nell'account.  In questa esercitazione viene usata una cartella radice denominata __/clusters__.
* **Un'entità servizio di Azure Active Directory**. Questa esercitazione fornisce tutte le istruzioni utili su come creare un'entità servizio in Azure Active Directory (Azure AD). Tuttavia, per creare un'entità servizio è necessario essere un amministratore di Azure AD. Se si è un amministratore, è possibile ignorare questo prerequisito e procedere con l'esercitazione.

    >[!NOTE]
    >È possibile creare un'entità servizio solo se si è un amministratore di Azure AD. Prima di poter creare un cluster HDInsight con Data Lake Store, un amministratore di Azure AD deve creare un'entità servizio. Inoltre, l'entità servizio deve essere creata usando un certificato, come descritto in [Creare un'entità servizio con certificato](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Creazione di un cluster HDInsight

In questa sezione viene creato un cluster HDInsight con account Data Lake Store come risorsa di archiviazione predefinita o aggiuntiva. Questo articolo illustra solo la configurazione degli account Data Lake Store.  Per informazioni generali sulla creazione di cluster e le relative procedure, vedere [Creare cluster Hadoop basati su Linux in HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Creare un cluster con Data Lake Store come risorsa di archiviazione predefinita

**Per creare un cluster HDInsight con Data Lake Store come account di archiviazione predefinito**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Seguire [Creare i cluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) per informazioni generali sulla creazione di cluster HDInsight.
3. Nel pannello **Archiviazione**, in **Tipo di archiviazione primario** selezionare **Data Lake Store** e quindi immettere le informazioni seguenti:

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Aggiungere l'entità servizio al cluster HDInsight")

    - **Seleziona account Data Lake Store**: selezionare un account Data Lake Store esistente. È necessario un account Data Lake Store esistente.  Vedere [Prerequisiti](#prereuisites).
    - **Percorso radice**: immettere un percorso in cui archiviare i file specifici del cluster. Nello screenshot è __/clusters/myhdiadlcluster/__, in cui la cartella __/clusters__ deve esistere e il portale crea la cartella *myhdicluster*.  *myhdicluster* è il nome del cluster.
    - **Accesso a Data Lake Store**: configurare l'accesso tra l'account Data Lake Store e il cluster HDInsight. Per istruzioni, vedere [Configurare l'accesso a Data Lake Store](#configure-data-lake-store-access).
    - **Account archiviazione aggiuntivi**: aggiungere account di archiviazione di Azure come account di archiviazione aggiuntivi per il cluster. Per aggiungere altre istanze di Data Lake Store, assegnare al cluster le autorizzazioni per i dati in più account Data Lake Store durante la configurazione di un account Data Lake Store come tipo di archiviazione primario. Vedere [Configurare l'accesso a Data Lake Store](#configure-data-lake-store-access).

4. In **Accesso a Data Lake Store** fare clic su **Seleziona** e continuare con la creazione del cluster come descritto in [Creare cluster Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>Creare un cluster con Data Lake Store come risorsa di archiviazione aggiuntiva

Le istruzioni seguenti illustrano come creare un cluster HDInsight con un account di archiviazione di Azure come risorsa di archiviazione predefinita e un account Data Lake Store come risorsa di archiviazione aggiuntiva.
**Per creare un cluster HDInsight con Data Lake Store come account di archiviazione predefinito**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Seguire [Creare i cluster](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) per informazioni generali sulla creazione di cluster HDInsight.
3. Nel pannello **Archiviazione**, in **Tipo di archiviazione primario** selezionare **Archiviazione di Azure** e quindi immettere le informazioni seguenti:

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Aggiungere l'entità servizio al cluster HDInsight")

    - **Metodo di selezione**: usare una delle opzioni seguenti:

        * Per specificare un account di archiviazione che fa parte della sottoscrizione di Azure, selezionare **Sottoscrizioni personali**, quindi selezionare l'account di archiviazione.
        * Per specificare un account di archiviazione esterno alla sottoscrizione di Azure, selezionare **Chiave di accesso**, quindi immettere le informazioni per l'account di archiviazione esterno.

    - **Contenitore predefinito**: usare il valore predefinito o specificare un altro nome.

    - Account archiviazione aggiuntivi: aggiungere altri account di archiviazione di Azure come risorsa di archiviazione aggiuntiva.
    - Accesso a Data Lake Store: configurare l'accesso tra l'account Data Lake Store e il cluster HDInsight. Per istruzioni, vedere [Configurare l'accesso a Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Configurare l'accesso a Data Lake Store 

In questa sezione è possibile configurare l'accesso a Data Lake Store dai cluster HDInsight usando un'entità servizio di Azure Active Directory. 

### <a name="specify-a-service-principal"></a>Specificare un'entità servizio

Dal portale di Azure è possibile usare un'entità servizio esistente o crearne una nuova.

**Per creare un'entità servizio dal portale di Azure**

1. Fare clic su **Accesso a Data Lake Store** nel pannello Archiviazione.
2. Nel pannello **Accesso a Data Lake Store** fare clic su **Crea nuovo**.
3. Fare clic su **Entità servizio** e quindi seguire le istruzioni per creare un'entità servizio.
4. Scaricare il certificato se si decide di usarlo ancora in futuro. Il download del certificato è un'operazione utile se in futuro si vorrà usare la stessa entità servizio per creare altri cluster HDInsight.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Aggiungere l'entità servizio al cluster HDInsight")

4. Fare clic su **Accesso** per configurare l'accesso alla cartella.  Vedere [Configurare le autorizzazioni file](#configure-file-permissions).


**Per usare un'entità servizio esistente dal portale di Azure**

1. Fare clic su **Accesso a Data Lake Store**.
1. Nel pannello **Accesso a Data Lake Store** fare clic su **Usa esistente**.
2. Fare clic su **Entità servizio** e quindi selezionare un'entità servizio. 
3. Caricare il certificato (file PFX) associato all'entità servizio selezionata e quindi immettere la password del certificato.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Aggiungere l'entità servizio al cluster HDInsight")

4. Fare clic su **Accesso** per configurare l'accesso alla cartella.  Vedere [Configurare le autorizzazioni file](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Configurare le autorizzazioni file

Le configurazioni sono diverse a seconda che l'account venga usato come risorsa di archiviazione predefinita o come account di archiviazione aggiuntivo:

- Uso come risorsa di archiviazione predefinita

    - Autorizzazione a livello di radice dell'account Data Lake Store.
    - Autorizzazione a livello di radice dell'archiviazione cluster HDInsight. Ad esempio, la cartella __/clusters__ usata prima nell'esercitazione.
- Uso come risorsa di archiviazione aggiuntiva

    - Autorizzazione a livello delle cartelle in cui è necessario l'accesso ai file.

**Per assegnare l'autorizzazione a livello di radice dell'account Data Lake Store**

1. Nel pannello **Accesso a Data Lake Store** fare clic su **Accesso**. Viene aperto il pannello per la **Selezionare le autorizzazioni file**. Questo pannello elenca tutti gli account Data Lake Store nella sottoscrizione.
2. Passare il mouse (non fare clic) sul nome dell'account Data Lake Store per rendere visibile la casella di controllo e selezionarla.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Aggiungere l'entità servizio al cluster HDInsight")

  Per impostazione predefinita, __LETTURA__, __SCRITTURA__ ed __ESECUZIONE__ sono selezionati.

3. Fare clic su **Seleziona** nella parte inferiore della pagina.
4. Fare clic su **Esegui** per assegnare l'autorizzazione.
5. Fare clic su **Done**.

**Per assegnare l'autorizzazione a livello di radice del cluster HDInsight**

1. Nel pannello **Accesso a Data Lake Store** fare clic su **Accesso**. Viene aperto il pannello per la **Selezionare le autorizzazioni file**. Questo pannello elenca tutti gli account Data Lake Store nella sottoscrizione.
1. Nel pannello **Selezionare le autorizzazioni file** fare clic sul nome Data Lake Store per visualizzarne il contenuto.
2. Selezionare la radice di archiviazione cluster HDInsight selezionando la casella di controllo a sinistra della cartella. In base allo screenshot precedente, la radice di archiviazione del cluster è la cartella __/clusters__ specificata durante la selezione di Data Lake Store come risorsa di archiviazione predefinita.
3. Impostare le autorizzazioni per la cartella.  Per impostazione predefinita, sono selezionate lettura, scrittura ed esecuzione.
4. Fare clic su **Seleziona** nella parte inferiore della pagina.
5. Fare clic su **Run**.
6. Fare clic su **Done**.

Se si usa Data Lake Store come risorsa di archiviazione aggiuntiva, è necessario assegnare autorizzazioni solo per le cartella a cui si vuole accedere dal cluster HDInsight. Ad esempio, nella schermata seguente, garantire l'accesso solo alla cartella **hdiaddonstorage** in un account Data Lake Store.

![Assegnare le autorizzazioni dell'entità servizio al cluster HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Assegnare le autorizzazioni dell'entità servizio al cluster HDInsight")


## <a name="verify-cluster-set-up"></a>Verificare la configurazione del cluster

Al termine della configurazione del cluster, nel pannello del cluster verificare i risultati eseguendo uno o entrambi i passaggi seguenti:

* Per verificare che la risorsa di archiviazione associata per il cluster sia l'account Data Lake Store specificato, fare clic su **Account di archiviazione** nel riquadro sinistro.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Aggiungere l'entità servizio al cluster HDInsight")

* Per verificare che l'entità servizio sia correttamente associata al cluster HDInsight, fare clic su **Accesso a Data Lake Store** nel riquadro sinistro.

    ![Aggiungere l'entità servizio al cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Aggiungere l'entità servizio al cluster HDInsight")


## <a name="examples"></a>esempi

Dopo aver configurato il cluster con Data Lake Store come risorsa di archiviazione, fare riferimento a questi esempi su come usare il cluster HDInsight per analizzare i dati archiviati in Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Eseguire una query Hive sui dati archiviati in Data Lake Store (come risorsa di archiviazione primaria)

Per eseguire una query Hive, usare l'interfaccia delle visualizzazioni Hive disponibile nel portale di Ambari. Per istruzioni su come usare le visualizzazioni Hive di Ambari, vedere [Usare la visualizzazione Hive con Hadoop in HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md).

Quando si usano dati in Data Lake Store, è necessario modificare alcune stringhe.

Ad esempio, se si usa il cluster creato con Data Lake Store come risorsa di archiviazione primaria, il percorso dei dati è: *adl://<nome_account_data_lake_store>/azuredatalakestore.net/path/to/file*. Una query Hive per creare una tabella dai dati di esempio archiviati nell'account Data Lake Store avrà un aspetto simile all'istruzione seguente:

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

