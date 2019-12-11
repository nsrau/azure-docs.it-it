---
title: Elenchi di frasi-servizio vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come fornire al servizio di riconoscimento vocale un elenco di frasi utilizzando l'oggetto `PhraseListGrammar` per migliorare i risultati del riconoscimento vocale-testo.
services: cognitive-services
author: rhurey
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 2ceb53b50810aef501278710ae990c57fc45030c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971673"
---
# <a name="phrase-lists-for-speech-to-text"></a>Elenchi di frasi per la sintesi vocale

Fornendo al servizio di riconoscimento vocale un elenco di frasi, è possibile migliorare l'accuratezza del riconoscimento vocale. Gli elenchi di frasi vengono usati per identificare le frasi note nei dati audio, ad esempio il nome di una persona o una posizione specifica.

Ad esempio, se si dispone di un comando "Move to" e di una possibile destinazione "Ward" che potrebbe essere pronunciata, è possibile aggiungere una voce "Move to Ward". L'aggiunta di una frase aumenterà la probabilità che, quando viene rilevata l'audio, verrà riconosciuto "sposta in Ward" anziché "sposta verso".

È possibile aggiungere parole singole o frasi complete a un elenco di frasi. Durante il riconoscimento, una voce in un elenco di frasi viene usata se una corrispondenza esatta per l'intera frase viene inclusa nell'audio come frase separata. Se non viene trovata una corrispondenza esatta con la frase, il riconoscimento non è assistito.

>[!Note]
> Attualmente, gli elenchi di frasi supportano solo l'inglese per la sintesi vocale.

## <a name="how-to-use-phrase-lists"></a>Come usare gli elenchi di frasi

Negli esempi seguenti viene illustrato come compilare un elenco di frasi utilizzando l'oggetto `PhraseListGrammar`.

::: zone pivot="programming-language-csharp"

```cs
PhraseListGrammar phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Move to Ward");
phraseList.AddPhrase("Move to Bill");
phraseList.AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
auto phraselist = PhraseListGrammar::FromRecognizer(recognizer);
phraselist->AddPhrase("Move to Ward");
phraselist->AddPhrase("Move to Bill");
phraselist->AddPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
PhraseListGrammar phraseListGrammar = PhraseListGrammar.fromRecognizer(recognizer);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar = speechsdk.PhraseListGrammar.from_recognizer(reco)
phrase_list_grammar.addPhrase("Move to Ward")
phrase_list_grammar.addPhrase("Move to Bill")
phrase_list_grammar.addPhrase("Move to Ted")
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Il numero massimo di elenchi di frasi che il servizio di riconoscimento vocale utilizzerà per la corrispondenza del riconoscimento vocale è di 1024 frasi.

È anche possibile cancellare le frasi associate all'`PhraseListGrammar` chiamando Clear ().

::: zone pivot="programming-language-csharp"

```cs
phraseList.Clear();
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
phraselist->Clear();
```

::: zone-end

::: zone pivot="programming-language-java"

```Java
phraseListGrammar.clear();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
phrase_list_grammar.clear()
```

::: zone-end

::: zone pivot="programming-language-more"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Le modifiche apportate a un oggetto `PhraseListGrammar` diventano effettive al successivo riconoscimento o dopo una riconnessione al servizio di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento per l'SDK vocale](speech-sdk.md)
