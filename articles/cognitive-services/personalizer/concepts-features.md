---
title: 'Caratteristiche: Azione e contesto - Personalizza esperienze'
titleSuffix: Azure Cognitive Services
description: Personalizza esperienze usa caratteristiche, ossia informazioni su azioni e contesto, per fornire suggerimenti più accurati per la classificazione. Le caratteristiche possono essere molto generiche o specifiche di un elemento.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: edjez
ms.openlocfilehash: c317cbec02b82743c233bf36f743cea808c30c69
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253594"
---
# <a name="features-are-information-about-actions-and-context"></a>Le caratteristiche sono informazioni su azioni e contesto

Il servizio Personalizza esperienze funziona identificando i contenuti che l'applicazione dovrà mostrare agli utenti in un determinato contesto.

Personalizza esperienze usa le **caratteristiche**, ossia informazioni sul **contesto corrente** per scegliere l'**azione** ottimale. Le caratteristiche rappresentano tutte le informazioni che si ritiene sarebbe utile personalizzare per ottenere ricompense più alte. Possono essere molto generiche o specifiche di un elemento. 

Ad esempio, una **caratteristica** potrebbe riguardare:

* L'_utente_, ad esempio un `UserID`. 
* Il _contenuto_, ad esempio se un video è di tipo `Documentary`, `Movie` o `TV Series` oppure se un articolo è o meno disponibile per la vendita nel negozio.
* Il periodo di tempo _corrente_, ad esempio il giorno della settimana.

Non prevede personalizer, limitare o correggere quali funzionalità è possibile inviare per azioni e il contesto:

* È possibile inviare alcune caratteristiche per alcune azioni e non per altre, se non sono disponibili. Ad esempio, una serie TV potrebbe avere attributi non presenti nei film.
* Alcune caratteristiche potrebbero essere disponibili solo alcune volte. Ad esempio, un'applicazione per dispositivi mobili potrebbe fornire più informazioni rispetto a una pagina Web. 
* Nel corso del tempo, è possibile aggiungere e rimuovere caratteristiche su contesto e azioni. Personalizza esperienze continua ad apprendere dalle informazioni disponibili.
* Il contesto deve includere almeno una caratteristica. Personalizza esperienze non supporta un contesto vuoto. Se ogni volta si invia solo un contesto fisso, Personalizza esperienze sceglierà l'azione solo per le classificazioni riguardanti le caratteristiche incluse nelle azioni. 
* Personalizza esperienze proverà a scegliere le azioni ottimali per tutti in qualsiasi momento.

## <a name="supported-feature-types"></a>Tipi di caratteristiche supportate

Personalizza esperienze supporta caratteristiche di tipo stringa, numerico e booleano.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Impatto di Machine Learning in Personalizer scelta del tipo di funzionalità

* **Le stringhe**: Per i tipi di stringa, ogni combinazione di chiave e valore Crea nuovo pesi in Personalizer modello di machine learning. 
* **Numerico**: Quando il numero in proporzione debba alterare il risultato di personalizzazione, è consigliabile usare i valori numerici. Si tratta di uno scenario dipendenti. Un esempio semplificato, ad esempio quando la personalizzazione delle vendite al dettaglio esperienza, NumberOfPetsOwned potrebbe essere una funzionalità che è di tipo numerica quando si considera le persone con 2 o 3 elementi Pet per influenzare il risultato di personalizzazione due volte o thrice quanto più verificati 1 pet. Le funzionalità che si basano sulle unità numeriche, ma in cui il significato non è lineare, ad esempio età, temperatura o persona Height - meglio vengono codificate come stringhe e la qualità di funzionalità in genere possono essere migliorate tramite intervalli. Ad esempio, età potrebbe essere codificato come "Age": "0-5", "Age": "6-10" e così via.
* **Booleano** inviati con valore "false" agire come se non era stato inviati a tutti i valori.

Le caratteristiche non presenti dovranno essere omesse dalla richiesta. Evitare di inviare caratteristiche con un valore Null, perché verrà elaborato come esistente e con un valore "null" quando si esegue il training del modello.

## <a name="categorize-features-with-namespaces"></a>Classificare le caratteristiche con gli spazi dei nomi

Personalizza esperienze accetta caratteristiche organizzate in spazi dei nomi. È necessario stabilire nell'applicazione se usare gli spazi dei nomi e in tal caso quali dovranno essere. Gli spazi dei nomi vengono usati per raggruppare le caratteristiche su un argomento simile oppure quelle provenienti da una determinata origine.

Di seguito sono riportati esempi di spazi dei nomi di caratteristiche usati dalle applicazioni:

* User_Profile_from_CRM
* Time
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Weather
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

È possibile assegnare nomi agli spazi dei nomi di caratteristiche in base alle proprie convenzioni, purché siano chiavi JSON valide. Gli spazi dei nomi vengono usati per organizzare le funzionalità in set distinti e per evitare ambiguità tra funzioni con nomi simili. È possibile considerare gli spazi dei nomi come "prefisso" che viene aggiunto ai nomi di funzionalità. Gli spazi dei nomi non possono essere annidate.


Nel codice JSON seguente `user`, `state` e `device` sono spazi dei nomi di caratteristiche. Nota di anteprima pubblica: Attualmente è fortemente consigliabile usare i nomi degli spazi dei nomi di funzionalità che sono basati su UTF-8 e iniziare con lettere diverse. Ad esempio, `user`, `state`, e `device` iniziano con `u`, `s`, e `d`. Dispone attualmente di spazi dei nomi con gli stessi caratteri prima potrebbe causare conflitti negli indici usati per machine learning.

Gli oggetti JSON annidati e proprietà/valori semplici, possono includere oggetti JSON. Una matrice può essere inclusa solo se gli elementi della matrice sono numeri. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": [47.6, -122.1]
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Come rendere più efficaci i set di caratteristiche per Personalizza esperienze

Con un set di caratteristiche efficace, Personalizza esperienze sarà in grado di prevedere l'azione che genererà la massima ricompensa. 

È consigliabile inviare all'API Personalizza esperienze caratteristiche che seguono queste raccomandazioni:

* Il numero di caratteristiche disponibili è sufficiente per la personalizzazione. Quanto più accuratamente mirato deve risultare il contenuto, tante più caratteristiche sono necessarie.

* Sono disponibili caratteristiche di *densità* diverse. Una caratteristica si considera *densa* se include molti elementi raggruppati in pochi bucket. Ad esempio, è possibile classificare migliaia di video come "Lunghi" (più di 5 minuti di durata) e "Brevi" (meno di 5 minuti di durata). Questa caratteristica è *molto densa*. D'altra parte, le stesse migliaia di elementi possono avere un attributo "Titolo", che non avrà quasi mai lo stesso valore da un elemento a un altro. Questa caratteristica è non densa, ovvero *diradata*.  

Con le caratteristiche ad alta densità, Personalizza esperienze è in grado di estrapolare l'apprendimento da un elemento a un altro. Ma nel caso di poche caratteristiche troppo dense, Personalizza esperienze proverà a definire contenuto accuratamente mirato solo con pochi bucket tra cui scegliere.

### <a name="improve-feature-sets"></a>Migliorare i set di caratteristiche 

Analizzare il comportamento degli utenti eseguendo una valutazione offline. In questo modo è possibile esaminare i dati passati per verificare quali sono le caratteristiche che contribuiscono maggiormente a ricompense positive rispetto a quelle che contribuiscono di meno. È possibile vedere quali caratteristiche risultano utili e sarà quindi necessario trovare quelle più efficaci da inviare a Personalizza esperienze per migliorare ulteriormente i risultati.

Le sezioni seguenti descrivono le procedure comuni per migliorare i set di caratteristiche inviati a Personalizza esperienze.

#### <a name="make-features-more-dense"></a>Aumentare la densità delle caratteristiche

È possibile migliorare i set di caratteristiche modificandoli in modo da renderli più grandi e più o meno densi.

Ad esempio, un timestamp misurato in secondi è una caratteristica molto diradata. Per renderla più densa, e quindi efficace, è possibile classificare il tempo suddividendolo in "mattina", "mezzogiorno", "pomeriggio" e così via.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Espandere i set di caratteristiche con le informazioni estrapolate

Per aumentare il numero di caratteristiche è anche possibile valutare gli attributi inesplorati derivabili dalle informazioni già disponibili. Ad esempio, in una personalizzazione all'elenco fittizia movie, è possibile che un giorno feriale e festivo determinato da un comportamento diverso dagli utenti? La caratteristica temporale potrebbe essere espansa per includere un attributo "weekend" o "giorno feriale". Le festività nazionali di tipo culturale attirano l'attenzione verso determinati tipi di film? Ad esempio, l'attributo "Halloween" è utile nelle località in cui è pertinente. È possibile che per molte persone la pioggia abbia un impatto significativo sulla scelta del film da guardare? Con una data e un luogo, un servizio meteo potrebbe fornire questa informazione ed è possibile includerla come caratteristica aggiuntiva. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Espandere i set di caratteristiche con l'intelligenza artificiale e i servizi cognitivi

L'intelligenza artificiale e i servizi cognitivi pronti per l'esecuzione possono rivelarsi particolarmente efficaci in aggiunta a Personalizza esperienze. 

Pre-elaborando gli elementi con i servizi di intelligenza artificiale, è possibile estrarre automaticamente le informazioni che saranno probabilmente più pertinenti per la personalizzazione.

Ad esempio:

* È possibile eseguire il file di un film tramite [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) per estrarre elementi delle scene, testo, sentiment e molti altri attributi. Questi attributi possono quindi essere resi più densi in base a caratteristiche non presenti nei metadati degli elementi originali. 
* È possibile eseguire immagini tramite rilevamento di oggetti, visi tramite sentiment e così via.
* Le informazioni nel testo possono essere aumentate estraendo entità e sentiment, espandendo le entità con Knowledge Graph di Bing e così via.

È possibile usare diversi altri [Servizi cognitivi di Azure](https://www.microsoft.com/cognitive-services), come

* [Collegamento delle entità](../entitylinking/home.md)
* [Analisi del testo](../text-analytics/overview.md)
* [Emozioni](../emotion/home.md)
* [Visione artificiale](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Le azioni rappresentano un elenco di opzioni

Ogni azione:

* È associata a un ID.
* Include un elenco di caratteristiche.
* L'elenco di caratteristiche può essere lungo (centinaia di voci) ma è consigliabile valutarne l'efficacia per rimuovere quelle che non contribuiscono a ottenere ricompense. 
* Le caratteristiche per le **azioni** possono avere o meno una correlazione con le caratteristiche per il **contesto** usate da Personalizza esperienze.
* Le caratteristiche per le azioni possono essere presenti in alcune azioni e non in altre. 
* Le caratteristiche per un determinato ID azione possono essere disponibili un giorno ma diventare indisponibili in seguito. 

Gli algoritmi di Machine Learning di Personalizza esperienze offriranno prestazioni migliori con i set di caratteristiche stabili, ma le chiamate all'API Classifica non avranno esito negativo se il set di caratteristiche cambia nel corso del tempo.

Non inviare più di 50 azioni per la classificazione delle azioni. Può trattarsi ogni volta delle stesse 50 azioni oppure di altre. Nel caso ad esempio di un catalogo di prodotti con 10.000 voci per un'applicazione di e-commerce, è possibile usare una raccomandazione o un motore di filtro per determinare i primi 40 prodotti che possono piacere ai clienti e usare Personalizza esperienze per trovare quello che genererà la massima ricompensa, ad esempio quello che l'utente aggiungerà al carrello, per il contesto corrente.


### <a name="examples-of-actions"></a>Esempi di azioni

Le azioni da inviare all'API Classifica cambiano in base a quello che si prova a personalizzare.

Ecco alcuni esempi:

|Scopo|Azione|
|--|--|
|Personalizzare l'articolo da evidenziare in un sito Web di notizie.|Ogni azione è un potenziale articolo di notizie.|
|Ottimizzare il posizionamento degli annunci in un sito Web.|Ogni azione sarà costituita da un layout o da regole per creare un layout per gli annunci (ad esempio in alto, a destra, immagini piccole, immagini grandi).|
|Visualizzare una classifica personalizzata di articoli consigliati in un sito Web di acquisti.|Ogni azione corrisponde a un prodotto specifico.|
|Suggerire elementi dell'interfaccia utente, come i filtri da applicare a una specifica foto.|Ogni azione può essere un filtro diverso.|
|Scegliere la risposta di un chatbot per chiarire le finalità dell'utente o suggerire un'azione.|Ogni azione corrisponde a un'opzione su come interpretare la risposta.|
|Scegliere la voce da visualizzare all'inizio di un elenco di risultati della ricerca|Ogni azione corrisponde a uno dei primi risultati della ricerca.|


### <a name="examples-of-features-for-actions"></a>Esempi di caratteristiche per le azioni

Di seguito sono riportati alcuni esempi validi di caratteristiche per le azioni, che variano notevolmente in base a ogni applicazione.

* Caratteristiche con attributi delle azioni. Ad esempio, si tratta di un film o di una serie TV?
* Caratteristiche riguardanti il modo in cui gli utenti possono aver interagito con l'azione nel passato. Ad esempio, il film viene visto prevalentemente da persone che rientrano nel livello demografico A o B e non viene solitamente riprodotto più di una volta.
* Caratteristiche che riguardano come vengono *viste* le azioni dall'utente. Ad esempio, il poster del film mostrato nell'anteprima include volti, automobili o panorami?

### <a name="load-actions-from-the-client-application"></a>Caricare azioni dall'applicazione client

Le caratteristiche delle azioni derivano in genere da sistemi di gestione di contenuti, cataloghi e sistemi di raccomandazioni. L'applicazione è responsabile del caricamento delle informazioni sulle azioni dai database e dai sistemi pertinenti disponibili. Se le azioni non cambiano o se il fatto di caricarle ogni volta influisce inutilmente sulle prestazioni, è possibile aggiungere logica nell'applicazione per memorizzare queste informazioni nella cache.

### <a name="prevent-actions-from-being-ranked"></a>Impedire la classificazione delle azioni

In alcuni casi è possibile che non si vogliano mostrare determinate azioni agli utenti. Il modo migliore per evitare che un'azione venga classificata come più importante consiste nel non includerla affatto nell'elenco di azioni dell'API Classifica.

In alcuni casi, è possibile determinare solo in seguito nella logica di business se mostrare o meno a un utente un'_azione_ risultante da una chiamata all'API Classifica. In questi casi, è consigliabile usare _eventi inattivi_.

## <a name="json-format-for-actions"></a>Formato JSON per le azioni

Quando si effettua una chiamata all'API Classifica, si inviano più azioni tra cui scegliere:

Gli oggetti JSON annidati e proprietà/valori semplici, possono includere oggetti JSON. Una matrice può essere inclusa solo se gli elementi della matrice sono numeri. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Esempi di informazioni per il contesto

Le informazioni per il _contesto_ variano in base all'applicazione e al caso d'uso, ma in genere possono includere, ad esempio:

* Informazioni demografiche e sul profilo dell'utente.
* Informazioni estratte da intestazioni HTTP, come l'agente utente, o derivate da informazioni HTTP, come le ricerche geografiche inverse basate su indirizzi IP.
* Informazioni sulla data e l'ora correnti, ad esempio il giorno della settimana, se si tratta di giorno feriale o no, mattina o pomeriggio, periodo festivo o no e così via.
* Informazioni estratte da applicazioni per dispositivi mobili, ad esempio posizione, movimento o livello della batteria.
* Dati aggregati cronologici del comportamento degli utenti, ad esempio quali generi di film hanno guardato più spesso.

L'applicazione è responsabile del caricamento delle informazioni sul contesto dai database, dai sensori e dai sistemi pertinenti disponibili. Se le informazioni sul contesto non cambiano, è possibile aggiungere logica nell'applicazione per memorizzarle nella cache prima di inviarle all'API Classifica.

## <a name="json-format-for-context"></a>Formato JSON per il contesto 

Il contesto viene espresso come oggetto JSON inviato all'API Classifica:

Gli oggetti JSON annidati e proprietà/valori semplici, possono includere oggetti JSON. Una matrice può essere inclusa solo se gli elementi della matrice sono numeri. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Passaggi successivi

[Apprendimento per rinforzo](concepts-reinforcement-learning.md) 
