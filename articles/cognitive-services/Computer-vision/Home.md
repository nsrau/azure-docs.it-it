---
title: Informazioni sulla Visione artificiale - Visione artificiale
titleSuffix: Azure Cognitive Services
description: Il servizio Visione artificiale consente agli sviluppatori di accedere ad algoritmi avanzati per l'elaborazione delle immagini e la restituzione delle informazioni.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 11/04/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 703e750c0d2515484cd9f1d2cb046096dfd1800d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794967"
---
# <a name="what-is-computer-vision"></a>Informazioni sulla Visione artificiale

Il servizio Visione artificiale di Azure consente agli sviluppatori di accedere ad algoritmi avanzati che elaborano le immagini e restituiscono informazioni, in base alle caratteristiche visive a cui si è interessati. Visione artificiale può ad esempio determinare se un'immagine include contenuto per adulti o può individuare tutti i visi umani in un'immagine.

È possibile usare Visione artificiale nell'applicazione tramite un SDK nativo oppure chiamando direttamente l'API REST. Questa pagina illustra su vasta scala le operazioni eseguibili con Visione artificiale.

## <a name="computer-vision-for-digital-asset-management"></a>Visione artificiale per la gestione delle risorse digitali

Visione artificiale può ottimizzare molti scenari di gestione delle risorse digitali. La gestione delle risorse digitali è il processo aziendale per l'organizzazione, l'archiviazione e il recupero di risorse multimediali elaborate e per la gestione dei diritti e delle autorizzazioni digitali. Una società potrebbe ad esempio voler raggruppare e identificare le immagini in base a logo visibili, visi, oggetti, colori e così via. Oppure si potrebbe voler [generare didascalie per le immagini](./Tutorials/storage-lab-tutorial.md) automaticamente e allegare parole chiave in modo che siano disponibili per la ricerca. Per una soluzione di gestione delle risorse digitali all-in-one che usa Servizi cognitivi, Ricerca cognitiva di Azure e creazione di report intelligenti, vedere la [guida su Solution Accelerator per Knowledge Mining](https://github.com/Azure-Samples/azure-search-knowledge-mining) in GitHub. Per altri esempi di gestione delle risorse digitali, vedere il repository [Computer Vision Solution Templates](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) (Modelli per la soluzione Visione artificiale).

## <a name="analyze-images-for-insight"></a>Analizzare le immagini per ottenere informazioni dettagliate

È possibile analizzare le immagini per rilevare e fornire informazioni dettagliate sugli elementi visivi e sulle caratteristiche. Tutte le funzionalità nella tabella seguente vengono fornite dall'API [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (Analisi dell'immagine).

| Azione | DESCRIZIONE |
| ------ | ----------- |
|**[Assegnare tag agli elementi visivi](concept-tagging-images.md)**|Identificare e assegnare tag agli elementi visivi di un'immagine in base a un set di migliaia di oggetti riconoscibili, esseri viventi, panorami e azioni. Quando i tag sono ambigui o non di conoscenza comune, la risposta dell'API fornisce suggerimenti per chiarire il contesto del tag. L'assegnazione di tag non è limitata al soggetto principale, ad esempio una persona in primo piano, ma include anche scenari (interni o esterni), arredamenti, strumenti, piante, animali, accessori, gadget e così via.|
|**[Rilevare gli oggetti](concept-object-detection.md)**| Il rilevamento di oggetti è simile all'assegnazione di tag, ma l'API restituisce le coordinate del rettangolo di selezione per ogni tag applicato. Se, ad esempio, un'immagine contiene un cane, gatto e una persona, l'operazione di rilevamento elencherà tali oggetti insieme alle relative coordinate nell'immagine. È possibile usare questa funzionalità per elaborare ulteriormente le relazioni tra gli oggetti in un'immagine. Questa funzionalità consente anche di sapere quando sono presenti più istanze dello stesso tag in un'immagine.|
|**[Rilevare i marchi](concept-brand-detection.md)**|È possibile identificare i marchi commerciali in immagini o video da un database di migliaia di logo globali. È ad esempio possibile usare questa funzionalità per individuare i marchi più popolari sui social media o quelli più prevalenti nel posizionamento dei prodotti multimediali.|
|**[Classificare un'immagine](concept-categorizing-images.md)**|È possibile identificare e classificare un'intera immagine usando una [tassonomia di categorie](Category-Taxonomy.md) con gerarchie ereditarie di tipo padre/figlio. Le categorie possono essere usate singolarmente o con i nuovi modelli di assegnazione di tag.<br/>L'inglese è attualmente l'unica lingua supportata per l'assegnazione di tag e la classificazione di immagini.|
|**[Descrivere un'immagine](concept-describing-images.md)**|È possibile generare una descrizione di un'intera immagine in un linguaggio leggibile dall'utente, con frasi complete. Gli algoritmi di Visione artificiale generano descrizioni diverse in base agli oggetti identificati nell'immagine. Tutte le descrizioni vengono valutate e per ognuna viene generato un punteggio di attendibilità. Viene quindi restituito un elenco dei punteggi di attendibilità in ordine decrescente.|
|**[Rilevare visi](concept-detecting-faces.md)** |È possibile rilevare visi in un'immagine e fornire informazioni su ogni viso rilevato. Visione artificiale restituisce le coordinate, un rettangolo, il sesso e l'età per ogni viso rilevato.<br/>Visione artificiale offre un subset delle funzionalità del servizio [Viso](/azure/cognitive-services/face/). È possibile usare il servizio Viso per analisi più dettagliate, come il riconoscimento facciale e il rilevamento della postura.|
|**[Rilevare i tipi di immagine](concept-detecting-image-types.md)**|È possibile rilevare le caratteristiche di un'immagine, ad esempio per determinare se un'immagine è un disegno a linee o per ottenere la probabilità che un'immagine sia ClipArt.|
|**[Rilevare contenuti specifici del dominio](concept-detecting-domain-content.md)**|È possibile usare modelli di dominio per rilevare e identificare contenuti specifici del dominio in un'immagine, ad esempio celebrità e luoghi di interesse. Se ad esempio un'immagine contiene persone, Visione artificiale può usare un modello di dominio per le celebrità per determinare se le persone rilevate nell'immagine corrispondono a celebrità note.|
|**[Rilevare la combinazione di colori](concept-detecting-color-schemes.md)**|È possibile analizzare l'utilizzo dei colori in un'immagine. Visione artificiale può determinare se un'immagine è in bianco e nero o a colori e nel caso delle immagini a colore può identificare il colore dominante e i colori in primo piano.|
|**[Generare un'anteprima](concept-generating-thumbnails.md)**|È possibile analizzare i contenuti di un'immagine per generare un'anteprima appropriata per tale immagine. Visione artificiale genera prima di tutto un'anteprima di qualità elevata e quindi analizza gli oggetti inclusi nell'immagine per determinare l'*area di interesse*. L'immagine viene quindi ritagliata per soddisfare i requisiti dell'area di interesse. L'anteprima generata può essere visualizzata con proporzioni diverse da quelle dell'immagine originale, in base alle esigenze specifiche.|
|**[Ottenere l'area di interesse](concept-generating-thumbnails.md#area-of-interest)**|Analizzare il contenuto di un'immagine per restituire le coordinate dell'*area di interesse*. Invece di ritagliare l'immagine e generare un'anteprima, Visione artificiale restituisce le coordinate del rettangolo di selezione dell'area, in modo che l'applicazione chiamante possa modificare l'immagine originale in base alle esigenze.|

## <a name="extract-text-from-images"></a>Estrarre testo dalle immagini

È possibile usare l'[API di lettura](concept-recognizing-text.md#read-api) di Visione artificiale per estrarre il testo stampato e scritto a mano dalle immagini in un flusso di caratteri leggibili dal computer. L'API di lettura usa i modelli più recenti e legge il testo su una varietà di superfici e sfondi, ad esempio ricevute, poster, biglietti da visita, lettere e lavagne. Attualmente l'inglese è l'unica lingua supportata.

È anche possibile usare l'API di [riconoscimento ottico dei caratteri (OCR)](concept-recognizing-text.md#ocr-optical-character-recognition-api) per estrarre testo stampato in diverse lingue. Se necessario, la funzione OCR corregge la rotazione del testo riconosciuto e fornisce le coordinate del frame per ogni parola. La funzione OCR supporta 25 lingue e rileva automaticamente la lingua del testo riconosciuto.

## <a name="moderate-content-in-images"></a>Moderare i contenuti nelle immagini

È possibile usare Visione artificiale per [rilevare i contenuti per adulti](concept-detecting-adult-content.md) in un'immagine e restituire punteggi di attendibilità per le diverse classificazioni. La soglia per contrassegnare i contenuti può essere impostata tramite un indicatore di scorrimento in base alle preferenze dell'utente.

## <a name="use-containers"></a>Usare i contenitori

[Usare i contenitori di Visione artificiale](computer-vision-how-to-install-containers.md) per riconoscere il testo stampato e scritto a mano in locale installando un contenitore Docker standardizzato più vicino ai dati.

## <a name="image-requirements"></a>Requisiti dell'immagine

Visione artificiale può analizzare immagini che rispettano i requisiti seguenti:

- L'immagine deve essere presentata in formato JPEG, PNG, GIF o BMP
- Le dimensioni del file dell'immagine devono essere minori di 4 megabyte (MB)
- Le dimensioni dell'immagine devono essere superiori a 50 x 50 pixel
  - Per l'API di lettura (Read), le dimensioni dell'immagine devono essere comprese tra 50 x 50 e 10000 x 10000 pixel.

## <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i Servizi cognitivi, gli sviluppatori che usano il servizio Visione artificiale devono conoscere i criteri di Microsoft sui dati dei clienti. Per altre informazioni, vedere la [pagina sui Servizi cognitivi](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) nel Centro protezione di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per un'introduzione a Visione artificiale, seguire una guida introduttiva:

- [Guida introduttiva: SDK .NET per Visione artificiale](quickstarts-sdk/csharp-sdk.md)
- [Guida introduttiva: SDK Python per Visione artificiale](quickstarts-sdk/python-sdk.md)
- [Guida introduttiva: SDK Java per Visione artificiale](quickstarts-sdk/java-sdk.md)
