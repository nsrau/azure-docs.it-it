---
title: Funzionalità di ottimizzazione delle prestazioni dell'attività di copia
description: Informazioni sulle funzionalità chiave che consentono di ottimizzare le prestazioni dell'attività di copia in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 45cecccd88b0b84b478bc6fc7346cb9ef9c2f454
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87846344"
---
# <a name="copy-activity-performance-optimization-features"></a>Funzionalità di ottimizzazione delle prestazioni dell'attività di copia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In questo articolo vengono descritte le funzionalità di ottimizzazione delle prestazioni dell'attività di copia che è possibile utilizzare in Azure Data Factory.

## <a name="data-integration-units"></a>Unità di integrazione dati

Un'unità di integrazione dati è una misura che rappresenta la potenza, ovvero una combinazione di CPU, memoria e allocazione di risorse di rete, di una singola unità in Azure Data Factory. L'unità di integrazione dati si applica solo al [runtime di integrazione di Azure](concepts-integration-runtime.md#azure-integration-runtime), ma non al runtime di [integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime).

Il DIUs consentito per consentire l'esecuzione di un'attività di copia è **compreso tra 2 e 256**. Se non è specificato o se si sceglie "auto" nell'interfaccia utente, Data Factory applica in modo dinamico l'impostazione ottimale di DIU in base alla coppia di sink di origine e al modello di dati. Nella tabella seguente sono elencati gli intervalli di DIU supportati e il comportamento predefinito in diversi scenari di copia:

| Scenario di copia | Intervallo di DIU supportato | Numero di unità di integrazione dati predefinite determinato dal servizio |
|:--- |:--- |---- |
| Tra archivi di file |- **Copia da o a file singolo**: 2-4 <br>- **Copia da e in più file**: 2-256 in base al numero e alle dimensioni dei file <br><br>Ad esempio, se si copiano dati da una cartella con 4 file di grandi dimensioni e si sceglie di mantenere la gerarchia, il valore massimo di DIU effettivo è 16; Quando si sceglie di eseguire il merge del file, il numero massimo di DIU effettivi è 4. |Compreso tra 4 e 32 in base al numero e alle dimensioni dei file |
| Da archivio file a archivio non file |- **Copia da file singolo**: 2-4 <br/>- **Copia da più file**: 2-256 in base al numero e alle dimensioni dei file <br/><br/>Ad esempio, se si copiano dati da una cartella con 4 file di grandi dimensioni, il valore massimo di DIU effettivo è 16. |- **Copiare nel database SQL di Azure o in Azure Cosmos DB**: tra 4 e 16 a seconda del livello di sink (DTU/UR) e del modello di file di origine<br>- **Copiare in Azure sinapsi Analytics** usando l'istruzione di base o di copia: 2<br>-Altro scenario: 4 |
| Da archivio non file ad archivio file |- **Copiare da archivi dati abilitati** per le partizioni (incluso [il database SQL di Azure](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL istanza gestita](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure sinapsi Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)e [Teradata](connector-teradata.md#teradata-as-source)): 2-256 durante la scrittura in una cartella e 2-4 durante la scrittura in un singolo file. Nota per partizione dati di origine può utilizzare fino a 4 DIUs.<br>- **Altri scenari**: 2-4 |- **Copia da Rest o http**: 1<br/>- **Copia da Amazon per** l'uso con unload: 2<br>- **Altro scenario**: 4 |
| Tra archivi non di file |- **Copiare da archivi dati abilitati** per le partizioni (incluso [il database SQL di Azure](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL istanza gestita](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure sinapsi Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)e [Teradata](connector-teradata.md#teradata-as-source)): 2-256 durante la scrittura in una cartella e 2-4 durante la scrittura in un singolo file. Nota per partizione dati di origine può utilizzare fino a 4 DIUs.<br/>- **Altri scenari**: 2-4 |- **Copia da Rest o http**: 1<br>- **Altro scenario**: 4 |

È possibile visualizzare i DIUs usati per ogni copia eseguita nella visualizzazione monitoraggio dell'attività di copia o nell'output dell'attività. Per altre informazioni, vedere [monitoraggio dell'attività di copia](copy-activity-monitoring.md). Per eseguire l'override di questa impostazione predefinita, specificare un valore per la `dataIntegrationUnits` proprietà come indicato di seguito. Il *numero effettivo di unità di integrazione dati* usate dall'operazione di copia in fase di esecuzione è minore o uguale al valore configurato, a seconda del modello di dati.

Ti verrà addebitato il **numero di \* \* unità di durata copia DIUs di utilizzo** Vedi i prezzi correnti [qui](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). È possibile applicare la valuta locale e la separazione separata per ogni tipo di sottoscrizione.

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
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Scalabilità del runtime di integrazione self-hosted

Per ottenere una velocità effettiva più elevata, è possibile scalare verticalmente il runtime di integrazione self-hosted:

- Se la CPU e la memoria disponibile nel nodo IR self-hosted non sono completamente utilizzate, ma l'esecuzione dei processi simultanei raggiunge il limite, è necessario aumentare il numero di processi simultanei in un nodo.  Per istruzioni, vedere [qui](create-self-hosted-integration-runtime.md#scale-up) .
- Se invece la CPU è elevata sul nodo IR indipendente o se la memoria disponibile è insufficiente, è possibile aggiungere un nuovo nodo per aumentare la scalabilità orizzontale del carico tra più nodi.  Per istruzioni, vedere [qui](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

Nota negli scenari seguenti, l'esecuzione dell'attività di copia singola può sfruttare più nodi IR indipendenti:

- Copiare i dati da archivi basati su file, a seconda del numero e delle dimensioni dei file.
- Copiare dati da un archivio dati abilitato per le opzioni di partizione (inclusi [database SQL di Azure](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL istanza gestita](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure sinapsi Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)e [Teradata](connector-teradata.md#teradata-as-source)), a seconda del numero di partizioni di dati.

## <a name="parallel-copy"></a>Copia parallela

È possibile impostare la copia parallela ( `parallelCopies` proprietà) sull'attività di copia per indicare il parallelismo che si desidera venga utilizzato dall'attività di copia. È possibile considerare questa proprietà come il numero massimo di thread all'interno dell'attività di copia che leggono dall'origine o scrivono negli archivi dati sink in parallelo.

La copia parallela è ortogonale a [unità di integrazione dati](#data-integration-units) o a [nodi IR indipendenti](#self-hosted-integration-runtime-scalability). Viene conteggiato in tutti i nodi IR DIUs o self-hosted.

Per impostazione predefinita, per ogni esecuzione dell'attività di copia Azure Data Factory applica in modo dinamico l'impostazione di copia parallela ottimale basata sulla coppia di sink di origine e sul modello di dati. 

> [!TIP]
> Il comportamento predefinito della copia parallela in genere fornisce la velocità effettiva migliore, che è determinata automaticamente da ADF in base alla coppia di sink di origine, al modello di dati e al numero di DIUs o al conteggio della CPU/memoria/nodo del runtime di integrazione self-hosted. Vedere [risolvere i problemi relativi alle prestazioni dell'attività di copia](copy-activity-performance-troubleshooting.md) in quando ottimizzare la copia parallela.

La tabella seguente elenca il comportamento di copia parallela:

| Scenario di copia | Comportamento di copia parallela |
| --- | --- |
| Tra archivi di file | `parallelCopies`determina il parallelismo **a livello di file**. La suddivisione in blocchi all'interno di ogni file avviene in modo automatico e trasparente. È progettato per usare le dimensioni del blocco più adatte per un determinato tipo di archivio dati per caricare i dati in parallelo. <br/><br/>Il numero effettivo di copie parallele utilizzate dall'attività di copia in fase di esecuzione non è maggiore del numero di file disponibili. Se il comportamento di copia è **mergeFile** nel sink di file, l'attività di copia non può sfruttare il parallelismo a livello di file. |
| Da archivio file a archivio non file | -Quando si copiano dati in un database SQL di Azure o in Azure Cosmos DB, la copia parallela predefinita dipende anche dal livello di sink (numero di DTU/UR).<br>-Quando si copiano dati in una tabella di Azure, la copia parallela predefinita è 4. |
| Da archivio non file ad archivio file | -Quando si copiano dati da un archivio dati abilitato per le opzioni di partizione (incluso il [database SQL di Azure](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL istanza gestita](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure sinapsi Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)e [Teradata](connector-teradata.md#teradata-as-source)), la copia parallela predefinita è 4. Il numero effettivo di copie parallele utilizzate dall'attività di copia in fase di esecuzione non è superiore al numero di partizioni di dati disponibili. Quando si usa Integration Runtime self-hosted e si esegue la copia in BLOB/ADLS Gen2 di Azure, si noti che la copia parallela effettiva massima è 4 o 5 per nodo IR.<br>-Per altri scenari, la copia parallela non ha effetto. Anche se il parallelismo viene specificato, non viene applicato. |
| Tra archivi non di file | -Quando si copiano dati in un database SQL di Azure o in Azure Cosmos DB, la copia parallela predefinita dipende anche dal livello di sink (numero di DTU/UR).<br/>-Quando si copiano dati da un archivio dati abilitato per le opzioni di partizione (incluso il [database SQL di Azure](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL istanza gestita](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure sinapsi Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)e [Teradata](connector-teradata.md#teradata-as-source)), la copia parallela predefinita è 4.<br>-Quando si copiano dati in una tabella di Azure, la copia parallela predefinita è 4. |

Per controllare il carico sui computer che ospitano gli archivi dati o per ottimizzare le prestazioni di copia, è possibile eseguire l'override del valore predefinito e specificare un valore per la `parallelCopies` Proprietà. Il valore deve essere un numero intero maggiore o uguale a 1. In fase di esecuzione, per ottenere prestazioni ottimali, l'attività di copia utilizza un valore minore o uguale al valore impostato.

Quando si specifica un valore per la `parallelCopies` proprietà, prendere in considerazione l'aumento del carico per gli archivi dati di origine e sink. Prendere in considerazione anche l'aumento del carico per il runtime di integrazione self-hosted se l'attività di copia è abilitata. Questo aumento del carico si verifica soprattutto quando si hanno più attività o esecuzioni simultanee delle stesse attività eseguite nello stesso archivio dati. Se si nota che l'archivio dati o il runtime di integrazione self-hosted è sovraccarico del carico, ridurre il `parallelCopies` valore per alleviare il carico.

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
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>copia di staging

Quando si copiano dati da un archivio dati di origine a un archivio dati sink, è possibile scegliere di usare un archivio BLOB come archivio di staging provvisorio. La funzionalità di staging è particolarmente utile nei casi seguenti:

- **Si vuole inserire dati da diversi archivi dati in Azure sinapsi Analytics (in precedenza SQL Data Warehouse) tramite la polibase.** Azure sinapsi Analytics usa la polibase come meccanismo di velocità effettiva elevata per caricare una grande quantità di dati in Azure sinapsi Analytics. I dati di origine devono trovarsi in un archivio BLOB o Azure Data Lake Store e devono soddisfare criteri aggiuntivi. Quando si caricano dati da un archivio dati non BLOB o Azure Data Lake Store, è possibile attivare la copia di dati tramite un'archiviazione BLOB di staging provvisoria. In tal caso, Azure Data Factory esegue le trasformazioni dei dati necessarie per verificare che soddisfi i requisiti di polibase. USA quindi la polibase per caricare i dati in Azure sinapsi Analytics in modo efficiente. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **A volte è necessario un po' di tempo per eseguire uno spostamento dati ibrido, ovvero per copiare da un archivio dati locale a un archivio dati cloud, su una connessione di rete lenta.** Per migliorare le prestazioni, è possibile usare la copia di staging per comprimere i dati in locale, in modo che sia necessario meno tempo per spostare i dati nell'archivio dati di staging nel cloud. Sarà quindi possibile decomprimere i dati nell'archivio di staging prima di caricarli nell'archivio dati di destinazione.
- **Non è necessario aprire porte diverse dalla porta 80 e dalla porta 443 nel firewall a causa dei criteri IT aziendali.** Ad esempio, quando si copiano dati da un archivio dati locale a un sink di database SQL di Azure o a un sink di Azure sinapsi Analytics, è necessario attivare la comunicazione TCP in uscita sulla porta 1433 per Windows Firewall e il firewall aziendale. In questo scenario, la copia di staging può trarre vantaggio dal runtime di integrazione self-hosted per prima cosa copiare i dati in un'istanza di staging dell'archiviazione BLOB tramite HTTP o HTTPS sulla porta 443. Quindi, può caricare i dati nel database SQL o in analisi sinapsi di Azure dalla gestione temporanea dell'archiviazione BLOB. In questo flusso non è necessario abilitare la porta 1433.

### <a name="how-staged-copy-works"></a>Come funziona la copia di staging

Quando si attiva la funzionalità di staging, i dati vengono prima copiati dall'archivio dati di origine nell'archivio BLOB di staging personale. Successivamente, vengono copiati dall'archivio dati di staging nell'archivio dati sink. Azure Data Factory gestisce automaticamente il flusso a due fasi. Azure Data Factory pulisce anche i dati temporanei dall'archiviazione di staging dopo il completamento dello spostamento dei dati.

![copia di staging](media/copy-activity-performance/staged-copy.png)

Quando si attiva lo spostamento dei dati tramite un archivio di staging, è possibile specificare se si desidera che i dati vengano compressi prima di spostare i dati dall'archivio dati di origine a un archivio dati provvisorio o di staging e quindi decompressi prima di spostare i dati da un archivio dati provvisorio o di staging all'archivio dati sink.

Attualmente, non è possibile copiare i dati tra due archivi dati connessi tramite un altro IRs indipendente, né con la copia temporanea. Per questo scenario, è possibile configurare due attività di copia concatenate in modo esplicito per la copia dall'origine alla gestione temporanea, quindi da staging a sink.

### <a name="configuration"></a>Configurazione

Configurare l'impostazione **enableStaging** nell'attività di copia per specificare se si desidera che i dati vengano gestiti temporaneamente nell'archivio BLOB prima di caricarli in un archivio dati di destinazione. Quando si imposta **enableStaging** su `TRUE` , specificare le proprietà aggiuntive elencate nella tabella seguente. È anche necessario creare un servizio collegato di archiviazione di Azure o di archiviazione con firma di accesso condiviso per la gestione temporanea, se non è già presente.

| Proprietà | Descrizione | Valore predefinito | Obbligatoria |
| --- | --- | --- | --- |
| enableStaging |Specificare se si vuole copiare i dati tramite un archivio di staging provvisorio. |False |No |
| linkedServiceName |Specificare il nome di un servizio collegato [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) che fa riferimento all'istanza di archiviazione usata come archivio di staging provvisorio. <br/><br/> Non è possibile usare l'archiviazione con una firma di accesso condiviso per caricare i dati in Azure sinapsi Analytics tramite la polibase. Può essere usata in tutti gli altri scenari. |N/D |Sì, quando **enableStaging** è impostato su TRUE |
| path |Specificare il percorso dell'archivio BLOB che deve contenere i dati di staging. Se non si specifica un percorso, il servizio crea un contenitore per archiviare i dati temporanei. <br/><br/>  Specificare un percorso solo se si usa l'archiviazione con una firma di accesso condiviso o se i dati temporanei devono trovarsi in un percorso specifico. |N/D |No |
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
* Quando si usa la gestione temporanea durante una copia ibrida, che copia i dati da un archivio dati locale a un archivio dati cloud, una fase abilitata da un runtime di integrazione self-hosted, viene addebitato il costo di [durata copia ibrida] x [prezzo unitario copia ibrida] + [durata copia cloud] x [prezzo unitario copia cloud].

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli relativi all'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Guida alla scalabilità e alle prestazioni dell'attività di copia](copy-activity-performance.md)
- [Risolvere i problemi delle prestazioni dell'attività di copia](copy-activity-performance-troubleshooting.md)
- [Usare Azure Data Factory per migrare i dati dal data Lake o da data warehouse ad Azure](data-migration-guidance-overview.md)
- [Eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure](data-migration-guidance-s3-azure-storage.md)