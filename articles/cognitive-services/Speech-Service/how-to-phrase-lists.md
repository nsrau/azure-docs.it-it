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
ms.openlocfilehash: 052e02ef562da0637b6b5b9683120f0c397dbfd5
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805876"
---
# <a name="phrase-lists-for-speech-to-text"></a>Elenchi di frasi per la sintesi vocale

Fornendo al servizio di riconoscimento vocale un elenco di frasi, è possibile migliorare l'accuratezza del riconoscimento vocale. Gli elenchi di frasi vengono usati per identificare le frasi note nei dati audio, ad esempio il nome di una persona o una posizione specifica.

Ad esempio, se si dispone di un comando "Move to" e di una possibile destinazione "Ward" che potrebbe essere pronunciata, è possibile aggiungere una voce "Move to Ward". L'aggiunta di una frase aumenterà la probabilità che, quando viene rilevata l'audio, verrà riconosciuto "sposta in Ward" anziché "sposta verso".

È possibile aggiungere parole singole o frasi complete a un elenco di frasi. Durante il riconoscimento, viene utilizzata una voce in un elenco di frasi se nell'audio viene inclusa una corrispondenza esatta. Basandosi sull'esempio precedente, se l'elenco di frasi include "sposta in Ward" e l'audio acquisito sembra abbastanza simile sia a "sposta verso" sia a "sposta in reparto", il risultato del riconoscimento verrà probabilmente riconosciuto come "sposta al reparto lento".

>[!Note]
> Attualmente, gli elenchi di frasi supportano solo l'inglese per la sintesi vocale.

## <a name="how-to-use-phrase-lists"></a>Come usare gli elenchi di frasi

Negli esempi seguenti viene illustrato come compilare un elenco di frasi utilizzando l'oggetto `PhraseListGrammar`.

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

È anche possibile cancellare le frasi associate all'`PhraseListGrammar` chiamando Clear ().

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
> Le modifiche apportate a un oggetto `PhraseListGrammar` diventano effettive al successivo riconoscimento o dopo una riconnessione al servizio di riconoscimento vocale.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di riferimento per l'SDK vocale](speech-sdk.md)
