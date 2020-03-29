---
title: Riconoscimento del testo stampato e scritto a mano - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concetti relativi al riconoscimento di testo stampato e scritto a mano presente nelle immagini tramite l'API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220160"
---
# <a name="recognize-printed-and-handwritten-text"></a>Riconoscere il testo stampato e scritto a mano

Computer Vision fornisce una serie di servizi che rilevano ed estraggono testo stampato o scritto a mano che appare nelle immagini. Ciò è utile in una varietà di scenari, ad esempio prendere appunti, cartelle cliniche, sicurezza e bancario. Nelle tre sezioni seguenti vengono descritte in dettaglio tre diverse API di riconoscimento del testo, ognuna ottimizzata per diversi casi d'uso.

## <a name="read-api"></a>API di lettura

L'API Read rileva il contenuto di testo in un'immagine utilizzando i modelli di riconoscimento più recenti e converte il testo identificato in un flusso di caratteri leggibile dal computer. È ottimizzato per le immagini con molte immagini con molte immagini con molte immagini (come i documenti digitalmente scansionati) e per le immagini con molto rumore visivo. Determina quale modello di riconoscimento utilizzare per ogni riga di testo, supportando le immagini con testo stampato e scritto a mano. L'API di lettura viene eseguita in modo asincrono perché documenti di dimensioni maggiori possono richiedere alcuni minuti per restituire un risultato.

L'operazione di lettura mantiene i raggruppamenti di righe originali delle parole riconosciute nell'output. Ogni riga è dotata di coordinate del riquadro di delimitazione e ogni parola all'interno della riga ha anche le proprie coordinate. Se una parola è stata riconosciuta con scarsa fiducia, anche queste informazioni vengono trasmesse. Per altre informazioni, vedi i documenti di [riferimento sull'API Read](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) e Read API [Preview.](https://go.microsoft.com/fwlink/?linkid=2118322)

> [!NOTE]
> Questa funzione è disponibile solo per il testo in inglese e spagnolo (anteprima).

### <a name="image-requirements"></a>Requisiti dell'immagine

L'API Read funziona con immagini che soddisfano i requisiti seguenti:The Read API works with images that meet the following requirements:

- L'immagine deve essere presentata in formato JPEG, PNG, BMP, PDF o TIFF.
- Le dimensioni dell'immagine devono essere comprese tra 50 x 50 e 10000 x 10000 pixel. Le pagine PDF devono essere di 17 x 17 pollici o più piccole.
- La dimensione del file dell'immagine deve essere inferiore a 20 megabyte (MB).

### <a name="limitations"></a>Limitazioni

Se si utilizza un abbonamento a livello gratuito, l'API di lettura elaborerà solo le prime due pagine di un documento PDF o TIFF. Con un abbonamento a pagamento, elaborerà fino a 200 pagine. Si noti inoltre che l'API rileverà un massimo di 300 righe per pagina.

## <a name="ocr-optical-character-recognition-api"></a>API OCR (riconoscimento ottico dei caratteri)

L'API di riconoscimento ottico dei caratteri (OCR) di Visione computergrafica è simile all'API Read, ma viene eseguita in modo sincrono e non è ottimizzata per documenti di grandi dimensioni. Utilizza un modello di riconoscimento precedente, ma funziona con più lingue; vedere [Supporto della lingua](language-support.md#text-recognition) per un elenco completo delle lingue supportate.

Se necessario, la funzionalità OCR corregge la rotazione del testo riconosciuto, restituendo l'angolo di rotazione intorno all'asse orizzontale dell'immagine in gradi. L'OCR fornisce anche le coordinate del frame di ogni parola, come illustrato nella figura seguente.

![Un'immagine ruotata e il relativo testo letto e delineato](./Images/vision-overview-ocr.png)

Per ulteriori informazioni, consulta i documenti di [riferimento OCR.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)

### <a name="image-requirements"></a>Requisiti dell'immagine

L'API OCR funziona su immagini che soddisfano i seguenti requisiti:

* L'immagine deve essere presentata in formato JPEG, PNG, GIF o BMP.
* Le dimensioni dell'immagine di input devono essere comprese tra 50 x 50 e 4200 x 4200 pixel.
* Il testo nell'immagine può essere ruotato di qualsiasi multiplo di 90 gradi più un piccolo angolo non superiore a 40 gradi.

### <a name="limitations"></a>Limitazioni

Nelle fotografie dove il testo è predominante, si possono ottenere risultati falsi positivi da parole riconosciute parzialmente. In alcune fotografie, in particolare le foto senza testo, la precisione può variare a seconda del tipo di immagine.

## <a name="recognize-text-api"></a>API Di riconoscimento del testo

> [!NOTE]
> L'API Recognize Text è deprecata a favore dell'API Read. L'API di lettura ha funzionalità simili e viene aggiornata per gestire file PDF, TIFF e multi-pagina.

L'API Recognize Text è simile all'OCR, ma viene eseguita in modo asincrono e utilizza modelli di riconoscimento aggiornati. Per altre informazioni, consulta i documenti di [riferimento dell'API Recognize Text.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)

### <a name="image-requirements"></a>Requisiti dell'immagine

L'API Riconosci testo funziona con immagini che soddisfano i requisiti seguenti:The Recognize Text API works with images that meet the following requirements:

- L'immagine deve essere presentata in formato JPEG, PNG o BMP.
- Le dimensioni dell'immagine devono essere comprese tra 50 x 50 e 4200 x 4200 pixel.
- La dimensione del file dell'immagine deve essere inferiore a 4 megabyte (MB).

## <a name="limitations"></a>Limitazioni

L'accuratezza delle operazioni di riconoscimento del testo dipende dalla qualità delle immagini. I seguenti fattori possono causare una lettura imprecisa:

* Immagini sfocate.
* Testo in corsivo o scritto a mano.
* Stili di caratteri artistici.
* Testo di piccole dimensioni.
* Sfondi complessi, ombre o riflessi sul testo o distorsione prospettica.
* Lettere maiuscole sovradimensionate o mancanti all'inizio delle parole.
* Testo barrato, pedici o apici.

## <a name="next-steps"></a>Passaggi successivi

Seguire la guida introduttiva [Estrai testo (lettura)](./QuickStarts/CSharp-hand-text.md) per implementare il riconoscimento del testo in una semplice app in C.
