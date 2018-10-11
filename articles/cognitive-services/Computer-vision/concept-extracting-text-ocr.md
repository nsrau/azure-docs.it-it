---
title: Estrazione di testo con OCR - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi all'estrazione di testo con il riconoscimento ottico dei caratteri (OCR) usando l'API Visione artificiale.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 8cfc654e881c7477e430515e62f8c78cfd0a2b84
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238589"
---
# <a name="extracting-text-with-ocr"></a>Estrazione di testo con OCR

La tecnologia di riconoscimento ottico dei caratteri (OCR) nella Visione artificiale rileva il contenuto di testo di un'immagine ed estrae il testo individuato in un flusso di caratteri leggibili dal computer. È possibile usare il risultato per eseguire ricerche e per numerosi altri scopi, ad esempio compilazione di cartelle cliniche, sicurezza e servizi bancari. La lingua viene rilevata automaticamente. La tecnologia OCR consente di risparmiare tempo ed è molto pratica perché permette agli utenti di scattare foto del testo invece di trascriverlo.

OCR supporta 25 lingue, ovvero arabo, cinese semplificato, cinese tradizionale, ceco, danese, olandese, inglese, finlandese, francese, tedesco, greco, ungherese, italiano, giapponese, coreano, norvegese, polacco, portoghese, romeno, russo, serbo (cirillico e latino), slovacco, spagnolo, svedese e turco.

Se necessario, la funzione OCR corregge la rotazione del testo riconosciuto, espresso in gradi, intorno all'asse orizzontale dell'immagine. La tecnologia OCR indica le coordinate del riquadro di tutte le parole come illustrato nell'immagine seguente.

![Panoramica sulla tecnologia OCR](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Requisiti della tecnologia OCR

La Visione artificiale consente di estrarre testo attraverso la tecnologia OCR da immagini che rispettano i requisiti seguenti:

* L'immagine deve essere presentata in formato JPEG, PNG, GIF o BMP
* Le dimensioni dell'immagine di input devono essere comprese tra 50x50 e 4200x4200 pixel


L'immagine di input può essere ruotata di qualsiasi multiplo di 90 gradi e in aggiunta di un piccolo angolo non superiore a 40 gradi.

## <a name="improving-ocr-accuracy"></a>Migliorare la precisione della tecnologia OCR

La precisione del riconoscimento del testo dipende dalla qualità dell'immagine. Una lettura non corretta potrebbe dipendere dalle situazioni seguenti:

* Immagini sfocate.
* Testo in corsivo o scritto a mano.
* Stili di caratteri artistici.
* Testo di piccole dimensioni.
* Sfondi complessi, ombre o riflessi sul testo o distorsione prospettica.
* Lettere maiuscole mancanti o di dimensioni eccessive all'inizio delle parole
* Testo barrato, pedici o apici.

### <a name="ocr-limitations"></a>Limitazioni della tecnologia OCR

Nelle fotografie dove il testo è predominante, si possono ottenere risultati falsi positivi da parole riconosciute parzialmente. In alcune foto, in particolare quelle in cui non è presente alcun testo, la precisione può variare notevolmente a seconda del tipo di immagine.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul [riconoscimento del testo stampato e di quello scritto a mano](concept-recognizing-text.md).
