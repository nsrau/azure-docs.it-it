---
title: Supporto per la lingua-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Questo articolo fornisce un elenco dei linguaggi naturali supportati dalle funzionalità di Visione artificiale; OCR, analisi delle immagini.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1e4dfb399432278ee09d1c016faae8fd3b584647
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318047"
---
# <a name="language-support-for-computer-vision"></a>Supporto delle lingue per Visione artificiale

Alcune funzionalità di Visione artificiale supportano più lingue; le funzionalità non indicate in questo documento supportano solo l'inglese.

## <a name="optical-character-recognition-ocr"></a>Riconoscimento ottico dei caratteri (OCR)

Le API OCR di Visione artificiale supportano diverse lingue. Non è necessario specificare un codice di lingua. Per ulteriori informazioni, vedere [riconoscimento ottico dei caratteri (OCR)](concept-recognizing-text.md) .

|Linguaggio| Codice lingua | API OCR | Lettura della versione 3.0 | Leggi l'anteprima pubblica v 3.1 |
|:-----|:----:|:-----:|:---:|:---:|
|Arabo | `ar`|✔ | | |
|Cinese (semplificato) | `zh-Hans`|✔ | |✔ |
|Cinese (tradizionale) | `zh-Hant`|✔ | | |
|Ceco | `cs` |✔ | | |
|Danese | `da` |✔ | | |
|Olandese | `nl` |✔ |✔ |✔ |
|Inglese | `en` |✔ |✔ |✔ |
|Finlandese | `fi` |✔ | | |
|Francese | `fr` |✔ |✔ |✔ |
|Tedesco | `de` |✔ |✔ |✔ |
|Greco | `el` |✔ | | |
|Ungherese | `hu` |✔ | | |
|Italiano | `it` |✔ |✔ |✔ |
|Giapponese | `ja` |✔ | | |
|Coreano | `ko` |✔ | | |
|Norvegese | `nb` |✔ | | |
|Polacco | `pl` |✔ | | |
|Portoghese | `pt` |✔ |✔ |✔ |
|Romeno | `ro` |✔ | | |
|Russo | `ru` |✔ | | |
|Serbo (alfabeto cirillico) | `sr-Cyrl` |✔ | | |
|Serbo (alfabeto latino) | `sr-Latn` |✔ | | |
|Slovacco | `sk` |✔ | | |
|Spagnolo | `es` |✔ |✔ |✔ |
|Svedese | `sw` |✔ | | |
|Turco | `tr` |✔ | | |

## <a name="image-analysis"></a>Analisi di immagini

Alcune azioni dell'API [Analyze-image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) possono restituire i risultati in altri linguaggi, specificati con il `language` parametro di query. Altre azioni restituiscono i risultati in inglese indipendentemente dalla lingua specificata e altre generano un'eccezione per le lingue non supportate. Le azioni vengono specificate con `visualFeatures` i `details` parametri di query e. per un elenco di tutte le azioni che è possibile eseguire con l'analisi delle immagini, vedere la [Panoramica](overview.md) .

|Linguaggio | Codice lingua | Categorie | Tag | Descrizione | Adult (Adulti) | Marchi | Color | Smile | ImageType | Oggetti | Celebrità | Punti di riferimento |
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
