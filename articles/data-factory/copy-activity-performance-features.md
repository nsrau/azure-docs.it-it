---
title: Funzionalità di ottimizzazione delle prestazioni dell'attività di copia
description: Informazioni sulle funzionalità principali che consentono di ottimizzare le prestazioni dell'attività di copia in Azure Data Factory.
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
ms.date: 03/09/2020
ms.openlocfilehash: d37b4648c0a37f16fe5c9d8794bd78417c5780ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257887"
---
# <a name="copy-activity-performance-optimization-features"></a>Funzionalità di ottimizzazione delle prestazioni dell'attività di copia

Questo articolo descrive le funzionalità di ottimizzazione delle prestazioni dell'attività di copia che è possibile sfruttare in Azure Data Factory.This article outlines the copy activity performance optimization features that you can leverage in Azure Data Factory.

## <a name="data-integration-units"></a>Unità di integrazione dati

A Data Integration Unit is a measure that represents the power (a combination of CPU, memory, and network resource allocation) of a single unit in Azure Data Factory. L'unità di [integrazione](concepts-integration-runtime.md#azure-integration-runtime)dei dati si applica solo al runtime di integrazione di Azure, ma non al runtime di [integrazione self-hosted.](concepts-integration-runtime.md#self-hosted-integration-runtime)

Le DII consentite per consentire l'esecuzione di un'attività di copia **sono comprese tra 2 e 256.** Se non viene specificato o si sceglie "Auto" nell'interfaccia utente, Data Factory applica dinamicamente l'impostazione DIU ottimale in base alla coppia di sink di origine e al modello di dati. Nella tabella seguente sono elencati gli intervalli DIU supportati e il comportamento predefinito in diversi scenari di copia:The following table lists the supported DIU ranges and default behavior in different copy scenarios:

| Scenario di copia | Gamma DIU supportata | Numero di unità di integrazione dati predefinite determinato dal servizio |
|:--- |:--- |---- |
| Tra archivi di file |- **Copia da o su un singolo file**: 2-4 <br>- **Copia da e in più file**: 2-256 a seconda del numero e delle dimensioni dei file <br><br>Ad esempio, se si copiano dati da una cartella con 4 file di grandi dimensioni e si sceglie di mantenere la gerarchia, il DIU effettivo massimo è 16; quando si sceglie di unire il file, il DIU effettivo massimo è 4. |Tra 4 e 32 a seconda del numero e delle dimensioni dei file |
| Dall'archivio file all'archivio non file |- **Copia da file singolo**: 2-4 <br/>- **Copia da più file**: 2-256 a seconda del numero e delle dimensioni dei file <br/><br/>Ad esempio, se si copiano dati da una cartella con 4 file di grandi dimensioni, il diU effettivo massimo è 16.For example, if you copy data from a folder with 4 large files, the max effective DIU is 16. |- **Copia nel database SQL**di Azure o nel database cosmo di Azure: tra 4 e 16 a seconda del livello sink (DKU/RU) e del modello di file di origine<br>- **Copia in Azure Synapse Analytics** usando PolyBase o l'istruzione COPY: 2Copy into Azure Synapse Analytics using PolyBase or COPY statement: 2<br>- Altro scenario: 4 |
| Dall'archivio non file all'archivio file |- **Copia da archivi dati abilitati per le opzioni** di partizione (incluso [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 durante la scrittura in una cartella e 2-4 quando si scrive in un unico file. Nota: per partizione di dati di origine può utilizzare fino a 4 DISu.<br>- **Altri scenari**: 2-4 |- **Copia da REST o HTTP**: 1<br/>- **Copia da Amazon Redshift** utilizzando UNLOAD: 2<br>- **Altro scenario**: 4 |
| Tra archivi non file |- **Copia da archivi dati abilitati per le opzioni** di partizione (incluso [Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)): 2-256 durante la scrittura in una cartella e 2-4 quando si scrive in un unico file. Nota: per partizione di dati di origine può utilizzare fino a 4 DISu.<br/>- **Altri scenari**: 2-4 |- **Copia da REST o HTTP**: 1<br>- **Altro scenario**: 4 |

È possibile visualizzare le DIU usate per ogni esecuzione della copia nella vista di monitoraggio dell'attività di copia o nell'output dell'attività. Per ulteriori informazioni, vedere [Monitoraggio dell'attività di copia](copy-activity-monitoring.md). Per eseguire l'override di `dataIntegrationUnits` questo valore predefinito, specificare un valore per la proprietà come indicato di seguito. Il *numero effettivo di unità di integrazione dati* usate dall'operazione di copia in fase di esecuzione è minore o uguale al valore configurato, a seconda del modello di dati.

Ti verrà addebitato **il \* numero \* di DIU usati copia durata prezzo unitario/DIU-ora**. Vedi i prezzi attuali [qui](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). Per ogni tipo di abbonamento possono essere applicati sconti separati e sconti separati.

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

Se si desidera ottenere una velocità effettiva più elevata, è possibile aumentare o ridurre il sistema di recapito self-hosted:If you would achieve higher throughput, you can either scale up or scale out the Self-hosted IR:

- Se la CPU e la memoria disponibile nel nodo ARi self-hosted non sono completamente utilizzate, ma l'esecuzione di processi simultanei raggiunge il limite, è necessario aumentare il numero di processi simultanei che possono essere eseguiti su un nodo.  Vedere [qui](create-self-hosted-integration-runtime.md#scale-up) per istruzioni.
- Se, d'altra parte, la CPU è elevata sul nodo ARi self-hosted o la memoria disponibile è insufficiente, è possibile aggiungere un nuovo nodo per aumentare il carico tra più nodi.  Vedere [qui](create-self-hosted-integration-runtime.md#high-availability-and-scalability) per istruzioni.

Si noti che negli scenari seguenti, l'esecuzione di un'attività di copia singola può sfruttare più nodi iraerizzati:Note in the following scenarios, single copy activity execution can leverage multiple Self-hosted IR nodes:

- Copiare i dati dagli archivi basati su file, a seconda del numero e delle dimensioni dei file.
- Copiare i dati dall'archivio dati abilitato per partition-option (inclusi [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)e SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), a seconda del numero di partizioni di dati.

## <a name="parallel-copy"></a>Copia parallela

È possibile impostare`parallelCopies` la copia parallela ( proprietà) nell'attività di copia per indicare il parallelismo che si desidera venga utilizzato dall'attività di copia. È possibile considerare questa proprietà come il numero massimo di thread all'interno dell'attività di copia che leggono dall'origine o scrivere negli archivi dati sink in parallelo.

La copia parallela è ortogonale alle unità di [integrazione dei dati](#data-integration-units) o [ai nodi a distribuzione automatica](#self-hosted-integration-runtime-scalability). Viene conteggiato in tutti i diU o nei nodi irregistrati self-hosted.

Per ogni attività di copia eseguita, per impostazione predefinita Azure Data Factory applica dinamicamente l'impostazione di copia parallela ottimale in base alla coppia di sink di origine e al modello di dati. 

> [!TIP]
> Il comportamento predefinito della copia parallela offre in genere la velocità effettiva migliore, che viene determinata automaticamente da ADF in base alla coppia di sink di origine, al modello di dati e al numero di DIU o al numero di CPU/memoria/nodo del componente IR self-hosted. Fare riferimento a Risolvere i problemi relativi alle [prestazioni dell'attività](copy-activity-performance-troubleshooting.md) di copia su quando ottimizzare la copia parallela.

Nella tabella seguente viene elencato il comportamento della copia parallela:The following table lists the parallel copy behavior:

| Scenario di copia | Comportamento della copia parallela |
| --- | --- |
| Tra archivi di file | `parallelCopies`determina il parallelismo **a livello**di file . La suddivisione in blocchi all'interno di ogni file avviene automaticamente e in modo trasparente. È progettato per utilizzare le dimensioni di blocco più adatte per un determinato tipo di archivio dati per caricare i dati in parallelo. <br/><br/>Il numero effettivo di copie parallele utilizzate dall'attività di copia in fase di esecuzione non è superiore al numero di file disponibili. Se il comportamento di copia è **mergeFile** nel sink di file, l'attività di copia non può sfruttare il parallelismo a livello di file. |
| Dall'archivio file all'archivio non file | - Quando si copiano dati nel database SQL di Azure o nel database Cosmos di Azure, la copia parallela predefinita dipende anche dal livello sink (numero di DCU/RU).<br>- Quando si copiano dati in Tabella di Azure, la copia parallela predefinita è 4.- When copying data into Azure Table, default parallel copy is 4. |
| Dall'archivio non file all'archivio file | - Quando si copiano dati dall'archivio dati abilitato per partition-option (inclusi [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)e SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), la copia parallela predefinita è 4. Il numero effettivo di copie parallele utilizzate dall'attività di copia in fase di esecuzione non è superiore al numero di partizioni di dati disponibili. Quando si usa Il runtime di integrazione self-hosted e la copia in BLOB di Azure/ADLS Gen2, tenere presente che la copia parallela validità massima è 4 o 5 per ogni nodo iR.<br>- Per altri scenari, la copia parallela non ha effetto. Anche se viene specificato il parallelismo, non viene applicato. |
| Tra archivi non file | - Quando si copiano dati nel database SQL di Azure o nel database Cosmos di Azure, la copia parallela predefinita dipende anche dal livello sink (numero di DCU/RU).<br/>- Quando si copiano dati dall'archivio dati abilitato per partition-option (inclusi [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)e SAP [Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)), la copia parallela predefinita è 4.<br>- Quando si copiano dati in Tabella di Azure, la copia parallela predefinita è 4.- When copying data into Azure Table, default parallel copy is 4. |

Per controllare il carico sui computer che ospitano gli archivi dati o per ottimizzare `parallelCopies` le prestazioni di copia, è possibile eseguire l'override del valore predefinito e specificare un valore per la proprietà. Il valore deve essere un numero intero maggiore o uguale a 1. In fase di esecuzione, per ottenere prestazioni ottimali, l'attività di copia utilizza un valore minore o uguale al valore impostato.

Quando si specifica un `parallelCopies` valore per la proprietà, prendere in considerazione l'aumento di carico sugli archivi dati di origine e sink. Considerare anche l'aumento del carico per il runtime di integrazione self-hosted se l'attività di copia è potenziata da esso. Questo aumento del carico si verifica soprattutto quando si dispone di più attività o esecuzioni simultanee delle stesse attività eseguite sullo stesso archivio dati. Se si nota che l'archivio dati o il runtime di integrazione self-hosted è sovraccaricato dal carico, diminuire il `parallelCopies` valore per alleviare il carico.

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

- **Si desidera inserire dati da vari archivi dati in SQL Data Warehouse tramite PolyBase.** SQL Data Warehouse fa uso di PolyBase come meccanismo a velocità effettiva elevata per il caricamento di grandi quantità di dati in SQL Data Warehouse. I dati di origine devono trovarsi nell'archivio BLOB o nell'archivio dati di Azure e devono soddisfare criteri aggiuntivi. Quando si caricano dati da un archivio dati non BLOB o Azure Data Lake Store, è possibile attivare la copia di dati tramite un'archiviazione BLOB di staging provvisoria. In that case, Azure Data Factory performs the required data transformations to ensure that it meets the requirements of PolyBase. Quindi usa PolyBase per caricare in modo efficiente i dati in SQL Data Warehouse. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **A volte è necessario un po' di tempo per eseguire uno spostamento ibrido dei dati (ovvero, per copiare da un archivio dati locale a un archivio dati cloud) tramite una connessione di rete lenta.** Per migliorare le prestazioni, è possibile usare la copia in fasi per comprimere i dati in locale in modo che lo spostamento dei dati nell'archivio dati di gestione temporanea nel cloud sia necessario. È quindi possibile decomprimere i dati nell'archivio di gestione temporanea prima di caricarli nell'archivio dati di destinazione.
- **Non si desidera aprire porte diverse dalla porta 80 e dalla porta 443 nel firewall a causa dei criteri IT aziendali.** Ad esempio, quando si copiano dati da un archivio dati locale a un sink del database SQL di Azure o un sink di Azure SQL Data Warehouse, è necessario attivare le comunicazioni TCP in uscita sulla porta 1433 per Windows Firewall e per il firewall aziendale. In questo scenario, la copia in fasi può sfruttare il runtime di integrazione self-hosted per copiare prima i dati in un'istanza di gestione temporanea dell'archiviazione BLOB tramite HTTP o HTTPS sulla porta 443.In this scenario, staged copy can take advantage of the self-hosted integration runtime to first copy data to a Blob storage staging instance over HTTP or HTTPS on port 443. È quindi possibile caricare i dati nel database SQL o in SQL Data Warehouse dalla gestione temporanea dell'archiviazione BLOB. In questo flusso non è necessario abilitare la porta 1433.

### <a name="how-staged-copy-works"></a>Come funziona la copia di staging

Quando si attiva la funzionalità di staging, i dati vengono prima copiati dall'archivio dati di origine nell'archivio BLOB di staging personale. Successivamente, vengono copiati dall'archivio dati di staging nell'archivio dati sink. Azure Data Factory gestisce automaticamente il flusso in due fasi. Azure Data Factory pulisce anche i dati temporanei dall'archiviazione di gestione temporanea al termine dello spostamento dei dati.

![copia di staging](media/copy-activity-performance/staged-copy.png)

Quando si attiva lo spostamento dei dati utilizzando un archivio di gestione temporanea, è possibile specificare se si desidera comprimere i dati prima di spostare i dati dall'archivio dati di origine a un archivio dati provvisorio o di gestione temporanea e quindi decompressi prima di spostare i dati da un archivio provvisorio o di gestione temporanea all'archivio dati sink.

Attualmente, non è possibile copiare i dati tra due archivi dati connessi tramite ir self-hosted diversi, né con né con né con copia in fasi. Per questo scenario, è possibile configurare due attività di copia concatenate in modo esplicito per copiare dall'origine all'area di gestione temporanea e quindi dalla gestione temporanea al sink.

### <a name="configuration"></a>Configurazione

Configurare l'impostazione **enableStaging** nell'attività di copia per specificare se si desidera che i dati vengano gestiti temporaneamente nell'archivio BLOB prima di caricarli in un archivio dati di destinazione. Quando si imposta `TRUE` **enableStaging** su , specificare le proprietà aggiuntive elencate nella tabella seguente. È anche necessario creare un servizio collegato alla firma di Archiviazione di Azure o Archiviazione per la gestione temporanea, se non è disponibile.

| Proprietà | Descrizione | Valore predefinito | Obbligatoria |
| --- | --- | --- | --- |
| enableStaging |Specificare se si vuole copiare i dati tramite un archivio di staging provvisorio. |False |No |
| linkedServiceName |Specificare il nome di un servizio collegato [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) che fa riferimento all'istanza di archiviazione usata come archivio di staging provvisorio. <br/><br/> Non è possibile usare l'archiviazione con una firma di accesso condiviso per caricare i dati in SQL Data Warehouse tramite PolyBase.You can't use Storage with a shared access signature to load data into SQL Data Warehouse via PolyBase. Può essere usata in tutti gli altri scenari. |N/D |Sì, quando **enableStaging** è impostato su TRUE |
| path |Specificare il percorso dell'archivio BLOB che deve contenere i dati di staging. Se non si specifica un percorso, il servizio crea un contenitore per archiviare i dati temporanei. <br/><br/>  Specificare un percorso solo se si usa l'archiviazione con una firma di accesso condiviso o se i dati temporanei devono trovarsi in un percorso specifico. |N/D |No |
| enableCompression |Specifica se i dati devono essere compressi prima di essere copiati nella destinazione. Questa impostazione ridurre il volume dei dati da trasferire. |False |No |

>[!NOTE]
> Se si usa la copia in fasi con la compressione abilitata, l'entità servizio o l'autenticazione MSI per il servizio collegato BLOB di gestione temporanea non è supportata.

Ecco una definizione di esempio di un'attività di copia con le proprietà descritte nella tabella precedente:Here's a sample definition of a copy activity with the properties that are described in the preceding table:

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

L'addebito viene effettuato in base a due passaggi: durata della copia e tipo di copia.

* Quando si usa la gestione temporanea durante una copia cloud, ovvero la copia dei dati da un archivio dati cloud in un altro archivio dati cloud, entrambe le fasi autorizzate dal runtime di integrazione di Azure, viene addebitato il pulsante [somma della durata della copia per il passaggio 1 e il passaggio 2] x [prezzo unitario della copia cloud].
* Quando si usa la gestione temporanea durante una copia ibrida, ovvero la copia dei dati da un archivio dati locale a un archivio dati cloud, una fase consentita da un runtime di integrazione self-hosted, viene addebitato [durata copia ibrida] x [prezzo unitario della copia ibrida] - [durata copia cloud]. x [prezzo unitario copia cloud].

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli sull'attività di copia:See the other copy activity articles:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Guida alle prestazioni e alla scalabilità dell'attività di copia](copy-activity-performance.md)
- [Risolvere i problemi relativi alle prestazioni dell'attività di copiaTroubleshoot copy activity](copy-activity-performance-troubleshooting.md)
- [Usare Azure Data Factory per eseguire la migrazione dei dati dal data lake o dal data warehouse ad AzureUse Azure Data Factory to migrate data from your data lake or data warehouse to Azure](data-migration-guidance-overview.md)
- [Eseguire la migrazione dei dati da un archivio dati Amazon S3 ad Archiviazione di Azure](data-migration-guidance-s3-azure-storage.md)