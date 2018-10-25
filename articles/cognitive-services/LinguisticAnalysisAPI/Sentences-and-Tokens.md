---
title: Frasi e token - API Analisi linguistica
titlesuffix: Azure Cognitive Services
description: Informazioni sulla separazione e tokenizzazione delle frasi nell'API Analisi linguistica.
services: cognitive-services
author: DavidLiCIG
manager: cgronlun
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: conceptual
ms.date: 03/21/2016
ms.author: davl
ROBOTS: NOINDEX
ms.openlocfilehash: 289cab4999276cbfb1fa558f558ebafa8e4e3a30
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237875"
---
# <a name="sentence-separation-and-tokenization"></a>Separazione e tokenizzazione delle frasi

> [!IMPORTANT]
> L'anteprima di Analisi linguistica è stata ritirata il 9 agosto 2018. Per l'elaborazione e l'analisi del testo è consigliabile usare i [moduli di analisi del testo di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics).

## <a name="background-and-motivation"></a>Informazioni generali e motivazione

In un corpo di testo specifico il primo passaggio dell'analisi linguistica consiste nel suddividerlo in frasi e token.

### <a name="sentence-separation"></a>Separazione delle frasi

A prima vista, la suddivisione del testo in frasi può sembrare semplice: è sufficiente trovare gli indicatori di fine frase, in corrispondenza dei quali interrompere le frasi.
Questi contrassegni tuttavia sono spesso complicati e ambigui.

Si consideri il testo di esempio seguente:

> What did you say?!? I didn't hear about the director's "new proposal." It's important to Mr. and Mrs. Smith.

Questo testo contiene tre frasi:

- What did you say?!?
- I didn't hear about the director's "new proposal."
- It's important to Mr. and Mrs. Smith.

Si noti che la fine delle frasi è contrassegnata in modi molto diversi.
La prima termina con una combinazione di punti interrogativi e punti esclamativi, detta talvolta punto esclarrogativo.
La seconda termina con un punto, ma le virgolette seguenti dovrebbero fare parte della frase precedente.
Nella terza frase è possibile osservare che lo stesso carattere punto può essere usato anche per contrassegnare le abbreviazioni.
Dalla semplice analisi della punteggiatura si ricava una valida serie di candidati, ma sono necessarie ulteriori operazioni per identificare i veri delimitatori di frase.

### <a name="tokenization"></a>Tokenizzazione

L'attività successiva consiste nel suddividere le frasi in token.
Nella lingua inglese i token vengono per lo più separati da spazi.
Trovare parole o token è molto più semplice in inglese che in cinese, dove gli spazi tra le parole non sono quasi mai usati.
La prima frase potrebbe essere scritta "Whatdidyousay?"

Esistono alcuni casi complessi.
Prima di tutto, la punteggiatura spesso (ma non sempre) dovrebbe essere separata dal contesto circostante.
In secondo luogo, l'inglese presenta *contrazioni*, ad esempio "didn't" o "it's", in cui le parole sono state compresse e abbreviate in parti più piccole.
L'obiettivo del tokenizer consiste nel suddividere la sequenza di caratteri in parole.

Si torni ora alle frasi di esempio precedenti,
in cui è stato inserito un "punto mediano" (&middot;) tra ogni singolo token.

- What &middot; did &middot; you &middot; say &middot; ?!?
- I &middot; did &middot; n't &middot; hear &middot; about &middot; the &middot; director &middot; 's &middot; " &middot; new &middot; proposal &middot; . &middot; "
- It &middot; 's &middot; important &middot; to &middot; Mr. &middot; and &middot; Mrs. &middot; Smith &middot; .

Si noti che la maggior parte dei token sono parole riportate nel dizionario, ad esempio *important* e *director*.
Gli altri sono costituti esclusivamente da punteggiatura.
Ci sono infine token più insoliti che rappresentano contrazioni, ad esempio *n't* per *not* e possessivi come *'s*.
Questa tokenizzazione consente di gestire la parola *didn't* e la locuzione *did not* in modo più coerente.

## <a name="specification"></a>Specifiche

È importante decidere in modo coerente da che cosa sono costituiti una frase e un token.
Viene usata la specifica di [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42) (altri dettagli sono disponibili all'indirizzo ftp://ftp.cis.upenn.edu/pub/treebank/public_html/tokenization.html).
