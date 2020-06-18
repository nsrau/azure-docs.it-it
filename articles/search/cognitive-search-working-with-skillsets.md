---
title: Concetti e flusso di lavoro dei set di competenze
titleSuffix: Azure Cognitive Search
description: È nei set di competenze che si crea una pipeline di arricchimento tramite intelligenza artificiale in Ricerca cognitiva di Azure. Sono disponibili concetti e dettagli importanti sulla composizione di set di competenze.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8e263d29bc71ac76c374eeda78e5250a0af2095
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744796"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Concetti e composizione di set di competenze in Ricerca cognitiva di Azure

Questo articolo è destinato agli sviluppatori che necessitano di capire meglio come funziona la pipeline di arricchimento e presuppone che l'utente abbia una conoscenza concettuale del processo di arricchimento di tramite intelligenza artificiale. Se non si ha ancora familiarità con questo concetto, iniziare con:
+ [Arricchimento tramite intelligenza artificiale con Ricerca cognitiva di Azure](cognitive-search-concept-intro.md)
+ [Che cos'è il knowledge store in Ricerca di Azure?](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Specificare il set di competenze
Un set di competenze è una risorsa riutilizzabile in Ricerca cognitiva di Azure che specifica una raccolta di competenze cognitive usate per l'analisi, la trasformazione e l'arricchimento di contenuto di testo o immagini durante l'indicizzazione. La creazione di un set di competenze consente di aggiungere arricchimenti di testo e immagini nella fase di inserimento dati, estraendo e creando nuove informazioni e strutture da contenuto non elaborato.

Un set di competenze include tre proprietà:

+    ```skills```, una raccolta non ordinata di competenze per le quali la piattaforma determina la sequenza di esecuzione in base agli input necessari per ogni competenza
+    ```cognitiveServices```, la chiave di servizi cognitivi necessaria per la fatturazione delle competenze cognitive richiamate
+    ```knowledgeStore```, l'account di archiviazione in cui verranno proiettati i documenti arricchiti



I set di competenze vengono creati in JSON. È possibile compilare set di competenze complessi con riproduzione ciclica e [la diramazione](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) utilizzando il [linguaggio delle espressioni](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional). Il linguaggio delle espressioni usa la notazione di percorso del [puntatore JSON](https://tools.ietf.org/html/rfc6901) con alcune modifiche per identificare i nodi nell'albero di arricchimento. Un elemento ```"/"``` attraversa un livello inferiore nell'albero e ```"*"``` funge da operatore for-each nel contesto. Questi concetti vengono chiariti meglio con un esempio. Per illustrare alcuni concetti e funzionalità, verranno descritti in dettaglio i set di competenze [di un esempio di recensioni di hotel](knowledge-store-connect-powerbi.md). Per visualizzare il set di competenze dopo aver seguito il flusso di lavoro di importazione dei dati, è necessario usare un client dell'API REST per [ottenere il set di competenze](https://docs.microsoft.com/rest/api/searchservice/get-skillset).

### <a name="enrichment-tree"></a>Albero di arricchimento

Per immaginare in che modo un set di competenze arricchisce progressivamente il documento, iniziamo con l'aspetto del documento prima di qualsiasi arricchimento. L'output del cracking di documenti dipende dall'origine dati e dalla specifica modalità di analisi selezionata. È anche lo stato del documento da cui i [mapping dei campi](search-indexer-field-mappings.md) possono ricavare il contenuto quando si aggiungono dati all'indice di ricerca.
![Archivio conoscenze nel diagramma della pipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Archivio conoscenze nel diagramma della pipeline")

Quando un documento si trova nella pipeline di arricchimento, viene rappresentato come albero di contenuto e arricchimenti associati. Viene creata un'istanza di questo albero come output del cracking di documenti. Il formato dell'albero di arricchimento consente alla pipeline di arricchimento di associare metadati a tipi di dati ancora primitivi, non è un oggetto JSON valido, ma può essere proiettato in un formato JSON valido. La tabella seguente illustra lo stato di un documento che entra nella pipeline di arricchimento:

|Origine dati/modalità di analisi|Predefinito|JSON, righe JSON e CSV|
|---|---|---|
|Archiviazione BLOB|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|N/D |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|N/D|

 Quando vengono eseguiti, i set di competenze aggiungono nuovi nodi all'albero di arricchimento. Questi nuovi nodi possono quindi essere usati come input per le competenze downstream, con la proiezione nell'archivio conoscenze o l'associazione a campi di indice. Gli arricchimenti non sono variabili: una volta creati, i nodi non possono essere modificati. Un albero di arricchimento diventa complesso contestualmente ai set di competenze, ma non tutti i nodi dell'albero di arricchimento devono necessariamente raggiungere l'indice o l'archivio conoscenze. 

È possibile salvare in modo selettivo solo un subset degli arricchimenti nell'indice o nell'archivio conoscenze.
Per la parte restante di questo documento si presuppone di usare un [esempio di recensioni di hotel](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi), ma gli stessi concetti si applicano anche all'arricchimento di documenti da tutte le altre origini dati.

### <a name="context"></a>Context
Ogni competenza richiede un contesto. Un contesto determina:
+    Il numero di volte in cui viene eseguita la competenza, in base ai nodi selezionati. Per valori di contesto di tipo raccolta, aggiungendo un elemento ```/*``` alla fine, la competenza viene richiamata una volta per ogni istanza nella raccolta. 
+    La posizione in cui vengono aggiunti gli output delle competenze nell'albero di arricchimento. Gli output vengono sempre aggiunti all'albero come elementi figlio del nodo di contesto. 
+    Forma degli input. Per le raccolte multilivello, l'impostazione del contesto sulla raccolta padre influirà sulla forma dell'input per la competenza. Ad esempio, nel caso di un albero di arricchimento con un elenco di paesi/aree, ognuno arricchito con un elenco di stati contenente un elenco di codici di avviamento postale.

|Context|Input|Forma dell'input|Chiamata della competenza|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Elenco di tutti i codici di avviamento postale nel paese/area |Una volta per paese/area |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Elenco di codici di avviamento postale nello stato | Una volta per ogni combinazione di paese/area e stato|

### <a name="sourcecontext"></a>SourceContext

`sourceContext` viene usato solo in input e [proiezioni](knowledge-store-projection-overview.md) di competenze. Consente di costruire oggetti annidati multilivello. Potrebbe essere necessario creare un nuovo oggetto per passarlo come input a una competenza o proiettarlo nell'archivio conoscenze. Poiché i nodi di arricchimento potrebbero non essere un oggetto JSON valido nell'albero di arricchimento e il riferimento a un nodo nell'albero restituisce solo lo stato del nodo al momento della creazione, l'uso degli arricchimenti come input o proiezioni delle competenze richiede la creazione di un oggetto JSON ben formato. `sourceContext` consente di creare un oggetto gerarchico di tipo anonimo, che richiederebbe più competenze se si usasse solo il contesto. L'uso di `sourceContext` è illustrato nella sezione seguente. Esaminare l'output delle competenze che ha generato un arricchimento per determinare se si tratta di un oggetto JSON valido e non di un tipo primitivo.

### <a name="projections"></a>Proiezioni

La proiezione è il processo di selezione dei nodi dall'albero di arricchimento da salvare nell'archivio conoscenze. Le proiezioni sono forme personalizzate del documento (contenuto e arricchimenti) che possono essere restituite come proiezioni di tabelle oppure oggetti. Per altre informazioni, vedere la pagina relativa all'[so delle proiezioni](knowledge-store-projection-overview.md).

![Opzioni di mapping dei campi](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opzioni di mapping dei campi per la pipeline di arricchimento")

Il diagramma precedente descrive il selettore che si usa in base alla posizione della pipeline di arricchimento.

## <a name="generate-enriched-data"></a>Generare dati arricchiti 

Passiamo ora al set di competenze relativo alle recensioni di hotel. È possibile seguire l'[esercitazione](knowledge-store-connect-powerbi.md) per creare il set di competenze o [visualizzarlo](https://github.com/Azure-Samples/azure-search-postman-samples/). Vedremo:

* in che modo si evolve l'albero di arricchimento con l'esecuzione di ogni competenza 
* in che modo funzionano il contesto e gli input per determinare il numero di esecuzioni di una competenza 
* qual è la forma dell'input in base al contesto. 

Poiché usiamo la modalità di analisi di testo delimitato per l'indicizzatore, un documento all'interno del processo di arricchimento rappresenta una singola riga all'interno del file CSV.

### <a name="skill-1-split-skill"></a>Competenza n. 1: competenza di divisione 

![albero di arricchimento dopo il cracking di documenti](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Albero di arricchimento dopo il cracking di documenti e prima dell'esecuzione delle competenze")

Con il contesto di competenze di ```"/document/reviews_text"```, questa competenza verrà eseguita una volta per `reviews_text`. L'output della competenza è un elenco in cui `reviews_text` è suddiviso in segmenti di 5000 caratteri. L'output della competenza di divisione è denominato `pages` e viene aggiunto all'albero di arricchimento. La funzionalità `targetName` consente di rinominare l'output di una competenza prima che venga aggiunto all'albero di arricchimento.

L'albero di arricchimento dispone ora di un nuovo nodo inserito nel contesto della competenza. Questo nodo è disponibile per il mapping dei campi di qualsiasi competenza, proiezione o output.


Il nodo radice per tutti gli arricchimenti è `"/document"`. Quando si usano gli indicizzatori BLOB, il nodo `"/document"` ha nodi figlio di `"/document/content"` e `"/document/normalized_images"`. Quando si usano dati CSV, come in questo esempio, i nomi delle colonne vengono mappati ai nodi sotto `"/document"`. Per accedere a uno qualsiasi degli arricchimenti aggiunti a un nodo da una competenza, è necessario il percorso completo per l'arricchimento. Se ad esempio si vuole usare il testo del nodo ```pages``` come input per un'altra competenza, è necessario specificarlo come ```"/document/reviews_text/pages/*"```.
 
 ![albero di arricchimento dopo la competenza n. 1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Albero di arricchimento dopo l'esecuzione della competenza n. 1")

### <a name="skill-2-language-detection"></a>Competenza n. 2: rilevamento della lingua
 Sebbene la competenza di rilevamento della lingua sia la terza (n. 3) definita, è quella successiva da eseguire. Poiché non è bloccata richiedendo alcun input, viene eseguita in parallelo con la competenza precedente. Analogamente alla competenza di divisione che la precede, anche la competenza di rilevamento della lingua viene richiamata una volta per ogni documento. L'albero di arricchimento dispone ora di un nuovo nodo per la lingua.
 ![albero di arricchimento dopo la competenza n. 2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Albero di arricchimento dopo l'esecuzione della competenza n. 2")
 
 ### <a name="skill-3-key-phrases-skill"></a>Competenza n. 3: competenza di frasi chiave 

Dato il contesto di ```/document/reviews_text/pages/*```, la competenza di frasi chiave viene richiamata una volta per ogni elemento nella raccolta `pages`. L'output della competenza sarà un nodo nell'elemento di pagina associato. 

 A questo punto dovrebbe essere possibile esaminare le altre competenze del set e visualizzare il modo in cui l'albero degli arricchimenti continuerà a crescere con l'esecuzione di ogni competenza. Alcune competenze, come quella di unione e dello shaper, creano nuovi nodi, ma usano solo i dati provenienti da nodi esistenti e non creano nuovi arricchimenti.

![albero di arricchimento dopo tutte le competenze](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Albero di arricchimento dopo tutte le competenze")

I colori dei connettori nell'albero sopra indicato indicano che gli arricchimenti sono stati creati da competenze diverse e che i nodi devono essere risolti singolarmente e non faranno parte dell'oggetto restituito quando si seleziona il nodo padre.

## <a name="save-enrichments-in-a-knowledge-store"></a>Salvare gli arricchimenti in un archivio conoscenze 

I set di competenze definiscono inoltre un archivio conoscenze in cui è possibile proiettare i documenti arricchiti come tabelle oppure oggetti. Per salvare i dati arricchiti nell'archivio conoscenze, è necessario definire un set di proiezioni per il documento arricchito. Per altre informazioni sull'archivio conoscenze, vedere la [panoramica dell'archivio conoscenze](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Proiezioni di sezionamento

Quando si definisce un gruppo di proiezione di una tabella, è possibile sezionare un singolo nodo nell'albero di arricchimento in più tabelle correlate. Se si aggiunge una tabella con un percorso di origine figlio di una proiezione di tabella esistente, il nodo figlio risultante non sarà un elemento figlio della proiezione della tabella esistente, ma verrà proiettato nella nuova tabella correlata. Questa tecnica di sezionamento consente di definire un singolo nodo in una competenza di shaper che può rappresentare l'origine di tutte le proiezioni di tabella. 

### <a name="shaping-projections"></a>Proiezioni di definizione della forma

Esistono due modi per definire una proiezione. È possibile usare una competenza di shaper per creare un nuovo nodo che rappresenta il nodo radice per tutti gli arricchimenti proiettati. Quindi, nelle proiezioni, si farà riferimento solo all'output della competenza di shaper. È anche possibile definire la forma inline di una proiezione con la definizione stessa.

L'approccio dello shaper è più dettagliato rispetto alla definizione della forma inline, ma garantisce che tutte le mutazioni dell'albero di arricchimento siano contenute all'interno delle competenze e che l'output sia un oggetto che può essere riutilizzato. La definizione della forma inline consente di creare la forma necessaria, ma è un oggetto anonimo disponibile solo per la proiezione per cui è definito. Gli approcci possono essere usati insieme o separatamente. Il set di competenze creato automaticamente nel flusso di lavoro del portale contiene entrambi. Usa una competenza di shaper per le proiezioni di tabella, ma anche la definizione della forma inline per proiettare la tabella delle frasi chiave.

Per estendere l'esempio, è possibile scegliere di rimuovere la definizione della forma inline e usare una competenza di shaper per creare un nuovo nodo per le frasi chiave. Per creare una forma proiettata in tre tabelle, vale a dire `hotelReviewsDocument`, `hotelReviewsPages` e `hotelReviewsKeyPhrases`, le due opzioni sono descritte nelle sezioni riportate di seguito.


#### <a name="shaper-skill-and-projection"></a>Competenza di shaper e proiezione 

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
