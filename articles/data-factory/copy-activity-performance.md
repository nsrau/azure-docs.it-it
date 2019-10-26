---
title: Guida alla scalabilità e alle prestazioni dell'attività di copia in Azure Data Factory | Microsoft Docs
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
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: ba08bbdca059b3e14281a3c26827d07f7b196d1c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72930934"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Guida alla scalabilità e alle prestazioni dell'attività di copia
> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory che si sta usando:"]
> * [Versione 1](v1/data-factory-copy-activity-performance.md)
> * [Versione corrente](copy-activity-performance.md)

Se si vuole eseguire una migrazione dei dati su larga scala da data Lake o Enterprise data warehouse (EDW) ad Azure o si vuole inserire dati su larga scala da origini diverse in Azure per Big Data analisi, è fondamentale ottenere prestazioni ottimali e scalabilità.  Azure Data Factory fornisce un meccanismo efficiente, resiliente ed economicamente conveniente per inserire i dati su larga scala, rendendolo ideale per gli ingegneri di dati che desiderano creare pipeline di inserimento dati altamente performanti e scalabili.

Dopo la lettura di questo articolo, si potrà rispondere alle domande seguenti:

- Quale livello di prestazioni e scalabilità è possibile ottenere utilizzando l'attività di copia ADF per la migrazione dei dati e gli scenari di inserimento dei dati?

- Quali operazioni è necessario eseguire per ottimizzare le prestazioni dell'attività di copia ADF?
- Quali manopole di ottimizzazione delle prestazioni di ADF è possibile utilizzare per ottimizzare le prestazioni per una singola esecuzione dell'attività di copia?
- Quali altri fattori all'esterno di ADF considerare durante l'ottimizzazione delle prestazioni di copia?

> [!NOTE]
> Se non si ha familiarità con l'attività di copia in generale, vedere la [Panoramica dell'attività di copia](copy-activity-overview.md) prima di leggere questo articolo.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Prestazioni di copia e scalabilità ottenibili con ADF

ADF offre un'architettura senza server che consente il parallelismo a livelli diversi, consentendo agli sviluppatori di compilare pipeline per sfruttare al meglio la larghezza di banda di rete, oltre a IOPS e larghezza di banda di archiviazione per ottimizzare la velocità effettiva di spostamento dei dati per l'ambiente.  Ciò significa che la velocità effettiva che è possibile ottenere può essere stimata misurando la velocità effettiva minima offerta dall'archivio dati di origine, l'archivio dati di destinazione e la larghezza di banda di rete tra l'origine e la destinazione.  La tabella seguente calcola la durata della copia in base alle dimensioni dei dati e al limite di larghezza di banda per l'ambiente. 

| Dimensioni dati/ <br/> bandwidth | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 min    | 1,4 min   | 0,3 min   | 0,1 min  | 0,03 min | 0,01 min | 0,0 min   |
| **10 GB**                   | 27,3 min   | 13,7 min  | 2,7 min   | 1,3 min  | 0,3 min  | 0,1 min  | 0,03 min  |
| **100 GB**                  | 4,6 ore    | 2,3 ore   | 0,5 ore   | 0,2 ore  | 0,05 ore | 0,02 ore | 0,0 ore   |
| **1 TB**                    | 46,6 ore   | 23,3 ore  | 4,7 ore   | 2,3 ore  | 0,5 ore  | 0,2 ore  | 0,05 ore  |
| **10 TB**                   | 19,4 giorni  | 9,7 giorni  | 1,9 giorni  | 0,9 giorni | 0,2 giorni | 0,1 giorni | 0,02 giorni |
| **100 TB**                  | 194,2 giorni | 97,1 giorni | 19,4 giorni | 9,7 giorni | 1,9 giorni | 1 giorno   | 0,2 giorni  |
| **1 PB**                    | 64,7 mo    | 32,4 Mo   | 6,5 Mo    | 3,2 Mo   | 0,6 Mo   | 0,3 Mo   | 0,06 mo   |
| **10 PB**                   | 647,3 Mo   | 323,6 Mo  | 64,7 mo   | 31,6 Mo  | 6,5 Mo   | 3,2 Mo   | 0,6 Mo    |

La copia di ADF è scalabile a livelli diversi:

![scalabilità delle copie di ADF](media/copy-activity-performance/adf-copy-scalability.png)

- Il flusso di controllo ADF può avviare più attività di copia in parallelo, ad esempio utilizzando [per ogni ciclo](control-flow-for-each-activity.md).
- Una singola attività di copia può sfruttare le risorse di calcolo scalabili: quando si usa Azure Integration Runtime, è possibile specificare [fino a 256 DIUs](#data-integration-units) per ogni attività di copia in modo senza server. Quando si usa Integration Runtime indipendenti, è possibile scalare manualmente il computer o scalare orizzontalmente in più computer ([fino a 4 nodi](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) e una singola attività di copia partiziona il set di file in tutti i nodi.
- Una singola attività di copia legge e scrive nell'archivio dati usando più thread [in parallelo](#parallel-copy).

## <a name="performance-tuning-steps"></a>Procedura di ottimizzazione delle prestazioni

Eseguire questi passaggi per ottimizzare le prestazioni del servizio Azure Data Factory con l'attività di copia.

1. **Selezionare un set di dati di test e stabilire una linea di base.** Durante la fase di sviluppo, testare la pipeline usando l'attività di copia su un campione di dati rappresentativi. Il set di dati scelto deve rappresentare i modelli di dati tipici (struttura di cartelle, modello di file, schema di dati e così via) ed è sufficientemente grande per valutare le prestazioni di copia, ad esempio per completare l'attività di copia è necessario 10 minuti o oltre. Raccogliere i dettagli di esecuzione e le caratteristiche delle prestazioni dopo il [monitoraggio dell'attività di copia](copy-activity-overview.md#monitoring).

2. **Come ottimizzare le prestazioni di una singola attività di copia**:

   Per iniziare, è consigliabile innanzitutto ottimizzare le prestazioni usando una singola attività di copia.

   **Se l'attività di copia viene eseguita in un Azure Integration Runtime:**

   Iniziare con i valori predefiniti per le [unità di integrazione dati (DIU)](#data-integration-units) e le impostazioni di [copia parallela](#parallel-copy) .  Eseguire un'esecuzione dei test delle prestazioni e prendere nota delle prestazioni, nonché dei valori effettivi usati per DIUs e copie parallele.  Vedere [monitoraggio dell'attività di copia](copy-activity-overview.md#monitoring) su come raccogliere i risultati dell'esecuzione e le impostazioni delle prestazioni usate.

   A questo punto, eseguire esecuzioni di test aggiuntive, ogni volta raddoppiando il valore per l'impostazione DIU.  In alternativa, se si ritiene che le prestazioni ottenibili usando l'impostazione predefinita si trovino molto al di sotto dell'aspettativa, è possibile aumentare l'impostazione di DIU in maniera più drastica nell'esecuzione dei test successiva.

   L'attività di copia deve essere scalata quasi perfettamente in modo lineare man mano che si aumenta l'impostazione DIU.  Se si raddoppia l'impostazione DIU, non si vede la velocità effettiva Double, è possibile che si verifichino due operazioni:

   - Il modello di copia specifico in esecuzione non trae vantaggio dall'aggiunta di altre DIUs.  Anche se è stato specificato un valore DIU più grande, il valore di DIU effettivo usato è rimasto invariato e pertanto si sta ottenendo la stessa velocità effettiva che precede.  In tal caso, ottimizzare la velocità effettiva aggregata eseguendo più copie simultaneamente facendo riferimento al passaggio 3.
   - Con l'aggiunta di più DIUs (maggiore potenza) e, di conseguenza, la velocità di estrazione, trasferimento e caricamento dei dati più elevata, ovvero l'archivio dati di origine, la rete tra o l'archivio dati di destinazione ha raggiunto il collo di bottiglia e potrebbe essere limitato.  In tal caso, provare a contattare l'amministratore dell'archivio dati o l'amministratore di rete per aumentare il limite superiore oppure, in alternativa, ridurre l'impostazione di DIU fino a quando non si verifica la limitazione delle richieste.

   **Se l'attività di copia viene eseguita in un Integration Runtime self-hosted:**

   Si consiglia di usare un computer dedicato separato dal server che ospita l'archivio dati per ospitare Integration Runtime.

   Iniziare con i valori predefiniti per l'impostazione di [copia parallela](#parallel-copy) e usare un singolo nodo per il runtime di integrazione self-hosted.  Eseguire un'esecuzione dei test delle prestazioni e prendere nota delle prestazioni.

   Per ottenere una velocità effettiva più elevata, è possibile scalare verticalmente il runtime di integrazione self-hosted:

   - Se la CPU e la memoria disponibile nel nodo IR self-hosted non sono completamente utilizzate, ma l'esecuzione dei processi simultanei raggiunge il limite, è necessario aumentare il numero di processi simultanei in un nodo.  Per istruzioni, vedere [qui](create-self-hosted-integration-runtime.md#scale-up) .
   - Se, d'altra parte, la CPU è elevata sul nodo IR indipendente o la memoria disponibile è insufficiente, è possibile aggiungere un nuovo nodo per aumentare la scalabilità orizzontale del carico tra più nodi.  Per istruzioni, vedere [qui](create-self-hosted-integration-runtime.md#high-availability-and-scalability) .

   Con la scalabilità verticale o orizzontale della capacità del runtime di integrazione self-hosted, ripetere l'esecuzione del test delle prestazioni per verificare se si sta ottenendo una velocità effettiva sempre maggiore.  Se la velocità effettiva smette di migliorare, probabilmente l'archivio dati di origine, la rete tra o l'archivio dati di destinazione ha raggiunto il collo di bottiglia e sta iniziando a essere limitato. In tal caso, provare a contattare l'amministratore dell'archivio dati o l'amministratore di rete per aumentare il limite superiore oppure, in alternativa, tornare all'impostazione di scalabilità precedente per il runtime di integrazione self-hosted. 

3. **Come ottimizzare la velocità effettiva aggregata eseguendo più copie simultaneamente:**

   Ora che sono state ingrandite le prestazioni di una singola attività di copia, se non sono ancora stati superati i limiti massimi di velocità effettiva dell'ambiente, ovvero la rete, l'archivio dati di origine e l'archivio dati di destinazione, è possibile eseguire più attività di copia in parallelo usando ADF costrutti del flusso di controllo, ad esempio [per ciascun ciclo](control-flow-for-each-activity.md).

4. **Suggerimenti per l'ottimizzazione delle prestazioni e funzionalità di ottimizzazione.** In alcuni casi, quando si esegue un'attività di copia in Azure Data Factory, viene visualizzato il messaggio "suggerimenti per l'ottimizzazione delle prestazioni" nella parte superiore del [monitoraggio dell'attività di copia](copy-activity-overview.md#monitor-visually), come illustrato nell'esempio seguente. Il messaggio indica il collo di bottiglia identificato per l'esecuzione della copia specificata. Vengono inoltre illustrati gli elementi da modificare per aumentare la velocità effettiva della copia. I suggerimenti per l'ottimizzazione delle prestazioni offrono attualmente suggerimenti come:

   - Usare la polibase quando si copiano i dati in Azure SQL Data Warehouse.
   - Aumentare Azure Cosmos DB unità richiesta o il database SQL di Azure DTU (unità di velocità effettiva del database) quando la risorsa sul lato archivio dati è il collo di bottiglia.
   - Rimuovere la copia temporanea non necessaria.

   Anche le regole di ottimizzazione delle prestazioni verranno gradualmente migliorate.

   **Esempio: copiare nel database SQL di Azure con suggerimenti per l'ottimizzazione delle prestazioni**

   In questo esempio, durante l'esecuzione di una copia, Azure Data Factory rileva che il database SQL di Azure sink raggiunge un utilizzo elevato di DTU, rallentando le operazioni di scrittura. Il suggerimento consiste nell'aumentare il livello del database SQL di Azure con più DTU. 

   ![Monitoraggio della copia con suggerimenti per l'ottimizzazione delle prestazioni](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   Inoltre, di seguito sono riportate alcune funzionalità di ottimizzazione delle prestazioni che è necessario conoscere:

   - [Copia parallela](#parallel-copy)
   - [Unità di integrazione dati](#data-integration-units)
   - [Copia di staging](#staged-copy)
   - [Scalabilità del runtime di integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **Espandere la configurazione per l'intero set di dati.** Quando si è soddisfatti dei risultati e delle prestazioni di esecuzione, è possibile espandere la definizione e la pipeline per coprire l'intero set di dati.

## <a name="copy-performance-optimization-features"></a>Funzionalità di ottimizzazione delle prestazioni di copia

Azure Data Factory offre le seguenti funzionalità di ottimizzazione delle prestazioni:

- [Copia parallela](#parallel-copy)
- [Unità di integrazione dati](#data-integration-units)
- [Copia di staging](#staged-copy)

### <a name="data-integration-units"></a>Unità di integrazione dati

Un'unità di integrazione dati è una misura che rappresenta la potenza, ovvero una combinazione di CPU, memoria e allocazione di risorse di rete, di una singola unità in Azure Data Factory. L'unità di integrazione dati si applica solo al [runtime di integrazione di Azure](concepts-integration-runtime.md#azure-integration-runtime), ma non al runtime di [integrazione self-hosted](concepts-integration-runtime.md#self-hosted-integration-runtime).

Ti verrà addebitato il **numero di DIUs usati \* durata della copia \* prezzo unitario/Diu ore**. Vedi i prezzi correnti [qui](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). È possibile applicare la valuta locale e la separazione separata per ogni tipo di sottoscrizione.

Il DIUs consentito per consentire l'esecuzione di un'attività di copia è **compreso tra 2 e 256**. Se non è specificato o se si sceglie "auto" nell'interfaccia utente, Data Factory applicare in modo dinamico l'impostazione Optimal DIU in base alla coppia di sink di origine e al modello di dati. La tabella seguente elenca le DIUs predefinite usate in diversi scenari di copia:

| Scenario di copia | Numero di unità di integrazione dati predefinite determinato dal servizio |
|:--- |:--- |
| Copiare dati tra archivi basati su file | Compreso tra 4 e 32 in base al numero e alle dimensioni dei file |
| Copiare i dati nel database SQL di Azure o Azure Cosmos DB |Tra 4 e 16 a seconda del livello del database SQL di Azure sink o del Cosmos DB (numero di DTU/UR) |
| Tutti gli altri scenari di copia | 4 |

Per ignorare l'impostazione predefinita, è possibile specificare un valore per la proprietà **dataIntegrationUnits** procedendo come segue. Il *numero effettivo di unità di integrazione dati* usate dall'operazione di copia in fase di esecuzione è minore o uguale al valore configurato, a seconda del modello di dati.

Quando si monitora un'esecuzione di attività, è possibile visualizzare i DIUs usati per ogni copia eseguita nell'output dell'attività di copia. Per altre informazioni, vedere [monitoraggio dell'attività di copia](copy-activity-overview.md#monitoring).

> [!NOTE]
> L'impostazione di DIUs maggiori di quattro attualmente si applica solo quando si copiano più file da BLOB di Azure/ADLS Gen1/ADLS Gen2/Amazon S3/Google Cloud storage/cloud FTP/cloud SFTP o da un archivio dati relazionale cloud abilitato per le opzioni di partizione (incluso [Oracle ](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source)) a qualsiasi altro archivio dati cloud.

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
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="parallel-copy"></a>Copia parallela

È possibile usare la proprietà **parallelCopies** per indicare il parallelismo che si vuole venga usato dall'attività di copia. È possibile considerare questa proprietà come il numero massimo di thread all'interno dell'attività di copia che può leggere dall'origine o scrivere negli archivi dati sink in parallelo.

Per ogni esecuzione dell'attività di copia, Azure Data Factory determina il numero di copie parallele da usare per copiare i dati dall'archivio dati di origine all'archivio dati di destinazione. Il numero predefinito di copie parallele usate dipende dal tipo di origine e sink usato.

| Scenario di copia | Numero predefinito di copie parallele determinato dal servizio |
| --- | --- |
| Copiare dati tra archivi basati su file |Dipende dalle dimensioni dei file e dal numero di DIUs usati per copiare i dati tra due archivi dati cloud o la configurazione fisica del computer del runtime di integrazione self-hosted. |
| Copiare da archivio dati relazionale con l'opzione di partizione abilitata (inclusi [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source))|4 |
| Copiare i dati da qualsiasi archivio di origine ad archiviazione tabelle di Azure |4 |
| Tutti gli altri scenari di copia |1 |

> [!TIP]
> Quando si copiano dati tra archivi basati su file, il comportamento predefinito in genere offre la migliore velocità effettiva. Il comportamento predefinito viene determinato automaticamente in base al modello di file di origine.

Per controllare il carico sui computer che ospitano gli archivi dati o per ottimizzare le prestazioni di copia, è possibile eseguire l'override del valore predefinito e specificare un valore per la proprietà **parallelCopies** . Il valore deve essere un numero intero maggiore o uguale a 1. In fase di esecuzione, per ottenere prestazioni ottimali, l'attività di copia utilizza un valore minore o uguale al valore impostato.

**Punti da notare:**

- Quando si copiano dati tra archivi basati su file, **parallelCopies** determina il parallelismo a livello di file. La suddivisione in blocchi all'interno di un singolo file avviene in modo automatico e trasparente. È progettato per usare le dimensioni del blocco più adatte per un determinato tipo di archivio dati di origine per caricare i dati in parallelo e ortogonali a **parallelCopies**. Il numero effettivo di copie parallele usate dal servizio di spostamento dati per l'operazione di copia in fase di esecuzione non è maggiore del numero di file disponibili. Se il comportamento di copia è **mergeFile**, l'attività di copia non può sfruttare il parallelismo a livello di file.
- Quando si copiano dati da archivi non basati su file (ad eccezione di [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source)e [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) Connector come origine con il partizionamento dei dati abilitato) per gli archivi basati su file, i dati il servizio di spostamento ignora la proprietà **parallelCopies** . Anche se viene specificato, in questo caso il parallelismo non viene applicato.
- La proprietà **parallelCopies** è ortogonale a **dataIntegrationUnits**. La prima viene conteggiata su tutte le unità di integrazione dati.
- Quando si specifica un valore per la proprietà **parallelCopies** , prendere in considerazione l'aumento del carico per gli archivi dati di origine e sink. Prendere in considerazione anche l'aumento del carico per il runtime di integrazione self-hosted se l'attività di copia è abilitata, ad esempio per la copia ibrida. Questo aumento del carico si verifica soprattutto quando si hanno più attività o esecuzioni simultanee delle stesse attività eseguite nello stesso archivio dati. Se si nota che l'archivio dati o il runtime di integrazione self-hosted è sovraccarico del carico, ridurre il valore di **parallelCopies** per alleggerire il carico.

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

### <a name="staged-copy"></a>copia di staging

Quando si copiano dati da un archivio dati di origine a un archivio dati sink, è possibile scegliere di usare un archivio BLOB come archivio di staging provvisorio. La funzionalità di staging è particolarmente utile nei casi seguenti:

- **Si vuole inserire dati da diversi archivi dati in SQL Data Warehouse tramite la polibase.** SQL Data Warehouse fa uso di PolyBase come meccanismo a velocità effettiva elevata per il caricamento di grandi quantità di dati in SQL Data Warehouse. I dati di origine devono trovarsi in un archivio BLOB o Azure Data Lake Store e devono soddisfare criteri aggiuntivi. Quando si caricano dati da un archivio dati non BLOB o Azure Data Lake Store, è possibile attivare la copia di dati tramite un'archiviazione BLOB di staging provvisoria. In tal caso, Azure Data Factory esegue le trasformazioni dei dati necessarie per verificare che soddisfi i requisiti di polibase. Quindi usa PolyBase per caricare in modo efficiente i dati in SQL Data Warehouse. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **A volte è necessario un po' di tempo per eseguire uno spostamento dati ibrido, ovvero per copiare da un archivio dati locale a un archivio dati cloud, su una connessione di rete lenta.** Per migliorare le prestazioni, è possibile usare la copia di staging per comprimere i dati in locale, in modo che sia necessario meno tempo per spostare i dati nell'archivio dati di staging nel cloud. Sarà quindi possibile decomprimere i dati nell'archivio di staging prima di caricarli nell'archivio dati di destinazione.
- **Non è necessario aprire porte diverse dalla porta 80 e dalla porta 443 nel firewall a causa dei criteri IT aziendali.** Ad esempio, quando si copiano dati da un archivio dati locale a un sink del database SQL di Azure o un sink di Azure SQL Data Warehouse, è necessario attivare le comunicazioni TCP in uscita sulla porta 1433 per Windows Firewall e per il firewall aziendale. In questo scenario, la copia di staging può trarre vantaggio dal runtime di integrazione self-hosted per prima cosa copiare i dati in un'istanza di staging dell'archiviazione BLOB tramite HTTP o HTTPS sulla porta 443. Quindi, può caricare i dati nel database SQL o SQL Data Warehouse dalla gestione temporanea dell'archiviazione BLOB. In questo flusso non è necessario abilitare la porta 1433.

#### <a name="how-staged-copy-works"></a>Come funziona la copia di staging

Quando si attiva la funzionalità di staging, i dati vengono prima copiati dall'archivio dati di origine nell'archivio BLOB di staging personale. Successivamente, vengono copiati dall'archivio dati di staging nell'archivio dati sink. Azure Data Factory gestisce automaticamente il flusso a due fasi. Azure Data Factory pulisce anche i dati temporanei dall'archiviazione di staging dopo il completamento dello spostamento dei dati.

![copia di staging](media/copy-activity-performance/staged-copy.png)

Quando si attiva lo spostamento dei dati usando un archivio di staging, è possibile specificare se si vuole che i dati vengano compressi prima di spostare i dati dall'archivio dati di origine a un archivio dati provvisorio o di staging e quindi decompressi prima di spostare i dati da un file dat provvisorio o di staging Archivio nell'archivio dati sink.

Attualmente, non è possibile copiare i dati tra due archivi dati connessi tramite un altro IRs indipendente, né con la copia temporanea. Per questo scenario, è possibile configurare due attività di copia concatenate in modo esplicito per la copia dall'origine alla gestione temporanea, quindi da staging a sink.

#### <a name="configuration"></a>Configurazione

Configurare l'impostazione **enableStaging** nell'attività di copia per specificare se si desidera che i dati vengano gestiti temporaneamente nell'archivio BLOB prima di caricarli in un archivio dati di destinazione. Quando si imposta **enableStaging** su `TRUE`, specificare le proprietà aggiuntive elencate nella tabella seguente. È anche necessario creare un servizio collegato di archiviazione di Azure o di archiviazione con firma di accesso condiviso per la gestione temporanea, se non è già presente.

| Proprietà | Description | Valore predefinito | Obbligatoria |
| --- | --- | --- | --- |
| enableStaging |Specificare se si vuole copiare i dati tramite un archivio di staging provvisorio. |Falso |No |
| linkedServiceName |Specificare il nome di un servizio collegato [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) che fa riferimento all'istanza di archiviazione usata come archivio di staging provvisorio. <br/><br/> Non è possibile usare l'archiviazione con una firma di accesso condiviso per caricare i dati in SQL Data Warehouse tramite la polibase. Può essere usata in tutti gli altri scenari. |N/D |Sì, quando **enableStaging** è impostato su TRUE |
| path |Specificare il percorso dell'archivio BLOB che deve contenere i dati di staging. Se non si specifica un percorso, il servizio crea un contenitore per archiviare i dati temporanei. <br/><br/> Specificare un percorso solo se si usa l'archiviazione con una firma di accesso condiviso o se i dati temporanei devono trovarsi in un percorso specifico. |N/D |No |
| enableCompression |Specifica se i dati devono essere compressi prima di essere copiati nella destinazione. Questa impostazione ridurre il volume dei dati da trasferire. |Falso |No |

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

#### <a name="staged-copy-billing-impact"></a>Impatto della copia di staging sulla fatturazione

I costi vengono addebitati in base a due passaggi: durata della copia e tipo di copia.

* Quando si usa la gestione temporanea durante una copia nel cloud, che copia i dati da un archivio dati cloud a un altro archivio dati cloud, entrambe le fasi sono potenziate dal runtime di integrazione di Azure, viene addebitato il valore [somma della durata della copia per i passaggi 1 e 2] x [prezzo unitario di copia cloud].
* Quando si usa la gestione temporanea durante una copia ibrida, che copia i dati da un archivio dati locale a un archivio dati cloud, una fase abilitata da un runtime di integrazione self-hosted, viene addebitato il costo di [durata copia ibrida] x [prezzo unitario copia ibrida] + [durata copia cloud] x [prezzo unitario della copia nel cloud].

## <a name="references"></a>Riferimenti

Di seguito sono riportati alcuni riferimenti sul monitoraggio e l'ottimizzazione delle prestazioni per alcuni degli archivi dati supportati:

* Archiviazione di Azure, che include archiviazione BLOB e archiviazione tabelle: [obiettivi di scalabilità di archiviazione di Azure](../storage/common/storage-scalability-targets.md) e [elenco di controllo di prestazioni e scalabilità per archiviazione di](../storage/common/storage-performance-checklist.md)Azure
* Database SQL di Azure: è possibile [monitorare le prestazioni](../sql-database/sql-database-single-database-monitor.md) e controllare la percentuale di unità di transazione di database (DTU).
* Azure SQL Data Warehouse: la relativa funzionalità viene misurata in unità di data warehouse (DWU). Vedere [gestire la potenza di calcolo in Azure SQL data warehouse (panoramica)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* Azure Cosmos DB: [livelli di prestazioni in Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server locale: [monitoraggio e ottimizzazione delle prestazioni](https://msdn.microsoft.com/library/ms189081.aspx).
* File server locale: [ottimizzazione delle prestazioni per i file server](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli altri articoli sull'attività di copia:

- [Panoramica dell'attività di copia](copy-activity-overview.md)
- [Usare Azure Data Factory per migrare i dati dal data Lake o da data warehouse ad Azure](data-migration-guidance-overview.md)
- [Migrare i dati da Amazon S3 ad archiviazione di Azure](data-migration-guidance-s3-azure-storage.md)
