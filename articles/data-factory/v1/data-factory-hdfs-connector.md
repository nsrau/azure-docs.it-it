---
title: Spostare dati da HDFS locale | Documentazione Microsoft
description: Informazioni su come spostare dati da un HDFS locale con Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 62a1052c0b2674e3292d5f89c0b8863439dd3928
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Spostare dati da HDFS locale con Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-hdfs-connector.md)
> * [Versione 2 - Anteprima](../connector-hdfs.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, che è in anteprima, vedere le informazioni sul [connettore HDFS nella versione 2](../connector-hdfs.md).

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da un HDFS locale. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

È possibile copiare dati da un HDFS a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory supporta attualmente solo lo spostamento di dati da un HDFS ad altri archivi dati, ma non da altri archivi dati a un HDFS locale.

> [!NOTE]
> L'attività di copia non elimina il file di origine dopo che è stato correttamente copiato nella destinazione. Se è necessario eliminare il file di origine dopo una copia con esito positivo, creare un'attività personalizzata per eliminare il file e usare l'attività nella pipeline. 

## <a name="enabling-connectivity"></a>Abilitazione della connettività
Il servizio Data Factory supporta la connessione a un HDFS locale tramite il Gateway di gestione dati. Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway. Usare il gateway per connettersi a HDFS anche se è ospitato in una VM IaaS di Azure.

> [!NOTE]
> Assicurarsi che il gateway di gestione dati possa accedere a **TUTTI** i [server del nodo dei nomi]: [porta del nodo dei nomi] e [server del nodo dati]: [porta del nodo dati] del cluster Hadoop. La [porta del nodo dei nomi] predefinita è 50070 e la [porta del nodo dati] predefinita è 50075.

Anche se è possibile installare il gateway nello stesso computer locale o nella stessa VM di Azure di HDFS, è consigliabile installarlo in un diverso computer o in una diversa VM IaaS di Azure. La presenza del gateway su un computer separato riduce i conflitti di risorse e consente di ottenere prestazioni migliori. Quando si installa il gateway in un computer separato, questo deve poter accedere al computer con un HDFS.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da un'origine HDFS usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia.
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output.

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per un esempio con definizioni JSON per entità di data factory utilizzate per copiare dati da un archivio dati HDFS, vedere la sezione [Esempio JSON: Copiare dati da un HDFS locale al BLOB di Azure](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) di questo articolo.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di data factory specifiche di HDFS:

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
Un servizio collegato collega un archivio dati a una data factory. Viene creato un servizio collegato di tipo **Hdfs** per collegare un HDFS locale alla data factory. La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato HDFS.

| Proprietà | DESCRIZIONE | Obbligatoria |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su: **Hdfs** |Sì |
| Url |URL di HDFS |Sì |
| authenticationType |Anonima o Windows. <br><br> Per usare l'**autenticazione Kerberos** per il connettore HDFS, fare riferimento a [questa sezione](#use-kerberos-authentication-for-hdfs-connector) per impostare correttamente l'ambiente locale. |Sì |
| userName |Nome utente per l'autenticazione di Windows Per l'autenticazione Kerberos specificare `<username>@<domain>.com`. |Sì (per l'autenticazione di Windows) |
| password |Password per l'autenticazione di Windows |Sì (per l'autenticazione di Windows) |
| gatewayName |Nome del gateway che il servizio Data Factory deve usare per connettersi a HDFS. |Sì |
| encryptedCredential |[New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) delle credenziali di accesso. |No  |

### <a name="using-anonymous-authentication"></a>Uso dell'autenticazione anonima

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Uso dell'autenticazione di Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **FileShare** , che include il set di dati HDFS, presenta le proprietà seguenti.

| Proprietà | DESCRIZIONE | Obbligatoria |
| --- | --- | --- |
| folderPath |Percorso della cartella. Esempio: `myfolder`<br/><br/>Usare il carattere di escape "\" per i caratteri speciali nella stringa. Ad esempio: per cartella\sottocartella specificare cartella\\\\sottocartella e per d:\cartellaesempio specificare l'unità d:\\\\cartellaesempio.<br/><br/>È possibile combinare questa proprietà con **partitionBy** per ottenere percorsi di cartelle basati su data e ora di inizio/fine delle sezioni. |Sì |
| fileName |Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato sarà nel formato seguente: <br/><br/>Data<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No  |
| partitionedBy |partitionedBy può essere usato per specificare un valore folderPath dinamico e un nome file per i dati di una serie temporale. Ad esempio, folderPath con parametri per ogni ora di dati. |No  |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No  |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No  |

> [!NOTE]
> filename e fileFilter non possono essere usati contemporaneamente.

### <a name="using-partionedby-property"></a>Uso della proprietà partionedBy
Come indicato nella sezione precedente, è possibile specificare valori fileName e folderPath dinamici per i dati di una serie temporale con la proprietà **partitionedBy**, le [funzioni di data factory e le variabili di sistema](data-factory-functions-variables.md).

Per ulteriori informazioni sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, vedere gli articoli [Creazione di set di dati](data-factory-create-datasets.md), [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) e [Creazione di pipeline](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Esempio 1.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
In questo esempio {Slice} viene sostituito con il valore della variabile di sistema SliceStart di Data Factory nel formato (AAAAMMGGHH) specificato. SliceStart fa riferimento all'ora di inizio della sezione. La proprietà folderPath è diversa per ogni sezione. Ad esempio: wikisampledataout/wikidatagateway/2014100103 o wikisampledataout/wikidatagateway/2014100104.

#### <a name="sample-2"></a>Esempio 2:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
In questo esempio l'anno, il mese, il giorno e l'ora di SliceStart vengono estratti in variabili separate che vengono usate dalle proprietà folderPath e fileName.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Per l'attività di copia con origine di tipo **FileSystemSource** , nella sezione typeProperties sono disponibili le proprietà seguenti:

**FileSystemSource** supporta le proprietà seguenti:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True, False (valore predefinito) |No  |

## <a name="supported-file-and-compression-formats"></a>Formati di file e di compressione supportati
Per i dettagli, vedere l'articolo relativo ai [file e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Esempio JSON: Copiare dati da un HDFS locale al BLOB di Azure
Questo esempio illustra come copiare dati da HDFS locale all'archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.  

L'esempio fornisce le definizioni JSON per le entità di data factory seguenti. È possibile usare queste definizioni per creare una pipeline per copiare dati da HDFS a un'archiviazione BLOB di Azure mediante il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

1. Un servizio collegato di tipo [OnPremisesHdfs](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [FileShare](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio copia i dati da un HDFS locale a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio, impostare il gateway di gestione dati. Le istruzioni sono disponibili nell'articolo [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Servizio collegato HDFS**: questo esempio usa l'autenticazione di Windows. Per i diversi tipi di autenticazione disponibili, vedere la sezione [Proprietà del servizio collegato HDFS](#linked-service-properties) .

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Servizio collegato Archiviazione di Azure:**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Set di dati di input HDFS**: questo set di dati fa riferimento alla cartella HDFS DataTransfer/UnitTest/. La pipeline copia tutti i file di questa cartella nella destinazione.

Impostando "external" su "true" si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Un'attività di copia in una pipeline con un'origine su file system e un sink BLOB:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **FileSystemSource** e il tipo di **sink** è impostato su **BlobSink**. La query SQL specificata per la proprietà **query** consente di selezionare i dati da copiare nell'ultima ora.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Uso dell'autenticazione Kerberos per il connettore HDFS
Sono disponibili due opzioni per configurare l'ambiente locale in modo da poter usare l'autenticazione Kerberos nel connettore HDFS. L'utente può scegliere quella più adatta al caso specifico.
* Opzione 1: [aggiungere un computer gateway all'area di autenticazione di Kerberos](#kerberos-join-realm)
* Opzione 2: [Abilitare il trust reciproco tra il dominio di Windows e l'area di autenticazione di Kerberos](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>Opzione 1: aggiungere un computer gateway all'area di autenticazione di Kerberos

#### <a name="requirement"></a>Requisito:

* Il computer gateway deve essere aggiunto all'area di autenticazione di Kerberos e non può essere aggiunto a un dominio di Windows.

#### <a name="how-to-configure"></a>Come configurare:

**Nel computer del gateway:**

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

#### <a name="requirement"></a>Requisito:
*   Il computer del gateway deve essere aggiunto a un dominio di Windows.
*   È necessaria l'autorizzazione per aggiornare le impostazioni del controller di dominio.

#### <a name="how-to-configure"></a>Come configurare:

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

        ![Configurare i tipi di crittografia per Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Utilizzare il comando **Ksetup** per specificare l'algoritmo di crittografia da usare nell'area di autenticazione specifica.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Creare il mapping tra l'account di dominio e l'entità Kerberos per poter usare quest'ultima nel dominio di Windows.

    1. Avviare Strumenti di amministrazione > **Utenti e computer di Active Directory**.

    2. Configurare funzionalità avanzate facendo clic su **Visualizza** > **Funzionalità avanzate**.

    3. Individuare l'account per cui si desidera creare i mapping e fare clic con il tasto destor del mouse per visualizzare **Mapping nomi**. Fare clic sulla scheda **Nomi Kerberos**. 

    4. Aggiungere un'entità dall'area di autenticazione.

        ![Eseguire il mapping di sicurezza e identità](media/data-factory-hdfs-connector/map-security-identity.png)

**Nel computer del gateway:**

* Eseguire i comandi **Ksetup** seguenti per aggiungere una voce di area di autenticazione.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**In Azure Data Factory:**

* Configurare il connettore HDFS usando **l'autenticazione di Windows** con l'account di dominio o l'entità Kerberos per connettersi all'origine dati HDFS. Controllare la sezione [Proprietà del servizio collegato HDFS](#linked-service-properties) per i dettagli di configurazione.

> [!NOTE]
> Per eseguire il mapping dal set di dati di origine alle colonne del set di dati sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).


## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).
