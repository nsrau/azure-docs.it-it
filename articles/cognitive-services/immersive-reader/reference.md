---
title: Riferimento al SDK del lettore coinvolgenti
titlesuffix: Azure Cognitive Services
description: Informazioni di riferimento per il lettore Immersive SDK
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: c128608b3c4a8e1155c3ac962bcfd07f589fbf23
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67311793"
---
# <a name="immersive-reader-sdk-reference"></a>Riferimento al SDK del lettore coinvolgenti

L'esempio di lettore Immersive SDK è una libreria JavaScript che consente di integrare il lettore Immersive nell'applicazione web.

## <a name="functions"></a>Funzioni

il SDK espone una singola funzione, `ImmersiveReader.launchAsync(token, resourceName, content, options)`.

### <a name="launchasync"></a>launchAsync

Avvia il lettore Immersive all'interno di un `iframe` nell'applicazione web.

```typescript
launchAsync(token: string, resourceName: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parametri

| NOME | Type | Descrizione |
| ---- | ---- |------------ |
| `token` | string | Il token di accesso acquisito dalla chiamata al `issueToken` endpoint. |
| `resourceName` | string | Riservato. Il valore deve essere impostato su `null`. |
| `content` | [Contenuto](#content) | Oggetto che contiene il contenuto da visualizzare nel lettore coinvolgente e concreto. |
| `options` | [Opzioni](#options) | Opzioni per la configurazione di determinati comportamenti del lettore coinvolgente e concreto. facoltativo. |

#### <a name="returns"></a>Restituisce

Restituisce un `Promise<HTMLDivElement>` che viene risolto quando viene caricato il lettore coinvolgente e concreto. Il `Promise` si risolve in un `div` elemento il cui unico elemento figlio è un `iframe` elemento che contiene la pagina del lettore coinvolgente e concreto.

#### <a name="exceptions"></a>Eccezioni

L'oggetto restituito `Promise` verranno rifiutati con un [ `Error` ](#error) dell'oggetto se il lettore Immersive non riesce a caricare. Per altre informazioni, vedere la [codici di errore](#error-codes).

## <a name="types"></a>Tipi

### <a name="content"></a>Content

Contiene il contenuto da visualizzare nel lettore coinvolgente e concreto.

```typescript
{
    title?: string;      // Title text shown at the top of the Immersive Reader (optional)
    chunks: [ {          // Array of chunks
        content: string; // Plain text string
        lang?: string;   // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
        mimeType?: string; // MIME type of the content (optional). Defaults to 'text/plain' if not specified.
    } ];
}
```

#### <a name="supported-mime-types"></a>Tipi MIME supportati

| Tipo MIME | Descrizione |
| --------- | ----------- |
| text/plain | Testo normale. |
| applicazione/mathml + xml | Mathematical Markup Language (MathML). [Altre informazioni](https://developer.mozilla.org/en-US/docs/Web/MathML)

### <a name="options"></a>Opzioni

Contiene le proprietà che consentono di configurare determinati comportamenti del lettore coinvolgente e concreto.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
}
```

### <a name="error"></a>Tipi di errore

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
| BadArgument | Argomento fornito non è valido, vedere `message` per informazioni dettagliate. |
| Timeout | Il lettore Immersive non è stato possibile caricare entro il timeout specificato. |
| TokenExpired| Il token fornito è scaduto. |

## <a name="launching-the-immersive-reader"></a>Avviare il lettore coinvolgente

il SDK fornisce lo stile predefinito per il pulsante per l'avvio del lettore coinvolgente e concreto. Usare il `immersive-reader-button` attributo della classe per abilitare questo stile.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Attributi facoltativi

Usare gli attributi seguenti per configurare l'aspetto del pulsante.

| Attributo | Descrizione |
| --------- | ----------- |
| `data-button-style` | Imposta lo stile del pulsante. Può essere `icon`, `text` o `iconAndText`. Il valore predefinito è `icon`. |
| `data-locale` | Imposta le impostazioni locali, ad esempio `en-US`, `fr-FR`. L'impostazione predefinita all'inglese. |
| `data-icon-px-size` | Imposta le dimensioni dell'icona in pixel. Il valore predefinito è 20px. |

## <a name="browser-support"></a>Supporto browser

Usare le versioni più recenti dei browser seguenti per ottenere risultati ottimali con il lettore coinvolgente e concreto.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Passaggi successivi

* Esplorare la [lettore Immersive SDK su GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* [Avvio rapido: Creare un'app web che consente di avviare il lettore Immersive (C#)](./quickstart.md)