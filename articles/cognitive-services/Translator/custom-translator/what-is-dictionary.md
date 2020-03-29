---
title: Cos’è un dizionario? - Custom Translator
titleSuffix: Azure Cognitive Services
description: Un dizionario è un documento allineato che specifica un elenco di espressioni o frasi (e le rispettive traduzioni) che Microsoft Translator deve sempre tradurre allo stesso modo. I dizionari sono detti talvolta anche glossari o termbase.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 36b449c4c4ca30eb658c9519ce8e870a4f1fab32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71970733"
---
# <a name="what-is-a-dictionary"></a>Cos’è un dizionario?

Un dizionario è una coppia allineata di documenti che specifica un elenco di espressioni o frasi e le traduzioni corrispondenti. Utilizzare un dizionario per il training se si desidera che Microsoft Translator traduca sempre tutte le istanze delle espressioni o delle frasi con la traduzione indicata nel dizionario. I dizionari sono detti talvolta glossari o termbase. È possibile considerare il dizionario come un copia e incolla deciso di tutti i termini indicati. Inoltre, il servizio Microsoft Custom Translator crea e utilizza i propri dizionari di uso generale per migliorare la qualità della sua traduzione. Tuttavia, un dizionario fornito dal cliente ha un precedente e verrà cercato prima per cercare parole o frasi.

I dizionari funzionano solo per i progetti in coppie di lingue che hanno un modello di rete neurale generale Microsoft completamente supportato dietro di loro. [Visualizza l'elenco completo delle lingue](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Dizionario di espressioni
Quando si include un dizionario di espressioni per il training sul modello, qualsiasi parola o epressione elencata viene tradotta nel modo specificato. Il resto della frase viene tradotto normalmente. È possibile utilizzare un dizionario di espressioni per specificare le espressioni che non devono essere tradotte, inserendo la stessa espressione non tradotta nel file con origine e destinazione del dizionario.

## <a name="sentence-dictionary"></a>Dizionario di frasi
Il dizionario di frasi consente di specificare una traduzione di destinazione esatta di una frase di origine. La corrispondenza con una frase del dizionario si verifica se l'intera frase inviata corrisponde alla voce di origine del dizionario.  Se solo una parte della frase corrisponde, la corrispondenza con la voce non si verifica.  Quando viene rilevata una corrispondenza, verrà restituita la voce di destinazione del dizionario di frasi.

## <a name="dictionary-only-trainings"></a>Training con solo dizionario
È possibile eseguire il training su un modello utilizzando solo i dati del dizionario. Per farlo, selezionare solo il documento del dizionario (o più documenti di dizionario) da includere e toccare Crea modello. Poiché si tratta di un training con solo dizionario, non vi è alcun numero minimo di frasi di training necessarie. Il modello verrà completato in genere molto più velocemente rispetto a un training standard.  I modelli risultanti utilizzeranno i modelli di base Microsoft per la traduzione con l'aggiunta dei dizionari aggiunti.  Non si otterrà un report di test.

>[!Note]
>Custom Translator non allinea le frasi del dizionario, pertanto è importante che sia presente un numero uguale di espressioni/frasi di origine e di destinazione nei documenti del dizionario e che siano esattamente allineate.

## <a name="recommendations"></a>Consigli

- I dizionari non sostituiscono il training di un modello usando i dati di training. Si consiglia di evitarli e lasciare che il sistema appaia dai dati di training. Tuttavia, quando le frasi o i sostantivi composti devono essere resi così come sono, utilizzare un dizionario.
- Il dizionario di frasi deve essere utilizzato solo se necessario. Quindi, essere consapevoli del fatto che quando una frase all'interno di una frase viene sostituita, il contesto all'interno di tale frase viene perso o limitato per tradurre il resto della frase. Il risultato è che mentre la frase o la parola all'interno della frase si tradurrà in base al dizionario fornito, la qualità complessiva di traduzione della frase spesso soffrirà.
- Il dizionario di espressioni funziona bene per i nomi composti, ad esempio i nomi di prodotto ("Microsoft SQL Server"), i nomi propri ("Città di Amburgo") o le funzionalità dei prodotti ("tabella pivot"). Non funziona altrettanto bene per i verbi o gli aggettivi, perché questi sono in genere altamente declinati nelle lingue di origine e di destinazione. La procedura consigliata consiste nell'evitare le voci del dizionario delle frasi per i sostantivi composti.
- Quando si utilizza un dizionario di frasi, le maiuscole e la punteggiatura sono importanti. Le voci del dizionario corrisponderanno solo alle parole e alle frasi nella frase di input che utilizzano esattamente la stessa maiuscola e punteggiatura specificate nel file del dizionario di origine. Anche le traduzioni rifletteranno le maiuscole e la punteggiatura fornite nel file del dizionario di destinazione. Ad esempio, se è stato eseguito il training di un sistema da inglese a spagnolo che utilizza un dizionario di frasi che specifica "US" nel file di origine e "EE. UU." nel file di destinazione. Quando richiedi la traduzione di una frase che include la parola "noi" (non maiuscola), questa NON corrisponderà al dizionario. Tuttavia, se richiedi la traduzione di una frase che contiene la parola "US" (maiuscolo), corrisponderebbe al dizionario e la traduzione conterrà "EE. UU." Si noti che le maiuscole e la punteggiatura nella traduzione possono essere diverse da quelle specificate nel file di destinazione del dizionario e possono essere diverse dalle maiuscole e dalla punteggiatura nell'origine. Segue le regole della lingua di destinazione.
- Se una parola è presente più volte in un file del dizionario, il sistema userà sempre l'ultima voce fornita. Di conseguenza, il dizionario non deve contenere più traduzioni della stessa parola.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [Linee guida sui formati di documento](document-formats-naming-convention.md).
