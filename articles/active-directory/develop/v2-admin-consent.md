---
title: Protocolli di consenso dell'amministratore di Microsoft Identity Platform
description: Descrizione dell'autorizzazione nell'endpoint della piattaforma di identità Microsoft, inclusi gli ambiti, le autorizzazioni e il consenso.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 537d609c1281929203d1891f37614b7627e1683a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868675"
---
# <a name="admin-consent-on-the-microsoft-identity-platform"></a>Consenso dell'amministratore sulla piattaforma di identità Microsoft

Alcune autorizzazioni richiedono il consenso di un amministratore per poter essere concesse all'interno di un tenant.  È anche possibile usare l'endpoint di consenso dell'amministratore per concedere le autorizzazioni a un intero tenant.

## <a name="recommended-sign-the-user-into-your-app"></a>Consigliato: accedere all'utente nell'app

In genere, durante la compilazione di un'applicazione che usa l'endpoint di consenso dell'amministratore, l'app necessita di una pagina o vista che consenta all'amministratore di approvare le autorizzazioni dell'applicazione. Questa pagina può essere parte del flusso di iscrizione all'app, delle impostazioni dell'applicazione o di un flusso di "connessione" dedicato. In molti casi, è utile per l'applicazione visualizzare la pagina di "connessione" solo dopo che un utente ha eseguito l'accesso con un account di lavoro o dell'istituto di istruzione Microsoft.

L'accesso dell'utente nell'app consente di identificarne l'organizzazione di appartenenza dell'amministratore prima di richiedere l'approvazione delle autorizzazioni necessarie. Sebbene non sia strettamente necessario, questo consente di creare un'esperienza più intuitiva per gli utenti dell'organizzazione. Per accedere all'utente, seguire le [esercitazioni del protocollo Microsoft Identity Platform](active-directory-v2-protocols.md).

## <a name="request-the-permissions-from-a-directory-admin"></a>Richiedere le autorizzazioni da un amministratore di directory

Quando si è pronti per richiedere le autorizzazioni all'amministratore dell'organizzazione, è possibile reindirizzare l'utente all'endpoint di *consenso dell'amministratore*di Microsoft Identity Platform.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parametro     | Condizione     | Descrizione                                                                               |
|--------------:|--------------:|:-----------------------------------------------------------------------------------------:|
| `tenant` | Obbligatoria | Il tenant della directory da cui si desidera richiedere autorizzazioni. Può essere specificato in formato di GUID o nome descrittivo OPPURE con il riferimento generico `organizations` come illustrato nell'esempio. Non usare ' Common ', perché gli account personali non possono fornire il consenso dell'amministratore tranne nel contesto di un tenant. Per garantire la massima compatibilità con gli account personali per la gestione dei tenant, usare l'ID tenant quando possibile. |
| `client_id` | Obbligatoria | **ID dell'applicazione (client)** che la [portale di Azure registrazioni app](https://go.microsoft.com/fwlink/?linkid=2083908) l'esperienza assegnata all'app. |
| `redirect_uri` | Obbligatoria |URI di reindirizzamento in cui si desidera che venga inviata la risposta per la gestione da parte dell'app. Deve corrispondere esattamente a uno degli URI di reindirizzamento registrati nel portale di registrazione delle applicazioni. |
| `state` | Consigliato | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Usare questo stato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava. |
|`scope`        | Obbligatoria      | Definisce il set di autorizzazioni richieste dall'applicazione. Può trattarsi di un ambito statico (con/.default) o di ambiti dinamici.  Possono essere inclusi gli ambiti OIDC (`openid`, `profile`, `email`). |


A questo punto, Azure AD richiede che solo un amministratore tenant possa accedere per completare la richiesta. All'amministratore viene chiesto di approvare tutte le autorizzazioni richieste nel `scope` parametro.  Se è stato usato un valore statico`/.default`(), funzionerà come l'endpoint di consenso dell'amministratore della versione 1.0 e richiederà il consenso per tutti gli ambiti presenti nelle autorizzazioni necessarie per l'app.

### <a name="successful-response"></a>Risposta di esito positivo

Se l'amministratore approva le autorizzazioni per l'app, la risposta con esito positivo si presenta come segue:

```
http://localhost/myapp/permissions?admin_consent=True&tenant=fa00d692-e9c7-4460-a743-29f2956fd429&state=12345&scope=https%3a%2f%2fgraph.microsoft.com%2fCalendars.Read+https%3a%2f%2fgraph.microsoft.com%2fMail.Send
```

| Parametro         | Descrizione                                                                                       |
|------------------:|:-------------------------------------------------------------------------------------------------:|
| `tenant`| Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato GUID.|
| `state`           | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava.|
| `scope`          | Set di autorizzazioni a cui è stato concesso l'accesso per l'applicazione.|
| `admin_consent`   | Sarà impostato su `True`.|

### <a name="error-response"></a>Risposta di errore

`http://localhost/myapp/permissions?error=consent_required&error_description=AADSTS65004%3a+The+resource+owner+or+authorization+server+denied+the+request.%0d%0aTrace+ID%3a+d320620c-3d56-42bc-bc45-4cdd85c41f00%0d%0aCorrelation+ID%3a+8478d534-5b2c-4325-8c2c-51395c342c89%0d%0aTimestamp%3a+2019-09-24+18%3a34%3a26Z&admin_consent=True&tenant=fa15d692-e9c7-4460-a743-29f2956fd429&state=12345`

Aggiunta ai parametri visualizzati in una risposta con esito positivo, i parametri di errore vengono visualizzati come indicato di seguito.

| Parametro          | Descrizione                                                                                      |
|-------------------:|:-------------------------------------------------------------------------------------------------:|
| `error`            | Stringa di codice di errore che può essere usata per classificare i tipi di errori che si verificano e correggerli.|
| `error_description`| Messaggio di errore specifico che consente a uno sviluppatore di identificare la causa principale di un errore.|
| `tenant`| Tenant della directory che ha concesso all'applicazione le autorizzazioni richieste, in formato GUID.|
| `state`           | Valore incluso nella richiesta che verrà restituito anche nella risposta del token. Può trattarsi di una stringa di qualsiasi contenuto. Lo stato viene usato per codificare le informazioni sullo stato dell'utente nell'app prima dell'esecuzione della richiesta di autenticazione, ad esempio la pagina o la vista in cui si trovava.|
| `admin_consent`   | Verrà impostato su `True` per indicare che questa risposta si è verificata in un flusso di consenso dell'amministratore.|

## <a name="next-steps"></a>Passaggi successivi
- Vedere [come convertire un'app multi-tenant](howto-convert-app-to-be-multi-tenant.md)
- Informazioni [su come il consenso è supportato a livello di protocollo OAuth 2,0 durante il flusso di concessione del codice di autorizzazione](v2-oauth2-auth-code-flow.md#request-an-authorization-code).
- Informazioni su [come un'applicazione multi-tenant può utilizzare il Framework di consenso](active-directory-devhowto-multi-tenant-overview.md) per implementare il consenso "utente" e "amministratore", supportando modelli di applicazione multilivello più avanzati.
- Informazioni sulle [esperienze di consenso dell'applicazione Azure ad](application-consent-experience.md)
