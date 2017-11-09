---
title: Autorizzazione di Azure AD da servizio a servizio con OAuth2.0 | Documentazione Microsoft
description: Questo articolo illustra come usare i messaggi HTTP per implementare l'autenticazione da servizio a servizio usando il flusso di concessione di credenziali client OAuth2.0.
services: active-directory
documentationcenter: .net
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: cc30a54cd56c0cb03a67f86e4552398baa764e58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# Chiamate da servizio a servizio mediante le credenziali client (certificato o segreto condiviso)
Il flusso di concessione delle credenziali client OAuth 2.0 consente a un servizio Web, (*client riservato*) di usare le proprie credenziali, invece di rappresentare un utente, per l'autenticazione durante la chiamata a un altro servizio Web. In questo scenario il client è in genere un servizio Web di livello intermedio, un servizio daemon o un sito Web. Per un livello più elevato di sicurezza, Azure AD consente al servizio chiamante di usare anche un certificato (invece di un segreto condiviso) come credenziale.

## Diagramma del flusso di concessione delle credenziali client
Il diagramma seguente illustra il funzionamento del flusso di concessione delle credenziali client in Azure Active Directory (Azure AD).

![Flusso di concessione delle credenziali client di OAuth 2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. L'applicazione client esegue l'autenticazione in corrispondenza dell'endpoint di emissione del token di Azure AD e richiede un token di accesso.
2. L'endpoint di emissione del token di Azure AD emette il token di accesso.
3. Il token di accesso viene usato per l'autenticazione nella risorsa protetta.
4. I dati della risorsa protetta vengono restituiti all'applicazione Web.

## Registrare i servizi in Azure AD
Registrare il servizio chiamante e il servizio ricevente in Azure Active Directory (Azure AD). Per altre informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md).

## Richiedere un token di accesso
Per richiedere un token di accesso, usare una richiesta HTTP POST per l'endpoint di Azure AD specifico del tenant.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## Richiesta del token di accesso da servizio a servizio
L'applicazione client può scegliere di essere protetta da un segreto condiviso oppure da un certificato.

### Primo caso: richiesta del token di accesso con un segreto condiviso
Quando si usa un segreto condiviso, una richiesta di token di accesso da servizio a servizio contiene i parametri seguenti:

| Parametro |  | Descrizione |
| --- | --- | --- |
| grant_type |Obbligatoria |Specifica il tipo di concessione richiesto. In un flusso di concessione delle credenziali client il valore deve essere **client_credentials**. |
| client_id |Obbligatoria |Specifica l'ID client di Azure AD del servizio Web chiamante. Per trovare l'ID client dell'applicazione chiamante, nel [portale Azure](https://portal.azure.com) fare clic su **Active Directory**, passare alla directory e fare clic sull'applicazione. client_id è l'*ID applicazione*. |
| client_secret |Obbligatoria |Immettere una chiave registrata per l'applicazione daemon o il servizio Web chiamante in Azure AD. Per creare una chiave, nel portale di Azure fare clic su **Active Directory**, passare alla directory, fare clic su **Impostazioni**, fare clic su **Chiavi** e aggiungere una chiave.|
| resource |Obbligatoria |Immettere l'URI ID app del servizio Web ricevente. Per trovare l'URI dell'ID app, nel portale di Azure fare clic su **Active Directory**, passare alla directory, fare clic sull'applicazione di servizio, quindi fare clic su **Impostazioni** e infine su **Proprietà**. |

#### Esempio
La richiesta HTTP POST seguente richiede un token di accesso per il servizio Web https://service.contoso.com/. `client_id` identifica il servizio Web che richiede il token di accesso.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### Secondo caso: richiesta del token di accesso con un certificato
Una richiesta di token di accesso da servizio a servizio con un certificato contiene i parametri seguenti:

| Parametro |  | Descrizione |
| --- | --- | --- |
| grant_type |Obbligatoria |Specifica il tipo di risposta richiesto. In un flusso di concessione delle credenziali client il valore deve essere **client_credentials**. |
| client_id |Obbligatoria |Specifica l'ID client di Azure AD del servizio Web chiamante. Per trovare l'ID client dell'applicazione chiamante, nel [portale Azure](https://portal.azure.com) fare clic su **Active Directory**, passare alla directory e fare clic sull'applicazione. client_id è l'*ID applicazione*. |
| client_assertion_type |Obbligatoria |Il valore deve essere `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`. |
| client_assertion |Obbligatoria | Un'asserzione (un token JSON Web) che è necessario creare e firmare con il certificato registrato come credenziale per l'applicazione. Leggere l'articolo relativo alle [credenziali basate su certificato](active-directory-certificate-credentials.md) per informazioni sulla registrazione del certificato e il formato dell'asserzione.|
| resource | Obbligatoria |Immettere l'URI ID app del servizio Web ricevente. Per trovare l'URI dell'ID app, nel portale di Azure fare clic su **Active Directory**, selezionare la directory, fare clic sull'applicazione e infine su **Configura**. |

Si noti che i parametri sono quasi uguali a quelli usati nella richiesta tramite segreto condiviso, con l'eccezione del parametro client_secret che viene sostituito da due parametri: client_assertion_type e client_assertion.

#### Esempio
La richiesta HTTP POST seguente richiede un token di accesso per il servizio Web https://service.contoso.com/ con un certificato. `client_id` identifica il servizio Web che richiede il token di accesso.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### Risposta del token di accesso da servizio a servizio

Una risposta corretta contiene una risposta OAuth 2.0 JSON con i parametri seguenti:

| Parametro | Descrizione |
| --- | --- |
| access_token |Token di accesso richiesto. Il servizio Web chiamante può usare questo token per l'autenticazione nel servizio Web ricevente. |
| token_type |Indica il valore del tipo di token. L'unico tipo supportato da Azure AD è **Bearer**. Per altre informazioni sui token di connessione, vedere [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)(Framework di autorizzazione di OAuth2.0: uso dei token di connessione - RFC 6750). |
| expires_in |Validità del token di accesso (espressa in secondi). |
| expires_on |Scadenza del token di accesso. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino alla scadenza. Questo valore viene usato per determinare la durata dei token memorizzati nella cache. |
| not_before |Ora da cui il token di accesso diventa utilizzabile. La data è rappresentata come numero di secondi da 1970-01-01T0:0:0Z UTC fino all'ora di validità per il token.|
| resource |URI ID app del servizio Web ricevente. |

#### Esempio di risposta
L'esempio seguente mostra una risposta corretta a una richiesta di token di accesso per un servizio Web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## Vedere anche
* [OAuth 2.0 in Azure AD](active-directory-protocols-oauth-code.md)
* [Esempio in C# della chiamata da servizio a servizio con un segreto condiviso](https://github.com/Azure-Samples/active-directory-dotnet-daemon) ed [esempio in C# della chiamata da servizio a servizio con un certificato](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
