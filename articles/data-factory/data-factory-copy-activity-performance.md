<properties
	pageTitle="Guida alle prestazioni delle attività di copia e all'ottimizzazione | Microsoft Azure"
	description="Informazioni sui fattori chiave che influiscono sulle prestazioni di spostamento dei dati in Data factory di Azure tramite l'attività di copia."
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
	ms.date="06/03/2016"
	ms.author="spelluru"/>


# Guida alle prestazioni delle attività di copia e all'ottimizzazione
Questo articolo descrive i fattori chiave che influiscono sulle prestazioni di spostamento dei dati (attività di copia) in Data factory di Azure. Elenca anche le prestazioni osservate durante i test interni e descrive i modi per ottimizzare le prestazioni dell'attività di copia.

L'attività di copia consente uno spostamento dei dati a velocità effettiva elevata come spiegano gli esempi seguenti:

- Inserimento di 1 TB di dati nell'archiviazione BLOB di Azure dal file system locale e dall'archiviazione BLOB di Azure in meno di 3 ore, vale a dire a una velocità di 100 MBps.
- Inserimento di 1 TB di dati nell'archivio Azure Data Lake dal file system locale e dall'archiviazione BLOB di Azure in meno di 3 ore, vale a dire a una velocità di 100 MBps.
- Inserimento di 1 TB di dati in Azure SQL Data Warehouse dall'archiviazione BLOB di Azure in meno di 3 ore, vale a dire a una velocità di 100 MBps.

Vedere le sezioni seguenti per altre informazioni sulle prestazioni delle attività di copia e suggerimenti per ottimizzarle.

> [AZURE.NOTE] Se non si ha una conoscenza generale dell'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md) prima di continuare a leggere l'articolo.

## Procedura di ottimizzazione delle prestazioni
Di seguito sono indicati i passaggi tipici da eseguire per ottimizzare le prestazioni della soluzione Data factory di Azure con l'attività di copia.

1.	**Stabilire una baseline.** Durante la fase di sviluppo, testare la pipeline con l'attività di copia confrontandola con dati di esempio rappresentativi. È possibile sfruttare il [modello di sezionamento](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) di Data factory di Azure per limitare la quantità di dati usati.

	Usare l'**app di monitoraggio e gestione** per raccogliere informazioni su tempo di esecuzione e caratteristiche di prestazione. Fare clic sul riquadro **Monitoraggio e gestione** nella home page della data factory, selezionare il **set di dati di output** nella visualizzazione ad albero e quindi selezionare l'attività di copia nell'elenco **Activity Windows** (Finestre attività). Nell'elenco **Activity Windows** (Finestre attività) verrà visualizzate la durata dell'attività di copia, mentre la dimensione dei dati copiati e la velocità effettiva saranno visualizzate nella finestra **Activity Window Explorer** (Esplora finestra attività) a destra. Vedere [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md) per altre informazioni sull'app.
	
	![Dettagli esecuzione attività](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

	È possibile confrontare le prestazioni e le configurazioni del proprio scenario con le [informazioni di riferimento sulle prestazioni](#performance-reference) basate sulle osservazioni interne pubblicate più avanti.
2. **Diagnosi e ottimizzazione delle prestazioni.** Se le prestazioni osservate non rispondono alle aspettative, è necessario identificare i colli di bottiglia delle prestazioni ed eseguire le ottimizzazioni opportune per eliminare o ridurre l'impatto dei colli di bottiglia. Una descrizione completa della diagnosi delle prestazioni non rientra nell'ambito di questo argomento, ma qui di seguito sono elencate alcune considerazioni comuni.
	- [Origine](#considerations-on-source)
	- [Sink](#considerations-on-sink)
	- [Serializzazione/Deserializzazione](#considerations-on-serializationdeserialization)
	- [Compressione](#considerations-on-compression)
	- [Mapping di colonne](#considerations-on-column-mapping)
	- [Gateway di gestione dati](#considerations-on-data-management-gateway)
	- [Altre considerazioni](#other-considerations)
	- [Copia parallela](#parallel-copy)
	- [Unità di spostamento dei dati cloud](#cloud-data-movement-units)

3. **Espandere la configurazione all'insieme dei dati.** Una volta verificati i risultati dell'esecuzione e le prestazioni, è possibile espandere la definizione del set di dati e il periodo attivo della pipeline per coprire tutti i dati presi in considerazione.

## Informazioni di riferimento sulle prestazioni
> [AZURE.IMPORTANT] **Dichiarazione di non responsabilità:** i dati seguenti sono stati pubblicati esclusivamente come linee guida e per fornire una pianificazione generale. Si presume che la larghezza di banda, l'hardware, la configurazione e così via siano di livello ottimale. Usare queste informazioni solo come riferimento. La velocità effettiva dello spostamento dati osservata verrà influenzata da numerose variabili. Vedere le sezioni seguenti per informazioni su come ottimizzare e ottenere prestazioni migliori in base alle proprie esigenze nell'ambito dello spostamento dati. Questi dati verranno aggiornati quando saranno disponibili nuovi miglioramenti e funzionalità per l'incremento delle prestazioni.

![Matrice delle prestazioni](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

Punti da notare:

- La velocità effettiva viene calcolata con la formula seguente: [dimensione dei dati letti dall'origine]/[durata dell'esecuzione dell'attività di copia]
- Per calcolare i valori precedenti, è stato usato il set di dati [TPC-H](http://www.tpc.org/tpch/).
- Nel caso degli archivi dati di Microsoft Azure, l'origine e il sink sono nella stessa area di Azure.
- Impostazione di **cloudDataMovementUnits** su 1 e valore di **parallelCopies** non specificato.
- Nel caso dello spostamento dati ibrido (da locale a cloud o da cloud a locale), il Gateway di gestione dati (istanza singola) era ospitato in un computer diverso da quello dell'archivio dati locale, con la configurazione seguente. Si noti che, con una sola attività in esecuzione nel gateway, l'operazione di copia ha utilizzato solo una piccola parte delle risorse della CPU/memoria di questo computer e della larghezza di banda di rete.
	<table>
	<tr>
		<td>CPU</td>
		<td>32 core, 2,20 GHz, Intel Xeon® E5-2660 v2</td>
	</tr>
	<tr>
		<td>Memoria</td>
		<td>128 GB</td>
	</tr>
	<tr>
		<td>Rete</td>
		<td>Interfaccia Internet: 10 Gbps; Interfaccia Intranet: 40 Gbps</td>
	</tr>
	</table>

## Copia parallela
Per ottimizzare la velocità effettiva di un'operazione di copia e ridurre il tempo di spostamento dei dati, è possibile considerare di leggere i dati dall'origine e/o scrivere i dati nella destinazione **parallelamente all'esecuzione di un'attività di copia**.
 
Si noti che questa impostazione è diversa dalla proprietà **Concurrency** disponibile nella definizione dell'attività. La proprietà Concurrency determina il numero di **attività di copia eseguite contemporaneamente** in runtime per elaborare i dati da finestre attività diverse (dall'1 alle 2, dalle 2 alle 3, dalle 3 alle 4 e così via). È utile quando si esegue un carico cronologico. Nel caso discusso in questo articolo la copia parallela è applicata all'**esecuzione di una singola attività**.

**Scenario di esempio**: considerare l'esempio seguente in cui è necessario elaborare più sezioni dati del passato. Il servizio Data Factory esegue un'istanza dell'attività di copia, ossia esegue un' attività, per ogni sezione dati.

- Sezione dati dalla prima finestra attività (dall'1 alle 2) = = > esecuzione attività 1
- Sezione dati dalla seconda finestra attività (dalle 2 alle 3) = = > esecuzione attività 2
- Sezione dati dalla seconda finestra attività (dalle 3 alle 4) = = > esecuzione attività 3
- e così via.

Avendo impostato la proprietà **Concurrency** su **2** in questo esempio, l'**esecuzione attività 1** e l'** esecuzione attività 2** possono copiare i dati da due finestre attività **contemporaneamente** così da migliorare le prestazioni dello spostamento dei dati. Se tuttavia più file sono associati all'esecuzione dell'attività 1, viene copiato dall'origine nella destinazione un file per volta.

### Proprietà parallelCopies
È possibile usare la proprietà **parallelCopies** per indicare il parallelismo che l'attività di copia deve usare. In altre parole, considerare questa proprietà come numero massimo di thread all'interno di un'attività di copia che leggono dall'origine e/o scrivono negli archivi di dati del sink in parallelo.

Per ogni esecuzione dell'attività di copia, Azure Data Factory determina in modo intelligente il numero di copie parallele da usare per copiare i dati dall'archivio dell'origine dati nell'archivio dati di destinazione. Il numero predefinito di copie parallele usate dipende dai tipi di origine e sink:

Origine e sink |	Numero predefinito di copie parallele determinato dal servizio
------------- | -------------------------------------------------
Copia di dati tra **archivi basati su file**, ad esempio BLOB di Azure, Azure Data Lake,file system locale, HDFS locale | Qualsiasi numero compreso **tra 1 e 32** in base alla **dimensione dei file** e al **numero di unità di spostamento dei dati cloud** (vedere la sezione successiva per la definizione) usate per la copia dei dati tra due archivi dati cloud (o) la configurazione fisica del computer del gateway impiegato per la copia ibrida (copia di dati in/da un archivio dati locale)
Copia di dati da **qualsiasi archivio dati di origine in Tabella di Azure** | 4
Tutte le altre coppie di origine e sink | 1

Nella maggior parte dei casi il comportamento predefinito dovrebbe garantire la velocità effettiva migliore. È tuttavia possibile sostituire il valore predefinito specificando un valore per la proprietà **parallelCopies** in modo da controllare il carico degli archivi dati sui computer o ottimizzare le prestazioni di copia. Deve essere compreso tra **1 e 32, in entrambi i casi**. In fase di esecuzione dell'attività di copia sarà scelto il valore minore o uguale al valore configurato così da garantire prestazioni ottimali.

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

Tenere presente quanto segue:

- Per copiare i dati tra archivi basati su file, il parallelismo avviene a livello di file. In altre parole, non si verifica una suddivisione in blocchi all'interno di un singolo file. Il numero effettivo di copie parallele usate per l'operazione di copia non sarà maggiore del numero dei file. Se il comportamento di copia è di tipo mergeFile, non sarà possibile sfruttare il parallelismo.
- Quando si specifica un valore per la proprietà parallelCopies, considerare che l'aumento del carico influisce sugli archivi dati di origine e sink, nonché sul gateway, in caso di una copia ibrida, soprattutto quando le attività sono multiple o le stesse attività vengono eseguite contemporaneamente sullo medesivo archivio dati. Se si nota che l'archivio dati o il gateway è sovraccaricato, diminuire il valore di parallelCopies per alleggerirlo.
- Quando i dati vengono copiati da archivi dati non basati su file in archivi basati su file, la proprietà parallelCopies non viene considerata anche se è specificata e non si assiste a operazioni di parallelismo.

> [AZURE.NOTE] Per sfruttare la funzionalità parallelCopies durante una copia ibrida, è necessario usare una versione successiva alla 1.11 di Gateway di gestione dati.

### Unità di spostamento dei dati cloud
L'**unità di spostamento dei dati cloud** è una misura che rappresenta la potenza, ossia la combinazione tra CPU, memoria e allocazione di risorse di rete, di una singola unità nel servizio Azure Data Factory, usata per eseguire un'operazione di copia da cloud a cloud. Non è contemplata nel caso di copia ibrida. Per impostazione predefinita, il servizio Azure Data Factory usa un'unica unità di spostamento dei dati cloud per eseguire una singola attività di copia. È possibile sostituire questa impostazione predefinita specificando un valore per la proprietà **cloudDataMovementUnits**. A questo punto l'impostazione cloudDataMovementUnits viene **supportata solo** quando vengono copiati dati **tra due archiviazioni BLOB di Azure** o da un'**archiviazione BLOB di Azure a un archivio Azure Data Lake** e ha effetto quando i file da copiare sono più di uno e la dimensione di ogni file è uguale o maggiore a 16 MB.

Se si copiano file di dimensioni relativamente grandi, è possibile che, impostando un valore elevato della proprietà **parallelCopies**, le prestazioni non migliorino a causa delle risorse insufficienti di un'unica unità di spostamento dei dati cloud. In questi casi è possibile scegliere di usare più unità di spostamento dei dati cloud per copiare molti dati a una velocità effettiva elevata. Per specificare il numero di unità di spostamento dei dati cloud che l'attività di copia deve usare, impostare un valore per la proprietà **cloudDataMovementUnits** come illustrato di seguito:

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

I **valori consentiti** per la proprietà cloudDataMovementUnits sono: 1 (valore predefinito), 2, 4 e 8. Se sono necessarie più unità di spostamento dei dati cloud per aumentare la velocità effettiva, contattare il [supporto di Azure ](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Il **numero effettivo di unità di spostamento dei dati cloud** usate per l'operazione di copia sarà uguale o minore del valore configurato, a seconda del numero di file da copiare dall'origine che soddisfa i criteri di dimensione.

> [AZURE.NOTE] La proprietà parallelCopies deve essere >= alla proprietà cloudDataMovementUnits ove specificata. Se cloudDataMovementUnits è maggiore di 1, lo spostamento dei dati parallelo coinvolge cloudDataMovementUnits nell'attività di copia, migliorando così le prestazioni.

Se si copiano più file di grandi dimensione impostando la proprietà **cloudDataMovementUnits** su 2, 4 e 8, le prestazioni possono essere pari a 2x (2 volte), 4x e 7x rispetto al valore di riferimento specificato nella sezione Informazioni di riferimento sulle prestazioni.

Fare riferimento ai [casi d'uso di esempio](#case-study---parallel-copy) descritti in questo articolo per sfruttare le due proprietà sopra illustrate e ottimizzare la velocità effettiva dello spostamento dei dati.
 
È **importante** ricordare che l'addebito è basato sul tempo totale impiegato per l'operazione di copia. Di conseguenza, se un processo di copia impiegava un'ora con 1 unità cloud e ora richiede 15 minuti con 4 unità cloud, la fattura complessiva sarà pressoché identica. Ecco un altro scenario. Supporre che si usino 4 unità cloud e che la prima impieghi 10 minuti, la seconda 10 minuti, la terza 5 minuti e la quarta 5 minuti durante l'esecuzione di un'attività di copia. Verrà addebitato il tempo totale necessario per la copia,ossia per lo spostamento dei dati, ovvero 10 + 10 + 5 + 5 = 30 minuti. L'uso di **parallelCopies** non influisce sui costi.

## Copia di staging
Quando si copiano dati da un archivio dati di origine a un archivio dati sink, è possibile usare un archivio BLOB di Azure come archivio di staging provvisorio. Questa funzionalità di staging è particolarmente utile nei casi seguenti:

1.	**A volte occorre tempo per eseguire lo spostamento di dati ibrido, ad esempio dall'archivio dati locale a un archivio dati cloud o viceversa, su una connessione di rete lenta.** Per migliorare le prestazioni dello spostamento dei dati, è possibile comprimere i dati in locale, in modo da ridurre il tempo per spostare i dati tramite la rete all'archivio dati di staging nel cloud, e quindi decomprimere i dati nell'archivio di staging prima di caricarli nell'archivio dati di destinazione.
2.	**Si vogliono aprire solo le porte 80 e 443 nel firewall a causa dei criteri IT.** Ad esempio, quando si copiano dati da un archivio dati locale a un sink del database SQL di Azure o un sink di Azure SQL Data Warehouse, è necessario abilitare le comunicazioni TCP in uscita sulla porta 1433 per Windows Firewall e per il firewall aziendale. In questo scenario è possibile utilizzare Gateway di gestione dati per copiare inizialmente i dati in un archivio BLOB di Azure di staging, che avviene tramite HTTP(s) ovvero sulla porta 443, e quindi caricare i dati nel database SQL o in SQL Data Warehouse dall'archivio BLOB di staging. In questo flusso non è necessario abilitare la porta 1433.
3.	**Inserire dati da diversi archivi dati in Azure SQL Data Warehouse tramite PolyBase.** Azure SQL Data Warehouse fornisce PolyBase come meccanismo a velocità effettiva elevata per caricare grandi quantità di dati in SQL Data Warehouse. Richiede tuttavia che i dati di origine si trovino nell'archivio BLOB di Azure e soddisfi alcuni criteri aggiuntivi. Quando si caricano dati da un archivio dati diverso dall'archivio BLOB di Azure, è possibile abilitare la copia dei dati tramite un archivio BLOB di Azure di staging provvisorio. In questo caso Azure Data Factory eseguirà le trasformazioni richieste sui dati per assicurare che soddisfino i requisiti di PolyBase e quindi userà PolyBase per caricare i dati in SQL Data Warehouse. Per altri dettagli ed esempi, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).

### Come funziona la copia di staging
Quando si abilita la funzionalità di staging, i dati vengono prima copiati dall'archivio dati di origine all'archivio dati di staging (Bring Your Own) e quindi copiati dall'archivio dati di staging all'archivio dati sink. Data Factory di Azure gestirà automaticamente il flusso della fase 2 e pulirà anche i dati temporanei dall'archivio di staging dopo aver completato lo spostamento dei dati.

Nello **scenario di copia cloud** in cui gli archivi dati di origine e sink sono nel cloud e non utilizzano Gateway di gestione dati, le operazioni di copia vengono eseguite da **servizio Data Factory di Azure**.

![Copia di staging: scenario cloud](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

Invece, nello **scenario di copia ibrido ** in cui l'origine è locale e il sink è nel cloud, lo spostamento dei dati dall'archivio dati di origine all'archivio dati di staging viene eseguito da **Gateway di gestione dati** e lo spostamento dei dati dall'archivio dati di staging all'archivio dati sink viene eseguito dal **servizio Data Factory di Azure**. La copia di dati da un archivio dati cloud in un archivio dati locale tramite la gestione temporanea è supportata con flusso invertito.

![Copia di staging: scenario ibrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Quando si abilita lo spostamento dei dati usando l'archivio di staging, è possibile specificare se i dati devono essere compressi prima dello spostamento dall'archivio dati di origine all'archivio dati di staging/provvisorio e decompressi prima dello spostamento dall'archivio dati di staging/provvisorio all'archivio dati sink.

La copia di dati tra due archivi dati locali con archivio di staging non è supportata al momento e verrà abilitata a breve.

### Configurazione
È possibile configurare l'impostazione **enableStaging** su Copia attività per specificare se si vuole che i dati siano inseriti in un archivio BLOB di Azure di staging prima di essere caricati in un archivio dati di destinazione. Quando si imposta enableStaging su true, è necessario specificare proprietà aggiuntive elencate nella tabella seguente. È anche necessario creare un servizio collegato Archiviazione di Azure o Firma di accesso condiviso di Archiviazione di Azure come risorsa di staging, se ancora non ne è disponibile uno.

Proprietà | Descrizione | Valore predefinito | Obbligatorio
--------- | ----------- | ------------ | --------
enableStaging | Specificare se si vuole copiare i dati tramite un archivio di staging provvisorio. | False | No
linkedServiceName | Specificare il nome di un servizio collegato [AzureStoage](data-factory-azure-blob-connector.md#azure-storage-linked-service) o [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) che fa riferimento alla risorsa di archiviazione di Azure che sarà usata come archivio di staging provvisorio. <br/><br/> Si noti che una risorsa di archiviazione di Azure con firma di accesso condiviso non può essere usata per caricare dati in Azure SQL Data Warehouse tramite PolyBase. Può essere usata in tutti gli altri scenari. | N/D | Sì, quando enableStaging è impostato su true. 
path | Specificare il percorso nell'archivio BLOB di Azure che conterrà i dati di staging. Se non si specifica un percorso, il servizio creerà un contenitore per archiviare i dati temporanei. <br/><br/> Non è necessario specificare il percorso, a meno che non si usi Archiviazione di Azure con firma di accesso condiviso o ci siano requisiti particolari riguardanti la posizione in cui devono risiedere i dati temporanei. | N/D | No
enableCompression | Specificare se è necessario comprimere i dati quando vengono spostati dall'archivio dati di origine all'archivio dati sink, per ridurre il volume dei dati trasferiti in rete. | False | No

Ecco una definizione di esempio di Copia attività con le proprietà precedenti:

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
Il costo verrà addebitato in base alle due fasi di durata della copia e dell relativo tipo di copia rispettivamente, ovvero:

- Quando si usa la funzionalità di staging durante una copia sul cloud, ovvero la copia dei dati da un archivio dati cloud a un altro archivio dati cloud, ad esempio, da Azure Data Lake a Azure SQL Data Warehouse, il costo addebitato sarà [somma della durata della copia per i passaggi 1 e 2] x [prezzo unitario della copia sul cloud]
- Quando si usa la funzionalità di staging durante una copia ibrida, ovvero una copia dei dati da un archivio dati locale a un archivio dati cloud, ad esempio da SQL Server locale ad Azure SQL Data Warehouse, il costo addebitato sarà [durata della copia ibrida] x [prezzo unitario della copia ibrida] + [cloud della copia durata] x [prezzo unitario della copia sul cloud]


## Considerazioni sull'origine
### Generale
Assicurarsi che l'archivio dati sottostante non venga sovraccaricato da altri carichi di lavoro in esecuzione in/per tale archivio, tra cui, ad esempio l'attività di copia.

Per gli archivi dati Microsoft, vedere gli [argomenti sul monitoraggio e l'ottimizzazione](#appendix-data-store-performance-tuning-reference) specifici degli archivi dati per informazioni sulle caratteristiche delle prestazioni degli archivi dati e su come ridurre i tempi di risposta e ottimizzare la velocità effettiva.

Se i dati vengono copiati da **Archivio BLOB di Azure** in **Azure SQL Data Warehouse**, provare ad abilitare **PolyBase** per migliorare le prestazioni. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse).


### Archivi dati basati su file
*Include il BLOB di Azure, Azure Data Lake e il file system locale*

- **Dimensioni del file e numero di file medi**: l'attività di copia trasferisce un file di dati per volta. Con la stessa quantità di dati da spostare, la velocità effettiva generale sarà minore se i dati sono costituiti da un numero elevato di piccoli file invece che da un piccolo numero di file più grandi, a causa della fase di bootstrap necessaria per ogni file. Se possibile, combinare quindi i file piccoli in file più grandi per raggiungere una maggiore velocità effettiva.
- **Formato e compressione di file**: vedere le sezioni relative alle [considerazioni sulla serializzazione/deserializzazione](#considerations-on-serializationdeserialization) e alle [considerazioni sulla compressione](#considerations-on-compression) per altre informazioni su come migliorare le prestazioni.
- Per lo scenario del **file system locale** in cui è necessario usare il **Gateway di gestione dati**, vedere anche la sezione [Considerazioni sul gateway](#considerations-on-data-management-gateway).

### Archivi dati relazionali
*(Include Database SQL di Azure, Azure SQL Data Warehouse, Database SQL Server, Oracle Database, Database MySQL, Database DB2, Database Teradata, Database Sybase, Database PostgreSQL)*

- **Modello di dati**: lo schema tabella influisce sulla velocità effettiva di copia. Per copiare la stessa quantità di dati, le dimensioni di riga grandi offriranno prestazioni migliori delle dimensioni di riga piccole perché il database può recuperare in modo più efficiente meno batch di dati contenenti un numero minore di righe.
- **Query o stored procedure**: ottimizzare la logica della query o della stored procedure specificata nell'origine dell'attività di copia, per poter recuperare i dati in modo più efficiente.
- Per i **database relazionali locali**, ad esempio SQL Server e Oracle, in cui è necessario usare il **Gateway di gestione dati**, vedere anche la sezione [Considerazioni sul gateway](#considerations-on-data-management-gateway).

## Considerazioni sul sink

### Generale
Assicurarsi che l'archivio dati sottostante non venga sovraccaricato da altri carichi di lavoro in esecuzione in/per tale archivio, tra cui, ad esempio l'attività di copia.

Per gli archivi dati Microsoft, vedere gli [argomenti sul monitoraggio e l'ottimizzazione](#appendix-data-store-performance-tuning-reference) specifici degli archivi dati per informazioni sulle caratteristiche delle prestazioni degli archivi dati e su come ridurre i tempi di risposta e ottimizzare la velocità effettiva.

Se i dati vengono copiati da **Archivio BLOB di Azure** in **Azure SQL Data Warehouse**, provare ad abilitare **PolyBase** per migliorare le prestazioni. Per altre informazioni, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md###use-polybase-to-load-data-into-azure-sql-data-warehouse).


### Archivi dati basati su file
*Include il BLOB di Azure, Azure Data Lake e il file system locale*

- **Comportamento di copia**: se si copiano dati da un altro archivio dati basato su file, l'attività di copia fornisce tre tipi di comportamento tramite la proprietà "copyBehavior": mantenere la gerarchia, rendere flat la gerarchia e unire i file. Conservare o rendere flat la gerarchia comporta un overhead delle prestazioni minimo, mentre l'unione dei file genera un overhead aggiuntivo delle prestazioni.
- **Formato e compressione di file**: vedere le sezioni relative alle [considerazioni sulla serializzazione/deserializzazione](#considerations-on-serializationdeserialization) e alle [considerazioni sulla compressione](#considerations-on-compression) per altre informazioni su come migliorare le prestazioni.
- Per il **BLOB di Azure**, attualmente sono supportati solo i BLOB in blocchi per la velocità effettiva e il trasferimento dati ottimizzati.
- Per gli scenari del **file system locale** in cui è necessario usare il **Gateway di gestione dati**, vedere anche la sezione [Considerazioni sul gateway](#considerations-on-data-management-gateway).

### Archivi dati relazionali
*(Include Database SQL di Azure, Azure SQL Data Warehouse, Database SQL Server)*

- **Comportamento di copia**: a seconda delle proprietà configurate per "sqlSink", l'attività di copia scriverà i dati nel database di destinazione in modi diversi:
	- Per impostazione predefinita, il servizio di spostamento dati usa l'API per la copia bulk per inserire i dati in modalità Append, che offre le prestazioni migliori.
	- Se si configura una stored procedure nel sink, il database applicherà i dati una riga alla volta invece che con il caricamento bulk e di conseguenza le prestazioni diminuiranno considerevolmente. Se le dimensioni dei dati sono elevate, se possibile, valutare il passaggio alla proprietà "sqlWriterCleanupScript" (vedere più avanti).
	- Se si configura la proprietà "sqlWriterCleanupScript", per ogni esecuzione dell'attività di copia, il servizio attiverà prima lo script, quindi userà l'API per la copia bulk per inserire i dati. Ad esempio, per sovrascrivere l'intera tabella con i dati più recenti, è possibile specificare uno script per eliminare tutti i record prima del caricamento bulk dei nuovi dati dall'origine.
- **Modello di dati e dimensioni batch**:
	- Lo schema tabella influirà sulla velocità effettiva di copia. Per copiare la stessa quantità di dati, le dimensioni di riga grandi offriranno prestazioni migliori delle dimensioni di riga piccole perché il database può eseguire in modo più efficiente il commit di meno batch di dati.
	- L'attività di copia inserisce i dati in una serie di batch, in cui il numero di righe contenute in un batch può essere impostato con la proprietà "writeBatchSize". Se le righe dei dati sono di piccole dimensioni, è possibile impostare la proprietà "writeBatchSize" con un valore più elevato per sfruttare l'overhead di un numero minore di batch e aumentare la velocità effettiva. Se le dimensioni di riga dei dati sono grandi, prestare attenzione nell'aumentare writeBatchSize: un valore elevato può generare un errore di copia a causa del sovraccarico del database.
- Per i **database relazionali locali**, ad esempio SQL Server e Oracle, in cui è necessario usare il **Gateway di gestione dati**, vedere anche la sezione [Considerazioni sul gateway](#considerations-on-data-management-gateway).


### Archivi NoSQL
*(Include Tabella di Azure, Azure DocumentDB)*

- Per **Tabella di Azure**:
	- **Partizione**: scrivere i dati nelle partizioni con interfoliazione ridurrà drasticamente le prestazioni. È possibile scegliere di ordinare i dati di origine per chiave di partizione in modo che i dati vengano inseriti in modo efficiente in una partizione dopo l'altra oppure è possibile modificare la logica e scrivere i dati in una sola partizione.
- Per **Azure DocumentDB**:
	- **Dimensioni batch**: la proprietà "writeBatchSize" indica il numero di richieste parallele di creare documenti al servizio DocumentDB. È possibile prevedere prestazioni migliori quando si aumenta "writeBatchSize", perché vengono inviate più richieste parallele a DocumentDB. Prestare tuttavia attenzione alla limitazione delle richieste quando si scrive in DocumentDB (messaggio di errore "La frequenza delle richieste è troppo elevata"). La limitazione delle richieste può essere dovuta a diversi fattori, inclusi la dimensione dei documenti, il numero di termini nei documenti e i criteri di indicizzazione della raccolta di destinazione. Per raggiungere una maggiore velocità effettiva di copia, valutare la possibilità di usare una raccolta più avanzata (ad esempio, S3).

## Considerazioni sulla serializzazione/deserializzazione
La serializzazione e la deserializzazione possono verificarsi quando il set di dati di input o il set di dati di output è un file. L'attività di copia supporta attualmente i formati di testo Avro e Testo (ad esempio, CSV e TSV).

**Comportamenti di copia:**

- Quando si copiano file tra archivi dati basati su file:
	- Quando i set di dati sia di input che di output hanno le stesse o nessuna impostazione di formato di file, il servizio di spostamento dati eseguirà una copia binaria senza nessuna serializzazione/deserializzazione. Si osserverà quindi una velocità effettiva più elevata rispetto allo scenario in cui le impostazioni di formato di file di origine/sink sono diverse.
	- Quando i set di dati sia di input che di output sono in formato Testo e solo il tipo di codifica è diverso, il servizio di spostamento dati eseguirà solo la conversione della codifica senza alcuna serializzazione/deserializzazione, generando un overhead delle prestazioni rispetto alla copia binaria.
	- Quando i set di dati di input e di output hanno formati di file diversi o configurazioni diverse, ad esempio i delimitatori, il servizio di spostamento dati deserializzerà i dati di origine per eseguire lo streaming, la trasformazione e quindi la serializzazione nel formato di output desiderato. L'overhead delle prestazioni risultante sarà decisamente maggiore rispetto agli scenari precedenti.
- Quando si copiano file in/da un archivio dati non basato su file (ad esempio un archivio basato su file in un archivio relazionale), sarà necessario un passaggio di serializzazione o deserializzazione che genererà un overhead considerevole delle prestazioni.

**Formato di file:** la scelta del formato di file può influire sulle prestazioni della copia. Avro, ad esempio, è un formato binario compatto che archivia i metadati con i dati ed ha un ampio supporto nell'ecosistema Hadoop per l'elaborazione e le query. Avro è tuttavia più costoso a livello di serializzazione/deserializzazione e di conseguenza la velocità effettiva di copia è inferiore rispetto al formato Testo. La scelta del formato di file da usare con il flusso di elaborazione deve essere fatta a livello globale, considerando il formato in cui i dati vengono salvati nell'archivio dati di origine o devono essere estratti dai sistemi esterni, il formato migliore per l'archiviazione, l'elaborazione analitica e le query e il formato in cui i dati devono essere esportati nei data mart per gli strumenti di creazione di report e visualizzazione. A volte un formato di file non ottimale dal punto di vista delle prestazioni di lettura e scrittura può invece esserlo dal punto di vista del processo analitico generale.

## Considerazioni sulla compressione
Quando il set di dati di input o di output è un file, è possibile configurare l'attività di copia per l'esecuzione della compressione o decompressione durante la scrittura dei dati nella destinazione. Abilitando la compressione, si crea un compromesso tra l'I/O e la CPU: la compressione dei dati richiederà risorse di calcolo aggiuntive, ma in cambio ridurrà l'I/O di rete e lo spazio di archiviazione, il che, a seconda dei dati, può incrementare la velocità effettiva di copia complessiva.

**Codec:** sono supportati i tipi di compressione GZIP, BZIP2 e Deflate. I tre tipi possono essere tutti utilizzati da Azure HDInsight per l'elaborazione. Ogni codec di compressione ha una caratteristica univoca. Ad esempio, BZIP2 ha la velocità effettiva di copia più bassa, ma è possibile ottenere le migliori prestazioni di query Hive a condizione di poterlo dividere per l'elaborazione. GZIP offre l'opzione più bilanciata ed è quello usato più spesso. È consigliabile scegliere il codec più adatto per lo scenario end-to-end.

**Livello:** per ogni codec di compressione, è possibile scegliere tra due opzioni, la compressione più veloce e la compressione ottimale. L'opzione di compressione più veloce comprime i dati il più rapidamente possibile, anche se il file risultante non viene compresso in modo ottimale. L'opzione di compressione ottimale impiegherà più tempo per la compressione restituendo la quantità minima di dati. È possibile testare entrambe le opzioni per verificare quale offra le migliori prestazioni complessive in base alle proprie esigenze.

**Una considerazione:** per copiare dati di grandi dimensioni tra l'archivio locale e il cloud, dove la larghezza di banda tra la rete aziendale e Azure è spesso il fattore limitante, e se si vuole che sia il set di dati di input che il set di dati di output siano in formato non compresso, si può pensare di usare un **BLOB di Azure provvisorio** con la compressione. Più in particolare, è possibile suddividere una singola attività di copia in due attività di copia: la prima che esegue la copia dall'origine al BLOB provvisorio o di staging in formato compresso e la seconda che copia i dati compressi dallo staging e li decomprime durante la scrittura nel sink.

## Considerazioni sul mapping di colonne
La proprietà "columnMappings" nell'attività di copia può essere usata per il mapping di tutte o di un subset delle colonne di input alle colonne di output. Dopo avere letto i dati dall'origine, il servizio di spostamento dati deve eseguire il mapping delle colonne sui dati prima di scriverli nel sink. Questa ulteriore elaborazione riduce la velocità effettiva di copia.

Se l'archivio dati di origine è disponibile per le query, ad esempio un archivio relazionale come SQL di Azure/SQL Server o un archivio NoSQL come Tabella di Azure/Azure DocumentDB, invece di usare il mapping delle colonne, è opportuno valutare la possibilità di basare la logica di filtro/riordinamento delle colonne sulla proprietà query, con cui la proiezione viene effettuata durante la lettura dei dati dall'archivio dati di origine e che è molto più efficiente.

## Considerazioni sul Gateway di gestione dati
Per suggerimenti sulla configurazione del gateway, vedere [Considerazioni per l'uso del Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway).

**Ambiente del computer gateway:** è consigliabile usare un computer dedicato per ospitare il Gateway di gestione dati. Usare strumenti come PerfMon per esaminare la CPU, la memoria e l'utilizzo della larghezza di banda durante un'operazione di copia nel computer gateway. Se la CPU, la memoria o la larghezza di banda di rete diventa un collo di bottiglia, passare a un computer più potente.

**Esecuzioni simultanee dell'attività di copia:** una sola istanza del Gateway di gestione dati può gestire più esecuzioni dell'attività di copia nello stesso momento. Un gateway, ad esempio, può eseguire simultaneamente diversi processi di copia. Il numero di processi simultanei viene calcolato in base alla configurazione hardware del computer gateway. I processi di copia aggiuntivi vengono accodati finché non vengono selezionati dal gateway o non si verifica il timeout del processo. Per evitare la contesa per le risorse nel gateway, è possibile inserire temporaneamente la pianificazione delle attività per ridurre la quantità di processi di copia accodati allo stesso tempo oppure valutare la divisione del carico in più gateway.


## Altre considerazioni
Se la dimensione dei dati da copiare è molto grande, è possibile modificare la logica di business per partizionare ulteriormente i dati nel meccanismo di sezionamento di Data factory di Azure e pianificare l'attività di copia con più frequenza per ridurre la dimensione dei dati per ogni esecuzione dell'attività di copia.

Prestare attenzione al numero di set di dati e di attività di copia che raggiungono lo stesso archivio dati in un determinato momento. Un numero elevato di processi di copia simultanei può limitare un archivio dati e causare un degrado delle prestazioni, tentativi interni di processi di copia e in alcuni casi errori di esecuzione.

## Case Study - Copia da SQL Server locale al BLOB di Azure
**Scenario:** viene compilata una pipeline per copiare dati da un server SQL locale al BLOB di Azure in formato CSV. Per eseguire la copia più velocemente, viene specificato che i file CSV devono essere compressi in formato BZIP2.

**Test e analisi:** si osserva che la velocità effettiva dell'attività di copia è inferiore a 2 MB/s, molto meno di quella del benchmark delle prestazioni.

**Analisi e ottimizzazione delle prestazioni:** per risolvere il problema di prestazioni, prima di tutto verrà illustrato come vengono elaborati e spostati i dati:

1.	**Lettura dei dati:** il gateway apre la connessione a SQL Server e invia la query. SQL Server risponde inviando il flusso dei dati al gateway tramite Intranet.
2.	Il gateway **serializza** il flusso dei dati nel formato CSV e **comprime** i dati in un flusso BZIP2.
3.	**Scrittura dei dati:** il gateway carica il flusso BZIP2 nel BLOB di Azure tramite Internet.

Come si può notare, i dati vengono elaborati e spostati in base a un flusso sequenziale: SQL Server -> LAN -> Gateway -> WAN -> BLOB di Azure. **Le prestazioni complessive vengono controllate dalla velocità effettiva minima in tutta la pipeline**.

![Flusso di dati](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Uno o più fattori seguenti possono costituire il collo di bottiglia delle prestazioni:

1.	**Origine:** SQL Server ha di per sé una velocità effettiva bassa a causa dei carichi elevati.
2.	**Gateway di gestione dati:**
	1.	**LAN:** il gateway è distante da SQL Server con una connessione con larghezza di banda bassa
	2.	Il **carico nel computer gateway** ha raggiunto i limiti previsti per eseguire le operazioni seguenti:
		1.	**Serializzazione:** la serializzazione del flusso dei dati in formato CSV ha una velocità effettiva bassa
		2.	**Compressione:** è stato scelto il codec di compressione lenta (ad esempio, BZIP2 a 2,8 MB/s con core i7)
	3.	**WAN:** larghezza di banda bassa tra la rete aziendale e Azure (ad esempio, T1= 1544 kbps, T2=6312 kbps)
4.	**Sink:** il BLOB di Azure ha una velocità effettiva bassa (anche se è molto improbabile dato che il Contratto di servizio garantisce un minimo di 60 MB/s).

In questo caso, è possibile che la compressione dati BZIP2 stia rallentando l'intera pipeline. Passando al codec di compressione GZIP è possibile ridurre questo collo di bottiglia.


## Case study: copia parallela  

**Scenario I:** copiare 1000 file da 1 MB dal file system locale nell'archivio BLOB di Azure

**Analisi e ottimizzazione delle prestazioni:** si supponga di aver installato Gateway di gestione dati in un computer quad-core. Per impostazione predefinita Data Factory usa 16 copie parallele per spostare contemporaneamente i file dal file system al BLOB di Azure. La velocità in questo caso sarà buona. Se necessario, è anche possibile specificare il numero di copie parallele. Quando si copia un numero elevato di file di piccole dimensioni, le copie parallele aumentano considerevolmente la velocità grazie a un uso più efficiente delle risorse coinvolte.

![Scenario 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Scenario II:** copiare 20 BLOB di 500 MB ognuno dall'archivio BLOB di Azure in Analisi Archivio Azure Data Lake e ottimizzare le prestazioni.

**Analisi e ottimizzazione delle prestazioni:** in questo scenario Data Factory copia i dati dall'archivio BLOB di Azure in Azure Data Lake usando un'unica copia e un'unica unità di spostamento dei dati cloud. La proprietà parallelCopies è impostata su 1. La velocità effettiva si avvicina ai valori indicati nella sezione [Informazioni di riferimento sulle prestazioni](#performance-reference) precedente.

![Scenario 2](./media/data-factory-copy-activity-performance/scenario-2.png)

Se si copiano file di dimensioni superiori a decine di MB e il volume totale è grande, è possibile che, impostando un valore più alto della proprietà parallelCopies, le prestazioni di copia non migliorino a causa delle risorse insufficienti di un'unica unità di spostamento dei dati cloud. È invece necessario specificare altre unità di spostamento dei dati cloud per ottenere più risorse ed eseguire lo spostamento dei dati. Non specificare un valore per la proprietà parallelCopies. In modo Data Factory eseguirà il parallelismo. In questo caso, impostare cloudDataMovementUnits su 4 perché la velocità effettiva sia circa 4x.

![Scenario 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## Informazioni di riferimento sull'ottimizzazione delle prestazioni dell'archivio dati
Ecco alcune informazioni di riferimento sul monitoraggio e sull'ottimizzazione delle prestazioni per alcuni archivi dati supportati:

- Archiviazione di Azure (inclusi BLOB di Azure e Tabella di Azure): [Obiettivi di scalabilità per Archiviazione di Azure](../storage/storage-scalability-targets.md) ed [Elenco di controllo relativo a prestazioni e scalabilità di Archiviazione di Azure](../storage//storage-performance-checklist.md)
- Database SQL di Azure: è possibile [monitorare le prestazioni](../sql-database/sql-database-service-tiers.md#monitoring-performance) e controllare la percentuale di DTU (Database Transaction Unit).
- Azure SQL Data Warehouse: la capacità viene misurata in unità Data Warehouse (DWU). Vedere [Scalabilità e prestazioni elastiche con SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
- Azure DocumentDB: [Livello di prestazioni in DocumentDB](../documentdb/documentdb-performance-levels.md).
- SQL Server locale: [Monitorare e ottimizzare le prestazioni](https://msdn.microsoft.com/library/ms189081.aspx).
- File server locale: [Ottimizzazione delle prestazioni per i file server](https://msdn.microsoft.com/library/dn567661.aspx)

<!---HONumber=AcomDC_0727_2016-->