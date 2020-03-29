---
title: Elenchi di frasi - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come fornire al servizio di `PhraseListGrammar` riconoscimento vocale un elenco di frasi utilizzando l'oggetto per migliorare i risultati del riconoscimento vocale-testo.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f84ea6b2b0f1e8246e1d765e54f663cd01f29b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77560848"
---
# <a name="phrase-lists-for-speech-to-text"></a>Elenchi di frasi per la sintesi vocale

Fornendo al servizio di riconoscimento vocale un elenco di frasi, è possibile migliorare l'accuratezza del riconoscimento vocale. Gli elenchi di frasi vengono utilizzati per identificare frasi note nei dati audio, ad esempio il nome di una persona o una posizione specifica.

Ad esempio, se hai un comando "Sposta in" e una possibile destinazione di "Ward" che può essere pronunciata, puoi aggiungere una voce di "Sposta in rione". L'aggiunta di una frase aumenterà la probabilità che quando l'audio viene riconosciuto che "Sposta in rione" verrà riconosciuto invece di "Sposta verso".

È possibile aggiungere singole parole o frasi complete a un elenco di frasi. Durante il riconoscimento, una voce in un elenco di frasi viene utilizzata se una corrispondenza esatta per l'intera frase viene inclusa nell'audio come frase separata. Se non viene trovata una corrispondenza esatta con la frase, il riconoscimento non viene assistito.

>[!Note]
> Attualmente, elenchi di frasi supporta solo l'inglese per la sintesi vocale.

## <a name="how-to-use-phrase-lists"></a>Come utilizzare gli elenchi di frasi

Negli esempi riportati di seguito viene `PhraseListGrammar` illustrato come creare un elenco di frasi usando l'oggetto.

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
> Il numero massimo di elenchi di frasi che il servizio di riconoscimento vocale utilizzerà per trovare una corrispondenza con il riconoscimento vocale è 1024 frasi.

È inoltre possibile cancellare le `PhraseListGrammar` frasi associate a il chiamando clear().

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
> Le modifiche `PhraseListGrammar` apportate a un oggetto hanno effetto sul riconoscimento successivo o su una riconnessione al servizio di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento di Speech SDK](speech-sdk.md)
