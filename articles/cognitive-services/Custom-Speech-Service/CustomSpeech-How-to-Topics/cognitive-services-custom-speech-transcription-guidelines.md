---
title: Linee guida per la trascrizione - Servizio di riconoscimento vocale personalizzato
titlesuffix: Azure Cognitive Services
description: Informazioni su come preparare i dati per il Servizio di riconoscimento vocale personalizzato.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: a5daca2dbb3fc792850264bca7d8b0c59e7c8faa
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220619"
---
# <a name="transcription-guidelines"></a>Indicazioni sulla trascrizione
Per garantire un uso ottimale dei dati di testo per la personalizzazione dei modelli acustici e linguistici, attenersi alle linee guida sulla trascrizione riportate di seguito. Queste linee guida sono specifiche per la lingua.

## <a name="text-normalization"></a>Normalizzazione del testo

Per un uso ottimale nella personalizzazione di un modello acustico o linguistico, i dati di testo devono essere normalizzati, ovvero trasformati in un formato standard, non ambiguo, leggibile dal sistema. Questa sezione descrive la normalizzazione del testo eseguita dal Servizio di riconoscimento vocale personalizzato al momento dell'importazione dei dati e la normalizzazione del testo che l'utente deve eseguire prima dell'importazione dei dati.

## <a name="inverse-text-normalization"></a>Normalizzazione del testo inversa

Il processo di conversione di testo non elaborato e non formattato in testo formattato, ad esempio con maiuscole e punteggiatura, è denominato normalizzazione del testo inversa. Viene eseguito sui risultati restituiti dall'API Riconoscimento vocale di Servizi cognitivi Microsoft. Un endpoint personalizzato distribuito tramite il Servizio di riconoscimento vocale personalizzato usa lo stesso processo di normalizzazione del testo inversa applicato dall'API Riconoscimento vocale di Servizi cognitivi Microsoft. Tuttavia, questo servizio non supporta attualmente la normalizzazione del testo inversa personalizzata, pertanto i nuovi termini introdotti da un modello linguistico personalizzato non saranno formattati nei risultati del riconoscimento.

## <a name="transcription-guidelines-for-en-us"></a>Linee guida sulla trascrizione per en-US

I dati di testo caricati in questo servizio devono essere scritti in testo normale usando solo il set di caratteri ASCII stampabili. Ogni riga del file deve contenere il testo per una singola espressione.

È importante evitare l'uso di caratteri di punteggiatura Unicode. Questi caratteri possono essere inclusi accidentalmente se si preparano i dati in un programma di elaborazione testi o si estrapolano dati da pagine Web. Sostituire questi caratteri con elementi sostitutivi ASCII appropriati. Ad esempio: 

| Unicode da evitare | Sostituzione ASCII |
|----- | ----- |
| “Hello world” (virgolette doppie di apertura e chiusura) | "Hello world" (virgolette doppie) |
| John’s day (virgoletta singola destra) | John's day (apostrofo) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Normalizzazione del testo eseguita dal Servizio di riconoscimento vocale personalizzato

Il servizio eseguirà la normalizzazione del testo seguente sui dati importati come set di dati linguistici o sulle trascrizioni di un set di dati acustici. Sono inclusi:

*   Lettere minuscole per tutto il testo
*   Rimozione di tutti i segni di punteggiatura tranne apostrofi interni alla parola
*   Espansione dei numeri alla forma orale, compresi gli importi in dollari

Di seguito sono riportati alcuni esempi

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| Starbucks Coffee | starbucks coffee |
| “Holy cow!” said Batman. | holy cow said batman |
| “What?” said Batman's sidekick, Robin. | what said batman’s sidekick robin |
| Go get -em! | go get em |
| I’m double-jointed | i’m double jointed |
| 104 Main Street | one oh four main street |
| Tune to 102.7 | tune to one oh two point seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

### <a name="text-normalization-required-by-users"></a>Normalizzazione del testo richiesta dagli utenti

Per garantire l'utilizzo ottimale dei dati, è necessario applicare ai dati le regole di normalizzazione seguenti prima di importarli.

*   Le abbreviazioni devono essere scritte a parole per riflettere la forma orale
*   Le stringhe numeriche non standard devono essere scritte a parole
*   Le parole con caratteri non alfabetici o con caratteri alfanumerici misti devono essere trascritte come vengono pronunciate
*   Gli acronimi comuni possono essere lasciati come singola entità senza punti o spazi tra le lettere, ma tutti gli altri acronimi devono essere scritti in lettere separate, con ogni lettera separata da uno spazio singolo

Di seguito sono riportati alcuni esempi

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr. Strangelove | Doctor Strangelove |
| James Bond 007 | james bond double oh seven |
| Ke$ha | Ke$ha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>Linee guida sulla trascrizione per zh-CN

I dati di testo caricati nel Servizio di riconoscimento vocale personalizzato devono usare la **codifica UTF-8 (con BOM)**. Ogni riga del file deve contenere il testo per una singola espressione.

È importante evitare l'uso di caratteri di punteggiatura a mezza larghezza. Questi caratteri possono essere inclusi accidentalmente se si preparano i dati in un programma di elaborazione testi o si estrapolano dati da pagine Web. Sostituire questi caratteri con elementi appropriati a larghezza intera. Ad esempio: 

| Unicode da evitare | Sostituzione ASCII |
|----- | ----- |
| “你好” (virgolette doppie di apertura e chiusura) | "你好" (virgolette doppie) |
| 需要什么帮助? (punto interrogativo) | 需要什么帮助？ |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Normalizzazione del testo eseguita dal Servizio di riconoscimento vocale personalizzato

Il servizio di riconoscimento vocale eseguirà la normalizzazione del testo seguente sui dati importati come set di dati linguistici o sulle trascrizioni di un set di dati acustici. Sono inclusi:

*   Rimozione di tutti i segni di punteggiatura
*   Espansione dei numeri alla forma parlata
*   Conversione di lettere a tutta larghezza in lettere a mezza larghezza
*   Lettera maiuscola per tutte le parole inglesi

Di seguito sono riportati alcuni esempi:

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | w f y z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗 ? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-required-by-users"></a>Normalizzazione del testo richiesta dagli utenti

Per garantire l'utilizzo ottimale dei dati, è necessario applicare ai dati le regole di normalizzazione seguenti _prima_ di importarli.

*   Le abbreviazioni devono essere scritte a parole per riflettere la forma orale
*   Questo servizio non copre tutte le quantità numeriche. È più affidabile scrivere stringhe numeriche in forma parlata

Di seguito sono riportati alcuni esempi

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>Linee guida sulla trascrizione per de-DE

I dati di testo caricati nel Servizio di riconoscimento vocale personalizzato devono usare esclusivamente la **codifica UTF-8 (con BOM)**. Ogni riga del file deve contenere il testo per una singola espressione.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Normalizzazione del testo eseguita dal Servizio di riconoscimento vocale personalizzato

Il servizio eseguirà la normalizzazione del testo seguente sui dati importati come set di dati linguistici o sulle trascrizioni di un set di dati acustici. Sono inclusi:

*   Lettere minuscole per tutto il testo
*   Rimozione di tutti i segni di punteggiatura, incluse le virgolette inglesi o tedesche ("test", 'test', "test„ o «test» vanno bene)
*   Eliminazione di qualsiasi riga contenente caratteri speciali, inclusi: ^ ¢ £ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬
*   Espansione dei numeri alla forma scritta, compresi gli importi in dollari o in euro
*   Gli umlaut sono accettati solo per a, o, u; gli altri saranno sostituiti da "th" o scartati

Di seguito sono riportati alcuni esempi

| Testo originale | Dopo la normalizzazione |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |
| Das macht $10 | das macht zehn dollars |


### <a name="text-normalization-required-by-users"></a>Normalizzazione del testo richiesta dagli utenti

Per garantire l'utilizzo ottimale dei dati, è necessario applicare ai dati le regole di normalizzazione seguenti prima di importarli.

*   Il separatore decimale deve essere "," e non ".", ad esempio 2,3% e non 2.3%
*   Il separatore tra ore e minuti deve essere ":" e non ".", ad esempio 12:00 Uhr
*   Le abbreviazioni, ad esempio 'ca.', 'bzw' non vengono sostituite. Per avere la pronuncia corretta, è consigliabile usare la forma estesa.
*   I cinque principali operatori matematici vengono eliminati: +, -, \*, /.
 Si consiglia di sostituirli con la loro forma letterale: plus, minus, mal, geteilt.
*   Ciò vale anche per gli operatori di confronto (=, <, >): gleich, kleiner als, grösser als
*   Usare le frazioni, come 3/4, in forma scritta, ad esempio "drei viertel" invece di ¾
*   Sostituire il simbolo di € con il formato scritto "Euro"


Di seguito sono riportati alcuni esempi

| Testo originale | Dopo la normalizzazione dell'utente | Dopo la normalizzazione del sistema
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| Das macht 12€ | Das macht 12 Euros | das macht zwölf euros |



## <a name="next-steps"></a>Passaggi successivi
* [Come usare un endpoint personalizzato per il riconoscimento vocale](cognitive-services-custom-speech-create-endpoint.md)
* Migliorare l'accuratezza con un [modello acustico personalizzato](cognitive-services-custom-speech-create-acoustic-model.md)
* Migliorare l'accuratezza con un [modello linguistico personalizzato](cognitive-services-custom-speech-create-language-model.md)
