---
title: "Guida alle prestazioni dell'attività di copia e all'ottimizzazione in Azure Data Factory | Microsoft Docs"
description: "Informazioni sui fattori principali che influiscono sulle prestazioni dello spostamento di dati in Azure Data Factory quando si usa l'attività di copia."
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 3f2b95e57e34905bf1128e9aee2862110a598f75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guida alle prestazioni dell'attività di copia e all'ottimizzazione
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-copy-activity-performance.md)
> * [Versione 2 - Anteprima](copy-activity-performance.md)


L'attività di copia di Azure Data Factory offre una soluzione di caricamento dei dati di primo livello in quanto a sicurezza, affidabilità e prestazioni. Consente di copiare decine di terabyte di dati ogni giorno in un'ampia gamma di archivi dati locali e cloud. Prestazioni di caricamento dei dati velocissime sono fondamentali per garantire di potersi concentrare sul problema centrale dei "big data": realizzare soluzioni avanzate di analisi e ricevere informazioni approfondite da tutti i dati.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sulle [prestazioni delle attività di copia in Data Factory versione 1](v1/data-factory-copy-activity-performance.md).

Azure fornisce un set di soluzioni di archiviazione dei dati e data warehouse di livello aziendale, e l'attività di copia offre un'esperienza di caricamento dei dati altamente ottimizzata, facile da configurare e impostare. Con un'unica attività di copia, è possibile ottenere:

* Caricamento dei dati in **Azure SQL Data Warehouse** a **1,2 GBps**.
* Caricamento dei dati nell'**Archiviazione BLOB di Azure** a **1 GBps**
* Caricamento dei dati in **Azure Data Lake Store** a **1 GBps**

L'articolo illustra:

* [I numeri di riferimento sulle prestazioni](#performance-reference) per gli archivi dati di origine e sink supportati per aiutare a pianificare il progetto;
* Funzionalità in grado di incrementare la velocità effettiva di copia in diversi scenari, tra cui [unità di spostamento dei dati cloud](#cloud-data-movement-units), [copia parallela](#parallel-copy) e [copia di staging](#staged-copy);
* [Indicazioni per l'ottimizzazione delle prestazioni](#performance-tuning-steps) che illustrano come ottimizzare le prestazioni e i fattori chiave che possono influire sulle prestazioni di copia.

> [!NOTE]
> Se non si ha familiarità con l'attività di copia in generale, vedere [Panoramica dell'attività di copia](copy-activity-overview.md) prima di continuare a leggere l'articolo.
>

## <a name="performance-reference"></a>Informazioni di riferimento sulle prestazioni

Come riferimento, la tabella sotto mostra la velocità effettiva di copia **in Mbps** per le coppie di origine e sink specifiche **nell'esecuzione di una singola attività di copia** in base a test interni. A scopo di confronto, viene illustrato anche in che modo le diverse impostazioni di [unità di spostamento dati cloud](#cloud-data-movement-units) o la [scalabilità del runtime di integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime) (nodi multipli) possono migliorare le prestazioni di copia.

![Matrice delle prestazioni](./media/copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>In Azure Data Factory versione 2, quando l'attività di copia viene eseguita in un runtime di integrazione di Azure, il numero minimo di unità di spostamento dati cloud è due.

Punti da notare:

* La velocità effettiva viene calcolata con la formula seguente: [dimensione dei dati letti dall'origine]/[durata dell'esecuzione dell'attività di copia].
* I numeri di riferimento delle prestazioni nella tabella sono stati misurati usando il set di dati [TPC-H](http://www.tpc.org/tpch/) nell'esecuzione di una singola attività di copia.
* Nel caso degli archivi dati di Azure, l'origine e il sink si trovano nella stessa area di Azure.
* Per la copia ibrida tra archivi dati locali e cloud, ogni nodo del runtime di integrazione self-hosted è in esecuzione su un computer separato dall'archivio dati, con le specifiche indicate di seguito. Durante l'esecuzione di una singola attività, l'operazione di copia ha usato solo una piccola parte della CPU, della memoria o della larghezza di banda di rete del computer di test.
    <table>
    <tr>
        <td>CPU</td>
        <td>Intel Xeon E5-2660 v2 da 32 core a 2,20 GHz</td>
    </tr>
    <tr>
        <td>Memoria</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Rete</td>
        <td>Interfaccia Internet: 10 Gbps, interfaccia Intranet: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> È possibile raggiungere una velocità effettiva più elevata usando un numero maggiore di unità di spostamento dati rispetto al numero massimo predefinito, pari a 32 per l'esecuzione di un'attività di copia da cloud a cloud. Ad esempio, con 100 unità di spostamento dati è possibile copiare i dati dal BLOB di Azure in Azure Data Lake Store a **1,0 GB al secondo**. Vedere la sezione [Unità di spostamento dati cloud](#cloud-data-movement-units) per informazioni dettagliate su questa funzionalità e sullo scenario supportato. Contattare il [Supporto tecnico di Azure](https://azure.microsoft.com/support/) per richiedere altre unità di spostamento dati.

## <a name="cloud-data-movement-units"></a>Unità di spostamento dati cloud

L' **unità di spostamento dati cloud** è una misura che rappresenta la potenza, ossia la combinazione tra CPU, memoria e allocazione di risorse di rete, di una singola unità in Data Factory. L'**unità di spostamento dati si applica solo al [runtime di integrazione di Azure](concepts-integration-runtime.md#azure-integration-runtime)** ma non al [runtime di integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime).

**Il numero minimo di unità di spostamento dati cloud per ottimizzare l'esecuzione dell'attività di copia è due.** Nella tabella seguente sono elencate le unità di spostamento dati predefinite usate in diversi scenari di copia.

| Scenario di copia | Numero di unità di spostamento dati predefinite determinato dal servizio |
|:--- |:--- |
| Copiare dati tra archivi basati su file | Tra 2 e 16 in base al numero e alle dimensioni dei file. |
| Copiare i dati da Salesforce/Dynamics | 4 |
| Tutti gli altri scenari di copia | 2 |

Per ignorare l'impostazione predefinita, è possibile specificare un valore per la proprietà **cloudDataMovementUnits** procedendo come segue. I **valori consentiti** per la proprietà **cloudDataMovementUnits** sono 2, 4, 8, 16, 32. Il **numero effettivo di unità di spostamento dati cloud** usate dall'operazione di copia in fase di esecuzione è minore o uguale al valore configurato, a seconda del modello di dati. Per informazioni sul livello di miglioramento delle prestazioni che è possibile ottenere quando si configurano più unità per un sink e un'origine della copia specifici, vedere la sezione [Informazioni di riferimento sulle prestazioni](#performance-reference).

Quando si monitora un'esecuzione attività, è possibile visualizzare le unità di spostamento dati cloud effettivamente usate per ogni esecuzione della copia nell'output dell'attività di copia. Vedere [Monitoraggio dell'attività di copia](copy-activity-overview.md#monitoring).

> [!NOTE]
> Se sono necessarie più unità di spostamento dati cloud per aumentare la velocità effettiva, contattare il [supporto di Azure](https://azure.microsoft.com/support/). Attualmente è possibile impostare la proprietà su valori maggiori o uguali a 8 soltanto per la **copia di più file da Archiviazione BLOB, Data Lake Store, Amazon S3, FTP cloud o SFTP cloud in qualsiasi altro archivio dati cloud**.
>

**Esempio:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

### <a name="cloud-data-movement-units-billing-impact"></a>Impatto sulla fatturazione delle unità di spostamento dati cloud

È **importante** ricordare che l'addebito è basato sul tempo totale impiegato per l'operazione di copia. La durata totale fatturata per lo spostamento dati equivale alla somma della durata di tutte le unità di spostamento dati. Se un processo di copia impiegava un'ora con due unità cloud e ora richiede 15 minuti con otto unità cloud, la fattura complessiva rimane pressoché identica.

## <a name="parallel-copy"></a>Copia parallela

È possibile usare la proprietà **parallelCopies** per indicare il parallelismo che l'attività di copia deve usare. Questa proprietà può essere considerata come il numero massimo di thread all'interno dell'attività di copia che possono leggere dall'origine o scrivere negli archivi dati sink in parallelo.

Per ogni esecuzione dell'attività di copia, Data Factory determina il numero di copie parallele da usare per copiare i dati dall'archivio dati di origine all'archivio dati di destinazione. Il numero predefinito di copie parallele usate dipende dal tipo di origine e dal tipo di sink usati:

| Scenario di copia | Numero predefinito di copie parallele determinato dal servizio |
| --- | --- |
| Copiare dati tra archivi basati su file |Tra 1 e 32. Dipende dalle dimensioni dei file e dal numero di unità di spostamento dati cloud usate per copiare dati tra due archivi dati cloud oppure dalla configurazione fisica del computer del runtime di integrazione self-hosted. |
| Copiare i dati da qualsiasi archivio dati di origine in un'archiviazione tabelle di Azure |4 |
| Tutti gli altri scenari di copia |1 |

In genere, il comportamento predefinito dovrebbe garantire la velocità effettiva migliore. Tuttavia, per controllare il carico sui computer che ospitano gli archivi dati o per ottimizzare le prestazioni di copia, è possibile scegliere di ignorare il valore predefinito e specificare un valore per la proprietà **parallelCopies** . Il valore deve essere un numero intero maggiore o uguale a 1. Per garantire prestazioni ottimali in fase di esecuzione, l'attività di copia usa un valore minore o uguale al valore configurato.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

Punti da notare:

* Quando si copiano i dati tra archivi basati su file, **parallelCopies** determina il parallelismo a livello di file. La suddivisione in blocchi all'interno di un singolo file si verificherebbe sottotraccia in modo automatico e trasparente; tale operazione è pensata per usare la dimensione di blocco più appropriata per un tipo di archivio dati di origine specificato al fine di caricare i dati in maniera parallela e ortogonale a parallelCopies. Il numero effettivo di copie parallele usate dal servizio di spostamento dati per l'operazione di copia in fase di esecuzione non è maggiore del numero di file disponibili. Se il comportamento di copia è **mergeFile**, l'attività di copia non può usare il parallelismo a livello di file.
* Quando si specifica un valore per la proprietà **parallelCopies**, prendere in considerazione l'aumento del carico per gli archivi dati sink e di origine e per il runtime di integrazione self-hosted se l'attività di copia ne viene ottimizzata, ad esempio se si tratta di una copia ibrida. Questo avviene soprattutto quando ci sono più attività o esecuzioni simultanee delle stesse attività che vengono eseguite con lo stesso archivio dati. Se si nota un sovraccarico dell'archivio dati o del runtime di integrazione self-hosted, diminuire il valore di **parallelCopies** per alleggerirlo.
* Quando si copiano dati da archivi non basati su file in archivi basati su file, il servizio di spostamento dati ignora la proprietà **parallelCopies** . Anche se viene specificato, in questo caso il parallelismo non viene applicato.
* Il valore **parallelCopies** è ortogonale a **cloudDataMovementUnits**. Il primo viene conteggiato tra tutte le unità di spostamento dati sul cloud.

## <a name="staged-copy"></a>copia di staging

Quando si copiano dati da un archivio dati di origine a un archivio dati sink, è possibile scegliere di usare un archivio BLOB come archivio di staging provvisorio. La funzionalità di staging è particolarmente utile nei casi seguenti:

- **Si inseriscono dati da vari archivi dati in SQL Data Warehouse tramite PolyBase**. SQL Data Warehouse fa uso di PolyBase come meccanismo a velocità effettiva elevata per il caricamento di grandi quantità di dati in SQL Data Warehouse. Tuttavia, i dati di origine devono trovarsi in Archiviazione BLOB o Azure Data Lake Store e devono soddisfare criteri aggiuntivi. Quando si caricano dati da un archivio dati non BLOB o Azure Data Lake Store, è possibile attivare la copia di dati tramite un'archiviazione BLOB di staging provvisoria. In tal caso, Data Factory esegue le trasformazioni di dati necessarie per garantire che vengano soddisfatti i requisiti di PolyBase. Quindi usa PolyBase per caricare in modo efficiente i dati in SQL Data Warehouse. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **A volte occorre tempo per eseguire uno spostamento dati ibrido, ovvero la copia da un archivio dati locale a un archivio dati cloud, su una connessione di rete lenta**. Per migliorare le prestazioni, è possibile usare la copia di staging per comprimere i dati in locale in modo che sia necessario meno tempo per spostare i dati nell'archivio dati di staging nel cloud e decomprimere i dati nell'archivio di staging prima di caricarli nell'archivio dati di destinazione.
- **Non si vuole aprire porte diverse dalla porta 80 e dalla porta 443 nel firewall, a causa dei criteri IT aziendali**. Ad esempio, quando si copiano dati da un archivio dati locale a un sink del database SQL di Azure o un sink di Azure SQL Data Warehouse, è necessario attivare le comunicazioni TCP in uscita sulla porta 1433 per Windows Firewall e per il firewall aziendale. In questo scenario, la copia di staging sfrutta il runtime di integrazione self-hosted prima di tutto per copiare i dati in un'istanza di staging dell'archiviazione BLOB tramite HTTP o HTTPS sulla porta 443 e poi per caricare i dati in un database SQL o in un SQL Data Warehouse dall'istanza di staging dell'archiviazione BLOB. In questo flusso non è necessario abilitare la porta 1433.

### <a name="how-staged-copy-works"></a>Come funziona la copia di staging

Quando si attiva la funzionalità di staging, i dati vengono prima copiati dall'archivio dati di origine nell'archiviazione BLOB di staging personale. Successivamente, vengono copiati dall'archivio dati di staging nell'archivio dati sink. Data Factory gestisce automaticamente il flusso in due fasi ed elimina i dati temporanei dall'archivio di staging al termine dello spostamento dati.

![copia di staging](media/copy-activity-performance/staged-copy.png)

Quando si attiva lo spostamento dei dati usando un archivio di staging, è possibile specificare se i dati devono essere compressi prima dello spostamento dall'archivio dati di origine all'archivio dati provvisorio o di staging e poi decompressi prima dello spostamento dall'archivio dati provvisorio o di staging all'archivio dati sink.

Attualmente non è possibile copiare dati tra due archivi dati locali usando un archivio di staging.

### <a name="configuration"></a>Configurazione

Configurare l'impostazione **enableStaging** nell'attività di copia per specificare se i dati devono essere inseriti in un archivio BLOB di Azure di staging prima del caricamento in un archivio dati di destinazione. Se si imposta **enableStaging** su `TRUE`, specificare le proprietà aggiuntive elencate nella tabella seguente. Se non è già disponibile, è necessario creare un servizio collegato alla firma di accesso condiviso di archiviazione o di Archiviazione di Azure per lo staging.

| Proprietà | Descrizione | Valore predefinito | Obbligatorio |
| --- | --- | --- | --- |
| **enableStaging** |Specificare se si vuole copiare i dati tramite un archivio di staging provvisorio. |False |No |
| **linkedServiceName** |Specificare il nome di un servizio collegato [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) che fa riferimento all'istanza di archiviazione usata come archivio di staging provvisorio. <br/><br/> L'archiviazione non può essere usata con una firma di accesso condiviso per caricare dati in SQL Data Warehouse tramite PolyBase. Può essere usata in tutti gli altri scenari. |N/D |Sì, quando **enableStaging** è impostato su TRUE |
| **path** |Specificare il percorso dell'archivio BLOB che deve contenere i dati di staging. Se non si specifica un percorso, il servizio crea un contenitore in cui archiviare i dati temporanei. <br/><br/> Specificare un percorso solo se si usa l'archiviazione con una firma di accesso condiviso o se i dati temporanei devono trovarsi in un percorso specifico. |N/D |No |
| **enableCompression** |Specifica se è necessario comprimere i dati prima di copiarli nella destinazione. Questa impostazione ridurre il volume dei dati da trasferire. |False |No |

Di seguito è riportata una definizione di esempio di attività di copia con le proprietà descritte nella tabella precedente:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Impatto della fatturazione della copia di staging

I costi addebitati si basano su due passaggi: durata della copia e tipo di copia.

* Quando si usa la funzionalità di staging durante una copia nel cloud, ovvero la copia di dati da un archivio dati cloud a un altro archivio dati cloud con l'ottimizzazione del runtime di integrazione di Azure, il costo addebitato sarà [somma della durata della copia per i passaggi 1 e 2] x [prezzo unitario della copia nel cloud].
* Quando si usa la funzionalità di staging durante una copia ibrida, ovvero la copia di dati da un archivio dati locale a un archivio dati cloud e solo uno beneficia dell'ottimizzazione del runtime di integrazione di Azure, il costo addebitato sarà [durata della copia ibrida] x [prezzo unitario della copia ibrida] + [durata della copia nel cloud] x [prezzo unitario della copia nel cloud].

## <a name="performance-tuning-steps"></a>Procedura di ottimizzazione delle prestazioni

Per ottimizzare le prestazioni del servizio Data Factory con l'attività di copia, è consigliabile seguire questa procedura:

1. **Stabilire una baseline**. Durante la fase di sviluppo, testare la pipeline usando l'attività di copia su un campione di dati rappresentativo. Raccogliere i dettagli dell'esecuzione e le caratteristiche relative alle prestazioni usando il [monitoraggio dell'attività di copia](copy-activity-overview.md#monitoring).

2. **Diagnosticare e ottimizzare le prestazioni**. Se le prestazioni osservate non soddisfano le aspettative, è necessario identificare gli eventuali colli di bottiglia e quindi ottimizzare le prestazioni per rimuovere o ridurre l'effetto dei colli di bottiglia. Una descrizione completa della diagnosi delle prestazioni non rientra nell'ambito di questo articolo, ma di seguito sono riportate alcune considerazioni comuni:

   * Funzionalità per le prestazioni:
     * [Copia parallela](#parallel-copy)
     * [Unità di spostamento dati cloud](#cloud-data-movement-units)
     * [Copia di staging](#staged-copy)
     * [Scalabilità del runtime di integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Runtime di integrazione self-hosted](#considerations-for-self-hosted-integration-runtime)
   * [Origine](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization)
   * [Compressione](#considerations-for-compression)
   * [Mapping di colonne](#considerations-for-column-mapping)
   * [Altre considerazioni](#other-considerations)

3. **Espandere la configurazione all'intero set di dati**. Dopo aver ottenuto prestazioni e risultati di esecuzione soddisfacenti, è possibile espandere la definizione e la pipeline per coprire l'intero set di dati.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Considerazioni sul runtime di integrazione self-hosted

Se l'attività di copia viene eseguita in un runtime di integrazione self-hosted, tenere presente quanto segue:

**Configurazione**: è consigliabile usare un computer dedicato per ospitare il runtime di integrazione. Vedere [Considerazioni sull'uso del runtime di integrazione self-hosted](concepts-integration-runtime.md).

**Scalabilità orizzontale**: un singolo runtime di integrazione self-hosted logico con uno o più nodi può consentire più esecuzioni di attività di copia contemporaneamente. Se si hanno necessità complesse di spostamento di dati ibridi, con un numero elevato di esecuzioni di attività di copia simultanee o con un volume elevato di dati da copiare, prendere in considerazione la possibilità di [scalare orizzontalmente il runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md#high-availability-and-scalability) in modo da utilizzare al meglio la risorsa o effettuare il provisioning di più risorse a supporto della copia.

## <a name="considerations-for-the-source"></a>Considerazioni sull'origine

### <a name="general"></a>Generale

Assicurarsi che l'archivio dati sottostante non sia sovraccarico a causa di altri carichi di lavoro in esecuzione in o su di esso.

Per gli archivi dati Microsoft, vedere gli [argomenti sul monitoraggio e l'ottimizzazione](#performance-reference) specifici degli archivi dati, per comprendere meglio le caratteristiche delle prestazioni degli archivi dati e come ridurre al minimo i tempi di risposta e ottimizzare la velocità effettiva.

* Se si copiano dati **da Archiviazione BLOB a SQL Data Warehouse**, valutare l'uso di **PolyBase** per migliorare le prestazioni. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) .
* Se si copiano dati **da HDFS a un archivio BLOB di Azure o Azure Data Lake Store**, valutare l'uso di **DistCp** per migliorare le prestazioni. Per altre informazioni, vedere [Usare DistCp per copiare dati da HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se si copiano dati **da Redshift a un archivio Azure SQL Data Warehouse, BLOB di Azure o Azure Data Lake Store**, valutare l'uso di **UNLOAD** per migliorare le prestazioni. Per informazioni dettagliate, vedere [Usare UNLOAD per copiare i dati da Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Archivi dati basati su file

* **Dimensioni medie dei file e numero medio di file**: l'attività di copia trasferisce i dati procedendo un file alla volta. Con la stessa quantità di dati da spostare, la velocità effettiva generale risulta inferiore se i dati sono costituiti da un numero elevato di file piccoli anziché da pochi file di grandi dimensioni. Ciò è dovuto alla fase di bootstrap necessaria per ogni file. Se possibile, combinare quindi i file piccoli in file più grandi per raggiungere una maggiore velocità effettiva.
* **Formato di file e compressione**: per altre informazioni su come migliorare le prestazioni, vedere le sezioni [Considerazioni sulla serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization) e [Considerazioni sulla compressione](#considerations-for-compression).

### <a name="relational-data-stores"></a>Archivi dati relazionali

* **Modello di dati**: lo schema di tabella influisce sulla velocità effettiva di copia. Una riga di grandi dimensioni offre migliori prestazioni rispetto a una riga di piccole dimensioni per copiare la stessa quantità di dati. Questo perché il database è in grado di recuperare in modo più efficiente un minor numero di batch di dati che contengono meno righe.
* **Query o stored procedure**: ottimizzare la logica della query o della stored procedure specificata nell'origine dell'attività di copia per recuperare i dati in modo più efficiente.

## <a name="considerations-for-the-sink"></a>Considerazioni sul sink

### <a name="general"></a>Generale

Assicurarsi che l'archivio dati sottostante non sia sovraccarico a causa di altri carichi di lavoro in esecuzione in o su di esso.

Per gli archivi dati Microsoft, vedere gli [argomenti sul monitoraggio e l'ottimizzazione](#performance-reference) specifici per gli archivi dati, per comprendere meglio le caratteristiche delle prestazioni degli archivi dati e come ridurre al minimo i tempi di risposta e ottimizzare la velocità effettiva.

* Se si copiano dati **da Archiviazione BLOB a SQL Data Warehouse**, valutare l'uso di **PolyBase** per migliorare le prestazioni. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) .
* Se si copiano dati **da HDFS a un archivio BLOB di Azure o Azure Data Lake Store**, valutare l'uso di **DistCp** per migliorare le prestazioni. Per altre informazioni, vedere [Usare DistCp per copiare dati da HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se si copiano dati **da Redshift a un archivio Azure SQL Data Warehouse, BLOB di Azure o Azure Data Lake Store**, valutare l'uso di **UNLOAD** per migliorare le prestazioni. Per informazioni dettagliate, vedere [Usare UNLOAD per copiare i dati da Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Archivi dati basati su file

* **Comportamento di copia**: se si copiano dati da un diverso archivio dati basato su file, l'attività di copia fornisce tre tipi di comportamento tramite la proprietà **copyBehavior**, mantenere la gerarchia, rendere flat la gerarchia e unire i file. Conservare o rendere flat la gerarchia comporta un overhead delle prestazioni minimo, mentre unire i file provoca un aumento dell'overhead delle prestazioni.
* **Formato di file e compressione**: per altre informazioni su come migliorare le prestazioni, vedere le sezioni [Considerazioni sulla serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization) e [Considerazioni sulla compressione](#considerations-for-compression).

### <a name="relational-data-stores"></a>Archivi dati relazionali

* **Comportamento di copia**: a seconda delle proprietà configurate per **sqlSink**, l'attività di copia scrive i dati nel database di destinazione in modi diversi.
  * Per impostazione predefinita, il servizio di spostamento dati usa l'API per la copia bulk per inserire i dati in modalità Append, che offre le prestazioni migliori.
  * Se si configura una stored procedure nel sink, il database applica i dati una riga alla volta anziché come caricamento bulk e le prestazioni ne risentono notevolmente. Se il set di dati è di grandi dimensioni, valutare l'opportunità di passare a usare la proprietà **preCopyScript**, se applicabile.
  * Se si configura la proprietà **preCopyScript** per ogni esecuzione dell'attività di copia, il servizio attiva lo script e quindi usa l'API per la copia bulk per inserire i dati. Ad esempio, per sovrascrivere l'intera tabella con i dati più recenti, è possibile specificare uno script per eliminare tutti i record prima del caricamento bulk dei nuovi dati dall'origine.
* **Modello di dati e dimensioni batch**:
  * Lo schema di tabella influisce sulla velocità effettiva di copia. Per copiare la stessa quantità di dati, dimensioni di riga grandi offrono prestazioni migliori rispetto a dimensioni di riga piccole, perché il database può eseguire in modo più efficiente il commit di un numero inferiore di batch di dati.
  * L'attività di copia inserisce i dati in una serie di batch. Per impostare il numero di righe in un batch è possibile usare la proprietà **writeBatchSize** . Se le righe dei dati sono di piccole dimensioni, è possibile impostare la proprietà **writeBatchSize** con un valore più elevato per sfruttare l'overhead di un numero minore di batch e aumentare la velocità effettiva. Se le righe sono di grandi dimensioni, prestare attenzione quando si aumenta il valore di **writeBatchSize**. Un valore elevato può causare un errore di copia dovuto a un sovraccarico del database.

### <a name="nosql-stores"></a>Archivi NoSQL

* Per gli **archivi tabelle**:
  * **Partizione**: scrivere i dati nelle partizioni con interleave riduce drasticamente le prestazioni. Ordinare i dati di origine per chiave di partizione in modo che i dati vengano inseriti in modo efficiente in una partizione dopo l'altra, oppure è possibile modificare la logica e scrivere i dati in una sola partizione.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerazioni sulla serializzazione e deserializzazione

Serializzazione e deserializzazione possono verificarsi quando il set di dati di input o il set di dati di output è costituito da un file. Per informazioni dettagliate sui formati di file supportati dall'attività di copia vedere [File supportati e formati di compressione](supported-file-formats-and-compression-codecs.md).

**Comportamento di copia**:

* Copia di file tra archivi dati basati su file:
  * Quando i set di dati di input e di output hanno le stesse impostazioni del formato di file o non hanno tali impostazioni, il servizio di spostamento dati esegue una **copia binaria** senza alcuna serializzazione o deserializzazione. La velocità effettiva è superiore rispetto allo scenario, in cui le impostazioni del formato di file di origine e del sink sono diverse tra loro.
  * Quando i set di dati di input e di output sono entrambi in formato testo e solo il tipo di codifica è diverso, il servizio di spostamento esegue solo la conversione della codifica. Non esegue alcuna operazione di serializzazione o deserializzazione e questo dà luogo a un certo overhead delle prestazioni rispetto alla copia binaria.
  * Quando i set di dati di input e di output hanno entrambi formati di file diversi o configurazioni diverse, ad esempio i delimitatori, il servizio di spostamento dati deserializza i dati di origine per trasmetterli, trasformarli e quindi serializzarli nel formato di output indicato. Questa operazione comporta un overhead delle prestazioni decisamente maggiore rispetto ad altri scenari.
* Quando si copiano file da e in un archivio dati che non è basato su file, ad esempio da un archivio basato su file in un archivio relazionale, il passaggio di serializzazione o deserializzazione è necessario. Questo passaggio comporta un notevole overhead delle prestazioni.

**Formato di file**: il formato di file scelto può influire sulle prestazioni di copia. Ad esempio, Avro è un formato binario compresso che archivia i metadati con i dati. È ampiamente supportato nell'ecosistema di Hadoop per l'elaborazione e l'esecuzione di query. Avro è tuttavia più costoso a livello di serializzazione e deserializzazione e di conseguenza la velocità effettiva di copia risulta inferiore rispetto al formato testo. Scegliere il formato di file per tutto il flusso di elaborazione a livello globale. Considerare prima di tutto il formato in cui i dati vengono salvati nell'archivio dati di origine o devono essere estratti dai sistemi esterni, il formato migliore per l'archiviazione, l'elaborazione analitica e le query e il formato in cui i dati devono essere esportati nei data mart per gli strumenti di creazione di report e visualizzazione. A volte un formato di file non ottimale dal punto di vista delle prestazioni di lettura e scrittura può invece essere una buona scelta dal punto di vista del processo analitico generale.

## <a name="considerations-for-compression"></a>Considerazioni sulla compressione

Quando il set di dati di input o di output è un file, è possibile impostare l'attività di copia per l'esecuzione della compressione o decompressione durante la scrittura dei dati nella destinazione. La scelta della compressione comporta un compromesso tra input/output (I/O) e CPU. La compressione dei dati ha un costo maggiore in termini di risorse di calcolo, ma riduce l'I/O di rete e l'archiviazione. A seconda dei dati, si potrebbe riscontrare un aumento della velocità effettiva di copia complessiva.

**Codec**: ogni codec di compressione presenta dei vantaggi. Ad esempio, bzip2 ha la velocità effettiva copia più bassa, ma offre prestazioni di query Hive migliori perché permette di dividerle per l'elaborazione. Gzip è l'opzione più bilanciata e quella usata più di frequente. Scegliere il codec più adatto allo scenario end-to-end personalizzato.

**Livello**: per ogni codec di compressione è possibile scegliere tra due opzioni, la compressione più veloce e la compressione ottimale. L'opzione di compressione più veloce comprime i dati il più rapidamente possibile, anche se il file risultante non viene compresso in modo ottimale. L'opzione di compressione ottimale impiega più tempo per la compressione e restituisce una quantità minima di dati. È possibile testare entrambe le opzioni per verificare quale offra le migliori prestazioni complessive in base alle proprie esigenze.

**Una considerazione**: per copiare una grande quantità di dati tra un archivio locale e il cloud, è consigliabile usare una [copia di staging](#staged-copy) con compressione abilitata. L'uso di un archivio provvisorio risulta utile quando la larghezza di banda della rete aziendale e i servizi di Azure pongono dei limiti e i set di dati di input e di output devono essere in formato non compresso.

## <a name="considerations-for-column-mapping"></a>Considerazioni sul mapping di colonne

È possibile impostare la proprietà **columnMappings** nell'attività di copia perché venga eseguito il mapping di tutte o di un subset delle colonne di input alle colonne di output. Dopo aver letto i dati dall'origine, il servizio di spostamento dati deve eseguire il mapping delle colonne sui dati prima di scriverli nel sink. Questa ulteriore elaborazione riduce la velocità effettiva di copia.

Se l'archivio dati di origine è disponibile per query, ad esempio nel caso di un archivio relazionale come il database SQL o SQL Server, oppure nel caso di un archivio NoSQL come un archivio tabelle o Azure Cosmos DB, è consigliabile eseguire il push della logica di filtro e riordinamento colonne per la proprietà **query** anziché usare il mapping colonne. In questo modo la proiezione si verifica quando il servizio di spostamento dati legge i dati dall'archivio dati di origine, in cui è molto più efficiente.

Informazioni sul [mapping dello schema dell'attività di copia](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Altre considerazioni

Se i dati da copiare sono di grandi dimensioni, è possibile modificare la logica di business per partizionare ulteriormente i dati e pianificare l'attività di copia in modo che venga eseguita più frequentemente per ridurre le dimensioni dei dati per ogni esecuzione dell'attività di copia.

Prestare attenzione al numero di set di dati e di attività di copia che richiedono la connessione di Data Factory allo stesso archivio dati nello stesso momento. Molti processi di copia simultanei possono limitare un archivio dati e causare un peggioramento delle prestazioni, nuovi tentativi interni dei processi di copia e, in alcuni casi, errori di esecuzione.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Scenario di esempio: copiare da un'istanza locale di SQL Server in un archivio BLOB

**Scenario**: viene compilata una pipeline per copiare dati da un'istanza locale di SQL Server in un archivio BLOB in formato CSV. Per velocizzare il processo di copia, i file CSV devono essere compressi in formato bzip2.

**Test e analisi**: la velocità effettiva dell'attività di copia è inferiore a 2 Mbps ed è molto inferiore al benchmark delle prestazioni.

**Analisi e ottimizzazione delle prestazioni**: per risolvere il problema delle prestazioni, occorre vedere prima di tutto come vengono elaborati e spostati i dati.

1. **Lettura dei dati**: il runtime di integrazione apre una connessione a SQL Server e invia la query. SQL Server risponde inviando il flusso di dati al runtime di integrazione tramite Intranet.
2. **Serializzazione e compressione dei dati**: il runtime di integrazione serializza il flusso dei dati in formato CSV e comprime i dati in un flusso bzip2.
3. **Scrittura dei dati**: il runtime di integrazione carica il flusso bzip2 in Archiviazione BLOB tramite Internet.

Come si può notare, i dati vengono elaborati e spostati in base a un flusso sequenziale: SQL Server > LAN > Runtime di integrazione > WAN > Archiviazione BLOB. **Le prestazioni complessive vengono controllate dalla velocità effettiva minima in tutta la pipeline**.

![Flusso di dati](./media/copy-activity-performance/case-study-pic-1.png)

Uno o più dei fattori seguenti possono provocare un collo di bottiglia nelle prestazioni:

* **Origine**: SQL Server ha di per sé una velocità effettiva bassa a causa dei carichi elevati.
* **Runtime di integrazione self-hosted**:
  * **LAN**: il runtime di integrazione è distante da SQL Server con una connessione a larghezza di banda bassa.
  * **Runtime di integrazione**: il runtime di integrazione ha raggiunto i relativi limiti di carico per eseguire queste operazioni:
    * **Serializzazione**: la serializzazione del flusso di dati in formato CSV ha una velocità effettiva bassa.
    * **Compressione**: si è scelto un codec di compressione lenta, ad esempio bzip2 a 2,8 Mbps con core i7.
  * **WAN**: la larghezza di banda tra la rete aziendale e i servizi di Azure è bassa, ad esempio T1 = 1.544 Kbps e T2 = 6.312 Kbps.
* **Sink**: l'archivio BLOB ha una velocità effettiva bassa. Questo scenario è poco probabile perché il relativo contratto di servizio garantisce un minimo di 60 Mbps.

In tal caso, la compressione dati bzip2 potrebbe rallentare l'intera pipeline. Il passaggio al codec di compressione gzip può ridurre questo collo di bottiglia.

## <a name="reference"></a>riferimento

Di seguito sono riportati alcuni riferimenti sul monitoraggio e l'ottimizzazione delle prestazioni per alcuni degli archivi dati supportati:

* Archiviazione di Azure, inclusi archivi BLOB e archivi tabelle: [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../storage/common/storage-scalability-targets.md) ed [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../storage/common/storage-performance-checklist.md)
* Database SQL di Azure: è possibile [monitorare le prestazioni](../sql-database/sql-database-single-database-monitor.md) e controllare la percentuale di DTU (Database Transaction Unit).
* Azure SQL Data Warehouse: la funzionalità viene misurata in unità data warehouse (DWU). Vedere in proposito [Gestire la potenza di calcolo in Azure SQL Data Warehouse (Panoramica)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [livelli di prestazioni in Azure Cosmos DB](../cosmos-db/performance-levels.md)
* SQL Server locale: [Monitoraggio e ottimizzazione delle prestazioni](https://msdn.microsoft.com/library/ms189081.aspx)
* File server locale: [Performance Tuning for File Servers](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping dello schema dell'attività di copia](copy-activity-schema-and-type-mapping.md)
- [Tolleranza di errore dell'attività di copia](copy-activity-fault-tolerance.md)
