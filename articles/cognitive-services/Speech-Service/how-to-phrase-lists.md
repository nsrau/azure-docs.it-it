---
title: Elenchi di frasi-servizio vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come fornire al servizio riconoscimento vocale un elenco di frasi utilizzando l' `PhraseListGrammar` oggetto per migliorare i risultati del riconoscimento vocale-testo.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-one-nomore-no-go
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1e0b345eb4f1d6b3ab8ba917794b6878180ac558
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320949"
---
# <a name="phrase-lists-for-speech-to-text"></a>Elenchi di frasi per la sintesi vocale

Fornendo al servizio di riconoscimento vocale un elenco di frasi, è possibile migliorare l'accuratezza del riconoscimento vocale. Gli elenchi di frasi vengono usati per identificare frasi note nei dati audio, ad esempio il nome di una persona o una specifica località.

Ad esempio, se si dispone di un comando "Move to" e di una possibile destinazione "Ward" che potrebbe essere pronunciata, è possibile aggiungere una voce "Move to Ward". L'aggiunta di una frase aumenterà la probabilità che, quando viene rilevata l'audio, verrà riconosciuto "sposta in Ward" anziché "sposta verso".

All'elenco di frasi è possibile aggiungere singole parole o frasi complete. Durante il riconoscimento, una voce in un elenco di frasi viene usata se una corrispondenza esatta per l'intera frase viene inclusa nell'audio come frase separata. Se non si trova una corrispondenza esatta, il riconoscimento non è assistito.

>[!Note]
> Attualmente, gli elenchi di frasi supportano solo l'inglese per la sintesi vocale.

## <a name="how-to-use-phrase-lists"></a>Come usare gli elenchi di frasi

Negli esempi seguenti viene illustrato come compilare un elenco di frasi utilizzando l' `PhraseListGrammar` oggetto.

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

::: zone pivot="programming-language-javascript"

```JavaScript
var phraseListGrammar = SpeechSDK.PhraseListGrammar.fromRecognizer(reco);
phraseListGrammar.addPhrase("Move to Ward");
phraseListGrammar.addPhrase("Move to Bill");
phraseListGrammar.addPhrase("Move to Ted");
```

::: zone-end

>[!Note]
> Il numero massimo di elenchi di frasi che il servizio di riconoscimento vocale utilizzerà per la corrispondenza del riconoscimento vocale è di 1024 frasi.

È anche possibile cancellare le frasi associate a chiamando `PhraseListGrammar` Clear ().

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

::: zone pivot="programming-language-javascript"

```JavaScript
phraseListGrammar.clear();
```

::: zone-end

> [!NOTE]
> Le modifiche `PhraseListGrammar` apportate a un oggetto diventano effettive al successivo riconoscimento o dopo una riconnessione al servizio di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento per l'SDK vocale](speech-sdk.md)
