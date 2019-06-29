---
title: Come creare un set di dati di training per un modello personalizzato - riconoscimento modulo
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare che il set di dati di training è ottimizzato per il training di un modello di riconoscimento di Form.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 611d5f7983c61fab12c55a46fedf35a3c420c4c8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454818"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Creare un set di dati di training per un modello personalizzato

Quando si usa il modello di riconoscimento modulo personalizzato, è fornire i propri dati di training in modo che il modello è possibile eseguire il training per i moduli specifici del settore. È possibile eseguire il training di un modello con cinque moduli compilati o un form vuoto (include la parola "vuota" nel nome del file) e due moduli compilati. Anche se si dispone di sufficienti moduli compilati per il training con, l'aggiunta di un form vuoto nel set di dati di training può migliorare l'accuratezza del modello.

## <a name="training-data-tips"></a>Suggerimenti sui dati di training

È importante usare un set di dati ottimizzato per il training. Usare i suggerimenti seguenti per assicurarsi di ottengono risultati ottimali dal [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) operazione:

* Se possibile, usare i documenti PDF basato su testo invece di documenti basati su immagine. Analizzati i PDF vengono gestiti come immagini.
* Se sono stati disponibili, usare un form vuoto e due moduli compilati.
* Per moduli compilati, usare gli esempi che hanno tutti i relativi campi compilati.
* Utilizzare moduli con valori diversi in ogni campo.
* Se le immagini del modulo sono di qualità inferiore, usare un set di dati più grande (10-15 immagini, ad esempio).

## <a name="general-input-requirements"></a>Requisiti generali di input

Assicurarsi che il set di dati di training segue anche i requisiti di input per tutto il contenuto per il riconoscimento modulo.
[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è appreso come creare un set di dati di training, seguire una Guida introduttiva per il training di un modello di riconoscimento modulo personalizzato e iniziare a usarla nei form.

* [Avvio rapido: Eseguire il training di un modello ed estrarre i dati del modulo usando cURL](./quickstarts/curl-train-extract.md)
* [Avvio rapido: Eseguire il training di un modello ed estrarre i dati del modulo usando l'API REST con Python](./quickstarts/python-train-extract.md)

