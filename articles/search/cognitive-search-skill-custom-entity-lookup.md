---
title: Competenza di ricerca cognitiva Ricerca entità personalizzata
titleSuffix: Azure Cognitive Search
description: Estrarre entità personalizzate diverse dal testo in una pipeline di ricerca cognitiva di Ricerca cognitiva di Azure.Extract different custom entities from text in an Azure Cognitive Search cognitive search pipeline. Questa abilità è attualmente in anteprima pubblica.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 8674438032ebd925296c95e9ffa0a2a0b95322f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369778"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Competenza cognitiva ricerca entità personalizzata (anteprima)Custom Entity Lookup cognitive skill (Preview)

> [!IMPORTANT] 
> Questa abilità è attualmente in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Attualmente non è disponibile alcun supporto per il portale o .NET SDK.

La competenza **Ricerca entità personalizzata** cerca il testo di un elenco personalizzato di parole e frasi definito dall'utente. Utilizzando questo elenco, vengono etichettati tutti i documenti con tutte le entità corrispondenti. L'abilità supporta anche un grado di corrispondenza fuzzy che può essere applicato per trovare corrispondenze che sono simili ma non del tutto esatte.  

Questa competenza non è associata a un'API di Servizi cognitivi e può essere utilizzata gratuitamente durante il periodo di anteprima. È comunque consigliabile collegare una risorsa di [Servizi cognitivi,](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)tuttavia, per ignorare il limite di arricchimento giornaliero. Il limite giornaliero si applica all'accesso gratuito ai servizi cognitivi quando si accede tramite Ricerca cognitiva di Azure.The daily limit applies to free access to Cognitive Services when accessed through Azure Cognitive Search.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>Limiti dei dati
+ La dimensione massima del record di input supportata è 256 MB. Se è necessario suddividere i dati prima di inviarli alla competenza di ricerca di entità personalizzata, è consigliabile usare la [competenza Suddivisione testo](cognitive-search-skill-textsplit.md).
+ La tabella di definizione delle entità massima supportata è 10 MB se viene fornita utilizzando il parametro *entitiesDefitionUri.* 
+ Se le entità sono definite inline, utilizzando il parametro *inlineEntitiesDefinition,* la dimensione massima supportata è 10 KB.

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | Descrizione |
|--------------------|-------------|
| entitàDefinizioneUri    | Percorso di un file JSON o CSV contenente tutto il testo di destinazione da confrontare. Questa definizione di entità viene letta all'inizio di un'esecuzione dell'indicizzatore. eventuali aggiornamenti a questo file a metà esecuzione non saranno realizzati fino alle esecuzioni successive. Questa configurazione deve essere accessibile tramite HTTPS. Vedere [Custom Entity Definition](#custom-entity-definition-format) Format" di seguito per lo schema CSV o JSON previsto.|
|inlineEntitiesDefinizione | Definizioni di entità JSON inline. Questo parametro sostituisce il parametro entitiesDefinitionUri, se presente. Non possono essere forniti inline oltre 10 KB di configurazione. Vedere [Definizione entità personalizzata](#custom-entity-definition-format) di seguito per lo schema JSON previsto. |
|defaultLanguageCode |    (Facoltativo) Codice lingua del testo di input utilizzato per suddividere in token e delineare il testo di input. Sono supportate le `da, de, en, es, fi, fr, it, ko, pt`seguenti lingue: . Il valore predefinito`en`è English ( ). Se si passa un formato languagecode-countrycode, viene usata solo la parte languagecode del formato.  |


## <a name="skill-inputs"></a>Input competenze

| Nome input      | Descrizione                   |
|---------------|-------------------------------|
| text          | Testo da analizzare.          |
| languageCode    | Facoltativa. Il valore predefinito è `"en"`.  |


## <a name="skill-outputs"></a>Output competenze


| Nome output      | Descrizione                   |
|---------------|-------------------------------|
| entities | Matrice di oggetti che contengono informazioni sulle corrispondenze trovate e sui metadati correlati. Ognuna delle entità identificate può contenere i seguenti campi:  <ul> <li> *name*: L'entità di primo livello identificata. L'entità rappresenta il modulo "normalizzato". </li> <li> *id*: Un identificatore univoco per l'entità come definito dall'utente nel "Formato di definizione dell'entità personalizzata".</li> <li> *description*: Descrizione dell'entità come definito dall'utente nel "Formato di definizione dell'entità personalizzata". </li> <li> *tipo:* Tipo di entità come definito dall'utente nel "Formato di definizione di entità personalizzato".</li> <li> *sottotipo:* Sottotipo di entità come definito dall'utente nel "Formato di definizione di entità personalizzato".</li>  <li> *corrisponde*: insieme che descrive ciascuna delle corrispondenze per l'entità nel testo di origine. Ogni partita avrà i seguenti membri: </li> <ul> <li> *text*: Il testo non elaborato corrisponde al documento di origine. </li> <li> *offset*: La posizione in cui è stata trovata la corrispondenza nel testo. </li> <li> *length*: La lunghezza del testo corrispondente. </li> <li> *matchDistance*: Il numero di caratteri diversi da questa corrispondenza è stato dal nome dell'entità originale o dall'alias.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Formato di definizione entità personalizzato

Esistono 3 modi diversi per fornire l'elenco di entità personalizzate alla competenza Ricerca entità personalizzata. È possibile fornire l'elenco in un file . CSV, un file . File JSON o come definizione inline come parte della definizione di competenza.  

Se il file di definizione è un file . CSV o . JSON, il percorso del file deve essere fornito come parte del parametro *entitiesDefitionUri.* In questo caso, il file viene scaricato una volta all'inizio di ogni esecuzione dell'indicizzatore. Il file deve essere accessibile finché è destinato all'esecuzione dell'indicizzatore. Inoltre, il file deve essere codificato UTF-8.

Se la definizione viene fornita inline, deve essere fornita come inline come contenuto del parametro di abilità *inlineEntitiesDefinition.* 

### <a name="csv-format"></a>Formato CSV

È possibile fornire la definizione delle entità personalizzate da cercare in un file CSV (Comma-Separated Value) specificando il percorso del file e impostandolo nel parametro di abilità *entitiesDefitionUri.* Il percorso deve trovarsi in un percorso https. Le dimensioni del file di definizione possono avere una dimensione massima di 10 MB.

Il formato CSV è semplice. Ogni riga rappresenta un'entità univoca, come illustrato di seguito:Each line represents a unique entity, as shown below:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

In questo caso, ci sono tre entità che possono essere restituite come entità trovate (Bill Gates, Satya Nadella, Microsoft), ma verranno identificate se uno dei termini sulla linea (alias) sono abbinati nel testo. Ad esempio, se la stringa "William H. Gates" viene trovata in un documento, verrà restituita una corrispondenza per l'entità "Bill Gates".

### <a name="json-format"></a>Formato JSON

È possibile fornire la definizione delle entità personalizzate da cercare anche in un file JSON. Il formato JSON offre un po' più flessibilità poiché consente di definire le regole di corrispondenza per termine. Ad esempio, è possibile specificare la distanza di corrispondenza fuzzy (distanza damerau-Levenshtein) per ogni termine o se la corrispondenza deve essere tra maiuscole e minuscole o meno. 

 Proprio come con i file CSV, è necessario fornire il percorso del file JSON e impostarlo nel parametro di abilità *entitiesDefitionUri.* Il percorso deve trovarsi in un percorso https. Le dimensioni del file di definizione possono avere una dimensione massima di 10 MB.

La definizione di elenco di entità personalizzate JSON più semplice può essere un elenco di entità di corrispondenza:The most basic JSON custom entity list definition can be a list of entities to match:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Un esempio più complesso di una definizione JSON può facoltativamente fornire l'id, la descrizione, il tipo e il sottotipo di ogni entità, nonché altri *alias.* Se viene trovato un termine alias, verrà restituita anche l'entità:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

Le tabelle seguenti descrivono in modo più indettagli i diversi parametri di configurazione che è possibile impostare quando si definiscono le entità da abbinare:

|  Nome campo  |        Descrizione  |
|--------------|----------------------|
| name | Descrittore di entità di primo livello. Le corrispondenze nell'output delle competenze verranno raggruppate in base a questo nome e dovrebbero rappresentare la forma "normalizzata" del testo trovato.  |
| description  | (Facoltativo) Questo campo può essere utilizzato come passthrough per i metadati personalizzati sui testi corrispondenti. Il valore di questo campo verrà visualizzato con ogni corrispondenza della relativa entità nell'output delle competenze. |
| type | (Facoltativo) Questo campo può essere utilizzato come passthrough per i metadati personalizzati sui testi corrispondenti. Il valore di questo campo verrà visualizzato con ogni corrispondenza della relativa entità nell'output delle competenze. |
| subtype | (Facoltativo) Questo campo può essere utilizzato come passthrough per i metadati personalizzati sui testi corrispondenti. Il valore di questo campo verrà visualizzato con ogni corrispondenza della relativa entità nell'output delle competenze. |
| id | (Facoltativo) Questo campo può essere utilizzato come passthrough per i metadati personalizzati sui testi corrispondenti. Il valore di questo campo verrà visualizzato con ogni corrispondenza della relativa entità nell'output delle competenze. |
| caseSensitive (caso)caseSensitive | (Facoltativo) Il valore predefinito è false. Valore booleano che indica se i confronti con il nome dell'entità devono essere sensibili all'uso di maiuscole e minuscole dei caratteri. Le corrispondenze di "Microsoft" senza distinzione tra maiuscole e minuscole di esempio potrebbero essere: microsoft, microSoft, MICROSOFTSample case insensitive matches of "Microsoft" could be: microsoft, microSoft, MICROSOFT |
| fuzzyEditDistance | (Facoltativo) Il valore predefinito è 0. Valore massimo di 5. Indica il numero accettabile di caratteri divergenti che costituirebbero comunque una corrispondenza con il nome dell'entità. Viene restituita la più piccola confusione possibile per una determinata partita.  Ad esempio, se la distanza di modifica è impostata su 3, "Windows 10" corrisponderebbe comunque a "Windows", "Windows10" e "windows 7". <br/> Quando la distinzione tra maiuscole e minuscole è impostata su false, le differenze tra maiuscole e minuscole NON vengono conteggiate per la tolleranza di confusione, ma in caso contrario. |
| defaultCaseSensitive (maiuscole/minuscole) | (Facoltativo) Modifica il valore predefinito per la distinzione tra maiuscole e minuscole per questa entità. Viene utilizzato per modificare il valore predefinito di tutti gli alias caseSensitive valori. |
| defaultFuzzyEditDistance | (Facoltativo) Modifica il valore predefinito della distanza di modifica fuzzy per questa entità. Può essere utilizzato per modificare il valore predefinito di tutti gli alias fuzzyEditDistance valori. |
| alias | (Facoltativo) Matrice di oggetti complessi che può essere utilizzata per specificare ortografie o sinonimi alternativi al nome dell'entità radice. |

| Proprietà alias | Descrizione |
|------------------|-------------|
| text  | L'ortografia o la rappresentazione alternativa di un nome di entità di destinazione.  |
| caseSensitive (caso)caseSensitive | (Facoltativo) Equivale al parametro "caseSensitive" dell'entità radice precedente, ma si applica solo a questo alias. |
| fuzzyEditDistance | (Facoltativo) Equivale al parametro "fuzzyEditDistance" dell'entità radice precedente, ma si applica a questo solo alias. |


### <a name="inline-format"></a>Formato in linea

In alcuni casi, può essere più conveniente fornire l'elenco di entità personalizzate da associare direttamente nella definizione della competenza. In tal caso è possibile usare un formato JSON simile a quello descritto in precedenza, ma è inline nella definizione della competenza.
Solo le configurazioni di dimensioni inferiori a 10 KB (dimensioni serializzate) possono essere definite inline. 

##    <a name="sample-definition"></a>Definizione di esempio

Di seguito è riportata una definizione di competenza di esempio che usa un formato inline:A sample skill definition using an inline format is shown below:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
In alternativa, se si decide di fornire un puntatore al file di definizione delle entità, viene illustrata una definizione di competenza di esempio usando il formato entitiesDefinitionUri:Alternatively, if you decide to provide a pointer to the entities definition file, a sample skill definition using the entitiesDefinitionUri format is shown below:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Input di esempio

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Output di esempio

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Errori e avvisi

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Avviso: ha raggiunto la capacità massima per le corrispondenze, ignorando tutte le altre corrispondenze duplicate.

Questo avviso verrà generato se il numero di corrispondenze rilevate è maggiore del valore massimo consentito. In questo caso, smetteremo di includere le corrispondenze duplicate. Se questo è inaccettabile per voi, si prega di presentare un ticket di [supporto](https://ms.portal.azure.com/#create/Microsoft.Support) in modo che possiamo aiutarvi con il vostro caso d'uso individuale.

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Competenza di riconoscimento entità (per cercare entità note)](cognitive-search-skill-entity-recognition.md)
