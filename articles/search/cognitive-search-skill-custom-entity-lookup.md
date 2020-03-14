---
title: Abilità di ricerca cognitiva per la ricerca di entità personalizzate
titleSuffix: Azure Cognitive Search
description: Estrae entità personalizzate diverse dal testo in una pipeline di ricerca cognitiva di Azure ricerca cognitiva. Questa competenza è attualmente disponibile in anteprima pubblica.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 8674438032ebd925296c95e9ffa0a2a0b95322f1
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79369778"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Abilità cognitiva per la ricerca di entità personalizzate (anteprima)

> [!IMPORTANT] 
> Questa competenza è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Attualmente non è disponibile alcun portale o supporto per .NET SDK.

L'abilità di **ricerca di entità personalizzata** Cerca testo da un elenco personalizzato di parole e frasi definito dall'utente. Utilizzando questo elenco, vengono etichettati tutti i documenti con le entità corrispondenti. L'abilità supporta inoltre un grado di corrispondenza fuzzy che può essere applicato per trovare corrispondenze simili ma non esatte.  

Questa competenza non è associata a un'API servizi cognitivi e può essere usata gratuitamente durante il periodo di anteprima. Tuttavia, è comunque necessario [aggiungere una risorsa Servizi cognitivi](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)per sostituire il limite di arricchimento giornaliero. Il limite giornaliero si applica all'accesso gratuito ai servizi cognitivi quando si accede tramite ricerca cognitiva di Azure.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. CustomEntityLookupSkill 

## <a name="data-limits"></a>Limiti dei dati
+ La dimensione massima dei record di input supportata è 256 MB. Se è necessario suddividere i dati prima di inviarli alla competenza personalizzata per la ricerca di entità, provare a usare la [capacità di suddivisione del testo](cognitive-search-skill-textsplit.md).
+ La tabella di definizione delle entità massima supportata è 10 MB se viene fornita utilizzando il parametro *entitiesDefitionUri* . 
+ Se le entità sono definite inline, usando il parametro *inlineEntitiesDefinition* , le dimensioni massime supportate sono di 10 KB.

## <a name="skill-parameters"></a>Parametri della competenza

Per i parametri viene fatta distinzione tra maiuscole e minuscole.

| Nome parametro     | Descrizione |
|--------------------|-------------|
| entitiesDefinitionUri    | Percorso di un file JSON o CSV contenente tutto il testo di destinazione rispetto al quale eseguire la corrispondenza. Questa definizione di entità viene letta all'inizio dell'esecuzione di un indicizzatore; eventuali aggiornamenti al file a metà esecuzione non verranno realizzati fino alle esecuzioni successive. Questa configurazione deve essere accessibile tramite HTTPS. Per lo schema CSV o JSON previsto, vedere il formato di [definizione dell'entità personalizzata](#custom-entity-definition-format) ".|
|inlineEntitiesDefinition | Definizioni di entità JSON inline. Questo parametro sostituisce il parametro entitiesDefinitionUri se presente. Non è possibile fornire inline più di 10 KB di configurazione. Vedere la [definizione di entità personalizzata](#custom-entity-definition-format) riportata di seguito per lo schema JSON previsto. |
|defaultLanguageCode |    Opzionale Codice di lingua del testo di input utilizzato per tokenize e delineare il testo di input. Sono supportate le seguenti lingue: `da, de, en, es, fi, fr, it, ko, pt`. Il valore predefinito è inglese (`en`). Se si passa un formato languagecode-countrycode, viene usata solo la parte languagecode del formato.  |


## <a name="skill-inputs"></a>Input competenze

| Nome input      | Descrizione                   |
|---------------|-------------------------------|
| testo          | Testo da analizzare.          |
| languageCode    | Facoltativa. Il valore predefinito è `"en"`.  |


## <a name="skill-outputs"></a>Output competenze


| Nome output      | Descrizione                   |
|---------------|-------------------------------|
| entities | Matrice di oggetti che contengono informazioni sulle corrispondenze trovate e sui metadati correlati. Ognuna delle entità identificate può contenere i campi seguenti:  <ul> <li> *Name*: entità di primo livello identificata. L'entità rappresenta il form "normalizzato". </li> <li> *ID*: identificatore univoco per l'entità in base a quanto definito dall'utente nel "formato di definizione dell'entità personalizzata".</li> <li> *Descrizione*: Descrizione dell'entità definita dall'utente nel "formato di definizione dell'entità personalizzata". </li> <li> *tipo:* Tipo di entità definito dall'utente nel formato di definizione dell'entità personalizzata.</li> <li> *Sottotipo:* Sottotipo di entità definito dall'utente nel "formato di definizione dell'entità personalizzata".</li>  <li> *Matches*: raccolta che descrive ognuna delle corrispondenze per quell'entità nel testo di origine. Ogni corrispondenza avrà i membri seguenti: </li> <ul> <li> *Text*: il testo non elaborato corrisponde al documento di origine. </li> <li> *offset*: posizione in cui è stata trovata la corrispondenza nel testo. </li> <li> *length*: lunghezza del testo corrispondente. </li> <li> *matchDistance*: il numero di caratteri diverso da questa corrispondenza è stato dal nome dell'entità originale o dall'alias.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Formato di definizione dell'entità personalizzata

Esistono tre modi diversi per fornire l'elenco di entità personalizzate alla capacità di ricerca di entità personalizzata. È possibile specificare l'elenco in un oggetto. File CSV, a. File JSON o come definizione inline come parte della definizione di competenze.  

Se il file di definizione è un oggetto. CSV o. File JSON, il percorso del file deve essere fornito come parte del parametro *entitiesDefitionUri* . In questo caso, il file viene scaricato una volta all'inizio di ogni indicizzatore eseguito. Il file deve essere accessibile fino a quando l'indicizzatore deve essere eseguito. Inoltre, il file deve essere codificato in UTF-8.

Se la definizione viene fornita inline, deve essere fornita come inline come contenuto del parametro skill *inlineEntitiesDefinition* . 

### <a name="csv-format"></a>Formato CSV

È possibile specificare la definizione delle entità personalizzate da cercare in un file con valori delimitati da virgole (CSV) specificando il percorso del file e impostandolo nel parametro skill *entitiesDefitionUri* . Il percorso deve trovarsi in una posizione HTTPS. Il file di definizione può avere dimensioni massime di 10 MB.

Il formato CSV è semplice. Ogni riga rappresenta un'entità univoca, come illustrato di seguito:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

In questo caso, esistono tre entità che possono essere restituite come entità trovate (Bill Gates, Nadella, Microsoft), ma verranno identificate se uno dei termini sulla riga (alias) corrisponde al testo. Se, ad esempio, la stringa "William H. Gates" si trova in un documento, verrà restituita una corrispondenza per l'entità "Bill Gates".

### <a name="json-format"></a>Formato JSON

È possibile specificare anche la definizione delle entità personalizzate da cercare in un file JSON. Il formato JSON offre una maggiore flessibilità perché consente di definire regole di corrispondenza per termine. Ad esempio, è possibile specificare la distanza di corrispondenza fuzzy (Damerau-Levenshtein distance) per ogni termine o se la corrispondenza deve fare distinzione tra maiuscole e minuscole. 

 Analogamente a quanto avviene con i file CSV, è necessario specificare il percorso del file JSON e impostarlo nel parametro skill *entitiesDefitionUri* . Il percorso deve trovarsi in una posizione HTTPS. Il file di definizione può avere dimensioni massime di 10 MB.

La definizione di elenco di entità personalizzate JSON più semplice può essere un elenco di entità per le quali trovare una corrispondenza:

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

Un esempio più complesso di una definizione JSON può facoltativamente fornire l'ID, la descrizione, il tipo e il sottotipo di ogni entità, oltre ad altri *alias*. Se viene trovato un termine di alias, verrà restituita anche l'entità:

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

Le tabelle seguenti descrivono in modo più dettagliato i diversi parametri di configurazione che è possibile impostare quando si definiscono le entità corrispondenti:

|  Nome del campo  |        Descrizione  |
|--------------|----------------------|
| name | Descrittore di entità di primo livello. Le corrispondenze nell'output skill verranno raggruppate in base a questo nome e dovrebbe rappresentare il formato "normalizzato" del testo trovato.  |
| description  | Opzionale Questo campo può essere utilizzato come passthrough per i metadati personalizzati relativi ai testi corrispondenti. Il valore di questo campo verrà visualizzato con ogni corrispondenza della relativa entità nell'output della competenza. |
| type | Opzionale Questo campo può essere utilizzato come passthrough per i metadati personalizzati relativi ai testi corrispondenti. Il valore di questo campo verrà visualizzato con ogni corrispondenza della relativa entità nell'output della competenza. |
| subtype | Opzionale Questo campo può essere utilizzato come passthrough per i metadati personalizzati relativi ai testi corrispondenti. Il valore di questo campo verrà visualizzato con ogni corrispondenza della relativa entità nell'output della competenza. |
| id | Opzionale Questo campo può essere utilizzato come passthrough per i metadati personalizzati relativi ai testi corrispondenti. Il valore di questo campo verrà visualizzato con ogni corrispondenza della relativa entità nell'output della competenza. |
| caseSensitive | Opzionale Il valore predefinito è false. Valore booleano che indica se i confronti con il nome dell'entità devono essere sensibili alle maiuscole e minuscole dei caratteri. Le corrispondenze senza distinzione tra maiuscole e minuscole di esempio di "Microsoft" possono essere: Microsoft, microSoft, MICROSOFT |
| fuzzyEditDistance | Opzionale Il valore predefinito è 0. Valore massimo di 5. Indica il numero accettabile di caratteri divergenti che costituirebbero comunque una corrispondenza con il nome dell'entità. Viene restituito il minor confusione possibile per ogni corrispondenza specificata.  Se ad esempio la distanza di modifica è impostata su 3, "Windows 10" corrisponderà ancora a "Windows", "Windows10" e "Windows 7". <br/> Se la distinzione tra maiuscole e minuscole è impostata su false, le differenze tra maiuscole e minuscole non vengono conteggiate per la tolleranza confusione. |
| defaultCaseSensitive | Opzionale Modifica il valore predefinito della distinzione tra maiuscole e minuscole per questa entità. Viene usato per modificare il valore predefinito di tutti i valori caseSensitive di alias. |
| defaultFuzzyEditDistance | Opzionale Modifica il valore predefinito della distanza di modifica fuzzy per questa entità. Può essere usato per modificare il valore predefinito di tutti i valori fuzzyEditDistance di alias. |
| alias | Opzionale Matrice di oggetti complessi che può essere utilizzata per specificare ortografie alternative o sinonimi per il nome dell'entità radice. |

| Proprietà alias | Descrizione |
|------------------|-------------|
| testo  | Ortografia alternativa o rappresentazione di un nome di entità di destinazione.  |
| caseSensitive | Opzionale Funziona allo stesso modo del parametro "caseSensitive" dell'entità radice, ma si applica solo a questo alias. |
| fuzzyEditDistance | Opzionale Funziona allo stesso modo del parametro "fuzzyEditDistance" dell'entità radice, ma si applica solo a questo alias. |


### <a name="inline-format"></a>Formato inline

In alcuni casi, può essere più pratico fornire l'elenco di entità personalizzate in modo che corrispondano direttamente alla definizione delle competenze. In tal caso, è possibile usare un formato JSON simile a quello descritto in precedenza, ma è inline nella definizione di competenze.
Solo le configurazioni di dimensioni inferiori a 10 KB (dimensioni serializzate) possono essere definite inline. 

##    <a name="sample-definition"></a>Definizione di esempio

Di seguito è riportata una definizione di competenze di esempio con un formato inline:

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
In alternativa, se si decide di fornire un puntatore al file di definizione delle entità, viene illustrata una definizione di competenze di esempio che usa il formato entitiesDefinitionUri:

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

##    <a name="sample-output"></a>Output dell'esempio:

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

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Avviso: è stata raggiunta la capacità massima per le corrispondenze, ignorando tutte le corrispondenze duplicate.

Questo avviso viene generato se il numero di corrispondenze rilevate è maggiore del valore massimo consentito. In questo caso, verrà interrotto l'inclusione di corrispondenze duplicate. Se questa operazione non è accettabile, inviare un [ticket di supporto](https://ms.portal.azure.com/#create/Microsoft.Support) in modo che sia possibile fornire assistenza per i singoli casi d'uso.

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Abilità di riconoscimento entità (per la ricerca di entità note)](cognitive-search-skill-entity-recognition.md)
