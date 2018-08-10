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
ms.openlocfilehash: 90fd5b05c2dabdac88c6c8da288ab629177be38d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082639"
---
# <a name="part-of-speech-tagging"></a>Tag delle parti del discorso

## <a name="background-and-motivation"></a>Informazioni generali e motivazione

Dopo che un testo è stato suddiviso in frasi e token, il passaggio successivo dell'analisi consiste nell'identificare la categoria o la parte del discorso di ogni parola,
ad esempio categorie come *nome* (che in genere rappresenta persone, luoghi, cose, idee e così via) e *verbo* (che in genere rappresenta azioni, cambiamenti di stato e così via). Per alcune parole, la parte del discorso è chiara (ad esempio, *quagmire* può essere solo un nome), per altre invece è difficile da stabilire.
*Table* in inglese può significare tavolo o tabella, ma anche "intavolare una discussione".

## <a name="list-of-part-of-speech-tags"></a>Elenco dei tag delle parti del discorso

| Tag | DESCRIZIONE | Parole di esempio |
|-----|-------------|---------------|
| $ | dollaro | $ |
| \`\` | virgolette doppie aperte | \` \`\` |
| '' | virgolette doppie chiuse | ' '' |
| ( | parentesi aperta | ( [ { |
| ) | parentesi chiusa | ) ] } |
| , | virgola | , |
| -- | trattino | -- |
| . | segno di interpunzione forte | . ! ? |
| : | due punti o puntini di sospensione | : ; ... |
| CC | congiunzione coordinante | e ma o tuttavia|
| CD | numerale cardinale | nove 20 1980 '96 |
| DT | determinante |un il uno tutto entrambi qualunque|
| EX | "there" esistenziale | there |
| FW | parola straniera | enfant terrible hoi polloi je ne sais quoi |
| IN | preposizione o congiunzione subordinante| in nel se dopo affinché |
| JJ | aggettivo o numerale ordinale | nono grazioso esecrabile multimodale |
| JJR | aggettivo comparativo | migliore più veloce più economico |
| JJS | aggettivo superlativo | il migliore velocissimo il più economico | 
| LS | indicatore di elemento elenco | (a) (b) 1 2 A B A. B. |
| MD | ausiliario modale | dovere volere potere sapere |
| NN | nome comune singolare o collettivo | patata denaro scarpa |
| NNP | nome proprio singolare | Kennedy Roosevelt Firenze Fiumicino |
| NNPS | nome proprio plurale | Montagne Rocciose |
| NNS | nome comune plurale | pezzi topi campi |
| PDT | predeterminante | tutto entrambi metà molti piuttosto tale questo |
| POS | indicatore di genitivo | ' 's |
| PRP | pronome personale | io tu lui lei egli ella esso noi voi essi |
| PRP$ | pronome possessivo | mio suo tuo nostro vostro loro |
| RB | avverbio | clinicamente solo |
| RBR | avverbio comparativo | più velocemente tanto perfettamente meno stancamente tanto dolcemente quanto naturalmente meglio peggio maggiormente più meno |
| RBS | avverbio superlativo | velocissimamente molto stancamente il più lentamente assai chiaramente ottimamente pessimamente massimamente minimamente benissimo malissimo grandissimamente pochissimo |
| RP | particella | mi ti vi ci si ne |
| SYM | simbolo | % & |
| A | "to" come preposizione o indicatore di infinito | to |
| UH | interiezione | ehi ah eh toh ahimè boh |
| VB | verbo, forma base | dare assegnare volare |
| VBD | verbo, passato | davo assegnavo volavo |
| VBG | verbo, participio presente o gerundio | dando assegnando volando |
| VBN | verbo, participio passato | dato assegnato volato |
| VBP | verbo, presente, non terza persona singolare | danno assegnano volano |
| VBZ | verbo, presente, terza persona singolare | dà assegna vola |
| WDT | determinante WH | that what which |
| WP | pronome WH | who whom |
| WP$ | pronome possessivo WH | whose |
| WRB | avverbio WH | how however whenever where |

## <a name="specification"></a>Specifiche

Come per la tokenizzazione, Microsoft si avvale della specifica di [Penn Treebank](https://catalog.ldc.upenn.edu/ldc99t42).
