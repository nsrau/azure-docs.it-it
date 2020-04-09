---
title: Verificare l'istanza del contenitore Key Phrase Extraction
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare l'istanza del contenitore Estrazione frase chiave.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c177517ec18d7526b1cc09da74e35cb5434766d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876430"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>Verificare l'istanza del contenitore Key Phrase Extraction

1. Selezionare la scheda **Panoramica** e copiare l'indirizzo IP.
1. Aprire una nuova scheda del browser e immettere l'indirizzo IP. Ad esempio, `http://<IP-address>:5000 (http://55.55.55.55:5000`immettere ). Viene visualizzata la home page del contenitore, che consente di sapere che il contenitore è in esecuzione.

    ![Visualizzare la home page del contenitore per verificare che sia in esecuzione](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selezionare il collegamento **Descrizione API servizio** per passare alla pagina Swagger del contenitore.

1. Scegliere una delle API **POST** e selezionare **Prova.** Vengono visualizzati i parametri, che include questo input di esempio:The parameters are displayed, which includes this example input:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
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

    Il modello incluso nel contenitore genera un punteggio compreso tra 0 e 1, dove 0 è negativo e 1 è positivo.

    La risposta JSON restituita include il sentiment per l'input di testo aggiornato:The JSON response that's returned includes sentiment for the updated text input:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
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

È ora possibile `id` correlare il documento dei dati JSON `id`del payload della risposta al documento di payload della richiesta originale. Il documento risultante ha una `keyPhrases` matrice, che contiene l'elenco delle frasi chiave che sono state estratte dal documento di input corrispondente. Inoltre, sono disponibili varie `characterCount` statistiche, ad esempio e `transactionCount` per ogni documento risultante.