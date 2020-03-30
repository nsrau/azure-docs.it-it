---
title: Credenziali del certificato della piattaforma di identità Microsoft
titleSuffix: Microsoft identity platform
description: In questo articolo vengono illustrate la registrazione e l'utilizzo delle credenziali del certificato per l'autenticazione dell'applicazione.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 26030c12d98d796ceb1f66f198aede6e40eebd94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399014"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Credenziali del certificato di autenticazione dell'applicazione della piattaforma di identità MicrosoftMicrosoft identity platform application authentication certificate credentials

La piattaforma di identità Microsoft consente a un'applicazione di utilizzare le proprie credenziali per l'autenticazione, ad esempio negli [osidi OAuth 2.0 Client Credentials Grant flowv2.0](v2-oauth2-client-creds-grant-flow.md) e [nel flusso On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md).

Un tipo di credenziale che un'applicazione può usare per l'autenticazione è un'asserzione di token JSON Web (JWT) firmata con un certificato di proprietà dell'applicazione.

## <a name="assertion-format"></a>Formato di asserzione
Piattaforma di identità Microsoft Per calcolare l'asserzione, è possibile utilizzare una delle numerose librerie [di token Web JSON](https://jwt.ms/) nel linguaggio desiderato. Le informazioni incluse nel token sono le seguenti:

### <a name="header"></a>Intestazione

| Parametro |  Commento |
| --- | --- |
| `alg` | Deve essere **RS256** |
| `typ` | Deve essere **JWT** |
| `x5t` | Deve essere l'identificazione personale SHA-1 del certificato X.509 |

### <a name="claims-payload"></a>Attestazioni (payload)

| Parametro |  Osservazioni |
| --- | --- |
| `aud` | Pubblico: deve essere ** https://login.microsoftonline.com/ *tenant_Id*/oauth2/token** |
| `exp` | Data di scadenza: la data di scadenza del token. L'ora è rappresentata come numero di secondi dal 1° gennaio 1970 (1970-01-01T0:0:0Z) UTC fino all'ora in cui scade la validità del token.|
| `iss` | Autorità di certificazione: deve essere il parametro client_id (ID applicazione del servizio client) |
| `jti` | GUID: l'ID token JWT |
| `nbf` | Non prima: la data prima della quale il token non può essere usato. La data e l'ora sono rappresentate come numero di secondi dal 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino alla data e all'ora in cui il token è stato rilasciato. |
| `sub` | Oggetto: per quanto riguarda `iss`, deve essere il parametro client_id (ID applicazione del servizio client) |

### <a name="signature"></a>Firma

La firma viene calcolata applicando il certificato come descritto nella [specifica RFC7519 sul token JSON Web](https://tools.ietf.org/html/rfc7519)

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

La stringa seguente è un esempio di asserzione codificata. Se si osserva con attenzione, è possibile notare tre sezioni separate da punti (.):
* La prima sezione codifica l'intestazione.
* La seconda sezione codifica il payload.
* L'ultima sezione è la firma calcolata con i certificati a partire dal contenuto delle prime due sezioni.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registrare il certificato con la piattaforma di identità MicrosoftRegister your certificate with Microsoft identity platform

È possibile associare le credenziali del certificato all'applicazione client nella piattaforma di identità Microsoft tramite il portale di Azure usando uno dei metodi seguenti:You can associate the certificate credential with the client application in Microsoft identity platform through the Azure portal using any of the following methods:

### <a name="uploading-the-certificate-file"></a>Caricamento del file del certificato

Nella registrazione dell'app di Azure per l'applicazione client:
1. Selezionare **Certificati e segreti**.
2. Fare clic su **Carica certificato** e selezionare il file del certificato da caricare.
3. Fare clic su **Aggiungi**.
  Una volta caricato il certificato, vengono visualizzati i valori di identificazione personale, data di inizio e scadenza.

### <a name="updating-the-application-manifest"></a>Aggiornamento del manifesto dell'applicazione

Con un certificato disponibile, è necessario calcolare:

- `$base64Thumbprint`, che è la codifica base64 dell'hash del certificato
- `$base64Value`, che è la codifica Base 64 dei dati non elaborati dell'hash del certificato

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
3. Salvare le modifiche nel manifesto dell'applicazione e quindi caricare il manifesto nella piattaforma di identità Microsoft.Save the edits to the application manifest and then upload the manifest to Microsoft identity platform.

   La proprietà `keyCredentials` è multivalore, quindi è possibile caricare più certificati per una gestione delle chiavi più avanzata.

## <a name="code-sample"></a>Esempio di codice

> [!NOTE]
> È necessario calcolare l'intestazione X5T convertendola in una stringa base 64 utilizzando l'hash del certificato. Il codice per eseguire questa `System.Convert.ToBase64String(cert.GetCertHash());`operazione in C ' è .

L'esempio di codice [.NET Core daemon applicazione console che utilizza Microsoft identity platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) viene illustrato come un'applicazione utilizza le proprie credenziali per l'autenticazione. Illustra anche come è possibile [creare un certificato autofirmato](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) usando il comando `New-SelfSignedCertificate` di PowerShell. È possibile anche sfruttare e usare gli [script di creazione di app](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) per creare i certificati, calcolare l'identificazione personale e così via.
