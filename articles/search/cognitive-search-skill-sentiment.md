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
ms.custom: seodec2018
ms.openlocfilehash: f52f5200f33d11db44d94b5a5f26d246f711e224
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023811"
---
#   <a name="sentiment-cognitive-skill"></a>Competenza valutazione cognitiva

La competenza **Valutazione** valuta il testo non strutturato in una sequenza di valori positivi-negativi e per ogni record restituisce un valore numerico compreso tra 0 e 1. I punteggi vicini all'1 indicano una valutazione positiva e i punteggi vicini allo 0 indicano una valutazione negativa. Questa competenza usa i modelli di Machine Learning forniti da [Analisi del testo](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) in Servizi cognitivi.

> [!NOTE]
> Come si espande ambito se si aumenta la frequenza di elaborazione, l'aggiunta di più documenti o aggiungendo più algoritmi di intelligenza artificiale, dovrai [collegare una risorsa di servizi cognitivi fatturabile](cognitive-search-attach-cognitive-services.md). Gli addebiti si accumulano quando si chiamano le API in Servizi cognitivi e per l'estrazione di immagini come parte della fase di individuazione di documenti in Ricerca di Azure. Non sono previsti addebiti per l'estrazione di testo dai documenti.
>
> L'esecuzione delle competenze incorporate verrà addebitato alla esistente [servizi cognitivi pay-capacità di passare prezzo](https://azure.microsoft.com/pricing/details/cognitive-services/). Prezzi di estrazione di immagini è descritta nel [pagina dei prezzi di ricerca di Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SentimentSkill

## <a name="data-limits"></a>Limiti dei dati
Le dimensioni massime di un record devono essere 5000 caratteri in base alla misurazione di `String.Length`. Se è necessario suddividere i dati prima di inviarli all'analizzatore di valutazione, usare la [competenza cognitiva di divisione del testo](cognitive-search-skill-textsplit.md).


## <a name="skill-parameters"></a>Parametri della competenza

I parametri fanno distinzione tra maiuscole e minuscole.

| Nome parametro |                      |
|----------------|----------------------|
| defaultLanguageCode | (facoltativo) Il codice lingua da applicare ai documenti che non specificano in modo esplicito una lingua. <br/> Vedere l'[elenco completo delle lingue supportate](../cognitive-services/text-analytics/text-analytics-supported-languages.md) |

## <a name="skill-inputs"></a>Input competenze 

| Nome input | DESCRIZIONE |
|--------------------|-------------|
| text | Testo da analizzare.|
| languageCode  |  (Facoltativo) Stringa che indica la lingua dei record. Se questo parametro non è specificato, il valore predefinito è "en". <br/>Vedere l'[elenco completo delle lingue supportate](../cognitive-services/text-analytics/text-analytics-supported-languages.md).|

## <a name="skill-outputs"></a>Output competenze

| Nome output | DESCRIZIONE |
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
