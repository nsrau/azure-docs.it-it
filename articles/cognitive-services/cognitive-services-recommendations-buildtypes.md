<properties
	pageTitle="Guida introduttiva: API Recommendations di Machine Learning | Microsoft Azure"
	description="Recommendations di Azure Machine Learning - Guida introduttiva"
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
## Quale tipo di compilazione di elementi consigliati si deve usare? ##

Attualmente sono supportati due tipi di compilazione: *Elementi consigliati* e *FBT*. Ogni tipo viene compilato usando algoritmi diversi e ha vantaggi diversi.

<a name="RecommendationBuild"></a>
### Tipo di compilazione di elementi consigliati ###

La compilazione di *elementi consigliati* usa la fattorizzazione di matrici per offrire indicazioni. Usa in sintesi le transazioni dell'utente per generare vettori di [funzionalità latenti](https://en.wikipedia.org/wiki/Latent_variable) per descrivere ogni elemento e quindi usa questi vettori latenti per confrontare gli elementi simili.

Supponendo che si esegua il training del modello in base agli acquisti effettuati nel proprio negozio di elettronica, e che in fase di assegnazione dei punteggi venga indicato un telefono Lumia 650 come input per il modello, verrà restituito un set di elementi che tendono a essere acquistati da utenti che è probabile acquistino un telefono Lumia 650. Si noti che gli elementi possono non essere complementari. In questo esempio è possibile che vengano restituiti altri telefoni perché gli utenti interessati al telefono Lumia 650 potrebbero essere interessati ad altri telefoni.

La compilazione di elementi consigliati presenta due funzionalità che la rendono interessante:

-	Supporta il posizionamento di elementi freddi.

 Per *elementi freddi* si intendono gli elementi che non hanno un utilizzo significativo. Si supponga ad esempio di avere appena ricevuto una spedizione del nuovissimo telefono Lumia. Dato che non è mai stato venduto in passato, il sistema non può dedurre elementi consigliati per questo prodotto in base alle sole transazioni. Ciò significa che il sistema deve apprendere dalle informazioni relative al prodotto.

 Per usare il posizionamento di elementi freddi è necessario indicare informazioni sulle caratteristiche di ogni elemento nel catalogo. Le prime righe del catalogo potrebbero essere simili alle seguenti. Si noti il formato chiave=valore delle caratteristiche:

> 6CX-00001,Surface Pro2, Surface, Type=Hardware, Storage=128GB, Memory=4G, Manufacturer=Microsoft

> 73H-00013,Wake Xbox 360,Gaming, Type=Software, Language=English, Rating=Mature

> WAH-0F05,Minecraft Xbox 360,Gaming, * Type=Software, Language=Spanish, Rating=Youth

> ...

 Nell'ambito dei parametri di compilazione è anche necessario impostare i seguenti:

| Parametro di compilazione | Note
|------------------     |-----------
|useFeaturesInModel | Impostare su true. Indica se le funzionalità possono essere usate in ordine per migliorare il modello di raccomandazione. 
|allowColdItemPlacement | Impostare su true. Indica se la raccomandazione dovrà effettuare il push anche degli elementi ignoti in base alla somiglianza di funzionalità.
| modelingFeatureList | Elenco con valori delimitati da virgole dei nomi di funzionalità da usare nella compilazione di raccomandazioni, allo scopo di migliorare la raccomandazione. Ad esempio "Language,Storage" per l'esempio precedente.

-	Supporta gli elementi consigliati per gli utenti.

 Una compilazione di elementi consigliati supporta [elementi consigliati per gli utenti](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd). Ciò significa che la cronologia delle transazioni di un utente verrà usata per indicare elementi consigliati personalizzati per quell'utente. Per gli elementi consigliati per gli utenti è possibile specificare l'id utente e/o la cronologia recente delle sue transazioni.

 Un classico esempio in cui applicare elementi consigliati per gli utenti è quando l'utente accede alla pagina iniziale del negozio/sito. È qui possibile promuovere il convenuto che si applica all'utente specifico.
 
 È anche possibile applicare una compilazione di elementi consigliati quando l'utente sta per effettuare il check-out. A quel punto sarà disponibile l'elenco degli elementi che il cliente sta per acquistare ed è possibile indicare elementi consigliati sulla base del carrello corrente.

<a name="FBTBuild"></a>
### Tipo di compilazione FBT ###

*FBT* è l'acronimo di "Frequently-Bought-Together", ovvero articoli frequentemente acquistati insieme. La compilazione FBT esegue un'analisi che conta il numero di volte in cui due o tre prodotti diversi vengono acquistati insieme e quindi ordina i set in base a una funzione di similarità (co-occorrenze, Jaccard, accuratezza).

Jaccard e accuratezza costituiscono modi per normalizzare le co-occorrenze. Ciò significa che gli elementi verranno restituiti solo se sono stati effettivamente acquistati con l'elemento seme.

Nell'esempio del telefono Lumia 650 verrà restituito un telefono X se e solo se il telefono X è stato acquistato nella stessa sessione del telefono Lumia 650. Poiché ciò può essere improbabile, è prevedibile che vengano restituiti elementi complementari per il Lumia 650, ad esempio una protezione per lo schermo o un adattatore di alimentazione.

Due elementi vengono attualmente considerati acquistati nella stessa sessione se presenti in una transazione con uguale ID utente e timestamp.

Le compilazioni FBT non supportano attualmente elementi freddi perché prevedono per definizione che due elementi vengano effettivamente acquistati nella stessa transazione. Nonostante le compilazioni FBT possano restituire set di elementi (gruppi di tre), non supportano elementi consigliati personalizzati perché accettano un solo elemento seme come input.

<a name="SelectBuild"></a>
## Come scegliere la compilazione da usare? ##

Le indicazioni precedenti possono essere sufficienti a determinare se usare una compilazione di elementi consigliati o una compilazione FBT. Possono tuttavia verificarsi casi in cui possono essere usate entrambe. Anche nei casi in cui si intende usare un tipo di compilazione FBT, è comunque possibile che si voglia decidere se usare la funzione di similarità Jaccard o di accuratezza.

Il modo migliore per scegliere tra le due diverse compilazioni è provarle nel mondo reale (valutazione online) e tenere traccia di un tasso di conversione per le diverse compilazioni. Il tasso di conversione può essere misurato in base al numero di clic per gli elementi consigliati, al numero di acquisti effettivi derivanti dalla visualizzazione degli elementi consigliati o anche in base all'effettivo importo in denaro derivante dalle vendite con la visualizzazione dei diversi elementi consigliati. È possibile selezionare la metrica del tasso di conversione in base all'obiettivo di business.

In alcuni casi può essere opportuno valutare il modello offline prima di inserirlo nell'ambiente di produzione. Anche se la valutazione offline non sostituisce la valutazione online, può servire da metrica.

<a name="OfflineEvaluation"></a>
## Valutazione offline  ##

L'obiettivo di una valutazione offline consiste nel prevedere la precisione, ovvero il numero di utenti che acquisterà effettivamente uno degli elementi consigliati, e la diversità degli elementi consigliati, ovvero il numero effettivo di elementi consigliati. Nell'ambito della valutazione delle metriche di precisione e diversità, il sistema trova un campione di utenti e quindi le transazioni di quegli utenti vengono suddivise in due gruppi: il set di dati di training e il set di dati di test.

> Nota:
>
> Per usare le metriche offline è necessario avere timestamp nei dati di utilizzo. I dati temporali sono infatti necessari per suddividere correttamente l'utilizzo tra i set di dati di training e di test.

> La valutazione offline potrebbe non produrre risultati per file di utilizzo di piccole dimensioni, semplicemente perché sono previsti almeno 1000 di punti di utilizzo nel set di dati di test per ottenere una valutazione dettagliata.

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
Nella tabella precedente, *K* rappresenta il numero di elementi consigliati visualizzati al cliente. La tabella deve essere quindi letta come segue: "Se durante il periodo di test fosse stato visualizzato agli utenti un solo elemento consigliato, solo il 13,75% degli utenti avrebbe effettivamente acquistato quell'elemento consigliato", presupponendo che il training del modello sia stato eseguito con i dati di acquisto. In altre parole, la "precisione a 1" è 13,75.

Si noterà che nel momento in cui altri elementi vengono visualizzati al cliente, la probabilità che il cliente acquisti un elemento consigliato aumenta. Per l'esperimento precedente, la probabilità raddoppia quasi al 26,61% quando sono consigliati 5 elementi.

#### Percentuale
Percentuale di utenti che ha interagito con almeno uno dei K elementi consigliati visualizzati. La percentuale viene calcolata dividendo il numero di utenti che ha interagito con almeno un elemento consigliato per il numero totale di utenti considerati. Vedere la definizione degli utenti considerati.

#### Utenti nel test
Numero totale di utenti nel set di dati di test.

#### Utenti considerati
Un utente viene considerato solo se il sistema ha consigliato almeno K elementi in base al modello generato con il set di dati di training.

#### Utenti non considerati
Tutti gli utenti non considerati; utenti che non hanno ricevuto almeno K elementi consigliati.

Utenti non considerati = utenti nel test – utenti considerati

<a name="Diversity"></a>
### Diversità ###
Le metriche di diversità misurano il tipo di elementi consigliati. La tabella seguente descrive l'output della valutazione offline della diversità.

|Bucket percentile |	0-90| 90-99| 99-100
|------------------|--------|-------|---------
|Percentuale | 34,258 | 55,127| 10,615


Totale elementi consigliati: 100.000

Elementi univoci consigliati: 954

#### Bucket percentili
Ogni bucket percentile è rappresentato da un intervallo, ovvero valori min/max compresi tra 0 e 100. Gli elementi prossimi a 100 sono i più richiesti, mentre gli elementi prossimi allo 0 sono i meno richiesti. Se ad esempio il valore percentuale per il bucket percentile 99-100 è 10,6, il 10,6% degli elementi consigliati ha restituito solo il primo 1% di elementi consigliati. Il valore minimo del bucket percentile è inclusivo e il valore massimo è esclusivo, ad eccezione di 100.
#### Elementi univoci consigliati
Numero di elementi distinti restituiti per la valutazione.
#### Totale elementi consigliati
Numero totale di elementi consigliati. Alcuni potrebbero essere duplicati.


<a name="ImplementingEvaluation"></a>
### Procedura per ottenere valutazioni offline ###
Le metriche offline di precisione e diversità possono essere utili nella scelta della compilazione da usare. Per ottenere metriche offline è necessario eseguire queste operazioni:

In fase di compilazione nell'ambito dei parametri per la compilazione FBT o di elementi consigliati:
1.	Impostare il parametro di compilazione enableModelingInsights su true.

2.	È possibile selezionare facoltativamente il parametro *splitterStrategy*, ovvero *RandomSplitter* o *LastEventSplitter*. *RandomSplitter* suddivide i dati di utilizzo in set di training e set di test basati sui valori percentuali e di seme casuali *randomSplitterParameters* specificati per il test. *LastEventSplitter* suddivide i dati di utilizzo in set di training e set di test in base all'ultima transazione per ogni utente.

Verrà attivata una compilazione che usa solo un subset dei dati per il training; il resto dei dati viene usato per calcolare le metriche di valutazione. Dopo aver completato la compilazione, per ottenere l'output della valutazione è sufficiente chiamare [l'API Get build metrics](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/573e43bb3e9d4627a8c4bd3e/console) passando il rispettivo elemento *modelId* e *buildId*.

 Di seguito è riportato l'output JSON per la valutazione di esempio eseguita:


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

<!---HONumber=AcomDC_0601_2016-->