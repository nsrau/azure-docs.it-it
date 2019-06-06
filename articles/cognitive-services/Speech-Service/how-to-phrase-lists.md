---
title: Elenchi di frase - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Informazioni su come specificare i servizi di riconoscimento vocale con un elenco di una frase usando il `PhraseListGrammar` oggetto per migliorare i risultati di riconoscimento vocale in testo.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 5/02/2019
ms.author: rhurey
ms.openlocfilehash: a3be5d28ebe394771a2d8b492f1f6a9c8a82fb9e
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66515315"
---
# <a name="phrase-lists-for-speech-to-text"></a>Elenchi di una frase per riconoscimento vocale

Grazie ai servizi di riconoscimento vocale con un elenco di una frase, è possibile migliorare l'accuratezza del riconoscimento vocale. Gli elenchi di una frase vengono utilizzati per identificare le frasi note in dati audio, ad esempio un nome di persona o un percorso specifico.

Ad esempio, se si dispone di un comando "Spostare" e una destinazione di "Ward" che può essere pronunciata, è possibile aggiungere una voce di "Spostare a Ward". Aggiunta di una frase aumentano le probabilità che quando l'audio viene riconosciuto che verranno riconosciuti anche "Spostarsi a Ward" anziché "Spostamento verso".

Singole parole o frasi complete possono essere aggiunti a un elenco di una frase. Durante il riconoscimento, una voce in un elenco di frase viene usata se una corrispondenza esatta è incluso nel file audio. La compilazione dell'esempio precedente, se l'elenco di una frase include "Sposta a Ward" e la frase acquisito è "Sposta lentamente verso", quindi il risultato di riconoscimento è "Sposta lentamente a Ward".

## <a name="how-to-use-phrase-lists"></a>Come utilizzare gli elenchi di frase

Gli esempi seguenti illustrano come compilare un elenco di una frase usando il `PhraseListGrammar` oggetto.

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
> Il numero massimo di una frase Elenca che verrà utilizzato dal servizio di riconoscimento vocale in modo che corrispondano vocale è 1024 frasi.

È anche possibile cancellare le frasi associate il `PhraseListGrammar` dal chiamante Clear ().

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
> Diventa un `PhraseListGrammar` oggetto hanno effetto sul riconoscimento successivo o in seguito una riconnessione per i servizi di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento di Speech SDK](speech-sdk.md)
