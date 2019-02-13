---
title: Conteggio dei caratteri - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Modalità di conteggio dei caratteri dell'API Traduzione testuale.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: f04e3ec4eedda7e260b205fea4856897689fb509
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700758"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Modalità di conteggio dei caratteri dell'API Traduzione testuale

L'API Traduzione testuale conteggia ogni elemento di codice Unicode del testo di input come un carattere. Ogni traduzione di un testo in una lingua viene conteggiata come una traduzione separata, anche se la richiesta è stata effettuata in una singola chiamata API per la traduzione in più lingue. La lunghezza della risposta non ha importanza.

Gli elementi importanti sono i seguenti:

* Il testo passato all'API Traduzione testuale nel corpo della richiesta
   * `Text` quando si usano i metodi relativi alla traduzione, alla traslitterazione e alla ricerca nel dizionario
   * `Text` e `Translation` quando si usa il metodo relativo agli esempi di dizionari
* Tutto il markup: HTML, tag XML e così via nel campo di testo del corpo della richiesta. La notazione JSON usata per compilare la richiesta, ad esempio "Text:", non viene conteggiata.
* Una singola lettera
* La punteggiatura
* Uno spazio, una tabulazione, il markup e qualsiasi tipo di carattere che rappresenti uno spazio vuoto
* Ogni elemento di codice definito in Unicode
* Una traduzione ripetuta, anche se lo stesso testo è stato tradotto in precedenza

Per gli script basati su ideogrammi come quelli del cinese e del giapponese kanji, l'API Traduzione testuale conteggerà comunque il numero degli elementi di codice Unicode, un carattere per ideogramma. Eccezione: I caratteri surrogati Unicode vengono contati come due caratteri.

Il numero di richieste, parole, byte o frasi non è rilevante nel conteggio dei caratteri. 

Le chiamate ai metodi Detect e BreakSentence non vengono conteggiate nel consumo dei caratteri. Tuttavia, è previsto che le chiamate ai metodi Detect e BreakSentence siano in proporzione ragionevole all'uso di altre funzioni che vengono conteggiate. Se il numero di chiamate Detect o BreakSentence effettuate supera di 100 volte il numero di altri metodi conteggiati, Microsoft si riserva il diritto di limitare l'uso dei metodi Detect e BreakSentence.


Altre informazioni sui conteggi di caratteri sono reperibili in [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx) (Domande frequenti su Microsoft Translator).
