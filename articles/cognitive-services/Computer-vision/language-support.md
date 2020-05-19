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
ms.openlocfilehash: a207118af0b07be79c934a9665f47e73c5eecde0
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589194"
---
# <a name="language-support-for-computer-vision"></a>Supporto delle lingue per Visione artificiale

Alcune funzionalità di Visione artificiale supportano più lingue; le funzionalità non indicate in questo documento supportano solo l'inglese.

## <a name="text-recognition"></a>Riconoscimento di testo

Visione artificiale possibile riconoscere il testo in molte lingue. Per le API OCR e Read non è necessario specificare un codice di lingua. Per altre informazioni su questa funzionalità e sui vantaggi di ogni API, vedere [riconoscere testo stampato e scritto a mano](concept-recognizing-text.md) .

|Linguaggio| Codice lingua | API OCR | API di lettura |
|:-----|:----:|:-----:|:---:|
|Arabo | `ar`|✔ | |
|Cinese (semplificato) | `zh-Hans`|✔ | |
|Cinese (tradizionale) | `zh-Hant`|✔ | |
|Ceco | `cs` |✔ | |
|Danese | `da` |✔ | |
|Olandese | `nl` |✔ |✔ |
|Inglese | `en` |✔ |✔ |
|Finlandese | `fi` |✔ | |
|Francese | `fr` |✔ |✔ |
|Tedesco | `de` |✔ |✔ |
|Greco | `el` |✔ | |
|Ungherese | `hu` |✔ | |
|Italiano | `it` |✔ |✔ |
|Giapponese | `ja` |✔ | |
|Coreano | `ko` |✔ | |
|Norvegese | `nb` |✔ | |
|Polacco | `pl` |✔ | |
|Portoghese | `pt` |✔ |✔ |
|Romeno | `ro` |✔ | |
|Russo | `ru` |✔ | |
|Serbo (alfabeto cirillico) | `sr-Cyrl` |✔ | |
|Serbo (alfabeto latino) | `sr-Latn` |✔ | |
|Slovacco | `sk` |✔ | |
|Spagnolo | `es` |✔ |✔ |
|Svedese | `sw` |✔ | |
|Turco | `tr` |✔ | |

## <a name="image-analysis"></a>Analisi immagini

Alcune azioni dell'API [Analyze-image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) possono restituire i risultati in altri linguaggi, specificati con il `language` parametro di query. Altre azioni restituiscono i risultati in inglese indipendentemente dalla lingua specificata e altre generano un'eccezione per le lingue non supportate. Le azioni vengono specificate con `visualFeatures` i `details` parametri di query e. per un elenco di tutte le azioni che è possibile eseguire con l'analisi delle immagini, vedere la [Panoramica](home.md) .

|Linguaggio | Codice lingua | Categorie | Tag | Descrizione | Adult (Adulti) | Marchi | Colore | Smile | ImageType | Oggetti | Celebrità | Punti di riferimento |
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
