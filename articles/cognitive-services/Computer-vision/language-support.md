---
title: Supporto di Language - visione artificiale
titleSuffix: Azure Cognitive Services
description: Un elenco dei linguaggi naturali supportate dalle caratteristiche di visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1a70d1b2ea504d0ccfba925810a2d19d0c7583cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60759606"
---
# <a name="language-support-for-computer-vision"></a>Supporto del linguaggio per visione artificiale

Alcune funzionalità di visione artificiale supporta più linguaggi. le funzionalità non indicate di seguito supportano solo in lingua inglese.

## <a name="text-recognition"></a>Riconoscimento di testo

Visione artificiale può riconoscere il testo in molte lingue. In particolare, il [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API supporta un'ampia gamma di linguaggi, mentre le [lettura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) API e [riconoscere testo](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) API supportano solo in lingua inglese. Visualizzare [Riconosci il testo scritto a mano e stampato](concept-recognizing-text.md) per altre informazioni su questa funzionalità e i vantaggi di ogni API.

OCR rileva automaticamente la lingua del materiale di input, pertanto non è necessario specificare un codice di lingua nella chiamata API. Tuttavia, i codici di lingua vengono sempre restituiti come il valore della `"language"` nodo nella risposta JSON.

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

Alcune operazioni dei [analizzare - immagine](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API possono restituire risultati in altri linguaggi, specificati con il `language` parametro di query. Altre azioni restituiscono i risultati in inglese indipendentemente da quale lingua è specificata, e ad altri utenti genera un'eccezione per le lingue non supportate. Le azioni vengono specificate con il `visualFeatures` e `details` i parametri di query, vedere la [Panoramica](home.md) per un elenco di tutte le azioni eseguibili con analisi delle immagini.

|Linguaggio | Codice lingua | Categories | `Tags` | Descrizione | Per adulti | Marchi | Colore | Visi | ImageType | Oggetti | Celebrità | Punti di riferimento |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|Cinese | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Inglese | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|Giapponese | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Portoghese | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|Spagnolo | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare le funzionalità di visione artificiale citate in questa Guida.

* [Analizzare un'immagine locale (REST)](./quickstarts/csharp-analyze.md)
* [Estrai testo stampato (REST)](./quickstarts/csharp-print-text.md)