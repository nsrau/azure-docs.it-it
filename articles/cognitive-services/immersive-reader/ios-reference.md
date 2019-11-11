---
title: Informazioni di riferimento su iOS SDK per i lettori immersivi
titleSuffix: Azure Cognitive Services
description: Immersive Reader iOS SDK è una CocoaPod rapida che consente di integrare il lettore immersivo nell'applicazione iOS.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 67d6b8c22c5635bd789078a7f91b02f8b07e5e70
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903135"
---
# <a name="immersive-reader-sdk-reference-for-ios"></a>Guida di riferimento a immersive Reader SDK per iOS

Immersive Reader iOS SDK è una CocoaPod rapida che consente di integrare il lettore immersivo nell'applicazione iOS.

## <a name="functions"></a>Functions

L'SDK espone una singola funzione `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`.

### <a name="launchimmersivereader"></a>launchImmersiveReader

Avvia il lettore immersivo avviando un controller di visualizzazione nell'applicazione iOS.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>parametri

| Nome | digitare | DESCRIZIONE |
| ---- | ---- |------------ |
| `navController` | UINavigationController | Il controller di spostamento per l'applicazione iOS da cui viene chiamata la funzione. |
| `token` | String | Token di autenticazione Azure AD. Vedere le [procedure per l'autenticazione Azure ad](./azure-active-directory-authentication.md). |
| `subdomain` | String | Sottodominio personalizzato della risorsa Reader immersiva in Azure. Vedere le [procedure per l'autenticazione Azure ad](./azure-active-directory-authentication.md). |
| `content` | [Contenuto](#content) | Oggetto contenente il contenuto da visualizzare nel lettore immersivo. |
| `options` | [Opzioni](#options) | Opzioni per la configurazione di determinati comportamenti del lettore immersivo. Facoltativa. |
| `onSuccess` | ()-> void | Una chiusura richiamata quando il lettore immersivo viene avviato correttamente. |
| `onFailure` | (_ errore: [errore](#error))-> void | Una chiusura richiamata quando il lettore immersivo non riesce a caricare. Questa chiusura restituisce un oggetto [`Error`](#error) che rappresenta un codice di errore e un messaggio di errore associato all'errore. Per ulteriori informazioni, vedere i [codici di errore](#error-codes). |

## <a name="types"></a>Types

### <a name="content"></a>Content

Contiene il contenuto da visualizzare nel lettore immersivo.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>Tipi MIME supportati

| Tipo MIME | DESCRIZIONE |
| --------- | ----------- |
| text/plain | Testo normale. |
| Application/MathML + XML | Linguaggio di markup matematico (MathML). [Altre informazioni](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Opzioni

Contiene proprietà che configurano comportamenti specifici del lettore immersivo.

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Tipi di errore

Contiene informazioni sull'errore.

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>Codici di errore

| Codice | DESCRIZIONE |
| ---- | ----------- |
| BadArgument | L'argomento fornito non è valido. per informazioni dettagliate, vedere `message`. |
| Timeout | Non è stato possibile caricare il lettore immersivo entro il timeout specificato. |
| TokenExpired | Il token fornito è scaduto. |
| Strozzato | È stato superato il limite di frequenza delle chiamate. |
| InternalError | Si è verificato un errore interno nel controller visualizzazione immersiva del lettore. Per informazioni dettagliate, vedere `message`.|

## <a name="os-version-support"></a>Supporto della versione del sistema operativo

Il Reader immersive iOS SDK è supportato per iOS 9,0 o versione successiva, su iPad e iPhone.

## <a name="next-steps"></a>Passaggi successivi

* Esplora il [Reader immersive per iOS SDK su GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS)
* [Guida introduttiva: creare un'app iOS che avvia il lettore immersivo (SWIFT)](./ios-quickstart.md)