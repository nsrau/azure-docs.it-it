---
title: Credenziali del certificato in Azure AD
titleSuffix: Microsoft identity platform
description: Questo articolo illustra la registrazione e l'uso delle credenziali del certificato per l'autenticazione dell'applicazione
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fca872d639ab5c2d4053656cdd3e68a59fdc1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473967"
---
# <a name="certificate-credentials-for-application-authentication"></a>Credenziali del certificato per l'autenticazione dell'applicazione

Azure Active Directory (Azure AD) consente a un'applicazione di usare le proprie credenziali per l'autenticazione, ad esempio, nel flusso di concessione delle credenziali client di OAuth 2.0 ([v1.0](v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) e nel flusso on-behalf-of ([v1.0](v1-oauth2-on-behalf-of-flow.md), [v2.0](v2-oauth2-on-behalf-of-flow.md)).

Un tipo di credenziale che un'applicazione può usare per l'autenticazione è un'asserzione di token JSON Web (JWT) firmata con un certificato di proprietà dell'applicazione.

## <a name="assertion-format"></a>Formato di asserzione
Per calcolare l'asserzione, è possibile usare una delle numerose librerie di [token JSON Web](https://jwt.ms/) nel linguaggio scelto. Le informazioni incluse nel token sono le seguenti:

### <a name="header"></a>Intestazione

| Parametro |  Commento |
| --- | --- |
| `alg` | Deve essere **RS256** |
| `typ` | Deve essere **JWT** |
| `x5t` | Deve essere l'identificazione personale SHA-1 del certificato X.509 |

### <a name="claims-payload"></a>Attestazioni (payload)

| Parametro |  Osservazioni |
| --- | --- |
| `aud` | Destinatari: deve essere **https://login.microsoftonline.com/*id_tenant*/oauth2/token** |
| `exp` | Data di scadenza: la data di scadenza del token. L'ora è rappresentata come numero di secondi dal 1° gennaio 1970 (1970-01-01T0:0:0Z) UTC fino all'ora in cui scade la validità del token.|
| `iss` | Autorità di certificazione: deve essere il parametro client_id (ID applicazione del servizio client) |
| `jti` | GUID: l'ID token JWT |
| `nbf` | Non prima: la data prima della quale il token non può essere usato. La data e l'ora sono rappresentate come numero di secondi dal 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino alla data e all'ora in cui il token è stato rilasciato. |
| `sub` | Oggetto: per quanto riguarda `iss`, deve essere il parametro client_id (ID applicazione del servizio client) |

### <a name="signature"></a>Firma

La firma viene calcolata applicando il certificato come descritto nella [specifica RFC7519 sul token JSON Web](https://tools.ietf.org/html/rfc7519)

## <a name="example-of-a-decoded-jwt-assertion"></a>Esempio di asserzione del token JWT decodificata

```
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

## <a name="register-your-certificate-with-azure-ad"></a>Registrare il certificato con Azure AD

È possibile associare la credenziale del certificato all'applicazione client in Azure AD tramite il portale di Azure usando uno dei metodi seguenti:

### <a name="uploading-the-certificate-file"></a>Caricamento del file del certificato

Nella registrazione dell'app di Azure per l'applicazione client:
1. Selezionare **Certificati e segreti**. 
2. Fare clic su **Carica certificato** e selezionare il file di certificato da caricare.
3. Fare clic su **Aggiungi**.
  Una volta caricato il certificato, vengono visualizzati i valori di identificazione personale, data di inizio e scadenza. 

### <a name="updating-the-application-manifest"></a>Aggiornamento del manifesto dell'applicazione

Con un certificato disponibile, è necessario calcolare:

- `$base64Thumbprint`, che è la codifica base64 dell'hash del certificato
- `$base64Value`, che è la codifica Base 64 dei dati non elaborati dell'hash del certificato

È anche necessario specificare un GUID per identificare la chiave nel manifesto dell'applicazione (`$keyId`).

Nella registrazione dell'app di Azure per l'applicazione client:
1. Selezionare **manifesto** per aprire il manifesto dell'applicazione.
2. Sostituire la proprietà *keyCredentials* con le nuove informazioni del certificato usando lo schema seguente.

   ```
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
3. Salvare le modifiche apportate al manifesto dell'applicazione e caricare il manifesto in Azure AD. 

   La proprietà `keyCredentials` è multivalore, quindi è possibile caricare più certificati per una gestione delle chiavi più avanzata.
   
## <a name="code-sample"></a>Esempio di codice

> [!NOTE]
> È necessario calcolare l'intestazione X5T usando l'hash del certificato e convertirlo in una stringa Base64. C# Il risultato sarà simile a quello di: `System.Convert.ToBase64String(cert.GetCertHash());`

Nell'esempio di codice su [Authenticating to Azure AD in daemon apps with certificates](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) (Autenticazione in Azure AD nelle app daemon con certificati) viene illustrato come un'applicazione usa le proprie credenziali per l'autenticazione. Illustra anche come è possibile [creare un certificato autofirmato](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) usando il comando `New-SelfSignedCertificate` di PowerShell. È possibile anche sfruttare e usare gli [script di creazione di app](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) per creare i certificati, calcolare l'identificazione personale e così via.
