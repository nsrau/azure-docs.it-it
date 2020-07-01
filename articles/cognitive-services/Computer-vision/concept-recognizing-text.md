---
title: Leggi il testo da immagini e documenti-Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al riconoscimento ottico dei caratteri (OCR) e al testo di immagini e documenti per la stampa e il testo scritto a mano usando il API Visione artificiale.
services: cognitive-services
author: msbbonsu
manager: netahw
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: t-bebon
ms.custom: seodec18
ms.openlocfilehash: 416a7edfdcd7e7915aa7886a8f53cf822b43fe93
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560808"
---
# <a name="read-text-from-images-and-documents"></a>Leggere testo da immagini e documenti

Visione artificiale include nuove funzionalità di riconoscimento ottico dei caratteri (OCR) basate sull'apprendimento avanzato che estraggono testo stampato o scritto a mano da immagini e documenti PDF. Visione artificiale estrae il testo da documenti analoghi (immagini, documenti digitalizzati) e documenti digitalizzati. È possibile estrarre il testo dalle immagini in modo selvaggio, ad esempio foto di targhe o contenitori di licenze con numeri di serie, nonché da documenti-fatture, fatture, rapporti finanziari, articoli e altro ancora. Questa funzionalità OCR è disponibile come parte del servizio gestito nel cloud o in locale (contenitori). Supporta inoltre reti virtuali ed endpoint privati per soddisfare le esigenze di conformità e privacy di livello aziendale.

## <a name="read-api"></a>API di lettura 

L' [API di lettura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) di visione artificiale è la tecnologia OCR più recente di Microsoft che estrae testo stampato (sette lingue), testo scritto a mano (solo in inglese), cifre e simboli di valuta da immagini e documenti PDF a più pagine. È ottimizzato per estrarre il testo dalle immagini in formato testo intenso e da documenti PDF a più pagine con lingue miste. Supporta il rilevamento di testo stampato e scritto a mano (solo in inglese) nella stessa immagine o documento. L'elenco completo delle lingue supportate è disponibile nella pagina [supporto del linguaggio per visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) .

### <a name="how-it-works"></a>Funzionamento

L' [API Read](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) supporta documenti di grandi quantità di testo fino a 2000 pagine e pertanto viene eseguita in modo asincrono. Il primo passaggio consiste nel chiamare l'operazione di lettura. L'operazione di lettura accetta un'immagine o un documento PDF come input e restituisce un ID operazione. 

Il secondo passaggio consiste nel chiamare l'operazione [Get results](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d9869604be85dee480c8750) . Questa operazione accetta l'ID operazione creato dall'operazione di lettura. Restituisce quindi il contenuto di testo estratto dall'immagine o dal documento sotto forma di JSON. La risposta JSON gestisce i raggruppamenti di righe originali delle parole riconosciute. Sono incluse le righe di testo estratte e le relative coordinate del rettangolo di delimitazione. Ogni riga di testo include tutte le parole estratte con le relative coordinate e i punteggi di confidenza.

Se necessario, Read corregge la rotazione della pagina riconosciuta restituendo l'offset rotazionale in gradi sull'asse dell'immagine orizzontale, come illustrato nella figura seguente.

![Un'immagine da ruotare e il testo da leggere e delineare](./Images/vision-overview-ocr-read.png)

Seguire la Guida introduttiva alla [stampa e al testo scritto a mano](./QuickStarts/CSharp-hand-text.md) per implementare OCR con C# e l'API REST.

### <a name="input-requirements"></a>Requisiti di input

L'API Read accetta gli input seguenti:
* Formati di file supportati: JPEG, PNG, BMP, PDF e TIFF
* Per PDF e TIFF, vengono elaborate fino a 2000 pagine. Per i sottoscrittori del livello gratuito, vengono elaborate solo le prime due pagine.
* Le dimensioni del file devono essere inferiori a 50 MB e dimensioni di almeno 50 x 50 pixel e al massimo 10000 x 10000 pixel.
* Le dimensioni PDF devono essere al massimo 17 x 17 pollici, corrispondenti a dimensioni della carta note o a3 e inferiori.


### <a name="text-from-images"></a>Testo dalle immagini

L'output dell'API Read seguente mostra le righe di testo estratte e le parole da un'immagine con testo in diversi angoli, colori e tipi di carattere

![Un'immagine da ruotare e il testo da leggere e delineare](./Images/text-from-images-example.png)

### <a name="text-from-documents"></a>Testo da documenti

Oltre alle immagini, l'API Read accetta un documento PDF come input.

![Un'immagine da ruotare e il testo da leggere e delineare](./Images/text-from-documents-example.png)


### <a name="handwritten-text-in-english"></a>Testo scritto a mano in inglese

Attualmente, l'operazione di lettura supporta l'estrazione del testo scritto a mano esclusivamente in inglese.

![Un'immagine da ruotare e il testo da leggere e delineare](./Images/handwritten-example.png)

### <a name="printed-text-in-supported-languages"></a>Testo stampato nelle lingue supportate

L'API Read supporta l'estrazione di testo stampato in lingua inglese, spagnolo, tedesco, francese, italiano, portoghese e olandese. Se lo scenario richiede il supporto di più lingue, vedere Cenni preliminari sull'API OCR in questo documento. Vedere l'elenco di tutte le [lingue supportate](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition)

![Un'immagine da ruotare e il testo da leggere e delineare](./Images/supported-languages-example.png)

### <a name="mixed-languages-support"></a>Supporto di linguaggi misti

L'API Read supporta immagini e documenti con più linguaggi, comunemente noti come documenti in linguaggio misto. Questa operazione viene eseguita classificando ogni riga di testo nel documento nella lingua rilevata prima di estrarre il contenuto di testo.

![Un'immagine da ruotare e il testo da leggere e delineare](./Images/mixed-language-example.png)

### <a name="data-privacy-and-security"></a>Sicurezza e privacy dei dati

Come con tutti i servizi cognitivi, gli sviluppatori che usano il servizio di lettura devono conoscere i criteri Microsoft sui dati dei clienti. Per altre informazioni, vedere la pagina Servizi cognitivi nel [Centro protezione Microsoft](https://www.microsoft.com/en-us/trust-center/product-overview) .

### <a name="deploy-on-premises"></a>Deploy on-premises (Distribuire in locale)

Read è disponibile anche come contenitore Docker (anteprima) per consentire la distribuzione delle nuove funzionalità OCR nell'ambiente in uso. I contenitori sono ottimi per requisiti specifici di sicurezza e governance dei dati. Vedere [come installare ed eseguire i contenitori di lettura.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)


## <a name="ocr-api"></a>API OCR

L' [API OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) usa un modello di riconoscimento precedente, supporta solo le immagini e viene eseguito in modo sincrono, restituendo immediatamente il testo rilevato. Supporta [più lingue](https://docs.microsoft.com/azure/cognitive-services/computer-vision/language-support#text-recognition) rispetto all'API Read.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull' [API REST di lettura 3,0](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005).
- Seguire la Guida introduttiva all' [estrazione del testo](./QuickStarts/CSharp-hand-text.md) per implementare OCR con C#, Java, JavaScript o Python insieme all'API REST.
