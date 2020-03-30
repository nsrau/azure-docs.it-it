---
title: Abilità cognitive di traduzione del testo
titleSuffix: Azure Cognitive Search
description: Valuta il testo e, per ogni record, restituisce il testo tradotto nella lingua di destinazione specificata in una pipeline di arricchimento dell'iaformazione aio in Ricerca cognitiva di Azure.Evaluates text and, for each record, returns text translated to the specified target language in an AI enrichment pipeline in Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5089174fcfd5a97128c1f789b818243243a5282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460765"
---
#   <a name="text-translation-cognitive-skill"></a>Abilità cognitive di traduzione del testo

La **competenza Traduzione** di testo valuta il testo e, per ogni record, restituisce il testo tradotto nella lingua di destinazione specificata. Questa competenza usa [l'API Translator Text v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) disponibile in Servizi cognitivi.

Questa funzionalità è utile se si prevede che i documenti non siano tutti in una lingua, nel qual caso è possibile normalizzare il testo in una singola lingua prima di indicizzare la ricerca traducendolo.  È utile anche per i casi d'uso di localizzazione, in cui è possibile disporre di copie dello stesso testo disponibili in più lingue.

[L'API Traduzione testo v3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) è un servizio cognitivo non regionale, ovvero non è garantito che i dati rimangano nella stessa area della risorsa Ricerca cognitiva di Azure o dei servizi cognitivi collegati.

> [!NOTE]
> Man mano che si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti o aggiungendo più algoritmi di ia', sarà necessario [collegare una risorsa servizi cognitivi fatturabile.](cognitive-search-attach-cognitive-services.md) Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di cracking dei documenti in Ricerca cognitiva di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione di immagini sono descritti nella [pagina dei prezzi di Ricerca cognitiva di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>Limiti dei dati
La dimensione massima di un record deve essere di [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)50.000 caratteri misurata da . Se è necessario suddividere i dati prima di inviarli alla competenza di traduzione del testo, è consigliabile utilizzare la [competenza Suddivisione testo](cognitive-search-skill-textsplit.md).

## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Input                | Descrizione |
|---------------------|-------------|
| defaultToLanguageCode (codice defaultToLanguageCode) | (Obbligatorio) Codice lingua in cui tradurre i documenti per i documenti che non specificano l'oggetto in lingua in modo esplicito. <br/> Vedere [Elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| defaultFromLanguageCode (codice defaultFromLanguageCode) | (Facoltativo) Codice lingua da cui tradurre i documenti per i documenti che non specificano l'oggetto da in modo esplicito.  Se il defaultFromLanguageCode non è specificato, il rilevamento automatico della lingua fornito dall'API Translator Text verrà utilizzato per determinare la lingua from. <br/> Vedere [Elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| suggestedFrom | (Facoltativo) Il codice della lingua per tradurre i documenti da quando non viene fornito né l'input fromLanguageCode né il parametro defaultFromLanguageCode e il rilevamento automatico della lingua non riesce.  Se la lingua suggestedFrom non è specificata, l'inglese (en) verrà utilizzato come lingua suggestedFrom. <br/> Vedere [Elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Input competenze

| Nome input     | Descrizione |
|--------------------|-------------|
| text | Testo da tradurre.|
| Codice lingua    | Una stringa che indica la lingua in cui deve essere tradotto il testo. Se questo input non è specificato, defaultToLanguageCode verrà utilizzato per tradurre il testo. <br/>Vedere l'[elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|
| FromLanguageCode (Codice lingua)  | Stringa che indica la lingua corrente del testo. Se questo parametro non viene specificato, il defaultFromLanguageCode (o il rilevamento automatico della lingua se non viene fornito il defaultFromLanguageCode) verrà utilizzato per tradurre il testo. <br/>Vedere l'[elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)|

## <a name="skill-outputs"></a>Output competenze

| Nome output    | Descrizione |
|--------------------|-------------|
| translatedText | Risultato della stringa della traduzione di testo da translatedFromLanguageCode a translatedToLanguageCode.|
| translatedToLanguageCode (codice translatedToLanguageCode)  | Una stringa che indica il codice lingua in cui è stato tradotto il testo. Utile se si sta traducendo in più lingue e si desidera essere in grado di tenere traccia di quale testo è quale lingua.|
| translatedFromLanguageCode (codice translatedFromLanguageCode)    | Una stringa che indica il codice della lingua da cui è stato tradotto il testo. Utile se hai optato per l'opzione di rilevamento automatico della lingua in quanto questo output ti darà il risultato di tale rilevamento.|

##  <a name="sample-definition"></a>Definizione di esempio

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
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
}
```


##  <a name="sample-output"></a>Output di esempio

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Errori e avvisi
Se si specifica un codice lingua non supportato per la lingua da o in, viene generato un errore e il testo non viene tradotto.
Se il testo è vuoto, verrà generato un avviso.
Se il testo è più grande di 50.000 caratteri, verranno tradotti solo i primi 50.000 caratteri e verrà generato un avviso.

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)
