---
title: Usare Azure Data Box per migrare i dati da un'istanza locale HDFS archiviare in archiviazione di Azure
description: La migrazione dei dati da un archivio HDFS in locale in archiviazione di Azure
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 65ef586d74297e692160dc075dead2f0b1d973b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116961"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Usare Azure Data Box per migrare i dati da un archivio HDFS in locale in archiviazione di Azure

È possibile migrare i dati da un archivio HDFS in locale del cluster Hadoop in archiviazione di Azure (archiviazione blob o Data Lake Storage Gen2) usando un dispositivo Data Box. È possibile scegliere da un Data Box 80 TB o pesante finestra dati 770 TB.

Questo articolo consente di completare queste attività:

> [!div class="checklist"]
> * Preparare la migrazione dei dati.
> * Copiare i dati in una casella di dati o un dispositivo di dati finestra pesanti.
> * Spedire il dispositivo a Microsoft.
> * Spostare i dati in Data Lake Storage Gen2.

## <a name="prerequisites"></a>Prerequisiti

Sono necessari questi elementi per completare la migrazione.

* Due account di archiviazione; quella che ha uno spazio dei nomi gerarchico abilitata su di esso e uno che non.

* Un cluster Hadoop in locale che contiene i dati di origine.

* Un' [dispositivo Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Ordinare i dati Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) oppure [Data Box con intensa attività](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Durante l'ordinamento del dispositivo, ricordarsi di scegliere un account di archiviazione che **non** hanno spazi dei nomi gerarchici abilitata su di esso. Questo avviene perché i dispositivi di Data Box non supportano ancora l'inserimento diretto in Azure Data Lake Storage Gen2. È necessario copiare in un account di archiviazione e quindi eseguire una seconda copia nell'account di Azure Data Lake Store Gen2. Le istruzioni per questa sono specificate nei passaggi riportati di seguito.

  * Cavo e connettere il [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) o [elevati di dati finestra](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a una rete locale.

Se si è pronti, iniziare.

## <a name="copy-your-data-to-a-data-box-device"></a>Copiare i dati in un dispositivo Data Box

Se si inserisce i dati in un singolo dispositivo Data Box, si saranno copiare i dati nel dispositivo Data Box. 

Se la dimensione dei dati supera la capacità del dispositivo Data Box, usare il [procedura facoltativa per dividere i dati tra più dispositivi di Data Box](#appendix-split-data-across-multiple-data-box-devices) e quindi eseguire questo passaggio. 

Per copiare i dati dall'archivio HDFS locale a un dispositivo Data Box, è possibile configurare alcuni aspetti e quindi utilizzare il [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) dello strumento.

Seguire questi passaggi per copiare i dati tramite lo spazio di archiviazione REST API di/oggetto Blob nel dispositivo Data Box. L'interfaccia REST API renderà il dispositivo vengono visualizzati come un archivio HDFS nel cluster.

1. Prima di copiare i dati tramite REST, identificare le primitive di sicurezza e connessione per connettersi all'interfaccia REST nel Data Box o pesante finestra di dati. Accedere al web locale dell'interfaccia utente di Data Box e passare a **Connetti e copia** pagina. Per l'archiviazione di Azure dell'account per il dispositivo, nella sezione **impostazioni di accesso**, individuare e selezionare **REST**.

    ![Pagina "Connetti e copia"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Nell'account di archiviazione di accesso e la finestra di caricamento dei dati, copiare il **endpoint del servizio Blob** e il **chiave dell'account di archiviazione**. Dall'endpoint del servizio blob, omettere il `https://` e la barra finale.

    In questo caso, l'endpoint è: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. La parte host dell'URI che verrà usato è: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Per un esempio, vedere come [connettersi a REST su http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Finestra di dialogo "Accedere account di archiviazione e caricare i dati"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Aggiungere l'endpoint e l'indirizzo IP nodo ingenti di finestra di dati o Data Box per `/etc/hosts` in ogni nodo.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Se si usa un altro meccanismo per DNS, è necessario assicurarsi che il Data Box endpoint può essere risolto.

4. Impostare la variabile della shell `azjars` al percorso del `hadoop-azure` e `azure-storage` file jar. È possibile trovare questi file nella directory di installazione di Hadoop.

    Per determinare se questi file esistono, usare il comando seguente: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Sostituire il `<hadoop_install_dir>` segnaposto con il percorso della directory in cui è stato installato Hadoop. Assicurarsi di utilizzare percorsi completi.

    Esempi:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Creare il contenitore di archiviazione che si desidera usare per la copia dei dati. È anche necessario specificare una directory di destinazione come parte di questo comando. Potrebbe trattarsi di una directory di destinazione fittizio a questo punto.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Sostituire il `<blob_service_endpoint>` segnaposto con il nome dell'endpoint del servizio blob.

    * Sostituire il `<account_key>` segnaposto con la chiave di accesso dell'account.

    * Sostituire il `<container-name>` segnaposto con il nome del contenitore.

    * Sostituire il `<destination_directory>` segnaposto con il nome della directory in cui si desidera copiare i dati.

6. Eseguire un comando di elenco per assicurarsi che siano stati creati il contenitore e una directory.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Sostituire il `<blob_service_endpoint>` segnaposto con il nome dell'endpoint del servizio blob.

   * Sostituire il `<account_key>` segnaposto con la chiave di accesso dell'account.

   * Sostituire il `<container-name>` segnaposto con il nome del contenitore.

7. Copiare dati da Hadoop HDFS nell'archivio Blob di dati finestra, nel contenitore creato in precedenza. Se la directory che si sta copiando non viene trovata, il comando viene creata automaticamente.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Sostituire il `<blob_service_endpoint>` segnaposto con il nome dell'endpoint del servizio blob.

    * Sostituire il `<account_key>` segnaposto con la chiave di accesso dell'account.

    * Sostituire il `<container-name>` segnaposto con il nome del contenitore.

    * Sostituire il `<exlusion_filelist_file>` segnaposto con il nome del file che contiene l'elenco delle esclusioni di file.

    * Sostituire il `<source_directory>` segnaposto con il nome della directory che contiene i dati che si desidera copiare.

    * Sostituire il `<destination_directory>` segnaposto con il nome della directory in cui si desidera copiare i dati.

    Il `-libjars` opzione viene usata per rendere il `hadoop-azure*.jar` e il tipo dipendente `azure-storage*.jar` i file disponibili per `distcp`. Ciò potrebbe essere già presente per alcuni cluster.

    L'esempio seguente illustra come il `distcp` comando viene usato per copiare i dati.

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

    * Provare a modificare il numero di Mapper. (L'esempio precedente Usa `m` = 4 Mapper.)

    * Provare a eseguire più `distcp` in parallelo.

    * Tenere presente che i file di grandi dimensioni offrono prestazioni migliori rispetto ai file di piccole dimensioni.

## <a name="ship-the-data-box-to-microsoft"></a>Spedire Data Box a Microsoft

Seguire questi passaggi per preparare e spedire il dispositivo Data Box a Microsoft.

1. Prima di tutto [Prepara per la spedizione nel Data Box o dati casella pesante](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Dopo aver completato la preparazione dei dispositivi, scaricare i file della distinta base. Si utilizzeranno questi DBA o manifesto i file in un secondo momento per verificare i dati caricati in Azure.

3. Arrestare il dispositivo e rimuovere i cavi.

4. Pianificare un ritiro con UPS.

    * Per i dispositivi di Data Box, vedere [spedire il Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Per i dispositivi dati casella pesante, vedere [spedire il pesante finestra dati](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Dopo che Microsoft riceve il dispositivo, è connesso alla rete del data center e i dati vengono caricati nell'account di archiviazione specificati (con gli spazi dei nomi gerarchico disabilitato) quando è inserito l'ordine di dispositivo. Verificare che tutti i dati viene caricato in Azure con i file della distinta base. È ora possibile passare i dati a un account di archiviazione Gen2 di archivio Data Lake.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Spostare i dati in Azure Data Lake Storage Gen2

Hai già i dati nell'account di archiviazione di Azure. Ora copiare i dati nell'account di archiviazione di Azure Data Lake e applicare le autorizzazioni di accesso a file e directory.

> [!NOTE]
> Questo passaggio è necessario se si usa Gen2 di archiviazione di Azure Data Lake come archivio dati. Se si usa solo un account di archiviazione blob senza spazio dei nomi gerarchico come archivio dati, è possibile ignorare questa sezione.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Copiare i dati all'account Azure Data Lake Storage generazione 2

È possibile copiare dati usando Data Factory di Azure o usando il cluster Hadoop basati su Azure.

* Per usare Azure Data Factory, vedere [Azure Data Factory per spostare i dati in Azure Data Lake Store Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Assicurarsi di specificare **archiviazione Blob di Azure** come origine.

* Per usare il cluster Hadoop basati su Azure, eseguire questo comando DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Sostituire il `<source_account>` e `<dest_account>` segnaposto con i nomi degli account di archiviazione di origine e destinazione.

    * Sostituire il `<source_container>` e `<dest_container>` segnaposto con i nomi dei contenitori di origine e destinazione.

    * Sostituire il `<source_path>` e `<dest_path>` segnaposto con i percorsi di directory di origine e destinazione.

    * Sostituire il `<source_account_key>` segnaposto con la chiave di accesso dell'account di archiviazione che contiene i dati.

    Questo comando Copia i metadati sia i dati dall'account di archiviazione nell'account di archiviazione Gen2 di archivio Data Lake.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Creare un'entità servizio per l'account di archiviazione Gen2 di Azure Data Lake

Per creare un'entità servizio, vedere [come: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Quando si esegue la procedura descritta nella sezione [Assegnare l'applicazione a un ruolo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) dell'articolo, assicurarsi di assegnare il ruolo **Collaboratore ai dati del BLOB di archiviazione** all'entità servizio.

* Quando si esegue la procedura descritta nel [ottenere i valori per l'accesso](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) sezione dell'articolo, ID applicazione e i valori del segreto client in un file di testo. Saranno necessari a breve.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Generare un elenco di file copiati con le relative autorizzazioni

Dal cluster Hadoop in locale, eseguire questo comando:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Questo comando genera un elenco di file copiati con le relative autorizzazioni.

> [!NOTE]
> A seconda del numero di file in HDFS, questo comando può richiedere molto tempo per l'esecuzione.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Generare un elenco di identità ed eseguirne il mapping alle identità di Azure Active Directory (Aggiungi)

1. Scaricare il `copy-acls.py` script. Vedere le [scaricare gli script helper e impostare il nodo perimetrale per eseguirli](#download-helper-scripts) sezione di questo articolo.

2. Eseguire questo comando per generare un elenco delle identità univoche.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Questo script genera un file denominato `id_map.json` che contiene le identità che è necessario per eseguire il mapping delle identità basate su Aggiungi.

3. Aprire il file `id_map.json` in un editor di testo.

4. Per ogni oggetto JSON che viene visualizzato nel file, aggiornare il `target` identità il mapping di attributi di AAD nome entità utente (UPN) o un ID oggetto (OID), con l'appropriato. Al termine, salvare il file. È necessario il file nel passaggio successivo.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Applicare le autorizzazioni per i file copiati e applicare il mapping di identità

Eseguire questo comando per applicare le autorizzazioni per i dati che è stato copiato nell'account di Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Sostituire il segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire il `<container-name>` segnaposto con il nome del contenitore.

* Sostituire il `<application-id>` e `<client-secret>` segnaposto con l'applicazione ID e il segreto client che sono stati raccolti durante la creazione dell'entità servizio.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Appendice: Divisione dei dati tra più dispositivi di Data Box

Prima di spostare i dati in un dispositivo Data Box, è necessario scaricare alcuni script helper, assicurarsi che i dati sono organizzati per adattarla a un dispositivo Data Box ed escludere i file non necessari.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Scaricare gli script helper e impostare il nodo perimetrale per eseguirli

1. Dal nodo head del cluster Hadoop in locale o edge, eseguire questo comando:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Questo comando consente di duplicare il repository GitHub che contiene gli script helper.

2. Assicurarsi che hanno le [jq](https://stedolan.github.io/jq/) pacchetto installato nel computer locale.

   ```bash
   
   sudo apt-get install jq
   ```

3. Installare il [richieste](http://docs.python-requests.org/en/master/) pacchetto python.

   ```bash
   
   pip install requests
   ```

4. Set di autorizzazioni di esecuzione sugli script necessari.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Assicurarsi che i dati sono organizzati per adattarla a un dispositivo Data Box

Se le dimensioni dei dati superano le dimensioni di un singolo dispositivo Data Box, è possibile suddividere i file di in gruppi che è possibile archiviare in più dispositivi di Data Box.

Se i dati non superano le dimensioni di un singolo dispositivo Data Box, è possibile procedere alla sezione successiva.

1. Con le autorizzazioni con privilegi elevate, eseguire il `generate-file-list` script scaricato seguendo le istruzioni riportate nella sezione precedente.

   Ecco una descrizione dei parametri di comando:

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

2. Copia il file generato sono elencati in HDFS in modo che siano accessibili per il [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) processo.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Escludere i file non necessari

È necessario escludere alcune directory dal processo di DisCp. Ad esempio, escludere le directory che contengono informazioni sullo stato che il cluster esegua.

Nel cluster Hadoop in locale in cui si intende avviare il processo DistCp, creare un file che specifica l'elenco delle directory in cui si desidera escludere.

Ad esempio:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul funzionamento di Data Lake Storage Gen2 con i cluster HDInsight. Vedere [Usare Archiviazione Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
