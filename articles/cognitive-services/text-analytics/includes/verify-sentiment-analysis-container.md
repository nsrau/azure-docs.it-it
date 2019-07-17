---
title: Verificare l'istanza di contenitore di analisi del Sentiment
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare l'istanza di contenitore di analisi del sentiment.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f69d573e9e70a505018e94cca354f363097cc1b8
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229179"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Verificare l'istanza di contenitore di analisi del Sentiment

1. Selezionare il **Panoramica** scheda e copiare l'indirizzo IP.
1. Aprire una nuova scheda del browser e immettere l'indirizzo IP. Ad esempio, immettere `http://<IP-address>:5000 (http://55.55.55.55:5000`). Viene visualizzata la home page del contenitore, che indica il contenitore è in esecuzione.

    ![Visualizzare la home page di contenitore per verificare che sia in esecuzione](../media/how-tos/container-instance/swagger-docs-on-container.png).

1. Selezionare il **descrizione del servizio API** collegamento per passare alla pagina swagger del contenitore.

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

1. Sostituire l'input con il contenuto JSON seguente:

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

    Il modello in un pacchetto nel contenitore genera un punteggio compreso tra 0 e 1, dove 0 è negativo e 1 è un valore positivo.

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

È ora possibile correlare il documento `id` dei dati JSON del payload della risposta per il documento di payload di richiesta originale `id`. Vediamo un punteggio pari a più di `.98`, che indica un sentimento positivo fortemente.