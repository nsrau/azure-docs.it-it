---
title: Conteggi caratteri-Translator
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra in che modo il traduttore di servizi cognitivi di Azure conta i caratteri per poter comprendere come inserisce il contenuto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7fad90cf47591b5ffab4232c2be3180138738f01
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992832"
---
# <a name="how-the-translator-counts-characters"></a>Conteggio dei caratteri del traduttore

Il traduttore conta ogni punto di codice Unicode del testo di input come carattere. Ogni traduzione di un testo in una lingua viene conteggiata come una traduzione separata, anche se la richiesta è stata effettuata in una singola chiamata API per la traduzione in più lingue. Lunghezza della risposta non ha importanza.

Gli elementi importanti sono i seguenti:

* Testo passato a Translator nel corpo della richiesta
   * `Text` quando si usano i metodi relativi alla traduzione, alla traslitterazione e alla ricerca nel dizionario
   * `Text` e `Translation` quando si usa il metodo relativo agli esempi di dizionari
* Tutti i markup: HTML, tag XML e così via all'interno del campo di testo del corpo della richiesta. La notazione JSON usata per compilare la richiesta, ad esempio "Text:", non viene conteggiata.
* Una singola lettera
* Punteggiatura
* Uno spazio, una tabulazione, il markup e qualsiasi tipo di carattere che rappresenti uno spazio vuoto
* Ogni elemento di codice definito in Unicode
* Una traduzione ripetuta, anche se lo stesso testo è stato tradotto in precedenza

Per gli script basati su degli ideogrammi, ad esempio il cinese e il Kanji giapponese, il servizio di conversione conta ancora il numero di punti di codice Unicode, un carattere per ogni ideogramma. Eccezione: i surrogati Unicode vengono conteggiati come due caratteri.

Il numero di richieste, parole, byte o frasi non è rilevante nel conteggio dei caratteri.

Le chiamate ai metodi Detect e BreakSentence non vengono conteggiate nel consumo dei caratteri. Tuttavia, è previsto che le chiamate ai metodi Detect e BreakSentence siano in proporzione ragionevole all'uso di altre funzioni che vengono conteggiate. Se il numero di chiamate Detect o BreakSentence effettuate supera di 100 volte il numero di altri metodi conteggiati, Microsoft si riserva il diritto di limitare l'uso dei metodi Detect e BreakSentence.

Per ulteriori informazioni sui conteggi di caratteri, consultare le [domande frequenti su Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
