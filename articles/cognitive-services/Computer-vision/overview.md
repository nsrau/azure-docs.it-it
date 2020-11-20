---
title: Informazioni sulla Visione artificiale
titleSuffix: Azure Cognitive Services
description: Il servizio Visione artificiale consente di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione di informazioni.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 11/13/2020
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
keywords: visione artificiale, applicazioni di visione artificiale, servizio visione artificiale
ms.openlocfilehash: aeaa10870b8b40b907da1d1660e77ebf0271c300
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629751"
---
# <a name="what-is-computer-vision"></a>Informazioni sulla Visione artificiale

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Il servizio Visione artificiale di Azure consente di accedere ad algoritmi avanzati che elaborano le immagini e restituiscono informazioni, in base alle caratteristiche visive a cui si è interessati. Ad esempio, Visione artificiale consente di determinare se un'immagine contiene contenuto per adulti, di trovare specifici marchi o oggetti o di individuare visi umani.

È possibile creare applicazioni di Visione artificiale tramite un [SDK di libreria client](./quickstarts-sdk/client-library.md) oppure chiamando direttamente l'[API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005). Questa pagina illustra su vasta scala le operazioni eseguibili con Visione artificiale.

## <a name="optical-character-recognition-ocr"></a>Riconoscimento ottico dei caratteri (OCR)

Visione artificiale include funzionalità di [riconoscimento ottico dei caratteri (OCR)](concept-recognizing-text.md). È possibile usare la nuova API di lettura per estrarre testo, scritto a mano o stampato, da immagini e documenti. Usa i modelli più recenti e funziona con testo su un'ampia gamma di superfici e sfondi, tra cui ricevute, poster, biglietti da visita, lettere e lavagne. Le due API di riconoscimento ottico dei caratteri supportano l'estrazione di testo stampato in [diverse lingue](./language-support.md). Per iniziare, seguire una [guida di avvio rapido](./quickstarts-sdk/client-library.md).

## <a name="computer-vision-for-digital-asset-management"></a>Visione artificiale per la gestione delle risorse digitali

Visione artificiale può ottimizzare molti scenari di gestione delle risorse digitali. La gestione delle risorse digitali è il processo aziendale per l'organizzazione, l'archiviazione e il recupero di risorse multimediali elaborate e per la gestione dei diritti e delle autorizzazioni digitali. Una società potrebbe ad esempio voler raggruppare e identificare le immagini in base a logo visibili, visi, oggetti, colori e così via. Oppure si potrebbe voler [generare didascalie per le immagini](./Tutorials/storage-lab-tutorial.md) automaticamente e allegare parole chiave in modo che siano disponibili per la ricerca. Per una soluzione di gestione delle risorse digitali all-in-one che usa Servizi cognitivi, Ricerca cognitiva di Azure e creazione di report intelligenti, vedere la [guida su Solution Accelerator per Knowledge Mining](https://github.com/Azure-Samples/azure-search-knowledge-mining) in GitHub. Per altri esempi di gestione delle risorse digitali, vedere il repository [Computer Vision Solution Templates](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) (Modelli per la soluzione Visione artificiale).

## <a name="analyze-images-for-insight"></a>Analizzare le immagini per ottenere informazioni dettagliate

È possibile analizzare le immagini per ricevere informazioni dettagliate sulle caratteristiche e gli aspetti visivi. Tutte le funzionalità nella tabella seguente vengono fornite dall'API [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) (Analisi dell'immagine). Per iniziare, seguire una [guida di avvio rapido](./quickstarts-sdk/client-library.md).


### <a name="tag-visual-features"></a>Assegnare tag agli elementi visivi

Identificare e assegnare tag agli elementi visivi di un'immagine in base a un set di migliaia di oggetti riconoscibili, esseri viventi, panorami e azioni. Quando i tag sono ambigui o non di conoscenza comune, la risposta dell'API fornisce suggerimenti per chiarire il contesto del tag. L'assegnazione di tag non è limitata al soggetto principale, ad esempio una persona in primo piano, ma include anche scenari (interni o esterni), arredamenti, strumenti, piante, animali, accessori, gadget e così via. [Assegnare tag agli elementi visivi](concept-tagging-images.md)

### <a name="detect-objects"></a>Rilevare oggetti

Il rilevamento di oggetti è simile all'assegnazione di tag, ma l'API restituisce le coordinate del rettangolo di selezione per ogni tag applicato. Se, ad esempio, un'immagine contiene un cane, gatto e una persona, l'operazione di rilevamento elencherà tali oggetti insieme alle relative coordinate nell'immagine. È possibile usare questa funzionalità per elaborare ulteriormente le relazioni tra gli oggetti in un'immagine. Questa funzionalità consente anche di sapere quando sono presenti più istanze dello stesso tag in un'immagine. [Rilevare oggetti](concept-object-detection.md)

### <a name="detect-brands"></a>Rilevare marchi

È possibile identificare i marchi commerciali in immagini o video da un database di migliaia di logo globali. È ad esempio possibile usare questa funzionalità per individuare i marchi più popolari sui social media o quelli più prevalenti nel posizionamento dei prodotti multimediali. [Rilevare marchi](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Classificare un'immagine

È possibile identificare e classificare un'intera immagine usando una [tassonomia di categorie](Category-Taxonomy.md) con gerarchie ereditarie di tipo padre/figlio. Le categorie possono essere usate singolarmente o con i nuovi modelli di assegnazione di tag.<br/>L'inglese è attualmente l'unica lingua supportata per l'assegnazione di tag e la classificazione di immagini. [Classificare un'immagine](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Descrivere un'immagine

È possibile generare una descrizione di un'intera immagine in un linguaggio leggibile dall'utente, con frasi complete. Gli algoritmi di Visione artificiale generano descrizioni diverse in base agli oggetti identificati nell'immagine. Tutte le descrizioni vengono valutate e per ognuna viene generato un punteggio di attendibilità. Viene quindi restituito un elenco dei punteggi di attendibilità in ordine decrescente. [Descrivere un'immagine](concept-describing-images.md)

### <a name="detect-faces"></a>Rilevare visi

È possibile rilevare visi in un'immagine e fornire informazioni su ogni viso rilevato. Visione artificiale restituisce le coordinate, un rettangolo, il sesso e l'età per ogni viso rilevato.<br/>Visione artificiale offre un subset delle funzionalità del servizio [Viso](/azure/cognitive-services/face/). È possibile usare il servizio Viso per analisi più dettagliate, come il riconoscimento facciale e il rilevamento della postura. [Rilevare visi](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Rilevare i tipi di immagine

È possibile rilevare le caratteristiche di un'immagine, ad esempio per determinare se un'immagine è un disegno a linee o per ottenere la probabilità che un'immagine sia ClipArt. [Rilevare i tipi di immagine](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Rilevare contenuti specifici di dominio

È possibile usare modelli di dominio per rilevare e identificare contenuti specifici del dominio in un'immagine, ad esempio celebrità e luoghi di interesse. Se ad esempio un'immagine contiene persone, Visione artificiale può usare un modello di dominio per le celebrità per determinare se le persone rilevate nell'immagine corrispondono a celebrità note. [Rilevare contenuti specifici del dominio](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Rilevare la combinazione di colori

È possibile analizzare l'utilizzo dei colori in un'immagine. Visione artificiale può determinare se un'immagine è in bianco e nero o a colori e nel caso delle immagini a colore può identificare il colore dominante e i colori in primo piano. [Rilevare la combinazione di colori](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Generare un'anteprima

È possibile analizzare i contenuti di un'immagine per generare un'anteprima appropriata per tale immagine. Visione artificiale genera prima di tutto un'anteprima di qualità elevata e quindi analizza gli oggetti inclusi nell'immagine per determinare l'*area di interesse*. L'immagine viene quindi ritagliata per soddisfare i requisiti dell'area di interesse. L'anteprima generata può essere visualizzata con proporzioni diverse da quelle dell'immagine originale, in base alle esigenze specifiche. [Generare un'anteprima](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>Ottenere l'area di interesse

Analizzare il contenuto di un'immagine per restituire le coordinate dell'*area di interesse*. Invece di ritagliare l'immagine e generare un'anteprima, Visione artificiale restituisce le coordinate del rettangolo di selezione dell'area, in modo che l'applicazione chiamante possa modificare l'immagine originale in base alle esigenze. [Ottenere l'area di interesse](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Moderare i contenuti nelle immagini

È possibile usare Visione artificiale per [rilevare i contenuti per adulti](concept-detecting-adult-content.md) in un'immagine e restituire punteggi di attendibilità per le diverse classificazioni. La soglia per contrassegnare i contenuti può essere impostata tramite un indicatore di scorrimento in base alle preferenze dell'utente.

## <a name="use-containers"></a>Usare i contenitori

Usare i contenitori Docker di Visione artificiale per usare il servizio in locale. Attualmente sono disponibili due contenitori:

* Il [contenitore di lettura di Visione artificiale](computer-vision-how-to-install-containers.md) consente di riconoscere il testo stampato e scritto a mano nelle immagini.

* Il [contenitore di analisi spaziale di Visione artificiale](spatial-analysis-container.md) consente di analizzare i video in streaming in tempo reale per comprendere le relazioni spaziali tra le persone e i loro movimenti negli ambienti fisici.

## <a name="image-requirements"></a>Requisiti dell'immagine

Visione artificiale può analizzare immagini che rispettano i requisiti seguenti:

- L'immagine deve essere presentata in formato JPEG, PNG, GIF o BMP
- Le dimensioni del file dell'immagine devono essere minori di 4 megabyte (MB)
- Le dimensioni dell'immagine devono essere superiori a 50 x 50 pixel
  - Per l'API di lettura (Read), le dimensioni dell'immagine devono essere comprese tra 50 x 50 e 10000 x 10000 pixel.

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Visione artificiale devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Seguire questa guida di avvio rapido nel linguaggio di sviluppo preferito per iniziare a usare Visione artificiale:

- [Avvio rapido: Libreria client di Visione artificiale](./quickstarts-sdk/client-library.md)
