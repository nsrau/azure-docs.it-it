---
title: Competenza valutazione cognitiva
titleSuffix: Azure Cognitive Search
description: Estrarre un punteggio positivo negativo del sentimento dal testo in una pipeline di arricchimento per l'intelligenza artificiale in Azure ricerca cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 895e22d94122539ee68bcac8ee4debce2d824ff0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935280"
---
# <a name="sentiment-cognitive-skill"></a>Competenza valutazione cognitiva

La competenza **Valutazione** valuta il testo non strutturato in una sequenza di valori positivi-negativi e per ogni record restituisce un valore numerico compreso tra 0 e 1. I punteggi vicini all'1 indicano una valutazione positiva e i punteggi vicini allo 0 indicano una valutazione negativa. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](../cognitive-services/text-analytics/overview.md) in Servizi cognitivi.

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di cracking dei documenti in Ricerca cognitiva di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione di immagini sono descritti nella [pagina dei prezzi di Ricerca cognitiva di Azure](https://azure.microsoft.com/pricing/details/search/).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limiti dei dati
La dimensione massima di un record deve essere di 5000 caratteri misurata da [`String.Length`](/dotnet/api/system.string.length) . Se è necessario suddividere i dati prima di inviarli all'analizzatore di valutazione, usare la [competenza cognitiva di divisione del testo](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro | Descrizione |
|----------------|----------------------|
| `defaultLanguageCode` | (facoltativo) Il codice lingua da applicare ai documenti che non specificano in modo esplicito una lingua. <br/> Vedere l'[elenco completo delle lingue supportate](../cognitive-services/text-analytics/language-support.md) |

## <a name="skill-inputs"></a>Input competenze 

| Nome input | Descrizione |
|--------------------|-------------|
| `text` | Testo da analizzare.|
| `languageCode`    |  (Facoltativo) Stringa che indica la lingua dei record. Se questo parametro non è specificato, il valore predefinito è "en". <br/>Vedere l'[elenco completo delle lingue supportate](../cognitive-services/text-analytics/language-support.md).|

## <a name="skill-outputs"></a>Output competenze

| Nome output | Descrizione |
|--------------------|-------------|
| `score` | Un valore compreso tra 0 e 1 che rappresenta la valutazione del testo analizzato. I valori prossimi allo 0 hanno una valutazione negativa, quelli prossimi allo 0,5 hanno una valutazione neutra, quelli prossimi all'1 hanno una valutazione positiva.|


##  <a name="sample-definition"></a>Definizione di esempio

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/languagecode"
        }
    ],
    "outputs": [
        {
            "name": "score",
            "targetName": "mySentiment"
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
            "data": {
                "text": "I had a terrible time at the hotel. The staff was rude and the food was awful.",
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
            "data": {
                "score": 0.01
            }
        }
    ]
}
```

## <a name="notes"></a>Note
Se non contiene valori, per questi record non viene restituito un punteggio di valutazione.

## <a name="error-cases"></a>Casi di errore
Se una lingua non è supportata, viene generato un errore e non viene restituito alcun punteggio di valutazione.

## <a name="see-also"></a>Vedere anche

+ [Competenze predefinite](cognitive-search-predefined-skills.md)
+ [Come definire un set di competenze](cognitive-search-defining-skillset.md)