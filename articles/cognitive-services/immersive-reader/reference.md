---
title: Guida di riferimento a immersive Reader SDK
titleSuffix: Azure Cognitive Services
description: Immersivous Reader SDK contiene una libreria JavaScript che consente di integrare il lettore immersivo nell'applicazione.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: dbd5724797fdaf44d147d2f29362b1e5092728dd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761550"
---
# <a name="immersive-reader-javascript-sdk-reference-v11"></a>Guida di riferimento all'SDK per JavaScript immersive (v 1.1)

Immersivous Reader SDK contiene una libreria JavaScript che consente di integrare il lettore immersivo nell'applicazione.

## <a name="functions"></a>Funzioni

L'SDK espone le funzioni:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

<br>

## <a name="launchasync"></a>launchAsync

Avvia il Reader immersivo all'interno `iframe` di un oggetto nell'applicazione Web. Si noti che la dimensione del contenuto è limitata a un massimo di 50 MB.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

#### <a name="launchasync-parameters"></a>Parametri launchAsync

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| `token` | string | Token di autenticazione Azure AD. Per altri dettagli, vedere [How-to create a immersive Reader Resource](./how-to-create-immersive-reader.md) . |
| `subdomain` | string | Sottodominio personalizzato della risorsa Reader immersiva in Azure. Per altri dettagli, vedere [How-to create a immersive Reader Resource](./how-to-create-immersive-reader.md) . |
| `content` | [Contenuto](#content) | Oggetto contenente il contenuto da visualizzare nel lettore immersivo. |
| `options` | [Opzioni](#options) | Opzioni per la configurazione di determinati comportamenti del lettore immersivo. Facoltativo. |

#### <a name="returns"></a>Risultati restituiti

Restituisce un oggetto `Promise<LaunchResponse>` , che viene risolto quando il lettore immersivo viene caricato. Viene `Promise` risolto in un [`LaunchResponse`](#launchresponse) oggetto.

#### <a name="exceptions"></a>Eccezioni

Il valore restituito `Promise` verrà rifiutato con un [`Error`](#error) oggetto se il lettore immersivo non viene caricato. Per ulteriori informazioni, vedere i [codici di errore](#error-codes).

<br>

## <a name="close"></a>chiudi

Chiude il lettore immersivo.

Un caso d'uso di esempio per questa funzione è se il pulsante Esci è nascosto impostando ```hideExitButton: true``` in [Opzioni](#options). Quindi, un pulsante diverso (ad esempio, la freccia indietro di un'intestazione mobile) può chiamare questa ```close``` funzione quando viene fatto clic su di essa.

```typescript
close(): void;
```

<br>

## <a name="immersive-reader-launch-button"></a>Pulsante di avvio del lettore immersivo

L'SDK fornisce lo stile predefinito per il pulsante per l'avvio del lettore immersivo. Usare l' `immersive-reader-button` attributo Class per abilitare questo stile. Per altri dettagli, vedere [How-to customize the immersive Reader Button](./how-to-customize-launch-button.md) .

```html
<div class='immersive-reader-button'></div>
```

#### <a name="optional-attributes"></a>Attributi facoltativi

Usare gli attributi seguenti per configurare l'aspetto del pulsante.

| Attributo | Descrizione |
| --------- | ----------- |
| `data-button-style` | Imposta lo stile del pulsante. Può essere `icon`, `text` o `iconAndText`. Il valore predefinito è `icon`. |
| `data-locale` | Imposta le impostazioni locali. Ad esempio, `en-US` o `fr-FR`. Il valore predefinito è inglese `en` . |
| `data-icon-px-size` | Imposta la dimensione dell'icona in pixel. Il valore predefinito è 20px. |

<br>

## <a name="renderbuttons"></a>renderButtons

La ```renderButtons``` funzione non è necessaria se si usa la Guida [How-to customize the immersive Reader Button](./how-to-customize-launch-button.md) .

Questa funzione ha lo stile e aggiorna gli elementi del pulsante Reader immersive del documento. Se ```options.elements``` viene fornito, i pulsanti verranno sottoposti a rendering all'interno di ogni elemento fornito in ```options.elements``` . L'uso del ```options.elements``` parametro è utile quando nel documento sono presenti più sezioni in cui avviare il lettore immersivo e si desidera un modo semplificato per eseguire il rendering di più pulsanti con lo stesso stile oppure per eseguire il rendering dei pulsanti con un modello di progettazione semplice e coerente. Per usare questa funzione con il parametro [Opzioni renderButtons](#renderbuttons-options) , chiamare ```ImmersiveReader.renderButtons(options: RenderButtonsOptions);``` sul caricamento della pagina, come illustrato nel frammento di codice seguente. In caso contrario, verrà eseguito il rendering dei pulsanti negli elementi del documento con la classe, ```immersive-reader-button``` come illustrato in [How-to customize the immersive Reader Button](./how-to-customize-launch-button.md) .

```typescript
// This snippet assumes there are two empty div elements in
// the page HTML, button1 and button2.
const btn1: HTMLDivElement = document.getElementById('button1');
const btn2: HTMLDivElement = document.getElementById('button2');
const btns: HTMLDivElement[] = [btn1, btn2];
ImmersiveReader.renderButtons({elements: btns});
```

Per altre opzioni di rendering, vedere gli [attributi facoltativi](#optional-attributes) precedenti. Per usare queste opzioni, aggiungere uno qualsiasi degli attributi di opzione a ognuno ```HTMLDivElement``` nel codice HTML della pagina.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

#### <a name="renderbuttons-parameters"></a>Parametri renderButtons

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| `options` | [opzioni di renderButtons](#renderbuttons-options) | Opzioni per la configurazione di determinati comportamenti della funzione renderButtons. Facoltativo. |

### <a name="renderbuttons-options"></a>Opzioni di renderButtons

Opzioni per il rendering dei pulsanti Reader immersivi.

```typescript
{
    elements: HTMLDivElement[];
}
```

#### <a name="renderbuttons-options-parameters"></a>Parametri delle opzioni di renderButtons

| Impostazione | Type | Descrizione |
| ------- | ---- | ----------- |
| Elementi figlio | HTMLDivElement [] | Elementi per il rendering dei pulsanti del lettore immersivi in. |

##### `elements`
```Parameters
Type: HTMLDivElement[]
Required: false
```

<br>

## <a name="launchresponse"></a>LaunchResponse

Contiene la risposta dalla chiamata a `ImmersiveReader.launchAsync` . Si noti che è possibile accedere a un riferimento all'oggetto `iframe` che contiene il lettore immersivo tramite `container.firstChild` .

```typescript
{
    container: HTMLDivElement;
    sessionId: string;
}
```

#### <a name="launchresponse-parameters"></a>Parametri LaunchResponse

| Impostazione | Type | Description |
| ------- | ---- | ----------- |
| contenitore | HTMLDivElement | Elemento HTML che contiene l'iframe del lettore immersivo. |
| sessionID | string | Identificatore univoco globale per questa sessione, usato per il debug. |
 
## <a name="error"></a>Errore

Contiene informazioni su un errore.

```typescript
{
    code: string;
    message: string;
}
```

#### <a name="error-parameters"></a>Parametri di errore

| Impostazione | Type | Descrizione |
| ------- | ---- | ----------- |
| codice | string | Uno di un set di codici di errore. Vedere i [codici di errore](#error-codes). |
| message | string | Rappresentazione leggibile dell'errore. |

#### <a name="error-codes"></a>Codici di errore

| Codice | Descrizione |
| ---- | ----------- |
| BadArgument | L'argomento fornito non è valido `message` . vedere il parametro dell' [errore](#error). |
| Timeout | Non è stato possibile caricare il lettore immersivo entro il timeout specificato. |
| TokenExpired | Il token fornito è scaduto. |
| Sospensione causata dal servizio Microsoft FullText | È stato superato il limite di frequenza delle chiamate. |

<br>

## <a name="types"></a>Tipi

### <a name="content"></a>Contenuto

Contiene il contenuto da visualizzare nel lettore immersivo.

```typescript
{
    title?: string;
    chunks: Chunk[];
}
```

#### <a name="content-parameters"></a>Parametri di contenuto

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| title | string | Testo del titolo visualizzato nella parte superiore del lettore immersivo (facoltativo) |
| blocchi | [Blocco []](#chunk) | Matrice di blocchi |

##### `title`
```Parameters
Type: String
Required: false
Default value: "Immersive Reader" 
```

##### `chunks`
```Parameters
Type: Chunk[]
Required: true
Default value: null 
```

<br>

### <a name="chunk"></a>Chunk

Singolo blocco di dati, che verrà passato al contenuto del lettore immersivo.

```typescript
{
    content: string;
    lang?: string;
    mimeType?: string;
}
```

#### <a name="chunk-parameters"></a>Parametri del blocco

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| contenuto | string | Stringa che contiene il contenuto inviato al lettore immersivo. |
| lang | string | Lingua del testo, il valore è nel formato di tag del linguaggio IETF BCP 47, ad esempio en, es-ES. La lingua verrà rilevata automaticamente se non è specificata. Vedere le [lingue supportate](#supported-languages). |
| mimeType | string | Sono supportati i formati testo normale, MathML, HTML & Microsoft Word DOCX. Per altri dettagli, vedere [tipi MIME supportati](#supported-mime-types) . |

##### `content`
```Parameters
Type: String
Required: true
Default value: null 
```

##### `lang`
```Parameters
Type: String
Required: false
Default value: Automatically detected 
```

##### `mimeType`
```Parameters
Type: String
Required: false
Default value: "text/plain"
```

#### <a name="supported-mime-types"></a>Tipi MIME supportati

| Tipo MIME | Descrizione |
| --------- | ----------- |
| text/plain | Testo normale. |
| text/html | Contenuto HTML. [Altre informazioni](#html-support)|
| Application/MathML + XML | Linguaggio di markup matematico (MathML). [Altre informazioni](./how-to/display-math.md)
| applicazione/vnd.openxmlformats-officedocument.wordprocessingml.document | Documento di formato Microsoft Word. docx.


<br>

## <a name="options"></a>Opzioni

Contiene proprietà che configurano comportamenti specifici del lettore immersivo.

```typescript
{
    uiLang?: string;
    timeout?: number;
    uiZIndex?: number;
    useWebview?: boolean;
    onExit?: () => any;
    allowFullscreen?: boolean;
    hideExitButton?: boolean;
    cookiePolicy?: CookiePolicy;
    disableFirstRun?: boolean;
    readAloudOptions?: ReadAloudOptions;
    translationOptions?: TranslationOptions;
    displayOptions?: DisplayOptions;
    preferences?: string;
    onPreferencesChanged?: (value: string) => any;
    customDomain?: string;
}
```

#### <a name="options-parameters"></a>Parametri delle opzioni

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| uiLang | string | Lingua dell'interfaccia utente, il valore è nel formato di tag del linguaggio IETF BCP 47, ad esempio en, es-ES. Se non è specificato, il valore predefinito è lingua del browser. |
| timeout | Numero | Durata (in millisecondi) prima che [launchAsync](#launchasync) abbia esito negativo con un errore di timeout (il valore predefinito è 15000 MS). Questo timeout si applica solo all'avvio iniziale della pagina Reader, in cui l'operazione viene eseguita quando si apre la pagina Reader e viene avviata la casella di selezione. Non è necessario rettificare il timeout. |
| uiZIndex | Numero | Indice Z dell'iframe che verrà creato (il valore predefinito è 1000). |
| useWebview | Boolean| Usare un tag WebView anziché un iframe per la compatibilità con le app Chrome (il valore predefinito è false). |
| onExit | Funzione | Viene eseguito quando il lettore immersivo viene chiuso. |
| allowFullscreen | Boolean | Possibilità di impostare il controllo a schermo intero (il valore predefinito è true). |
| hideExitButton | Boolean | Indica se nascondere o meno la freccia del pulsante di uscita del lettore immersivo (il valore predefinito è false). Questo dovrebbe essere vero solo se è disponibile un meccanismo alternativo per uscire dal reader immersivo, ad esempio una freccia indietro della barra degli strumenti per dispositivi mobili. |
| cookiePolicy | [CookiePolicy](#cookiepolicy-options) | Impostazione per l'utilizzo dei cookie del lettore immersivo (il valore predefinito è *CookiePolicy. Disable*). È responsabilità dell'applicazione host ottenere il consenso dell'utente necessario in base ai criteri di conformità dei cookie dell'Unione europea. Vedere [Opzioni dei criteri dei cookie](#cookiepolicy-options). |
| disableFirstRun | Boolean | Disabilitare la prima esperienza di esecuzione. |
| readAloudOptions | [ReadAloudOptions](#readaloudoptions) | Opzioni per configurare la lettura a voce alta. |
| translationOptions | [TranslationOptions](#translationoptions) | Opzioni per la configurazione della traduzione. |
| displayOptions | [DisplayOptions](#displayoptions) | Opzioni per la configurazione delle dimensioni del testo, del tipo di carattere e così via. |
| Preferenze | string | Stringa restituita da onPreferencesChanged che rappresenta le preferenze dell'utente nel lettore immersivo. Per ulteriori informazioni, vedere [parametri delle impostazioni](#settings-parameters) e [modalità di archiviazione delle preferenze utente](./how-to-store-user-preferences.md) . |
| onPreferencesChanged | Funzione | Viene eseguito quando le preferenze dell'utente sono state modificate. Per ulteriori informazioni, vedere [come archiviare le preferenze utente](./how-to-store-user-preferences.md) . |
| customDomain | string | Riservato per utilizzo interno. Dominio personalizzato in cui è ospitato il webapp del lettore immersivo (il valore predefinito è null). |

##### `uiLang`
```Parameters
Type: String
Required: false
Default value: User's browser language 
```

##### `timeout`
```Parameters
Type: Number
Required: false
Default value: 15000
```

##### `uiZIndex`
```Parameters
Type: Number
Required: false
Default value: 1000
```

##### `onExit`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `preferences`

> [!CAUTION]
> **Importante** Non tentare di modificare a livello di codice i valori della `-preferences` stringa inviata da e verso l'applicazione immersiva Reader, perché questo potrebbe causare un comportamento imprevisto che può comportare un'esperienza utente ridotta per i clienti. Le applicazioni host non devono mai assegnare un valore personalizzato a o modificare la `-preferences` stringa. Quando si usa l' `-preferences` opzione String, usare solo il valore esatto restituito dall'opzione di `-onPreferencesChanged` callback.

```Parameters
Type: String
Required: false
Default value: null
```

##### `onPreferencesChanged`
```Parameters
Type: Function
Required: false
Default value: null
```

##### `customDomain`
```Parameters
Type: String
Required: false
Default value: null
```

<br>

## <a name="readaloudoptions"></a>ReadAloudOptions

```typescript
type ReadAloudOptions = {
    voice?: string;
    speed?: number;
    autoplay?: boolean;
};
```

#### <a name="readaloudoptions-parameters"></a>Parametri ReadAloudOptions

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| voice | string | Voice, "Female" o "male". Si noti che non tutti i linguaggi supportano entrambi i sessi. |
| velocità | Numero | La velocità di riproduzione deve essere compresa tra 0,5 e 2,5 inclusi. |
| autoPlay | Boolean | Avvia automaticamente la lettura a voce alta quando viene caricato il lettore immersivo. |

##### `voice`
```Parameters
Type: String
Required: false
Default value: "Female" or "Male" (determined by language) 
Values available: "Female", "Male"
```

##### `speed`
```Parameters
Type: Number
Required: false
Default value: 1
Values available: 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2, 2.25, 2.5
```

> [!NOTE]
> A causa delle limitazioni del browser, AutoPlay non è supportato in Safari.

<br>

## <a name="translationoptions"></a>TranslationOptions

```typescript
type TranslationOptions = {
    language: string;
    autoEnableDocumentTranslation?: boolean;
    autoEnableWordTranslation?: boolean;
};
```

#### <a name="translationoptions-parameters"></a>Parametri TranslationOptions

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| Linguaggio | string | Imposta il linguaggio di traduzione, il valore è nel formato di tag del linguaggio IETF BCP 47, ad esempio fr-FR, es-MX, zh-Hans-CN. Necessario per abilitare automaticamente la traduzione di Word o documenti. |
| autoEnableDocumentTranslation | Boolean | Tradurre automaticamente l'intero documento. |
| autoEnableWordTranslation | Boolean | Abilita automaticamente la traduzione in Word. |

##### `language`
```Parameters
Type: String
Required: true
Default value: null 
Values available: See the Supported Languages section
```

<br>

## <a name="displayoptions"></a>DisplayOptions

```typescript
type DisplayOptions = {
    textSize?: number;
    increaseSpacing?: boolean;
    fontFamily?: string;
};
```

#### <a name="displayoptions-parameters"></a>Parametri DisplayOptions

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| textSize | Numero | Imposta la dimensione del testo scelta. |
| increaseSpacing | Boolean | Imposta un valore che indica se la spaziatura del testo viene attivata o disattivata. |
| fontFamily | string | Imposta il tipo di carattere scelto ("calibri", "ComicSans" o "Sitka"). |

##### `textSize`
```Parameters
Type: Number
Required: false
Default value: 20, 36 or 42 (Determined by screen size)
Values available: 14, 20, 28, 36, 42, 48, 56, 64, 72, 84, 96
```

##### `fontFamily`
```Parameters
Type: String
Required: false
Default value: "Calibri"
Values available: "Calibri", "Sitka", "ComicSans"
```

<br>

## <a name="cookiepolicy-options"></a>Opzioni di CookiePolicy

```typescript
enum CookiePolicy { Disable, Enable }
```

**Le impostazioni elencate di seguito sono esclusivamente a scopo informativo**. Il lettore immersivo archivia le impostazioni o le preferenze dell'utente nei cookie. Questa *cookiePolicy* opzione cookiePolicy **Disabilita** l'uso dei cookie per impostazione predefinita per conformarsi alle leggi di conformità dei cookie UE. Se si desidera riabilitare i cookie e ripristinare la funzionalità predefinita per le preferenze utente del lettore immersivo, è necessario assicurarsi che il sito Web o l'applicazione ottenga il consenso appropriato dall'utente per abilitare i cookie. Quindi, per riabilitare i cookie nel lettore immersivo, è necessario impostare in modo esplicito l'opzione *cookiePolicy* su *cookiePolicy. Enable* quando si avvia il lettore immersivo. La tabella seguente descrive le impostazioni archiviate dal lettore immersivo nel cookie quando è abilitata l'opzione *cookiePolicy* .

#### <a name="settings-parameters"></a>Parametri delle impostazioni

| Impostazione | Type | Descrizione |
| ------- | ---- | ----------- |
| textSize | Numero | Imposta la dimensione del testo scelta. |
| fontFamily | string | Imposta il tipo di carattere scelto ("calibri", "ComicSans" o "Sitka"). |
| textSpacing | Numero | Imposta un valore che indica se la spaziatura del testo viene attivata o disattivata. |
| formattingEnabled | Boolean | Imposta se la formattazione HTML è attivata o disattivata. |
| tema | string | Imposta il tema scelto (ad esempio "Light", "Dark"...). |
| syllabificationEnabled | Boolean | Imposta un valore che indica se syllabification è attivato o disattivato. |
| nounHighlightingEnabled | Boolean | che specifica se l'evidenziazione del sostantivo viene attivata o disattivata. |
| nounHighlightingColor | string | Imposta il colore di evidenziazione del sostantivo scelto. |
| verbHighlightingEnabled | Boolean | Specifica se l'evidenziazione del verbo è attivata o disattivata. |
| verbHighlightingColor | string | Imposta il colore di evidenziazione del verbo scelto. |
| adjectiveHighlightingEnabled | Boolean | Indica se l'evidenziazione degli aggettivi viene attivata o disattivata. |
| adjectiveHighlightingColor | string | Imposta il colore di evidenziazione degli aggettivi scelto. |
| adverbHighlightingEnabled | Boolean | Indica se l'evidenziazione degli avverbi viene attivata o disattivata. |
| adverbHighlightingColor | string | Imposta il colore di evidenziazione dell'avverbio scelto. |
| pictureDictionaryEnabled | Boolean | Imposta un valore che indica se il dizionario immagini è attivato o disattivato. |
| posLabelsEnabled | Boolean | Imposta un valore che indica se l'etichetta di testo superscript di ogni parte evidenziata del riconoscimento vocale è attivata o disattivata.  |

<br>

## <a name="supported-languages"></a>Lingue supportate

La funzionalità di traduzione del lettore immersivo supporta diversi linguaggi. Vedere [supporto linguistico](./language-support.md) per ulteriori dettagli.

<br>

## <a name="html-support"></a>Supporto HTML

Quando è abilitata la formattazione, il rendering del contenuto seguente verrà eseguito come HTML nel lettore immersivo.

| HTML | Contenuto supportato |
| --------- | ----------- |
| Stili dei tipi di carattere | Grassetto, corsivo, sottolineato, codice, barrato, apice, pedice |
| Elenchi non ordinati | Disco, cerchio, quadrato |
| Elenchi ordinati | Decimale, superiore-alfa, inferiore-alfa, superiore-romana, minore-romana |

I tag non supportati verranno sottoposti a rendering in modo paragonabile. Le immagini e le tabelle non sono attualmente supportate.

<br>

## <a name="browser-support"></a>Supporto browser

Usare le versioni più recenti dei browser seguenti per un'esperienza ottimale con il lettore immersivo.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

<br>

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK in GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Guida introduttiva: creare un'app Web che avvia il lettore immersivo (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
