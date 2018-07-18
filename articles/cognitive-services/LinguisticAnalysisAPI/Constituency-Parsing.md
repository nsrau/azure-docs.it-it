---
title: Analisi a costituenti nell'API Analisi linguistica | Microsoft Docs
description: Informazioni sul modo in cui l'analisi a costituenti, nota anche come "analisi della struttura delle frasi", identifica i sintagmi nel testo.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 03/21/2016
ms.author: lesun
ms.openlocfilehash: bff5e587621e1278c260d555aec280a0f4c7c8a1
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082173"
---
# <a name="constituency-parsing"></a>Analisi a costituenti

Lo scopo dell'analisi a costituenti, nota anche come "analisi della struttura delle frasi", è identificare i sintagmi nel testo.
Può essere utile durante l'estrazione di informazioni dal testo.
I clienti potrebbero voler trovare nomi di funzionalità o sintagmi chiave in un corpo di testo di grandi dimensioni e visualizzare i modificatori e le azioni che circondano ognuno di tali sintagmi.

## <a name="phrases"></a>Sintagmi

Per un linguista, un *sintagma* rappresenta più di una sequenza di parole.
Per essere un sintagma, un gruppo di parole deve svolgere un ruolo specifico all'interno della frase.
Tale gruppo di parole può essere spostato o sostituito nel suo complesso e la frase deve rimanere scorrevole e grammaticalmente corretta.

Si consideri la frase

> I want to find a new hybrid automobile with Bluetooth.

Questa frase contiene il sintagma nominale: "I want to find a new hybrid automobile with Bluetooth".
Come sappiamo che si tratta di un sintagma?
È possibile riscrivere la frase (in modo poetico) spostando quell'intero sintagma all'inizio:

> A new hybrid automobile with Bluetooth I want to find.

In alternativa, si può sostituire il sintagma con un altro con funzione e significato analogo, ad esempio "a fancy new car":

> I want to find a fancy new car.

Se si fosse scelto un subset diverso di parole, queste attività di sostituzione avrebbero prodotto frasi strane o illeggibili.
Si consideri cosa succede spostando "find a new" all'inizio:

> Find a new I want to hybrid automobile with Bluetooth.

Il risultato è molto difficile da leggere e comprendere.

L'obiettivo di un parser consiste nell'individuare tutti i sintagmi di questo tipo.
È interessante notare che, in linguaggio naturale, i sintagmi tendono a essere annidati l'uno nell'altro.
Una rappresentazione di questi sintagmi è una struttura ad albero, come la seguente:

![Albero](./Images/tree.png)

In quest'albero, i rami contrassegnati come "NP" sono sintagmi nominali (noun phrase).
Sono presenti vari sintagmi di questo tipo: *I*, *a new hybrid automobile*, *Bluetooth* e *a new hybrid automobile with Bluetooth*.

## <a name="phrase-types"></a>Tipi di sintagma

| Etichetta | DESCRIZIONE | Esempio |
|-------|-------------|---------|
|ADJP   | Adjective Phrase (sintagma aggettivale) | "so rude" |
|ADVP   | Adverb Phrase (sintagma avverbiale) | "clear through" |
|CONJP  | Conjunction Phrase (sintagma di congiunzione) | "as well as" |
|FRAG   | Frammento, usato per input incompleti o frammentari | "Highly recommended..." |
|INTJ   | Interjection (interiezione) | "Hooray" |
|LST    | List marker (contrassegno di elenco), inclusa la punteggiatura | "#4)" |
|NAC    | Not A Constituent (non un costituente), usato per indicare l'ambito di un sintagma non costituente |  "and for a good deal" in "you get things and for a good deal" |
|NP | Noun Phrase (sintagma nominale) | "a tasty potato pancake" |
|NX | Usato in alcuni NP complessi per contrassegnare la testa| |
|PP | Prepositional Phrase (sintagma preposizionale)| "in the pool" |
|PRN    | Parenthetical (parentetico)| "(so called)" |
|PRT    | Particle (particella)| "out" in "ripped out" |
|QP | Quantity Phrase ( sintagma quantitativo), ad esempio misura o quantità complessa, all'interno di un sintagma nominale| "around $75" |
|RRC    | Reduced Relative Clause (proposizione relativa ridotta).| "still unresolved" in "many issues still unresolved" |
|S  | Frase o proposizione | "This is a sentence."
|SBAR   | Proposizione subordinata, spesso introdotta da una congiunzione subordinante | "as I left" in "I looked around as I left."|
|SBARQ  | Domanda diretta introdotta da elemento o sintagma wh | "What was the point?" |
|SINV   | Inverted declarative sentence (proposizione dichiarativa inversa) | "At no time were they aware." (si noti come il soggetto normale "they" è stato spostato dopo il verbo "were") |
|SQ | Domanda si/no invertita o proposizione principale di una domanda introdotta da elemento wh | "Did they get the car?" |
|UCP    | Unlike Coordinated Phrase (sintagma coordinante di categorie sintattiche dissimili)| "small and with bugs" (si noti come un aggettivo e un sintagma preposizionale sono congiunti con "and")|
|VP | Verb Phrase (sintagma verbale) | "ran into the woods" |
|WHADJP | Wh-adjective Phrase (sintagma con elemento wh aggettivale) | "how uncomfortable" |
|WHADVP | Wh-adverb Phrase (sintagma con elemento wh avverbiale)| "when" |
|WHNP   | Wh-noun Phrase (sintagma con elemento wh nominale)| "which potato", "how much soup"|
|WHPP   | Wh-prepositional Phrase (sintagma con elemento wh preposizionale)| "in which country"|
|X  | Sconosciuto, incerto o impossibile da parentesizzare.| first "the" in "the... the soup" |


## <a name="specification"></a>Specifiche

Gli alberi in questo argomento usano le S-expression della [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
