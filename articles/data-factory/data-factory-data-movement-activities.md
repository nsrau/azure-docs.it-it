<properties 
	pageTitle="Attività di spostamento dei dati" 
	description="Informazioni sulle entità di Data factory che è possibile usare per spostare dati in una pipeline di Data factory." 
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
	ms.date="03/11/2016" 
	ms.author="spelluru"/>

# Attività di spostamento dei dati
L'[attività di copia](#copyactivity) esegue lo spostamento dei dati in Data factory di Azure e si basa su un [servizio disponibile a livello globale](#global) che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Il servizio sceglie automaticamente l'area ottimale per eseguire lo spostamento dei dati. Viene usata l'area più vicina all'archivio dati sink.
 
Di seguito vengono illustrati i modi in cui avviene lo spostamento dei dati in scenari diversi.

## Copia di dati tra due archivi dati cloud
Nei casi in cui sia l'archivio dati di origine che l'archivio dati sink (destinazione) si trovano nel cloud, l'attività di copia prevede le fasi seguenti per copiare/spostare i dati dall'origine al sink. Il servizio su cui si basa l'attività di copia esegue queste operazioni:

1. Legge i dati dall'archivio dati di origine
2.	Esegue la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e la conversione del tipo in base alle configurazioni dei set di dati di input e output e all'attività di copia 
3.	Scrive i dati nell'archivio dati di destinazione

![copia da cloud a cloud](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Copia dei dati tra un archivio dati locale e un archivio dati cloud
Per [spostare in modo sicuro i dati tra gli archivi dati locali protetti da firewall aziendale e un archivio dati cloud](#moveonpremtocloud), è necessario installare il Gateway di gestione dati, ovvero un agente che consente lo spostamento e l'elaborazione ibridi dei dati sul computer locale. Il Gateway di gestione dati può essere installato sullo stesso computer dell'archivio dati o su un computer separato che è in grado di accedere all'archivio dati. In questo scenario, la serializzazione/deserializzazione, la compressione/decompressione, il mapping di colonne e la conversione del tipo vengono eseguiti dal Gateway di gestione dati. In questo caso, i dati non transitano attraverso il servizio Data factory di Azure. Gateway di gestione dati scrive direttamente i dati nell'archivio di destinazione.

![copia da locale a cloud](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Copiare dati da/a un archivio dati in una macchina virtuale IaaS di Azure 
È anche possibile spostare dati da/a archivi dati supportati ospitati su VM IaaS (macchine virtuali Infrastructure-as-a-Service) di Azure tramite il Gateway di gestione dati. In questo caso, il Gateway di gestione dati può essere installato sulla stessa macchina virtuale di Azure dell'archivio dati o su una macchina virtuale separata in grado di accedere all'archivio dati.

## Archivi dati supportati
L'attività di copia esegue la copia dei dati da un archivio dati **di origine** a un archivio dati **sink**. Data factory supporta gli archivi dati seguenti ed **è possibile scrivere i dati da qualsiasi origine in qualsiasi sink**. Fare clic su un archivio dati per informazioni su come copiare dati da e verso tale archivio.

| Origini| Sink |
|:------- | :---- |
| <ul><li>[BLOB di Azure](data-factory-azure-blob-connector.md)</li><li>[Tabella di Azure](data-factory-azure-table-connector.md)</li><li>[Database SQL di Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (vedere la nota riportata di seguito)](data-factory-azure-documentdb-connector.md)</li><li>[Archivio Azure Data Lake](data-factory-azure-datalake-connector.md)</li><li>[SQL Server locale/IaaS di Azure](data-factory-sqlserver-connector.md)</li><li>[File System locale/IaaS di Azure](data-factory-onprem-file-system-connector.md)</li><li>[Database Oracle locale/IaaS di Azure](data-factory-onprem-oracle-connector.md)</li><li>[Database MySQL locale/IaaS di Azure](data-factory-onprem-mysql-connector.md)</li><li>[Database DB2 locale/IaaS di Azure](data-factory-onprem-db2-connector.md)</li><li>[Database Teradata locale/IaaS di Azure ](data-factory-onprem-teradata-connector.md)</li><li>[Database Sybase locale/IaaS di Azure](data-factory-onprem-sybase-connector.md)</li><li>[Database PostgreSQL locale/IaaS di Azure](data-factory-onprem-postgresql-connector.md)</li><li>[Origini dati ODBC locali/IaaS di Azure](data-factory-odbc-connector.md)</li><li>[Hadoop Distributed File System (HDFS) locale/IaaS di Azure](data-factory-hdfs-connector.md)</li><li>[Origini OData](data-factory-odata-connector.md)</li><li>[Tabella Web](data-factory-web-table-connector.md)</li><li>[GE Historian locale/IaaS di Azure](data-factory-odbc-connector.md#ge-historian-store)</li></ul> | <ul><li>[BLOB di Azure](data-factory-azure-blob-connector.md)</li><li>[Tabella di Azure](data-factory-azure-table-connector.md)</li><li>[Database SQL di Azure](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (vedere la nota riportata di seguito)](data-factory-azure-documentdb-connector.md)</li><li>[Archivio Azure Data Lake](data-factory-azure-datalake-connector.md)</li><li>[SQL Server locale/IaaS di Azure](data-factory-sqlserver-connector.md)</li><li>[File system locale/IaaS di Azure](data-factory-onprem-file-system-connector.md)</li></ul> |


> [AZURE.NOTE] È possibile spostare solo da e verso Azure DocumentDB da e verso altri servizi di Azure come BLOB di Azure, Tabella di Azure, Database SQL di Azure, Azure SQL Data Warehouse, Azure DocumentDB e Archivio Azure Data Lake. Anche la matrice completa per Azure Document DB sarà supportata a breve.

Per spostare i dati da e verso un archivio dati che non è supportato dall'**attività di copia**, è possibile usare l'**attività personalizzata** in Data Factory con la logica richiesta per copiare o spostare i dati. Per i dettagli sulla creazione e l'uso di un'attività personalizzata, vedere l'articolo [Usare attività personalizzate in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md).

## Esercitazione
Per un'esercitazione rapida sull'uso dell'attività di copia, vedere [Esercitazione: Copiare i dati di un BLOB di Azure in Azure SQL](data-factory-get-started.md). Nell'esercitazione si userà l'attività di copia per copiare dati da un'archiviazione BLOB di Azure a un database SQL di Azure.

## <a name="copyactivity"></a>Attività di copia
L'attività di copia esegue la copia dei dati da un set di dati di input (**origine**) in un set di dati di output (**sink**). La copia dei dati viene eseguita in modalità batch in base alla pianificazione specificata per l'attività. Per informazioni sulla definizione di attività in generale, vedere l'articolo relativo a [pipeline e attività](data-factory-create-pipelines.md).

L'attività di copia offre le funzionalità seguenti:

### <a name="global"></a>Spostamento dei dati disponibile a livello globale
Anche se Data factory di Azure è disponibile solo negli Stati Uniti occidentali e in Europa settentrionale, il servizio su cui si basa l'attività di copia è disponibile a livello globale nelle aree geografiche seguenti. La topologia disponibile a livello globale garantisce uno spostamento di dati efficiente e nella maggior parte dei casi consente di evitare passaggi tra diverse aree.

Il **Gateway di gestione dati** o **Azure Data Factory** esegue lo spostamento dei dati in base alla posizione degli archivi dati di origine e destinazione in un'operazione di copia. Per informazioni dettagliate, vedere la tabella seguente:

Posizione dell'archivio dati di origine | Posizione dell'archivio dati di destinazione | Lo spostamento dei dati viene eseguito da  
-------------------------- | ------------------------------- | ----------------------------- 
locale/VM di Azure (IaaS) | cloud | **Gateway di gestione dati** in un computer locale o una VM di Azure. I dati non transitano attraverso il servizio nel cloud. <br/><br/>Nota: il Gateway di gestione dati può trovarsi nello stesso computer locale o nella stessa VM di Azure in cui risiede l'archivio dati o in un altro computer locale o un'altra VM di Azure, purché possa connettersi a entrambi gli archivi dati.
cloud | locale/VM di Azure (IaaS) | Come sopra. 
locale/VM di Azure (IaaS) | locale/VM di Azure | **Gateway di gestione dati associato all'origine**. I dati non transitano attraverso il servizio nel cloud. Vedere la nota precedente.   
cloud | cloud | **Servizio cloud su cui si basa l'attività di copia**. Data factory di Azure usa la distribuzione del servizio nell'area più vicina alla posizione del sink nella stessa area geografica. Per il mapping, vedere la tabella seguente: <br/><br/><table><tr><th>Area dell'archivio dati di destinazione</th> <th>Area usata per lo spostamento dei dati</th></tr><tr><td>Stati Uniti orientali</td><td>Stati Uniti orientali</td></tr><tr><td>Stati Uniti orientali 2</td><td>Stati Uniti orientali 2</td><tr/><tr><td>Stati Uniti centrali</td><td>Stati Uniti centrali</td><tr/><tr><td>Stati Uniti occidentali</td><td>Stati Uniti occidentali</td></tr><tr><td>Stati Uniti centro-settentrionali</td><td>Stati Uniti centro-settentrionali</td></tr><tr><td>Stati Uniti centro-meridionali</td><td>Stati Uniti centro-meridionali</td></tr><tr><td>Europa settentrionale</td><td>Europa settentrionale</td></tr><tr><td>Europa occidentale</td><td>Europa occidentale</td></tr><tr><td>Asia sud-orientale</td><td>Asia sud-orientale</td></tr><tr><td>Asia orientale</td><td>Asia sud-orientale</td></tr><tr><td>Giappone orientale</td><td>Giappone orientale</td></tr><tr><td>Giappone occidentale</td><td>Giappone orientale</td></tr><tr><td>Brasile meridionale</td><td>Brasile meridionale</td></tr><tr><td>Australia orientale</td><td>Australia orientale</td></tr><tr><td>Australia sud-orientale</td><td>Australia sud-orientale</td></tr></table>


> [AZURE.NOTE] Se l'area dell'archivio dati di destinazione non è nell'elenco precedente, l'attività di copia non viene completata invece di passare attraverso un'area alternativa.



### <a name="moveonpremtocloud"></a>Spostamento sicuro dei dati tra un ambiente locale e il cloud
Una delle maggiori difficoltà relative all'integrazione moderna dei dati consiste nello spostamento uniforme di dati da ambienti locali al cloud e viceversa. Il Gateway di gestione dati è un agente che è possibile installare in locale per abilitare le pipeline di dati ibride.

Il Gateway di gestione dati offre le funzionalità seguenti:

1.	Consente di gestire in modo sicuro l'accesso ad archivi di dati locali.
2.	Consente di modellare gli archivi dati locali e nel cloud all'interno di un'unica istanza di Data factory e di spostare i dati al suo interno.
3.	Consente di monitorare e gestire lo stato del gateway in un'unica schermata attraverso un dashboard di Data factory basato sul cloud.

È necessario considerare l'origine dati come un'origine dati locale, ovvero come se fosse protetta da firewall, anche quando si usa **ExpressRoute** e **il gateway** per stabilire la connettività tra il servizio e l'origine dati.

Per altre informazioni, vedere [Spostare dati tra origini locali e il cloud mediante il Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).


### Spostamento di dati affidabile e conveniente
L'attività di copia è progettata per spostare in modo affidabile volumi elevati di dati provenienti da una vasta gamma di origini dati, evitando gli errori temporanei. È possibile copiare i dati a un costo conveniente, con la possibilità di abilitare la compressione durante la connessione.

### Conversioni dei tipi tra diversi sistemi di tipi
Gli archivi dati provengono tutti da uno specifico sistema di tipi nativo. L'attività di copia esegue automaticamente la conversione dai tipi di origine ai tipi sink mediante il metodo seguente in due passaggi:

1. Conversione dai tipi di origine nativi al tipo .NET
2. Conversione dal tipo .NET al tipo sink nativo

Per trovare il mapping di un sistema di tipi nativo a .NET per uno specifico archivio dati, vedere gli argomenti relativi al connettore archivio dati corrispondente. È possibile usare tali mapping per determinare i tipi appropriati durante la creazione di tabelle, in modo che durante l'attività di copia vengano eseguite le conversioni corrette.

### Uso di diversi formati di file
L'attività di copia supporta una vasta gamma di formati di file, inclusi i formati binari, di testo, Avro e JSON per gli archivi basati su file. È possibile usare l'attività di copia per convertire i dati da un formato a un altro. Esempio: testo (CSV) ad Avro. Se i dati non sono strutturati, è possibile omettere la proprietà **Structure** nella definizione JSON del [set di dati](data-factory-create-datasets.md).

### Proprietà dell'attività di copia
Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output, diversi criteri e così via. D'altra parte, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività.

Quando viene eseguita un'attività di copia, la sezione **typeProperties** varia a seconda dei tipi di origine e sink. Fare clic su un'origine o un sink nella sezione relativa [alle origini e ai sink supportati](#supported-data-stores) per informazioni sulle proprietà supportate dall'attività di copia per l'archivio dati.

Ciascuna pagina relativa agli archivi dati elencati sopra contiene informazioni su queste proprietà, che variano in base al tipo di archivio dati.

### Copia ordinata
È possibile eseguire più operazioni di copia l'una dopo l'altra in modo sequenziale o ordinato. Si supponga di avere due attività di copia in una pipeline: CopyActivity1 e CopyActivity con i set di dati di input e output seguenti.

CopyActivity1: Input: Dataset1 Output Dataset2

CopyActivity2: Inputs: Dataset2 Output: Dataset4

CopyActivity2 viene eseguita solo se l'esecuzione di CopyActivity1 è riuscita e Dataset2 è disponibile.

Nell'esempio precedente CopyActivity2 può avere un input diverso, ad esempio Dataset3, ma sarà necessario specificare Dataset2 anche come input per CopyActivity2, in modo che l'attività non venga eseguita fino a quando CopyActivity1 non è stata completata. ad esempio:

CopyActivity1: Input: Dataset1 Output Dataset2

CopyActivity2: Inputs: Dataset3, Dataset2 Output: Dataset4

Quando si specificano più input, solo il primo set di dati di input viene usato per la copia dei dati e gli altri set di dati vengono usati come dipendenze. L'esecuzione di CopyActivity2 si avvia solo quando le seguenti condizioni sono soddisfatte:

- L'esecuzione di CopyActivity2 è riuscita e Dataset2 è disponibile. Questo set di dati non sarà usato per la copia dei dati in Dataset4. La sua funzione è semplicemente quella di pianificare la dipendenza per CopyActivity2.   
- Dataset3 è disponibile. Questo set di dati rappresenta i dati che vengono copiati nella destinazione.  


### Prestazioni delle attività di copia e ottimizzazione 
Vedere l’articolo [Guida alle prestazione delle attività di copia e all’ottimizzazione](data-factory-copy-activity-performance.md), che descrive i fattori chiave che influiscono sulle prestazioni di spostamento dei dati (attività di copia) in Data Factory di Azure. Inoltre, vengono elencate le prestazioni osservate durante il test interni e vengono descritti i modi per ottimizzare le prestazioni dell'attività di copia.


## Copia guidata di data factory
La procedura **Copia guidata di Data Factory** consente di creare una pipeline per copiare dati dalle origini supportate alle destinazioni senza scrivere definizioni JSON per i servizi collegati, i set di dati e le pipeline. Per avviare la copia guidata, fare clic sul riquadro **Copia dati** nella home page della data factory.

![Copia di dati guidata](./media/data-factory-data-movement-activities/copy-data-wizard.png)

### Funzionalità

#### Una procedura guidata intuitiva e perfettamente integrata per la copia dei dati 
Questa procedura guidata consente di spostare facilmente i dati da un'origine a una destinazione in pochi minuti con i semplici passaggi seguenti:

1.	Selezionare l'**origine**
2.	Selezionare la **destinazione**
3.	Configurare le **impostazioni**

![Selezionare l'origine dati](./media/data-factory-data-movement-activities/select-data-source-page.png)

#### Esplorazione avanzata dei dati e mapping dello schema
Mediante la procedura guidata è possibile sfogliare tabelle o cartelle, visualizzare dati in anteprima, mappare lo schema, convalidare espressioni ed eseguire semplici trasformazioni di dati.

**Sfogliare tabelle o cartelle** ![Sfogliare tabelle e cartelle](./media/data-factory-data-movement-activities/browse-tables-folders.png)

#### Esperienza scalabile per tipi di dati e di oggetti diversi
L'esperienza è totalmente pensata per i Big Data. Creare pipeline di Data Factory che spostano centinaia di cartelle, file o tabelle è una procedura semplice ed efficiente.

**Visualizzare dati in anteprima, mappare lo schema ed eseguire semplici trasformazioni** ![Impostazioni sul formato del file](./media/data-factory-data-movement-activities/file-format-settings.png) ![Mapping dello schema](./media/data-factory-data-movement-activities/schema-mapping.png) ![Espressione di convalida](./media/data-factory-data-movement-activities/validate-expressions.png)

#### Esperienza scalabile per tipi di dati e di oggetti diversi
L'esperienza è totalmente pensata per i Big Data. Spostare centinaia di cartelle, file o tabelle è una procedura altrettanto semplice ed efficiente mediante la copia guidata.

![Selezionare le tabelle per copiare i dati](./media/data-factory-data-movement-activities/select-tables-to-copy-data.png)

#### Opzioni di pianificazione più avanzate
È possibile eseguire l'operazione di copia una sola volta oppure ripeterla in base a una pianificazione (a intervalli di un'ora, un giorno, ecc.). Entrambe queste opzioni possono essere usate per tutti i vari connettori tra copie in locale, sul cloud e sui desktop locali. La copia una tantum consente lo spostamento dei dati da un'origine a una destinazione una sola volta e si applica a dati di qualsiasi dimensione e a tutti i formati supportati. La copia pianificata consente di copiare i dati secondo una ricorrenza predeterminata. Sono disponibili impostazioni avanzate (come ad esempio la ripetizione dei tentativi, timeout, avvisi, ecc.) per configurare la copia pianificata.

![Proprietà di pianificazione](./media/data-factory-data-movement-activities/scheduling-properties.png)


### Provare il servizio 
Per una procedura dettagliata per l'uso di **Copia guidata di Data Factory** per creare una pipeline con un'attività di copia, vedere [Esercitazione: Creare una pipeline con l'attività di copia usando la Copia guidata di Data Factory](data-factory-copy-data-wizard-tutorial.md).


### Variabili nel percorso della cartella di Azure Blob
È possibile usare le variabili nel percorso della cartella per copiare i dati da una cartella che viene determinata nel runtime in base alla [variabile di sistema WindowStart](data-factory-functions-variables.md#data-factory-system-variables). Le variabili supportate sono: **year**, **month**, **day**, **hour**, **minute** e **{custom}**. Esempio: inputfolder/{year}/{month}/{day}.

Si supponga di avere cartelle di input nel formato seguente:
	
	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

Fare clic sul pulsante **Sfoglia** per il **File o cartella**, passare a una di queste cartelle, ad esempio 2016->03->01->02, e fare clic su **Scegli**. Nella casella di testo ora dovrebbe essere visualizzato **2016/03/01/02**. Sostituire **2016** con **{year}**, **03** con **{month}**, **01** con **{day}**, **02** con **{hour}** e premere **TAB**. Dovrebbero essere visualizzati elenchi a discesa da cui selezionare il **formato** per queste quattro variabili come indicato di seguito:

![Uso di variabili di sistema](./media/data-factory-data-movement-activities/blob-standard-variables-in-folder-path.png)

È anche possibile usare una variabile **personalizzata** come indicato di seguito e [stringhe di formati supportati](https://msdn.microsoft.com/library/8kb3ddd4.aspx). Verificare di aver selezionato una cartella con quella struttura usando il pulsante Sfoglia, sostituire un valore con **{custom}** e premere **TAB** per vedere la casella di testo in cui digitare la stringa del formato.

![Uso di variabili personalizzate](./media/data-factory-data-movement-activities/blob-custom-variables-in-folder-path.png)

<!---HONumber=AcomDC_0420_2016-->