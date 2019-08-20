---
title: Guida alle prestazioni e all'ottimizzazione dell'attività di copia in Azure Data Factory | Microsoft Docs
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
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: d8ce0a4f6bacdd1c8c858d474e6f3957a23c6357
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967369"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guida alle prestazioni dell'attività di copia e all'ottimizzazione
> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory che si sta usando:"]
> * [Versione 1](v1/data-factory-copy-activity-performance.md)
> * [Versione corrente](copy-activity-performance.md)


L'attività di copia Azure Data Factory offre una soluzione di caricamento dei dati sicura, affidabile e a prestazioni elevate di prima classe. È possibile usarlo per copiare decine di terabyte di dati ogni giorno in un'ampia gamma di archivi dati locali e cloud. Il rapido miglioramento delle prestazioni di caricamento dei dati è fondamentale per garantire la possibilità di concentrarsi sul problema principale Big Data: creare soluzioni di analisi avanzate e ottenere informazioni approfondite da tutti i dati.

Azure offre un set di soluzioni di archiviazione e data warehouse di dati di livello aziendale. L'attività di copia offre un'esperienza di caricamento dei dati altamente ottimizzata e facile da configurare e configurare. Con una singola attività di copia, è possibile caricare i dati in:

* Azure SQL Data Warehouse a 1,2 GBps.
* Archiviazione BLOB di Azure a 1,0 GBps.
* Azure Data Lake Store a 1,0 GBps.

L'articolo illustra:

* [Numeri di riferimento delle prestazioni](#performance-reference) per gli archivi dati di origine e sink supportati per semplificare la pianificazione del progetto.
* Funzionalità che consentono di aumentare la velocità effettiva di copia in diversi scenari, incluse le [unità di integrazione dati](#data-integration-units) (DIUs), la [copia parallela](#parallel-copy)e la [copia temporanea](#staged-copy).
* [Linee guida](#performance-tuning-steps) per l'ottimizzazione delle prestazioni per ottimizzare le prestazioni e i fattori chiave che possono influire sulle prestazioni di copia.

> [!NOTE]
> Se non si ha familiarità con l'attività di copia in generale, vedere la [Panoramica dell'attività di copia](copy-activity-overview.md) prima di leggere questo articolo.
>

## <a name="performance-reference"></a>Informazioni di riferimento sulle prestazioni

Come riferimento, la tabella seguente mostra il numero di velocità effettiva di copia in MBps per le coppie di origine e sink specificate in una singola esecuzione dell'attività di copia in base a test interni. Per il confronto, viene anche illustrato come le diverse impostazioni di [unità di integrazione dati](#data-integration-units) o la scalabilità del [runtime di integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime) (più nodi) possano essere utili per le prestazioni di copia.

![Matrice delle prestazioni](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Quando l'attività di copia viene eseguita in un runtime di integrazione di Azure, le unità di integrazione dati minime consentite (in precedenza note come unità di spostamento dati) sono due. Se non è specificato, vedere le unità di integrazione dati predefinite usate in [unità di integrazione dati](#data-integration-units).

**Punti da notare:**

* La velocità effettiva viene calcolata usando la formula seguente: [dimensione dei dati letti dall'origine]/[durata dell'esecuzione dell'attività di copia].
* I numeri di riferimento delle prestazioni nella tabella sono stati misurati usando un set di dati [TPC-H](http://www.tpc.org/tpch/) in un'unica esecuzione dell'attività di copia. I file di test per gli archivi basati su file sono più file con dimensioni di 10 GB.
* Nel caso degli archivi dati di Azure, l'origine e il sink si trovano nella stessa area di Azure.
* Per la copia ibrida tra archivi dati locali e cloud, ogni nodo del runtime di integrazione self-hosted è in esecuzione in un computer separato dall'archivio dati con la specifica seguente. Durante l'esecuzione di una singola attività, l'operazione di copia ha usato solo una piccola parte della CPU, della memoria o della larghezza di banda di rete del computer di test.
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
> È possibile ottenere una velocità effettiva più elevata usando più DIUs. Con 100 DIUs, ad esempio, è possibile copiare i dati dall'archiviazione BLOB di Azure in Azure Data Lake Store a 1,0 GBps. Per ulteriori informazioni su questa funzionalità e sullo scenario supportato, vedere la sezione [unità di integrazione dati](#data-integration-units) . 

## <a name="data-integration-units"></a>Unità di integrazione dati

Un'unità di integrazione dati è una misura che rappresenta la potenza, ovvero una combinazione di CPU, memoria e allocazione di risorse di rete, di una singola unità in Azure Data Factory. L'unità di integrazione dati si applica solo al [runtime di integrazione di Azure](concepts-integration-runtime.md#azure-integration-runtime), ma non al runtime di [integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime).

Il DIUs minimo per consentire l'esecuzione di un'attività di copia è due. Se non specificato, nella tabella seguente sono elencate le unità di integrazione dati predefinite usate in diversi scenari di copia:

| Scenario di copia | Numero di unità di integrazione dati predefinite determinato dal servizio |
|:--- |:--- |
| Copiare dati tra archivi basati su file | Compreso tra 4 e 32 in base al numero e alle dimensioni dei file |
| Copiare i dati nel database SQL di Azure o Azure Cosmos DB |Tra 4 e 16 a seconda del livello del database SQL di Azure sink o del Cosmos DB (numero di DTU/UR) |
| Tutti gli altri scenari di copia | 4 |

Per ignorare l'impostazione predefinita, è possibile specificare un valore per la proprietà **dataIntegrationUnits** procedendo come segue. I *valori* consentiti per la proprietà **dataIntegrationUnits** sono fino a 256. Il *numero effettivo di unità di integrazione dati* usate dall'operazione di copia in fase di esecuzione è minore o uguale al valore configurato, a seconda del modello di dati. Per informazioni sul livello di miglioramento delle prestazioni che è possibile ottenere quando si configurano più unità per un sink e un'origine della copia specifici, vedere la sezione [Informazioni di riferimento sulle prestazioni](#performance-reference).

Quando si monitora un'esecuzione di attività, è possibile visualizzare i DIUs usati per ogni copia eseguita nell'output dell'attività di copia. Per altre informazioni, vedere [monitoraggio dell'attività di copia](copy-activity-overview.md#monitoring).

> [!NOTE]
> L'impostazione di DIUs maggiori di quattro attualmente si applica solo quando si copiano più file da archiviazione di Azure, Azure Data Lake Storage, Amazon S3, Google Cloud Storage, cloud FTP o cloud SFTP a tutti gli altri archivi dati cloud.
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

Tenere presente che l'addebito è basato sul tempo totale di esecuzione dell'operazione di copia. La durata totale fatturata per lo spostamento dei dati è la somma della durata tra DIUs. Se un processo di copia impiegava un'ora con due unità cloud e ora richiede 15 minuti con otto unità cloud, la fattura complessiva rimane pressoché identica.

## <a name="parallel-copy"></a>Copia parallela

È possibile usare la proprietà **parallelCopies** per indicare il parallelismo che si vuole venga usato dall'attività di copia. È possibile considerare questa proprietà come il numero massimo di thread all'interno dell'attività di copia che può leggere dall'origine o scrivere negli archivi dati sink in parallelo.

Per ogni esecuzione dell'attività di copia, Azure Data Factory determina il numero di copie parallele da usare per copiare i dati dall'archivio dati di origine all'archivio dati di destinazione. Il numero predefinito di copie parallele usate dipende dal tipo di origine e sink usato.

| Scenario di copia | Numero predefinito di copie parallele determinato dal servizio |
| --- | --- |
| Copiare dati tra archivi basati su file |Dipende dalle dimensioni dei file e dal numero di DIUs usati per copiare i dati tra due archivi dati cloud o la configurazione fisica del computer del runtime di integrazione self-hosted. |
| Copiare i dati da qualsiasi archivio di origine ad archiviazione tabelle di Azure |4 |
| Tutti gli altri scenari di copia |1 |

> [!TIP]
> Quando si copiano dati tra archivi basati su file, il comportamento predefinito in genere offre la migliore velocità effettiva. Il comportamento predefinito viene determinato automaticamente in base al modello di file di origine.

Per controllare il carico sui computer che ospitano gli archivi dati o per ottimizzare le prestazioni di copia, è possibile eseguire l'override del valore predefinito e specificare un valore per la proprietà **parallelCopies** . Il valore deve essere un numero intero maggiore o uguale a 1. In fase di esecuzione, per ottenere prestazioni ottimali, l'attività di copia utilizza un valore minore o uguale al valore impostato.

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

* Quando si copiano dati tra archivi basati su file, **parallelCopies** determina il parallelismo a livello di file. La suddivisione in blocchi all'interno di un singolo file avviene in modo automatico e trasparente. È progettato per usare le dimensioni del blocco più adatte per un determinato tipo di archivio dati di origine per caricare i dati in parallelo e ortogonali a **parallelCopies**. Il numero effettivo di copie parallele usate dal servizio di spostamento dati per l'operazione di copia in fase di esecuzione non è maggiore del numero di file disponibili. Se il comportamento di copia è **mergeFile**, l'attività di copia non può sfruttare il parallelismo a livello di file.
* Quando si copiano dati da archivi che non sono basati su file (ad eccezione di [Oracle](connector-oracle.md#oracle-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) Connector come origine con il partizionamento dei dati abilitato) per gli archivi basati su file, il servizio di spostamento dei dati Ignora la proprietà **parallelCopies** . Anche se viene specificato, in questo caso il parallelismo non viene applicato.
* La proprietà **parallelCopies** è ortogonale a **dataIntegrationUnits**. La prima viene conteggiata su tutte le unità di integrazione dati.
* Quando si specifica un valore per la proprietà **parallelCopies** , prendere in considerazione l'aumento del carico per gli archivi dati di origine e sink. Prendere in considerazione anche l'aumento del carico per il runtime di integrazione self-hosted se l'attività di copia è abilitata, ad esempio per la copia ibrida. Questo aumento del carico si verifica soprattutto quando si hanno più attività o esecuzioni simultanee delle stesse attività eseguite nello stesso archivio dati. Se si nota che l'archivio dati o il runtime di integrazione self-hosted è sovraccarico del carico, ridurre il valore di **parallelCopies** per alleggerire il carico.

## <a name="staged-copy"></a>copia di staging

Quando si copiano dati da un archivio dati di origine a un archivio dati sink, è possibile scegliere di usare un archivio BLOB come archivio di staging provvisorio. La funzionalità di staging è particolarmente utile nei casi seguenti:

- **Si vuole inserire dati da diversi archivi dati in SQL Data Warehouse tramite la polibase.** SQL Data Warehouse fa uso di PolyBase come meccanismo a velocità effettiva elevata per il caricamento di grandi quantità di dati in SQL Data Warehouse. I dati di origine devono trovarsi in un archivio BLOB o Azure Data Lake Store e devono soddisfare criteri aggiuntivi. Quando si caricano dati da un archivio dati non BLOB o Azure Data Lake Store, è possibile attivare la copia di dati tramite un'archiviazione BLOB di staging provvisoria. In tal caso, Azure Data Factory esegue le trasformazioni dei dati necessarie per verificare che soddisfi i requisiti di polibase. Quindi usa PolyBase per caricare in modo efficiente i dati in SQL Data Warehouse. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **A volte è necessario un po' di tempo per eseguire uno spostamento dati ibrido, ovvero per copiare da un archivio dati locale a un archivio dati cloud, su una connessione di rete lenta.** Per migliorare le prestazioni, è possibile usare la copia di staging per comprimere i dati in locale, in modo che sia necessario meno tempo per spostare i dati nell'archivio dati di staging nel cloud. Sarà quindi possibile decomprimere i dati nell'archivio di staging prima di caricarli nell'archivio dati di destinazione.
- **Non è necessario aprire porte diverse dalla porta 80 e dalla porta 443 nel firewall a causa dei criteri IT aziendali.** Ad esempio, quando si copiano dati da un archivio dati locale a un sink del database SQL di Azure o un sink di Azure SQL Data Warehouse, è necessario attivare le comunicazioni TCP in uscita sulla porta 1433 per Windows Firewall e per il firewall aziendale. In questo scenario, la copia di staging può trarre vantaggio dal runtime di integrazione self-hosted per prima cosa copiare i dati in un'istanza di staging dell'archiviazione BLOB tramite HTTP o HTTPS sulla porta 443. Quindi, può caricare i dati nel database SQL o SQL Data Warehouse dalla gestione temporanea dell'archiviazione BLOB. In questo flusso non è necessario abilitare la porta 1433.

### <a name="how-staged-copy-works"></a>Come funziona la copia di staging

Quando si attiva la funzionalità di staging, i dati vengono prima copiati dall'archivio dati di origine nell'archivio BLOB di staging personale. Successivamente, vengono copiati dall'archivio dati di staging nell'archivio dati sink. Azure Data Factory gestisce automaticamente il flusso a due fasi. Azure Data Factory pulisce anche i dati temporanei dall'archiviazione di staging dopo il completamento dello spostamento dei dati.

![copia di staging](media/copy-activity-performance/staged-copy.png)

Quando si attiva lo spostamento dei dati usando un archivio di staging, è possibile specificare se si vuole che i dati vengano compressi prima di spostare i dati dall'archivio dati di origine a un archivio dati provvisorio o di staging e quindi decompressi prima di spostare i dati da un file dat provvisorio o di staging Archivio nell'archivio dati sink.

Attualmente, non è possibile copiare i dati tra due archivi dati connessi tramite un altro IRs indipendente, né con la copia temporanea. Per questo scenario, è possibile configurare due attività di copia concatenate in modo esplicito per la copia dall'origine alla gestione temporanea, quindi da staging a sink.

### <a name="configuration"></a>Configurazione

Configurare l'impostazione **enableStaging** nell'attività di copia per specificare se si desidera che i dati vengano gestiti temporaneamente nell'archivio BLOB prima di caricarli in un archivio dati di destinazione. Quando si imposta **enableStaging** su `TRUE`, specificare le proprietà aggiuntive elencate nella tabella seguente. È anche necessario creare un servizio collegato di archiviazione di Azure o di archiviazione con firma di accesso condiviso per la gestione temporanea, se non è già presente.

| Proprietà | Descrizione | Valore predefinito | Obbligatoria |
| --- | --- | --- | --- |
| enableStaging |Specificare se si vuole copiare i dati tramite un archivio di staging provvisorio. |False |No |
| linkedServiceName |Specificare il nome di un servizio collegato [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) che fa riferimento all'istanza di archiviazione usata come archivio di staging provvisorio. <br/><br/> Non è possibile usare l'archiviazione con una firma di accesso condiviso per caricare i dati in SQL Data Warehouse tramite la polibase. Può essere usata in tutti gli altri scenari. |N/D |Sì, quando **enableStaging** è impostato su TRUE |
| path |Specificare il percorso dell'archivio BLOB che deve contenere i dati di staging. Se non si specifica un percorso, il servizio crea un contenitore per archiviare i dati temporanei. <br/><br/> Specificare un percorso solo se si usa l'archiviazione con una firma di accesso condiviso o se i dati temporanei devono trovarsi in un percorso specifico. |N/D |No |
| enableCompression |Specifica se i dati devono essere compressi prima di essere copiati nella destinazione. Questa impostazione ridurre il volume dei dati da trasferire. |False |No |

>[!NOTE]
> Se si usa la copia di staging con la compressione abilitata, l'entità servizio o l'autenticazione MSI per il servizio collegato del BLOB di staging non è supportata.

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

I costi vengono addebitati in base a due passaggi: durata della copia e tipo di copia.

* Quando si usa la gestione temporanea durante una copia nel cloud, che copia i dati da un archivio dati cloud a un altro archivio dati cloud, entrambe le fasi sono potenziate dal runtime di integrazione di Azure, viene addebitato il valore [somma della durata della copia per i passaggi 1 e 2] x [prezzo unitario di copia cloud].
* Quando si usa la gestione temporanea durante una copia ibrida, che copia i dati da un archivio dati locale a un archivio dati cloud, una fase abilitata da un runtime di integrazione self-hosted, viene addebitato il costo di [durata copia ibrida] x [prezzo unitario copia ibrida] + [durata copia cloud] x [prezzo unitario della copia nel cloud].

## <a name="performance-tuning-steps"></a>Procedura di ottimizzazione delle prestazioni

Eseguire questi passaggi per ottimizzare le prestazioni del servizio Azure Data Factory con l'attività di copia.

1. **Stabilire una linea di base.** Durante la fase di sviluppo, testare la pipeline usando l'attività di copia su un campione di dati rappresentativi. Raccogliere i dettagli di esecuzione e le caratteristiche delle prestazioni dopo il [monitoraggio dell'attività di copia](copy-activity-overview.md#monitoring).

2. **Diagnosticare e ottimizzare le prestazioni.** Se le prestazioni osservate non soddisfano le aspettative, identificare i colli di bottiglia delle prestazioni. e quindi ottimizzare le prestazioni per rimuovere o ridurre l'effetto dei colli di bottiglia.

    In alcuni casi, quando si esegue un'attività di copia in Azure Data Factory, viene visualizzato il messaggio "suggerimenti per l'ottimizzazione delle prestazioni" nella parte superiore della [pagina Monitoraggio attività di copia](copy-activity-overview.md#monitor-visually), come illustrato nell'esempio seguente. Il messaggio indica il collo di bottiglia identificato per l'esecuzione della copia specificata. Vengono inoltre illustrati gli elementi da modificare per aumentare la velocità effettiva della copia. I suggerimenti per l'ottimizzazione delle prestazioni offrono attualmente suggerimenti come:

    - Usare la polibase quando si copiano i dati in Azure SQL Data Warehouse.
    - Aumentare Azure Cosmos DB unità richiesta o il database SQL di Azure DTU (unità di velocità effettiva del database) quando la risorsa sul lato archivio dati è il collo di bottiglia.
    - Rimuovere la copia temporanea non necessaria.

    Anche le regole di ottimizzazione delle prestazioni verranno gradualmente migliorate.

    **Esempio: Copia nel database SQL di Azure con suggerimenti per l'ottimizzazione delle prestazioni**

    In questo esempio, durante l'esecuzione di una copia, Azure Data Factory rileva che il database SQL di Azure sink raggiunge un utilizzo elevato di DTU, rallentando le operazioni di scrittura. Il suggerimento consiste nell'aumentare il livello del database SQL di Azure con più DTU. 

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

3. **Espandere la configurazione per l'intero set di dati.** Quando si è soddisfatti dei risultati e delle prestazioni di esecuzione, è possibile espandere la definizione e la pipeline per coprire l'intero set di dati.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Considerazioni sul runtime di integrazione self-hosted

Se l'attività di copia viene eseguita in un runtime di integrazione self-hosted, tenere presente quanto segue:

**Configurazione**: Si consiglia di utilizzare un computer dedicato per ospitare Integration Runtime. Vedere [considerazioni sull'uso del runtime di integrazione self-hosted](concepts-integration-runtime.md).

**Aumentare il numero di istanze**: Un singolo runtime di integrazione self-hosted logico con uno o più nodi può gestire simultaneamente più esecuzioni di attività di copia. Se si ha bisogno di un elevato livello di spostamento di dati ibridi con un numero elevato di esecuzioni di attività di copia simultanee o con un volume elevato di dati da copiare, provare a [scalare il runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md#high-availability-and-scalability) per effettuare il provisioning di più risorse per consentire la copia.

## <a name="considerations-for-the-source"></a>Considerazioni sull'origine

### <a name="general"></a>Generale

Assicurarsi che l'archivio dati sottostante non sia sovraccaricato da altri carichi di lavoro in esecuzione su di esso.

Per gli archivi dati Microsoft, vedere gli argomenti relativi a [monitoraggio e ottimizzazione](#performance-reference) specifici per gli archivi dati. per comprendere meglio le caratteristiche delle prestazioni degli archivi dati e come ridurre al minimo i tempi di risposta e ottimizzare la velocità effettiva.

* Se si copiano dati da un archivio BLOB a SQL Data Warehouse, provare a usare la polibase per migliorare le prestazioni. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Se si copiano dati da HDFS nell'archivio BLOB di Azure o in Azure Data Lake Store, provare a usare DistCp per migliorare le prestazioni. Per altre informazioni, vedere [usare DistCp per copiare dati da HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se si copiano dati da spostamento in Azure SQL Data Warehouse, archiviazione BLob di Azure o Azure Data Lake Store, provare a usare UNLOAD per migliorare le prestazioni. Per altre informazioni, vedere [usare Unload per copiare i dati da Amazon](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Archivi dati basati su file

* **Dimensioni medie dei file e numero medio di file**: L'attività di copia trasferisce i dati di un file alla volta. Con la stessa quantità di dati da spostare, la velocità effettiva generale risulta inferiore se i dati sono costituiti da un numero elevato di file piccoli anziché da pochi file di grandi dimensioni. Ciò è dovuto alla fase di bootstrap necessaria per ogni file. Se possibile, combinare piccoli file in file più grandi per ottenere una velocità effettiva più elevata.
* **Formato di file e compressione**: per altre informazioni su come migliorare le prestazioni, vedere le sezioni [Considerazioni sulla serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization) e [Considerazioni sulla compressione](#considerations-for-compression).

### <a name="relational-data-stores"></a>Archivi dati relazionali

* **Modello di dati**: Lo schema di tabella influisce sulla velocità effettiva di copia. Una dimensione di riga elevata garantisce prestazioni migliori rispetto a dimensioni di riga ridotte per la copia della stessa quantità di dati. Questo perché il database è in grado di recuperare in modo più efficiente un minor numero di batch di dati che contengono meno righe.
* **Query o stored procedure**: Ottimizzare la logica della query o stored procedure specificata nell'origine dell'attività di copia per recuperare i dati in modo più efficiente.

## <a name="considerations-for-the-sink"></a>Considerazioni sul sink

### <a name="general"></a>Generale

Assicurarsi che l'archivio dati sottostante non sia sovraccaricato da altri carichi di lavoro in esecuzione su di esso.

Per gli archivi dati Microsoft, vedere gli argomenti relativi a [monitoraggio e ottimizzazione](#performance-reference) specifici per gli archivi dati. per comprendere meglio le caratteristiche delle prestazioni degli archivi dati e come ridurre al minimo i tempi di risposta e ottimizzare la velocità effettiva.

* Se si copiano dati da un archivio dati a Azure SQL Data Warehouse, provare a usare la polibase per migliorare le prestazioni. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Se si copiano dati da HDFS nell'archivio BLOB di Azure o in Azure Data Lake Store, provare a usare DistCp per migliorare le prestazioni. Per altre informazioni, vedere [usare DistCp per copiare dati da HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se si copiano dati da spostamento in Azure SQL Data Warehouse, archiviazione BLOB di Azure o Azure Data Lake Store, provare a usare UNLOAD per migliorare le prestazioni. Per altre informazioni, vedere [usare Unload per copiare i dati da Amazon](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Archivi dati basati su file

* **Comportamento di copia**: Se si copiano dati da un archivio dati diverso basato su file, l'attività di copia include tre opzioni tramite la proprietà **copyBehavior** . mantenere la gerarchia, rendere flat la gerarchia e unire i file. Conservare o rendere flat la gerarchia comporta un overhead delle prestazioni minimo, mentre unire i file provoca un aumento dell'overhead delle prestazioni.
* **Formato di file e compressione**: per altre informazioni su come migliorare le prestazioni, vedere le sezioni [Considerazioni sulla serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization) e [Considerazioni sulla compressione](#considerations-for-compression).

### <a name="relational-data-stores"></a>Archivi dati relazionali

* **Comportamento di copia e implicazione delle prestazioni**: Esistono diversi modi per scrivere i dati in un sink SQL. Per altre informazioni, vedere [la procedura consigliata per il caricamento dei dati nel database SQL di Azure](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Modello di dati e dimensioni batch**:
  * Lo schema di tabella influisce sulla velocità effettiva di copia. Per copiare la stessa quantità di dati, dimensioni di riga grandi offrono prestazioni migliori rispetto a dimensioni di riga piccole, perché il database può eseguire in modo più efficiente il commit di un numero inferiore di batch di dati.
  * L'attività di copia inserisce i dati in una serie di batch. Per impostare il numero di righe in un batch è possibile usare la proprietà **writeBatchSize** . Se le righe dei dati sono di piccole dimensioni, è possibile impostare la proprietà **writeBatchSize** con un valore più elevato per sfruttare l'overhead di un numero minore di batch e aumentare la velocità effettiva. Se le righe sono di grandi dimensioni, prestare attenzione quando si aumenta il valore di **writeBatchSize**. Un valore elevato può causare un errore di copia dovuto a un sovraccarico del database.

### <a name="nosql-stores"></a>Archivi NoSQL

* Per gli **archivi tabelle**:
  * **Partizione**: scrivere i dati nelle partizioni con interleave riduce drasticamente le prestazioni. Ordinare i dati di origine in base alla chiave di partizione in modo che i dati vengano inseriti in modo efficiente in una partizione dopo l'altra. In alternativa, è possibile modificare la logica per scrivere i dati in una singola partizione.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerazioni sulla serializzazione e deserializzazione

La serializzazione e la deserializzazione possono verificarsi quando il set di dati di input o di output è un file. Per ulteriori informazioni sui formati di file supportati dall'attività di copia, vedere [formati di compressione e file supportati](supported-file-formats-and-compression-codecs.md).

**Comportamento di copia**:

* Copia di file tra archivi dati basati su file:
  * Quando i set di dati di input e di output hanno entrambe le stesse impostazioni o nessun formato di file, il servizio di spostamento dati esegue una *copia binaria* senza alcuna serializzazione o deserializzazione. La velocità effettiva è superiore rispetto allo scenario, in cui le impostazioni del formato di file di origine e del sink sono diverse tra loro.
  * Quando i set di dati di input e di output sono entrambi in formato testo e solo il tipo di codifica è diverso, il servizio di spostamento dati esegue solo la conversione della codifica. Non esegue alcuna operazione di serializzazione o deserializzazione e questo dà luogo a un certo overhead delle prestazioni rispetto alla copia binaria.
  * Quando i set di dati di input e di output hanno formati di file diversi o configurazioni diverse, ad esempio i delimitatori, il servizio di spostamento dei dati deserializza i dati di origine in flusso, li trasforma e li serializza nel formato di output indicato. Questa operazione comporta un overhead delle prestazioni decisamente maggiore rispetto ad altri scenari.
* Quando si copiano file in o da un archivio dati che non è basato su file, ad esempio da un archivio basato su file a un archivio relazionale, è necessario eseguire il passaggio di serializzazione o deserializzazione. Questo passaggio comporta un notevole overhead delle prestazioni.

**Formato di file**: il formato di file scelto può influire sulle prestazioni di copia. Ad esempio, Avro è un formato binario compresso che archivia i metadati con i dati. È ampiamente supportato nell'ecosistema di Hadoop per l'elaborazione e l'esecuzione di query. Avro è più costoso per la serializzazione e la deserializzazione, con conseguente riduzione della velocità effettiva di copia rispetto al formato testo. 

Scegliere il formato di file per tutto il flusso di elaborazione a livello globale. Inizia con:

- Il modulo in cui vengono archiviati i dati, gli archivi dati di origine o da estrarre da sistemi esterni.
- Il formato migliore per l'archiviazione, l'elaborazione analitica e l'esecuzione di query.
- Il formato in cui i dati devono essere esportati nei data mart per gli strumenti di creazione di report e visualizzazione.

A volte un formato di file non ottimale dal punto di vista delle prestazioni di lettura e scrittura può invece essere una buona scelta dal punto di vista del processo analitico generale.

## <a name="considerations-for-compression"></a>Considerazioni sulla compressione

Quando il set di dati di input o di output è un file, è possibile impostare l'attività di copia per eseguire la compressione o la decompressione mentre scrive i dati nella destinazione. La scelta della compressione comporta un compromesso tra input/output (I/O) e CPU. La compressione dei dati ha un costo maggiore in termini di risorse di calcolo, ma riduce l'I/O di rete e l'archiviazione. A seconda dei dati, è possibile che venga visualizzato un aumento della velocità effettiva complessiva della copia.

**Codec**: Ogni codec di compressione presenta dei vantaggi. Ad esempio, bzip2 ha la velocità effettiva copia più bassa, ma offre prestazioni di query Hive migliori perché permette di dividerle per l'elaborazione. Gzip è l'opzione più bilanciata e viene usata più spesso. Scegliere il codec più adatto allo scenario end-to-end personalizzato.

**Livello**: per ogni codec di compressione è possibile scegliere tra due opzioni, la compressione più veloce e la compressione ottimale. L'opzione compressa più veloce comprime i dati il più rapidamente possibile, anche se il file risultante non viene compresso in modo ottimale. L'opzione di compressione ottimale impiega più tempo per la compressione e restituisce una quantità minima di dati. È possibile testare entrambe le opzioni per verificare quale offra le migliori prestazioni complessive in base alle proprie esigenze.

**Una considerazione**: per copiare una grande quantità di dati tra un archivio locale e il cloud, è consigliabile usare una [copia di staging](#staged-copy) con compressione abilitata. L'uso dell'archiviazione provvisoria è utile quando la larghezza di banda della rete aziendale e dei servizi di Azure è il fattore di limitazione e si vuole che il set di dati di input e il set di dati di output siano in formato non compresso.

## <a name="considerations-for-column-mapping"></a>Considerazioni sul mapping di colonne

È possibile impostare la proprietà **ColumnMappings** in un'attività di copia per eseguire il mapping di tutte le colonne di input o di un subset alle colonne di output. Dopo aver letto i dati dall'origine, il servizio di spostamento dati deve eseguire il mapping delle colonne sui dati prima di scriverli nel sink. Questa ulteriore elaborazione riduce la velocità effettiva di copia.

Se l'archivio dati di origine è disponibile per query, ad esempio nel caso di un archivio relazionale come il database SQL o SQL Server, oppure nel caso di un archivio NoSQL come un archivio tabelle o Azure Cosmos DB, è consigliabile eseguire il push della logica di filtro e riordinamento colonne per la proprietà **query** anziché usare il mapping colonne. In questo modo, la proiezione si verifica quando il servizio di spostamento dei dati legge i dati dall'archivio dati di origine, dove è molto più efficiente.

Scopri di più sul [mapping dello schema dell'attività di copia](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Altre considerazioni

Se le dimensioni dei dati da copiare sono elevate, è possibile modificare la logica di business per partizionare ulteriormente i dati. È possibile pianificare l'attività di copia in modo che venga eseguita più frequentemente per ridurre le dimensioni dei dati per ogni attività di copia eseguita.

Prestare attenzione al numero di set di dati e alle attività di copia che richiedono Azure Data Factory per connettersi allo stesso archivio dati nello stesso momento. Molti processi di copia simultanei possono limitare un archivio dati e causare un peggioramento delle prestazioni, nuovi tentativi interni dei processi di copia e, in alcuni casi, errori di esecuzione.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Scenario di esempio: Copiare da un'istanza di SQL Server locale all'archiviazione BLOB

**Scenario**: Viene creata una pipeline per copiare i dati da un'istanza di SQL Server locale all'archiviazione BLOB in formato CSV. Per velocizzare il processo di copia, i file CSV devono essere compressi in formato bzip2.

**Test e analisi**: La velocità effettiva dell'attività di copia è inferiore a 2 MBps, che è molto più lenta rispetto al benchmark delle prestazioni.

**Analisi e ottimizzazione delle prestazioni**: per risolvere il problema delle prestazioni, occorre vedere prima di tutto come vengono elaborati e spostati i dati.

- **Lettura dei dati**: Il runtime di integrazione apre una connessione a SQL Server e Invia la query. SQL Server risponde inviando il flusso di dati al runtime di integrazione tramite Intranet.
- **Serializzazione e compressione dei dati**: Il runtime di integrazione serializza il flusso di dati in formato CSV e comprime i dati in un flusso bzip2.
- **Scrittura dei dati**: Il runtime di integrazione carica il flusso bzip2 nell'archivio BLOB tramite Internet.

Come si può notare, i dati vengono elaborati e spostati in modo sequenziale: SQL Server > LAN > Runtime di integrazione > WAN > Archiviazione BLOB. Le prestazioni complessive sono gestite dalla velocità effettiva minima sulla pipeline.

![Flusso di dati](./media/copy-activity-performance/case-study-pic-1.png)

Uno o più dei fattori seguenti possono provocare un collo di bottiglia nelle prestazioni:

* **Origine**: SQL Server ha di per sé una velocità effettiva bassa a causa dei carichi elevati.
* **Runtime di integrazione self-hosted**:
  * **LAN**: il runtime di integrazione è distante da SQL Server con una connessione a larghezza di banda bassa.
  * **Runtime di integrazione**: il runtime di integrazione ha raggiunto i relativi limiti di carico per eseguire queste operazioni:
    * **Serializzazione**: la serializzazione del flusso di dati in formato CSV ha una velocità effettiva bassa.
    * **Compressione**: Si è scelto un codec di compressione lenta, ad esempio bzip2, che è 2,8 MBps con Core i7.
  * **WAN**: La larghezza di banda tra la rete aziendale e i servizi di Azure è bassa, ad esempio T1 = 1.544 Kbps; T2 = 6.312 Kbps.
* **Sink**: l'archivio BLOB ha una velocità effettiva bassa. Questo scenario è improbabile perché il contratto di servizio (SLA) garantisce un minimo di 60 MBps.

In tal caso, la compressione dati bzip2 potrebbe rallentare l'intera pipeline. Il passaggio al codec di compressione gzip può ridurre questo collo di bottiglia.

## <a name="references"></a>Riferimenti

Di seguito sono riportati alcuni riferimenti sul monitoraggio e l'ottimizzazione delle prestazioni per alcuni degli archivi dati supportati:

* Archiviazione di Azure, che include archiviazione BLOB e archiviazione tabelle: [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../storage/common/storage-scalability-targets.md) ed [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../storage/common/storage-performance-checklist.md).
* Database SQL di Azure: È possibile [monitorare le prestazioni](../sql-database/sql-database-single-database-monitor.md) e controllare la percentuale di unità di transazione di database (DTU).
* Azure SQL Data Warehouse: La relativa funzionalità viene misurata in unità data warehouse (DWU). Vedere [gestire la potenza di calcolo in Azure SQL data warehouse (panoramica)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [Livelli di prestazioni in Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server locale: [Monitorare e ottimizzare le prestazioni](https://msdn.microsoft.com/library/ms189081.aspx).
* File server locale: [Ottimizzazione delle prestazioni per i file server](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli sull'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Mapping dello schema dell'attività di copia](copy-activity-schema-and-type-mapping.md)
- [Tolleranza di errore dell'attività di copia](copy-activity-fault-tolerance.md)
