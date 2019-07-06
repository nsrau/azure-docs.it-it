---
title: Riconoscimento vocale sintesi Markup Language (SSML) - servizi di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Uso di Speech Synthesis Markup Language per controllare la pronuncia e la prosodia nella sintesi vocale.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8285a76f8cd07863874f9c8e8eebe96f1cb968dd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604827"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Linguaggio di Markup sintesi della voce (SSML) è un linguaggio di markup basato su XML che consente agli sviluppatori di specificare il testo come input viene convertito in sintesi vocale, utilizzando il servizio di sintesi vocale. Rispetto al testo normale, SSML consente agli sviluppatori di ottimizzare il tono, pronuncia, velocità di pronuncia, volume e più output di sintesi vocale. Punteggiatura normali, ad esempio tramite l'intonazione corretto quando si termina una frase con un punto interrogativo o sospensione dopo un periodo vengono gestiti automaticamente.

L'implementazione di servizi di riconoscimento vocale di SSML si basa sul World Wide Web Consortium [vocale sintesi Markup Language versione 1.0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Numero di caratteri cinese, giapponese e coreano come due caratteri per la fatturazione. Per altre informazioni, vedere [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Voices standard neurale e personalizzati

Scegliere tra standard e neurale voices oppure creare la tua voce personalizzato univoco per un prodotto o un marchio. 75 voices standard sono disponibili in più di 45 lingue e impostazioni locali e 5 voices neurale sono disponibili in 4 lingue e impostazioni locali. Per un elenco completo delle lingue supportate, delle impostazioni locali e delle voci (neurale e standard), vedere [Supporto per le lingue](language-support.md).

Per altre informazioni su standard, neurale e voci personalizzate, vedere [Panoramica di sintesi vocale](text-to-speech.md).

## <a name="supported-ssml-elements"></a>Elementi supportati SSML

Ogni documento SSML viene creato con gli elementi SSML (o i tag). Questi elementi vengono usati per regolare il passo, prosodia, volume e altro ancora. Le sezioni seguenti illustrano come ogni elemento viene usato, e quando un elemento è obbligatorio o facoltativo.  

> [!IMPORTANT]
> Non dimenticare di usare le virgolette doppie per racchiudere i valori di attributo. Gli standard per XML ben formato, valido richiede valori di attributo per essere racchiuso tra virgolette doppie. Ad esempio, `<prosody volume="90">` è un elemento corretto e valido, ma `<prosody volume=90>` non. SSML potrebbe non riconoscere i valori di attributo che non sono racchiusi tra virgolette.

## <a name="create-an-ssml-document"></a>Creare un documento SSML

`speak` è l'elemento radice e viene **obbligatorio** per tutti i documenti SSML. Il `speak` elemento contiene informazioni importanti, ad esempio versione, lingua e la definizione del vocabolario di markup.

**Sintassi**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| version | Indica la versione della specifica SSML usata per interpretare il markup di documento. La versione corrente è 1.0. | Obbligatorio |
| xml:lang | Specifica la lingua del documento radice. Il valore può contenere un codice di linguaggio due lettere minuscole (ad esempio, **en**), o il codice della lingua e il paese/regione lettere maiuscole (ad esempio **en-US**). | Obbligatorio |
| xmlns | Specifica l'URI per il documento che definisce il vocabolario di markup (tipi di elemento e i nomi degli attributi) del documento SSML. L'URI corrente è https://www.w3.org/2001/10/synthesis. | Obbligatorio |

## <a name="choose-a-voice-for-text-to-speech"></a>Scegliere una voce per la sintesi vocale

Il `voice` elemento è obbligatorio. Viene utilizzato per specificare la voce che viene usata per la sintesi vocale.

**Sintassi**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| name | Identifica la voce usata per l'output di sintesi vocale. Per un elenco completo delle voci supportati, vedere [supporto delle lingue](language-support.md#text-to-speech). | Obbligatorio |

**Esempio**

> [!NOTE]
> Questo esempio viene usato il `en-US-Jessa24kRUS` vocali. Per un elenco completo delle voci supportati, vedere [supporto delle lingue](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Usare più voci

All'interno di `speak` elemento, è possibile specificare più voci per l'output di sintesi vocale. Queste voci possono trovarsi in diverse lingue. Per ogni voce, il testo deve essere racchiuse in un `voice` elemento.

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| name | Identifica la voce usata per l'output di sintesi vocale. Per un elenco completo delle voci supportati, vedere [supporto delle lingue](language-support.md#text-to-speech). | Obbligatorio |

**Esempio**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Modificare gli stili di pronuncia

> [!IMPORTANT]
> Questa funzionalità funziona solo con le voci neurale.

Per impostazione predefinita, il servizio di sintesi vocale sintetizza testo utilizzando un modo di parlare neutra per standard e neurale voci. Con le voci neurale, è possibile modificare il modo di parlare per esprimere cheerfulness, empatia o dei sentimenti con la `<mstts:express-as>` elemento. Si tratta di un elemento facoltativo univoco vocale in servizi di Azure.

Pronuncia le regolazioni di stile sono attualmente supportate per queste voci neurale:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

Le modifiche vengono applicate a livello di frase e stile variano in base al vocali. Se uno stile non è supportato, il servizio restituirà vocale predefinita neutre a proposito di stile.

**Sintassi**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| type | Specifica il modo di parlare. Attualmente, gli stili pronuncia sono vocale specifico. | Obbligatorio se modifica il modo di parlare di una voce neurale. Se si usa `mstts:express-as`, quindi è necessario specificare tipo. Se viene specificato un valore non valido, questo elemento verrà ignorato. |

Usare questa tabella per determinare quali stili pronuncia sono supportati per ogni voce neurale.

| Chiamata vocale | Type | Descrizione |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Esprime un'emozione che è positivo e soddisfazione |
| | type=`empathy` | Esprime un senso di occuparsi e comprensione |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Esprime un tono formale, simile a trasmissioni notizie |
| | type=`sentiment` | Fornisce un messaggio tocca o una storia |

**Esempio**

Questo frammento di SSML viene illustrato come la `<mstts:express-as>` elemento viene usato per modificare il modo di parlare a `cheerful`.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Aggiungere o rimuovere una pausa/pausa

Uso di `break` elemento da inserire pause (o interruzioni) tra le parole o impedire pause aggiunte automaticamente dal servizio di sintesi vocale.

> [!NOTE]
> Usare questo elemento per eseguire l'override del comportamento predefinito di sintesi vocale (TTS) per una parola o frase se la sintesi vocale per tale parola o frase suona innaturale. Impostare `strength` a `none` per evitare un'interruzione prosodica, che viene inserita automaticamente dal servizio tex vocale.

**Sintassi**

```xml
<break strength="string" />
<break time="string" />
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| forza | Specifica la durata relativa di una sospensione tramite uno dei valori seguenti:<ul><li>Nessuno</li><li>x-"Weak"</li><li>debole</li><li>Media (predefinita)</li><li>Sicuro</li><li>x-strong</li></ul> | Facoltativo |
| time | Specifica la durata di una pausa in secondi o millisecondi assoluta. Esempi di valori validi sono 2s e 500 | Facoltativo |

| forza | Descrizione |
|----------|-------------|
| None, o se nessun valore specificato | 0 ms |
| x-"Weak" | 250 ms |
| debole | 500 ms |
| media | 750 ms |
| Sicuro | 1000 ms |
| x-strong | 1250 ms |


**Esempio**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Specificare i paragrafi e le frasi

`p` e `s` gli elementi vengono usati per indicare i paragrafi e frasi, rispettivamente. In assenza di questi elementi, il servizio di sintesi vocale determina automaticamente la struttura del documento SSML.

Il `p` elemento può contenere testo e gli elementi seguenti: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`, e `s`.

Il `s` elemento può contenere testo e gli elementi seguenti: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`, e `sub`.

**Sintassi**

```XML
<p></p>
<s></s>
```

**Esempio**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Consente di migliorare la pronuncia fonemi

Il `ph` elemento viene usato per la pronuncia fonetica nei documenti SSML. Il `ph` elemento può contenere solo testo, nessun altro elemento. Fornire sempre vocale leggibile dall'utente come fallback.

Alfabeti fonetici sono costituiti da telefoni, sono costituiti da lettere, numeri o caratteri, a volte in combinazione. Ogni telefono descrive un suono univoco di riconoscimento vocale. Ciò è in contrasto alfabeto latino, in cui qualsiasi lettera può rappresentare più suoni vocali. Prendere in considerazione la pronuncia diversi della lettera "c" nella caramelle"parole" e "termine" o la pronuncia diversi di combinazione di lettere "th" in "thing le parole" e "i".

**Sintassi**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| alfabeto | Specifica l'alfabeto fonetico da utilizzare per la sintesi la pronuncia della stringa nel `ph` attributo. Stringa che specifica l'alfabeto deve essere specificata in lettere minuscole. Di seguito sono gli alfabeti possibili che si possono specificare.<ul><li>pacchetto IPA &ndash; alfabeto fonetico internazionale</li><li>SAPI &ndash; Speech API telefono configurato</li><li>gruppo di continuità &ndash; Universal Phone Set</li></ul>L'alfabeto si applica solo a fonema nell'elemento. Per altre informazioni, vedere [alfabeto fonetico riferimento](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Facoltativo |
| ph | Stringa contenente i telefoni che specificano la pronuncia della parola all'interno di `phoneme` elemento. Se la stringa specificata contiene i telefoni non riconosciuti, servizio di sintesi vocale (TTS) rifiuta l'intero documento SSML e produce nessuno dell'output vocale specificata nel documento. | Obbligatorio se si usa fonemi. |

**esempi**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Regolare prosodia

Il `prosody` elemento viene usato per specificare le modifiche di tono, countour, intervallo, frequenza, durata e volume per l'output di sintesi vocale. Il `prosody` elemento può contenere testo e gli elementi seguenti: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`, e `s`.

Poiché i valori di attributo prosodica possono variare per un'ampia gamma, riconoscimento vocale interpreta i valori assegnati come un suggerimento di ciò che i valori effettivi prosodico della voce selezionata devono essere. Il servizio di sintesi vocale limita o sostituisce i valori che non sono supportati. Esempi di valori non supportati sono un tono di MHz 1 o un volume di 120.

**Sintassi**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| Passo | Indica il tono di linea di base per il testo. È possibile esprimere il tono come:<ul><li>Valore assoluto, espresso come numero seguito da "Hz" (Hertz). Ad esempio, 600Hz.</li><li>Valore relativo, espresso come numero preceduto da "+" o "-", seguito da "Hz" o "st", che ne specifica una quantità per modificare il passo. Ad esempio: + 80 Hz o - 2st. "st" indica una che modifica dell'unità è semitono, pari a metà di un segnale acustico (un passaggio di metà) sulla scala diatonic standard.</li><li>Un valore costante:<ul><li>x-basso</li><li>bassa</li><li>media</li><li>elevata</li><li>x-alto</li><li>default</li></ul></li></ul>. | Facoltativo |
| contorno | Distribuzione non è supportata per voices neurale. Contour rappresenta le modifiche nel passo per il contenuto di riconoscimento vocale come una matrice di destinazioni in posizioni di tempo specificato nell'output vocale. Ogni destinazione è definita dai set di coppie di parametri. Ad esempio: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Il primo valore in ogni set di parametri specifica la posizione della modifica del passo come percentuale della durata del testo. Il secondo valore specifica la quantità per aumentare o diminuire il passo, usando un valore di enumerazione o un valore relativo per il passo (vedere `pitch`). | Facoltativo |
| range  | Un valore che rappresenta l'intervallo di passo per il testo. È possibile esprimere `range` usando la stessi valori assoluti, valori relativi o valori di enumerazione utilizzato per descrivere `pitch`. | Facoltativo |
| Frequenza  | Indica la velocità di pronuncia del testo. È possibile esprimere `rate` come:<ul><li>Valore relativo, espresso come un numero che agisce come un moltiplicatore del valore predefinito. Ad esempio, il valore *1* comporta alcuna modifica della frequenza. Un valore pari *,5* comporta un dimezzamento della frequenza. Un valore pari *3* comporta un triplicano della frequenza.</li><li>Un valore costante:<ul><li>x-lenta</li><li>lento</li><li>media</li><li>Fast</li><li>x-fast</li><li>default</li></ul></li></ul> | Facoltativo |
| duration  | Il periodo di tempo che deve trascorrere durante il riconoscimento vocale servizio sintesi (TTS) legge il testo, in secondi o millisecondi. Ad esempio, *2s* oppure *1800ms*. | Facoltativo |
| volume  | Indica il livello di volume della voce parlante. È possibile esprimere il volume come:<ul><li>Un valore assoluto, espresso come numero compreso nell'intervallo di 0.0 a 100,0, dalla *silenziosi* al *più*. Ad esempio 75. Il valore predefinito è 100,0.</li><li>Valore relativo, espresso come numero preceduto da "+" o "-" che specifica una quantità per modificare il volume. Ad esempio + 10 o-5.5.</li><li>Un valore costante:<ul><li>Invisibile all'utente</li><li>x-soft</li><li>soft</li><li>media</li><li>loud</li><li>x-loud</li><li>default</li></ul></li></ul> | Facoltativo |

### <a name="change-speaking-rate"></a>Cambiare la velocità del parlato

Velocità di pronuncia può essere applicato a voices standard a livello di frase o word. Mentre velocità di pronuncia applicabile solo a neurale voices a livello di frase.

**Esempio**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Cambiare il volume

Volume modifiche possono essere applicate a voices standard a livello di frase o word. Mentre le modifiche di volumi è applicabile solo ai neurale voices a livello di frase.

**Esempio**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Cambiare il tono

Modifiche di tono possono essere applicate a voices standard a livello di frase o word. Mentre le modifiche di tono è applicabile solo ai neurale voices a livello di frase.

**Esempio**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Cambiare il profilo di intonazione

> [!IMPORTANT]
> Modifiche di contour passo non sono supportate con voci neurale.

**Esempio**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="next-steps"></a>Passaggi successivi

* [Supporto per le lingue: voci, impostazioni locali, lingue](language-support.md)
