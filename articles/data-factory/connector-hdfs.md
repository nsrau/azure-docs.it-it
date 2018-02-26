---
title: Copiare dati da HDFS usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da un'origine HDFS locale o cloud in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 400c58abf04d28dd0e5f1d7aac204f09c43b942e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-hdfs-using-azure-data-factory"></a>Copiare dati da HDFS usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-hdfs-connector.md)
> * [Versione 2 - Anteprima](connector-hdfs.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da HDFS. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore HDFS nella versione 1](v1/data-factory-hdfs-connector.md).


## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un HDFS a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore HDFS supporta:

- La copia di file usando l'autenticazione di **Windows** (Kerberos) o **Anonima**.
- La copia di file usando il protocollo **webhdfs** o il supporto per **DistCp integrato**.
- La copia di file così come sono o l'analisi/generazione di file con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).

## <a name="prerequisites"></a>prerequisiti

Per copiare dati da un server HDFS non accessibile pubblicamente, è necessario configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](concepts-integration-runtime.md).

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per HDFS.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di HDFS sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Hdfs**. | Sì |
| URL |URL di HDFS |Sì |
| authenticationType | I valori consentiti sono: **Anonima** o **Windows**. <br><br> Per usare l'**autenticazione Kerberos** per il connettore HDFS, fare riferimento a [questa sezione](#use-kerberos-authentication-for-hdfs-connector) per impostare correttamente l'ambiente locale. |Sì |
| userName |Nome utente per l'autenticazione di Windows Per l'autenticazione Kerberos specificare `<username>@<domain>.com`. |Sì (per l'autenticazione di Windows) |
| password |Password per l'autenticazione di Windows Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì (per l'autenticazione di Windows) |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati HDFS.

Per copiare dati da HDFS, impostare la proprietà type del set di dati su **FileShare**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **FileShare** |Sì |
| folderPath | Percorso della cartella. Ad esempio: folder/subfolder/ |Sì |
| fileName | Se si vuole copiare da un file specifico, specificare il nome del file in **folderPath**. Se non si specifica alcun valore per questa proprietà, il set di dati seleziona tutti i file nella cartella come origine. |No  |
| format | Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output.<br/><br/>Se si vuole analizzare file con un formato specifico, sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](supported-file-formats-and-compression-codecs.md#text-format), [JsonFormat](supported-file-formats-and-compression-codecs.md#json-format), [AvroFormat](supported-file-formats-and-compression-codecs.md#avro-format), [OrcFormat](supported-file-formats-and-compression-codecs.md#orc-format) e [ParquetFormat](supported-file-formats-and-compression-codecs.md#parquet-format). |No (solo per uno scenario di copia binaria) |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No  |

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
            "fileName": "myfile.csv.gz",
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

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine HDFS.

### <a name="hdfs-as-source"></a>HDFS come origine

Per copiare dati da HDFS, impostare il tipo di origine nell'attività di copia su **HdfsSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **HdfsSource** |Sì |
| ricorsiva | Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. Si noti che se recursive è impostata su true e il sink è un archivio basato su file, la cartella o la sottocartella vuota non verrà copiata o creata nel sink.<br/>I valori consentiti sono: **true** (predefinito), **false** | No  |
| distcpSettings | Il gruppo di proprietà quando si usa HDFS DistCp. | No  |
| resourceManagerEndpoint | L'endpoint di ResourceManager YARN | Sì, se si usa DistCp |
| tempScriptPath | Un percorso di cartella usato per archiviare lo script di comandi temporaneo per DistCp. Il file di script viene generato da Data Factory e verrà rimosso al termine del processo di copia. | Sì, se si usa DistCp |
| distcpOptions | Opzioni aggiuntive per il comando DistCp. | No  |

**Esempio: origine HDFS nell'attività di copia usando UNLOAD**

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

Per altre informazioni su come usare DistCp per copiare in modo efficiente dati da HDFS, vedere la sezione successiva.

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Usare DistCp per copiare dati da HDFS

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) è uno strumento da riga di comando nativo di Hadoop per eseguire la copia distribuita in un cluster Hadoop. Quando si esegue un comando DistCp, vengono prima elencati tutti i file da copiare e quindi vengono creati vari processi di mapping nel cluster Hadoop, ognuno dei quali esegue la copia binaria dall'origine al sink.

L'attività di copia supporta l'uso di DistCp per copiare file così come sono in BLOB di Azure (inclusa una [copia di staging](copy-activity-performance.md)) o in Azure Data Lake Store, in modo da poter sfruttare appieno la potenza del cluster anziché eseguirlo nel runtime di integrazione self-hosted. I miglioramenti in termini di velocità di copia risultano particolarmente significativi se il cluster è molto potente. In base alla configurazione in Azure Data Factory, l'attività di copia crea automaticamente un comando distcp, lo invia al cluster Hadoop e monitorizza lo stato della copia.

### <a name="prerequsites"></a>Prerequisiti

Per usare DistCp per copiare file così come sono da HDFS in BLOB di Azure (inclusa una copia di staging) o in Azure Data Lake Store, assicurarsi che il cluster Hadoop soddisfi i requisiti seguenti:

1. I servizi MapReduce e Yarn siano abilitati.
2. La versione di Yarn sia almeno 2.5.
3. Il server HDFS sia integrato con l'archivio dati di destinazione: BLOB di Azure o Azure Data Lake Store.

    - Il FileSystem di BLOB di Azure è supportato in modo nativo a partire da Hadoop 2.7. È sufficiente specificare il percorso del file con estensione jar nella configurazione dell'ambiente Hadoop.
    - Il FileSystem di Azure Data Lake Store è disponibile come pacchetto a partire da Hadoop 3.0.0-alpha1. Se la versione del cluster Hadoop è inferiore, è necessario importare manualmente nel cluster pacchetti JAR correlati ad Azure Data Lake Store (azure-Lake-store.jar) da [qui](https://hadoop.apache.org/releases.html) e specificare il percorso del file JAR nella configurazione dell'ambiente Hadoop.

4. Preparare una cartella temporanea in HDFS. Questa cartella temporanea viene usata per archiviare lo script della shell DistCp, in modo che occupi solo qualche KB di spazio.
5. Verificare che l'account utente specificato nel servizio collegato di HDFS disponga dell'autorizzazione per a) inviare l'applicazione a Yarn; b) creare una sottocartella, leggere/scrivere file nella cartella temporanea sopra indicata.

### <a name="configurations"></a>Configurazioni

Di seguito è riportato un esempio di configurazione dell'attività di copia per copiare dati da HDFS in BLOB di Azure tramite DistCp:

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromHDFSToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "HDFSDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HdfsSource",
                "distcpSettings": {
                    "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                    "tempScriptPath": "/usr/hadoop/tempscript",
                    "distcpOptions": "-strategy dynamic -map 100"
                }
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Uso dell'autenticazione Kerberos per il connettore HDFS

Sono disponibili due opzioni per configurare l'ambiente locale in modo da poter usare l'autenticazione Kerberos nel connettore HDFS. L'utente può scegliere quella più adatta al caso specifico.
* Opzione 1: [aggiungere il computer del runtime di integrazione self-hosted nell'area di autenticazione di Kerberos](#kerberos-join-realm)
* Opzione 2: [Abilitare il trust reciproco tra il dominio di Windows e l'area di autenticazione di Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opzione 1: aggiungere il computer del runtime di integrazione self-hosted nell'area di autenticazione di Kerberos

#### <a name="requirements"></a>Requisiti

* Il computer del runtime di integrazione self-hosted deve essere aggiunto all'area di autenticazione di Kerberos e non può essere aggiunto a un dominio di Windows.

#### <a name="how-to-configure"></a>Come configurare

**Nel computer del runtime di integrazione self-hosted:**

1.  Eseguire l'utilità **Ksetup** per configurare il server e l'area didi autenticazione di Kerberos KDC.

    Il computer deve essere configurato come membro di un gruppo di lavoro poiché un'area di autenticazione di Kerberos è diversa da un dominio di Windows. È possibile farlo configurando l'area di autenticazione di Kerberos e aggiungendo un server KDC come indicato di seguito. Sostituire *REALM.COM* con la propria area di autenticazione.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Riavviare** il computer dopo aver eseguito questi 2 comandi.

2.  Verificare la configurazione con il comando **Ksetup**. L'output dovrebbe essere simile al seguente:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**In Azure Data Factory:**

* Configurare il connettore HDFS usando **l'autenticazione di Windows** con il nome e la password principali di Kerberos per connettersi all'origine dati HDFS. Controllare la sezione [Proprietà del servizio collegato HDFS](#linked-service-properties) per i dettagli di configurazione.

### <a name="kerberos-mutual-trust"></a>Opzione 2: Abilitare il trust reciproco tra il dominio di Windows e l'area di autenticazione di Kerberos

#### <a name="requirements"></a>Requisiti

*   Il computer del runtime di integrazione self-hosted deve essere aggiunto a un dominio di Windows.
*   È necessaria l'autorizzazione per aggiornare le impostazioni del controller di dominio.

#### <a name="how-to-configure"></a>Come configurare

> [!NOTE]
> Sostituire REALM.COM e AD.COM nell'esercitazione di seguito con le rispettive aree di autenticazione e controller di dominio in base alle esigenze.

**Nel server KDC:**

1.  Modificare la configurazione KDC nel file **krb5.conf** per far considerare attendibile a KDC il dominio di Windows che fa riferimento al modello di configurazione di seguito. Per impostazione predefinita, la configurazione si trova in **/etc/krb5.conf**.

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

  **Riavviare** il servizio KDC dopo la configurazione.

2.  Preparare un'entità denominata **krbtgt/REALM.COM@AD.COM** nel server KDC con il comando seguente:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  Nel file di configurazione del servizio HDFS **hadoop.security.auth_to_local** aggiungere `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**Nel controller di dominio:**

1.  Eseguire i comandi **Ksetup** seguenti per aggiungere una voce di area di autenticazione:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Stabilire una relazione di trust dal dominio di Windows all'area di autenticazione di Kerberos. [password] è la password per l'entità **krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Selezionare l'algoritmo di crittografia usato in Kerberos.

    1. Passare a Server Manager > Gestione Criteri di gruppo > Dominio > Oggetti Criteri di gruppo > Criteri dominio attivi o predefiniti e selezionare Modifica.

    2. Nella finestra popup **Editor Gestione criteri di gruppo**, passare a Configurazione computer > Criteri > Impostazioni di Windows > Impostazioni di sicurezza > Criteri locali > Opzioni di sicurezza e configurare **Sicurezza di rete: configura tipi di crittografia consentiti per Kerberos**.

    3. Selezionare l'algoritmo di crittografia da usare per connettersi a KDC. In genere, è possibile selezionare semplicemente tutte le opzioni.

        ![Configurare i tipi di crittografia per Kerberos](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    4. Utilizzare il comando **Ksetup** per specificare l'algoritmo di crittografia da usare nell'area di autenticazione specifica.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Creare il mapping tra l'account di dominio e l'entità Kerberos per poter usare quest'ultima nel dominio di Windows.

    1. Avviare Strumenti di amministrazione > **Utenti e computer di Active Directory**.

    2. Configurare funzionalità avanzate facendo clic su **Visualizza** > **Funzionalità avanzate**.

    3. Individuare l'account per cui si desidera creare i mapping e fare clic con il tasto destor del mouse per visualizzare **Mapping nomi**. Fare clic sulla scheda **Nomi Kerberos**. 

    4. Aggiungere un'entità dall'area di autenticazione.

        ![Eseguire il mapping di sicurezza e identità](media/connector-hdfs/map-security-identity.png)

**Nel computer del runtime di integrazione self-hosted:**

* Eseguire i comandi **Ksetup** seguenti per aggiungere una voce di area di autenticazione.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**In Azure Data Factory:**

* Configurare il connettore HDFS usando **l'autenticazione di Windows** con l'account di dominio o l'entità Kerberos per connettersi all'origine dati HDFS. Controllare la sezione [Proprietà del servizio collegato HDFS](#linked-service-properties) per i dettagli di configurazione.


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).