---
title: Competenza Riconoscimento delle entità della ricerca cognitiva - Ricerca di Azure
description: Estrarre tipi diversi di entità dal testo in una pipeline di ricerca cognitiva di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7a7fe9603716575c241ca78ebdc9b674888ca835
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452208"
---
#    <a name="entity-recognition-cognitive-skill"></a>Competenza cognitiva Riconoscimento delle entità

La competenza **Riconoscimento delle entità** estrae le entità di tipi diversi dal testo. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Servizi cognitivi.

> [!NOTE]
> Dal 21 dicembre 2018 è possibile [collegare una risorsa di Servizi cognitivi](cognitive-search-attach-cognitive-services.md) a un set di competenze di Ricerca di Azure. Ciò consente di iniziare l'addebito per l'esecuzione del set di competenze. In questa data è iniziato anche l'addebito dell'estrazione delle immagini come parte della fase di individuazione dei documenti. L'estrazione di testo dai documenti continua a essere offerta gratuitamente.
>
> L'esecuzione delle [competenze cognitive predefinite](cognitive-search-predefined-skills.md) viene addebitata in base ai [prezzi con pagamento in base al consumo di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services), alla stessa tariffa che verrebbe usata se l'attività venisse eseguita direttamente. L'estrazione di immagini è un addebito di Ricerca di Azure, attualmente offerto al prezzo di anteprima. Per informazioni dettagliate, vedere la [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400) oppure [Come funziona la fatturazione](search-sku-tier.md#how-billing-works).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limiti dei dati
Le dimensioni massime di un record devono essere di 50.000 caratteri in base alla misurazione di `String.Length`. Se è necessario suddividere i dati prima di inviarli all'estrattore di frasi chiave, è possibile usare la competenza [Divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole e sono tutti facoltativi.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| Categorie    | Matrice di categorie che devono essere estratte.  Possibili tipi di categorie: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Se non vengono fornite categorie, vengono restituiti tutti i tipi.|
|defaultLanguageCode |  Codice lingua del testo di input. Sono supportate le lingue seguenti: `de, en, es, fr, it`|
|minimumPrecision | Non utilizzato. Riservato per utilizzi futuri. |
|includeTypelessEntities | Quando è impostato su true, se il testo contiene un'entità conosciuta, ma che non può essere classificata in una delle categorie supportate, verrà restituito come parte del campo di output complesso `"entities"`. Il valore predefinito è `false` |


## <a name="skill-inputs"></a>Input competenze

| Nome input      | DESCRIZIONE                   |
|---------------|-------------------------------|
| languageCode  | facoltativo. Il valore predefinito è `"en"`.  |
| text          | Testo da analizzare.          |

## <a name="skill-outputs"></a>Output competenze

**NOTA**: non tutte le categorie di entità sono supportate per tutte le lingue.
Solo _en_, _es_ supportano l'estrazione dei tipi `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`.

| Nome output     | DESCRIZIONE                   |
|---------------|-------------------------------|
| persons      | Una matrice di stringhe in cui ogni stringa rappresenta il nome di una persona. |
| locations  | Una matrice di stringhe in cui ogni stringa rappresenta il nome una posizione. |
| organizations  | Una matrice di stringhe in cui ogni stringa rappresenta un'organizzazione. |
| quantities  | Una matrice di stringhe in cui ogni stringa rappresenta una quantità. |
| dateTimes  | Una matrice di stringhe in cui ogni stringa rappresenta un valore DateTime (come viene visualizzato nel testo). |
| urls | Una matrice di stringhe in cui ogni stringa rappresenta un URL |
| emails | Una matrice di stringhe in cui ogni stringa rappresenta un indirizzo di posta elettronica |
| namedEntities | Una matrice di tipi complessi, che contiene i campi seguenti: <ul><li>category</li> <li>value (nome entità effettivo)</li><li>offset (percorso in cui è stato trovato nel testo)</li><li>confidence (non usato per il momento. Verrà impostato sul valore -1)</li></ul> |
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

## <a name="see-also"></a>Vedere anche 

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)