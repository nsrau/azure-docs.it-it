---
title: Estrazione di testo con il riconoscimento ottico dei caratteri (OCR) - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi all'estrazione di testo con il riconoscimento ottico dei caratteri (OCR) usando l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb73eb9fdd6879a5fbe1fed820bf92b2d627b65
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310441"
---
# <a name="extract-text-with-optical-character-recognition"></a>Estrarre testo con il riconoscimento ottico dei caratteri

La funzionalità di riconoscimento ottico dei caratteri (OCR) di Visione artificiale rileva il contenuto di testo di un'immagine e converte il testo individuato in un flusso di caratteri leggibili dal computer. È possibile usare il risultato per numerosi scopi, ad esempio ricerche, cartelle cliniche, sicurezza e servizi bancari. 

La funzionalità OCR supporta 25 lingue: arabo, cinese semplificato, cinese tradizionale, ceco, coreano, danese, finlandese, francese, giapponese, greco, inglese, italiano, norvegese, olandese, polacco, portoghese, rumeno, russo, serbo (cirillico e latino), slovacco, spagnolo, svedese, tedesco, turco e ungherese. Questa funzionalità rileva automaticamente la lingua del testo rilevato.

Se necessario, la funzionalità OCR corregge la rotazione del testo riconosciuto, restituendo l'angolo di rotazione intorno all'asse orizzontale dell'immagine in gradi. Questa funzionalità indica anche le coordinate del riquadro di ogni parola, come illustrato nella figura seguente.

![Un diagramma che illustra un'immagine che viene ruotata e il suo testo che viene letto e delineato](./Images/vision-overview-ocr.png)

## <a name="image-requirements"></a>Requisiti dell'immagine

La Visione artificiale consente di estrarre testo attraverso la tecnologia OCR da immagini che rispettano i requisiti seguenti:

* L'immagine deve essere presentata in formato JPEG, PNG, GIF o BMP
* Le dimensioni dell'immagine di input devono essere comprese tra 50x50 e 4200x4200 pixel
* Il testo nell'immagine può essere ruotato di qualsiasi multiplo di 90 gradi più un piccolo angolo non superiore a 40 gradi.

## <a name="improving-ocr-accuracy"></a>Migliorare la precisione della tecnologia OCR

La precisione del riconoscimento del testo dipende dalla qualità dell'immagine. Una lettura non corretta può dipendere da quanto segue:

* Immagini sfocate.
* Testo in corsivo o scritto a mano.
* Stili di caratteri artistici.
* Testo di piccole dimensioni.
* Sfondi complessi, ombre o riflessi sul testo o distorsione prospettica.
* Lettere maiuscole mancanti o di dimensioni eccessive all'inizio delle parole
* Testo barrato, pedici o apici.

### <a name="ocr-limitations"></a>Limitazioni della tecnologia OCR

Nelle immagini dove il testo è predominante, si possono ottenere risultati falsi positivi da parole riconosciute parzialmente. In alcune immagini, in particolare quelle in cui non è presente alcun testo, la precisione può variare notevolmente a seconda del tipo di immagine.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere la [documentazione di riferimento sulla funzionalità OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc).
