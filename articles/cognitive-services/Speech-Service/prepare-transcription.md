---
title: Linee guida per la trascrizione per il training del servizio Voce
titleSuffix: Azure Cognitive Services
description: Informazioni su come preparare il testo per personalizzare i modelli acustici e linguistici e i caratteri voce per il servizio Voce.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 07b90942c8d231418888491fd47a9be3caff77ea
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467736"
---
# <a name="transcription-guidelines-for-using-the-speech-service"></a>Linee guida per la trascrizione per l'uso del servizio Voce

Per personalizzare le funzioni di **Riconoscimento vocale** o **Sintesi vocale**, è necessario fornire testo insieme alla voce. Ogni linea di testo corrisponde a una singola espressione. Il testo deve corrispondere il più possibile al parlato. Il testo viene definito *trascrizione* ed è necessario crearlo in un formato specifico.

Il servizio Voce normalizza l'input per mantenere coerente il testo. 

In questo articolo vengono descritti entrambi i tipi di normalizzazione. Le linee guida variano leggermente per le diverse lingue.

## <a name="us-english-en-us"></a>Inglese Stati Uniti (en-us)

I dati di testo devono essere scritti con un'espressione per linea, in testo normale usando solo il set di caratteri ASCII.

Evitare l'uso di caratteri di punteggiatura estesi (Latin-1) o Unicode. Questi caratteri possono essere inseriti inavvertitamente durante la preparazione dei dati in un programma di elaborazione di testo o durante l'eliminazione di dati da pagine Web. Sostituire i caratteri con elementi sostitutivi ASCII appropriati. Ad esempio: 

| Caratteri da evitare | Sostituzione |
|----- | ----- |
| “Hello world” (virgolette doppie di apertura e chiusura) | "Hello world" (virgolette doppie) |
| John’s day (virgoletta singola destra) | John's day (apostrofo) |
| it was good—no, it was great! (trattino em) | it was good--no, it was great! (trattini) |

### <a name="text-normalization-rules-for-english"></a>Regole di normalizzazione del testo per la lingua inglese

Il servizio Voce esegue le regole di normalizzazione seguenti:

* Uso delle lettere minuscole per tutto il testo
* Rimozione di tutti i segni di punteggiatura tranne apostrofi interni alla parola
* Espansione dei numeri alla forma orale, compresi gli importi in dollari

Di seguito sono riportati alcuni esempi:

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

Applicare la normalizzazione seguente alle trascrizioni di testo:

* Le abbreviazioni devono essere scritte in parole.
* Le stringhe numeriche non standard, ad esempio alcuni formati di data o contabili, devono essere scritte in parole.
* Le parole con caratteri non alfabetici o con caratteri alfanumerici misti devono essere trascritte come vengono pronunciate.
* Lasciare invariate le abbreviazioni che vengono pronunciate come parole, ad esempio "radar", "laser", "RAM" o "NATO".
* Scrivere le abbreviazioni pronunciate come lettere separate con lettere separate da spazi, ad esempio "IBM", "CPU", "FBI", "TBD" o "NaN". 

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

## <a name="chinese-zh-cn"></a>Cinese (zh-cn)

I dati di testo caricati nel Servizio di riconoscimento vocale personalizzato devono usare la codifica UTF-8 con un marcatore dell'ordine dei byte. Il file deve essere scritto con un'espressione per linea.

Evitare l'uso di caratteri di punteggiatura a mezza larghezza. Questi caratteri possono essere inseriti inavvertitamente durante la preparazione dei dati in un programma di elaborazione di testo o durante l'eliminazione di dati da pagine Web. Sostituirli con elementi appropriati a larghezza intera. Ad esempio: 

| Caratteri da evitare | Sostituzione |
|----- | ----- |
| "你好" (virgolette doppie di apertura e chiusura) | "你好" (virgolette doppie) |
| 需要什么帮助? (punto interrogativo) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>Regole di normalizzazione del testo per la lingua cinese

Il servizio Voce esegue le regole di normalizzazione seguenti:

* Rimozione di tutti i segni di punteggiatura
* Espansione dei numeri alla forma parlata
* Conversione di lettere a tutta larghezza in lettere a mezza larghezza
* Uso delle lettere maiuscole per tutte le parole in inglese

Di seguito sono riportati alcuni esempi:

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | w f y z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

Applicare la normalizzazione seguente al testo prima di importarlo:

* Le abbreviazioni devono essere scritte in parole (come nel parlato).
* Scrivere stringhe numeriche in forma parlata.

Di seguito sono riportati alcuni esempi:

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>Altri linguaggi

I dati di testo caricati nel servizio **Riconoscimento vocale** devono usare la codifica UTF-8 con un marcatore dell'ordine dei byte. Il file deve essere scritto con un'espressione per linea.

> [!NOTE]
> Gli esempi seguenti sono basati sulla lingua tedesca, ma le linee guida si applicano a tutte le lingue che non siano inglese (Stati Uniti) o cinese.

### <a name="text-normalization-rules-for-german"></a>Regole di normalizzazione del testo per la lingua tedesca

Il servizio Voce esegue le regole di normalizzazione seguenti:

* Uso delle lettere minuscole per tutto il testo
* Rimozione di tutti i segni di punteggiatura, inclusi i vari tipi di virgolette ("prova", 'prova', "prova„ e «prova»)
* Eliminazione delle linee contenenti caratteri speciali dal set ^ ¢ £ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
* Espansione dei numeri in forma scritta, compresi gli importi in dollari o in euro
* Accettazione degli umlaut per a, o, u; con altre lettere saranno sostituiti da "th" o eliminati

Di seguito sono riportati alcuni esempi:

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

Applicare la normalizzazione seguente al testo prima di importarlo:

* Il separatore decimale deve essere "," e non ".".
* Il separatore tra ore e minuti deve essere ":" e non ".", ad esempio 12:00 Uhr.
* Le abbreviazioni, ad esempio "ca.", non vengono sostituiti. È consigliabile usare la forma estesa.
* I quattro operatori matematici principali (+, -, \* e /) vengono rimossi. È consigliabile sostituirli con la rispettiva forma in lettere: "plus", "minus", "mal" e "geteilt".
* La stessa regola vale per gli operatori di confronto (=, <, and >). È consigliabile sostituirli con "gleich", "kleiner als" e "grösser als".
* Usare le frazioni, ad esempio 3/4, scritte per esteso ("drei viertel" anziché ¾).
* Sostituire il simbolo € con la forma scritta "Euro".

Di seguito sono riportati alcuni esempi:

| Testo originale | Dopo la normalizzazione dell'utente | Dopo la normalizzazione del sistema
|--------  | ----- | -------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf ||
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>Passaggi successivi

- [Ottenere una sottoscrizione di prova al servizio Voce](https://azure.microsoft.com/try/cognitive-services/)
- [Riconoscimento vocale in C#](quickstart-csharp-dotnet-windows.md)
