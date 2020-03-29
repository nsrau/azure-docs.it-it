---
title: Verificare l'istanza del contenitore Analisi del sentimentVerify the Sentiment Analysis container instance
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare l'istanza del contenitore Analisi del sentiment.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 2e201b4ec0d1364ea99b376171efabad65af0a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968585"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Verificare l'istanza del contenitore Analisi del sentimentVerify the Sentiment Analysis container instance

1. Selezionare la scheda **Panoramica** e copiare l'indirizzo IP.
1. Aprire una nuova scheda del browser e immettere l'indirizzo IP. Ad esempio, `http://<IP-address>:5000 (http://55.55.55.55:5000`immettere ). Viene visualizzata la home page del contenitore, che consente di sapere che il contenitore è in esecuzione.

    ![Visualizzare la home page del contenitore per verificare che sia in esecuzione](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selezionare il collegamento **Descrizione API servizio** per passare alla pagina Swagger del contenitore.

1. Scegliere una delle API **POST** e selezionare **Prova.** Vengono visualizzati i parametri, che include questo input di esempio:The parameters are displayed, which includes this example input:

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

1. Sostituire l'input con il contenuto JSON seguente:Replace the input with the following JSON content:

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

1. Impostare **showStats** su `true`.

1. Selezionare **Esegui** per determinare il sentiment del testo.

    Il modello incluso nel contenitore genera un punteggio compreso tra 0 e 1, dove 0 è un sentiment negativo e 1 è un sentiment positivo.

    La risposta JSON restituita include il sentiment per l'input di testo aggiornato:The JSON response that's returned includes sentiment for the updated text input:

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

È ora possibile `id` correlare il documento dei dati JSON `id`del payload della risposta al documento di payload della richiesta originale. Il punteggio `0.98` di più che indica un sentimento molto positivo.