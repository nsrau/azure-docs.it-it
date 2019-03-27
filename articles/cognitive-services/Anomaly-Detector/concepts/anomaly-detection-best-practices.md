---
title: Le procedure consigliate quando si usa l'API rilevatore di anomalie
description: Informazioni sulle procedure consigliate per il rilevamento di anomalie con l'API rilevatore di anomalie.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 467ac4e475a1c23e25b62c76cfbc959e7ed49465
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484035"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Le procedure consigliate per l'API rilevatore di anomalie

L'API rilevatore di anomalie è un servizio di rilevamento di anomalie senza stato. L'accuratezza e le prestazioni dei relativi risultati possono essere interessati da:

* La preparazione dati delle serie temporali.
* I parametri di API rilevatore di anomalie che sono stati utilizzati.
* Il numero di punti dati nella richiesta di API. 

Usare questo articolo per apprendere le procedure consigliate per l'uso dell'API ottenimento dei migliori risultati per i dati. 

## <a name="data-preparation"></a>Preparazione dei dati

L'API rilevatore di anomalie accetta serie temporale dei dati formattati in un oggetto della richiesta JSON. Una serie temporale può essere qualsiasi dati numerici registrati nel corso del tempo in ordine sequenziale. È possibile inviare windows dei dati delle serie temporali per l'endpoint API rilevatore di anomalie per migliorare le prestazioni dell'API. Il numero minimo di punti dati, che è possibile inviare è 12, mentre quello massimo è 8640 punti. 

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

## <a name="next-steps"></a>Fasi successive

* [Che cos'è l'API rilevatore di anomalie?](../overview.md)
* [Guida introduttiva: Rilevare le anomalie nei dati delle serie temporali utilizzando l'API REST di rilevatore di anomalie](../quickstarts/detect-data-anomalies-csharp.md)
