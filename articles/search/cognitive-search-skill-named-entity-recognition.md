---
title: Competenza Riconoscimento delle entità denominate della ricerca cognitiva - Ricerca di Azure
description: Estrarre le entità denominate per utente, posizione e organizzazione dal testo in una pipeline di ricerca cognitiva di Ricerca di Azure.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: f91d0b17cfc984ccf56ad4a25ef2ece651dcbed5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183335"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Competenza cognitiva di Riconoscimento di entità denominate

La competenza **Riconoscimento di entità denominate** estrae le entità denominate dal testo. Le entità disponibili includono i tipi `person`, `location` e `organization`.

> [!IMPORTANT]
> La funzionalità di riconoscimento entità denominata ora non è più disponibile, sostituito da [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Il supporto è stato interrotto il 15 febbraio 2019 e l'API è stata rimossa dal prodotto il 2 maggio 2019. Per eseguire la migrazione a una competenza supportata, seguire le indicazioni riportate nelle [competenze di ricerca cognitive deprecate](cognitive-search-skill-deprecated.md) .

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di individuazione di documenti in Ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione delle immagini sono descritti nella [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>Limiti dei dati
La dimensione massima di un record deve essere di 50.000 caratteri misurata [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)da. Se è necessario suddividere i dati prima di inviarli all'estrattore di frasi chiave, è possibile usare la competenza [Divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro     | DESCRIZIONE |
|--------------------|-------------|
| categories    | Matrice di categorie che devono essere estratte.  Possibili tipologie di categorie: `"Person"`, `"Location"`, `"Organization"`. Se non vengono fornite categorie, vengono restituiti tutti i tipi.|
|defaultLanguageCode |  Codice lingua del testo di input. Sono supportate le lingue seguenti: `de, en, es, fr, it`|
| minimumPrecision  | Un numero compreso tra 0 e 1. Se la precisione è inferiore a questo valore, non viene restituita l'entità. Il valore predefinito è 0.|

## <a name="skill-inputs"></a>Input competenze

| Nome di input      | Descrizione                   |
|---------------|-------------------------------|
| languageCode  | facoltativo. Il valore predefinito è `"en"`.  |
| text          | Testo da analizzare.          |

## <a name="skill-outputs"></a>Output competenze

| Nome output     | Descrizione                   |
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
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Esempio di output

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
