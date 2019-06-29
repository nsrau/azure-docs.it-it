---
title: Procedure consigliate per l'API Rilevamento anomalie
description: Informazioni sulle procedure consigliate per il rilevamento di anomalie con l'API rilevatore di anomalie.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 1ad4a67d7737733e4c910d3495be29860769f27e
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477817"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Le procedure consigliate per l'API rilevatore di anomalie

L'API rilevatore di anomalie è un servizio di rilevamento di anomalie senza stato. L'accuratezza e le prestazioni dei relativi risultati possono essere interessati da:

* La preparazione dati delle serie temporali.
* I parametri di API rilevatore di anomalie che sono stati utilizzati.
* Il numero di punti dati nella richiesta di API. 

Usare questo articolo per apprendere le procedure consigliate per l'uso dell'API ottenimento dei migliori risultati per i dati. 

## <a name="when-to-use-batch-entire-or-latest-last-point-anomaly-detection"></a>Quando usare batch (intero) o versione più recente (ultima) punto di rilevamento anomalie

Endpoint rilevamento batch dell'API di rilevamento delle anomalie consentono di rilevare anomalie attraverso l'intera volte i dati della serie. In questa modalità di rilevamento, un modello statistico singolo creato e applicato a ogni punto nel set di dati. Se la serie temporale presenta le caratteristiche, seguenti è consigliabile usare il rilevamento di batch per visualizzare in anteprima i dati in un'unica chiamata API.

* Una serie temporale stagionale, con anomalie occasionali.
* Una serie temporale tendenza piatta, con picchi occasionali o DIP. 

Non è consigliabile usare il rilevamento delle anomalie di batch per i dati in tempo reale di monitoraggio o utilizzarlo nei dati delle serie temporali che non hanno precedenza caratteristiche. 

* Rilevamento batch Crea e applica un solo modello, il rilevamento per ogni punto viene eseguito nel contesto dell'intera serie. Se la serie dati le tendenze nel tempo su e giù senza stagionalità, alcuni punti di modifica (DIP e i picchi di dati) non vengano trovate dal modello. Allo stesso modo, alcuni punti di modifica che sono meno significative rispetto a quelle in un secondo momento nel set di dati non possono essere considerate sufficientemente elevata da essere incorporate nel modello.

* Rilevamento batch è più lento rispetto a rilevare lo stato delle anomalie del punto più recente quando si esegue il monitoraggio dei dati in tempo reale, a causa del numero di punti che si sta analizzando.

Per il monitoraggio in tempo reale dei dati, è consigliabile rilevare lo stato delle anomalie di solo il punto dati più recente. Con l'applicazione in modo continuo più recente punto di rilevamento, il monitoraggio dei dati di streaming può essere eseguito in modo più efficiente e con maggiore precisione.

L'esempio seguente viene descritto l'impatto di che queste modalità di rilevamento possono avere sulle prestazioni. La prima figura mostra il risultato di rilevare in modo continuo il punto più recente stato anomalie lungo i punti dati già visualizzati 28. I punti rossi sono le anomalie.

![Un'immagine che mostra il rilevamento di anomalie usando il punto più recente](../media/last.png)

Di seguito è lo stesso set di dati tramite il rilevamento anomalie di batch. Il modello compilato per l'operazione ha ignorato anomalie diversi, contrassegnate da rettangoli.

![Un'immagine che mostra il rilevamento di anomalie usando il metodo di batch](../media/entire.png)

## <a name="data-preparation"></a>Preparazione dei dati

L'API rilevatore di anomalie accetta serie temporale dei dati formattati in un oggetto della richiesta JSON. Una serie temporale può essere qualsiasi dati numerici registrati nel corso del tempo in ordine sequenziale. È possibile inviare windows dei dati delle serie temporali per l'endpoint API rilevatore di anomalie per migliorare le prestazioni dell'API. Il numero minimo di punti dati, che è possibile inviare è 12, mentre quello massimo è 8640 punti. [Granularità](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.anomalydetector.models.granularity?view=azure-dotnet-preview) è definito come la frequenza con cui i dati vengono campionati. 

Punti dati inviati per l'API rilevatore di anomalie devono avere un timestamp valido Coordinated Universal Time (UTC) e valore numerico. 

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

Se i dati verranno campionati con un intervallo di tempo non standard, è possibile specificare mediante l'aggiunta di `customInterval` attributo nella richiesta. Ad esempio, se le serie vengono campionati ogni 5 minuti, è possibile aggiungere quanto segue per la richiesta JSON:

```json
{
    "granularity" : "minutely", 
    "customInterval" : 5
}
```

### <a name="missing-data-points"></a>Punti dati mancanti

I punti dati mancanti sono comuni nei set di dati serie ora distribuite in modo uniforme, in particolare quelli con una notevole granularità (un intervallo di campionamento di piccole dimensioni. Ad esempio, i dati campionati ogni pochi minuti). Manca meno di 10% del numero previsto di punti dati non dovrà avere un impatto negativo sui risultati dell'individuazione. Prendere in considerazione colmando i vuoti nei dati in base alle relative caratteristiche, ad esempio sostituendo i punti dati da un periodo precedente, l'interpolazione lineare o una media mobile.

### <a name="aggregate-distributed-data"></a>Aggregazione dati distribuiti

L'API rilevatore di anomalie funziona meglio su una serie temporale uniforme. Se i dati vengono distribuiti in modo casuale, è necessario aggregare da un'unità di tempo, ad esempio al minuto, oraria o giornaliero, ad esempio.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Rilevamento anomalie sui dati con modelli stagionali

Se si sa che i dati della serie temporale dispongono di un modello stagionale (uno che si verifica a intervalli regolari), è possibile migliorare la precisione e tempo di risposta dell'API. 

Specificando un `period` quando si costruisce la richiesta JSON può ridurre la latenza di rilevamento anomalie fino al 50%. Il `period` è un numero intero che specifica la serie temporale i punti dati approssimativamente quante necessarie per la ripetizione di un modello. Ad esempio, una serie temporale con un punto dati al giorno avrebbe una `period` come `7`, e una serie temporale con un solo punto ogni ora (con lo stesso modello settimana) avrebbe un `period` di `7*24`. Se non si è certi dei modelli dei dati, non è necessario specificare questo parametro.

Per ottenere risultati ottimali, specificare 4 `period`del patrimonio di punto dati, più un uno aggiuntivo. Ad esempio, i dati ogni orari con un criterio a settimana come descritto in precedenza devono fornire 673 punti dati nel corpo della richiesta (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Dati di campionamento per il monitoraggio in tempo reale

Se i dati di streaming viene campionati a un breve intervallo (ad esempio secondi o minuti), inviare il numero consigliato di punti dati potrebbe superare massimo numero consentito (8640 punti dati) dell'API di rilevamento delle anomalie. Se i dati di viene illustrato un modello stagionale stabile, prendere in considerazione l'invio di un campione di dati delle serie temporali in un intervallo di tempo più grande, ad esempio ore. Campionamento dei dati in questo modo anche notevolmente migliorare i tempi di risposta API. 

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è l'API rilevatore di anomalie?](../overview.md)
* [Avvio rapido: Rilevare le anomalie nei dati delle serie temporali utilizzando l'API REST di rilevatore di anomalie](../quickstarts/detect-data-anomalies-csharp.md)
