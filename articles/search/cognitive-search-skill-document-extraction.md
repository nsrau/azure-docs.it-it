---
title: Competenza cognitiva di Estrazione documento (anteprima)Document Extraction cognitive skill (preview)
titleSuffix: Azure Cognitive Search
description: Estrae il contenuto da un file all'interno della pipeline di arricchimento. Questa abilità è attualmente in anteprima pubblica.
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76837732"
---
# <a name="document-extraction-cognitive-skill"></a>Abilità cognitiva di Estrazione documento

> [!IMPORTANT] 
> Questa abilità è attualmente in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Attualmente non è disponibile alcun supporto per il portale o .NET SDK.

La competenza **Estrazione documento** estrae il contenuto da un file all'interno della pipeline di arricchimento. Ciò consente di sfruttare la fase di estrazione del documento che normalmente avviene prima dell'esecuzione del set di competenze con file che possono essere generati da altre competenze.

> [!NOTE]
> Man mano che si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti o aggiungendo più algoritmi di ia', sarà necessario [collegare una risorsa servizi cognitivi fatturabile.](cognitive-search-attach-cognitive-services.md) Gli addebiti vengono accumulati quando si chiamano le API in Servizi cognitivi e per l'estrazione delle immagini come parte della fase di distribuzione dei documenti nell'indicizzazione. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione delle immagini sono descritti nella pagina dei [prezzi.](https://go.microsoft.com/fwlink/?linkid=2042400)
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Input            | Valori consentiti | Descrizione |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | Impostare `default` su per l'estrazione di documenti da file che non sono testo puro o json. Impostare `text` su per migliorare le prestazioni dei file di testo normale. Impostare `json` su per estrarre contenuto strutturato da file json. Se `parsingMode` non è definito in modo `default`esplicito, verrà impostato su . |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | Impostare `contentAndMetadata` su per estrarre tutti i metadati e il contenuto testuale da ogni file. Impostare `allMetadata` su per estrarre solo i [metadati specifici](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) del tipo di contenuto (ad esempio, i metadati univoci solo per i file png). Se `dataToExtract` non è definito in modo `contentAndMetadata`esplicito, verrà impostato su . |
| `configuration` | Vedere di seguito. | Dizionario di parametri facoltativi che regolano la modalità di esecuzione dell'estrazione del documento. Vedere la tabella seguente per le descrizioni delle proprietà di configurazione supportate. |

| Parametro di configurazione   | Valori consentiti | Descrizione |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | Impostare `none` su per ignorare le immagini incorporate o i file di immagine nel set di dati. Questa è la modalità predefinita. <br/>Per [l'analisi](cognitive-search-concept-image-scenarios.md)delle immagini `generateNormalizedImages` utilizzando le abilità cognitive , impostare per avere la competenza creare una serie di immagini normalizzate come parte del documento cracking. Questa azione `parsingMode` richiede che `default` `dataToExtract` sia impostata su e impostata su `contentAndMetadata`. Per immagine normalizzata si intende un'elaborazione aggiuntiva che produce un output dell'immagine uniforme, ridimensionato e ruotato per favorire un rendering coerente quando si includono immagini nei risultati della ricerca visiva (ad esempio, fotografie delle stesse dimensioni in un controllo grafico, come illustrato nella [demo su JFK](https://github.com/Microsoft/AzureSearch_JFK_Files)). Queste informazioni vengono generate per ogni immagine quando si utilizza questa opzione.  <br/>Se si `generateNormalizedImagePerPage`imposta su , i file PDF verranno trattati in modo diverso in quanto invece di estrarre le immagini incorporate, ogni pagina verrà renderizzata come immagine e normalizzata di conseguenza.  I tipi di file non PDF `generateNormalizedImages` verranno trattati allo stesso modo come se fosse stato impostato.
| `normalizedImageMaxWidth` | Qualsiasi numero intero compreso tra 50 e 10000 | La larghezza massima (in pixel) per le immagini normalizzate generate. Il valore predefinito è 2000. | 
| `normalizedImageMaxHeight` | Qualsiasi numero intero compreso tra 50 e 10000 | L'altezza massima (in pixel) per le immagini normalizzate generate. Il valore predefinito è 2000. |

> [!NOTE]
> Il valore predefinito di 2000 pixel per i valori massimi di altezza e larghezza delle immagini normalizzate è basato sulle dimensioni massime supportate dalla [competenza OCR](cognitive-search-skill-ocr.md) e dalla [competenza di analisi delle immagini](cognitive-search-skill-image-analysis.md). La [competenza OCR](cognitive-search-skill-ocr.md) supporta una larghezza e un'altezza massime di 4200 per le lingue diverse dall'inglese e 10000 per l'inglese.  Se si aumentano i limiti massimi, l'elaborazione potrebbe non riuscire su immagini più grandi a seconda della definizione del set di competenze e della lingua dei documenti. 
## <a name="skill-inputs"></a>Input competenze

| Nome input     | Descrizione |
|--------------------|-------------|
| file_data | Il file da cui deve essere estratto il contenuto. |

L'input "file_data" deve essere un oggetto definito come segue:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Questo oggetto di riferimento file può essere generato in uno dei tre modi seguenti:This file reference object can be generated one of 3 ways:

 - Impostazione `allowSkillsetToReadFileData` del parametro nella definizione dell'indicizzatore su "true".  Verrà creato un `/document/file_data` percorso che rappresenta un oggetto che rappresenta i dati del file originale scaricati dall'origine dati BLOB. Questo parametro si applica solo ai dati nell'archivio BLOB.

 - L'impostazione del `imageAction` parametro nella definizione dell'indicizzatore su un valore diverso da `none`.  In questo modo viene creata una matrice di immagini che segue la convenzione richiesta per l'input a questa competenza se passata singolarmente (ad esempio `/document/normalized_images/*`).

 - La presenza di una competenza personalizzata restituisce un oggetto json definito ESATTAMENTE come sopra.  Il `$type` parametro deve `file` essere `data` impostato esattamente e il parametro deve essere costituito dai dati della matrice di byte con codifica base 64 del contenuto del file.

## <a name="skill-outputs"></a>Output competenze

| Nome output    | Descrizione |
|--------------|-------------|
| content | Contenuto testuale del documento. |
| normalized_images | Quando `imageAction` l'oggetto è impostato `none`su un valore diverso, il nuovo campo *normalized_images* conterrà una matrice di immagini. Vedere [la documentazione per l'estrazione dell'immagine](cognitive-search-concept-image-scenarios.md) per ulteriori dettagli sul formato di output di ogni immagine. |

##  <a name="sample-definition"></a>Definizione di esempio

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>Input di esempio

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>Output di esempio

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
+ [Come elaborare ed estrarre informazioni da immagini in scenari di ricerca cognitiva](cognitive-search-concept-image-scenarios.md)