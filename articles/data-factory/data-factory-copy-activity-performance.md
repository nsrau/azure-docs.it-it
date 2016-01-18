<properties
	pageTitle="Guida alle prestazioni delle attività di copia e all'ottimizzazione"
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
	ms.date="12/17/2015"
	ms.author="spelluru"/>


# Guida alle prestazioni delle attività di copia e all'ottimizzazione
Questo articolo descrive i fattori chiave che influiscono sulle prestazioni di spostamento dei dati (attività di copia) in Data factory di Azure. Elenca anche le prestazioni osservate durante i test interni e descrive i modi per ottimizzare le prestazioni dell'attività di copia.

## Panoramica dello spostamento dati in Data factory di Azure
L'attività di copia esegue lo spostamento dati in Data factory di Azure e si basa su un [servizio di spostamento dati disponibile a livello globale](data-factory-data-movement-activities.md#global), che può copiare dati tra [diversi archivi dati](data-factory-data-movement-activities.md#supported-data-stores-for-copy-activity) in modo sicuro, affidabile, scalabile ed efficiente. Il servizio di spostamento dati sceglie automaticamente l'area ottimale per eseguire l'operazione di spostamento dati in base alla posizione degli archivi dati di origine e sink. Attualmente viene usata l'area più vicina all'archivio dati sink.

Di seguito vengono illustrati i modi in cui avviene lo spostamento dei dati in scenari diversi.

### Copia di dati tra due archivi dati cloud
Quando sia l'archivio dati di origine che l'archivio dati sink (destinazione) si trovano nel cloud, l'attività di copia prevede le fasi seguenti per copiare/spostare i dati dall'origine al sink.

1.	Legge i dati dall'archivio dati di origine
2.	Esegue la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e la conversione del tipo in base alle configurazioni dei set di dati di input e output e all'attività di copia
3.	Scrive i dati nell'archivio dati di destinazione

![Copiare dati tra due archivi dati cloud](./media/data-factory-copy-activity-performance/copy-data-between-two-cloud-stores.png)

**Nota:** le forme delimitate da linee con punti (compressione, mapping delle colonne e così via) sono funzionalità che possono essere sfruttate o meno nel proprio caso d'uso.


### Copia dei dati tra un archivio dati locale e un archivio dati cloud
Per [spostare i dati tra un archivio dati locale e un archivio dati cloud](data-factory-move-data-between-onprem-and-cloud.md), è necessario installare il Gateway di gestione dati, ovvero un agente che consente lo spostamento e l'elaborazione ibridi dei dati sul computer locale. In questo scenario la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e la conversione del tipo in base alle configurazioni dei set di dati di input e output e all'attività di copia vengono eseguiti dal Gateway di gestione dati.

![Copiare dati tra un archivio dati locale e un archivio dati cloud](./media/data-factory-copy-activity-performance/copy-data-between-onprem-and-cloud.png)

## Procedura di ottimizzazione delle prestazioni
Di seguito sono indicati i passaggi tipici da eseguire per ottimizzare le prestazioni della soluzione Data factory di Azure con l'attività di copia.

1.	**Stabilire una baseline.** Durante la fase di sviluppo, testare la pipeline con l'attività di copia confrontandola con dati di esempio rappresentativi. È possibile sfruttare il [modello di sezionamento](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) di Data factory di Azure per limitare la quantità di dati usati.

	Raccogliere le caratteristiche relative a tempi di esecuzione e prestazioni, controllando il pannello "Sezione dati" e il pannello "Dettagli esecuzione attività" del set di dati di output nel portale di anteprima di Azure, che mostra la durata dell'attività di copia e la dimensione dei dati copiati.

	![Dettagli esecuzione attività](./media/data-factory-copy-activity-performance/activity-run-details.png)

	È possibile confrontare le prestazioni e le configurazioni del proprio scenario con le [informazioni di riferimento sulle prestazioni](#performance-reference) basate sulle osservazioni interne pubblicate più avanti.
2. **Diagnosi e ottimizzazione delle prestazioni.** Se le prestazioni osservate non rispondono alle aspettative, è necessario identificare i colli di bottiglia delle prestazioni ed eseguire le ottimizzazioni opportune per eliminare o ridurre l'impatto dei colli di bottiglia. Una descrizione completa della diagnosi delle prestazioni non rientra nell'ambito di questo argomento, ma qui di seguito sono elencate alcune considerazioni comuni.
	- [Origine](#considerations-on-source)
	- [Sink](#considerations-on-sink)
	- [Serializzazione/Deserializzazione](#considerations-on-serializationdeserialization)
	- [Compressione](#considerations-on-compression)
	- [Mapping di colonne](#considerations-on-column-mapping)
	- [Gateway di gestione dati](#considerations-on-data-management-gateway)
	- [Altre considerazioni](#other-considerations)
3. **Espandere la configurazione all'insieme dei dati.** Una volta verificati i risultati dell'esecuzione e le prestazioni, è possibile espandere la definizione del set di dati e il periodo attivo della pipeline per coprire tutti i dati presi in considerazione.

## Informazioni di riferimento sulle prestazioni
> [AZURE.IMPORTANT]**Dichiarazione di non responsabilità:** i dati seguenti sono stati pubblicati esclusivamente come linee guida e per fornire una pianificazione generale. Si presume che la larghezza di banda, l'hardware, la configurazione e così via siano di livello ottimale. Usare queste informazioni solo come riferimento. La velocità effettiva dello spostamento dati osservata verrà influenzata da numerose variabili. Vedere le sezioni seguenti per informazioni su come ottimizzare e ottenere prestazioni migliori in base alle proprie esigenze nell'ambito dello spostamento dati. Questi dati verranno aggiornati quando saranno disponibili nuovi miglioramenti e funzionalità per l'incremento delle prestazioni.

![Matrice delle prestazioni](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

> [AZURE.NOTE]**Presto disponibili:** attualmente si sta lavorando per migliorare le caratteristiche delle prestazioni di base e a breve nella tabella precedente saranno presenti nuovi e più elevati valori per la velocità effettiva.

Punti da notare:

- La velocità effettiva viene calcolata con la formula seguente: [dimensione dei dati letti dall'origine]/[durata dell'esecuzione dell'attività di copia]
- Per calcolare i valori precedenti, è stato usato il set di dati [TPC-H](http://www.tpc.org/tpch/).
- Nel caso degli archivi dati di Microsoft Azure, l'origine e il sink sono nella stessa area di Azure.
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

## Considerazioni sull'origine
### Generale
Assicurarsi che l'archivio dati sottostante non venga sovraccaricato da altri carichi di lavoro in esecuzione in/per tale archivio, tra cui, ad esempio l'attività di copia.

Per gli archivi dati Microsoft, vedere gli [argomenti sul monitoraggio e l'ottimizzazione](#appendix-data-store-performance-tuning-reference) specifici degli archivi dati per informazioni sulle caratteristiche delle prestazioni degli archivi dati e su come ridurre i tempi di risposta e ottimizzare la velocità effettiva.

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

## Appendice - Informazioni di riferimento sull'ottimizzazione delle prestazioni dell'archivio dati
Ecco alcune informazioni di riferimento sul monitoraggio e sull'ottimizzazione delle prestazioni per alcuni archivi dati supportati:

- Archiviazione di Azure (inclusi BLOB di Azure e Tabella di Azure): [Obiettivi di scalabilità per Archiviazione di Azure](../storage/storage-scalability-targets.md) ed [Elenco di controllo relativo a prestazioni e scalabilità di Archiviazione di Azure](../storage//storage-performance-checklist.md)
- Database SQL di Azure: è possibile [monitorare le prestazioni](../sql-database/sql-database-service-tiers.md#monitoring-performance) e controllare la percentuale DTU (Database Throughput Unit).
- Azure SQL Data Warehouse: la capacità viene misurata in unità Data Warehouse (DWU). Vedere [Scalabilità e prestazioni elastiche con SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-performance-scale.md).
- Azure DocumentDB: [Livello di prestazioni in DocumentDB](../documentdb/documentdb-performance-levels.md).
- SQL Server locale: [Monitorare e ottimizzare le prestazioni](https://msdn.microsoft.com/library/ms189081.aspx).
- File server locale: [Ottimizzazione delle prestazioni per i file server](https://msdn.microsoft.com/library/dn567661.aspx)

<!---HONumber=AcomDC_0107_2016-->