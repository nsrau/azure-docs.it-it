---
title: Concetti e flusso di lavoro dei set di competenze
titleSuffix: Azure Cognitive Search
description: È nei set di competenze che si crea una pipeline di arricchimento tramite intelligenza artificiale in Ricerca cognitiva di Azure. Sono disponibili concetti e dettagli importanti sulla composizione di set di competenze.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: f1d8715fcadeda5ccd1a98192a70939b0c359c88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84976677"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Concetti relativi alle competenze in Azure ricerca cognitiva

Questo articolo è rivolto agli sviluppatori che necessitano di una conoscenza più approfondita dei concetti e della composizione delle competenze e presuppone una certa familiarità con il processo di arricchimento di intelligenza artificiale. Se non si ha familiarità con questo concetto, iniziare con l' [arricchimento di intelligenza artificiale in Azure ricerca cognitiva](cognitive-search-concept-intro.md).

## <a name="introducing-skillsets"></a>Introduzione a skillsets

Un insieme di competenze è una risorsa riutilizzabile in Azure ricerca cognitiva collegata a un indicizzatore e specifica una raccolta di competenze utilizzate per analizzare, trasformare e arricchire il contenuto di testo o immagine durante l'indicizzazione. Le competenze hanno input e output e spesso l'output di un'abilità diventa l'input di un altro in una catena o una sequenza di processi.

Un skillt ha tre proprietà principali:

+ `skills`, una raccolta non ordinata di competenze per le quali la piattaforma determina la sequenza di esecuzione in base agli input necessari per ogni competenza.
+ `cognitiveServices`, la chiave di una risorsa Servizi cognitivi che esegue l'elaborazione di immagini e testo per skillsets che includono competenze predefinite.
+ `knowledgeStore`, (facoltativo) account di archiviazione di Azure in cui verranno proiettati i documenti arricchiti. I documenti arricchiti vengono utilizzati anche dagli indici di ricerca.

I set di competenze vengono creati in JSON. L'esempio seguente è una versione leggermente semplificata di questo [Hotel-revisioni di competenze](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json), usato per illustrare i concetti in questo articolo. 

Le prime due competenze sono illustrate di seguito:

+ Skill #1 è una [competenza di suddivisione del testo](cognitive-search-skill-textsplit.md) che accetta il contenuto del campo "reviews_text" come input e divide il contenuto in "pagine" di 5000 caratteri come output.
+ Skill #2 è un' [abilità di rilevamento dei sentimenti](cognitive-search-skill-sentiment.md) accetta "Pages" come input e produce un nuovo campo denominato "sentimento" come output che contiene i risultati dell'analisi dei sentimenti.


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> È possibile compilare skillsets complessi con ciclo e diramazioni, usando la [competenza condizionale](cognitive-search-skill-conditional.md) per creare le espressioni. La sintassi è basata sulla notazione del percorso del [puntatore JSON](https://tools.ietf.org/html/rfc6901) , con alcune modifiche per identificare i nodi nell'albero di arricchimento. Un elemento `"/"` attraversa un livello inferiore nell'albero e `"*"` funge da operatore for-each nel contesto. Molti esempi in questo articolo illustrano la sintassi. 

### <a name="enrichment-tree"></a>Albero di arricchimento

Nell'avanzamento dei [passaggi in una pipeline di arricchimento](cognitive-search-concept-intro.md#enrichment-steps), l'elaborazione del contenuto segue la fase di *cracking del documento* , in cui il testo e le immagini vengono estratti dall'origine. Il contenuto dell'immagine può quindi essere instradato alle competenze che specificano l'elaborazione dell'immagine, mentre il contenuto di testo viene accodato per l'elaborazione del testo. Per i documenti di origine che contengono grandi quantità di testo, è possibile impostare una *modalità di analisi* nell'indicizzatore per segmentare il testo in blocchi più piccoli per un'elaborazione più ottimale. 

![Archivio conoscenze nel diagramma della pipeline](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Archivio conoscenze nel diagramma della pipeline")

Quando un documento si trova nella pipeline di arricchimento, viene rappresentato come albero di contenuto e arricchimenti associati. Viene creata un'istanza di questo albero come output del cracking di documenti.  Il formato dell'albero di arricchimento consente alla pipeline di arricchimento di associare metadati a tipi di dati ancora primitivi, non è un oggetto JSON valido, ma può essere proiettato in un formato JSON valido. La tabella seguente illustra lo stato di un documento che entra nella pipeline di arricchimento:

|Origine dati/modalità di analisi|Predefinito|JSON, righe JSON e CSV|
|---|---|---|
|Archiviazione BLOB|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/D |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/D|

 Quando vengono eseguiti, i set di competenze aggiungono nuovi nodi all'albero di arricchimento. Questi nuovi nodi possono quindi essere usati come input per le competenze downstream, con la proiezione nell'archivio conoscenze o l'associazione a campi di indice. Gli arricchimenti non sono variabili: una volta creati, i nodi non possono essere modificati. Un albero di arricchimento diventa complesso contestualmente ai set di competenze, ma non tutti i nodi dell'albero di arricchimento devono necessariamente raggiungere l'indice o l'archivio conoscenze. 

È possibile salvare in modo selettivo solo un subset degli arricchimenti nell'indice o nell'archivio conoscenze.

### <a name="context"></a>Context

Ogni competenza richiede un contesto. Un contesto determina:

+ Il numero di volte in cui viene eseguita la competenza, in base ai nodi selezionati. Per valori di contesto di tipo raccolta, aggiungendo un elemento `/*` alla fine, la competenza viene richiamata una volta per ogni istanza nella raccolta. 

+ La posizione in cui vengono aggiunti gli output delle competenze nell'albero di arricchimento. Gli output vengono sempre aggiunti all'albero come elementi figlio del nodo di contesto. 

+ Forma degli input. Per le raccolte multilivello, l'impostazione del contesto sulla raccolta padre influirà sulla forma dell'input per la competenza. Ad esempio, se si dispone di un albero di arricchimento con un elenco di paesi/aree geografiche, ciascuno arricchito con un elenco di stati contenente un elenco di codici postali.

|Context|Input|Forma dell'input|Chiamata della competenza|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Elenco di tutti i codici postali nel paese/area geografica |Una volta per paese/area |
|`/document/countries/*/states/*` |'/Document/countries/*/States/*/zipcodes/*'' |Elenco di codici postali nello stato | Una volta per ogni combinazione di paese/area e stato|

## <a name="generate-enriched-data"></a>Generare dati arricchiti 

Utilizzando le [revisioni degli Alberghi](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) come punto di riferimento, verranno esaminate le seguenti informazioni:

+ Evoluzione dell'albero di arricchimento con l'esecuzione di ogni competenza
+ Funzionamento del contesto e degli input per determinare il numero di esecuzioni di un'abilità
+ Qual è la forma dell'input basata sul contesto

Un "documento" all'interno del processo di arricchimento rappresenta una singola riga (revisione di un hotel) all'interno del file di origine hotel_reviews.csv.

### <a name="skill-1-split-skill"></a>Competenza n. 1: competenza di divisione

Quando il contenuto di origine è costituito da grandi blocchi di testo, è utile suddividerlo in componenti più piccoli per una maggiore accuratezza della lingua, del sentimento e del rilevamento di frasi chiave. Sono disponibili due granularità: pagine e frasi. Una pagina è costituita da circa 5000 caratteri.

Una competenza di suddivisione del testo è in genere innanzitutto in un skillt.

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

Con il contesto di competenza di `"/document/reviews_text"` , l'abilità Split verrà eseguita una volta per `reviews_text` . L'output della competenza è un elenco in cui `reviews_text` è suddiviso in segmenti di 5000 caratteri. L'output dell'abilità Split è denominato `pages` e viene aggiunto all'albero di arricchimento. La funzionalità `targetName` consente di rinominare l'output di una competenza prima che venga aggiunto all'albero di arricchimento.

L'albero di arricchimento dispone ora di un nuovo nodo inserito nel contesto della competenza. Questo nodo è disponibile per il mapping dei campi di qualsiasi competenza, proiezione o output. A livello concettuale, l'albero è simile al seguente:

![albero di arricchimento dopo il cracking di documenti](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Albero di arricchimento dopo il cracking di documenti e prima dell'esecuzione delle competenze")

Il nodo radice per tutti gli arricchimenti è `"/document"`. Quando si usano gli indicizzatori BLOB, il nodo `"/document"` ha nodi figlio di `"/document/content"` e `"/document/normalized_images"`. Quando si usano dati CSV, come in questo esempio, i nomi delle colonne vengono mappati ai nodi sotto `"/document"`. 

Per accedere a uno qualsiasi degli arricchimenti aggiunti a un nodo da una competenza, è necessario il percorso completo per l'arricchimento. Se ad esempio si vuole usare il testo del nodo ```pages``` come input per un'altra competenza, è necessario specificarlo come ```"/document/reviews_text/pages/*"```.
 
 ![albero di arricchimento dopo la competenza n. 1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Albero di arricchimento dopo l'esecuzione della competenza n. 1")

### <a name="skill-2-language-detection"></a>Competenza n. 2: rilevamento della lingua

I documenti di revisione degli alberghi includono i suggerimenti dei clienti espressi in più lingue. La competenza di rilevamento della lingua determina il linguaggio usato. Il risultato viene quindi passato all'estrazione di frasi chiave e al rilevamento del sentimento, prendendo in considerazione la lingua quando si rilevano sentimenti e frasi.

Sebbene la competenza di rilevamento della lingua sia la terza (n. 3) definita, è quella successiva da eseguire. Poiché non è bloccata richiedendo alcun input, viene eseguita in parallelo con la competenza precedente. Analogamente alla competenza di divisione che la precede, anche la competenza di rilevamento della lingua viene richiamata una volta per ogni documento. L'albero di arricchimento dispone ora di un nuovo nodo per la lingua.

 ![albero di arricchimento dopo la competenza n. 2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Albero di arricchimento dopo l'esecuzione della competenza n. 2")
 
 ### <a name="skill-3-key-phrases-skill"></a>Competenza n. 3: competenza di frasi chiave 

Dato il contesto di `/document/reviews_text/pages/*`, la competenza di frasi chiave viene richiamata una volta per ogni elemento nella raccolta `pages`. L'output della competenza sarà un nodo nell'elemento di pagina associato. 

 A questo punto dovrebbe essere possibile esaminare le altre competenze del set e visualizzare il modo in cui l'albero degli arricchimenti continuerà a crescere con l'esecuzione di ogni competenza. Alcune competenze, come quella di unione e dello shaper, creano nuovi nodi, ma usano solo i dati provenienti da nodi esistenti e non creano nuovi arricchimenti.

![albero di arricchimento dopo tutte le competenze](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Albero di arricchimento dopo tutte le competenze")

I colori dei connettori nell'albero sopra indicato indicano che gli arricchimenti sono stati creati da competenze diverse e che i nodi devono essere risolti singolarmente e non faranno parte dell'oggetto restituito quando si seleziona il nodo padre.

## <a name="save-enrichments"></a>Salva arricchimenti

In Azure ricerca cognitiva, un indicizzatore Salva l'output creato. Uno degli output è sempre un [Indice ricercabile](search-what-is-an-index.md). La specifica di un indice è un requisito e, quando si connette un oggetto Skills, i dati inseriti da un indice includono la sostanza degli arricchimenti. In genere, gli output di competenze specifiche, ad esempio le frasi chiave o i punteggi dei sentimenti, vengono inseriti nell'indice in un campo creato a tale scopo.

Facoltativamente, un indicizzatore può anche inviare l'output a un [Archivio informazioni](knowledge-store-concept-intro.md) per l'utilizzo in altri strumenti o processi. Un archivio informazioni viene definito come parte di un insieme di competenze. La definizione determina se i documenti arricchiti vengono proiettati come tabelle o oggetti (file o BLOB). Le proiezioni tabulari sono particolarmente adatte per l'analisi interattiva in strumenti come Power BI, mentre i file e i BLOB vengono in genere usati in data science o in processi simili. In questa sezione verrà illustrato in che modo la composizione delle competenze può definire le tabelle o gli oggetti che si desidera proiettare.

### <a name="projections"></a>Proiezioni

Per il contenuto destinato a un archivio informazioni, è opportuno considerare la struttura del contenuto. La *proiezione* è il processo di selezione dei nodi dall'albero di arricchimento e della creazione di un'espressione fisica nell'archivio delle informazioni. Le proiezioni sono forme personalizzate del documento (contenuto e arricchimenti) che possono essere restituite come proiezioni di tabelle oppure oggetti. Per altre informazioni, vedere la pagina relativa all'[so delle proiezioni](knowledge-store-projection-overview.md).

![Opzioni di mapping dei campi](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opzioni di mapping dei campi per la pipeline di arricchimento")

### <a name="sourcecontext"></a>SourceContext

L' `sourceContext` elemento viene usato solo in input e proiezioni di competenze. Consente di costruire oggetti annidati multilivello. Potrebbe essere necessario creare un nuovo oggetto per passarlo come input a una competenza o proiettarlo nell'archivio conoscenze. Poiché i nodi di arricchimento potrebbero non essere un oggetto JSON valido nell'albero di arricchimento e il riferimento a un nodo nell'albero restituisce solo lo stato del nodo al momento della creazione, l'uso degli arricchimenti come input o proiezioni delle competenze richiede la creazione di un oggetto JSON ben formato. `sourceContext` consente di creare un oggetto gerarchico di tipo anonimo, che richiederebbe più competenze se si usasse solo il contesto. 

`sourceContext`Negli esempi seguenti viene illustrato l'utilizzo di. Esaminare l'output delle competenze che ha generato un arricchimento per determinare se si tratta di un oggetto JSON valido e non di un tipo primitivo.

### <a name="slicing-projections"></a>Proiezioni di sezionamento

Quando si definisce un gruppo di proiezione di una tabella, è possibile sezionare un singolo nodo nell'albero di arricchimento in più tabelle correlate. Se si aggiunge una tabella con un percorso di origine figlio di una proiezione di tabella esistente, il nodo figlio risultante non sarà un elemento figlio della proiezione della tabella esistente, ma verrà proiettato nella nuova tabella correlata. Questa tecnica di sezionamento consente di definire un singolo nodo in una competenza di shaper che può rappresentare l'origine di tutte le proiezioni di tabella. 

### <a name="shaping-projections"></a>Proiezioni di definizione della forma

Esistono due modi per definire una proiezione:

+ Usare la capacità di Text shaper per creare un nuovo nodo che rappresenta il nodo radice per tutti gli arricchimenti che si sta proiettando. Quindi, nelle proiezioni, si farà riferimento solo all'output della competenza di shaper.

+ Utilizzare una forma inline di una proiezione all'interno della definizione di proiezione.

L'approccio dello shaper è più dettagliato rispetto alla definizione della forma inline, ma garantisce che tutte le mutazioni dell'albero di arricchimento siano contenute all'interno delle competenze e che l'output sia un oggetto che può essere riutilizzato. Al contrario, il data shaping in linea consente di creare la forma necessaria, ma è un oggetto anonimo ed è disponibile solo per la proiezione per cui è definita. Gli approcci possono essere usati insieme o separatamente. Il set di competenze creato automaticamente nel flusso di lavoro del portale contiene entrambi. Usa una competenza di shaper per le proiezioni di tabella, ma anche la definizione della forma inline per proiettare la tabella delle frasi chiave.

Per estendere l'esempio, è possibile scegliere di rimuovere la definizione della forma inline e usare una competenza di shaper per creare un nuovo nodo per le frasi chiave. Per creare una forma proiettata in tre tabelle, vale a dire `hotelReviewsDocument`, `hotelReviewsPages` e `hotelReviewsKeyPhrases`, le due opzioni sono descritte nelle sezioni riportate di seguito.

#### <a name="shaper-skill-and-projection"></a>Competenza di shaper e proiezione

This 

> [!Note]
> Alcune colonne della tabella del documento sono state rimosse da questo esempio per brevità.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

Con il nodo `tableprojection` definito nella sezione `outputs` precedente, è ora possibile usare la funzionalità di sezionamento per proiettare parti del nodo `tableprojection` in tabelle diverse:

> [!Note]
> Questo è solo un frammento di codice della proiezione nella configurazione dell'archivio conoscenze.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>Proiezioni di definizione della forma inline

L'approccio di definizione della forma inline non richiede una competenza di shaper, in quanto tutte le forme necessarie per le proiezioni vengono create all'occorrenza. Per proiettare gli stessi dati dell'esempio precedente, l'opzione di proiezione inline avrà un aspetto simile al seguente:

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
Un'osservazione dal punto di vista di entrambi gli approcci è il modo in cui i valori di `"Keyphrases"` vengono proiettati mediante `"sourceContext"`. Il nodo `"Keyphrases"`, che contiene una raccolta di stringhe, è esso stesso un elemento figlio del testo della pagina. Tuttavia, poiché le proiezioni richiedono un oggetto JSON e la pagina è una primitiva (stringa), `"sourceContext"` viene usato per eseguire il wrapping della frase chiave in un oggetto con una proprietà denominata. Questa tecnica consente di proiettare anche le primitive in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi

Come passaggio successivo, creare il primo set di competenze con competenze cognitive.

> [!div class="nextstepaction"]
> [Creare il primo set di competenze](cognitive-search-defining-skillset.md).
