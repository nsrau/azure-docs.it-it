<properties
	pageTitle="Guida alle prestazioni dell'attività di copia e all'ottimizzazione | Microsoft Azure"
	description="Informazioni sui fattori principali che influiscono sulle prestazioni dello spostamento di dati in Azure Data Factory quando si usa l'attività di copia."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2016"
	ms.author="spelluru"/>


# Guida alle prestazioni dell'attività di copia e all'ottimizzazione
Questo articolo descrive i fattori principali che influiscono sulle prestazioni dello spostamento di dati quando si usa Azure Data Factory con l'attività di copia. Elenca anche le prestazioni osservate durante i test e descrive vari modi per ottimizzare le prestazioni dell'attività di copia.

L'attività di copia consente lo spostamento dei dati a velocità effettiva elevata:

- Inserimento di 1 TB di dati nell'archivio BLOB di Azure da un file system locale in meno di tre ore a una velocità di 100 MBps
- Inserimento di 1 TB di dati in Azure Data Lake Store da un file system locale e un archivio BLOB di Azure in meno di tre ore a una velocità di 100 MBps
- Inserimento di 1 TB di dati in Azure SQL Data Warehouse da un archivio BLOB in meno di tre ore a una velocità di 100 MBps

Vedere le sezioni seguenti per altre informazioni sulle prestazioni dell'attività di copia e suggerimenti su come ottimizzarle.

> [AZURE.NOTE] Se non si ha familiarità con l'attività di copia in generale, vedere [Spostare dati con l'attività di copia](data-factory-data-movement-activities.md) prima di continuare a leggere l'articolo.

## Procedura di ottimizzazione delle prestazioni
Per ottimizzare le prestazioni del servizio Data Factory con l'attività di copia, è consigliabile seguire questa procedura:

1.	**Stabilire una baseline**. Durante la fase di sviluppo, testare la pipeline usando l'attività di copia su un campione di dati rappresentativo. È possibile usare il [modello di sezionamento](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) di Data Factory per limitare la quantità di dati utilizzati.

	Per raccogliere le caratteristiche relative a prestazioni e tempo di esecuzione è possibile usare l'**app di monitoraggio e gestione**. Scegliere **Monitoraggio e gestione** nella home page di Data Factory. Nella visualizzazione albero scegliere il **set di dati di output**. Nell'elenco **Activity Windows** (Finestre attività) scegliere l'esecuzione dell'attività di copia. L'elenco **Activity Windows** (Finestre attività) riporta la durata dell'attività di copia e le dimensioni dei dati copiati. La velocità effettiva è elencata in **Activity Window Explorer** (Esplora finestre attività). Per altre informazioni sull'app, vedere [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md).

	![Dettagli esecuzione attività](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

	È possibile confrontare le prestazioni e la configurazione dello scenario personalizzato con le [informazioni di riferimento sulle prestazioni](#performance-reference) dell'attività di copia ottenute dai test e pubblicate più avanti.
2. **Diagnosticare e ottimizzare le prestazioni**. Se le prestazioni osservate non soddisfano le aspettative, è necessario identificare gli eventuali colli di bottiglia e quindi ottimizzare le prestazioni per rimuovere o ridurre l'effetto dei colli di bottiglia. Una descrizione completa della diagnosi delle prestazioni non rientra nell'ambito di questo articolo, ma di seguito sono riportate alcune considerazioni comuni:
	- [Origine](#considerations-for-the-source)
	- [Sink](#considerations-for-the-sink)
	- [Serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization)
	- [Compressione](#considerations-for-compression)
	- [Mapping di colonne](#considerations-for-column-mapping)
	- [Gateway di gestione dati](#considerations-for-data-management-gateway)
	- [Altre considerazioni](#other-considerations)
	- [Copia parallela](#parallel-copy)
	- [Unità di spostamento dati cloud](#cloud-data-movement-units)

3. **Espandere la configurazione all'intero set di dati**. Dopo aver ottenuto prestazioni e risultati di esecuzione soddisfacenti, è possibile espandere la definizione del set di dati e il periodo attivo della pipeline per coprire l'intero set di dati.

## Informazioni di riferimento sulle prestazioni

![Matrice delle prestazioni](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

Punti da notare:

- La velocità effettiva viene calcolata con la formula seguente: [dimensione dei dati letti dall'origine]/[durata dell'esecuzione dell'attività di copia].
- Per calcolare i numeri della tabella precedente è stato usato il set di dati [TPC-H](http://www.tpc.org/tpch/).
-	Per eseguire la copia tra archivi dati cloud, impostare **cloudDataMovementUnits** su 1 e 4 per il confronto. **parallelCopies** non è specificato. Per informazioni dettagliate su queste funzionalità, vedere la sezione [Copia parallela](#parallel-copy).
- Nel caso degli archivi dati di Azure, l'origine e il sink si trovano nella stessa area di Azure.
- Per lo spostamento di dati ibrido, ovvero dall'archivio dati locale al cloud o viceversa, è stata usata una singola istanza di Gateway di gestione dati in un computer separato dall'archivio dati locale. La configurazione è elencata nella tabella seguente. Durante l'esecuzione di una singola attività nel gateway, l'operazione di copia ha utilizzato solo una piccola parte della CPU del computer di test o delle risorse di memoria e una piccola parte della larghezza di banda di rete.
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

## Copia parallela
È possibile leggere dati dall'origine o scrivere dati nella destinazione **in parallelo all'interno di un'esecuzione dell'attività di copia**. Questa funzionalità migliora la velocità effettiva di un'operazione di copia e riduce il tempo necessario per spostare i dati.

Questa impostazione è diversa dalla proprietà **concurrency** nella definizione dell'attività. La proprietà **concurrency** determina il numero di **esecuzioni simultanee dell'attività di copia** per elaborare i dati da finestre attività diverse. Ad esempio, dall'1.00 alle 2.00, dalle 2.00 alle 3.00, dalle 3.00 alle 4.00 e così via. Questa funzionalità è utile quando si esegue un caricamento cronologico. La funzionalità di copia parallela si applica all'**esecuzione di una singola attività**.

Verrà ora esaminato uno scenario di esempio. Nell'esempio seguente è necessario elaborare più sezioni trascorse. Data Factory esegue un'istanza dell'attività di copia, ovvero un'esecuzione attività, per ogni sezione:

- Sezione dati dalla prima finestra attività (dall'1.00 alle 2.00) = = > esecuzione attività 1
- Sezione dati dalla seconda finestra attività (dalle 2.00 alle 3.00) = = > esecuzione attività 2
- Sezione dati dalla terza finestra attività (dalle 3.00 alle 4.00) = = > esecuzione attività 3

e così via.

In questo esempio quando il valore **concurrency** è impostato su 2, l'**esecuzione attività 1** e l'**esecuzione attività 2** copiano i dati da due finestre attività **simultaneamente** per migliorare le prestazioni dello spostamento dati. Tuttavia, se all'esecuzione attività 1 sono associati più file, il servizio di spostamento dati copia un file alla volta dall'origine alla destinazione.

### Proprietà parallelCopies
È possibile usare la proprietà **parallelCopies** per indicare il parallelismo che l'attività di copia deve usare. Questa proprietà può essere considerata come il numero massimo di thread all'interno dell'attività di copia che possono leggere dall'origine o scrivere negli archivi dati sink in parallelo.

Per ogni esecuzione dell'attività di copia, Data Factory determina il numero di copie parallele da usare per copiare i dati dall'archivio dati di origine all'archivio dati di destinazione. Il numero predefinito di copie parallele usate dipende dal tipo di origine e dal tipo di sink usati.

Origine e sink |	Numero predefinito di copie parallele determinato dal servizio
------------- | -------------------------------------------------
Copia di dati tra archivi basati su file, come un archivio BLOB, Data Lake Store, un file system locale, un Hadoop Distributed File System o HDFS locale | Tra 1 e 32. Dipende dalle dimensioni dei file e del numero di unità di spostamento dati cloud usate per copiare dati tra due archivi dati cloud oppure dalla configurazione fisica del computer gateway usato per una copia ibrida, ovvero la copia di dati da o in un archivio dati locale.
Copia di dati da **qualsiasi archivio dati di origine in un archivio tabelle di Azure** | 4
Tutte le altre coppie di origine e sink | 1

Nella maggior parte dei casi il comportamento predefinito dovrebbe garantire la velocità effettiva migliore. Tuttavia, per controllare il carico sui computer che ospitano gli archivi dati o per ottimizzare le prestazioni di copia, è possibile scegliere di ignorare il valore predefinito e specificare un valore per la proprietà **parallelCopies**. Il valore deve essere compreso tra 1 e 32, estremi inclusi. Per garantire prestazioni ottimali in fase di esecuzione, l'attività di copia usa un valore minore o uguale al valore configurato.

	"activities":[  
	    {
	        "name": "Sample copy activity",
	        "description": "",
	        "type": "Copy",
	        "inputs": [{ "name": "InputDataset" }],
	        "outputs": [{ "name": "OutputDataset" }],
	        "typeProperties": {
	            "source": {
	                "type": "BlobSource",
	            },
	            "sink": {
	                "type": "AzureDataLakeStoreSink"
	            },
	            "parallelCopies": 8
	        }
	    }
	]

Punti da notare:

- Quando si copiano dati tra archivi basati su file, il parallelismo si verifica a livello di file. Non c'è alcun blocco all'interno dei singoli file. Il numero effettivo di copie parallele usate dal servizio di spostamento dati per l'operazione di copia in fase di esecuzione non è maggiore del numero di file disponibili. Se il comportamento di copia è **mergeFile**, l'attività di copia non può usare il parallelismo.
- Quando si specifica un valore per la proprietà **parallelCopies**, prendere in considerazione l'aumento del carico per gli archivi dati sink e di origine e per il gateway, se si tratta di una copia ibrida. Ciò vale soprattutto quando ci sono più attività o esecuzioni simultanee delle stesse attività che vengono eseguite con lo stesso archivio dati. Se si nota un sovraccarico dell'archivio dati o del gateway, diminuire il valore di **parallelCopies** per alleggerirlo.
- Quando si copiano dati da archivi non basati su file in archivi basati su file, il servizio di spostamento dati ignora la proprietà **parallelCopies**. Anche se viene specificato, in questo caso il parallelismo non viene applicato.

> [AZURE.NOTE] Per poter usare la funzionalità **parallelCopies** durante una copia ibrida, è necessario usare Gateway di gestione dati 1.11 o versione successiva.

### Unità di spostamento dati cloud
L'**unità di spostamento dati cloud** è una misura che rappresenta la potenza, ossia la combinazione tra CPU, memoria e allocazione di risorse di rete, di una singola unità in Data Factory. Una unità di spostamento dati può essere usata in un'operazione di copia da cloud a cloud, ma non in una copia ibrida.

Per impostazione predefinita, Data Factory usa una singola unità di spostamento dati cloud per una singola esecuzione dell'attività di copia. Per ignorare l'impostazione predefinita, è possibile specificare un valore per la proprietà **cloudDataMovementUnits** procedendo come segue. Per informazioni sul livello di miglioramento delle prestazioni che è possibile ottenere quando si configurano più unità per un sink e un'origine della copia specifici, vedere la sezione [Informazioni di riferimento sulle prestazioni](#performance-reference).

	"activities":[  
	    {
	        "name": "Sample copy activity",
	        "description": "",
	        "type": "Copy",
	        "inputs": [{ "name": "InputDataset" }],
	        "outputs": [{ "name": "OutputDataset" }],
	        "typeProperties": {
	            "source": {
	                "type": "BlobSource",
	            },
	            "sink": {
	                "type": "AzureDataLakeStoreSink"
	            },
	            "cloudDataMovementUnits": 4
	        }
	    }
	]

I **valori consentiti** per la proprietà **cloudDataMovementUnits** sono: 1 (valore predefinito), 2, 4 e 8. Il **numero effettivo di unità di spostamento dati cloud** usate dall'operazione di copia in fase di esecuzione è minore o uguale al valore configurato, a seconda del modello di dati. Se sono necessarie più unità di spostamento dati cloud per aumentare la velocità effettiva, contattare il [supporto di Azure](https://azure.microsoft.com/support/). Attualmente è possibile impostare la proprietà su valori maggiori o uguali a 8 soltanto per la copia di più file da un archivio BLOB in un archivio BLOB o in un'istanza di Data Lake Store maggiore o uguale a 16 MB singolarmente.

Per usare al meglio queste due proprietà e per migliorare la velocità effettiva dello spostamento dati, vedere i [casi d'uso di esempio](#case-study-use-parallel-copy). Per usare il comportamento predefinito non è necessario configurare **parallelCopies**. Se si esegue la configurazione e il valore di **parallelCopies** è troppo basso, è possibile che più unità di spostamento dati cloud non vengano utilizzate appieno.

È **importante** ricordare che l'addebito è basato sul tempo totale impiegato per l'operazione di copia. Se un processo di copia impiegava un'ora con una unità cloud e ora richiede 15 minuti con quattro unità cloud, la fattura complessiva sarà pressoché identica. Si prenda ad esempio un utilizzo di quattro unità cloud. La prima unità cloud impiega 10 minuti, la seconda 10 minuti, la terza 5 minuti e la quarta 5 minuti, tutto in un'unica esecuzione dell'attività di copia. Verrà addebitato il tempo totale dell'operazione di copia, ovvero di spostamento dei dati, che è pari a 10 + 10 + 5 + 5 = 30 minuti. L'uso di **parallelCopies** non influisce sulla fatturazione.

## Copia di staging
Quando si copiano dati da un archivio dati di origine a un archivio dati sink, è possibile scegliere di usare un archivio BLOB come archivio di staging provvisorio. La funzionalità di staging è particolarmente utile nei casi seguenti:

-	**A volte occorre tempo per eseguire uno spostamento dati ibrido, ovvero la copia da un archivio dati locale a un archivio dati cloud o viceversa, su una connessione di rete lenta**. Per migliorare le prestazioni, è possibile comprimere i dati in locale in modo che sia necessario meno tempo per spostare i dati nell'archivio dati di staging nel cloud. È quindi possibile decomprimere i dati nell'archivio di staging prima di caricarli nell'archivio dati di destinazione.
2.	**Non si vuole aprire porte diverse dalla porta 80 e dalla porta 443 nel firewall, a causa dei criteri IT aziendali**. Ad esempio, quando si copiano dati da un archivio dati locale a un sink del database SQL di Azure o un sink di Azure SQL Data Warehouse, è necessario attivare le comunicazioni TCP in uscita sulla porta 1433 per Windows Firewall e per il firewall aziendale. In tale scenario è possibile usare Gateway di gestione dati per copiare prima di tutto i dati un'istanza di staging dell'archivio BLOB tramite HTTP o HTTPS sulla porta 443. Quindi, caricare i dati nel database SQL o in SQL Data Warehouse dall'archivio BLOB di staging. In questo flusso non è necessario abilitare la porta 1433.
3.	**Si inseriscono dati da diversi archivi dati in SQL Data Warehouse tramite PolyBase**. SQL Data Warehouse fa uso di PolyBase come meccanismo a velocità effettiva elevata per il caricamento di grandi quantità di dati in SQL Data Warehouse. Tuttavia, i dati di origine devono essere in un archivio BLOB e devono soddisfare criteri aggiuntivi. Quando si caricano dati da un archivio dati non BLOB, è possibile attivare la copia di dati tramite un archivio BLOB di staging provvisorio. In tal caso, Data Factory esegue le trasformazioni di dati necessarie per garantire che vengano soddisfatti i requisiti di PolyBase. Quindi usa PolyBase per caricare i dati in SQL Data Warehouse. Per altri dettagli ed esempi, vedere [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).

### Come funziona la copia di staging
Quando si attiva la funzionalità di staging, i dati vengono prima copiati dall'archivio dati di origine nell'archivio dati di staging personale. Successivamente, vengono copiati dall'archivio dati di staging nell'archivio dati sink. Data Factory gestisce automaticamente il flusso in due fasi ed elimina i dati temporanei dall'archivio di staging al termine dello spostamento dati.

Nello scenario di copia nel cloud in cui gli archivi dati di origine e sink sono nel cloud e non usano Gateway di gestione dati, le operazioni di copia vengono eseguite da Data Factory.

![Copia di staging: scenario cloud](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

Nello scenario di copia ibrido in cui l'origine è in locale e il sink è nel cloud, Gateway di gestione dati sposta i dati dall'archivio dati di origine in un archivio dati di staging. Data Factory sposta quindi i dati dall'archivio dati di staging nell'archivio dati sink. La copia di dati da un archivio dati cloud in un archivio dati locale tramite la funzionalità di staging è supportata anche con un flusso invertito.

![Copia di staging: scenario ibrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Quando si attiva lo spostamento dei dati usando un archivio di staging, è possibile specificare se i dati devono essere compressi prima dello spostamento dall'archivio dati di origine all'archivio dati provvisorio o di staging e poi decompressi prima dello spostamento dall'archivio dati provvisorio o di staging all'archivio dati sink.

Attualmente non è possibile copiare dati tra due archivi dati locali usando un archivio di staging. Questa opzione sarà presto disponibile.

### Configurazione
È possibile configurare l'impostazione **enableStaging** nell'attività di copia per specificare se i dati devono essere inseriti in un archivio BLOB di Azure di staging prima del caricamento in un archivio dati di destinazione. Se si imposta **enableStaging** su TRUE, specificare le proprietà aggiuntive elencate nella tabella seguente. Se non è già disponibile, è necessario creare un servizio collegato alla firma di accesso condiviso di archiviazione o di Archiviazione di Azure per lo staging.

Proprietà | Descrizione | Valore predefinito | Obbligatorio
--------- | ----------- | ------------ | --------
**enableStaging** | Specificare se si vuole copiare i dati tramite un archivio di staging provvisorio. | False | No
**linkedServiceName** | Specificare il nome di un servizio collegato [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) o [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) che fa riferimento all'istanza di archiviazione usata come archivio di staging provvisorio. <br/><br/> L'archiviazione non può essere usata con una firma di accesso condiviso per caricare dati in SQL Data Warehouse tramite PolyBase. Può essere usata in tutti gli altri scenari. | N/D | Sì, quando **enableStaging** è impostato su TRUE
**path** | Specificare il percorso dell'archivio BLOB che deve contenere i dati di staging. Se non si specifica un percorso, il servizio crea un contenitore in cui archiviare i dati temporanei. <br/><br/> Specificare un percorso solo se si usa l'archiviazione con una firma di accesso condiviso o se i dati temporanei devono trovarsi in un percorso specifico. | N/D | No
**enableCompression** | Per ridurre il volume dei dati trasferiti, specificare se è necessario comprimere i dati quando il servizio di spostamento dati li sposta da un archivio dati di origine a un archivio dati sink. | False | No

Di seguito è riportata una definizione di esempio di attività di copia con le proprietà descritte nella tabella precedente:

	"activities":[  
	{
		"name": "Sample copy activity",
		"type": "Copy",
		"inputs": [{ "name": "OnpremisesSQLServerInput" }],
		"outputs": [{ "name": "AzureSQLDBOutput" }],
		"typeProperties": {
			"source": {
				"type": "SqlSource",
			},
			"sink": {
				"type": "SqlSink"
			},
	    	"enableStaging": true,
			"stagingSettings": {
				"linkedServiceName": "MyStagingBlob",
				"path": "stagingcontainer/path",
				"enableCompression": true
			}
		}
	}
	]


### Impatto della fatturazione
I costi addebitati si basano su due passaggi: durata della copia e tipo di copia.

- Quando si usa la funzionalità di staging durante una copia nel cloud, ovvero la copia di dati da un archivio dati cloud a un altro archivio dati cloud, ad esempio da Data Lake Store a SQL Data Warehouse, il costo addebitato sarà [somma della durata della copia per i passaggi 1 e 2] x [prezzo unitario della copia nel cloud].
- Quando si usa la funzionalità di staging durante una copia ibrida, ovvero la copia di dati da un archivio dati locale a un archivio dati cloud, ad esempio da un database di SQL Server locale a SQL Data Warehouse, il costo addebitato sarà [durata della copia ibrida] x [prezzo unitario della copia ibrida] + [durata della copia nel cloud] x [prezzo unitario della copia nel cloud].


## Considerazioni sull'origine
### Generale
Assicurarsi che l'archivio dati sottostante non sia sovraccarico a causa di altri carichi di lavoro in esecuzione in o su di esso.

Per gli archivi dati Microsoft, vedere gli [argomenti sul monitoraggio e l'ottimizzazione](#performance-reference) specifici degli archivi dati, per comprendere meglio le caratteristiche delle prestazioni degli archivi dati e come ridurre al minimo i tempi di risposta e ottimizzare la velocità effettiva.

Se si copiano dati da un archivio BLOB a SQL Data Warehouse, valutare l'uso di **PolyBase** per migliorare le prestazioni. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse).


### Archivi dati basati su file
*Inclusi archivi BLOB, Data Lake Store e file system locali*

- **Dimensioni medie dei file e numero medio di file**: l'attività di copia trasferisce i dati procedendo un file alla volta. Con la stessa quantità di dati da spostare, la velocità effettiva generale risulta inferiore se i dati sono costituiti da un numero elevato di file piccoli anziché da pochi file di grandi dimensioni. Ciò è dovuto alla fase di bootstrap necessaria per ogni file. Se possibile, combinare quindi i file piccoli in file più grandi per raggiungere una maggiore velocità effettiva.
- **Formato di file e compressione**: per altre informazioni su come migliorare le prestazioni, vedere le sezioni [Considerazioni sulla serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization) e [Considerazioni sulla compressione](#considerations-for-compression).
- Per scenari di **file system locali** in cui è necessario usare **Gateway di gestione dati**, vedere la sezione [Considerazioni su Gateway di gestione dati](#considerations-for-data-management-gateway).

### Archivi dati relazionali
*Inclusi database SQL, SQL Data Warehouse, database di SQL Server e database Oracle, MySQL, DB2, Teradata, Sybase e PostgreSQL*

- **Modello di dati**: lo schema di tabella influisce sulla velocità effettiva di copia. Per copiare la stessa quantità di dati, dimensioni di riga grandi offrono prestazioni migliori rispetto a dimensioni di riga piccole, perché il database può recuperare in modo più efficiente un numero inferiore di batch di dati contenenti meno righe.
- **Query o stored procedure**: ottimizzare la logica della query o della stored procedure specificata nell'origine dell'attività di copia per recuperare i dati in modo più efficiente.
- Per i **database relazionali locali** come SQL Server e Oracle, in cui è necessario usare **Gateway di gestione dati**, vedere la sezione [Considerazioni su Gateway di gestione dati](#considerations-on-data-management-gateway).

## Considerazioni sul sink

### Generale
Assicurarsi che l'archivio dati sottostante non sia sovraccarico a causa di altri carichi di lavoro in esecuzione in o su di esso.

Per gli archivi dati Microsoft, vedere gli [argomenti sul monitoraggio e l'ottimizzazione](#performance-reference) specifici per gli archivi dati, per comprendere meglio le caratteristiche delle prestazioni degli archivi dati e come ridurre al minimo i tempi di risposta e ottimizzare la velocità effettiva.

Se si copiano dati da un **archivio BLOB** a **SQL Data Warehouse**, valutare l'uso di **PolyBase** per migliorare le prestazioni. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse).


### Archivi dati basati su file
*Inclusi archivi BLOB, Data Lake Store e file system locali*

- **Comportamento di copia**: se si copiano dati da un diverso archivio dati basato su file, l'attività di copia fornisce tre tipi di comportamento tramite la proprietà **copyBehavior**, mantenere la gerarchia, rendere flat la gerarchia e unire i file. Conservare o rendere flat la gerarchia comporta un overhead delle prestazioni minimo, mentre unire i file provoca un aumento dell'overhead delle prestazioni.
- **Formato di file e compressione**: per altre informazioni su come migliorare le prestazioni, vedere le sezioni [Considerazioni sulla serializzazione e deserializzazione](#considerations-for-serialization-and-deserialization) e [Considerazioni sulla compressione](#considerations-for-compression).
- **Archivio BLOB**: attualmente l'archivio BLOB supporta solo i BLOB in blocchi per l'ottimizzazione della velocità effettiva e del trasferimento dati.
- Per scenari di **file system locali** in cui è necessario usare **Gateway di gestione dati**, vedere la sezione [Considerazioni su Gateway di gestione dati](#considerations-for-data-management-gateway).

### Archivi dati relazionali
*Inclusi database SQL, SQL Data Warehouse e database di SQL Server*

- **Comportamento di copia**: a seconda delle proprietà configurate per **sqlSink**, l'attività di copia scrive i dati nel database di destinazione in modi diversi.
	- Per impostazione predefinita, il servizio di spostamento dati usa l'API per la copia bulk per inserire i dati in modalità Append, che offre le prestazioni migliori.
	- Se si configura una stored procedure nel sink, il database applica i dati una riga alla volta anziché come caricamento bulk e le prestazioni ne risentono notevolmente. Se il set di dati è di grandi dimensioni, valutare l'opportunità di passare a usare la proprietà **sqlWriterCleanupScript**, se applicabile.
	- Se si configura la proprietà **sqlWriterCleanupScript** per ogni esecuzione dell'attività di copia, il servizio attiva lo script e quindi usa l'API per la copia bulk per inserire i dati. Ad esempio, per sovrascrivere l'intera tabella con i dati più recenti, è possibile specificare uno script per eliminare tutti i record prima del caricamento bulk dei nuovi dati dall'origine.
- **Modello di dati e dimensioni batch**:
	- Lo schema di tabella influisce sulla velocità effettiva di copia. Per copiare la stessa quantità di dati, dimensioni di riga grandi offrono prestazioni migliori rispetto a dimensioni di riga piccole, perché il database può eseguire in modo più efficiente il commit di un numero inferiore di batch di dati.
	- L'attività di copia inserisce i dati in una serie di batch. Per impostare il numero di righe in un batch è possibile usare la proprietà **writeBatchSize**. Se le righe dei dati sono di piccole dimensioni, è possibile impostare la proprietà **writeBatchSize** con un valore più elevato per sfruttare l'overhead di un numero minore di batch e aumentare la velocità effettiva. Se le righe sono di grandi dimensioni, prestare attenzione quando si aumenta il valore di **writeBatchSize**. Un valore elevato può causare un errore di copia dovuto a un sovraccarico del database.
- Per i **database relazionali locali** come SQL Server e Oracle, in cui è necessario usare **Gateway di gestione dati**, vedere la sezione [Considerazioni su Gateway di gestione dati](#considerations-for-data-management-gateway).


### Archivi NoSQL
*Inclusi gli archivi tabelle e Azure DocumentDB*

- Per gli **archivi tabelle**:
	- **Partizione**: scrivere i dati nelle partizioni con interleave riduce drasticamente le prestazioni. È possibile ordinare i dati di origine per chiave di partizione in modo che i dati vengano inseriti in modo efficiente in una partizione dopo l'altra oppure è possibile modificare la logica e scrivere i dati in una sola partizione.
- Per **DocumentDB**:
	- **Dimensioni batch**: la proprietà **writeBatchSize** permette di impostare il numero di richieste parallele per la creazione di documenti al servizio DocumentDB. Aumentando **writeBatchSize** è possibile prevedere prestazioni migliori, perché vengono inviate più richieste parallele a DocumentDB. Tuttavia, quando si scrive in DocumentDB è opportuno tenere sotto controllo le limitazioni. Il messaggio di errore è: "La frequenza delle richieste è troppo elevata". Le limitazioni possono essere dovute a vari fattori, incluse le dimensioni dei documenti, il numero di termini nei documenti e i criteri di indicizzazione della raccolta di destinazione. Per ottenere una maggiore velocità effettiva di copia, valutare la possibilità di usare una raccolta più avanzata, ad esempio S3.

## Considerazioni sulla serializzazione e deserializzazione
Serializzazione e deserializzazione possono verificarsi quando il set di dati di input o il set di dati di output è costituito da un file. L'attività di copia supporta attualmente i formati di dati Avro e Testo, ad esempio CSV e TSV.

**Comportamento di copia**:

-	Copia di file tra archivi dati basati su file:
	- Quando i set di dati di input e di output hanno le stesse impostazioni del formato di file o non hanno tali impostazioni, il servizio di spostamento dati esegue una copia binaria senza alcuna serializzazione o deserializzazione. La velocità effettiva sarà superiore rispetto allo scenario, in cui le impostazioni del formato di file di origine e del sink sono diverse tra loro.
	- Quando i set di dati di input e di output sono entrambi in formato testo e solo il tipo di codifica è diverso, il servizio di spostamento esegue solo la conversione della codifica. Non esegue alcuna operazione di serializzazione o deserializzazione e questo dà luogo a un certo overhead delle prestazioni rispetto alla copia binaria.
	- Quando i set di dati di input e di output hanno entrambi formati di file diversi o configurazioni diverse, ad esempio i delimitatori, il servizio di spostamento dati deserializza i dati di origine per trasmetterli, trasformarli e quindi serializzarli nel formato di output indicato. Questa operazione comporta un overhead delle prestazioni decisamente maggiore rispetto ad altri scenari.
- Quando si copiano file da e in un archivio dati che non è basato su file, ad esempio da un archivio basato su file in un archivio relazionale, il passaggio di serializzazione o deserializzazione è necessario. Questo passaggio comporta un notevole overhead delle prestazioni.

**Formato di file**: il formato di file scelto può influire sulle prestazioni di copia. Ad esempio, Avro è un formato binario compresso che archivia i metadati con i dati. È ampiamente supportato nell'ecosistema di Hadoop per l'elaborazione e l'esecuzione di query. Avro è tuttavia più costoso a livello di serializzazione e deserializzazione e di conseguenza la velocità effettiva di copia risulta inferiore rispetto al formato testo. Scegliere il formato di file per tutto il flusso di elaborazione a livello globale. Considerare prima di tutto il formato in cui i dati vengono salvati nell'archivio dati di origine o devono essere estratti dai sistemi esterni, il formato migliore per l'archiviazione, l'elaborazione analitica e le query e il formato in cui i dati devono essere esportati nei data mart per gli strumenti di creazione di report e visualizzazione. A volte un formato di file non ottimale dal punto di vista delle prestazioni di lettura e scrittura può invece essere una buona scelta dal punto di vista del processo analitico generale.

## Considerazioni sulla compressione
Quando il set di dati di input o di output è un file, è possibile impostare l'attività di copia per l'esecuzione della compressione o decompressione durante la scrittura dei dati nella destinazione. La scelta della compressione comporta un compromesso tra input/output (I/O) e CPU. La compressione dei dati ha un costo maggiore in termini di risorse di calcolo, ma riduce l'I/O di rete e l'archiviazione. A seconda dei dati, si potrebbe riscontrare un aumento della velocità effettiva di copia complessiva.

**Codec**: l'attività di copia supporta i tipi di compressione gzip, bzip2 e Deflate. Azure HDInsight può utilizzare tutti e tre i tipi per l'elaborazione. Ogni codec di compressione presenta dei vantaggi. Ad esempio, bzip2 ha la velocità effettiva copia più bassa, ma offre prestazioni di query Hive migliori perché permette di dividerle per l'elaborazione. Gzip è l'opzione più bilanciata e quella usata più di frequente. Scegliere il codec più adatto allo scenario end-to-end personalizzato.

**Livello**: per ogni codec di compressione è possibile scegliere tra due opzioni, la compressione più veloce e la compressione ottimale. L'opzione di compressione più veloce comprime i dati il più rapidamente possibile, anche se il file risultante non viene compresso in modo ottimale. L'opzione di compressione ottimale impiega più tempo per la compressione e restituisce una quantità minima di dati. È possibile testare entrambe le opzioni per verificare quale offra le migliori prestazioni complessive in base alle proprie esigenze.

**Una considerazione**: per copiare una grande quantità di dati tra un archivio locale e il cloud, è consigliabile usare un archivio BLOB provvisorio e la compressione. L'uso di un archivio provvisorio risulta utile quando la larghezza di banda della rete aziendale e i servizi di Azure pongono dei limiti e i set di dati di input e di output devono essere in formato non compresso. In particolare, è possibile dividere un'attività di copia singola in due attività di copia. La prima copia dall'origine in un BLOB di staging o provvisorio in formato compresso. La seconda copia i dati compressi dal BLOB di staging e li decomprime durante la scrittura nel sink.

## Considerazioni sul mapping di colonne
È possibile impostare la proprietà **columnMappings** nell'attività di copia perché venga eseguito il mapping di tutte o di un subset delle colonne di input alle colonne di output. Dopo aver letto i dati dall'origine, il servizio di spostamento dati deve eseguire il mapping delle colonne sui dati prima di scriverli nel sink. Questa ulteriore elaborazione riduce la velocità effettiva di copia.

Se l'archivio dati di origine è disponibile per query, ad esempio nel caso di un archivio relazionale come il database SQL o SQL Server o nel caso di un archivio NoSQL come un archivio tabelle o DocumentDB, è consigliabile eseguire il push della logica di filtro e riordinamento colonne per la proprietà **query** anziché usare il mapping colonne. In questo modo la proiezione si verifica quando il servizio di spostamento dati legge i dati dall'archivio dati di origine, in cui è molto più efficiente.

## Considerazioni su Gateway di gestione dati
Per le raccomandazioni sulla configurazione del gateway, vedere le [considerazioni per l'uso di Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway).

**Ambiente del computer gateway**: è consigliabile usare un computer dedicato per ospitare Gateway di gestione dati. Usare strumenti come PerfMon per esaminare la CPU, la memoria e l'uso della larghezza di banda durante un'operazione di copia nel computer gateway. Se la CPU, la memoria o la larghezza di banda di rete diventa un collo di bottiglia, passare a un computer più potente.

**Esecuzioni simultanee dell'attività di copia**: una singola istanza di Gateway di gestione dati può gestire più esecuzioni dell'attività di copia nello stesso momento, o simultaneamente. Il numero massimo di processi simultanei viene calcolato in base alla configurazione hardware del computer gateway. I processi di copia aggiuntivi vengono accodati finché non vengono selezionati dal gateway o non si verifica il timeout di un altro processo. Per evitare la contesa per le risorse nel computer gateway, è possibile usare la funzionalità di staging sulla pianificazione dell'attività di copia per ridurre il numero di processi di copia in coda allo stesso tempo o valutare la possibilità di dividere il carico in più computer gateway.


## Altre considerazioni
Se i dati da copiare sono di grandi dimensioni, è possibile modificare la logica di business per partizionare ulteriormente i dati usando il meccanismo di sezionamento in Data Factory. Quindi, pianificare esecuzioni più frequenti dell'attività di copia per ridurre le dimensioni dei dati per ogni singola esecuzione.

Prestare attenzione al numero di set di dati e di attività di copia che richiedono la connessione di Data Factory allo stesso archivio dati nello stesso momento. Un numero elevato di processi di copia simultanei può limitare un archivio dati e causare un peggioramento delle prestazioni, nuovi tentativi interni dei processi di copia e, in alcuni casi, errori di esecuzione.

## Case study: copiare da un'istanza locale di SQL Server in un archivio BLOB
**Scenario**: viene compilata una pipeline per copiare dati da un'istanza locale di SQL Server in un archivio BLOB in formato CSV. Per velocizzare il processo di copia, i file CSV devono essere compressi in formato bzip2.

**Test e analisi**: la velocità effettiva dell'attività di copia è inferiore a 2 Mbps ed è molto inferiore al benchmark delle prestazioni.

**Analisi e ottimizzazione delle prestazioni**: per risolvere il problema delle prestazioni, occorre vedere prima di tutto come vengono elaborati e spostati i dati.

1.	**Lettura dei dati**: il gateway apre una connessione a SQL Server e invia la query. SQL Server risponde inviando il flusso di dati al gateway tramite Intranet.
2.	**Serializzazione e compressione dei dati**: il gateway serializza il flusso dei dati in formato CSV e comprime i dati in un flusso bzip2.
3.	**Scrittura dei dati**: il gateway carica il flusso bzip2 nell'archivio BLOB tramite Internet.

Come si può notare, i dati vengono elaborati e spostati in base a un flusso sequenziale: SQL Server > LAN > Gateway > WAN > archivio BLOB. **Le prestazioni complessive vengono controllate dalla velocità effettiva minima in tutta la pipeline**.

![Flusso di dati](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Uno o più dei fattori seguenti possono provocare un collo di bottiglia nelle prestazioni:

-	**Origine**: SQL Server ha di per sé una velocità effettiva bassa a causa dei carichi elevati.
-	**Gateway di gestione dati**:
	-	**LAN**: il gateway è distante da SQL Server con una connessione a larghezza di banda bassa.
	-	**Gateway**: il gateway ha raggiunto i relativi limiti di carico per eseguire queste operazioni:
		-	**Serializzazione**: la serializzazione del flusso di dati in formato CSV ha una velocità effettiva bassa.
		-	**Compressione**: si è scelto un codec di compressione lenta, ad esempio bzip2 a 2,8 Mbps con core i7.
	-	**WAN**: la larghezza di banda tra la rete aziendale e i servizi di Azure è bassa, ad esempio T1 = 1.544 Kbps e T2 = 6.312 Kbps.
-	**Sink**: l'archivio BLOB ha una velocità effettiva bassa. Questo scenario è poco probabile perché il relativo contratto di servizio garantisce un minimo di 60 Mbps.

In tal caso, la compressione dati bzip2 potrebbe rallentare l'intera pipeline. Il passaggio al codec di compressione gzip può ridurre questo collo di bottiglia.


## Case study: usare la copia parallela  

**Scenario I**: copiare 1.000 file da 1 MB dal file system locale nell'archivio BLOB.

**Analisi e ottimizzazione delle prestazioni**: si supponga di aver installato Gateway di gestione dati in un computer quad-core. Data Factory usa 16 copie parallele per spostare simultaneamente i file dal file system all'archivio BLOB. L'esecuzione parallela deve garantire una velocità effettiva elevata. È anche possibile specificare in modo esplicito il numero di copie parallele. Quando si copiano molti file di piccole dimensioni, le copie parallele migliorano notevolmente la velocità effettiva garantendo un uso più efficiente delle risorse.

![Scenario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenario II**: copiare 20 BLOB da 500 MB dall'archivio BLOB in Data Lake Store Analytics e ottimizzare le prestazioni.

**Analisi e ottimizzazione delle prestazioni**: in questo scenario Data Factory copia i dati dall'archivio BLOB in Data Lake Store tramite copia singola, con **parallelCopies** impostato su 1, e unità di spostamento dati cloud singole. La velocità effettiva osservata si avvicinerà ai valori indicati nella sezione [Informazioni di riferimento sulle prestazioni](#performance-reference) precedente.

![Scenario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Scenario III**: i singoli file hanno grandi dimensioni e il volume totale molto elevato.

**Analisi e ottimizzazione delle prestazioni**: l'aumento di **parallelCopies** non permette di migliorare le prestazioni di copia a causa dei limiti previsti per le risorse di una unità di spostamento dati cloud singola. È invece necessario specificare altre unità di spostamento dati cloud per ottenere più risorse ed eseguire lo spostamento dati. Non specificare un valore per la proprietà **parallelCopies**. Il parallelismo è gestito da Data Factory. Se, in questo caso, si imposta **cloudDataMovementUnits** su 4, è possibile ottenere una velocità effettiva di circa quattro volte superiore.

![Scenario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## Riferimenti
Di seguito sono riportati alcuni riferimenti sul monitoraggio e l'ottimizzazione delle prestazioni per alcuni degli archivi dati supportati:

- Archiviazione di Azure, inclusi archivi BLOB e archivi tabelle: [Obiettivi di scalabilità e prestazioni per Archiviazione di Azure](../storage/storage-scalability-targets.md) ed [Elenco di controllo di prestazioni e scalabilità per Archiviazione di Microsoft Azure](../storage//storage-performance-checklist.md).
- Database SQL di Azure: è possibile [monitorare le prestazioni](../sql-database/sql-database-service-tiers.md#monitoring-performance) e controllare la percentuale di DTU (Database Transaction Unit).
- Azure SQL Data Warehouse: la funzionalità viene misurata in unità data warehouse (DWU). Vedere in proposito [Gestire la potenza di calcolo in Azure SQL Data Warehouse (Panoramica)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
- Azure DocumentDB: [Livelli di prestazioni in DocumentDB](../documentdb/documentdb-performance-levels.md).
- SQL Server locale: [Monitoraggio e ottimizzazione delle prestazioni](https://msdn.microsoft.com/library/ms189081.aspx).
- File server locale: [Performance Tuning for File Servers](https://msdn.microsoft.com/library/dn567661.aspx) (Ottimizzazione delle prestazioni per i file server).

<!---HONumber=AcomDC_0831_2016-->