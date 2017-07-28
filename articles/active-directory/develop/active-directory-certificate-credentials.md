---
title: Credenziali del certificato in Azure AD | Microsoft Docs
description: Questo articolo illustra la registrazione e l&quot;uso delle credenziali del certificato per l&quot;autenticazione dell&quot;applicazione
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/02/2017
ms.author: nacanuma
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 08bb5140bb35bbd120aaa506afeab8ad247f81e1
ms.contentlocale: it-it
ms.lasthandoff: 06/17/2017


---

# <a name="certificate-credentials-for-application-authentication"></a>Credenziali del certificato per l'autenticazione dell'applicazione

Azure Active Directory consente a un'applicazione di usare le proprie credenziali per l'autenticazione, ad esempio, nel flusso di concessione delle credenziali client di OAuth 2.0 e nel flusso on-behalf-of.
Un tipo di credenziale che può essere usato è un'asserzione di token JSON Web (JWT) firmata con un certificato di proprietà dell'applicazione.

## <a name="format-of-the-assertion"></a>Formato dell'asserzione
Per calcolare l'asserzione, è preferibile usare una delle numerose librerie di [token JSON Web](https://jwt.io/) nel linguaggio scelto. Le informazioni incluse nel token sono:

#### <a name="header"></a>Intestazione

| Parametro |  Commento |
| --- | --- | --- |
| `alg` | Deve essere **RS256** |
| `typ` | Deve essere **JWT** |
| `x5t` | Deve essere l'identificazione personale SHA-1 del certificato X.509 |

#### <a name="claims-payload"></a>Attestazioni (payload)

| Parametro |  Commento |
| --- | --- | --- |
| `aud` | Gruppo di destinatari: deve essere **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | Data di scadenza: la data di scadenza del token. L'ora è rappresentata come numero di secondi dal 1° gennaio 1970 (1970-01-01T0:0:0Z) UTC fino all'ora in cui scade la validità del token.|
| `iss` | Autorità di certificazione: deve essere il parametro client_id (ID applicazione del servizio client) |
| `jti` | GUID: l'ID token JWT |
| `nbf` | Non prima: la data prima della quale il token non può essere usato. La data e l'ora sono rappresentate come numero di secondi dal 1 gennaio 1970 (1970-01-01T0:0:0Z) UTC fino alla data e all'ora in cui il token è stato rilasciato. |
| `sub` | Oggetto: per quanto riguarda `iss`, deve essere il parametro client_id (ID applicazione del servizio client) |

#### <a name="signature"></a>Firma
La firma viene calcolata applicando il certificato come descritto nella [specifica RFC7519 sul token JSON Web](https://tools.ietf.org/html/rfc7519)

### <a name="example-of-a-decoded-jwt-assertion"></a>Esempio di asserzione del token JWT decodificata
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

### <a name="example-of-an-encoded-jwt-assertion"></a>Esempio di asserzione del token JWT codificata
La stringa seguente è un esempio di asserzione codificata. Se si osserva con attenzione, è possibile notare tre sezioni separate da punti (.).
La prima sezione codifica l'intestazione, la seconda il payload e l'ultima è la firma calcolata con i certificati a partire dal contenuto delle prime due sezioni.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>Registrare il certificato con Azure AD
Per associare la credenziale del certificato all'applicazione client in Azure AD, è necessario modificare il manifesto dell'applicazione.
Con un certificato disponibile, è necessario calcolare:
- `$base64Thumbprint`, che è la codifica Base 64 dell'hash del certificato
- `$base64Value`, che è la codifica Base 64 dei dati non elaborati dell'hash del certificato

È anche necessario specificare un GUID per identificare la chiave nel manifesto dell'applicazione (`$keyId`).

Nella registrazione dell'app di Azure per l'applicazione client aprire il manifesto dell'applicazione e sostituire la proprietà *keyCredentials* con le informazioni del nuovo certificato usando lo schema seguente:
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

Salvare le modifiche apportate al manifesto dell'applicazione e caricare in Azure AD. La proprietà keyCredentials è multivalore, quindi è possibile caricare più certificati per una gestione delle chiavi più completa.

