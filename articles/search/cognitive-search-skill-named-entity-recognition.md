---
title: Competenza Riconoscimento di entità denominate della ricerca cognitiva (Ricerca di Azure) | Microsoft Docs
description: Estrarre le entità denominate per utente, posizione e organizzazione dal testo in una pipeline di ricerca cognitiva di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: luisca
ms.openlocfilehash: f9ff3f66f3a73fbaf1a4c2ca280c85f4bde65444
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442030"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Competenza cognitiva di Riconoscimento di entità denominate

La competenza **Riconoscimento di entità denominate** estrae le entità denominate dal testo. Le entità disponibili includono i tipi `person`, `location` e `organization`.

> [!NOTE]
> <ul>
> <li>La ricerca cognitiva è disponibile in anteprima pubblica. Le funzionalità di esecuzione di set di competenze e di estrazione e normalizzazione di immagini sono attualmente disponibili gratuitamente. Il prezzo per queste funzionalità verrà annunciato in un momento successivo. </li>
> <li> La competenza Riconoscimento di entità denominate è considerata "deprecata" e non sarà più ufficialmente supportata a partire dal 15 febbraio 2019. Seguire le raccomandazioni elencate in <a href="cognitive-search-skill-deprecated.md">Competenze di ricerca cognitiva deprecate</a> per eseguire la migrazione a una competenza supportata</li>

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Limiti dei dati
Le dimensioni massime di un record devono essere di 50.000 caratteri in base alla misurazione di `String.Length`. Se è necessario suddividere i dati prima di inviarli all'estrattore di frasi chiave, è possibile usare la competenza [Divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| Categorie    | Matrice di categorie che devono essere estratte.  Possibili tipologie di categorie: `"Person"`, `"Location"`, `"Organization"`. Se non vengono fornite categorie, vengono restituiti tutti i tipi.|
|defaultLanguageCode |  Codice lingua del testo di input. Sono supportate le lingue seguenti: `de, en, es, fr, it`|
| minimumPrecision  | Un numero compreso tra 0 e 1. Se la precisione è inferiore a questo valore, non viene restituita l'entità. Il valore predefinito è 0.|

## <a name="skill-inputs"></a>Input competenze

| Nome input      | DESCRIZIONE                   |
|---------------|-------------------------------|
| languageCode  | facoltativo. Il valore predefinito è `"en"`.  |
| text          | Testo da analizzare.          |

## <a name="skill-outputs"></a>Output competenze

| Nome output     | DESCRIZIONE                   |
|---------------|-------------------------------|
| persons      | Una matrice di stringhe in cui ogni stringa rappresenta il nome di una persona. |
| locations  | Una matrice di stringhe in cui ogni stringa rappresenta il nome una posizione. |
| organizations  | Una matrice di stringhe in cui ogni stringa rappresenta un'organizzazione. |
| entities | Una matrice di tipi complessi. Ogni tipo complesso include i campi seguenti: <ul><li>category (`"person"`, `"organization"` o `"location"`)</li> <li>value (nome entità effettivo)</li><li>offset (percorso in cui è stato trovato nel testo)</li><li>confidence (un valore compreso tra 0 e 1 che rappresenta l'attendibilità che il valore sia di un'entità effettiva)</li></ul> |

##  <a name="sample-definition"></a>Definizione di esempio

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
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
             "text": "This is the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
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
+ [Competenza di riconoscimento entità](cognitive-search-skill-entity-recognition.md)
