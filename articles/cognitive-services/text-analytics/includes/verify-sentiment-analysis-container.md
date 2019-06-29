---
title: Supporto dei contenitori
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare l'istanza di contenitore di analisi del sentiment.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455090"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Verificare l'istanza di contenitore di analisi del Sentiment

1. Selezionare il **Panoramica** scheda e copiare l'indirizzo IP.
1. Aprire una nuova scheda del browser e usare l'indirizzo IP, ad esempio, `http://<IP-address>:5000 (http://55.55.55.55:5000`). Home page del contenitore viene visualizzato, che indica il contenitore è in esecuzione.

    ![Visualizzare la home page di contenitore per verificare che sia in esecuzione](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selezionare il **descrizione del servizio API** collegamento per passare alla pagina swagger contenitori.

1. Scegliere una qualsiasi delle **POST** API e selezionare **provarlo**.  Vengono visualizzati i parametri, tra cui l'input di esempio:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Sostituire l'input con il codice JSON seguente:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Impostare **showStats** su true.

1. Selezionare **Execute** per determinare il sentiment del testo.

    Il modello incluso nel pacchetto nel contenitore genera un punteggio compreso tra 0 e 1, dove 0 è negativo e 1 è un valore positivo.

    La risposta JSON restituita include del sentiment per l'input di testo aggiornato:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

È ora possibile correlare il documento `id` dei payload risposta JSON per il documento di payload di richiesta originale `id`e vedere che si è verificato un punteggio pari a failover `.98` che indica un sentimento positivo molto.