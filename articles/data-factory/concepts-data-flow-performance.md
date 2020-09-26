---
title: Guida alle prestazioni e all'ottimizzazione del flusso di dati per mapping
description: Informazioni sui fattori principali che influiscono sulle prestazioni dei flussi di dati per mapping in Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 08/12/2020
ms.openlocfilehash: 4a78e966d420591ebe7a9607777158cf17ddf698
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91370879"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guida alle prestazioni e all'ottimizzazione dei flussi di dati per mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Il mapping di flussi di dati in Azure Data Factory offrire un'interfaccia senza codice per la progettazione e l'esecuzione di trasformazioni di dati su larga scala. Se non si ha familiarità con i flussi di dati per mapping, vedere la [panoramica del flusso di dati per mapping](concepts-data-flow-overview.md). Questo articolo illustra vari modi per ottimizzare e ottimizzare i flussi di dati in modo che soddisfino i benchmark delle prestazioni.

Guardare il video seguente per visualizzare alcuni intervalli di esempio che trasformano i dati con flussi di dati.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>Test della logica del flusso di dati

Quando si progettano e si testano i flussi di dati dall'UX di ADF, la modalità di debug consente di eseguire test in modo interattivo in un cluster Spark attivo. In questo modo è possibile visualizzare in anteprima i dati ed eseguire i flussi di dati senza attendere il riscaldamento di un cluster. Per altre informazioni, vedere [Modalità di debug](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Monitoraggio delle prestazioni dei flussi di dati

Una volta verificata la logica di trasformazione con la modalità di debug, eseguire il flusso di dati end-to-end come attività in una pipeline. I flussi di dati sono operativi in una pipeline utilizzando l' [attività Esegui flusso di dati](control-flow-execute-data-flow-activity.md). L'attività flusso di dati dispone di un'esperienza di monitoraggio univoca rispetto ad altre attività Azure Data Factory che visualizza un piano di esecuzione dettagliato e un profilo delle prestazioni della logica di trasformazione. Per visualizzare informazioni dettagliate sul monitoraggio di un flusso di dati, fare clic sull'icona degli occhiali nell'output dell'esecuzione dell'attività di una pipeline. Per altre informazioni, vedere [Monitoraggio dei flussi di dati per mapping](concepts-data-flow-monitoring.md).

![Monitoraggio del flusso di dati](media/data-flow/monitoring-details.png "Monitoraggio del flusso di dati 2")

Quando si esegue il monitoraggio delle prestazioni del flusso di dati, è possibile esaminare quattro colli di bottiglia:

* Ora di inizio del cluster
* Lettura da un'origine
* Tempo trasformazione
* Scrittura in un sink 

![Monitoraggio dei flussi di dati](media/data-flow/monitoring-performance.png "Monitoraggio del flusso di dati 3")

Il tempo di avvio del cluster è il tempo necessario per creare un cluster Apache Spark. Questo valore si trova nell'angolo superiore destro della schermata di monitoraggio. I flussi di dati vengono eseguiti in un modello just-in-Time in cui ogni processo usa un cluster isolato. Questo tempo di avvio richiede in genere 3-5 minuti. Per i processi sequenziali, questa operazione può essere ridotta abilitando un valore di durata (TTL). Per ulteriori informazioni, vedere [ottimizzazione del Azure Integration Runtime](#ir).

I flussi di dati usano un ottimizzatore Spark che riordina ed esegue la logica di business in ' fasi ' per eseguire il più rapidamente possibile. Per ogni sink in cui viene scritto il flusso di dati, l'output di monitoraggio elenca la durata di ogni fase di trasformazione, insieme al tempo necessario per scrivere i dati nel sink. Il tempo maggiore è probabilmente il collo di bottiglia del flusso di dati. Se la fase di trasformazione che accetta il valore più grande contiene un'origine, potrebbe essere opportuno esaminare ulteriormente l'ottimizzazione del tempo di lettura. Se una trasformazione impiega molto tempo, potrebbe essere necessario ripartizionare o aumentare le dimensioni del runtime di integrazione. Se il tempo di elaborazione del sink è elevato, potrebbe essere necessario aumentare le prestazioni del database o verificare che non si stia commettendo un singolo file.

Dopo aver identificato il collo di bottiglia del flusso di dati, usare le strategie di ottimizzazione seguenti per migliorare le prestazioni.

## <a name="optimize-tab"></a>Scheda Ottimizza

La scheda **optimize (Ottimizza** ) contiene le impostazioni per configurare lo schema di partizionamento del cluster Spark. Questa scheda esiste in ogni trasformazione del flusso di dati e specifica se si desidera ripartizionare i dati **dopo** il completamento della trasformazione. La regolazione del partizionamento consente di controllare la distribuzione dei dati tra i nodi di calcolo e le ottimizzazioni di località dei dati che possono avere effetti positivi e negativi sulle prestazioni complessive del flusso di dati.

![Screenshot mostra la scheda Ottimizza, che include l'opzione di partizione, il tipo di partizione e il numero di partizioni.](media/data-flow/optimize.png)

Per impostazione predefinita, è selezionata l'opzione *Usa il partizionamento corrente* che indica Azure Data Factory Mantieni il partizionamento dell'output corrente della trasformazione. Poiché i dati di ripartizionamento richiedono tempo, nella maggior parte degli scenari è consigliabile *utilizzare il partizionamento corrente* . Scenari in cui potrebbe essere necessario ripartizionare i dati, tra cui aggregazioni e join che dipendono in modo significativo i dati o quando si usa il partizionamento di origine in un database SQL.

Per modificare il partizionamento in qualsiasi trasformazione, selezionare la scheda **ottimizza** e selezionare il pulsante di opzione **imposta partizionamento** . Viene visualizzata una serie di opzioni per il partizionamento. Il metodo migliore per il partizionamento è diverso a seconda dei volumi di dati, delle chiavi candidate, dei valori null e della cardinalità. 

> [!IMPORTANT]
> Una singola partizione combina tutti i dati distribuiti in un'unica partizione. Si tratta di un'operazione molto lenta che interessa significativamente anche tutte le operazioni di trasformazione e scrittura a valle. Il Azure Data Factory consiglia vivamente di utilizzare questa opzione a meno che non esista un motivo aziendale esplicito.

In ogni trasformazione sono disponibili le seguenti opzioni di partizionamento:

### <a name="round-robin"></a>Round Robin 

Round Robin distribuisce i dati equamente tra le partizioni. Usare Round Robin se non si hanno candidati chiave validi per implementare una strategia di partizionamento intelligente solida. È possibile impostare il numero di partizioni fisiche.

### <a name="hash"></a>Hash

Azure Data Factory produce un hash di colonne per produrre partizioni uniformi in modo che le righe con valori simili rientrino nella stessa partizione. Quando si usa l'opzione hash, verificare la possibile asimmetria della partizione. È possibile impostare il numero di partizioni fisiche.

### <a name="dynamic-range"></a>Intervallo dinamico

L'intervallo dinamico usa intervalli dinamici Spark basati sulle colonne o sulle espressioni fornite. È possibile impostare il numero di partizioni fisiche. 

### <a name="fixed-range"></a>Intervallo fisso

Consente di compilare un'espressione che fornisce un intervallo fisso per i valori nelle colonne di dati partizionati. Per evitare l'inclinazione della partizione, è necessario avere una conoscenza corretta dei dati prima di utilizzare questa opzione. I valori immessi per l'espressione vengono utilizzati come parte di una funzione di partizione. È possibile impostare il numero di partizioni fisiche.

### <a name="key"></a>Chiave

Se si dispone di una conoscenza corretta della cardinalità dei dati, il partizionamento delle chiavi potrebbe essere una strategia efficace. Il partizionamento delle chiavi crea partizioni per ogni valore univoco nella colonna. Non è possibile impostare il numero di partizioni perché il numero è basato su valori univoci nei dati.

> [!TIP]
> L'impostazione manuale dello schema di partizionamento consente di rimescolare i dati e di sfalsare i vantaggi di Spark Optimizer. Una procedura consigliata consiste nel non impostare manualmente il partizionamento, a meno che non sia necessario.

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> Ottimizzazione del Azure Integration Runtime

I flussi di dati vengono eseguiti in cluster Spark che vengono attivati in fase di esecuzione. La configurazione per il cluster usato viene definita nel runtime di integrazione (IR) dell'attività. Quando si definisce il runtime di integrazione, è necessario tenere presenti tre considerazioni sulle prestazioni: tipo di cluster, dimensioni del cluster e durata (TTL).

Per altre informazioni su come creare un runtime di integrazione, vedere [Runtime di integrazione in Azure Data Factory](concepts-integration-runtime.md).

### <a name="cluster-type"></a>Tipo di cluster

Sono disponibili tre opzioni disponibili per il tipo di cluster Spark: utilizzo generico, ottimizzazione per la memoria e calcolo ottimizzato.

I cluster con **finalità generali** sono la selezione predefinita e saranno ideali per la maggior parte dei carichi di lavoro del flusso di dati. Che tendono a essere il migliore equilibrio tra prestazioni e costi.

Se il flusso di dati include molti join e ricerche, è consigliabile usare un cluster con ottimizzazione per la **memoria** . I cluster con ottimizzazione per la memoria possono archiviare più dati in memoria e ridurre al minimo gli errori di memoria insufficiente che si possono ottenere. Il prezzo ottimizzato per la memoria è il punto di prezzo più alto per core, ma tende anche a produrre pipeline più efficaci. Se si verificano errori di memoria insufficiente durante l'esecuzione di flussi di dati, passare a una configurazione di Azure IR con ottimizzazione per la memoria. 

Il **calcolo ottimizzato** non è ideale per i flussi di lavoro ETL e non è consigliato dal team di Azure Data Factory per la maggior parte dei carichi di lavoro di produzione. Per le trasformazioni dei dati più semplici e non a elevato utilizzo di memoria, ad esempio il filtro dei dati o l'aggiunta di colonne derivate, i cluster ottimizzati per il calcolo possono essere usati a un prezzo più basso per core.

### <a name="cluster-size"></a>Dimensioni del cluster

I flussi di dati distribuiscono l'elaborazione dei dati su nodi diversi in un cluster Spark per eseguire operazioni in parallelo. Un cluster Spark con più core aumenta il numero di nodi nell'ambiente di calcolo. Altri nodi aumentano la potenza di elaborazione del flusso di dati. L'aumento delle dimensioni del cluster è spesso un modo semplice per ridurre il tempo di elaborazione.

Le dimensioni predefinite del cluster sono quattro nodi driver e quattro nodi di lavoro.  Quando si elaborano più dati, è consigliabile usare cluster più grandi. Di seguito sono elencate le possibili opzioni di ridimensionamento:

| Core del ruolo di lavoro | Core Driver | Totale core | Note |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Non disponibile per il calcolo ottimizzato |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Ai flussi di dati viene addebitato il prezzo di Vcore-HRS, ovvero le dimensioni del cluster e il fattore di tempo di esecuzione in questo. Quando si esegue la scalabilità verticale, il costo del cluster al minuto aumenterà, ma il tempo complessivo diminuirà.

> [!TIP]
> Il numero massimo di dimensioni di un cluster influisce sulle prestazioni di un flusso di dati. A seconda delle dimensioni dei dati, si verifica un punto in cui l'aumento delle dimensioni di un cluster smette di migliorare le prestazioni. Se, ad esempio, sono presenti più nodi rispetto alle partizioni di dati, l'aggiunta di nodi aggiuntivi non sarà utile. Una procedura consigliata consiste nell'iniziare con dimensioni ridotte e scalarle per soddisfare le esigenze di prestazioni. 

### <a name="time-to-live"></a>Durata (TTL)

Per impostazione predefinita, ogni attività del flusso di dati avvia un nuovo cluster basato sulla configurazione IR. Il tempo di avvio del cluster richiede alcuni minuti e l'elaborazione dei dati non può essere avviata finché non viene completata. Se le pipeline contengono più flussi di dati **sequenziali** , è possibile abilitare un valore TTL (time to Live). La specifica di un valore di durata (TTL) mantiene attivo un cluster per un determinato periodo di tempo dopo il completamento dell'esecuzione. Se un nuovo processo inizia a usare il runtime di integrazione nel tempo TTL, riutilizzerà il cluster esistente e il tempo di avvio sarà notevolmente ridotto. Al termine del secondo processo, il cluster resterà attivo per il tempo TTL.

È possibile eseguire un solo processo in un singolo cluster alla volta. Se è presente un cluster disponibile, ma vengono avviati due flussi di dati, solo uno utilizzerà il cluster attivo. Il secondo processo creerà il proprio cluster isolato.

Se la maggior parte dei flussi di dati viene eseguita in parallelo, non è consigliabile abilitare la durata (TTL). 

> [!NOTE]
> La durata (TTL) non è disponibile quando si usa il runtime di integrazione per la risoluzione automatica

## <a name="optimizing-sources"></a>Ottimizzazione di origini

Per ogni origine, ad eccezione del database SQL di Azure, è consigliabile **usare il partizionamento corrente** come valore selezionato. Durante la lettura da tutti gli altri sistemi di origine, i flussi di dati partizionano automaticamente i dati in base alle dimensioni dei dati. Viene creata una nuova partizione per circa ogni 128 MB di dati. Man mano che aumentano le dimensioni dei dati, aumenta il numero di partizioni.

Qualsiasi partizionamento personalizzato si verifica *dopo* le letture Spark nei dati e influirà negativamente sulle prestazioni del flusso di dati. Poiché i dati sono partizionati in modo uniforme in lettura, questa operazione non è consigliata. 

> [!NOTE]
> La velocità di lettura può essere limitata dalla velocità effettiva del sistema di origine.

### <a name="azure-sql-database-sources"></a>Origini del database SQL di Azure

Il database SQL di Azure dispone di un'opzione di partizionamento univoca denominata partizionamento "origine". L'abilitazione del partizionamento dell'origine può migliorare i tempi di lettura dal database SQL di Azure abilitando le connessioni parallele nel sistema di origine. Specificare il numero di partizioni e il modo in cui partizionare i dati. Usare una colonna di partizione con cardinalità elevata. È anche possibile immettere una query corrispondente allo schema di partizionamento della tabella di origine.

> [!TIP]
> Per il partizionamento di origine, l'i/O del SQL Server è il collo di bottiglia. L'aggiunta di un numero eccessivo di partizioni può saturare il database di origine. Quando si usa questa opzione, in genere si tratta di quattro o cinque partizioni ideali.

![Partizionamento di origine](media/data-flow/sourcepart3.png "Partizionamento di origine")

#### <a name="isolation-level"></a>Livello di isolamento

Il livello di isolamento della lettura in un sistema di origine SQL di Azure ha un effetto sulle prestazioni. La scelta di ' Read uncommitted ' fornirà le prestazioni più veloci e impedirà i blocchi del database. Per altre informazioni sui livelli di isolamento SQL, vedere [informazioni sui livelli di isolamento](https://docs.microsoft.com/sql/connect/jdbc/understanding-isolation-levels?view=sql-server-ver15).

#### <a name="read-using-query"></a>Leggi con query

È possibile leggere dal database SQL di Azure usando una tabella o una query SQL. Se si esegue una query SQL, la query deve essere completata prima che la trasformazione possa essere avviata. Le query SQL possono essere utili per eseguire il push delle operazioni che possono essere eseguite più velocemente e ridurre la quantità di dati letti da un SQL Server, ad esempio istruzioni SELECT, WHERE e JOIN. Quando si esegue il push delle operazioni, si perde la possibilità di tenere traccia delle derivazioni e delle prestazioni delle trasformazioni prima che i dati vengano inseriti nel flusso di dati.

### <a name="azure-synapse-analytics-sources"></a>Origini di Azure sinapsi Analytics

Quando si usa Azure sinapsi Analytics, un'impostazione denominata **Abilita staging** esiste nelle opzioni di origine. Ciò consente ad ADF di leggere da sinapsi usando la modalità [polibase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15), che migliora notevolmente le prestazioni di lettura. Per abilitare la polibase è necessario specificare un archivio BLOB di Azure o Azure Data Lake Storage percorso di gestione temporanea Gen2 nelle impostazioni dell'attività flusso di dati.

![Abilitare lo staging](media/data-flow/enable-staging.png "Abilitare lo staging")

### <a name="file-based-sources"></a>Origini basate su file

Mentre i flussi di dati supportano un'ampia gamma di tipi di file, il Azure Data Factory consiglia di usare il formato parquet nativo Spark per tempi di lettura e scrittura ottimali.

Se si sta eseguendo lo stesso flusso di dati in un set di file, è consigliabile leggere da una cartella, usando percorsi con caratteri jolly o leggendo da un elenco di file. Una singola esecuzione dell'attività flusso di dati può elaborare tutti i file in batch. Altre informazioni su come impostare queste impostazioni sono disponibili nella documentazione del connettore, ad esempio [archiviazione BLOB di Azure](connector-azure-blob-storage.md#source-transformation).

Se possibile, evitare di utilizzare l'attività for-each per eseguire il flusso di dati in un set di file. In questo modo ogni iterazione di for-each può creare il proprio cluster Spark, che spesso non è necessario e può essere costoso. 

## <a name="optimizing-sinks"></a>Ottimizzazione di sink

Quando i flussi di dati scrivono in sink, qualsiasi partizionamento personalizzato si verificherà immediatamente prima della scrittura. Come per l'origine, nella maggior parte dei casi è consigliabile **utilizzare il partizionamento corrente** come opzione di partizione selezionata. I dati partizionati scriveranno molto più rapidamente rispetto ai dati non partizionati, anche se la destinazione non è partizionata. Di seguito sono riportate le singole considerazioni per diversi tipi di sink. 

### <a name="azure-sql-database-sinks"></a>Sink del database SQL di Azure

Con il database SQL di Azure, il partizionamento predefinito dovrebbe funzionare nella maggior parte dei casi. È possibile che il sink disponga di un numero eccessivo di partizioni per la gestione del database SQL. Se si esegue questa operazione, ridurre il numero di partizioni restituite dal sink del database SQL.

#### <a name="disabling-indexes-using-a-sql-script"></a>Disabilitazione di indici tramite uno script SQL

La disabilitazione degli indici prima del caricamento in un database SQL può migliorare significativamente le prestazioni di scrittura nella tabella. Eseguire il comando seguente prima di scrivere nel sink SQL.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

Al termine della scrittura, ricompilare gli indici usando il comando seguente:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

Queste operazioni possono essere eseguite in modo nativo usando gli script pre e post-SQL all'interno di un database SQL di Azure o di un sink di sinapsi nel mapping dei flussi di dati.

![Disabilita indici](media/data-flow/disable-indexes-sql.png "Disabilita indici")

> [!WARNING]
> Quando si disabilitano gli indici, il flusso di dati acquisisce effettivamente il controllo di un database e non è probabile che le query abbiano esito positivo in questo momento. Di conseguenza, molti processi ETL vengono attivati a metà della notte per evitare questo conflitto. Per ulteriori informazioni, vedere la pagina relativa ai [vincoli di disabilitazione degli indici](https://docs.microsoft.com/sql/relational-databases/indexes/disable-indexes-and-constraints?view=sql-server-ver15)

#### <a name="scaling-up-your-database"></a>Scalabilità verticale del database

Pianificare un ridimensionamento di Azure SLQ DB e DW di origine e sink prima dell'esecuzione della pipeline per aumentare la velocità effettiva e ridurre al minimo la limitazione di Azure quando si raggiungono i limiti di DTU. Al termine dell'esecuzione della pipeline, ridimensionare nuovamente i database fino alla frequenza di esecuzione normale.

### <a name="azure-synapse-analytics-sinks"></a>Sink di analisi della sinapsi di Azure

Quando si scrive in Azure sinapsi Analytics, assicurarsi che **Abilita staging** sia impostato su true. In questo modo, ADF è in grado di scrivere utilizzando la [polibase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) , che carica i dati in blocco. È necessario fare riferimento a un Azure Data Lake Storage account di archiviazione BLOB di Azure o Gen2 per la gestione temporanea dei dati quando si usa la funzione di base.

Oltre a polibase, le stesse procedure consigliate si applicano ad Azure sinapsi Analytics come database SQL di Azure.

### <a name="file-based-sinks"></a>Sink basati su file 

Mentre i flussi di dati supportano un'ampia gamma di tipi di file, il Azure Data Factory consiglia di usare il formato parquet nativo Spark per tempi di lettura e scrittura ottimali.

Se i dati sono distribuiti in modo uniforme, **utilizzare il partizionamento corrente** come opzione di partizionamento più veloce per la scrittura di file.

#### <a name="file-name-options"></a>Opzioni di nomi di file

Quando si scrivono file, è possibile scegliere tra le opzioni di denominazione che hanno un effetto sulle prestazioni.

![Opzioni sink](media/data-flow/file-sink-settings.png "Opzioni sink")

Se si seleziona l'opzione **predefinita** , il più veloce viene scritto. Ogni partizione corrisponderà a un file con il nome predefinito di Spark. Questa operazione è utile se si sta semplicemente leggendo dalla cartella di dati.

Impostando un **modello** di denominazione, ogni file di partizione viene rinominato in un nome più descrittivo. Questa operazione si verifica dopo la scrittura ed è leggermente più lenta rispetto alla scelta del valore predefinito. Per partizione è possibile assegnare manualmente un nome a ogni singola partizione.

Se una colonna corrisponde a come si desidera restituire i dati, è possibile selezionare **come dati nella colonna**. In questo modo i dati vengono rimescolati e le prestazioni possono avere un effetto se le colonne non sono distribuite in modo uniforme.

L' **output in un singolo file** combina tutti i dati in una singola partizione. Ciò comporta tempi di scrittura lunghi, soprattutto per set di impostazioni di grandi dimensioni. Il team di Azure Data Factory consiglia vivamente di **non** scegliere questa opzione a meno che non esista un motivo aziendale esplicito.

### <a name="cosmosdb-sinks"></a>Sink CosmosDB

Quando si scrive in CosmosDB, la modifica della velocità effettiva e delle dimensioni del batch durante l'esecuzione del flusso di dati può migliorare le prestazioni. Queste modifiche diventano effettive durante l'esecuzione dell'attività flusso di dati e restituiranno le impostazioni della raccolta originale dopo la conclusione. 

**Dimensioni batch:** Calcolare la dimensione approssimativa delle righe dei dati e verificare che le dimensioni delle righe * batch siano minori di 2 milioni. In caso contrario, aumentare la dimensione del batch per ottenere una velocità effettiva migliore

**Velocità effettiva:** Impostare un'impostazione della velocità effettiva superiore per consentire ai documenti di scrivere più velocemente in CosmosDB. Tenere presente i costi delle unità richiesta maggiori in base a un'impostazione di velocità effettiva elevata.

**Budget della velocità effettiva di scrittura:** Usare un valore inferiore al numero totale di ur al minuto. Se si dispone di un flusso di dati con un numero elevato di partizioni Spark, l'impostazione di una velocità effettiva del budget consentirà un maggiore equilibrio tra le partizioni.


## <a name="optimizing-transformations"></a>Ottimizzazione delle trasformazioni

### <a name="optimizing-joins-exists-and-lookups"></a>Ottimizzazione di join, EXISTS e ricerche

#### <a name="broadcasting"></a>Broadcasting

Nelle trasformazioni join, ricerche ed EXISTS, se uno o entrambi i flussi di dati sono sufficientemente piccoli da adattarsi alla memoria del nodo di lavoro, è possibile ottimizzare le prestazioni abilitando la **trasmissione**. Il broadcast è quando si inviano frame di dati di piccole dimensioni a tutti i nodi del cluster. Questo consente al motore Spark di eseguire un join senza rimischiare i dati nel flusso di grandi dimensioni. Per impostazione predefinita, il motore Spark deciderà automaticamente se trasmettere o meno un lato di un join. Se si ha familiarità con i dati in arrivo e si sa che un flusso sarà significativamente più piccolo dell'altro, è possibile selezionare broadcast **fisso** . Il broadcast fisso forza la trasmissione del flusso selezionato da Spark. 

Se la dimensione dei dati trasmessi è troppo grande per il nodo Spark, è possibile che si verifichi un errore di memoria insufficiente. Per evitare errori di memoria insufficiente, usare cluster con ottimizzazione per la **memoria** . Se si verificano timeout di trasmissione durante le esecuzioni del flusso di dati, è possibile disattivare l'ottimizzazione della trasmissione. Questa operazione comporterà tuttavia un'esecuzione dei flussi di dati più lenta.

![Ottimizzazione della trasformazione tramite join](media/data-flow/joinoptimize.png "Ottimizzazione dei join")

#### <a name="cross-joins"></a>Cross join

Se si usano valori letterali nelle condizioni di join o si hanno più corrispondenze su entrambi i lati di un join, Spark eseguirà il join come cross join. Un cross join è un prodotto cartesiano completo che filtra i valori Uniti in join. Questo è significativamente più lento rispetto ad altri tipi di join. Per evitare l'effetto sulle prestazioni, assicurarsi di disporre di riferimenti a colonne su entrambi i lati delle condizioni di join.

#### <a name="sorting-before-joins"></a>Ordinamento prima di join

A differenza di Merge join in strumenti come SSIS, la trasformazione tramite join non rappresenta un'operazione di merge obbligatoria. Le chiavi di join non richiedono l'ordinamento prima della trasformazione. Il team di Azure Data Factory non consiglia di usare le trasformazioni di ordinamento nel mapping di flussi di dati.

### <a name="repartitioning-skewed-data"></a>Ripartizionamento di dati inclinati

Alcune trasformazioni, ad esempio join e aggregazioni, rimescolano le partizioni di dati e talvolta possono causare dati inclinati. Dati inclinati significa che i dati non sono distribuiti uniformemente tra le partizioni. I dati fortemente inclinati possono causare trasformazioni a valle più lente e scritture di sink. È possibile controllare l'asimmetria dei dati in qualsiasi punto di un'esecuzione del flusso di dati facendo clic sulla trasformazione nella visualizzazione monitoraggio.

![Asimmetria e curtosi](media/data-flow/skewness-kurtosis.png "Asimmetria e curtosi")

La visualizzazione monitoraggio indicherà come vengono distribuiti i dati in ogni partizione insieme a due metriche, asimmetria e curtosi. L' **asimmetria** è una misura del modo in cui i dati sono asimmetrici e possono avere un valore positivo, zero, negativo o non definito. L'asimmetria negativa indica che la coda sinistra è più lunga del diritto. **Curtosi** è la misura che indica se i dati sono con code pesanti o con una coda leggera. I valori curtosi elevati non sono auspicabili. Gli intervalli ideali di asimmetria sono compresi tra-3 e 3 e gli intervalli di curtosi sono inferiori a 10. Un modo semplice per interpretare questi numeri è esaminare il grafico delle partizioni e vedere se 1 barra è significativamente più grande del resto.

Se i dati non sono partizionati in modo uniforme dopo una trasformazione, è possibile usare la [scheda Optimize](#optimize-tab) per eseguire la ripartizione. Il rimescolamento dei dati richiede tempo e potrebbe non migliorare le prestazioni del flusso di dati.

> [!TIP]
> Se si ripartizionano i dati, ma si dispone di trasformazioni a valle che rimescolano i dati, utilizzare il partizionamento hash in una colonna utilizzata come chiave di join.

## <a name="using-data-flows-in-pipelines"></a>Uso di flussi di dati nelle pipeline 

Quando si compilano pipeline complesse con più flussi di dati, il flusso logico può avere un notevole impatto sulla tempistica e sui costi. Questa sezione illustra l'effetto delle diverse strategie di architettura.

### <a name="executing-data-flows-in-parallel"></a>Esecuzione di flussi di dati in parallelo

Se si eseguono più flussi di dati in parallelo, ADF crea cluster Spark distinti per ogni attività. In questo modo, ogni processo può essere isolato ed eseguito in parallelo, ma comporterà la presenza di più cluster in esecuzione nello stesso momento.

Se i flussi di dati vengono eseguiti in parallelo, è consigliabile non abilitare la proprietà Azure IR time to Live, perché condurrà più pool caldi inutilizzati.

> [!TIP]
> Anziché eseguire lo stesso flusso di dati più volte in un per ogni attività, organizzare i dati in un data Lake e usare i percorsi con caratteri jolly per elaborare i dati in un singolo flusso di dati.

### <a name="execute-data-flows-sequentially"></a>Eseguire flussi di dati in sequenza

Se si eseguono le attività del flusso di dati in sequenza, è consigliabile impostare una durata (TTL) nella configurazione del Azure IR. ADF riutilizzerà le risorse di calcolo con un conseguente tempo di avvio del cluster più rapido. Ogni attività sarà ancora isolata per ricevere un nuovo contesto Spark per ogni esecuzione.

L'esecuzione sequenziale dei processi richiederà probabilmente il tempo più lungo per l'esecuzione end-to-end, ma fornisce una netta separazione delle operazioni logiche.

### <a name="overloading-a-single-data-flow"></a>Overload di un singolo flusso di dati

Se si inserisce tutta la logica all'interno di un singolo flusso di dati, ADF eseguirà l'intero processo in una singola istanza di Spark. Sebbene questo possa sembrare un modo per ridurre i costi, combina flussi logici diversi e può essere difficile da monitorare ed eseguire il debug. Se un componente ha esito negativo, anche tutte le altre parti del processo avranno esito negativo. Il team di Azure Data Factory consiglia di organizzare i flussi di dati in base a flussi indipendenti della logica di business. Se il flusso di dati diventa troppo grande, la suddivisione in componenti separati renderà più semplice il monitoraggio e il debug. Sebbene non esistano limiti rigidi al numero di trasformazioni in un flusso di dati, la presenza di un numero eccessivo renderà il processo complesso.

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili altri articoli relativi alle prestazioni dei flussi di dati:

- [Attività flusso di dati](control-flow-execute-data-flow-activity.md)
- [Monitoraggio delle prestazioni dei flussi di dati](concepts-data-flow-monitoring.md)
