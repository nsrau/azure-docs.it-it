---
title: Informazioni di riferimento su Immersive Reader SDK
titleSuffix: Azure Cognitive Services
description: Immersive Reader SDK contiene una libreria JavaScript che consente di integrare Immersive Reader nell'applicazione.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: b20a3e6dd3b32b183bbf34dbefd76f0e4cd56b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156404"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Guida di riferimento all'SDK di Immersive Reader

Immersive Reader SDK contiene una libreria JavaScript che consente di integrare Immersive Reader nell'applicazione.

## <a name="functions"></a>Funzioni

L'SDK espone le funzioni:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Avvia il lettore Immersive `iframe` all'interno di un'applicazione Web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parametri

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| `token` | string | Token di autenticazione di Azure AD. |
| `subdomain` | string | Sottodominio personalizzato della risorsa Lettore immersivo in Azure.The custom subdomain of your Immersive Reader resource in Azure. |
| `content` | [Contenuto](#content) | Oggetto contenente il contenuto da visualizzare nel Lettore Immersivo. |
| `options` | [Opzioni](#options) | Opzioni per la configurazione di determinati comportamenti del Lettore Immersivo. Facoltativa. |

### <a name="returns"></a>Valori di codice restituiti

Restituisce `Promise<LaunchResponse>`un oggetto , che viene risolto quando viene caricato il lettore immersivo. L'oggetto `Promise` viene [`LaunchResponse`](#launchresponse) risolto in un oggetto.

### <a name="exceptions"></a>Eccezioni

L'oggetto `Promise` restituito verrà rifiutato con un [`Error`](#error) oggetto se il lettore Immersive non viene caricato. Per ulteriori informazioni, vedere i [codici di errore](#error-codes).

## <a name="close"></a>chiudi

Chiude il Lettore Immersivo.

Un esempio di caso d'uso per questa ```hideExitButton: true``` funzione è se il pulsante di uscita è nascosto impostando in [options](#options). Quindi, un pulsante diverso (ad esempio la freccia ```close``` Indietro di un'intestazione mobile) può chiamare questa funzione quando si fa clic su di essa.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons (pulsanti)

Questa funzione applica uno stile e aggiorna gli elementi del pulsante Lettore immersivo del documento. Se ```options.elements``` viene fornito, allora questa ```options.elements```funzione eseguirà il rendering dei pulsanti all'interno di . In caso contrario, verrà eseguito il rendering ```immersive-reader-button```dei pulsanti all'interno degli elementi del documento che dispongono della classe .

Questa funzione viene chiamata automaticamente dall'SDK quando viene caricata la finestra.

Consultate [Attributi facoltativi](#optional-attributes) per altre opzioni di rendering.

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametri

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Opzioni per la configurazione di determinati comportamenti della funzione renderButtons. Facoltativa. |

## <a name="types"></a>Tipi

### <a name="content"></a>Contenuto

Contiene il contenuto da visualizzare nel Lettore Immersive.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Blocco

Un singolo blocco di dati, che verrà passato nel contenuto del lettore Immersive.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

Contiene la risposta dalla `ImmersiveReader.launchAsync`chiamata a .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>Enumerazione CookiePolicy

Enum utilizzato per impostare i criteri per l'utilizzo dei cookie di Immersive Reader. Vedere [opzioni](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Tipi MIME supportati

| Tipo MIME | Descrizione |
| --------- | ----------- |
| text/plain | Testo normale. |
| text/html | Contenuto HTML. [Scopri di più](#html-support)|
| applicazione/mathml | MathML (Mathematical Markup Language). [Scopri di più](./how-to/display-math.md).
| application/vnd.openxmlformats-officedocument.wordprocessingml.document | Documento in formato .docx di Microsoft Word.

### <a name="html-support"></a>Supporto HTML

| HTML | Contenuti supportati |
| --------- | ----------- |
| Stili carattere | Grassetto, Corsivo, Sottolineato, Codice, Barrato, Apice, Pedice |
| Elenchi non ordinati | Disco, Cerchio, Quadrato |
| Elenchi ordinati | Decimale, Superiore-Alfa, Alfa inferiore, Alto-Romano, Inferiore-Romano |
| Collegamenti ipertestuali | Imminente |

I tag non supportati verranno visualizzati in modo comparabile. Le immagini e le tabelle non sono attualmente supportate.

### <a name="options"></a>Opzioni

Contiene proprietà che configurano determinati comportamenti di Immersive Reader.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Opzioni per il rendering dei pulsanti Lettore immersivo.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Errore

Contiene informazioni sull'errore.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Codici di errore

| Codice | Descrizione |
| ---- | ----------- |
| BadArgument | L'argomento fornito non `message` è valido, vedere per i dettagli. |
| Timeout | Il caricamento di Immersive Reader non è riuscito entro il timeout specificato. |
| TokenExpired | Il token fornito è scaduto. |
| Sospensione causata dal servizio Microsoft FullText | È stato superato il limite di frequenza delle chiamate. |

## <a name="launching-the-immersive-reader"></a>Lancio del lettore Immersive

L'SDK fornisce lo stile predefinito per il pulsante per l'avvio di Immersive Reader. Utilizzare `immersive-reader-button` l'attributo class per abilitare questo stile. Vedere [questo articolo](./how-to-customize-launch-button.md) per ulteriori dettagli.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Attributi facoltativi

Utilizzare gli attributi seguenti per configurare l'aspetto del pulsante.

| Attributo | Descrizione |
| --------- | ----------- |
| `data-button-style` | Imposta lo stile del pulsante. Può essere `icon`, `text` o `iconAndText`. L'impostazione predefinita è `icon`. |
| `data-locale` | Imposta le impostazioni locali. Ad esempio, `en-US` o `fr-FR`. Il valore `en`predefinito è L'inglese . |
| `data-icon-px-size` | Imposta le dimensioni dell'icona in pixel. Il valore predefinito è 20px. |

## <a name="browser-support"></a>Supporto browser

Utilizza le versioni più recenti dei seguenti browser per un'esperienza ottimale con Immersive Reader.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK in GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Guida introduttiva: Creare un'app Web che avvia il lettore Immersive (C )Quickstart: Create a web app that launches the Immersive Reader (C](./quickstart.md)
