---
title: Linee guida per le trascrizioni con etichettatura umana - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Per migliorare la precisione del riconoscimento vocale, ad esempio quando le parole vengono eliminate o sostituite in modo non corretto, è possibile utilizzare trascrizioni con etichetta umana insieme ai dati audio. Le trascrizioni con etichetta tura umana sono trascrizioni parola per parola e verbatim di un file audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806063"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Come creare trascrizioni con etichetta umana

Se stai cercando di migliorare l'accuratezza del riconoscimento, in particolare i problemi che si verificano quando le parole vengono eliminate o sostituite in modo non corretto, ti consigliamo di utilizzare le trascrizioni con etichetta umana insieme ai dati audio. Quali sono le trascrizioni etichettate dall'uomo? È facile, sono trascrizioni parola per parola e verbatim di un file audio.

Per migliorare il riconoscimento è necessario un ampio campione di dati di trascrizione, che consigliamo di fornire tra le 10 e le 1.000 ore di dati di trascrizione. In questa pagina esamineremo le linee guida progettate per aiutarti a creare trascrizioni di alta qualità. Questa guida è suddivisa per impostazioni locali, con sezioni per inglese americano, cinese mandarino e tedesco.

## <a name="us-english-en-us"></a>Inglese Stati Uniti (en-US)

Le trascrizioni con etichettatura umana per l'audio inglese devono essere fornite come testo normale, utilizzando solo caratteri ASCII. Evitare l'uso di caratteri di punteggiatura Latin-1 o Unicode. Questi caratteri vengono spesso aggiunti inavvertitamente quando si copia il testo da un'applicazione di elaborazione testi o si scraping di dati da pagine web. Se questi caratteri sono presenti, assicurarsi di aggiornarli con la sostituzione ASCII appropriata.

Di seguito sono disponibili alcuni esempi:

| Caratteri da evitare | Sostituzione | Note |
| ------------------- | ------------ | ----- |
| "Ciao mondo" | "Hello world" | Le virgolette di apertura e chiusura sono state sostituite con caratteri ASCII appropriati. |
| Il giorno di Giovanni | Il giorno di Giovanni | L'apostrofo è stato sostituito con il carattere ASCII appropriato. |
| it was good—no, it was great! | it was good--no, it was great! | Il trattino em è stato sostituito da due trattini. |

### <a name="text-normalization-for-us-english"></a>Normalizzazione del testo per l'inglese degli Stati Uniti

La normalizzazione del testo è la trasformazione delle parole in un formato coerente utilizzato durante il training di un modello. Alcune regole di normalizzazione vengono applicate automaticamente al testo, tuttavia, si consiglia di utilizzare queste linee guida durante la preparazione dei dati di trascrizione con etichetta umana:Some normalization rules are applied to text automatically, however, we recommend using these guidelines as you prepareing your human-labeled transcription data:

- Scrivere le abbreviazioni in parole.
- Scrivere stringhe numeriche non standard in parole (ad esempio termini contabili).
- I caratteri non alfabetici o i caratteri alfanumerici misti devono essere trascritti come pronunciati.
- Abbreviazioni pronunciate come parole non devono essere modificate (come "radar", "laser", "RAM" o "NATO").
- Scrivere le abbreviazioni pronunciate come lettere separate con ogni lettera separata da uno spazio.

Di seguito sono riportati alcuni esempi di normalizzazione che è necessario eseguire sulla trascrizione:

| Testo originale               | Testo dopo la normalizzazione              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce Banner            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Ke$ha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| Il mio gruppo sanguigno è         | My blood type is O positive           |
| L'acqua è H20                | L'acqua è H 2 O                        |
| Play OU812 di Van Halen     | Gioca O U 8 1 2 di Van Halen           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

Le seguenti regole di normalizzazione vengono applicate automaticamente alle trascrizioni:

- Utilizzare lettere minuscole.
- Rimuovere tutti i punteggi ad eccezione degli apostrofi all'interno delle parole.
- Espandi i numeri in parole/forme parlate, ad esempio importi in dollari.

Ecco alcuni esempi di normalizzazione eseguita automaticamente sulla trascrizione:

| Testo originale                          | Testo dopo la normalizzazione          |
| -------------------------------------- | --------------------------------- |
| "Holy cow!" said Batman.               | holy cow said batman              |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| Costa \$3,14                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>Cinese mandarino (zh-CN)

Le trascrizioni con etichetta umana per l'audio cinese mandarino devono essere codificate in UTF-8 con un indicatore per l'ordine dei byte. Evitare l'uso di caratteri di punteggiatura a mezza larghezza. Questi caratteri possono essere inclusi inavvertitamente quando si preparano i dati in un programma di elaborazione testi o si raschiano i dati dalle pagine Web. Se questi caratteri sono presenti, assicurarsi di aggiornarli con la sostituzione a larghezza intera appropriata.

Di seguito sono disponibili alcuni esempi:

| Caratteri da evitare | Sostituzione   | Note |
| ------------------- | -------------- | ----- |
| "" e " | "" e " | Le virgolette di apertura e chiusura sono state sostituite con i caratteri appropriati. |
| 需要什么帮助? | 需要什么帮助？| Il punto interrogativo è stato sostituito con il carattere appropriato. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalizzazione del testo per il cinese mandarino

La normalizzazione del testo è la trasformazione delle parole in un formato coerente utilizzato durante il training di un modello. Alcune regole di normalizzazione vengono applicate automaticamente al testo, tuttavia, si consiglia di utilizzare queste linee guida durante la preparazione dei dati di trascrizione con etichetta umana:Some normalization rules are applied to text automatically, however, we recommend using these guidelines as you prepareing your human-labeled transcription data:

- Scrivere le abbreviazioni in parole.
- Scrivere stringhe numeriche in forma parlata.

Di seguito sono riportati alcuni esempi di normalizzazione che è necessario eseguire sulla trascrizione:

| Testo originale | Testo dopo la normalizzazione |
| ------------- | ------------------------ |
| 21 USD (21) | 我今年二十一 |
| 504 USD (504) | 三号 楼 五 零 四 |

Le seguenti regole di normalizzazione vengono applicate automaticamente alle trascrizioni:

- Rimuovere tutti i punteggiatura
- Espandere i numeri alla forma parlata
- Convertire lettere a larghezza intera in lettere a mezza larghezza
- Uso delle lettere maiuscole per tutte le parole in inglese

Ecco alcuni esempi di normalizzazione eseguita automaticamente sulla trascrizione:

| Testo originale | Testo dopo la normalizzazione |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| 3,5 USD | 三 元 五 角 |
| w f y z | w f y z |
| 1992 X 8 o 8 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 5:00 USD | 下午 五点 的 航班 |
| 21 USD (21) | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Tedesco (de-DE) e altre lingue

Le trascrizioni con etichetta umana per l'audio tedesco (e altre lingue cinesi non inglesi o mandarino) devono essere codificate in UTF-8 con un indicatore dell'ordine dei byte. Per ogni file audio deve essere fornita una trascrizione con etichetta umana.

### <a name="text-normalization-for-german"></a>Normalizzazione del testo per il tedesco

La normalizzazione del testo è la trasformazione delle parole in un formato coerente utilizzato durante il training di un modello. Alcune regole di normalizzazione vengono applicate automaticamente al testo, tuttavia, si consiglia di utilizzare queste linee guida durante la preparazione dei dati di trascrizione con etichetta umana:Some normalization rules are applied to text automatically, however, we recommend using these guidelines as you prepareing your human-labeled transcription data:

- Scrivere i decimali come "," e non ".".
- Scrivi separatori di ora come ":" e non "." (ad esempio: 12:00 Uhr).
- Le abbreviazioni, ad esempio "ca.", non vengono sostituiti. Si consiglia di utilizzare la forma parlata completa.
- I quattro operatori matematici principali (+, -, \* e /) vengono rimossi. Ti consigliamo di sostituirli con la forma scritta: "più", "meno", "mal" e "geteilt".
- Gli operatori di confronto vengono rimossi (Sezione <, < e >). È consigliabile sostituirli con "gleich", "kleiner als" e "grösser als".
- Scrivere frazioni, ad esempio 3/4, in forma scritta (ad esempio: "drei viertel" invece di 3/4).
- Sostituire il simbolo "" con la forma scritta "Euro".

Di seguito sono riportati alcuni esempi di normalizzazione che è necessario eseguire sulla trascrizione:

| Testo originale    | Testo dopo la normalizzazione dell'utente | Testo dopo la normalizzazione del sistema       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

Le seguenti regole di normalizzazione vengono applicate automaticamente alle trascrizioni:

- Utilizzare lettere minuscole per tutto il testo.
- Rimuovere tutti i segni di punteggiatura, inclusi vari tipi di virgolette ("test", 'test', "test" e "test" sono OK).
- Eliminare le righe con qualsiasi carattere speciale da questo set: ® © .
- Espandere i numeri in forma parlata, compresi gli importi in dollari o in euro.
- Accettate umlaut solo per a, o, e voi. Altri saranno sostituiti da "th" o saranno scartati.

Ecco alcuni esempi di normalizzazione eseguita automaticamente sulla trascrizione:

| Testo originale    | Testo dopo la normalizzazione |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>Passaggi successivi

- [Preparare e testare i dati](how-to-custom-speech-test-data.md)
- [Ispezionare i dati](how-to-custom-speech-inspect-data.md)
- [Valutare i dati](how-to-custom-speech-evaluate-data.md)
- [Eseguire il training del modello](how-to-custom-speech-train-model.md)
- [Distribuire il modello](how-to-custom-speech-deploy-model.md)
