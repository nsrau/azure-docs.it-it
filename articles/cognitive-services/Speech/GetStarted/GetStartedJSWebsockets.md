---
title: Introduzione all'API Riconoscimento vocale Bing in JavaScript | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Usare l'API Riconoscimento vocale Bing in Servizi cognitivi per sviluppare applicazioni per la conversione continua di audio parlato in testo.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: c5869133d94f73e1d463a176614617df2587bdfb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226106"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Introduzione all'API Riconoscimento vocale in JavaScript

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Tramite l'API Riconoscimento vocale, è possibile sviluppare applicazioni che convertono audio parlato in testo. La libreria client JavaScript usa il [protocollo WebSocket del Servizio di riconoscimento vocale](../API-Reference-REST/websocketprotocol.md), che consente di parlare e contemporaneamente ricevere testo trascritto. Questo articolo illustra come usare l'API Riconoscimento vocale in JavaScript.

## <a name="prerequisites"></a>Prerequisiti

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Eseguire la sottoscrizione all'API Riconoscimento vocale e ottenere una chiave di sottoscrizione della versione di valutazione gratuita

Speech API fa parte di Servizi cognitivi. È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita dalla pagina di [sottoscrizione di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/). Dopo aver selezionato Speech API, fare clic su **Ottieni la chiave API** per ottenere la chiave. Vengono restituite una chiave primaria e una chiave secondaria. Entrambe le chiavi sono legate alla stessa quota ed è quindi possibile usare indifferentemente una delle due.

> [!IMPORTANT]
> Ottenere una chiave di sottoscrizione. Prima di poter usare le librerie client per il riconoscimento vocale, è necessario avere una [chiave di sottoscrizione](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Attività iniziali

Questa sezione illustra i passaggi necessari per caricare una pagina HTML di esempio. L'esempio si trova nel [repository GitHub](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). È possibile **aprire l'esempio direttamente** dal repository o **aprire il file di esempio da una copia locale** del repository.

> [!NOTE]
> Alcuni browser bloccano l'accesso al microfono da origini non sicure. In tal caso, è consigliabile ospitare 'esempio'/'app' su https, in modo che funzioni su tutti i browser supportati.

### <a name="open-the-sample-directly"></a>Aprire l'esempio direttamente

Acquisire una chiave di sottoscrizione come descritto in precedenza. Aprire quindi il [collegamento all'esempio](https://htmlpreview.github.io/? https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Questa pagina viene caricata nel browser predefinito (visualizzabile tramite [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Aprire l'esempio da una copia locale

Per provare l'esempio in locale, clonare questo repository:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Compilare le origini di TypeScript e aggregarle esplorabili in un singolo file JavaScript ([npm](https://www.npmjs.com/) deve essere installato nel computer). Modificare la radice del repository clonato ed eseguire i comandi:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Aprire il file `samples\browser\Sample.html` nel browser preferito.

## <a name="next-steps"></a>Passaggi successivi

In [questo articolo](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) sono disponibili altre informazioni su come includere l'SDK nella pagina Web.

## <a name="remarks"></a>Osservazioni

- L'API Riconoscimento vocale supporta tre [modalità di riconoscimento](../concepts.md#recognition-modes). È possibile cambiare la modalità aggiornando la funzionalità **Setup()** nel file Sample.html. L'esempio imposta la modalità su `Interactive` per impostazione predefinita. Per cambiare la modalità, aggiornare il parametro `SR.RecognitionMode.Interactive` con un'altra modalità. Ad esempio, impostare il parametro su `SR.RecognitionMode.Conversation`.
- Per un elenco completo delle lingue supportate, vedere [Lingue supportate](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Argomenti correlati

- [JavaScript Speech Recognition API sample repository](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript) (Repository di esempio dell'API Riconoscimento vocale per JavaScript)
- [Introduzione all'API REST](GetStartedREST.md)
