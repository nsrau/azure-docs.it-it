---
title: Definire le proiezioni in un archivio conoscenze
titleSuffix: Azure Cognitive Search
description: Esempi di modelli comuni su come proiettare documenti arricchiti nell'archivio informazioni per l'uso con Power BI o Azure ML.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: f40841887b8116ad1384dc2b827d8215ea659490
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537498"
---
# <a name="how-to-shape-and-export-enrichments"></a>Come modellare ed esportare gli arricchimenti

Le proiezioni sono l'espressione fisica dei documenti arricchiti in un archivio informazioni. Un utilizzo efficace dei documenti arricchiti richiede la struttura. In questo articolo si esamineranno sia la struttura che le relazioni, apprendendo come creare le proprietà di proiezione, nonché come correlare i dati tra i tipi di proiezione creati. 

Per creare una proiezione, i dati vengono modellati usando un' [abilità di shaper](cognitive-search-skill-shaper.md) per creare un oggetto personalizzato o usando la sintassi di shaping in linea all'interno di una definizione di proiezione. 

Una forma dati contiene tutti i dati progettati per il progetto, formati come una gerarchia di nodi. Questo articolo illustra diverse tecniche per il data shaping, in modo che possa essere proiettato in strutture fisiche che contribuiscono alla creazione di report, analisi o elaborazione downstream. 

Gli esempi presentati in questo articolo sono reperibili in questo [esempio di API REST](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json), che è possibile scaricare ed eseguire in un client http.

## <a name="introduction-to-projection-examples"></a>Introduzione agli esempi di proiezione

Esistono tre tipi di [proiezioni](knowledge-store-projection-overview.md):

+ Tabelle
+ Oggetti
+ File

Le proiezioni delle tabelle vengono archiviate nell'archiviazione tabelle di Azure. Le proiezioni di oggetti e file vengono scritte nell'archivio BLOB, in cui le proiezioni di oggetti vengono salvate come file JSON e possono contenere contenuto del documento di origine, nonché qualsiasi output o arricchimento delle competenze. La pipeline di arricchimento può inoltre estrarre i file binari come le immagini, che vengono proiettati come proiezioni di file. Quando un oggetto binario viene proiettato come proiezione di oggetti, solo i metadati associati vengono salvati come BLOB JSON. 

Per comprendere l'intersezione tra la data shaping e le proiezioni, si utilizzeranno le seguenti competenze come base per esplorare varie configurazioni. Questo skillt elabora le immagini e il contenuto di testo non elaborati. Le proiezioni verranno definite dal contenuto del documento e dagli output delle competenze, per gli scenari desiderati.

> [!IMPORTANT] 
> Quando si sperimentano le proiezioni, è utile [impostare la proprietà cache dell'indicizzatore](search-howto-incremental-index.md) per assicurare il controllo dei costi. Con la modifica delle proiezioni, l'intero documento verrà nuovamente arricchito se la cache dell'indicizzatore non è impostata. Quando viene impostata la cache e vengono aggiornate solo le proiezioni, le esecuzioni di competenze per i documenti precedentemente arricchiti non comportano alcun nuovo addebito per servizi cognitivi.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

Utilizzando questo insieme di competenze, con il relativo valore null `knowledgeStore` come base, nel primo esempio viene compilato l' `knowledgeStore` oggetto, configurato con proiezioni che consentono di creare strutture di dati tabulari che è possibile utilizzare in altri scenari. 

## <a name="projecting-to-tables"></a>Proiezione in tabelle

La proiezione per le tabelle in archiviazione di Azure è utile per la creazione di report e l'analisi tramite strumenti come Power BI. Power BI possibile leggere dalle tabelle e individuare le relazioni in base alle chiavi generate durante la proiezione. Se si sta provando a creare un dashboard, i dati correlati verranno semplificati dall'attività. 

Viene ora creato un dashboard per visualizzare le frasi chiave estratte dai documenti come un cloud di Word. Per creare la struttura di dati corretta, aggiungere una competenza shaper al skillt per creare una forma personalizzata con i dettagli e le frasi chiave specifici del documento. La forma personalizzata verrà chiamata `pbiShape` sul `document` nodo radice.

> [!NOTE] 
> Le proiezioni di tabella sono tabelle di archiviazione di Azure, regolate dai limiti di archiviazione imposti da archiviazione di Azure. Per altre informazioni, vedere [limiti di archiviazione tabelle](/rest/api/storageservices/understanding-the-table-service-data-model). È utile tenere presente che la dimensione dell'entità non può superare 1 MB e una singola proprietà non può essere maggiore di 64 KB. Questi vincoli rendono le tabelle una soluzione efficace per l'archiviazione di un numero elevato di entità di piccole dimensioni.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Uso di un'abilità di shaper per creare una forma personalizzata

Creare una forma personalizzata che è possibile proiettare nell'archivio tabelle. Senza una forma personalizzata, una proiezione può fare riferimento solo a un singolo nodo (una proiezione per output). La creazione di una forma personalizzata aggrega vari elementi in un nuovo intero logico che può essere proiettato come una singola tabella oppure sezionato e distribuito in una raccolta di tabelle. 

In questo esempio, la forma personalizzata combina i metadati e le entità identificate e le frasi chiave. L'oggetto viene chiamato `pbiShape` ed è associato a un elemento padre `/document` . 

> [!IMPORTANT] 
> Uno degli scopi di data shaping è garantire che tutti i nodi di arricchimento siano espressi in formato JSON ben formato, necessario per la proiezione nell'archivio delle informazioni. Ciò vale soprattutto quando un albero di arricchimento contiene nodi che non sono JSON ben formati, ad esempio quando un arricchimento è associato a una primitiva come una stringa.
>
> Si notino gli ultimi due nodi, `KeyPhrases` e `Entities` . Questi vengono inclusi in un oggetto JSON valido con `sourceContext` . Questa operazione è necessaria perché `keyphrases` e `entities` sono arricchimenti sulle primitive e devono essere convertiti in JSON valido prima di poter essere proiettati.
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

Aggiungere la capacità di shaper sopra indicata al skillt. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

Ora che sono disponibili tutti i dati necessari per proiettare le tabelle, aggiornare l'oggetto knowledgeStore con le definizioni della tabella. In questo esempio sono disponibili tre tabelle, definite impostando le `tableName` proprietà, `source` e `generatedKeyName` .

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

È possibile elaborare il lavoro attenendosi alla procedura seguente:

1. Impostare la ```storageConnectionString``` proprietà su una stringa di connessione dell'account di archiviazione per utilizzo generico V2 valida.  

1. Aggiornare il skillt inviando la richiesta PUT.

1. Dopo aver aggiornato le competenze, eseguire l'indicizzatore. 

È ora disponibile una proiezione funzionante con tre tabelle. L'importazione di queste tabelle in Power BI dovrebbe comportare Power BI l'individuazione automatica delle relazioni.

Prima di passare all'esempio successivo, è possibile esaminare gli aspetti della proiezione della tabella per comprendere i meccanismi di sezionamento e di correlazione dei dati.

### <a name="slicing"></a>Affettare 

Il sezionamento è una tecnica che suddivide un'intera forma consolidata in parti costituenti. Il risultato è costituito da tabelle separate ma correlate che è possibile utilizzare singolarmente.

Nell'esempio `pbiShape` è la forma consolidata (o il nodo di arricchimento). Nella definizione della proiezione, `pbiShape` viene sezionato in tabelle aggiuntive, che consente di estrarre parti della forma, ```keyPhrases``` e ```Entities``` . In Power BI, questo è utile perché più entità e frasi chiave sono associate a ogni documento e si ottengono maggiori informazioni se è possibile visualizzare le entità e le frasi chiave come dati categorizzati.

Il sezionamento genera in modo implicito una relazione tra le tabelle padre e figlio, usando nella ```generatedKeyName``` tabella padre per creare una colonna con lo stesso nome nella tabella figlio. 

### <a name="naming-relationships"></a>Relazioni di denominazione

Le ```generatedKeyName``` ```referenceKeyName``` proprietà e vengono usate per correlare i dati tra tabelle o persino tra tipi di proiezione. Ogni riga nella tabella/proiezione figlio ha una proprietà che punta all'elemento padre. Il nome della colonna o della proprietà nell'elemento figlio è ```referenceKeyName``` dall'elemento padre. Quando ```referenceKeyName``` non viene specificato, il servizio ne imposta il valore predefinito su ```generatedKeyName``` dall'elemento padre. 

Power BI si basa su queste chiavi generate per individuare le relazioni all'interno delle tabelle. Se è necessario che la colonna della tabella figlio sia denominata in modo diverso, impostare la ```referenceKeyName``` proprietà nella tabella padre. Un esempio consiste nell'impostare l' ```generatedKeyName``` ID As nella tabella pbiDocument e ```referenceKeyName``` come DocumentID. Ciò comporterebbe la colonna nelle tabelle pbiEntities e pbiKeyPhrases contenente l'ID documento denominato DocumentID.

## <a name="projecting-to-objects"></a>Proiezione in oggetti

Le proiezioni di oggetti non hanno le stesse limitazioni delle proiezioni di tabella e sono più adatte per la proiezione di documenti di grandi dimensioni. In questo esempio, l'intero documento viene inviato come proiezione di oggetti. Le proiezioni di oggetti sono limitate a una singola proiezione in un contenitore e non possono essere sezionate.

Per definire una proiezione di oggetti, usare la ```objects``` matrice nelle proiezioni. È possibile generare una nuova forma usando l'abilità dell'shaper o usare il data shaping in linea della proiezione dell'oggetto. Mentre nell'esempio Tables è stato illustrato l'approccio per la creazione di una forma e un sezionamento, in questo esempio viene illustrato l'utilizzo di shaping in linea. 

Il data shaping è la possibilità di creare una nuova forma nella definizione degli input di una proiezione. Il data shaping crea un oggetto anonimo che è identico a quello prodotto da un'abilità shaper (in questo caso `pbiShape` ). Il data shaping è utile se si definisce una forma che non si prevede di riutilizzare.

La proprietà proiezioni è una matrice. In questo esempio viene aggiunta una nuova istanza di proiezione alla matrice, in cui la definizione knowledgeStore contiene proiezioni inline. Quando si usano le proiezioni inline, è possibile omettere la capacità dell'shaper.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>Proiezione in un file

Le proiezioni di file sono immagini estratte dal documento di origine o output di arricchimento che possono essere proiettate dal processo di arricchimento. Le proiezioni di file, simili alle proiezioni degli oggetti, vengono implementate come BLOB in archiviazione di Azure e contengono l'immagine. 

Per generare una proiezione di file, usare la `files` matrice nell'oggetto di proiezione. Questo esempio proietta tutte le immagini estratte dal documento in un contenitore denominato `samplefile` .

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>Proiezione a più tipi

Uno scenario più complesso potrebbe richiedere di proiettare contenuto tra tipi di proiezione. Se, ad esempio, è necessario proiettare alcuni dati come frasi chiave ed entità nelle tabelle, salvare i risultati OCR del testo e del testo di layout come oggetti, quindi proiettare le immagini come file. 

Questo esempio aggiorna le competenze con le modifiche seguenti:

1. Creare una tabella con una riga per ogni documento.
1. Creare una tabella correlata alla tabella Document con ogni frase chiave identificata come riga in questa tabella.
1. Creare una tabella correlata alla tabella Document con ogni entità identificata come riga in questa tabella.
1. Creare una proiezione di oggetti con il testo del layout per ogni immagine.
1. Creare una proiezione di file, proiettando ogni immagine estratta.
1. Creare una tabella di riferimento incrociato che contiene riferimenti alla tabella del documento, proiezione dell'oggetto con il testo del layout e proiezione del file.

Queste modifiche vengono riflesse nella definizione knowledgeStore più in basso. 

### <a name="shape-data-for-cross-projection"></a>Data Shaping per la proiezione incrociata

Per ottenere le forme necessarie per queste proiezioni, iniziare aggiungendo una nuova abilità shaper che crea un oggetto con forma denominato `crossProjection` . 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>Definire la tabella, l'oggetto e le proiezioni di file

Dall'oggetto crossProjection consolidato, sezionare l'oggetto in più tabelle, acquisire l'output OCR come BLOB e quindi salvare l'immagine come file (anche nell'archivio BLOB).

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

Per le proiezioni di oggetti è necessario un nome di contenitore per ogni proiezione, le proiezioni di oggetti o le proiezioni di file non possono condividere un contenitore. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relazioni tra la tabella, l'oggetto e le proiezioni di file

In questo esempio viene evidenziata anche un'altra funzionalità delle proiezioni. Definendo più tipi di proiezioni all'interno dello stesso oggetto di proiezione, esiste una relazione espressa all'interno e tra i diversi tipi (tabelle, oggetti, file). In questo modo è possibile iniziare con una riga di tabella per un documento e trovare tutto il testo OCR per le immagini all'interno del documento nella proiezione dell'oggetto. 

Se non si desidera che i dati siano correlati, definire le proiezioni in oggetti di proiezione diversi. Il frammento di codice seguente, ad esempio, comporterà la correlazione tra le tabelle, ma senza le relazioni tra le tabelle e le proiezioni dell'oggetto (testo OCR). 

I gruppi di proiezione sono utili quando si desidera proiettare gli stessi dati in forme diverse per esigenze diverse. Ad esempio, un gruppo di proiezione per il dashboard Power BI e un altro gruppo di proiezione per l'acquisizione dei dati usati per eseguire il training di un modello di apprendimento automatico incluso in un'abilità personalizzata.

Quando si compilano proiezioni di tipi diversi, vengono generate prima le proiezioni di file e oggetti e i percorsi vengono aggiunti alle tabelle.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>Problemi comuni

Quando si definisce una proiezione, si verificano alcuni problemi comuni che possono causare risultati imprevisti. Verificare la presenza di questi problemi se l'output nell'archivio informazioni non è quello previsto.

+ Impossibile definire il data shaping in JSON valido. Quando le stringhe sono arricchite, ad esempio `merged_content` arricchite con frasi chiave, la proprietà arricchita viene rappresentata come elemento figlio di `merged_content` all'interno dell'albero di arricchimento. La rappresentazione predefinita non è JSON ben formato. Quindi, in fase di proiezione, assicurarsi di trasformare l'arricchimento in un oggetto JSON valido con un nome e un valore.

+ Omettere l'oggetto ```/*``` alla fine di un percorso di origine. Se l'origine di una proiezione è `/document/pbiShape/keyPhrases` , la matrice di frasi chiave viene proiettata come un singolo oggetto/riga. Impostare invece il percorso di origine su `/document/pbiShape/keyPhrases/*` per produrre una singola riga o un oggetto per ogni frase chiave.

+ Errori di sintassi del percorso. I selettori di percorso distinguono tra maiuscole e minuscole e possono causare la mancata presenza di avvisi di input se non si usa il caso esatto del selettore.

## <a name="next-steps"></a>Passaggi successivi

Gli esempi in questo articolo illustrano i modelli comuni per la creazione di proiezioni. Ora che si dispone di una conoscenza approfondita dei concetti, è preferibile creare proiezioni per uno scenario specifico.

Quando si esplorano nuove funzionalità, prendere in considerazione l'arricchimento incrementale come passaggio successivo. L'arricchimento incrementale è basato sulla memorizzazione nella cache, che consente di riutilizzare eventuali arricchimenti che non sono altrimenti interessati da una modifica di competenze. Questa operazione è particolarmente utile per le pipeline che includono OCR e analisi delle immagini.

> [!div class="nextstepaction"]
> [Introduzione all'arricchimento e alla memorizzazione nella cache incrementali](cognitive-search-incremental-indexing-conceptual.md)

Per una panoramica sulle proiezioni, vedere altre informazioni sulle funzionalità, ad esempio gruppi e sezionamento, e su come [definirle in un insieme di competenze](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Proiezioni in un archivio conoscenze](knowledge-store-projection-overview.md)