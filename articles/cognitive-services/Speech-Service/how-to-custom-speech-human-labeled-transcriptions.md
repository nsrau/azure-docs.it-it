---
title: Linee guida per un'etichetta umane trascrizioni - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Se si intende per migliorare la precisione del riconoscimento, in particolare i problemi che vengono generati quando le parole vengono eliminate o sostituite in modo non corretto, è opportuno usare un'etichetta umane trascrizioni insieme ai dati audio. Quali sono un'etichetta umane trascrizioni? La risposta è semplice, si tratta di parola per parola, verbatim trascrizioni di un file audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 1fca2a21758a060dbfdc4acb2123a59fcae585fd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606558"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Come creare un'etichetta umane trascrizioni

Se si intende per migliorare la precisione del riconoscimento, in particolare i problemi che vengono generati quando le parole vengono eliminate o sostituite in modo non corretto, è opportuno usare un'etichetta umane trascrizioni insieme ai dati audio. Quali sono un'etichetta umane trascrizioni? La risposta è semplice, si tratta di parola per parola, verbatim trascrizioni di un file audio.

Per migliorare il riconoscimento è necessario un ampio campione di dati di trascrizione, è consigliabile fornire compreso tra 10 e 1.000 ore di dati di trascrizione. In questa pagina, verranno esaminate le linee guida che consentono di creare trascrizioni di alta qualità. In questa guida è suddivisa nelle impostazioni locali, con sezioni per inglese Stati Uniti, il cinese mandarino e tedesco.

## <a name="us-english-en-us"></a>Inglese Stati Uniti (en-US)

Come testo normale, usando solo caratteri ASCII, è necessario specificare un'etichetta umane trascrizioni per l'audio in lingua inglese. Evitare l'uso dei caratteri di punteggiatura Latin 1 o Unicode. Questi caratteri vengono spesso inavvertitamente aggiunti durante la copia di testo da un'applicazione di elaborazione di testi o esamina i dati dalle pagine web. Se questi caratteri sono presenti, assicurarsi di aggiornarli con la sostituzione di ASCII appropriata.

Di seguito sono disponibili alcuni esempi:

| Caratteri da evitare | Sostituzione | Note |
|---------------------|--------------|-------|
| "Hello world" | "Hello world" | Le virgolette di apertura e chiusura sono state sostituite con caratteri ASCII appropriati. |
| Giorno di John | Giorno di John | Apostrofo è stato sostituito con il carattere ASCII appropriato. |
| it was good—no, it was great! | it was good--no, it was great! | Il trattino em è stato sostituito con due segni meno. |

### <a name="text-normalization-for-us-english"></a>Normalizzazione del testo per l'inglese Americano

Normalizzazione del testo è la trasformazione delle parole in un formato coerente usato durante il training di un modello. Alcune regole di normalizzazione vengono applicate automaticamente al testo, tuttavia, è consigliabile usare queste linee guida durante la preparazione dei dati con etichetta umane trascrizione:

* Scrivere le abbreviazioni in parole.
* Scrive stringhe numeriche non standard in parole (ad esempio termini di contabilità).
* Misti caratteri alfanumerici o caratteri non alfabetici dovrebbero essere trascritto particolarmente evidenti.
* Le abbreviazioni che vengono pronunciate come parole non devono essere modificate (ad esempio, "radar", "laser", "RAM" o "NATO").
* Scrittura out abbreviazioni che vengono pronunciati come lettere separate con ogni lettera separato da uno spazio.

Ecco alcuni esempi di normalizzazione che è consigliabile eseguire la trascrizione di:

| Testo originale | Testo dopo la normalizzazione |
|---------------|--------------------------|
| Dr. Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Ke$ha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| il tipo di sangue è + O | My blood type is O positive |
| Water è H20 | Water è O 2 H |
| Riproduci OU812 da Van Halen | Riproduci U O 8 1 2 da Halen Van |
| UTF-8 with BOM | U T F 8 with BOM |

Le seguenti regole di normalizzazione vengono applicate automaticamente a trascrizioni:

* Usare lettere minuscole.
* Rimuovere tutti i segni di punteggiatura tranne apostrofi all'interno delle parole.
* Espandere i numeri in formato parole/pronunciare, ad esempio quantità di denaro.

Ecco alcuni esempi di normalizzazione eseguita automaticamente la trascrizione di:

| Testo originale | Testo dopo la normalizzazione |
|---------------|--------------------------|
| "Holy cow!" said Batman. | holy cow said batman |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
It costs $3.14| it costs three fourteen |

## <a name="mandarin-chinese-zh-cn"></a>Cinese mandarino (zh-CN)

Con l'etichetta umane trascrizioni per l'audio cinese mandarino devono essere codificato con un byte order mark UTF-8. Evitare l'uso di caratteri di punteggiatura a mezza larghezza. Questi caratteri possono essere inclusi inavvertitamente quando si prepara i dati in un programma di elaborazione testi o cercare i dati dalle pagine web. Se questi caratteri sono presenti, assicurarsi di aggiornarli con la sostituzione a larghezza intera appropriata.

Di seguito sono disponibili alcuni esempi:

| Caratteri da evitare | Sostituzione | Note |
|---------------------|--------------|-------|
| "你好" | "你好" | Le virgolette di apertura e chiusura sono state sostituite con caratteri appropriati. |
| 需要什么帮助? | 需要什么帮助？ | Il punto interrogativo è stato sostituito con caratteri appropriati. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalizzazione del testo per il cinese mandarino

Normalizzazione del testo è la trasformazione delle parole in un formato coerente usato durante il training di un modello. Alcune regole di normalizzazione vengono applicate automaticamente al testo, tuttavia, è consigliabile usare queste linee guida durante la preparazione dei dati con etichetta umane trascrizione:

* Scrivere le abbreviazioni in parole.
* Scrivere stringhe numeriche in forma parlata.

Ecco alcuni esempi di normalizzazione che è consigliabile eseguire la trascrizione di:

| Testo originale | Testo dopo la normalizzazione |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

Le seguenti regole di normalizzazione vengono applicate automaticamente a trascrizioni:

* Rimuovere tutti i segni di punteggiatura
* Espandere i numeri in forma parlata
* Converte le lettere a larghezza intera a metà larghezza lettere
* Uso delle lettere maiuscole per tutte le parole in inglese

Ecco alcuni esempi di normalizzazione eseguita automaticamente la trascrizione di:

| Testo originale | Testo dopo la normalizzazione |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |w f y z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Tedesco (de-DE) e altri linguaggi

Con l'etichetta umane trascrizioni per audio tedesco (e non in inglese o altre lingue cinese mandarino) devono essere codificato con un byte order mark UTF-8. Per ogni file audio, è necessario specificare una trascrizione con etichetta umane.

### <a name="text-normalization-for-german"></a>Normalizzazione del testo per il tedesco

Normalizzazione del testo è la trasformazione delle parole in un formato coerente usato durante il training di un modello. Alcune regole di normalizzazione vengono applicate automaticamente al testo, tuttavia, è consigliabile usare queste linee guida durante la preparazione dei dati con etichetta umane trascrizione:

*   Scrivere i separatori decimali come ","e non".".
*   Separatori del tempo di scrittura ":"e non"." (ad esempio: 12:00 Uhr).
*   Le abbreviazioni, ad esempio "ca.", non vengono sostituiti. È consigliabile utilizzare la forma parlata completa.
*   I quattro operatori matematici principali (+, -, \* e /) vengono rimossi. È consigliabile sostituirli con la forma scritta: "più (+)," "meno," "" e "geteilt."
*   Vengono rimossi gli operatori di confronto (=, <, e >). È consigliabile sostituirli con "gleich", "kleiner als" e "grösser als".
*   Scrivere le frazioni, ad esempio 3 e 4, in forma scritta (ad esempio: "drei viertel" anziché 3 e 4).
*   Sostituire il simbolo "€" con la forma scritta "Euro."

Ecco alcuni esempi di normalizzazione che è consigliabile eseguire la trascrizione di:

| Testo originale | Testo dopo la normalizzazione di utente | Testo dopo la normalizzazione di sistema |
|---------------|-------------------------------|---------------------------------|
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier |

Le seguenti regole di normalizzazione vengono applicate automaticamente a trascrizioni:

* Usare lettere minuscole per tutto il testo.
* Rimuovere tutti i segni di punteggiatura, compresi i vari tipi di virgolette doppie ("test", 'test', "test" e «testare» sono OK).
* Ignorare le righe con set di caratteri speciali: stato ¤ ¥ ¦ sezione © ª ¬® ° + ² µ × ÿ Ø¬¬.
* Espandere i numeri in forma parlata, tra cui dollaro o importi in Euro.
* Accettare gli umlaut solo per un oggetto, o e si. Gli altri verranno sostituiti da "th" o annullati.

Ecco alcuni esempi di normalizzazione eseguita automaticamente la trascrizione di:

| Testo originale | Testo dopo la normalizzazione |
|---------------|--------------------------|
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

## <a name="next-steps"></a>Fasi successive

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare i dati](how-to-custom-speech-evaluate-data.md)
* [Il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
