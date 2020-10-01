---
title: Credenziali del certificato di Microsoft Identity Platform
titleSuffix: Microsoft identity platform
description: Questo articolo illustra la registrazione e l'uso delle credenziali del certificato per l'autenticazione di un'applicazione.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 77e34e4a18012f15b9e907e3b9efc1965b98f824
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612121"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Credenziali del certificato per l'autenticazione di un'applicazione con Microsoft Identity Platform

Microsoft Identity Platform consente a un'applicazione di usare le proprie credenziali per l'autenticazione ovunque sia possibile usare un segreto client, ad esempio nel flusso di  [concessione delle credenziali client](v2-oauth2-client-creds-grant-flow.md) OAuth 2,0 e nel flusso [di per conto di](v2-oauth2-on-behalf-of-flow.md) (OBO).

Una forma di credenziale che un'applicazione può usare per l'autenticazione è un'asserzione [JSON Web token](./security-tokens.md#json-web-tokens-jwts-and-claims) (JWT) firmata con un certificato di proprietà dell'applicazione.

## <a name="assertion-format"></a>Formato di asserzione

Per calcolare l'asserzione, è possibile usare una delle numerose librerie JWT nel linguaggio scelto: [MSAL supporta questa operazione usando `.WithCertificate()` ](msal-net-client-assertions.md). Le informazioni vengono trasferite dal token nell' [intestazione](#header), nelle [attestazioni](#claims-payload)e nella [firma](#signature).

### <a name="header"></a>Intestazione

| Parametro |  Commento |
| --- | --- |
| `alg` | Deve essere **RS256** |
| `typ` | Deve essere **JWT** |
| `x5t` | Rappresentazione esadecimale dell'hash del certificato X. 509 (nota anche come *identificazione digitale*SHA-1 del certificato) codificata come valore stringa Base64. Ad esempio, dato un hash del certificato X. 509 di `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (hex), l' `x5t` attestazione sarebbe `hOBcHZi846VCHSJbFAs26Go9VTQ=` (base64). |

### <a name="claims-payload"></a>Attestazioni (payload)

Tipo di attestazione | Valore | Descrizione
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | L'attestazione "AUD" (audience) identifica i destinatari a cui è destinato il JWT (in questo Azure AD) vedere [RFC 7519, sezione 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  In questo caso, il destinatario è il server di accesso (login.microsoftonline.com).
exp | 1601519414 | L'attestazione "exp" (expiration time) identifica l'ora di scadenza a partire dalla quale o successivamente alla quale il token JWT non deve essere accettato per l'elaborazione. Vedere la [specifica RFC 7519, sezione 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Questo consente di usare l'asserzione fino a quel momento, quindi mantenerla a breve-5-10 minuti dopo `nbf` al massimo.  Azure AD non applica restrizioni al `exp` momento attuale. 
iss | ClientID | L'attestazione "ISS" (emittente) identifica l'entità che ha emesso il JWT, in questo caso l'applicazione client.  Usare l'ID applicazione GUID.
jti | (Guid) | L'attestazione "ITC" (ID JWT) fornisce un identificatore univoco per il JWT. Il valore dell'identificatore deve essere assegnato in modo da garantire che esista una probabilità trascurabile che lo stesso valore verrà assegnato accidentalmente a un oggetto dati diverso. Se l'applicazione usa più autorità emittenti, è necessario impedire conflitti tra i valori prodotti da autorità di certificazione diverse. Il valore "ITC" è una stringa con distinzione tra maiuscole e minuscole. [RFC 7519, sezione 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | L'attestazione "nbf" (not before) identifica l'ora prima della quale il token JWT non deve essere accettato per l'elaborazione. [RFC 7519, sezione 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  L'utilizzo dell'ora corrente è appropriato. 
sub | ClientID | L'attestazione "Sub" (Subject) identifica l'oggetto di JWT, in questo caso anche l'applicazione. Utilizzare lo stesso valore di `iss` . 

### <a name="signature"></a>Firma

La firma viene calcolata applicando il certificato come descritto nella [specifica RFC7519 del token Web JSON](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Esempio di asserzione del token JWT decodificata

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Esempio di asserzione del token JWT codificata

La stringa seguente è un esempio di asserzione codificata. Se si osserva attentamente, si notano tre sezioni separate da punti ( `.` ):

* La prima sezione codifica l' *intestazione*
* La seconda sezione codifica le *attestazioni* (payload)
* L'ultima sezione è la *firma* calcolata con i certificati dal contenuto delle prime due sezioni

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registrare un certificato con Microsoft Identity Platform

È possibile associare la credenziale del certificato all'applicazione client in Microsoft Identity Platform tramite il portale di Azure usando uno dei metodi seguenti:

### <a name="uploading-the-certificate-file"></a>Caricamento del file del certificato

Nella registrazione dell'app di Azure per l'applicazione client:
1. Selezionare **Certificati e segreti**.
2. Fare clic su **Carica certificato** e selezionare il file di certificato da caricare.
3. Scegliere **Aggiungi**.
  Dopo aver caricato il certificato, vengono visualizzati i valori di identificazione personale, data di inizio e scadenza.

### <a name="updating-the-application-manifest"></a>Aggiornamento del manifesto dell'applicazione

Con un certificato disponibile, è necessario calcolare:

- `$base64Thumbprint` -Valore con codifica Base64 dell'hash del certificato
- `$base64Value` -Valore con codifica Base64 dei dati non elaborati del certificato

È anche necessario specificare un GUID per identificare la chiave nel manifesto dell'applicazione (`$keyId`).

Nella registrazione dell'app di Azure per l'applicazione client:
1. Selezionare **Manifesto** per aprire il manifesto dell'applicazione.
2. Sostituire la proprietà *keyCredentials* con le nuove informazioni del certificato usando lo schema seguente.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Salvare le modifiche apportate al manifesto dell'applicazione e caricare il manifesto in Microsoft Identity Platform.

   La proprietà `keyCredentials` è multivalore, quindi è possibile caricare più certificati per una gestione delle chiavi più avanzata.
   
## <a name="using-a-client-assertion"></a>Uso di un'asserzione client

Le asserzioni client possono essere utilizzate ovunque venga utilizzato un segreto client.  Nel [flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md), ad esempio, è possibile passare un `client_secret` per dimostrare che la richiesta è proveniente dall'app. È possibile sostituire con i `client_assertion` `client_assertion_type` parametri e. 

| Parametro | Valore | Descrizione|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Si tratta di un valore fisso, che indica che si sta utilizzando una credenziale del certificato. |
|`client_assertion`| Token JSON Web |Si tratta del JWT creato in precedenza. |

## <a name="next-steps"></a>Passaggi successivi

La [libreria MSAL.NET gestisce questo scenario](msal-net-client-assertions.md) in una sola riga di codice.

L' [applicazione console daemon .NET Core che usa](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) l'esempio di codice Microsoft Identity Platform in GitHub Mostra come un'applicazione usa le proprie credenziali per l'autenticazione. Viene anche illustrato come [creare un certificato autofirmato](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) usando il cmdlet di `New-SelfSignedCertificate` PowerShell. È anche possibile usare gli [script di creazione di app](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) nel repository di esempio per creare certificati, calcolare l'identificazione personale e così via.
