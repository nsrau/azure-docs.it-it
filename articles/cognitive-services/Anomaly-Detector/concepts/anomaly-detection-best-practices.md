---
title: Procedure consigliate per l'API Rilevamento anomalie
titleSuffix: Azure Cognitive Services
description: Informazioni sulle procedure consigliate per il rilevamento di anomalie con l'API del rilevatore di anomalie.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: mbullwin
ms.openlocfilehash: 6bf00d8b5327ddd539190b6e990c7edb35c876f7
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018390"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Procedure consigliate per l'uso dell'API del rilevamento anomalie

L'API rilevatore di anomalie è un servizio di rilevamento anomalie senza stato. L'accuratezza e le prestazioni dei risultati possono essere influenzate da:

* Modalità di preparazione dei dati delle serie temporali.
* Parametri dell'API del rilevatore di anomalie utilizzati.
* Il numero di punti dati nella richiesta dell'API. 

Usare questo articolo per informazioni sulle procedure consigliate per l'uso dell'API per ottenere risultati ottimali per i dati. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Quando usare il rilevamento di anomalie di batch (intero) o più recente (ultimo) punto

L'endpoint di rilevamento batch dell'API del rilevatore di anomalie consente di rilevare le anomalie nei dati delle serie temporali. In questa modalità di rilevamento viene creato e applicato un singolo modello statistico a ogni punto del set di dati. Se la serie temporale presenta le caratteristiche seguenti, è consigliabile usare il rilevamento batch per visualizzare in anteprima i dati in una chiamata API.

* Una serie temporale stagionale, con anomalie occasionali.
* Serie temporale di tendenza piatta, con picchi/DIP occasionali. 

Non è consigliabile usare il rilevamento delle anomalie in batch per il monitoraggio dei dati in tempo reale o l'uso di dati di serie temporali che non hanno caratteristiche precedenti. 

* Il rilevamento batch crea e applica un solo modello, il rilevamento per ogni punto viene eseguito nel contesto di una serie intera. Se le tendenze dei dati delle serie temporali aumentano e diminuiscono senza stagionalità, alcuni punti di modifica (DIP e picchi nei dati) potrebbero non essere presenti nel modello. Analogamente, è possibile che alcuni punti di modifica meno significativi di quelli successivi nel set di dati non vengano conteggiati come sufficientemente significativi da incorporare nel modello.

* Il rilevamento batch è più lento rispetto al rilevamento dello stato anomalie del punto più recente durante il monitoraggio dei dati in tempo reale, a causa del numero di punti analizzati.

Per il monitoraggio dei dati in tempo reale, è consigliabile rilevare lo stato anomalie solo del punto dati più recente. Con l'applicazione continua del rilevamento dei punti più recenti, il monitoraggio dei dati di streaming può essere eseguito in modo più efficiente e accurato.

Nell'esempio seguente viene descritto l'effetto che queste modalità di rilevamento possono avere sulle prestazioni. La prima figura mostra il risultato del rilevamento continuo del punto più recente dello stato anomalie lungo 28 punti dati precedentemente visualizzati. I punti rossi sono le anomalie.

![Immagine che mostra il rilevamento delle anomalie usando il punto più recente](../media/last.png)

Di seguito è riportato lo stesso set di dati con rilevamento anomalie batch. Il modello creato per l'operazione ha ignorato diverse anomalie, contrassegnate da rettangoli.

![Immagine che mostra il rilevamento delle anomalie tramite il metodo batch](../media/entire.png)

## <a name="data-preparation"></a>Preparazione dei dati

L'API rilevatore di anomalie accetta dati di serie temporali formattati in un oggetto richiesta JSON. Una serie temporale può essere costituita da qualsiasi dato numerico registrato nel tempo in ordine sequenziale. È possibile inviare Windows dei dati della serie temporale all'endpoint API del rilevatore di anomalie per migliorare le prestazioni dell'API. Il numero minimo di punti dati che è possibile inviare è 12 e il valore massimo è 8640 punti. La [granularità](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) è definita come la frequenza con cui vengono campionati i dati. 

I punti dati inviati all'API del rilevatore di anomalie devono avere un timestamp UTC (Coordinated Universal Time) valido e un valore numerico. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

Se i dati vengono campionati in un intervallo di tempo non standard, è possibile specificarli aggiungendo l' `customInterval` attributo nella richiesta. Se, ad esempio, la serie viene campionata ogni 5 minuti, è possibile aggiungere quanto segue alla richiesta JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Punti dati mancanti

I punti dati mancanti sono comuni nei set di dati di serie temporali distribuiti in modo uniforme, soprattutto quelli con una granularità fine (un piccolo intervallo di campionamento. Ad esempio, dati campionati ogni pochi minuti). Manca meno del 10% del numero previsto di punti nei dati non dovrebbe avere un impatto negativo sui risultati del rilevamento. Si consiglia di riempire i gap nei dati in base alle caratteristiche, ad esempio sostituendo i punti dati da un periodo precedente, un'interpolazione lineare o una media mobili.

### <a name="aggregate-distributed-data"></a>Aggregare dati distribuiti

L'API rilevatore di anomalie funziona meglio in una serie temporale distribuita in modo uniforme. Se i dati vengono distribuiti in modo casuale, è consigliabile aggregarli in base a un'unità di tempo, ad esempio al minuto, ogni ora o ogni giorno.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Rilevamento di anomalie sui dati con modelli stagionali

Se si è certi che i dati delle serie temporali hanno un modello stagionale (uno che si verifica a intervalli regolari), è possibile migliorare l'accuratezza e il tempo di risposta dell'API. 

Se si specifica un oggetto `period` quando si costruisce la richiesta JSON, è possibile ridurre la latenza di rilevamento anomalie fino al 50%. `period`È un numero intero che specifica approssimativamente il numero di punti dati che la serie temporale impiega per ripetere un modello. Ad esempio, una serie temporale con un punto dati al giorno avrà `period` come `7` e una serie temporale con un punto all'ora (con lo stesso modello settimanale) avrebbe un `period` di  `7*24` . Se non si è certi dei modelli di dati, non è necessario specificare questo parametro.

Per ottenere risultati ottimali, fornire `period` un punto dati di 4, più uno aggiuntivo. Ad esempio, i dati orari con un modello settimanale, come descritto in precedenza, devono fornire 673 punti dati nel corpo della richiesta ( `7 * 24 * 4 + 1` ).

### <a name="sampling-data-for-real-time-monitoring"></a>Campionamento dei dati per il monitoraggio in tempo reale

Se i dati di streaming vengono campionati a breve (ad esempio secondi o minuti), l'invio del numero consigliato di punti dati può superare il numero massimo di API del rilevamento anomalie consentito (punti dati 8640). Se i dati mostrano un modello stagionale stabile, provare a inviare un campione dei dati della serie temporale a un intervallo di tempo maggiore, ad esempio le ore. Il campionamento dei dati in questo modo può anche migliorare notevolmente il tempo di risposta dell'API. 

## <a name="next-steps"></a>Passaggi successivi

* [Cos'è l'API Rilevamento anomalie?](../overview.md)
* [Avvio rapido: Rilevare le anomalie nei dati delle serie temporali tramite l'API REST Rilevamento anomalie](../quickstarts/detect-data-anomalies-csharp.md)
