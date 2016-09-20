<properties
	pageTitle="Raccolta dei dati per eseguire il training del modello: API Recommendations di Machine Learning | Microsoft Azure"
	description="Raccomandazioni per il Machine Learning di Azure: Raccolta dei dati per eseguire il training del modello"
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
	ms.date="09/06/2016"
	ms.author="luisca"/>

#  Raccolta dei dati per eseguire il training del modello #

API Recommendations apprende dalle transazioni precedenti per individuare quali elementi devono essere consigliati a un determinato utente.

Dopo aver creato un modello, è necessario fornire due informazioni prima di eseguire le azioni di formazione: un file di catalogo e i dati di utilizzo.

>   Se non è già stato fatto, è consigliabile completare la [Guida introduttiva](cognitive-services-recommendations-quick-start.md).


## Dati del catalogo ##

### Formato del file di catalogo ###

Il file di catalogo contiene informazioni sugli elementi che si offrono al cliente. I dati del catalogo devono seguire il formato seguente:

- Senza funzionalità: `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Con funzionalità: `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### Righe di esempio in un file di catalogo

Senza funzionalità:

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Con funzionalità:

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### Dettagli relativi al formato

| Name | Mandatory | Tipo | Descrizione |
|:---|:---|:---|:---|
| Item Id |Sì | [A-z], [a-z], [0-9], [\_] &#40;carattere di sottolineatura&#41;, [-] &#40;trattino&#41;<br> Lunghezza massima: 50 | Identificatore univoco di un elemento. |
| Item Name | Sì | Qualsiasi carattere alfanumerico<br> Lunghezza massima: 255 | Nome dell'elemento. |
| Item Category | Sì | Qualsiasi carattere alfanumerico <br> Lunghezza massima: 255 | Categoria alla quale appartiene l'elemento (ad esempio, libri di cucina, letteratura e così via); può essere vuoto. |
| Descrizione | No, a meno che siano presenti funzionalità (può essere vuoto) | Qualsiasi carattere alfanumerico<br> Lunghezza massima: 4000 | Descrizione dell'elemento. |
| Elenco di funzionalità | No | Qualsiasi carattere alfanumerico <br> Lunghezza massima: 4000, numero massimo di funzionalità: 20 | Elenco delimitato da virgole di nome funzionalità=valore funzionalità che è possibile usare per migliorare il modello di raccomandazione.|

#### Caricamento di un file di catalogo

Per caricare un file di catalogo, vedere il [riferimento all'API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1).

Si noti che il contenuto del file di catalogo deve essere passato come corpo della richiesta.

Se si caricano diversi file del catalogo nello stesso modello con diverse chiamate, verranno inseriti solo i nuovi elementi del catalogo. Gli elementi esistenti manterranno i valori originali. Non è possibile aggiornare i dati del catalogo con questo metodo.

>   Nota: le dimensioni massime del file sono pari a 200 MB. Il numero massimo di elementi supportato nel catalogo è 100.000.


## Motivazioni dell'aggiunta di funzionalità al catalogo

Il motore di raccomandazioni crea un modello statistico che indica quali elementi potrebbero essere graditi o acquistati da un cliente. Quando si dispone di un nuovo prodotto senza alcuna interazione, non è possibile creare un modello basato esclusivamente su co-occorrenze. Si supponga che nel negozio si avvia una nuova offerta di "violini per bambini". Dal momento che non sono mai stati venduti violini nel passato, non è possibile stabilire quali altri elementi consigliare con il violino.

Ciò vuol dire che, se il motore dispone di informazioni sul violino, ad esempio che è un strumento musicale, che è destinato ai bambini di età compresa tra 7 e 10 anni, che non è costoso e così via, è in grado di apprendere da altri prodotti con caratteristiche simili. Ad esempio, il violino è stato venduto nel passato e i clienti che hanno comprato il violino mostravano propensione nell'acquisto di CD di musica classica e leggii per spartiti. Il sistema è in grado di individuare le connessioni tra le caratteristiche e fornire consigli basati su esse, anche se il nuovo violino messo in vendita registra dati di utilizzo scarsi.

Le funzionalità vengono importate come parte dei dati del catalogo e quindi la relativa classificazione (o l'importanza della funzionalità nel modello) viene associata durante una compilazione della classifica. La classificazione delle funzionalità può cambiare in base al modello di dati di utilizzo e al tipo di elementi. Per la coerenza dei dati di utilizzo e degli elementi, è opportuno che le fluttuazioni siano limitate. La classificazione delle funzionalità è espressa mediante un numero non negativo. Il numero 0 significa che la funzionalità non è stata classificata, ad esempio nel caso il cui l'API venga richiamata prima che sia completata la prima compilazione della classifica. La data in cui è stata attribuita la classificazione è detta aggiornamento del punteggio.


###Le funzionalità sono di tipo categorico

Ciò significa che è necessario creare funzionalità simili a una categoria. Ad esempio, price=9.34 non è una funzionalità categorica, mentre una funzionalità come priceRange=Under5Dollars lo è. Un altro errore comune consiste nell'usare il nome dell'elemento come funzionalità. Così facendo, il nome di un elemento diventa univoco e non descrive una categoria. Assicurarsi che le funzionalità rappresentino categorie di elementi.


###Quante e quali funzionalità è necessario usare


In definitiva, la build Raccomandazioni supporta la creazione di un modello con un massimo di 20 funzionalità. È possibile assegnare più di 20 funzionalità agli elementi del catalogo, ma occorrerà eseguire una compilazione di classificazione, selezionando solo le funzionalità con una classificazione elevata. Una funzionalità con una classificazione pari o superiore a 2.0 è da considerare valida.


###Quando vengono effettivamente usate le funzionalità

Le funzionalità vengono usate dal modello quando non sono disponibili dati di transazione sufficienti per fornire raccomandazioni solo sulle informazioni relative alla transazione. Le funzionalità avranno quindi il maggiore impatto sugli "elementi ad accesso sporadico", ovvero gli elementi con poche transazioni. Se per tutti gli elementi sono disponibili informazioni sulla transazione sufficienti, potrebbe non essere necessario aggiungere funzionalità al modello.


###Uso delle funzionalità del prodotto

Per usare le funzionalità come parte della compilazione è necessario:

1. Assicurarsi che il catalogo includa le funzionalità quando viene caricato.

2. Attivare la compilazione della classifica. L'analisi sarà eseguita in base all'importanza/classifica delle funzionalità.

3. Attivare una compilazione di elementi consigliati, impostando i parametri di compilazione seguenti: impostare useFeaturesInModel su true, allowColdItemPlacement su true e modelingFeatureList su un elenco delimitato da virgole delle caratteristiche che si desidera usare per migliorare il modello. Per altre informazioni, vedere i [parametri per il tipo di compilazione di elementi consigliati](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0).





## Dati di utilizzo ##
Un file di dati utilizzo contiene informazioni sulle modalità d'uso degli elementi o sulle transazioni dell'azienda.

#### Dettagli relativi al formato del file di dati di utilizzo
Il file di dati di utilizzo presenta il formato CSV (valori delimitati da virgola) in cui ogni riga del file rappresenta un'interazione tra un utente e un elemento. Ogni riga è formattata come illustrato di seguito:<br> `<User Id>,<Item Id>,<Time>,[<Event>]`



| Nome | Mandatory | Tipo | Descrizione
|-------|------------|------|---------------
|User Id| Sì|[A-z], [a-z], [0-9], [\_] &#40;carattere di sottolineatura&#41;, [-] &#40;trattino&#41;<br> Lunghezza massima: 255 |Identificatore univoco di un utente.
|Item Id|Sì|[A-z], [a-z], [0-9], [&#95;] &#40;carattere di sottolineatura&#41;, [-] &#40;trattino&#41;<br> Lunghezza massima: 50|Identificatore univoco di un elemento.
|Time|Sì|Data in formato: AAAA/MM/GGTHH:MM:SS (ad esempio 2013/06/20T10:00:00)|Ora dei dati.
|Evento|No | Uno dei seguenti:<br>• Clic<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Acquisto| Tipo di transazione. |

#### Righe di esempio in un file di dati di utilizzo

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### Caricamento di un file di dati di utilizzo

Per caricare file di dati di utilizzo, vedere il [riferimento all'API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2). Si noti che è necessario passare il contenuto del file di dati di utilizzo come corpo della chiamata HTTP.

>  Note:

>  Dimensione massima file: 200 MB. È possibile caricare diversi file di dati di utilizzo.

>  Prima di iniziare ad aggiungere dati di utilizzo al modello, è necessario caricare un file di catalogo. Solo gli elementi nel file di catalogo saranno usati durante la fase di formazione. Tutti gli altri elementi verranno ignorati.

## Quanti dati sono necessari?

La qualità del modello dipende fortemente dalla qualità e dalla quantità dei dati. Il sistema apprende quando gli utenti acquistano elementi diversi (denominiamoli co-occorrenze). Per le compilazioni FBT, è importante anche sapere quali elementi vengono acquistati nelle stesse transazioni.

Una buona regola consiste nella distribuzione della maggior parte degli elementi in almeno 20 transazioni, pertanto se si dispone di 10.000 elementi nel catalogo, è consigliabile disporre di almeno 20 volte il numero di transazioni o circa 200.000 transazioni. Si tratta comunque di una regola generale. È necessario provare a utilizzare i dati.

Dopo aver creato un modello, è possibile eseguire una [valutazione offline](cognitive-services-recommendations-buildtypes.md) per controllare il potenziale andamento del modello.

<!---HONumber=AcomDC_0907_2016-->