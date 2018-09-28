---
title: Informazioni di riferimento sui token di accesso di Azure Active Directory | Microsoft Docs
description: Accettazione dei token di accesso generati dagli endpoint v1.0 e v2.0 di Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: df02ab01-3490-419b-90f5-be7adaeadd58
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a047a36112ff599038500f0cb58305f07a5838dd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994820"
---
# <a name="azure-active-directory-access-tokens"></a>Token di accesso di Azure Active Directory

I token di accesso consentono ai client di chiamare in modo sicuro le API protette da Azure. I token di accesso di Azure Active Directory (Azure AD) sono [token JWT](https://tools.ietf.org/html/rfc7519) JSON con codifica Base64 firmati da Azure. I client devono trattare i token di accesso come stringhe opache, in quanto i contenuti dei token sono destinati solo alla risorsa. A scopo di convalida e debug, gli sviluppatori possono decodificare i token JWT usando un sito come [jwt.ms](https://jwt.ms). Il client può ottenere un token di accesso da un endpoint (v1.0 o v2.0) tramite un'ampia gamma di protocolli.

Quando si richiede un token di accesso, Azure AD restituisce anche alcuni metadati relativi al token di accesso per l'utilizzo dell'app. Queste informazioni includono l'ora di scadenza del token di accesso e gli ambiti per i quali è valido. Questi dati consentono all'app di eseguire la memorizzazione intelligente nella cache dei token di accesso senza la necessità di analizzare il token di accesso stesso.

Se l'applicazione è una risorsa (API Web) a cui i client possono richiedere l'accesso, i token di accesso forniscono informazioni utili per l'autenticazione e l'autorizzazione, ad esempio utente, client, autorità emittente, autorizzazioni e molto altro. 

Vedere le sezioni seguenti per informazioni su come una risorsa può convalidare e usare le attestazioni all'interno di un token di accesso.

> [!NOTE]
> Quando si testa un'applicazione client con un account personale (ad esempio hotmail.com o outlook.com), è possibile notare che il token di accesso ricevuto dal client è una stringa opaca. Ciò avviene perché la risorsa a cui si accede ha richiesto ticket dell'account del servizio gestito (account Microsoft) legacy che sono crittografati e non possono essere riconosciuti dal client.

## <a name="sample-tokens"></a>Token di esempio

I token v1.0 e v2.0 sono molto simili e contengono molte delle stesse attestazioni. Di seguito sono illustrati esempi di ognuno.

### <a name="v10"></a>v1.0

   `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd`

Visualizzare questo token v1.0 in [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0=.D3H6pMUtQnoJAGq6AHd).

### <a name="v20"></a>v2.0

   `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt`

Visualizzare questo token v2.0 in [JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt).

## <a name="claims-in-access-tokens"></a>Attestazioni nei token di accesso

I token JWT sono suddivisi in tre parti: 

* **Intestazione**: fornisce informazioni su come [convalidare il token](#validating-tokens), incluse informazioni sul tipo di token e su come è stato firmato.
* **Payload**: contiene tutti i dati importanti relativi all'utente o all'app che sta cercando di chiamare il servizio.
* **Firma**: rappresenta il materiale non elaborato usato per convalidare il token.

Ogni parte è separata da un punto (`.`) e a ogni parte viene applicata separatamente la codifica Base64.

Le attestazioni sono presenti solo se c'è un valore da inserire. Di conseguenza, l'app non deve avere una dipendenza legata alla presenza di un'attestazione. Gli esempi includono `pwd_exp` (non ogni tenant richiede la scadenza delle password) o `family_name` (i flussi delle [credenziali client](v1-oauth2-client-creds-grant-flow.md) avvengono per conto delle applicazioni, che non hanno nomi). Le attestazioni usate per la convalida dei token di accesso sono sempre presenti.

> [!NOTE]
> Alcune attestazioni vengono usate per proteggere i token in Azure AD in caso di riutilizzo. In questo caso vengono descritte come "opache" per indicare che non sono destinate all'uso pubblico. Queste attestazioni possono o meno essere presenti in un token e possono venirne aggiunte di nuove senza preavviso.

### <a name="header-claims"></a>Attestazioni di intestazione

|Attestazione | Format | DESCRIZIONE |
|--------|--------|-------------|
| `typ` | Stringa, sempre "JWT" | Indica che il token è un token JWT.|
| `nonce` | string | Identificatore univoco usato per la protezione contro attacchi di riproduzione dei token. La risorsa può registrare questo valore per la protezione da operazioni di riproduzione. |
| `alg` | string | Indica l'algoritmo usato per firmare il token, ad esempio "RS256" |
| `kid` | string | Specifica l'identificazione personale per la chiave pubblica usata per firmare il token. Generata nei token di accesso sia v1.0 che v2.0. |
| `x5t` | string | Funziona in modo analogo, per uso e valore, a `kid`. Si tratta di un'attestazione legacy generata solo nei token di accesso v1.0 per motivi di compatibilità. |

### <a name="payload-claims"></a>Attestazioni di payload

| Attestazione | Format | DESCRIZIONE |
|-----|--------|-------------|
| `aud` | Stringa, un URI ID app | Identifica il destinatario del token. Nei token di accesso il destinatario è l'ID applicazione assegnato all'app nel portale di Azure. L'app deve convalidare questo valore e rifiutare il token se il valore non corrisponde. |
| `iss` | Stringa, un URI del servizio token di sicurezza | Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token e il tenant di Azure AD in cui l'utente è stato autenticato. Se il token è stato rilasciato dall'endpoint v2.0, l'URI termina con `/v2.0`. Il GUID che indica che l'utente è un utente consumer di un account Microsoft è `9188040d-6c67-4c5b-b112-36a304b66dad`. L'app deve usare la parte relativa al GUID dell'attestazione per limitare il set di tenant che possono accedere all'app, se applicabile. |
| `iat` | int, timestamp UNIX | "Issued At" indica quando è avvenuta l'autenticazione per il token. |
| `nbf` | int, timestamp UNIX | L'attestazione "nbf" (not before) identifica l'ora prima della quale il token JWT non deve essere accettato per l'elaborazione. |
| `exp` | int, timestamp UNIX | L'attestazione "exp" (expiration time) identifica l'ora di scadenza a partire dalla quale o successivamente alla quale il token JWT non deve essere accettato per l'elaborazione. È importante notare che una risorsa può rifiutare il token anche prima di questa ora, ad esempio quando è necessaria una modifica nell'autenticazione oppure quando è stata rilevata una revoca di un token. |
| `acr` | Stringa, "0" o "1" | Attestazione "Authentication context class" (classe contesto di autenticazione). Il valore "0" indica che l'autenticazione dell'utente finale non soddisfa i requisiti ISO/IEC 29115. |
| `aio` | Stringa opaca | Attestazione interna usata da Azure AD per registrare i dati per il riutilizzo dei token. Le risorse non devono usare questa attestazione. |
| `amr` | Matrice di stringhe JSON | Presente solo nei token v1.0. Identifica la modalità di autenticazione dell'oggetto del token. Per maggiori dettagli, vedere la [sezione relativa all'attestazione amr](#the-amr-claim). |
| `appid` | Stringa, un GUID | Presente solo nei token v1.0. ID applicazione del client che usa il token. L'applicazione può fungere per conto proprio o per conto dell'utente. L'ID dell'applicazione in genere rappresenta un oggetto applicazione, ma può anche rappresentare un oggetto di entità servizio in Azure AD. |
| `appidacr` | "0", "1" o "2" | Presente solo nei token v1.0. Indica la modalità di autenticazione del client. Per un client pubblico, il valore è "0". Se vengono usati l'ID client e il segreto client, il valore è "1". Se per l'autenticazione è stato usato un certificato client, il valore è "2". |
| `azp` | Stringa, un GUID | Presente solo nei token v2.0. ID applicazione del client che usa il token. L'applicazione può fungere per conto proprio o per conto dell'utente. L'ID dell'applicazione in genere rappresenta un oggetto applicazione, ma può anche rappresentare un oggetto di entità servizio in Azure AD. |
| `azpacr` | "0", "1" o "2" | Presente solo nei token v2.0. Indica la modalità di autenticazione del client. Per un client pubblico, il valore è "0". Se vengono usati l'ID client e il segreto client, il valore è "1". Se per l'autenticazione è stato usato un certificato client, il valore è "2". |
| `groups` | Matrice di GUID JSON | Fornisce gli ID oggetto che rappresentano le appartenenze ai gruppi del soggetto. Questi valori sono univoci (vedere ID oggetto) e possono essere usati in modo sicuro per la gestione dell'accesso, ad esempio l'attivazione dell'autorizzazione per accedere a una risorsa. I gruppi inclusi nell'attestazione dei gruppi sono configurati per ogni singola applicazione, tramite la proprietà `groupMembershipClaims` del [manifesto dell'applicazione](reference-app-manifest.md). Un valore null escluderà tutti i gruppi, un valore "SecurityGroup" includerà solo le appartenenze al gruppo di sicurezza di Active Directory e un valore "All" includerà sia i gruppi di sicurezza che le liste di distribuzione di Office 365. <br><br>Vedere di seguito l'attestazione `hasgroups` per informazioni dettagliate sull'uso dell'attestazione `groups` con la concessione implicita. <br>Per gli altri flussi, se il numero di gruppi in cui è presente l'utente è superiore al limite (150 per SAML, 200 per JWT), viene aggiunta un'attestazione di eccedenza alle origini delle attestazioni, che punta all'endpoint Graph che contiene l'elenco dei gruppi per l'utente. |
| `hasgroups` | boolean | Se presente, sempre `true`, per indicare che l'utente è presente in almeno un gruppo. Usato invece dell'attestazione `groups` per i token JWT nei flussi di concessione implicita se l'attestazione completa dei gruppi estenderebbe il frammento dell'URI oltre i limiti di lunghezza dell'URL (attualmente 6 o più gruppi). Indica che il client deve usare Graph per determinare i gruppi dell'utente (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` | Oggetto JSON | Per le richieste di token che non hanno un limite di lunghezza (vedere `hasgroups` più indietro), ma sono comunque troppo grandi per il token, verrà incluso un collegamento all'elenco di gruppi completo per l'utente. Per i token JWT come un'attestazione distribuita, per SAML come una nuova attestazione invece dell'attestazione `groups`. <br><br>**Valore token JWT di esempio**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `preferred_name` | string | Presente solo nei token v2.0. Nome utente primario che rappresenta l'utente. Potrebbe trattarsi di un indirizzo di posta elettronica, di un numero di telefono o di un nome utente generico senza un formato specificato. Il valore è modificabile e può variare nel tempo. Poiché è mutevole, questo valore non deve essere usato per prendere decisioni in merito alle autorizzazioni. Per ricevere questa attestazione, è necessario l'ambito `profile` . |
| `name` | string | Fornisce un valore leggibile che identifica l'oggetto del token. Il valore potrebbe non essere univoco, è modificabile e può essere usato solo per scopi di visualizzazione. Per ricevere questa attestazione, è necessario l'ambito `profile` . |
| `oid` | Stringa, un GUID | Identificatore non modificabile per un oggetto in Microsoft Identity Platform, in questo caso un account utente. Può essere usato anche per eseguire i controlli di autorizzazioni in modo sicuro e come chiave nelle tabelle di database. Questo ID identifica in modo univoco l'utente nelle applicazioni; due applicazioni differenti che consentono l'accesso dello stesso utente riceveranno lo stesso valore nell'attestazione `oid`. È quindi possibile usare `oid` quando si eseguono query sui Microsoft Online Services, ad esempio Microsoft Graph. Microsoft Graph restituirà l'ID come proprietà `id` per un determinato account utente. Poiché `oid` consente a più app di correlare utenti, per ricevere questa attestazione è necessario l'ambito `profile`. Si noti che se un singolo utente è presente in più tenant, l'utente conterrà un ID oggetto diverso in ogni tenant; vengono considerati account diversi, anche se l'utente accede a ogni account con le stesse credenziali. |
| `rh` | Stringa opaca | Attestazione interna usata da Azure per riconvalidare i token. Le risorse non devono usare questa attestazione. |
| `scp` | Stringa, elenco di ambiti separati da spazi | Set di ambiti esposti dall'applicazione per cui l'applicazione client ha richiesto (e ricevuto) il consenso. L'app deve verificare che questi ambiti siano ambiti validi esposti dall'app e prendere decisioni relative alle autorizzazioni in base al valore di questi ambiti. Inclusa solo per i [token utente](#user-and-application-tokens). |
| `roles`| Stringa, elenco di autorizzazioni separate da spazi | Set di autorizzazioni esposte dall'applicazione per le quali l'applicazione richiedente ha ricevuto l'autorizzazione di chiamata. Viene usata durante il flusso di [credenziali client](v1-oauth2-client-creds-grant-flow.md) al posto degli ambiti utente ed è presente solo nei [token delle applicazioni](#user-and-application-tokens). |
| `sub` | Stringa, un GUID | Entità su cui il token asserisce informazioni, ad esempio l'utente di un'app. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. Può essere usato per eseguire controlli di autorizzazione in modo sicuro, ad esempio quando il token viene usato per accedere a una risorsa oppure come chiave nelle tabelle di database. Dato che il soggetto è sempre presente nei token generati da Azure AD, è consigliabile usare questo valore in un sistema di autorizzazione di uso generico. L'oggetto è tuttavia un identificatore pairwise univoco per un ID di applicazione specifico. Se quindi un singolo utente accede a due app diverse usando due ID client differenti, queste app riceveranno due valori differenti per l'attestazione dell'oggetto. Questa condizione può non essere appropriata a seconda dei requisiti a livello di architettura e di privacy. |
| `tid` | Stringa, un GUID | Rappresenta il tenant di Azure AD da cui proviene l'utente. Per gli account aziendali e dell'istituto di istruzione, il GUID è l'ID tenant non modificabile dell'organizzazione a cui appartiene l'utente. Per gli account personali il valore è `9188040d-6c67-4c5b-b112-36a304b66dad`. Per ricevere questa attestazione, è necessario l'ambito `profile` . |
| `unique_name` | string | Presente solo nei token v1.0. Fornisce un valore leggibile che identifica l'oggetto del token. Questo valore potrebbe non essere univoco all'interno di un tenant e deve essere usato solo per scopi di visualizzazione. |
| `upn` | string | Nome utente dell'utente. Può essere un numero di telefono, un indirizzo di posta elettronica o una stringa non formattata. Deve essere usata solo per scopi di visualizzazione e per fornire suggerimenti per il nome utente in scenari di ripetizione dell'autenticazione. |
| `uti` | Stringa opaca | Attestazione interna usata da Azure per riconvalidare i token. Le risorse non devono usare questa attestazione. |
| `ver` | Stringa, 1.0 o 2.0 | Indica la versione del token di accesso. |

#### <a name="v10-basic-claims"></a>Attestazioni di base v1.0

Le attestazioni seguenti vengono incluse nei token v1.0, se applicabili, ma non sono incluse nei token v2.0 per impostazione predefinita. Se si usa la versione 2.0 ed è necessaria una di queste attestazioni, richiederla usando le [attestazioni facoltative](active-directory-optional-claims.md).

| Attestazione | Format | DESCRIZIONE |
|-----|--------|-------------|
| `ipaddr`| string | Indirizzo IP da cui l'utente ha eseguito l'autenticazione. |
| `onprem_sid`| Stringa, in [formato SID](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | Nei casi in cui l'utente ha un'autenticazione in locale, questa attestazione fornisce il relativo SID. Può essere usata per l'autorizzazione nelle applicazioni legacy. |
| `pwd_exp`| int, timestamp UNIX | Indica quando scade la password dell'utente. |
| `pwd_url`| string | URL a cui gli utenti possono essere rimandati per reimpostare la password. |
| `in_corp`|boolean | Segnala se il client sta effettuando l'accesso dalla rete aziendale. In caso contrario, l'attestazione non è inclusa. |
| `nickname`| string | Nome aggiuntivo per l'utente, distinto dal nome o dal cognome.|
| `family_name` | string | Fornisce il cognome dell'utente, come definito nell'oggetto utente. |
| `given_name` | string | Fornisce il nome dell'utente, come impostato nell'oggetto utente. |

#### <a name="the-amr-claim"></a>Attestazione `amr`

Le identità Microsoft possono autenticarsi in diversi modi, che possono essere pertinenti per l'applicazione. L'attestazione `amr` è una matrice che può contenere più elementi, ad esempio `["mfa", "rsa", "pwd"]`, per un'autenticazione che ha usato sia una password che l'app Authenticator. 

| Valore | DESCRIZIONE |
|-----|-------------|
| `pwd` | Autenticazione della password, ovvero una password Microsoft dell'utente o un segreto client dell'app. |
| `rsa` | L'autenticazione è avvenuta in base alla prova di una chiave RSA, ad esempio con l'[app Microsoft Authenticator](https://aka.ms/AA2kvvu). Indica se l'autenticazione è stata eseguita da un token JWT autofirmato con un certificato X509 di proprietà del servizio. |
| `otp` | Passcode monouso tramite un messaggio di posta elettronica o un SMS. |
| `fed` | È stata usata un'asserzione di autenticazione federata (ad esempio token JWT o SAML). |
| `wia` | Autenticazione integrata di Windows |
| `mfa` | È stata usata l'autenticazione a più fattori. Quando è presente sono inclusi anche gli altri metodi di autenticazione. |
| `ngcmfa` | Equivalente a `mfa`, usata per il provisioning di determinati tipi di credenziali avanzate. |
| `wiaormfa`| L'utente ha usato Windows o una credenziale di autenticazione a più fattori per l'autenticazione. |
| `none` | Non è stata eseguita alcuna autenticazione. |

## <a name="validating-tokens"></a>Convalida dei token

Per convalidare un oggetto id_token o access_token, l'app deve convalidare sia la firma del token che le attestazioni. Per convalidare i token di accesso, l'applicazione deve convalidare anche l'autorità emittente, i destinatari e i token di firma. Questi elementi devono essere convalidati in base ai valori contenuti nel documento di individuazione OpenID. Ad esempio, la versione indipendente dal tenant del documento si trova all'indirizzo [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration). 

Il middleware di Azure AD offre funzionalità predefinite per la convalida dei token di accesso ed è possibile cercare tra gli [esempi](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) per trovarne una nel linguaggio preferito. Per altre informazioni sulla convalida esplicita di un token JWT, vedere l'[esempio di convalida manuale del token JWT](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Microsoft fornisce esempi di codice e librerie che illustrano come gestire in modo semplice la convalida dei token. Le informazioni seguenti vengono fornite per chi vuole comprendere il processo sottostante. Sono disponibili anche numerose librerie open source di terze parti per la convalida dei token JWT. C'è almeno un'opzione per ogni piattaforma e ogni linguaggio. Per altre informazioni sulle librerie di autenticazione di Azure AD e per ottenere esempi di codice, vedere [Librerie di autenticazione v1.0](active-directory-authentication-libraries.md) e [Librerie di autenticazione v2.0](reference-v2-libraries.md).

### <a name="validating-the-signature"></a>Convalida della firma

Un token JWT contiene tre segmenti separati dal carattere `.` . Il primo segmento è noto come **intestazione**, il secondo come **corpo** e il terzo come **firma**. Il segmento di firma può essere usato per convalidare l'autenticità del token, in modo che possa essere considerato attendibile dall'app.

I token emessi da Azure AD vengono firmati usando algoritmi di crittografia asimmetrica standard del settore, come RSA 256. L'intestazione del token JWT contiene informazioni sulla chiave e sul metodo di crittografia usati per firmare il token:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

L'attestazione `alg` indica l'algoritmo usato per firmare il token, mentre l'attestazione `kid` indica la chiave pubblica specifica usata per firmare il token.

In qualsiasi momento, Azure AD può firmare un token ID usando un determinato set di coppie di chiavi pubbliche/private. Azure AD ruota il set di chiavi su base periodica, quindi l'app deve essere scritta in modo da gestire automaticamente le modifiche delle chiavi. Una frequenza ragionevole per la ricerca di aggiornamenti per le chiavi pubbliche usate da Azure AD è di circa 24 ore.

È possibile acquisire i dati della chiave per la firma necessari per convalidare la firma usando il documento dei metadati OpenID Connect disponibile all'indirizzo:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Provare questo URL in un browser.

Il documento di metadati ha le caratteristiche seguenti:

* È un oggetto JSON contenente diverse informazioni utili, ad esempio la posizione dei vari endpoint necessari per eseguire l'autenticazione di OpenID Connect. 
* Include un oggetto `jwks_uri` che fornisce la posizione del set di chiavi pubbliche usate per firmare i token. Il documento JSON disponibile in `jwks_uri` contiene tutte le informazioni sulla chiave pubblica usata in un determinato momento. L'app può usare l'attestazione `kid` nell'intestazione JWT per selezionare quale chiave pubblica è stata usata in questo documento per firmare un determinato token. Può quindi eseguire la convalida della firma usando la chiave pubblica corretta e l'algoritmo indicato.

> [!NOTE]
> L'endpoint v1.0 restituisce entrambe le attestazioni `x5t` e `kid`. L'attestazione `x5t` non è presente nei token v2.0. L'endpoint v2.0 risponde con l'attestazione `kid`. In futuro è consigliabile usare l'attestazione `kid` per convalidare il token.

L'esecuzione della convalida della firma non rientra nell'ambito di questo documento, sono tuttavia disponibili numerose librerie open source che contengono informazioni per eseguire questa operazione.

### <a name="claims-based-authorization"></a>Autorizzazione basata su attestazioni

La logica di business dell'applicazione richiede questo passaggio e di seguito sono elencati alcuni metodi di autorizzazione comuni.

* Controllare l'attestazione `scp` o `roles` per verificare che tutti gli ambiti presenti corrispondano a quelli esposti dall'API e permettere al client di eseguire l'azione richiesta.
* Verificare che il client chiamante sia autorizzato a chiamare l'API usando l'attestazione `appid`.
* Convalidare lo stato dell'autenticazione del client chiamante usando `appidacr`: il valore non deve essere 0 se i client pubblici non sono autorizzati a chiamare l'API.
* Eseguire una verifica in base a un elenco di attestazioni `nonce` passate per controllare che il token non venga riprodotto.
* Controllare che `tid` corrisponda a un tenant autorizzato a chiamare l'API.
* Usare l'attestazione `acr` per verificare che l'utente abbia eseguito l'autenticazione a più fattori. Si noti che ciò può essere applicato usando l'[accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).
* Se è stata richiesta l'attestazione `roles` o `groups` nel token di accesso, verificare che l'utente sia incluso nel gruppo autorizzato a eseguire questa azione.
  * Per i token recuperati tramite il flusso implicito, sarà probabilmente necessario eseguire una query su [Graph](https://developer.microsoft.com/graph/) per ottenere questi dati, in quanto in genere le dimensioni sono troppo grandi per l'inserimento nel token. 

## <a name="user-and-application-tokens"></a>Token utente e dell'applicazione

L'applicazione può ricevere token per conto di un utente (flusso normale) o direttamente da un'applicazione (tramite il [flusso di credenziali client](v1-oauth2-client-creds-grant-flow.md)). Questi token solo app indicano che la chiamata proviene da un'applicazione e non da un utente. Questi token vengono gestiti in modo per lo più analogo, ma con alcune differenze:

* I token solo app non hanno un'attestazione `scp`, ma hanno invece un'attestazione `roles`. In questa posizione verranno registrate le autorizzazioni dell'applicazione (in contrapposizione alle autorizzazioni delegate). Per altre informazioni sulle autorizzazioni dell'applicazione e delegate, vedere le informazioni relative ad autorizzazioni e consenso in [v1.0](v1-permissions-and-consent.md) e [v2.0](v2-permissions-and-consent.md).
* Molte attestazioni di carattere umano non sono presenti, ad esempio `name`.

## <a name="token-revocation"></a>Revoca dei token

I token di aggiornamento possono essere invalidati in qualsiasi momento per vari motivi. Questi motivi appartengono a due categorie: timeout e revoca.

### <a name="token-timeouts"></a>Timeout dei token

* MaxInactiveTime: se il token di aggiornamento non è stato usato entro il tempo specificato da MaxInactiveTime, non è più valido. 
* MaxSessionAge: se il parametro MaxAgeSessionMultiFactor o MaxAgeSessionSingleFactor è stato impostato su un valore diverso da quello predefinito (Until-revoked), sarà necessaria una nuova autenticazione allo scadere del tempo impostato in MaxAgeSession*. 
* Esempi:
  * Il tenant ha un parametro MaxInactiveTime impostato su cinque giorni, ma l'utente è andato in vacanza per una settimana e di conseguenza AAD non ha riscontrato una nuova richiesta di token da parte dell'utente per sette giorni. Alla successiva richiesta di un token, l'utente noterà che il token di aggiornamento è stato revocato e dovrà immettere di nuovo le credenziali.
  * Nel caso di un'applicazione riservata, MaxAgeSessionSingleFactor è impostato su un giorno. Se un utente accede il lunedì e il martedì (dopo che sono trascorse 25 ore) dovrà autenticarsi di nuovo.

### <a name="revocation"></a>Revoca

|   | Cookie basato su password | Token basato su password | Cookie non basato su password | Token non basato su password | Token client riservato| 
|---|-----------------------|----------------------|---------------------------|--------------------------|--------------------------|
| Scadenza password | Rimarrà attivo| Rimarrà attivo | Rimarrà attivo | Rimarrà attivo | Rimarrà attivo |
| Password cambiata dall'utente | Revocato | Revocato | Rimarrà attivo | Rimarrà attivo | Rimarrà attivo |
| Utente esegue SSPR | Revocato | Revocato | Rimarrà attivo | Rimarrà attivo | Rimarrà attivo |
| Amministratore reimposta password | Revocato | Revocato | Rimarrà attivo | Rimarrà attivo | Rimarrà attivo |
| Utente revoca token di aggiornamento [tramite PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Revocato | Revocato |Revocato | Revocato |Revocato | Revocato |
| Amministrazione revoca tutti i token di aggiornamento per il tenant [tramite PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Revocato | Revocato |Revocato | Revocato |Revocato | Revocato |
| [Single Sign-Out](v1-protocols-openid-connect-code.md#single-sign-out) nel Web | Revocato | Rimarrà attivo | Revocato | Rimarrà attivo | Rimarrà attivo |

> [!NOTE]
> Un accesso "Non basato su password" si ha quando l'utente non digita alcuna password per accedere. Ad esempio, con il riconoscimento facciale di Windows Hello, una chiave FIDO o un PIN. 
>
> Vi è un problema noto con il token di aggiornamento primario di Windows. Se quest'ultimo viene ottenuto tramite una password e quindi l'utente esegue l'accesso tramite Hello, non si modifica l'origine del token di aggiornamento primario e questo verrà revocato se l'utente modifica la password.

## <a name="next-steps"></a>Passaggi successivi

* Leggere le informazioni su [`id_tokens` in Azure AD](id-tokens.md).
* Leggere le informazioni su autorizzazioni e consenso in [v1.0](v1-permissions-and-consent.md) e [v2.0](v2-permissions-and-consent.md).
