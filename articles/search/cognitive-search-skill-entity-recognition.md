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
ms.openlocfilehash: 6ef5952b6413563b2c2e16ff2218f709b414fb84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297821"
---
#    <a name="entity-recognition-cognitive-skill"></a>Competenza cognitiva Riconoscimento delle entità

La competenza **Riconoscimento delle entità** estrae le entità di tipi diversi dal testo. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Servizi cognitivi.

> [!NOTE]
> Quando si espande l'ambito aumentando la frequenza di elaborazione, l'aggiunta di altri documenti o l'aggiunta di altri algoritmi di intelligenza artificiale, sarà necessario [alleghi una risorsa di servizi cognitivi fatturabile](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di cracking dei documenti in Ricerca cognitiva di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione di immagini sono descritti nella [pagina dei prezzi di Ricerca cognitiva di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>Limiti dei dati
La dimensione massima di un record deve essere di 50.000 caratteri misurata [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)da. Se è necessario suddividere i dati prima di inviarli all'estrattore di frasi chiave, è possibile usare la competenza [Divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole e sono tutti facoltativi.

| Nome parametro     | Descrizione |
|--------------------|-------------|
| Categorie    | Matrice di categorie che devono essere estratte.  Possibili tipi di categorie: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. Se non vengono fornite categorie, vengono restituiti tutti i tipi.|
|defaultLanguageCode |    Codice lingua del testo di input. Sono supportate le lingue seguenti: `ar, cs, da, de, en, es, fi, fr, hu, it, ja, ko, nl, no, pl, pt-BR, pt-PT, ru, sv, tr, zh-hans`. Non tutte le categorie di entità sono supportate per tutte le lingue. vedere la nota seguente.|
|minimumPrecision | Immettere un valore compreso tra 0 e 1. Se il Punteggio di confidenza ( `namedEntities` nell'output) è inferiore a questo valore, l'entità non viene restituita. Il valore predefinito è 0. |
|includeTypelessEntities | Impostare su `true` se si desidera riconoscere entità note che non rientrano nelle categorie correnti. Le `entities` entità riconosciute vengono restituite nel campo di output complesso. Ad esempio, "Windows 10" è un'entità nota (un prodotto), ma dal momento che "prodotti" non è una categoria supportata, questa entità verrebbe inclusa nel campo di output entità. Il valore predefinito è `false` |


## <a name="skill-inputs"></a>Input competenze

| Nome input      | Descrizione                   |
|---------------|-------------------------------|
| languageCode    | Facoltativo. Il valore predefinito è `"en"`.  |
| text          | Testo da analizzare.          |

## <a name="skill-outputs"></a>Output competenze

> [!NOTE]
> non tutte le categorie di entità sono supportate per tutte le lingue. I `"Person"`tipi `"Location"`di categoria `"Organization"` di entità, e sono supportati per l'elenco completo delle lingue precedenti. Solo _de_, _en_, _es_, _fr_e _zh-Hans_ supportano l'estrazione dei `"Quantity"`tipi `"Datetime"`, `"URL"`, e `"Email"` . Per ulteriori informazioni, vedere [supporto per lingue e aree per la API analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support).  

| Nome output      | Descrizione                   |
|---------------|-------------------------------|
| persons       | Una matrice di stringhe in cui ogni stringa rappresenta il nome di una persona. |
| locations  | Una matrice di stringhe in cui ogni stringa rappresenta il nome una posizione. |
| organizations  | Una matrice di stringhe in cui ogni stringa rappresenta un'organizzazione. |
| quantities  | Una matrice di stringhe in cui ogni stringa rappresenta una quantità. |
| dateTimes  | Una matrice di stringhe in cui ogni stringa rappresenta un valore DateTime (come viene visualizzato nel testo). |
| urls | Una matrice di stringhe in cui ogni stringa rappresenta un URL |
| emails | Una matrice di stringhe in cui ogni stringa rappresenta un indirizzo di posta elettronica |
| namedEntities | Una matrice di tipi complessi, che contiene i campi seguenti: <ul><li>category</li> <li>valore (il nome effettivo dell'entità)</li><li>offset (percorso in cui è stato trovato nel testo)</li><li>confidenza (valore più elevato significa che si tratta di un'entità reale)</li></ul> |
| entities | Una matrice di tipi complessi, che contiene informazioni dettagliate sulle entità estratte dal testo, con i campi seguenti <ul><li> name (nome entità effettivo. Rappresenta una forma "normalizzata")</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl (collegamento alla pagina di Wikipedia dell'entità)</li><li>bingId</li><li>type (categoria dell'entità riconosciuta)</li><li>subType (disponibile solo per determinate categorie, in modo da offrire una visualizzazione più granulare del tipo di entità)</li><li> matches (raccolta complessa contenente)<ul><li>testo (testo non elaborato per l'entità)</li><li>offset (posizione in cui è stata trovata)</li><li>length (lunghezza del testo dell'entità non elaborato)</li></ul></li></ul> |

##    <a name="sample-definition"></a>Definizione di esempio

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
    "minimumPrecision": 0.5,
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
##    <a name="sample-input"></a>Input di esempio

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

##    <a name="sample-output"></a>Output di esempio

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
            "confidence": 0.98
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

Si noti che gli offset restituiti per le entità nell'output di questa competenza vengono restituiti direttamente dalla [API analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), il che significa che se li si usa per eseguire l'indicizzazione nella stringa originale, è necessario usare la classe [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) in .NET per estrarre il contenuto corretto.  [Altre informazioni sono disponibili qui.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/concepts/text-offsets)

## <a name="error-cases"></a>Casi di errore
Se il codice della lingua per il documento non è supportato, viene restituito un errore e non vengono estratte entità.

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
