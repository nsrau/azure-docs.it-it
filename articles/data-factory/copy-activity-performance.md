---
title: Copia attività Guida alle prestazioni e ottimizzazione in Azure Data Factory | Microsoft Docs
description: Informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati in Azure Data Factory quando si usa l'attività di copia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: 3ea89e9f6a6bb8a4c377c70bbe1b5540d3b74d44
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341238"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Copiare attività Guida alle prestazioni e ottimizzazione
> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in cui in uso:"]
> * [Versione 1](v1/data-factory-copy-activity-performance.md)
> * [Versione corrente](copy-activity-performance.md)


L'attività di copia di Azure Data Factory offre un'eccellente protetto, affidabili e ad alte prestazioni caricamento dati soluzione. È possibile utilizzarlo per copiare decine di terabyte di dati ogni giorno in un'ampia gamma di archivi dati cloud e locali. Rapido caricamento dei dati delle prestazioni sono fondamentali per garantire che è possibile concentrarsi sul problema principali dei big data: creazione di soluzioni avanzate di analitica e ricevere informazioni approfondite da tutti i dati.

Azure offre un set di livello aziendale di soluzioni di data archiviazione e data warehouse. L'attività di copia offre un'esperienza semplice da configurare e impostare di caricamento di dati altamente ottimizzati. Con una singola attività di copia, è possibile caricare i dati in:

* Azure SQL Data Warehouse a 1,2 GBps.
* Archiviazione Blob di Azure a 1,0 GBps.
* Azure Data Lake Store a 1,0 GBps.

L'articolo illustra:

* [Numeri di riferimento relativi alle prestazioni](#performance-reference) per gli archivi di dati di origine e sink che consentono di pianificare il progetto è supportato.
* Le funzionalità che possono migliorare la velocità effettiva di copia in diversi scenari, che include [integrazione di Data Unit](#data-integration-units) (DIUs), [copia parallela](#parallel-copy), e [copia di staging](#staged-copy).
* [Indicazioni di ottimizzazione delle prestazioni](#performance-tuning-steps) su come ottimizzare le prestazioni e i fattori chiave che possono influire sulle prestazioni di copia.

> [!NOTE]
> Se non si ha familiarità con l'attività di copia in generale, vedere la [panoramica dell'attività di copia](copy-activity-overview.md) prima di leggere questo articolo.
>

## <a name="performance-reference"></a>Informazioni di riferimento sulle prestazioni

Come riferimento, nella tabella seguente mostra la velocità effettiva di copia in MBps per l'origine specificata e coppie di sink nell'esecuzione di un'attività di copia singola basato su test interni. Per il confronto, viene inoltre illustrato come diverse impostazioni dei [integrazione di Data Unit](#data-integration-units) oppure [self-hosted scalabilità del runtime di integrazione](concepts-integration-runtime.md#self-hosted-integration-runtime) (nodi multipli) possono migliorare le prestazioni di copia.

![Matrice delle prestazioni](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Quando l'attività di copia viene eseguita in un runtime di integrazione di Azure, le unità di integrazione di dati consentiti minimo (precedentemente noto come unità di spostamento dati) è due. Se non specificato, vedere le unità di integrazione dei dati predefinite usate nel [unità di integrazione dati](#data-integration-units).

**Punti da notare:**

* Velocità effettiva viene calcolata utilizzando la formula seguente: [dimensione dei dati letti dall'origine] / [durata dell'esecuzione di attività di copia].
* I numeri di riferimento prestazioni nella tabella sono stati misurati usando un [TPC-H](http://www.tpc.org/tpch/) set di dati in un'attività di copia singola esecuzione. File di test per gli archivi basati su file sono più file con 10 GB di dimensioni.
* Nel caso degli archivi dati di Azure, l'origine e il sink si trovano nella stessa area di Azure.
* Per la copia ibrida tra origini locali e cloud gli archivi dati, ogni nodo del runtime di integrazione self-hosted era in esecuzione in un computer separato dall'archivio dati, con la specifica seguente. Durante l'esecuzione di una singola attività, l'operazione di copia ha usato solo una piccola parte della CPU, della memoria o della larghezza di banda di rete del computer di test.
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
> È possibile ottenere una velocità effettiva usando DIUs altre. Con 100 DIUs, ad esempio, è possibile copiare dati da archiviazione Blob di Azure in Azure Data Lake Store a 1,0 GBps. Per altre informazioni su questa funzionalità e sullo scenario supportato, vedere la [unità di integrazione dati](#data-integration-units) sezione. 

## <a name="data-integration-units"></a>Unità di integrazione dati

Un'unità di integrazione dei dati è una misura che rappresenta la potenza (una combinazione di CPU, memoria e allocazione di risorse di rete) di una singola unità in Azure Data Factory. Integrazione di Data Unit si applica solo ai [runtime di integrazione di Azure](concepts-integration-runtime.md#azure-integration-runtime), ma non [runtime di integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime).

Il minimo DIUs per consentire l'esecuzione di un'attività di copia è due. Se non specificato, nella tabella seguente sono elencate le unità di integrazione dati predefinite usate in diversi scenari di copia:

| Scenario di copia | Numero di unità di integrazione dati predefinite determinato dal servizio |
|:--- |:--- |
| Copiare dati tra archivi basati su file | Tra 4 e 32 a seconda del numero e dimensioni dei file |
| Tutti gli altri scenari di copia | 4 |

Per ignorare l'impostazione predefinita, è possibile specificare un valore per la proprietà **dataIntegrationUnits** procedendo come segue. Il *i valori consentiti* per il **dataIntegrationUnits** proprietà è fino a 256. Il *numero effettivo di unità di integrazione dati* usate dall'operazione di copia in fase di esecuzione è minore o uguale al valore configurato, a seconda del modello di dati. Per informazioni sul livello di miglioramento delle prestazioni che è possibile ottenere quando si configurano più unità per un sink e un'origine della copia specifici, vedere la sezione [Informazioni di riferimento sulle prestazioni](#performance-reference).

È possibile visualizzare il DIUs usato per ogni esecuzione della copia nell'output di attività di copia quando si monitora un'esecuzione di attività. Per altre informazioni, vedere [Monitoraggio attività di copia](copy-activity-overview.md#monitoring).

> [!NOTE]
> Impostazione di DIUs maggiori quattro attualmente si applica solo quando si copiano più file da archiviazione di Azure, Azure Data Lake Store, Amazon S3, Google Cloud Storage, cloud FTP o SFTP da cloud a qualsiasi altro archivio dati cloud.
>

**Esempio**

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
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="data-integration-units-billing-impact"></a>Impatto sulla fatturazione delle unità di integrazione dati

Tenere presente che addebiti sono basati sul tempo totale dell'operazione di copia. La durata totale che costi vengono addebitati per lo spostamento dei dati è la somma della durata tra DIUs. Se un processo di copia impiegava un'ora con due unità cloud e ora richiede 15 minuti con otto unità cloud, la fattura complessiva rimane pressoché identica.

## <a name="parallel-copy"></a>Copia parallela

È possibile usare la **parallelCopies** proprietà per indicare il parallelismo che l'attività di copia da utilizzare. È possibile considerare questa proprietà come il numero massimo di thread all'interno dell'attività di copia di lettura dall'origine o la scrittura agli archivi dati sink in parallelo.

Per ogni esecuzione attività di copia, Data Factory di Azure determina il numero di copie parallele da usare per copiare dati dall'origine archivio dati e per i dati di destinazione di archiviazione. Il numero predefinito di copie parallele usate dipende dal tipo di origine e sink in uso.

| Scenario di copia | Numero predefinito di copie parallele determinato dal servizio |
| --- | --- |
| Copiare dati tra archivi basati su file |Dipende dalle dimensioni dei file e il numero di DIUs utilizzate per copiare dati tra due archivi dati cloud oppure dalla configurazione fisica del computer del runtime di integrazione self-hosted. |
| Copiare dati da qualsiasi archivio dati di origine in un'archiviazione tabelle di Azure |4 |
| Tutti gli altri scenari di copia |1 |

> [!TIP]
> Quando si copiano dati tra archivi basati su file, il comportamento predefinito in genere offre la massima velocità effettiva. Il comportamento predefinito viene determinato automaticamente.

Per controllare il carico sui computer che ospitano i dati vengono archiviati, o per ottimizzare le prestazioni di copia, è possibile sostituire il valore predefinito e specificare un valore per il **parallelCopies** proprietà. Il valore deve essere un numero intero maggiore o uguale a 1. In fase di esecuzione per ottenere prestazioni ottimali, l'attività di copia Usa un valore che è minore o uguale al valore impostato.

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

**Punti da notare:**

* Quando si copiano dati tra archivi basati su file, **parallelCopies** determina il parallelismo a livello di file. La suddivisione in blocchi all'interno di un singolo file succede dietro le quinte automatico e trasparente. È progettato per usare il più adatto blocco dimensione per un tipo di archivio dati di origine specificato caricare i dati in parallelo e ortogonale a **parallelCopies**. Il numero effettivo di copie parallele usate dal servizio di spostamento dati per l'operazione di copia in fase di esecuzione non è maggiore del numero di file disponibili. Se è il comportamento di copia **mergeFile**, l'attività di copia non può trarre vantaggio dal parallelismo a livello di file.
* Quando si specifica un valore per il **parallelCopies** proprietà, prendere in considerazione l'aumento del carico nell'origine e archivi dati sink. Anche prendere in considerazione l'aumento del carico per il runtime di integrazione self-hosted se l'attività di copia viene ottimizzata da quest, ad esempio, per la copia ibrida. Questo aumento del carico si verifica in particolare quando sono presenti più attività o esecuzioni simultanee delle stesse attività che vengono eseguiti a fronte dell'archivio dati stesso. Se si nota un sovraccarico quando il carico è l'archivio dati o il runtime di integrazione self-hosted, diminuire la **parallelCopies** valore per alleggerirlo.
* Quando si copiano dati da archivi non basati su file in archivi basati su file, il servizio di spostamento dati ignora la **parallelCopies** proprietà. Anche se viene specificato, in questo caso il parallelismo non viene applicato.
* Il **parallelCopies** proprietà è ortogonale a **dataIntegrationUnits**. La prima viene conteggiata su tutte le unità di integrazione dati.

## <a name="staged-copy"></a>copia di staging

Quando si copiano dati da un archivio dati di origine a un archivio dati sink, è possibile scegliere di usare un archivio BLOB come archivio di staging provvisorio. La funzionalità di staging è particolarmente utile nei casi seguenti:

- **Si desidera inserire i dati da diversi archivi dati in SQL Data Warehouse tramite PolyBase.** SQL Data Warehouse fa uso di PolyBase come meccanismo a velocità effettiva elevata per il caricamento di grandi quantità di dati in SQL Data Warehouse. I dati di origine devono essere nell'archivio Blob o Azure Data Lake Store, e devono soddisfare criteri aggiuntivi. Quando si caricano dati da un archivio dati non BLOB o Azure Data Lake Store, è possibile attivare la copia di dati tramite un'archiviazione BLOB di staging provvisoria. In tal caso, Azure Data Factory esegue le trasformazioni di dati necessari per assicurarsi che vengano soddisfatti i requisiti di PolyBase. Quindi usa PolyBase per caricare in modo efficiente i dati in SQL Data Warehouse. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **A volte occorre tempo per eseguire uno spostamento dati ibrido (vale a dire, per copiare da un locale archivio dati a un archivio dati cloud) tramite una connessione di rete lenta.** Per migliorare le prestazioni, è possibile utilizzare una copia di staging per comprimere i dati in locale in modo che sia necessario meno tempo per spostare i dati nell'archivio di staging dei dati nel cloud. È quindi possibile decomprimere i dati nell'archivio di staging prima del caricamento nell'archivio dati di destinazione.
- **Non si desidera aprire porte diverse dalle porte 80 e 443 nel firewall a causa dei criteri IT aziendali.** Ad esempio, quando si copiano dati da un archivio dati locale a un sink del database SQL di Azure o un sink di Azure SQL Data Warehouse, è necessario attivare le comunicazioni TCP in uscita sulla porta 1433 per Windows Firewall e per il firewall aziendale. In questo scenario, una copia di staging può sfruttare i vantaggi del runtime di integrazione self-hosted innanzitutto copiare dati in un archivio Blob di staging istanza tramite HTTP o HTTPS sulla porta 443. Quindi possibile caricare i dati nel Database SQL o SQL Data Warehouse dall'archivio Blob di staging. In questo flusso non è necessario abilitare la porta 1433.

### <a name="how-staged-copy-works"></a>Come funziona la copia di staging

Quando si attiva la funzionalità di staging, i dati vengono prima copiati dall'archivio dati di origine nell'archivio BLOB di staging personale. Successivamente, vengono copiati dall'archivio dati di staging nell'archivio dati sink. Azure Data Factory gestisce automaticamente il flusso in due fasi per l'utente. Azure Data Factory ed elimina i dati temporanei dall'archivio di staging dopo lo spostamento dei dati è stata completata.

![copia di staging](media/copy-activity-performance/staged-copy.png)

Quando si attiva lo spostamento dei dati tramite un archivio di staging, è possibile specificare se si desidera archiviano i dati deve essere compresso prima di spostare i dati dall'origine dati per un provvisorio o archivio dati di staging e poi decompressi prima dello spostamento dei dati da un dat staging o provvisorio un archivio all'archivio dati sink.

Attualmente non è possibile copiare dati tra due archivi dati locali usando un archivio di staging.

### <a name="configuration"></a>Configurazione

Configurare il **enableStaging** impostazione nell'attività di copia per specificare se si desidera che i dati vengano aggiunti temporaneamente in archiviazione Blob, prima di caricarli in un archivio dati di destinazione. Quando si imposta **enableStaging** a `TRUE`, specificare le proprietà aggiuntive elencate nella tabella seguente. È anche necessario creare una risorsa di archiviazione di Azure o l'archiviazione condivisa servizio collegato di firma di accesso per la gestione temporanea se non hai uno.

| Proprietà | Descrizione | Valore predefinito | Obbligatorio |
| --- | --- | --- | --- |
| enableStaging |Specificare se si vuole copiare i dati tramite un archivio di staging provvisorio. |False |No |
| linkedServiceName |Specificare il nome di un servizio collegato [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) che fa riferimento all'istanza di archiviazione usata come archivio di staging provvisorio. <br/><br/> È non è possibile usare l'archiviazione con firma di accesso condiviso per caricare dati in SQL Data Warehouse tramite PolyBase. Può essere usata in tutti gli altri scenari. |N/D |Sì, quando **enableStaging** è impostato su TRUE |
| path |Specificare il percorso dell'archivio BLOB che deve contenere i dati di staging. Se non si specifica un percorso, il servizio crea un contenitore per archiviare dati temporanei. <br/><br/> Specificare un percorso solo se si usa l'archiviazione con una firma di accesso condiviso o se i dati temporanei devono trovarsi in un percorso specifico. |N/D |No |
| enableCompression |Specifica se i dati devono essere compressi prima di copiarli nella destinazione. Questa impostazione ridurre il volume dei dati da trasferire. |False |No |

>[!NOTE]
> Se si usa una copia di staging con compressione abilitata, l'entità servizio o autenticazione del servizio gestito per la gestione temporanea del servizio blob collegato non è supportato.

Ecco una definizione di esempio di un'attività di copia con le proprietà descritte nella tabella precedente:

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

### <a name="staged-copy-billing-impact"></a>Impatto della copia di staging sulla fatturazione

Ti viene addebitata in base alle due passaggi: durata della copia e tipo di copia.

* Quando si usa staging durante una copia nel cloud, che sta copiando i dati da un archivio dati cloud a un altro archivio dati cloud, entrambe fasi attivate dal runtime di integrazione di Azure, ti viene addebitata [somma della durata della copia per i passaggi 1 e 2] x [prezzo unitario della copia cloud].
* Quando si utilizzano staging durante una copia ibrida, che sta copiando i dati da un archivio dati locale a un archivio dati cloud, un'unica fase abilitata da un runtime di integrazione self-hosted, viene addebitato [durata della copia ibrida] x [prezzo unitario della copia ibrida] + [durata della copia nel cloud] x [prezzo unitario della copia cloud].

## <a name="performance-tuning-steps"></a>Procedura di ottimizzazione delle prestazioni

Eseguire questi passaggi per ottimizzare le prestazioni del servizio Azure Data Factory con l'attività di copia.

1. **Stabilire una linea di base.** Durante la fase di sviluppo, testare la pipeline usando l'attività di copia su un campione di dati rappresentativo. Raccogliere i dettagli di esecuzione e le caratteristiche di prestazioni seguendo [Monitoraggio attività di copia](copy-activity-overview.md#monitoring).

2. **Diagnosticare e ottimizzare le prestazioni.** Se le prestazioni osservate non soddisfano le aspettative, identificare eventuali colli di bottiglia. e quindi ottimizzare le prestazioni per rimuovere o ridurre l'effetto dei colli di bottiglia.

    In alcuni casi, quando si esegue un'attività di copia in Azure Data Factory, viene visualizzato un messaggio "Suggerimenti sull'ottimizzazione delle prestazioni" in cima il [pagina Monitoraggio attività di copia](copy-activity-overview.md#monitor-visually), come illustrato nell'esempio seguente. Il messaggio indica un collo di bottiglia che è stata identificata per l'esecuzione di copia specificata. Vengono inoltre sulle operazioni da passare alla velocità effettiva di copia di boost. I suggerimenti di ottimizzazione delle prestazioni è attualmente forniscono, ad esempio suggerimenti:

    - Usare PolyBase, quando si copiano dati in Azure SQL Data Warehouse.
    - Aumentare le unità di richiesta di Azure Cosmos DB o Azure SQL Database Dtu (Database Throughput Unit) quando la risorsa sul lato dell'archivio di dati è il collo di bottiglia.
    - Rimuovere la copia di staging non necessaria.

    Anche le regole di ottimizzazione delle prestazioni verranno gradualmente migliorate.

    **Esempio: Copiare nel Database SQL di Azure con suggerimenti per l'ottimizzazione delle prestazioni**

    In questo esempio, durante una copia di eseguire, Azure Data Factory rileva il sink che Azure SQL Database raggiunge elevato utilizzo di DTU, rallentando così le operazioni di scrittura. Il suggerimento consiste nell'aumentare il livello di Database SQL di Azure con più Dtu. 

    ![Monitoraggio della copia con suggerimenti per l'ottimizzazione delle prestazioni](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Si riportano inoltre le considerazioni comuni seguenti. Una descrizione completa della diagnosi delle prestazioni non rientra nell'ambito di questo articolo.

   * Funzionalità per le prestazioni:
     * [Copia parallela](#parallel-copy)
     * [Unità di integrazione dati](#data-integration-units)
     * [Copia di staging](#staged-copy)
     * [Scalabilità del runtime di integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Runtime di integrazione self-hosted](#considerations-for-self-hosted-integration-runtime)
   * [Origine](#considerations-for-the-source)
   * [Sink](#considerations-for-the-sink)
   * [Serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization)
   * [Compressione](#considerations-for-compression)
   * [Mapping di colonne](#considerations-for-column-mapping)
   * [Altre considerazioni](#other-considerations)

3. **Espandere la configurazione per l'intero set di dati.** Quando è soddisfatti delle prestazioni e risultati di esecuzione, è possibile espandere la definizione e la pipeline per coprire l'intero set di dati.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Considerazioni per il runtime di integrazione self-hosted

Se l'attività di copia viene eseguita in un runtime di integrazione self-hosted, tenere presente quanto segue:

**Configurazione**: È consigliabile usare un computer dedicato per il runtime di integrazione di host. Visualizzare [considerazioni sull'uso del runtime di integrazione self-hosted](concepts-integration-runtime.md).

**Aumentare il numero di istanze**: Un runtime di integrazione self-hosted logico singolo con uno o più nodi può servire più esecuzioni di attività di copia nello stesso momento contemporaneamente. Se si hanno necessità complesse di spostamento di dati ibridi con un numero elevato di esecuzioni di attività di copia simultanee o con una quantità notevole di dati da copiare, prendere in considerazione [scalabilità orizzontale di runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md#high-availability-and-scalability) effettuare il provisioning di altre risorse per supporto della copia.

## <a name="considerations-for-the-source"></a>Considerazioni sull'origine

### <a name="general"></a>Generale

Assicurarsi che l'archivio dati sottostante non viene sovraccaricato di altri carichi di lavoro in esecuzione su o su di esso.

Per gli archivi dati di Microsoft, vedere [di monitoraggio e ottimizzazione argomenti](#performance-reference) che sono specifiche per gli archivi dati. per comprendere meglio le caratteristiche delle prestazioni degli archivi dati e come ridurre al minimo i tempi di risposta e ottimizzare la velocità effettiva.

* Se si copiano dati da un archivio Blob a SQL Data Warehouse, prendere in considerazione l'uso di PolyBase per migliorare le prestazioni. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Se si copiano dati da HDFS, archiviazione Blob di Azure o Azure Data Lake Store, prendere in considerazione l'uso di DistCp per migliorare le prestazioni. Per altre informazioni, vedere [usare DistCp per copiare dati da HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se si copiano dati da Redshift ad Azure SQL Data Warehouse, archiviazione BLob di Azure o Azure Data Lake Store, è consigliabile usare UNLOAD per migliorare le prestazioni. Per altre informazioni, vedere [usare UNLOAD per copiare dati da Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Archivi dati basati su file

* **Dimensioni medie dei file e numero medio di file**: L'attività di copia trasferisce i file di dati di uno alla volta. Con la stessa quantità di dati da spostare, la velocità effettiva generale risulta inferiore se i dati sono costituiti da un numero elevato di file piccoli anziché da pochi file di grandi dimensioni. Ciò è dovuto alla fase di bootstrap necessaria per ogni file. Se possibile, combinare file di piccole dimensioni in file più grandi per ottenere una velocità effettiva superiore.
* **Formato di file e compressione**: per altre informazioni su come migliorare le prestazioni, vedere le sezioni [Considerazioni sulla serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization) e [Considerazioni sulla compressione](#considerations-for-compression).

### <a name="relational-data-stores"></a>Archivi dati relazionali

* **Modello di dati**: Lo schema di tabella influisce sulla velocità effettiva di copia. Dimensioni di riga grandi offrono prestazioni migliori rispetto a una riga di piccole dimensioni per copiare la stessa quantità di dati. Questo perché il database è in grado di recuperare in modo più efficiente un minor numero di batch di dati che contengono meno righe.
* **Query o stored procedure**: Ottimizzare la logica della query o stored procedure specificate nell'origine dell'attività di copia per recuperare i dati in modo più efficiente.

## <a name="considerations-for-the-sink"></a>Considerazioni sul sink

### <a name="general"></a>Generale

Assicurarsi che l'archivio dati sottostante non viene sovraccaricato di altri carichi di lavoro in esecuzione su o su di esso.

Per gli archivi dati di Microsoft, vedere [di monitoraggio e ottimizzazione argomenti](#performance-reference) che sono specifiche per gli archivi dati. per comprendere meglio le caratteristiche delle prestazioni degli archivi dati e come ridurre al minimo i tempi di risposta e ottimizzare la velocità effettiva.

* Se si copiano dati da qualsiasi archivio dati in Azure SQL Data Warehouse, prendere in considerazione l'uso di PolyBase per migliorare le prestazioni. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Se si copiano dati da HDFS, archiviazione Blob di Azure o Azure Data Lake Store, prendere in considerazione l'uso di DistCp per migliorare le prestazioni. Per altre informazioni, vedere [usare DistCp per copiare dati da HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se si copiano dati da Redshift ad Azure SQL Data Warehouse, archiviazione Blob di Azure o Azure Data Lake Store, è consigliabile usare UNLOAD per migliorare le prestazioni. Per altre informazioni, vedere [usare UNLOAD per copiare dati da Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Archivi dati basati su file

* **Comportamento di copia**: Se si copiano dati da un archivio dati basato su file differente, l'attività di copia è disponibili tre opzioni tramite il **copyBehavior** proprietà. mantenere la gerarchia, rendere flat la gerarchia e unire i file. Conservare o rendere flat la gerarchia comporta un overhead delle prestazioni minimo, mentre unire i file provoca un aumento dell'overhead delle prestazioni.
* **Formato di file e compressione**: per altre informazioni su come migliorare le prestazioni, vedere le sezioni [Considerazioni sulla serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization) e [Considerazioni sulla compressione](#considerations-for-compression).

### <a name="relational-data-stores"></a>Archivi dati relazionali

* **Copiare implicazione di comportamento e le prestazioni**: Esistono diversi modi per scrivere dati in un sink SQL. Altre informazioni, vedere [procedure consigliate per il caricamento dei dati nel Database SQL di Azure](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Modello di dati e dimensioni batch**:
  * Lo schema di tabella influisce sulla velocità effettiva di copia. Per copiare la stessa quantità di dati, dimensioni di riga grandi offrono prestazioni migliori rispetto a dimensioni di riga piccole, perché il database può eseguire in modo più efficiente il commit di un numero inferiore di batch di dati.
  * L'attività di copia inserisce i dati in una serie di batch. Per impostare il numero di righe in un batch è possibile usare la proprietà **writeBatchSize** . Se le righe dei dati sono di piccole dimensioni, è possibile impostare la proprietà **writeBatchSize** con un valore più elevato per sfruttare l'overhead di un numero minore di batch e aumentare la velocità effettiva. Se le righe sono di grandi dimensioni, prestare attenzione quando si aumenta il valore di **writeBatchSize**. Un valore elevato può causare un errore di copia dovuto a un sovraccarico del database.

### <a name="nosql-stores"></a>Archivi NoSQL

* Per gli **archivi tabelle**:
  * **Partizione**: scrivere i dati nelle partizioni con interleave riduce drasticamente le prestazioni. Per ordinare i dati di origine chiave di partizione in modo che i dati vengono inseriti in modo efficiente in una partizione dopo l'altra. In alternativa, è possibile regolare la logica per scrivere i dati in una singola partizione.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerazioni sulla serializzazione e deserializzazione

Serializzazione e deserializzazione possono verificarsi quando il set di dati di input o di un set di dati di output è un file. Per altre informazioni sui formati di file supportati dall'attività di copia, vedere [formati di file e di compressione supportati](supported-file-formats-and-compression-codecs.md).

**Comportamento di copia**:

* Copia di file tra archivi dati basati su file:
  * Quando dispone di input e output i set di dati sia lo stesso o nessuna impostazione di formato di file, il servizio di spostamento dati esegue una *copia binaria* senza alcuna serializzazione o deserializzazione. La velocità effettiva è superiore rispetto allo scenario, in cui le impostazioni del formato di file di origine e del sink sono diverse tra loro.
  * Se l'input e output i set di dati entrambi sono in formato testo e solo la codifica del tipo è diverso, il servizio di spostamento dati esegue solo la conversione di codifica. Non esegue alcuna operazione di serializzazione o deserializzazione e questo dà luogo a un certo overhead delle prestazioni rispetto alla copia binaria.
  * Quando dispone di input e output i set di dati entrambi formati di file diversi o configurazioni diverse, ad esempio i delimitatori, il servizio di spostamento dati deserializza i dati di origine per trasmettere, trasformarli e quindi serializzarli nel formato di output indicato. Questa operazione comporta un overhead delle prestazioni decisamente maggiore rispetto ad altri scenari.
* Quando si copiano file da o verso un archivio dati che non è file di base, ad esempio, da un archivio basato su file a un archivio relazionale, è necessario il passaggio di serializzazione o deserializzazione. Questo passaggio comporta un notevole overhead delle prestazioni.

**Formato di file**: il formato di file scelto può influire sulle prestazioni di copia. Ad esempio, Avro è un formato binario compresso che archivia i metadati con i dati. È ampiamente supportato nell'ecosistema di Hadoop per l'elaborazione e l'esecuzione di query. Avro è più costosa per la serializzazione e deserializzazione, con conseguente velocità effettiva di copia inferiore rispetto al formato di testo. 

Scegliere il formato di file per tutto il flusso di elaborazione a livello globale. Iniziare con:

- Formato in cui i dati verrà archiviato in archivi dati di origine o devono essere estratti dai sistemi esterni.
- Il formato migliore per l'archiviazione, elaborazione analitica e l'esecuzione di query.
- In quale formato di dati devono essere esportati nei data mart per gli strumenti di visualizzazione e creazione di report.

A volte un formato di file non ottimale dal punto di vista delle prestazioni di lettura e scrittura può invece essere una buona scelta dal punto di vista del processo analitico generale.

## <a name="considerations-for-compression"></a>Considerazioni sulla compressione

Quando il set di dati di input o output è un file, è possibile impostare l'attività di copia per eseguire la compressione o decompressione durante la scrittura dei dati nella destinazione. La scelta della compressione comporta un compromesso tra input/output (I/O) e CPU. La compressione dei dati ha un costo maggiore in termini di risorse di calcolo, ma riduce l'I/O di rete e l'archiviazione. A seconda dei dati, si potrebbe riscontrare un aumento di velocità effettiva di copia complessiva.

**Codec**: Ogni codec di compressione presenta dei vantaggi. Ad esempio, bzip2 ha la velocità effettiva copia più bassa, ma offre prestazioni di query Hive migliori perché permette di dividerle per l'elaborazione. Gzip è l'opzione più bilanciata, e ha utilizzato più spesso. Scegliere il codec più adatto allo scenario end-to-end personalizzato.

**Livello**: per ogni codec di compressione è possibile scegliere tra due opzioni, la compressione più veloce e la compressione ottimale. L'opzione di compressione più veloce comprime i dati più rapidamente possibile, anche se il file risultante non viene compresso in modo ottimale. L'opzione di compressione ottimale impiega più tempo per la compressione e restituisce una quantità minima di dati. È possibile testare entrambe le opzioni per verificare quale offra le migliori prestazioni complessive in base alle proprie esigenze.

**Una considerazione**: per copiare una grande quantità di dati tra un archivio locale e il cloud, è consigliabile usare una [copia di staging](#staged-copy) con compressione abilitata. Uso di archivio provvisorio risulta utile quando la larghezza di banda della rete azienda e i servizi di Azure è il fattore limitante e si desidera il set di dati di input e output i set di dati sia per essere in formato non compresso.

## <a name="considerations-for-column-mapping"></a>Considerazioni sul mapping di colonne

È possibile impostare il **columnMappings** proprietà nell'attività di copia per tutti i mapping oppure un subset delle colonne di input alle colonne di output. Dopo aver letto i dati dall'origine, il servizio di spostamento dati deve eseguire il mapping delle colonne sui dati prima di scriverli nel sink. Questa ulteriore elaborazione riduce la velocità effettiva di copia.

Se l'archivio dati di origine è disponibile per query, ad esempio nel caso di un archivio relazionale come il database SQL o SQL Server, oppure nel caso di un archivio NoSQL come un archivio tabelle o Azure Cosmos DB, è consigliabile eseguire il push della logica di filtro e riordinamento colonne per la proprietà **query** anziché usare il mapping colonne. In questo modo, la proiezione si verifica quando il servizio di spostamento dati legge i dati dall'archivio dati di origine, in cui è molto più efficiente.

Altre informazioni, vedere [mapping dello schema di attività di copia](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Altre considerazioni

Se le dimensioni dei dati da copiare sono grande, è possibile modificare la logica di business per partizionare ulteriormente i dati. È possibile pianificare l'attività di copia venga eseguita più frequentemente per ridurre le dimensioni dei dati per ogni attività di copia che viene eseguito.

Prestare attenzione il numero di set di dati e copiare le attività che richiedono Azure Data Factory per connettersi allo stesso archivio dati nello stesso momento. Molti processi di copia simultanei possono limitare un archivio dati e causare un peggioramento delle prestazioni, nuovi tentativi interni dei processi di copia e, in alcuni casi, errori di esecuzione.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Scenario di esempio: Copiare da un server SQL in locale nell'archiviazione Blob

**Scenario**: Viene compilata una pipeline per copiare dati da un server SQL in locale nell'archiviazione Blob in formato CSV. Per velocizzare il processo di copia, i file CSV devono essere compressi in formato bzip2.

**Test e analisi**: La velocità effettiva dell'attività di copia è minore di 2 MBps, che risulta decisamente più lento rispetto al benchmark delle prestazioni.

**Analisi e ottimizzazione delle prestazioni**: per risolvere il problema delle prestazioni, occorre vedere prima di tutto come vengono elaborati e spostati i dati.

- **Lettura dei dati**: Il runtime di integrazione apre una connessione a SQL Server e invia la query. SQL Server risponde inviando il flusso di dati per il runtime di integrazione tramite intranet.
- **Serializzazione e compressione dei dati**: Il runtime di integrazione serializza il flusso di dati in formato CSV e comprime i dati in un flusso bzip2.
- **Scrittura dei dati**: Il runtime di integrazione carica il flusso bzip2 in archiviazione Blob tramite internet.

Come può notare, i dati vengono elaborati e spostati in un flusso sequenziale: SQL Server > LAN > Runtime di integrazione > WAN > Archiviazione BLOB. Le prestazioni complessive vengono controllate dalla velocità effettiva minima in tutta la pipeline.

![Flusso di dati](./media/copy-activity-performance/case-study-pic-1.png)

Uno o più dei fattori seguenti possono provocare un collo di bottiglia nelle prestazioni:

* **Origine**: SQL Server ha di per sé una velocità effettiva bassa a causa dei carichi elevati.
* **Runtime di integrazione self-hosted**:
  * **LAN**: il runtime di integrazione è distante da SQL Server con una connessione a larghezza di banda bassa.
  * **Runtime di integrazione**: il runtime di integrazione ha raggiunto i relativi limiti di carico per eseguire queste operazioni:
    * **Serializzazione**: la serializzazione del flusso di dati in formato CSV ha una velocità effettiva bassa.
    * **Compressione**: Si è scelto un codec di compressione lenta, ad esempio, bzip2, ovvero 2,8 MBps con Core i7.
  * **WAN**: La larghezza di banda tra la rete aziendale e i servizi di Azure è bassa, ad esempio T1 = 1.544 kbps; T2 = 6.312 kbps.
* **Sink**: l'archivio BLOB ha una velocità effettiva bassa. Questo scenario è poco probabile perché il contratto di servizio (SLA) garantisce un minimo di 60 MBps.

In tal caso, la compressione dati bzip2 potrebbe rallentare l'intera pipeline. Il passaggio al codec di compressione gzip può ridurre questo collo di bottiglia.

## <a name="references"></a>Riferimenti

Di seguito sono riportati alcuni riferimenti sul monitoraggio e l'ottimizzazione delle prestazioni per alcuni degli archivi dati supportati:

* Archiviazione di Azure, che include l'archiviazione Blob e archiviazione di tabella: [Obiettivi di scalabilità di archiviazione di Azure](../storage/common/storage-scalability-targets.md) e [elenco di controllo delle prestazioni e scalabilità per archiviazione di Azure](../storage/common/storage-performance-checklist.md).
* Database SQL di Azure: È possibile [monitorare le prestazioni](../sql-database/sql-database-single-database-monitor.md) e controllare la percentuale di unità di transazione Database (DTU).
* Azure SQL Data Warehouse: La funzionalità viene misurata in unità Data Warehouse (Dwu). Visualizzare [Gestisci potenza di calcolo in Azure SQL Data Warehouse (panoramica)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Livelli di prestazioni in Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server locale: [Monitorare e ottimizzare le prestazioni](https://msdn.microsoft.com/library/ms189081.aspx).
* File server locale: [Ottimizzazione delle prestazioni per file server](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli di attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping dello schema di attività di copia](copy-activity-schema-and-type-mapping.md)
- [Tolleranza di errore dell'attività di copia](copy-activity-fault-tolerance.md)
