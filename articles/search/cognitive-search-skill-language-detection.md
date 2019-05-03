---
title: Competenza rilevamento lingua della ricerca cognitiva - Ricerca di Azure
description: Valuta testo non strutturato e per ogni record restituisce un identificatore di lingua con un punteggio che indica il livello di attendibilità dell'analisi in una pipeline di arricchimento di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: a076eee9818f294a8e5c4b10cebbcb9e5a55d80c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021855"
---
#   <a name="language-detection-cognitive-skill"></a>Competenza cognitiva di rilevamento lingua

Il **rilevamento lingua** competenza rileva la lingua del testo di input e segnala il codice una sola lingua per ogni documento inviato nella richiesta. Il codice lingua è associato a un punteggio che indica il livello di attendibilità dell'analisi. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Servizi cognitivi.

Questa funzionalità è particolarmente utile quando è necessario specificare la lingua del testo come input per altre competenze (ad esempio, la competenza [Analisi del sentiment](cognitive-search-skill-sentiment.md) o la competenza [Divisione del testo](cognitive-search-skill-textsplit.md)).

Rilevamento della lingua si basa su librerie di elaborazione in linguaggio naturale di Bing, che supera il numero del [lingue e aree geografiche supportate](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) elencati per testo Analitica. L'elenco esatto delle lingue non è pubblicato, ma include tutte le lingue, diffuse in più varianti, dialetti e alcune lingue internazionali e relative alla lingua. Se si dispone di contenuto espresso in un linguaggio usato meno frequentemente, è possibile [provare l'API di rilevamento lingua](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) per vedere se viene restituito un codice. La risposta per le lingue che non può essere rilevato è `unknown`.

> [!NOTE]
> Come si espande ambito se si aumenta la frequenza di elaborazione, l'aggiunta di più documenti o aggiungendo più algoritmi di intelligenza artificiale, dovrai [collegare una risorsa di servizi cognitivi fatturabile](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di individuazione di documenti in Ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze incorporate verrà addebitato alla esistente [servizi cognitivi pay-capacità di passare prezzo](https://azure.microsoft.com/pricing/details/cognitive-services/). Prezzi di estrazione di immagini è descritta nel [pagina dei prezzi di ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


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
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
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
