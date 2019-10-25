---
title: Competenza cognitiva Riconoscimento delle entità
titleSuffix: Azure Cognitive Search
description: Estrarre tipi diversi di entità dal testo in una pipeline di arricchimento in Azure ricerca cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 08e9656e3b899cbb6d4de733696175e8f31b0e66
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792017"
---
#   <a name="entity-recognition-cognitive-skill"></a>Competenza cognitiva Riconoscimento delle entità

La competenza **Riconoscimento delle entità** estrae le entità di tipi diversi dal testo. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Servizi cognitivi.

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti aumentano quando si chiamano le API nei servizi cognitivi e per l'estrazione di immagini come parte della fase di cracking del documento in Azure ricerca cognitiva. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione delle immagini sono descritti nella [pagina dei prezzi di Azure ricerca cognitiva](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limiti dei dati
La dimensione massima di un record deve essere di 50.000 caratteri misurata da [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Se è necessario suddividere i dati prima di inviarli all'estrattore di frasi chiave, è possibile usare la competenza [Divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole e sono tutti facoltativi.

| Nome parametro     | Description |
|--------------------|-------------|
| Categorie    | Matrice di categorie che devono essere estratte.  Possibili tipi di categorie: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Se non vengono fornite categorie, vengono restituiti tutti i tipi.|
|defaultLanguageCode |  Codice lingua del testo di input. Sono supportate le lingue seguenti: `de, en, es, fr, it`|
|minimumPrecision | Non utilizzato. Riservato per utilizzi futuri. |
|includeTypelessEntities | Quando è impostato su true, se il testo contiene un'entità conosciuta, ma che non può essere classificata in una delle categorie supportate, verrà restituito come parte del campo di output complesso `"entities"`. 
Si tratta di entità note che non sono classificate come parte delle "categorie" supportate correnti. Ad esempio, "Windows 10" è un'entità nota (un prodotto), ma i "prodotti" non sono inclusi nelle categorie attualmente supportate. Il valore predefinito è `false` |


## <a name="skill-inputs"></a>Input competenze

| Nome input      | Description                   |
|---------------|-------------------------------|
| languageCode  | facoltativo. Il valore predefinito è `"en"`.  |
| text          | Testo da analizzare.          |

## <a name="skill-outputs"></a>Output competenze

> [!NOTE]
> non tutte le categorie di entità sono supportate per tutte le lingue. Solo _en_, _es_ supportano l'estrazione dei tipi `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`.

| Nome output     | Description                   |
|---------------|-------------------------------|
| persons      | Una matrice di stringhe in cui ogni stringa rappresenta il nome di una persona. |
| Località  | Una matrice di stringhe in cui ogni stringa rappresenta il nome una posizione. |
| organizations  | Una matrice di stringhe in cui ogni stringa rappresenta un'organizzazione. |
| quantities  | Una matrice di stringhe in cui ogni stringa rappresenta una quantità. |
| dateTimes  | Una matrice di stringhe in cui ogni stringa rappresenta un valore DateTime (come viene visualizzato nel testo). |
| urls | Una matrice di stringhe in cui ogni stringa rappresenta un URL |
| emails | Una matrice di stringhe in cui ogni stringa rappresenta un indirizzo di posta elettronica |
| namedEntities | Una matrice di tipi complessi, che contiene i campi seguenti: <ul><li>category</li> <li>valore (il nome effettivo dell'entità)</li><li>offset (percorso in cui è stato trovato nel testo)</li><li>confidence (non usato per il momento. Verrà impostato sul valore -1)</li></ul> |
| entities | Una matrice di tipi complessi, che contiene informazioni dettagliate sulle entità estratte dal testo, con i campi seguenti <ul><li> name (nome entità effettivo. Rappresenta una forma "normalizzata")</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (collegamento alla pagina di Wikipedia dell'entità)</li><li>bingId</li><li>type (categoria dell'entità riconosciuta)</li><li>subType (disponibile solo per determinate categorie, in modo da offrire una visualizzazione più granulare del tipo di entità)</li><li> matches (raccolta complessa contenente)<ul><li>testo (testo non elaborato per l'entità)</li><li>offset (posizione in cui è stata trovata)</li><li>length (lunghezza del testo dell'entità non elaborato)</li></ul></li></ul> |

##  <a name="sample-definition"></a>Definizione di esempio

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
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
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
             "languageCode": "en"
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
      "data" : 
      {
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>Casi di errore
Se il codice della lingua per il documento non è supportato, viene restituito un errore e non vengono estratte entità.

## <a name="see-also"></a>Vedi anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un insieme di competenze](cognitive-search-defining-skillset.md)
