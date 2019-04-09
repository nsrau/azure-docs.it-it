---
title: "Guida introduttiva: Generare un'anteprima - REST, cURL"
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva, si genererà l'anteprima di un'immagine usando l'API Visione artificiale con cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 20a438f4eb932596647002bf9d3072c651ac969c
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630644"
---
# <a name="quickstart-generate-a-thumbnail-using-the-rest-api-and-curl-in-computer-vision"></a>Avvio rapido: Generare un'anteprima con l'API REST e cURL in Visione artificiale

In questo argomento di avvio rapido si genererà l'anteprima di un'immagine usando l'API REST di Visione artificiale. Si specificano l'altezza e la larghezza desiderate, che possono essere diverse rispetto alle proporzioni dell'immagine di input. Visione artificiale ricorre al ritaglio intelligente per identificare l'area di interesse in modo intelligente e generare le coordinate di ritaglio in tale area.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- È necessario avere [cURL](https://curl.haxx.se/windows).
- È necessario avere una chiave di sottoscrizione per Visione artificiale. Per ottenere una chiave di sottoscrizione, vedere [Come ottenere chiavi di sottoscrizione](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-thumbnail-request"></a>Richiesta di generazione di un'anteprima

Con il [metodo Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), è possibile generare un'anteprima di un'immagine.

Per eseguire l'esempio, seguire questa procedura:

1. Copiare il codice seguente in un editor.
1. Sostituire `<Subscription Key>` con la propria chiave di sottoscrizione valida.
1. Sostituire `<File>` con il percorso e il nome del file per salvare l'anteprima.
1. Modificare l'URL della richiesta (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) in modo da usare l'indirizzo in cui si sono ottenute le chiavi di sottoscrizione, se necessario.
1. Facoltativamente, modificare l'immagine (`{\"url\":\"...`) da analizzare.
1. Aprire un prompt dei comandi in un computer in cui è installato cURL.
1. Incollare il codice nella finestra ed eseguire il comando.

>[!NOTE]
>Usare lo stesso percorso nella chiamata REST usto per ottenere le chiavi di sottoscrizione. Se ad esempio si sono ottenute le chiavi di sottoscrizione da westus, sostituire "westcentralus" nell'URL riportato di seguito con "westus".

## <a name="create-and-run-the-sample-command"></a>Creare ed eseguire il comando di esempio

Per creare ed eseguire l'esempio, seguire questa procedura:

1. Copiare il comando seguente in un editor di testo.
1. Apportare le modifiche seguenti al comando, dove necessario:
    1. Sostituire il valore di `<subscriptionKey>` con la chiave di sottoscrizione.
    1. Sostituire il valore di `<thumbnailFile>` con il percorso e il nome del file in cui salvare l'anteprima.
    1. Se necessario, sostituire l'URL della richiesta (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail`) con l'URL endpoint per il metodo [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) dall'area di Azure in cui sono state ottenute le chiavi di sottoscrizione.
    1. Facoltativamente, modificare l'URL dell'immagine nel corpo della richiesta (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) nell'URL di un'altra immagine da cui generare un'anteprima.
1. Aprire una finestra del prompt dei comandi.
1. Incollare il comando dall'editor di testo nella finestra del prompt dei comandi e quindi eseguire il comando.

    ```console
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Esaminare i risultati

Una risposta con esito positivo scrive l'immagine di anteprima nel file specificato in `<thumbnailFile>`. Se invece la richiesta ha esito negativo, la risposta contiene un codice di errore e un messaggio per determinarne la causa. Se sembra che la richiesta abbia esito positivo ma l'anteprima creata non è un file di immagine valido, è possibile che la chiave di sottoscrizione non sia valida.

## <a name="next-steps"></a>Passaggi successivi

Esaminare l'API Visione artificiale per analizzare un'immagine, rilevare celebrità e luoghi di interesse, creare un'anteprima ed estrarre testo scritto a mano e stampato. Per sperimentare rapidamente l'API Visione artificiale, provare la [console di test dell'API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Esplorare l'API Visione artificiale](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
