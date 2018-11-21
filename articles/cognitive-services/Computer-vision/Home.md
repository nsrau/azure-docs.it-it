---
title: Informazioni sull'API Visione artificiale
titlesuffix: Azure Cognitive Services
description: Il servizio Visione artificiale consente agli sviluppatori di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione delle informazioni.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: overview
ms.date: 08/22/2018
ms.author: pafarley
ms.openlocfilehash: 8f18b01409680c07b942087b75b1618c75973391
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634329"
---
# <a name="what-is-computer-vision"></a>Informazioni sulla Visione artificiale

Il servizio Visione artificiale basato sul cloud consente agli sviluppatori di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione delle informazioni. Visione artificiale usa i formati di immagine più diffusi, ad esempio JPEG e PNG. Per analizzare un'immagine, è possibile caricare un'immagine o specificare un URL di immagine. Gli algoritmi di Visione artificiale consentono di analizzare il contenuto di un'immagine in modi diversi, in base alle funzionalità visive rilevanti. Visione artificiale può ad esempio determinare se un'immagine include contenuto per adulti o audace o può individuare tutti i visi in un'immagine.

È possibile usare Visione artificiale nell'applicazione usando le [librerie client](quickstarts-sdk/csharp-analyze-sdk.md) per richiamare il servizio o chiamando direttamente l'[API REST](vision-api-how-to-topics/howtocallvisionapi.md) per:

- [Analizzare le immagini per ottenere informazioni dettagliate](#analyzing-images-for-insight)
- [Estrarre testo dalle immagini](#extracting-text-from-images)
- [Moderare i contenuti nelle immagini](#moderating-content-in-images)

## <a name="analyzing-images-for-insight"></a>Analisi di immagini per ottenere informazioni dettagliate

È possibile analizzare le immagini usando Visione artificiale per rilevare e fornire informazioni dettagliate sugli elementi visivi e sulle caratteristiche delle immagini. È possibile caricare i contenuti di un'immagine per analizzare le immagini locali oppure specificare l'URL di un'immagine per analizzare immagini remote.

Visione artificiale può eseguire le azioni seguenti durante l'analisi di un'immagine:

| Azione | Descrizione |
| ------ | ----------- |
|**[Assegnare tag agli elementi visivi](concept-tagging-images.md)**|È possibile identificare e assegnare tag agli elementi visivi di un'immagine in base a più di 2.000 oggetti riconoscibili, esseri viventi, panorami e azioni. Quando i tag sono ambigui o di conoscenza non comune, la risposta fornisce suggerimenti per chiarire il significato del tag nel contesto di un'impostazione nota. L'assegnazione di tag non è limitata al soggetto principale, ad esempio una persona in primo piano, ma include anche scenari (interni o esterni), arredamenti, strumenti, piante, animali, accessori, gadget e così via.|
|**[Classificare un'immagine](concept-categorizing-images.md)**|È possibile identificare e classificare un'intera immagine usando una [tassonomia di categorie](Category-Taxonomy.md) con gerarchie ereditarie di tipo padre/figlio. Le categorie possono essere usate singolarmente o con i nuovi modelli di assegnazione di tag.<br/>L'inglese è attualmente l'unica lingua supportata per l'assegnazione di tag e la classificazione di immagini.|
|**[Descrivere un'immagine](concept-describing-images.md)**|È possibile generare una descrizione di un'intera immagine in un linguaggio leggibile dall'utente, con frasi complete. Gli algoritmi di Visione artificiale generano descrizioni diverse in base agli oggetti identificati nell'immagine. Tutte le descrizioni vengono valutate e per ognuna viene generato un punteggio di attendibilità. Viene quindi restituito un elenco dei punteggi di attendibilità in ordine decrescente.|
|**[Rilevare visi](concept-detecting-faces.md)** |È possibile rilevare visi in un'immagine e fornire informazioni su ogni viso rilevato. Visione artificiale restituisce le coordinate, un rettangolo, il sesso e l'età per ogni viso rilevato.<br/>Visione artificiale fornisce un sottoinsieme delle funzionalità disponibili in [Viso](/azure/cognitive-services/face/) ed è possibile usare il servizio Viso per ottenere analisi più dettagliate, ad esempio l'identificazione dei visi e il rilevamento della posa.|
|**[Rilevare i tipi di immagine](concept-detecting-image-types.md)**|È possibile rilevare le caratteristiche di un'immagine, ad esempio per determinare se un'immagine è un disegno a linee o per ottenere la probabilità che un'immagine sia ClipArt.|
|**[Rilevare contenuti specifici del dominio](concept-detecting-domain-content.md)**|È possibile usare modelli di dominio per rilevare e identificare contenuti specifici del dominio in un'immagine, ad esempio celebrità e luoghi di interesse. Se ad esempio un'immagine contiene persone, Visione artificiale può usare un modello di dominio per le celebrità incluso nel servizio per determinare se le persone rilevate nell'immagine corrispondono a celebrità note.|
|**[Rilevare la combinazione di colori](concept-detecting-color-schemes.md)**|È possibile analizzare l'utilizzo dei colori in un'immagine. Visione artificiale può determinare se un'immagine è in bianco e nero o a colori e nel caso delle immagini a colore può identificare il colore dominante e i colori in primo piano.|
|**[Generare un'anteprima](concept-generating-thumbnails.md)**|È possibile analizzare i contenuti di un'immagine per generare un'anteprima appropriata per tale immagine. Visione artificiale genera prima di tutto un'anteprima di qualità elevata e quindi analizza gli oggetti inclusi nell'immagine per determinare l'*area di interesse*. Visione artificiale ritaglia quindi l'immagine per soddisfare i requisiti dell'area di interesse. L'anteprima generata può essere visualizzata con proporzioni diverse da quelle dell'immagine originale, in base alle esigenze specifiche.|

## <a name="extracting-text-from-images"></a>Estrazione di testo dalle immagini

È possibile usare Visione artificiale per [estrarre testo mediante OCR](concept-extracting-text-ocr.md) da un'immagine in un flusso di caratteri leggibile dal computer. Se necessario, la funzione OCR corregge la rotazione del testo riconosciuto, espresso in gradi, intorno all'asse orizzontale dell'immagine e fornisce le coordinate del frame per ogni parola. OCR supporta 25 lingue e rileva automaticamente la lingua del testo estratto.

È anche possibile [riconoscere testo stampato e scritto a mano](concept-recognizing-text.md) in un'immagine. Visione artificiale può rilevare ed estrarre testo stampato e scritto a mano da immagini di diversi oggetti con superfici e sfondi diversi, ad esempio ricette, poster, biglietti da visita, lettere e lavagne. La funzionalità di riconoscimento di testo stampato e scritto a mano è attualmente disponibile in anteprima e l'inglese è l'unica lingua supportata.  

## <a name="moderating-content-in-images"></a>Moderazione di contenuti nelle immagini

È possibile usare Visione artificiale per [rilevare contenuti per adulti e audaci](concept-detecting-adult-content.md) in un'immagine, determinando la probabilità che l'immagine contenga contenuti per adulti o audaci e generando un punteggio di attendibilità per entrambi. Il filtro per il rilevamento del contenuto audace e per adulti può essere impostato tramite un indicatore di scorrimento in base alle preferenze dell'utente.

## <a name="using-containers"></a>Uso dei contenitori

[Usare i contenitori di Visione artificiale](computer-vision-how-to-install-containers.md) per riconoscere testo stampato e scritto a mano in locale installando un contenitore Docker standardizzato più vicino ai dati.

## <a name="image-requirements"></a>Requisiti dell'immagine

Visione artificiale può analizzare immagini che rispettano i requisiti seguenti:

- L'immagine deve essere presentata in formato JPEG, PNG, GIF o BMP
- Le dimensioni del file dell'immagine devono essere minori di 4 megabyte (MB)
- Le dimensioni dell'immagine devono essere superiori a 50 x 50 pixel  
  Per l'OCR, le dimensioni dell'immagine devono essere comprese tra 50 x 50 e 4200 x 4200 pixel

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione a Visione artificiale, vedere le guide introduttive seguenti:

- [Analizzare un'immagine](quickstarts-sdk/csharp-analyze-sdk.md)
- [Estrarre testo scritto a mano](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Generare un'anteprima](quickstarts-sdk/csharp-thumb-sdk.md)
