---
title: Ottenere la finalità, Python - LUIS
titleSuffix: Azure Cognitive Services
description: In questa guida introduttiva si passano le espressioni a un endpoint LUIS per ottenere la restituzione di finalità ed entità.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: defcb6713516952e56e6cca2d63a99ee61f5b868
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563786"
---
# <a name="quickstart-get-intent-using-python"></a>Avvio rapido: Ottenere la finalità tramite Python
In questa guida introduttiva si passano le espressioni a un endpoint LUIS per ottenere la restituzione di finalità ed entità.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Prerequisiti

* [Python 3.6](https://www.python.org/downloads/) o versione successiva.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Ottenere la chiave di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Ottenere la finalità tramite browser

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>Ottenere la finalità a livello di codice

È possibile usare Python per accedere agli stessi risultati illustrati nella finestra del browser nel passaggio precedente.

1. Copiare uno dei frammenti di codice seguenti in un file denominato `quickstart-call-endpoint.py`:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Sostituire il valore del campo `Ocp-Apim-Subscription-Key` con la chiave dell'endpoint LUIS.

3. Installare le dipendenze con il comando `pip install requests`.

4. Eseguire lo script con il comando `python ./quickstart-call-endpoint.py`. Apparirà lo stesso codice JSON visualizzato in precedenza nella finestra del browser.

## <a name="luis-keys"></a>Chiavi di LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Pulire le risorse
Eliminare il file Python. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere espressioni](luis-get-started-python-add-utterance.md)
