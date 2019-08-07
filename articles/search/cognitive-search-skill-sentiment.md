---
title: Competenza valutazione della ricerca cognitiva - Ricerca di Azure
description: Estrarre un punteggio di valutazione positiva/negativa dal testo in una pipeline di arricchimento di Ricerca di Azure.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: d4ac762f94f05d165eb157f4cce183055f5fe099
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840974"
---
#   <a name="sentiment-cognitive-skill"></a>Competenza valutazione cognitiva

La competenza **Valutazione** valuta il testo non strutturato in una sequenza di valori positivi-negativi e per ogni record restituisce un valore numerico compreso tra 0 e 1. I punteggi vicini all'1 indicano una valutazione positiva e i punteggi vicini allo 0 indicano una valutazione negativa. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Servizi cognitivi.

> [!NOTE]
> Se si espande l'ambito aumentando la frequenza di elaborazione, aggiungendo più documenti oppure aggiungendo altri algoritmi di intelligenza artificiale, sarà necessario [collegare una risorsa fatturabile di Servizi cognitivi](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di individuazione di documenti in Ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze predefinite viene addebitata secondo gli attuali [prezzi con pagamento in base al consumo dei Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/). I prezzi per l'estrazione delle immagini sono descritti nella [pagina dei prezzi di Ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limiti dei dati
La dimensione massima di un record deve essere di 5000 caratteri misurata [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)da. Se è necessario suddividere i dati prima di inviarli all'analizzatore di valutazione, usare la [competenza cognitiva di divisione del testo](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro |                      |
|----------------|----------------------|
| defaultLanguageCode | (facoltativo) Il codice lingua da applicare ai documenti che non specificano in modo esplicito una lingua. <br/> Vedere l'[elenco completo delle lingue supportate](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Input competenze 

| Nome input | Descrizione |
|--------------------|-------------|
| text | Testo da analizzare.|
| languageCode  |  (Facoltativo) Stringa che indica la lingua dei record. Se questo parametro non è specificato, il valore predefinito è "en". <br/>Vedere l'[elenco completo delle lingue supportate](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Output competenze

| Nome output | Descrizione |
|--------------------|-------------|
| score | Un valore compreso tra 0 e 1 che rappresenta la valutazione del testo analizzato. I valori prossimi allo 0 hanno una valutazione negativa, quelli prossimi allo 0,5 hanno una valutazione neutra, quelli prossimi all'1 hanno una valutazione positiva.|


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


##  <a name="sample-output"></a>Esempio di output

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
