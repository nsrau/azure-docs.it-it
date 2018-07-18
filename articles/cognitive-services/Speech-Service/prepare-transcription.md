---
title: Linee guida per la trascrizione vocale | Microsoft Docs
description: Di seguito viene descritto come preparare il testo per personalizzare i modelli acustici e linguistici e i caratteri voce per il servizio Voce.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 93ab7c81a773f692b2b970bb1901d82b7aceb5a2
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "35378860"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>Linee guida per la trascrizione per l'utilizzo del servizio Voce

Per personalizzare le funzioni di **Riconoscimento vocale** o **Sintesi vocale**, è necessario fornire testo insieme alla voce. Ogni linea di testo corrisponde a una singola espressione. Il testo viene definito *trascrizione* ed è necessario crearlo in un formato specifico.

Il servizio Voce esegue alcune normalizzazioni per rendere il testo coerente. Prima che venga inviato il testo per il training, è necessario effettuare altre attività di normalizzazione. 

In questo articolo vengono descritti entrambi i tipi di normalizzazione. Le linee guida variano leggermente per le diverse lingue.

## <a name="us-english-en-us"></a>Inglese USA (en-US)

I dati di testo caricati in questo servizio devono essere scritti in testo normale utilizzando solo il set di caratteri ASCII. Ogni linea del file deve contenere il testo per una singola espressione.

È importante evitare l'uso di caratteri di punteggiatura estesi (Latin-1) o Unicode. Questi caratteri possono essere inclusi inavvertitamente durante la preparazione dei dati in un programma di elaborazione testi o durante l'eliminazione di dati da pagine Web. Sostituire questi caratteri con elementi sostitutivi ASCII appropriati. Ad esempio: 

| Caratteri da evitare | Sostituzione |
|----- | ----- |
| "Hello world" (virgolette doppie di apertura e chiusura) | "Hello world" (virgolette doppie) |
| John’s day (virgoletta singola destra) | John's day (apostrofo) |
| it was good—no, it was great! (trattino em) | it was good--no, it was great! (trattini) |

### <a name="text-normalization-performed-by-the-service"></a>Normalizzazione del testo eseguita dal servizio

Il servizio Voce esegue la normalizzazione di testo seguente su trascrizioni di testo.

*   Lettere minuscole per tutto il testo
*   Rimozione di tutti i segni di punteggiatura tranne apostrofi interni alla parola
*   Espansione dei numeri alla forma orale, compresi gli importi in dollari

Di seguito sono riportati alcuni esempi

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| Starbucks Coffee | starbucks coffee |
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | i'm double jointed |
| 104 Main Street | one oh four main street |
| Tune to 102.7 | tune to one oh two point seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

### <a name="text-normalization-you-must-perform"></a>Normalizzazione del testo che deve essere eseguita dall’utente

Applicare la seguente normalizzazione per le trascrizioni di testo.

*   Le abbreviazioni devono essere scritte a parole per riflettere la forma orale
*   Le stringhe numeriche non standard (come alcune date o forme contabili) devono essere scritte a parole
*   Le parole con caratteri non alfabetici o con caratteri alfanumerici misti devono essere trascritte come vengono pronunciate
*   Lasciare invariate le abbreviazioni pronunciate come parole. Ad esempio, radar, laser, RAM, NATO e Mr.
*   Scrivere le abbreviazioni pronunciate come lettere separate, con le lettere separate da spazi. Ad esempio, IBM, CPU, FBI, TBD, NaN. 

Di seguito sono riportati alcuni esempi:

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr. Strangelove | Doctor Strangelove |
| James Bond 007 | James Bond double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

## <a name="chinese-zh-cn"></a>Cinese (zh-CN)

I dati di testo caricati nel servizio di Riconoscimento vocale personalizzato devono utilizzare la codifica UTF-8 con il marcatore dell'ordine dei byte. Ogni linea del file deve contenere il testo per una singola espressione.

È importante evitare l'uso di caratteri di punteggiatura a mezza larghezza. Questi caratteri possono essere inclusi inavvertitamente durante la preparazione dei dati in un programma di elaborazione testi o durante l'eliminazione di dati da pagine Web. Sostituirli con elementi appropriati a larghezza intera. Ad esempio: 

| Caratteri da evitare | Sostituzione |
|----- | ----- |
| "你好" (virgolette doppie di apertura e chiusura) | "你好" (virgolette doppie) |
| 需要什么帮助? (punto interrogativo) | 需要什么帮助？ |

### <a name="text-normalization-performed-by-the-service"></a>Normalizzazione del testo eseguita dal servizio

Il servizio Voce esegue la normalizzazione di testo seguente su trascrizioni di testo.

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
| 你吃饭了吗 ? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-you-must-perform"></a>Normalizzazione del testo che deve essere eseguita dall’utente

Applicare la seguente normalizzazione al testo prima di importarlo.

*   Le abbreviazioni devono essere scritte a parole per riflettere la forma orale
*   Questo servizio non copre tutte le quantità numeriche. È più affidabile scrivere stringhe numeriche in forma parlata.

Di seguito sono riportati alcuni esempi.

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Altri linguaggi

I dati di testo caricati nel servizio di **Riconoscimento vocale** devono utilizzare la codifica UTF-8 con il marcatore dell'ordine dei byte. Ogni linea del file deve contenere il testo per una singola espressione.

> [!NOTE]
> Questi esempi usano il tedesco. Tuttavia, queste linee guida si applicano a tutte le lingue che non siano inglese o cinese.

### <a name="text-normalization-performed-by-the-service"></a>Normalizzazione del testo eseguita dal servizio

Il servizio Voce esegue la normalizzazione di testo seguente su trascrizioni di testo.

*   Lettere minuscole per tutto il testo
*   Rimozione di tutti i segni di punteggiatura, inclusi i vari tipi di virgolette ("test", 'test', "test„ o «test» sono ok)
*   Eliminazione di qualsiasi linea contenente caratteri speciali dal set ^ ¢ £ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
*   Espansione dei numeri alla forma scritta, compresi gli importi in dollari o in euro
*   Gli Umlaut sono accettati solo per a, o, u; gli altri saranno sostituiti da "th" o scartati

Di seguito sono riportati alcuni esempi

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |
| Das macht $10 | das macht zehn dollars |

### <a name="text-normalization-you-must-perform"></a>Normalizzazione del testo che deve essere eseguita dall’utente

Applicare la seguente normalizzazione al testo prima di importarlo.

*   Il separatore decimale deve essere ","e non".": 2,3% e non 2.3%
*   Il separatore dell'ora tra ore e minuti deve essere ":"e non".": 12:00 Uhr
*   Le abbreviazioni, ad esempio 'ca.', 'bzw' non vengono sostituite. È consigliabile utilizzare la forma estesa.
*   I cinque principali operatori matematici vengono eliminati: +, -, \*, /. Si consiglia di sostituirli con la loro forma letterale: plus, minus, mal, geteilt.
*   Ciò vale anche per gli operatori di confronto (=, <, >): gleich, kleiner als, grösser als
*   Utilizzare frazioni, come 3/4, in forma scritta (come "drei viertel" invece di ¾)
*   Sostituire il simbolo di € con il formato scritto "Euro"

Di seguito sono riportati alcuni esempi.

| Testo originale | Dopo la normalizzazione dell'utente | Dopo la normalizzazione del sistema
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| Das macht 12€ | Das macht 12 Euros | das macht zwölf euros |

## <a name="next-steps"></a>Passaggi successivi

- [Accedere alla versione di prova del servizio Voce](https://azure.microsoft.com/try/cognitive-services/)
- [Riconoscimento vocale in C#](quickstart-csharp-windows.md)
