---
title: Eseguire la migrazione da un archivio HDFS locale ad archiviazione di Azure con Azure Data Box
description: Migrare i dati da un archivio HDFS locale ad archiviazione di Azure
author: normesta
ms.service: storage
ms.date: 11/19/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 3360209e9de54d6011a2a430cd2c1fb54a315c43
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327593"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Eseguire la migrazione da un archivio HDFS locale ad archiviazione di Azure con Azure Data Box

È possibile eseguire la migrazione dei dati da un archivio HDFS locale del cluster Hadoop in archiviazione di Azure (archiviazione BLOB o Data Lake Storage Gen2) usando un dispositivo Data Box. È possibile scegliere tra un Data Box da 80 TB o una Data Box Heavy da 770 TB.

Questo articolo consente di completare queste attività:

> [!div class="checklist"]
> * Preparare la migrazione dei dati.
> * Copiare i dati in un Data Box o in un dispositivo Data Box Heavy.
> * Rispedire il dispositivo a Microsoft.
> * Spostare i dati su Data Lake Storage Gen2.

## <a name="prerequisites"></a>prerequisiti

Queste operazioni sono necessarie per completare la migrazione.

* Due account di archiviazione; una che dispone di uno spazio dei nomi gerarchico abilitato e che non lo è.

* Un cluster Hadoop locale che contiene i dati di origine.

* Un [dispositivo Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Ordinare il data box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) o [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Durante l'ordinamento del dispositivo, ricordarsi di scegliere un account di archiviazione in cui **non** sono abilitati gli spazi dei nomi gerarchici. Questo perché i dispositivi Data Box non supportano ancora l'inserimento diretto in Azure Data Lake Storage Gen2. Sarà necessario eseguire la copia in un account di archiviazione e quindi eseguire una seconda copia nell'account ADLS Gen2. Le istruzioni per questa operazione sono fornite nei passaggi seguenti.

  * Cablare e connettere il [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) o [Data Box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a una rete locale.

Se si è pronti, iniziamo.

## <a name="copy-your-data-to-a-data-box-device"></a>Copiare i dati in un dispositivo Data Box

Se i dati si integrano in un singolo dispositivo Data Box, i dati verranno copiati nel dispositivo Data Box. 

Se le dimensioni dei dati superano la capacità del dispositivo Data Box, usare la [procedura facoltativa per suddividere i dati tra più dispositivi data box](#appendix-split-data-across-multiple-data-box-devices) e quindi eseguire questo passaggio. 

Per copiare i dati dall'archivio HDFS locale a un dispositivo Data Box, è necessario impostare alcune operazioni e quindi usare lo strumento [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) .

Seguire questa procedura per copiare i dati tramite le API REST di archiviazione BLOB/oggetti nel dispositivo Data Box. L'interfaccia API REST renderà il dispositivo visualizzato come archivio HDFS nel cluster.

1. Prima di copiare i dati tramite REST, identificare le primitive di sicurezza e connessione per connettersi all'interfaccia REST nell'Data Box o Data Box Heavy. Accedere all'interfaccia utente Web locale del Data Box e passare alla pagina **Connetti e copia** . Nell'account di archiviazione di Azure per il dispositivo, in **impostazioni di accesso**individuare e selezionare **Rest**.

    ![Pagina "Connetti e copia"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Nella finestra di dialogo Access Storage account and upload data copiare l' **endpoint del servizio BLOB** e la **chiave dell'account di archiviazione**. Dall'endpoint del servizio BLOB omettere il `https://` e la barra finale.

    In questo caso, l'endpoint è: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. La parte host dell'URI da usare è: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Per un esempio, vedere come [connettersi a Rest su http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Finestra di dialogo "accedi a account di archiviazione e carica dati"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Aggiungere l'endpoint e l'indirizzo IP del nodo Data Box o Data Box Heavy per `/etc/hosts` in ogni nodo.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Se si usa un altro meccanismo per DNS, è necessario assicurarsi che l'endpoint del Data Box possa essere risolto.

4. Impostare la variabile della shell `azjars` sul percorso dei file di `hadoop-azure` e `azure-storage` jar. È possibile trovare questi file nella directory di installazione di Hadoop.

    Per determinare se sono presenti file, usare il comando seguente: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Sostituire il segnaposto `<hadoop_install_dir>` con il percorso della directory in cui è stato installato Hadoop. Assicurarsi di usare percorsi completi.

    Esempi:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Creare il contenitore di archiviazione che si vuole usare per la copia dei dati. È inoltre necessario specificare una directory di destinazione come parte di questo comando. A questo punto potrebbe essere una directory di destinazione fittizia.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Sostituire il segnaposto `<blob_service_endpoint>` con il nome dell'endpoint del servizio BLOB.

    * Sostituire il segnaposto `<account_key>` con la chiave di accesso dell'account.

    * Sostituire il segnaposto `<container-name>` con il nome del contenitore.

    * Sostituire il segnaposto `<destination_directory>` con il nome della directory in cui si desidera copiare i dati.

6. Eseguire un comando list per assicurarsi che il contenitore e la directory siano stati creati.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Sostituire il segnaposto `<blob_service_endpoint>` con il nome dell'endpoint del servizio BLOB.

   * Sostituire il segnaposto `<account_key>` con la chiave di accesso dell'account.

   * Sostituire il segnaposto `<container-name>` con il nome del contenitore.

7. Copiare i dati da Hadoop HDFS a Data Box archiviazione BLOB nel contenitore creato in precedenza. Se non viene trovata la directory in cui si esegue la copia, il comando lo crea automaticamente.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Sostituire il segnaposto `<blob_service_endpoint>` con il nome dell'endpoint del servizio BLOB.

    * Sostituire il segnaposto `<account_key>` con la chiave di accesso dell'account.

    * Sostituire il segnaposto `<container-name>` con il nome del contenitore.

    * Sostituire il segnaposto `<exlusion_filelist_file>` con il nome del file che contiene l'elenco di esclusioni dei file.

    * Sostituire il segnaposto `<source_directory>` con il nome della directory che contiene i dati che si desidera copiare.

    * Sostituire il segnaposto `<destination_directory>` con il nome della directory in cui si desidera copiare i dati.

    L'opzione `-libjars` viene utilizzata per rendere disponibili i `hadoop-azure*.jar` e i file `azure-storage*.jar` dipendenti per `distcp`. Questa situazione potrebbe essere già presente per alcuni cluster.

    Nell'esempio seguente viene illustrato il modo in cui viene utilizzato il comando `distcp` per copiare i dati.

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

    * Provare a modificare il numero di Mapper. L'esempio precedente USA `m` = 4 Mapper.

    * Provare a eseguire più `distcp` in parallelo.

    * Tenere presente che i file di grandi dimensioni offrono prestazioni migliori rispetto ai file piccoli.

## <a name="ship-the-data-box-to-microsoft"></a>Invia il Data Box a Microsoft

Seguire questa procedura per preparare e spedire il dispositivo Data Box a Microsoft.

1. Per prima cosa, [prepara per la spedizione nel data box o data box Heavy](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Al termine della preparazione del dispositivo, scaricare i file DBA. Questi file DBA o manifesto vengono usati in un secondo momento per verificare i dati caricati in Azure.

3. Arrestare il dispositivo e rimuovere i cavi.

4. Pianificare un ritiro con UPS.

    * Per Data Box dispositivi, vedere [spedire il data box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Per Data Box Heavy dispositivi, vedere [spedire il data box Heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Al termine della ricezione, il dispositivo è connesso alla rete data center e i dati vengono caricati nell'account di archiviazione specificato (con gli spazi dei nomi gerarchici disabilitati) quando è stato inserito l'ordine del dispositivo. Verificare in base ai file DBA che tutti i dati vengono caricati in Azure. È ora possibile spostare i dati in un account di archiviazione Data Lake Storage Gen2.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Spostare i dati in Azure Data Lake Storage Gen2

I dati sono già presenti nell'account di archiviazione di Azure. Verranno ora copiati i dati nell'account di archiviazione Azure Data Lake e verranno applicate le autorizzazioni di accesso a file e directory.

> [!NOTE]
> Questo passaggio è necessario se si usa Azure Data Lake Storage Gen2 come archivio dati. Se si usa solo un account di archiviazione BLOB senza spazio dei nomi gerarchico come archivio dati, è possibile ignorare questa sezione.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Copiare i dati nell'account Azure Data Lake Storage generazione 2

È possibile copiare i dati usando Azure Data Factory o usando il cluster Hadoop basato su Azure.

* Per usare Azure Data Factory, vedere [Azure Data Factory per spostare i dati in ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Assicurarsi di specificare l' **Archivio BLOB di Azure** come origine.

* Per usare il cluster Hadoop basato su Azure, eseguire questo comando DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Sostituire i segnaposto `<source_account>` e `<dest_account>` con i nomi degli account di archiviazione di origine e di destinazione.

    * Sostituire i segnaposto `<source_container>` e `<dest_container>` con i nomi dei contenitori di origine e di destinazione.

    * Sostituire i segnaposto `<source_path>` e `<dest_path>` con i percorsi della directory di origine e di destinazione.

    * Sostituire il segnaposto `<source_account_key>` con la chiave di accesso dell'account di archiviazione che contiene i dati.

    Questo comando copia i dati e i metadati dall'account di archiviazione nell'account di archiviazione Data Lake Storage Gen2.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Creare un'entità servizio per l'account Azure Data Lake Storage Gen2

Per creare un'entità servizio, vedere [procedura: usare il portale per creare un'applicazione Azure ad e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Quando si esegue la procedura descritta nella sezione [Assegnare l'applicazione a un ruolo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) dell'articolo, assicurarsi di assegnare il ruolo **Collaboratore ai dati del BLOB di archiviazione** all'entità servizio.

* Quando si eseguono i passaggi nella sezione [ottenere i valori per l'accesso](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) dell'articolo, salvare i valori di ID applicazione e segreto client in un file di testo. Saranno necessari a breve.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Genera un elenco di file copiati con le relative autorizzazioni

Dal cluster Hadoop locale eseguire questo comando:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Questo comando genera un elenco di file copiati con le relative autorizzazioni.

> [!NOTE]
> A seconda del numero di file nel HDFS, l'esecuzione di questo comando può richiedere molto tempo.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Generare un elenco di identità e mapparle a Azure Active Directory (Aggiungi) identità

1. Scaricare lo script `copy-acls.py`. Vedere la sezione [scaricare gli script dell'helper e configurare il nodo perimetrale per eseguirli in](#download-helper-scripts) questo articolo.

2. Eseguire questo comando per generare un elenco di identità univoche.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Questo script genera un file denominato `id_map.json` contenente le identità di cui è necessario eseguire il mapping alle identità basate su Aggiungi.

3. Aprire il file `id_map.json` in un editor di testo.

4. Per ogni oggetto JSON che viene visualizzato nel file, aggiornare l'attributo `target` di un nome dell'entità utente (UPN) AAD o di ObjectId (OID) con l'identità mappata appropriata. Al termine, salvare il file. Questo file sarà necessario nel passaggio successivo.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Applicare le autorizzazioni per i file copiati e applicare i mapping di identità

Eseguire questo comando per applicare le autorizzazioni ai dati copiati nell'account Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Sostituire il segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire il segnaposto `<container-name>` con il nome del contenitore.

* Sostituire i segnaposto `<application-id>` e `<client-secret>` con l'ID applicazione e il segreto client raccolti al momento della creazione dell'entità servizio.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Appendice: suddividere i dati tra più dispositivi Data Box

Prima di spostare i dati in un dispositivo di Data Box, è necessario scaricare alcuni script helper, assicurarsi che i dati siano organizzati per adattarsi a un dispositivo Data Box ed escludere eventuali file non necessari.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Scaricare gli script helper e configurare il nodo perimetrale per eseguirli

1. Dal nodo perimetrale o Head del cluster Hadoop locale eseguire questo comando:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Questo comando clona il repository GitHub che contiene gli script helper.

2. Verificare che nel computer locale sia installato il pacchetto [JQ](https://stedolan.github.io/jq/) .

   ```bash
   
   sudo apt-get install jq
   ```

3. Installare il pacchetto python [richieste](http://docs.python-requests.org/en/master/) .

   ```bash
   
   pip install requests
   ```

4. Impostare le autorizzazioni di esecuzione per gli script richiesti.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Assicurarsi che i dati siano organizzati per adattarsi a un dispositivo Data Box

Se le dimensioni dei dati superano le dimensioni di un singolo dispositivo di Data Box, è possibile suddividere i file in gruppi che è possibile archiviare su più dispositivi Data Box.

Se i dati non superano le dimensioni di un dispositivo Data Box singe, è possibile passare alla sezione successiva.

1. Con autorizzazioni elevate, eseguire lo script `generate-file-list` scaricato seguendo le istruzioni riportate nella sezione precedente.

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

2. Copiare gli elenchi di file generati in HDFS in modo che siano accessibili per il processo [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) .

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Escludi file non necessari

È necessario escludere alcune directory dal processo DisCp. Ad esempio, escludere le directory che contengono informazioni sullo stato che mantengono il cluster in esecuzione.

Nel cluster Hadoop locale in cui si intende avviare il processo DistCp, creare un file che specifichi l'elenco di directory che si desidera escludere.

Ecco un esempio:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul funzionamento di Data Lake Storage Gen2 con i cluster HDInsight. Vedere [Usare Archiviazione Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
