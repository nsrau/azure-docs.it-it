---
title: Tag delle parti del discorso nell'API Analisi linguistica | Microsoft Docs
description: Informazioni su come i tag delle parti del discorso in Servizi cognitivi Microsoft identificano la categoria o la parte del discorso di ogni parola del testo.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 09/27/2016
ms.author: lesun
ms.openlocfilehash: 35d2042db5f8d64c7dbd1df6bfdebcba6d6aab28
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373292"
---
# <a name="part-of-speech-tagging"></a>Tag delle parti del discorso

## <a name="background-and-motivation"></a>Informazioni generali e motivazione

Dopo che un testo è stato suddiviso in frasi e token, il passaggio successivo dell'analisi consiste nell'identificare la categoria o la parte del discorso di ogni parola,
ad esempio categorie come *nome* (che in genere rappresenta persone, luoghi, cose, idee e così via) e *verbo* (che in genere rappresenta azioni, cambiamenti di stato e così via). Per alcune parole, la parte del discorso è chiara (ad esempio, *quagmire* può essere solo un nome), per altre invece è difficile da stabilire.
*Table* in inglese può significare tavolo o tabella, ma anche "intavolare una discussione".

## <a name="list-of-part-of-speech-tags"></a>Elenco dei tag delle parti del discorso

| Tag | DESCRIZIONE | Parole di esempio |
|-----|-------------|---------------|
| $ | dollar (dollaro) | $ |
| \`\` | opening quotation mark (virgolette doppie aperte) | \` \`\` |
| '' | closing quotation mark (virgolette doppie chiuse) | ' '' |
| ( | opening parenthesis (parentesi aperta) | ( [ { |
| ) | closing parenthesis (parentesi chiusa) | ) ] } |
| , | comma (virgola) | , |
| -- | dash (trattino) | -- |
| . | sentence terminator (segno di interpunzione forte) | . ! ? |
| : | colon or ellipsis (due punti o puntini di sospensione) | : ; ... |
| CC | conjunction, coordinating (congiunzione coordinante) | and but or yet|
| CD | numeral, cardinal (numerale cardinale) | nine 20 1980 '96 |
| DT | determiner (determinante) |a the an all both neither|
| EX | existential there (there esistenziale) | there |
| FW | foreign word (parola straniera) | enfant terrible hoi polloi je ne sais quoi |
| IN | preposition or subordinating conjunction (preposizione o congiunzione subordinante)| in inside if upon whether |
| JJ | adjective or numeral, ordinal (aggettivo o numerale ordinale) | ninth pretty execrable multimodal |
| JJR | adjective, comparative (aggettivo comparativo) | better faster cheaper |
| JJS | adjective, superlative (aggettivo superlativo) | best fastest cheapest | 
| LS | list item marker (indicatore di elemento elenco) | (a) (b) 1 2 A B A. B. |
| MD | modal auxiliary (ausiliario modale) | can may shall will could might should ought |
| NN | noun, common, singular or mass (nome comune singolare o collettivo) | potato money shoe |
| NNP | noun, proper, singular (nome proprio singolare) | Kennedy Roosevelt Chicago Weehauken |
| NNPS | noun, proper, plural (nome proprio plurale) | Springfields Bushes |
| NNS | noun, common, plural (nome comune plurale) | pieces mice fields |
| PDT | pre-determiner (predeterminante) | all both half many quite such sure this |
| POS | genitive marker (indicatore di genitivo) | ' 's |
| PRP | pronoun, personal (pronome personale) | she he it I we they you |
| PRP$ | pronoun, possessive (pronome possessivo) | hers his its my our their your |
| RB | adverb (avverbio) | clinically only |
| RBR | adverb, comparative (avverbio comparativo) | further gloomier grander graver greater grimmer harder harsher healthier heavier higher however larger later leaner lengthier less-perfectly lesser lonelier longer louder lower more... |
| RBS | adverb, superlative (avverbio superlativo) | best biggest bluntest earliest farthest first furthest hardest heartiest highest largest least less most nearest second tightest worst |
| RP | particle (particella) | on off up out about |
| SYM | symbol (simbolo) | % & |
| A | "to" as preposition or infinitive marker ("to" come preposizione o indicatore di infinito) | to |
| UH | interjection (interiezione) | uh hooray howdy hello |
| VB | verb, base form (verbo, forma base) | give assign fly |
| VBD | verb, past tense (verbo, passato) | gave assigned flew |
| VBG | verb, present participle or gerund (verbo, participio presente o gerundio) | giving assigning flying |
| VBN | verb, past participle (verbo, participio passato) | given assigned flown |
| VBP | verb, present tense, not 3rd person singular (verbo, presente, non terza persona singolare) | give assign fly |
| VBZ | verb, present tense, 3rd person singular (verbo, presente, terza persona singolare) | gives assigns flies |
| WDT | WH-determiner (determinante WH) | that what which |
| WP | WH-pronoun (pronome WH) | who whom |
| WP$ | WH-pronoun, possessive (pronome possessivo WH) | whose |
| WRB | Wh-adverb (avverbio Wh) | how however whenever where |

## <a name="specification"></a>Specifiche

Come per la tokenizzazione, Microsoft si avvale della specifica di [Penn Treebank](https://www.cis.upenn.edu/~treebank/).
