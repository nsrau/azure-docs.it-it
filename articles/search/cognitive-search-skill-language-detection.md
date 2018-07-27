---
title: Competenza rilevamento lingua della ricerca cognitiva (Ricerca di Azure) | Microsoft Docs
description: Valuta testo non strutturato e per ogni record restituisce un identificatore di lingua con un punteggio che indica il livello di attendibilità dell'analisi in una pipeline di arricchimento di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 443ac895085053b7c4c876c3deecaa1943c9f506
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171059"
---
#   <a name="language-detection-cognitive-skill"></a>Competenza cognitiva di rilevamento lingua

La competenza **rilevamento lingua**, per un massimo di 120 lingue, rileva la lingua del testo di input e restituisce un codice lingua singolo per ogni documento inviato nella richiesta. Il codice lingua è associato a un punteggio che indica il livello di attendibilità dell'analisi.

Questa funzionalità è particolarmente utile quando è necessario specificare la lingua del testo come input per altre competenze (ad esempio, la competenza [Analisi del sentiment](cognitive-search-skill-sentiment.md) o la competenza [Divisione del testo](cognitive-search-skill-textsplit.md)).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>Limiti dei dati
Le dimensioni massime di un record devono essere di 50.000 caratteri in base alla misurazione di `String.Length`. Se è necessario suddividere i dati prima di inviarli all'analizzatore di valutazione, è possibile usare la [competenza di divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="skill-inputs"></a>Input competenze

I parametri fanno distinzione tra maiuscole e minuscole.

| Input     | DESCRIZIONE |
|--------------------|-------------|
| text | Testo da analizzare.|

## <a name="skill-outputs"></a>Output competenze

| Nome output    | DESCRIZIONE |
|--------------------|-------------|
| languageCode | Il codice di lingua ISO 6391 per la lingua identificata. Ad esempio, "en". |
| languageName | Il nome della lingua. Ad esempio, "Inglese". |
| score | Immettere un valore compreso tra 0 e 1. La probabilità che lingua sia identificata correttamente. Il punteggio può essere inferiore a 1 se la frase contiene lingue miste.  |

##  <a name="sample-definition"></a>Definizione di esempio

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill ",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>Output di esempio

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>Casi di errore
Se il testo è espresso in una lingua non supportata, viene generato un errore e non viene restituito alcun identificatore di lingua.

## <a name="see-also"></a>Vedere anche 

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
