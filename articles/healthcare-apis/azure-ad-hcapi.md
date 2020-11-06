---
title: Configurazione dell'identità Azure Active Directory per l'API di Azure per FHIR
description: Informazioni sui principi di identità, autenticazione e autorizzazione per i server FHIR di Azure.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: b362a81fc9b533fe00987a74d7e25dbba61a2589
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398250"
---
# <a name="azure-active-directory-identity-configuration-for-azure-api-for-fhir"></a>Configurazione dell'identità Azure Active Directory per l'API di Azure per FHIR

Una parte importante quando si lavora con i dati sanitari è garantire che i dati siano protetti e che non siano accessibili da utenti o applicazioni non autorizzati. I server FHIR usano [OAuth 2,0](https://oauth.net/2/) per garantire la protezione dei dati. L' [API di Azure per FHIR](https://azure.microsoft.com/services/azure-api-for-fhir/) è protetta usando [Azure Active Directory](../active-directory/index.yml), che è un esempio di provider di identità OAuth 2,0. Questo articolo fornisce una panoramica dell'autorizzazione del server FHIR e i passaggi necessari per ottenere un token per accedere a un server FHIR. Anche se questi passaggi si applicano a qualsiasi server FHIR e a qualsiasi provider di identità, Microsoft analizzerà l'API di Azure per FHIR come server FHIR e Azure AD come provider di identità in questo articolo.

## <a name="access-control-overview"></a>Panoramica del controllo di accesso

Per consentire a un'applicazione client di accedere all'API di Azure per FHIR, deve presentare un token di accesso. Il token di accesso è una raccolta di proprietà (attestazioni) con codifica [base64](https://en.wikipedia.org/wiki/Base64) firmata che fornisce informazioni sull'identità e sui ruoli e sui privilegi del client concessi al client.

Esistono diversi modi per ottenere un token, ma l'API di Azure per FHIR non è rilevante come viene ottenuto il token, purché sia un token firmato in modo appropriato con le attestazioni corrette. 

Se si usa il [flusso del codice di autorizzazione](../active-directory/azuread-dev/v1-protocols-oauth-code.md) come esempio, l'accesso a un server FHIR esegue i quattro passaggi seguenti:

![Autorizzazione FHIR](media/azure-ad-hcapi/fhir-authorization.png)

1. Il client invia una richiesta all' `/authorize` endpoint di Azure ad. Azure AD reindirizza il client a una pagina di accesso in cui l'utente eseguirà l'autenticazione con le credenziali appropriate, ad esempio nome utente e password o autenticazione a due fattori. Vedere i dettagli su [come ottenere un codice di autorizzazione](../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code). Al completamento dell'autenticazione, al client viene restituito un *codice di autorizzazione* . Azure AD consentirà la restituzione di questo codice di autorizzazione solo a un URL di risposta registrato configurato nella registrazione dell'applicazione client (vedere di seguito).
1. L'applicazione client scambia il codice di autorizzazione per un *token di accesso* all' `/token` endpoint di Azure ad. Quando si richiede un token, l'applicazione client potrebbe dover fornire un segreto client (la password delle applicazioni). Vedere i dettagli su [come ottenere un token di accesso](../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
1. Il client effettua una richiesta all'API di Azure per FHIR, ad esempio `GET /Patient` per cercare tutti i pazienti. Quando si esegue la richiesta, include il token di accesso in un'intestazione di richiesta HTTP, ad esempio `Authorization: Bearer eyJ0e...` , dove `eyJ0e...` rappresenta il token di accesso con codifica Base64.
1. L'API di Azure per FHIR convalida che il token contiene attestazioni appropriate (proprietà nel token). Se tutto viene verificato, completerà la richiesta e restituirà un bundle FHIR con i risultati al client.

È importante notare che l'API di Azure per FHIR non è richiesta per la convalida delle credenziali utente e non rilascia il token. La creazione di autenticazione e di token viene eseguita da Azure AD. L'API di Azure per FHIR convalida semplicemente che il token è firmato correttamente (è autentico) e che dispone di attestazioni appropriate.

## <a name="structure-of-an-access-token"></a>Struttura di un token di accesso

Lo sviluppo di applicazioni FHIR comporta spesso problemi di accesso al debug. Se a un client viene negato l'accesso all'API di Azure per FHIR, è utile comprendere la struttura del token di accesso e il modo in cui può essere decodificato per esaminare il contenuto (attestazioni) del token. 

I server FHIR in genere prevedono un [token JSON Web](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, talvolta pronunciato "Iota"). È costituito da tre parti:

1. Un'intestazione, che potrebbe essere simile alla seguente:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```
1. Payload (attestazioni), ad esempio:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```
1. Firma, calcolata concatenando il contenuto con codifica Base64 dell'intestazione e il payload e calcolando un hash crittografico in base all'algoritmo ( `alg` ) specificato nell'intestazione. Un server sarà in grado di ottenere le chiavi pubbliche dal provider di identità e di verificare che il token sia stato emesso da un provider di identità specifico e che non sia stato alterato.

Il token completo è costituito dalle versioni codificate Base64 (effettivamente codificate con URL Base64) di questi tre segmenti. I tre segmenti sono concatenati e separati da un `.` (punto).

Un token di esempio è illustrato di seguito:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

Il token può essere decodificato e controllato con strumenti quali [https://jwt.ms](https://jwt.ms) . Il risultato della decodifica del token è:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Come ottenere un token di accesso

Come indicato in precedenza, esistono diversi modi per ottenere un token da Azure AD. Sono descritte in dettaglio nella documentazione per gli [sviluppatori Azure ad](../active-directory/develop/index.yml).

Azure AD dispone di due diverse versioni degli endpoint OAuth 2,0, denominate `v1.0` e `v2.0` . Entrambe queste versioni sono endpoint OAuth 2,0 e le `v1.0` `v2.0` designazioni e si riferiscono alle differenze nel modo in cui Azure ad implementa tale standard. 

Quando si usa un server FHIR, è possibile usare gli `v1.0` endpoint o `v2.0` . La scelta può dipendere dalle librerie di autenticazione usate nell'applicazione client.

Le sezioni pertinenti della documentazione Azure AD sono:

* Endpoint `v1.0`:
    * [Flusso del codice di autorizzazione](../active-directory/azuread-dev/v1-protocols-oauth-code.md).
    * [Flusso di credenziali client](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).
* Endpoint `v2.0`:
    * [Flusso del codice di autorizzazione](../active-directory/develop/v2-oauth2-auth-code-flow.md).
    * [Flusso di credenziali client](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).

Sono presenti altre varianti, ad esempio per conto del flusso, per ottenere un token. Per informazioni dettagliate, vedere la documentazione Azure AD. Quando si usa l'API di Azure per FHIR, sono disponibili anche alcuni tasti di scelta rapida per ottenere un token di accesso (a scopo di debug) [usando l'interfaccia della](get-healthcare-apis-access-token-cli.md)riga di comando di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questo documento sono stati illustrati alcuni concetti di base relativi alla protezione dell'accesso all'API di Azure per FHIR usando Azure AD. Per informazioni su come distribuire un'istanza dell'API di Azure per FHIR, passare alla Guida introduttiva per la distribuzione.

>[!div class="nextstepaction"]
>[Distribuire l'API di Azure per FHIR](fhir-paas-portal-quickstart.md)