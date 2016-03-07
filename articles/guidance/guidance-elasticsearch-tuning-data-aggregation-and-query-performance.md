<properties
   pageTitle="Ottimizzazione dell'aggregazione dei dati e delle prestazioni delle query con Elasticsearch in Azure | Microsoft Azure"
   description="Riepilogo delle considerazioni relative all'ottimizzazione delle prestazioni di query e ricerca per Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Ottimizzazione dell'aggregazione dei dati e delle prestazioni delle query con Elasticsearch in Azure

Questo articolo fa [parte di una serie](guidance-elasticsearch.md).

Il motivo principale per l'uso di Elasticsearch è il supporto delle ricerche nei dati, per consentire agli utenti di trovare rapidamente le informazioni che stanno cercando. Il sistema deve anche consentire agli utenti di porre domande sui dati, cercare correlazioni e giungere a conclusioni utili per prendere decisioni aziendali. Questo tipo di elaborazione è ciò che distingue i dati dalle informazioni.

Questo documento riepiloga le opzioni che è possibile prendere in considerazione nel determinare il modo migliore di ottimizzare le prestazioni del sistema per query e ricerche.

Tutte le indicazioni relative alle prestazioni dipendono in larga misura dagli scenari applicabili alla propria situazione, dal volume dei dati da indicizzare e dalla frequenza con cui gli utenti e le applicazioni eseguono query sui dati. È consigliabile testare con attenzione i risultati di qualsiasi modifica nella configurazione o nella struttura di indicizzazione usando dati e carichi di lavoro personalizzati per valutare i vantaggi per scenari personalizzati specifici. A tal fine, questo documento descrive anche alcuni benchmark eseguiti per uno scenario specifico implementato con configurazioni diverse. È possibile modificare l'approccio adottato per valutare le prestazioni dei sistemi in uso.

## Considerazioni sulle prestazioni di indici e query

Questa sezione descrive alcuni fattori comuni che è opportuno considerare quando si progettano indici che devono supportare l'esecuzione rapida di query e ricerche.

### Archiviazione di più tipi in un indice

Un indice Elasticsearch può contenere più tipi. Può risultare più efficace evitare questo approccio e creare un indice separato per ogni tipo. Tenere presente quanto segue:

- Tipi diversi possono specificare analizzatori diversi e non è sempre chiaro quali analizzatori deve usare Elasticsearch se una query viene eseguita a livello di indice invece che a livello di tipo. Per informazioni dettagliate, vedere l'articolo su come [evitare problemi relativi ai tipi](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas).

- Le dimensioni delle partizioni per gli indici che contengono più tipi saranno probabilmente maggiori di quelle per gli indici che contengono un singolo tipo. Più è grande una partizione, maggiore è l'attività che Elasticsearch deve svolgere per filtrare i dati durante l'esecuzione delle query.

- Se si verifica una mancata corrispondenza significativa tra i volumi di dati per i tipi, le informazioni per un tipo possono essere sparpagliate in molte partizioni, riducendo l'efficienza delle ricerche che recuperano questi dati.

![](./media/guidance-elasticsearch/query-performance1.png)

***Figura 1. Effetti della condivisione di un indice tra tipi***

La figura 1 descrive questo scenario. Nella parte superiore del diagramma lo stesso indice è condiviso da documenti di tipo A e di tipo B. Sono presenti molti più documenti di tipo A rispetto a quelli di tipo B. Le ricerche del tipo A comportano l'esecuzione di query su tutte e quattro le partizioni. La parte inferiore del diagramma mostra l'effetto della creazione di indici separati per ogni tipo. In questo caso, le ricerche del tipo A richiedono l'accesso solo a due partizioni.

Le partizioni di piccole dimensioni possono essere distribuite in modo più uniforme rispetto a quelle di grandi dimensioni, rendendo più semplice per Elasticsearch ripartire il carico tra i nodi.

Tipi diversi possono avere periodi di conservazione diversi. Può essere difficile archiviare dati obsoleti che condividono le partizioni con dati attivi.

In alcune circostanze la condivisione di un indice tra più tipi può tuttavia essere efficiente se:

- Le ricerche sono estese regolarmente a tipi inclusi nello stesso indice.

- Ogni tipo ha solo un numero limitato di documenti. In questo caso, mantenere un set di partizioni distinto per ogni tipo può comportare un sovraccarico significativo.

### Ottimizzazione dei tipi di indice

Un indice di Elasticsearch contiene una copia dei documenti JSON originali usati per popolarlo. Queste informazioni sono incluse nel campo [*\_source*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) di ogni elemento indicizzato. Questi dati non sono disponibili per la ricerca ma, per impostazione predefinita, vengono restituiti dalle richieste *get* e *search*. Questo campo comporta tuttavia un sovraccarico e occupa spazio di archiviazione, aumentando le dimensioni delle partizioni e il volume delle operazioni di I/O eseguite. È possibile disabilitare il campo *\_source* in base al tipo:

```http
PUT my_index
{
    "mappings": {
		"my_type": {
			"_source": {
				"enabled": false
			}
		}
	}
}
```

Se si disabilita questo campo, viene rimossa anche la possibilità di eseguire le operazioni seguenti:

- Aggiornamento dei dati nell'indice con l'API *update*.
- Esecuzione di ricerche che restituiscono dati evidenziati.
- Reindicizzazione da un indice di Elasticsearch direttamente in un altro.
- Modifica dei mapping o delle impostazioni di analisi.
- Debug di query visualizzando il documento originale.
 
### Reindicizzazione dei dati

Il numero di partizioni disponibili per un indice determina in ultima analisi la capacità dell'indice. È possibile formulare un'ipotesi iniziale, e basata su informazioni aggiornate, circa il numero di partizioni che saranno necessarie, ma è opportuno considerare prima di tutto le strategia di reindicizzazione dei documenti in atto. In molti casi, la reindicizzazione può essere un'attività prevista man mano che aumenta il volume dei dati. È possibile che si voglia evitare di allocare inizialmente un numero elevato di partizioni a un indice allo scopo di ottimizzare la ricerca, ma che si preferisca allocare nuove partizioni man mano che cresce il volume dei dati. In altri casi può essere necessario eseguire la reindicizzazione su una base più ad hoc se le stime sulla crescita del volume di dati si rivelano semplicemente non corrette.

> [AZURE.NOTE] La reindicizzazione potrebbe non essere necessaria per i dati che diventano rapidamente obsoleti. In questo caso, un'applicazione può creare un nuovo indice per ogni periodo di tempo. Sono inclusi, ad esempio, i log delle prestazioni o i dati di controllo che possono essere archiviati in un nuovo indice ogni giorno.

La reindicizzazione prevede in effetti la creazione di un nuovo indice dai dati contenuti in uno vecchio e la successiva rimozione dell'indice precedente. Se un indice è di grandi dimensioni, il processo può richiedere tempo e potrebbe essere necessario assicurarsi che i dati rimangono disponibili per la ricerca durante questo intervallo. Per questo motivo, è necessario creare un [alias per ogni indice](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html) e le query devono recuperare i dati con questi alias. Durante la reindicizzazione l'alias dovrà continuare a puntare all'indice precedente e dovrà quindi essere modificato per fare riferimento al nuovo indice una volta completata la reindicizzazione. Questo approccio è utile anche per l'accesso ai dati temporizzati che creano un nuovo indice ogni giorno. Per accedere ai dati correnti viene usato un alias che passa automaticamente al nuovo indice non appena viene creato.

### Gestione dei mapping

Elasticsearch usa i mapping per determinare come interpretare i dati presenti in ogni campo di un documento. Ogni tipo ha un proprio mapping, che definisce in effetti uno schema per quel tipo. Elasticsearch usa queste informazioni per generare indici invertiti per ogni campo presente nei documenti in un tipo. In qualsiasi documento ogni campo ha un tipo di dati, ad esempio *string*, *date* o *long*, e un valore. Quando si crea l'indice si possono specificare i mapping oppure questi possono essere dedotti da Elasticsearch quando vengono aggiunti nuovi documenti a un tipo. Tenere tuttavia presente quanto segue:

- I mapping generati in modo dinamico possono causare errori a seconda di come vengono interpretati i campi quando si aggiungono documenti a un indice. Ad esempio, il documento 1 può includere un campo A che contiene un numero. In questo caso Elasticsearch dovrà aggiungere un mapping che specifica che questo campo è di tipo *long*. Se in seguito viene aggiunto un documento in cui il campo A contiene dati non numerici, si verificherà un errore. In questo caso, il campo A avrebbe dovuto essere interpretato come una stringa quando è stato aggiunto il primo documento. Per evitare questi problemi, può essere utile specificare il mapping quando si crea l'indice.

- Progettare i documenti in modo da evitare di generare mapping di dimensioni eccessive, perché questo può comportare un sovraccarico significativo durante l'esecuzione delle ricerche, utilizzare una grande quantità di memoria e anche impedire che le query trovino i dati. Adottare una convenzione di denominazione coerente per i campi presenti nei documenti che condividono lo stesso tipo. Ad esempio, non usare nomi di campo come "nome", "prenome" e "nome di battesimo" in documenti diversi, ma usare lo stesso nome di campo in ogni documento. Evitare anche di usare valori come chiavi. Questo è un approccio comune nei database di tipo famiglia di colonne, ma può causare errori e inefficienze con Elasticsearch. Per altre informazioni, vedere l'articolo relativo all'[esplosione del mapping](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion).

- Usare *not\_analyzed* per evitare la tokenizzazione, se necessario. Ad esempio, se un documento contiene un campo di tipo stringa denominato *data* che contiene il valore "ABC DEF", è possibile provare a eseguire una ricerca di tutti i documenti che corrispondono a questo valore come indicato di seguito:

```http
GET /myindex/mydata/_search
{
	"query" : {
		"filtered" : {
			"filter" : {
				"term" : {
					"data" : "ABC-DEF"
				}
			}
		}
	}
}
```

Tuttavia, la ricerca non restituirà i risultati previsti a causa della modalità di tokenizzazione della stringa ABC DEF al momento dell'indicizzazione. La stringa verrà in effetti suddivisa in base al trattino in due token, ABC e DEF. Questa funzionalità è progettata per supportare la ricerca full-text, ma se si vuole che la stringa sia interpretata come un singolo elemento atomico, è consigliabile disabilitare la tokenizzazione quando il documento viene aggiunto all'indice. È possibile usare un mapping simile al seguente:

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					"type" : "string",
					"index" : "not_analyzed"
				}
			}
		}
	}
}
```

Per altre informazioni, vedere l'articolo relativo alla [ricerca di valori esatti](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text).

### Uso di valori dei documenti (doc\_values)

Molte query e aggregazioni richiedono che i dati siano ordinati come parte dell'operazione di ricerca. L'ordinamento richiede che sia possibile eseguire il mapping di uno o più termini a un elenco di documenti. Per semplificare questo processo, Elasticsearch può caricare in memoria tutti i valori per un campo usato come chiave di ordinamento. Queste informazioni sono note come *dati dei campi* o "fielddata". Lo scopo è memorizzare i dati dei campi nella cache perché richiede meno operazioni di I/O e può essere più veloce rispetto alla lettura ripetuta degli stessi dati dal disco. Tuttavia, se un campo ha una cardinalità elevata, l'archiviazione dei dati dei campi in memoria può comportare l'uso di una quantità elevata di spazio dell'heap, con un possibile impatto sulla capacità di eseguire altre operazioni simultanee o la possibilità di esaurire lo spazio di archiviazione, determinando un errore di Elasticsearch.

Come approccio alternativo, Elasticsearch supporta anche *valori dei documenti* (doc\_values). Un valore di documento è simile a un elemento di dati dei campi in memoria, salvo che viene archiviato su disco e creato quando i dati vengono archiviati in un indice. I dati dei campi vengono costruiti dinamicamente quando si esegue una query. I valori dei documenti non utilizzano lo spazio dell'heap e quindi sono utili per le query che ordinano o aggregano dati tra campi che possono contenere un numero molto elevato di valori univoci. Inoltre, la minore pressione sull'heap consente di compensare le differenze di prestazioni tra il recupero dei dati dal disco e la lettura dalla memoria. È probabile che le operazioni di Garbage Collection avvengano meno frequentemente e che altre operazioni simultanee che utilizzano la memoria siano meno interessate.

I valori dei documenti vengono abilitati o disabilitati per le singole proprietà di un indice tramite l'attributo *doc\_values*, come illustrato nell'esempio seguente:

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					...
				"doc_values": true
				}
			}
		}
	}
}
```

> [AZURE.NOTE] I valori dei documenti sono abilitati per impostazione predefinita a partire da Elasticsearch versione 2.0.0.

L'esatto impatto dell'uso di valori dei documenti è molto probabilmente specifico degli scenari di dati e query in uso, quindi sarà necessario eseguire test delle prestazioni per stabilire la loro utilità. Si noti anche che i valori dei documenti non funzionano con i campi di tipo stringa analizzati. Per altre informazioni, vedere l'articolo relativo ai [valori dei documenti](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values)

### Uso di nodi client

Tutte le query vengono elaborate dal nodo che per primo riceve la richiesta. Questo nodo invia ulteriori richieste a tutti gli altri nodi che contengono partizioni per gli indici su cui viene eseguita la query e quindi accumula i risultati per restituire la risposta. Se una query implica l'aggregazione dei dati o l'esecuzione di calcoli complessi, il nodo iniziale è responsabile dell'esecuzione dell'elaborazione appropriata. Se il sistema deve supportare un numero relativamente ridotto di query complesse, provare a creare un pool di nodi client per ridurre il carico dei nodi dati. Viceversa, se il sistema deve gestire un numero elevato di query semplici, inviare queste richieste direttamente ai nodi dati e usare un servizio di bilanciamento del carico per distribuire le richieste in modo uniforme.

### Uso di repliche per ridurre la contesa di query

Una strategia comune per migliorare le prestazioni delle query consiste nel creare molte repliche di ogni indice. Le operazioni di recupero dei dati possono essere soddisfatte recuperando i dati da una replica. Questa strategia può tuttavia compromettere le prestazioni delle operazioni di inserimento dei dati, quindi deve essere usata con cautela negli scenari che prevedono carichi di lavoro misti. Questa strategia costituisce un vantaggio solo se le repliche vengono distribuite tra i nodi e non contendono le risorse con partizioni primarie che fanno parte dello stesso indice. Tenere presente che è possibile aumentare o diminuire dinamicamente il numero di repliche di un indice.

### Uso della cache delle richieste delle partizioni

Elasticsearch può memorizzare nella cache i dati locali richiesti dalle query in ogni partizione. In questo modo le query che accedono agli stessi dati possono essere eseguite più rapidamente. I dati possono infatti essere recuperati dalla memoria invece che dallo spazio di archiviazione su disco. I dati nella cache vengono invalidati quando si aggiorna la partizione e i dati vengono modificati. La frequenza degli aggiornamenti è disciplinata dal valore dell'impostazione *refresh\_interval* dell'indice. La cache delle richieste delle partizioni per un indice è disabilitata per impostazione predefinita, ma è possibile abilitarla come indicato di seguito:

```http
PUT /myindex/_settings
{
	"index.requests.cache.enable": true
}
```

La cache delle richieste delle partizioni è più adatta per le informazioni che rimangono relativamente statiche, ad esempio i dati cronologici o di registrazione.

## Aggregazione di test e analisi e prestazioni della ricerca
Questa sezione descrive i risultati di una serie di test eseguiti su diverse configurazioni di cluster e indici. Ogni test è iniziato con un indice vuoto, che è stato compilato durante l'avanzamento del test mediante l'esecuzione di operazioni di inserimento in blocco, dove ogni operazione ha aggiunto 1000 documenti. Contemporaneamente, a intervalli di 5 secondi è stato ripetuto un certo numero di query progettate per la ricerca di dati specifici e per generare aggregazioni. Lo scopo dei test è stato quello di stabilire in che modo il volume dei dati ha inciso sulle prestazioni delle query.

Ogni documento nell'indice aveva lo stesso schema. Questa tabella riepiloga i campi presenti nello schema:

 Nome | Tipo | Note |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 Organizzazione | String | Il test genera 200 organizzazioni univoche. |
 CustomField1 - CustomField5 |String |Sono presenti cinque campi di tipo stringa impostati su una stringa vuota.|
 DateTimeRecievedUtc |Timestamp |Data e ora in cui è stato aggiunto il documento.|
 Host |String |Questo campo è impostato su una stringa vuota.|
 HttpMethod |String |Questo campo è impostato su uno dei valori seguenti: "POST", "GET", "PUT".|
 HttpReferrer |String |Questo campo è impostato su una stringa vuota.|
 HttpRequest |String |Questo campo viene popolato con testo casuale con una lunghezza compresa tra 10 e 200 caratteri.|
 HttpUserAgent |String |Questo campo è impostato su una stringa vuota.|
 HttpVersion |String |Questo campo è impostato su una stringa vuota.|
 OrganizationName |String |Questo campo è impostato sullo stesso valore del campo relativo all'organizzazione.|
 SourceIp |IP |Questo campo contiene un indirizzo IP che indica "l'origine" dei dati. |
 SourceIpAreaCode |Long |Questo campo è impostato su 0.|
 SourceIpAsnNr |String |Questo campo è impostato su "AS#####".|
 SourceIpBase10 |Long |Questo campo è impostato su 500.|
 SourceIpCountryCode |String |Questo campo contiene un codice paese di 2 caratteri. |
 SourceIpCity |String |Questo campo contiene una stringa che identifica una città in un paese. |
 SourceIpLatitude |Double |Questo campo contiene un valore casuale.|
 SourceIpLongitude |Double |Questo campo contiene un valore casuale.|
 SourceIpMetroCode |Long |Questo campo è impostato su 0.|
 SourceIpPostalCode |String |Questo campo è impostato su una stringa vuota.|
 SourceLatLong |GeoPoint |Questo campo è impostato su un punto geografico casuale.|
 SourcePort |String |Questo campo viene popolato con la rappresentazione di stringa di un numero casuale.|
 TargetIp |IP |Viene popolato con un indirizzo IP casuale nell'intervallo da 0.0.100.100 a 255.9.100.100|
 SourcedFrom |String |Questo campo è impostato sulla stringa "MonitoringCollector".|
 TargetPort |String |Questo campo viene popolato con la rappresentazione di stringa di un numero casuale.|
 Rating |String |Questo campo viene popolato con uno dei 20 diversi valori stringa selezionati in modo casuale.|
 UseHumanReadableDateTimes |Boolean |Questo campo è impostato su false.|

Le query seguenti sono state eseguite come operazione batch in ogni iterazione del test. I nomi in corsivo vengono usati per fare riferimento a queste query nel resto di questo documento:

- Quanti documenti con ogni valore *Rating* sono stati immessi negli ultimi 15 minuti (*Count By Rating*)?

- Quanti documenti sono stati aggiunti in ogni intervallo di 5 minuti negli ultimi 15 minuti (*Count Over Time*)?

- Quanti documenti con ogni valore *Rating* sono stati aggiunti per ogni paese negli ultimi 15 minuti (*Hits By Country*)?

- Quali 15 organizzazioni ricorrono con maggiore frequenza nei documenti aggiunti negli ultimi 15 minuti (*Top 15 Organizations*)?

- Quante organizzazioni diverse ricorrono nei documenti aggiunti negli ultimi 15 minuti (*Unique Count Organizations*)?

- Quanti documenti sono stati aggiunti negli ultimi 15 minuti (*Total Hits Count*)?

- Quanti valori *SourceIp* diversi ricorrono nei documenti aggiunti negli ultimi 15 minuti (*Unique IP Count*)?

I test sono stati eseguiti per comprendere gli effetti delle variabili seguenti:

- **Tipo di disco**. Il test è stato eseguito su un cluster di 6 nodi con macchine virtuali D4 che usano l'archiviazione Standard (HDD) e ripetuto su un cluster di 6 nodi con macchine virtuali DS4 che usano l'Archiviazione Premium (unità SSD).

- **Dimensioni delle macchine virtuali: passaggio a un piano superiore**. Il test è stato eseguito su un cluster di 6 nodi che comprende macchine virtuali DS3 (designato come cluster *small*), ripetuto su un cluster di macchine virtuali DS4 (il cluster *medium* ) e ripetuto di nuovo su un cluster di macchine virtuali DS14 (il cluster *large* ). La tabella seguente riepiloga le caratteristiche principali di ogni SKU di VM:

Cluster |SKU di VM |Numero di core |Numero di dischi dati |RAM (GB)|
--------- |--------------- |----------------- |---------------------- |--------|
Small |Standard DS3 |4 |8 |14 |
Medium |DS4 standard |8 |16 |28 |
Large |Standard DS14 |16 |32 |112 |

- **Valori dei documenti**. Inizialmente sono stati eseguiti i test con l'impostazione dell'indice *doc\_values* impostata su *true*. Test selezionati sono stati ripetuti con *doc\_values* impostata su *false*.

## Risultati delle prestazioni: tipo di disco

La tabella seguente riepiloga i tempi di risposta delle operazioni eseguite tramite l'esecuzione del test sul cluster di 6 nodi con macchine virtuali D4 (HDD) e sul cluster di 6 nodi con macchine virtuali DS4 (unità SSD). La configurazione di Elasticsearch in entrambi i cluster era la stessa. I dati sono stati ripartiti su 16 dischi in ogni nodo e 14 GB di RAM di ogni nodo sono stati allocati alla JVM che esegue Elasticsearch. La memoria rimanente, anche in questo caso 14 GB, è rimasta disponibile per l'uso del sistema operativo. Ogni test è stato eseguito per 24 ore. Questo intervallo è stato scelto per consentire che gli effetti del volume di dati crescente diventassero evidenti e per consentire al sistema di stabilizzarsi:

 Cluster |Operazione/query |Tempo medio di risposta (ms)|
 -----------|---------------------------- |--------------------------|
 D4 |Ingestion |978 |
 |Count By Rating |103 |
 |Count Over Time |134 |
 |Hits By Country |199 |
 |Top 15 Organizations |137 |
 |Unique Count Organizations |139 |
 |Unique IP Count |510 |
 |Total Hits Counts |89 |
 DS4 |Ingestion |511 |
 |Count By Rating |187 |
 |Count Over Time |411 |
 |Hits By Country |402 |
 |Top 15 Organizations |307 |
 |Unique Count Organizations |320 |
 |Unique IP Count |841 |
 |Total Hits Counts |236 |

A prima vista, sembra che il cluster DS4 abbia eseguito query con prestazioni inferiori rispetto al cluster D4, a volte raddoppiando (o peggiorando) il tempo di risposta. Questo risultato non è però esauriente. La tabella seguente illustra il numero di operazioni di inserimento eseguite da ogni cluster. Tenere presente che ogni operazione carica 1000 documenti:

 Cluster | Operazioni di inserimento
 ----------|---------------------
 D4 | 264769              
 DS4 | 503157              

Durante il test il cluster DS4 è riuscito a caricare quasi due volte la quantità di dati rispetto al cluster D4. Quando si analizzano i tempi di risposta per ogni operazione, è tuttavia necessario considerare il numero di documenti che ogni query ha dovuto analizzare e quanti documenti sono stati restituiti.

Queste sono cifre dinamiche, perché il volume di documenti nell'indice cresce continuamente. Non si può dividere semplicemente 503137 per 264769, ovvero il numero di operazioni di inserimento eseguite da ogni cluster, e quindi moltiplicare il risultato per il tempo di risposta medio per ogni query eseguita dal cluster D4 per fornire una cifra comparativa, perché in questo modo si ignora la quantità di operazioni di I/O eseguite contemporaneamente dall'operazione di inserimento.

Al contrario, si dovrebbe misurare la quantità fisica dei dati scritti sul disco e letti durante l'esecuzione del test. Il piano di test JMeter consente di acquisire queste informazioni per ogni nodo. I risultati riepilogati sono stati:

 Cluster |Media dei byte scritti o letti da ogni operazione|
  --------- |--------------------------------------------|
 D4 |13471557 |
 DS4 |24643470 |

Queste cifre mostrano che il cluster DS4 è riuscito a sostenere una frequenza di I/O circa 1,8 volte quella del cluster D4. Ciò detto, a parte la natura dei dischi, tutte le altre risorse sono uguali, quindi la differenza deve essere dovuta all'uso di unità SSD invece che HDD.

Per giustificare questa conclusione, i grafici seguenti illustrano come sono state eseguite le operazioni di I/O nel corso del tempo per ogni cluster:

![](./media/guidance-elasticsearch/query-performance2.png)

Il grafico per il cluster D4 mostra una variazione significativa, soprattutto durante la prima metà del test. Ciò è probabilmente dovuto alla limitazione per ridurre la velocità di I/O. Nelle fasi iniziali del test le query sono state eseguite rapidamente, data la quantità ridotta di dati da analizzare. È quindi probabile che i dischi nel cluster D4 funzionino quasi alla massima capacità di IOPS, anche se ogni operazione di I/O potrebbe non restituire molti dati. Il cluster DS4 può supportare una maggiore velocità di IOPS e non risente dello stesso livello di limitazione. Le velocità di I/O sono più regolari.

Per illustrare questa teoria, la coppia di grafici successiva mostra come la CPU sia stata bloccata dall'I/O del disco nel corso del tempo. I tempi di attesa del disco visualizzati nei grafici sono la percentuale del tempo impiegato dalla CPU in attesa di I/O:

![](./media/guidance-elasticsearch/query-performance3.png)

È importante comprendere che in questo scenario di test ci sono due motivi principali per cui le operazioni di I/O bloccano la CPU:

- È possibile che il sottosistema di I/O stia leggendo o scrivendo dati da e sul disco.

- Il sottosistema di I/O potrebbe essere limitato dall'ambiente host. I dischi di Azure supportati dall'archiviazione Standard hanno una velocità effettiva massima di 500 IOPS, mentre quelli supportati dall'Archiviazione Premium hanno una velocità effettiva massima di 5000 IOPS.

Per il cluster D4 la quantità di tempo trascorso in attesa di I/O durante la prima metà del test è strettamente correlata inversamente con il grafico che mostra le velocità di I/O. I periodi di bassa velocità di I/O corrispondono ai significativi periodi di tempo durante i quali la CPU rimane bloccata.

Ciò indica che le operazioni di I/O sono limitate. La situazione cambia man mano che vengono aggiunti dati al cluster e nella seconda metà del test i picchi nei tempi di attesa di I/O corrispondono a picchi nella velocità effettiva di I/O. A questo punto, la CPU è bloccata durante l'esecuzione di operazioni di I/O effettive. Anche in questo caso, con il cluster DS4 il tempo di attesa di I/O è molto più uniforme e ogni picco corrisponde a un picco equivalente nelle prestazioni di I/O invece di un calo. Ciò implica che la limitazione è minima o del tutto assente.

Occorre considerare anche un altro fattore. Durante il test il cluster D4 ha generato 10584 errori di inserimento e 21 errori di query. Il test sul cluster DS4 non ha prodotto errori.

## Risultati delle prestazioni: passaggio a un piano superiore

La tabella seguente riepiloga i risultati dell'esecuzione dei test sui cluster di medie (DS4) e grandi dimensioni (DS14). Ogni macchina virtuale usava unità SSD per contenere i dati. Ogni test è stato eseguito per 24 ore:

| Cluster |Operazione/query |Numero di richieste |Tempo medio di risposta (ms)|
|  -------------- |---------------------------- |-------------------- |--------------------------|
| Medium (DS4) |Ingestion |503157 |511 |
| |Count By Rating |6958 |187 |
| |Count Over Time |6958 |411 |
| |Hits By Country |6958 |402 |
| |Top 15 Organizations |6958 |307 |
| |Unique Count Organizations |6956 |320 |
| |Unique IP Count |6955 |841 |
| |Total Hits Counts |6958 |236 |
| Large (DS14) |Ingestion |502714 |511 |
| |Count By Rating |7041 |201 |
| |Count Over Time |7040 |298 |
| |Hits By Country |7039 |363 |
| |Top 15 Organizations |7038 |244 |
| |Unique Count Organizations |7037 |283 |
| |Unique IP Count |7037 |681 |
| |Total Hits Counts |7038 |200 |

<!-- 
DISCUSSION POINTS:

Similar volume of data ingested – same disk configuration for each cluster, and ingestion rate is 
constrained by I/O performance?

Average response time for queries decreases with SKU.

Show CPU graphs

Show memory utilization – more data cached, fewer GCs, etc.

-->

<!--
To isolate the effects of the ingestion operations and show how query performance varies as nodes scale up, a second set of tests was performed using the same nodes. The ingestion part of the test was omitted, and the index on each node was pre-populated with 100 million documents. An amended set of queries was performed; the time element limiting documents to those added in the last 15 minutes was removed as the data was now static. The tests ran for 90 minutes; there is less need to allow the system to stabilize due to the fixed amount of data. The following table summarizes the results obtained on each cluster:

> [AZURE.NOTE] The amended version of the test that omits the data ingestion process and that uses a set of indexes containing 100 million documents is referred to as the *query-only* test in the remainder of this document. You should not compare the performance of the queries in this test with that of the tests that perform ingestion and query operations because the queries have been modified and the volume of documents involved is different.

 |Cluster        |Operation/Query              |Number of Requests   Average Response Time (ms)
 | --------------| ----------------------------| -------------------- ----------------------------
 | Small (DS3)   | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Medium (DS4)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Large (DS14)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      

### Performance Results – Scaling Out
-->


<!--
To show the system scales out with the number of nodes, tests were run using DS14 clusters comprising 1, 3, and 6 nodes. This time, only the query-only test was performed, using 100 million documents and running for 90 minutes:

> [AZURE.NOTE] For detailed information on how scaling out can affect the behavior of data ingestion operations, see the document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

|  Cluster   |Operation/Query              |Number of Requests   |Average Response Time (ms)
|  --------- |---------------------------- |-------------------- |----------------------------
|  1 Node    |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  3 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  6 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
-->

<!--
### Performance Results – Number of Replicas

The ingestion and query tests were run against an index with a single replica. The tests were repeated on the 6-node DS4 and DS14 clusters using an index configured with two replicas. All tests ran for 24 hours. The table below shows the comparative results for one and two replicas:

|  Cluster|  Operation/Query            | response time 1 Replica (ms)| response time 2 Replicas (ms) | % Difference in Response Time
|  -------| ----------------------------| ----------------------------|----------- -------------------|--------------------- --------
|  DS4    |   Ingestion                 |   511                       |               655             |                          +28%
|         |   Count By Rating           |   187                       |               168             |                          -10%
|         |   Count Over Time           |   411                       |               309             |                          -25%
|         |   Hits By Country           |   402                       |               562             |                          +40%
|         |   Top 15 Organizations      |   307                       |               366             |                          +19%
|         |   Unique Count Organizations|   320                       |               378             |                          +18%
|         |   Unique IP Count           |   841                       |               987             |                          +17%
|         |   Total Hits Counts         |   236                       |               236             |                          +0%
|  DS14   |   Ingestion                 |   511                       |               618             |                          +21%
|         |   Count By Rating           |   201                       |               275             |                          +37%
|         |   Count Over Time           |   298                       |               466             |                          +56%
|         |   Hits By Country           |   363                       |               529             |                          +46%
|         |   Top 15 Organizations      |   244                       |               407             |                          +67%
|         |   Unique Count Organizations|   283                       |               403             |                          +42%
|         |   Unique IP Count           |   681                       |               823             |                          +21%
|         |   Total Hits Counts         |   200                       |               221             |                          +11%


NEED ## OF DOCUMENTS RETURNED TO JUSTIFY THIS DATA, OTHERWISE PERF FOR 2 REPLICAS LOOKS OFF!

PRESENT QUERY-ONLY TEST RESULTS TO SHOW BETTER RESULTS
-->

## Risultati delle prestazioni: valori dei documenti

I test di inserimento e query sono stati eseguiti con i valori dei documenti (doc\_values) abilitati, causando l'archiviazione su disco dei dati usati per i campi di ordinamento da parte di Elasticsearch. I test sono stati ripetuti con valori dei documenti disabilitati e Elasticsearch ha costruito dinamicamente i dati dei campi e li ha memorizzato nella cache. Tutti i test sono stati eseguiti per 24 ore.
 
 La tabella seguente confronta i tempi di risposta per i test eseguiti su cluster di 6 nodi creati con macchine virtuali D4, DS4 e DS14.

| Cluster |Operazione/query |Valori dei documenti abilitati (ms) | Valori dei documenti disabilitati (ms) | Differenza: % |
|  --------- |---------------------------| -----------------------|--------------------------  |--------------------|
| D4 |Ingestion | 978 | 835 | -15% |
| |Count By Rating | 103 | 132 | +28% |
| |Count Over Time | 134 | 189 | +41% |
| |Hits By Country | 199 | 259 | +30% |
| |Top 15 Organizations | 137 | 184 | +34% |
| |Unique Count Organizations | 139 | 197 | +42% |
| |Unique IP Count | 510 | 604 | +18% |
| |Total Hits Counts | 89 | 134 | +51% |
| DS4 |Inserimento | 511 | 581 | +14% |
| |Count By Rating | 187 | 190 | +2% |
| |Count Over Time | 411 | 409 | -0,5% |
| |Hits By Country | 402 | 414 | +3% |
| |Top 15 Organizations | 307 | 284 | -7% |
| |Unique Count Organizations | 320 | 313 | -2% |
| |Unique IP Count | 841 | 955 | +14% |
| |Total Hits Counts | 236 | 281 | +19% |
| DS14 |Ingestion | 511 | 571 | +12% |
| |Count By Rating | 201 | 232 | +15% |
| |Count Over Time | 298 | 341 | +14% |
| |Hits By Country | 363 | 457 | +26% |
| |Top 15 Organizations | 244 | 338 | +39% |
| |Unique Count Organizations | 283 | 350 | +24% |
| |Unique IP Count | 681 | 909 | +33% |
| |Total Hits Counts | 200 | 245 | +23% |


<!--
The next table compares the number of ingestion operations performed by the tests:

  Cluster   Ingestion Operations – Doc Values Enabled   ## Ingestion Operations – Doc Values Disabled   % Difference in ## Ingestion Operations
  --------- ---------------------------------------------- ----------------------------------------------- -----------------------------------------
  D4        264769                                         408690                                          +54%
  DS4       503137                                         578237                                          +15%
  DS14      502714                                         586472                                          +17%

The improved ingestion rates occur with doc values disabled as less data is being written to disk as documents are inserted. The improved performance is especially noticeable with the D4 VM using HDDs to store data. In this case, the response time for ingestion operations also decreased by 15% (see the first table in this section). This could be due to the reduced pressure on the HDDs which were likely running close to their IOPS limits in the test with doc values enabled; see the [Disk Type](#performance-results-disk-type) test for more information. The following graph compares the I/O performance of the D4 VMs with doc values enabled (values held on disk) and doc values disabled (values held in memory):

![](./media/guidance-elasticsearch/query-performance4.png)

In contrast, the ingestion figures for the VMs using SSDs show a small increase in the number of documents but also an increase in the response time of the ingestion operations. With one or two small exceptions, the query response times were also worse. The SSDs are less likely to be running close to their IOPS limits with doc values enabled, so changes in performance are more likely due to increased processing activity and the overhead of managing the JVM heap. This is evident by comparing the CPU utilization with doc values enabled and disabled. The next graph highlights this data for the DS4 cluster, where most of the CPU utilization moves from the 30%-40% band with doc values enabled, to the 40%-50% band with doc values disabled (the DS14 cluster showed a similar trend):

![](./media/guidance-elasticsearch/query-performance5.png)

To isolate the effects that doc values on query performance from data ingestion, a pair of query-only tests were performed for each cluster as follows:

In the first test, the Elasticsearch index was created with doc values enabled, the index was pre-populated with 100 million documents, and then modified set of queries used by the replica tests were performed repeatedly for 90 minutes while performance statistics were gathered.

In the second test, the Elasticsearch index was created with doc values disabled, populated, and then subjected to the same query load for the same period as the first test.
-->

<!--
## Appendix: The Query and Aggregation Performance Test

This appendix describes the performance test performed against the Elasticsearch cluster. The tests were run by using JMeter running on a separate set of VMs. Details the configuration of the test environment are described in [Creating a Performance Testing Environment for Elasticsearch on Azure][]. To perform your own testing, you can create your own JMeter test plan manually following the guidance in this appendix, or you can use the automated test scripts available separately. See [Running the Automated Elasticsearch Performance Tests][] for further information.

The data query workload performed the set of queries described below while performing a large-scale upload of documents at the same time (the data was uploaded by using a JUnit test, following the same approach for the data ingestion tests described in the document Maximizing Data Ingestion Performance with Elasticsearch on Azure.) The purpose of this workload was to simulate a production environment where new data is constantly being added while searches are performed. The queries were structured to retrieve only the most recent data from documents added in the last 15 minutes.

Each document was stored in a single index named *sample*, and had the type *ctip*. You can use the following HTTP request to create the index. The *number\_of\_replicas* and *number\_of\_shards* settings varied from the values shown below in many of the tests. Additionally, for the tests that used fielddata rather than doc values, each property was annotated with the attribute *"doc\_values" : false*.

**Important**. The index was dropped and recreated prior to each test run.

```http
PUT /sample
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "ctip": {
        "mappings": {
            "event": {
                "_all": {
                    "enabled": false
                },
                "_timestamp": {
                    "enabled": true,
                    "store": true,
                    "format": "date_time"
                },
                "properties": {
                    "Organization": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField1": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField2": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField3": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField4": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField5": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "DateTimeReceivedUtc": {
                        "type": "date",
                        "format": "dateOptionalTime"
                    },
                    "Host": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpMethod": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpReferrer": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpRequest": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpUserAgent": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpVersion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "OrganizationName": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIp": {
                        "type": "ip"
                    },
                    "SourceIpAreaCode": {
                        "type": "long"
                    },
                    "SourceIpAsnNr": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpBase10": {
                        "type": "long"
                    },
                    "SourceIpCity": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpCountryCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpLatitude": {
                        "type": "double"
                    },
                    "SourceIpLongitude": {
                        "type": "double"
                    },
                    "SourceIpMetroCode": {
                        "type": "long"
                    },
                    "SourceIpPostalCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpRegion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceLatLong": {
                        "type": "geo_point",
                        "doc_values": true,
                        "lat_lon": true,
                        "geohash": true
                    },
                    "SourcePort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourcedFrom": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "TargetIp": {
                        "type": "ip"
                    },
                    "TargetPort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "Rating": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "UseHumanReadableDateTimes": {
                        "type": "boolean"
                    }
                }
            }
        }
    }
}

```

The following queries were performed by the test:

- How many documents with each *Rating* value have been entered in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many documents have been added in each 5 minute interval during the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "sort": [],
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "DateTimeReceivedUtc",
        "interval": "5m",
        "time_zone": "America/Los_Angeles",
        "min_doc_count": 1,
        "extended_bounds": {
          "min": "now-15m",
          "max": "now"
        }
      }
    }
  }
}

```

- How many documents of each *Rating* value have been added for each country in the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      },
      "aggs": {
        "3": {
          "terms": {
            "field": "SourceIpCountryCode",
            "size": 15,
            "order": {
              "_count": "desc"
            }
          }
        }
      }
    }
  }
}

```

- Which 15 organizations occur most frequently in documents added in the last 15 minutes?

``` http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Organization",
        "size": 15,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many different organizations occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "Organization"
      }
    }
  }
}

```

- How many documents have been added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "analyze_wildcard": true,
                  "query": "*"
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {}
}

```

- How many different *SourceIp* values occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "SourceIp"
      }
    }
  }
} 

```

[Running the Automated Elasticsearch Performance Tests]: guidance-elasticsearch-running-automated-performance-tests.md
[Creating a Performance Testing Environment for Elasticsearch on Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
-->

<!---HONumber=AcomDC_0224_2016-->