---
title: Supporto per la lingua-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Questo articolo fornisce un elenco dei linguaggi naturali supportati dalle funzionalità di Visione artificiale; OCR, riconoscimento del testo e Read.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718604"
---
# <a name="language-support-for-computer-vision"></a>Supporto delle lingue per Visione artificiale

Alcune funzionalità di Visione artificiale supportano più lingue; le funzionalità non indicate in questo documento supportano solo l'inglese.

## <a name="text-recognition"></a>Riconoscimento di testo

Visione artificiale possibile riconoscere il testo in molte lingue. In particolare, l'API [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) supporta un'ampia gamma di linguaggi, mentre l'API di [lettura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) e l'API di [riconoscimento del testo](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) supportano solo l'inglese. Per altre informazioni su questa funzionalità e sui vantaggi di ogni API, vedere [riconoscere testo stampato e scritto a mano](concept-recognizing-text.md) .

OCR rileva automaticamente la lingua del materiale di input, pertanto non è necessario specificare un codice della lingua nella chiamata API. Tuttavia, i codici di lingua vengono sempre restituiti come valore del nodo `"language"` nella risposta JSON.

|Lingua| Codice lingua | API OCR |
|:-----|:----:|:-----:|
|Arabo | `ar`|✔ |
|Cinese (semplificato) | `zh-Hans`|✔ |
|Cinese (tradizionale) | `zh-Hant`|✔ |
|Ceco | `cs` |✔ |
|Danese | `da` |✔ |
|Olandese | `nl` |✔ |
|Inglese | `en` |✔ |
|Finlandese | `fi` |✔ |
|Francese | `fr` |✔ |
|Tedesco | `de` |✔ |
|Greco | `el` |✔ |
|Ungherese | `hu` |✔ |
|Italiano | `it` |✔ |
|Giapponese | `ja` |✔ |
|Coreano | `ko` |✔ |
|Norvegese | `nb` |✔ |
|Polacco | `pl` |✔ |
|Portoghese | `pt` |✔ |
|Rumeno | `ro` |✔ |
|Russo | `ru` |✔ |
|Serbo (alfabeto cirillico) | `sr-Cyrl` |✔ |
|Serbo (alfabeto latino) | `sr-Latn` |✔ |
|Slovacco | `sk` |✔ |
|Spagnolo | `es` |✔ |
|Svedese | `sw` |✔ |
|Turco | `tr` |✔ |

## <a name="image-analysis"></a>Analisi immagini

Alcune azioni dell'API [Analyze-image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) possono restituire i risultati in altri linguaggi, specificati con il parametro di query `language`. Altre azioni restituiscono i risultati in inglese indipendentemente dalla lingua specificata e altre generano un'eccezione per le lingue non supportate. Le azioni vengono specificate con i parametri di query `visualFeatures` e `details`. per un elenco di tutte le azioni che è possibile eseguire con l'analisi delle immagini, vedere la [Panoramica](home.md) .

|Lingua | Codice lingua | Categorie | Tag | Descrizione | Per adulti | Marchi | Colore | Visi | ImageType | Oggetti | Celebrità | Punti di riferimento |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Cinese | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglese | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Giapponese | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portoghese | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spagnolo | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare le funzionalità di Visione artificiale citate in questa guida.

* [Analizzare un'immagine locale (REST)](./quickstarts/csharp-analyze.md)
* [Estrai testo stampato (REST)](./quickstarts/csharp-print-text.md)