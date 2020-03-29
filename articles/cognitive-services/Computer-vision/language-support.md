---
title: Supporto linguistico - Visione artificiale
titleSuffix: Azure Cognitive Services
description: In questo articolo viene fornito un elenco delle lingue naturali supportate dalle funzionalità di Visione artificiale; OCR, Riconosci testo e Leggi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220138"
---
# <a name="language-support-for-computer-vision"></a>Supporto linguistico per Computer Vision

Alcune funzionalità di Computer Vision supportano più lingue; tutte le funzionalità non menzionate qui supportano solo l'inglese.

## <a name="text-recognition"></a>Riconoscimento di testo

Visione artificiale è in grado di riconoscere il testo in molte lingue. In particolare, l'API [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) supporta diverse lingue, mentre l'API [Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) e [l'API Recognize Text](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) supportano solo l'inglese. Per altre informazioni su questa funzionalità e sui vantaggi di ogni API, vedere Riconoscere il [testo stampato e scritto a mano.](concept-recognizing-text.md)

L'OCR rileva automaticamente la lingua del materiale di input, pertanto non è necessario specificare un codice lingua nella chiamata API. Tuttavia, i codici lingua vengono sempre `"language"` restituiti come valore del nodo nella risposta JSON.

|Linguaggio| Codice lingua | OCR API |
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

## <a name="image-analysis"></a>Analisi delle immagini

Alcune azioni dell'API [Analyze - Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) possono restituire `language` risultati in altri linguaggi, specificati con il parametro query. Altre azioni restituiscono risultati in inglese indipendentemente dalla lingua specificata e altre generano un'eccezione per le lingue non supportate. Le azioni vengono `visualFeatures` `details` specificate con i parametri di query e ; vedere [Panoramica](home.md) per un elenco di tutte le azioni che è possibile eseguire con l'analisi delle immagini.

|Linguaggio | Codice lingua | Categorie | Tag | Descrizione | Adult (Adulti) | Marchi | Colore | Smile | ImageType | Oggetti | Celebrità | Punti di riferimento |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Cinese | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglese | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Giapponese | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portoghese | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spagnolo | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Passaggi successivi

Inizia a usare le funzionalità di Visione artificiale menzionate in questa guida.

* [Analizzare un'immagine locale (REST)Analyze a local image (REST)](./quickstarts/csharp-analyze.md)
* [Estrarre il testo stampato (REST)](./quickstarts/csharp-print-text.md)