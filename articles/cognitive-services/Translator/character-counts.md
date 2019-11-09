---
title: Conteggio dei caratteri - API Traduzione testuale
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra il modo in cui i servizi cognitivi di Azure API Traduzione testuale conteggiano i caratteri in modo da comprendere il modo in cui inserisce il contenuto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888151"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Modalità di conteggio dei caratteri dell'API Traduzione testuale

L'API Traduzione testuale conteggia ogni elemento di codice Unicode del testo di input come un carattere. Ogni traduzione di un testo in una lingua viene conteggiata come una traduzione separata, anche se la richiesta è stata effettuata in una singola chiamata API per la traduzione in più lingue. Lunghezza della risposta non ha importanza.

Gli elementi importanti sono i seguenti:

* Il testo passato all'API Traduzione testuale nel corpo della richiesta
   * `Text` quando si usano i metodi relativi alla traduzione, alla traslitterazione e alla ricerca nel dizionario
   * `Text` e `Translation` quando si usa il metodo relativo agli esempi di dizionari
* Tutti i markup: HTML, tag XML e così via all'interno del campo di testo del corpo della richiesta. La notazione JSON usata per compilare la richiesta, ad esempio "Text:", non viene conteggiata.
* Una singola lettera
* Punteggiatura
* Uno spazio, una tabulazione, il markup e qualsiasi tipo di carattere che rappresenti uno spazio vuoto
* Ogni elemento di codice definito in Unicode
* Una traduzione ripetuta, anche se lo stesso testo è stato tradotto in precedenza

Per gli script basati su ideogrammi come quelli del cinese e del giapponese kanji, l'API Traduzione testuale conteggerà comunque il numero degli elementi di codice Unicode, un carattere per ideogramma. Eccezione: i surrogati Unicode vengono conteggiati come due caratteri.

Il numero di richieste, parole, byte o frasi non è rilevante nel conteggio dei caratteri.

Le chiamate ai metodi Detect e BreakSentence non vengono conteggiate nel consumo dei caratteri. Tuttavia, è previsto che le chiamate ai metodi Detect e BreakSentence siano in proporzione ragionevole all'uso di altre funzioni che vengono conteggiate. Se il numero di chiamate Detect o BreakSentence effettuate supera di 100 volte il numero di altri metodi conteggiati, Microsoft si riserva il diritto di limitare l'uso dei metodi Detect e BreakSentence.


Altre informazioni sui conteggi di caratteri sono reperibili in [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx) (Domande frequenti su Microsoft Translator).
