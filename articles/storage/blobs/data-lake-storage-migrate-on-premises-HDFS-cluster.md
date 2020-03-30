---
title: Eseguire la migrazione dall'archivio HDFS locale ad Archiviazione di Azure con Azure Data BoxMigrate from on-prem HDFS store to Azure Storage with Azure Data Box
description: Eseguire la migrazione dei dati da un archivio HDFS locale ad Archiviazione di AzureMigrate data from an on-premises HDFS store to Azure Storage
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: c0c6a8637223727a9b0c88245d939605f6a8530e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302001"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Eseguire la migrazione dall'archivio HDFS locale ad Archiviazione di Azure con Azure Data BoxMigrate from on-prem HDFS store to Azure Storage with Azure Data Box

È possibile eseguire la migrazione dei dati da un archivio HDFS locale del cluster Hadoop in Archiviazione di Azure (archiviazione BLOB o Generazione 2 di Archiviazione data lake) usando un dispositivo Data Box.You can migrate data from an on-premises HDFS store of your Hadoop cluster into Azure Storage (blob storage or Data Lake Storage Gen2) by using a Data Box device. È possibile scegliere tra una scatola dati da 80 TB o una Data Box Heavy da 770 TB.

Questo articolo consente di completare queste attività:This article helps you complete these tasks:

> [!div class="checklist"]
> * Preparare la migrazione dei dati.
> * Copiare i dati in una casella dati o in un dispositivo Data Box Heavy.
> * Spedire il dispositivo a Microsoft.
> * Applicare le autorizzazioni di accesso a file e directory (solo Data Lake Storage Gen2)

## <a name="prerequisites"></a>Prerequisiti

Queste operazioni sono necessarie per completare la migrazione.

* Un account dell'Archiviazione di Azure.

* Un cluster Hadoop locale che contiene i dati di origine.

* Un [dispositivo Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Ordina la tua Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) o Data Box [Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). 

  * Cablare e collegare la [casella dati](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) o Data [Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a una rete locale.

Se sei pronto, iniziamo.

## <a name="copy-your-data-to-a-data-box-device"></a>Copiare i dati su un dispositivo Data Box

Se i tuoi dati si adattano a un singolo dispositivo Data Box, i dati verranno copiati nel dispositivo Data Box. 

Se le dimensioni dei dati superano la capacità del dispositivo Data Box, utilizzare la [procedura facoltativa per suddividere i dati tra più dispositivi Data Box](#appendix-split-data-across-multiple-data-box-devices) e quindi eseguire questo passaggio. 

Per copiare i dati dall'archivio HDFS locale in un dispositivo Data Box, è necessario impostare alcune operazioni e quindi utilizzare lo strumento [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

Seguire questi passaggi per copiare i dati tramite le API REST dell'archiviazione BLOB/oggetti nel dispositivo Data Box.Follow these steps to copy data via the REST APIs of Blob/Object storage to your Data Box device. L'interfaccia dell'API REST farà apparire il dispositivo come archivio HDFS nel cluster.

1. Prima di copiare i dati tramite REST, identificare le primitive di sicurezza e connessione per connettersi all'interfaccia REST nella casella dati o su Data Box Heavy. Accedere all'interfaccia utente Web locale di Data Box e passare alla pagina **Connetti e copia.** Per l'account di archiviazione di Azure per il dispositivo, in **Impostazioni di accesso**individuare e selezionare **REST**.

    ![Pagina "Connetti e copia"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Nella finestra di dialogo Accesso all'account di archiviazione e caricamento dei dati copiare **l'endpoint** del servizio BLOB e la chiave dell'account **di archiviazione.** Dall'endpoint del servizio BLOB, `https://` omettere la barra e quella finale.

    In questo caso, l'endpoint è: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. La parte host dell'URI da utilizzare `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`è: . Per un esempio, vedere come [connettersi a REST su http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Finestra di dialogo "Accedi all'account di archiviazione e al caricamento dei dati"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Aggiungere l'endpoint e l'indirizzo IP del `/etc/hosts` nodo Casella dati o Blocco dati a ogni nodo.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Se si utilizza un altro meccanismo per DNS, è necessario assicurarsi che l'endpoint Data Box possa essere risolto.

4. Impostare la `azjars` variabile di `hadoop-azure` shell `azure-storage` sulla posizione dei file e jar. È possibile trovare questi file nella directory di installazione di Hadoop.

    Per determinare se questi file esistono, `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`utilizzare il comando seguente: . Sostituire `<hadoop_install_dir>` il segnaposto con il percorso della directory in cui è stato installato Hadoop. Assicurarsi di utilizzare percorsi completi.

    Esempi:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Creare il contenitore di archiviazione che si vuole usare per la copia dei dati. È inoltre necessario specificare una directory di destinazione come parte di questo comando. Potrebbe trattarsi di una directory di destinazione fittizia a questo punto.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Sostituire `<blob_service_endpoint>` il segnaposto con il nome dell'endpoint del servizio BLOB.

    * Sostituire `<account_key>` il segnaposto con la chiave di accesso dell'account.

    * Sostituire `<container-name>` il segnaposto con il nome del contenitore.

    * Sostituire `<destination_directory>` il segnaposto con il nome della directory in cui si desidera copiare i dati.

6. Eseguire un comando list per assicurarsi che il contenitore e la directory siano stati creati.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Sostituire `<blob_service_endpoint>` il segnaposto con il nome dell'endpoint del servizio BLOB.

   * Sostituire `<account_key>` il segnaposto con la chiave di accesso dell'account.

   * Sostituire `<container-name>` il segnaposto con il nome del contenitore.

7. Copiare i dati dall'HDFS Hadoop all'archiviazione BLOB della casella di dati nel contenitore creato in precedenza. Se la directory in cui si sta copiando non viene trovata, il comando la crea automaticamente.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Sostituire `<blob_service_endpoint>` il segnaposto con il nome dell'endpoint del servizio BLOB.

    * Sostituire `<account_key>` il segnaposto con la chiave di accesso dell'account.

    * Sostituire `<container-name>` il segnaposto con il nome del contenitore.

    * Sostituire `<exlusion_filelist_file>` il segnaposto con il nome del file che contiene l'elenco delle esclusioni di file.

    * Sostituire `<source_directory>` il segnaposto con il nome della directory contenente i dati che si desidera copiare.

    * Sostituire `<destination_directory>` il segnaposto con il nome della directory in cui si desidera copiare i dati.

    `-libjars` L'opzione viene utilizzata `hadoop-azure*.jar` per `azure-storage*.jar` rendere disponibili `distcp`i file e i file dipendenti per . Ciò può già verificarsi per alcuni cluster.

    Nell'esempio seguente `distcp` viene illustrato come utilizzare il comando per copiare i dati.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Per migliorare la velocità di copia:

    * Provare a modificare il numero di mappatori. (L'esempio `m` precedente usa 4 mappatori.)

    * Provare a `distcp` eseguire più in parallelo.

    * Tenere presente che le prestazioni di file di grandi dimensioni sono migliori rispetto ai file di piccole dimensioni.

## <a name="ship-the-data-box-to-microsoft"></a>Spedire la casella dei dati a Microsoft

Seguire questi passaggi per preparare e spedire il dispositivo Data Box a Microsoft.

1. In primo luogo, [Prepararsi a spedire sul vostro Data Box o Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Al termine della preparazione del dispositivo, scaricare i file della distinta componenti. Questi file DELLA distinta materiali o del manifesto verranno utilizzati in un secondo momento per verificare i dati caricati in Azure.You will use these BOM or manifest files later to verify the data uploaded to Azure.

3. Spegnere il dispositivo e rimuovere i cavi.

4. Pianificare un ritiro con UPS.

    * Per i dispositivi Data Box, consultate [Spedire la casella dati.](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)

    * Per i dispositivi Data Box Heavy, consultate [Spedire il tuo Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Dopo che Microsoft riceve il dispositivo, questo viene connesso alla rete del data center e i dati vengono caricati nell'account di archiviazione specificato al momento dell'ordine del dispositivo. Verificare che tutti i dati vengano caricati in Azure per verificare che tutti i dati vengano caricati in Azure.Verify against the BOM files that all your data is uploaded to Azure. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Applicare le autorizzazioni di accesso a file e directory (solo Data Lake Storage Gen2)

I dati sono già presenti nell'account di archiviazione di Azure.You already have the data into your Azure Storage account. Ora applicherai le autorizzazioni di accesso a file e directory.

> [!NOTE]
> Questo passaggio è necessario solo se si usa Azure Data Lake Storage Gen2 come archivio dati. Se si usa solo un account di archiviazione BLOB senza spazio dei nomi gerarchico come archivio dati, è possibile ignorare questa sezione.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Creare un'entità servizio per l'account Azure Data Lake Storage Gen2Create a service principal for your Azure Data Lake Storage Gen2 account

Per creare un'entità servizio, vedere [Procedura: Usare il portale per creare un'applicazione Azure AD e un'entità servizio in grado](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)di accedere alle risorse.

* Quando si esegue la procedura descritta nella sezione [Assegnare l'applicazione a un ruolo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) dell'articolo, assicurarsi di assegnare il ruolo **Collaboratore ai dati del BLOB di archiviazione** all'entità servizio.

* Quando si eseguono i passaggi nella sezione [Ottenere valori per l'accesso](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) dell'articolo, salvare l'ID applicazione e i valori dei segreti client in un file di testo. Saranno necessari a breve.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Generare un elenco di file copiati con le relative autorizzazioni

Dal cluster Hadoop locale eseguire questo comando:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Questo comando genera un elenco di file copiati con le relative autorizzazioni.

> [!NOTE]
> A seconda del numero di file nell'HDFS, l'esecuzione di questo comando può richiedere molto tempo.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Generare un elenco di identità ed eseguire il mapping alle identità di Azure Active Directory (ADD)

1. Scaricare `copy-acls.py` lo script. Vedere la sezione [Scaricare gli script helper e configurare il nodo perimetrale per eseguirli.](#download-helper-scripts)

2. Eseguire questo comando per generare un elenco di identità univoche.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Questo script genera `id_map.json` un file denominato che contiene le identità di cui è necessario eseguire il mapping alle identità basate su ADD.

3. Aprire il file `id_map.json` in un editor di testo.

4. Per ogni oggetto JSON visualizzato nel `target` file, aggiornare l'attributo di un nome dell'entità utente AAD (UPN) o ObjectId (OID), con l'identità mappata appropriata. Al termine, salvare il file. Questo file sarà necessario nel passaggio successivo.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Applicare le autorizzazioni ai file copiati e applicare i mapping di identità

Eseguire questo comando per applicare le autorizzazioni ai dati copiati nell'account Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Sostituire il segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire `<container-name>` il segnaposto con il nome del contenitore.

* Sostituire `<application-id>` i `<client-secret>` segnaposto e con l'ID applicazione e il segreto client raccolti al momento della creazione dell'entità servizio.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Appendice: Dividere i dati su più dispositivi Data BoxAppendix: Split data across multiple Data Box devices

Prima di spostare i dati su un dispositivo Data Box, è necessario scaricare alcuni script helper, assicurarsi che i dati siano organizzati per adattarli a un dispositivo Data Box ed escludere eventuali file non necessari.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Scaricare gli script helper e configurare il nodo perimetrale per eseguirli

1. Dal nodo edge o head del cluster Hadoop locale eseguire questo comando:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Questo comando clona il repository GitHub che contiene gli script helper.

2. Assicurarsi che nel computer locale sia installato il pacchetto [jq.](https://stedolan.github.io/jq/)

   ```bash
   
   sudo apt-get install jq
   ```

3. Installare il pacchetto [Python delle richieste.](https://2.python-requests.org/en/master/)

   ```bash
   
   pip install requests
   ```

4. Impostare le autorizzazioni di esecuzione per gli script necessari.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Assicurarsi che i dati siano organizzati per adattarli a un dispositivo Data Box

Se le dimensioni dei dati superano le dimensioni di un singolo dispositivo Data Box, è possibile suddividere i file in gruppi che è possibile archiviare su più dispositivi Data Box.

Se i tuoi dati non superano le dimensioni di un dispositivo Singe Data Box, puoi passare alla sezione successiva.

1. Con autorizzazioni elevate, `generate-file-list` eseguire lo script scaricato seguendo le indicazioni riportate nella sezione precedente.

   Ecco una descrizione dei parametri del comando:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Copiare gli elenchi di file generati in HDFS in modo che siano accessibili al processo [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Escludere i file non necessari

È necessario escludere alcune directory dal processo DisCp. Ad esempio, escludere le directory che contengono informazioni sullo stato che mantengono in esecuzione il cluster.

Nel cluster Hadoop locale in cui si prevede di avviare il processo DistCp, creare un file che specifichi l'elenco delle directory che si desidera escludere.

Ad esempio:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul funzionamento di Data Lake Storage Gen2 con i cluster HDInsight.Learn how Data Lake Storage Gen2 works with HDInsight clusters. Vedere [Usare Archiviazione Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
