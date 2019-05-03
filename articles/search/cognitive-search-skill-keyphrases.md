---
title: Competenza Estrazione frasi chiave della ricerca cognitiva - Ricerca di Azure
description: Valuta il testo non strutturato e restituisce un elenco di frasi chiave per ciascun record in una pipeline di arricchimento di Ricerca di Azure.
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
ms.openlocfilehash: 1d221e3bcdfd781da79c73e8f228b9e449a7f5bd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021824"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>Competenza Estrazione frasi chiave della ricerca cognitiva

La competenza **Estrazione frasi chiave** valuta il testo non strutturato e restituisce un elenco di frasi chiave per ciascun record. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Servizi cognitivi.

Questa funzionalità è utile se occorre identificare rapidamente i punti di discussione principali nel record. Ad esempio, dato il testo di input "Il cibo era delizioso e il personale era meraviglioso", il servizio restituisce "cibo" e "personale meraviglioso".

> [!NOTE]
> Come si espande ambito se si aumenta la frequenza di elaborazione, l'aggiunta di più documenti o aggiungendo più algoritmi di intelligenza artificiale, dovrai [collegare una risorsa di servizi cognitivi fatturabile](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di individuazione di documenti in Ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze incorporate verrà addebitato alla esistente [servizi cognitivi pay-capacità di passare prezzo](https://azure.microsoft.com/pricing/details/cognitive-services/). Prezzi di estrazione di immagini è descritta nel [pagina dei prezzi di ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>Limiti dei dati
Le dimensioni massime di un record devono essere di 50.000 caratteri in base alla misurazione di `String.Length`. Se è necessario suddividere i dati prima di inviarli all'estrattore di frasi chiave, è possibile usare la competenza [Divisione del testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Input                | DESCRIZIONE |
|---------------------|-------------|
| defaultLanguageCode | (Facoltativo) Il codice lingua da applicare ai documenti che non specificano in modo esplicito una lingua.  Se il codice lingua predefinito non è specificato, Inglese (en) verrà usato come il codice lingua predefinito. <br/> Vedere l'[elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages). |
| maxKeyPhraseCount   | (Facoltativo) Il numero massimo di frasi chiave da produrre. |

## <a name="skill-inputs"></a>Input competenze

| Input     | DESCRIZIONE |
|--------------------|-------------|
| text | Testo da analizzare.|
| languageCode  |  Stringa che indica la lingua dei record. Se questo parametro viene omesso, il codice lingua predefinito verrà usato per analizzare i record. <br/>Vedere l'[elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)|

##  <a name="sample-definition"></a>Definizione di esempio

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "languageCode",
        "source": "/document/languagecode" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
             "language": "en"
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
            "keyPhrases": 
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
           }
      }
    ]
}
```


## <a name="errors-and-warnings"></a>Errori e avvisi
Se si fornisce un codice lingua non supportato, viene generato un errore e le frasi chiave non vengono estratte.
Se il testo è vuoto, verrà generato un avviso.
Se il testo contiene più di 50.000 caratteri, verranno analizzati solo i primi 50.000 caratteri e verrà generato un avviso.

## <a name="see-also"></a>Vedere anche 

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
