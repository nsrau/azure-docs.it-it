---
title: Copiare dati da HDFS usando Azure Data Factory
description: Informazioni su come copiare dati da un'origine HDFS locale o cloud in archivi dati di sink supportati usando l'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jingwang
ms.openlocfilehash: 562acfe1ae96f7f88b72945846bcb49c0cc1f216
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179539"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Copiare i dati dal server HDFS usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
> * [Versione 1](v1/data-factory-hdfs-connector.md)
> * [Versione corrente](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come copiare dati dal server Hadoop Distributed File System (HDFS). Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore HDFS è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine e sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

In particolare, il connettore HDFS supporta:

- Copia di file usando l'autenticazione di *Windows* (Kerberos) o *anonima* .
- Copia di file usando il protocollo *webhdfs* o il supporto *DistCp incorporato* .
- La copia di file così come sono o tramite l'analisi o la generazione di file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Verificare che il runtime di integrazione possa accedere a *tutti* i [nome server del nodo]: [porta del nodo nome] e [server del nodo dati]: [porta del nodo dati] del cluster Hadoop. Il valore predefinito [porta del nodo dei nomi] è 50070 e il valore predefinito [porta del nodo dati] è 50075.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per HDFS.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato HDFS sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà *Type* deve essere impostata su *HDFS*. | Sì |
| url |URL di HDFS |Sì |
| authenticationType | I valori consentiti sono *anonimi* o *Windows*. <br><br> Per configurare l'ambiente locale, vedere la sezione usare l' [autenticazione Kerberos per il connettore HDFS](#use-kerberos-authentication-for-the-hdfs-connector) . |Sì |
| userName |Nome utente per l'autenticazione di Windows. Per l'autenticazione Kerberos, specificare ** \<username> @ \<domain> . com**. |Sì (per l'autenticazione di Windows) |
| password |Password per l'autenticazione di Windows. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro nel data factory oppure [fare riferimento a un segreto archiviato in un insieme di credenziali delle chiavi di Azure](store-credentials-in-key-vault.md). |Sì (per l'autenticazione di Windows) |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per ulteriori informazioni, vedere la sezione [prerequisiti](#prerequisites) . Se il runtime di integrazione non è specificato, il servizio usa il Azure Integration Runtime predefinito. |No |

**Esempio: uso dell'autenticazione anonima**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: uso dell'autenticazione di Windows**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere [DataSets in Azure Data Factory](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per HDFS `location` in impostazioni nel set di dati basato sul formato:

| Proprietà   | Descrizione                                                  | Obbligatoria |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | La proprietà *Type* in `location` nel set di dati deve essere impostata su *HdfsLocation*. | Sì      |
| folderPath | Percorso della cartella. Se si vuole usare un carattere jolly per filtrare la cartella, ignorare questa impostazione e specificare il percorso nelle impostazioni dell'origine dell'attività. | No       |
| fileName   | Nome del file sotto il folderPath specificato. Se si vuole usare un carattere jolly per filtrare i file, ignorare questa impostazione e specificare il nome del file in Impostazioni origine attività. | No       |

**Esempio:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [pipeline e attività in Azure Data Factory](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine HDFS.

### <a name="hdfs-as-source"></a>HDFS come origine

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Le proprietà seguenti sono supportate per HDFS `storeSettings` in impostazioni nell'origine copia basata sul formato:

| Proprietà                 | Descrizione                                                  | Obbligatoria                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | La proprietà *Type* in `storeSettings` deve essere impostata su **HdfsReadSettings**. | Sì                                           |
| ***Individuare i file da copiare*** |  |  |
| OPZIONE 1: percorso statico<br> | Copiare la cartella o il percorso del file specificato nel set di dati. Se si desidera copiare tutti i file da una cartella, specificare anche `wildcardFileName` come `*`. |  |
| OPZIONE 2: carattere jolly<br>- wildcardFolderPath | Percorso della cartella con caratteri jolly per filtrare le cartelle di origine. <br>I caratteri jolly consentiti sono: `*` (corrispondenza di zero o più caratteri) e `?` (corrispondenza di zero caratteri o di un carattere singolo). Usare `^` per eseguire l'escape se il nome della cartella effettivo ha un carattere jolly o questo carattere di escape all'interno di. <br>Per altri esempi, vedere [esempi di filtro di file e cartelle](#folder-and-file-filter-examples). | No                                            |
| OPZIONE 2: carattere jolly<br>- wildcardFileName | Nome file con caratteri jolly sotto il folderPath/wildcardFolderPath specificato per filtrare i file di origine. <br>I caratteri jolly consentiti sono: `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un carattere singolo); usare `^` per eseguire l'escape se il nome della cartella effettivo contiene un carattere jolly o questo carattere di escape.  Per altri esempi, vedere [esempi di filtro di file e cartelle](#folder-and-file-filter-examples). | Sì |
| OPZIONE 3: un elenco di file<br>- fileListPath | Indica la copia di un set di file specificato. Puntare a un file di testo che include un elenco di file da copiare, ovvero un file per riga, con il percorso relativo del percorso configurato nel set di dati.<br/>Quando si usa questa opzione, non specificare il nome del file nel set di dati. Per altri esempi, vedere [esempi di elenco di file](#file-list-examples). |No |
| ***Impostazioni aggiuntive*** |  | |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando `recursive` è impostato su *true* e il sink è un archivio basato su file, una cartella o una sottocartella vuota non viene copiata o creata nel sink. <br>I valori consentiti sono *true* (predefinito) e *false*.<br>Questa proprietà non è applicabile quando si configura `fileListPath`. |No |
| modifiedDatetimeStart    | I file vengono filtrati in base all' *Ultima modifica*apportata all'attributo. <br>I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di `modifiedDatetimeStart` a `modifiedDatetimeEnd` . L'ora viene applicata al fuso orario UTC nel formato *2018-12-01T05:00:00Z*. <br> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati.<br/>Questa proprietà non è applicabile quando si configura `fileListPath`. | No                                            |
| enablePartitionDiscovery | Per i file partizionati, specificare se analizzare le partizioni dal percorso del file e aggiungerle come colonne di origine aggiuntive.<br/>I valori consentiti sono **false** (impostazione predefinita) e **true**. | No                                            |
| partitionRootPath | Quando è abilitata l'individuazione delle partizioni, specificare il percorso radice assoluto per leggere le cartelle partizionate come colonne di dati.<br/><br/>Se non viene specificato, per impostazione predefinita<br/>-Quando si usa il percorso del file in un set di dati o un elenco di file nell'origine, il percorso radice della partizione è il percorso configurato nel set di dati.<br/>-Quando si usa il filtro di cartelle con caratteri jolly, il percorso radice della partizione è il percorso secondario prima del primo carattere jolly.<br/><br/>Si supponga, ad esempio, di configurare il percorso nel set di dati come "root/folder/Year = 2020/month = 08/Day = 27":<br/>-Se si specifica il percorso radice della partizione come "root/folder/Year = 2020", l'attività di copia genererà altre due colonne `month` e `day` con il valore "08" e "27", oltre alle colonne all'interno dei file.<br/>-Se il percorso radice della partizione non è specificato, non verrà generata alcuna colonna aggiuntiva. | No                                            |
| maxConcurrentConnections | Il numero di connessioni che possono connettersi all'archivio di archiviazione simultaneamente. Specificare un valore solo quando si desidera limitare la connessione simultanea all'archivio dati. | No                                            |
| ***Impostazioni DistCp*** |  | |
| distcpSettings | Gruppo di proprietà da usare quando si usa HDFS DistCp. | No |
| resourceManagerEndpoint | Endpoint YARN (anche un altro negoziatore di risorse) | Sì, se si usa DistCp |
| tempScriptPath | Percorso della cartella usato per archiviare lo script del comando Temp DistCp. Il file di script viene generato da Data Factory e verrà rimosso al termine del processo di copia. | Sì, se si usa DistCp |
| distcpOptions | Opzioni aggiuntive per il comando DistCp. | No |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Esempi di filtro file e cartelle

In questa sezione viene descritto il comportamento risultante se si utilizza un filtro con caratteri jolly con il percorso e il nome file della cartella.

| folderPath | fileName             | ricorsiva | Struttura delle cartelle di origine e risultato del filtro (i file in **grassetto** sono stati recuperati) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (vuoto, usare valore predefinito) | false     | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (vuoto, usare valore predefinito) | true      | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AltraCartellaB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Esempi di elenco di file

Questa sezione descrive il comportamento risultante dall'uso di un percorso elenco file nell'origine dell'attività di copia. Si presuppone che sia presente la struttura di cartelle di origine seguente e si desidera copiare i file in grassetto:

| Esempio di struttura di origine                                      | Contenuto in FileListToCopy.txt                             | Configurazione di Azure Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;CartellaA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadati<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Sottocartella1/File3.csv<br>Sottocartella1/File5.csv | **Nel set di dati:**<br>- Percorso cartella: `root/FolderA`<br><br>**Nell'origine dell'attività di copia:**<br>- Percorso elenco file: `root/Metadata/FileListToCopy.txt` <br><br>Il percorso dell'elenco dei file punta a un file di testo nello stesso archivio dati che include un elenco di file da copiare, ovvero un file per riga, con il percorso relativo del percorso configurato nel set di dati. |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Usare DistCp per copiare dati da HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) è uno strumento da riga di comando nativo di Hadoop per eseguire una copia distribuita in un cluster Hadoop. Quando si esegue un comando in DistCp, elenca prima tutti i file da copiare e quindi crea diversi processi di mapping nel cluster Hadoop. Ogni processo di mapping esegue una copia binaria dall'origine al sink.

L'attività di copia supporta l'uso di DistCp per copiare i file così come sono nell'archiviazione BLOB di Azure (inclusa la [copia temporanea](copy-activity-performance.md)) o in un archivio Azure Data Lake. In questo caso, DistCp può sfruttare la potenza del cluster anziché eseguire il runtime di integrazione self-hosted. L'uso di DistCp offre una migliore velocità effettiva di copia, soprattutto se il cluster è molto potente. In base alla configurazione nel data factory, l'attività di copia crea automaticamente un comando DistCp, lo invia al cluster Hadoop e monitora lo stato della copia.

### <a name="prerequisites"></a>Prerequisiti

Per usare DistCp per copiare i file così come sono da HDFS nell'archivio BLOB di Azure (inclusa la copia temporanea) o in Azure Data Lake Store, assicurarsi che il cluster Hadoop soddisfi i requisiti seguenti:

* I servizi MapReduce e YARN sono abilitati.  
* La versione di YARN è 2,5 o successiva.  
* Il server HDFS è integrato con l'archivio dati di destinazione: **archiviazione BLOB di Azure** o **Azure Data Lake Store (ADLS Gen1)**: 

    - Il FileSystem di BLOB di Azure è supportato in modo nativo a partire da Hadoop 2.7. È necessario solo specificare il percorso del file JAR nella configurazione dell'ambiente Hadoop.
    - Il FileSystem di Azure Data Lake Store è disponibile come pacchetto a partire da Hadoop 3.0.0-alpha1. Se la versione del cluster Hadoop è precedente alla versione, è necessario importare manualmente i pacchetti JAR correlati a Azure Data Lake Store (Azure-datalake-Store. jar) nel cluster da [qui](https://hadoop.apache.org/releases.html)e specificare il percorso del file jar nella configurazione dell'ambiente Hadoop.

* Preparare una cartella temporanea in HDFS. Questa cartella temporanea viene usata per archiviare uno script della shell DistCp, quindi occuperà lo spazio a livello di KB.
* Assicurarsi che l'account utente fornito nel servizio collegato HDFS disponga delle autorizzazioni per:
   * Inviare un'applicazione in YARN.
   * Creare una sottocartella e i file di lettura/scrittura nella cartella Temp.

### <a name="configurations"></a>Configurazioni

Per le configurazioni e gli esempi correlati a DistCp, passare alla sezione [HDFS come origine](#hdfs-as-source) .

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>Usare l'autenticazione Kerberos per il connettore HDFS

Sono disponibili due opzioni per la configurazione dell'ambiente locale per l'uso dell'autenticazione Kerberos per il connettore HDFS. È possibile scegliere quello più adatto alla propria situazione.
* Opzione 1: [aggiungere un computer del runtime di integrazione self-hosted nell'area di autenticazione Kerberos](#kerberos-join-realm)
* Opzione 2: [Abilitare il trust reciproco tra il dominio Windows e l'area di autenticazione Kerberos](#kerberos-mutual-trust)

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opzione 1: aggiungere un computer del runtime di integrazione self-hosted nell'area di autenticazione Kerberos

#### <a name="requirements"></a>Requisiti

* Il computer del runtime di integrazione self-hosted deve essere aggiunto all'area di autenticazione Kerberos e non può essere aggiunto a un dominio Windows.

#### <a name="how-to-configure"></a>Modalità di configurazione

**Nel computer del runtime di integrazione self-hosted:**

1.  Eseguire l'utilità Ksetup per configurare l'area di autenticazione e il server Centro distribuzione chiavi (KDC) Kerberos.

    Il computer deve essere configurato come membro di un gruppo di lavoro, perché un'area di autenticazione Kerberos è diversa da un dominio Windows. Per ottenere questa configurazione, è possibile impostare l'area di autenticazione Kerberos e aggiungere un server KDC eseguendo i comandi seguenti. Sostituire *Realm.com* con il proprio nome dell'area di autenticazione.

    ```console
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    Dopo aver eseguito questi comandi, riavviare il computer.

2.  Verificare la configurazione con il `Ksetup` comando. L'output dovrebbe essere simile al seguente:

    ```output
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**Nel data factory:**

* Configurare il connettore HDFS usando l'autenticazione di Windows insieme al nome e alla password dell'entità Kerberos per connettersi all'origine dati HDFS. Per informazioni dettagliate sulla configurazione, vedere la sezione [proprietà del servizio collegato HDFS](#linked-service-properties) .

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Opzione 2: Abilitare il trust reciproco tra il dominio Windows e l'area di autenticazione Kerberos

#### <a name="requirements"></a>Requisiti

*   Il computer del runtime di integrazione self-hosted deve essere aggiunto a un dominio Windows.
*   È necessaria l'autorizzazione per aggiornare le impostazioni del controller di dominio.

#### <a name="how-to-configure"></a>Modalità di configurazione

> [!NOTE]
> Sostituire REALM.COM e AD.COM nell'esercitazione seguente con il nome dell'area di autenticazione e il controller di dominio.

**Nel server KDC:**

1. Modificare la configurazione KDC nel file *krb5. conf* per consentire a KDC di considerare attendibile il dominio Windows facendo riferimento al modello di configurazione seguente. Per impostazione predefinita, la configurazione si trova in */etc/krb5.conf*.

   ```config
   [logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log
            
   [libdefaults]
    default_realm = REALM.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h
    renew_lifetime = 7d
    forwardable = true
            
   [realms]
    REALM.COM = {
     kdc = node.REALM.COM
     admin_server = node.REALM.COM
    }
   AD.COM = {
    kdc = windc.ad.com
    admin_server = windc.ad.com
   }
            
   [domain_realm]
    .REALM.COM = REALM.COM
    REALM.COM = REALM.COM
    .ad.com = AD.COM
    ad.com = AD.COM
            
   [capaths]
    AD.COM = {
     REALM.COM = .
    }
    ```

   Dopo aver configurato il file, riavviare il servizio KDC.

2. Preparare un'entità denominata *krbtgt/Realm. COM \@ ad.com* nel server KDC con il comando seguente:

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. Nel file di configurazione del servizio HDFS *hadoop.security.auth_to_local* aggiungere `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`.

**Nel controller di dominio:**

1.  Eseguire i `Ksetup` comandi seguenti per aggiungere una voce dell'area di autenticazione:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Stabilire il trust dal dominio Windows all'area di autenticazione Kerberos. [password] è la password per l'entità di sicurezza *krbtgt/REALM.COM\@AD.COM*.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Selezionare l'algoritmo di crittografia utilizzato in Kerberos.

    a. Selezionare **Server Manager**  >  **criteri di gruppo**  >  **dominio**  >  di gestione**criteri di gruppo oggetti**  >  **predefiniti o criteri di dominio attivi**, quindi scegliere **modifica**.

    b. Nel riquadro **Editor gestione criteri di gruppo** selezionare **Configurazione computer**  >  **criteri**impostazioni di  >  **Windows**impostazioni  >  di**sicurezza**  >  **criteri locali**  >  **Opzioni di sicurezza**e quindi configurare **sicurezza di rete: configura tipi di crittografia consentiti per Kerberos**.

    c. Selezionare l'algoritmo di crittografia che si desidera utilizzare per la connessione al server KDC. È possibile selezionare tutte le opzioni.

    ![Screenshot del riquadro "sicurezza di rete: configura tipi di crittografia consentiti per Kerberos"](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. Usare il `Ksetup` comando per specificare l'algoritmo di crittografia da usare nell'area di autenticazione specificata.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Creare il mapping tra l'account di dominio e l'entità Kerberos, in modo che sia possibile usare l'entità Kerberos nel dominio Windows.

    a. Selezionare **strumenti**  >  **di amministrazione Active Directory utenti e computer**.

    b. Configurare le funzionalità avanzate selezionando **Visualizza** > **Funzionalità avanzate**.

    c. Nel riquadro **funzionalità avanzate** , fare clic con il pulsante destro del mouse sull'account in cui si desidera creare i mapping e nel riquadro **mapping** nomi selezionare la scheda **nomi Kerberos** .

    d. Aggiungere un'entità di sicurezza dall'area di autenticazione.

       ![Riquadro "mapping identità di sicurezza"](media/connector-hdfs/map-security-identity.png)

**Nel computer del runtime di integrazione self-hosted:**

* Eseguire i `Ksetup` comandi seguenti per aggiungere una voce dell'area di autenticazione.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**Nel data factory:**

* Configurare il connettore HDFS usando l'autenticazione di Windows insieme all'account di dominio o all'entità Kerberos per connettersi all'origine dati HDFS. Per informazioni dettagliate sulla configurazione, vedere la sezione [proprietà del servizio collegato HDFS](#linked-service-properties) .

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per informazioni sulle proprietà dell'attività Lookup, vedere [attività Lookup in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="legacy-models"></a>Modalità legacy

>[!NOTE]
>I modelli seguenti sono ancora supportati come per la compatibilità con le versioni precedenti. Si consiglia di utilizzare il nuovo modello illustrato in precedenza, poiché l'interfaccia utente di creazione Azure Data Factory ha cambiato la generazione del nuovo modello.

### <a name="legacy-dataset-model"></a>Modello di set di dati legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà *Type* del set di dati deve essere impostata su *FileShare* |Sì |
| folderPath | Percorso della cartella. È supportato un filtro con caratteri jolly. I caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un singolo carattere); utilizzare `^` per eseguire l'escape se il nome file effettivo dispone di un carattere jolly o di questo carattere di escape all'interno di. <br/><br/>Esempi: cartellaradice/sottocartella/. Vedere altri esempi in [Esempi di filtro file e cartelle](#folder-and-file-filter-examples). |Sì |
| fileName |  Il nome o il filtro con caratteri jolly per i file sotto il "folderPath" specificato. Se non si specifica alcun valore per questa proprietà, il set di dati punta a tutti i file nella cartella. <br/><br/>Per il filtro, i caratteri jolly consentiti sono `*` (corrisponde a zero o più caratteri) e `?` (corrisponde a zero o a un carattere singolo).<br/>- Esempio 1: `"fileName": "*.csv"`<br/>- Esempio 2: `"fileName": "???20180427.txt"`<br/>Usare `^` per eseguire l'escape se il nome della cartella effettivo ha un carattere jolly o questo carattere di escape all'interno di. |No |
| modifiedDatetimeStart | I file vengono filtrati in base all' *Ultima modifica*apportata all'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di `modifiedDatetimeStart` a `modifiedDatetimeEnd` . L'ora viene applicata al fuso orario UTC nel formato *2018-12-01T05:00:00Z*. <br/><br/> Tenere presente che le prestazioni complessive dello spostamento dei dati saranno influenzate dall'abilitazione di questa impostazione quando si desidera applicare un filtro di file a un numero elevato di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati.| No |
| modifiedDatetimeEnd | I file vengono filtrati in base all' *Ultima modifica*apportata all'attributo. I file vengono selezionati se l'ora dell'Ultima modifica rientra nell'intervallo di `modifiedDatetimeStart` a `modifiedDatetimeEnd` . L'ora viene applicata al fuso orario UTC nel formato *2018-12-01T05:00:00Z*. <br/><br/> Tenere presente che le prestazioni complessive dello spostamento dei dati saranno influenzate dall'abilitazione di questa impostazione quando si desidera applicare un filtro di file a un numero elevato di file. <br/><br/> Le proprietà possono essere NULL, il che significa che al set di dati non viene applicato alcun filtro di attributi di file.  Quando `modifiedDatetimeStart` ha un valore DateTime ma `modifiedDatetimeEnd` è null, significa che i file il cui ultimo attributo modificato è maggiore o uguale al valore DateTime sono selezionati.  Quando `modifiedDatetimeEnd` ha un valore DateTime ma `modifiedDatetimeStart` è null, significa che i file il cui attributo Last modified è minore del valore DateTime sono selezionati.| No |
| format | Per copiare i file così come sono tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vogliono analizzare file con un formato specifico, sono supportati i tipi di formato file seguenti: *TextFormat*, *JsonFormat*, *AvroFormat*, *OrcFormat*, *ParquetFormat*. Impostare la proprietà *type* nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Formato JSON](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Formato AVRO](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Formato OCR](supported-file-formats-and-compression-codecs-legacy.md#orc-format) e [Formato Parquet](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>I tipi supportati sono: *gzip*, *deflate*, *bzip2*e *ZipDeflate*.<br/>I livelli supportati sono: *Optimal* (Ottimale) e *Fastest* (Più veloce). |No |

>[!TIP]
>Per copiare tutti i file in una cartella, specificare solo **folderPath**.<br>Per copiare un singolo file con un nome specificato, specificare **folderPath** con la parte della cartella e **filename** con il nome file.<br>Per copiare un subset di file in una cartella, specificare **folderPath** con il percorso della cartella e **fileName** con il filtro con caratteri jolly.

**Esempio:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Modello di origine dell'attività di copia legacy

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà *Type* dell'origine dell'attività di copia deve essere impostata su *HdfsSource*. |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Quando ricorsivo è impostato su *true* e il sink è un archivio basato su file, una cartella o una sottocartella vuota non verrà copiata o creata nel sink.<br/>I valori consentiti sono *true* (predefinito) e *false*. | No |
| distcpSettings | Il gruppo di proprietà quando si usa HDFS DistCp. | No |
| resourceManagerEndpoint | Endpoint Gestione risorse YARN | Sì, se si usa DistCp |
| tempScriptPath | Percorso della cartella usato per archiviare lo script del comando Temp DistCp. Il file di script viene generato da Data Factory e verrà rimosso al termine del processo di copia. | Sì, se si usa DistCp |
| distcpOptions | Per il comando DistCp sono disponibili opzioni aggiuntive. | No |
| maxConcurrentConnections | Il numero di connessioni che possono connettersi all'archivio di archiviazione simultaneamente. Specificare un valore solo quando si desidera limitare la connessione simultanea all'archivio dati. | No |

**Esempio: origine HDFS nell'attività di copia mediante DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
