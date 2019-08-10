---
title: Inviare dati input penna all'API Riconoscimento input penna
titleSuffix: Azure Cognitive Services
description: Informazioni sulla chiamata all'API Riconoscimento input penna per diverse applicazioni
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: a5fe77ace5f745911bb9085dd6996a8d21fe265f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879046"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Inviare dati input penna all'API Riconoscimento input penna 

Con input penna digitale ci si riferisce alle tecnologie che abilitano le rappresentazioni digitali di input quali la scrittura manuale e i disegni. A tale scopo, si usa in genere un digitalizzatore che acquisisce i movimenti dei dispositivi di input, ad esempio uno stilo. Mentre i dispositivi continuano a permettere esperienze di input penna digitale avanzate, l'intelligenza artificiale e l'apprendimento automatico consentono il riconoscimento delle forme e del testo scritto in qualsiasi contesto. L'API Riconoscimento input penna consente di inviare tratti input penna e di ottenere informazioni dettagliate su di essi. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Confronto tra API Riconoscimento input penna e servizi OCR

L'API Riconoscimento input penna non usa il riconoscimento ottico dei caratteri (OCR). I servizi OCR elaborano i dati di pixel dalle immagini per fornire il riconoscimento della scrittura manuale e del testo, talvolta definito riconoscimento offline. L'API Riconoscimento input penna, invece, richiede i dati del tratto input penna digitale acquisiti durante l'uso del dispositivo di input. Una tale elaborazione dei dati input penna può produrre risultati di riconoscimento più accurati rispetto ai servizi OCR. 

## <a name="sending-ink-data"></a>Invio dei dati input penna

L'API Riconoscimento input penna richiede le coordinate X e Y che rappresentano tratti input penna creati da un dispositivo di input, dal momento in cui tocca la superficie di rilevamento fino a quando viene sollevato. I punti di ogni tratto devono essere una stringa di valori delimitati da virgole e in formato JSON, come l'esempio seguente. In più, ciascun tratto input penna deve contenere un ID univoco in ogni richiesta. Se l'ID si ripete entro la stessa richiesta, l'API restituirà un errore. Per ottenere risultati di riconoscimento più accurati occorre prevedere almeno otto cifre dopo il separatore decimale. Si presuppone che l'origine (0,0) del canvas sia l'angolo superiore sinistro del canvas di input penna.

> [!NOTE]
> L'esempio seguente non è in formato JSON valido. Su [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909) è possibile trovare una richiesta JSON completa di Riconoscimento input penna.
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Risposta di Riconoscimento input penna

L'API Riconoscimento input penna restituisce una risposta di analisi sugli oggetti che ha riconosciuto dal contenuto dell'input penna. La risposta contiene le unità di riconoscimento che descrivono le relazioni tra diversi tratti input penna. Per esempio, i tratti che creano forme distinte e separate saranno contenuti in unità diverse. Ogni unità contiene informazioni dettagliate sui relativi tratti input penna, tra cui l'oggetto riconosciuto, le sue coordinate e altri attributi del disegno.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Forme riconosciute dall'API Riconoscimento input penna

L'API Riconoscimento input penna può identificare le forme più comunemente usate per prendere appunti. L'immagine seguente mostra alcuni esempi di base. Per un elenco completo di forme e altri contenuti di input penna riconosciuti dall'API, vedere l'[articolo Informazioni di riferimento sulle API](https://go.microsoft.com/fwlink/?linkid=2089907). 

![Elenco di forme riconosciute dall'API Riconoscimento input penna](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Modelli di chiamata consigliati

È possibile chiamare l'API REST Riconoscimento input penna in modi diversi in base all'applicazione. 

### <a name="user-initiated-api-calls"></a>Chiamate API avviate dall'utente

Se si sta creando un'app che accetta l'input dell'utente (ad esempio, un'app di gestione degli appunti o di annotazione), è possibile assegnare ad essa il controllo di quando e quali input penna vengono inviati all'API Riconoscimento input penna. Questa funzionalità è particolarmente utile quando nel canvas sono presenti sia testo che forme e gli utenti vogliono eseguire azioni diverse per ciascuno di essi. Occorre prendere in considerazione l'aggiunta di funzionalità di selezione (come un lazo o altri strumenti di selezione geometrica) che consentano agli utenti di scegliere ciò che viene inviato all'API.  

### <a name="app-initiated-api-calls"></a>Chiamate API avviate dall'app

È anche possibile impostare l'app in modo da chiamare l'API Riconoscimento input penna dopo un timeout. Inviando regolarmente i tratti input penna correnti all'API, è possibile archiviare i risultati del riconoscimento mentre vengono creati, migliorando al contempo i tempi di risposta dell'API. Ad esempio, è possibile inviare una riga di testo scritto a mano all'API dopo aver rilevato che l'utente l'ha completata. 

Ottenendo i risultati di riconoscimento in anticipo si hanno informazioni sulle caratteristiche dei tratti input penna e sulla correlazione tra essi; ad esempio, quali tratti sono raggruppati in modo da formare la stessa parola, linea, elenco, paragrafo o forma. Queste informazioni possono migliorare le funzionalità di selezione input penna dell'app perché, ad esempio, permettono di selezionare gruppi di tratti contemporaneamente.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrare l'API Riconoscimento input penna con Windows Ink

[Windows Ink](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions) fornisce strumenti e tecnologie per abilitare esperienze di input penna digitale su una vasta gamma di dispositivi. È possibile combinare la piattaforma Windows Ink con l'API Riconoscimento input penna per creare applicazioni che visualizzano e interpretano i tratti input penna.

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sull'API Riconoscimento input penna](../overview.md)
* [Informazioni di riferimento sull'API Riconoscimento input penna](https://go.microsoft.com/fwlink/?linkid=2089907)

* Iniziare a inviare dati del tratto input penna digitale usando:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)