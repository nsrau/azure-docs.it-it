---
title: Verificare l'istanza del contenitore di rilevamento della linguaVerify the Language Detection container instance
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare l'istanza del contenitore di rilevamento della lingua.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876432"
---
### <a name="verify-the-language-detection-container-instance"></a>Verificare l'istanza del contenitore di rilevamento della linguaVerify the Language Detection container instance

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

1. Impostare **showStats** su `true`.

1. Selezionare **Esegui** per determinare il sentiment del testo.

    Il modello incluso nel contenitore genera un punteggio compreso tra 0 e 1, dove 0 è un sentiment negativo e 1 è un sentiment positivo.

    La risposta JSON restituita include il sentiment per l'input di testo aggiornato:The JSON response that's returned includes sentiment for the updated text input:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

È ora possibile correlare i documenti dei dati JSON del payload `id`della risposta ai documenti di payload della richiesta originale in base ai corrispondenti file . Ogni documento viene trattato in `characterCount` modo `transactionCount`indipendente contenente varie statistiche come e . Inoltre, ogni documento risultante `detectedLanguages` include `name` `iso6391Name`la `score` matrice con , e per ogni lingua rilevata. Quando vengono rilevate `score` più lingue, l'oggetto viene utilizzato per determinare la lingua più probabile.