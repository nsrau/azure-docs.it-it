---
title: Credenziali del certificato della piattaforma di identità Microsoft
titleSuffix: Microsoft identity platform
description: Questo articolo illustra la registrazione e l'uso delle credenziali del certificato per l'autenticazione dell'applicazione.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47a35f70251622674205a28af9b7cc64132d0530
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690278"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Credenziali del certificato di autenticazione dell'applicazione Microsoft Identity Platform

Microsoft Identity Platform consente a un'applicazione di usare le proprie credenziali per l'autenticazione, ad esempio, nelle [credenziali client OAuth 2,0 concedere flowv 2.0](v2-oauth2-client-creds-grant-flow.md) e il [flusso per conto di](v2-oauth2-on-behalf-of-flow.md).

Un tipo di credenziale che un'applicazione può usare per l'autenticazione è un'asserzione di token JSON Web (JWT) firmata con un certificato di proprietà dell'applicazione.

## <a name="assertion-format"></a>Formato di asserzione
Piattaforma di identità Microsoft per calcolare l'asserzione, è possibile usare una delle numerose librerie di [token Web JSON](https://jwt.ms/) nel linguaggio scelto. Le informazioni incluse nel token sono le seguenti:

### <a name="header"></a>Intestazione

| Parametro |  Commento |
| --- | --- |
| `alg` | Deve essere **RS256** |
| `typ` | Deve essere **JWT** |
| `x5t` | Deve essere l'identificazione personale SHA-1 del certificato X.509 |

### <a name="claims-payload"></a>Attestazioni (payload)

| Parametro |  Osservazioni |
| --- | --- |
| `aud` | Destinatari: deve essere ** https://login.microsoftonline.com/ *tenant_Id*/OAuth2/token** |
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

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registrare il certificato con Microsoft Identity Platform

È possibile associare la credenziale del certificato all'applicazione client nella piattaforma Microsoft Identity tramite il portale di Azure usando uno dei metodi seguenti:

### <a name="uploading-the-certificate-file"></a>Caricamento del file del certificato

Nella registrazione dell'app di Azure per l'applicazione client:
1. Selezionare **Certificati e segreti**.
2. Fare clic su **Carica certificato** e selezionare il file di certificato da caricare.
3. Scegliere **Aggiungi**.
  Una volta caricato il certificato, vengono visualizzati i valori di identificazione personale, data di inizio e scadenza.

### <a name="updating-the-application-manifest"></a>Aggiornamento del manifesto dell'applicazione

Con un certificato disponibile, è necessario calcolare:

- `$base64Thumbprint`, che è la codifica base64 dell'hash del certificato
- `$base64Value`, che è la codifica Base 64 dei dati non elaborati dell'hash del certificato

È anche necessario specificare un GUID per identificare la chiave nel manifesto dell'applicazione (`$keyId`).

Nella registrazione dell'app di Azure per l'applicazione client:
1. Selezionare **manifesto** per aprire il manifesto dell'applicazione.
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
3. Salvare le modifiche nel manifesto dell'applicazione e quindi caricare il manifesto nella piattaforma di identità Microsoft.

   La proprietà `keyCredentials` è multivalore, quindi è possibile caricare più certificati per una gestione delle chiavi più avanzata.

## <a name="code-sample"></a>Esempio di codice

> [!NOTE]
> Per calcolare l'intestazione X5T, è necessario convertirla in una stringa di base 64 usando l'hash del certificato. Il codice per eseguire questa operazione in C# `System.Convert.ToBase64String(cert.GetCertHash());`è.

L'applicazione console di esempio di codice [.NET Core daemon con la piattaforma di identità Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) Mostra come un'applicazione usa le proprie credenziali per l'autenticazione. Illustra anche come è possibile [creare un certificato autofirmato](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) usando il comando `New-SelfSignedCertificate` di PowerShell. È possibile anche sfruttare e usare gli [script di creazione di app](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) per creare i certificati, calcolare l'identificazione personale e così via.
