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
   ms.date="02/29/2016"
   ms.author="masimms"/>
   
# Ottimizzazione dell'aggregazione dei dati e delle prestazioni delle query con Elasticsearch in Azure


Questo articolo fa [parte di una serie](guidance-elasticsearch.md).

Il motivo principale per l'uso di Elasticsearch è il supporto delle ricerche nei dati, per consentire agli utenti di trovare rapidamente le informazioni che stanno cercando. Il sistema deve anche consentire agli utenti di porre domande sui dati, cercare correlazioni e giungere a conclusioni utili per prendere decisioni aziendali. Questo tipo di elaborazione è ciò che distingue i dati dalle informazioni.

Questo documento riepiloga le opzioni che è possibile prendere in considerazione nel determinare il modo migliore di ottimizzare le prestazioni del sistema per query e ricerche.

Tutte le indicazioni relative alle prestazioni dipendono in larga misura dagli scenari applicabili alla propria situazione, dal volume dei dati da indicizzare e dalla frequenza con cui gli utenti e le applicazioni eseguono query sui dati. È consigliabile testare con attenzione i risultati di qualsiasi modifica nella configurazione o nella struttura di indicizzazione usando dati e carichi di lavoro personalizzati per valutare i vantaggi per scenari personalizzati specifici. A tal fine, questo documento descrive anche alcuni benchmark eseguiti per uno scenario specifico implementato con configurazioni diverse. È possibile modificare l'approccio adottato per valutare le prestazioni dei sistemi in uso. I dettagli di questi test sono descritti nell'[appendice](#appendix-the-query-and-aggregation-performance-test).

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

- Le partizioni di piccole dimensioni possono essere distribuite in modo più uniforme rispetto a quelle di grandi dimensioni, rendendo più semplice per Elasticsearch ripartire il carico tra i nodi.

- Tipi diversi possono avere periodi di conservazione diversi. Può essere difficile archiviare dati obsoleti che condividono le partizioni con dati attivi.


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

<!-- -->

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

    However, this search will fail to return the expected results due to the way in which the string ABC-DEF is tokenized when it is indexed; it will be effectively split into two tokens, ABC and DEF, by the hyphen. This feature is designed to support full text searching, but if you want the string to be interpreted as a single atomic item you should disable tokenization when the document is added to the index. You can use a mapping such as this:

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

### Uso di repliche per ridurre la contesa di query

Una strategia comune per migliorare le prestazioni delle query consiste nel creare molte repliche di ogni indice. Le operazioni di recupero dei dati possono essere soddisfatte recuperando i dati da una replica. Questa strategia può tuttavia compromettere le prestazioni delle operazioni di inserimento dei dati, quindi deve essere usata con cautela negli scenari che prevedono carichi di lavoro misti. Questa strategia costituisce un vantaggio solo se le repliche vengono distribuite tra i nodi e non contendono le risorse con partizioni primarie che fanno parte dello stesso indice. Tenere presente che è possibile aumentare o diminuire dinamicamente il numero di repliche di un indice.

### Uso della cache delle richieste delle partizioni

Elasticsearch può memorizzare nella cache i dati locali richiesti dalle query in ogni partizione. In questo modo le ricerche che recuperano gli stessi dati possono essere eseguite più rapidamente. I dati possono infatti essere letti dalla memoria invece che dallo spazio di archiviazione su disco. Questo tipo di memorizzazione dei dati nella cache può quindi migliorare le prestazioni di alcune operazioni di ricerca, ma comporta la riduzione della memoria disponibile per altre attività che vengono eseguite simultaneamente. Esiste anche il rischio che i dati forniti dalla cache siano obsoleti. I dati nella cache vengono invalidati solo quando si aggiorna la partizione e i dati vengono modificati. La frequenza degli aggiornamenti è disciplinata dal valore dell'impostazione *refresh\_interval* dell'indice.

La memorizzazione nella cache delle richieste per un indice è disabilitata per impostazione predefinita, ma è possibile abilitarla come indicato di seguito:

```http
PUT /myindex/_settings
{
  "index.requests.cache.enable": true
}
```

La cache delle richieste delle partizioni è più adatta per le informazioni che rimangono relativamente statiche, ad esempio i dati cronologici o di registrazione.

### Uso di nodi client

Tutte le query vengono elaborate dal nodo che per primo riceve la richiesta. Questo nodo invia ulteriori richieste a tutti gli altri nodi che contengono partizioni per gli indici su cui viene eseguita la query e quindi accumula i risultati per restituire la risposta. Se una query implica l'aggregazione dei dati o l'esecuzione di calcoli complessi, il nodo iniziale è responsabile dell'esecuzione dell'elaborazione appropriata. Se il sistema deve supportare un numero relativamente ridotto di query complesse, provare a creare un pool di nodi client per ridurre il carico dei nodi dati. Viceversa, se il sistema deve gestire un numero elevato di query semplici, inviare queste richieste direttamente ai nodi dati e usare un servizio di bilanciamento del carico per distribuire le richieste in modo uniforme.

### Ottimizzazione delle query

I punti seguenti riepilogano i suggerimenti per ottimizzare le prestazioni delle query di Elasticsearch:

- Evitare query contenenti caratteri jolly, se possibile.

- Se lo stesso campo è soggetto alla ricerca full-text e alla corrispondenza esatta, considerare la possibilità di archiviare i dati per il campo in formati analizzati e non analizzati. Eseguire le ricerche full-text nel campo analizzato e le corrispondenze esatte nel campo non analizzato.

- Restituire solo i dati necessari. Se sono presenti documenti di grandi dimensioni, ma un'applicazione richiede solo le informazioni contenute in un subset dei campi, restituire questo subset dalle query invece che gli interi documenti. Questa strategia può ridurre i requisiti di larghezza di banda di rete del cluster.

- Se possibile, usare i filtri invece delle query quando si cercano i dati. Un filtro determina semplicemente se un documento corrisponde a un determinato criterio, mentre una query calcola anche il livello di corrispondenza di un documento (punteggio). Internamente i valori generati da un filtro vengono archiviati come bitmap indicante la corrispondenza/non corrispondenza per ogni documento e possono essere memorizzati nella cache da Elasticsearch. Se lo stesso criterio di filtro viene applicato in un momento successivo, la bitmap può essere recuperata dalla cache e usata per recuperare rapidamente i documenti corrispondenti. Per altre informazioni, vedere la sezione relativa all'[operazione di filtro interno](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_internal_filter_operation).

- Usare i filtri *bool* per eseguire confronti statici e usare i filtri *and*, *or* e *not* solo per i filtri calcolati in modo dinamico, ad esempio quelli che comportano lo scripting o i filtri *geo-**.

- Se una query combina filtri *bool* con filtri *and*, *or* o *not* con *geo-**, inserire i filtri *and*/*or*/*not geo-** per ultimi in modo che vengano applicati al più piccolo set di dati possibile.

    Allo stesso modo, usare un filtro *post\_filter* per eseguire operazioni di filtro dispendiose. Questi filtri verranno eseguiti per ultimi.

- Usare le aggregazioni invece dei facet. Evitare di calcolare aggregati analizzati o con più valori possibili.

    > **Nota**: i facet sono stati rimossi in Elasticsearch versione 2.0.0.

- Preferire l'aggregazione *cardinality* invece dell'aggregazione *value\_count* a meno che l'applicazione non richieda un conteggio esatto degli elementi corrispondenti. Un conteggio esatto può diventare rapidamente obsoleto e diverse applicazioni richiedono solo un'approssimazione ragionevole.

- Evitare lo scripting. Gli script nelle query e nei filtri possono essere dispendiosi e i risultati non vengono memorizzati nella cache. Gli script con esecuzione prolungata possono utilizzare i thread di ricerca in modo illimitato, facendo accodare le richieste successive. Se la coda si riempie, le richieste successive verranno rifiutate.

## Aggregazione di test e analisi e prestazioni della ricerca

Questa sezione descrive i risultati di una serie di test eseguiti su diverse configurazioni di cluster e indici. Sono stati eseguiti due tipi di test, come segue:

- **Il test di *inserimento e query***. Questo test è iniziato con un indice vuoto, che è stato compilato durante l'avanzamento del test mediante l'esecuzione di operazioni di inserimento in blocco, dove ogni operazione ha aggiunto 1000 documenti. Contemporaneamente, a intervalli di 5 secondi è stato ripetuto un certo numero di query progettate per la ricerca di documenti aggiunti durante i 15 minuti precedenti e per la generazione di aggregazioni. L'esecuzione di questo test è stata in genere consentita per 24 ore, per riprodurre gli effetti di un carico di lavoro impegnativo che comprende l'inserimento di dati su larga scala con query in tempo quasi reale.

- **Il test *solo delle query***. Questo test è simile al test di *inserimento e query* a eccezione del fatto che la parte dell'inserimento viene omessa e che l'indice in ogni nodo viene prepopolato con 100 milioni di documenti. Viene eseguito un set di query modificato. L'elemento temporale che limita i documenti a quelli aggiunti negli ultimi 15 minuti è stato rimosso perché i dati ora sono statici. I test sono stati eseguiti per 90 minuti. È necessario meno tempo per stabilire un modello di prestazioni perché la quantità di dati è fissa.

---

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
 
Le query seguenti sono state eseguite come batch a ogni iterazione dei test. I nomi in corsivo vengono usati per fare riferimento alle query nel resto di questo documento. Si noti che il criterio temporale (documenti aggiunti negli ultimi 15 minuti) è stato omesso dai test *solo delle query*:

- Quanti documenti con ogni valore *Rating* sono stati immessi negli ultimi 15 minuti (*Count By Rating*)?

- Quanti documenti sono stati aggiunti in ogni intervallo di 5 minuti negli ultimi 15 minuti (*Count Over Time*)?

- Quanti documenti con ogni valore *Rating* sono stati aggiunti per ogni paese negli ultimi 15 minuti (*Hits By Country*)?

- Quali sono le 15 organizzazioni che ricorrono con maggiore frequenza nei documenti aggiunti negli ultimi 15 minuti (*Top 15 Organizations*)?

- Quante organizzazioni diverse ricorrono nei documenti aggiunti negli ultimi 15 minuti (*Unique Count Organizations*)?

- Quanti documenti sono stati aggiunti negli ultimi 15 minuti (*Total Hits Count*)?

- Quanti valori *SourceIp* diversi ricorrono nei documenti aggiunti negli ultimi 15 minuti (*Unique IP Count*)?


La definizione dell'indice e i dettagli delle query sono descritti nell'[appendice](#appendix-the-query-and-aggregation-performance-test).

I test sono stati progettati per comprendere gli effetti delle variabili seguenti:

- **Tipo di disco**. I test sono stati eseguiti su un cluster di 6 nodi con macchine virtuali D4 che usano l'Archiviazione Standard (HDD) e ripetuti su un cluster di 6 nodi con macchine virtuali DS4 che usano l'Archiviazione Premium (unità SSD).

- **Dimensioni delle macchine virtuali: aumento delle prestazioni**. I test sono stati eseguiti su un cluster di 6 nodi che comprende macchine virtuali DS3 (designato come cluster *small*), ripetuti su un cluster di macchine virtuali DS4 (il cluster *medium*) e ripetuti di nuovo su un cluster di macchine virtuali DS14 (il cluster *large*). La tabella seguente riepiloga le caratteristiche principali di ogni SKU di VM:

 Cluster | SKU di VM | Numero di core | Numero di dischi dati | RAM (GB) |
---------|---------------|-----------------|----------------------|----------|
 Small | Standard DS3 | 4 | 8 | 14 |
 Medium | DS4 standard | 8 | 16 | 28 |
 Large | Standard DS14 | 16 | 32 | 112 |

- **Dimensioni dei cluster: scalabilità orizzontale**. I test sono stati eseguiti su cluster di VM DS14 che comprende 1, 3 e 6 nodi.

- **Numero di repliche di indice**. I test sono stati eseguiti usando indici configurati con 1 e 2 repliche.

- **Valori dei documenti**. Inizialmente sono stati eseguiti i test con l'impostazione dell'indice *doc\_values* impostata su *true* (valore predefinito). Test selezionati sono stati ripetuti con *doc\_values* impostata su *false*.

- **Memorizzazione nella cache**. I test sono stati eseguiti con la cache delle richieste delle partizioni abilitata nell'indice.

- **Numero di partizioni**. I test sono stati ripetuti usando un numero variabile di partizioni per stabilire se le query sono state eseguite in modo più efficiente negli indici contenenti meno partizioni più grandi o più partizioni più piccole.


## Risultati delle prestazioni: tipo di disco

Le prestazioni del disco sono state valutate eseguendo il test di *inserimento e query* sul cluster di 6 nodi con macchine virtuali D4 (HDD) e sul cluster di 6 nodi con macchine virtuali DS4 (unità SSD). La configurazione di Elasticsearch in entrambi i cluster era la stessa. I dati sono stati ripartiti su 16 dischi in ogni nodo e 14 GB di RAM di ogni nodo sono stati allocati alla JVM che esegue Elasticsearch. La memoria rimanente, anche in questo caso 14 GB, è rimasta disponibile per l'uso del sistema operativo. Ogni test è stato eseguito per 24 ore. Questo intervallo è stato scelto per consentire che gli effetti del volume di dati crescente diventassero evidenti e per consentire al sistema di stabilizzarsi. La tabella seguente riepiloga i risultati, evidenziando i tempi di risposta delle diverse operazioni previste dal test.

 Cluster | Operazione/query | Tempo medio di risposta (ms) |
---------|----------------------------|----------------------------|
 D4 | Ingestion | 978 |
 | Count By Rating | 103 |
 | Count Over Time | 134 |
 | Hits By Country | 199 |
 | Top 15 Organizations | 137 |
 | Unique Count Organizations | 139 |
 | Unique IP Count | 510 |
 | Total Hits Counts | 89
 DS4 | Inserimento | 511 |
 | Count By Rating | 187 |
 | Count Over Time | 411 |
 | Hits By Country | 402 |
 | Top 15 Organizations | 307 |
 | Unique Count Organizations | 320 |
 | Unique IP Count | 841 |
 | Total Hits Counts | 236 |

A prima vista, sembra che il cluster DS4 abbia eseguito query con prestazioni inferiori rispetto al cluster D4, a volte raddoppiando (o peggiorando) il tempo di risposta. Questo risultato non è però esauriente. La tabella seguente illustra il numero di operazioni di inserimento eseguite da ogni cluster. Tenere presente che ogni operazione carica 1000 documenti:

 Cluster | N. di operazioni di inserimento |
---------|-------------------------|
 D4 | 264769 |
 DS4 | 503157 |

Durante il test il cluster DS4 è riuscito a caricare quasi due volte la quantità di dati rispetto al cluster D4. Quando si analizzano i tempi di risposta per ogni operazione, è tuttavia necessario considerare il numero di documenti che ogni query ha dovuto analizzare e quanti documenti sono stati restituiti. Queste sono cifre dinamiche, perché il volume di documenti nell'indice cresce continuamente. Non si può dividere semplicemente 503137 per 264769, ovvero il numero di operazioni di inserimento eseguite da ogni cluster, e quindi moltiplicare il risultato per il tempo di risposta medio per ogni query eseguita dal cluster D4 per fornire informazioni comparative, perché in questo modo si ignora la quantità di operazioni di I/O eseguite contemporaneamente dall'operazione di inserimento. Al contrario, si dovrebbe misurare la quantità fisica dei dati scritti sul disco e letti durante l'esecuzione del test. Il piano di test JMeter consente di acquisire queste informazioni per ogni nodo. I risultati riepilogati sono:

 Cluster | Media dei byte scritti o letti da ogni operazione |
---------|----------------------------------------------|
 D4 | 13471557 |
 DS4 | 24643470 |

Questi dati mostrano che il cluster DS4 è riuscito a sostenere una frequenza di I/O circa 1,8 volte quella del cluster D4. Ciò detto, a parte la natura dei dischi, tutte le altre risorse sono uguali, quindi la differenza deve essere dovuta all'uso di unità SSD invece che HDD.

Per giustificare questa conclusione, i grafici seguenti illustrano come sono state eseguite le operazioni di I/O nel corso del tempo per ogni cluster:

![](./media/guidance-elasticsearch/query-performance2.png)

<!-- -->

***Figura 2. Attività del disco per i cluster D4 e DS4***

Il grafico per il cluster D4 mostra una variazione significativa, soprattutto durante la prima metà del test. Ciò è probabilmente dovuto alla limitazione per ridurre la velocità di I/O. Nelle fasi iniziali del test le query sono state eseguite rapidamente, data la quantità ridotta di dati da analizzare. È quindi probabile che i dischi nel cluster D4 funzionino quasi alla massima capacità di IOPS, anche se ogni operazione di I/O potrebbe non restituire molti dati. Il cluster DS4 può supportare una maggiore velocità di IOPS e non risente dello stesso livello di limitazione. Le velocità di I/O sono più regolari. Per supportare questa teoria, la coppia di grafici successiva mostra come la CPU sia stata bloccata dall'I/O del disco nel corso del tempo. I tempi di attesa del disco visualizzati nei grafici sono la percentuale del tempo impiegato dalla CPU in attesa di I/O:

![](./media/guidance-elasticsearch/query-performance3.png)

***Figura 3. Tempi di attesa di I/O del disco della CPU per i cluster D4 e DS4***

È importante comprendere che ci sono due motivi principali per cui le operazioni di I/O bloccano la CPU:

- È possibile che il sottosistema di I/O stia leggendo o scrivendo dati da e sul disco.

- Il sottosistema di I/O potrebbe essere limitato dall'ambiente host. I dischi di Azure implementati usando dischi HDD hanno una velocità effettiva massima di 500 operazioni di I/O al secondo e le unità SSD hanno una velocità effettiva massima di 5000 operazioni di I/O al secondo.


Per il cluster D4 la quantità di tempo trascorso in attesa di I/O durante la prima metà del test è strettamente correlata inversamente con il grafico che mostra le frequenze di I/O. I periodi di bassa velocità di I/O corrispondono ai significativi periodi di tempo durante i quali la CPU rimane bloccata. Questo indica che la velocità di I/O è limitata. La situazione cambia man mano che vengono aggiunti dati al cluster e nella seconda metà del test i picchi nei tempi di attesa di I/O corrispondono a picchi nella velocità effettiva di I/O. A questo punto, la CPU è bloccata durante l'esecuzione di operazioni di I/O effettive. Anche in questo caso, con il cluster DS4 il tempo di attesa di I/O è molto più uniforme e ogni picco corrisponde a un picco equivalente nelle prestazioni di I/O invece di un calo. Ciò implica che la limitazione è minima o del tutto assente.

Occorre considerare anche un altro fattore. Durante il test il cluster D4 ha generato 10584 errori di inserimento e 21 errori di query. Il test sul cluster DS4 non ha prodotto errori.

## Risultati delle prestazioni: aumento delle prestazioni

I test per aumentare le prestazioni sono stati eseguiti su un cluster di 6 nodi con VM DS3, DS4 e DS14. Queste SKU sono state selezionate perché una VM DS4 fornisce il doppio di core CPU e di memoria rispetto a DS3 e un computer DS14 raddoppia ancora le risorse della CPU fornendo al contempo il quadruplo di memoria. La tabella seguente confronta gli aspetti chiave di ogni SKU:

 SKU | N. di core CPU | Memoria (GB) | Numero massimo di operazioni di I/O al secondo del disco | Larghezza di banda massima (MB/s)|
------|-------------|-------------|---------------|--------------|
 DS3 | 4 | 14 | 12\.800| 128 |
 DS4 | 8 | 28 | 25\.600| 256 |
 DS14 | 16 | 112 | 50\.000| 512 |

La tabella seguente riepiloga i risultati dell'esecuzione dei test sui cluster di piccole (DS3), medie (DS4) e grandi dimensioni (DS14). Ogni macchina virtuale usava unità SSD per contenere i dati. Ogni test è stato eseguito per 24 ore:

> **Nota**: la tabella indica il numero di richieste riuscite per ogni tipo di query. Gli errori non sono inclusi. Il numero di tentativi di richiesta per ogni tipo di query è più o meno lo stesso nell'arco di un'esecuzione del test. Il piano di test JMeter infatti esegue una sola occorrenza di ogni query (Count By Rating, Count Over Time, Hits By Country, Top 15 Organizations, Unique Count Organizations, Unique IP Count e Total Hits Counts) insieme in una sola unità nota come *transazione di test*. Questa transazione è indipendente dall'attività che effettua l'operazione di inserimento che viene eseguita da un thread separato. Ogni iterazione del piano di test esegue una singola transazione di test. Il numero di transazioni di test completate è quindi una misura del tempo di risposta della query più lenta in ogni transazione.

| Cluster | Operazione/query | Numero di richieste | Tempo medio di risposta (ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Piccolo (DS3) | Inserimento | 207284 | 3328 |
| | Count By Rating | 18444 | 268 |
| | Count Over Time | 18444 | 340 |
| | Hits By Country | 18445 | 404 |
| | Top 15 Organizations | 18439 | 323 |
| | Unique Count Organizations | 18437 | 338 |
| | Unique IP Count | 18442 | 468 |
| | Total Hits Counts | 18428 | 294   
|||||
| Medium (DS4) | Ingestion | 503157 | 511 |
| | Count By Rating | 6958 | 187 |
| | Count Over Time | 6958 | 411 |
| | Hits By Country | 6958 | 402 |
| | Top 15 Organizations | 6958 | 307 |
| | Unique Count Organizations | 6956 | 320 |
| | Unique IP Count | 6955 | 841 |
| | Total Hits Counts | 6958 | 236 |
|||||
| Large (DS14) | Ingestion | 502714 | 511 |
| | Count By Rating | 7041 | 201 |
| | Count Over Time | 7040 | 298 |
| | Hits By Country | 7039 | 363 |
| | Top 15 Organizations | 7038 | 244 |
| | Unique Count Organizations | 7037 | 283 |
| | Unique IP Count | 7037 | 681 |
| | Total Hits Counts | 7038 | 200 |

Queste cifre indicano che, per il test, le prestazioni dei cluster DS4 e DS14 sono state ragionevolmente simili. Il confronto dei tempi di risposta per le operazioni di query è inizialmente favorevole al cluster DS3 e il numero di operazioni di query eseguite è di gran luna superiore ai valori dei cluster DS4 e DS14. È tuttavia consigliabile prestare anche molta attenzione alla frequenza di inserimento e al conseguente numero di documenti in cui eseguire la ricerca. Nel cluster DS3 l'inserimento è molto più limitato e alla fine del test il database conteneva solo il 40% circa dei documenti letti da ognuno degli altri due cluster. Questa limitazione può essere dovuta alle risorse di elaborazione, alla rete e alla larghezza di banda dei dischi disponibili per una VM DS3 rispetto a una VM DS4 o DS14. Posto che una VM DS4 ha il doppio delle risorse disponibili di una VM DS3 e che una VM DS14 ha il doppio (il quadruplo per la memoria) delle risorse di una VM DS4, resta da chiedersi: perché la differenza delle frequenze di inserimento tra i cluster DS4 e DS14 è considerevolmente minore di quella esistente tra i cluster DS3 e DS4? La differenza può essere dovuta all'utilizzo della rete ai limiti di larghezza di banda delle VM di Azure. Il grafico seguente illustra questi dati per i tre cluster:

![](./media/guidance-elasticsearch/query-performance4.png)

***Figura 4. Utilizzo della rete per i cluster DS3, DS4 e DS14 che eseguono il test di *inserimento e query****

<!-- -->

I limiti della larghezza di banda di rete disponibile con le VM di Azure non sono pubblicati e possono variare, ma il fatto che l'attività della rete si sia stabilizzata su una media di circa 2,75 Gbps per entrambi i test DS4 e DS14 suggerisce che tale limite sia stato raggiunto e sia diventato il fattore primario nella limitazione della velocità effettiva. Nel caso del cluster DS3, l'attività della rete era considerevolmente più bassa, quindi le minori prestazioni sono più probabilmente dovute ai vincoli di disponibilità di altre risorse.

Per isolare gli effetti delle operazioni di inserimento e illustrare come le prestazioni delle query variano con l'aumento delle prestazioni, è stato eseguito un set di test solo delle query usando gli stessi nodi. La tabella seguente riepiloga i risultati ottenuti in ogni cluster:

> [AZURE.NOTE] È consigliabile non confrontare le prestazioni e il numero di richieste eseguite dalle query nel test *solo delle query* con quelle eseguite dal test di *inserimento e query*. Infatti le query sono state modificate e il volume dei documenti coinvolti è diverso.

| Cluster | Operazione/query | Numero di richieste | Tempo medio di risposta (ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Piccolo (DS3) | Count By Rating | 464 | 11758 |
| | Count Over Time | 464 | 14699 |
| | Hits By Country | 463 | 14075 |
| | Top 15 Organizations | 464 | 11856 |
| | Unique Count Organizations | 462 | 12314 |
| | Unique IP Count | 461 | 19898 |
| | Total Hits Counts | 462 | 8882  
|||||
| Medium (DS4) | Count By Rating | 1045 | 4489 |
| | Count Over Time | 1045 | 7292 |
| | Hits By Country | 1053 | 7564 |
| | Top 15 Organizations | 1055 | 5066 |
| | Unique Count Organizations | 1051 | 5231 |
| | Unique IP Count | 1051 | 9228 |
| | Total Hits Counts | 1051 | 2180 |
|||||
| Large (DS14) | Count By Rating | 1842 | 1927 |
| | Count Over Time | 1839 | 4483 |
| | Hits By Country | 1838 | 4761 |
| | Top 15 Organizations | 1842 | 2117 |
| | Unique Count Organizations | 1837 | 2393 |
| | Unique IP Count | 1837 | 7159 |
| | Total Hits Counts | 1837 | 642 |

In questo caso, le tendenze nei tempi medi di risposta nei diversi cluster sono più chiare. L'utilizzo della rete è molto al di sotto dei 2,75 Gbps richiesti in precedenza dai cluster DS4 e DS14 (che probabilmente saturavano la rete nei test di inserimento e query) e degli 1,5 Gbps per il cluster DS3. È infatti più vicino a 200 Mbps in tutti i casi illustrati dai grafici seguenti:

![](./media/guidance-elasticsearch/query-performance5.png)

***Figura 5. Utilizzo della rete per i cluster DS3, DS4 e DS14 che eseguono il test *solo delle query****

Il fattore limitante nei cluster DS3 e DS4 è ora l'utilizzo della CPU, che è vicino al 100% per gran parte del tempo. Nel cluster DS14 l'utilizzo della CPU è in media appena al di sopra dell'80%, che è ancora piuttosto elevato, ma mette chiaramente in evidenza i vantaggi della disponibilità di più core CPU. La figura seguente illustra i modelli di utilizzo della CPU per i cluster DS3, DS4 e DS14.

![](./media/guidance-elasticsearch/query-performance6.png)

***Figura 6. Utilizzo della CPU per i cluster DS3 e DS14 che eseguono il test *solo delle query****

## Risultati delle prestazioni: scalabilità orizzontale

Per illustrare la scalabilità orizzontale del sistema con l'aumento del numero di nodi, i test sono stati eseguiti usando cluster DS14 che comprendono 1, 3 e 6 nodi. Questa volta è stato eseguito solo il test *solo delle query*, usando 100 milioni di documenti ed eseguendolo per 90 minuti:

> [AZURE.NOTE] Per informazioni dettagliate su come la scalabilità orizzontale possa avere effetto sul comportamento delle operazioni di inserimento dei dati, vedere il documento relativo all'[ottimizzazione delle prestazioni di inserimento dei dati con Elasticsearch in Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

| Cluster | Operazione/query | Numero di richieste | Tempo medio di risposta (ms) |
|---------|----------------------------|--------------------|----------------------------|
| 1 nodo | Count By Rating | 288 | 6216 |
| | Count Over Time | 288 | 28933 |
| | Hits By Country | 288 | 29455 |
| | Top 15 Organizations | 288 | 9058 |
| | Unique Count Organizations | 287 | 19916 |
| | Unique IP Count | 284 | 54203 |
| | Total Hits Counts | 287 | 3333 |
|||||
| 3 nodi | Count By Rating | 1194 | 3427 |
| | Count Over Time | 1194 | 5381 |
| | Hits By Country | 1191 | 6840 |
| | Top 15 Organizations | 1196 | 3819 |
| | Unique Count Organizations | 1190 | 2938 |
| | Unique IP Count | 1189 | 12516 |
| | Total Hits Counts | 1191 | 1272 |
|||||
| 6 nodi | Count By Rating | 1842 | 1927 |
| | Count Over Time | 1839 | 4483 |
| | Hits By Country | 1838 | 4761 |
| | Top 15 Organizations | 1842 | 2117 |
| | Unique Count Organizations | 1837 | 2393 |
| | Unique IP Count | 1837 | 7159 |
| | Total Hits Counts | 1837 | 642 |

Il numero di nodi crea una considerevole differenza nelle prestazioni query del cluster, anche se in modo non lineare. Il cluster a 3 nodi completa circa 4 volte le query del cluster a nodo singolo, invece il cluster a 6 nodi ne gestisce 6 volte tante. Per illustrare questa mancanza di linearità, i grafici seguenti mostrano come la CPU è stata utilizzata dai tre cluster:

![](./media/guidance-elasticsearch/query-performance7.png)

***Figura 7. Utilizzo della CPU per i cluster a 1, 3 e 6 nodi che eseguono il test *solo delle query****

I cluster a nodo singolo e a 3 nodi dipendono dalla CPU, mentre, anche se l'utilizzo della CPU è elevato nel cluster a 6 nodi, è disponibile un capacità di elaborazione di riserva. In questo caso, è probabile che altri fattori limitino la velocità effettiva. È possibile verificarlo eseguendo il test con 9 e 12 nodi, che probabilmente mostrerà un'ulteriore capacità di elaborazione di riserva.

I dati della tabella precedente mostrano anche come i tempi medi di risposta per le query possano variare. Questo è l'elemento che offre il maggior numero di informazioni quando si testa come viene ridimensionato un sistema per specifici tipi di query. Alcune ricerche sono evidentemente molto più efficienti se distribuite su più nodi di altre. La causa può essere il rapporto tra il numero di nodi e il numero di documenti nel cluster in aumento. Ogni cluster conteneva 100 milioni di documenti. Quando si eseguono ricerche che comportano l'aggregazione di dati, Elasticsearch elaborerà e memorizzerà nel buffer i dati recuperati durante il processo di aggregazione nella memoria in ogni nodo. Più sono i nodi, meno sono i dati da recuperare, memorizzare nel buffer ed elaborare in ogni nodo.

## Risultati delle prestazioni: numero di repliche

I test di *inserimento e query* sono stati eseguiti in un indice con una singola replica. I test sono stati ripetuti nei cluster DS4 e DS14 a 6 nodi usando un indice configurato con due repliche. Tutti i test sono stati eseguiti per 24 ore. La tabella seguente mette a confronto i risultati per una e due repliche:

| Cluster | Operazione/query | Tempo medio di risposta (ms) - 1 replica | Tempo medio di risposta (ms) - 2 repliche | Differenza % nel tempo di risposta |
|---------|----------------------------|----------------------------------------|-----------------------------------------|-------------------------------|
| DS4 | Inserimento | 511 | 655 | +28% |
| | Count By Rating | 187 | 168 | -10% |
| | Count Over Time | 411 | 309 | -25% |
| | Hits By Country | 402 | 562 | +40% |
| | Top 15 Organizations | 307 | 366 | +19% |
| | Unique Count Organizations | 320 | 378 | +18% |
| | Unique IP Count | 841 | 987 | +17% |
| | Total Hits Counts | 236 | 236 | +0% |
||||||
| DS14 | Ingestion | 511 | 618 | +21% |
| | Count By Rating | 201 | 275 | +37% |
| | Count Over Time | 298 | 466 | +56% |
| | Hits By Country | 363 | 529 | +46% |
| | Top 15 Organizations | 244 | 407 | +67% |
| | Unique Count Organizations | 283 | 403 | +42% |
| | Unique IP Count | 681 | 823 | +21% |
| | Total Hits Counts | 200 | 221 | +11% |

La frequenza di inserimento è diminuita con l'aumentare del numero di repliche, come era prevedibile perché Elasticsearch sta scrivendo più copie di ogni documento, generando un ulteriore I/O su disco. Ciò è osservabile nei grafici per il cluster DS14 per gli indici con 1 e 2 repliche, come illustrato nella figura seguente. Nel caso dell'indice con 1 replica la frequenza di I/O media era di 16896573 byte/secondo. Per l'indice con 2 repliche, la frequenza di I/O media era di 33986843 byte/secondo, ovvero poco più del doppio.

![](./media/guidance-elasticsearch/query-performance8.png)

***Figura 8. Frequenze di I/O del disco per nodi con 1 e 2 repliche che eseguono il test di *inserimento e query****

| Cluster | Query | Tempo medio di risposta (ms) - 1 replica | Tempo medio di risposta (ms) - 2 repliche |
|---------|----------------------------|----------------------------------------|-----------------------------------------|
| DS4 | Count By Rating | 4489 | 4079 |
| | Count Over Time | 7292 | 6697 |
| | Hits By Country | 7564 | 7173 |
| | Top 15 Organizations | 5066 | 4650 |
| | Unique Count Organizations | 5231 | 4691 |
| | Unique IP Count | 9228 | 8752 |
| | Total Hits Counts | 2180 | 1909 |
|||||
| DS14 | Count By Rating | 1927 | 2330 |
| | Count Over Time | 4483 | 4381 |
| | Hits By Country | 4761 | 5341 |
| | Top 15 Organizations | 2117 | 2560 |
| | Unique Count Organizations | 2393 | 2546 |
| | Unique IP Count | 7159 | 7048 |
| | Total Hits Counts | 642 | 708 |

Questi risultati mostrano un miglioramento nel tempo medio di risposta per il cluster DS4, ma un aumento per il cluster DS14. Per interpretare questi risultati, è consigliabile tenere in considerazione anche il numero di query eseguite da ogni test:

| Cluster | Query | Numero di query eseguite - 1 replica | Numero di query eseguite - 2 repliche |
|---------|----------------------------|------------------------------|-------------------------------|
| DS4 | Count By Rating | 1054 | 1141 |
| | Count Over Time | 1054 | 1139 |
| | Hits By Country | 1053 | 1138 |
| | Top 15 Organizations | 1055 | 1141 |
| | Unique Count Organizations | 1051 | 1136 |
| | Unique IP Count | 1051 | 1135 |
| | Total Hits Counts | 1051 | 1136 |
|||||
| DS14 | Count By Rating | 1842 | 1718 |
| | Count Over Time | 1839 | 1716 |
| | Hits By Country | 1838 | 1714 |
| | Top 15 Organizations | 1842 | 1718 |
| | Unique Count Organizations | 1837 | 1712 |
| | Unique IP Count | 1837 | 1712 |
| | Total Hits Counts | 1837 | 1712 |

Questi dati mostrano che il numero di query eseguite dal cluster DS4 è aumentato parallelamente alla riduzione del tempo medio di risposta, ma anche in questo caso vale il contrario per il cluster DS14. Un fattore importante è che l'utilizzo della CPU del cluster DS4 nei test con 1 replica e 2 repliche non è stato distribuito uniformemente. Alcuni nodi hanno mostrato un utilizzo vicino al 100%, mentre altri avevano una capacità di elaborazione di riserva. Il miglioramento delle prestazioni è molto probabilmente dovuto alla maggiore capacità di distribuire l'elaborazione tra i nodi del cluster. L'immagine seguente mostra la variazione nell'elaborazione della CPU tra le VM meno usate e più usate (nodi 4 e 3):

![](./media/guidance-elasticsearch/query-performance9.png)

***Figura 9. Utilizzo della CPU per i nodi meno usati e più usati nel cluster DS4 che esegue il test *solo delle query****

Ciò non è accaduto per il cluster DS14. L'utilizzo della CPU per entrambi i test è stato inferiore in tutti i nodi e la disponibilità di una seconda replica, più che un vantaggio, si è rivelata un sovraccarico:

![](./media/guidance-elasticsearch/query-performance10.png)

***Figura 10. Utilizzo della CPU per i nodi meno usati e più usati nel cluster DS14 che esegue il test *solo delle query****

Questi risultati indicano la necessità di effettuare un attento benchmark del sistema quando si decide se usare più repliche. È consigliabile avere sempre almeno una replica di ogni indice (a meno che non si voglia correre il rischio di perdere i dati se si verificano problemi in un nodo), ma un numero maggiore di repliche può diventare un onere per il sistema a fronte di un vantaggio minimo, a seconda dei carichi di lavoro e delle risorse hardware disponibili per il cluster.

## Risultati delle prestazioni: valori dei documenti

I test di *inserimento e query* sono stati eseguiti con i valori dei documenti (doc\_values) abilitati, causando l'archiviazione su disco dei dati usati per i campi di ordinamento da parte di Elasticsearch. I test sono stati ripetuti con valori dei documenti disabilitati e Elasticsearch ha costruito dinamicamente i dati dei campi e li ha memorizzato nella cache. Tutti i test sono stati eseguiti per 24 ore. La tabella seguente confronta i tempi di risposta per i test eseguiti in cluster di 6 nodi compilati usando VM D4, DS4 e DS14. Il cluster D4 usa normali dischi rigidi, mentre i cluster DS4 e DS14 usano unità SSD.

| Cluster | Operazione/query | Tempo medio di risposta (ms) - Valori documenti abilitati | Tempo medio di risposta (ms) - Valori documenti disabilitati | Differenza % nel tempo di risposta |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| D4 | Ingestion | 978 | 835 | -15% |
| | Count By Rating | 103 | 132 | +28% |
| | Count Over Time | 134 | 189 | +41% |
| | Hits By Country | 199 | 259 | +30% |
| | Top 15 Organizations | 137 | 184 | +34% |
| | Unique Count Organizations | 139 | 197 | +42% |
| | Unique IP Count | 510 | 604 | +18% |
| | Total Hits Counts | 89 | 134 | +51% |
||||||
| DS4 | Inserimento | 511 | 581 | +14% |
| | Count By Rating | 187 | 190 | +2% |
| | Count Over Time | 411 | 409 | -0,5% |
| | Hits By Country | 402 | 414 | +3% |
| | Top 15 Organizations | 307 | 284 | -7% |
| | Unique Count Organizations | 320 | 313 | -2% |
| | Unique IP Count | 841 | 955 | +14% |
| | Total Hits Counts | 236 | 281 | +19% |
||||||
| DS14 | Ingestion | 511 | 571 | +12% |
| | Count By Rating | 201 | 232 | +15% |
| | Count Over Time | 298 | 341 | +14% |
| | Hits By Country | 363 | 457 | +26% |
| | Top 15 Organizations | 244 | 338 | +39% |
| | Unique Count Organizations | 283 | 350 | +24% |
| | Unique IP Count | 681 | 909 | +33% |
| | Total Hits Counts | 200 | 245 | +23% |

La tabella successiva confronta il numero di operazioni di inserimento eseguite dai test:

| Cluster | N. di operazioni di inserimento - Valori documenti abilitati | N. di operazioni di inserimento - Valori documenti disabilitati | Differenza % nel n. di operazioni di inserimento |
|---------|----------------------------------------------|-----------------------------------------------|-----------------------------------------|
| D4 | 264769 | 408690 | +54% |
| DS4 | 503137 | 578237 | +15% |
| DS14 | 502714 | 586472 | +17% |

Il miglioramento nelle frequenze di inserimento avviene con i valori dei documenti abilitati perché vengono scritti meno dati su disco mentre i documenti vengono inseriti. Il miglioramento delle prestazioni è particolarmente evidente quando la VM D4 usa dischi HDD per archiviare i dati. In questo caso, anche il tempo di risposta per le operazioni di inserimento è diminuito del 15% (vedere la prima tabella di questa sezione). La causa potrebbe essere la minore pressione sui dischi HDD che probabilmente durante l'esecuzione erano vicini ai limiti per le operazioni di I/O al secondo nel test con i valori dei documenti abilitati. Per altre informazioni, vedere il test Tipo di disco. Il grafico seguente confronta le prestazioni di I/O delle VM D4 con i valori dei documenti abilitati (valori contenuti nel disco) e con i valori dei documenti disabilitati (valori contenuti nella memoria):

![](./media/guidance-elasticsearch/query-performance11.png)

***Figura 11. Attività del disco per il cluster D4 con i valori dei documenti abilitati e disabilitati***

Al contrario, i valori di inserimento per le VM che usano unità SSD mostrano un piccolo aumento nel numero di documenti, ma anche un aumento nel tempo di risposta delle operazioni di inserimento. A parte una o due piccole eccezioni, i tempi di risposta delle query sono ulteriormente peggiorati. Poiché è meno probabile che le unità SSD durante l'esecuzione siano vicine ai limiti per le operazioni di I/O al secondo con i valori dei documenti abilitati, è più probabile che le prestazioni siano state modificate dalla maggiore attività di elaborazione e all'overhead dovuto alla gestione dell'heap JVM, come risulta evidente confrontando l'utilizzo della CPU con i valori dei documenti abilitati e disabilitati. Il grafico successivo evidenzia questi dati per il cluster DS4, dove la maggior parte dell'utilizzo della CPU passa dalla fascia del 30%-40% con i valori dei documenti abilitati alla fascia del 40%-50% con i valori dei documenti disabilitati (il cluster DS14 ha mostrato una tendenza simile):

![](./media/guidance-elasticsearch/query-performance12.png)

***Figura 12. Utilizzo della CPU per il cluster DS4 con i valori dei documenti abilitati e disabilitati***

Per distinguere gli effetti dei valori dei documenti sulle prestazioni delle query dall'inserimento di dati, sono state eseguite coppie di test solo delle query per i cluster DS4 e DS14 con i valori dei documenti abilitati e disabilitati. La tabella seguente riepiloga i risultati di questi test:

| Cluster | Operazione/query | Tempo medio di risposta (ms) - Valori documenti abilitati | Tempo medio di risposta (ms) - Valori documenti disabilitati | Differenza % nel tempo di risposta |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4 | Count By Rating | 4489 | 3736 | -16% |
| | Count Over Time | 7293 | 5459 | -25% |
| | Hits By Country | 7564 | 5930 | -22% |
| | Top 15 Organizations | 5066 | 3874 | -14% |
| | Unique Count Organizations | 5231 | 4483 | -2% |
| | Unique IP Count | 9228 | 9474 | +3% |
| | Total Hits Counts | 2180 | 1218 | -44% |
||||||
| DS14 | Count By Rating | 1927 | 2144 | +11% |
| | Count Over Time | 4483 | 4337 | -3% |
| | Hits By Country | 4761 | 4840 | +2% |
| | Top 15 Organizations | 2117 | 2302 | +9% |
| | Unique Count Organizations | 2393 | 2497 | +4% |
| | Unique IP Count | 7159 | 7639 | +7% |
| | Total Hits Counts | 642 | 633 | -1% |

Si ricordi che a partire da Elasticsearch versione 2.0 i valori dei documenti sono abilitati per impostazione predefinita. Nei test relativi al cluster DS4 la disabilitazione dei valori dei documenti ha in generale un effetto positivo, mentre per il cluster DS14 si verifica di solito il contrario. I due casi in cui le prestazioni sono migliori con i valori dei documenti disabilitati sono del tutto marginali.

Per il cluster DS4, l'utilizzo della CPU in entrambi i casi è stato vicino al 100% per tutta la durata di entrambi i test. Ciò indica che il cluster dipendeva dalla CPU. Il numero di query elaborate è tuttavia sceso da 7369 a 5894 (20%). Vedere la tabella seguente. Si ricordi che, se i valori dei documenti sono disabilitati, Elasticsearch genererà in modo dinamico dati dei campi in memoria, utilizzando la potenza della CPU. Poiché questa configurazione ha ridotto la frequenza di I/O del disco, ma ha aumentato lo stress per le CPU già in esecuzione vicino al limite massimo delle funzionalità, in questo caso le query sono più veloci con i valori dei documenti disabilitati, ma sono in numero minore.

Nei test DS14 con e senza i valori dei documenti, l'attività della CPU è stata elevata, ma non pari al 100%. Il numero di query eseguite è stato leggermente più alto (circa il 4%) nei test con i valori dei documenti abilitati:

| Cluster | Query | Numero di query eseguite - Valori dei documenti abilitati | Numero di query eseguite - Valori dei documenti disabilitati |
|---------|----------------------------|---------------------------------------|----------------------------------------|
| DS4 | Count By Rating | 1054 | 845 |
| | Count Over Time | 1054 | 844 |
| | Hits By Country | 1053 | 842 |
| | Top 15 Organizations | 1055 | 846 |
| | Unique Count Organizations | 1051 | 839 |
| | Unique IP Count | 1051 | 839 |
| | Total Hits Counts | 1051 | 839  
||||| |
| DS14 | Count By Rating | 1772 | 1842 |
| | Count Over Time | 1772 | 1839 |
| | Hits By Country | 1770 | 1838 |
| | Top 15 Organizations | 1773 | 1842 |
| | Unique Count Organizations | 1769 | 1837 |
| | Unique IP Count | 1768 | 1837 |
| | Total Hits Counts | 1769 | 1837 |

## Risultati delle prestazioni: cache delle richieste delle partizioni

Per dimostrare come l'inserimento dei dati dell'indice nella memoria di ogni nodo possa compromettere le prestazioni, il test *di query e inserimento* è stato condotto in un cluster a 6 nodi DS4 e in uno DS14 con la memorizzazione nella cache dell'indice abilitata. Per altre informazioni, vedere la sezione [Uso della cache delle richieste delle partizioni](#using-the-shard-request-cache). I risultati sono stati confrontati con quelli generati dai test precedenti usando lo stesso indice, ma con la memorizzazione nella cache dell'indice disabilitata. La tabella seguente riepiloga i risultati. Si noti che i dati sono stati limitati ai primi 90 minuti del test. A questo punto la tendenza emergente dal confronto era evidente e, anche continuando con il test, probabilmente non si sarebbero ottenute informazioni aggiuntive:

| Cluster | Operazione/query | Tempo medio di risposta (ms) - Cache indice disabilitata | Tempo medio di risposta (ms) - Cache indice abilitata | Differenza % nel tempo di risposta |
|---------|----------------------------|---------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4 | Inserimento | 504 | 3260 | +547% |
| | Count By Rating | 218 | 273 | +25% |
| | Count Over Time | 450 | 314 | -30% |
| | Hits By Country | 447 | 397 | -11% |
| | Top 15 Organizations | 342 | 317 | -7% |
| | Unique Count Organizations | 370 | 324 | -12%% |
| | Unique IP Count | 760 | 355 | -53% |
| | Total Hits Counts | 258 | 291 | +12% |
||||||
| DS14 | Inserimento | 503 | 3365 | +569% |
| | Count By Rating | 234 | 262 | +12% |
| | Count Over Time | 357 | 298 | -17% |
| | Hits By Country | 416 | 383 | -8% |
| | Top 15 Organizations | 272 | 324 | -7% |
| | Unique Count Organizations | 330 | 321 | -3% |
| | Unique IP Count | 674 | 352 | -48% |
| | Total Hits Counts | 227 | 292 | +29% |

Questi dati mostrano due aspetti interessanti:

-  Le frequenze di inserimento dei dati si sono considerevolmente abbassate abilitando la memorizzazione nella cache dell'indice.

-  La memorizzazione nella cache dell'indice non migliora necessariamente il tempo di risposta di tutti i tipi di query e può avere un effetto negativo su alcune operazioni di aggregazione, ad esempio quelle eseguite dalle query Count By Rating e Total Hits Counts.
 

Per comprendere la causa di questo comportamento, è opportuno considerare il numero di query eseguite correttamente in ogni situazione durante le esecuzioni dei test. La tabella seguente riepiloga questi dati:

| Cluster | Operazione/query | N. di operazioni/query - Cache indice disabilitata | N. di operazioni/query - Cache indice abilitata |
|---------|----------------------------|-------------------------------------------------|------------------------------------------------|
| DS4 | Inserimento | 38611 | 13232 |
| | Count By Rating | 524 | 18704 |
| | Count Over Time | 523 | 18703 |
| | Hits By Country | 522 | 18702 |
| | Top 15 Organizations | 521 | 18706 |
| | Unique Count Organizations | 521 | 18700 |
| | Unique IP Count | 521 | 18699 |
| | Total Hits Counts | 521 | 18701  
|||| |
| DS14 | Inserimento | 38769 | 12835 |
| | Count By Rating | 528 | 19239 |
| | Count Over Time | 528 | 19239 |
| | Hits By Country | 528 | 19238 |
| | Top 15 Organizations | 527 | 19240 |
| | Unique Count Organizations | 524 | 19234 |
| | Unique IP Count | 524 | 19234 |
| | Total Hits Counts | 527 | 19236 |

È possibile osservare che, anche se la frequenza di inserimento con la memorizzazione nella cache abilitata era circa 1/3 di quella con la memorizzazione nella cache disabilitata, il numero di query eseguite è aumentato di un fattore pari a 34. Le query non richiedono più lo stesso I/O del disco e non devono contendersi le risorse di rete, come risulta dai grafici della figura 13 che confrontano l'attività di I/O per i quattro casi:

![](./media/guidance-elasticsearch/query-performance13.png)

***Figura 13. Attività di I/O del disco per il test di *inserimento e query* con la memorizzazione nella cache dell'indice disabilitata e abilitata***

La riduzione di I/O del disco indica anche che la CPU ha impiegato meno tempo in attesa del completamento dell'I/O, come è evidenziato nella figura 14:

![](./media/guidance-elasticsearch/query-performance14.png)

***Figura 14. Tempo impiegato dalla CPU in attesa del completamento dell'I/O del disco per il test di *inserimento e query* con la memorizzazione nella cache dell'indice disabilitata e abilitata***

La riduzione nell'I/O del disco indica che Elasticsearch non è riuscito a impiegare un periodo di tempo molto più lungo per la gestione delle query dai dati contenuti in memoria. In questo modo è aumentato l'utilizzo della CPU, come è evidente se si esamina l'utilizzo della CPU per i quattro casi. I grafici seguenti illustrano come l'uso della CPU sia stato maggiore con la memorizzazione nella cache abilitata:

![](./media/guidance-elasticsearch/query-performance15.png)

***Figura 15. Utilizzo della CPU per il test di *inserimento e query* con la memorizzazione nella cache dell'indice disabilitata e abilitata***

Il volume di I/O di rete in entrambi gli scenari per tutta la durata dei test è stato molto simile. I test senza memorizzazione nella cache hanno mostrato una graduale riduzione delle prestazioni durante la fase di test, ma le esecuzioni più lunghe di questi test, della durata di 24 ore, hanno mostrato che questo valore si è stabilizzato su circa 2,75 Gbps. L'immagine seguente mostra questi dati per il cluster DS4 (i dati per i cluster DS14 erano molto simili):

![](./media/guidance-elasticsearch/query-performance16.png)

***Figura 16. Volumi del traffico di rete per il test di *inserimento e query* con la memorizzazione nella cache dell'indice disabilitata e abilitata***

Come illustrato nel test [Aumento delle prestazioni](#performance-results-scaling-up), le restrizioni alla larghezza di banda di rete con le VM di Azure non sono state pubblicate e possono variare, ma i bassi livelli di attività della CPU e del disco suggeriscono che l'utilizzo della rete può essere il fattore limitante in questo scenario.

La memorizzazione nella cache è più adatta agli scenari in cui i dati non vengono modificati spesso. Per evidenziare l'impatto della memorizzazione nella cache in questo scenario, i test *solo delle query* sono stati eseguiti con la memorizzazione nella cache abilitata. I risultati sono illustrati sotto (questi test sono stati eseguiti per 90 minuti e gli indici sottoposto a test contenevano 100 milioni di documenti):

| Cluster | Query | Tempo medio di risposta (ms) | N. di query eseguite |
|---------|----------------------------|----------------------------|-------------------------|
| | | **Cache disabilitata** | **Cache abilitata** |
| DS4 | Count By Rating | 4489 | 210 |
| | Count Over Time | 7292 | 211 |
| | Hits By Country | 7564 | 231 |
| | Top 15 Organizations | 5066 | 211 |
| | Unique Count Organizations | 5231 | 211 |
| | Unique IP Count | 9228 | 218 |
| | Total Hits Counts | 2180 | 210  
|||| |
| DS14 | Count By Rating | 1927 | 211 |
| | Count Over Time | 4483 | 219 |
| | Hits By Country | 4761 | 236 |
| | Top 15 Organizations | 2117 | 212 |
| | Unique Count Organizations | 2393 | 212 |
| | Unique IP Count | 7159 | 220 |
| | Total Hits Counts | 642 | 211 |

La varianza nelle prestazioni dei test non eseguiti nella cache è dovuta alla differenza nelle risorse disponibili tra le VM DS4 e DS14. In entrambi i casi del test eseguito nella cache il tempo medio di risposta è diminuito considerevolmente perché i dati sono stati recuperati direttamente dalla memoria. È anche opportuno notare che i tempi di risposta per i test dei cluster DS4 e DS14 eseguiti nella cache erano molto simili nonostante la differenza con i risultati non memorizzati nella cache. Anche la differenza tra i tempi di risposta per ogni query in ogni test è minima. Il tempo di risposta è sempre di circa 220 ms. Le frequenze di I/O del disco e l'utilizzo della CPU per entrambi i cluster erano molto bassi perché, una volta che tutti i dati sono in memoria, l'I/O o l'elaborazione richiesta è minima. La frequenza di I/O di rete era simile a quella dei test non eseguiti nella cache, a conferma del fatto che la larghezza di banda di rete può essere un fattore limitante in questo test. I grafici seguenti presentano queste informazioni per il cluster DS4. Il profilo del cluster DS14 era molto simile:

![](./media/guidance-elasticsearch/query-performance17.png)

***Figura 17. I/O del disco, utilizzo della CPU e utilizzo della rete per il test *solo delle query* con la memorizzazione nella cache dell'indice abilitata***

Le cifre della tabella precedente suggeriscono che l'uso dell'architettura DS14 offre pochi vantaggi rispetto a DS4. Infatti il numero di campioni generati dal cluster DS14 era circa il 5% in meno rispetto al cluster DS4, ma la causa di ciò possono essere anche le restrizioni di rete che possono variare leggermente nel corso del tempo.

## Risultati delle prestazioni: numero di partizioni

Lo scopo di questo test era di determinare se il numero di partizioni create per un indice influisce sulle prestazioni delle query di tale indice.

Test separati condotti in precedenza hanno dimostrato che la configurazione delle partizioni di un indice può influire sulla frequenza di inserimento dei dati. Questi test sono illustrati nel documento relativo all'[ottimizzazione delle prestazioni di inserimento dei dati con Elasticsearch in Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md). I test eseguiti per verificare le prestazioni delle query hanno seguito una metodologia simile, ma sono stati limitati a un cluster a 6 nodi in esecuzione su hardware DS14. Questo approccio consente di ridurre al minimo il numero di variabili, quindi eventuali differenze nelle prestazioni saranno da ricondurre al volume delle partizioni.

Il test *solo delle query* è stato eseguito su copie dello stesso indice configurato con 7, 13, 23, 37 e 61 partizioni primarie. L'indice conteneva 100 milioni di documenti e aveva una sola replica, raddoppiando il numero di partizioni nel cluster. Ogni test è stato eseguito per 90 minuti. La tabella seguente riepiloga i risultati. Il tempo medio di risposta indicato è il tempo di risposta per la transazione di test JMeter che include il set completo di query eseguite da ogni iterazione del test. Per altre informazioni, vedere la nota nella sezione [Risultati delle prestazioni: aumento delle prestazioni](#performance-results-scaling-up):

| Numero di partizioni | Layout partizioni (partizioni per nodo, incluse le repliche) | Numero di query eseguite | Tempo medio di risposta (ms) |
|---------------------------|----------------------------------------------------|-----------------------------|------------------------|
| 7 (14 incluse le repliche) | 3-2-2-2-2-3 | 7461 | 40524 |
| 13 (26) | 5-4-5-4-4-4 | 7369 | 41055 |
| 23 (46) | 7-8-8-7-8-8 | 14193 | 21283 |
| 37 (74) | 13-12-12-13-12-12 | 13399 | 22506 |
| 61 (122) | 20-21-20-20-21-20 | 14743 | 20445 |

Questi risultati indicano una differenza considerevole a livello di prestazioni tra il cluster con 13 (26) partizioni e il cluster con 23 (46) partizioni. La velocità effettiva è quasi doppia e i tempi di risposta sono dimezzati. La causa sono molto probabilmente la configurazione delle VM e le strutture usate da Elasticsearch per elaborare le richieste di ricerca. Le richieste di ricerca vengono accodate e ognuna viene gestita da un solo thread di ricerca. Il numero di thread di ricerca creati da un nodo di Elasticsearch è una funzione del numero di processori disponibili nel computer che ospita il nodo. I risultati suggeriscono che con solo 4 o 5 partizioni in un nodo, le risorse di elaborazione non vengono utilizzate completamente. Questa ipotesi è supportata dall'osservazione dell'utilizzo della CPU durante l'esecuzione del test. L'immagine seguente è uno snapshot acquisito da Marvel durante l'esecuzione del test di 13 (26) partizioni:

![](./media/guidance-elasticsearch/query-performance18.png)

***Figura 18. Utilizzo della CPU per il test *solo delle query* nel cluster con 7 (14) partizioni***

Confrontare queste cifre con quelle del test di 23 (46) partizioni:

![](./media/guidance-elasticsearch/query-performance19.png)

***Figura 19. Utilizzo della CPU per il test *solo delle query* nel cluster con 23 (46) partizioni***

Nel test di 23 (46) partizioni l'utilizzo della CPU era molto più elevato. Ogni nodo contiene 7 o 8 partizioni. L'architettura DS14 fornisce 16 processori ed Elasticsearch può sfruttare meglio questo numero di core con le partizioni aggiuntive. Le cifre della tabella precedente suggeriscono che aumentare il numero di partizioni oltre questo limite può migliorare leggermente le prestazioni, ma ciò comporterebbe un overhead aggiuntivo dovuto alla gestione di un volume elevato di partizioni. La condizione ideale di questi test richiede che il numero ottimale di partizioni per ogni nodo sia la metà del numero di core del processore disponibili in ogni nodo. Si ricordi tuttavia che questi risultati sono stati raggiunti solo durante l'esecuzione delle query. Se il sistema importa dati, è anche consigliabile considerare come il partizionamento può influire sulle prestazioni delle operazioni di inserimento dei dati. Per altre informazioni su questo aspetto, vedere il documento relativo all'[ottimizzazione delle prestazioni di inserimento dei dati con Elasticsearch in Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

## Riepilogo

Elasticsearch offre molte opzioni che è possibile usare per strutturare gli indici e ottimizzarli per supportare operazioni di query su larga scala. Queste documento ha riepilogato alcune configurazioni e tecniche comuni che è possibile usare per ottimizzare il database per eseguire query. Tuttavia, è innegabile che esista un compromesso tra l'ottimizzazione di un database per supportare il recupero rapido e il supporto dell'inserimento di volumi elevati di dati. A volte ciò che funziona bene per le query può avere un impatto negativo sulle operazioni di inserimento e viceversa. In un sistema esposto a carichi di lavoro misti è necessario trovare un equilibrio e modificare di conseguenza i parametri di sistema.

Inoltre, l'applicabilità di configurazioni e tecniche diverse può variare a seconda della struttura dei dati e delle limitazioni (o altro) dell'hardware in cui il sistema viene costruito. Diversi test illustrati in questo documento dimostrano come la selezione della piattaforma hardware possa influire sulla velocità effettiva e anche come alcune strategie possano essere vantaggiose in alcuni casi, ma dannose in altri. Ciò che conta è conoscere le opzioni disponibili e quindi effettuare un benchmark rigoroso usando i propri dati per determinare la combinazione ottimale.

Si ricordi infine che un database di Elasticsearch non è necessariamente un elemento statico, ma probabilmente crescerà nel tempo e potrebbe essere necessario riesaminare periodicamente le strategie usate per strutturare i dati. Ad esempio, può essere necessario aumentare le prestazioni, aumentare il numero di istanze o reindicizzare i dati con partizioni aggiuntive. Man mano che aumentano le dimensioni e la complessità del sistema, è importante essere pronti a testare ininterrottamente le prestazioni per essere certi di rispettare i Contratti di servizio garantiti ai clienti.

## Appendice: Test delle prestazioni di query e aggregazioni

Questa appendice descrive il test delle prestazioni eseguito sul cluster Elasticsearch. I test sono stati eseguiti con JMeter in esecuzione in un set di VM separato. Per informazioni dettagliate sull'ambiente di test, vedere il documento relativo alla procedura di creazione di un ambiente di test delle prestazioni per Elasticsearch. Per eseguire il test, è possibile creare manualmente il proprio piano di test con JMeter seguendo le istruzioni di questa appendice o usare gli script di test automatici disponibili separatamente. Per altre informazioni, vedere il documento relativo alla procedura di esecuzione di test automatici delle query con Elasticsearch.

Il carico di lavoro di query sui dati ha eseguito il set di query illustrate sotto eseguendo contemporaneamente un caricamento di documenti su larga scala. I dati sono stati caricati usando un test JUnit e seguendo lo stesso approccio per i test di inserimento dei dati descritto nel documento relativo all'[ottimizzazione delle prestazioni di inserimento dei dati con Elasticsearch in Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md). Lo scopo di questo carico di lavoro era quello di simulare un ambiente di produzione in cui vengono continuamente aggiunti nuovi dati mentre vengono eseguite le ricerche. Le query sono state strutturate per recuperare solo i dati più recenti dai documenti aggiunti negli ultimi 15 minuti.

Ogni documento è stato archiviato in un singolo indice denominato *idx* con il tipo *doc*. È possibile usare la richiesta HTTP seguente per creare l'indice. In molti test le impostazioni *number\_of\_replicas* e *number\_of\_shards* erano diverse rispetto ai valori illustrati di seguito. Inoltre, per i test che hanno usato dati dei campi invece di valori dei documenti, ogni proprietà è stata annotata con l'attributo *"doc\_values" : false*.

> **Importante**. L'indice è stato eliminato e ricreato prima di ogni esecuzione di test.

``` http
PUT /idx
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "doc": {
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

Le query seguenti sono state eseguite dal test:
* Quanti documenti con ogni valore Rating sono stati immessi negli ultimi 15 minuti?

  ```http
  GET /idx/doc/_search
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

* Quanti documenti sono stati aggiunti in ogni intervallo di 5 minuti negli ultimi 15 minuti?

  ```http
  GET /idx/doc/_search
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

* Quanti documenti con ogni valore Rating sono stati aggiunti per ogni paese negli ultimi 15 minuti?

  ```HTTP
  GET /idx/doc/_search
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

* Quali sono le 15 organizzazioni che ricorrono con maggiore frequenza nei documenti aggiunti negli ultimi 15 minuti?

  ```http
  GET /idx/doc/_search
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

* Quante organizzazioni diverse ricorrono nei documenti aggiunti negli ultimi 15 minuti?

  ```http
  GET /idx/doc/_search
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

* Quanti documenti sono stati aggiunti negli ultimi 15 minuti?

  ```http
  GET /idx/doc/_search
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

* Quante valori SourceIp diversi ricorrono nei documenti aggiunti negli ultimi 15 minuti?

  ```http
  GET /idx/doc/_search
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

<!---HONumber=AcomDC_0302_2016-->