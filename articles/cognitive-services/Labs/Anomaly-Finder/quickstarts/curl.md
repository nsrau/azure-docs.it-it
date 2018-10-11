---
title: Come usare l'API Ricerca di anomalie con cURL - Servizi cognitivi Microsoft | Microsoft Docs
description: Ottenere informazioni per iniziare rapidamente a usare cURL e l'API Ricerca di anomalie in Servizi cognitivi.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 31049e24687192b1ea1030a7180299f57bc76771
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48246331"
---
# <a name="use-the-anomaly-finder-api-with-curl"></a>Usare l'API Ricerca di anomalie con cURL

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Questo articolo fornisce informazioni ed esempi di codice per aiutare a iniziare rapidamente a usare l'API Ricerca di anomalie con cURL per acquisire i risultati sulle anomalie di dati di serie temporali.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-finder-api-using-curl"></a>Recupero di punti di anomalie con l'API Ricerca di anomalie usando cURL 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Esempio di dati di serie temporali

L'esempio di punti dati di una serie temporale è riportato di seguito.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-curl-example"></a>Esempio di analisi dei dati e acquisizione dei punti di anomalie con cURL

Per usare l'esempio seguire questi passaggi.

1. Sostituire il valore `[YOUR_SUBSCRIPTION_KEY]` con la propria chiave di sottoscrizione valida.
2. Sostituire `[YOUR_REGION]` per usare la posizione in cui sono state ottenute le chiavi di sottoscrizione.
3. Sostituire `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` con l'esempio o con i propri punti dati.
4. Eseguire il codice e controllare la risposta.

```cURL

curl -v -X POST "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### <a name="example-response"></a>Risposta di esempio
Una risposta con esito positivo viene restituita in JSON. Di seguito viene riportata una risposta di esempio:[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
