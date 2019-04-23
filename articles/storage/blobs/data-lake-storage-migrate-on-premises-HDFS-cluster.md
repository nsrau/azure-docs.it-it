---
title: Usare Azure Data Box per migrare i dati da un'istanza locale HDFS archiviare in archiviazione di Azure
description: La migrazione dei dati da un archivio HDFS in locale in archiviazione di Azure
services: storage
author: normesta
ms.service: storage
ms.date: 03/01/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: d0908e9edce8efb7a378ee04b6076b61cae2d2bf
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998295"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Usare Azure Data Box per migrare i dati da un archivio HDFS in locale in archiviazione di Azure

È possibile migrare i dati da un archivio HDFS in locale del cluster Hadoop in archiviazione di Azure (archiviazione blob o Data Lake Storage Gen2) usando un dispositivo Data Box.

Questo articolo consente di completare queste attività:

:heavy_check_mark: Copiare i dati in un dispositivo Data Box.

:heavy_check_mark: Spedire il dispositivo Data Box a Microsoft.

:heavy_check_mark: Spostare i dati nell'account di archiviazione Gen2 di archivio Data Lake.

## <a name="prerequisites"></a>Prerequisiti

Sono necessari questi elementi per completare la migrazione.

* Account di archiviazione di Azure che **non** hanno spazi dei nomi gerarchici abilitata su di esso.

* Se si desidera usare account di Azure Data Lake Storage Gen2 (un account di archiviazione che **viene** hanno spazi dei nomi gerarchici abilitato), quindi si potrebbe voler crearla a questo punto.

* Un cluster Hadoop in locale che contiene i dati di origine.

* Un' [dispositivo Azure Data Box](https://azure.microsoft.com/services/storage/databox/). 

    - [Ordinare il Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered). Durante la finestra di ordinamento, ricordarsi di scegliere un account di archiviazione che **non** hanno spazi dei nomi gerarchici abilitata su di esso. Questo avviene perché Data Box non supporta ancora l'inserimento diretto in Azure Data Lake Storage Gen2. È necessario copiare in un account di archiviazione e quindi eseguire una seconda copia nell'account di Azure Data Lake Store Gen2. Le istruzioni per questa sono specificate nei passaggi riportati di seguito.
    - [Cavo e connettere il Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) a una rete locale.

Se si è pronti, iniziare.

## <a name="copy-your-data-to-a-data-box-device"></a>Copiare i dati in un dispositivo Data Box

Per copiare i dati dall'archivio HDFS locale a un dispositivo Data Box, è possibile configurare alcuni aspetti e quindi utilizzare il [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) dello strumento.

Se la quantità di dati che si siano copiando è maggiore rispetto alla capacità di una singola casella dei dati, è necessario suddividere il set di dati in dimensioni che rientrano nelle finestre di dati.

Seguire questi passaggi per copiare i dati tramite lo spazio di archiviazione REST API di/oggetto Blob a Data Box. L'interfaccia REST API renderà Data Box vengono visualizzati come un archivio HDFS nel cluster. 


1. Prima di copiare i dati tramite REST, identificare le primitive di sicurezza e connessione per connettersi all'interfaccia REST in Data Box. Accedere al web locale dell'interfaccia utente di Data Box e passare a **Connetti e copia** pagina. Per l'archiviazione di Azure dell'account per il Data Box, sotto **impostazioni di accesso**, individuare e selezionare **REST(Preview)**.

    ![Pagina "Connetti e copia"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Nell'account di archiviazione di accesso e la finestra di caricamento dei dati, copiare il **endpoint del servizio Blob** e il **chiave dell'account di archiviazione**. Dall'endpoint del servizio blob, omettere il `https://` e la barra finale.

    In questo caso, l'endpoint è: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. La parte host dell'URI che verrà usato è: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Per un esempio, vedere come [connettersi a REST su http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Finestra di dialogo "Accedere account di archiviazione e caricare i dati"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Aggiungere l'endpoint e l'indirizzo IP di Data Box per `/etc/hosts` in ogni nodo.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Se si usa un altro meccanismo per DNS, è necessario assicurarsi che il Data Box endpoint può essere risolto.
    
3. Impostare una variabile della shell `azjars` in modo che punti la `hadoop-azure` e il `microsoft-windowsazure-storage-sdk` file jar. Questi file sono sotto la directory di installazione di Hadoop (si può verificare l'esistono di questi file tramite il comando `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` in cui `<hadoop_install_dir>` è la directory in cui è stato installato Hadoop) usare i percorsi completi. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

4. Copiare dati da Hadoop HDFS nell'archivio Blob di dati finestra.

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   Il `-libjars` opzione viene usata per rendere il `hadoop-azure*.jar` e il tipo dipendente `azure-storage*.jar` i file disponibili per `distcp`. Ciò potrebbe essere già presente per alcuni cluster.
   
   L'esempio seguente illustra come il `distcp` comando viene usato per copiare i dati.
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
Per migliorare la velocità di copia:
- Provare a modificare il numero di Mapper. (L'esempio precedente Usa `m` = 4 Mapper.)
- Provare a eseguire più `distcp` in parallelo.
- Tenere presente che i file di grandi dimensioni offrono prestazioni migliori rispetto ai file di piccole dimensioni.       
    
## <a name="ship-the-data-box-to-microsoft"></a>Spedire Data Box a Microsoft

Seguire questi passaggi per preparare e spedire il dispositivo Data Box a Microsoft.

1. Dopo aver completata la copia dei dati, eseguire [Prepara per la spedizione](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest) nella casella di dati. Dopo aver completato la preparazione dei dispositivi, scaricare i file della distinta base. Si utilizzeranno questi DBA o manifesto i file in un secondo momento per verificare i dati caricati in Azure. Arrestare il dispositivo e rimuovere i cavi. 
2.  Pianificare un ritiro con gruppo di continuità [spedire il Data Box ad Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up). 
3.  Dopo che Microsoft riceve il dispositivo, è connesso al Data Center alla rete e i dati vengono caricati nell'account di archiviazione specificati (con gli spazi dei nomi gerarchico disabilitato) quando è stato ordinato Data Box. Verificare che tutti i dati viene caricato in Azure con i file della distinta base. È ora possibile passare i dati a un account di archiviazione Gen2 di archivio Data Lake.

## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Spostare i dati nell'account di archiviazione Gen2 di archivio Data Lake

Questo passaggio è necessario se si usa Gen2 di archiviazione di Azure Data Lake come archivio dati. Se si usa solo un account di archiviazione blob senza spazio dei nomi gerarchico come archivio dati, non occorre eseguire questo passaggio.

È possibile farlo in 2 modi. 

- Uso [Azure Data Factory per spostare i dati in Azure Data Lake Store Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). È necessario specificare **archiviazione Blob di Azure** come origine.

- Usare il cluster Hadoop basati su Azure. È possibile eseguire questo comando DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

Questo comando Copia i metadati sia i dati dall'account di archiviazione nell'account di archiviazione Gen2 di archivio Data Lake.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul funzionamento di Data Lake Storage Gen2 con i cluster HDInsight. Vedere [Usare Archiviazione Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
