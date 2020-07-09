---
title: Guida alle prestazioni e all'ottimizzazione del flusso di dati per mapping
description: Informazioni sui fattori principali che influiscono sulle prestazioni dei flussi di dati per mapping in Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 07/06/2020
ms.openlocfilehash: 9f420b37bd44a46d4149e89cf5876d8e8b712581
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86114381"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guida alle prestazioni e all'ottimizzazione dei flussi di dati per mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I flussi di dati per mapping di Azure Data Factory offrono un'interfaccia senza codice per progettare, distribuire e orchestrare le trasformazioni di dati su larga scala. Se non si ha familiarità con i flussi di dati per mapping, vedere la [panoramica del flusso di dati per mapping](concepts-data-flow-overview.md).

Quando si progettano e si testano i flussi di dati dall'esperienza utente del file di definizione dell'applicazione (AFD), assicurarsi di attivare la modalità di debug per eseguire i flussi di dati in tempo reale senza attendere il riscaldamento di un cluster. Per altre informazioni, vedere [Modalità di debug](concepts-data-flow-debug-mode.md).

Questo video mostra alcuni intervalli di esempio di trasformazione dei dati tramite flussi:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Monitoraggio delle prestazioni dei flussi di dati

Quando si progettano flussi di dati per mapping, è possibile eseguire l'unit test di ogni trasformazione facendo clic sulla scheda di anteprima dei dati nel pannello di configurazione. Dopo aver verificato la logica, testare il flusso di dati end-to-end come attività in una pipeline. Aggiungere un'attività di esecuzione del flusso di dati e usare il pulsante Debug per testare le prestazioni del flusso di dati. Per aprire il piano di esecuzione e il profilo delle prestazioni del flusso di dati, fare clic sull'icona a forma di occhiali alla voce "azioni" nella scheda di output della pipeline.

![Monitoraggio del flusso di dati](media/data-flow/mon002.png "Monitoraggio del flusso di dati 2")

 È possibile usare queste informazioni per stimare le prestazioni del flusso di dati in base a origini dati di dimensioni diverse. Per altre informazioni, vedere [Monitoraggio dei flussi di dati per mapping](concepts-data-flow-monitoring.md).

![Monitoraggio dei flussi di dati](media/data-flow/mon003.png "Monitoraggio del flusso di dati 3")

 Per le esecuzioni di debug della pipeline, un cluster attivo richiede circa un minuto di tempo di configurazione nei calcoli delle prestazioni complessive. Se si sta inizializzando la Azure Integration Runtime predefinita, il tempo di rotazione potrebbe richiedere circa 4 minuti.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Aumento delle dimensioni di calcolo in Azure Integration Runtime

Un runtime di integrazione con più core aumenta il numero di nodi negli ambienti di calcolo Spark e offre una maggiore potenza di elaborazione per la lettura, la scrittura e la trasformazione dei dati. Il flusso di dati del file di definizione dell'applicazione (ADF) usa Spark per il motore di calcolo. L'ambiente Spark funziona molto bene con le risorse ottimizzate per la memoria.

È consigliabile usare la **memoria con ottimizzazione** per la maggior parte dei carichi di lavoro di produzione. Sarà possibile archiviare più dati in memoria e ridurre al minimo gli errori di memoria insufficiente. L'ottimizzazione per la memoria ha un punto di prezzo superiore per core rispetto al calcolo ottimizzato, ma probabilmente comporta velocità di trasformazione più veloci e pipeline più efficaci. Se si verificano errori di memoria insufficiente durante l'esecuzione del flusso di dati, passare a una configurazione di Azure IR ottimizzata per la memoria.

Il **calcolo ottimizzato** può essere sufficiente per il debug e l'anteprima dei dati di un numero limitato di righe di dati. Il calcolo ottimizzato probabilmente non potrà essere eseguito anche con i carichi di lavoro di produzione.

![Nuovo runtime di integrazione](media/data-flow/ir-new.png "Nuovo runtime di integrazione")

Per altre informazioni su come creare un runtime di integrazione, vedere [Runtime di integrazione in Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Aumento delle dimensioni del cluster di debug

Per impostazione predefinita, l'attivazione del debug userà il runtime di integrazione di Azure predefinito creato automaticamente per ogni data factory. Questo Azure IR predefinito è impostato su otto core: quattro per un nodo driver e quattro per un nodo di lavoro, usando le proprietà di calcolo generali. Quando si esegue il test con dati di dimensioni maggiori, è possibile aumentare le dimensioni del cluster di debug creando un Azure IR con configurazioni più grandi e sceglierlo quando si passa al debug. In questo modo si ordina al file di definizione dell'applicazione (ADF) di usare questo Azure IR per l'anteprima dei dati e il debug della pipeline con i flussi di dati.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Ridurre il tempo di avvio del calcolo del cluster con l'impostazione di durata (TTL)

Una delle proprietà del flusso di dati di Azure IR consente di creare un pool di risorse di calcolo del cluster per la factory. Con questo pool è possibile inviare in sequenza le attività del flusso di dati per l'esecuzione. Dopo aver stabilito il pool, il cluster Spark su richiesta impiegherà 1 o 2 minuti a eseguire ogni processo successivo. La configurazione iniziale del pool di risorse può richiedere circa 4 minuti. Specificare la quantità di tempo per cui si vuole mantenere il pool di risorse nell'impostazione di durata (TTL).

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Ottimizzazione del database SQL di Azure e Azure SQL Data Warehouse Synapse

### <a name="partitioning-on-source"></a>Partizionamento nell'origine

1. Andare alla scheda **Ottimizza** e selezionare **Imposta partizionamento**
1. Selezionare **Origine**.
1. Alla voce **Numero di partizioni** impostare il numero massimo di connessioni al database SQL di Azure. È possibile provare un'impostazione superiore per ottenere connessioni parallele al database. Tuttavia, alcuni casi possono comportare prestazioni più rapide con un numero limitato di connessioni.
1. Scegliere se eseguire la partizione in base a una colonna della tabella o una query specifica.
1. Se è stata selezionata l'opzione **Colonna**, selezionare la colonna di partizione.
1. Se è stata selezionata l'opzione **Query**, immettere una query corrispondente allo schema di partizione della tabella del database. Questa query consente al motore di database di origine di sfruttare l'eliminazione della partizione. Non è necessario partizionare le tabelle del database di origine. Se l'origine non è già partizionata, il file di definizione dell'applicazione (ADF) userà comunque il partizionamento dei dati nell'ambiente di trasformazione Spark in base alla chiave selezionata nella trasformazione di origine.

![Parte di origine](media/data-flow/sourcepart3.png "Parte di origine")

> [!NOTE]
> Una buona guida alla scelta del numero di partizioni per l'origine si basa sul numero di core impostati per Azure Integration Runtime moltiplicato per cinque. Se, ad esempio, si sta trasformando una serie di file nelle cartelle ADLS e si intende usare un Azure IR da 32 core, il numero di partizioni di destinazione sarà 32 x 5 = 160 partizioni.

### <a name="source-batch-size-input-and-isolation-level"></a>Dimensioni del batch di origine, input e livello di isolamento

Le impostazioni seguenti in **Opzioni di origine** nella trasformazione di origine possono avere effetti sulle prestazioni:

* Le dimensioni del batch indicano al file di definizione dell'applicazione (ADF) di archiviare i dati in set nella memoria Spark anziché riga per riga. Le dimensioni del batch sono un'impostazione facoltativa e, se le risorse nei nodi di calcolo non hanno le dimensioni corrette, potrebbero esaurirsi. Se questa proprietà non viene impostata, saranno usate le impostazioni predefinite del batch per la memorizzazione nella cache di Spark.
* L'impostazione di una query consente di filtrare le righe nell'origine prima che arrivino nel flusso di dati per l'elaborazione. Questo può velocizzare l'acquisizione iniziale dei dati. Se si usa una query, è possibile aggiungere hint per la query facoltativi per il database SQL di Azure, ad esempio READ UNCOMMITTED.
* READ UNCOMMITTED fornirà risultati più veloci per le query sulla trasformazione origine

![Origine](media/data-flow/source4.png "Source (Sorgente)")

### <a name="sink-batch-size"></a>Dimensioni massime sink

Per evitare l'elaborazione riga per riga dei flussi di dati, impostare **Dimensioni batch** nella scheda Impostazioni per il database SQL di Azure e i sink di Azure SQL DW. Se sono state impostate le dimensioni del batch, il file di definizione dell'applicazione (ADF) elabora le scritture dati in batch in base alle dimensioni specificate. Se questa proprietà non viene impostata, saranno usate le impostazioni predefinite del batch per la memorizzazione nella cache di Spark.

![Sink](media/data-flow/sink4.png "Sink")

### <a name="partitioning-on-sink"></a>Partizionamento nel sink

Anche se i dati non sono partizionati nelle tabelle di destinazione, è consigliabile partizionare i dati nella trasformazione del sink. I dati partizionati spesso generano un carico molto più rapido rispetto alla forzatura di tutte le connessioni a usare un singolo nodo/partizione. Passare alla scheda Ottimizza del sink e selezionare il partizionamento in modalità *round robin* per selezionare il numero di partizioni ideale per la scrittura nel sink.

### <a name="disable-indexes-on-write"></a>Disabilitazione degli indici durante la scrittura

Aggiungere nella pipeline un'[attività stored procedure](transform-data-using-stored-procedure.md) prima dell'attività flusso di dati che disabilita gli indici nelle tabelle di destinazione scritte dal sink. Dopo l'attività flusso di dati, aggiungere un'altra attività stored procedure per abilitare tali indici. In alternativa, usare gli script di pre e post-elaborazione in un sink del database.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Aumento delle dimensioni di Azure SQL DB e Azure SQL DW

Pianificare un ridimensionamento di Azure SLQ DB e DW di origine e sink prima dell'esecuzione della pipeline per aumentare la velocità effettiva e ridurre al minimo la limitazione di Azure quando si raggiungono i limiti di DTU. Al termine dell'esecuzione della pipeline, ridimensionare nuovamente i database fino alla frequenza di esecuzione normale.

* Il passaggio da una tabella di origine del database SQL con 887.000 righe e 74 colonne a una tabella del database SQL con una singola trasformazione a colonna derivata richiede circa 3 minuti end-to-end usando Azure IR con debug da 80 core con ottimizzazione per la memoria.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Solo Azure Synapse SQL DW] Usare il processo di gestione temporanea per caricare i dati in blocco tramite Polybase

Per evitare gli inserimenti riga per riga nel data warehouse, selezionare **Abilita staging** nelle impostazioni del sink in modo che il file di definizione dell'applicazione (ADF) possa usare [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). PolyBase consente al file di definizione dell'applicazione (ADF) di caricare i dati in blocco.
* Quando si esegue l'attività flusso di dati da una pipeline, è necessario selezionare un percorso di archiviazione BLOB o ADLS Gen2 per organizzare i dati durante il caricamento in blocco.

* Il passaggio da un file di origine da 421 MB con 74 colonne a una tabella Synapse e una trasformazione a colonna derivata singola richiede circa 4 minuti end-to-end usando Azure IR con debug da 80 core con ottimizzazione per la memoria.

## <a name="optimizing-for-files"></a>Ottimizzazione per i file

A ogni trasformazione è possibile impostare lo schema di partizione da fare usare alla data factory nella scheda Ottimizza. È buona prassi prima di tutto testare i sink basati su file mantenendo le ottimizzazioni e il partizionamento predefiniti. Se si lascia il partizionamento a "partizionamento corrente" nel sink per una destinazione file, Spark può impostare un partizionamento predefinito appropriato per i carichi di lavoro. Il partizionamento predefinito usa 128Mb per partizione.

* Per i file di dimensioni ridotte, scegliere un numero inferiore di partizioni può risultare più produttivo e più veloce, piuttosto che richiedere a Spark di partizionare i file di piccole dimensioni.
* Se non si dispone di informazioni sufficienti sui dati di origine, scegliere il partizionamento in modalità *round robin* e impostare il numero di partizioni.
* Se i dati hanno colonne che possono essere chiavi hash valide, scegliere *Partizionamento hash*.

* Un'origine di file con sink di file da 421 MB con 74 colonne e una trasformazione a colonna derivata singola richiede circa 2 minuti end-to-end usando Azure IR con debug da 80 core con ottimizzazione per la memoria.

Quando si esegue il debug nell'anteprima dei dati e nel debug della pipeline, le dimensioni del limite e del campionamento per i set di dati di origine basati su file si applicano solo al numero di righe restituite, non al numero di righe lette. Questo può influire sulle prestazioni delle esecuzioni del debug e causare l'esito negativo del flusso.
* I cluster di debug sono piccoli cluster a nodo singolo per impostazione predefinita ed è consigliabile usare file campione di piccole dimensioni per il debug. Andare su Impostazioni di debug e puntare a un piccolo subset di dati usando un file temporaneo.

    ![Impostazioni di debug](media/data-flow/debugsettings3.png "Impostazioni di debug")

### <a name="file-naming-options"></a>Opzioni di denominazione dei file

Il modo più comune per scrivere i dati trasformati nei flussi di dati per mapping che scrivono in archivi di file BLOB o ADLS. Selezionare nel sink un set di dati che punti a un contenitore o a una cartella, non a un file con nome. Poiché il flusso di dati per mapping usa Spark per l'esecuzione, l'output viene suddiviso su più file in base allo schema di partizionamento.

Uno schema di partizione comune consiste nello scegliere _Output in un singolo file_, che unisce tutti i file PART di output in un singolo file del sink. Per questa operazione è necessario che l'output si riduca a una singola partizione in un singolo nodo del cluster. È possibile esaurire le risorse del nodo del cluster se si combinano molti file di origine di grandi dimensioni in un unico file di output.

Per evitare l'esaurimento delle risorse del nodo di calcolo, è possibile usare lo schema ottimizzato predefinito nel flusso di dati e aggiungere un'attività di copia nella pipeline che unisca tutti i file PART della cartella di output in un nuovo file singolo. Questa tecnica separa l'azione di trasformazione dall'unione di file e ottiene lo stesso risultato dell'impostazione _Output in un singolo file_.

### <a name="looping-through-file-lists"></a>Ciclo negli elenchi file

L'esecuzione di un flusso di dati per mapping è migliore se la trasformazione di origine esegue l'iterazione su più file anziché eseguire il ciclo tramite l'attività ForEach. È consigliabile usare caratteri jolly o elenchi di file nella trasformazione di origine. Il processo del flusso di dati verrà eseguito più velocemente consentendo il ciclo all'interno del cluster Spark. Per altre informazioni, vedere [Caratteri jolly nella trasformazione origine](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Se, ad esempio, si dispone di un elenco di file di dati da luglio 2019 che si desidera elaborare in una cartella nell'archiviazione BLOB, di seguito è riportato un carattere jolly che può essere usato nella trasformazione di origine.

```DateFiles/*_201907*.txt```

Usando i caratteri jolly, la pipeline conterrà solo un'attività flusso di dati. Questo consente di ottenere prestazioni migliori rispetto a una ricerca nell'archivio BLOB, che quindi esegue l'iterazione su tutti i file con corrispondenze usando un elemento ForEach con un'attività di esecuzione del flusso di dati al suo interno.

L'elemento ForEach della pipeline in modalità parallela genererà più cluster attivando cluster di processi per ogni attività flusso di dati eseguita. Questo può causare una limitazione nel servizio Azure, con numeri elevati di esecuzioni simultanee. Tuttavia, l'uso di un'attività di esecuzione del flusso di dati all'interno di un elemento ForEach con un set sequenziale nella pipeline eviterà limitazioni ed esaurimento delle risorse. In questo si forzerà la data factory a eseguire in modo sequenziale ogni file in un flusso di dati.

Se si usa ForEach con un flusso di dati in sequenza, si consiglia di usare l'impostazione di durata (TTL) in Azure Integration Runtime. Questo perché ogni file comporterà un tempo di avvio completo del cluster di 4 minuti all'interno dell'iteratore.

### <a name="optimizing-for-cosmosdb"></a>Ottimizzazione per CosmosDB

L'impostazione delle proprietà di velocità effettiva e batch nei sink CosmosDB ha effetto solo durante l'esecuzione del flusso di dati da un'attività flusso di dati della pipeline. Le impostazioni di raccolta originali verranno rispettate da CosmosDB dopo l'esecuzione del flusso di dati.

* Dimensioni batch: calcolare la dimensione approssimativa delle righe dei dati e verificare che il prodotto dimensioni riga * dimensioni batch sia minore di 2 milioni. In caso contrario, aumentare la dimensione del batch per ottenere una velocità effettiva migliore
* Velocità effettiva: impostare una velocità effettiva superiore per consentire ai documenti di scrivere più velocemente in CosmosDB. Tenere presente che i costi delle unità richieste saranno maggiori con un'impostazione di velocità effettiva elevata.
*   Budget per la velocità effettiva di scrittura: usare un valore inferiore al numero totale di unità richieste al minuto. Se si dispone di un flusso di dati con un numero elevato di partizioni Spark, l'impostazione di una velocità effettiva del budget consentirà un maggiore equilibrio tra le partizioni.

## <a name="join-and-lookup-performance"></a>Prestazioni di join e ricerca

La gestione delle prestazioni dei join nel flusso di dati è un'operazione molto comune che verrà eseguita per tutto il ciclo di vita delle trasformazioni dei dati. Nel file di definizione dell'applicazione (ADF) i flussi di dati non richiedono l'ordinamento dei dati prima dei join, perché queste operazioni vengono eseguite come hash join in Spark. Tuttavia, è possibile trarre vantaggio dalle prestazioni migliorate grazie all'ottimizzazione "Broadcast" del join, applicabile a trasformazioni Join, Esiste e Ricerca.

In questo modo si eviteranno i rimescolamenti in tempo reale, distribuendo il contenuto di entrambi i lati della relazione di join nel nodo Spark. Questa operazione funziona correttamente per le tabelle più piccole usate per le ricerche di riferimento. Le tabelle di dimensioni maggiori che potrebbero non rientrare nella memoria del nodo non sono ideali per l'ottimizzazione Broadcast.

La configurazione consigliata per i flussi di dati con molte operazioni di join consiste nel lasciare l'ottimizzazione impostata su "auto" per "broadcast" e utilizzare una configurazione di Azure Integration Runtime con ottimizzazione per la ***memoria*** . Se si verificano errori di memoria insufficiente o timeout di trasmissione durante le esecuzioni del flusso di dati, è possibile disattivare l'ottimizzazione della trasmissione. Questa operazione comporterà tuttavia un'esecuzione dei flussi di dati più lenta. Facoltativamente, è possibile indicare al flusso di dati di distribuire solo il lato sinistro o destro del join o entrambi.

![Impostazioni di trasmissione](media/data-flow/newbroad.png "Impostazioni di trasmissione")

Un'altra ottimizzazione Join consiste nel creare i join in modo da evitare la tendenza di Spark a implementare i cross join. Ad esempio, quando si includono valori letterali nelle condizioni di join, Spark potrebbe interpretarlo come requisito per eseguire prima un prodotto cartesiano completo, quindi filtrare i valori uniti in join. Tuttavia, se ci si assicura di avere valori delle colonne su entrambi i lati della condizione di join, è possibile evitare questo prodotto cartesiano indotto da Spark e migliorare le prestazioni dei join e dei flussi di dati.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altri articoli relativi alle prestazioni dei flussi di dati:

- [Scheda di ottimizzazione del flusso di dati](concepts-data-flow-overview.md#optimize)
- [Attività flusso di dati](control-flow-execute-data-flow-activity.md)
- [Monitoraggio delle prestazioni dei flussi di dati](concepts-data-flow-monitoring.md)
