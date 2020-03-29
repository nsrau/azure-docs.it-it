---
title: Procedure consigliate per l'API Rilevamento anomalie
titleSuffix: Azure Cognitive Services
description: Informazioni sulle procedure consigliate per il rilevamento di anomalie con l'API Rilevatore anomalie.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 9407f2fc9375765efb6eb9688b3ebfeef24ba90a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67721629"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Procedure consigliate per l'utilizzo dell'API Rilevatore di anomalie

L'API Rilevatore di anomalie è un servizio di rilevamento anomalie senza stato. L'accuratezza e le prestazioni dei suoi risultati possono essere influenzate da:

* Come vengono preparati i dati della serie temporale.
* Parametri API rilevatore di anomalie utilizzati.
* Il numero di punti dati nella richiesta API. 

Usare questo articolo per informazioni sulle procedure consigliate per l'uso dell'API per ottenere i risultati migliori per i dati. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Quando utilizzare il rilevamento delle anomalie in batch (intero) o più recente (ultimo) punto

L'endpoint di rilevamento batch dell'API Rilevatore anomalie consente di rilevare anomalie durante l'intero numero di dati della serie di tempi. In questa modalità di rilevamento, un singolo modello statistico viene creato e applicato a ogni punto del set di dati. Se la serie temporale presenta le caratteristiche seguenti, è consigliabile usare il rilevamento batch per visualizzare in anteprima i dati in una chiamata API.

* Una serie temporale stagionale, con anomalie occasionali.
* Una serie temporale di tendenza piatta, con picchi/tuffi occasionali. 

Non è consigliabile usare il rilevamento delle anomalie in batch per il monitoraggio dei dati in tempo reale o utilizzarlo in dati di serie temporali che non hanno caratteristiche precedenti. 

* Il rilevamento batch crea e applica un solo modello, il rilevamento per ogni punto viene eseguito nel contesto di un'intera serie. Se i dati della serie temporale si esibiscono e si abbassano senza stagionalità, alcuni punti di modifica (tuffi e picchi nei dati) potrebbero essere persi dal modello. Analogamente, alcuni punti di modifica che sono meno significativi di quelli successivi nel set di dati potrebbero non essere considerati sufficientemente significativi da essere incorporati nel modello.

* Il rilevamento batch è più lento rispetto al rilevamento dello stato di anomalia del punto più recente quando si esegue il monitoraggio dei dati in tempo reale, a causa del numero di punti analizzati.

Per il monitoraggio dei dati in tempo reale, è consigliabile rilevare solo lo stato di anomalia del punto dati più recente. Applicando continuamente il rilevamento dei punti più recente, il monitoraggio dei dati in streaming può essere eseguito in modo più efficiente e preciso.

L'esempio seguente descrive l'impatto che queste modalità di rilevamento possono avere sulle prestazioni. La prima immagine mostra il risultato del rilevamento continuo dello stato di anomalia ultimo punto lungo 28 punti dati precedentemente visti. I punti rossi sono anomalie.

![Immagine che mostra il rilevamento di anomalie utilizzando il punto più recente](../media/last.png)

Di seguito è riportato lo stesso set di dati utilizzando il rilevamento delle anomalie batch. Il modello costruito per l'operazione ha ignorato diverse anomalie, contrassegnate da rettangoli.

![Immagine che mostra il rilevamento delle anomalie tramite il metodo batch](../media/entire.png)

## <a name="data-preparation"></a>Preparazione dei dati

L'API Rilevatore di anomalie accetta i dati della serie temporale formattati in un oggetto richiesta JSON. Una serie temporale può essere qualsiasi dato numerico registrato nel tempo in ordine sequenziale. È possibile inviare finestre dei dati della serie temporale all'endpoint dell'API Anomaly Detector per migliorare le prestazioni dell'API. Il numero minimo di punti dati che è possibile inviare è 12 e il numero massimo è 8640 punti. [La granularità](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) è definita come la frequenza con cui vengono campionati i dati. 

I punti dati inviati all'API Rilevatore di anomalie devono avere un timestamp UTC (Coordinated Universal Time) valido e un valore numerico. 

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

Se i dati vengono campionati a un intervallo di tempo `customInterval` non standard, è possibile specificarli aggiungendo l'attributo nella richiesta. Ad esempio, se la serie viene campionata ogni 5 minuti, è possibile aggiungere quanto segue alla richiesta JSON:For example, if your series is sampled every 5 minutes, you can add the following to your JSON request:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Punti dati mancanti

I punti dati mancanti sono comuni nei set di dati delle serie temporali distribuiti uniformemente, in particolare quelli con granularità fine (intervallo di campionamento ridotto. Ad esempio, i dati campionati ogni pochi minuti). Manca meno del 10% del numero previsto di punti nei dati non dovrebbe avere un impatto negativo sui risultati del rilevamento. È consigliabile colmare le lacune nei dati in base alle relative caratteristiche, ad esempio la modifica dei punti dati di un periodo precedente, l'interpolazione lineare o una media mobile.

### <a name="aggregate-distributed-data"></a>Dati distribuiti aggregati

L'API Anomaly Detector funziona meglio su una serie temporale distribuita in modo uniforme. Se i dati vengono distribuiti in modo casuale, è necessario aggregarlo in base a un'unità di tempo, ad esempio Per minuto, ogni ora o ogni giorno.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Rilevamento delle anomalie sui dati con modelli stagionali

Se si sa che i dati della serie temporale hanno un modello stagionale (uno che si verifica a intervalli regolari), è possibile migliorare la precisione e il tempo di risposta API. 

Specificare `period` un quando si crea la richiesta JSON può ridurre la latenza di rilevamento anomalie fino al 50%. Il `period` è un numero intero che specifica approssimativamente il numero di punti dati impiegati dalla serie temporale per ripetere un modello. Ad esempio, una serie temporale con un `period` punto `7`dati al giorno avrebbe un come e una serie temporale `period` `7*24`con un punto all'ora (con lo stesso modello settimanale) avrebbe un di . Se non si è sicuri dei modelli dei dati, non è necessario specificare questo parametro.

Per ottenere i `period`migliori risultati, fornire 4 'vale la pena di punto dati, più uno aggiuntivo. Ad esempio, i dati orari con un modello settimanale come descritto in`7 * 24 * 4 + 1`precedenza devono fornire 673 punti dati nel corpo della richiesta ( ).

### <a name="sampling-data-for-real-time-monitoring"></a>Dati di campionamento per il monitoraggio in tempo reale

Se i dati di streaming vengono campionati a intervalli brevi (ad esempio secondi o minuti), l'invio del numero consigliato di punti dati può superare il numero massimo consentito dall'API rilevatore di anomalie (8640 punti dati). Se i dati mostrano un modello stagionale stabile, è consigliabile inviare un campione dei dati delle serie temporali a un intervallo di tempo maggiore, ad esempio le ore. Il campionamento dei dati in questo modo può anche migliorare notevolmente il tempo di risposta dell'API. 

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è l'API Rilevatore di anomalie?](../overview.md)
* [Guida introduttiva: Rilevare le anomalie nei dati della serie temporale usando l'API REST di Rilevamento anomalie](../quickstarts/detect-data-anomalies-csharp.md)
