---
title: Intelligenza artificiale
description: LUIS USA l'intelligenza artificiale per fornire informazioni sulla lingua ai dati, in base allo schema definito.
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 4f145585e097a3cf6a2338dbab879f6fce07f71d
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802653"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Intelligenza artificiale in Language Understanding (LUIS)

LUIS USA l'intelligenza artificiale per fornire la comprensione del linguaggio naturale (NLU) ai dati, in base allo schema definito.

## <a name="natural-language-processing"></a>Elaborazione del linguaggio naturale

Natural Language Understanding (NLU) è un argomento specifico di elaborazione del linguaggio naturale (PNL).

L'elaborazione del linguaggio naturale è un concetto più ampio che gestisce qualsiasi forma di elaborazione di dati testuali, incluse le seguenti:

* Tokenizzazione
* Parte dell'assegnazione di tag (POS)
* Segmentazione
* Analisi morfologica
* Somiglianza semantica
* Discorso
* Traduzione

## <a name="natural-language-processing-in-luis"></a>Elaborazione del linguaggio naturale in LUIS

L'elaborazione del linguaggio naturale è disponibile per l'app LUIS nei modi seguenti:
* Luis ( [Natural Language Understanding](#natural-language-understanding) )
* Aspetti PNL configurabili in LUIS:
    * [Tokenizzazione](luis-language-support.md#tokenization)
    * Morfologia tramite segni diacritici, punteggiatura e [Impostazioni API](luis-reference-application-settings.md) form di Word
* Pre-o post-elaborazione dell'espressione di query fornita da altri [Servizi cognitivi](../Welcome.md) , ad esempio:
    * [Traduzione](../translator/translator-info-overview.md)

## <a name="natural-language-understanding"></a>Comprensione del linguaggio naturale

NLU è la possibilità di _trasformare_ un'istruzione linguistica in una rappresentazione che consente di comprendere naturalmente gli utenti. La comprensione del linguaggio naturale rimane un problema molto complesso e viene definito come un problema di _intelligenza artificiale_ .

LUIS ha lo scopo di concentrarsi sull'intenzione e sull'estrazione, inclusa la possibilità di identificare:
* Elementi desiderati dall'utente
* Cosa sta parlando.

LUIS ha poca o nessuna conoscenza degli aspetti più ampi della _PNL_ , ad esempio la somiglianza semantica, senza l'identificazione esplicita degli esempi. Ad esempio, i token seguenti (parole) sono tre elementi diversi fino a quando non vengono usati in contesti simili negli esempi forniti:

* acquistare
* acquisto
* acquistato

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sul ciclo di vita di [sviluppo](luis-concept-app-iteration.md) per un'app Luis