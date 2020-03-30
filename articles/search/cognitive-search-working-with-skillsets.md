---
title: Concetti e flusso di lavoro delle competenze
titleSuffix: Azure Cognitive Search
description: I set di competenze consentono di creare una pipeline di arricchimento dell'io in Ricerca cognitiva di Azure.Skillsets are where you author an AI enrichment pipeline in Azure Cognitive Search. Informazioni sui concetti e i dettagli importanti sulla composizione dei set di competenze.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191034"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Concetti e composizione delle competenze in Ricerca cognitiva di AzureSkillset concepts and composition in Azure Cognitive Search

Questo articolo è destinato agli sviluppatori che necessitano di una comprensione più approfondita del funzionamento della pipeline di arricchimento e presuppone che si disponga di una comprensione concettuale del processo di arricchimento dell'iA. Se sei nuovo di questo concetto, inizia con:
+ [Arricchimento dell'iA in Ricerca cognitiva di AzureAI enrichment in Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Che cos'è il knowledge store in Ricerca di Azure?](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>Specificare il set di competenze
Un set di competenze è una risorsa riutilizzabile in Ricerca cognitiva di Azure che specifica una raccolta di competenze cognitive usate per analizzare, trasformare e arricchire il contenuto di testo o immagini durante l'indicizzazione. La creazione di un set di competenze consente di allegare gli arricchimenti di testo e immagini nella fase di inserimento dei dati, estraendo e creando nuove informazioni e strutture dal contenuto non elaborato.

Un set di competenze ha tre proprietà:

+   ```skills```, una raccolta non ordinata di competenze per le quali la piattaforma determina la sequenza di esecuzione in base agli input necessari per ogni competenza
+   ```cognitiveServices```, la chiave dei servizi cognitivi necessaria per la fatturazione delle competenze cognitive
+   ```knowledgeStore```, l'account di archiviazione in cui verranno proiettati i documenti arricchiti



I set di competenze vengono creati in JSON. È possibile creare set di competenze complesse con looping e [diramazione](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) utilizzando il [linguaggio](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)delle espressioni . Il linguaggio delle espressioni usa la notazione del percorso del [puntatore JSON](https://tools.ietf.org/html/rfc6901) con alcune modifiche per identificare i nodi nell'albero di arricchimento. Un ```"/"``` attraversa un livello inferiore nell'albero e ```"*"``` funge da operatore for-each nel contesto. Questi concetti sono meglio descritti con un esempio. Per illustrare alcuni dei concetti e delle funzionalità, esamineremo le recensioni degli hotel, il set di [competenze di esempio.](knowledge-store-connect-powerbi.md) Per visualizzare il set di competenze dopo aver seguito il flusso di lavoro di importazione dei dati, è necessario utilizzare un client DELL'API REST per [ottenere il set](https://docs.microsoft.com/rest/api/searchservice/get-skillset)di competenze.

### <a name="enrichment-tree"></a>Albero di arricchimento

Per immaginare come un set di competenze arricchisce progressivamente il documento, iniziamo con l'aspetto del documento prima di qualsiasi arricchimento. L'output del cracking del documento dipende dall'origine dati e dalla modalità di analisi specifica selezionata. Questo è anche lo stato del documento da cui [i mapping](search-indexer-field-mappings.md) dei campi possono originare il contenuto quando si aggiungono dati all'indice di ricerca.
![Archivio conoscenze nel diagramma della pipeline](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "Archivio conoscenze nel diagramma della pipeline")

Una volta che un documento è nella pipeline di arricchimento, è rappresentato come un albero di contenuto e gli arricchimenti associati. Viene creata un'istanza di questo albero come output della fessurazione del documento. Il formato dell'albero di arricchimento consente alla pipeline di arricchimento di associare metadati anche a tipi di dati primitivi, non è un oggetto JSON valido, ma può essere proiettato in un formato JSON valido. Nella tabella seguente viene illustrato lo stato di un documento che entra nella pipeline di arricchimento:

|Origine dati - Modalità di analisi|Predefinito|JSON, Linee JSON & CSV|
|---|---|---|
|Archiviazione BLOB|/documento/contenuto<br>/documento/normalized_images/<br>…|/document/ "chiave1"<br>/document/ "chiave2"<br>…|
|SQL|/document/ colonna1<br>/document/ colonna2<br>…|N/D |
|Cosmos DB|/document/ "chiave1"<br>/document/ "chiave2"<br>…|N/D|

 Quando le competenze vengono eseguite, aggiungono nuovi nodi all'albero di arricchimento. Questi nuovi nodi possono quindi essere utilizzati come input per le competenze a valle, la proiezione nel Knowledge Store o il mapping ai campi dell'indice. Gli arricchimenti non sono modificabili: una volta creati, i nodi non possono essere modificati. Man mano che i set di competenze diventeranno più complessi, anche l'albero di arricchimento, ma non tutti i nodi nell'albero di arricchimento devono essere resi nell'indice o nell'archivio informazioni. 

È possibile rendere persistente solo un sottoinsieme degli arricchimenti con l'indice o l'archivio informazioni.
Per il resto di questo documento, presumiamo che stiamo lavorando con [l'esempio](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)di recensioni di hotel , ma gli stessi concetti si applicano per arricchire i documenti provenienti da tutte le altre fonti di dati.

### <a name="context"></a>Context
Ogni abilità richiede un contesto. Un contesto determina:
+   Il numero di volte in cui la competenza viene eseguita, in base ai nodi selezionati. Per i valori di contesto di raccolta di tipi, l'aggiunta di un ```/*``` alla fine comporterà la competenza viene richiamata una volta per ogni istanza nella raccolta. 
+   Dove nell'albero di arricchimento vengono aggiunti i risultati delle competenze. Gli output vengono sempre aggiunti alla struttura ad albero come elementi figlio del nodo di contesto. 
+   Forma degli ingressi. Per le raccolte a più livelli, l'impostazione del contesto sulla raccolta padre influirà sulla forma dell'input per la competenza. Ad esempio, se si dispone di un albero di arricchimento con un elenco di paesi, ognuno arricchito con un elenco di stati contenente un elenco di codici postali.

|Context|Input|Forma dell'ingresso|Invocazione delle competenze|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |Un elenco di tutti i codici postali nel paese |Una volta per paese |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |Un elenco di codici postali nello stato | Una volta per combinazione di paese e stato|

### <a name="sourcecontext"></a>SourceContext

Il `sourceContext` viene utilizzato solo in input di abilità e [proiezioni](knowledge-store-projection-overview.md). Viene utilizzato per costruire oggetti annidati a più livelli. Potrebbe essere necessario creare un nuovo oggetto per passarlo come input a una competenza o un progetto nel Knowledge Store. Poiché i nodi di arricchimento potrebbero non essere un oggetto JSON valido nell'albero di arricchimento e il riferimento a un nodo nell'albero restituisce solo lo stato del nodo al momento della creazione, l'uso degli arricchimenti come input o proiezioni di competenze richiede la creazione di un oggetto JSON ben formato. Consente `sourceContext` di costruire un oggetto di tipo anonimo gerarchico, che richiederebbe più competenze se si utilizzasse solo il contesto. L'utilizzo `sourceContext` è illustrato nella sezione successiva. Esaminare l'output delle competenze che ha generato un arricchimento per determinare se si tratta di un oggetto JSON valido e non di un tipo primitivo.

### <a name="projections"></a>Proiezioni

La proiezione è il processo di selezione dei nodi dall'albero di arricchimento da salvare nel Knowledge Store. Le proiezioni sono forme personalizzate del documento (contenuto e arricchimenti) che possono essere emesse come proiezioni di tabelle o oggetti. Per ulteriori informazioni sull'utilizzo delle proiezioni, consultate [Operazioni con le proiezioni.](knowledge-store-projection-overview.md)

![Opzioni di mappatura dei campi](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "Opzioni di mappatura dei campi per la pipeline di arricchimento")

Il diagramma precedente descrive il selettore con cui si lavora in base alla posizione in cui ci si trova nella pipeline di arricchimento.

## <a name="generate-enriched-data"></a>Generare dati arricchiti 

Esaminiamo ora il set di competenze delle recensioni dell'hotel, è possibile seguire il [tutorial](knowledge-store-connect-powerbi.md) per creare il set di competenze o [visualizzare](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) il set di competenze. Vedremo:

* come si evolve l'albero di arricchimento con l'esecuzione di ogni abilità 
* come funzionano il contesto e gli input per determinare quante volte un'abilità viene eseguita 
* la forma dell'input è basata sul contesto. 

Poiché si usa la modalità di analisi del testo delimitata per l'indicizzatore, un documento all'interno del processo di arricchimento rappresenta una singola riga all'interno del file CSV.

### <a name="skill-1-split-skill"></a>#1 abilità: abilità divisa 

![albero di arricchimento dopo il cracking del documento](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Albero di arricchimento dopo la fessurazione del documento e prima dell'esecuzione delle abilità")

Con il contesto di abilità di ```"/document/reviews_text"``` `reviews_text`, questa abilità verrà eseguita una volta per il . L'output delle competenze `reviews_text` è un elenco in cui l'oggetto viene suddiviso in 5000 segmenti di caratteri. L'output della competenza `pages` divisa viene denominato e aggiunto all'albero di arricchimento. La `targetName` funzione consente di rinominare un output di abilità prima di essere aggiunto all'albero di arricchimento.

L'albero di arricchimento ora ha un nuovo nodo posto nel contesto della competenza. Questo nodo è disponibile per qualsiasi mapping di competenze, proiezioni o campi di output.


Il nodo radice per tutti `"/document"`gli arricchimenti è . Quando si utilizzano indicizzatori BLOB, il `"/document"` nodo diverrà costituito da nodi figlio di `"/document/content"` e `"/document/normalized_images"`. Quando si lavora con dati CSV, come in questo esempio, `"/document"`i nomi delle colonne verranno mappati ai nodi al di sotto di . Per accedere a uno qualsiasi degli arricchimenti aggiunti a un nodo da una competenza, è necessario il percorso completo per l'arricchimento. Ad esempio, se si desidera utilizzare ```pages``` il testo del nodo come input per ```"/document/reviews_text/pages/*"```un'altra competenza, sarà necessario specificarlo come .
 
 ![arricchimento dopo #1 di abilità](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "L'albero di arricchimento dopo l'esecuzione di #1 di abilitàEnrichment tree after skill #1 executes")

### <a name="skill-2-language-detection"></a>Rilevamento delle competenze #2 della lingua
 Mentre la competenza di rilevamento della lingua è la terza abilità (#3 di abilità) definita nel set di competenze, è la competenza successiva da eseguire. Poiché non è bloccato richiedendo alcun input, verrà eseguito in parallelo con la competenza precedente. Come l'abilità di divisione che l'ha preceduta, anche l'abilità di rilevamento della lingua viene richiamata una volta per ogni documento. L'albero di arricchimento ora ha un nuovo nodo per il linguaggio.
 ![arricchimento dopo #2 di abilità](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Albero di arricchimento dopo l'esecuzione di #2 di abilitàEnrichment tree after skill #2 executes")
 
 ### <a name="skill-3-key-phrases-skill"></a>#3: abilità delle frasi chiave 

Dato il ```/document/reviews_text/pages/*``` contesto delle frasi chiave skill verrà richiamato una `pages` volta per ciascuno degli elementi nella raccolta. L'output della competenza sarà un nodo sotto l'elemento di pagina associato. 

 Ora dovresti essere in grado di guardare il resto delle abilità nel set di competenze e visualizzare come l'albero degli arricchimenti continuerà a crescere con l'esecuzione di ogni abilità. Alcune abilità, come la competenza di unione e la competenza shaper, creano anche nuovi nodi, ma utilizzano solo i dati dei nodi esistenti e non creano nuovi arricchimenti netti.

![arricchimento dopo tutte le competenze](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Albero di arricchimento dopo tutte le competenze")

I colori dei connettori nell'albero sopra indicati indicano che gli arricchimenti sono stati creati da competenze diverse e i nodi dovranno essere indirizzati singolarmente e non faranno parte dell'oggetto restituito quando si seleziona il nodo padre.

## <a name="save-enrichments-in-a-knowledge-store"></a>Salvare gli arricchimenti in un archivio di conoscenze 

I set di competenze definiscono anche un Knowledge Store in cui i documenti arricchiti possono essere proiettati come tabelle o oggetti. Per salvare i dati arricchiti nel Knowledge Store, è necessario definire un set di proiezioni per il documento arricchito. Per ulteriori informazioni sull'archivio informazioni, vedere [Panoramica dell'archivio informazioni](knowledge-store-concept-intro.md)

### <a name="slicing-projections"></a>Proiezioni di affettatura

Quando si definisce un gruppo di proiezione di tabelle, un singolo nodo nell'albero di arricchimento può essere suddiviso in più tabelle correlate. Se si aggiunge una tabella con un percorso di origine figlio di una proiezione di tabella esistente, il nodo figlio risultante non sarà un elemento figlio della proiezione di tabella esistente, ma verrà proiettato nella nuova tabella correlata. Questa tecnica di affettatura consente di definire un singolo nodo in una competenza di shaper che può essere l'origine per tutte le proiezioni della tabella. 

### <a name="shaping-projections"></a>Modellare le proiezioni

Esistono due modi per definire una proiezione. È possibile utilizzare una competenza shaper per creare un nuovo nodo che è il nodo radice per tutti gli arricchimenti che si stanno proiettando. Quindi, nelle vostre proiezioni, farete riferimento solo all'uscita dell'abilità shaper. È anche possibile formare in linea una proiezione all'interno della definizione di proiezione stessa.

L'approccio shaper è più dettagliato della forma in linea, ma assicura che tutte le mutazioni dell'albero di arricchimento siano contenute all'interno delle competenze e che l'output sia un oggetto che può essere riutilizzato. La forma in linea consente di creare la forma necessaria, ma è un oggetto anonimo ed è disponibile solo per la proiezione per cui è definita. Gli approcci possono essere utilizzati insieme o separatamente. Il set di competenze creato automaticamente nel flusso di lavoro del portale contiene entrambi. Utilizza una abilità shaper per le proiezioni della tabella, ma usa anche la forma in linea per proiettare la tabella delle frasi chiave.

Per estendere l'esempio, è possibile scegliere di rimuovere la forma in linea e utilizzare una competenza di shaper per creare un nuovo nodo per le frasi chiave. Per creare una forma proiettata in `hotelReviewsDocument` `hotelReviewsPages`tre `hotelReviewsKeyPhrases`tabelle, ovvero , e , le due opzioni sono descritte nelle sezioni seguenti.


#### <a name="shaper-skill-and-projection"></a>Abilità e proiezione di Shaper 

> [!Note]
> Alcune delle colonne della tabella documenti sono state rimosse da questo esempio per brevità.
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

Con `tableprojection` il nodo `outputs` definito nella sezione precedente, è ora possibile usare `tableprojection` la funzionalità di sezionamento per proiettare parti del nodo in tabelle diverse:With the node defined in the section above, we can now use the slicing feature to project parts of the node into different tables:

> [!Note]
> Si tratta solo di un frammento della proiezione all'interno della configurazione del knowledge store.
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

#### <a name="inline-shaping-projections"></a>Proiezioni di sagomatura in linea

L'approccio di sagomatura in linea non richiede un'abilità di shaper in quanto tutte le forme necessarie per le proiezioni vengono create nel momento in cui sono necessarie. Per proiettare gli stessi dati dell'esempio precedente, l'opzione di proiezione inline sarà simile alla seguente:

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
  
Un'osservazione da entrambi gli `"Keyphrases"` approcci è come `"sourceContext"`i valori di vengono proiettati utilizzando il . Il `"Keyphrases"` nodo, che contiene una raccolta di stringhe, è a sua volta un elemento figlio del testo della pagina. Tuttavia, poiché le proiezioni richiedono un oggetto JSON `"sourceContext"` e la pagina è una primitiva (stringa), l'oggetto viene utilizzato per eseguire il wrapping della frase chiave in un oggetto con una proprietà denominata. Questa tecnica consente anche di proiettare le primitive in modo indipendente.

## <a name="next-steps"></a>Passaggi successivi

Come passaggio successivo, crea il tuo primo set di competenze con abilità cognitive.

> [!div class="nextstepaction"]
> [Creare il primo set di competenze](cognitive-search-defining-skillset.md).
