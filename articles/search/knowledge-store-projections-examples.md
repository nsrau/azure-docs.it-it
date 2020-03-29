---
title: Definire proiezioni in un archivio informazioni
titleSuffix: Azure Cognitive Search
description: Esempi di modelli comuni su come proiettare documenti arricchiti nel Knowledge Store per l'uso con Power BI o Azure ML.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 23c370289669c2dde4f8969a2921018cd0abc08c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943674"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments"></a>Proiezioni del Knowledge Store: come modellare ed esportare gli arricchimenti

> [!IMPORTANT] 
> L'archivio conoscenze è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Queste funzionalità di anteprima vengono fornite dall'[API REST versione 2019-05-06-Preview](search-api-preview.md). Il supporto del portale è attualmente limitato e non è disponibile alcun supporto per .NET SDK.

Le proiezioni sono l'espressione fisica di documenti arricchiti in un knowledge store. L'uso efficace dei documenti arricchiti richiede una struttura. In questo articolo verranno esaminate sia la struttura che le relazioni, imparando a creare le proprietà di proiezione, nonché come correlare i dati tra i tipi di proiezione creati. 

Per creare una proiezione, è necessario modellare i dati utilizzando una [competenza Shaper](cognitive-search-skill-shaper.md) per creare un oggetto personalizzato o utilizzare la sintassi di modellazione in linea all'interno di una definizione di proiezione. 

Una forma di dati contiene tutti i dati che si intende proiettare, formati come una gerarchia di nodi. In questo articolo vengono illustrate diverse tecniche per la modellazione dei dati in modo che possano essere proiettati in strutture fisiche che sono favorevoli alla creazione di report, analisi o elaborazione a valle. 

Gli esempi presentati in questo articolo sono disponibili in questo esempio di [API REST,](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)che è possibile scaricare ed eseguire in un client HTTP.

## <a name="introduction-to-the-examples"></a>Introduzione agli esempi

Se si ha familiarità con [le proiezioni](knowledge-store-projection-overview.md), si ricorderà che ci sono tre tipi:

+ Tabelle
+ Oggetti
+ File

Le proiezioni di tabella vengono archiviate nell'archiviazione tabelle di Azure.Table projections are stored in Azure Table storage. Le proiezioni di oggetti e file vengono scritte nell'archiviazione BLOB, in cui le proiezioni di oggetti vengono salvate come file JSON e possono includere contenuto del documento di origine, nonché eventuali output o arricchimenti delle competenze. La pipeline di arricchimento può anche estrarre i file binari come le immagini, questi file binari vengono proiettati come proiezioni di file. Quando un oggetto binario viene proiettato come proiezione di un oggetto, solo i metadati ad esso associati vengono salvati come BLOB JSON. 

Per comprendere l'intersezione tra la definizione dei dati e le proiezioni, useremo il set di competenze seguente come base per esplorare varie configurazioni. Questo set di competenze elabora il contenuto di testo e immagini non elaborati. Le proiezioni saranno definite dal contenuto del documento e dai risultati delle competenze, per gli scenari che vogliamo supportare.

> [!IMPORTANT] 
> Quando si sperimentano le proiezioni, è utile [impostare la proprietà della cache dell'indicizzatore](search-howto-incremental-index.md) per garantire il controllo dei costi. La modifica delle proiezioni comporterà l'arricchimento dell'intero documento se la cache dell'indicizzatore non è impostata. Quando la cache è impostata e solo le proiezioni aggiornate, le esecuzioni del set di competenze per i documenti arricchiti in precedenza non comportano nuovi addebiti per servizi cognitivi.

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

Usando questo set di `knowledgeStore` competenze, con il relativo `knowledgeStore` valore null come base, il primo esempio compila l'oggetto, configurato con proiezioni che creano strutture di dati tabulari che possiamo usare in altri scenari. 

## <a name="projecting-to-tables"></a>Proiezione su tabelle

La proiezione in tabelle in Archiviazione di Azure è utile per la creazione di report e l'analisi tramite strumenti come Power BI. Power BI può leggere dalle tabelle e individuare le relazioni in base alle chiavi generate durante la proiezione. Se si sta tentando di creare un dashboard, la presenza di dati correlati semplificherà tale attività. 

Supponiamo che stiamo cercando di costruire un dashboard in cui possiamo visualizzare le frasi chiave estratte dai documenti come un cloud di parole. Per creare la struttura di dati corretta, è possibile aggiungere una competenza Shaper al set di competenze per creare una forma personalizzata con i dettagli specifici del documento e le frasi chiave. La forma personalizzata `pbiShape` verrà `document` chiamata sul nodo radice.

> [!NOTE] 
> Le proiezioni di tabella sono tabelle di Archiviazione di Azure, regolate dai limiti di archiviazione imposti da Archiviazione di Azure.Table projections are Azure Storage tables, governed by the storage limits mposed by Azure Storage. Per ulteriori informazioni, vedere [Limiti di archiviazione delle tabelle](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). È utile sapere che la dimensione dell'entità non può superare 1 MB e una singola proprietà non può essere superiore a 64 KB. Questi vincoli rendono le tabelle una buona soluzione per archiviare un numero elevato di entità di piccole dimensioni.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Uso di un'abilità Shaper per creare una forma personalizzata

Creare una forma personalizzata che è possibile proiettare nell'archivio tabelle. Senza una forma personalizzata, una proiezione può fare riferimento solo a un singolo nodo (una proiezione per output). La creazione di una forma personalizzata consente di aggregare vari elementi in un nuovo insieme logico che può essere proiettato come una singola tabella o suddiviso e distribuito in una raccolta di tabelle. 

In questo esempio, la forma personalizzata combina i metadati e le entità identificate e le frasi chiave. L'oggetto `pbiShape` viene chiamato ed `/document`è associato a . 

> [!IMPORTANT] 
> Uno scopo della forma consiste nel garantire che tutti i nodi di arricchimento siano espressi in JSON ben formato, necessario per la proiezione in archivio informazioni. Ciò è particolarmente vero quando un albero di arricchimento contiene nodi che non sono BEN formati JSON (ad esempio, quando un arricchimento è associato a una primitiva come una stringa).
>
> Si notino `KeyPhrases` gli `Entities`ultimi due nodi e . Questi sono incapsulati in `sourceContext`un oggetto JSON valido con il file . Questa operazione `keyphrases` è `entities` necessaria come e sono arricchimenti nelle primitive e devono essere convertiti in JSON valido prima che possano essere proiettati.
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

Aggiungi la competenza Shaper di cui sopra al set di competenze. 

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

Ora che abbiamo tutti i dati necessari per proiettare alle tabelle, aggiornare l'oggetto knowledgeStore con le definizioni di tabella. In questo esempio sono disponibili tre tabelle, `source` `generatedKeyName` definite impostando le `tableName`proprietà e .

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

È possibile elaborare il lavoro attenendosi alla seguente procedura:

1. Impostare ```storageConnectionString``` la proprietà su una stringa di connessione dell'account di archiviazione V2 valido.  

1. Aggiornare il set di competenze inviando la richiesta PUT.

1. Dopo aver aggiornato il set di competenze, eseguire l'indicizzatore. 

È ora possibile disporre di una proiezione di lavoro con tre tabelle. L'importazione di queste tabelle in Power BI dovrebbe comportare l'individuazione automatica delle relazioni da parte di Power BI.

Prima di passare all'esempio successivo, consente di rivedere aspetti della proiezione della tabella per comprendere i meccanismi di sezionamento e correlazione dei dati.

### <a name="slicing"></a>Affettare 

Il taglio è una tecnica che suddivide un'intera forma consolidata in parti costitutive. Il risultato è costituito da tabelle separate ma correlate che è possibile utilizzare singolarmente.

Nell'esempio, `pbiShape` è la forma consolidata (o nodo di arricchimento). Nella definizione `pbiShape` di proiezione, viene suddiviso in tabelle aggiuntive, ```keyPhrases``` che ```Entities```consentono di estrarre parti della forma, e . In Power BI questo è utile perché più entità e keyPhrases sono associate a ogni documento e si otterranno maggiori informazioni se è possibile visualizzare entità e keyPhrases come dati categorizzati.

Sezionamento genera implicitamente una relazione tra ```generatedKeyName``` le tabelle padre e figlio, utilizzando il nella tabella padre per creare una colonna con lo stesso nome nella tabella figlio. 

### <a name="naming-relationships"></a>Denominazione delle relazioni

Le ```generatedKeyName``` ```referenceKeyName``` proprietà e vengono utilizzate per correlare i dati tra tabelle o anche tra tipi di proiezione. Ogni riga nella tabella/proiezione figlio ha una proprietà che punta all'elemento padre. Il nome della colonna o della ```referenceKeyName``` proprietà nell'elemento figlio è l'elemento padre. Quando ```referenceKeyName``` l'oggetto non viene fornito, ```generatedKeyName``` il servizio viene impostato per impostazione predefinita sul padre. 

Power BI si basa su queste chiavi generate per individuare le relazioni all'interno delle tabelle. Se è necessaria la colonna nella tabella figlio ```referenceKeyName``` con un nome diverso, impostare la proprietà nella tabella padre. Un esempio potrebbe essere ```generatedKeyName``` quello di impostare l'ID as nella tabella pbiDocument e il ```referenceKeyName``` come DocumentID. In questo modo la colonna nelle tabelle pbiEntities e pbiKeyPhrases contenente l'ID documento denominato DocumentID.

## <a name="projecting-to-objects"></a>Proiezione di oggetti

Le proiezioni di oggetti non hanno le stesse limitazioni delle proiezioni di tabella e sono più adatte per proiettare documenti di grandi dimensioni. In questo esempio, proiettiamo l'intero documento in proiezione di un oggetto. Le proiezioni di oggetto sono limitate a una singola proiezione in un contenitore e non possono essere suddivise.

Per definire una proiezione di ```objects``` oggetto, useremo la matrice nelle proiezioni. È possibile generare una nuova forma utilizzando l'abilità Shaper o utilizzare la sagomatura in linea della proiezione dell'oggetto. Mentre nell'esempio di tabelle è stato illustrato l'approccio di creazione di una forma e sezionamento, in questo esempio viene illustrato l'utilizzo della forma inline. 

La forma in linea è la possibilità di creare una nuova forma nella definizione degli input in una proiezione. La forma in linea crea un oggetto anonimo identico a quello prodotto `pbiShape`da un'abilità Shaper (nel nostro caso, ). La forma in linea è utile se si definisce una forma che non si intende riutilizzare.

Il projections proprietà è una matrice. Per questo esempio, stiamo aggiungendo una nuova istanza di proiezione alla matrice, in cui la definizione di knowledgeStore contiene proiezioni inline. Quando si utilizzano proiezioni in linea, è possibile omettere l'abilità Shaper.

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

## <a name="projecting-to-file"></a>Proiezione su file

Le proiezioni di file sono immagini estratte dal documento di origine o output di arricchimento che possono essere proiettati dal processo di arricchimento. Le proiezioni di file, simili alle proiezioni di oggetti, vengono implementate come BLOB in Archiviazione di Azure e contengono l'immagine. 

Per generare una proiezione `files` di file, usiamo la matrice nell'oggetto proiezione. In questo esempio vengono proiettate tutte le `samplefile`immagini estratte dal documento in un contenitore denominato .

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

## <a name="projecting-to-multiple-types"></a>Proiezione su più tipi

Uno scenario più complesso potrebbe richiedere la proiezione del contenuto tra i tipi di proiezione. Ad esempio, se è necessario proiettare alcuni dati come frasi chiave ed entità in tabelle, salvare i risultati OCR del testo e del testo di layout come oggetti e quindi proiettare le immagini come file. 

In questo esempio, gli aggiornamenti al set di competenze includono le seguenti modifiche:

1. Creare una tabella con una riga per ogni documento.
1. Creare una tabella correlata alla tabella del documento con ogni frase chiave identificata come riga in questa tabella.
1. Creare una tabella correlata alla tabella documenti con ogni entità identificata come riga in questa tabella.
1. Creare una proiezione di oggetto con il testo del formato per ogni immagine.
1. Creare una proiezione di file, proiettando ogni immagine estratta.
1. Create una tabella di riferimenti incrociati che contiene riferimenti alla tabella di documento, la proiezione dell'oggetto con il testo del formato e la proiezione del file.

Queste modifiche si riflettono nella definizione di knowledgeStore più in basso. 

### <a name="shape-data-for-cross-projection"></a>Dati della forma per la proiezione incrociata

Per ottenere le forme necessarie per queste proiezioni, inizia aggiungendo una nuova `crossProjection`abilità Shaper che crea un oggetto sagomato chiamato . 

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

### <a name="define-table-object-and-file-projections"></a>Definire proiezioni di tabelle, oggetti e file

Dall'oggetto crossProjection consolidato, è possibile suddividere l'oggetto in più tabelle, acquisire l'output OCR come BLOB e quindi salvare l'immagine come file (anche nell'archivio BLOB).

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

Le proiezioni di oggetti richiedono un nome di contenitore per ogni proiezione, le proiezioni di oggetti o le proiezioni di file non possono condividere un contenitore. 

### <a name="relationships-among-table-object-and-file-projections"></a>Relazioni tra proiezioni di tabelle, oggetti e file

Questo esempio evidenzia anche un'altra caratteristica delle proiezioni. Definendo più tipi di proiezioni all'interno dello stesso oggetto di proiezione, esiste una relazione espressa all'interno e tra i diversi tipi (tabelle, oggetti, file), che consente di iniziare con una riga di tabella per un documento e trovare tutto il testo OCR per le immagini all'interno di tale documento nella proiezione dell'oggetto. 

Se non si desidera che i dati siano correlati, definire le proiezioni in diversi oggetti di proiezione. Ad esempio, il frammento di codice seguente comporterà la correlazione tra le tabelle, ma senza relazioni tra le tabelle e le proiezioni dell'oggetto (testo OCR). 

I gruppi di proiezione sono utili quando si desidera proiettare gli stessi dati in forme diverse per esigenze diverse. Ad esempio, un gruppo di proiezione per il dashboard di Power BI e un altro gruppo di proiezione per l'acquisizione dei dati usati per eseguire il training di un modello di apprendimento automatico avvolto in una competenza personalizzata.

Quando si creano proiezioni di tipi diversi, le proiezioni di file e oggetti vengono generate per prime e i tracciati vengono aggiunti alle tabelle.

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

Quando si definisce una proiezione, esistono alcuni problemi comuni che possono causare risultati imprevisti. Verificare la presenza di questi problemi se l'output nell'archivio informazioni non è quello previsto.

+ Non modellare gli arricchimenti di stringhe in JSON valido. Quando le stringhe vengono `merged_content` arricchite, ad esempio arricchite con frasi chiave, `merged_content` la proprietà arricchita è rappresentata come figlio dell'albero di arricchimento. La rappresentazione predefinita non è JSON in formato corretto. Così al momento di proiezione, assicurarsi di trasformare l'arricchimento in un oggetto JSON valido con un nome e un valore.

+ Omettendo ```/*``` l'oggetto alla fine di un percorso di origine. Se l'origine di `/document/pbiShape/keyPhrases`una proiezione è , la matrice di frasi chiave viene proiettata come un singolo oggetto/riga. Impostare invece il `/document/pbiShape/keyPhrases/*` percorso di origine in modo da restituire una singola riga o oggetto per ognuna delle frasi chiave.

+ Errori di sintassi del percorso. I selettori di percorso fanno distinzione tra maiuscole e minuscole e possono causare avvisi di input mancanti se non si utilizza la combinazione esatta di maiuscole e minuscole per il selettore.

## <a name="next-steps"></a>Passaggi successivi

Gli esempi in questo articolo illustrano modelli comuni su come creare proiezioni. Ora che hai una buona comprensione dei concetti, sei meglio attrezzato per creare proiezioni per il tuo scenario specifico.

Quando si esplorano le nuove funzionalità, considerare l'arricchimento incrementale come passaggio successivo. L'arricchimento incrementale si basa sulla memorizzazione nella cache, che consente di riutilizzare eventuali arricchimenti che non sono altrimenti interessati da una modifica del set di competenze. Ciò è particolarmente utile per le pipeline che includono l'OCR e l'analisi delle immagini.

> [!div class="nextstepaction"]
> [Introduzione all'arricchimento incrementale e alla memorizzazione nella cache](cognitive-search-incremental-indexing-conceptual.md)

Per una panoramica sulle proiezioni, scopri di più sulle funzionalità come i gruppi e il sezionamento e su come [definirle in un set di competenze](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Proiezioni in un archivio di conoscenze](knowledge-store-projection-overview.md)

