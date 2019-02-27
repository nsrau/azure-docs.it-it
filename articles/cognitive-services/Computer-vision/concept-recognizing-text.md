---
title: Riconoscere testo stampato e scritto a mano - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al riconoscimento di testo stampato e scritto a mano presente nelle immagini tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 48ce15a11c3e3282535420f3e1bb1915276d70f5
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313178"
---
# <a name="recognizing-printed-and-handwritten-text"></a>Riconoscendo di testo stampato e scritto a mano

Visione artificiale può rilevare ed estrarre testo stampato o scritto a mano dalle immagini di diversi oggetti con superfici e sfondi diversi, ad esempio ricette, poster, biglietti da visita, lettere e lavagne.

La funzionalità di riconoscimento di testo è molto simile alla funzionalità di [riconoscimento ottico dei caratteri (OCR)](concept-extracting-text-ocr.md), ma, a differenza di quest'ultima, il riconoscimento avviene in modo asincrono e tramite modelli di riconoscimento aggiornati.

> [!NOTE]
> Questa tecnologia è attualmente disponibile in anteprima e solo per testo in lingua inglese.

## <a name="text-recognition-requirements"></a>Requisiti per il riconoscimento di testo

Visione artificiale può riconoscere testo stampato e scritto a mano nelle immagini che soddisfano i requisiti seguenti:

- L'immagine deve essere presentata in formato JPEG, PNG, o BMP.
- Le dimensioni del file dell'immagine devono essere minori di 4 megabyte (MB)
- Le dimensioni dell'immagine devono essere comprese tra 50x50 e 4200x4200 pixel

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [documentazione di riferimento sul riconoscimento di testo](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200).