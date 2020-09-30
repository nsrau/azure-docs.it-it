---
title: 'Guida introduttiva: Ottenere la finalità con le API REST - LUIS'
description: In questo avvio rapido dell'API REST si usa un'app LUIS pubblica disponibile per stabilire l'intenzione di un utente partendo da un testo discorsivo.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: d03ebc1ec5730f1905ce1ee5b9c484312f5aeee1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316333"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Guida introduttiva: Ottenere la finalità con le API REST

In questa argomento di avvio rapido si userà un'app LUIS pubblica disponibile per stabilire l'intenzione di un utente partendo da un testo discorsivo. Inviare l'intenzione dell'utente come testo all'endpoint di previsione HTTP dell'app Pizza. Nell'endpoint, LUIS applica il modello dell'app Pizza per analizzare il testo in linguaggio naturale allo scopo di identificare il significato, stabilire la finalità generale ed estrarre i dati pertinenti per il dominio dell'app.

Per questo articolo è necessario un account [LUIS](https://www.luis.ai) gratuito.

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
