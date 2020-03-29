---
title: Speech Synthesis Markup Language (SSML) - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Uso di Speech Synthesis Markup Language per controllare la pronuncia e la prosodia nella sintesi vocale.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: 7d5dd79399b15ade90173a55aeb71dacbc61fa78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80365818"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Migliorare la sintesi con Speech Synthesis Markup Language (SSML)

Speech Synthesis Markup Language (SSML) è un linguaggio di markup basato su XML che consente agli sviluppatori di specificare come il testo di input viene convertito in sintesi vocale utilizzando il servizio di sintesi vocale. Rispetto al testo normale, SSML consente agli sviluppatori di ottimizzare l'altezza, la pronuncia, la velocità di pronuncia, il volume e altro ancora dell'output di sintesi vocale. La punteggiatura normale, ad esempio la pausa dopo un periodo, o l'utilizzo dell'intonazione corretta quando una frase termina con un punto interrogativo vengono gestiti automaticamente.

L'implementazione del servizio di riconoscimento vocale di SSML si basa sulla [versione 1.0](https://www.w3.org/TR/speech-synthesis)del linguaggio di sintesi vocale del World Wide Web Consortium.

> [!IMPORTANT]
> I caratteri cinese, giapponese e coreano vengono conteggiati come due caratteri per la fatturazione. Per altre informazioni, vedere [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Voci standard, neurali e personalizzate

Scegli tra voci standard e neurali o crea la tua voce personalizzata unica per il tuo prodotto o marchio. Sono disponibili oltre 75 voci standard in più di 45 lingue e impostazioni locali e 5 voci neurali sono disponibili in quattro lingue e impostazioni locali. Per un elenco completo delle lingue supportate, delle impostazioni locali e delle voci (neurale e standard), vedere [Supporto per le lingue](language-support.md).

Per ulteriori informazioni sulle voci standard, neurali e personalizzate, vedere [Sintesi](text-to-speech.md)della sintesi vocale .

## <a name="special-characters"></a>Caratteri speciali

Durante l'utilizzo di SSML, tenere presente che i caratteri speciali, ad esempio virgolette, apostrofi e parentesi quadre, devono essere sottoposti a escape. Per ulteriori informazioni, vedere [XML (Extensible Markup Language) 1.0: Appendice D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Elementi SSML supportati

Ogni documento SSML viene creato con elementi SSML (o tag). Questi elementi vengono utilizzati per regolare tono, prosodia, volume e altro ancora. Nelle sezioni seguenti viene descritto in dettaglio come viene utilizzato ogni elemento e quando un elemento è obbligatorio o facoltativo.  

> [!IMPORTANT]
> Non dimenticare di usare le virgolette doppie intorno ai valori degli attributi. Gli standard per xml valido e ben formato richiedono che i valori degli attributi siano racchiusi tra virgolette doppie. Ad esempio, `<prosody volume="90">` è un elemento valido `<prosody volume=90>` e ben formato, ma non lo è. SSML potrebbe non riconoscere i valori di attributo che non sono tra virgolette.

## <a name="create-an-ssml-document"></a>Creare un documento SSMLCreate an SSML document

`speak`è l'elemento radice ed è **obbligatorio** per tutti i documenti SSML. L'elemento `speak` contiene informazioni importanti, ad esempio la versione, il linguaggio e la definizione del vocabolario di markup.

**Sintassi**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `version` | Indica la versione della specifica SSML utilizzata per interpretare il markup del documento. La versione corrente è 1.0. | Obbligatoria |
| `xml:lang` | Specifica la lingua del documento radice. Il valore può contenere un codice lingua minuscolo `en`di due lettere (ad esempio, ) `en-US`o il codice lingua e il paese maiuscolo (ad esempio, ). | Obbligatoria |
| `xmlns` | Specifica l'URI del documento che definisce il vocabolario di markup (i tipi di elemento e i nomi degli attributi) del documento SSML. L'URI http://www.w3.org/2001/10/synthesiscorrente è . | Obbligatoria |

## <a name="choose-a-voice-for-text-to-speech"></a>Scegliere una voce per la sintesi vocale

L'elemento `voice` è obbligatorio. Viene utilizzato per specificare la voce utilizzata per la sintesi vocale.

**Sintassi**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `name` | Identifica la voce utilizzata per l'output di sintesi vocale. Per un elenco completo delle voci supportate, vedere [Supporto della lingua](language-support.md#text-to-speech). | Obbligatoria |

**Esempio**

> [!NOTE]
> In questo `en-US-AriaRUS` esempio viene utilizzata la voce. Per un elenco completo delle voci supportate, vedere [Supporto della lingua](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Usare più voci

All'interno dell'elemento, `speak` è possibile specificare più voci per l'output di sintesi vocale. Queste voci possono essere in diverse lingue. Per ogni voce, il testo `voice` deve essere mandato a capo in un elemento. 

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `name` | Identifica la voce utilizzata per l'output di sintesi vocale. Per un elenco completo delle voci supportate, vedere [Supporto della lingua](language-support.md#text-to-speech). | Obbligatoria |

> [!IMPORTANT]
> Le voci multiple non sono compatibili con la funzione di contorno della parola. La funzione di confine di parole deve essere disabilitata per poter utilizzare più voci.

### <a name="disable-word-boundary"></a>Disattiva confine parola

A seconda del linguaggio Speech SDK, la `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` proprietà verrà impostata su `false` un'istanza dell'oggetto. `SpeechConfig`

# <a name="c"></a>[C #](#tab/csharp)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Repentino](#tab/swift)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Regolare gli stili di lingua

> [!IMPORTANT]
> La regolazione degli stili di conversazione funzionerà solo con le voci neurali.

Per impostazione predefinita, il servizio di sintesi vocale sintetizza il testo usando uno stile di parlare neutro sia per le voci standard che per le voci neurali. Con le voci neurali, è possibile regolare lo stile di parlare `<mstts:express-as>` per esprimere allegria, empatia o sentimento con l'elemento. Si tratta di un elemento facoltativo univoco per il servizio di riconoscimento vocale.

Attualmente, le regolazioni di stile di parlare sono supportate per queste voci neurali:
* `en-US-AriaNeural`
* `zh-CN-XiaoxiaoNeural`
* `pt-BR-FranciscaNeural`

Le modifiche vengono applicate a livello di frase e lo stile varia in base alla voce. Se uno stile non è supportato, il servizio restituirà il riconoscimento vocale nello stile di linguaggio neutro predefinito.

**Sintassi**

```xml
<mstts:express-as style="string"></mstts:express-as>
```

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `style` | Specifica lo stile di pronuncia. Attualmente, gli stili di conversazione sono specifici della voce. | Obbligatorio se si regola lo stile di conversazione per una voce neurale. Se `mstts:express-as`si utilizza , è necessario specificare lo stile. Se viene fornito un valore non valido, questo elemento verrà ignorato. |

Utilizzare questa tabella per determinare quali stili di parlare sono supportati per ogni voce neurale.

| Chiamata vocale | Style | Descrizione |
|-------|------|-------------|
| `en-US-AriaNeural` | `style="newscast"` | Esprime un tono formale e professionale per narrare le notizie |
| | `style="customerservice"` | Esprime un tono cordiale e utile per l'assistenza clienti |
| | `style="chat"` | Esprime un tono informale e rilassato |
| | `style="cheerful"` | Esprime un tono positivo e felice |
| | `style="empathetic"` | Esprime un senso di cura e comprensione |
| `zh-CN-XiaoxiaoNeural` | `style="newscast"` | Esprime un tono formale e professionale per narrare le notizie |
| | `style="customerservice"` | Esprime un tono cordiale e utile per l'assistenza clienti |
| | `style="assistant"` | Esprime un tono caldo e rilassato per gli assistenti digitali  |
| | `style="lyrical"` | Esprime emozioni in modo melodico e sentimentale |
| `pt-BR-FranciscaNeural` | `style="cheerful"` | Esprime un tono positivo e felice |

**Esempio**

Questo frammento SSML `<mstts:express-as>` illustra come l'elemento viene `cheerful`utilizzato per modificare lo stile di pronuncia in .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Aggiungere o rimuovere un'interruzione o una pausa

Utilizzare `break` l'elemento per inserire pause (o interruzioni) tra le parole o impedire che le pause vengano aggiunte automaticamente dal servizio di sintesi vocale.

> [!NOTE]
> Utilizzare questo elemento per ignorare il comportamento predefinito della sintesi vocale (TTS) per una parola o una frase se il discorso sintetizzato per tale parola o frase non è naturale. `strength` Impostare `none` su per evitare un'interruzione prosodica, che viene inserita automaticamente dal servizio di sintesi vocale.

**Sintassi**

```xml
<break strength="string" />
<break time="string" />
```

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `strength` | Specifica la durata relativa di una pausa utilizzando uno dei seguenti valori:<ul><li>none</li><li>x-debole</li><li>Debole</li><li>medio (predefinito)</li><li>complessa</li><li>X-forte</li></ul> | Facoltativo |
| `time` | Specifica la durata assoluta di una pausa in secondi o millisecondi. Esempi di valori `2s` validi sono e`500` | Facoltativo |

| Forza | Descrizione |
|----------|-------------|
| Nessuno, o se nessun valore fornito | 0 ms |
| x-debole | 250 ms |
| Debole | 500 ms |
| media | 750 ms |
| complessa | 1000 ms |
| X-forte | 1250 ms |


**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Specificare paragrafi e frasi

`p`e `s` gli elementi vengono utilizzati per indicare rispettivamente paragrafi e frasi. In assenza di questi elementi, il servizio di sintesi vocale determina automaticamente la struttura del documento SSML.

L'elemento `p` può contenere testo `audio` `break`e `phoneme` `prosody`i `say-as`seguenti elementi: , , , , , `sub` `mstts:express-as`, e `s`.

L'elemento `s` può contenere testo `audio` `break`e i `mstts:express-as`seguenti `sub`elementi: , , `phoneme`, `prosody`, `say-as`, e .

**Sintassi**

```XML
<p></p>
<s></s>
```

**Esempio**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>Utilizzare i fonemi per migliorare la pronuncia

L'elemento `ph` viene utilizzato per la pronuncia fonetica nei documenti SSML. L'elemento `ph` può contenere solo testo, nessun altro elemento. Fornire sempre un discorso leggibile come fallback.

Gli alfabeti fonetici sono composti da telefoni, che sono costituiti da lettere, numeri o caratteri, a volte in combinazione. Ogni telefono descrive un suono unico del discorso. Questo è in contrasto con l'alfabeto latino, dove qualsiasi lettera può rappresentare più suoni vocali. Considerare le diverse pronunce della lettera "c" nelle parole "candy" e "cease", o le diverse pronunce della combinazione di lettere "th" nelle parole "cosa" e "quelle".

**Sintassi**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `alphabet` | Specifica l'alfabeto fonetico da utilizzare per sintetizzare `ph` la pronuncia della stringa nell'attributo. La stringa che specifica l'alfabeto deve essere specificata in lettere minuscole. Di seguito sono riportati i possibili alfabeti che è possibile specificare.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Alfabeto <span class="docon docon-navigate-external x-hidden-focus"></span> fonetico internazionale</a></li><li>`sapi`&ndash; [Alfabeto fonetico del servizio vocale](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; Set di telefoni universali</li></ul><br>L'alfabeto si `phoneme` applica solo all'elemento nell'elemento. | Facoltativo |
| `ph` | Stringa contenente telefoni che specificano la pronuncia `phoneme` della parola nell'elemento. Se la stringa specificata contiene telefoni non riconosciuti, il servizio di sintesi vocale (TTS) rifiuta l'intero documento SSML e non produce alcun output vocale specificato nel documento. | Obbligatorio se si utilizzano fonemi. |

**Esempi**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Utilizzare un lessico personalizzato per migliorare la pronuncia

A volte la parola TTS non è in grado di pronunciare con precisione una parola, ad esempio un nome di società o di un nome straniero. Gli sviluppatori possono definire la lettura di queste `phoneme` `sub` entità in SSML usando e taggare o definire la `lexicon` lettura di più entità facendo riferimento a un file di lessico personalizzato usando tag.

**Sintassi**

```XML
<lexicon uri="string"/>
```

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `uri` | Indirizzo del documento PLS esterno. | Obbligatorio. |

**Utilizzo**

Passaggio 1: Definire un lessico personalizzatoStep 1: Define custom lexicon 

È possibile definire la lettura delle entità tramite un elenco di elementi lessico personalizzato, archiviati come file con estensione xml o pls.

**Esempio**

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

Ogni `lexeme` elemento è un elemento lessico. `grapheme`contiene testo che descrive l'ortografo di `lexeme`. Il modulo di lettura `alias`può essere fornito come . La stringa del `phoneme` telefono potrebbe essere fornita nell'elemento.

L'elemento `lexicon` contiene `lexeme` almeno un elemento. Ogni `lexeme` elemento contiene `grapheme` almeno un elemento `grapheme` `alais`e `phoneme` uno o più elementi , e . L'elemento `grapheme` contiene testo che descrive <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">l'ortografia <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>. Gli `alias` elementi vengono utilizzati per indicare la pronuncia di un acronimo o di un termine abbreviato. L'elemento `phoneme` fornisce testo `lexeme` che descrive come viene pronunciato.

Per ulteriori informazioni sul file lexicon personalizzato, vedere [Pronunciation Lexicon Specification (PLS) Version 1.0](https://www.w3.org/TR/pronunciation-lexicon/) sul sito Web W3C.

Passaggio 2: Caricare il file di lessico personalizzato creato nel passaggio 1 online, è possibile archiviarlo ovunque e si consiglia di archiviarlo in Microsoft Azure, ad esempio Archiviazione BLOB di [Azure.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

Passaggio 3: Fare riferimento al file di lessico personalizzato in SSMLStep 3: Refer to custom lexicon file in SSML

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW" verrà letto come "A proposito". "Benigni" sarà letto con l'IPA a condizione che l'IPA "b" sia il "b"niànji".  

**Limitazione**
- Dimensione del file: il limite massimo di dimensione del file lexicon personalizzato è 100 KB, se oltre questa dimensione, la richiesta di sintesi avrà esito negativo.
- Aggiornamento della cache del lexicon: il lessico personalizzato verrà memorizzato nella cache con URI come chiave nel servizio di servizi di gestione catalogo al primo utilizzo. Lexicon con lo stesso URI non verrà ricaricato entro 15 min, pertanto la modifica del lessico personalizzato deve attendere al massimo 15 min per rendere effettive.

**Set fonetici del servizio di riconoscimento vocale**

Nell'esempio precedente, stiamo usando l'alfabeto fonetico internazionale, noto anche come set di telefoni IPA. Suggeriamo agli sviluppatori di utilizzare l'IPA, perché è lo standard internazionale. Considerando che l'IPA non è facile da ricordare, il servizio`en-US` `fr-FR`di `de-DE` `es-ES`riconoscimento vocale definisce un set fonetico per sette lingue ( , , , , `ja-JP`, `zh-CN`, e `zh-TW`).

È possibile `sapi` utilizzare il come `alphabet` vale per l'attributo con lessici personalizzati come illustrato di seguito:You can use the as the vale for the attribute with custom lexicons as demonstrated below:

```xml
<?xml version="1.0" encoding="UTF-16"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Per ulteriori informazioni sull'alfabeto fonetico del servizio di riconoscimento vocale dettagliato, vedere i [set fonetici](speech-ssml-phonetic-sets.md)del servizio di riconoscimento vocale .

## <a name="adjust-prosody"></a>Regolare la prosodia

L'elemento `prosody` viene utilizzato per specificare le modifiche apportate a passo, contorno, intervallo, frequenza, durata e volume per l'output di sintesi vocale. L'elemento `prosody` può contenere testo `audio` `break`e `p` `phoneme`i `prosody`seguenti elementi: , , , , , `say-as` `sub`, e `s`.

Poiché i valori degli attributi prosodici possono variare su un ampio intervallo, il sistema di riconoscimento vocale interpreta i valori assegnati come suggerimento dei valori prosodici effettivi della voce selezionata. Il servizio di sintesi vocale limita o sostituisce i valori non supportati. Esempi di valori non supportati sono un'altezza di 1 MHz o un volume di 120.Examples of unsupported values are a pitch of 1 MHz or a volume of 120.

**Sintassi**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `pitch` | Indica l'altezza della linea di base per il testo. Puoi esprimere il passo come:<ul><li>Un valore assoluto, espresso come numero seguito da "Hz" (Hertz). Ad esempio, 600 Hz.</li><li>Valore relativo, espresso come numero preceduto da "" o "-" e seguito da "Hz" o "st", che specifica un importo per modificare il passo. Ad esempio: 80 Hz o -2st. La "st" indica che l'unità di cambiamento è semitonica, che è la metà di un tono (un mezzo passo) sulla scala diatonica standard.</li><li>Valore costante:<ul><li>x-basso</li><li>low</li><li>media</li><li>high</li><li>x-alto</li><li>default</li></ul></li></ul>. | Facoltativo |
| `contour` | Il contorno non è supportato per le voci neurali. Contorno rappresenta cambiamenti di passo. Queste modifiche sono rappresentate come una matrice di destinazioni in posizioni temporali specificate nell'output vocale. Ogni destinazione è definita da set di coppie di parametri. Ad esempio: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Il primo valore in ogni set di parametri specifica la posizione della modifica dell'altezza come percentuale della durata del testo. Il secondo valore specifica la quantità da alzare o abbassare il passo, `pitch`utilizzando un valore relativo o un valore di enumerazione per il passo (vedere ). | Facoltativo |
| `range` | Valore che rappresenta l'intervallo di altezza per il testo. È possibile `range` esprimere utilizzando gli stessi valori assoluti, relativi o valori di enumerazione utilizzati per descrivere `pitch`. | Facoltativo |
| `rate` | Indica la velocità di pronuncia del testo. Puoi esprimere: `rate`<ul><li>Valore relativo, espresso come numero che funge da moltiplicatore dell'impostazione predefinita. Ad esempio, un valore pari a *1* non comporta alcuna modifica della tariffa. Un valore pari a *0,5* determina una dimezzamento del tasso. Un valore pari a *3* determina una triplicazione della tariffa.</li><li>Valore costante:<ul><li>x-lento</li><li>lento</li><li>media</li><li>veloce</li><li>x-fast</li><li>default</li></ul></li></ul> | Facoltativo |
| `duration` | Periodo di tempo che deve trascorrere mentre il servizio di sintesi vocale (TTS) legge il testo, in secondi o millisecondi. Ad esempio, *2s* o *1800ms*. | Facoltativo |
| `volume` | Indica il livello di volume della voce parlante. È possibile esprimere il volume come:<ul><li>Un valore assoluto, espresso come numero compreso tra 0,0 e 100,0, dal *più tranquillo* al *più forte.* Ad esempio, 75. Il valore predefinito è 100.0.The default is 100.0.</li><li>Valore relativo, espresso come numero preceduto da "" o "-" che specifica un importo per modificare il volume. Ad esempio, 10 usd o -5,5.</li><li>Valore costante:<ul><li>nessun suono</li><li>x-soft</li><li>Morbido</li><li>media</li><li>Forte</li><li>x-loud</li><li>default</li></ul></li></ul> | Facoltativo |

### <a name="change-speaking-rate"></a>Cambiare la velocità del parlato

La velocità di pronuncia può essere applicata alle voci standard a livello di parola o di frase. Mentre la velocità di parola può essere applicata solo alle voci neurali a livello di frase.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Cambiare il volume

Le modifiche di volume possono essere applicate alle voci standard a livello di parola o di frase. Mentre le modifiche di volume possono essere applicate solo alle voci neurali a livello di frase.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Cambiare il tono

Le modifiche di passo possono essere applicate alle voci standard a livello di parola o di frase. Mentre le modifiche di tono possono essere applicate solo alle voci neurali a livello di frase.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Cambiare il profilo di intonazione

> [!IMPORTANT]
> Le modifiche al contorno del passo non sono supportate con le voci neurali.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>elemento say-as

`say-as`è un elemento facoltativo che indica il tipo di contenuto (ad esempio numero o data) del testo dell'elemento. In questo modo vengono fornite indicazioni al motore di sintesi vocale su come pronunciare il testo.

**Sintassi**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `interpret-as` | Indica il tipo di contenuto del testo dell'elemento. Per un elenco dei tipi, vedere la tabella seguente. | Obbligatoria |
| `format` | Fornisce informazioni aggiuntive sulla formattazione precisa del testo dell'elemento per i tipi di contenuto che possono avere formati ambigui. SSML definisce i formati per i tipi di contenuto che li utilizzano (vedere la tabella seguente). | Facoltativo |
| `detail` | Indica il livello di dettaglio da pronunciare. Ad esempio, questo attributo potrebbe richiedere che il motore di sintesi vocale pronunci segni di punteggiatura. Non sono definiti valori `detail`standard per . | Facoltativo |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Di seguito sono riportati `interpret-as` i `format` tipi di contenuto supportati per gli attributi e . Includere `format` l'attributo solo se `interpret-as` è impostato su data e ora.

| interpreti-come | format | Interpretazione |
|--------------|--------|----------------|
| `address` | | Il testo viene pronunciato come indirizzo. Il motore di sintesi vocale pronuncia:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Come "Sono alla 150a corte a nord est rosso Washington." |
| `cardinal`, `number` | | Il testo viene pronunciato come numero cardinale. Il motore di sintesi vocale pronuncia:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Come "Ci sono tre alternative." |
| `characters`, `spell-out` | | Il testo viene pronunciato come singole lettere (scritto). Il motore di sintesi vocale pronuncia:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Come "T E S T." |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | Il testo viene pronunciato come data. L'attributo `format` specifica il formato della data (*d, giorno, m , mese e y*, anno ). Il motore di sintesi vocale pronuncia:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Come "Oggi è ottobre 19 duemila sedici." |
| `digits`, `number_digit` | | Il testo viene pronunciato come una sequenza di singole cifre. Il motore di sintesi vocale pronuncia:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Come "1 2 3 4 5 6 7 8". |
| `fraction` | | Il testo viene pronunciato come numero frazionario. Il motore di sintesi vocale pronuncia:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Come "tre ottavi di pollice." |
| `ordinal` | | Il testo viene pronunciato come numero ordinale. Il motore di sintesi vocale pronuncia:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Come "Seleziona la terza opzione". |
| `telephone` | | Il testo viene pronunciato come numero di telefono. L'attributo `format` può contenere cifre che rappresentano un codice paese. Ad esempio, "1" per gli Stati Uniti o "39" per l'Italia. Il motore di sintesi vocale può utilizzare queste informazioni per guidare la sua pronuncia di un numero di telefono. Il numero di telefono può anche includere il codice del paese e, in tal caso, ha la precedenza sul codice del paese nel `format`file . Il motore di sintesi vocale pronuncia:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Come "Il mio numero è prefisso otto otto otto cinque cinque cinque uno due due due." |
| `time` | hms12, hms24 | Il testo è pronunciato come un tempo. L'attributo `format` specifica se l'ora viene specificata utilizzando un orologio a 12 ore (hms12) o un orologio a 24 ore (hms24). Utilizzare i due punti per separare i numeri che rappresentano ore, minuti e secondi. Di seguito sono riportati esempi di tempo validi: 12:35, 1:14:32, 08:15 e 02:50:45. Il motore di sintesi vocale pronuncia:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Come "Il treno parte alle quattro A M." |

**Utilizzo**

L'elemento `say-as` può contenere solo testo.

**Esempio**

Il motore di sintesi vocale parla il seguente esempio come "La tua prima richiesta è stata per una stanza il 19 ottobre ventidieci e arrivo anticipato alle dodici e trentacinque di premier".
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Aggiungere l'audio registrato

`audio`è un elemento facoltativo che consente di inserire audio MP3 in un documento SSML. Il corpo dell'elemento audio può contenere testo normale o markup SSML che viene pronunciato se il file audio non è disponibile o non è riproducibile. Inoltre, `audio` l'elemento può contenere testo `audio` `break`e `p` `s`i `phoneme`seguenti elementi: , , , , , `prosody`, `say-as`e `sub`.

Qualsiasi audio incluso nel documento SSML deve soddisfare i seguenti requisiti:

* L'MP3 deve essere ospitato in un endpoint HTTPS accessibile da Internet.The MP3 must be hosted on an Internet-accessible HTTPS endpoint. HTTPS è obbligatorio e il dominio che ospita il file MP3 deve presentare un certificato TLS/SSL valido e attendibile.
* MP3 deve essere un file MP3 valido (MPEG v2).
* La velocità in bit deve essere di 48 kbps.
* La frequenza di campionamento deve essere di 16.000 Hz.
* Il tempo totale combinato per tutti i file di testo e audio in una singola risposta non può superare i novanta (90) secondi.
* L'MP3 non deve contenere informazioni riservate specifiche del cliente o altre.

**Sintassi**

```xml
<audio src="string"/></audio>
```

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `src` | Specifica la posizione/URL del file audio. | Obbligatorio se si utilizza l'elemento audio nel documento SSML. |

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Aggiungere l'audio di sfondo

L'elemento `mstts:backgroundaudio` consente di aggiungere audio di sfondo ai documenti SSML (o mixare un file audio con sintesi vocale). Con `mstts:backgroundaudio` è possibile riprodurre in loop un file audio in background, dissolvenza in entrata all'inizio della sintesi vocale e dissolvenza in uscita alla fine della sintesi vocale.

Se l'audio di sfondo fornito è più breve della sintesi vocale o della dissolvenza in uscita, verrà eseguito un ciclo continuo. Se è più lungo della sintesi vocale, verrà interrotta al termine della dissolvenza in uscita.

È consentito un solo file audio in background per documento SSML. Tuttavia, è possibile `audio` interspersioni di tag all'interno dell'elemento `voice` per aggiungere audio aggiuntivo al documento SSML.

**Sintassi**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attributi**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `src` | Specifica la posizione/URL del file audio in background. | Obbligatorio se si utilizza l'audio in background nel documento SSML. |
| `volume` | Specifica il volume del file audio in background. **Valori accettati**: `0` a `100` inclusive. Il valore predefinito è `1`. | Facoltativo |
| `fadein` | Specifica la durata della "dissolvenza in entrata" dell'audio di sfondo come millisecondi. Il valore `0`predefinito è , che equivale a non applicare alcuna dissolvenza in entrata. **Valori accettati**: `0` a `10000` inclusive.  | Facoltativo |
| `fadeout` | Specifica la durata della dissolvenza in uscita dell'audio di sfondo in millisecondi. Il valore `0`predefinito è , che equivale a non effettuare la dissolvenza in uscita. **Valori accettati**: `0` a `10000` inclusive.  | Facoltativo |

**Esempio**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Passaggi successivi

* [Supporto per le lingue: voci, impostazioni locali, lingue](language-support.md)
