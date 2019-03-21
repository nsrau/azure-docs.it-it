---
title: Cos’è un dizionario? - Custom Translator
titleSuffix: Azure Cognitive Services
description: Un dizionario è un documento allineato che specifica un elenco di espressioni o frasi (e le rispettive traduzioni) che Microsoft Translator deve sempre tradurre allo stesso modo. I dizionari sono detti talvolta anche glossari o termbase.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: bfefb1fe44959bc7e5186a0f14813f41256cf2d5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775442"
---
# <a name="what-is-a-dictionary"></a>Cos’è un dizionario?

Un dizionario è una coppia allineata di documenti che specifica un elenco di espressioni o frasi e le traduzioni corrispondenti. Utilizzare un dizionario per il training se si desidera che Microsoft Translator traduca sempre tutte le istanze delle espressioni o delle frasi con la traduzione indicata nel dizionario. I dizionari sono detti talvolta glossari o termbase. È possibile considerare il dizionario come un copia e incolla deciso di tutti i termini indicati.

I dizionari funzionano solo per i progetti in coppie di lingue per le quali è disponibile un sistema di traduzione automatica neurale (NMT) Microsoft completamente supportato. [Visualizzare l'elenco completo delle lingue](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Dizionario di espressioni
Quando si include un dizionario di espressioni per il training sul modello, qualsiasi parola o epressione elencata viene tradotta nel modo specificato. Il resto della frase viene tradotto normalmente. È possibile utilizzare un dizionario di espressioni per specificare le espressioni che non devono essere tradotte, inserendo la stessa espressione non tradotta nel file con origine e destinazione del dizionario.

## <a name="sentence-dictionary"></a>Dizionario di frasi
Il dizionario di frasi consente di specificare una traduzione di destinazione esatta di una frase di origine. La corrispondenza con una frase del dizionario si verifica se l'intera frase inviata corrisponde alla voce di origine del dizionario.  Se solo una parte della frase corrisponde, la corrispondenza con la voce non si verifica.  Quando viene rilevata una corrispondenza, verrà restituita la voce di destinazione del dizionario di frasi.

## <a name="dictionary-only-trainings"></a>Training con solo dizionario
È possibile eseguire il training su un modello utilizzando solo i dati del dizionario. Per farlo, selezionare solo il documento del dizionario (o più documenti di dizionario) da includere e toccare Crea modello. Poiché si tratta di un training con solo dizionario, non vi è alcun numero minimo di frasi di training necessarie. Il modello verrà completato in genere molto più velocemente rispetto a un training standard.  I modelli risultanti utilizzeranno i modelli di base Microsoft per la traduzione con l'aggiunta dei dizionari aggiunti.  Non si otterrà un report di test.

>[!Note]
>Custom Translator non allinea le frasi dei file di dizionario ed è quindi importante che sia presente un numero uguale di espressioni/frasi di origine e di destinazione nei documenti di dizionario e che queste siano esattamente allineate.

## <a name="recommendations"></a>Consigli

- I dizionari non sono un sostituto dei modelli di training contenenti dati di training.  I dizionari si limitano a trovare e sostituire parole o frasi.  Lasciare che il sistema apprenda frasi complete dal materiale di training in genere è consigliabile, rispetto all’uso di un dizionario.
- Il dizionario di frasi deve essere utilizzato solo se necessario. Quando viene sostituita un’espressione all'interno di una frase, il contesto all'interno di tale frase viene smarrito o limitato per tradurre il resto della frase. Il risultato è che mentre la frase o la parola all'interno della frase verrà tradotta in base al dizionario di espressioni, la qualità complessiva della traduzione della frase ne risentirà.
- Il dizionario di espressioni funziona bene per i nomi composti, ad esempio i nomi di prodotto ("Microsoft SQL Server"), i nomi propri ("Città di Amburgo") o le funzionalità dei prodotti ("tabella pivot"). Non funziona altrettanto bene per i verbi o gli aggettivi, perché questi sono in genere altamente declinati nelle lingue di origine e di destinazione. Evitare di usare le voci del dizionario di espressioni per espressioni diverse dai nomi composti.
- Quando si usa un dizionario, l'uso delle maiuscole e la punteggiatura nelle traduzioni rifletterà l’uso delle maiuscole e la punteggiatura forniti nel file di destinazione. L’uso delle maiuscole e la punteggiatura vengono ignorati quando si tenta di identificare le corrispondenze tra la frase inserita e la frasi di origine nel file di dizionario. Ad esempio, se si è eseguito il training di un sistema dall’inglese allo spagnolo usando un dizionario all’interno del quale era presente “City of Hamburg” nel file di origine e “Ciudad de hamburg” nel file di destinazione. Se si richiede la traduzione di una frase che include l’espressione “city of Hamburg”, l’espressione corrisponde alla voce “City of Hamburg” inclusa nel file di dizionario, pertanto viene mappata a “Ciudad de hamburg” nella traduzione finale.
- Se una parola è presente più volte in un file del dizionario, il sistema userà sempre l'ultima voce fornita. Il dizionario non deve contenere più traduzioni della stessa parola.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [Linee guida sui formati di documento](document-formats-naming-convention.md).
