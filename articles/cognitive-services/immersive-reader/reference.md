---
title: Guida di riferimento a immersive Reader SDK
titleSuffix: Azure Cognitive Services
description: Informazioni di riferimento sull'SDK di immersive Reader
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 22860e0798ec852b6d25da27e108befe8a3089a2
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488822"
---
# <a name="immersive-reader-sdk-reference"></a>Guida di riferimento a immersive Reader SDK

Immersive Reader SDK è una libreria JavaScript che consente di integrare il lettore immersivo nell'applicazione Web.

## <a name="functions"></a>Funzioni

L'SDK espone una singola funzione, `ImmersiveReader.launchAsync(token, subdomain, content, options)`.

### <a name="launchasync"></a>launchAsync

Avvia il Reader immersivo all'interno `iframe` di un oggetto nell'applicazione Web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<HTMLDivElement>;
```

#### <a name="parameters"></a>Parametri

| Name | Type | Descrizione |
| ---- | ---- |------------ |
| `token` | string | Token di autenticazione Azure AD. Vedere le [procedure per l'autenticazione Azure ad](./azure-active-directory-authentication.md). |
| `subdomain` | string | Sottodominio personalizzato della risorsa Reader immersiva in Azure. Vedere le [procedure per l'autenticazione Azure ad](./azure-active-directory-authentication.md). |
| `content` | [Contenuto](#content) | Oggetto contenente il contenuto da visualizzare nel lettore immersivo. |
| `options` | [Opzioni](#options) | Opzioni per la configurazione di determinati comportamenti del lettore immersivo. facoltativo. |

#### <a name="returns"></a>Risultati restituiti

Restituisce un `Promise<HTMLDivElement>` oggetto che viene risolto quando il lettore immersivo viene caricato. Viene `Promise` risolto in un `div` elemento il cui unico figlio è un `iframe` elemento che contiene la pagina di lettura immersiva.

#### <a name="exceptions"></a>Eccezioni

Il valore `Promise` restituito verrà rifiutato con un [`Error`](#error) oggetto se il lettore immersivo non viene caricato. Per ulteriori informazioni, vedere i [codici di errore](#error-codes).

## <a name="types"></a>Tipi

### <a name="content"></a>Content

Contiene il contenuto da visualizzare nel lettore immersivo.

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
| testo/normale | Testo normale. |
| Application/MathML + XML | Linguaggio di markup matematico (MathML). [Altre informazioni](https://developer.mozilla.org/en-US/docs/Web/MathML)

### <a name="options"></a>Opzioni

Contiene proprietà che configurano comportamenti specifici del lettore immersivo.

```typescript
{
    uiLang?: string;   // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;  // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number; // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean; // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
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
| BadArgument | L'argomento fornito non è valido `message` . per informazioni dettagliate, vedere. |
| Timeout | Non è stato possibile caricare il lettore immersivo entro il timeout specificato. |
| TokenExpired| Il token fornito è scaduto. |

## <a name="launching-the-immersive-reader"></a>Avvio del lettore immersivo

L'SDK fornisce lo stile predefinito per il pulsante per l'avvio del lettore immersivo. Usare l' `immersive-reader-button` attributo Class per abilitare questo stile.

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Attributi facoltativi

Usare gli attributi seguenti per configurare l'aspetto del pulsante.

| Attributo | Descrizione |
| --------- | ----------- |
| `data-button-style` | Imposta lo stile del pulsante. Può essere `icon`, `text` o `iconAndText`. Il valore predefinito è `icon`. |
| `data-locale` | Imposta le impostazioni locali, ad esempio `en-US`. `fr-FR` Il valore predefinito è inglese. |
| `data-icon-px-size` | Imposta la dimensione dell'icona in pixel. Il valore predefinito è 20px. |

## <a name="browser-support"></a>Supporto browser

Usare le versioni più recenti dei browser seguenti per un'esperienza ottimale con il lettore immersivo.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK in GitHub](https://github.com/Microsoft/immersive-reader-sdk)
* [Avvio rapido: Creare un'app Web che avvii il lettore immersivoC#()](./quickstart.md)