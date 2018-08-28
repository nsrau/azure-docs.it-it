---
title: Linee guida di trascrizione per l'impostazione del riconoscimento vocale
description: Di seguito viene descritto come preparare il testo per personalizzare i modelli acustici e linguistici e i caratteri voce per il servizio Voce.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: db324b6c5444955debdc6a3e09906a0de47ff819
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2018
ms.locfileid: "41929819"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>Linee guida per la trascrizione per l'utilizzo del servizio Voce

Per personalizzare le funzioni di **Riconoscimento vocale** o **Sintesi vocale**, è necessario fornire testo insieme alla voce. Ogni linea di testo corrisponde a una singola espressione. Il testo deve corrispondere il più esattamente possibile al riconoscimento vocale. Il testo viene definito *trascrizione* ed è necessario crearlo in un formato specifico.

Il servizio riconoscimento vocale Normalizza l'input per mantenere coerente il testo. 

In questo articolo vengono descritti entrambi i tipi di normalizzazione. Le linee guida variano leggermente per le diverse lingue.

## <a name="us-english-en-us"></a>Inglese Stati Uniti (en-US)

I dati di testo devono essere scritti con un'espressione per linea, in testo normale usando solo il set di caratteri ASCII.

Evitare l'uso di caratteri di punteggiatura estesi (Latin-1) o Unicode. Questi caratteri possono essere inclusi inavvertitamente durante la preparazione dei dati in un programma di elaborazione testi o durante l'eliminazione di dati da pagine Web. Sostituire questi caratteri con elementi sostitutivi ASCII appropriati. Ad esempio: 

| Caratteri da evitare | Sostituzione |
|----- | ----- |
| "Hello world" (virgolette doppie di apertura e chiusura) | "Hello world" (virgolette doppie) |
| John’s day (virgoletta singola destra) | John's day (apostrofo) |
| it was good—no, it was great! (trattino em) | it was good--no, it was great! (trattini) |

### <a name="text-normalization-rules-for-english"></a>Regole di normalizzazione del testo per la lingua inglese

Il servizio riconoscimento vocale esegue le regole di normalizzazione seguenti.

*   Lettere minuscole per tutto il testo
*   Rimozione di tutti i segni di punteggiatura tranne apostrofi interni alla parola
*   Espansione dei numeri alla forma orale, compresi gli importi in dollari

Di seguito sono riportati alcuni esempi

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

Applicare la seguente normalizzazione per le trascrizioni di testo.

*   Le abbreviazioni devono essere scritte a parole
*   Le stringhe numeriche non standard (come alcune date o forme contabili) devono essere scritte a parole
*   Le parole con caratteri non alfabetici o con caratteri alfanumerici misti devono essere trascritte come vengono pronunciate
*   Lasciare invariate le abbreviazioni pronunciate come parole. Ad esempio, radar, laser, RAM, NATO.
*   Scrivere le abbreviazioni pronunciate come lettere separate, con le lettere separate da spazi. Ad esempio, IBM, CPU, FBI, TBD, NaN. 

Di seguito sono riportati alcuni esempi:

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr. Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Ke$ha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

## <a name="chinese-zh-cn"></a>Cinese (zh-CN)

I dati di testo caricati nel servizio di Riconoscimento vocale personalizzato devono utilizzare la codifica UTF-8 con il marcatore dell'ordine dei byte. Il file deve essere scritto con un'espressione per linea.

Evitare l'uso di caratteri di punteggiatura a mezza larghezza. Questi caratteri possono essere inclusi inavvertitamente durante la preparazione dei dati in un programma di elaborazione testi o durante l'eliminazione di dati da pagine Web. Sostituirli con elementi appropriati a larghezza intera. Ad esempio: 

| Caratteri da evitare | Sostituzione |
|----- | ----- |
| "你好" (virgolette doppie di apertura e chiusura) | "你好" (virgolette doppie) |
| 需要什么帮助? (punto interrogativo) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>Regole di normalizzazione del testo per la lingua cinese

Il servizio riconoscimento vocale esegue le regole di normalizzazione seguenti.

*   Rimozione di tutti i segni di punteggiatura
*   Espansione dei numeri alla forma parlata
*   Conversione di lettere a tutta larghezza in lettere a mezza larghezza
*   Lettera maiuscola per tutte le parole inglesi

Di seguito sono riportati alcuni esempi.

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | w f y z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

Applicare la seguente normalizzazione al testo prima di importarlo.

*   Le abbreviazioni devono essere scritte a parole (come nella forma orale)
*   Scrivere stringhe numeriche in forma parlata.

Di seguito sono riportati alcuni esempi.

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Altri linguaggi

I dati di testo caricati nel servizio di **Riconoscimento vocale** devono utilizzare la codifica UTF-8 con il marcatore dell'ordine dei byte. Il file deve essere scritto con un'espressione per linea.

> [!NOTE]
> Questi esempi usano il tedesco. Tuttavia, queste linee guida si applicano a tutte le lingue che non siano inglese o cinese.

### <a name="text-normalization-rules-for-german"></a>Regole di normalizzazione del testo per la lingua tedesca

Il servizio riconoscimento vocale esegue le regole di normalizzazione seguenti.

*   Lettere minuscole per tutto il testo
*   Rimozione di tutti i segni di punteggiatura, inclusi i vari tipi di virgolette ("test", 'test', "test„ o «test» sono ok)
*   Eliminazione delle linee contenenti caratteri speciali dal set ^ ¢ £ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
*   Espansione dei numeri alla forma scritta, compresi gli importi in dollari o in euro
*   Gli Umlaut sono accettati solo per a, o, u; gli altri saranno sostituiti da "th" o scartati

Di seguito sono riportati alcuni esempi

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

Applicare la seguente normalizzazione al testo prima di importarlo.

*   Il separatore decimale deve essere "," e non ".",
*   Il separatore dell'ora tra ore e minuti deve essere ":"e non".": 12:00 Uhr
*   Le abbreviazioni, ad esempio "ca." non vengono sostituiti. È consigliabile utilizzare la forma estesa.
*   I quattro principali operatori matematici vengono eliminati: +, -, \*, /. Si consiglia di sostituirli con la loro forma letterale: plus, minus, mal, geteilt.
*   Ciò vale anche per gli operatori di confronto (=, <, >): gleich, kleiner als, grösser als
*   Utilizzare frazioni, come 3/4, in forma scritta (come "drei viertel" invece di ¾)
*   Sostituire il simbolo di € con il formato scritto "Euro"

Di seguito sono riportati alcuni esempi.

| Testo originale | Dopo la normalizzazione dell'utente | Dopo la normalizzazione del sistema
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf ||
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di valutazione gratuita del Servizio di riconoscimento vocale](https://azure.microsoft.com/try/cognitive-services/)
- [Riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
