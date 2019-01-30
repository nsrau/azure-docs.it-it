---
title: Competenza Riconoscimento delle entità denominate della ricerca cognitiva - Ricerca di Azure
description: Estrarre le entità denominate per utente, posizione e organizzazione dal testo in una pipeline di ricerca cognitiva di Ricerca di Azure.
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
ms.openlocfilehash: 0480b3d3037a6b0a0dcd4d35965020f794ad8273
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412499"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>Competenza cognitiva di Riconoscimento di entità denominate

La competenza **Riconoscimento di entità denominate** estrae le entità denominate dal testo. Le entità disponibili includono i tipi `person`, `location` e `organization`.

> [!IMPORTANT]
> La competenza Riconoscimento di entità denominate è deprecata ed è stata sostituita da [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Il supporto terminerà il 15 febbraio 2019. Seguire le raccomandazioni in [Competenze di ricerca cognitiva deprecate](cognitive-search-skill-deprecated.md) per eseguire la migrazione a una competenza supportata.

> [!NOTE]
> Dal 21 dicembre 2018 è possibile [collegare una risorsa di Servizi cognitivi](cognitive-search-attach-cognitive-services.md) a un set di competenze di Ricerca di Azure. Ciò consente di iniziare l'addebito per l'esecuzione del set di competenze. In questa data è iniziato anche l'addebito dell'estrazione delle immagini come parte della fase di individuazione dei documenti. L'estrazione di testo dai documenti continua a essere offerta gratuitamente.
>
> L'esecuzione delle [competenze cognitive predefinite](cognitive-search-predefined-skills.md) viene addebitata in base ai [prezzi con pagamento in base al consumo di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services), alla stessa tariffa che verrebbe usata se l'attività venisse eseguita direttamente. L'estrazione di immagini è un addebito di Ricerca di Azure, attualmente offerto al prezzo di anteprima. Per informazioni dettagliate, vedere la [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400) oppure [Come funziona la fatturazione](search-sku-tier.md#how-billing-works).

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
