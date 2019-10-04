---
title: Elenchi di frasi-servizio vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come fornire ai servizi di riconoscimento vocale un elenco di frasi `PhraseListGrammar` usando l'oggetto per migliorare i risultati del riconoscimento vocale-testo.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
ms.openlocfilehash: 0e552d502184d1b537263c2c1f6b2a8562cdf791
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562784"
---
# <a name="phrase-lists-for-speech-to-text"></a>Elenchi di frasi per la sintesi vocale

Fornendo i servizi di sintesi vocale con un elenco di frasi, è possibile migliorare l'accuratezza del riconoscimento vocale. Gli elenchi di frasi vengono usati per identificare le frasi note nei dati audio, ad esempio il nome di una persona o una posizione specifica.

Ad esempio, se si dispone di un comando "Move to" e di una possibile destinazione "Ward" che potrebbe essere pronunciata, è possibile aggiungere una voce "Move to Ward". L'aggiunta di una frase aumenterà la probabilità che, quando viene rilevata l'audio, verrà riconosciuto "sposta in Ward" anziché "sposta verso".

È possibile aggiungere parole singole o frasi complete a un elenco di frasi. Durante il riconoscimento, viene utilizzata una voce in un elenco di frasi se nell'audio viene inclusa una corrispondenza esatta. Basandosi sull'esempio precedente, se l'elenco di frasi include "sposta in reparto" e la frase acquisita è "sposta lentamente verso il basso", il risultato del riconoscimento sarà "sposta in senso lento".

>[!Note]
> Attualmente, gli elenchi di frasi supportano solo l'inglese per la sintesi vocale.

## <a name="how-to-use-phrase-lists"></a>Come usare gli elenchi di frasi

Negli esempi seguenti viene illustrato come compilare un elenco di frasi utilizzando l' `PhraseListGrammar` oggetto.

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

>[!Note]
> Il numero massimo di elenchi di frasi che il servizio di riconoscimento vocale utilizzerà per la corrispondenza del riconoscimento vocale è di 1024 frasi.

È anche possibile cancellare le frasi associate a `PhraseListGrammar` chiamando Clear ().

```C++
phraselist->Clear();
```

```cs
phraseList.Clear();
```

```Python
phrase_list_grammar.clear()
```

```JavaScript
phraseListGrammar.clear();
```

```Java
phraseListGrammar.clear();
```

> [!NOTE]
> Le modifiche apportate a un `PhraseListGrammar` oggetto hanno effetto sul successivo riconoscimento o dopo una riconnessione ai servizi di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento per l'SDK vocale](speech-sdk.md)
