<properties
	pageTitle="Guida introduttiva: API Recommendations di Machine Learning | Microsoft Azure"
	description="API Recommendations di Azure Machine Learning: guida introduttiva"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="luisca"/>  

#  Tipi di compilazione e qualità dei modelli #

<a name="TypeofBuilds"></a>
## Tipi di compilazione supportati ##

L'API Recommendations attualmente supporta due tipi di compilazione: *elementi consigliati* e *FBT*. Ogni tipo viene compilato usando algoritmi diversi e presenta vantaggi differenti. Questo documento descrive i tipi di compilazione e le tecniche per confrontare la qualità dei modelli generati.

Se non è già stato fatto, è consigliabile completare la [Guida introduttiva](cognitive-services-recommendations-quick-start.md).

<a name="RecommendationBuild"></a>
### Tipo di compilazione di elementi consigliati ###

Il tipo di compilazione di elementi consigliati usa la fattorizzazione di matrice per fornire indicazioni. Genera vettori di [funzionalità latenti](https://en.wikipedia.org/wiki/Latent_variable) basati sulle transazioni per descrivere ogni elemento e li usa per confrontare gli elementi simili.

Supponendo che si esegua il training del modello in base agli acquisti effettuati nel proprio negozio di elettronica e che venga indicato un telefono Lumia 650 come input per il modello, verrà restituito un set di elementi che tendono a essere acquistati dalle persone che è probabile acquistino un telefono Lumia 650. Gli elementi possono non essere complementari. In questo esempio il modello può restituire altri telefoni perché le persone interessate al telefono Lumia 650 potrebbero essere interessate anche ad altri telefoni.

La compilazione di elementi consigliati presenta due funzionalità che la rendono interessante:

**La compilazione di elementi consigliati supporta il posizionamento di *elementi ad accesso sporadico***

Gli elementi che non vengono usati di frequente sono detti elementi ad accesso sporadico. Se, ad esempio, si riceve una spedizione di un telefono mai venduto in precedenza, il sistema non può dedurre le raccomandazioni per questo prodotto in base alle sole transazioni. Ciò significa che il sistema deve apprendere dalle informazioni relative al prodotto stesso.

Per usare il posizionamento di elementi ad accesso sporadico, è necessario specificare le informazioni sulle funzionalità per ognuno degli elementi nel catalogo. Di seguito viene riportato un esempio di come possono apparire le prime righe del catalogo. Si noti il formato chiave=valore usato per le funzionalità.

>6CX-00001,Surface Pro2, Surface,, Type=Hardware, Storage=128 GB, Memory=4G, Manufacturer=Microsoft

>73H-00013,Wake Xbox 360,Gaming,, Type=Software, Language=English, Rating=Mature

>WAH-0F05,Minecraft Xbox 360,Gaming,, * Type=Software, Language=Spanish, Rating=Youth

È necessario impostare anche i parametri di compilazione seguenti:

| Parametro di compilazione | Note
|------------------     |-----------
|*useFeaturesInModel* | Impostare su **true**. Indica se le funzionalità possono essere usate per migliorare il modello di raccomandazione.
|*allowColdItemPlacement* | Impostare su **true**. Indica se la raccomandazione dovrà effettuare il push anche degli elementi ignoti in base alla somiglianza di funzionalità.
| *modelingFeatureList* | Elenco delimitato da virgole di nomi di funzionalità da usare nella compilazione di elementi consigliati, allo scopo di migliorare la raccomandazione. Ad esempio "Language,Storage" per l'esempio precedente.

**La compilazione di elementi consigliati supporta elementi consigliati per gli utenti**

La compilazione di elementi consigliati supporta [elementi consigliati per gli utenti](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Ciò significa che può indicare elementi consigliati agli utenti, personalizzati in base alla relativa cronologia delle transazioni. Per gli elementi consigliati per gli utenti è possibile specificare l'ID utente o la cronologia recente delle relative transazioni.

Un esempio tipico di utilizzo possibile degli elementi consigliati per gli utenti è il momento dell'accesso alla pagina introduttiva. Qui è possibile promuovere il contenuto che si applica all'utente specifico.

È possibile applicare una compilazione di elementi consigliati anche quando l'utente sta per effettuare il check-out. A quel punto sarà disponibile l'elenco degli elementi che l'utente sta per acquistare ed è possibile proporre indicare elementi consigliati sulla base del carrello corrente.

#### Parametri di compilazione di elementi consigliati

| Nome | 	Descrizione |	 Tipo, <br> valori validi<br> (valore predefinito)
|-------|-------------------|------------------
| *NumberOfModelIterations* |	Il numero di iterazioni eseguite dal modello viene riflesso dal tempo di calcolo complessivo e dall'accuratezza del modello. A un numero più alto corrisponde un modello più accurato, ma il tempo di calcolo sarà più lungo. |	 Integer, <br> da 10 a 50 <br>(40)
| *NumberOfModelDimensions* |	Il numero di dimensioni è correlato al numero di funzionalità che il modello proverà a trovare nei dati. L'aumento del numero di dimensioni consentirà l'ottimizzazione dei risultati in cluster più piccoli. Troppe dimensioni impediranno tuttavia al modello di trovare correlazioni tra gli elementi. |	Integer, <br> da 10 a 40 <br>(20) |
| *ItemCutOffLowerBound* |	Definisce il numero minimo di punti di utilizzo in cui deve essere presente un elemento per essere considerato parte del modello. |		Integer, <br> 2 o più <br> (2) |
| *ItemCutOffUpperBound* | 	Definisce il numero massimo di punti di utilizzo in cui deve essere presente un elemento per essere considerato parte del modello. | Integer, <br>2 o più<br> (2147483647) |
|*UserCutOffLowerBound* |	Definisce il numero minimo di transazioni di che un utente deve avere eseguito per essere considerato parte del modello. |	Integer, <br> 2 o più <br> (2)
| *UserCutOffUpperBound* |	Definisce il numero massimo di transazioni di che un utente deve avere eseguito per essere considerato parte del modello. |	Integer, <br> 2 o più <br> (2147483647)|
| *UseFeaturesInModel* |	Indica se le funzionalità possono essere usate per migliorare il modello di raccomandazione. | 	 Booleano <br> Valore predefinito: True
|*ModelingFeatureList* |	Elenco delimitato da virgole di nomi di funzionalità da usare nella compilazione di elementi consigliati, allo scopo di migliorare la raccomandazione. Dipende dalle funzionalità considerate importanti. |	Stringa, fino a 512 caratteri
| *AllowColdItemPlacement* |	Indica se la raccomandazione dovrà effettuare il push anche degli elementi ignoti in base alla somiglianza di funzionalità. | Booleano <br> Valore predefinito: False
| *EnableFeatureCorrelation* | Indica se le funzionalità possono essere usate nella motivazione. |	Booleano <br> Valore predefinito: False
| *ReasoningFeatureList* |	Elenco delimitato da virgole di nomi delle funzionalità da usare nelle frasi relative alla motivazione, ad esempio le spiegazioni delle raccomandazioni. Dipende dalle funzionalità considerate importanti per i clienti. | Stringa, fino a 512 caratteri
| *EnableU2I* |	Consente di abilitare i consigli personalizzati, anche detti consigli da utente a elemento (U2I). | Booleano <br> Valore predefinito: True
|*EnableModelingInsights* |	Definisce se deve essere eseguita la valutazione offline per raccogliere informazioni di modellazione, vale a dire metriche di precisione e diversità. Se impostato su true, il subset dei dati non verrà usato per il training perché dovrà essere riservato per il test del modello. Altre informazioni sulle [valutazioni offline](#OfflineEvaluation). | Booleano <br> Valore predefinito: False
| *SplitterStrategy* | Se EnableModelingInsights è impostato su *true*, indica il modo in cui i dati devono essere suddivisi ai fini della valutazione. | Stringa, *RandomSplitter* o *LastEventSplitter* <br>Valore predefinito: RandomSplitter


<a name="FBTBuild"></a>
### Tipo di compilazione FBT ###

La compilazione FBT (Frequently Bought Together) esegue un'analisi che conta il numero di volte in cui due o tre prodotti diversi ricorrono insieme. Quindi, ordina i set in base a una funzione di somiglianza, ad esempio **co-occorrenza**, **Jaccard** e **accuratezza**.

**Jaccard** e **accuratezza** costituiscono modi per normalizzare le co-occorrenze. Ciò significa che gli elementi verranno restituiti solo se sono stati acquistati con l'elemento seme.

Nell'esempio del telefono Lumia 650 verrà restituito un telefono X solo se il telefono X è stato acquistato nella stessa sessione del telefono Lumia 650. Dato che ciò può essere improbabile, è prevedibile che vengano restituiti elementi complementari per il Lumia 650, ad esempio una protezione per lo schermo o un adattatore di alimentazione.

Attualmente, due elementi vengono considerati acquistati nella stessa sessione se presenti in una transazione con lo stesso ID utente e timestamp.

Le compilazioni FBT non supportano elementi ad accesso sporadico perché per definizione prevedono che due elementi vengano acquistati nella stessa transazione. Nonostante le compilazioni FBT possano restituire set di elementi, a gruppi di tre, non supportano elementi consigliati personalizzati perché accettano un solo elemento seme come input.


#### Parametri della compilazione FBT

| Name | 	Descrizione |		Tipo, <br> valori validi<br> (valore predefinito)
|-------|---------------|-----------------------
| *FbtSupportThreshold* | Indica il livello conservativo del modello. Numero di co-occorrenze di elementi da considerare per la creazione del modello. | Integer, <br> da 3 a 50 <br> (6)
| *FbtMaxItemSetSize* | Limita il numero di elementi in un set frequente.| Integer <br> da 2 a 3 <br> (2)
| *FbtMinimalScore* | Punteggio minimo che un set frequente deve avere per essere incluso nei risultati restituiti. Più alto è il valore, migliori saranno i risultati. | Double <br> maggiore o uguale a 0 <br> (0)
| *FbtSimilarityFunction* | Definisce la funzione di somiglianza da usare per la compilazione. L'**accuratezza** predilige la casualità, la **co-occorrenza** predilige la prevedibilità e la funzione **Jaccard** rappresenta un compromesso tra la prime due. | Stringa, <br> <i>co-occorrenza, accuratezza, Jaccard</i><br> Valore predefinito: <i>Jaccard</i>

<a name="SelectBuild"></a>
## Valutazione e selezione della compilazione ##

Queste indicazioni possono contribuire a stabilire se usare una compilazione di elementi consigliati o una compilazione FBT, ma non forniscono una risposta definitiva nei casi in cui possono essere usate entrambe. Anche nei casi in cui si intende usare un tipo di compilazione FBT, è comunque possibile che si voglia usare una funzione di similarità **Jaccard** o di **accuratezza**.

Il modo migliore per scegliere tra le due diverse compilazioni è provarle nel mondo reale, con una valutazione online, e tenere traccia di un tasso di conversione per le diverse compilazioni. Il tasso di conversione può essere misurato in base al numero di clic per gli elementi consigliati, al numero di acquisti effettivi derivanti dalla visualizzazione degli elementi consigliati o anche in base all'effettivo importo degli acquisti con la visualizzazione dei diversi elementi consigliati. È possibile selezionare la metrica del tasso di conversione in base all'obiettivo di business.

In alcuni casi può essere opportuno valutare il modello offline prima di inserirlo nell'ambiente di produzione. Anche se la valutazione offline non sostituisce la valutazione online, può servire da metrica.

<a name="OfflineEvaluation"></a>
## Valutazione offline  ##

L'obiettivo di una valutazione offline consiste nel prevedere la precisione, ovvero il numero di utenti che acquisterà uno degli elementi consigliati, e la diversità degli elementi consigliati, ovvero il numero di elementi consigliati. Nell'ambito della valutazione delle metriche di precisione e diversità il sistema trova un campione di utenti e suddivide le relative transazioni in due gruppi: il set di dati di training e il set di dati di test.

> [AZURE.NOTE] Per usare le metriche offline è necessario avere il timestamp nei dati di utilizzo. I dati temporali sono infatti necessari per suddividere correttamente l'utilizzo tra i set di dati di training e quello di test.

> La valutazione offline potrebbe non produrre risultati per i file dei dati di utilizzo di piccole dimensioni. Perché sia veramente accurata, la valutazione deve prendere in considerazione almeno 1.000 di punti di utilizzo nel set di dati di test.

<a name="Precision"></a>
### Precisione su K elementi ###
La tabella seguente descrive l'output della valutazione offline della precisione su K elementi.

| K | 1 | 2 | 3 | 	4 | 	5
|---|---|---|---|---|---|
|Percentuale |	13,75 |	18,04 | 21 |	24,31 |	26,61
|Utenti nel test |	10\.000 |	10\.000 |	10\.000 |	10\.000 |	10\.000
|Utenti considerati |	10\.000 |	10\.000 |	10\.000 |	10\.000 |	10\.000
|Utenti non considerati |	0 |	0 |	0 |	0 |	0

#### K
Nella tabella precedente, *K* rappresenta il numero di elementi consigliati visualizzati al cliente. La tabella deve essere letta come segue: "Se durante il periodo di test fosse stato mostrato agli utenti un solo elemento consigliato, solo il 13,75% degli utenti avrebbe acquistato quell'elemento consigliato". Questa affermazione si basa sul presupposto che il modello sia stato sottoposto a training con i dati di acquisto. In altre parole, la precisione a 1 è 13,75.

Si noterà che nel momento in cui altri elementi vengono visualizzati al cliente, la probabilità che il cliente acquisti un elemento consigliato aumenta. Per l'esperimento precedente, la probabilità raddoppia quasi al 26,61% quando sono consigliati 5 elementi.

#### Percentuale
Percentuale di utenti che ha interagito con almeno uno dei *K* elementi consigliati visualizzati. La percentuale viene calcolata dividendo il numero di utenti che ha interagito con almeno un elemento consigliato per il numero totale di utenti considerati. Per altre informazioni, vedere Utenti considerati.

#### Utenti nel test
I dati in questa riga rappresentano il numero totale di utenti nel set di dati di test.

#### Utenti considerati
Un utente viene considerato solo se il sistema ha consigliato almeno *K* elementi in base al modello generato con il set di dati di training.

#### Utenti non considerati
I dati in questa riga rappresentano tutti gli utenti non considerati. Si tratta degli utenti che non hanno ricevuto almeno *K* elementi consigliati.

Utenti non considerati = utenti nel test - utenti considerati

<a name="Diversity"></a>
### Diversità ###
Le metriche di diversità misurano il tipo di elementi consigliati. La tabella seguente descrive l'output della valutazione offline della diversità.

|Bucket percentile |	0-90| 90-99| 99-100
|------------------|--------|-------|---------
|Percentuale | 34,258 | 55,127| 10,615


Totale elementi consigliati: 100.000

Elementi univoci consigliati: 954

#### Bucket percentili
Ogni bucket percentile è rappresentato da un intervallo, ovvero valori compresi tra un minimo pari a 0 e un massimo pari a 100. Gli elementi prossimi a 100 sono i più richiesti, mentre gli elementi prossimi allo 0 sono i meno richiesti. Se ad esempio il valore percentuale per il bucket percentile 99-100 è 10,6, il 10,6% degli elementi consigliati ha restituito solo il primo 1% di elementi consigliati. Il valore minimo del bucket percentile è inclusivo e il valore massimo è esclusivo, ad eccezione di 100.
#### Elementi univoci consigliati
La metrica elementi univoci consigliati indica il numero di elementi distinti restituiti per la valutazione.
#### Totale elementi consigliati
La metrica totale elementi consigliati indica il numero di elementi consigliati. Alcuni potrebbero essere duplicati.

<a name="ImplementingEvaluation"></a>
### Metriche di valutazione offline ###
Le metriche offline di precisione e diversità possono essere utili nella scelta della compilazione da usare. In fase di compilazione nell'ambito dei parametri per la compilazione FBT o di elementi consigliati:

-	Impostare il parametro di compilazione *enableModelingInsights* su **true**.
-	È possibile selezionare facoltativamente il parametro *splitterStrategy*, ovvero *RandomSplitter* o *LastEventSplitter*. *RandomSplitter* suddivide i dati di utilizzo in set di training e set di test basati sui valori percentuali e di seme casuali *randomSplitterParameters* specificati per il test. *LastEventSplitter* suddivide i dati di utilizzo in set di training e set di test in base all'ultima transazione per ogni utente.

Verrà attivata una compilazione che usa solo un subset dei dati per il training, mentre il resto dei dati viene usato per calcolare le metriche di valutazione. Dopo aver completato la compilazione, per ottenere l'output della valutazione è sufficiente chiamare l'[API Get build metrics](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/577eaa75eda565095421666f) passando i relativi oggetti *modelId* e *buildId*.

 Di seguito è riportato l'output JSON per la valutazione di esempio.


    {
     "Result": {
     "precisionItemRecommend": null,
     "precisionUserRecommend": {
      "precisionMetrics": [
        {
          "k": 1,
          "percentage": 13.75,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 2,
          "percentage": 18.04,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 3,
          "percentage": 21.0,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 4,
          "percentage": 24.31,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        },
        {
          "k": 5,
          "percentage": 26.61,
          "usersInTest": 10000,
          "usersConsidered": 10000,
          "usersNotConsidered": 0
        }
      ],
      "error": null
    },
    "diversityItemRecommend": null,
    "diversityUserRecommend": {
      "percentileBuckets": [
        {
          "min": 0,
          "max": 90,
          "percentage": 34.258
        },
        {
          "min": 90,
          "max": 99,
          "percentage": 55.127
        },
        {
          "min": 99,
          "max": 100,
          "percentage": 10.615
        }
      ],
      "totalItemsRecommended": 100000,
      "uniqueItemsRecommended": 954,
      "uniqueItemsInTrainSet": null,
      "error": null
      }
     },
    "Id": 1,
    "Exception": null,
    "Status": 5,
    "IsCanceled": false,
    "IsCompleted": true,
    "CreationOptions": 0,
    "AsyncState": null,
    "IsFaulted": false
    }

<!---HONumber=AcomDC_0824_2016-->