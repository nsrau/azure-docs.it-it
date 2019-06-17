---
title: Riconosci il testo stampato/scritta a mano, visione artificiale
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
ms.openlocfilehash: bcaa990cc2186a5f1eecdbbca91804c92370277c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66357191"
---
# <a name="recognize-printed-and-handwritten-text"></a>Riconoscere il testo stampato e scritto a mano

Visione artificiale fornisce numerosi servizi che rileva ed Estrai testo stampato o scritte a mano che viene visualizzato nelle immagini. Ciò è utile in un'ampia gamma di scenari, ad esempio gli Appunti, cartelle cliniche dei pazienti, sicurezza e settore bancario. I seguenti tre sezioni dettaglio tre diversi riconoscimento del testo API, ognuna ottimizzata per diversi casi d'uso.

## <a name="read-api"></a>Lettura API

L'API di lettura rileva il contenuto di testo in un'immagine con gli ultimi modelli di riconoscimento e converte il testo identificato in un flusso di caratteri leggibile dal computer. È ottimizzato per le immagini di grandi quantità di testo (ad esempio i documenti che sono stati analizzati firma digitale) e per le immagini con un'elevata quantità di disturbo. Determina quale modello di riconoscimento da utilizzare per ogni riga di testo, che supportano le immagini con testo stampato sia scritto a mano. L'API di lettura viene eseguita in modo asincrono perché documenti di grandi dimensioni possono richiedere alcuni minuti per restituire un risultato.

L'operazione di lettura gestisce i raggruppamenti di riga originale delle parole riconosciute nel relativo output. Ogni riga viene fornito con le coordinate di delimitazione, e ogni parola all'interno della riga ha anche i proprio coordinate. Se è stata riconosciuta una parola con confidenza basso in, tali informazioni vengono comunicate anche. Vedere le [documentazione di riferimento delle API di lettura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) per altre informazioni.

> [!NOTE]
> Questa funzionalità è disponibile solo per testo in lingua inglese.

### <a name="image-requirements"></a>Requisiti dell'immagine

L'API di lettura funziona con le immagini che soddisfano i requisiti seguenti:

- L'immagine deve essere presentato in formato TIFF, PDF, BMP, PNG o JPEG.
- Le dimensioni dell'immagine devono essere compreso tra 50 x 50 e 4200 x 4200 pixel. Pagine PDF devono essere 17 x 17 pollici o più piccole.
- Le dimensioni del file dell'immagine devono essere inferiore a 20 megabyte (MB).

### <a name="limitations"></a>Limitazioni

Se si usa una sottoscrizione di livello gratuito, l'API di lettura elaborerà solo le prime due pagine di un documento PDF o TIFF. Con una sottoscrizione a pagamento, verranno elaborati fino a 200 pagine. Si noti inoltre che l'API rileverà un massimo di 300 righe per pagina.

## <a name="ocr-optical-character-recognition-api"></a>OCR (riconoscimento ottico dei caratteri) API

API di riconoscimento ottico dei caratteri (OCR) di visione artificiale è simile all'API di lettura, ma lo esegue in modo sincrono e non è ottimizzato per documenti di grandi dimensioni. Usa un modello di riconoscimento precedente ma funziona con più lingue; visualizzare [supporto delle lingue](language-support.md#text-recognition) per un elenco completo delle lingue supportate.

Se necessario, la funzionalità OCR corregge la rotazione del testo riconosciuto, restituendo l'angolo di rotazione intorno all'asse orizzontale dell'immagine in gradi. OCR fornisce inoltre le coordinate della cornice di ogni parola, come illustrato nella figura seguente.

![Un'immagine da ruotare e il testo da leggere e delimitata](./Images/vision-overview-ocr.png)

Vedere le [documentazione di riferimento OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) per altre informazioni.

### <a name="image-requirements"></a>Requisiti dell'immagine

L'API di OCR funziona nelle immagini che soddisfano i requisiti seguenti:

* L'immagine deve essere presentato in formato JPEG, GIF, PNG o BMP.
* Le dimensioni dell'immagine di input devono essere compreso tra 50 x 50 e 4200 x 4200 pixel.
* Il testo nell'immagine può essere ruotato di qualsiasi multiplo di 90 gradi più un piccolo angolo non superiore a 40 gradi.

### <a name="limitations"></a>Limitazioni

Nelle fotografie dove il testo è predominante, si possono ottenere risultati falsi positivi da parole riconosciute parzialmente. In alcuni fotografie, in particolare le foto senza alcun testo, la precisione può variare a seconda del tipo di immagine.

## <a name="recognize-text-api"></a>Riconoscere l'API traduzione testuale

> [!NOTE]
> L'API di testo riconoscere stia diventando deprecato a favore dell'API di lettura. L'API di lettura ha funzionalità simili e viene aggiornato per la gestione di file a più pagine, TIFF e PDF.

L'API traduzione testuale riconoscere è simile a OCR, ma lo esegue in modo asincrono e utilizza i modelli di riconoscimento aggiornato. Vedere le [documentazione di riferimento API traduzione testuale riconoscere](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) per altre informazioni.

### <a name="image-requirements"></a>Requisiti dell'immagine

L'API di testo riconoscere funziona con le immagini che soddisfano i requisiti seguenti:

- L'immagine deve essere presentato in formato JPEG, PNG o BMP.
- Le dimensioni dell'immagine devono essere compreso tra 50 x 50 e 4200 x 4200 pixel.
- Le dimensioni del file dell'immagine devono essere inferiore a 4 megabyte (MB).

## <a name="limitations"></a>Limitazioni

L'accuratezza delle operazioni di riconoscimento testo dipende dalla qualità delle immagini. I fattori seguenti possono causare una lettura non accurata:

* Immagini sfocate.
* Testo in corsivo o scritto a mano.
* Stili di caratteri artistici.
* Testo di piccole dimensioni.
* Sfondi complessi, ombre o riflessi sul testo o distorsione prospettica.
* Lettere maiuscole dimensioni eccessive o mancante nella parte iniziale di parole.
* Testo barrato, pedici o apici.

## <a name="next-steps"></a>Passaggi successivi

Seguire le [estrarre testo stampato (OCR)](./quickstarts/csharp-print-text.md) Guida introduttiva per implementare il riconoscimento del testo in un semplice C# app.
