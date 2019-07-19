---
title: Riconosci testo stampato/scritto a mano Visione artificiale
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
ms.openlocfilehash: cfbbd0b353699c4b04ede07df0450e66bd59612f
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311955"
---
# <a name="recognize-printed-and-handwritten-text"></a>Riconoscere il testo stampato e scritto a mano

Visione artificiale fornisce una serie di servizi che rilevano ed estraggono testo stampato o scritto a mano visualizzato in immagini. Questa operazione è utile in diversi scenari, ad esempio la presa di nota, le registrazioni mediche, la sicurezza e le operazioni bancarie. Nelle tre sezioni seguenti vengono illustrate tre diverse API di riconoscimento del testo, ciascuna ottimizzata per diversi casi di utilizzo.

## <a name="read-api"></a>Leggi API

L'API Read rileva il contenuto di testo in un'immagine usando i modelli di riconoscimento più recenti e converte il testo identificato in un flusso di caratteri leggibile dal computer. È ottimizzato per immagini con un numero elevato di testo, ad esempio documenti digitalizzati digitalmente, e per immagini con molto rumore visivo. Determinerà il modello di riconoscimento da usare per ogni riga di testo, supportando immagini con testo stampato e scritto a mano. L'API Read viene eseguita in modo asincrono perché i documenti di dimensioni maggiori possono richiedere diversi minuti per restituire un risultato.

L'operazione di lettura mantiene i raggruppamenti di righe originali delle parole riconosciute nell'output. Ogni riga include le coordinate del rettangolo di delimitazione e ogni parola all'interno della riga presenta anche le proprie coordinate. Se una parola è stata riconosciuta con una bassa confidenza, vengono trasmesse anche le informazioni. Per altre informazioni, vedere la [documentazione di riferimento](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) per le API.

> [!NOTE]
> Questa funzionalità è disponibile solo per testo in lingua inglese.

### <a name="image-requirements"></a>Requisiti dell'immagine

L'API Read funziona con le immagini che soddisfano i requisiti seguenti:

- L'immagine deve essere visualizzata in formato JPEG, PNG, BMP, PDF o TIFF.
- Le dimensioni dell'immagine devono essere comprese tra 50 x 50 e 10000 x 10000 pixel. Le pagine PDF devono essere di 17 x 17 centimetri o più piccole.
- Le dimensioni del file dell'immagine devono essere inferiori a 20 megabyte (MB).

### <a name="limitations"></a>Limitazioni

Se si usa una sottoscrizione di livello gratuito, l'API di lettura elabora solo le prime due pagine di un documento PDF o TIFF. Con una sottoscrizione a pagamento, verrà elaborato un massimo di 200 pagine. Si noti inoltre che l'API rileva un massimo di 300 righe per pagina.

## <a name="ocr-optical-character-recognition-api"></a>API OCR (Optical Character Recognition)

L'API di riconoscimento ottico dei caratteri (OCR) di Visione artificiale è simile all'API Read, ma viene eseguita in modo sincrono e non è ottimizzata per documenti di grandi dimensioni. Usa un modello di riconoscimento precedente ma funziona con più linguaggi; per un elenco completo delle lingue supportate, vedere [supporto della lingua](language-support.md#text-recognition) .

Se necessario, la funzionalità OCR corregge la rotazione del testo riconosciuto, restituendo l'angolo di rotazione intorno all'asse orizzontale dell'immagine in gradi. OCR fornisce anche le coordinate del frame di ogni parola, come illustrato nella figura seguente.

![Un'immagine da ruotare e il testo da leggere e delineare](./Images/vision-overview-ocr.png)

Per altre informazioni, vedere la [documentazione di riferimento per OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) .

### <a name="image-requirements"></a>Requisiti dell'immagine

L'API OCR funziona su immagini che soddisfano i requisiti seguenti:

* L'immagine deve essere visualizzata in formato JPEG, PNG, GIF o BMP.
* Le dimensioni dell'immagine di input devono essere comprese tra 50 x 50 e 4200 x 4200 pixel.
* Il testo nell'immagine può essere ruotato di qualsiasi multiplo di 90 gradi più un piccolo angolo non superiore a 40 gradi.

### <a name="limitations"></a>Limitazioni

Nelle fotografie dove il testo è predominante, si possono ottenere risultati falsi positivi da parole riconosciute parzialmente. In alcune fotografie, in particolare foto senza testo, la precisione può variare a seconda del tipo di immagine.

## <a name="recognize-text-api"></a>API riconoscimento del testo

> [!NOTE]
> L'API riconoscimento del testo viene deprecata a favore dell'API Read. L'API Read ha funzionalità simili e viene aggiornata per gestire i file PDF, TIFF e a più pagine.

L'API riconoscimento del testo è simile a OCR, ma viene eseguita in modo asincrono e usa modelli di riconoscimento aggiornati. Per altre informazioni, vedere la documentazione di riferimento per l' [API riconoscimento del testo](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) .

### <a name="image-requirements"></a>Requisiti dell'immagine

L'API riconoscimento del testo funziona con le immagini che soddisfano i requisiti seguenti:

- L'immagine deve essere visualizzata in formato JPEG, PNG o BMP.
- Le dimensioni dell'immagine devono essere comprese tra 50 x 50 e 4200 x 4200 pixel.
- Le dimensioni del file dell'immagine devono essere inferiori a 4 megabyte (MB).

## <a name="limitations"></a>Limitazioni

L'accuratezza delle operazioni di riconoscimento del testo dipende dalla qualità delle immagini. I seguenti fattori possono causare una lettura non accurata:

* Immagini sfocate.
* Testo in corsivo o scritto a mano.
* Stili di caratteri artistici.
* Testo di piccole dimensioni.
* Sfondi complessi, ombre o riflessi sul testo o distorsione prospettica.
* Lettere maiuscole sovradimensionate o mancanti all'inizio delle parole.
* Testo barrato, pedici o apici.

## <a name="next-steps"></a>Passaggi successivi

Seguire la Guida introduttiva all' [OCR (Estrai testo stampato)](./quickstarts/csharp-print-text.md) per implementare il C# riconoscimento del testo in una semplice app.
