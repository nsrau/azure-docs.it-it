---
title: Definire le proiezioni in un archivio informazioni
titleSuffix: Azure Cognitive Search
description: Esempi di modelli comuni su come proiettare documenti arricchiti nell'archivio informazioni per l'uso con Power BI o Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165514"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>Proiezioni dell'archivio informazioni: come eseguire la modellazione e l'esportazione di arricchimenti nell'archivio informazioni

> [!IMPORTANT] 
> L'archivio conoscenze è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Queste funzionalità di anteprima vengono fornite dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

Le proiezioni sono l'espressione fisica dei documenti arricchiti in un archivio informazioni. L'uso efficace dei documenti arricchiti richiede la struttura. In questo articolo si esamineranno sia la struttura che le relazioni, apprendendo come creare le proprietà di proiezione e come correlare i dati tra i tipi di proiezione creati. 

Per creare una proiezione, è necessario modellare i dati usando una competenza per la creazione di un oggetto personalizzato o usare la sintassi di shaping in linea. Una forma dati contiene tutti i dati che si intende proiettare. Questo documento fornisce un esempio di ciascuna opzione, è possibile scegliere di usare una delle opzioni per le proiezioni create.


Esistono tre tipi di proiezioni:
+ Tabelle
+ Oggetti
+ File

Le proiezioni delle tabelle vengono archiviate nell'archiviazione tabelle di Azure. Le proiezioni di oggetti e file vengono scritte nell'archivio BLOB, le proiezioni di oggetti vengono salvate come file JSON e possono contenere contenuto del documento e qualsiasi output o arricchimento di competenze. La pipeline di arricchimento può inoltre estrarre i file binari come le immagini, che vengono proiettati come proiezioni di file. Quando un oggetto binario viene proiettato come proiezione di oggetti, solo i metadati associati vengono salvati come BLOB JSON. 

Per comprendere l'intersezione tra la data shaping e le proiezioni, si utilizzeranno le seguenti competenze come base per esplorare varie configurazioni. Questo skillt elabora le immagini e il contenuto di testo non elaborati. Le proiezioni verranno definite dal contenuto del documento e dagli output delle competenze, per gli scenari che si desidera supportare.

In alternativa, è possibile scaricare e usare un [esempio di API REST](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) con tutte le chiamate in questa procedura dettagliata.

> [!IMPORTANT] 
> Quando si sperimentano le proiezioni, è utile [impostare la proprietà cache dell'indicizzatore](search-howto-incremental-index.md) per assicurare il controllo dei costi. Con la modifica delle proiezioni, l'intero documento verrà nuovamente arricchito se la cache dell'indicizzatore non è impostata. Quando viene impostata la cache e vengono aggiornate solo le proiezioni, le esecuzioni di competenze per i documenti precedentemente arricchiti non comportano alcun addebito per servizi cognitivi.


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

È ora possibile aggiungere l'oggetto `knowledgeStore` e configurare le proiezioni per ogni scenario in base alle esigenze. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Proiezione a tabelle per scenari come Power BI

> [!NOTE] 
> Poiché l'archivio informazioni è un account di archiviazione di Azure, le proiezioni di tabella sono tabelle di archiviazione di Azure e sono regolate dai limiti di archiviazione sulle tabelle. per altre informazioni, vedere [limiti di archiviazione tabelle](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). È utile tenere presente che la dimensione dell'entità non può superare 1 MB e una singola proprietà non può essere maggiore di 64 KB. Questi vincoli rendono le tabelle una soluzione efficace per l'archiviazione di un numero elevato di entità di piccole dimensioni.

Power BI possibile leggere dalle tabelle e individuare le relazioni in base alle chiavi create dalle proiezioni dell'archivio delle informazioni, in modo da rendere le tabelle un'opzione efficace per proiettare i dati quando si tenta di compilare un dashboard sui dati arricchiti. Supponendo che si stia provando a creare un dashboard in cui è possibile visualizzare le frasi chiave estratte da documenti come un cloud di Word, è possibile aggiungere una competenza shaper per creare una forma personalizzata con i dettagli e le frasi chiave specifici del documento. Aggiungere la competenza shaper al skillt per creare un nuovo arricchimento denominato ```pbiShape``` nel ```document```.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Uso di un'abilità di shaper per creare una forma personalizzata

Creare una forma personalizzata che è possibile proiettare nell'archivio tabelle. Senza una forma personalizzata, una proiezione può fare riferimento solo a un singolo nodo (una proiezione per output). La creazione di una forma personalizzata consente di aggregare vari elementi in un nuovo intero logico che può essere proiettato come una singola tabella oppure sezionato e distribuito in una raccolta di tabelle. In questo esempio, la forma personalizzata combina i metadati e le entità identificate e le frasi chiave. L'oggetto è denominato pbiShape ed è associato a un elemento padre in `/document`. 

> [!IMPORTANT] 
> Per poter essere proiettati, è necessario che i percorsi di origine per l'arricchimento siano oggetti JSON ben formati. L'albero di arricchimento può rappresentare arricchimenti non ben formati JSON, ad esempio quando un arricchimento è associato a un primitivi come una stringa. Si noti che `KeyPhrases` e `Entities` sono racchiusi in un oggetto JSON valido con il `sourceContext`, questo è necessario come `keyphrases` e `entities` sono arricchimenti sulle primitive e devono essere convertiti in JSON valido prima di poter essere proiettati.

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
Aggiungere le competenze per lo shaper appena definite nell'elenco di competenze. 

Ora che sono disponibili tutti i dati necessari per proiettare le tabelle, aggiornare l'oggetto knowledgeStore con le definizioni della tabella. 

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

Impostare la proprietà ```storageConnectionString``` su una stringa di connessione dell'account di archiviazione per utilizzo generico V2 valida. In questo scenario vengono definite tre tabelle nell'oggetto Projection impostando le proprietà ```tableName```, ```source``` e ```generatedKeyName```. È ora possibile aggiornare le competenze eseguendo la richiesta PUT.

### <a name="slicing"></a>Affettare 

Quando si inizia con una forma consolidata in cui tutto il contenuto che deve essere proiettato si trova in una singola forma (o nodo di arricchimento), il sezionamento consente di sezionare un singolo nodo in più tabelle o oggetti. In questo caso, l'oggetto ```pbiShape``` viene sezionato in più tabelle. La funzionalità di sezionamento consente di estrarre parti della forma, ```keyPhrases``` e ```Entities``` in tabelle separate. Questa operazione è utile perché più entità e frasi chiave sono associate a ogni documento. Il sezionamento implicito genera una relazione tra le tabelle padre e figlio, usando il ```generatedKeyName``` nella tabella padre per creare una colonna con lo stesso nome nella tabella figlio. 

### <a name="naming-relationships"></a>Relazioni di denominazione
Le proprietà ```generatedKeyName``` e ```referenceKeyName``` vengono utilizzate per correlare i dati tra tabelle o anche tra tipi di proiezione. Ogni riga nella tabella/proiezione figlio ha una proprietà che punta all'elemento padre. Il nome della colonna o della proprietà nell'elemento figlio è il ```referenceKeyName``` dall'elemento padre. Quando non viene specificato il ```referenceKeyName```, il servizio ne imposta il valore predefinito sul ```generatedKeyName``` dal padre. Power bi si basa su queste chiavi generate per individuare le relazioni all'interno delle tabelle. Se è necessario che la colonna della tabella figlio sia denominata in modo diverso, impostare la proprietà ```referenceKeyName``` nella tabella padre. È ad esempio possibile impostare il ```generatedKeyName``` come ID nella tabella pbiDocument e il ```referenceKeyName``` come DocumentID. Ciò comporterebbe la colonna nelle tabelle pbiEntities e pbiKeyPhrases contenente l'ID documento denominato DocumentID.

Salvare il insieme di competenze aggiornato ed eseguire l'indicizzatore. è ora disponibile una proiezione funzionante con tre tabelle. L'importazione di queste tabelle in Power BI dovrebbe comportare Power BI l'individuazione automatica delle relazioni.

## <a name="projecting-to-objects"></a>Proiezione in oggetti

Le proiezioni di oggetti non hanno le stesse limitazioni delle proiezioni di tabella, sono più adatte per la proiezione di documenti di grandi dimensioni. In questo esempio l'intero documento viene proiettato in una proiezione di oggetti. Le proiezioni di oggetti sono limitate a una singola proiezione in un contenitore.
Per definire una proiezione di oggetti, si utilizzerà la matrice ```objects``` nelle proiezioni. È possibile generare una nuova forma usando l'abilità dell'shaper o usare il data shaping in linea della proiezione dell'oggetto. Mentre nell'esempio Tables è stato illustrato l'approccio per la creazione di una forma e un sezionamento, in questo esempio viene illustrato l'utilizzo di shaping in linea. Il data shaping è la possibilità di creare una nuova forma nella definizione degli input di una proiezione. Il data shaping crea un oggetto anonimo che è identico a quello che produrrebbe un shaper simile. Il data shaping è utile se si definisce una forma che non si prevede di riutilizzare.
La proprietà proiezioni è una matrice, in questo esempio viene aggiunta una nuova istanza di proiezione alla matrice. Aggiornare la definizione knowledgeStore con le proiezioni definite inline. non è necessaria una capacità di shaper quando si usano le proiezioni inline.

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
## <a name="file-projections"></a>Proiezioni di file

Le proiezioni di file sono immagini estratte dal documento di origine o output di arricchimenti che è possibile proiettare al di fuori del processo di arricchimento. Le proiezioni di file, simili alle proiezioni di oggetti, vengono implementate come BLOB e contengono l'immagine. Per generare una proiezione di file, si usa la matrice ```files``` nell'oggetto Projection. Questo esempio proietta tutte le immagini estratte dal documento in un contenitore denominato `samplefile`.

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

Uno scenario più complesso potrebbe richiedere di proiettare contenuto tra tipi di proiezione. Se, ad esempio, è necessario proiettare alcuni dati come frasi chiave ed entità nelle tabelle, salvare i risultati OCR del testo e del testo del layout come oggetti e proiettare le immagini come file. Questo aggiornamento per il livello di competenze verrà:

1. Creare una tabella con una riga per ogni documento.
2. Creare una tabella correlata alla tabella Document con ogni frase chiave identificata come riga in questa tabella.
3. Creare una tabella correlata alla tabella Document con ogni entità identificata come riga in questa tabella.
4. Creare una proiezione di oggetti con il testo del layout per ogni immagine.
5. Creare una proiezione di file, proiettando ogni immagine estratta.
6. Creare una tabella di riferimento incrociato che contiene riferimenti alla tabella del documento, proiezione dell'oggetto con il testo del layout e proiezione del file.

Per iniziare, aggiungere una nuova abilità shaper all'array di competenze che crea un oggetto a forma di. 

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

### <a name="relationships"></a>Relazioni

In questo esempio viene evidenziata anche un'altra funzionalità delle proiezioni, definendo più tipi di proiezioni all'interno dello stesso oggetto di proiezione, esiste una relazione espressa entro e tra i diversi tipi (tabelle, oggetti, file) delle proiezioni, consentendo è possibile iniziare con una riga di tabella per un documento e trovare tutto il testo OCR per le immagini all'interno del documento nella proiezione dell'oggetto. Se non si desidera che i dati siano correlati, definire le proiezioni in oggetti proiezione diversi, ad esempio il frammento di codice seguente comporterà la correlazione tra le tabelle, ma non le relazioni tra le tabelle e le proiezioni di testo OCR. I gruppi di proiezione sono utili quando si desidera proiettare gli stessi dati in forme diverse per esigenze diverse. Ad esempio, un gruppo di proiezione per il dashboard Power BI e un altro gruppo di proiezione per l'uso dei dati per eseguire il training di un modello di intelligenza artificiale per una competenza.
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

In questi esempi sono stati illustrati i modelli comuni su come usare le proiezioni, a questo punto si dovrebbe avere una conoscenza approfondita dei concetti relativi alla creazione di una proiezione per uno scenario specifico.

## <a name="common-issues"></a>Problemi comuni

Quando si definisce una proiezione, si verificano alcuni problemi comuni che possono causare risultati imprevisti.

1. Non shaping le funzionalità di arricchimento delle stringhe. Quando le stringhe sono arricchite, ad esempio ```merged_content``` arricchite con frasi chiave, la proprietà arricchita viene rappresentata come elemento figlio di merged_content all'interno dell'albero di arricchimento. Tuttavia, al momento della proiezione, questo deve essere trasformato in un oggetto JSON valido con un nome e un valore.
2. Omettere il ```/*``` alla fine di un percorso di origine. Se, ad esempio, l'origine di una proiezione è ```/document/pbiShape/keyPhrases``` la matrice di frasi chiave viene proiettata come un singolo oggetto/riga. Impostando il percorso di origine su ```/document/pbiShape/keyPhrases/*``` viene restituita una singola riga o un oggetto per ogni frase chiave.
3. I selettori di percorso fanno distinzione tra maiuscole e minuscole e possono causare la mancata presenza di avvisi di input se non si usa il caso esatto del selettore

