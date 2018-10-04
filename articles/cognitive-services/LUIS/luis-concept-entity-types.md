---
title: Tipi di entità in app LUIS - Language Understanding
titleSuffix: Azure Cognitive Services
description: Aggiungere le entità (dati chiave nel dominio dell'applicazione) nelle app Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 3ed10ac428b7ce2e528ccf46e34c1d394523bdec
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042449"
---
# <a name="entities-in-luis"></a>Entità in LUIS

Le entità sono parole o frasi nelle espressioni che rappresentano dati chiave nel dominio dell'applicazione.

## <a name="entity-compared-to-intent"></a>Entità e finalità
L'entità rappresenta una parola o una frase all'interno dell'espressione che si intende estrarre. Un'espressione può includere molte entità oppure nessuna. Un'entità rappresenta una classe che include una raccolta di oggetti simili (luoghi, cose, persone, eventi o concetti). Le entità descrivono le informazioni rilevanti per la finalità e sono talvolta essenziali affinché l'app svolga la sua attività. Ad esempio, un'app di ricerca di notizie potrebbe includere entità come "topic", "source", "keyword" e "publishing date", ovvero i dati chiave per la ricerca delle notizie. In un'app per la prenotazione di viaggi, "location", "date", "airline", "travel class" e "tickets" sono informazioni chiave per la prenotazione dei voli (rilevanti per la finalità "Bookflight").

La finalità rappresenta invece la previsione dell'intera espressione. 

## <a name="entities-represent-data"></a>Le entità rappresentano dati
Le entità sono dati di cui si intende eseguire il pull dall'espressione. Può trattarsi di un nome, di una data, del nome di un prodotto o di un qualsiasi gruppo di parole. 

|Espressione|Entità|Dati|
|--|--|--|
|Buy 3 tickets to New York|Number predefinito<br>Location.Destination|3<br>New York|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>DatetimeV2 predefinito|New York<br>Londra<br>March 5, 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>Le entità sono facoltative ma consigliate
A differenza delle finalità che sono obbligatorie, le entità sono facoltative. Non è necessario creare entità per ogni concetto nell'app, ma solo per quelli necessari all'app per eseguire un'azione. 

Se le espressioni non contengono dettagli che il bot deve continuare, non è necessario aggiungerle. Man mano che l'app evolve, sarà possibile aggiungerle in un secondo momento. 

In caso di dubbi su come usare le informazioni, aggiungere alcune entità predefinite comuni, ad esempio datetimeV2, ordinal, email e phone number.

## <a name="label-for-word-meaning"></a>Assegnare etichette per il significato delle parole
Se la scelta o la disposizione della parola è la stessa ma il significato è diverso, non etichettarla con le entità. 

Nelle espressioni seguenti, la parola `fair` è un omografo. L'ortografia è la stessa ma il significato è diverso:

|Espressione|
|--|
|Quali sono le fiere in programma nell'area di Seattle la prossima estate?|
|L'attuale classificazione per la recensione di Seattle è valida?|

Se si desidera che un'entità di evento trovi tutti i dati dell'evento, etichettare la parola `fair` nella prima espressione, ma non nella seconda.

## <a name="entities-are-shared-across-intents"></a>Le entità vengono condivise tra tutte le finalità
Le entità vengono condivise tra le finalità. Non appartengono a una singola finalità. Finalità ed entità possono essere associate semanticamente, ma non si tratta di una relazione esclusiva.

Nell'espressione "Book me a ticket to Paris", "Paris" è un'entità di tipo location. Attraverso il riconoscimento delle entità che vengono menzionate nell'input dell'utente, LUIS aiuta a scegliere le azioni specifiche da eseguire per soddisfare una finalità.

## <a name="assign-entities-in-none-intent"></a>Assegnare le entità nella finalità None
Tutte le finalità, inclusa la finalità **None**, devono avere entità etichettate. Ciò consente a LUIS di apprendere maggiori informazioni sulla posizione in cui si trovano le entità nell'espressione e su quali parole circondano le entità. 

## <a name="types-of-entities"></a>Tipi di entità
LUIS offre molti tipi di entità: entità predefinite, entità basate su Machine Learning personalizzate ed entità elenco.

| NOME | Possibilità di etichetta | DESCRIZIONE |
| -- |--|--|
| **Predefinita** <br/>[Impostazione personalizzata](#prebuilt)| |  **Definizione**<br>Tipi predefiniti che rappresentano concetti comuni. <br><br>**Elenco**<br/>key phrase number, ordinal, temperature, dimension, money, age, percentage, email, URL, phone number e key phrase. <br><br>I nomi delle entità predefinite sono riservati. <br><br>Tutte le entità predefinite che vengono aggiunte all'applicazione vengono restituite nella query [endpoint](luis-glossary.md#endpoint). Per altre informazioni, vedere [Entità predefinite](./luis-prebuilt-entities.md). <br/><br/>[Risposta di esempio per l'entità](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 -->**Espressione regolare**<br/>[RegEx](#regex)||**Definizione**<br>Espressione regolare personalizzata per il testo dell'espressione non elaborata formattato. Maiuscole e minuscole vengono ignorate così come la variante relativa alla lingua.  <br><br>Questa entità è valida per le parole o le frasi che sono formattate in modo coerente con qualsiasi variazione altrettanto coerente.<br><br>La corrispondenza tramite le espressioni regolari viene applicata dopo le modifiche del controllo ortografico. <br><br>Se l'espressione regolare è troppo complessa, ad esempio con molte parentesi, non è possibile aggiungere l'espressione al modello. <br><br>**Esempio**<br>`kb[0-9]{6,}` corrisponde a kb123456.<br/><br/>[Guida introduttiva](luis-quickstart-intents-regex-entity.md)<br>[Risposta di esempio per l'entità](luis-concept-data-extraction.md)|
| **Simple** <br/>[Basata su Machine Learning](#machine-learned) | ✔ | **Definizione**<br>Un'entità semplice è un'entità generica che descrive un singolo concetto e che viene appresa dal contesto basato su Machine Learning. Il contesto include scelta e posizione delle parole e lunghezza dell'espressione.<br/><br/>Questa entità è valida per le parole o le frasi che non sono formattate in modo coerente ma che indicano la stessa cosa. <br/><br/>[Guida introduttiva](luis-quickstart-primary-and-secondary-data.md)<br/>[Risposta di esempio per l'entità](luis-concept-data-extraction.md#simple-entity-data)|  
| **Elenco** <br/>[Corrispondenza esatta](#exact-match)|| **Definizione**<br>Le entità elenco rappresentano un set chiuso e fisso di parole correlate insieme ai relativi sinonimi nel sistema. <br><br>Ogni entità elenco può avere uno o più moduli. Sono particolarmente adatte per un set noto di varianti per rappresentare lo stesso concetto.<br/><br/>LUIS non individua valori aggiuntivi per le entità elenco. Usare la funzione **consigliata** per visualizzare i suggerimenti per le nuove parole in base all'elenco corrente.<br/><br>Se sono presenti più entità elenco con lo stesso valore, ogni entità viene restituita nella query endpoint. <br/><br/>[Guida introduttiva](luis-quickstart-intent-and-list-entity.md)<br>[Risposta di esempio per l'entità](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[Mista](#mixed) | ✔|**Definizione**<br>Patterns.any è un segnaposto di lunghezza variabile usato solo nell'espressione di modello del criterio per contrassegnare l'inizio e la fine dell'entità.  <br><br>**Esempio**<br>Data un'espressione per la ricerca di libri in base al titolo, pattern.any estrae il titolo completo. Un'espressione di modello con pattern.any è `Who wrote {BookTitle}[?]`.<br/><br/>[Esercitazione](luis-tutorial-pattern.md)<br>[Risposta di esempio per l'entità](luis-concept-data-extraction.md#composite-entity-data)|  
| **Composita** <br/>[Basata su Machine Learning](#machine-learned) | ✔|**Definizione**<br>Un'entità composita è costituita da altre entità, ad esempio entità predefinite, semplici, espressione regolare, elenco, gerarchiche. Le entità separate formano un'entità intera. <br><br>**Esempio**<br>Un'entità composita denominata PlaneTicketOrder potrebbe avere le entità figlio predefinite `number` e `ToLocation`. <br/><br/>[Esercitazione](luis-tutorial-composite-entity.md)<br>[Risposta di esempio per l'entità](luis-concept-data-extraction.md#composite-entity-data)|  
| **Gerarchica** <br/>[Basata su Machine Learning](#machine-learned) |✔ | **Definizione**<br>Un'entità gerarchica è una categoria di entità semplici con apprendimento su base contestuale.<br><br>**Esempio**<br>Data un'entità gerarchica di `Location` con figli `ToLocation` e `FromLocation`, ogni figlio può essere determinato in base al **contesto** all'interno dell'espressione. Nell'espressione `Book 2 tickets from Seattle to New York`, le entità `ToLocation` e `FromLocation` sono contestualmente diverse in base alle parole circostanti. <br/><br/>**Non usare se**<br>Se si sta cercando un'entità che abbia corrispondenze di testo esatte per i figli indipendentemente dal contesto, è consigliabile usare un'entità elenco. Se si sta cercando una relazione padre-figlio con altri tipi di entità, è consigliabile usare un'entità composita.<br/><br/>[Guida introduttiva](luis-quickstart-intent-and-hier-entity.md)<br>[Risposta di esempio per l'entità](luis-concept-data-extraction.md#hierarchical-entity-data)|

Le entità <a name="prebuilt"></a>
**predefinite** sono entità personalizzate fornite da LUIS. Alcune di esse sono definite nel progetto open source [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). Sono disponibili numerosi [esempi](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs) nella directory /Specs per le lingue supportate. Se una lingua o un'entità specifica non è attualmente supportata, è possibile collaborare al progetto. 

Le entità <a name="machine-learned"></a>
**basate su Machine Learning** funzionano meglio quando vengono testate tramite [query endpoint](luis-concept-test.md#endpoint-testing) e si [esaminano le espressioni endpoint](luis-how-to-review-endoint-utt.md). 

Le <a name="regex"></a>
**entità di espressione regolare** sono definite da un'espressione regolare fornita dall'utente come parte della definizione dell'entità. 

Le entità <a name="exact-match"></a>
**a corrispondenza esatta** usano il testo fornito nell'entità per trovare una corrispondenza di testo esatta.

Le entità <a name="mixed"></a>
**miste** usano una combinazione di metodi di rilevamento delle entità.

## <a name="entity-limits"></a>Limiti delle entità
Esaminare i [limiti](luis-boundaries.md#model-boundaries) per comprendere il numero di entità di ciascun tipo che è possibile aggiungere a un modello.

## <a name="entity-roles"></a>Ruoli entità
I [ruoli](luis-concept-roles.md) entità vengono usati solo nei criteri. 

## <a name="composite-vs-hierarchical-entities"></a>Entità composite e gerarchiche
Sia le entità composite che le entità gerarchiche hanno relazioni padre-figlio e sono basate su Machine Learning. L'apprendimento basato su Machine Learning consente a LUIS di comprendere le entità in base ai diversi contesti (disposizione delle parole). Le entità composite sono più flessibili perché consentono tipi di entità diversi come figli. I figli delle entità gerarchiche sono solo entità semplici. 

|type|Scopo|Esempio|
|--|--|--|
|Gerarchica|Padre-figlio di entità semplici|Location.Origin=New York<br>Location.Destination=London|
|Composita|Entità padre-figlio: predefinite, elenco, semplici, gerarchiche| number=3<br>list=first class<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>Dati corrispondenti a più entità
Se una parola o frase corrisponde a più entità, la query endpoint restituisce ciascuna entità. Se si aggiungono sia l'entità number predefinita che l'entità datetimeV2 predefinita e si ha un'espressione `create meeting on 2018/03/12 for lunch with wayne`, LUIS riconosce tutte le entità e restituisce una matrice di entità come parte della risposta dell'endpoint JSON: 

```JSON
{
  "query": "create meeting on 2018/03/12 for lunch with wayne",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.9333419
  },
  "entities": [
    {
      "entity": "2018/03/12",
      "type": "builtin.datetimeV2.date",
      "startIndex": 18,
      "endIndex": 27,
      "resolution": {
        "values": [
          {
            "timex": "2018-03-12",
            "type": "date",
            "value": "2018-03-12"
          }
        ]
      }
    },
    {
      "entity": "2018",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 21,
      "resolution": {
        "value": "2018"
      }
    },
    {
      "entity": "03/12",
      "type": "builtin.number",
      "startIndex": 23,
      "endIndex": 27,
      "resolution": {
        "value": "0.25"
      }
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>Dati corrispondenti a più entità elenco
Se una parola o frase corrisponde a più entità elenco, la query di endpoint restituisce ciascuna entità elenco.

Per la query `when is the best time to go to red rock?`, se l'app contiene la parola `red` in più di un elenco, LUIS riconosce tutte le entità e restituisce una matrice di entità come parte della risposta dell'endpoint JSON: 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Se sono necessarie più entità rispetto al numero massimo 

Potrebbe essere necessario usare entità gerarchiche e composite. Le entità gerarchiche riflettono la relazione tra le entità che condividono caratteristiche o che sono membri di una categoria. Le entità figlio sono tutti i membri della categoria del relativo padre. Ad esempio, un'entità gerarchica denominata PlaneTicketClass potrebbe presentare le entità figlio EconomyClass e FirstClass. La gerarchia si estende a un solo livello di profondità.  

Le entità composite rappresentano parti di un intero. Ad esempio, un'entità composita denominata PlaneTicketOrder potrebbe presentare le entità figlio Airline, Destination, DepartureCity, DepartureDate e PlaneTicketClass. Si crea un'entità composita da entità semplici preesistenti, da figli di entità gerarchiche o da entità predefinite.  

LUIS fornisce anche il tipo di entità elenco che non è basato su Machine Learning ma che consente all'app LUIS di specificare un elenco fisso di valori. Vedere il riferimento [Limiti di LUIS](luis-boundaries.md) per esaminare i limiti del tipo di entità elenco. 

Se sono state considerate le entità gerarchiche, composite ed elenco e comunque ne occorrono altre in numero superiore al limite, contattare il supporto tecnico. A tale scopo, raccogliere informazioni dettagliate relative al sistema, visitare il sito Web [LUIS](luis-reference-regions.md#luis-website) e quindi selezionare l'opzione relativa al **supporto**. Se la sottoscrizione di Azure include servizi di assistenza, contattare [il team di supporto di Azure](https://azure.microsoft.com/support/options/). 

## <a name="best-practices"></a>Procedure consigliate

Creare un'[entità](luis-concept-entity-types.md) quando l'applicazione chiamante o il bot richiede alcuni parametri o dati dell'espressione per eseguire un'azione. Un'entità è una parola o frase nell'espressione che occorre estrarre, probabilmente come parametro per una funzione. 

Per selezionare il tipo di entità corretto da aggiungere all'applicazione, è necessario conoscere il modo in cui gli utenti immettono i dati. Ogni tipo di entità viene trovato tramite un meccanismo diverso, ad esempio Machine Learning, elenco chiuso o espressione regolare. In caso di dubbi, iniziare con un'entità semplice ed etichettare la parola o frase che rappresenta i dati in tutte le espressioni, per tutte le finalità, inclusa la finalità None.  

Esaminare le espressioni endpoint a intervalli regolari per trovare un utilizzo comune laddove un'entità può essere identificata come un'espressione regolare o trovata con una corrispondenza di testo esatta.  

Come parte della revisione, considerare la possibilità di aggiungere un elenco di frasi per segnalare a LUIS parole o frasi significative per il dominio ma che non rappresentano corrispondenze esatte e per cui LUIS non ha un elevato livello di attendibilità.  

Per altre informazioni, vedere [Procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui concetti delle [espressioni](luis-concept-utterance.md) valide. 

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per altre informazioni sull'aggiunta di entità all'app LUIS.