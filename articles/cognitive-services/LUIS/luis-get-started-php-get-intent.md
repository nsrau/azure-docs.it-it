---
title: Analizzare testo in linguaggio naturale in Language Understanding (LUIS) con PHP - Servizi cognitivi di Azure | Microsoft Docs
description: In questa guida introduttiva si usa un'app LUIS pubblica disponibile per stabilire l'intenzione di un utente partendo da un testo discorsivo. Con PHP inviare l'intenzione dell'utente come testo all'endpoint di previsione HTTP dell'app pubblica. Nell'endpoint, LUIS applica il modello dell'app pubblica per analizzare il testo in linguaggio naturale allo scopo di identificare il significato, stabilire la finalità generale ed estrarre i dati pertinenti per il dominio dell'app.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 80d9371cc36ca9ab6b25e79a78e15b7445f0084d
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160269"
---
# <a name="quickstart-analyze-text-using-php"></a>Guida introduttiva: Analizzare il testo con PHP

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Prerequisiti

* Linguaggio di programmazione [PHP](http://php.net/)
* [Visual Studio Code](https://code.visualstudio.com/)
* ID app pubblica: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Analizzare il testo con il browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-php"></a>Analizzare il testo con PHP 

È possibile usare PHP per accedere agli stessi risultati illustrati nella finestra del browser nel passaggio precedente. 

1. Copiare il codice seguente e salvarlo con il nome file `endpoint-call.php`:

   [!code-php[PHP code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/php/endpoint-call.php)]

2. Sostituire `"YOUR-KEY"` con la chiave dell'endpoint.

3. Eseguire l'applicazione PHP con il comando `php endpoint-call.php`. Apparirà lo stesso codice JSON visualizzato in precedenza nella finestra del browser.

## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il file PHP.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere espressioni](luis-get-started-php-add-utterance.md)